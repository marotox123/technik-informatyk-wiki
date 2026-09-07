## 1. Netplan — poprawny YAML do wklejenia

Pliki Netplanu znajdują się zazwyczaj w katalogu:

```bash
/etc/netplan/
```

Najpierw sprawdź nazwę interfejsu sieciowego:

```bash
ip link
```

Może to być na przykład `enp0s3`, `ens18` albo `eth0`. W poniższych przykładach używam `enp0s3`.

### DHCP — automatyczny adres IPv4

Otwórz plik:

```bash
sudo nano /etc/netplan/01-szkola.yaml
```

Wklej:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: true
```

### Statyczny adres IPv4

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: false
      addresses:
        - 192.168.10.20/24
      routes:
        - to: default
          via: 192.168.10.1
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
        search:
          - szkola.local
```

W YAML-u używaj spacji, a nie tabulatorów. Zachowaj dokładnie pokazane wcięcia.

### Sprawdzenie i zastosowanie konfiguracji

```bash
sudo netplan generate
sudo netplan try
sudo netplan apply
```

Następnie sprawdź wynik:

```bash
ip addr show enp0s3
ip route
resolvectl status
ping -c 4 192.168.10.1
ping -c 4 1.1.1.1
```

`netplan generate` sprawdza i generuje konfigurację. `netplan try` tymczasowo ją uruchamia i pozwala ją cofnąć, jeśli stracisz połączenie. `netplan apply` stosuje konfigurację.

---

[← Spis tematów](../README.md) · [Następny temat →](./02-linux-tworzenie-uzytkownikow-przez-useradd.md)
