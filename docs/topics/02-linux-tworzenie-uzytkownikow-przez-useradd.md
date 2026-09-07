## 2. Linux — tworzenie użytkowników przez useradd

W ćwiczeniach konta tworzymy poleceniem `useradd`.

```bash
sudo useradd -m -s /bin/bash -c "Jan Kowalski" jan
sudo passwd jan
```

Najważniejsze flagi:

| Flaga | Znaczenie |
|---|---|
| `-m` | utwórz katalog domowy |
| `-M` | nie twórz katalogu domowego |
| `-d /ścieżka` | ustaw katalog domowy |
| `-s /bin/bash` | ustaw powłokę logowania |
| `-c "opis"` | ustaw opis lub pełną nazwę |
| `-u UID` | ustaw numer UID |
| `-g grupa` | ustaw grupę podstawową |
| `-G grupa1,grupa2` | ustaw grupy dodatkowe |
| `-e RRRR-MM-DD` | ustaw datę wygaśnięcia konta |
| `-f dni` | dni nieaktywności po wygaśnięciu hasła |
| `-r` | utwórz konto systemowe |

Przykład bardziej kompletny:

```bash
sudo useradd \
  -m \
  -d /home/ania \
  -s /bin/bash \
  -c "Anna Nowak" \
  -u 1101 \
  -g uczniowie \
  -G projekt,sudo \
  -e 2027-06-30 \
  -f 7 \
  ania

sudo passwd ania
id ania
```

Grupy podane w `-g` i `-G` muszą już istnieć. `-g` oznacza jedną grupę podstawową, a `-G` grupy dodatkowe.

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./01-netplan-poprawny-yaml-do-wklejenia.md) · [Następny temat →](./03-groupadd-tworzenie-grup.md)
