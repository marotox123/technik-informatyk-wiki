## Ethernet i okablowanie miedziane

### Czym jest Ethernet

Ethernet to rodzina technologii sieci lokalnych opisana głównie przez standardy IEEE 802.3. Określa sposób przesyłania ramek, format danych oraz wiele odmian warstwy fizycznej — zarówno dla kabli miedzianych, jak i światłowodów.

W typowej sieci Ethernet:

- karta sieciowa ma adres MAC,
- dane warstwy drugiej są przesyłane w ramkach,
- switch uczy się, na których portach znajdują się określone adresy MAC,
- switch przekazuje ramkę do odpowiedniego portu lub rozsyła ją, jeśli nie zna jeszcze adresu docelowego,
- broadcast jest rozsyłany do wszystkich portów w danej domenie rozgłoszeniowej, z wyjątkiem portu wejściowego.

### Ramka Ethernet

Najważniejsze pola ramki:

| Pole | Znaczenie |
|---|---|
| preambuła i SFD | synchronizacja i oznaczenie początku ramki |
| docelowy MAC | adres odbiorcy |
| źródłowy MAC | adres nadawcy |
| EtherType/długość | informacja o przenoszonym protokole lub długości |
| dane | przenoszony pakiet, np. IPv4 |
| FCS | suma kontrolna wykrywająca uszkodzenie ramki |

Standardowy adres MAC ma 48 bitów i jest zwykle zapisywany jako sześć par cyfr szesnastkowych:

```text
00:1A:2B:3C:4D:5E
```

### Hub, switch i domeny kolizyjne

- Hub powtarza sygnał na wszystkie porty. Wszystkie urządzenia współdzielą jedną domenę kolizyjną.
- Switch tworzy osobną domenę kolizyjną dla każdego portu.
- Router rozdziela domeny rozgłoszeniowe.
- VLAN pozwala logicznie podzielić switch na różne domeny rozgłoszeniowe.

Historyczny mechanizm CSMA/CD wykrywał kolizje w sieciach współdzielonych i half-duplex. W nowoczesnym przełączanym Ethernecie full-duplex kolizje praktycznie nie występują.

### Half-duplex i full-duplex

| Tryb | Działanie |
|---|---|
| half-duplex | urządzenie w danej chwili nadaje albo odbiera |
| full-duplex | urządzenie może jednocześnie nadawać i odbierać |

Niezgodność ustawień duplex może powodować bardzo niską wydajność, błędy i retransmisje. Zwykle używa się autonegocjacji po obu stronach.

### Najczęstsze odmiany Ethernetu po miedzi

| Standard | Szybkość | Typowe medium | Maksymalna długość segmentu |
|---|---:|---|---:|
| 10BASE-T | 10 Mb/s | skrętka kat. 3 lub lepsza | 100 m |
| 100BASE-TX | 100 Mb/s | kat. 5 lub lepsza | 100 m |
| 1000BASE-T | 1 Gb/s | kat. 5e lub lepsza | 100 m |
| 2.5GBASE-T | 2,5 Gb/s | zwykle kat. 5e lub lepsza | do 100 m |
| 5GBASE-T | 5 Gb/s | zwykle kat. 6 lub lepsza | do 100 m zależnie od instalacji |
| 10GBASE-T | 10 Gb/s | kat. 6A do 100 m; kat. 6 krócej | do 100 m |

Rzeczywista możliwość transmisji zależy od kategorii, jakości wykonania, zakłóceń i zgodności całego toru.

### Skrętka — dlaczego przewody są skręcone

Przewody są skręcone w pary, aby ograniczać zakłócenia elektromagnetyczne i przesłuchy między parami. Każda para ma inny skok skrętu.

W kablu czteroparowym znajdują się pary:

1. biało-pomarańczowa i pomarańczowa,
2. biało-zielona i zielona,
3. biało-niebieska i niebieska,
4. biało-brązowa i brązowa.

Nie wolno nadmiernie rozkręcać par przy zarabianiu kabla, ponieważ pogarsza to parametry transmisyjne.

### Ekranowanie kabli

W oznaczeniu `X/YTP` pierwsza część opisuje ekran całego kabla, a druga ekranowanie par.

| Oznaczenie | Budowa |
|---|---|
| U/UTP | brak ekranu całego kabla i brak ekranów par |
| F/UTP | folia wokół wszystkich par, pary bez osobnych ekranów |
| S/UTP | oplot wokół wszystkich par |
| U/FTP | brak wspólnego ekranu, każda para w folii |
| F/FTP | wspólna folia i osobna folia na każdej parze |
| S/FTP | wspólny oplot i osobna folia na każdej parze |

Ekran ma sens tylko wtedy, gdy został poprawnie zakończony i uziemiony zgodnie z projektem. Błędne wykonanie może nie zapewnić oczekiwanej ochrony.

### Kategorie skrętki

| Kategoria | Typowe zastosowanie |
|---|---|
| Cat 5e | najczęściej 1 Gb/s do 100 m |
| Cat 6 | 1 Gb/s do 100 m; 10 Gb/s na krótszych odcinkach |
| Cat 6A | 10 Gb/s do 100 m |
| Cat 7/7A | kable ekranowane; zastosowania zależne od systemu okablowania |
| Cat 8 | bardzo szybkie, krótkie połączenia, głównie centra danych |

Kategoria całego toru nie jest wyższa niż kategoria jego najsłabszego elementu: kabla, modułu, patch panelu, gniazda lub patchcordu.

### Kanał 100 metrów

Typowy kanał poziomy Ethernetu po skrętce ma maksymalnie 100 m:

```text
90 m stałego okablowania poziomego + łącznie 10 m patchcordów = 100 m
```

Nie jest to reguła dla każdego możliwego standardu, lecz podstawowa zasada typowego okablowania strukturalnego.

### Kabel prosty i krosowany

- prosty: ten sam standard, np. T568B–T568B,
- krosowany: T568A–T568B,
- rollover/console: inny układ używany w niektórych połączeniach konsolowych; nie jest zwykłym kablem Ethernet.

Auto MDI-X pozwala wielu współczesnym portom automatycznie rozpoznać pary nadawcze i odbiorcze, ale nadal trzeba znać różnicę między kablem prostym i krosowanym.

### Patchcord, kabel instalacyjny i patch panel

| Element | Zastosowanie |
|---|---|
| kabel instalacyjny — drut | stałe okablowanie w ścianach i trasach kablowych |
| patchcord — linka | elastyczne połączenie urządzenia z gniazdem lub panelem |
| patch panel | zakończenie i uporządkowanie kabli instalacyjnych w szafie |
| keystone/gniazdo | moduł kończący kabel instalacyjny |
| organizer kabli | prowadzenie patchcordów i zachowanie porządku |

Kabla typu drut zwykle nie używa się jako często zginanego patchcordu, a kabla typu linka nie traktuje się jako typowego stałego okablowania poziomego.

### PoE — zasilanie przez Ethernet

PoE pozwala przesyłać dane i zasilanie tym samym przewodem Ethernet. Stosuje się je np. do punktów dostępowych, kamer IP i telefonów VoIP.

| Standard | Nazwa | Maksymalna moc dostarczana przez urządzenie zasilające — orientacyjnie |
|---|---|---:|
| IEEE 802.3af | PoE | 15,4 W |
| IEEE 802.3at | PoE+ | 30 W |
| IEEE 802.3bt Type 3 | PoE++ | 60 W |
| IEEE 802.3bt Type 4 | PoE++ | 90 W |

- PSE to urządzenie dostarczające zasilanie, np. switch PoE.
- PD to urządzenie zasilane, np. kamera.
- Trzeba sprawdzić standard, budżet mocy switcha i wymagania odbiornika.

### Testowanie kabla miedzianego

Prosty tester ciągłości sprawdza kolejność żył, przerwy, zwarcia i zamianę par. Certyfikator mierzy parametry wymagane dla danej kategorii.

Typowe problemy:

- przerwana żyła,
- zwarcie,
- zamienione żyły,
- split pair — żyły mają poprawną kolejność elektryczną, ale pochodzą z niewłaściwych par,
- zbyt mocne rozkręcenie par,
- załamanie lub zgniecenie kabla,
- przekroczenie długości,
- złe ekranowanie lub uziemienie,
- uszkodzone gniazdo albo patchcord.

### Diagnostyka połączenia Ethernet

1. Sprawdź wpięcie przewodu i diody link/activity.
2. Podmień patchcord na sprawny.
3. Sprawdź port switcha i kartę sieciową.
4. Sprawdź wynegocjowaną szybkość i duplex.
5. Sprawdź testerem mapę żył.
6. Sprawdź adres IP, maskę, bramę i DHCP.
7. Sprawdź liczniki błędów interfejsu.

Linux:

```bash
ip link
ip -s link show enp0s3
ethtool enp0s3
```

Windows PowerShell:

```powershell
Get-NetAdapter
Get-NetAdapterStatistics
```

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./06-podstawy-sieci-komputerowych.md) · [Następny temat →](./08-okablowanie-sieciowe.md)
