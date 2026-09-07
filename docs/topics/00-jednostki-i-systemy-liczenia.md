# Jednostki i systemy liczenia

## Jednostki informacji

W informatyce mała litera `b` oznacza bit, a wielka litera `B` oznacza bajt.

```text
1 b = 1 bit = jedna cyfra dwójkowa, 0 albo 1
1 B = 1 bajt = 8 bitów
```

W jednostkach binarnych każdy kolejny poziom jest 1024 razy większy, czyli o `2^10`.

| Jednostka | Przeliczenie |
|---|---|
| 1 KiB | `1024 B = 2^10 B` |
| 1 MiB | `1024 KiB = 2^20 B = 1 048 576 B` |
| 1 GiB | `1024 MiB = 2^30 B = 1 073 741 824 B` |
| 1 TiB | `1024 GiB = 2^40 B = 1 099 511 627 776 B` |
| 1 PiB | `1024 TiB = 2^50 B = 1 125 899 906 842 624 B` |

Skróty:

```text
KiB — kibibajt
MiB — mebibajt
GiB — gibibajt
TiB — tebibajt
PiB — pebibajt
```

> Nie myl jednostek binarnych `KiB`, `MiB`, `GiB` z dziesiętnymi `kB`, `MB`, `GB`, gdzie przedrostek oznacza mnożnik 1000.

## Binarny system liczbowy

W systemie binarnym występują tylko dwie cyfry: `0` i `1`.

| Dziesiętnie | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Binarnie | 0 | 1 | 10 | 11 | 100 | 101 | 110 | 111 | 1000 | 1001 | 1010 | 1011 | 1100 | 1101 | 1110 | 1111 |

## Zamiana dziesiętnego na binarny — dzielenie przez 2

1. Podziel liczbę przez 2.
2. Zapisz resztę z dzielenia — `0` albo `1`.
3. Wynik całkowity podziel ponownie przez 2.
4. Powtarzaj, aż wynik będzie równy 0.
5. Odczytaj reszty od dołu do góry.

### Przykład: `947₁₀`

| Wynik dzielenia | Reszta |
|---:|---:|
| 947 | 1 |
| 473 | 1 |
| 236 | 0 |
| 118 | 0 |
| 59 | 1 |
| 29 | 1 |
| 14 | 0 |
| 7 | 1 |
| 3 | 1 |
| 1 | 1 |
| 0 | — |

Czytając reszty od dołu:

```text
947₁₀ = 1110110011₂
```

## Zamiana dziesiętnego na binarny — odejmowanie wag

To szybsza metoda, gdy znasz potęgi dwójki:

```text
512, 256, 128, 64, 32, 16, 8, 4, 2, 1
```

Sprawdź każdą wagę od największej. Jeśli mieści się w pozostałej liczbie, wpisz `1` i odejmij ją. Jeśli się nie mieści, wpisz `0`.

### Przykład: `947₁₀`

```text
947 - 512 = 435  → 1
435 - 256 = 179  → 1
179 - 128 = 51   → 1
64 nie mieści się → 0
51 - 32 = 19     → 1
19 - 16 = 3      → 1
8 nie mieści się  → 0
4 nie mieści się  → 0
3 - 2 = 1        → 1
1 - 1 = 0        → 1
```

```text
947₁₀ = 1110110011₂
```

## Zamiana dziesiętnego na szesnastkowy

Dziel przez 16, zapisuj reszty i odczytaj je od dołu. W systemie HEX wartości 10–15 zapisujemy jako `A–F`.

### Przykład: `1593₁₀`

```text
1593 : 16 = 99 reszty 9
99   : 16 = 6  reszty 3
6    : 16 = 0  reszty 6
```

```text
1593₁₀ = 639₁₆
```

## Zamiana szesnastkowego na binarny

Każdą cyfrę HEX zamień na dokładnie cztery bity:

| HEX | BIN | HEX | BIN |
|---|---|---|---|
| 0 | 0000 | 8 | 1000 |
| 1 | 0001 | 9 | 1001 |
| 2 | 0010 | A | 1010 |
| 3 | 0011 | B | 1011 |
| 4 | 0100 | C | 1100 |
| 5 | 0101 | D | 1101 |
| 6 | 0110 | E | 1110 |
| 7 | 0111 | F | 1111 |

Przykład:

```text
A41F5₁₆ = 1010 0100 0001 1111 0101₂
```

## Zamiana binarnego na szesnastkowy

Podziel liczbę binarną od prawej strony na grupy po 4 bity. Jeśli pierwsza grupa jest za krótka, dopisz z lewej zera.

```text
111100101₂ = 0001 1110 0101₂ = 1E5₁₆
```

## Szybka metoda BIN → DEC — suma wag

Wagi od prawej strony to:

```text
128 64 32 16 8 4 2 1
```

Dodaj tylko te wagi, pod którymi stoi `1`.

```text
1010100₂
wagi: 64 32 16 8 4 2 1
bity:  1  0  1 0 1 0 0
64 + 16 + 4 = 84
```

```text
1010100₂ = 84₁₀
```

## Szybka metoda DEC → BIN — odejmowanie wag

Dla `53` sprawdzamy wagi `32, 16, 8, 4, 2, 1`:

```text
53 - 32 = 21 → 1
21 - 16 = 5  → 1
8 nie mieści się → 0
5 - 4 = 1    → 1
2 nie mieści się → 0
1 - 1 = 0    → 1
```

```text
53₁₀ = 110101₂
```

---

[← Spis tematów](../README.md) · [Następny temat →](01-netplan-poprawny-yaml-do-wklejenia.md)
