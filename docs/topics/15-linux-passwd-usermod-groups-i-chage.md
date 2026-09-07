## 14. Linux — passwd, usermod, groups i chage

```bash
sudo passwd ania
sudo passwd -l ania
sudo passwd -u ania
```

`passwd -l` blokuje hasło, a `passwd -u` je odblokowuje. Nie zawsze oznacza to całkowite zablokowanie wszystkich możliwych metod logowania do konta.

Najważniejsze przykłady `usermod`:

```bash
sudo usermod -aG projekt ania
sudo usermod -g uczniowie ania
sudo usermod -s /bin/bash ania
sudo usermod -d /home/anna -m ania
sudo usermod -c "Anna Nowak" ania
sudo usermod -L ania
sudo usermod -U ania
```

Przy grupach dodatkowych pamiętaj o `-aG`. Samo `-G` może zastąpić dotychczasową listę grup dodatkowych.

```bash
groups ania
id ania
getent passwd ania
getent group projekt
```

Ważność hasła:

```bash
sudo chage -M 90 ania
sudo chage -W 7 ania
sudo chage -l ania
```

- `chage -M 90` ustawia maksymalny wiek hasła na 90 dni.
- `chage -W 7` ostrzega siedem dni przed wygaśnięciem.
- `chage -l` pokazuje aktualne ustawienia.

---

[← Spis tematów](../README.md) · [← Poprzedni temat](./14-windows-drukarka-przez-port-tcp-ip.md) · [Następny temat →](./16-linux-chmod-chown-i-chgrp.md)
