---
Exercise:
  title: "M08 - Unité 3 Superviser une ressource d’équilibreur de charge à l’aide d’Azure\_Monitor"
  module: Module 08 - Design and implement network monitoring
---

# M08 - Unité 3 Superviser une ressource d’équilibreur de charge à l’aide d’Azure Monitor

## Scénario de l’exercice

Dans cet exercice, vous allez créer un équilibreur de charge interne pour l’organisation fictive Contoso Ltd. Vous créerez ensuite un espace de travail Log Analytics et utiliserez les insights Azure Monitor pour afficher des informations sur votre équilibreur de charge interne. Vous afficherez la vue Dépendance fonctionnelle, puis afficherez des métriques détaillées pour la ressource d’équilibreur de charge ainsi que des informations sur l’intégrité des ressources pour l’équilibreur de charge. Pour finir, vous configurerez les paramètres de diagnostic de l’équilibreur de charge de façon à envoyer des métriques à l’espace de travail Log Analytics que vous avez créé.

Le diagramme ci-dessous illustre l’environnement que vous déploierez dans cet exercice.

![Diagramme illustrant l’architecture d’équilibreur de charge qui sera créée dans l’exercice (comprend l’équilibreur de charge, le réseau virtuel, le sous-réseau, le sous-réseau Bastion et les machines virtuelles)](../media/3-exercise-monitor-load-balancer-resource-using-azure-monitor.png)

### Compétences de tâche

 Dans cet exercice, vous allez :

+ Tâche 1 : Créer le réseau virtuel
+ Tâche 2 : Créer l’équilibreur de charge
+ Tâche 3 : Créer un pool de back-ends
+ Tâche 4 : Créer une sonde d’intégrité
+ Tâche 5 : Créer une règle d’équilibreur de charge
+ Tâche 6 : Créer des serveurs back-end
+ Tâche 7 : Ajouter des machines virtuelles au pool de back-ends
+ Tâche 8 : tester l’équilibreur de charge
+ Tâche 9 : créer un espace de travail Log Analytics
+ Tâche 10 : utiliser l’affichage Dépendance fonctionnelle
+ Tâche 11 : afficher les métriques détaillées
+ Tâche 12 : afficher l’intégrité des ressources
+ Tâche 13 : configurer les paramètres de diagnostic

### Simulations de labo interactives

>**Note** : les simulations de labo qui ont été fournies précédemment ont été supprimées.

### Durée estimée : 55 minutes

## Tâche 1 : Créer le réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau.

1. Connectez-vous au portail Azure.

1. Sur la page d’accueil du portail Azure, recherchez **Réseau virtuel**, puis sélectionnez Réseau virtuel sous Services.

1. Sélectionnez **+ Créer**.

   ![Création d’un réseau virtuel](../media/create-virtual-network.png)

1. Sous l’onglet **De base**, utilisez les informations du tableau ci-dessous pour créer le réseau virtuel.

   | **Paramètre**    | **Valeur**                                           |
   | -------------- | --------------------------------------------------- |
   | Abonnement   | Sélectionnez votre abonnement                            |
   | Resource group | Sélectionnez **Créer**<br /><br />Nom : **IntLB-RG** |
   | Nom           | **IntLB-VNet**                                      |
   | Région         | **(États-Unis) USA Ouest**                                    |

1. Sélectionnez **Suivant : Adresses IP**.

1. Sous l’onglet **Adresses IP**, dans la zone **Espace d’adressage IPv4**, entrez **10.1.0.0/16**.

1. Sous **Nom du sous-réseau**, sélectionnez **+ Ajouter un sous-réseau**.

1. Dans le volet **Ajouter un sous-réseau**, spécifiez **myBackendSubnet** comme nom de sous-réseau et **10.1.0.0/24** comme plage d’adresses de sous-réseau.

1. Sélectionnez **Ajouter**.

1. Sélectionnez **Suivant : Sécurité**.

1. Sous **BastionHost** sélectionnez **Activer**, puis entrez les informations du tableau ci-dessous.

    | **Paramètre**                       | **Valeur**                                              |
    | --------------------------------- | ------------------------------------------------------ |
    | Nom du bastion                      | **myBastionHost**                                      |
    | Espace d’adressage AzureBastionSubnet  | **10.1.1.0/24**                                        |
    | Adresse IP publique                 | Sélectionnez **Créer**<br /><br />Nom : **myBastionIP** |

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

## Tâche 2 : Créer l’équilibreur de charge

Dans cette section, vous allez créer un équilibreur de charge interne de référence SKU Standard. La raison pour laquelle nous créons un équilibreur de charge de référence SKU Standard dans cet exercice, plutôt qu’un équilibreur de charge de référence SKU De base, est que certains exercices ultérieurs nécessiteront une version SKU Standard de l’équilibreur de charge.

1. Dans la page d’accueil Azure, dans la barre de recherche, entrez **Équilibreur de charge**.

1. Sélectionnez **Créer un équilibreur de charge**.

1. Sous l’onglet **De base**, utilisez les informations du tableau ci-dessous pour créer l’équilibreur de charge.

   | **Paramètre**           | **Valeur**                |
   | --------------------- | ------------------------ |
   | Onglet Informations de base            |                          |
   | Abonnement          | Sélectionnez votre abonnement |
   | Resource group        | **IntLB-RG**             |
   | Nom                  | **myIntLoadBalancer**    |
   | Région                | **(États-Unis) USA Ouest**         |
   | Référence SKU                   | **Standard**             |
   | Type                  | **Interne**             |
   | Configuration d'adresse IP front-end | + Ajouter une configuration IP front-end |
   | Nom                  | **LoadBalancerFrontEnd** |
   | Réseau virtuel       | **IntLB-VNet**           |
   | Subnet                | **myBackendSubnet**      |
   | Affectation d’adresses IP | **Dynamique**              |

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

## Tâche 3 : Créer un pool de back-ends

Le pool d’adresses de back-ends contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge.

1. Sur la page d’accueil du portail Azure, sélectionnez **Toutes les ressources**, puis **myIntLoadBalancer** dans la liste des ressources.

1. Sous **Paramètres**, sélectionnez **Pools principaux**, puis **Ajouter**.

1. Dans la page **Ajouter un pool de backends**, entrez les informations du tableau ci-dessous.

   | **Paramètre**     | **Valeur**            |
   | --------------- | -------------------- |
   | Nom            | **myBackendPool**    |
   | Réseau virtuel | **IntLB-VNet**       |
   | Configuration d’un pool de back-ends   | **Carte d’interface réseau** |

1. Sélectionnez **Ajouter**.

   ![Afficher le pool de back-ends créé dans l’équilibreur de charge](../media/create-backendpool.png)

## Tâche 4 : Créer une sonde d’intégrité

L’équilibreur de charge supervise l’état de votre application avec une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime des machines virtuelles dans l’équilibreur de charge en fonction de leur réponse aux contrôles d’intégrité. Vous allez maintenant créer une sonde d’intégrité afin de superviser l’intégrité des machines virtuelles.

1. Sur la page **Pools back-end** de votre équilibreur de charge, sous **Paramètres**, sélectionnez **Sondes d’intégrité**, puis **Ajouter**.

1. Dans la page **Ajouter une sonde d’intégrité**, entrez les informations du tableau ci-dessous.

   | **Paramètre**         | **Valeur**         |
   | ------------------- | ----------------- |
   | Nom                | **myHealthProbe** |
   | Protocol            | **HTTP**          |
   | Port                | **80**            |
   | Chemin d’accès                | **/**             |
   | Intervalle            | **15**            |

1. Sélectionnez **Ajouter**.

   ![Afficher la sonde d’intégrité créée dans l’équilibreur de charge](../media/create-healthprobe.png)

## Tâche 5 : Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP front-end pour le trafic entrant et le pool d’adresses IP de back-ends pour la réception du trafic. Les ports source et de destination sont définis dans la règle. Maintenant, vous allez créer une règle d’équilibreur de charge.

1. Sur la page **Pools back-end** de votre équilibreur de charge, sous **Paramètres**, sélectionnez **Règles d’équilibrage de charge**, puis **Ajouter**.

1. Dans la page **Ajouter une règle d’équilibrage de charge**, entrez les informations du tableau ci-dessous.

   | **Paramètre**            | **Valeur**                |
   | ---------------------- | ------------------------ |
   | Nom                   | **myHTTPRule**           |
   | Version de l’adresse IP             | **IPv4**                 |
   | Adresse IP du serveur frontal    | **LoadBalancerFrontEnd** |
   | Protocol               | **TCP**                  |
   | Port                   | **80**                   |
   | Port principal           | **80**                   |
   | Pool principal           | **myBackendPool**        |
   | Sonde d’intégrité           | **myHealthProbe**        |
   | Persistance de session    | **Aucun**                 |
   | Délai d’inactivité (minutes) | **15**                   |
   | IP flottante            | **Disabled**             |

1. Sélectionnez **Ajouter**.

   ![Afficher la règle d’équilibrage de charge créée dans l’équilibreur de charge](../media/create-loadbalancerrule.png)

## Tâche 6 : Créer des serveurs back-end

Dans cette section, vous allez créer trois machines virtuelles pour le pool principal de l’équilibreur de charge, ajouter les machines virtuelles au pool principal, puis installer IIS sur les trois machines virtuelles afin de tester l’équilibreur de charge.

1. Sélectionnez l’icône Cloud Shell en haut à droite du Portail Azure. Si nécessaire, configurez l’interpréteur de commandes.  
    + Sélectionnez **PowerShell**.
    + Sélectionnez **Aucun compte de stockage requis** et votre **abonnement**, puis sélectionnez **Appliquer**.
    + Attendez que le terminal crée et qu’une invite s’affiche. 

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Gérer des fichiers**. Dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **azuredeploy.json** et **azuredeploy.parameters.json** dans le répertoire de base de Cloud Shell.

    > **Note :** si vous travaillez dans votre propre abonnement, les [fichiers de modèles](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/tree/master/Allfiles/Exercises) sont disponibles dans le référentiel de labo GitHub.

1. Déployez les modèles ARM suivants pour créer le réseau virtuel, les sous-réseaux et les machines virtuelles nécessaires à cet exercice :

   >**Remarque** : Vous serez invité à fournir un mot de passe d’administrateur.

   ```powershell
   $RGName = "IntLB-RG"

   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```
  
    > **Remarque :** le déploiement peut prendre plusieurs minutes.

## Tâche 7 : Ajouter des machines virtuelles au pool de back-ends

1. Sur la page d’accueil du portail Azure, sélectionnez **Toutes les ressources**, puis **myIntLoadBalancer** dans la liste des ressources.

1. Sous **Paramètres**, sélectionnez **Pools principaux**, puis **myBackendPool**.

1. Dans la zone **Associé à**, sélectionnez **Machines virtuelles**.

1. Sous **Machines virtuelles**, sélectionnez **Ajouter**.

1. Cochez les cases des trois machines virtuelles (**myVM1**, **myVM2** et **myVM3**), puis sélectionnez **Ajouter**.

1. Sur la page **myBackendPool**, sélectionnez **Enregistrer**.

   ![Afficher les machines virtuelles ajoutées au pool de back-ends dans l’équilibreur de charge](../media/add-vms-backendpool.png)

## Tâche 8 : tester l’équilibreur de charge

Dans cette section, vous allez créer une machine virtuelle de test, puis tester l’équilibreur de charge.

### Créer une machine virtuelle de test

   >**Remarque** : il peut exister de légères différences entre les instructions et l’interface du portail Azure, mais le concept de base est identique.

1. Sur la page d’accueil Azure, dans la zone de recherche globale, entrez **Machines virtuelles** et sélectionnez Machines virtuelles sous Services.

1. Sélectionnez **+ Créer; + Machine virtuelle**, dans l’onglet **Fonctions de base**, créez la première machine virtuelle en utilisant les informations du tableau ci-dessous.

   | **Paramètre**          | **Valeur**                                    |
   | -------------------- | -------------------------------------------- |
   | Abonnement         | Sélectionnez votre abonnement                     |
   | Resource group       | **IntLB-RG**                                 |
   | Nom de la machine virtuelle | **myTestVM**                                 |
   | Région               | **(États-Unis) USA Ouest**                             |
   | Options de disponibilité | **Aucune redondance de l’infrastructure requise**    |
   | Type de sécurité        | **Standard**                                 |
   | Image                | **Voir toutes les images** --> **Datacenter pour Windows Server 2019**  |
   | Taille                 | **Standard_DS2_v3 - 2 processeurs virtuels, 8 Gio de mémoire** |
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
   | Équilibrage de charge                                               | **Aucun** (ou case non cochée)       |

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

1. Attendez que cette dernière machine virtuelle soit déployée avant de passer à la tâche suivante.

### Se connecter à la machine virtuelle de test pour tester l’équilibreur de charge

1. Sur la page d’accueil du portail Azure, sélectionnez **Toutes les ressources**, puis **myIntLoadBalancer** dans la liste des ressources.

1. Dans la page **Vue d’ensemble**, prenez note de l’**Adresse IP privée** ou copiez-la dans le Presse-papiers. Remarque : vous devrez peut-être sélectionner **Afficher plus** pour voir l’**adresse IP privée**.

1. Sélectionnez **Accueil** puis, sur la page d’accueil du portail Azure, sélectionnez **Toutes les ressources**, puis la machine virtuelle **myTestVM** que vous venez de créer.

1. Dans la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

1. Sélectionnez **Utiliser Bastion**.

1. Dans la zone **Nom d’utilisateur**, entrez **TestUser** et dans la zone **Mot de passe**, entrez le mot de passe que vous avez spécifié pendant le déploiement, puis sélectionnez **Se connecter**.

1. La fenêtre **myTestVM** s’ouvre dans un autre onglet de navigateur.

1. Si un volet **Réseaux** s’affiche, sélectionnez **Oui**.

1. Sélectionnez l’icône **Internet Explorer** dans la barre des tâches pour ouvrir le navigateur web.

1. Sélectionnez **OK** dans la boîte de dialogue **Configurer Internet Explorer 11**.

1. Entrez (ou collez) l’**Adresse IP privée** (par exemple 10.1.0.4) obtenue à l’étape précédente dans la barre d’adresse du navigateur, puis appuyez sur Entrée.

1. La page d’accueil web par défaut du serveur web IIS s’affiche dans la fenêtre du navigateur. L’une des trois machines virtuelles du pool de back-ends répondra.
    ![Fenêtre de navigateur montrant la réponse Hello World renvoyée par VM1](../media/load-balancer-web-test-1.png)

1. Si vous cliquez plusieurs fois sur le bouton Actualiser dans le navigateur, vous verrez que la réponse est renvoyée de manière aléatoire à partir des différentes machines virtuelles du pool back-end de l’équilibreur de charge interne.

    ![Fenêtre de navigateur montrant la réponse Hello World renvoyée par VM3](../media/load-balancer-web-test-2.png)

## Tâche 9 : créer un espace de travail Log Analytics

1. Sur la page d’accueil du portail Azure, sélectionnez **Tous les services** puis, dans la zone de recherche en haut de la page, entrez **Log Analytics** et sélectionnez **Espaces de travail Log Analytics** dans la liste filtrée.

   ![Accès aux espaces de travail Log Analytics à partir de la page d’accueil du portail Azure](../media/log-analytics-workspace-1.png)

1. Sélectionnez **Créer**.

1. Dans la page **Créer un espace de travail Log Analytics**, sous l’onglet **De base**, utilisez les informations du tableau ci-dessous pour créer l’espace de travail.

   | **Paramètre**    | **Valeur**                |
   | -------------- | ------------------------ |
   | Abonnement   | Sélectionnez votre abonnement |
   | Resource group | **IntLB-RG**             |
   | Nom           | **myLAworkspace**        |
   | Région         | **USA Ouest**              |

1. Sélectionnez **Vérifier + créer**, puis **Créer**.

   ![Liste des espaces de travail Log Analytics](../media/log-analytics-workspace-2.png)

## Tâche 10 : utiliser l’affichage Dépendance fonctionnelle

1. Sur la page d’accueil du portail Azure, sélectionnez **Toutes les ressources** puis, dans la liste des ressources, sélectionnez **myIntLoadBalancer**.

   ![Liste de toutes les ressources dans le portail Azure](../media/network-insights-functional-dependency-view-1.png)

1. Sous **Supervision**, sélectionnez **Insights**.

1. Dans le coin supérieur droit de la page, sélectionnez **X** pour fermer le volet **Métriques** pour le moment. Vous le rouvrirez d’ici peu.

1. Cet affichage est appelé Dépendance fonctionnelle et, dans ce mode, vous disposez d’un diagramme interactif utile qui illustre la topologie de la ressource réseau sélectionnée (en l’occurrence un équilibreur de charge). Pour Standard Load Balancer, les ressources de votre pool principal suivent un code de couleur en fonction de l’état de la sonde d’intégrité, indiquant la disponibilité actuelle de votre pool principal pour traiter le trafic.

1. Utilisez les boutons **Zoom avant (+)** et **Zoom arrière (-)** dans le coin inférieur droit de la page pour effectuer un zoom avant ou arrière sur le diagramme de la topologie (vous pouvez également utiliser la molette de la souris si vous en avez une). Vous pouvez aussi faire glisser le diagramme de topologie sur la page pour le déplacer.

1. Pointez sur le composant **LoadBalancerFrontEnd** dans le diagramme, puis sur le composant **myBackendPool**.

1. Notez que vous pouvez utiliser les liens de ces fenêtres indépendantes pour afficher des informations sur ces composants d’équilibreur de charge et ouvrir leurs panneaux de portail Azure respectifs.

1. Pour télécharger une copie au format de fichier .SVG du diagramme de topologie, sélectionnez **Télécharger la topologie**, puis enregistrez le fichier dans votre dossier **Téléchargements**.

1. Dans le coin supérieur droit, sélectionnez **Afficher les métriques** pour rouvrir le volet de métriques sur le côté droit de l’écran.
    ![Affichage Dépendance fonctionnelle des insights réseau Azure Monitor - Bouton Afficher les métriques mis en évidence](../media/network-insights-functional-dependency-view-3.png)

1. Le volet Métriques fournit un aperçu rapide de certaines métriques clés pour cette ressource d’équilibreur de charge, sous la forme de graphiques à barres et en courbes.

    ![Insights réseau Azure Monitor - Vue de base des métriques](../media/network-insights-basicmetrics-view.png)

## Tâche 11 : afficher les métriques détaillées

1. Pour afficher des métriques plus complètes pour cette ressource réseau, sélectionnez **Afficher les métriques détaillées**.
   ![Insights réseau Azure Monitor - Bouton Afficher les métriques détaillées mis en évidence](../media/network-insights-detailedmetrics-1.png)

1. Cela ouvre une grande page de **Métriques** complètes dans la plateforme d’insights réseau Azure. Le premier onglet qui s’affiche est **Vue d’ensemble** ; il indique l’état de disponibilité de l’équilibreur de charge ainsi que le débit global des données et la disponibilité du back-end et du front-end pour chaque adresse IP front-end attachée à votre équilibreur de charge. Ces mesures indiquent si l’adresse IP frontale est réactive et si les instances de calcul de votre pool principal sont réactives individuellement aux connexions entrantes.
   ![Insights réseau Azure Monitor – Vue détaillée des métriques – Onglet Vue d’ensemble](../media/network-insights-detailedmetrics-2.png)

1. Cliquez sur l’onglet **Disponibilité front-end &amp; back-end**, puis faites défiler la page pour afficher les graphiques d’état de sonde d’intégrité. Si vous observez des **valeurs inférieures à 100** pour ces éléments, cela indique une panne sur ces ressources.
   ![Insights réseau Azure Monitor - Vue détaillée des métriques - Graphiques de l’état de sonde d’intégrité mis en évidence](../media/network-insights-detailedmetrics-5.png)

1. Sélectionnez l’onglet **Débit de données** et faites défiler la page pour afficher les autres graphiques de débit de données.

1. Placez le curseur sur certains des points de données des graphiques ; vous constaterez que les valeurs changent et affichent la valeur exacte à ce moment précis.
   ![Insights réseau Azure Monitor - Vue détaillée des métriques - Onglet Débit de données](../media/network-insights-detailedmetrics-3.png)

1. Sélectionnez l’onglet **Distribution du flux** et faites défiler la page pour afficher les graphiques sous la section **Création de flux de machine virtuelle et trafic réseau**.

   ![Insights réseau Azure Monitor - Vue détaillée des métriques - Graphiques de création de flux de machine virtuelle et de trafic réseau](../media/network-insights-detailedmetrics-4.png)

## Tâche 12 : afficher l’intégrité des ressources

1. Pour afficher l’intégrité de vos ressources Load Balancer, sur la page d’accueil du portail Azure, sélectionnez **Tous les services**, puis **Monitor**.

1. Sur la page **Monitor&gt;Vue d’ensemble**, dans le menu de gauche, sélectionnez **Service Health**.

1. Sur la page **Service Health&gt;Problèmes liés au service**, dans le menu de gauche, sélectionnez **Resource Health**.

1. Dans la page **Service Health&gt;Resource Health**, dans la liste déroulante **Type de ressource**, faites défiler la liste et sélectionnez **Équilibreur de charge**.

   ![Accédez à Service Health>Resource Health pour la ressource d’équilibreur de charge](../media/resource-health-1.png)

1. Sélectionnez ensuite le nom de votre équilibreur de charge dans la liste.

1. La page **Resource Health** identifie les éventuels gros problèmes de disponibilité liés à votre ressource d’équilibreur de charge. Si des événements figurent sous la section **Historique d’intégrité**, vous pouvez développer l’événement d’intégrité pour afficher plus de détails. Vous pouvez même enregistrer les détails de l’événement sous forme de fichier PDF, afin de pouvoir l’examiner ultérieurement et créer des rapports.

   ![Service Health>Vue Resource health](../media/resource-health-2.png)

## Tâche 13 : configurer les paramètres de diagnostic

1. Sur la page d’accueil du portail Azure, sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources **IntLB-RG** dans la liste.

1. Sur la page **IntLB-RG**, sélectionnez le nom de la ressource d’équilibreur de charge **myIntLoadBalancer** dans la liste des ressources.

1. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**, puis **Ajouter un paramètre de diagnostic**.

   ![Paramètres de diagnostic>Bouton Ajouter un paramètre de diagnostic mis en évidence](../media/diagnostic-settings-1.png)

1. Sur la page **Paramètre de diagnostic**, dans la zone Nom, tapez **myLBDiagnostics**.

1. Cochez la case **AllMetrics**, puis la case **Envoyer à l’espace de travail Log Analytics**.

1. Sélectionnez votre abonnement dans la liste, puis sélectionnez **myLAworkspace (westus)** dans la liste déroulante d’espaces de travail.

1. Sélectionnez **Enregistrer**.

   ![Page Paramètre de diagnostic pour l’équilibreur de charge](../media/diagnostic-settings-2.png)

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
+ Résumez les outils Azure disponibles pour la surveillance des réseaux virtuels.
+ Quels sont les outils de surveillance Azure Network Watcher disponibles ?

## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Présentation d’Azure Monitor](https://learn.microsoft.com/training/modules/intro-to-azure-monitor/). Dans ce module, vous allez découvrir comment utiliser Azure Monitor pour fournir des insights sur les performances et les opérations de vos ressources Azure.
+ [Monitorez et dépannez votre infrastructure réseau Azure de bout en bout en tirant parti des outils de monitoring réseau](https://learn.microsoft.com/training/modules/troubleshoot-azure-network-infrastructure/). Dans ce modume, vous allez découvrir comment utiliser les outils, les diagnostics et les journaux d’Azure Network Watcher pour vous aider à détecter et à résoudre les problèmes réseau dans votre infrastructure Azure.

## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 

+ Azure Monitor fournit des fonctionnalités et des outils pour collecter, gérer et analyser les données informatiques provenant de toutes vos ressources locales, Azure et d’autres cloud.
+ Les métriques sont des mesures quantitatives qui montrent des instantanés des performances des applications ou des ressources. Les métriques sont généralement des valeurs numériques que vous pouvez mesurer au fil du temps.
+ Les journaux sont des enregistrements textuels d’événements, d’actions et de messages qui se produisent dans une ressource ou une application. 
+ Les insights, les visualisations et les tableaux de bord Azure Monitor peuvent consommer et transmettre des informations de surveillance concernant vos applications.
+ Les alertes vous avertissent de conditions critiques, et sont susceptibles de prendre des actions correctives. Les règles d’alerte peuvent être basées sur des données de mesure ou de journal.+ 
    
