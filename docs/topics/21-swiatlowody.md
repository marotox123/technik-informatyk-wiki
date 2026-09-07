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

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./20-koncowa-sciaga.md) · [Następny temat →](./22-test-powtorkowy.md)
