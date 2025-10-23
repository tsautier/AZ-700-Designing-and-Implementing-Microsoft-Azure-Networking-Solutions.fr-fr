---
Exercise:
  title: "M06 - Unité 9 Sécuriser votre hub virtuel avec Azure\_Firewall\_Manager"
  module: Module 06 - Design and implement network security
---


# M06 - Unité 9 Sécuriser votre hub virtuel avec Azure Firewall Manager

## Scénario de l’exercice

Dans cet exercice, vous allez créer le réseau virtuel Spoke et créer un hub virtuel sécurisé, puis connecter les réseaux virtuels Hub and Spoke et acheminer le trafic vers votre hub. Ensuite, vous allez déployer les serveurs de charge de travail, puis créer une stratégie de pare-feu et sécuriser votre hub. Enfin, vous allez tester le pare-feu.

![Diagramme de l’architecture de réseau virtuel avec un hub sécurisé.](../media/9-exercise-secure-your-virtual-hub-using-azure-firewall-manager.png)

## Créer une architecture hub and spoke

Dans cette partie de l’exercice, vous allez créer les réseaux virtuels Spoke et les sous-réseaux dans lesquels vous allez placer les serveurs de charge de travail. Vous allez ensuite créer le hub virtuel sécurisé et connecter les réseaux virtuels Hub and Spoke.

### Compétences de tâche

Dans cet exercice, vous allez :

+ Tâche 1 : créer deux réseaux virtuels et sous-réseaux à rayons
+ Tâche 2 : créer le hub virtuel sécurisé
+ Tâche 3 : connecter les réseaux virtuels en étoile
+ Tâche 4 : déployer les serveurs
+ Tâche 5 : créer une stratégie de pare-feu et sécuriser votre hub
+ Tâche 6 : associer la stratégie de pare-feu
+ Tâche 7 : acheminer le trafic vers votre hub
+ Tâche 8 : tester la règle d’application
+ Tâche 9 : tester la règle réseau
+ Tâche 10 : Nettoyer les ressources

### Durée estimée : 35 minutes

## Tâche 1 : créer deux réseaux virtuels et sous-réseaux à rayons

Au cours de cette tâche, vous allez créer les deux réseaux virtuels Spoke contenant chacun un sous-réseau qui hébergera vos serveurs de charge de travail.

1. Dans le portail Azure, recherchez et sélectionnez `Virtual Networks`.

1. Sélectionnez **Créer**.

1. Dans **Groupe de ressources**, sélectionnez **Créer un nouveau**, puis entrez `fw-manager-rg` comme nom et sélectionnez **OK**.

1. Dans **Nom du réseau virtuel**, entrez `Spoke-01`.

1. Dans **Région**, sélectionnez votre région.

1. Cliquez sur **Suivant**. Vérifiez l’onglet **Sécurité** sans y apporter de modifications. 

1. Sélectionnez **Suivant** et accédez à l’onglet **Adresses IP**.

1. Sélectionnez **Supprimer l’espace d’adressage**, puis **Ajoutez un espace d’adressage IPv4**. 

1. Vérifiez que l’espace d’adressage IP est **10.0.0.0/16**.

1. Sélectionnez **Ajouter un sous-réseau**. 

1. Changez le **Nom** du sous-réseau en `Workload-01-SN`.

1. Changez l’**Adresse de départ** en `10.0.1.0`.

1. Sélectionnez **Ajouter**.

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Créer**.

Répétez les étapes 1 à 14 ci-dessus pour créer un autre réseau virtuel et sous-réseau similaires à l'aide des informations suivantes. Vous n’avez pas besoin d’attendre que le premier réseau virtuel termine son déploiement. 

+ Groupe de ressources : **fw-manager-rg** (sélectionner un groupe existant)
+ Nom du réseau virtuel : `Spoke-02`
+ Espace d’adressage : **10.1.0.0/16** (supprimer tous les autres espaces d’adressage listés)
+ Nom du sous-réseau : `Workload-02-SN`
+ Adresse de début du sous-réseau : `10.1.1.0`

## Tâche 2 : créer le hub virtuel sécurisé

Au cours de cette tâche, vous allez créer votre hub virtuel sécurisé à l’aide de Firewall Manager.

1. Dans le portail, recherchez `firewall manager`, puis sélectionnez **Gestionnaire de pare-feu de mots clés de sécurité réseau**.
   
1. Dans le panneau **Sécuriser vos ressources**, sélectionnez **Hubs virtuels**.

1. Sélectionnez **Créer un nouvel hub virtuel sécurisé**.

1. Pour **Groupe de ressources**, sélectionnez **fw-manager-rg**.

1. Pour **Région**, sélectionnez votre région.

1. Pour le **Nom du hub virtuel sécurisé**, entrez `Hub-01`.

1. Pour **Espace d’adressage du hub**, entrez `10.2.0.0/16`.

1. S’assurer que **Nouveau vWAN** est sélectionné

1. Dans **Nom de WAN virtuel**, entrez `Vwan-01`.

1. Sélectionnez **Suivant : Pare-feu Azure**. Vérifiez, mais n’apportez aucune modification. 

1. Sélectionnez **Suivant : Fournisseur de partenaire de sécurité**. Vérifiez, mais n’apportez aucune modification. 

1. Sélectionnez **Suivant : Vérifier + créer**.

1. Sélectionnez **Créer**.

    > Note : Le déploiement peut durer jusqu’à 30 minutes.

1. Attendez la fin du déploiement. 

1. Dans le portail, recherchez `firewall manager`, puis sélectionnez **Gestionnaire de pare-feu de mots clés de sécurité réseau**.

1. Dans la page **Firewall Manager**, sélectionnez **Afficher les hubs virtuels**.

1. Sélectionnez **Hub-01**.

1. Sélectionnez **Pare-feu Azure** puis **Configuration de l'adresse IP publique**.

1. Notez l'adresse IP publique (par exemple,**172.191.79.203**), que vous utiliserez plus tard.

## Tâche 3 : connecter les réseaux virtuels en étoile

Au cours de cette tâche, vous allez connecter les réseaux virtuels Hub and Spoke. Cela est communément appelé peering.

1. Dans le portail, recherchez et sélectionnez le `Vwan-01` WAN virtuel.

1. Sous **Connectivité**, sélectionnez **Connexions de réseau virtuel**.

1. Sélectionnez **Ajouter une connexion**.

1. Pour **Connection name** (Nom de la connexion, entrez `hub-spoke-01`.

1. Pour **Hubs**, sélectionnez **Hub-01**.

1. Pour **Groupe de ressources**, sélectionnez **fw-manager-rg**.

1. Pour **Réseau virtuel**, sélectionnez **Spoke-01**.

1. Sélectionnez **Créer**.

1. Répétez les étapes 4 à 9 ci-dessus pour créer une autre connexion similaire, mais en utilisant le nom de connexion `hub-spoke-02` pour connecter le réseau virtuel **Spoke-02**.

1. **Actualisez** la page des connexions réseau virtuelles et vérifiez que vous disposez de deux réseaux virtuels, Spoke-01 et Spoke-02.
   
## Tâche 4 : déployer les serveurs

1. Sélectionnez l’icône Cloud Shell en haut à droite du portail Azure. Si nécessaire, configurez l’interpréteur de commandes.  
    + Sélectionnez **PowerShell**.
    + Sélectionnez **Aucun compte de stockage requis** et votre **abonnement**, puis sélectionnez **Appliquer**.
    + Attendez que le terminal crée et qu’une invite s’affiche. 

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Gérer des fichiers**. Dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **FirewallManager.json** et **FirewallManager.parameters.json** dans le répertoire de base de Cloud Shell.

    > **Note :** si vous travaillez dans votre propre abonnement, les [fichiers de modèles](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/tree/master/Allfiles/Exercises) sont disponibles dans le référentiel de labo GitHub.

1. Déployez les modèles ARM suivants pour créer les machines virtuelles nécessaires à cet exercice :

   >**Remarque** : Vous serez invité à fournir un mot de passe d’administrateur. **Vous aurez besoin de ce mot de passe dans une étape ultérieure.**

   ```powershell
   $RGName = "fw-manager-rg"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile FirewallManager.json -TemplateParameterFile FirewallManager.parameters.json
   ```
  
1. Une fois le déploiement terminé, accédez à la page d’accueil du portail Azure, puis sélectionnez **Machines virtuelles**.

1. Dans la page **Vue d’ensemble** de **Srv-workload-01**, dans le volet de droite, sous la section **Réseau**, notez l’**Adresse IP privée** (par exemple **10.0.1.4**).

1. Dans la page **Vue d’ensemble** de **Srv-workload-02**, dans le volet de droite, sous la section **Réseau**, notez l’**Adresse IP privée** (par exemple **10.1.1.4**).

## Tâche 5 : créer une stratégie de pare-feu et sécuriser votre hub

Au cours de cette tâche, vous allez d’abord créer votre stratégie de pare-feu, puis sécuriser votre hub. La stratégie de pare-feu définit des collections de règles pour diriger le trafic sur un ou plusieurs hubs virtuels sécurisés.

1. Dans le portail, recherchez et sélectionnez `Firewall Policies`.

1. Sélectionnez **Créer**.

    | **Paramètre**    | **Valeur** |
    | ---------- | --------------|
    | Resource group | **fw-manager-rg** |
    | Nom    | `Policy-01` |
    | Région     | Sélectionner votre région |
    | Niveau de stratégie | **Standard** |

1. Sélectionnez **Suivant : Paramètres DNS**. Vérifiez, mais n’apportez aucune modification. 

1. Sélectionnez **Suivant : Inspection TLS**. Vérifiez, mais n’apportez aucune modification. 

**Ajouter un regroupement de règles et une règle pour autoriser le domaine Microsoft**

1. Sélectionnez **Suivant : Règles**, puis sélectionnez **Ajouter un regroupement de règles**.

   | **Paramètre** | **Valeur** |
   | ---------- | --------------|
   | Nom        |  `App-RC-01` |
   | Type de regroupement de règles | **Application** |
   | Priorité    | `100` |
   | Action de regroupement de règles | **Autoriser** |

1. Dans la section **Règles**.

   | **Paramètre** | **Valeur** |
   | ---------- | --------------|
   | Nom |  `Allow-msft` |
   | Type de source | **Adresse IP** |
   | Source | `*` |
   | Protocol | `http,https` |
   | Type de destination | **FQDN** |
   | Destination | `*.microsoft.com` |

**Ajoutez un regroupement de règles et une règle pour autoriser une connexion Bureau à distance à la machine virtuelle Srv-workload-01.**

1. Sélectionnez **Ajouter une collection de règles**.

   | **Paramètre** | **Valeur** |
   | ---------- | --------------|
   | Nom        |  `dnat-rdp` |
   | Type de regroupement de règles | **DNAT** |
   | Priority    | `100` |
   | Action de regroupement de règles | **Autoriser** |

1. Dans la section **Règles**.

   | **Paramètre** | **Valeur** |
   | ---------- | --------------|
   | Nom |  `Allow-rdp` |
   | Type de source | **Adresse IP** |
   | Source | `*` |
   | Protocole | **TCP** |
   | Ports de destination | `3389` |
   | Destination (adresse IP du pare-feu) | Entrer l’adresse IP publique du hub virtuel du pare-feu |
   | Type traduit | **Adresse IP** |
   | Adresse ou FQDN traduit | Entrer l’adresse IP privée pour la machine virtuelle Srv-workload-01 |
   | Port traduit | `3389` |
   
**Ajoutez un regroupement de règles et une règle pour autoriser une connexion Bureau à distance à la machine virtuelle Srv-workload-02.**

1. Sélectionnez **Ajouter une collection de règles**.

   | **Paramètre** | **Valeur** |
   | ---------- | --------------|
   | Nom        |  `vnet-rdp` |
   | Type de regroupement de règles | **Réseau** |
   | Priorité    | `100` |
   | Action de regroupement de règles | **Autoriser** |

1. Dans la section **Règles**.

   | **Paramètre** | **Valeur** |
   | ---------- | --------------|
   | Nom |  `Allow-vnet` |
   | Type de source | **Adresse IP** |
   | Source | `*` |
   | Protocole | **TCP** |
   | Ports de destination | `3389` |
   | Destination (adresse IP du pare-feu) | Entrer l’adresse IP publique du hub virtuel du pare-feu |
   | Type traduit | **Adresse IP** |
   | Adresse ou FQDN traduit | Entrer l’adresse IP privée pour la machine virtuelle Srv-workload-02 |
   | Port traduit | `3389` |

1. Sélectionnez **Ajouter**.

1. Vérifiez que vous disposez de trois regroupements de règles.

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

## Tâche 6 : associer la stratégie de pare-feu

Au cours de cette tâche, vous allez associer la stratégie de pare-feu au hub virtuel.

1. Dans le portail, recherchez et sélectionnez `Hub-01`.

1. Dans le volet **Paramètres**, sélectionnez **Fournisseurs de sécurité**

1. Cochez la case pour **Ajouter une politique**.

1. Sélectionnez **Policy-01** puis **Enregistrer**.

1. Activez la case à cocher **Hub-01**.

1. Sélectionnez **Ajouter**.

1. Une fois la police associée, sélectionnez **Actualiser**. L’association doit être affichée.

## Tâche 7 : acheminer le trafic vers votre hub

Pour cette tâche, vous devez vérifiez que le trafic réseau est acheminé via votre pare-feu.

1. Dans le portail, recherchez et sélectionnez **Vwan-01**.

1. Dans le volet **Connectivité**, sélectionnez **Concentrateurs**, puis **Hub-01**.
   
1. Dans le volet **Sécurité**, sélectionnez **Pare-feu Azure et Gestionnaire de pare-feu**, sélectionnez **Hub-01**, puis **Configuration de la sécurité.**.

1. Dans **Trafic Internet**, sélectionnez **Pare-feu Azure**.

1. Dans **Trafic privé**, sélectionnez **Envoyer via le pare-feu Azure**.

1. Sélectionnez **Enregistrer** et cliquez sur **OK** pour confirmer votre choix.

1. L’exécution de cette opération nécessite quelques minutes.

1. Une fois la configuration terminée, vérifiez que sous **TRAFIC INTERNET** et **TRAFIC PRIVÉ**, vous pouvez voir **Sécurisé par le pare-feu Azure** pour les connexions Hub and Spoke.

## Tâche 8 : tester la règle d’application

Dans cette partie de l’exercice, vous allez connecter un bureau à distance à l’adresse IP publique du pare-feu, traduite par NAT en Srv-workload-01. De là, vous allez utiliser un navigateur web pour tester la règle d’application et connecter un bureau à distance à Srv-Workload-02 pour tester la règle réseau.

Dans cette tâche, vous allez tester la règle d’application pour confirmer qu’elle fonctionne comme prévu.

1. Ouvrez **Connexion Bureau à distance** sur votre PC.

1. Dans la zone **Ordinateur**, entrez l’**adresse IP publique du pare-feu** (par exemple **51.143.226.18**).

1. Sélectionnez **Afficher les options**.

1. Dans la zone **Nom d’utilisateur**, entrez **TestUser**.

1. Sélectionnez **Se connecter**.

   ![Connexion RDP à srv-workload-01](../media/rdp-srv-workload-01.png)

1. Dans la boîte de dialogue **Entrez vos informations d’identification**, connectez-vous à la machine virtuelle du serveur **Srv-workload-01** à l’aide du mot de passe que vous avez spécifié pendant le déploiement.

1. Cliquez sur **OK**.

1. Sélectionnez **Oui** dans le message du certificat.

1. Ouvrez Internet Explorer, puis sélectionnez **OK** dans la boîte de dialogue **Configurer Internet Explorer 11**.

1. Accédez à `https://www.microsoft.com`

1. Dans la boîte de dialogue **Alerte de sécurité**, sélectionnez **OK**.

1. Sélectionnez **Fermer** dans les alertes de sécurité Internet Explorer qui peuvent apparaître.

1. La page d’accueil de Microsoft doit s’afficher.

    ![Session RDP - Exploration de microsoft.com](../media/microsoft-home-page.png)

1. Accédez à **https://** **<www.google.com>**.

1. Vous devriez être bloqué par le pare-feu.

    ![Session RDP - Navigateur bloqué sur google.com](../media/google-home-page-fail.png)

1. Ainsi, vous avez vérifié que vous pouvez vous connecter au nom de domaine complet (FQDN) autorisé, mais que tous les autres vous bloquent.

## Tâche 9 : tester la règle réseau

Dans cette tâche, vous allez tester la règle réseau pour confirmer qu’elle fonctionne comme prévu.

1. En étant toujours connecté à la session RDP **Srv-workload-01**, à partir de cet ordinateur distant, ouvrez **Connexion Bureau à distance**.

1. Dans la zone **Ordinateur**, entrez l’**adresse IP privée** de **Srv-workload-02** (par exemple **10.1.1.4**).

1. Dans la boîte de dialogue **Entrer vos informations d’identification**, connectez-vous au serveur **Srv-workload-02** à l’aide du nom d’utilisateur **TestUser** et du mot de passe que vous avez spécifié pendant le déploiement.

1. Cliquez sur **OK**.

1. Sélectionnez **Oui** dans le message du certificat.

   ![Session RDP Srv-workload-01 vers une autre session RDP sur Srv-workload-02](../media/rdp-srv-workload-02-from-srv-workload-01.png)

1. Maintenant, vous avez vérifié que la règle réseau de pare-feu fonctionnait, car vous avez connecté un bureau à distance d’un serveur à un autre serveur situé dans un autre réseau virtuel.

1. Fermez les deux sessions RDP pour les déconnecter.

## Tâche 10 : Nettoyer les ressources

>**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name 'fw-manager-rg' -Force -AsJob
   ```

   >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.
