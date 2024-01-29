# Meow
### Summary
Server running telnet with common username "root" having a blank password

---

### Scanning
Service detection on open ports  \
`nmap -sV IP`
>23/tcp open  telnet  Linux telnetd


### Telnet
Telnet provides remote access to a virtual terminal \
`telnet IP` \
Access gained with username: root, blank password

### Flag
In remote terminal: \
`ls`
> flag.txt

`cat flag.txt`
