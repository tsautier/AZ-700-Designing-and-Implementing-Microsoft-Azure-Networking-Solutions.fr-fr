---
Exercise:
  title: M01 - Unité 8 Connecter deux réseaux virtuels Azure à l’aide du peering de réseaux virtuels globaux
  module: Module 01 - Introduction to Azure Virtual Networks
---
# M01 - Unité 8 Connecter deux réseaux virtuels Azure à l’aide du peering de réseaux virtuels globaux

## Scénario de l’exercice 
Dans cette unité, vous allez configurer la connectivité entre CoreServicesVnet et ManufacturingVnet en ajoutant des peerings pour autoriser le flux de trafic. 

Dans cette unité, vous allez :

+ Tâche 1 : Créer une machine virtuelle pour tester la configuration
+ Tâche 2 : Se connecter aux machines virtuelles de test à l’aide du protocole RDP
+ Tâche 3 : Tester la connexion entre les machines virtuelles
+ Tâche 4 : Créer des peerings de réseaux virtuels entre CoreServicesVnet et ManufacturingVnet
+ Tâche 5 : Tester la connexion entre les machines virtuelles
+ Tâche 6 : Nettoyer les ressources

**Remarque :** Une **[simulation de labo interactive](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Connect%20two%20Azure%20virtual%20networks%20using%20global%20virtual%20network%20peering)** est disponible et vous permet de progresser à votre propre rythme. Il peut exister de légères différences entre la simulation interactive et le labo hébergé. Toutefois, les concepts et idées de base présentés sont identiques.

#### Durée estimée : 20 minutes

## Tâche 1 : Créer une machine virtuelle pour tester la configuration

Dans cette section, vous allez créer une machine virtuelle de test sur le réseau virtuel Manufacturing pour tester si vous pouvez accéder à des ressources à l’intérieur d’un autre réseau virtuel Azure à partir de ManufacturingVnet.

### Créer ManufacturingVM

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.
  > **Remarque :** si c’est la première fois que vous ouvrez Cloud Shell, vous serez peut-être invité à créer un compte de stockage. Sélectionnez **Créer le stockage**.

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Charger/télécharger des fichiers**, dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **ManufacturingVMazuredeploy.json** et **ManufacturingVMazuredeploy.parameters.json** l’un après l’autre dans le répertoire racine de Cloud Shell à partir du dossier source **F:\Allfiles\Exercises\M01**.

1. Déployez les modèles ARM suivants pour créer les machines virtuelles nécessaires à cet exercice :

   >**Remarque** : Vous serez invité à fournir un mot de passe d’administrateur.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile ManufacturingVMazuredeploy.json -TemplateParameterFile ManufacturingVMazuredeploy.parameters.json
   ```
  
1. Une fois le déploiement terminé, accédez à la page d’accueil du portail Azure, puis sélectionnez **Machines virtuelles**.

1. Vérifiez que la machine virtuelle a été créée.

## Tâche 2 : Se connecter aux machines virtuelles de test à l’aide du protocole RDP

1. Sur la page d’accueil du portail Azure, sélectionnez **Machines virtuelles**.

1. Sélectionnez **ManufacturingVM**.

1. Dans ManufacturingVM, sélectionnez **Se connecter &gt; RDP**.

1. Dans ManufacturingVM | Se connecter, sélectionnez **Télécharger le fichier RDP**.

1. Enregistrez le fichier RDP sur votre bureau.

1. Connectez-vous à ManufacturingVM à l’aide du fichier RDP, en employant le nom d’utilisateur **TestUser** et le mot de passe que vous avez spécifié pendant le déploiement.

1. Sur la page d’accueil du portail Azure, sélectionnez **Machines virtuelles**.

1. Sélectionnez **TestVM1**.

1. Dans TestVM1, sélectionnez **Se connecter &gt; RDP**.

1. Dans TestVM1 | Se connecter, sélectionnez **Télécharger le fichier RDP**.

1. Enregistrez le fichier RDP sur votre bureau.

1. Connectez-vous à TestVM1 à l’aide du fichier RDP, en employant le nom d’utilisateur **TestUser** et le mot de passe que vous avez spécifié pendant le déploiement.

1. Sur les deux machines virtuelles, dans **Choisir les paramètres de confidentialité pour votre appareil**, sélectionnez **Accepter**.

1. Sur les deux machines virtuelles, dans **Réseaux**, sélectionnez **Oui**.

1. Sur TestVM1, ouvrez une invite PowerShell et exécutez la commande suivante : ipconfig

1. Notez l’adresse IPv4. 

 

## Tâche 3 : Tester la connexion entre les machines virtuelles

1. Sur ManufacturingVM, ouvrez une invite de commandes PowerShell.

1. Utilisez la commande suivante pour vérifier qu’il n’existe aucune connexion à TestVM1 sur CoreServicesVnet. Veillez à utiliser l’adresse IPv4 pour TestVM1.

   ```powershell
    Test-NetConnection 10.20.20.4 -port 3389
    ```


1. Le test de connexion doit échouer et un résultat similaire à ce qui suit s’affiche : ![Fenêtre PowerShell avec Test-NetConnection 10.20.20.4-port 3389 qui indique un échec ](../media/test-netconnection-fail.png)

 

## Tâche 4 : Créer des peerings de réseaux virtuels entre CoreServicesVnet et ManufacturingVnet

1. Dans la page d’accueil Azure, sélectionnez **Réseaux virtuels**, puis **CoreServicesVnet**.

1. Dans CoreServicesVnet, sous **Paramètres**, sélectionnez **Peerings**.
   ![Capture d’écran des paramètres du peering de réseaux virtuels de Core Services ](../media/create-peering-on-coreservicesvnet.png)

1. Sur CoreServicesVnet | Peerings, sélectionnez **+ Ajouter**.

1. Utilisez les informations du tableau suivant pour créer le peering.

| **Section**                          | **Option**                                    | **Valeur**                             |
| ------------------------------------ | --------------------------------------------- | ------------------------------------- |
| Ce réseau virtuel                 |                                               |                                       |
|                                      | Nom du lien de peering                             | CoreServicesVnet-to-ManufacturingVnet |
|                                      | Trafic vers le réseau virtuel distant             | Autoriser (par défaut)                       |
|                                      | Trafic transféré à partir du réseau virtuel distant | Autoriser (par défaut)                       |
|                                      | Passerelle ou serveur de routes de réseau virtuel       | Aucun (par défaut)                        |
| Réseau virtuel distant               |                                               |                                       |
|                                      | Nom du lien de peering                             | ManufacturingVnet-to-CoreServicesVnet |
|                                      | Modèle de déploiement de réseau virtuel              | Gestionnaire des ressources                      |
|                                      | Je connais mon ID de ressource                         | Non sélectionné                          |
|                                      | Abonnement                                  | Sélectionnez l’abonnement fourni      |
|                                      | Réseau virtuel                               | ManufacturingVnet                     |
|                                      | Trafic vers le réseau virtuel distant             | Autoriser (par défaut)                       |
|                                      | Trafic transféré à partir du réseau virtuel distant | Autoriser (par défaut)                       |
|                                      | Passerelle ou serveur de routes de réseau virtuel       | Aucun (par défaut)                        |
| Passez en revue vos paramètres, puis sélectionnez Ajouter. |                                               |                                       |
|                                      |                                               |                                       |

 >**Remarque** : si vous n’avez pas d’abonnement MOC, utilisez l’abonnement dont vous vous serviez précédemment. Il doit consister en un simple nom.

1. Dans CoreServicesVnet | Peerings, vérifiez que le peering **CoreServicesVnet-ManufacturingVnet** est répertorié.

1. Sous Réseaux virtuels, sélectionnez **ManufacturingVnet**, puis vérifiez que le peering **ManufacturingVnet-to-CoreServicesVnet** est répertorié.

 

## Tâche 5 : Tester la connexion entre les machines virtuelles

1. Sur ManufacturingVM, ouvrez une invite de commandes PowerShell.

1. Utilisez la commande suivante pour vérifier qu’il existe maintenant une connexion à TestVM1 sur CoreServicesVnet. 

   ```powershell
    Test-NetConnection 10.20.20.4 -port 3389
    ```


1. Le test de connexion doit réussir et un résultat similaire à ce qui suit s’affiche : ![Fenêtre PowerShell avec Test-NetConnection 10.20.20.4 -port 3389 indiquant TCP test succeeded: true](../media/test-connection-succeeded.png)

 

Félicitations ! Vous avez réussi à configurer la connectivité entre les réseaux virtuels en ajoutant des peerings. 

## Tâche 6 : Nettoyer les ressources

   >**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**. (Créez un stockage Cloud Shell si nécessaire, en choisissant les paramètres par défaut.)

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob
   ```

    >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.
