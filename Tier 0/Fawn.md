# Fawn
### Summary
Server running FTP is misconfigured to allow anonymous passwordless access.

---

### Scanning
Service detection on open ports  \
`nmap -sV IP`
>21/tcp open  ftp     vsftpd 3.0.3 \
Service Info: OS: Unix

Unix machine running ftp on port 21

### FTP
`ftp IP` \
username: anonymous, password not required

### Flag
flag.txt in landing directory \
`tcp> get flag.txt flag-download.txt`

