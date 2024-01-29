# Appointment
### Summary
Passwordless log-in using SQL injection. Caused by inserting into queries user input which hasn't been properly escaped.

---

### Scanning
Service detection on open ports  \
`nmap -sV [IP]`
>PORT   STATE SERVICE VERSION \
80/tcp open  http    Apache httpd 2.4.38 ((Debian))

There is an Apache web server running. A log-in form can be found when visiting http://[IP] in the browser.


### Log-in form
The page is refreshed after a log-in attempt. This refresh is a lot slower when using the following username: `abc' OR SLEEP(5) #`, where `'` escapes out of the string and `#` comments out the rest of the query. \
The longer delay indicates that the SQL code was injected and the SLEEP function was executed. 

Assuming the query looks something like: \
`SELECT * FROM users WHERE username='{user}' AND password='{password}'`, \
the password-check can be skipped by commenting out the rest of the query: `username'#`.

The log-in succeeds with `admin'#`, meaning a user named `admin` exists. 


### Flag
Flag is displayed after logging in as `admin`.
