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

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./12-windows-mkdir-net-share-i-net-use.md) · [Następny temat →](./14-windows-drukarka-przez-port-tcp-ip.md)
