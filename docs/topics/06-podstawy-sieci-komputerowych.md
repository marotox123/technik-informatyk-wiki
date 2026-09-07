## 6. Podstawy sieci komputerowych

### Model OSI i TCP/IP

| Warstwa OSI | Przykłady | Model TCP/IP |
|---|---|---|
| 7. Aplikacji | HTTP, DNS, DHCP, FTP | Aplikacji |
| 6. Prezentacji | kodowanie, kompresja, szyfrowanie | Aplikacji |
| 5. Sesji | zestawianie i utrzymanie sesji | Aplikacji |
| 4. Transportowa | TCP, UDP, porty | Transportowa |
| 3. Sieciowa | IPv4, router, ICMP | Internetu |
| 2. Łącza danych | Ethernet, MAC, switch | Dostępu do sieci |
| 1. Fizyczna | przewód, złącze, sygnał | Dostępu do sieci |

### Urządzenia i usługi

- Switch przełącza ramki głównie na podstawie adresów MAC.
- Router łączy różne sieci IP i wybiera trasę pakietu.
- Punkt dostępowy zapewnia dostęp do sieci bezprzewodowej.
- DHCP automatycznie przydziela adres IP, maskę, bramę i często DNS.
- DNS tłumaczy nazwy, na przykład `example.com`, na adresy IP.
- Brama domyślna jest używana do komunikacji poza lokalną podsiecią.
- NAT tłumaczy adresy, najczęściej prywatne na publiczny adres routera.

### Prywatne zakresy IPv4

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Adres `127.0.0.1` to pętla zwrotna. Adres z zakresu `169.254.0.0/16` może oznaczać automatyczny adres link-local po nieudanym pobraniu konfiguracji DHCP.

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./05-systemy-liczbowe-bin-dec-i-hex.md) · [Następny temat →](./07-ethernet-i-okablowanie-miedziane.md)
