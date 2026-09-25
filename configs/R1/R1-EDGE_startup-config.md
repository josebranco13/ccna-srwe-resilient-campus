# R1-EDGE — Edge Router

## Descrição Geral

O **`R1-EDGE`** é o router de fronteira (Gateway de Saída) da infraestrutura da rede de campus[cite: 12]. Atua na Camada 3 do modelo OSI, sendo responsável pelo encaminhamento de tráfego entre a rede interna do campus e a saída externa/WAN[cite: 12].

---

## Papel no Design e Arquitetura da Rede

- **Encaminhamento Estático e Agregação de Rotas:** Utiliza uma rota estática apontada para a gama `10.20.0.0/16` com next-hop no `DSW1-CORE` (`10.20.254.2`), agregando todo o tráfego das sub-redes internas (VLANs 10, 20, 30, 40, 50, 60 e 99)[cite: 12].
- **Ligação Ponto-a-Ponto Roteada (Point-to-Point Link):** A interface `Gi0/0` está ligada diretamente à interface `Gi1/0/1` do `DSW1-CORE`, utilizando uma sub-rede restrita `/30` (`10.20.254.0/30`)[cite: 12].
- **Acesso Seguro e Gestão:** Configurado com acesso remoto encriptado via **SSH v2**, autenticação local (`admin`), domain name `campus.local`, e aviso de acesso restrito (MOTD)[cite: 12].
- **Hardening de Interfaces:** As interfaces não utilizadas (`Gi0/1`, `Gi0/2` e `Vlan1`) encontram-se desativadas (`shutdown`) sem endereço IP atribuído[cite: 12].

---

## Mapeamento de Interfaces

| Interface | Descrição / Destino | Tipo de Ligação | Endereço IP / Máscara | Estado |
|:---|:---|:---|:---|:---:|
| `GigabitEthernet0/0` | Link para `DSW1-CORE` (routed) | Routed Link (Point-to-Point) | `10.20.254.1 255.255.255.252` | Active[cite: 12] |
| `GigabitEthernet0/1` | Interface não utilizada | — | Sem IP | Disabled (`shutdown`)[cite: 12] |
| `GigabitEthernet0/2` | Interface não utilizada | — | Sem IP | Disabled (`shutdown`)[cite: 12] |
| `Vlan1` | Interface de Gestão Default | — | Sem IP | Disabled (`shutdown`)[cite: 12] |

---

## Configuração de Arranque Extraída (`R1-EDGE`)

```text
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname R1-EDGE
!
enable secret 5 $1$mERr$16XWnNeROA.vjE/XZL8LP1
!
ip cef
no ipv6 cef
!
username admin privilege 15 secret 5 $1$mERr$slUmKS/I4gB0NqnKSWU5y0
!
license udi pid CISCO2911/K9 sn FTX15244ZA4-
!
ip ssh version 2
no ip domain-lookup
ip domain-name campus.local
!
spanning-tree mode pvst
!
interface GigabitEthernet0/0
 description Link to DSW1-CORE (routed)
 ip address 10.20.254.1 255.255.255.252
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface GigabitEthernet0/2
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
ip route 10.20.0.0 255.255.0.0 10.20.254.2 
!
ip flow-export version 9
!
banner motd #Restricted access - just authorized people#
!
line con 0
 logging synchronous
!
line aux 0
!
line vty 0 4
 login local
 transport input ssh
!
end