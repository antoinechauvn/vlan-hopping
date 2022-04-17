# VLAN Hopping


### Qu'est-ce que l'attaque VLAN Hopping?
Le saut de VLAN est un exploit de sécurité informatique , une méthode d'attaque des ressources en réseau sur un LAN virtuel (VLAN).
Le concept de base derrière toutes les attaques par saut de VLAN est qu'un hôte attaquant sur un VLAN accède au trafic sur d'autres VLAN qui ne seraient normalement pas accessibles. Il existe deux méthodes principales de saut de VLAN : l'usurpation de commutateur et le double marquage.

# Méthodes d'attaques

## Switch spoofing

```
L'usurpation de commutateur est un type d'attaque par saut de VLAN tirant parti d'un port trunk configuré de
manière incorrecte. Par défaut, les ports trunk ont accès à tous les VLAN et acheminent le trafic de
plusieurs VLAN sur la même liaison physique, généralement entre des commutateurs. Dans une attaque de base
d'usurpation de commutateur, le pirate tire parti du fait que la configuration par défaut du port du
commutateur est dynamique automatique. Il configure un système afin de se faire passer pour un commutateur.
Cette usurpation exige que le pirate soit capable d'émuler 802.1Q et les messages DTP. En amenant un
commutateur à penser qu'un autre commutateur tente de former un trunk, un pirate peut accéder à tous les
VLAN autorisés sur le port trunk
```
![VLAN-SPOOF1](https://user-images.githubusercontent.com/83721477/163724494-9164b7ba-1691-4dd0-9bb6-a0517308f385.png)
___
![VLAN-SPOOF2](https://user-images.githubusercontent.com/83721477/163724627-cc972305-bf52-4102-bcdf-ff0b2532d6a2.png)
___
![VLAN-SPOOF3](https://user-images.githubusercontent.com/83721477/163724759-5c1c153e-a023-4428-b726-2eab1405165d.png)

1. On commence par envoyer des trames DTP sur un port Access
2. Si le mode DTP est en DYNAMIC AUTO ou DYNAMIC DESIRABLE alors l'attaque est possible
3. On envoie une demande de négociation pour basculer le lien en mode trunk

### Remédiations
La technique de Switch Spoofing n'est exploitable que lorsque les interfaces d'un switch sont configurés pour négocier une jonction (trunk).

La première chose à faire est de désactiver le DTP<br>
`switchport nonegotiate`

Ensuite, il faut s'assurer que les ports non configurés en jonction sont configurés en port d'accès<br>
`switchport mode access`

## Double-Tagging

```
L'attaque double-tagging (ou double encapsulation) est un autre type d'attaque par saut de VLAN. Ce type
d'attaque tire parti de la manière dont le matériel de la plupart des commutateurs fonctionne. La majorité
des commutateurs réalisent un seul niveau de désencapsulation 802.1Q, ce qui permet à un pirate d'intégrer
un tag 802.1Q masquée à l'intérieur de la trame. Ce tag permet à la trame d'être transférée vers un VLAN
que le tag 802.1Q d'origine n'a pas spécifié. Il est important de noter que l'attaque par saut de VLAN de
double encapsulation fonctionne même si les ports trunk sont désactivés, car un hôte envoie généralement
une trame sur un segment qui n'est pas une liaison trunk.
```
![DOUBLE-TAGGING1](https://user-images.githubusercontent.com/83721477/163726501-e83d0cf9-9498-4e44-a0c1-e65f143748ac.png)
___
![DOUBLE-TAGGING2](https://user-images.githubusercontent.com/83721477/163726708-1e2ad643-6e2f-41dc-a896-5f1d29d3d786.png)
___
![DOUBLE-TAGGING3](https://user-images.githubusercontent.com/83721477/163726750-a8888e07-da47-4653-ba69-071530a256e1.png)
___
![DOUBLE-TAGGING4](https://user-images.githubusercontent.com/83721477/163726808-3f579887-4a35-4b3a-850b-d104994fc5d5.png)

1. Le pirate envoie une trame 802.1Q marquée de deux tags au commutateur. L'en-tête externe porte le tag VLAN du pirate, qui est identique au VLAN natif du port trunk. Supposons que le commutateur traite la trame envoyée par le pirate comme si elle se trouvait sur un port trunk ou un port disposant d'un VLAN voix (un commutateur ne doit pas recevoir de trame Ethernet avec un tag sur un port d'accès). Dans cet exemple, supposons que le VLAN natif est le VLAN 1. Le tag interne est le VLAN victime ; dans ce cas, il s'agit du VLAN 2.
2. La trame arrive sur le commutateur, qui vérifie le premier tag 802.1Q de 4 octets. Le commutateur détecte que la trame est destinée au VLAN 1, qui est le VLAN natif. Le commutateur transfère le paquet par tous les ports du VLAN 1 après avoir éliminé le tag du VLAN 1. Sur le port trunk, le tag du VLAN 1 est éliminée et le paquet n'est pas taggué de nouveau, car il fait partie du VLAN natif. À ce stade, le tag du VLAN 2 reste inchangée et n'a pas été inspectée par le premier commutateur.
3. Le deuxième commutateur examine uniquement le tag 802.1Q interne que le pirate a envoyée et constate que la trame est destinée au VLAN 2, le VLAN cible. Il envoie la trame sur le port victime ou la diffuse, selon qu'il existe une entrée de table d'adresse MAC pour l'hôte victime.

*Note: Ce type d'attaque est unidirectionnel et ne fonctionne que si le pirate est connecté à un port se trouvant dans le même VLAN que le VLAN natif du port trunk. Ce type d'attaque est plus difficile à contrer que les simples attaques par saut de VLAN.*

### Remédiations

La technique de Double Tagging n'est exploitable que sur les ports de switch configurés pour utiliser des VLAN natifs.<br>
Il est important de ne pas utiliser le VLAN par défaut (VLAN 1)<br>
`switchport access vlan 2`

Vous pouvez aussi remplacer le VLAN natif de tous les ports en mode trunk par un ID de VLAN inutilisé<br>
`switchport trunk native vlan 999`

Vous pouvez également forcer le marquage explicite du VLAN natif sur tous les ports en mode trunk<br>
`vlan dot1q tag native`
