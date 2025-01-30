---
Exercise:
  title: M03 - Unité 4 Configurer une passerelle ExpressRoute
  module: Module 03 - Design and implement Azure ExpressRoute
---
# M03 - Unité 4 Configurer une passerelle ExpressRoute

## Scénario de l’exercice

![Diagramme d’une passerelle de réseau virtuel.](../media/4-exercise-configure-expressroute-gateway.png)

Pour connecter votre réseau virtuel Azure et votre réseau local via ExpressRoute, vous devez d’abord créer une passerelle réseau virtuelle. Une passerelle de réseau virtuel a deux rôles : échanger des routes IP entre les réseaux et router le trafic réseau.

   >**Remarque :** Une **[simulation de labo interactive](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Configure%20an%20ExpressRoute%20gateway)** est disponible et vous permet de progresser à votre propre rythme. Il peut exister de légères différences entre la simulation interactive et le labo hébergé. Toutefois, les concepts et idées de base présentés sont identiques.

### Durée estimée : 60 minutes (dont environ 45 minutes d’attente pour le déploiement)

**Types de passerelles**

Lorsque vous créez une passerelle de réseau virtuel, vous devez spécifier plusieurs paramètres. L’un des paramètres requis, « Type Passerelle », spécifie si la passerelle est utilisée pour le trafic ExpressRoute, ou le trafic VPN. Les types de passerelles sont les suivants:

- **VPN** : pour envoyer le trafic chiffré sur l’Internet public, vous utilisez le type de passerelle « VPN ». Il s’agit alors d’une passerelle VPN. Les connexions site à site, point à site et réseau virtuel à réseau virtuel utilisent toutes une passerelle VPN.
- **ExpressRoute** : Pour envoyer le trafic réseau sur une connexion privée, vous utilisez le type de passerelle « ExpressRoute ». Ce type de passerelle est également appelé « passerelle ExpressRoute » et est utilisé lors de la configuration d'ExpressRoute.

Chaque réseau virtuel ne peut posséder qu’une seule passerelle de réseau virtuel par type de passerelle. Par exemple, une passerelle de réseau virtuel peut utiliser le type de passerelle VPN, et une autre le type de passerelle ExpressRoute.

Dans cet exercice, vous allez :

- Tâche 1 : Créer le réseau virtuel et le sous-réseau de passerelle
- Tâche 2 : Créer la passerelle de réseau virtuel

## Tâche 1 : Créer le réseau virtuel et le sous-réseau de passerelle

1. Sur n’importe quelle page du portail Azure, dans **Rechercher des ressources, des services et des documents**, entrez réseau virtuel, puis sélectionnez **Réseaux virtuels** dans les résultats.

1. Dans la page Réseaux virtuels, sélectionnez **+ Créer**.

1. Dans le volet Créer des réseaux virtuels, sous l’onglet **De base**, utilisez les informations du tableau suivant pour créer le réseau virtuel :

   | **Paramètre**          | **Valeur**                        |
   | -------------------- | -------------------------------- |
   | Nom du réseau virtuel | CoreServicesVNet                 |
   | Groupe de ressources       | ContosoResourceGroup             |
   | Location             | USA Est                          |

1. Sélectionnez **Suivant : Adresses IP**.

1. Sous l’onglet **Adresses IP**, dans **Espace d’adressage IPv4**, entrez 10.20.0.0/16, puis sélectionnez **+ Ajouter un sous-réseau**.

1. Dans le volet Ajouter un sous-réseau, utilisez les informations du tableau suivant pour créer le sous-réseau :

   | **Paramètre**                  | **Valeur**               |
   | ---------------------------- | ----------------------- |
   | Objectif du sous-réseau               | Passerelle de réseau virtuel |
   | Espace d’adressage du sous-réseau de passerelle | 10.20.0.0/27            |

Notez que le nom du sous-réseau sera automatiquement renseigné.

1. Ensuite, sélectionnez **Ajouter**.

1. Dans la page Créer un réseau virtuel, sélectionnez **Vérifier + créer**.

   ![Portail Azure - Ajouter un sous-réseau de passerelle](../media/add-gateway-subnet.png)

1. Confirmez la validation du réseau virtuel, puis sélectionnez **Créer**.

   >**Remarque :** si vous utilisez un réseau virtuel à double pile et prévoyez d’utiliser un peering privé IPv6 plutôt qu’ExpressRoute, cliquez sur Ajouter un espace d’adressage IP6, puis entrez les valeurs de la plage d’adresses IPv6.

## Tâche 2 : Créer la passerelle de réseau virtuel

1. Sur n’importe quelle page du portail Azure, dans **Rechercher des ressources, des services et des documents (G+/)**, entrez passerelle de réseau virtuel, puis sélectionnez **Passerelles de réseaux virtuels** dans les résultats.

1. Sur la page Passerelles de réseaux virtuels, sélectionnez **+Créer**.

1. Dans la page **Créer une passerelle de réseau virtuel**, utilisez les informations du tableau suivant pour créer la passerelle :

   | **Paramètre**               | **Valeur**                  |
   | ------------------------- | -------------------------- |
   | **Détails du projet**       |                            |
   | Groupe de ressources            | ContosoResourceGroup       |
   | **Détails de l’instance**      |                            |
   | Nom                      | CoreServicesVnetGateway    |
   | Région                    | USA Est                    |
   | Type de passerelle              | ExpressRoute               |
   | Référence SKU                       | standard                   |
   | Réseau virtuel           | CoreServicesVNet           |
   | **Adresse IP publique**     |                            |
   | Adresse IP publique         | Création                 |
   | Nom de l’adresse IP publique    | CoreServicesVnetGateway-IP |
   | Affectation                | Non configurable           |

1. Sélectionnez **Vérifier + créer**.

1. Confirmez la validation de la configuration de la passerelle, puis sélectionnez **Créer**.

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

   >**Remarque :** le déploiement d’une passerelle peut prendre jusqu’à 45 minutes.


## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+ En quoi le service Azure ExpressRoute est-il différent de Virtual WAN ? Pourriez-vous utiliser ces technolgies ensemble ? Fournir des exemples.
+ Que dois-je prendre en compte lors du choix entre un modèle de fournisseur ExpressRoute et ExpressRoute Direct ?
+ Créez une tableau qui récapitule la référence (SKU) Azure ExpressRoute et ses fonctionnalités.

## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Présentation d’Azure ExpressRoute](https://learn.microsoft.com/training/modules/intro-to-azure-expressroute/). Dans ce module, vous allez découvrir Azure ExpressRoute et ses fonctionnalités.
+ [Concevez et implémentez ExpressRoute](https://learn.microsoft.com/training/modules/design-implement-azure-expressroute/). Dans ce module, vous allez apprendre à concevoir et à implémenter Azure ExpressRoute, ExpressRoute Global Reach, et ExpressRoute FastPath.

## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 
+ Azure ExpressRoute permet à une organisation de connecter ses réseaux locaux directement aux clouds Microsoft Azure et Microsoft 365. Azure ExpressRoute utilise une connexion dédiée haut débit fournie par un partenaire Microsoft.
+ Microsoft garantit une disponibilité d’au moins 99,95 % pour les connexions dédiées ExpressRoute. La connexion est privée et se déplace sur une ligne dédiée. Les tiers ne peuvent pas intercepter le trafic.
+ Vous pouvez créer une connexion entre votre réseau local et le cloud Microsoft de quatre façons différentes : avec une colocalisation avec échange de cloud, avec une connexion Ethernet point à point, avec une connexion universelle (IPVPN) et avec ExpressRoute Direct.
+ Les fonctionnalités ExpressRoute sont déterminées par la référence (SKU) : local, standard et premium. 
