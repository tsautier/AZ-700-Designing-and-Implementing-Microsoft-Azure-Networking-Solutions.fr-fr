---
Exercise:
  title: M04 - Unité 4 Créer et configurer un équilibreur de charge Azure
  module: Module 04 - Load balancing non-HTTP(S) traffic in Azure
---


# M04 - Unité 4 Créer et configurer un équilibreur de charge Azure

Dans cet exercice, vous allez créer un équilibreur de charge interne pour l’organisation fictive Contoso Ltd.

### Simulations de labo interactives

>**Note** : les simulations de labo qui ont été fournies précédemment ont été supprimées.

### Durée estimée : 60 minutes (dont environ 45 minutes d’attente pour le déploiement)

Les étapes de création d’un équilibreur de charge interne sont très similaires à celles que vous avez déjà apprises dans ce module pour créer un équilibreur de charge public. La principale différence réside dans le fait qu’avec un équilibreur de charge public, le serveur frontal est accessible via une adresse IP publique et que vous testez la connectivité à partir d’un hôte situé en dehors de votre réseau virtuel. En revanche, avec un équilibreur de charge interne, le serveur frontal est une adresse IP privée à l’intérieur de votre réseau virtuel et vous testez la connectivité à partir d’un hôte situé dans le même réseau.

![diagramme d’équilibreur de charge standard interne](../media/4-exercise-create-configure-azure-load-balancer.png)

### Compétences de tâche

Dans cet exercice, vous allez :

+ Tâche 1 : Créer le réseau virtuel
+ Tâche 2 : Créer des serveurs de back-end
+ Tâche 3 : Créer l’équilibreur de charge
+ Tâche 4 : Créer des ressources d’équilibreur de charge
+ Tâche 5 : Tester l’équilibreur de charge

## Tâche 1 : Créer le réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau.

1. Connectez-vous au portail Azure.

2. Dans la page d’accueil du portail Azure, accédez à la barre de recherche globale et recherchez **Réseaux virtuels**, puis sélectionnez Réseaux virtuels sous Services.  ![Résultats de la recherche de « réseau virtuel» dans la barre de recherche globale de la page d’accueil du portail Azure.](../media/global-search-bar.PNG)

3. Sélectionnez **Créer** dans la page Réseaux virtuels.  ![Assistant Créer un réseau virtuel.](../media/create-virtual-network.png)

4. Sous l’onglet **De base**, utilisez les informations du tableau ci-dessous pour créer le réseau virtuel.

   | **Paramètre**    | **Valeur**                                  |
   | -------------- | ------------------------------------------ |
   | Abonnement   | Sélectionnez votre abonnement                   |
   | Resource group | Sélectionnez **Créer nouveau** Nom : **IntLB-RG** |
   | Nom           | **IntLB-VNet**                             |
   | Région         | **(États-Unis) USA Est**                           |

5. Sélectionnez **Suivant** (vous dirige vers l’onglet Sécurité).

6. Sous **Azure Bastion**, sélectionnez **Activer Azure Bastion**, puis entrez les informations du tableau ci-dessous.

    | **Paramètre**                   | **Valeur**                                                    |
    | ----------------------------- | ------------------------------------------------------------ |
    | Nom de l’hôte                     | **myBastionHost**                                            |
    | Adresse IP publique             | Sélectionnez **Créer une adresse IP publique**  Nom : **myBastionIP** |

7. Sélectionnez **Suivant** (vous dirige vers l’onglet Adresses IP).

8. Sous l’onglet **Adresses IP**, dans la zone **Espace d’adressage IPv4**, supprimez le paramètre par défaut et entrez **10.1.0.0/16**.

9. Sous l’onglet **Adresses IP**, sélectionnez **+ Ajouter un sous-réseau**.

10. Dans le volet **Ajouter un sous-réseau**, spécifiez **myBackendSubnet** comme nom de sous-réseau et **10.1.0.0/24** comme plage d’adresses de sous-réseau. Sélectionnez **Ajouter**

11. Dans le volet **Ajouter un sous-réseau**, spécifiez **myFrontEndSubnet** comme nom de sous-réseau et **10.1.2.0/24** comme plage d’adresses de sous-réseau. Sélectionnez **Ajouter**

12. Dans la notification relative à Azure Bastion, sélectionnez **Ajouter un sous-réseau Azure Bastion**.

13. Sélectionnez **Revoir + créer**.

14. Sélectionnez **Create** (Créer).

## Tâche 2 : Créer des serveurs de back-end

Dans cette section, vous allez créer trois machines virtuelles qui seront dans le même groupe à haute disponibilité, pour le pool de back-ends de l’équilibreur de charge, ajouter les machines virtuelles au pool de back-ends, puis installer IIS sur les trois machines virtuelles afin de tester l’équilibreur de charge.

1. Sélectionnez l’icône Cloud Shell en haut à droite du portail Azure. Si nécessaire, configurez l’interpréteur de commandes.  
    + Sélectionnez **PowerShell**.
    + Sélectionnez **Aucun compte de stockage requis** et votre **abonnement**, puis sélectionnez **Appliquer**.
    + Attendez que le terminal crée et qu’une invite s’affiche. 

2. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Charger/télécharger des fichiers**. Dans le menu déroulant, sélectionnez **Charger** et chargez un par un les fichiers azuredeploy.json et azuredeploy.parameters.json dans le répertoire de base de Cloud Shell.

    > **Note :** si vous travaillez dans votre propre abonnement, les [fichiers de modèles](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/tree/master/Allfiles/Exercises) sont disponibles dans le référentiel de labo GitHub.

4. Déployez les modèles ARM suivants pour créer les machines virtuelles nécessaires à cet exercice :

   >**Remarque** : Vous serez invité à fournir un mot de passe d’administrateur.

   ```powershell
   $RGName = "IntLB-RG"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```

La création de ces trois machines virtuelles peut prendre 5 à 10 minutes. Vous n’avez pas besoin d’attendre la fin de ce travail, vous pouvez passer à la tâche suivante dès maintenant.

## Tâche 3 : Créer l’équilibreur de charge

Dans cette section, vous allez créer un équilibreur de charge interne de référence SKU Standard. La raison pour laquelle nous créons un équilibreur de charge de référence SKU Standard dans cet exercice, plutôt qu’un équilibreur de charge de référence SKU De base, est que certains exercices ultérieurs nécessiteront une version SKU Standard de l’équilibreur de charge.

1. Sur la page d’accueil du portail Azure, sélectionnez **Créer une ressource**.

1. Dans la zone de recherche en haut de la page, entrez **Load Balancer**, puis appuyez sur **Entrée** (**remarque :** n’en sélectionnez pas un dans la liste).

1. Dans la page des résultats, recherchez et sélectionnez **Load Balancer** (celui sous le nom duquel figurent « Microsoft » et « Azure Service »).

1. Sélectionnez **Créer**.

1. Sous l’onglet **De base**, utilisez les informations du tableau ci-dessous pour créer l’équilibreur de charge.

   | **Paramètre**           | **Valeur**                |
   | --------------------- | ------------------------ |
   | Abonnement          | Sélectionnez votre abonnement |
   | Resource group        | **IntLB-RG**             |
   | Nom                  | **myIntLoadBalancer**    |
   | Région                | **(États-Unis) USA Est**         |
   | Référence (SKU)                   | **Standard**             |
   | Type                  | **Interne**             |
   | Niveau                  | **Regional**             |

1. Sélectionnez **Suivant : configurations d’adresse IP front-end**.
   
1. Sélectionnez Ajouter une adresse IP front-end

1. Dans le volet **Ajouter une adresse IP front-end**, entrez les informations du tableau ci-dessous et sélectionnez **Ajouter**.

   | **Paramètre**     | **Valeur**                |
   | --------------- | ------------------------ |
   | Nom            | **LoadBalancerFrontEnd** |
   | Réseau virtuel | **IntLB-VNet**           |
   | Subnet          | **myFrontEndSubnet**     |
   | Affectation      | **Dynamique**              |

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

## Tâche 4 : Créer des ressources d’équilibreur de charge

Dans cette section, vous allez configurer les paramètres de l’équilibreur de charge pour un pool d’adresses principal, puis créer une sonde d’intégrité et une règle d’équilibreur de charge.

### Créez un pool principal et ajouter des machines virtuelles au pool principal

Le pool d’adresses de back-ends contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge.

1. Sur la page d’accueil du portail Azure, sélectionnez **Toutes les ressources**, puis **myIntLoadBalancer** dans la liste des ressources.

1. Sous **Paramètres**, sélectionnez **Pools principaux**, puis **Ajouter**.

1. Dans la page **Ajouter un pool de backends**, entrez les informations du tableau ci-dessous.

   | **Paramètre**     | **Valeur**            |
   | --------------- | -------------------- |
   | Nom            | **myBackendPool**    |
   | Réseau virtuel | **IntLB-VNet**       |

1. Sous **Machines virtuelles**, sélectionnez **Ajouter**.

1. Cochez les cases des trois machines virtuelles (**myVM1**, **myVM2** et **myVM3**), puis sélectionnez **Ajouter**.

1. Sélectionnez **Enregistrer**.
   ![Image 7](../media/add-vms-backendpool.png)

### Créer une sonde d’intégrité

L’équilibreur de charge supervise l’état de votre application avec une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime des machines virtuelles dans l’équilibreur de charge en fonction de leur réponse aux contrôles d’intégrité. Vous allez maintenant créer une sonde d’intégrité afin de superviser l’intégrité des machines virtuelles.

1. Sous **Paramètres**, sélectionnez **Sondes d’intégrité**, puis **Ajouter**.

1. Dans la page **Ajouter une sonde d’intégrité**, entrez les informations du tableau ci-dessous.

   | **Paramètre**         | **Valeur**         |
   | ------------------- | ----------------- |
   | Nom                | **myHealthProbe** |
   | Protocol            | **HTTP**          |
   | Port                | **80**            |
   | Chemin d’accès                | **/**             |
   | Intervalle            | **15**            |

1. Sélectionnez **Ajouter**.
   ![Image 5](../media/create-healthprobe.png)

### Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP front-end pour le trafic entrant et le pool d’adresses IP de back-ends pour la réception du trafic. Les ports source et de destination sont définis dans la règle. Maintenant, vous allez créer une règle d’équilibreur de charge.

1. Sous **Paramètres**, sélectionnez **Règles d’équilibrage de charge**, puis **Ajouter**.

1. Dans la page **Ajouter une règle d’équilibrage de charge**, entrez les informations du tableau ci-dessous.

   | **Paramètre**            | **Valeur**                |
   | ---------------------- | ------------------------ |
   | Nom                   | **myHTTPRule**           |
   | Version de l’adresse IP             | **IPv4**                 |
   | Adresse IP du serveur frontal    | **LoadBalancerFrontEnd** |
   | Pool principal           | **myBackendPool**        |
   | Protocole               | **TCP**                  |
   | Port                   | **80**                   |
   | Port principal           | **80**                   |
   | Sonde d’intégrité           | **myHealthProbe**        |
   | Persistance de session    | **Aucun**                 |
   | Délai d’inactivité (minutes) | **15**                   |
   | IP flottante            | **Disabled**             |

1. Sélectionnez **Enregistrer**.
   ![Image 6](../media/create-loadbalancerrule.png)

## Tâche 5 : Tester l’équilibreur de charge

Dans cette section, vous allez créer une machine virtuelle de test, puis tester l’équilibreur de charge.

### Créer une machine virtuelle de test

1. Sur la page d’accueil du portail Azure, sélectionnez **Créer une ressource**, puis **Virtuelle**, puis sélectionnez **Machine virtuelle** (si ce type de ressource n’est pas listé dans la page, utilisez la zone de recherche en haut de la page pour le rechercher et le sélectionner).

1. Dans la page **Créer une machine virtuelle**, sous l’onglet **De base**, créez la première machine virtuelle en utilisant les informations du tableau ci-dessous.

   | **Paramètre**          | **Valeur**                                    |
   | -------------------- | -------------------------------------------- |
   | Abonnement         | Sélectionnez votre abonnement                     |
   | Resource group       | **IntLB-RG**                                 |
   | Nom de la machine virtuelle | **myTestVM**                                 |
   | Région               | **(États-Unis) USA Est**                             |
   | Options de disponibilité | **Aucune redondance de l’infrastructure requise**    |
   | Image                | **Windows Server 2019 Datacenter - Gen 2**   |
   | Taille                 | **Standard_DS2_v3 - 2 processeurs virtuels, 8 Gio de mémoire**   |
   | Nom d’utilisateur             | **TestUser**                                 |
   | Mot de passe             | **Choisissez un mot de passe sécurisé**                |
   | Confirmer le mot de passe     | **Choisissez un mot de passe sécurisé**                |

1. Sélectionnez **Suivant : Disques**, puis **Suivant : Mise en réseau**.

1. Sous l’onglet **Mise en réseau**, utilisez les informations du tableau ci-dessous pour configurer les paramètres réseau.

   | **Paramètre**                                                  | **Valeur**                     |
   | ------------------------------------------------------------ | ----------------------------- |
   | Réseau virtuel                                              | **IntLB-VNet**                |
   | Subnet                                                       | **myBackendSubnet**           |
   | Adresse IP publique                                                    | Remplacez par **Aucun**            |
   | Groupe de sécurité réseau de la carte réseau                                   | **Avancée**                  |
   | Configurer un groupe de sécurité réseau                             | Sélectionnez le groupe **myNSG** existant |
   | Options d’équilibrage de charge                                       | **Aucun**                      |

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

1. Attendez que cette dernière machine virtuelle soit déployée avant de passer à la tâche suivante.

### Se connecter à la machine virtuelle de test pour tester l’équilibreur de charge

1. Sur la page d’accueil du portail Azure, sélectionnez **Toutes les ressources**, puis **myIntLoadBalancer** dans la liste des ressources.

1. Dans la page **Vue d’ensemble**, prenez note de l’**Adresse IP privée** ou copiez-la dans le Presse-papiers. Remarque : vous devrez peut-être sélectionner **Afficher plus** pour voir le champ **Adresse IP privée**.

1. Sélectionnez **Accueil** puis, sur la page d’accueil du portail Azure, sélectionnez **Toutes les ressources**, puis la machine virtuelle **myTestVM** que vous venez de créer.

1. Dans la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

1. Sélectionnez **Utiliser Bastion**.

1. Dans la zone **Nom d’utilisateur**, entrez **TestUser** et dans la zone **Mot de passe**, entrez le mot de passe que vous avez créé, puis sélectionnez **Se connecter**. Si le bloqueur de fenêtre contextuelle empêche l’affichage de la nouvelle fenêtre, autorisez son affichage dans le bloqueur de fenêtre contextuelle et sélectionnez **Se connecter** à nouveau.

1. La fenêtre **myTestVM** s’ouvre dans un autre onglet de navigateur.

1. Si un volet **Réseaux** s’affiche, sélectionnez **Oui**.

1. Sélectionnez l’icône **Internet Explorer** dans la barre des tâches pour ouvrir le navigateur web.

1. Sélectionnez **OK** dans la boîte de dialogue **Configurer Internet Explorer 11**.

1. Entrez (ou collez) l’**Adresse IP privée** (par exemple 10.1.0.4) obtenue à l’étape précédente dans la barre d’adresse du navigateur, puis appuyez sur Entrée.

1. La page d’accueil web par défaut du serveur web IIS s’affiche dans la fenêtre du navigateur. L’une des trois machines virtuelles du pool de back-ends répondra.
    ![Image 8](../media/load-balancer-web-test-1.png)

1. Si vous cliquez plusieurs fois sur le bouton Actualiser dans le navigateur, vous verrez que la réponse est renvoyée de manière aléatoire à partir des différentes machines virtuelles du pool back-end de l’équilibreur de charge interne.
    ![Image 9](../media/load-balancer-web-test-2.png)

## Nettoyer les ressources

   >**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name 'IntLB-RG' -Force -AsJob
   ```

   >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.

## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+ En quoi les équilibreurs de charge publics et privés Azure sont-ils différents ? Fournissez des exemples de scénarios pour chaque type.
+ Fournissez une tableau qui compare les références (SKU) de base et standard de l’équilibreur de charge Azure.
+ Comment l’équilibreur de charge Azure décide-t-il de traiter les demandes entrantes ?


## En savoir plus grâce à l’apprentissage auto-rythmé
+ [Introduction à Azure Load Balancer](https://learn.microsoft.com/training/modules/intro-to-azure-load-balancer/). Ce module explique à quoi sert Azure Load Balancer, comment il fonctionne et quand choisir de l’utiliser comme solution pour répondre aux besoins de votre organisation.
+ [Résolvez les problèmes de connectivité réseau entrante d’Azure Load Balancer](https://learn.microsoft.com/en-us/training/modules/troubleshoot-inbound-connectivity-azure-load-balancer/). Dans ce module, vous allez identifier et résoudre les problèmes courants de connectivité entrante d’Azure Load Balancer.

## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 
+ L’équilibrage de charge fait référence à une distribution efficace du trafic réseau entrant au sein d’un groupe de serveurs ou ressources back-end.
+ Azure Load Balancer distribue les flux entrants depuis le serveur frontal de l’équilibreur de charge sur des instances du pool principal. Ces flux sont distribués selon des règles d’équilibrage de charge et des sondes d’intégrité configurées. Les instances de pool de back-ends peuvent être des machines virtuelles Azure ou des groupes de machines virtuelles identiques.
+ Azure offre des équilibreurs de charge privés et publics. Les équilibreurs de charge publics sont idéaux pour les applications accessibles sur Internet, les connexions sortantes et les applications web. Les équilibreurs de charge privés sont meilleurs pour les applications internes, les services principaux et les scénarios hybrides.


