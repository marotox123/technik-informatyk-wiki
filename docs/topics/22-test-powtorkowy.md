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

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./21-swiatlowody.md)
