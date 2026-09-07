## 18. Diagnostyka sieci w Linuksie

```bash
ip addr
ip link
ip route
ping -c 4 192.168.1.1
ping -c 4 1.1.1.1
getent hosts example.com
resolvectl status
ss -tulpn
ip neigh
hostname
hostnamectl
```

Przy problemie z siecią sprawdź kolejno interfejs, adres i maskę, trasę domyślną, połączenie z bramą, połączenie z publicznym IP oraz DNS.

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./18-linux-pliki-wildcardy-i-codzienne-komendy.md) · [Następny temat →](./20-koncowa-sciaga.md)
