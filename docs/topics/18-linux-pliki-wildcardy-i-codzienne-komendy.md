## 17. Linux — pliki, wildcardy i codzienne komendy

### Ścieżki, które trzeba znać

| Zapis | Znaczenie |
|---|---|
| `/` | główny katalog systemu |
| `.` | bieżący katalog |
| `..` | katalog nadrzędny |
| `~` | katalog domowy aktualnego użytkownika |
| `~/Dokumenty` | katalog `Dokumenty` we własnym katalogu domowym |
| `/etc/hosts` | ścieżka bezwzględna, rozpoczynająca się od `/` |
| `projekt/plik.txt` | ścieżka względna, liczona od bieżącego katalogu |

Sprawdzenie miejsca i zmiana katalogu:

```bash
pwd
cd /etc
cd ..
cd ~
cd -
```

- `pwd` pokazuje pełną ścieżkę bieżącego katalogu.
- `cd ..` przechodzi poziom wyżej.
- `cd ~` wraca do katalogu domowego.
- `cd -` wraca do poprzedniego katalogu.

### Wildcardy — znaki wieloznaczne

Wildcardy są rozwijane przez powłokę jeszcze przed uruchomieniem komendy. Pozwalają wskazywać wiele nazw bez wypisywania każdej osobno.

| Wildcard | Znaczenie | Przykład dopasowania |
|---|---|---|
| `*` | dowolna liczba dowolnych znaków, również zero | `*.txt` → wszystkie pliki kończące się na `.txt` |
| `?` | dokładnie jeden dowolny znak | `plik?.txt` → `plik1.txt`, ale nie `plik10.txt` |
| `[abc]` | dokładnie jeden znak z podanego zestawu | `plik[123].txt` |
| `[a-z]` | dokładnie jeden znak z zakresu | `raport[a-c].txt` |
| `[!abc]` | jeden znak, którego nie ma w zestawie | `plik[!0].txt` |

Przykłady:

```bash
ls *.txt
ls raport?.pdf
ls zdjecie[1-5].jpg
cp *.txt kopie/
```

Przed użyciem wildcardu z `cp`, `mv` albo `rm` najpierw sprawdź dopasowanie przez `ls`:

```bash
ls *.log
```

> Ważne: nazwa zaczynająca się od kropki jest ukryta. Samo `*` zwykle jej nie dopasuje. Do wyświetlania ukrytych plików użyj `ls -a`.

### `ls` — wyświetlanie zawartości katalogu

```bash
ls
ls -l
ls -a
ls -la
ls -lh
ls -R katalog
ls -ld katalog
```

| Opcja | Znaczenie |
|---|---|
| `-l` | widok szczegółowy: prawa, właściciel, rozmiar i data |
| `-a` | pokaż również pliki ukryte |
| `-h` | rozmiary czytelne dla człowieka, np. KiB lub MiB; zwykle z `-l` |
| `-R` | wyświetlaj również zawartość podkatalogów |
| `-d` | pokaż sam katalog zamiast jego zawartości |

### `touch` — tworzenie pustego pliku i zmiana czasu

Jeżeli plik nie istnieje, `touch` tworzy pusty plik. Jeżeli istnieje, aktualizuje jego czasy dostępu i modyfikacji bez usuwania zawartości.

```bash
touch notatka.txt
touch plik1.txt plik2.txt plik3.txt
touch raport{1..5}.txt
touch -a plik.txt
touch -m plik.txt
touch -t 202609071530 plik.txt
```

| Opcja | Znaczenie |
|---|---|
| `-a` | zmień tylko czas ostatniego dostępu |
| `-m` | zmień tylko czas ostatniej modyfikacji |
| `-c` | nie twórz pliku, jeżeli nie istnieje |
| `-t CZAS` | ustaw podany czas w formacie `[[CC]YY]MMDDhhmm[.ss]` |
| `-r PLIK` | skopiuj znaczniki czasu z innego pliku |

### `cp` — kopiowanie plików i katalogów

Podstawowa składnia:

```text
cp [opcje] źródło cel
```

Przykłady:

```bash
cp notatka.txt kopia.txt
cp notatka.txt ~/Dokumenty/
cp plik1.txt plik2.txt kopie/
cp *.txt kopie/
cp -r projekt projekt_kopia
cp -a projekt projekt_kopia
cp -i wazny.txt kopia.txt
cp -v raport.txt kopie/
```

| Opcja | Znaczenie |
|---|---|
| `-r`, `-R` | kopiuj katalog wraz z zawartością |
| `-a` | tryb archiwalny: zachowaj strukturę i możliwie dużo atrybutów |
| `-i` | zapytaj przed nadpisaniem istniejącego pliku |
| `-n` | nie nadpisuj istniejących plików |
| `-u` | kopiuj, gdy źródło jest nowsze albo celu nie ma |
| `-v` | pokazuj kopiowane elementy |
| `-p` | zachowaj czasy, tryb i — gdy to możliwe — właściciela |

> Gdy kopiujesz wiele plików, ostatni argument musi być istniejącym katalogiem docelowym.

### `rm` — usuwanie plików i katalogów

`rm` usuwa elementy bez przenoszenia ich do kosza. W terminalu zwykle nie ma prostego „cofnij”.

```bash
rm notatka.txt
rm -i notatka.txt
rm -v *.tmp
rm -r stary_katalog
rm -ri stary_katalog
```

| Opcja | Znaczenie |
|---|---|
| `-i` | pytaj przed każdym usunięciem |
| `-I` | jedno dodatkowe ostrzeżenie przy większej operacji |
| `-r`, `-R` | usuń katalog i całą jego zawartość |
| `-f` | wymuś operację i nie pytaj; używaj bardzo ostrożnie |
| `-v` | pokazuj usuwane elementy |

Bezpieczny szkolny schemat:

```bash
pwd
ls stary_katalog
rm -ri stary_katalog
```

> Nigdy nie wpisuj przypadkowo `rm -rf` z szeroką, niesprawdzoną ścieżką. Połączenie `-r` i `-f` może bez pytań usunąć całe drzewo katalogów.

### `mkdir` i `rmdir` — katalogi

```bash
mkdir projekt
mkdir -p projekt/dokumentacja/notatki
mkdir -m 750 prywatne
rmdir pusty_katalog
```

- `mkdir -p` tworzy również brakujące katalogi nadrzędne.
- `mkdir -m 750` od razu ustawia prawa nowego katalogu.
- `rmdir` usuwa tylko pusty katalog.

### `mv` — przenoszenie i zmiana nazwy

```bash
mv stara.txt nowa.txt
mv raport.txt Dokumenty/
mv -i raport.txt Dokumenty/
mv -v *.txt archiwum/
```

- `-i` pyta przed nadpisaniem.
- `-n` nie nadpisuje istniejącego celu.
- `-v` pokazuje wykonywane operacje.

### `cat`, `less`, `head` i `tail` — czytanie plików

```bash
cat notatka.txt
less /var/log/syslog
head -n 10 plik.txt
tail -n 20 plik.txt
tail -f /var/log/syslog
```

- `cat` wypisuje cały plik.
- `less` pozwala wygodnie przewijać; klawisz `q` kończy program.
- `head` pokazuje początek pliku.
- `tail` pokazuje koniec, a `tail -f` śledzi dopisywane linie.

### Pipe `|` — łączenie poleceń w potok

Pipe, czyli pionowa kreska `|`, przekazuje standardowe wyjście jednej komendy na standardowe wejście następnej komendy.

Schemat:

```text
polecenie1 | polecenie2 | polecenie3
```

Każde polecenie przetwarza wynik poprzedniego. Dane nie muszą być najpierw zapisywane w pliku tymczasowym.

Najprostszy przykład:

```bash
ls -la | less
```

1. `ls -la` tworzy długą listę plików.
2. Pipe `|` przekazuje tę listę do `less`.
3. `less` pozwala wygodnie ją przewijać. Klawisz `q` kończy podgląd.

#### Filtrowanie przez `grep`

```bash
ls -la | grep ".txt"
ps aux | grep firefox
ip addr | grep "inet "
cat /etc/passwd | grep bash
```

`grep` zostawia tylko linie zawierające podany tekst. Ostatni przykład można zapisać prościej bez `cat`:

```bash
grep bash /etc/passwd
```

Przydatne opcje `grep`:

| Opcja | Znaczenie |
|---|---|
| `-i` | ignoruj wielkość liter |
| `-v` | pokaż linie, które nie pasują |
| `-n` | pokaż numery linii |
| `-r` | szukaj rekurencyjnie w katalogach |
| `-E` | użyj rozszerzonych wyrażeń regularnych |
| `-c` | pokaż liczbę pasujących linii |

```bash
grep -in "error" aplikacja.log
grep -r "adres IP" ~/Dokumenty
ps aux | grep -v grep | grep firefox
```

#### Sortowanie i usuwanie powtórzeń

```bash
cat imiona.txt | sort
cat imiona.txt | sort | uniq
cat imiona.txt | sort | uniq -c
```

- `sort` sortuje linie.
- `uniq` usuwa sąsiadujące, powtarzające się linie.
- Dlatego przed `uniq` często używa się `sort`.
- `uniq -c` dodatkowo liczy wystąpienia.

Przykład analizy powłok użytkowników:

```bash
cut -d: -f7 /etc/passwd | sort | uniq -c
```

1. `cut -d: -f7` wybiera siódme pole rozdzielone dwukropkiem, czyli powłokę.
2. `sort` grupuje takie same wartości obok siebie.
3. `uniq -c` liczy wystąpienia każdej powłoki.

#### Liczenie przez `wc`

```bash
ls -1 | wc -l
grep -i "error" aplikacja.log | wc -l
cat tekst.txt | wc -w
```

| Opcja `wc` | Co liczy |
|---|---|
| `-l` | linie |
| `-w` | słowa |
| `-c` | bajty |
| `-m` | znaki |

#### Wybieranie początku i końca wyniku

```bash
ps aux | head -n 5
ls -lh | tail -n 10
journalctl | tail -n 20
```

- `head -n 5` zachowuje pierwszych pięć linii.
- `tail -n 10` zachowuje ostatnich dziesięć linii.

#### `tee` — pokaż wynik i jednocześnie zapisz go do pliku

```bash
ip addr | tee adresy.txt
ip route | tee -a diagnostyka.txt
```

- `tee plik` nadpisuje plik i jednocześnie pokazuje wynik w terminalu.
- `tee -a plik` dopisuje dane na końcu.

Gdy zapis wymaga uprawnień administratora, `sudo` należy zastosować do `tee`:

```bash
echo "192.168.10.20 serwer" | sudo tee -a /etc/hosts
```

Samo `sudo echo ... > /etc/hosts` może nie zadziałać, ponieważ przekierowanie wykonuje bieżąca powłoka, a nie polecenie uruchomione przez `sudo`.

#### Pipe a przekierowania — różnica

| Operator | Działanie |
|---|---|
| `|` | przekaż wynik do następnego polecenia |
| `>` | zapisz wynik do pliku, nadpisując go |
| `>>` | dopisz wynik na końcu pliku |
| `<` | pobierz wejście z pliku |
| `2>` | zapisz błędy do pliku |
| `2>&1` | połącz strumień błędów ze standardowym wyjściem |

Przykłady:

```bash
ls -la > lista.txt
date >> dziennik.txt
sort < imiona.txt
find /etc -name "*.conf" 2> bledy.txt
find /etc -name "*.conf" > wyniki.txt 2>&1
```

Pipe nie zapisuje automatycznie danych w pliku. Przekierowanie `>` zapisuje wynik i nadpisuje dotychczasową zawartość. `>>` dopisuje wynik.

#### Kilka praktycznych potoków

```bash
# Dziesięć największych elementów w bieżącym katalogu
du -ah . | sort -hr | head -n 10

# Liczba aktywnych procesów użytkownika
ps -u "$USER" --no-headers | wc -l

# Unikalne adresy IP z pierwszej kolumny pliku
cut -d' ' -f1 access.log | sort | uniq -c | sort -nr

# Wyszukanie nasłuchujących portów TCP
ss -tln | grep LISTEN
```

> Potok może zawierać wiele etapów, ale każdy powinien mieć jasne zadanie. Najpierw uruchom poszczególne polecenia osobno, a dopiero potem połącz je pipe’ami.

### `tar`, `alias`, `whoami` i `source`

```bash
ls
ls -l
ls -la /var/log
cp plik.txt kopia.txt
cp -r katalog katalog_kopia
cp -a projekt projekt_kopia
whoami
```

Archiwizacja:

```bash
tar -czf projekt.tar.gz projekt/
tar -tzf projekt.tar.gz
tar -xzf projekt.tar.gz
```

- `-c` tworzy archiwum.
- `-x` rozpakowuje.
- `-t` wyświetla zawartość.
- `-z` używa kompresji gzip.
- `-f` wskazuje nazwę pliku archiwum.

Alias tymczasowy:

```bash
alias ll='ls -alF'
ll
```

Alias trwały dla Bash:

```bash
echo "alias ll='ls -alF'" >> ~/.bashrc
source ~/.bashrc
```

`source ~/.bashrc` wczytuje zmiany do bieżącej powłoki bez ponownego logowania.

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./17-linux-wazne-pliki-systemowe.md) · [Następny temat →](./19-diagnostyka-sieci-w-linuksie.md)
