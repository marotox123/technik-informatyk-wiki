## 3. groupadd — tworzenie grup

Do tworzenia grup używamy systemowego polecenia `groupadd`.

### Utworzenie zwykłej grupy

```bash
sudo groupadd projekt
```

### Utworzenie grupy z wybranym GID

```bash
sudo groupadd -g 1500 projekt
```

### Utworzenie grupy systemowej

```bash
sudo groupadd -r aplikacja
```

### Utworzenie grupy z własnym hasłem w `/etc/gshadow`

```bash
sudo groupadd -p ZASZYFROWANE_HASLO projekt
```

Opcji `-p` zwykle nie stosuje się ręcznie, ponieważ podanie skrótu hasła w poleceniu może być niebezpieczne. Do zwykłych zadań wystarczą `groupadd`, `usermod` i `gpasswd`.

### Dodanie użytkownika do istniejącej grupy

```bash
sudo usermod -aG projekt ania
```

### Najważniejsze opcje groupadd

| Opcja | Znaczenie |
|---|---|
| `-g GID`, `--gid GID` | nadaj grupie określony numer GID |
| `-r`, `--system` | utwórz grupę systemową |
| `-f`, `--force` | zakończ bez błędu, jeśli grupa już istnieje; przy zajętym GID dobierz inny |
| `-o`, `--non-unique` | pozwól użyć nieunikalnego GID razem z `-g` |
| `-p HASŁO`, `--password HASŁO` | ustaw zaszyfrowane hasło grupy; opcja rzadko zalecana |
| `-K KLUCZ=WARTOŚĆ` | nadpisz ustawienie z `/etc/login.defs` |
| `-R KATALOG`, `--root KATALOG` | wykonaj operację względem wskazanego katalogu głównego |
| `-P KATALOG`, `--prefix KATALOG` | zastosuj prefiks katalogu do plików systemowych |
| `--help` | wyświetl pomoc |

Dokładny zestaw opcji może zależeć od wersji systemu. Sprawdź go lokalnie:

```bash
groupadd --help
man groupadd
```

Sprawdzanie grup:

```bash
groups ania
id ania
getent group projekt
```

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./02-linux-tworzenie-uzytkownikow-przez-useradd.md) · [Następny temat →](./04-magiczna-pileczkowa-metoda-pelny-tutorial.md)
