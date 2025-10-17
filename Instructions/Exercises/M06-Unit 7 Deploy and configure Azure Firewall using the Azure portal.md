---
Exercise:
  title: M06 - Unité 7 Déployer et configurer le Pare-feu Azure à l’aide du portail Azure
  module: 'Module 06 - Design and implement network security '
---

# M06 - Unité 7 Déployer et configurer le Pare-feu Azure à l’aide du portail Azure

## Scénario de l’exercice

En tant que membre de l’équipe de sécurité réseau de Contoso, la tâche suivante consiste à créer des règles de pare-feu pour autoriser/refuser l’accès à certains sites web. Les étapes suivantes vous guident dans la création d’un groupe de ressources, d’un réseau virtuel et de sous-réseaux, et d’une machine virtuelle en tant que tâches de préparation de l’environnement, puis le déploiement d’un pare-feu et d’une stratégie de pare-feu, la configuration des itinéraires par défaut et des règles d’application, réseau et DNAT, et enfin les tests du pare-feu.

![Diagramme de réseau virtuel avec l’architecture du pare-feu Azure.](../media/7-exercise-deploy-configure-azure-firewall-using-azure-portal.png)

### Compétences de tâche

Dans cet exercice, vous allez :

+ Tâche 1 : Créer un groupe de ressources
+ Tâche 2 : Créer un réseau virtuel et des sous-réseaux
+ Tâche 3 : Créer une machine virtuelle
+ Tâche 4 : Déployer le pare-feu et la stratégie de pare-feu
+ Tâche 5 : Créer un itinéraire par défaut
+ Tâche 6 : Configurer une règle d’application
+ Tâche 7 : Configurer une règle réseau
+ Tâche 8 : Configurer une règle NAT de destination (DNAT)
+ Tâche 9 : Modifier les adresses DNS principales et secondaires de l’interface réseau du serveur
+ Tâche 10 : Tester le pare-feu

### Simulations de labo interactives

>**Note** : les simulations de labo qui ont été fournies précédemment ont été supprimées.

### Durée estimée : 60 minutes

## Tâche 1 : Créer un groupe de ressources

Dans cette tâche, vous allez créer un nouveau groupe de ressources.

1. Connectez-vous à votre compte Azure.

1. Dans la page d’accueil du portail Azure, sélectionnez **Groupes de ressources**.

1. Sélectionnez **Créer**.

1. Sous l’onglet **Général**, dans **Groupe de ressources**, entrez **Test-FW-RG**.

1. Dans **Région**, sélectionnez votre région dans la liste.

   ![Créer un groupe de ressources pour le pare-feu Azure](../media/create-resource-group-for-azure-firewall.png)

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

## Tâche 2 : Créer un réseau virtuel et des sous-réseaux

Au cours de cette tâche, vous allez créer un seul réseau virtuel avec deux sous-réseaux.

1. Sur la page d’accueil du portail Azure, dans la zone de recherche, entrez **Réseau virtuel**, puis sélectionnez **Réseau virtuel** lorsque la valeur apparaît.

1. Sélectionnez **Créer**.

1. Sélectionnez le groupe de ressources **Test-FW-RG** que vous avez créé précédemment.

1. Dans la zone **Nom**, entrez **Test-FW-VN**.

   ![Créer un réseau virtuel, onglet Général](../media/create-vnet-basics-for-azure-firewall.png)

1. Sélectionnez **Suivant : Adresses IP**. Entrez l’espace d’adressage IPv4 10.0.0.0/16 si ce n’est pas déjà fait par défaut.

1. Sous **Nom de sous-réseau**, sélectionnez le mot **par défaut**.

1. Dans la boîte de dialogue **Modifier le sous-réseau**, remplacez le nom par **AzureFirewallSubnet**.

1. Remplacez la **Plage d’adresses de sous-réseau** par **10.0.1.0/26**.

1. Sélectionnez **Enregistrer**.

1. Sélectionnez **Ajouter un sous-réseau** pour créer un autre sous-réseau qui hébergera le serveur de charge de travail que vous allez bientôt créer.

    ![Ajouter un sous-réseau](../media/add-workload-subnet.png)

1. Dans la boîte de dialogue **Modifier le sous-réseau**, remplacez le nom par **Workload-SN**.

1. Remplacez la **Plage d’adresses de sous-réseau** par **10.0.2.0/24**.

1. Sélectionnez **Ajouter**.

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

## Tâche 3 : Créer une machine virtuelle

Au cours de cette tâche, vous allez créer la machine virtuelle de charge de travail et la placer dans le sous-réseau Workload-SN créé précédemment.

1. Sélectionnez l’icône Cloud Shell en haut à droite du portail Azure. Si nécessaire, configurez l’interpréteur de commandes.  
    + Sélectionnez **PowerShell**.
    + Sélectionnez **Aucun compte de stockage requis** et votre **abonnement**, puis sélectionnez **Appliquer**.
    + Attendez que le terminal crée et qu’une invite s’affiche. 

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Gérer des fichiers**. Dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **firewallManager.json** et **firewallManager.parameters.json** dans le répertoire de base de Cloud Shell.

    > **Note :** si vous travaillez dans votre propre abonnement, les [fichiers de modèles](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/tree/master/Allfiles/Exercises) sont disponibles dans le référentiel de labo GitHub.

1. Déployez les modèles ARM suivants pour créer les machines virtuelles nécessaires à cet exercice :

   >**Remarque** : Vous serez invité à fournir un mot de passe d’administrateur.

   ```powershell
   $RGName = "Test-FW-RG"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile firewall.json -TemplateParameterFile firewall.parameters.json
   ```
  
1. Une fois le déploiement terminé, accédez à la page d’accueil du portail Azure, puis sélectionnez **Machines virtuelles**.

1. Vérifiez que la machine virtuelle a été créée.

1. Sur la page **Vue d’ensemble** de **Srv-Work**, à droite de la page, sous **Réseau**, prenez note de l’**Adresse IP privée** pour cette machine virtuelle (par exemple **10.0.2.4**).

## Tâche 4 : Déployer le pare-feu et la stratégie de pare-feu

Au cours de cette tâche, vous allez déployer le pare-feu dans le réseau virtuel avec une stratégie de pare-feu configurée.

1. Dans la page d’accueil du portail Azure, sélectionnez **Créer une ressource**, puis dans la zone de recherche, saisissez **Pare-feu** et sélectionnez **Pare-feu** lorsque la valeur apparaît.

1. Dans la page **Pare-feu**, sélectionnez **Créer**.

1. Dans l’onglet **Général**, créez un pare-feu en utilisant les informations du tableau ci-dessous.

   | **Paramètre**          | **Valeur**                                                    |
   | -------------------- | ------------------------------------------------------------ |
   | Abonnement         | Sélectionnez votre abonnement                                     |
   | Resource group       | **Test-FW-RG**                                               |
   | Nom du pare-feu        | **Test-FW01**                                                |
   | Région               | Votre région                                                  |
   | Référence SKU de pare-feu        | **Standard**                                                 |
   | Gestion de pare-feu  | **Utiliser une stratégie de pare-feu pour gérer ce pare-feu**            |
   | Stratégie de pare-feu      | Sélectionnez **Ajouter**<br />Nom : **fw-test-pol**<br />Région : **votre région** |

   ![Créer une stratégie de pare-feu](../media/create-firewall-policy.png)

   | Choisir un réseau virtuel | **Utiliser existant**                         |
   | ------------------------ | ---------------------------------------- |
   | Réseau virtuel          | **Test-FW-VN**                           |
   | Adresse IP publique        | Sélectionnez **Ajouter**<br />Nom : **fw-pip** |

   ![Ajouter une adresse IP publique au pare-feu](../media/assign-public-ip-to-firewall.png)

1. Nous n’utilisons pas Firewall Manager, donc décochez la case **Activer la carte d’interface réseau de gestion du pare-feu**. 

1. Passez vos paramètres en revue. 

   ![Créer un pare-feu - Vérifier les paramètres](../media/review-all-configurations-for-firewall.png)

1. Passez à **Examiner et créer**, puis **Créer**.

1. Attendez la fin du déploiement du pare-feu.

1. Une fois le déploiement du pare-feu terminé, sélectionnez **Accéder à la ressource**.

1. Sur la page **Vue d’ensemble** de **Test-FW01**, à droite de la page, prenez note de l’**Adresse IP privée du pare-feu** pour ce pare-feu (par exemple **10.0.1.4**).

1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Configuration IP publique**.

1. Prenez note de l’adresse sous **Adresse IP** pour la configuration IP publique **fw-pip** (par exemple **20.90.136.51**).

## Tâche 5 : Créer un itinéraire par défaut

Dans cette tâche, sur le sous-réseau Workload-SN, vous allez configurer l’itinéraire par défaut sortant pour traverser le pare-feu.

1. Dans la page d’accueil du portail Azure, sélectionnez **Créer une ressource**, puis dans la zone de recherche, saisissez **routage** et sélectionnez **Table de routage** lorsque la valeur apparaît.

1. Sur la page **Table de routage**, sélectionnez **Créer**.

1. Dans l’onglet **Général**, créez une table de routage en utilisant les informations du tableau ci-dessous.

   | **Paramètre**              | **Valeur**                |
   | ------------------------ | ------------------------ |
   | Abonnement             | Sélectionnez votre abonnement |
   | Resource group           | **Test-FW-RG**           |
   | Région                   | Votre région              |
   | Nom                     | **Firewall-route**       |
   | Propager des itinéraires de passerelle | **Oui**                  |

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

   ![Créer une table de routage](../media/create-route-table.png)

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

1. Sur la page **Firewall-route**, sous **Paramètres**, sélectionnez **Sous-réseaux**, puis **Associer**.

1. Dans **Réseau virtuel**, sélectionnez **Test-FW-VN**.

1. Dans **Sous-réseau**, sélectionnez **Workload-SN**. Veillez à ne sélectionner que le sous-réseau Workload-SN pour cette route, sinon votre pare-feu ne fonctionnera pas correctement.

1. Cliquez sur **OK**.

1. Sous **Paramètres**, sélectionnez **Itinéraires**, puis **Ajouter**.

1. Dans **Nom de l’itinéraire**, entrez **fw-dg**.

1. Dans **Destination du préfixe d’adresse**, entrez **0.0.0.0/0**.

1. Dans **Type de tronçon suivant**, sélectionnez **Appliance virtuelle**.

1. Dans **Adresse du tronçon suivant**, saisissez l’adresse IP privée du pare-feu que vous avez notée précédemment (par exemple **10.0.1.4**).

1. Sélectionnez **Ajouter**.

    ![Ajouter un itinéraire de pare-feu](../media/add-firewall-route.png)

## Tâche 6 : Configurer une règle d’application

Dans cette tâche, vous allez ajouter une règle d’application qui autorise l’accès sortant à <www.google.com>.

1. Dans la page d’accueil du portail Azure, sélectionnez **Toutes les ressources**.

1. Dans la liste des ressources, sélectionnez votre stratégie de pare-feu, **fw-test-pol**.

1. Sous **Règles**, sélectionnez **Règles d’application**.

1. Sélectionnez **Ajouter une collection de règles**.

1. Sur la page **Ajouter un regroupement de règles**, créez une nouvelle règle d’application en utilisant les informations du tableau ci-dessous.

   | **Paramètre**            | **Valeur**                                 |
   | ---------------------- | ----------------------------------------- |
   | Nom                   | **App-Coll01**                            |
   | Type de regroupement de règles   | **Application**                           |
   | Priorité               | **200**                                   |
   | Action de regroupement de règles | **Autoriser**                                 |
   | Groupe de regroupement de règles  | **DefaultApplicationRuleCollectionGroup** |
   | **Section Règles**      |                                           |
   | Nom                   | **Allow-Google**                          |
   | Type de source            | **Adresse IP**                            |
   | Source                 | **10.0.2.0/24**                           |
   | Protocol               | **http,https**                            |
   | Type de destination       | **FQDN**                                  |
   | Destination            | **<www.google.com>**                        |

   ![Ajouter un regroupement de règles d’application](../media/add-an-application-rule-for-firewall.png)

1. Sélectionnez **Ajouter**.

## Tâche 7 : Configurer une règle réseau

Dans cette tâche, vous allez ajouter une règle réseau qui autorise l’accès sortant à deux adresses IP sur le port 53 (DNS).

1. Sur la page **fw-test-pol**, sous **Règles**, sélectionnez **Règles réseau**.

1. Sélectionnez **Ajouter une collection de règles**.

1. Sur la page **Ajouter un regroupement de règles**, créez une nouvelle règle réseau en utilisant les informations du tableau ci-dessous.

   | **Paramètre**            | **Valeur**                                                    |
   | ---------------------- | ------------------------------------------------------------ |
   | Nom                   | **Net-Coll01**                                               |
   | Type de regroupement de règles   | **Réseau**                                                  |
   | Priorité               | **200**                                                      |
   | Action de regroupement de règles | **Autoriser**                                                    |
   | Groupe de regroupement de règles  | **DefaultNetworkRuleCollectionGroup**                        |
   | **Section Règles**      |                                                              |
   | Nom                   | **Allow-DNS**                                                |
   | Type de source            | **Adresse IP**                                               |
   | Source                 | **10.0.2.0/24**                                              |
   | Protocol               | **UDP**                                                      |
   | Ports de destination      | **53**                                                       |
   | Type de destination       | **Adresse IP**                                               |
   | Destination            | **209.244.0.3, 209.244.0.4**<br />Il s’agit de serveurs DNS publics gérés par CenturyLink |

    ![Ajouter un regroupement de règles réseau](../media/add-a-network-rule-for-firewall.png)

1. Sélectionnez **Ajouter**.

## Tâche 8 : Configurer une règle NAT de destination (DNAT)

Au cours de cette tâche, vous allez ajouter une règle DNAT qui vous permet de connecter un bureau à distance à la machine virtuelle Srv-Work par le biais du pare-feu.

1. Dans la page **fw-test-pol**, sous **Règles**, sélectionnez **Règles DNAT**.

1. Sélectionnez **Ajouter une collection de règles**.

1. Sur la page **Ajouter un regroupement de règles**, créez une nouvelle règle DNAT en utilisant les informations du tableau ci-dessous.

   | **Paramètre**           | **Valeur**                                                    |
   | --------------------- | ------------------------------------------------------------ |
   | Nom                  | **rdp**                                                      |
   | Type de regroupement de règles  | **DNAT**                                                     |
   | Priority              | **200**                                                      |
   | Groupe de regroupement de règles | **DefaultDnatRuleCollectionGroup**                           |
   | **Section Règles**     |                                                              |
   | Nom                  | **rdp-nat**                                                  |
   | Type de source           | **Adresse IP**                                               |
   | Source                | *                                                            |
   | Protocole              | **TCP**                                                      |
   | Ports de destination     | **3389**                                                     |
   | Type de destination      | **Adresse IP**                                               |
   | Destination           | Entrez l’adresse IP publique de pare-feu de **fw-pip** que vous avez notée précédemment.<br />**par ex. 20.90.136.51** |
   | Adresse traduite    | Entrez l’adresse IP privée de **Srv-Work** que vous avez notée précédemment.<br />**par ex. 10.0.2.4** |
   | Port traduit       | **3389**                                                     |

  ![Ajouter un regroupement de règles DNAT](../media/add-a-dnat-rule.png)

1. Sélectionnez **Ajouter**.

## Tâche 9 : Modifier les adresses DNS principales et secondaires de l’interface réseau du serveur

À des fins de test dans cet exercice, au cours de cette tâche, vous allez configurer les adresses DNS principale et secondaire du serveur Srv-Work. Cependant, cela n’est pas obligatoire pour le Pare-feu Azure.

1. Dans la page d’accueil du portail Azure, sélectionnez **Groupes de ressources**.

1. Dans la liste de groupes de ressources, sélectionnez votre groupe de ressources, **Test-FW-RG**.

1. Dans la liste des ressources de ce groupe de ressources, sélectionnez l’interface réseau de la machine virtuelle **Srv-Work** (par exemple **srv-work350**).

   ![Sélectionner une carte réseau dans un groupe de ressources](../media/change-dns-servers-srv-work-nic-1.png)

1. Sous**Paramètres**, sélectionnez **Serveurs DNS**.

1. Sous **Serveurs DNS**, sélectionnez **Personnalisé**.

1. entrez **209.244.0.3** dans la zone de texte **Ajouter un serveur DNS**, et **209.244.0.4** dans la zone de texte suivante.

1. Sélectionnez **Enregistrer**.

   ![Modifier les serveurs DNS sur la carte réseau](../media/change-dns-servers-srv-work-nic-2.png)

1. Redémarrez la machine virtuelle **Srv-Work**.

## Tâche 10 : Tester le pare-feu

Dans cette dernière tâche, vous allez tester le pare-feu pour vérifier que les règles sont configurées correctement et qu’elles fonctionnent comme prévu. Cette configuration vous permet d’établir une connexion Bureau à distance à la machine virtuelle Srv-Work par le biais du pare-feu, via l’adresse IP publique du pare-feu.

1. Ouvrez **Connexion Bureau à distance** sur votre PC.

1. Dans la zone **Ordinateur**, entrez l’adresse IP publique du pare-feu (par exemple **20.90.136.51**) suivie de **:3389** (par exemple **20.90.136.51:3389**).

1. Dans la zone **Nom d’utilisateur**, entrez **TestUser**.

1. Sélectionnez **Se connecter**.

   ![Connexion RDP à l’adresse IP publique du pare-feu](../media/remote-desktop-connection-1.png)

1. Dans la boîte de dialogue **Entrez vos informations d’identification**, connectez-vous à la machine virtuelle du serveur **Srv-Work** à l’aide du mot de passe que vous avez spécifié pendant le déploiement.

1. Cliquez sur **OK**.

1. Sélectionnez **Oui** dans le message du certificat.

1. Ouvrez Internet Explorer et accédez à **<https://www.google.com>**.

1. Dans la boîte de dialogue **Alerte de sécurité**, sélectionnez **OK**.

1. Sélectionnez **Fermer** dans les alertes de sécurité Internet Explorer qui peuvent apparaître.

1. La page d’accueil Google doit s’afficher.

    ![Session RDP sur le serveur Srv-Work - Navigateur sur google.com](../media/remote-desktop-connection-2.png)

1. Accédez à **<https://www.microsoft.com>**.

1. Vous devriez être bloqué par le pare-feu.

    ![Session RDP sur le serveur Srv-Work - Navigateur bloqué sur microsoft.com](../media/remote-desktop-connection-3.png)

## Nettoyer les ressources

   >**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name 'Test-FW-RG' -Force -AsJob
   ```

   >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.

## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+ Présentez trois scénarios d’utilisation courante des pare-feu. 
+ Fournissez un tableau comparant les fonctionnalités des références SKU du Pare-feu Azure.
+ Le tableau suivant décrit les trois types de règles que vous pouvez créer pour un Pare-feu Azure.

## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Présentation du Pare-feu Azure](https://learn.microsoft.com/training/modules/introduction-azure-firewall/). Dans ce module, vous allez découvrir comment le Pare-feu Azure protège les ressources de réseau virtuel Azure, notamment les fonctionnalités, les règles et les options de déploiement.
+ [Présentation d’Azure Firewall Manager](https://learn.microsoft.com/training/modules/intro-to-azure-firewall-manager/). Dans ce module, vous allez découvrir comment Azure Firewall Manager permet de centraliser la gestion des routes et des stratégies de sécurité pour les périmètres de sécurité basés sur le cloud.

## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 
+ Un pare-feu est une fonctionnalité de sécurité réseau qui se situe entre un réseau approuvé et un réseau non approuvé, comme Internet. Le travail du pare-feu consiste à analyser le trafic réseau, et à l’autoriser ou à le rejeter.
+ Le Pare-feu Azure est un service de pare-feu basé sur le cloud. Dans la plupart des configurations, le Pare-feu Azure est provisionné dans un réseau virtuel Hub. Le trafic en provenance et à destination des réseaux virtuels Spoke et le réseau local est dirigé vers le pare-feu.
+ Les règles de pare-feu évaluent le trafic réseau. Le Pare-feu Azure comporte trois types de règles : application, réseau et NAT. 
+ Le Pare-feu Azure est proposé en trois références SKU : Standard, Premium et De base.
