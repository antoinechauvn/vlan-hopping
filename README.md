Le saut de VLAN permet à un VLAN de voir le trafic en provenance d'un autre VLAN. L'usurpation de commutateur est un type d'attaque par saut de VLAN tirant parti d'un port trunk configuré de manière incorrecte. Par défaut, les ports trunk ont accès à tous les VLAN et acheminent le trafic de plusieurs VLAN sur la même liaison physique, généralement entre des commutateurs.

Dans la figure, cliquez sur le bouton Lecture pour voir l'animation illustrant une attaque d'usurpation de commutateur.

Dans une attaque de base d'usurpation de commutateur, le pirate tire parti du fait que la configuration par défaut du port du commutateur est dynamique automatique. Il configure un système afin de se faire passer pour un commutateur. Cette usurpation exige que le pirate soit capable d'émuler 802.1Q et les messages DTP. En amenant un commutateur à penser qu'un autre commutateur tente de former un trunk, un pirate peut accéder à tous les VLAN autorisés sur le port trunk.

Le meilleur moyen d'éviter une attaque de base d'usurpation de commutateur est de désactiver le trunking sur tous les ports, excepté ceux qui le requièrent spécifiquement. Sur les ports de trunking requis, désactivez DTP, puis activez manuellement le trunk.


![Capture d’écran 2022-04-17 114556](https://user-images.githubusercontent.com/83721477/163709246-3e6972e8-23dd-4725-9d61-8efb7976454a.png)

![image](https://user-images.githubusercontent.com/83721477/163709303-2856b6b9-dcbb-4c37-bfa6-80bc4cec2bc7.png)
