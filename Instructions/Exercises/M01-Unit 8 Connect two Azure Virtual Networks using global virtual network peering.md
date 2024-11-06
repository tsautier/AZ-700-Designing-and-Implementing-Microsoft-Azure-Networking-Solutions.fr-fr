---
Exercise:
  title: M01 - Unité 8 Connecter deux réseaux virtuels Azure à l’aide du peering de réseaux virtuels globaux
  module: Module 01 - Introduction to Azure Virtual Networks
---

# M01 - Unité 8 Connecter deux réseaux virtuels Azure à l’aide du peering de réseaux virtuels globaux

## Scénario de l’exercice

Dans cette unité, vous allez configurer la connectivité entre CoreServicesVnet et ManufacturingVnet en ajoutant des peerings pour autoriser le flux de trafic.

![Diagramme du peering de réseaux virtuels.](../media/8-exercise-connect-two-azure-virtual-networks-global.png)

Cette unité vous montrera :

+ Tâche 1 : créer une machine virtuelle pour tester la configuration
+ Tâche 2 : se connecter aux machines virtuelles de test à l’aide du protocole RDP
+ Tâche 3 : tester la connexion entre les machines virtuelles
+ Tâche 4 : créer des peerings de réseaux virtuels entre CoreServicesVnet et ManufacturingVnet
+ Tâche 5 : tester la connexion entre les machines virtuelles

**Remarque :** Une **[simulation de labo interactive](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Connect%20two%20Azure%20virtual%20networks%20using%20global%20virtual%20network%20peering)** est disponible et vous permet de progresser à votre propre rythme. Il peut exister de légères différences entre la simulation interactive et le labo hébergé. Toutefois, les concepts et idées de base présentés sont identiques.

### Durée estimée : 20 minutes

## Tâche 1 : créer une machine virtuelle pour tester la configuration

Dans cette section, vous allez créer une machine virtuelle de test sur le réseau virtuel Manufacturing pour tester si vous pouvez accéder à des ressources à l’intérieur d’un autre réseau virtuel Azure à partir de ManufacturingVnet.

### Créer ManufacturingVM

1. Sélectionnez l’icône Cloud Shell en haut à droite du portail Azure. Si nécessaire, configurez l’interpréteur de commandes.  
    + Sélectionnez **PowerShell**.
    + Sélectionnez **Aucun compte de stockage requis** et votre **abonnement**, puis sélectionnez **Appliquer**.
    + Attendez que le terminal crée et qu’une invite s’affiche. 

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Gérer des fichiers**, dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **ManufacturingVMazuredeploy.json** et **ManufacturingVMazuredeploy.parameters.json** dans le répertoire racine de Cloud Shell à partir du dossier source **F:\Allfiles\Exercises\M01**.

1. Déployez les modèles ARM suivants pour créer les machines virtuelles nécessaires à cet exercice :

   >**Remarque** : Vous serez invité à fournir un mot de passe d’administrateur.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile ManufacturingVMazuredeploy.json -TemplateParameterFile ManufacturingVMazuredeploy.parameters.json
   ```
  
1. Une fois le déploiement terminé, accédez à la page d’accueil du portail Azure, puis sélectionnez **Machines virtuelles**.

1. Vérifiez que la machine virtuelle a été créée.

## Tâche 2 : se connecter aux machines virtuelles de test à l’aide du protocole RDP

1. Sur la page d’accueil du portail Azure, sélectionnez **Machines virtuelles**.

1. Sélectionnez **ManufacturingVM**.

1. Dans ManufacturingVM, sélectionnez **Se connecter &gt; RDP**.

1. Dans ManufacturingVM \| Se connecter, sélectionnez **Télécharger le fichier RDP**.

1. Enregistrez le fichier RDP sur votre bureau.

1. Connectez-vous à ManufacturingVM à l’aide du fichier RDP, en employant le nom d’utilisateur **TestUser** et le mot de passe que vous avez spécifié pendant le déploiement.

1. Sur la page d’accueil du portail Azure, sélectionnez **Machines virtuelles**.

1. Sélectionnez **TestVM1**.

1. Dans TestVM1, sélectionnez **Se connecter &gt; RDP**.

1. Dans TestVM1 \| Se connecter, sélectionnez **Télécharger le fichier RDP**.

1. Enregistrez le fichier RDP sur votre bureau.

1. Connectez-vous à TestVM1 à l’aide du fichier RDP, en employant le nom d’utilisateur **TestUser** et le mot de passe que vous avez spécifié pendant le déploiement.

1. Sur les deux machines virtuelles, dans **Choisir les paramètres de confidentialité pour votre appareil**, sélectionnez **Accepter**.

1. Sur les deux machines virtuelles, dans **Réseaux**, sélectionnez **Oui**.

1. Sur TestVM1, ouvrez une invite PowerShell et exécutez la commande suivante : ipconfig

1. Notez l’adresse IPv4.

## Tâche 3 : tester la connexion entre les machines virtuelles

1. Sur ManufacturingVM, ouvrez une invite de commandes PowerShell.

1. Utilisez la commande suivante pour vérifier qu’il n’existe aucune connexion à TestVM1 sur CoreServicesVnet. Veillez à utiliser l’adresse IPv4 pour TestVM1.

   ```powershell
    Test-NetConnection 10.20.20.4 -port 3389
    ```

1. Le test de connexion doit échouer et un résultat similaire à ce qui suit s’affiche : ![Fenêtre PowerShell avec Test-NetConnection 10.20.20.4-port 3389 qui indique un échec ](../media/test-netconnection-fail.png)

## Tâche 4 : créer des peerings de réseaux virtuels entre CoreServicesVnet et ManufacturingVnet

1. Dans la page d’accueil Azure, sélectionnez **Réseaux virtuels**, puis **CoreServicesVnet**.

1. Dans CoreServicesVnet, sous **Paramètres**, sélectionnez **Peerings**.
   ![Capture d’écran des paramètres du peering de réseaux virtuels de services principaux ](../media/create-peering-on-coreservicesvnet.png)

1. Sur CoreServicesVnet \| Peerings, sélectionnez **+ Ajouter**.

1. Utilisez ces informations pour créer le peering. Lorsque vous avez terminé, sélectionnez **Ajouter**. 

   **Résumé du réseau virtuel distant**

   | **Option**                                    | **Valeur**                             |
   | ------------------------------------ | --------------------------------------------- | 
   | Nom du lien de peering    | `CoreServicesVnet-to-ManufacturingVnet` |
   | Réseau virtuel | ManufacturingVnet |

    **Paramètres d’appairage de réseaux virtuels distants**
   
   | **Option**                                    | **Valeur**                             |
   | ------------------------------------ | --------------------------------------------- | 
   | Autoriser « ManufacturingVnet » à accéder à « CoreServicesVnet » | Activé(e) |
   |Autoriser « ManufacturingVnet » à recevoir le trafic transféré à partir de « CoreServicesVnet » | Activé(e) |
 
    **Résumé du réseau virtuel local**

    | **Option**                                    | **Valeur**                             |
    | ------------------------------------ | --------------------------------------------- | 
    | Nom du lien de peering | `CoreServicesVnet-to-ManufacturingVnet` |
 
    **Paramètres d’appairage de réseaux virtuels distants**
   
    | **Option**                                    | **Valeur**                             |
    | ------------------------------------ | --------------------------------------------- | 
    | Autoriser « CoreServicesVnet » à accéder à « ManufacturingVnet » | Activé(e)
    | Autoriser « CoreServicesVnet » à recevoir le trafic transféré à partir de « ManufacturingVnet » | Activé(e) |
 
1. Dans CoreServicesVnet\|Peerings, vérifiez que le peering **CoreServicesVnet-to-ManufacturingVnet** est **Connecté**.

1. Dans Réseaux virtuels, sélectionnez **ManufacturingVnet**, puis vérifiez que le peering **ManufacturingVnet-to-CoreServicesVnet** est **Connecté**.

## Tâche 5 : tester la connexion entre les machines virtuelles

1. Sur ManufacturingVM, ouvrez une invite de commandes PowerShell.

1. Utilisez la commande suivante pour vérifier qu’il existe maintenant une connexion à TestVM1 sur CoreServicesVnet.

   ```powershell
    Test-NetConnection 10.20.20.4 -port 3389
    ```

1. Le test de connexion doit réussir et un résultat similaire à ce qui suit s’affiche : ![Fenêtre PowerShell avec Test-NetConnection 10.20.20.4 -port 3389 indiquant TCP test succeeded: true](../media/test-connection-succeeded.png)


## Nettoyer les ressources

   >**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**. (Créez un stockage Cloud Shell si nécessaire, en choisissant les paramètres par défaut.)

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob
   ```
   >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.
   
## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+ Quelles sont les erreurs les plus courantes lors de la configuration de l’appairage de réseaux virtuels Azure ?
+ Dans Azure, si j’appaire Vnet1 avec Vnet2, puis que j’appaire Vnet2 avec Vnet3, Vnet1 est-il appairé à Vnet3 ?
+ Les pare-feu et les passerelles peuvent-ils affecter l’appairage de réseaux virtuels Azure ?


## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Présentation des réseaux virtuels Azure](https://learn.microsoft.com/training/modules/introduction-to-azure-virtual-networks/). Dans ce module, vous apprenez à concevoir et à implémenter des services de mise en réseau Azure. Vous y découvrez les réseaux virtuels, les adresses IP publiques et privées, le DNS, l’appairage de réseaux virtuels, le routage et la NAT virtuelle Azure.
+ [Distribuez vos services sur des réseaux virtuels Azure et les intégrer en utilisant l’appairage de réseaux virtuels](https://learn.microsoft.com/training/modules/integrate-vnets-with-vnet-peering/). Dans ce module, vous allez apprendre à configurer l’appairage de réseaux virtuels.

## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 

+ Le peering de réseaux virtuels vous permet de connecter deux réseaux virtuels Azure en toute transparence. Les réseaux virtuels apparaissent comme un seul réseau à des fins de connectivité.
+ Azure prend en charge la connexion de réseaux virtuels au sein de la même région Azure et entre les régions Azure (global).
+ Le trafic entre les machines virtuelles dans des réseaux virtuels homologués est acheminé directement via l’infrastructure principale de Microsoft et non via une passerelle ou une connexion Internet publique.
+ Vous pouvez redimensionner l’espace d’adressage des réseaux virtuels Azure qui sont appairés sans que cela n’entraîne de temps d’arrêt sur l’espace d’adressage actuellement appairé.
