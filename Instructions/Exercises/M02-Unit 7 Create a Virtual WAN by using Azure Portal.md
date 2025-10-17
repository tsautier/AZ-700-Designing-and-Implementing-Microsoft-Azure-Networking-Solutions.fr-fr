---
Exercise:
  title: M02 - Unité 7 Créer un WAN virtuel à l’aide du portail Azure
  module: Module 02 - Design and implement hybrid networking
---

# M02 - Unité 7 Créer un WAN virtuel à l’aide du portail Azure

## Scénario de l’exercice

Dans cet exercice, vous allez créer un WAN virtuel pour Contoso.

![Diagramme de l’architecture WAN de réseau virtuel.](../media/7-exercise-create-virtual-wan-by-using-azure-portal.png)

### Compétences de tâche
Dans cet exercice, vous allez :

+ Tâche 1 : Créer un WAN virtuel
+ Tâche 2 : créer un hub à partir du portail Azure
+ Tâche 3 : Connecter un VNet au hub virtuel

### Simulations de labo interactives

>**Note** : les simulations de labo qui ont été fournies précédemment ont été supprimées.
>
### Durée estimée : 65 minutes (dont environ 45 minutes d’attente pour le déploiement)

## Tâche 1 : Créer un WAN virtuel

1. Dans un navigateur, accédez au portail Azure et connectez-vous avec votre compte Azure.

1. Dans le portail, entrez WAN virtuel dans la zone de recherche et sélectionnez **WAN virtuels** dans la liste des résultats.

   ![Recherchez WAN virtuel dans le portail Azure.](../media/search-for-virtual-wan.png)

1. Dans la page WAN virtuel, sélectionnez + **Créer**.

1. Sur la page Créer un WAN, dans l’onglet **Fonctions de base**, renseignez les champs suivants :

   + **Abonnement :** utilisez l’abonnement existant

   + **Groupe de ressources :** ContosoResourceGroup

   + **Emplacement du groupe de ressources** : choisissez un emplacement pour les ressources dans la liste déroulante. Un WAN est une ressource globale et ne réside pas dans une région particulière. Toutefois, vous devez sélectionner une région pour gérer et localiser la ressource WAN que vous créez.

   + **Nom :** ContosoVirtualWAN

   + **Type :** Standard.

1. Lorsque vous avez terminé de renseigner les champs, sélectionnez **Vérifier + créer**.

1. Après la validation, sélectionnez **Créer** pour créer le WAN virtuel.

## Tâche 2 : créer un hub à partir du portail Azure

Un hub contient des passerelles pour offrir des fonctionnalités site à site, ExpressRoute ou point à site. La création de la passerelle VPN de site à site prend 30 minutes dans le hub virtuel. Vous devez créer un WAN virtuel avant de pouvoir créer un hub.

1. Localisez l’instance Virtual WAN que vous avez créée.
   
1. Dans la page WAN virtuel, sous **Connectivité**, sélectionnez **Hubs**.

1. Dans la page Hubs, sélectionnez **+Nouveau hub** pour ouvrir la page Créer un hub virtuel.
   ![Créer un hub virtuel, onglet Informations de base.](../media/create-vwan-hub.png)

1. Sur la page Créer un hub virtuel, dans l’onglet **Fonctions de base**, renseignez les champs suivants :
   + **Région :** USA Ouest
   + **Nom :** ContosoVirtualWANHub-WestUS
   + **Espace d’adressage privé du hub :** 10.60.0.0/24
   + **Capacité du hub virtuel :** 2 unités d’infrastructure de routage
   + **Préférence de routage du hub :** laissez la valeur par défaut.

1. Sélectionnez **Suivant : site à site**.

1. Sous l’onglet **Site à site**, complétez les champs suivants :
   + **Voulez-vous créer une configuration site à site (passerelle VPN) ? :** Oui
   + Le champ **Numéro AS** ne peut pas être modifié.
   + **Unités d’échelle de la passerelle :** 1 unité d’échelle = 500 Mbits/s x 2.
   + **Préférence de routage du hub :** laissez la valeur par défaut.

1. Sélectionnez **Vérifier + créer** pour valider.

1. Sélectionnez **Créer** pour créer le hub.

1. Après 30 minutes, **Actualisez** pour afficher le hub dans la page Hubs.

## Tâche 3 : Connecter un VNet au hub virtuel

1. Localisez l’instance Virtual WAN que vous avez créée.

1. Dans ContosoVirtualWAN, sous **Connectivité**, sélectionnez **Connexions de réseau virtuel**.

   ![Page de configuration de Virtual WAN avec des connexions de réseau virtuel mises en évidence.](../media/connect-vnet-to-virtual-hub.png)

1. Sur ContosoVirtualWAN | Connexions de réseau virtuel, sélectionnez **+ Ajouter une connexion**.

1. Dans Ajouter une connexion, utilisez les informations suivantes pour créer la connexion.

   + **Nom de la connexion :** ContosoVirtualWAN-to-ResearchVNet

   + **Hubs :** ContosoVirtualWANHub-WestUS

   + **Abonnement :** aucune modification

   + **Groupe de ressources :** ContosoResourceGroup

   + **Réseau virtuel :** ResearchVNet

   + **Propager à aucun :** Oui

   + **Associer une table de routage :** Par défaut

1. Sélectionnez **Créer**.

## Nettoyer les ressources

   >**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob
   ```

   >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.

## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+ Quel type d’architecture réseau est utilisé par Azure VWAN ?
+ Quelle sont les différences entre les types de base et standard d’Azure VWAN ? Fournissez des exemples.
+ Un réseau VWAN Azure peut-il être créé avec des outils de script ?

## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Introduction à Azure Virtual WAN](https://learn.microsoft.com/training/modules/introduction-azure-virtual-wan/). Dans ce module, vous allez découvrir les fonctionnalités d’Azure Virtual WAN. 
+ [Concevez et implémentez la mise en réseau hybride](https://learn.microsoft.com/training/modules/design-implement-hybrid-networking/). Dans ce module, vous allez apprendre à concevoir et à implémenter Azure Virtual WAN.

## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 

+ Azure Virtual WAN est un service de mise en réseau qui combine un grand nombre de fonctionnalités de réseau, de sécurité et de routage en une unique interface opérationnelle.
+ Virtual WAN repose sur une architecturede réseau en étoile avec des fonctionnalités de mise à l’échelle et de performances intégrées pour les branches, les utilisateurs, les circuits ExpressRoute et les réseaux virtuels.
+ Il existe trois principaux cas d’utilisation pour Virtual WAN : site à site, point à site et ExpressRoute. 
+ Il existe deux types de Virtual WAN : de base et standard.









