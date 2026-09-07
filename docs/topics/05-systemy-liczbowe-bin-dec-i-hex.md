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

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./04-magiczna-pileczkowa-metoda-pelny-tutorial.md) · [Następny temat →](./06-podstawy-sieci-komputerowych.md)
