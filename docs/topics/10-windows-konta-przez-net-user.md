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

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./09-diagnostyka-sieci-w-windows.md) · [Następny temat →](./11-windows-grupy-przez-net-localgroup.md)
