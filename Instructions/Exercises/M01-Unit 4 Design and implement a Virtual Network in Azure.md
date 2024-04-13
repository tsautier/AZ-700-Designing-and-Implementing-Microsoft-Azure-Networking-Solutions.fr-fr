---
Exercise:
  title: M01 - Unité 4 Conception et implémentation d’un réseau virtuel dans Azure
  module: Module 01 - Introduction to Azure Virtual Networks
---
# M01 - Unité 4 Conception et implémentation d’un réseau virtuel dans Azure

## Scénario de l’exercice

Vous êtes maintenant prêt à déployer des réseaux virtuels dans le portail Azure.

Prenons l’exemple d’une organisation fictive, Contoso Ltd, qui est en train de migrer son infrastructure et ses applications vers Azure. Dans votre rôle d’ingénieur réseau, vous devez planifier et implémenter trois réseaux virtuels et des sous-réseaux pour prendre en charge les ressources dans ces réseaux virtuels.

**Remarque :** Une **[simulation de labo interactive](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Design%20and%20implement%20a%20virtual%20network%20in%20Azure)** est disponible et vous permet de progresser à votre propre rythme. Il peut exister de légères différences entre la simulation interactive et le labo hébergé. Toutefois, les concepts et idées de base présentés sont identiques.

### Durée estimée : 20 minutes

Le réseau virtuel **CoreServicesVnet** est déployé dans la région **USA Est**. Ce réseau virtuel aura le plus grand nombre de ressources. Il sera connecté à des réseaux locaux via une connexion VPN. Ce réseau aura des services web, des bases de données et d’autres systèmes qui sont essentiels au fonctionnement de l’entreprise. Les services partagés, tels que les contrôleurs de domaine et les DNS, sont également situés à cet endroit. Une croissance importante est prévue, il faut donc un espace d’adressage important pour ce réseau virtuel.

Le réseau virtuel **ManufacturingVnet** est déployé dans la région **Europe Ouest**, près du site de fabrication de votre entreprise. Ce réseau virtuel contiendra des systèmes pour les opérations du site de fabrication. L’organisation prévoit un grand nombre d’appareils connectés internes à partir desquels leurs systèmes doivent récupérer des données telles que la température. Elle a donc besoin d’un espace d’adressage IP dans lequel une expansion est possible.

Le réseau virtuel **ResearchVnet** est déployé dans la région **Asie Sud-Est**, près de l’endroit où se trouve l’équipe Recherche et développement de votre entreprise. L’équipe Recherche et développement utilise ce réseau virtuel. Cette équipe dispose d’un ensemble de ressources réduit et stable, qui n’est pas censé croître. Elle a besoin d’un petit nombre d’adresses IP pour quelques machines virtuelles.

![Disposition réseau pour Contoso.
Local 10.10.0.0/16 ResearchVNet Asie du Sud-Est 10.40.40.0/24 CoreServicesVNet USA Est 10.20.0.0/16 ManufacturingVNet Europe de l’Ouest 10.30.0.0/16
](../media/design-implement-vnet-peering.png)

Vous allez créer les ressources suivantes :

| **Réseau virtuel** | **Région**     | **Espace d’adressage du réseau virtuel** | **Sous-réseau**                | **Sous-réseau**    |
| ------------------- | -------------- | --------------------------------- | ------------------------- | ------------- |
| CoreServicesVnet    | USA Est        | 10.20.0.0/16                      |                           |               |
|                     |                |                                   | GatewaySubnet             | 10.20.0.0/27  |
|                     |                |                                   | SharedServicesSubnet      | 10.20.10.0/24 |
|                     |                |                                   | DatabaseSubnet            | 10.20.20.0/24 |
|                     |                |                                   | PublicWebServiceSubnet    | 10.20.30.0/24 |
| ManufacturingVnet   | Europe Ouest    | 10.30.0.0/16                      |                           |               |
|                     |                |                                   | ManufacturingSystemSubnet | 10.30.10.0/24 |
|                     |                |                                   | SensorSubnet1             | 10.30.20.0/24 |
|                     |                |                                   | SensorSubnet2             | 10.30.21.0/24 |
|                     |                |                                   | SensorSubnet3             | 10.30.22.0/24 |
| ResearchVnet        | Asie Sud-Est | 10.40.0.0/16                      |                           |               |
|                     |                |                                   | ResearchSystemSubnet      | 10.40.0.0/24  |

Ces réseaux virtuels et ces sous-réseaux sont structurés de manière à accueillir les ressources existantes, tout en permettant la croissance prévue. Nous allons créer ces réseaux virtuels et ces sous-réseaux pour poser les bases de notre infrastructure réseau.

Dans cet exercice, vous allez :

+ Tâche 1 : créer le groupe de ressources Contoso
+ Tâche 2 : créer le réseau virtuel et les sous-réseaux CoreServicesVnet
+ Tâche 3 : créer le réseau virtuel et les sous-réseaux ManufacturingVnet
+ Tâche 4 : créer le réseau virtuel et les sous-réseaux ResearchVnet
+ Tâche 5 : vérifier la création des réseaux virtuels et des sous-réseaux

## Tâche 1 : créer le groupe de ressources Contoso

1. Accédez au [portail Azure](https://portal.azure.com/).

2. Sur la page d’accueil, sous **Services Azure**, sélectionnez **Groupes de ressources**.  

3. Dans Groupes de ressources, sélectionnez **+ Créer**.

4. Utilisez les informations du tableau suivant pour créer le groupe de ressources.

| **Tab**         | **Option**                                 | **Valeur**            |
| --------------- | ------------------------------------------ | -------------------- |
| Concepts de base          | Resource group                             | ContosoResourceGroup |
|                 | Région                                     | (États-Unis) USA Est         |
| Balises            | Aucune modification n’est requise                        |                      |
| Vérifier + créer | Passez en revue vos paramètres, puis sélectionnez **Créer** |                      |

5. Dans Groupes de ressources, vérifiez que **ContosoResourceGroup** apparaît dans la liste.

## Tâche 2 : créer le réseau virtuel et les sous-réseaux CoreServicesVnet

1. Dans la page d’accueil du portail Azure, accédez à la barre de recherche globale et recherchez **Réseaux virtuels**, puis sélectionnez Réseaux virtuels sous Services.  ![Résultats de la recherche de « réseau virtuel» dans la barre de recherche globale de la page d’accueil du portail Azure.](../media/global-search-bar.PNG)
2. Sélectionnez **Créer** dans la page Réseaux virtuels.  ![Assistant Créer un réseau virtuel.](../media/create-virtual-network.png)
3. Utilisez les informations du tableau suivant pour créer le réseau virtuel CoreServicesVnet.  
   Supprimer ou remplacer l’espace d’adressage IP par défaut![Configuration d’adresse IP pour le déploiement d’un réseau virtuel Azure  ](../media/default-vnet-ip-address-range-annotated.png)

| **Tab**      | **Option**         | **Valeur**            |
| ------------ | ------------------ | -------------------- |
| Concepts de base       | Groupe de ressources     | ContosoResourceGroup |
|              | Nom               | CoreServicesVnet     |
|              | Région             | (États-Unis) USA Est         |
| Adresses IP | Espace d’adressage IPv4 | 10.20.0.0/16         |

 4. Utilisez les informations du tableau suivant pour créer les sous-réseaux de CoreServicesVnet.

 5. Pour commencer à créer chaque sous-réseau, sélectionnez **+ Ajouter un sous-réseau**. Pour terminer la création de chaque sous-réseau, sélectionnez **Ajouter**.

| **Sous-réseau**             | **Option**           | **Valeur**              |
| ---------------------- | -------------------- | ---------------------- |
| Sous-réseau de passerelle          | Nom du sous-réseau          | Sous-réseau de passerelle          |
|                        | Plage d’adresses de sous-réseau | 10.20.0.0/27           |
| SharedServicesSubnet   | Nom du sous-réseau          | SharedServicesSubnet   |
|                        | Plage d’adresses de sous-réseau | 10.20.10.0/24          |
| DatabaseSubnet         | Nom du sous-réseau          | DatabaseSubnet         |
|                        | Plage d’adresses de sous-réseau | 10.20.20.0/24          |
| PublicWebServiceSubnet | Nom du sous-réseau          | PublicWebServiceSubnet |
|                        | Plage d’adresses de sous-réseau | 10.20.30.0/24          |

 6. Pour terminer la création de CoreServicesVnet et des sous-réseaux associés, sélectionnez **Vérifier + créer**.

 7. Vérifiez que la validation de votre configuration a réussi, puis sélectionnez **Créer**.

 8. Répétez les étapes 1 à 8 pour chaque réseau virtuel en suivant les tableaux ci-dessous  

## Tâche 3 : créer le réseau virtuel et les sous-réseaux ManufacturingVnet

| **Tab**      | **Option**         | **Valeur**            |
| ------------ | ------------------ | -------------------- |
| Concepts de base       | Groupe de ressources     | ContosoResourceGroup |
|              | Nom               | ManufacturingVnet    |
|              | Région             | (Europe) Europe Ouest |
| Adresses IP | Espace d’adressage IPv4 | 10.30.0.0/16         |

| **Sous-réseau**                | **Option**           | **Valeur**                 |
| ------------------------- | -------------------- | ------------------------- |
| ManufacturingSystemSubnet | Nom du sous-réseau          | ManufacturingSystemSubnet |
|                           | Plage d’adresses de sous-réseau | 10.30.10.0/24             |
| SensorSubnet1             | Nom du sous-réseau          | SensorSubnet1             |
|                           | Plage d’adresses de sous-réseau | 10.30.20.0/24             |
| SensorSubnet2             | Nom du sous-réseau          | SensorSubnet2             |
|                           | Plage d’adresses de sous-réseau | 10.30.21.0/24             |
| SensorSubnet3             | Nom du sous-réseau          | SensorSubnet3             |
|                           | Plage d’adresses de sous-réseau | 10.30.22.0/24             |

## Tâche 4 : créer le réseau virtuel et les sous-réseaux ResearchVnet

| **Tab**      | **Option**         | **Valeur**            |
| ------------ | ------------------ | -------------------- |
| Concepts de base       | Groupe de ressources     | ContosoResourceGroup |
|              | Nom               | ResearchVnet         |
|              | Région             | Asie Sud-Est       |
| Adresses IP | Espace d’adressage IPv4 | 10.40.0.0/16         |

| **Sous-réseau**           | **Option**           | **Valeur**            |
| -------------------- | -------------------- | -------------------- |
| ResearchSystemSubnet | Nom du sous-réseau          | ResearchSystemSubnet |
|                      | Plage d’adresses de sous-réseau | 10.40.0.0/24         |

## Tâche 5 : vérifier la création des réseaux virtuels et des sous-réseaux

1. Dans la page d’accueil du portail Azure, sélectionnez **Toutes les ressources**.

2. Vérifiez que CoreServicesVnet, ManufacturingVnet et ResearchVnet sont répertoriés.

3. Sélectionnez **CoreServicesVnet**.

4. Dans CoreServicesVnet, sous **Paramètres**, sélectionnez **Sous-réseaux**.

5. Dans CoreServicesVnet | Sous-réseaux, vérifiez que les sous-réseaux que vous avez créés sont répertoriés et que les plages d’adresses IP sont correctes.

   ![Liste des sous-réseaux dans CoreServicesVnet.](../media/verify-subnets-annotated.png)

6. Répétez les étapes 3 à 5 pour chaque réseau virtuel.

Félicitations ! Vous avez créé avec succès un groupe de ressources, trois réseaux virtuels et les sous-réseaux associés.
