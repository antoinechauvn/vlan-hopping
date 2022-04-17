Le saut de VLAN permet à un VLAN de voir le trafic en provenance d'un autre VLAN. L'usurpation de commutateur est un type d'attaque par saut de VLAN tirant parti d'un port trunk configuré de manière incorrecte. Par défaut, les ports trunk ont accès à tous les VLAN et acheminent le trafic de plusieurs VLAN sur la même liaison physique, généralement entre des commutateurs.

Dans la figure, cliquez sur le bouton Lecture pour voir l'animation illustrant une attaque d'usurpation de commutateur.

Dans une attaque de base d'usurpation de commutateur, le pirate tire parti du fait que la configuration par défaut du port du commutateur est dynamique automatique. Il configure un système afin de se faire passer pour un commutateur. Cette usurpation exige que le pirate soit capable d'émuler 802.1Q et les messages DTP. En amenant un commutateur à penser qu'un autre commutateur tente de former un trunk, un pirate peut accéder à tous les VLAN autorisés sur le port trunk.

Voici le déroulé de cette attaque :

On commence par envoyer des trames DTP sur un port Access
Si le mode DTP est en DYNAMIC AUTO ou DYNAMIC DESIRABLE alors l'attaque est possible
On envoie une demande de négociation pour basculer le lien en mode trunk

Remédiations
La technique de Switch Spoofing n'est exploitable que lorsque les interfaces d'un switch sont configurés pour négocier une jonction.

La première chose à faire est de désactiver le DTP : switchport nonegotiate

Ensuite, il faut s'assurer que les ports non configurés en jonction sont configurés en port d'accès : switchport mode access


![Capture d’écran 2022-04-17 114556](https://user-images.githubusercontent.com/83721477/163709246-3e6972e8-23dd-4725-9d61-8efb7976454a.png)

![image](https://user-images.githubusercontent.com/83721477/163709303-2856b6b9-dcbb-4c37-bfa6-80bc4cec2bc7.png)


L'attaque double-tagging (ou double encapsulation) est un autre type d'attaque par saut de VLAN. Ce type d'attaque tire parti de la manière dont le matériel de la plupart des commutateurs fonctionne. La majorité des commutateurs réalisent un seul niveau de désencapsulation 802.1Q, ce qui permet à un pirate d'intégrer une étiquette 802.1Q masquée à l'intérieur de la trame. Cette étiquette permet à la trame d'être transférée vers un VLAN que l'étiquette 802.1Q d'origine n'a pas spécifié. Il est important de noter que l'attaque par saut de VLAN de double encapsulation fonctionne même si les ports trunk sont désactivés, car un hôte envoie généralement une trame sur un segment qui n'est pas une liaison trunk.

Une attaque par saut de VLAN double-tagging se déroule en trois étapes :

1. Le pirate envoie une trame 802.1Q marquée de deux étiquettes au commutateur. L'en-tête externe porte l'étiquette VLAN du pirate, qui est identique au VLAN natif du port trunk. Supposons que le commutateur traite la trame envoyée par le pirate comme si elle se trouvait sur un port trunk ou un port disposant d'un VLAN voix (un commutateur ne doit pas recevoir de trame Ethernet étiquetée sur un port d'accès). Dans cet exemple, supposons que le VLAN natif est le VLAN 10. L'étiquette interne est le VLAN victime ; dans ce cas, il s'agit du VLAN 20.

2. La trame arrive sur le commutateur, qui vérifie la première étiquette 802.1Q de 4 octets. Le commutateur détecte que la trame est destinée au VLAN 10, qui est le VLAN natif. Le commutateur transfère le paquet par tous les ports du VLAN 10 après avoir éliminé l'étiquette du VLAN 10. Sur le port trunk, l'étiquette du VLAN 10 est éliminée et le paquet n'est pas balisé de nouveau, car il fait partie du VLAN natif. À ce stade, l'étiquette du VLAN 20 reste inchangée et n'a pas été inspectée par le premier commutateur.

3. Le deuxième commutateur examine uniquement l'étiquette 802.1Q interne que le pirate a envoyée et constate que la trame est destinée au VLAN 20, le VLAN cible. Il envoie la trame sur le port victime ou la diffuse, selon qu'il existe une entrée de table d'adresse MAC pour l'hôte victime.

Ce type d'attaque est unidirectionnel et ne fonctionne que si le pirate est connecté à un port se trouvant dans le même VLAN que le VLAN natif du port trunk. Ce type d'attaque est plus difficile à contrer que les simples attaques par saut de VLAN.

Le meilleur moyen pour repousser les attaques de double-tagging consiste à s'assurer que le VLAN natif des ports trunk est différent du VLAN de tous les ports utilisateur. En réalité, il est recommandé d'utiliser un VLAN fixe différent de tous les VLAN utilisateur du réseau commuté en tant que VLAN natif pour toutes les trunks 802.1Q.

Double Tagging
Cette attaque consiste à encapsuler deux champs VLAN dans les trames envoyés. Pour explopiter cette technique, il est nécessaire d'être connecté à un port compatible 802.1Q.

Voici le déroulé de cette attaque :

On envoie d'une trame avec deux champs VLAN encapsulés (le premier correspond au VLAN auquel on a accès et le second celui que l'on souhaite atteindre)
La trame est transmise sans la première balise car il s'agit du VLAN natif d'une interface de jonction (mode trunk)
La deuxième balise est alors visible par le deuxième switch que la trame rencontre
Cette deuxième balise VLAN indique que la trame est destinée à un hôte cible sur un deuxième switch
La trame est ensuite envoyée à l'hôte cible comme si elle provenait du VLAN cible, contournant efficacement les mécanismes de réseau qui isolent logiquement les VLAN les uns des autres
Remédiations
La technique de Double Tagging n'est exploitable que sur les ports de switch configurés pour utiliser des VLAN natifs.

Il est important de ne pas utiliser le VLAN par défaut (VLAN 1) : switchport access vlan 2

Vous pouvez aussi remplacer le VLAN natif de tous les ports en mode trunk par un ID de VLAN inutilisé : switchport trunk native vlan 999

Vous pouvez également forcer le marquage explicite du VLAN natif sur tous les ports en mode trunk : vlan dot1q tag native
