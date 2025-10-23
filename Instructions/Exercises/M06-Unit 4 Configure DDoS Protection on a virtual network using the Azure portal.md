---
Exercise:
  title: M06 - Unité 4 Configurer la protection DDoS sur un réseau virtuel à l’aide du portail Azure
  module: Module 06 - Design and implement network security
---

# M06 - Unité 4 Configurer la protection DDoS sur un réseau virtuel à l’aide du portail Azure

## Scénario de l’exercice

En tant que responsable de l’équipe de sécurité réseau de Contoso, vous allez exécuter une attaque DDoS sur le réseau virtuel. Les étapes suivantes vous guident dans la création d’un réseau virtuel, la configuration de la DDoS Protection et la création d’une attaque que vous pouvez observer et surveiller à l’aide de la télémétrie et des métriques.

![Diagramme de l’architecture DDoS.](../media/4-exercise-configure-ddos-protection-virtual-network-using-azure-portal.png)

### Compétences de tâche

Dans cet exercice, vous allez :

+ Tâche 1 : Créer un groupe de ressources
+ Tâche 2 : Créer un plan de protection DDoS
+ Tâche 3 : Activer la protection DDoS sur un réseau virtuel nouveau ou existant
+ Tâche 4 : Configurer la télémétrie DDoS
+ Tâche 5 : Configurer les journaux de diagnostic DDoS
+ Tâche 6 : Configurer les alertes DDoS
+ Tâche 7 : tester avec des partenaires de simulation
  
### Durée estimée : 40 minutes

## Tâche 1 : Créer un groupe de ressources

1. Connectez-vous à votre compte Azure.

1. Dans la page d’accueil du portail Azure, sélectionnez **Groupes de ressources**.

1. Sélectionnez **Créer**.

1. Sous l’onglet **Général**, dans **Groupe de ressources**, entrez **MyResourceGroup**.

1. Comme **Région**, sélectionnez USA Est.

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

## Tâche 2 : Créer un plan de protection DDoS

1. Sur la page d’accueil du portail Azure, dans la zone de recherche, entrez **DDoS**, puis sélectionnez **Plan de protection DDoS** lorsque la valeur apparaît.

1. Sélectionnez **+ Créer**.

1. Sous l’onglet **Général**, dans la liste **Groupe de ressources**, sélectionnez le groupe de ressources que vous venez de créer.

1. Dans la zone **Nom de l’instance**, entrez **MyDdoSProtectionPlan**, puis sélectionnez **Vérifier + créer**.

1. Sélectionnez **Créer**.

## Tâche 3 : Activer la protection DDoS sur un réseau virtuel nouveau ou existant

Ici, vous allez activer la protection DDoS sur un nouveau réseau virtuel plutôt que sur un réseau existant. Vous devez d’abord créer le nouveau réseau virtuel, puis activer la protection DDoS sur ce réseau à l’aide du plan que vous avez créé précédemment.

1. Sur la page d’accueil du portail Azure, sélectionnez **Créer une ressource**, puis dans la zone de recherche, entrez **Réseau virtuel**, puis sélectionnez **Réseau virtuel** lorsque la valeur apparaît.

1. Dans la page **Réseau virtuel**, sélectionnez **Créer**.

1. Dans l’onglet **De base**, sélectionnez le groupe de ressources que vous avez créé précédemment.

1. Dans la zone **Nom**, entrez **MyVirtualNetwork**, puis sélectionnez l’onglet **Sécurité**.

1. Dans l’onglet **Sécurité**, en regard de **Protection réseau DDoS**, sélectionnez **Activer**.

1. Dans la liste déroulante **Plan de protection DDoS**, sélectionnez **MyDdosProtectionPlan**.

   ![Créer un réseau virtuel - Onglet Sécurité](../media/create-virtual-network-security-for-ddos-protection.png)

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

## Tâche 4 : Configurer la télémétrie DDoS

Vous créez une adresse IP publique, puis vous configurez les données de télémétrie dans les étapes suivantes.

1. Dans la page d’accueil du portail Azure, sélectionnez **Créer une ressource**, puis dans la zone de recherche, saisissez **IP publique**, puis sélectionnez **Adresse IP publique** lorsque la valeur s’affiche.

1. Dans la page **Adresse IP publique**, sélectionnez **Créer**.

1. Sur la page **Créer une adresse IP publique**, sous **Référence SKU**, sélectionnez **Standard**.

1. Dans la zone **Nom**, entrez **MyPublicIPAddress**.

1. Sous **Attribution d’adresse IP**, sélectionnez **Statique**.

1. Dans **Étiquette du nom DNS**, entrez **mypublicdnsxx** (où xx sont vos initiales, pour le rendre unique).

1. Sélectionnez **Créer**.

1. Pour configurer la télémétrie, accédez à la page d’accueil Azure, sélectionnez **Toutes les ressources**.

1. Dans la liste de vos ressources, sélectionnez **MyDdosProtectionPlan**.

1. Sous **Supervision**, sélectionnez **Métriques**.

1. Cochez la case **Étendue**, puis activez la case à cocher en regard de **MyPublicIPAddress**.

    ![Créer une étendue de métriques pour la télémétrie DDoS](../media/create-metrics-scope-for-ddos-telemetry.png)

1. Sélectionnez **Appliquer**.

1. Dans la zone **Métriques**, sélectionnez **Paquets entrants ignorés - DDoS**.

1. Dans la zone **Agrégation**, sélectionnez **Max**.

    ![Métriques créées pour la télémétrie DDoS](../media/metrics-created-for-ddos-telemetry.png)

## Tâche 5 : Configurer les journaux de diagnostic DDoS

1. Dans la page d’accueil d’Azure, sélectionnez **Toutes les ressources**.

1. Dans la liste de vos ressources, sélectionnez **MyPublicIPAddress**.

1. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.

1. Sélectionnez **Ajouter le paramètre de diagnostic**.

1. Sur la page **Paramètres de diagnostic**, dans la zone **Nom du paramètre de diagnostic**, saisissez **MyDiagnosticSetting**.

1. Sous **Détails de la catégorie**, activez les cases à cocher pour les 3 **journaux** et **AllMetrics**.

1. Sous **Détails de la destination**, cochez la case **Envoyer à l’espace de travail Log Analytics**. Ici, vous pouvez sélectionner un espace de travail Log Analytics préexistant, mais comme vous n’avez pas encore configuré de destination pour les journaux de diagnostic, il vous suffit d’entrer les paramètres, puis de les abandonner à l’étape suivante de cet exercice.

   ![Configurer de nouveaux paramètres de diagnostic pour DDoS](../media/configure-ddos-diagnostic-settings-new.png)

1. Normalement, vous sélectionnez **Enregistrer** pour enregistrer vos paramètres de diagnostic. Notez que cette option est encore grisée, car nous ne pouvons pas encore terminer la configuration du paramètre.

1. Sélectionnez **Ignorer**, puis cliquez sur **Oui**.

## Tâche 6 : Configurer les alertes DDoS

Au cours de cette étape, vous allez créer une machine virtuelle, lui affecter une adresse IP publique, puis configurer des alertes DDoS.

### Création de la machine virtuelle

1. Sur la page d’accueil du portail Azure, sélectionnez **Créer une ressource**, puis dans la zone de recherche, entrez **machine virtuelle**, et sélectionnez **Machine virtuelle** lorsque la valeur apparaît.

1. Sur la page **Machine virtuelle**, sélectionnez **Créer**.

1. Dans l’onglet **Général**, créez une machine virtuelle en utilisant les informations du tableau ci-dessous.

   | **Paramètre**           | **Valeur**                                                    |
   | --------------------- | ------------------------------------------------------------ |
   | Abonnement          | Sélectionnez votre abonnement                                     |
   | Resource group        | **MyResourceGroup**                                          |
   | Nom de la machine virtuelle  | **MyVirtualMachine**                                         |
   | Région                | Votre région                                                  |
   | Options de disponibilité  | **Aucune redondance d’infrastructure nécessaire**                   |
   | Image                 | **Ubuntu Server 18.04 LTS - Gen 2** (sélectionnez le lien Configurer la génération de machine virtuelle si nécessaire) |
   | Taille                  | Sélectionnez **Afficher toutes les tailles**, puis choisissez **B1ls** dans la liste et **Sélectionner****(Standard_B1ls - 1 vcpu, 0,5 Gio de mémoire**) |
   | Type d'authentification   | **Clé publique SSH**                                           |
   | Nom d’utilisateur              | **azureuser**                                                |
   | Source de la clé publique SSH | **Générer une nouvelle paire de clés**                                    |
   | Nom de la paire de clés         | **myvirtualmachine-ssh-key**                                 |
   | Aucun port d’entrée public  | Sélectionnez Aucun                                                  |

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

1. Dans la boîte de dialogue **Générer une nouvelle paire de clés**, sélectionnez **Télécharger la clé privée et créer une ressource**.

1. Enregistrez la clé privée.

1. Une fois le déploiement effectué, sélectionnez **Accéder à la ressource**.

### Affecter l’adresse IP publique

1. Sur la page **Vue d’ensemble** de la nouvelle machine virtuelle, sous **Paramètres**, sélectionnez **Réseau**.

1. Sélectionnez **myvirtualmachine-nic** à côté de **Interface réseau**. Le nom de la carte réseau peut différer.

1. Sous **Paramètres**, sélectionnez **Configurations IP**.

1. Sélectionnez **ipconfig1**.

1. Dans la liste **Adresse IP publique**, sélectionnez **MyPublicIPAddress**.

1. Sélectionnez **Enregistrer**.

   ![Modifier l’adresse IP publique pour la machine virtuelle de DDoS](../media/change-public-ip-config-for-ddos-vm-new.png)

### Configurer des alertes DDoS

1. Dans la page d’accueil d’Azure, sélectionnez **Toutes les ressources**.

1. Dans la liste de vos ressources, sélectionnez **MyPublicIPAddress**.

1. Sous **Supervision**, sélectionnez **Alertes**.

1. Sélectionnez **Créer une règle d’alerte**.

1. Sur la page **Créer une règle d’alerte**, sous **Étendue**, sélectionnez **Modifier la ressource**.

1. Comme nom du signal, sélectionnez **Subit une attaque DDoS ou non**.

1. Sous Logique d’alerte, recherchez le paramètre **Opérateur** et sélectionnez **Supérieur ou égal à**.

1. Dans **Valeur de seuil**, entrez **1** (cela signifie « subit une attaque »).

1. Accédez à l’onglet Détails et sélectionnez **Nom de la règle d’alerte**, puis entrez **MyDdosAlert**.

    ![Point final de la création d’une nouvelle règle d’alerte](../media/new-alert-rule-end.png)

1. Sélectionnez **Créer une règle d’alerte**.

## Tâche 7 : tester avec des partenaires de simulation

1. Consultez la page [Stratégie de test de simulation DDoS Azure](https://learn.microsoft.com/en-us/azure/ddos-protection/test-through-simulations#configure-a-ddos-attack-simulation). 

1. Remarquez qu’il existe plusieurs partenaires de test. Lorsque vous avez le temps, configurez une simulation d’attaque DDoS. Pour BreakingPoint Cloud, vous devez d’abord créer un compte BreakingPoint Cloud.

## Nettoyer les ressources

   >**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name 'MyResourceGroup' -Force -AsJob
   ```

   >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.

## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+ Que sont les attaques DDoS ? Comment les attaques DDoS sont-elles classées et existe-t-il des stratégies d’atténuation ?
+ Fournissez une tableau récapitulant les deux différents niveaux Azure DDoS Protection.
+ Quelles ressources Azure peuvent être protégées par DDoS Protection ?


## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Présentation d’Azure DDoS Protection](https://learn.microsoft.com/training/modules/introduction-azure-ddos-protection/). Dans ce module, vous allez évaluer Azure DDoS Protection, ses fonctionnalités et ses options d’architecture.
+ [Concevez et implémentez la sécurité réseau](https://learn.microsoft.com/training/modules/design-implement-network-security-monitoring/). Dans ce module, vous allez découvrir et déployer Azure DDoS Protection.

  
## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 
+ Une attaque DDoS est une tentative malveillante de saturer les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. 
+ Azure DDoS Protection fournit une protection contre les attaques DDoS. Cette solution s’adapte automatiquement pour protéger vos ressources Azure spécifiques dans un réseau virtuel. 
+ Les fonctionnalités d’Azure DDoS Protection sont les suivantes : surveillance du trafic Always-On, réglage adaptatif en temps réel, télémétrie et alertes.  
+ Azure DDoS Protection prend en charge deux types de niveaux : Protection IP DDoS et Protection réseau DDoS.
