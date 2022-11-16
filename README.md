# NFTables

- Es la evolución de iptables, y, de hecho, las reemplaza y mejora en comparacion con las anteriores..

1. Install | Location file | Status service NFTable

```console
$ sudo apt install nftables
$ cat /etc/nftables.conf
$ sudo service nftables status
```

2. Listar reglas.

```console
$ sudo nft list ruleset
```

3. Crear reglas a partir de un archivo nft

```bash
# Declaramos variables que utilizamos en el script
define RedLocal = 192.168.153.0/24 # Apunta a mi red local.
define updSI    = { 53 }
define tcp      = { 22, 80, 443 }
#Borramos cualquier regla establecida en el sistema
flush ruleset
# Declaramos la tabla "filtrado" asociada a la familia "ip"
# Por 10 tanto estamos solo aplicando estas reglas a IPv4
add table ip filtrado
#add table ip6 filtrado
#add table inet filtrado
# Establecemos política por defecto DROP en las cadenas input, output y forward] 
# asociadas a la tabla filtrado
add chain ip filtrado INPUT     { type filter hook input    priority 0; policy drop; }
add chain ip filtrado FORWARD   { type filter hook forward  priority 0; policy drop; }
add chain ip filtrado OUTPUT    { type filter hook output   priority 0; policy drop; }
# Permitimos cualquier tipo de tráfijo local loopback ( nombre interface lo )
add rule ip filtrado INPUT  iifname lo counter accept
add rule ip filtrado OUTPUT oifname lo counter accept
#Permitimos acceso ssh al servidor desde cualquier máquina de la Red Local
#definida en la variable RedLocal
add rule ip filtrado INPUT ip saddr $RedLocal tcp dport 22 counter accept
add rule ip filtrado OUTPUT ip daddr $RedLocal tcp sport 22 counter accept
# Permitimos todos los protocolos UP definidos en la variable udpsI
add rule ip filtrado OUTPUT udp port $udpSI counter accept
add rule ip filtrado INPUT udp sport $udpSI counter accept
# Permitimos todos los protocolos TCP definidos en la variable tcpsI
add rule ip filtrado OUTPUT tcp dport $tcpSI counter accept
add rule ip filtrado INPUT tcp sport $tcpsI counter accept
```

```console
$ sudo nft -f reglas.nft
```

```console
$ sudo nft list ruleset
```

- Info completa -> <https://www.redeszone.net/tutoriales/seguridad/nftables-firewall-linux-configuracion/>
- Video Youtube -> <https://www.youtube.com/watch?v=A8aRH3oYyR8>
