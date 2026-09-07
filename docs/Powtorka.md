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

## 5. Systemy liczbowe — BIN, DEC i HEX

### 1. Zamiana z binarnego na dziesiętny — metoda „Suma wag”

Każda pozycja w ośmiobitowej liczbie binarnej ma stałą wartość, czyli wagę. Czytając od lewej do prawej, są to:

```text
128, 64, 32, 16, 8, 4, 2, 1
```

Są to kolejne potęgi liczby 2. Aby zamienić liczbę binarną na dziesiętną, zsumuj tylko te wagi, pod którymi znajduje się `1`.

#### Przykład: `1010100₂` na system dziesiętny

Liczba ma siedem bitów, więc zaczynamy od wagi `64`:

| Waga | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|---|---:|---:|---:|---:|---:|---:|---:|
| Bit | **1** | **0** | **1** | **0** | **1** | **0** | **0** |

Jedynki stoją pod wagami `64`, `16` i `4`:

```text
64 + 16 + 4 = 84
```

Wynik:

```text
1010100₂ = 84₁₀
```

> Szybka zasada: bit `1` oznacza „dodaj tę wagę”, a bit `0` oznacza „pomiń tę wagę”.

### 2. Zamiana z dziesiętnego na binarny — metoda „Odejmowanie wag”

Zamiast dzielić liczbę przez 2 i zapisywać reszty, można kolejno sprawdzać, które potęgi dwójki mieszczą się w liczbie.

#### Przykład: `53₁₀` na system binarny

1. Największą wagą mieszczącą się w 53 jest `32`. Wpisujemy `1`: `53 - 32 = 21`.
2. Waga `16` mieści się w 21. Wpisujemy `1`: `21 - 16 = 5`.
3. Waga `8` nie mieści się w 5. Wpisujemy `0`.
4. Waga `4` mieści się w 5. Wpisujemy `1`: `5 - 4 = 1`.
5. Waga `2` nie mieści się w 1. Wpisujemy `0`.
6. Waga `1` mieści się w 1. Wpisujemy `1`: `1 - 1 = 0`.

| Waga | 32 | 16 | 8 | 4 | 2 | 1 |
|---|---:|---:|---:|---:|---:|---:|
| Bit | **1** | **1** | **0** | **1** | **0** | **1** |

Wynik:

```text
53₁₀ = 110101₂
```

> Szybka zasada: jeżeli waga mieści się w pozostałej liczbie, wpisz `1` i ją odejmij. Jeżeli się nie mieści, wpisz `0` i przejdź do następnej wagi.

### 3. Szybka metoda BIN↔HEX

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

## 20. Światłowody

### Jak działa światłowód

Światłowód przesyła dane za pomocą impulsów świetlnych. Światło jest prowadzone w rdzeniu dzięki różnicy współczynników załamania rdzenia i płaszcza oraz zjawisku całkowitego wewnętrznego odbicia.

Podstawowe elementy włókna:

| Element | Rola |
|---|---|
| rdzeń | prowadzi światło |
| płaszcz optyczny | utrzymuje światło w rdzeniu |
| powłoka ochronna | chroni włókno przed wilgocią i uszkodzeniami |
| elementy wzmacniające | chronią kabel przed rozciąganiem |
| płaszcz zewnętrzny | mechaniczna ochrona całego kabla |

Światłowód nie przewodzi prądu, jest odporny na zakłócenia elektromagnetyczne i pozwala przesyłać dane na duże odległości. Jest jednak wrażliwy na zabrudzenia złączy, zbyt mocne zgięcia oraz uszkodzenia mechaniczne.

### Światłowód jednomodowy i wielomodowy

| Cecha | Jednomodowy — SM | Wielomodowy — MM |
|---|---|---|
| oznaczenie | OS1, OS2 | OM1, OM2, OM3, OM4, OM5 |
| typowa średnica rdzenia | około 9 µm | najczęściej 50 µm; OM1 zwykle 62,5 µm |
| liczba modów | jeden | wiele |
| typowe źródło | laser | LED lub VCSEL |
| typowe odległości | duże, nawet wiele kilometrów | krótsze, zwykle sieci budynkowe i centra danych |
| dyspersja modalna | praktycznie brak | występuje |
| typowa barwa kabla | żółta | pomarańczowa, turkusowa lub limonkowa zależnie od klasy |

> Kolor płaszcza jest pomocą, a nie stuprocentowym dowodem typu kabla. Zawsze sprawdź nadruk i dokumentację.

Najważniejsza zasada: moduł optyczny, włókno i parametry łącza muszą do siebie pasować. Nie należy przypadkowo mieszać toru jednomodowego i wielomodowego.

### Popularne długości fal

| Długość fali | Typowe zastosowanie |
|---:|---|
| 850 nm | głównie światłowody wielomodowe |
| 1310 nm | często transmisja jednomodowa na średnie odległości |
| 1490 nm | m.in. niektóre systemy PON |
| 1550 nm | transmisja jednomodowa na duże odległości i systemy WDM |

Dokładna długość fali zawsze wynika ze specyfikacji wkładki optycznej lub urządzenia.

### Złącza światłowodowe

| Złącze | Cechy |
|---|---|
| LC | małe, bardzo popularne przy modułach SFP |
| SC | większe, prostokątne, zatrzaskowe |
| ST | okrągłe, mocowanie bagnetowe; spotykane w starszych instalacjach |
| FC | okrągłe i nakręcane; stabilne mechanicznie |
| MPO/MTP | wiele włókien w jednym złączu, używane przy dużej przepustowości |

### UPC i APC — sposób polerowania

| Typ | Zwykły kolor | Cechy |
|---|---|---|
| UPC | niebieski | czoło polerowane prosto, małe odbicia |
| APC | zielony | czoło polerowane pod kątem, jeszcze mniejsze odbicia zwrotne |

Nie łącz bezpośrednio złączy UPC z APC. Różny sposób polerowania powoduje złe dopasowanie, większe tłumienie i odbicia.

### Simplex, duplex, Tx i Rx

- `simplex` używa jednego toru transmisyjnego,
- `duplex` zazwyczaj używa dwóch włókien: jednego do nadawania i drugiego do odbioru,
- `Tx` oznacza nadajnik,
- `Rx` oznacza odbiornik.

Przy połączeniu duplex nadajnik jednego urządzenia musi trafić do odbiornika drugiego:

```text
Urządzenie A Tx  ─────────>  Rx Urządzenie B
Urządzenie A Rx  <─────────  Tx Urządzenie B
```

Jeżeli link nie wstaje, jedną z pierwszych rzeczy do sprawdzenia jest zamiana włókien Tx/Rx.

### Wkładki SFP i SFP+

Wkładka optyczna zamienia sygnał elektryczny urządzenia na światło i odwrotnie.

| Rodzina | Typowa maksymalna szybkość |
|---|---:|
| SFP | 1 Gb/s |
| SFP+ | 10 Gb/s |
| SFP28 | 25 Gb/s |
| QSFP+ | 40 Gb/s |
| QSFP28 | 100 Gb/s |

Przed doborem wkładki sprawdź:

1. obsługiwaną szybkość portu,
2. typ włókna — SM lub MM,
3. długość fali,
4. maksymalny dystans,
5. rodzaj złącza,
6. liczbę włókien,
7. zgodność wkładki z urządzeniem,
8. budżet mocy optycznej.

Popularne oznaczenia Ethernetu:

| Oznaczenie | Typowe znaczenie |
|---|---|
| 1000BASE-SX | 1 Gb/s, MM, zwykle 850 nm |
| 1000BASE-LX | 1 Gb/s, najczęściej SM, zwykle 1310 nm |
| 10GBASE-SR | 10 Gb/s, MM, zwykle 850 nm |
| 10GBASE-LR | 10 Gb/s, SM, zwykle 1310 nm |

### Tłumienie i budżet mocy

Tłumienie oznacza utratę mocy sygnału. Podaje się je w decybelach — `dB`. Moc optyczną często podaje się w `dBm`.

Na całkowite tłumienie toru wpływają:

- długość włókna,
- złącza i adaptery,
- spawy,
- zgięcia kabla,
- zabrudzenia i uszkodzenia,
- zapas bezpieczeństwa.

Uproszczony budżet mocy:

```text
budżet = minimalna moc nadajnika - czułość odbiornika
```

Przykład:

```text
minimalna moc Tx: -3 dBm
czułość Rx:      -18 dBm
budżet: (-3) - (-18) = 15 dB
```

Suma strat toru musi być mniejsza od dostępnego budżetu, z pozostawieniem zapasu.

### Łączenie włókien

Najczęstsze metody:

- spawanie termiczne — włókna są precyzyjnie ustawiane i stapiane w spawarce,
- spaw mechaniczny — włókna są ustawiane i łączone mechanicznie,
- gotowe złącza i patchcordy.

Podstawowa kolejność wykonania spawu termicznego:

1. zdejmij powłokę odpowiednim stripperem,
2. wyczyść włókno alkoholem izopropylowym,
3. przytnij włókno cleaverem pod właściwym kątem,
4. umieść włókna w spawarce,
5. wykonaj spaw i sprawdź oszacowane tłumienie,
6. zabezpiecz spaw osłonką termokurczliwą,
7. ułóż włókno w kasecie z zachowaniem promienia gięcia.

### Pomiary i diagnostyka

| Przyrząd | Zastosowanie |
|---|---|
| VFL | czerwone światło widzialne; wykrywanie przerw i mocnych zgięć na krótkim odcinku |
| miernik mocy optycznej | pomiar odebranej mocy w dBm |
| źródło światła | wysyła sygnał o określonej długości fali do pomiaru tłumienia |
| OTDR | pokazuje zdarzenia i ich odległość: spawy, złącza, zgięcia i przerwy |
| mikroskop/inspektor | kontrola czystości czoła złącza |

Podstawowa diagnostyka niedziałającego linku:

1. sprawdź zgodność wkładek po obu stronach,
2. sprawdź SM/MM i długość fali,
3. sprawdź połączenie Tx ↔ Rx,
4. obejrzyj i wyczyść złącza,
5. sprawdź, czy kabel nie jest zbyt mocno zgięty,
6. zmierz moc optyczną,
7. w razie potrzeby wykonaj pomiar OTDR.

### Czyszczenie złączy

Brud na czole ferruli jest jedną z najczęstszych przyczyn problemów. Obowiązuje zasada:

```text
Inspect → Clean → Inspect → Connect
Sprawdź → Wyczyść → Sprawdź ponownie → Połącz
```

Używaj przeznaczonych do tego czyścików, bezpyłowych materiałów i odpowiedniego alkoholu. Nie dotykaj palcem czoła złącza.

### Bezpieczeństwo pracy

- Nigdy nie patrz w koniec włókna ani w port optyczny. Niewidzialne promieniowanie może uszkodzić wzrok.
- Do sprawdzania używaj miernika lub inspektora przeznaczonego do światłowodów.
- Odłącz źródło światła przed czyszczeniem i pracami montażowymi.
- Odłamki włókna są bardzo ostre. Odkładaj je do zamykanego pojemnika.
- Nie jedz i nie pij przy stanowisku do spawania.
- Używaj okularów ochronnych i utrzymuj czyste stanowisko.
- Przestrzegaj minimalnego promienia gięcia podanego przez producenta kabla.

## 21. Test powtórkowy

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
21. Zamień `1010100₂` na system dziesiętny metodą sumy wag.
22. Zamień `53₁₀` na system binarny metodą odejmowania wag.
23. Jaki wildcard dopasuje wszystkie pliki kończące się na `.txt`?
24. Jaka jest różnica między wildcardami `*` i `?`?
25. Utwórz pięć pustych plików od `raport1.txt` do `raport5.txt`.
26. Skopiuj rekurencyjnie katalog `projekt` jako `projekt_kopia`.
27. Usuń katalog `stary` wraz z zawartością, ale pytaj przed usuwaniem.
28. Wyświetl szczegółową listę plików przez program `less` przy użyciu pipe’a.
29. Policz linie zawierające słowo `error` w pliku `aplikacja.log`, ignorując wielkość liter.
30. Posortuj plik `imiona.txt`, usuń powtórzenia i policz wystąpienia.
31. Czym różnią się operatory `>`, `>>` i `|`?
32. Pokaż wynik `ip addr` i jednocześnie zapisz go do `adresy.txt`.
33. Czym różni się światłowód jednomodowy od wielomodowego?
34. Co oznaczają skróty `Tx` i `Rx`?
35. Dlaczego nie należy łączyć złącza UPC ze złączem APC?
36. Jakie typowe zastosowanie mają długości fal 850 nm i 1310 nm?
37. Do czego służy OTDR?
38. Co należy sprawdzić przed dobraniem wkładki SFP?
39. Oblicz budżet mocy dla minimalnego Tx `-3 dBm` i czułości Rx `-18 dBm`.
40. Podaj najważniejszą zasadę bezpieczeństwa podczas pracy ze światłowodem.

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
21. `64 + 16 + 4 = 84`, więc `1010100₂ = 84₁₀`.
22. Wagi `32 + 16 + 4 + 1 = 53`, więc `53₁₀ = 110101₂`.
23. `*.txt`.
24. `*` dopasowuje dowolną liczbę znaków, a `?` dokładnie jeden znak.
25. `touch raport{1..5}.txt`.
26. `cp -r projekt projekt_kopia`.
27. `rm -ri stary`.
28. `ls -la | less`.
29. `grep -i "error" aplikacja.log | wc -l`.
30. `sort imiona.txt | uniq -c`.
31. `>` nadpisuje plik, `>>` dopisuje do pliku, a `|` przekazuje wyjście do następnego polecenia.
32. `ip addr | tee adresy.txt`.
33. Jednomodowy ma znacznie mniejszy rdzeń, prowadzi jeden mod i nadaje się na większe odległości; wielomodowy prowadzi wiele modów i jest typowy dla krótszych połączeń.
34. `Tx` to nadajnik, a `Rx` to odbiornik. W duplexie Tx jednej strony łączy się z Rx drugiej.
35. Mają inaczej wypolerowane czoła ferruli. Połączenie powoduje niedopasowanie, większe tłumienie i odbicia.
36. `850 nm` jest typowe głównie dla MM, a `1310 nm` często dla transmisji SM.
37. Do lokalizowania i mierzenia zdarzeń w torze — spawów, złączy, zgięć oraz przerw — wraz z ich odległością.
38. Szybkość portu, SM/MM, długość fali, dystans, złącze, liczbę włókien, zgodność z urządzeniem oraz budżet mocy.
39. `(-3) - (-18) = 15 dB`.
40. Nigdy nie patrzeć w koniec włókna ani port optyczny; do kontroli używać odpowiedniego miernika lub inspektora.
