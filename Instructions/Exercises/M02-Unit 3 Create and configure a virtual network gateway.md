---
Exercise:
  title: M02 - Unité 3 Créer et configurer une passerelle de réseau virtuel
  module: Module 02 - Design and implement hybrid networking
---


# M02 - Unité 3 Créer et configurer une passerelle de réseau virtuel

## Scénario de l’exercice

Dans cet exercice, vous allez configurer une passerelle de réseau virtuel pour connecter le VNet Contoso Core Services et le VNet Manufacturing.

![Diagramme d’une passerelle de réseau virtuel.](../media/3-exercise-create-configure-local-network-gateway.png)

### Compétences de tâche

Dans cet exercice, vous allez :

+ Tâche 1 : Créer CoreServicesVnet et ManufacturingVnet
+ Tâche 2 : créer CoreServicesVM
+ Tâche 3 : créer ManufacturingVM
+ Tâche 4 : Se connecter aux machines virtuelles à l’aide du protocole RDP
+ Tâche 5 : Tester la connexion entre les machines virtuelles
+ Tâche 6 : Créer la passerelle CoreServicesVnet
+ Tâche 7 : Créer la passerelle ManufacturingVnet
+ Tâche 8 : Connecter CoreServicesVnet à ManufacturingVnet
+ Tâche 9 : Connecter ManufacturingVnet à CoreServicesVnet
+ Tâche 10 : Vérifier que les connexions sont établies
+ Tâche 11 : Tester la connexion entre les machines virtuelles

### Simulations de labo interactives

>**Note** : les simulations de labo qui ont été fournies précédemment ont été supprimées.

### Durée estimée : 70 minutes (dont environ 45 minutes d’attente pour le déploiement)

## Tâche 1 : Créer CoreServicesVnet et ManufacturingVnet

1. Sélectionnez l’icône Cloud Shell en haut à droite du portail Azure. Si nécessaire, configurez l’interpréteur de commandes.  
    + Sélectionnez **PowerShell**.
    + Sélectionnez **Aucun compte de stockage requis** et votre **abonnement**, puis sélectionnez **Appliquer**.
    + Attendez que le terminal crée et qu’une invite s’affiche. 

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Gérer des fichiers**. Dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **azuredeploy.json** et **azuredeploy.parameters.json** dans le répertoire de base de Cloud Shell.

        Note:: If you are working in your own subscription the [template files](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/tree/master/Allfiles/Exercises) are available in the GitHub lab repository.

1. Déployez les modèles ARM suivants pour créer le réseau virtuel et les sous-réseaux nécessaires à cet exercice :

   ```powershell
   $RGName = "ContosoResourceGroup"
   #create resource group if it doesnt exist
   New-AzResourceGroup -Name $RGName -Location "eastus"
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```
   
## Tâche 2 : créer CoreServicesVM

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Gérer des fichiers**, dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **CoreServicesVMazuredeploy.json** et **CoreServicesVMazuredeploy.parameters.json** l’un après l’autre dans le répertoire racine de Cloud Shell à partir du dossier source **F:\Allfiles\Exercises\M02**.

1. Déployez les modèles ARM suivants pour créer les machines virtuelles nécessaires à cet exercice :

   >**Remarque** : Vous serez invité à fournir un mot de passe d’administrateur.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile CoreServicesVMazuredeploy.json -TemplateParameterFile CoreServicesVMazuredeploy.parameters.json
   ```
  
1. Une fois le déploiement terminé, accédez à la page d’accueil du portail Azure, puis sélectionnez **Machines virtuelles**.

1. Vérifiez que la machine virtuelle a été créée.

## Tâche 3 : créer ManufacturingVM

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Gérer des fichiers**, dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **ManufacturingVMazuredeploy.json** et **ManufacturingVMazuredeploy.parameters.json** l’un après l’autre dans le répertoire racine de Cloud Shell à partir du dossier source **F:\Allfiles\Exercises\M02**.

1. Déployez les modèles ARM suivants pour créer les machines virtuelles nécessaires à cet exercice :

   >**Remarque** : Vous serez invité à fournir un mot de passe d’administrateur.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile ManufacturingVMazuredeploy.json -TemplateParameterFile ManufacturingVMazuredeploy.parameters.json
   ```
  
1. Une fois le déploiement terminé, accédez à la page d’accueil du portail Azure, puis sélectionnez **Machines virtuelles**.

1. Vérifiez que la machine virtuelle a été créée.

## Tâche 4 : Se connecter aux machines virtuelles à l’aide du protocole RDP

1. Sur la page d’accueil du portail Azure, sélectionnez **Machines virtuelles**.

1. Sélectionnez **ManufacturingVM**.

1. Dans **ManufacturingVM**, sélectionnez **Se connecter**, puis **RDP**.

1. Sélectionnez **Télécharger le fichier RDP**.

1. Enregistrez le fichier RDP sur votre bureau.

1. Connectez-vous à **ManufacturingVM** à l’aide du fichier RDP, ainsi que du nom d’utilisateur **TestUser** et du mot de passe que vous avez spécifié pendant le déploiement. Après la connexion, réduisez la session RDP.

1. Sur la page d’accueil du portail Azure, sélectionnez **Machines virtuelles**.

1. Sélectionnez **CoreServicesVM**.

1. Dans **CoreServicesVM**, sélectionnez **Se connecter**, puis **RDP**.

1. Sélectionnez **Télécharger le fichier RDP**.

1. Enregistrez le fichier RDP sur votre bureau.

1. Connectez-vous à **CoreServicesVM** à l’aide du fichier RDP, ainsi que le nom d’utilisateur **TestUser** et du mot de passe que vous avez spécifié pendant le déploiement.

1. Sur les deux machines virtuelles, dans **Choisir les paramètres de confidentialité pour votre appareil**, sélectionnez **Accepter**.

1. Sur les deux machines virtuelles, dans **Réseaux**, sélectionnez **Oui**.

1. Sur **CoreServicesVM**, ouvrez PowerShell, puis exécutez la commande suivante : ipconfig

1. Notez l’adresse IPv4.

## Tâche 5 : tester la connexion entre les machines virtuelles

1. Sur **ManufacturinVM**, ouvrez PowerShell.

1. Exécutez la commande suivante pour vérifier qu’il n’existe aucune connexion à CoreServicesVM sur CoreServicesVnet. Veillez à utiliser l’adresse IPv4 de CoreServicesVM.

   ```Powershell
   Test-NetConnection 10.20.20.4 -port 3389
   ```

1. Le test de connexion doit échouer et un résultat similaire à ce qui suit doit s’afficher :

   ![Échec de Test-NetConnection.](../media/test-netconnection-fail.png)

## Tâche 6 : Créer la passerelle CoreServicesVnet

1. Dans **Rechercher des ressources, des services et des documents (G+/)**, tapez **passerelle de réseau virtuel**, puis sélectionnez **Passerelles de réseaux virtuels** dans les résultats.
   ![Recherche de passerelle de réseau virtuel sur le portail Azure.](../media/virtual-network-gateway-search.png)

1. Dans Passerelles de réseau virtuel, sélectionnez **+ Créer**.

1. Utilisez les informations du tableau suivant pour créer la passerelle de réseau virtuel :

   | **Tab**         | **Section**       | **Option**                                  | **Valeur**                    |
   | --------------- | ----------------- | ------------------------------------------- | ---------------------------- |
   | Concepts de base          | Détails du projet   | Abonnement                                | Aucune modification n’est requise          |
   |                 |                   | ResourceGroup                               | ContosoResourceGroup         |
   |                 | Détails de l’instance  | Nom                                        | CoreServicesVnetGateway      |
   |                 |                   | Région                                      | USA Est                      |
   |                 |                   | Type de passerelle                                | VPN                          |
   |                 |                   | SKU                                         | VpnGw1                       |
   |                 |                   | Generation                                  | Génération1                  |
   |                 |                   | Réseau virtuel                             | CoreServicesVnet             |
   |                 |                   | Sous-réseau                                      | GatewaySubnet (10.20.0.0/27) |
   |                 |                   | Type d’adresse IP publique                      | Standard                     |
   |                 | Adresse IP publique | Adresse IP publique                           | Création                   |
   |                 |                   | Nom de l’adresse IP publique                      | CoreServicesVnetGateway-ip   |
   |                 |                   | Activer le mode actif/actif                   | Désactivé                     |
   |                 |                   | Configurer BGP                               | Désactivé                     |
   | Vérifier + créer |                   | Passez en revue vos paramètres, puis sélectionnez **Créer**. |                              |

   >**Remarque** : la création d’une passerelle de réseau virtuel peut prendre de 15 à 30 minutes. Vous n’avez pas besoin d’attendre que le déploiement soit terminé. Passez à la création de la passerelle suivante. 

## Tâche 7 : Créer la passerelle ManufacturingVnet

### Créer le GatewaySubnet

   >**Remarque :** le modèle a créé le GatewaySubnet pour le CoreServicesVnet. Ici, vous créez le sous-réseau manuellement. 

1. Recherchez et sélectionnez le **manufacturingVnet**.

1. Dans le panneau **Paramètres**, sélectionnez **Sous-réseaux**, puis **+ Sous-réseau**. 

    | Paramètre | Valeur |
    | --------------- | ----------------- | 
    | Objectif du sous-réseau | **Passerelle de réseau virtuel** |
    | Taille | **/27 (32 adresses)** |

1. Sélectionnez **Ajouter**. 

### Créer la passerelle de réseau virtuel

1. Dans **Rechercher des ressources, des services et des documents (G+/)**, tapez **passerelle de réseau virtuel**, puis sélectionnez **Passerelles de réseaux virtuels** dans les résultats.

1. Dans Passerelles de réseau virtuel, sélectionnez **+ Créer**.

1. Utilisez ces informations et l’onglet **Paramètres** pour créer la passerelle de réseau virtuel. 

   | **Tab**         | **Section**       | **Option**                                  | **Valeur**                    |
   | --------------- | ----------------- | ------------------------------------------- | ---------------------------- |
   | Concepts de base          | Détails du projet   | Abonnement                                | Aucune modification n’est requise          |
   |                 |                   | ResourceGroup                               | ContosoResourceGroup         |
   |                 | Détails de l’instance  | Nom                                        | ManufacturingVnetGateway     |
   |                 |                   | Région                                      | Europe Ouest                  |
   |                 |                   | Type de passerelle                                | VPN                          |
   |                 |                   | SKU                                         | VpnGw1                       |
   |                 |                   | Generation                                  | Génération1                  |
   |                 |                   | Réseau virtuel                             | ManufacturingVnet            |
   |                 |                   | Sous-réseau                                      | GatewaySubnet |
   |                 |                   | Type d’adresse IP publique                      | Standard                     |
   |                 | Adresse IP publique | Adresse IP publique                           | Création                   |
   |                 |                   | Nom de l’adresse IP publique                      | ManufacturingVnetGateway-ip  |
   |                 |                   | Activer le mode actif/actif                   | Désactivé                     |
   |                 |                   | Configurer BGP                               | Désactivé                     |
   | Vérifier + créer |                   | Passez en revue vos paramètres, puis sélectionnez **Créer**. |                              |

   >**Remarque** : la création d’une passerelle de réseau virtuel peut prendre de 15 à 30 minutes.

## Tâche 8 : Connecter CoreServicesVnet à ManufacturingVnet

1. Dans **Rechercher des ressources, des services et des documents (G+/)**, tapez **passerelle de réseau virtuel**, puis sélectionnez **Passerelles de réseaux virtuels** dans les résultats.

1. Dans Passerelles de réseau virtuel, sélectionnez **CoreServicesVnetGateway**.

1. Dans CoreServicesGateway, sélectionnez **Connexions**, puis **+ Ajouter**.

   >**Remarque** : vous ne pouvez pas effectuer cette configuration tant que les passerelles de réseau virtuel ne sont pas entièrement déployées.

1. Utilisez ces informations et l’onglet **Paramètres** pour créer la passerelle de réseau virtuel. 


   | **Option**                     | **Valeur**                         |
   | ------------------------------ | --------------------------------- |
   | Nom                           | CoreServicesGW-to-ManufacturingGW |
   | Type de connexion                | Connexion entre deux réseaux virtuels                      |
   | Région                         | USA Est                           |
   | Passerelle du premier réseau virtuel  | CoreServicesVnetGateway           |
   | Passerelle du deuxième réseau virtuel | ManufacturingVnetGateway          |
   | Clé partagée (PSK)               | abc123                            |
   | Utiliser des adresses IP privées Azure   | Non sélectionné                      |
   | Activer BGP                     | Non sélectionné                      |
   | Protocole IKE                   | IKEv2                             |
   | Abonnement                   | Aucune modification n’est requise               |
   | Resource group                 | Aucune modification n’est requise               |


1. Sélectionnez **Vérifier et créer**, puis **Créer** pour créer la connexion.

## Tâche 9 : Connecter ManufacturingVnet à CoreServicesVnet

1. Dans **Rechercher des ressources, des services et des documents (G+/)**, tapez **passerelle de réseau virtuel**, puis sélectionnez **Passerelles de réseaux virtuels** dans les résultats.

1. Dans Passerelles de réseau virtuel, sélectionnez **ManufacturingVnetGateway**.

1. Dans CoreServicesGateway, sélectionnez **Connexions**, puis **+ Ajouter**.

1. Utilisez les informations du tableau suivant pour créer la connexion :

   | **Option**                     | **Valeur**                         |
   | ------------------------------ | --------------------------------- |
   | Nom                           | ManufacturingGW-to-CoreServicesGW |
   | Type de connexion                | Connexion entre deux réseaux virtuels                      |
   | Emplacement                       | Europe Nord                      |
   | Passerelle du premier réseau virtuel  | ManufacturingVnetGateway          |
   | Passerelle du deuxième réseau virtuel | CoreServicesVnetGateway           |
   | Clé partagée (PSK)               | abc123                            |
   | Utiliser des adresses IP privées Azure   | Non sélectionné                      |
   | Activer BGP                     | Non sélectionné                      |
   | Protocole IKE                   | IKEv2                             |
   | Abonnement                   | Aucune modification n’est requise               |
   | Resource group                 | Aucune modification n’est requise               |


1. Sélectionnez **Vérifier et créer**, puis **Créer** pour créer la connexion.

## Tâche 10 : Vérifier que les connexions sont établies

1. Dans **Rechercher des ressources, des services et des documents (G+/)**, entrez **VPN**, puis sélectionnez **Connexions** dans les résultats.

1. Attendez que l’état des deux connexions soit **Connecté**. Vous devrez peut-être actualiser l’écran.

   ![Connexions à la passerelle VPN correctement créées.](../media/connections-status-connected.png)

## Tâche 11 : Tester la connexion entre les machines virtuelles

1. Sur **ManufacturinVM**, ouvrez PowerShell.

1. Exécutez la commande suivante pour vérifier qu’il existe maintenant une connexion à CoreServicesVM sur CoreServicesVnet. Veillez à utiliser l’adresse IPv4 de CoreServicesVM.

   ```Powershell
   Test-NetConnection 10.20.20.4 -port 3389
   ```

1. Le test de connexion doit aboutir et un résultat similaire à ce qui suit doit s’afficher :

   ![Test-NetConnection réussie.](../media/test-connection-succeeded.png)

1. Fermez la fenêtre de connexion Bureau à distance.

## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+ Quels sont les principaux types de passerelles VPN Azure et à quoi servent-ils ?
+ Quels facteurs dois-je prendre en compte lors de la sélection de la référence (SKU) de la passerelle VPN Azure ? Donnez des exemples.
+ Existe-t-il des coûts associés aux passerelles VPN Azure ?


## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Connectez votre réseau local à Azure avec une passerelle VPN](https://learn.microsoft.com/training/modules/connect-on-premises-network-with-vpn-gateway/). Dans ce module, vous allez utiliser l’interface CLI pour approvisionner des passerelles VPN.
+ [Résolvez les problèmes de passerelles VPN dans Microsoft Azure](https://learn.microsoft.com/training/modules/troubleshoot-vpn-gateways/). Dans ce module, vous allez découvrir comment surveiller et résoudre les problèmes de VPN site à site et point à site.

## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 

+ La passerelle VPN Azure est un service qui fournit une connectivité sécurisée entre vos réseaux locaux et vos réseaux virtuels Azure.
+ Des connexion site à site (S2S) permettent de connecter votre réseau local à un réseau virtuel Azure à l’aide de tunnels VPN IPsec/IKE. Idéal pour les scénarios de cloud hybride.
+ Les connexions point à site (P2S) connnectent des clients individuels vers un réseau virtuel Azure à partir d’emplacements distants. Les protocoles VPN comprennent OpenVPN, IKEv2 ou SSTP. Utile pour les télétravailleurs.
+ Les connexions de réseau virtuel à réseau virtuel connectent deux réseaux virtuels Azure ou plus à l’aide de tunnels VPN IPsec/IKE. Convient aux déploiements sur plusieurs régions ou plusieurs éseaux virtuels.
+ Différentes références(SKU) de passerelle VPN offrent des niveaux de performances, de débit et de fonctionnalités divers. 

