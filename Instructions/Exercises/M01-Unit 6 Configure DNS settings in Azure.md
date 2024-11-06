---
Exercise:
  title: M01 - Unité 6 Configurer les paramètres DNS dans Azure
  module: Module 01 - Introduction to Azure Virtual Networks
---

# M01 - Unité 6 Configurer les paramètres DNS dans Azure

## Scénario de l’exercice

Dans cette unité, vous allez configurer la résolution de noms DNS pour Contoso Ltd. Vous allez créer une zone DNS privée nommée contoso.com, lier les réseaux virtuels pour l’inscription et la résolution, puis créer deux machines virtuelles et tester la configuration.

![Diagramme de l’architecture DNS.](../media/6-exercise-configure-domain-name-servers-configuration-azure.png)

Dans cet exercice, vous allez :

+ Tâche 1 : Créer une zone DNS privée
+ Tâche 2 : Lier le sous-réseau pour l’inscription automatique
+ Tâche 3 : Créer des machines virtuelles pour tester la configuration
+ Tâche 4 : Vérifier que les enregistrements sont présents dans la zone DNS

**Remarque :** Une **[simulation de labo interactive](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Configure%20DNS%20settings%20in%20Azure)** est disponible et vous permet de progresser à votre propre rythme. Il peut exister de légères différences entre la simulation interactive et le labo hébergé. Toutefois, les concepts et idées de base présentés sont identiques.

### Durée estimée : 25 minutes

## Tâche 1 : Créer une zone DNS privée

1. Accédez au [portail Azure](https://portal.azure.com/).

1. Dans la page d’accueil Azure, dans la barre de recherche, entrez DNS, puis sélectionnez **Zones DNS privé**.  
   ![Page d’accueil du portail Azure avec la recherche DNS.](../media/create-private-dns-zone.png)

1. Dans Zones DNS privées, sélectionnez **+ Créer**.

1. Utilisez les informations du tableau suivant pour créer la zone DNS privée.

    | **Tab**         | **Option**                             | **Valeur**            |
    | --------------- | -------------------------------------- | -------------------- |
    | Concepts de base          | Resource group                         | ContosoResourceGroup |
    |                 | Nom                                   | Contoso.com          |
    | Balises            | Aucune modification n’est requise                    |                      |
    | Vérifier + créer | Passez en revue vos paramètres, puis sélectionnez Créer |                      |

1. Attendez la fin du déploiement, puis sélectionnez **Accéder à la ressource**.

1. Vérifiez que la zone a été créée.

## Tâche 2 : Lier le sous-réseau pour l’inscription automatique

1. Dans Contoso.com, dans **Gestion des DNS**, sélectionnez **Liaisons de réseaux virtuels**.

1. Sur contoso.com \| Liaisons de réseau virtuel, sélectionnez **+ Ajouter**.

    ![contoso.com \| Liaisons de réseau virtuel avec + Ajouter en évidence.](../media/add-network-link-dns.png)

1. Utilisez les informations du tableau suivant pour ajouter la liaison de réseau virtuel.

    | **Option**                          | **Valeur**                               |
    | ----------------------------------- | --------------------------------------- |
    | Nom de la liaison                           | CoreServicesVnetLink                    |
    | Abonnement                        | Aucune modification n’est requise                     |
    | Réseau virtuel                     | CoreServicesVnet (ContosoResourceGroup) |
    | Activer l’inscription automatique            | Volumes sélectionnés                                |
    | Passez en revue vos paramètres, puis sélectionnez OK. |                                         |

1. Cliquez sur **Actualiser**.

1. Vérifiez que CoreServicesVnetLink a été créé et que l’inscription automatique est activée.

1. Répétez les étapes 2 à 5 pour ManufacturingVnet en utilisant les informations du tableau suivant :

    | **Option**                          | **Valeur**                                |
    | ----------------------------------- | ---------------------------------------- |
    | Nom de la liaison                           | ManufacturingVnetLink                    |
    | Abonnement                        | Aucune modification n’est requise                      |
    | Réseau virtuel                     | ManufacturingVnet (ContosoResourceGroup) |
    | Activer l’inscription automatique            | Volumes sélectionnés                                 |
    | Passez en revue vos paramètres, puis sélectionnez OK. |                                          |

1. Cliquez sur **Actualiser**.

1. Vérifiez que le ManufacturingVnetLink a été créé et que l’inscription automatique est activée.

1. Répétez les étapes 2 à 5 pour ResearchVnet, en utilisant les informations du tableau suivant :

    | **Option**                          | **Valeur**                           |
    | ----------------------------------- | ----------------------------------- |
    | Nom de la liaison                           | ResearchVnetLink                    |
    | Abonnement                        | Aucune modification n’est requise                 |
    | Réseau virtuel                     | ResearchVnet (ContosoResourceGroup) |
    | Activer l’inscription automatique            | Volumes sélectionnés                            |
    | Passez en revue vos paramètres, puis sélectionnez OK. |                                     |

1. Cliquez sur **Actualiser**.

1. Vérifiez que ResearchVnetLink a été créé et que l’inscription automatique est activée.

## Tâche 3 : Créer des machines virtuelles pour tester la configuration

Dans cette section, vous allez créer deux machines virtuelles de test pour tester la configuration de la zone DNS privée.

1. Sélectionnez l’icône Cloud Shell en haut à droite du portail Azure. Si nécessaire, configurez l’interpréteur de commandes.  
    + Sélectionnez **PowerShell**.
    + Sélectionnez **Aucun compte de stockage requis** et votre **abonnement**, puis sélectionnez **Appliquer**.
    + Attendez que le terminal crée et qu’une invite s’affiche. 

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Gérer des fichiers**, dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **azuredeploy.json** et **azuredeploy.parameters.json** l’un après l’autre dans le répertoire racine de Cloud Shell à partir du dossier source **F:\Allfiles\Exercises\M01**.

1. Déployez les modèles ARM suivants pour créer les machines virtuelles nécessaires à cet exercice :

    >**Remarque** : Vous serez invité à fournir un mot de passe d’administrateur.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```
  
1. Une fois le déploiement terminé, accédez à la page d’accueil du portail Azure, puis sélectionnez **Machines virtuelles**.

1. Vérifiez que les deux machines virtuelles ont été créées.

## Tâche 4 : Vérifier que les enregistrements sont présents dans la zone DNS

1. Sur la page d’accueil du portail Azure, sélectionnez **Zones DNS privées**.

1. Dans Zones DNS privées, sélectionnez **contoso.com**.

1. Vérifiez que les enregistrements de l’hôte (A) sont répertoriés pour les deux machines virtuelles, comme indiqué :

    ![Zone DNS contoso.com présentant les enregistrements A de l’hôte inscrit automatiquement.](../media/contoso_com-dns-zone.png)

1. Notez les noms et adresses IP des machines virtuelles.

### Se connecter aux machines virtuelles de test à l’aide du protocole RDP

1. Sur la page d’accueil du portail Azure, sélectionnez **Machines virtuelles**.

1. Sélectionnez **TestVM1**.

1. Dans TestVM1, sélectionnez **Se connecter &gt; RDP** et téléchargez le fichier RDP.

    ![TestVM1 avec Connecter et RDP mis en évidence.](../media/connect-to-am.png)

1. Enregistrez le fichier RDP sur votre bureau.

1. Suivez les mêmes étapes pour **TestVM2**

1. Connectez-vous à TestVM1 à l’aide du fichier RDP, en employant le nom d’utilisateur **TestUser** et le mot de passe que vous avez spécifié pendant le déploiement.

1. Connectez-vous à TestVM2 à l’aide du fichier RDP et du nom d’utilisateur **TestUser** et du mot de passe que vous avez spécifié pendant le déploiement.

1. Sur les deux machines virtuelles, dans **Choisir les paramètres de confidentialité pour votre appareil**, sélectionnez **Accepter**.

1. Sur les deux machines virtuelles, si vous y êtes invité, dans **Réseaux**, sélectionnez **Oui**.

1. Sur TestVM1, ouvrez une invite de commandes et entrez la commande `ipconfig /all`.

1. Vérifiez que l’adresse IP est identique à celle que vous avez notée dans la zone DNS.

1. Entrez la commande ping TestVM2.contoso.com.

1. Vérifiez que le nom de domaine complet (FQDN) correspond à l’adresse IP que vous avez notée dans la zone DNS privé. Le test Ping lui-même expire en raison du pare-feu Windows activé sur les machines virtuelles.

1. Vous pouvez également entrer la commande nslookup TestVM2.contoso.com et vérifier que vous recevez un enregistrement de résolution de noms correct pour VM2.

## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+ Quelle est la différence entre Azure DNS et Azure DNS privé ? Fournissez des exemples de situations où Azure DNS privé peut être utilisé.
+ Quel est l’objectif de l’inscription automatique lors de la création d’une zone Azure DNS ?

## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Introduction à Azure DNS](https://learn.microsoft.com/training/modules/intro-to-azure-dns/). Ce module explique ce que fait Azure DNS, comment il fonctionne et quand choisir de l’utiliser comme solution pour répondre aux besoins de votre organisation.
+ [Héberger votre domaine sur Azure DNS](https://learn.microsoft.com/training/modules/host-domain-azure-dns/). Dans ce module, vous créez une zone DNS et des enregistrements DNS pour mapper le domaine à une adresse IP. Vous allez également effectuer des tests pour vérifier que le nom de domaine est résolu sur votre serveur web.

## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 

+ Azure DNS est un service cloud qui vous permet d’héberger et de gérer des domaines DNS (Domain Name System), également appelés zones DNS. 
+ Les zones publiques Azure DNS hébergent les données de zone de nom de domaine pour les enregistrements que vous avez l’intention de résoudre par n’importe quel hôte sur Internet.
+ Les zones Azure DNS privé vous permettent de configurer un espace de noms de zone DNS privée pour les ressources Azure privées.
+ Une zone DNS est une collection d’enregistrements DNS. Les enregistrements DNS fournissent des informations sur le domaine.
