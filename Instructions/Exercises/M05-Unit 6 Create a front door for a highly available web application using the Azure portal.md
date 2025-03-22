---
Exercise:
  title: M05 – Unité 6 Créer une instance Front Door pour une application web hautement disponible à l’aide du portail Azure
  module: Module 05 - Load balancing HTTP(S) traffic in Azure
---



# M05 – Unité 6 Créer une instance Front Door pour une application web hautement disponible à l’aide du portail Azure

## Scénario de l’exercice  

Dans cet exercice, vous allez configurer une configuration Azure Front Door regroupant deux instances d’une application web qui s’exécutent dans différentes régions Azure. Cette configuration dirige le trafic vers le site le plus proche qui exécute l’application. Azure Front Door supervise en permanence l’application web. Vous ferez la preuve d’un basculement automatique vers le site disponible suivant quand le site le plus proche n’est pas disponible. La configuration réseau est présentée dans la table suivante :

![Configuration réseau pour Azure Front Door.](../media/6-exercise-create-front-door-for-highly-available.png)

Dans cet exercice, vous allez :

+ Tâche 1 : créer deux instances d’une application web
+ Tâche 2 : créer une instance Front Door pour votre application
+ Tâche 3 : afficher Azure Front Door à l’œuvre


   >**Remarque :** Une **[simulation de labo interactive](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Create%20a%20Front%20Door%20profile%20for%20a%20highly%20available%20web%20application)** est disponible et vous permet de progresser à votre propre rythme. Il peut exister de légères différences entre la simulation interactive et le labo hébergé. Toutefois, les concepts et idées de base présentés sont identiques.

### Durée estimée : 30 minutes

## Tâche 1 : créer deux instances d’une application web

Cet exercice demande deux instances d’une application web qui s’exécutent dans différentes régions Azure. Les deux instances de l’application web s’exécutent en mode Actif/Actif, donc l’une ou l’autre peut s’occuper du trafic. Cette configuration diffère d’une configuration Active/Veille où l’une sert de basculement.

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

1. Sur la page d’accueil Azure, dans la zone de recherche globale, entrez **WebApp** et sélectionnez **App Services** sous services.

1. Sélectionnez **+ Créer** pour créer une application web.

1. Sur la page Créer une application web, sous l’onglet **Informations de base**, entrez ou sélectionnez les informations suivantes.

   | **Paramètre**      | **Valeur**                                                    |
   | ---------------- | ------------------------------------------------------------ |
   | Abonnement     | Sélectionnez votre abonnement.                                    |
   | Resource group   | Sélectionnez le groupe de ressources ContosoResourceGroup.               |
   | Nom             | Attribuez un Nom unique à votre application web. Cet exemple utilise WebAppContoso-1. |
   | Publish          | Sélectionnez **Code**.                                             |
   | Pile d’exécution    | Sélectionnez **.NET 8 (LTS)**.                                     |
   | Système d’exploitation | Sélectionnez **Windows**.                                          |
   | Région           | Sélectionnez **USA Centre**.                                       |
   | Plan Windows     | Sélectionnez **Créer** et entrez myAppServicePlanCentralUS dans la zone de texte. |
   | Plan tarifaire    | Sélectionnez **Standard S1 100 ACU au total, 1,75 Go de mémoire**.        |

1. Sélectionnez **Analyser + créer**, vérifiez le Résumé, puis sélectionnez **Créer**.
   Le déploiement peut prendre plusieurs minutes.

1. Créez une deuxième application web. Sur la page d’accueil du portail Azure, recherchez **WebApp**.

1. Sélectionnez **+ Créer** pour créer une application web.

1. Sur la page Créer une application web, sous l’onglet **Informations de base**, entrez ou sélectionnez les informations suivantes.

   | **Paramètre**      | **Valeur**                                                    |
   | ---------------- | ------------------------------------------------------------ |
   | Abonnement     | Sélectionnez votre abonnement.                                    |
   | Resource group   | Sélectionnez le groupe de ressources ContosoResourceGroup.               |
   | Nom             | Attribuez un Nom unique à votre application web. Cet exemple utilise WebAppContoso-2. |
   | Publish          | Sélectionnez **Code**.                                             |
   | Pile d’exécution    | Sélectionnez **.NET 8 (LTS)**.                                     |
   | Système d’exploitation | Sélectionnez **Windows**.                                          |
   | Région           | Sélectionnez **USA Est**.                                          |
   | Plan Windows     | Sélectionnez **Créer** et entrez myAppServicePlanEastUS dans la zone de texte. |
   | Plan tarifaire     | Sélectionnez **Standard S1 100 ACU au total, 1,75 Go de mémoire**.        |

1. Sélectionnez **Analyser + créer**, vérifiez le Résumé, puis sélectionnez **Créer**.
   Le déploiement peut prendre plusieurs minutes.

   >**Remarque :** si vous recevez une erreur de déploiement, lisez attentivement la notification. Si l’erreur implique la disponibilité de la région en raison de quotas, essayez de passer à une autre région. 

## Tâche 2 : créer une instance Front Door pour votre application

Configurez Azure Front Door pour diriger le trafic utilisateur selon la plus petite latence entre les serveurs des deux applications web. Pour commencer, ajoutez un hôte front-end pour Azure Front Door.

1. Sur n’importe quelle page du portail Azure, dans **Rechercher dans les ressources, services et documents (G+/)**, recherchez des profils Front Door et CDN, puis sélectionnez **Profils Front Door et CDN**.

1. Sélectionnez **Créer des profils Front Door et CDN**. Sur la page Comparer les offres, sélectionnez **Création rapide**. Sélectionnez ensuite **Continuer pour créer une instance Front Door**.

1. Sous l’onglet Informations de base, entrez ou sélectionnez les informations suivantes.

   | **Paramètre**             | **Valeur**                                    |
   | ----------------------- | -------------------------------------------- |
   | Abonnement            | Sélectionnez votre abonnement.                    |
   | Resource group          | Sélectionnez ContosoResourceGroup                  |
   | Emplacement du groupe de ressources | Acceptez les paramètres par défaut.                       |
   | Nom                    | Entrez un nom unique dans cet abonnement, par exemple FrontDoor(vosinitiales).   |
   | Niveau                    | Standard   |
   | Nom du point de terminaison           | FDendpoint   |
   | Type d'origine             | App Service|
   | Nom d’hôte d’origine        | Nom de l’application web que vous avez déployée précédemment |

1. Sélectionnez **Vérifier et créer**, puis sélectionnez **Créer**.

1. Attendez le déploiement des ressources, puis sélectionnez **Accéder à la ressource**.

1. Dans la ressource Front Door, au sein du panneau Vue d’ensemble, recherchez les **Groupes d’origine** et sélectionnez le groupe d’origine créé.

1. Pour mettre à jour le groupe d’origine, sélectionnez le nom **default-origin-group** dans la liste. Sélectionnez **Ajouter une origine** et ajoutez la deuxième application web. Sélectionnez Ajouter, puis sélectionnez Mettre à jour.

## Tâche 3 : afficher Azure Front Door à l’œuvre

Une fois la porte d’entrée créée, le déploiement global de la configuration prend quelques minutes. Une fois l’opération terminée, accédez à l’hôte front-end que vous avez créé.

1. Dans la ressource Front Door, au sein du panneau Vue d’ensemble, recherchez le nom d’hôte créé pour votre point de terminaison. Il doit être sous la forme fdendpoint suivi d’un trait d’union et d’une chaîne de caractères aléatoires. Par exemple, **fdendpoint-fxa8c8hddhhgcrb9.z01.azurefd.net**. **Copiez** ce nom de domaine complet.

1. Dans un nouvel onglet de navigateur, accédez au nom de domaine complet du point de terminaison Front Door. La page App Service par défaut s’affichera.
   ![Navigateur présentant la page d’informations App Service](../media/app-service-info-page.png)

1. Pour tester le basculement global instantané, essayez les étapes suivantes :

1. Passez au Portail Azure, recherchez et sélectionnez **App Services**.

1. Sélectionnez l’une de vos applications web, sélectionnez **Arrêter**, puis cliquez sur **Oui** pour vérifier.

   ![Portail Azure montrant l’application web arrêtée](../media/stop-web-app.png)

1. Revenez à votre navigateur, puis sélectionnez Actualiser. Vous devriez voir la même page d’informations.

**Il peut y avoir un retard suite à l’arrêt de l’application web. Si vous recevez une page d’erreur dans votre navigateur, actualisez la page**.

1. Revenez au portail Azure, recherchez l’autre application web, puis arrêtez-la.

1. Revenez à votre navigateur, puis sélectionnez Actualiser. Cette fois, vous devriez voir un message d’erreur.

   ![Navigateur présentant la page d’erreur App Service](../media/web-apps-both-stopped.png)

   Félicitations ! Vous avez configuré et testé Azure Front Door.

## Nettoyer les ressources

   >**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell

   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob

   ```

   >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.

## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+ Quelle sont les différences entre Azure Front Door et Azure Application Gateway ? Donnez des exemples de situations dans lesquelles utiliser chaque produit.
+ Fournissez une liste de contrôle des tâches à effectuer lors de la configuration d’Azure Front Door.
+ Qu’est-ce qu’une origine dans Azure Front Door et en quoi est-elle différente d’un point de terminaison ?


## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Présentation d’Azure Front Door](https://learn.microsoft.com/training/modules/intro-to-azure-front-door/). Dans ce module, vous allez découvrir comment Azure Front Door peut protéger vos applications.
+ [Équilibrez la charge du trafic de votre service web avec Front Door](https://learn.microsoft.com/training/modules/create-first-azure-front-door/). Dans ce module, vous allez apprendre à créer et configurer Azure Front Door. 

## Points clés

Félicitations, vous avez terminé le labo. Voici les principaux points à retenir de ce labo. 
+ Azure Front Door est un service cloud qui permet de diffuser vos applications partout dans le monde. 
+ Azure Front Door utilise l’équilibrage de charge de couche 7 pour distribuer le trafic entre plusieurs régions et plusieurs points de terminaison.
+ Azure Front Door prend en charge quatre méthodes différentes de routage du trafic pour déterminer comment votre trafic HTTP/HTTPS est distribué. Les méthodes de routage sont les suivantes : latence, priorité, pondération et affinité de session. 
