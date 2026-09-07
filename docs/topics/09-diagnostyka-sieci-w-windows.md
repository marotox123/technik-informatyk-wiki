## 8. Diagnostyka sieci w Windows

```bat
ipconfig
ipconfig /all
ipconfig /release
ipconfig /renew
ipconfig /flushdns
ping 192.168.1.1
ping example.com
tracert 8.8.8.8
nslookup example.com
arp -a
route print
netstat -ano
getmac
hostname
```

Kolejność diagnostyki:

1. Sprawdź kabel, Wi-Fi i diody interfejsu.
2. Sprawdź adres, maskę, bramę oraz DNS przez `ipconfig /all`.
3. Pinguj `127.0.0.1`.
4. Pinguj własny adres.
5. Pinguj bramę domyślną.
6. Pinguj publiczny adres IP, np. `1.1.1.1`.
7. Sprawdź nazwę przez `nslookup` lub `ping example.com`.

Jeżeli działa ping do `1.1.1.1`, ale nie działa nazwa domenowa, prawdopodobnym problemem jest DNS.

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./08-okablowanie-sieciowe.md) · [Następny temat →](./10-windows-konta-przez-net-user.md)
