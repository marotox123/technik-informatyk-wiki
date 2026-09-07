## 16. Linux — ważne pliki systemowe

| Plik | Zawartość |
|---|---|
| `/etc/passwd` | konta: login, UID, GID, opis, katalog domowy i powłoka |
| `/etc/shadow` | skróty haseł i dane o ich ważności; dostęp ograniczony |
| `/etc/group` | grupy, GID i członkowie dodatkowi |
| `/etc/gshadow` | chronione dane administracyjne grup |
| `/etc/hosts` | lokalne mapowanie nazw na adresy IP |
| `/etc/resolv.conf` | ustawienia resolvera DNS, często generowane automatycznie |

Przykładowy wpis `/etc/passwd`:

```text
ania:x:1001:1001:Anna Nowak:/home/ania:/bin/bash
```

Kolejne pola:

```text
login:x:UID:GID:komentarz:katalog_domowy:powłoka
```

Bezpieczne sprawdzanie:

```bash
getent passwd ania
getent group projekt
ls -l /etc/passwd /etc/shadow /etc/group /etc/gshadow
cat /etc/hosts
resolvectl status
```

Nie edytuj ręcznie `/etc/shadow`. Do kont i haseł używaj `useradd`, `usermod`, `passwd` oraz `chage`.

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./16-linux-chmod-chown-i-chgrp.md) · [Następny temat →](./18-linux-pliki-wildcardy-i-codzienne-komendy.md)
