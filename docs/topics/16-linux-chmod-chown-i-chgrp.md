## 15. Linux — chmod, chown i chgrp

| Prawo | Wartość | Znaczenie |
|---|---:|---|
| `r` | 4 | odczyt |
| `w` | 2 | zapis |
| `x` | 1 | wykonanie lub wejście do katalogu |

Trzy cyfry opisują kolejno właściciela, grupę i innych.

```text
chmod 754
właściciel: 7 = rwx
grupa:       5 = r-x
inni:        4 = r--
```

Przykłady:

```bash
chmod 754 skrypt.sh
chmod u+x skrypt.sh
chmod g-w,o-r plik.txt
chmod -R 750 /srv/projekt
chown ania plik.txt
chown ania:projekt plik.txt
chown -R ania:projekt /srv/projekt
chgrp projekt plik.txt
chgrp -R projekt /srv/projekt
```

Nie ustawiaj automatycznie `777`. Daje wszystkim pełne prawa. Opcji `-R` używaj ostrożnie, ponieważ zmienia całe drzewo katalogów.

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./15-linux-passwd-usermod-groups-i-chage.md) · [Następny temat →](./17-linux-wazne-pliki-systemowe.md)
