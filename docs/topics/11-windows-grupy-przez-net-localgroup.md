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

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./10-windows-konta-przez-net-user.md) · [Następny temat →](./12-windows-mkdir-net-share-i-net-use.md)
