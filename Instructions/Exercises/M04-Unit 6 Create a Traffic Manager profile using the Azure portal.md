---
Exercise:
  title: M04 - Unité 6 Créer un profil Traffic Manager à l’aide du portail Azure
  module: Module 04 - Load balancing non-HTTP(S) traffic in Azure
---

# M04 - Unité 6 Créer un profil Traffic Manager à l’aide du portail Azure

## Scénario de l’exercice

Dans cet exercice, vous allez créer un profil Traffic Manager pour fournir une haute disponibilité pour l’application web de l’organisation fictive Contoso Ltd.

### Durée estimée : 35 minutes

Vous allez créer deux instances d’une application web déployées dans deux régions différentes (USA Est et Europe Ouest). La région USA Est fait office de point de terminaison principal pour Traffic Manager et la région Europe Ouest agit comme un point de terminaison de basculement.

Vous allez ensuite créer un profil Traffic Manager en fonction de la priorité du point de terminaison. Le profil dirige le trafic utilisateur vers le site principal exécutant l’application web. Traffic Manager surveille en permanence l’application web et, si le site principal dans la région USA Est n’est pas disponible, il fournit un basculement automatique vers le site de sauvegarde dans la région Europe Ouest.

Le diagramme ci-dessous illustre l’environnement que vous allez déployer dans cet exercice.

 ![Image 14](../media/exercise-traffic-manager-environment-diagram.png)

### Compétences de tâche

 Dans cet exercice, vous allez :

+ Tâche 1 : Créer les applications web
+ Tâche 2 : Créer un profil Traffic Manager
+ Tâche 3 : ajouter des points de terminaison Traffic Manager
+ Tâche 4 : Tester le profil Traffic Manager

## Tâche 1 : Créer les applications web

Dans cette section, vous allez créer deux instances d’une application web déployée dans deux régions Azure différentes.

1. Dans la page d’accueil du portail Azure, sélectionnez **Créer une ressource**, puis **Application web** (si ce type de ressource n’est pas listé dans la page, utilisez la zone de recherche en haut de la page pour le rechercher et le sélectionner).

1. Sur la page **Créer une application web**, sous l’onglet **De base**, créez la première application web en utilisant les informations du tableau ci-dessous.

   | **Paramètre**      | **Valeur**                                                    |
   | ---------------- | ------------------------------------------------------------ |
   | Abonnement     | Sélectionnez votre abonnement                                     |
   | Resource group   | Sélectionnez **Créer nouveau** Nom : **Contoso-RG-TM1**             |
   | Nom             | **ContosoWebAppEastUSxx** (où xx sont vos initiales pour rendre le nom unique) |
   | Publier          | **Code**                                                     |
   | Pile d’exécution    | **ASP.NET V4.8**                                             |
   | Système d’exploitation | **Windows**                                                  |
   | Région           | **USA Est**                                                  |
   | Plan Windows     | Sélectionnez **Créer nouveau** Nom : **ContosoAppServicePlanEastUS** |
   | Plan tarifaire     | **Premium V3 P1V3**  (Sélectionnez un autre plan si celui-ci n’est pas disponible)          |

1. Sélectionnez l’onglet **Surveiller + sécuriser**.

1. Sélectionnez l’option **Non** pour **Activer Application Insights**.

1. Sélectionnez **Revoir + créer**.

   ![Image 18](../media/create-web-app-1.png)

1. Sélectionnez **Créer**. Quand l’application web est déployé correctement, elle crée un site web par défaut.

1. Répétez les étapes 1 à 6 ci-dessus pour créer une deuxième application web. Utilisez les mêmes paramètres que précédemment, à l’exception des informations du tableau ci-dessous.

   | **Paramètre**    | **Valeur**                                                    |
   | -------------- | ------------------------------------------------------------ |
   | Groupe de ressources | Sélectionnez **Créer nouveau** Nom : **Contoso-RG-TM2**             |
   | Nom           | **ContosoWebAppWestEuropexx** (où xx sont vos initiales pour rendre le nom unique)  |
   | Région         | **Europe Ouest**                                              |
   | Plan Windows   | Sélectionnez **Créer nouveau** Nom : **ContosoAppServicePlanWestEurope** |

1. Dans la page d’accueil Azure, sélectionnez **Tous les services**, dans le menu de navigation de gauche, sélectionnez **Web**, puis sélectionnez **App Services**.

1. Les deux nouvelles applications web doivent apparaître.

   ![Image 19](../media/create-web-app-2.png)

## Tâche 2 : Créer un profil Traffic Manager

Vous allez maintenant créer un profil Traffic Manager qui dirige le trafic utilisateur en fonction de la priorité du point de terminaison.

1. Sur la page d’accueil du portail Azure, sélectionnez **Créer une ressource**.

1. Dans la zone de recherche en haut de la page, entrez **Profil Traffic Manager**, puis sélectionnez-le dans la liste contextuelle.

   ![Image 20](../media/create-tmprofile-1.png)

1. Sélectionnez **Créer**.

1. Sur la page **Créer un profil Traffic Manager**, utilisez les informations du tableau ci-dessous pour créer le profil Traffic Manager.

   | **Paramètre**             | **Valeur**                |
   | ----------------------- | ------------------------ |
   | Nom                    | **Contoso-TMProfilexx** (où xx sont vos initiales pour rendre le nom unique) |
   | Méthode de routage          | **Priorité**             |
   | Abonnement            | Sélectionnez votre abonnement |
   | Resource group          | **Contoso-RG-TM1**       |
   | Emplacement du groupe de ressources | **USA Est**              |

1. Sélectionnez **Vérifier + créer**, puis **Créer**.

## Tâche 3 : ajouter des points de terminaison Traffic Manager

Dans cette section, vous allez ajouter le site web dans la région USA Est comme point de terminaison principal pour acheminer tout le trafic utilisateur. Vous allez ensuite ajouter le site web dans la région Europe Ouest en tant que point de terminaison de basculement. Si le point de terminaison principal devient indisponible, le trafic est automatiquement acheminé vers le point de terminaison de basculement.

1. Dans la page d’accueil du portail Azure, sélectionnez **Toutes les ressources**, puis **Contoso-TMProfile** dans la liste des ressources.

1. Sous **Paramètres**, sélectionnez **Points de terminaison**, puis **Ajouter**.

   ![Image 21](../media/create-tmendpoints-1.png)

1. Sur la page **Ajouter un point de terminaison**, entrez les informations du tableau ci-dessous.

   | **Paramètre**          | **Valeur**                         |
   | -------------------- | --------------------------------- |
   | Type                 | **Point de terminaison Azure**                |
   | Nom                 | **myPrimaryEndpoint**             |
   | Type de ressource cible | **App Service**                   |
   | Ressource cible      | **ContosoWebAppEastUS (East US)** |
   | Priorité             | **1**                             |

1. Sélectionnez **Ajouter**.

1. Répétez les étapes 2 à 4 ci-dessus pour créer le point de terminaison de basculement. Utilisez les mêmes paramètres que précédemment, à l’exception des informations du tableau ci-dessous.

   | **Paramètre**     | **Valeur**                                 |
   | --------------- | ----------------------------------------- |
   | Nom            | **myFailoverEndpoint**                    |
   | Ressource cible | **ContosoWebAppWestEurope (West Europe)** |
   | Priorité        | **2**                                     |

1. Définir une priorité sur 2 signifie que le trafic est acheminé vers ce point de terminaison de basculement si le point de terminaison principal configuré devient non sain.

1. Sous **Paramètres**, sélectionnez **Configuration**, puis définissez le **Protocole** des paramètres de surveillance des points de terminaison sur HTTPS et le **Port** sur 443, puis sélectionnez **Enregistrer**.

1. Les deux nouveaux points de terminaison s’affichent dans le profil Traffic Manager. Notez qu’après quelques minutes, l'**État de la supervision** doit passer à **En ligne**.

   ![Image 22](../media/create-tmendpoints-2.png)

## Tâche 4 : Tester le profil Traffic Manager

Dans cette section, vous allez vérifier le nom DNS de votre profil Traffic Manager, puis vous allez configurer le point de terminaison principal afin qu’il ne soit pas disponible. Vous allez ensuite vous assurer que l’application web est toujours disponible pour vérifier que le profil Traffic Manager envoie correctement le trafic au point de terminaison de basculement.

1. Dans la page **Contoso-TMProfile**, sélectionnez **Vue d’ensemble**.

1. Dans l’écran **Vue d’ensemble**, copiez l’entrée **Nom DNS** dans le Presse-papiers (ou notez-la quelque part).

   ![Image 23](../media/check-dnsname-1.png)

1. Ouvrez un onglet de navigateur web et collez (ou entrez) l’entrée **Nom DNS**contoso-tmprofile.trafficmanager.net) dans la barre d’adresse, puis appuyez sur Entrée.

1. Le site web par défaut de l’application web doit être affiché. Si un message **Erreur 404 - Site web introuvable** s’affiche, **Désactivez le profil** à partir de la page de vue d’ensemble du profil Traffic Manager **Contoso-TMProfilexx** et **Activez le profil**. Actualisez ensuite la page web.

   ![Image 24](../media/tm-webapp-test-1a.png)

1. Actuellement, tout le trafic est envoyé au point de terminaison principal, car vous affectez la valeur **1** à sa **Priorité**.

1. Pour tester le bon fonctionnement du point de terminaison de basculement, vous devez désactiver le site principal.

1. Sur la page **Contoso-TMProfile**, dans l’écran de vue d’ensemble, sélectionnez le numéro **2** à droite de **Points de terminaison :**.

1. Sélectionnez le bouton Modifier (icône en forme de crayon) pour **myPrimaryEndpoint**.

1. Dans le volet **myPrimaryEndpoint**, sous **État**, décochez la case **Activer le point de terminaison**, puis sélectionnez **Enregistrer**.

1. Si nécessaire, fermez le volet **myPrimaryEndpoint** (sélectionnez le **X** dans le coin supérieur droit de la page).

1. Sur la page **Contoso-TMProfile**, l'**État de la supervision** pour **myPrimaryEndpoint** doit maintenant être **Désactivé**.

1. Ouvrez une nouvelle session de navigateur web et collez (ou entrez) l’entrée **Nom DNS** (contoso-tmprofile.trafficmanager.net) dans la barre d’adresse, puis appuyez sur Entrée.

1. Vérifiez que l’application web répond toujours. Étant donné que le point de terminaison principal n’était pas disponible, le trafic était acheminé vers le point de terminaison de basculement pour permettre au site web de continuer à fonctionner.

## Nettoyer les ressources

   >**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell

   Remove-AzResourceGroup -Name 'Contoso-RG-TM1' -Force -AsJob
   Remove-AzResourceGroup -Name 'Contoso-RG-TM2' -Force -AsJob

   ```

   >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.

## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+  Quelles sont les étapes générales de configuration d’Azure Traffic Manager ?
+ Comparez les situations dans lesquelles utiliser Azure Load Balancer et Azure Traffic Manager.
+ Expliquez les profils de routage Traffic Manager et les situations dans lesquelles ils doivent être utilisés.

## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Améliorez la disponibilité de vos services et l’emplacement de vos données avec Azure Traffic Manager](https://learn.microsoft.com/training/modules/distribute-load-with-traffic-manager/). Dans ce module, vous allez découvrir comment utiliser Traffic Manager pour distribuer dynamiquement le trafic réseau.
+ [Équilibrage de charge du trafic non-HTTP(S) dans Azure](https://learn.microsoft.com/training/modules/load-balancing-non-https-traffic-azure/). Dans ce module, vous allez découvrir Azure Traffic Manager et implémenter des méthodes de routage. 

## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 
+ Azure Traffic Manager est un équilibreur de charge de trafic DNS. Ce service vous permet de distribuer le trafic à vos applications publiques dans les régions Azure globales.
+ Traffic Manager comprend une série de méthodes de routage du trafic qui vous permet de contrôler la manière dont Traffic Manager choisit le point de terminaison qui doit recevoir le trafic de chaque utilisateur final. Combien pouvez-vous en citer ?
+ Vous pouvez imbriquer des profils Traffic Manager pour combiner les avantages de plusieurs méthodes de routage du trafic. Les profils imbriqués permettent de modifier le comportement par défaut de Traffic Manager pour prendre en charge des déploiements d’applications plus importants et plus complexes.
