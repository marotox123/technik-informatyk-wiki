## 19. Końcowa ściąga

| Zadanie | Windows | Linux |
|---|---|---|
| Utwórz konto | `net user ola * /add` | `sudo useradd -m -s /bin/bash ola` |
| Ustaw hasło | `net user ola *` | `sudo passwd ola` |
| Utwórz grupę | `net localgroup Projekt /add` | `sudo groupadd projekt` |
| Dodaj do grupy | `net localgroup Projekt ola /add` | `sudo usermod -aG projekt ola` |
| Sprawdź grupy | `net localgroup Projekt` | `groups ola` lub `id ola` |
| Utwórz katalog | `mkdir C:\Dane` | `mkdir -p /srv/dane` |
| Uprawnienia | `icacls C:\Dane ...` | `chmod 750 /srv/dane` |
| Właściciel | `icacls ... /setowner ...` | `chown ola:projekt /srv/dane` |
| Udział/mapowanie | `net share`, `net use` | serwer Samba nie jest omawiany w tym materiale |
| Konfiguracja IP | `ipconfig /all` | `ip addr`, `ip route`, Netplan |

Piłeczkowa w skrócie:

```text
krok = 256 - maska w ciekawym oktecie
dolna piłeczka = adres sieci
następna piłeczka - 1 = broadcast
sieć + 1 = pierwszy host
broadcast - 1 = ostatni host
```

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./19-diagnostyka-sieci-w-linuksie.md) · [Następny temat →](./21-swiatlowody.md)
