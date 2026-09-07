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

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./11-windows-grupy-przez-net-localgroup.md) · [Następny temat →](./13-windows-icacls-i-uprawnienia-ntfs.md)
