# Dancing
### Summary
Windows server running SMB with shares having blank passwords

---

### Scanning
Scan for open ports \
`nmap -sV IP`
>135/tcp open  msrpc         Microsoft Windows RPC \
139/tcp open  netbios-ssn   Microsoft Windows netbios-ssn \
445/tcp open  microsoft-ds? \
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Windows server running SMB (used for file sharing) on port 445

### SMB
List SMB services \
`smbclient -L IP` List SMB services\
Services: ADMIN$, C$, IPC$, WorkShares

`smbclient //IP/[Sharename]` \
Access gained to IPC$ and WorkShares with a blank password

### Flag
Flag is in WorkShares: James.P\flag.txt
