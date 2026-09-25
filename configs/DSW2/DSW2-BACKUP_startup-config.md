# DSW2-BACKUP — Core / Distribution Secondary Switch

## Descrição Geral

O **`DSW2-BACKUP`** é o switch de camada de distribuição/core secundário da infraestrutura de campus[cite: 11]. O seu objetivo principal é garantir **alta disponibilidade e redundância de Camada 2**, atuando como ponte de backup para o Spanning Tree e mantendo os caminhos ativos caso o switch principal (`DSW1-CORE`) fique indisponível[cite: 11].

---

## Papel no Design e Arquitetura da Rede

- **Redundância Spanning Tree (Rapid PVST+):** Configurado como **Secondary Root Bridge** com prioridade `28672` em todas as VLANs (`10, 20, 30, 40, 50, 60, 99, 999`), ficando preparado para assumir a raiz do STP se o `DSW1-CORE` falhar[cite: 11].
- **Agregação de Interligação (LACP EtherChannel):** As portas `Gi1/0/23` e `Gi1/0/24` formam o canal lógico `Port-channel1` com o protocolo LACP em modo ativo para interligar os dois switches core[cite: 11].
- **Hardening e Isolamento de Trunks:** Todas as ligações de trunk (`Port-channel1`, `Gi1/0/2`, `Gi1/0/3` e `Gi1/0/4`) utilizam a `VLAN 999` como VLAN Nativa (estratégia de *blackhole*) e aplicam listas estritas de VLANs permitidas (*allowed VLANs*)[cite: 11].
- **Gestão Segura (Out-of-Band SVI):** Possui um endereço IP fixo na `VLAN 99` (`10.20.99.12/24`) com gateway predefinido apontado para o core principal (`10.20.99.11`)[cite: 11]. O acesso remoto está restrito a **SSH v2** com autenticação local[cite: 11].
- **Mitigação de Ataques:** As portas sem uso estão explicitamente desativadas (`shutdown`) e associadas à `VLAN 999`[cite: 11].

---

## Mapeamento de Interfaces

| Interface | Descrição / Destino | Modo Operacional | VLAN Nativa | VLANs Permitidas | Estado |
|:---|:---|:---|:---:|:---|:---:|
| `Port-channel1` | Agregação LACP para `DSW1-CORE` | Trunk | 999 | 10,20,30,40,50,60,99,999 | Active[cite: 11] |
| `Gi1/0/1` | Porta não utilizada | Access | 999 | — | Disabled (`shutdown`)[cite: 11] |
| `Gi1/0/2` | Trunk para `ASW1-FLOOR1` | Trunk | 999 | 10,30,50,99,999 | Active[cite: 11] |
| `Gi1/0/3` | Trunk para `ASW2-FLOOR2` | Trunk | 999 | 20,60,99,999 | Active[cite: 11] |
| `Gi1/0/4` | Trunk para `ASW3-SERVERS` | Trunk | 999 | 40,99,999 | Active[cite: 11] |
| `Gi1/0/5` – `Gi1/0/22` | Portas não utilizadas | Access | 999 | — | Disabled (`shutdown`)[cite: 11] |
| `Gi1/0/23` | Membro LACP (`Port-channel1`) | Trunk | 999 | 10,20,30,40,50,60,99,999 | Active[cite: 11] |
| `Gi1/0/24` | Membro LACP (`Port-channel1`) | Trunk | 999 | 10,20,30,40,50,60,99,999 | Active[cite: 11] |
| `Vlan99` | Interface SVI de Gestão | L3 Interface | N/A | `10.20.99.12/24` | Up[cite: 11] |

---

## Configuração de Arranque Extraída (`DSW2-BACKUP`)

```text
!
version 16.3.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname DSW2-BACKUP
!
no profinet
enable secret 5 $1$mERr$16XWnNeROA.vjE/XZL8LP1
!
no ip cef
no ipv6 cef
!
username admin privilege 15 secret 5 $1$mERr$slUmKS/I4gB0NqnKSWU5y0
!
ip ssh version 2
no ip domain-lookup
ip domain-name campus.local
!
spanning-tree mode rapid-pvst
spanning-tree vlan 10,20,30,40,50,60,99,999 priority 28672
!
interface Port-channel1
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,40,50,60,99,999
 switchport mode trunk
!
interface GigabitEthernet1/0/1
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/2
 description Trunk para ASW1-FLOOR1
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,30,50,99,999
 switchport mode trunk
!
interface GigabitEthernet1/0/3
 description Trunk para ASW2-FLOOR2
 switchport trunk native vlan 999
 switchport trunk allowed vlan 20,60,99,999
 switchport mode trunk
!
interface GigabitEthernet1/0/4
 description Trunk para ASW3-SERVERS
 switchport trunk native vlan 999
 switchport trunk allowed vlan 40,99,999
 switchport mode trunk
!
interface GigabitEthernet1/0/5
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/6
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/7
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/8
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/9
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/10
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/11
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/12
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/13
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/14
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/15
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/16
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/17
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/18
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/19
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/20
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/21
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/22
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/23
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,40,50,60,99,999
 switchport mode trunk
 channel-protocol lacp
 channel-group 1 mode active
!
interface GigabitEthernet1/0/24
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,40,50,60,99,999
 switchport mode trunk
 channel-protocol lacp
 channel-group 1 mode active
!
interface GigabitEthernet1/1/1
!
interface GigabitEthernet1/1/2
!
interface GigabitEthernet1/1/3
!
interface GigabitEthernet1/1/4
!
interface Vlan1
 no ip address
 shutdown
!
interface Vlan99
 mac-address 0001.6326.e601
 ip address 10.20.99.12 255.255.255.0
!
ip default-gateway 10.20.99.11
ip classless
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