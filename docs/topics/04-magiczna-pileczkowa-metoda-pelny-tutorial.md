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

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./03-groupadd-tworzenie-grup.md) · [Następny temat →](./05-systemy-liczbowe-bin-dec-i-hex.md)
