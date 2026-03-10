
[Documentação](../../documentacao.md) > [How-to](../how-to.md)

# Configuracao da VPN no WSL 2

# Usando WSL pela VPN Cisco Anyconnect

1. Dentro do WSL, adicionar esse conteúdo ao arquivo `/etc/resolv.conf` :

```bash
nameserver 10.128.17.136
nameserver 192.168.207.23
nameserver 8.8.8.8
nameserver 4.4.4.4
nameserver a1-vulcano1.uolcorp.intranet
```

2. Tornar o arquivo imutável, para evitar do WSL sobrescrever:

```bash
sudo chattr -f +i /etc/resolv.conf
```

3. Adicionar esse conteúdo no arquivo /etc/wsl.conf:

```bash
[network]
generateResolvConf=false
```

4. Reiniciar WSL

```bash
wsl --shutdown
wsl
```

5. Sempre que conectar na VPN, rodar comando no Power Shell como Administrador, conectado à VPN:

```bash
Get-NetAdapter | Where-Object {$_.InterfaceDescription -Match "Cisco AnyConnect"} | Set-NetIPInterface -InterfaceMetric 6000
```
