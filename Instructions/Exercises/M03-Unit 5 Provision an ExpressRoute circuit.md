---
Exercise:
  title: M03 - Unité 5 Provisionner un circuit ExpressRoute
  module: Module 03 - Design and implement Azure ExpressRoute
---
# M03 - Unité 5 Provisionner un circuit ExpressRoute

## Scénario de l’exercice

Dans cet exercice, vous allez créer un circuit ExpressRoute à l’aide du portail Azure et du modèle de déploiement Azure Resource Manager.

### Durée estimée : 15 minutes

![Diagramme de disposition de circuit ExpressRoute pour l’exercice](../media/5-exercise-provision-expressroute-circuit.png)

### Compétences de tâche

Dans cet exercice, vous allez :

+ Tâche 1 : Créer et provisionner un circuit ExpressRoute
+ Tâche 2 : Récupérer votre clé de service
+ Tâche 3 : Déprovisionner un circuit ExpressRoute


## Tâche 1 : Créer et provisionner un circuit ExpressRoute

1. Dans un navigateur, accédez au [portail Azure](https://portal.azure.com/) et connectez-vous avec votre compte Azure.

   >**Important** : votre circuit ExpressRoute est facturé à partir de l’émission d’une clé de service. Effectuez cette opération seulement quand le fournisseur de connectivité prêt à approvisionner le circuit.

1. Dans le menu du portail Azure, recherchez et sélectionnez **Circuits ExpressRoute**.

1. Dans la page **Créer ExpressRoute**, indiquez `ExpressRouteResourceGroup` pour le **groupe de ressources**. Sélectionnez ensuite **Résilience standard** pour la **résilience**.

1. Pour **Détails du circuit**, vérifiez que vous spécifiez la région correcte (**USA Est 2**), le nom du circuit (**TestERCircuit**), l’emplacement de peering (**Seattle**), le fournisseur (**Equinix**), la bande passante (**50 Mops**), le niveau de référence SKU (**Standard**) et le modèle de facturation des données (**Limitées**).

1. Sélectionnez **Vérifier + créer**.

1. Confirmez la validation de la configuration ExpressRoute, puis sélectionnez **Créer**.

![Portail Azure - Onglet de configuration de circuit ExpressRoute](../media/expressroute-create-configuration2.png)

+ Type de port détermine si vous vous connectez à un fournisseur de services ou directement au réseau global de Microsoft dans un emplacement de peering.
+ L’option Créer ou importer à partir du modèle classique détermine si un nouveau circuit est en cours de création ou si vous migrez un circuit classique vers Azure Resource Manager.
+ Le fournisseur est le service Internet à partir duquel vous allez demander votre service.
+ L’Emplacement de peering est l’emplacement physique où vous effectuez le peering auprès de Microsoft.

> [!Important]
>
> L’emplacement de peering indique [l’emplacement physique](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-locations) où vous effectuez le peering auprès de Microsoft. Cet emplacement n’est pas lié à la propriété « Emplacement », qui fait référence à la zone géographique où se trouve le fournisseur de ressources réseau Azure. Bien que ces éléments ne soient pas liés, nous vous conseillons de choisir un fournisseur de ressources réseau géographiquement proche de l’emplacement de peering du circuit.

+ La **référence SKU** détermine si un module complémentaire ExpressRoute local, ExpressRoute standard ou ExpressRoute premium est activé. Vous pouvez spécifier **Local** pour obtenir la référence SKU locale, **Standard** pour obtenir la référence SKU standard ou **Premium** pour le module complémentaire Premium. Vous pouvez changer la référence SKU pour activer le module complémentaire Premium.

   >**Important** : vous ne pouvez pas remplacer la référence SKU de Standard/Premium par celle de Local.

+ Le **modèle de facturation** détermine le type de facturation. Vous pouvez spécifier **Limité** pour un forfait de données limité, et **Illimité** pour un forfait de données illimité. Vous pouvez changer le type de facturation de **Limité** à **Illimité**.

> [!Important]
>
> Vous ne pouvez pas modifier le type de Illimité à Limité.

+ **Autoriser les opérations classiques** permet de lier des réseaux virtuels classiques au circuit.

## Tâche 2 : Récupérer votre clé de service

1. Vous pouvez afficher tous les circuits que vous avez créés en sélectionnant **Tous les services &gt; Mise en réseau &gt; Circuits ExpressRoute**.

   ![Portail Azure - Créer un menu de ressources ExpressRoute](../media/expressroute-circuit-menu.png)

1. Tous les circuits ExpressRoute créés dans l’abonnement sont listés ici.

   ![Portail Azure - Afficher les circuits ExpressRoute existants](../media/expressroute-circuit-list.png)

1. La page Circuit affiche les propriétés du circuit. La clé de service s’affiche dans le champ du même nom. Votre fournisseur de services aura besoin de la clé de service pour terminer le processus de provisionnement. La clé de service est spécifique à votre circuit. **Vous devez envoyer la clé de service à votre fournisseur de connectivité pour le provisionnement.**

   ![Portail Azure - Propriétés du circuit ExpressRoute montrant la clé de service](../media/expressroute-circuit-overview.png)

1. Dans cette page, **Statut du fournisseur** vous donne des informations sur l’état actuel du provisionnement du côté fournisseur de services. **Statut du circuit** vous indique l’état du côté Microsoft.

1. Quand vous créez un circuit ExpressRoute, ce circuit affiche l’état suivant :

   + Statu du fournisseur : Non approvisionné
   + État du circuit : Activé

   + Le circuit passe à l’état suivant quand le fournisseur de connectivité l’active pour vous :
     + Statut du fournisseur : En cours d’approvisionnement
     + Statut du circuit : Activé
   + Pour être utilisé, le circuit ExpressRoute doit être dans l’état suivant :
     + Statut du fournisseur : Approvisionné
     + Statut du circuit : Activé
   + Vous devez vérifier régulièrement l’état du provisionnement et l’état du circuit.

![Portail Azure - Propriétés du circuit ExpressRoute montrant que le statut est maintenant « Provisionné »](../media/provisioned.png)

Félicitations ! Vous avez créé un circuit ExpressRoute et localisé la clé de service, dont vous auriez besoin pour terminer le provisionnement du circuit.

## Tâche 3 : Déprovisionner un circuit ExpressRoute

Si l’état de provisionnement du fournisseur de services du circuit ExpressRoute est **En cours de provisionnement** ou **Provisionné**, vous devez vous mettre en relation avec votre fournisseur de services pour déprovisionner le circuit de son côté. Microsoft peut continuer à réserver des ressources et à vous facturer jusqu’à ce que le fournisseur de services termine le déprovisionnement du circuit et nous informe.

   >**Remarque** : vous devez dissocier tous les réseaux virtuels du circuit ExpressRoute avant le déprovisionnement. Si cette opération échoue, vérifiez si certains de vos réseaux virtuels sont liés au circuit. Si le fournisseur de services a annulé l’approvisionnement du circuit (l’état d’approvisionnement du fournisseur de services affiche la valeur Non approvisionné), vous pouvez supprimer le circuit. Cette opération arrête la facturation du circuit.

## Nettoyer les ressources

Vous pouvez supprimer votre circuit ExpressRoute en sélectionnant l’icône **Supprimer**. Avant de continuer, assurez-vous que l’état du fournisseur est Non approvisionné.

![Portail Azure - Supprimer un circuit ExpressRoute](../media/expressroute-circuit-delete.png)

   >**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob
   Remove-AzResourceGroup -Name 'ExpressRouteResourceGroup' -Force -AsJob
   ```

   >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.

## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+ Quels fournisseurs de services sont disponibles pour Azure ExpressRoute ?
+ Quels sont les problèmes de configuration les plus courants avec Azure ExpressRoute ? Que dois-je faire si je rencontre ce problème ?

## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Présentation d’Azure ExpressRoute](https://learn.microsoft.com/training/modules/intro-to-azure-expressroute/). Dans ce module, vous allez découvrir Azure ExpressRoute et ses fonctionnalités.
+ [Concevez et implémentez ExpressRoute](https://learn.microsoft.com/training/modules/design-implement-azure-expressroute/). Dans ce module, vous allez apprendre à concevoir et à implémenter Azure ExpressRoute, ExpressRoute Global Reach, et ExpressRoute FastPath.

## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 
+ Azure ExpressRoute permet à une organisation de connecter ses réseaux locaux directement aux clouds Microsoft Azure et Microsoft 365. Azure ExpressRoute utilise une connexion dédiée haut débit fournie par un partenaire Microsoft.
+ Microsoft garantit une disponibilité d’au moins 99,95 % pour les connexions dédiées ExpressRoute. La connexion est privée et se déplace sur une ligne dédiée. Les tiers ne peuvent pas intercepter le trafic.
+ Vous pouvez créer une connexion entre votre réseau local et le cloud Microsoft de quatre façons différentes : avec une colocalisation avec échange de cloud, avec une connexion Ethernet point à point, avec une connexion universelle (IPVPN) et avec ExpressRoute Direct.
+ Les fonctionnalités ExpressRoute sont déterminées par la référence (SKU) : local, standard et premium. 


