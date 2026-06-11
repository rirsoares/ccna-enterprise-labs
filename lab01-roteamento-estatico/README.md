# Lab 01 \- Roteamento Estatico entre Duas Redes

## Objetivo

Interconectar dois segmentos de rede distintos (192.168.1.0/24 e 192.168.2.0/24) atraves de dois roteadores Cisco (vIOS), utilizando rotas estaticas, e validar a conectividade fim-a-fim entre dois hosts (PC1 e PC2).

## Topologia

PC1 \--- (Gi0/1) R1 (Gi0/0) \--- (Gi0/0) R2 (Gi0/1) \--- PC2

192.168.1.0/24      10.0.0.0/30        192.168.2.0/24

*(adicione aqui a imagem `topologia.png` com o print do PNETLab)*

## Equipamentos

| Dispositivo | Tipo | Imagem |
| :---- | :---- | :---- |
| R1, R2 | Roteador | Cisco vIOS (adventerprisek9-m) |
| PC1, PC2 | Host | Virtual PC (VPCS) |

## Plano de Endereçamento IP

| Dispositivo | Interface | Endereço IP | Gateway |
| :---- | :---- | :---- | :---- |
| PC1 | eth0 | 192.168.1.10/24 | 192.168.1.1 |
| R1 | GigabitEthernet0/1 | 192.168.1.1/24 | \- |
| R1 | GigabitEthernet0/0 | 10.0.0.1/30 | \- |
| R2 | GigabitEthernet0/0 | 10.0.0.2/30 | \- |
| R2 | GigabitEthernet0/1 | 192.168.2.1/24 | \- |
| PC2 | eth0 | 192.168.2.10/24 | 192.168.2.1 |

## Configuração

### R1

conf t

hostname R1

interface GigabitEthernet0/1

 ip address 192.168.1.1 255.255.255.0

 no shutdown

interface GigabitEthernet0/0

 ip address 10.0.0.1 255.255.255.252

 no shutdown

ip route 192.168.2.0 255.255.255.0 10.0.0.2

end

write memory

### R2

conf t

hostname R2

interface GigabitEthernet0/0

 ip address 10.0.0.2 255.255.255.252

 no shutdown

interface GigabitEthernet0/1

 ip address 192.168.2.1 255.255.255.0

 no shutdown

ip route 192.168.1.0 255.255.255.0 10.0.0.1

end

write memory

### PC1

ip 192.168.1.10/24 192.168.1.1

### PC2

ip 192.168.2.10/24 192.168.2.1

## Verificação

### Tabela de roteamento \- R1

R1\#sh ip route

Codes: L \- local, C \- connected, S \- static, ...

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks

C        10.0.0.0/30 is directly connected, GigabitEthernet0/0

L        10.0.0.1/32 is directly connected, GigabitEthernet0/0

      192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks

C        192.168.1.0/24 is directly connected, GigabitEthernet0/1

L        192.168.1.1/32 is directly connected, GigabitEthernet0/1

S     192.168.2.0/24 \[1/0\] via 10.0.0.2

### Tabela de roteamento \- R2

R2\#sh ip route

Codes: L \- local, C \- connected, S \- static, ...

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks

C        10.0.0.0/30 is directly connected, GigabitEthernet0/0

L        10.0.0.2/32 is directly connected, GigabitEthernet0/0

S     192.168.1.0/24 \[1/0\] via 10.0.0.1

      192.168.2.0/24 is variably subnetted, 2 subnets, 2 masks

C        192.168.2.0/24 is directly connected, GigabitEthernet0/1

L        192.168.2.1/32 is directly connected, GigabitEthernet0/1

### Teste de conectividade \- PC1 \-\> PC2

PC1\> ping 192.168.2.10

84 bytes from 192.168.2.10 icmp\_seq=1 ttl=62 time=10.908 ms

84 bytes from 192.168.2.10 icmp\_seq=2 ttl=62 time=2.537 ms

84 bytes from 192.168.2.10 icmp\_seq=3 ttl=62 time=2.338 ms

84 bytes from 192.168.2.10 icmp\_seq=4 ttl=62 time=3.843 ms

84 bytes from 192.168.2.10 icmp\_seq=5 ttl=62 time=3.722 ms

### Teste de conectividade \- PC2 \-\> PC1

PC2\> ping 192.168.1.10

84 bytes from 192.168.1.10 icmp\_seq=1 ttl=62 time=2.271 ms

84 bytes from 192.168.1.10 icmp\_seq=2 ttl=62 time=3.883 ms

84 bytes from 192.168.1.10 icmp\_seq=3 ttl=62 time=4.079 ms

84 bytes from 192.168.1.10 icmp\_seq=4 ttl=62 time=6.668 ms

84 bytes from 192.168.1.10 icmp\_seq=5 ttl=62 time=3.838 ms

## Resultado

100% de sucesso nos testes de ping entre PC1 e PC2, confirmando que as rotas estáticas configuradas em R1 e R2 estão funcionando corretamente.

## Conceitos praticados

- Configuração de interfaces (IP, máscara, no shutdown)  
- Rotas estáticas (`ip route`)  
- Verificação de tabela de roteamento (`show ip route`)  
- Verificação de interfaces (`show ip int brief`)  
- Configuração de hosts no VPCS

