# Powtórka technika informatyka — klasa 2, v4

To jest wersja tekstowa do nauki i kopiowania komend. Polecenia administracyjne wykonuj w maszynie wirtualnej albo na komputerze szkolnym, na którym masz zgodę na zmiany.

## 1. Netplan — poprawny YAML do wklejenia

Pliki Netplanu znajdują się zazwyczaj w katalogu:

```bash
/etc/netplan/
```

Najpierw sprawdź nazwę interfejsu sieciowego:

```bash
ip link
```

Może to być na przykład `enp0s3`, `ens18` albo `eth0`. W poniższych przykładach używam `enp0s3`.

### DHCP — automatyczny adres IPv4

Otwórz plik:

```bash
sudo nano /etc/netplan/01-szkola.yaml
```

Wklej:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: true
```

### Statyczny adres IPv4

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: false
      addresses:
        - 192.168.10.20/24
      routes:
        - to: default
          via: 192.168.10.1
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
        search:
          - szkola.local
```

W YAML-u używaj spacji, a nie tabulatorów. Zachowaj dokładnie pokazane wcięcia.

### Sprawdzenie i zastosowanie konfiguracji

```bash
sudo netplan generate
sudo netplan try
sudo netplan apply
```

Następnie sprawdź wynik:

```bash
ip addr show enp0s3
ip route
resolvectl status
ping -c 4 192.168.10.1
ping -c 4 1.1.1.1
```

`netplan generate` sprawdza i generuje konfigurację. `netplan try` tymczasowo ją uruchamia i pozwala ją cofnąć, jeśli stracisz połączenie. `netplan apply` stosuje konfigurację.

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

## 4. Magiczna Piłeczkowa Metoda — pełny tutorial

Piłeczkowa Metoda służy do szybkiego wyznaczania:

- adresu sieci,
- adresu rozgłoszeniowego, czyli broadcastu,
- pierwszego i ostatniego hosta,
- liczby hostów w podsieci.

Nie jest to metoda BIN↔HEX. Piłeczki oznaczają granice kolejnych podsieci.

### Przykład 1: `192.168.1.75/27`

#### Krok 1. Zamień prefiks na maskę

Prefiks `/27` oznacza:

```text
255.255.255.224
```

Maskę można odczytać z tabeli:

| Prefiks | Ostatni oktet maski |
|---|---:|
| `/25` | 128 |
| `/26` | 192 |
| `/27` | 224 |
| `/28` | 240 |
| `/29` | 248 |
| `/30` | 252 |

#### Krok 2. Znajdź ciekawy oktet

Maska to:

```text
255.255.255.224
```

Ciekawy oktet to taki, który nie jest równy `255` ani `0`. Tutaj jest to czwarty oktet:

```text
224
```

Dlatego w adresie IP interesuje nas również czwarty oktet, czyli liczba `75`.

#### Krok 3. Oblicz krok piłeczek

```text
krok = 256 - ciekawy oktet maski
krok = 256 - 224
krok = 32
```

#### Krok 4. Wypisz piłeczki

Zaczynamy od zera i dodajemy po 32:

```text
0, 32, 64, 96, 128, 160, 192, 224, 256
```

Są to granice kolejnych podsieci.

#### Krok 5. Umieść oktet adresu między piłeczkami

Szukamy liczby `75`:

```text
64 -------- 75 -------- 96
```

Dolna piłeczka, czyli `64`, jest początkiem podsieci. Następna piłeczka, czyli `96`, rozpoczyna już następną podsieć.

#### Krok 6. Wyznacz sieć i broadcast

```text
adres sieci = dolna piłeczka = 64
broadcast = następna piłeczka - 1 = 96 - 1 = 95
```

Pełne adresy:

```text
sieć:     192.168.1.64
broadcast: 192.168.1.95
```

#### Krok 7. Wyznacz hosty

```text
pierwszy host = sieć + 1
ostatni host = broadcast - 1
```

Wynik:

```text
pierwszy host: 192.168.1.65
ostatni host:  192.168.1.94
```

#### Krok 8. Policz liczbę hostów

Dla `/27` pozostaje:

```text
32 - 27 = 5 bitów hosta
```

```text
2⁵ = 32 wszystkich adresów
32 - 2 = 30 użytecznych adresów hostów
```

Odejmujemy adres sieci i broadcast.

### Ostateczna odpowiedź dla `192.168.1.75/27`

| Element | Wynik |
|---|---|
| Maska | `255.255.255.224` |
| Krok | `32` |
| Adres sieci | `192.168.1.64` |
| Broadcast | `192.168.1.95` |
| Pierwszy host | `192.168.1.65` |
| Ostatni host | `192.168.1.94` |
| Użyteczne hosty | `30` |

### Przykład 2: ciekawy oktet nie jest ostatni

Adres:

```text
172.20.77.10/20
```

Maska `/20`:

```text
255.255.240.0
```

Ciekawy jest trzeci oktet maski: `240`. Dlatego z adresu IP bierzemy trzeci oktet: `77`.

```text
krok = 256 - 240 = 16
```

Piłeczki:

```text
0, 16, 32, 48, 64, 80, 96, 112, ...
```

Liczba `77` leży tutaj:

```text
64 -------- 77 -------- 80
```

Sieć zaczyna się od `64`. Następna podsieć zaczyna się od `80`, więc broadcast ma w ciekawym oktecie `79`.

Wszystkie oktety znajdujące się za ciekawym oktetem ustawiamy:

- dla adresu sieci na `0`,
- dla broadcastu na `255`.

Wynik:

```text
sieć:      172.20.64.0
broadcast: 172.20.79.255
pierwszy:  172.20.64.1
ostatni:   172.20.79.254
```

### Piłeczkowa ściąga

```text
1. Prefiks → maska.
2. Znajdź oktet maski różny od 255 i 0.
3. Krok = 256 - ten oktet maski.
4. Wypisz piłeczki od 0 co wartość kroku.
5. Znajdź przedział zawierający odpowiedni oktet IP.
6. Dolna piłeczka = sieć.
7. Następna piłeczka - 1 = broadcast.
8. Sieć + 1 = pierwszy host.
9. Broadcast - 1 = ostatni host.
```

## 5. Osobna szybka metoda BIN↔HEX

Grupuj bity po cztery od prawej strony:

```text
10110110₂ = 1011 0110 = B6₁₆
```

W drugą stronę każdą cyfrę HEX zamień na cztery bity:

```text
3A₁₆ = 0011 1010₂
```

Podstawowa tabela:

```text
0000=0  0001=1  0010=2  0011=3
0100=4  0101=5  0110=6  0111=7
1000=8  1001=9  1010=A  1011=B
1100=C  1101=D  1110=E  1111=F
```

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

## 7. Okablowanie sieciowe

### Standard T568A

| Pin | Kolor żyły |
|---:|---|
| 1 | biało-zielony |
| 2 | zielony |
| 3 | biało-pomarańczowy |
| 4 | niebieski |
| 5 | biało-niebieski |
| 6 | pomarańczowy |
| 7 | biało-brązowy |
| 8 | brązowy |

### Standard T568B

| Pin | Kolor żyły |
|---:|---|
| 1 | biało-pomarańczowy |
| 2 | pomarańczowy |
| 3 | biało-zielony |
| 4 | niebieski |
| 5 | biało-niebieski |
| 6 | zielony |
| 7 | biało-brązowy |
| 8 | brązowy |

- Kabel prosty: ten sam standard na obu końcach.
- Kabel krosowany: T568A na jednym końcu i T568B na drugim.
- Nowoczesne urządzenia często mają Auto MDI-X, ale na sprawdzianie trzeba znać oba sposoby.
- Po zarobieniu przewodu sprawdź wszystkie osiem żył testerem.

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

## 9. Windows — konta przez net user

Wyświetlenie kont lokalnych:

```bat
net user
```

Utworzenie konta z bezpiecznym pytaniem o hasło:

```bat
net user uczen * /add
```

Kompletniejszy przykład:

```bat
net user uczen * /add /fullname:"Jan Kowalski" /comment:"Klasa 2" /active:yes
```

| Przełącznik | Znaczenie |
|---|---|
| `/add` | utwórz konto |
| `/delete` | usuń konto |
| `/active:yes` lub `/active:no` | włącz albo wyłącz konto |
| `/fullname:"Jan Kowalski"` | ustaw pełną nazwę |
| `/comment:"opis"` | ustaw opis konta |
| `/usercomment:"tekst"` | ustaw komentarz użytkownika |
| `/passwordchg:yes` lub `no` | zezwól lub zabroń użytkownikowi zmieniać hasło |
| `/passwordreq:yes` lub `no` | określ, czy hasło jest wymagane |
| `/expires:RRRR-MM-DD` lub `never` | ustaw wygaśnięcie konta |
| `/times:czasy` lub `all` | ustaw dozwolone godziny logowania |
| `/workstations:nazwy` lub `*` | określ dozwolone stacje robocze |
| `/homedir:ścieżka` | ustaw katalog domowy |
| `/profilepath:ścieżka` | ustaw ścieżkę profilu |
| `/scriptpath:plik` | ustaw skrypt logowania |
| `/countrycode:NNN` | ustaw kod kraju |
| `/domain` | wykonaj operację w bieżącej domenie |

Sprawdzenie, wyłączenie i usunięcie konta:

```bat
net user uczen
net user uczen /active:no
net user uczen /delete
```

## 10. Windows — grupy przez net localgroup

```bat
net localgroup
net localgroup Praktykanci /add
net localgroup Praktykanci uczen /add
net localgroup Praktykanci
net localgroup Praktykanci uczen /delete
net localgroup Praktykanci /delete
```

Nazwę zawierającą spacje zapisuj w cudzysłowie:

```bat
net localgroup "Użytkownicy pulpitu zdalnego" uczen /add
```

Dodanie konta domenowego do grupy lokalnej:

```bat
net localgroup Praktykanci SZKOLA\jkowalski /add
```

Nazwy wbudowanych grup mogą zależeć od języka systemu Windows.

## 11. Windows — mkdir, net share i net use

### Tworzenie katalogu

```bat
mkdir C:\Dane\Materialy
mkdir "C:\Dane szkolne\Klasa 2"
```

### Udostępnienie katalogu

```bat
net share
net share Materialy=C:\Dane\Materialy
net share Materialy=C:\Dane\Materialy /remark:"Materiały szkolne"
net share Materialy=C:\Dane\Materialy /grant:Uczniowie,read
net share Materialy /delete
```

Najważniejsze opcje `net share`:

| Opcja | Znaczenie |
|---|---|
| `/grant:nazwa,read` | przyznaj odczyt |
| `/grant:nazwa,change` | przyznaj zmianę |
| `/grant:nazwa,full` | przyznaj pełną kontrolę |
| `/change:nazwa,uprawnienie` | zmień uprawnienie |
| `/revoke:nazwa` | odbierz uprawnienie |
| `/users:liczba` | ogranicz liczbę użytkowników |
| `/unlimited` | brak limitu użytkowników |
| `/remark:"opis"` | ustaw opis udziału |
| `/delete` | usuń udział |

### Mapowanie udziału sieciowego

```bat
net use
net use Z: \\SERWER\Materialy
net use Z: \\SERWER\Materialy * /user:SZKOLA\uczen
net use Z: \\SERWER\Materialy /persistent:yes
net use Z: /delete
net use * /delete
```

`/persistent:yes` zapamiętuje połączenie, a `/persistent:no` go nie utrwala.

## 12. Windows — icacls i uprawnienia NTFS

Najważniejsze oznaczenia:

| Kod | Znaczenie |
|---|---|
| `F` | pełna kontrola |
| `M` | modyfikacja |
| `RX` | odczyt i wykonanie |
| `R` | odczyt |
| `W` | zapis |
| `D` | usuwanie |
| `(OI)` | dziedziczenie przez pliki |
| `(CI)` | dziedziczenie przez podkatalogi |

```bat
icacls C:\Dane\Materialy
icacls C:\Dane\Materialy /inheritance:r
icacls C:\Dane\Materialy /grant "Uczniowie:(OI)(CI)RX"
icacls C:\Dane\Materialy /grant:r "Nauczyciele:(OI)(CI)M"
icacls C:\Dane\Materialy /remove "Goście"
icacls C:\Dane\Materialy /setowner Administratorzy
icacls C:\Dane\Materialy /save C:\acl.txt /t
icacls C:\Dane\Materialy /restore C:\acl.txt
```

- `/inheritance:r` wyłącza dziedziczenie i zachowuje dotychczas odziedziczone wpisy jako jawne.
- `/grant` dodaje uprawnienia.
- `/grant:r` zastępuje jawne uprawnienia wskazanego użytkownika lub grupy.
- `/t` działa rekurencyjnie.
- `/c` kontynuuje mimo błędów.
- `/q` ogranicza komunikaty.

Szkolny katalog tylko do odczytu:

```bat
mkdir C:\Dane\Materialy
icacls C:\Dane\Materialy /inheritance:r
icacls C:\Dane\Materialy /grant "Administratorzy:(OI)(CI)F"
icacls C:\Dane\Materialy /grant "Uczniowie:(OI)(CI)RX"
net share Materialy=C:\Dane\Materialy /grant:Uczniowie,read
```

Uprawnienia udziału i NTFS działają jednocześnie. Efektywnie obowiązuje bardziej ograniczający wynik.

## 13. Windows — drukarka przez port TCP/IP

1. Ustal adres drukarki i wykonaj `ping 192.168.1.50`.
2. Otwórz **Ustawienia → Bluetooth i urządzenia → Drukarki i skanery**.
3. Wybierz dodanie urządzenia, a następnie dodawanie ręczne.
4. Wybierz drukarkę przy użyciu adresu TCP/IP lub nazwy hosta.
5. Wpisz adres IP drukarki.
6. Wybierz właściwy sterownik producenta i modelu.
7. Nadaj czytelną nazwę i wydrukuj stronę testową.

Gdy drukarka nie działa:

- sprawdź IP, maskę, bramę i połączenie fizyczne,
- sprawdź port, zwykle RAW 9100 albo LPR zgodnie z instrukcją urządzenia,
- sprawdź kolejkę i tryb offline,
- sprawdź usługę Bufor wydruku,
- sprawdź zgodność sterownika.

## 14. Linux — passwd, usermod, groups i chage

```bash
sudo passwd ania
sudo passwd -l ania
sudo passwd -u ania
```

`passwd -l` blokuje hasło, a `passwd -u` je odblokowuje. Nie zawsze oznacza to całkowite zablokowanie wszystkich możliwych metod logowania do konta.

Najważniejsze przykłady `usermod`:

```bash
sudo usermod -aG projekt ania
sudo usermod -g uczniowie ania
sudo usermod -s /bin/bash ania
sudo usermod -d /home/anna -m ania
sudo usermod -c "Anna Nowak" ania
sudo usermod -L ania
sudo usermod -U ania
```

Przy grupach dodatkowych pamiętaj o `-aG`. Samo `-G` może zastąpić dotychczasową listę grup dodatkowych.

```bash
groups ania
id ania
getent passwd ania
getent group projekt
```

Ważność hasła:

```bash
sudo chage -M 90 ania
sudo chage -W 7 ania
sudo chage -l ania
```

- `chage -M 90` ustawia maksymalny wiek hasła na 90 dni.
- `chage -W 7` ostrzega siedem dni przed wygaśnięciem.
- `chage -l` pokazuje aktualne ustawienia.

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

## 17. Linux — ls, cp, tar, alias, whoami i source

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

## 20. Test powtórkowy

1. Jak utworzyć w Linuksie konto `ola` z katalogiem domowym i powłoką Bash?
2. Czym różnią się `useradd -m` i `useradd -M`?
3. Czym różnią się opcje `-g` i `-G`?
4. Jak utworzyć grupę `projekt` z GID 1500?
5. Jak dodać użytkownika `ola` do grupy dodatkowej `projekt` bez utraty innych grup?
6. Jak ustawić maksymalny wiek hasła 60 dni i ostrzeżenie siedem dni wcześniej?
7. Co oznacza `chmod 754`?
8. Jak zmienić właściciela i grupę całego katalogu?
9. Który plik przechowuje skróty haseł?
10. Jak wczytać zmieniony `~/.bashrc`?
11. Jak utworzyć archiwum `projekt.tar.gz`?
12. Jak wyłączyć dziedziczenie NTFS, zachowując wpisy jako jawne?
13. Jak dać grupie `Uczniowie` odczyt i wykonanie w całym drzewie?
14. Jak zmapować `\\SERWER\Materialy` jako dysk `Z:`?
15. Jak utworzyć lokalne konto Windows `ola` z pytaniem o hasło?
16. Jak utworzyć udział `Materialy` tylko do odczytu dla grupy `Uczniowie`?
17. Jaka jest maska i krok dla `/27`?
18. Wyznacz sieć i broadcast dla `192.168.1.75/27`.
19. Zamień `10110110₂` na HEX.
20. Jak bezpiecznie sprawdzić konfigurację Netplanu przed jej trwałym zastosowaniem?

### Odpowiedzi

1. `sudo useradd -m -s /bin/bash ola`, a następnie `sudo passwd ola`.
2. `-m` tworzy katalog domowy, `-M` go nie tworzy.
3. `-g` ustawia grupę podstawową, `-G` grupy dodatkowe.
4. `sudo groupadd -g 1500 projekt`.
5. `sudo usermod -aG projekt ola`.
6. `sudo chage -M 60 -W 7 ola`.
7. Właściciel `rwx`, grupa `r-x`, inni `r--`.
8. `sudo chown -R użytkownik:grupa katalog`.
9. `/etc/shadow`.
10. `source ~/.bashrc`.
11. `tar -czf projekt.tar.gz projekt/`.
12. `icacls ścieżka /inheritance:r`.
13. `icacls ścieżka /grant "Uczniowie:(OI)(CI)RX"`.
14. `net use Z: \\SERWER\Materialy`.
15. `net user ola * /add`.
16. `net share Materialy=C:\Dane\Materialy /grant:Uczniowie,read`.
17. `255.255.255.224`, krok `32`.
18. Sieć `192.168.1.64`, broadcast `192.168.1.95`.
19. `B6₁₆`.
20. `sudo netplan generate`, a następnie `sudo netplan try`.
