---
Exercise:
  title: M07 - Unité 5 Restreindre l’accès réseau aux ressources PaaS avec des points de terminaison de service de réseau virtuel
  module: Module 07 - Design and implement private access to Azure Services
---

# M07 - Unité 5 Restreindre l’accès réseau aux ressources PaaS avec des points de terminaison de service de réseau virtuel

## Scénario de l’exercice

Les points de terminaison de service de réseau virtuel permettent de restreindre l’accès réseau à certaines ressources du service Azure en n’autorisant leur accès qu’à partir d’un sous-réseau du réseau virtuel. Vous pouvez également supprimer l’accès Internet aux ressources. Les points de terminaison de service fournissent une connexion directe entre votre réseau virtuel et les services Azure pris en charge, ce qui vous permet d’utiliser l’espace d’adressage privé de votre réseau virtuel pour accéder aux services Azure. Le trafic destiné aux ressources Azure via les points de terminaison de service reste toujours sur le serveur principal de Microsoft Azure.

![Diagramme de l’architecture du point de terminaison de service.](../media/5-exercise-restrict-network-paas-resources-virtual-network-service-endpoints.png)

### Compétences de tâche

Dans cet exercice, vous allez :

+ Tâche 1 : Créer un réseau virtuel
+ Tâche 2 : Activer un point de terminaison de service
+ Tâche 3 : Restreindre l’accès réseau d’un sous-réseau
+ Tâche 4 : Ajouter des règles de trafic sortant supplémentaires
+ Tâche 5 : Autoriser l’accès pour les connexions RDP
+ Tâche 6 : Restreindre l’accès réseau à une ressource
+ Tâche 7 : Créer un partage de fichiers dans le compte de stockage
+ Tâche 8 : Restreindre l’accès réseau à un sous-réseau
+ Tâche 9 : Créer des machines virtuelles
+ Tâche 10 : Confirmer l’accès au compte de stockage


### Simulations de labo interactives

>**Note** : les simulations de labo qui ont été fournies précédemment ont été supprimées.

### Durée estimée : 35 minutes

## Tâche 1 : Créer un réseau virtuel

1. Connectez-vous au portail Azure.

1. Dans la page d’accueil du portail Microsoft Azure, recherchez `virtual network`, puis sélectionnez **Réseau virtuel** dans les résultats.

1. Sélectionnez **+** **Créer**.

1. Entrez ou sélectionnez les informations suivantes : ![interface utilisateur graphique, texte, application, description générée automatiquement](../media/create-virtual-network.png)

   | **Paramètre**    | **Valeur**                                     |
   | -------------- | --------------------------------------------- |
   | Abonnement   | Sélectionnez votre abonnement                      |
   | Resource group | (Nouveau) myResourceGroup                         |
   | Nom           | CoreServicesVNet                              |
   | Emplacement       | Sélectionnez **USA Est**.                            |

1. Sélectionnez l’onglet **Sécurité** et entrez les valeurs suivantes : ![interface utilisateur graphique, texte, application, adresse e-mail, description générée automatiquement](../media/ create-virtual-network-security.png).

   | **Paramètre**             | **Valeur** |
   | ----------------------- | --------- |
   | BastionHost             | Désactivé  |
   | Protection réseau DDoS | Désactivé  |
   | Pare-feu                | Désactivé  |

1. Sélectionnez l’onglet **Adresses IP** et entrez les valeurs suivantes (sélectionnez **par défaut** pour modifier le nom du sous-réseau) : ![interface utilisateur graphique, texte, application, adresse e-mail, description générée automatiquement](../media/create-virtual-network-ip.png).

   | **Paramètre**          | **Valeur**   |
   | -------------------- | ----------- |
   | Espace d’adressage        | 10.0.0.0/16 |
   | Nom du sous-réseau          | Public      |
   | Plage d’adresses de sous-réseau | 10.0.0.0/24 |

1. Sélectionnez **Vérifier + créer**. Une fois la ressource validée, sélectionnez **Créer**.

## Tâche 2 : Activer un point de terminaison de service

Les points de terminaison de service sont activés par service, par sous-réseau. Créer un sous-réseau et activer un point de terminaison de service pour le sous-réseau.

1. Dans la zone **Rechercher des ressources, services et documents** en haut du portail, tapez CoreServicesVNet. Lorsque la mention CoreServicesVNet apparaît dans les résultats de recherche, sélectionnez-la.

1. Ajoutez un sous-réseau au réseau virtuel. Sous **Paramètres**, sélectionnez **Sous-réseaux**, puis **+ Sous-réseau**, comme illustré par l’image suivante : ![interface utilisateur graphique, application, description générée automatiquement](../media/create-subnet.png).

1. Sous **Ajouter un sous-réseau**, sélectionnez ou entrez les informations suivantes :

   | **Paramètre**                 | **Valeur**                    |
   | --------------------------- | ---------------------------- |
   | Name                        | Privée                      |
   | Plage d’adresses               | 10.0.1.0/24                  |
   | Points de terminaison de service : services | Sélectionnez **Microsoft.Storage** |

1. Sélectionnez **Ajouter**.

Deux sous-réseaux doivent maintenant être configurés :

![Interface utilisateur graphique, texte, application, e-mail, description générée automatiquement](../media/configured-subnets.png)

## Tâche 3 : Restreindre l’accès réseau d’un sous-réseau

Par défaut, toutes les machines virtuelles d’un sous-réseau peuvent communiquer avec l’ensemble des ressources. Vous pouvez limiter les communications vers et à partir de toutes les ressources d’un sous-réseau par la création d’un groupe de sécurité réseau et l’association au sous-réseau.

1. Dans la zone **Rechercher des ressources, services et documents** en haut du portail, tapez **groupe de sécurité**. Lorsque **Groupes de sécurité réseau** apparaît dans les résultats de la recherche, sélectionnez-le.

1. Dans Groupes de sécurité réseau, sélectionnez **+ Créer**.

1. Entrez ou sélectionnez les informations suivantes :

   | **Paramètre**    | **Valeur**                                                    |
   | -------------- | ------------------------------------------------------------ |
   | Abonnement   | Sélectionnez votre abonnement                                     |
   | Resource group | myResourceGroup                                              |
   | Nom           | ContosoPrivateNSG                                            |
   | Emplacement       | Sélectionnez **USA Est**.                                           |

1. Sélectionnez **Vérifier + créer**, puis **Créer** :

1. Une fois le groupe de sécurité réseau ContosoPrivateNSG créé, sélectionnez **Accéder à la ressource**.

1. Sous **Paramètres**, sélectionnez **Règles de sécurité de trafic sortant**.

1. Sélectionnez **Ajouter**.

1. Créer une règle qui autorise les communications sortantes vers le service Stockage Azure. Entrez ou sélectionnez les informations suivantes : ![interface utilisateur graphique, application, description générée automatiquement](../media/add-outbound-security-rule.png).

   | **Paramètre**             | **Valeur**                 |
   | ----------------------- | ------------------------- |
   | Source                  | Sélectionnez **Service Tag** (Identification)    |
   | Balise du service source      | Sélectionnez **VirtualNetwork** |
   | Source port ranges      | *                         |
   | Destination             | Sélectionnez **Service Tag** (Identification)    |
   | Identification de destination | Sélectionnez **Stockage**        |
   | Service                 | Custom                    |
   | Plages de ports de destination | *                         |
   | Protocol                | Any                       |
   | Action                  | Autoriser                     |
   | Priorité                | 100                       |
   | Nom                    | `Allow-Storage-All`         |

1. Sélectionnez **Ajouter** :

## Tâche 4 : Ajouter des règles de trafic sortant supplémentaires

Créer une règle de sécurité de trafic sortant qui refuse les communications vers Internet. Cette règle qui permet la communication Internet sortante se substitue à une règle par défaut dans tous les groupes de sécurité réseau.

1. Sélectionnez **+ Ajouter** sous **Règles de sécurité de trafic sortant**.

1. Entrez ou sélectionnez les informations suivantes : ![interface utilisateur graphique, application, adresse e-mail, description générée automatiquement](../media/add-outbound-security-rule-deny.png).

   | **Paramètre**             | **Valeur**                 |
   | ----------------------- | ------------------------- |
   | Source                  | Sélectionnez **Service Tag** (Identification)    |
   | Balise du service source      | Sélectionnez **VirtualNetwork** |
   | Source port ranges      | *                         |
   | Destination             | Sélectionnez **Service Tag** (Identification)    |
   | Identification de destination | Sélectionnez **Internet**       |
   | Service                 | Custom                    |
   | Plages de ports de destination | *                         |
   | Protocol                | Any                       |
   | Action                  | Deny                      |
   | Priority                | 110                       |
   | Nom                    | `Deny-Internet-All`         |

1. Sélectionnez **Ajouter**.

## Tâche 5 : Autoriser l’accès pour les connexions RDP

Créez une règle de sécurité de trafic entrant qui autorise le trafic du protocole RDP (Remote Desktop Protocol) vers le sous-réseau en provenance de n’importe quel endroit. La règle remplace une règle de sécurité par défaut qui refuse tout le trafic entrant provenant d’Internet. Les connexions Bureau à distance sont autorisées sur le sous-réseau afin que la connectivité puisse être testée dans une étape ultérieure.

1. Sur ContosoPrivateNSG | Règles de sécurité de trafic sortant, sous **Paramètres**, sélectionnez **Règles de sécurité de trafic entrant**.

1. Sélectionnez **Ajouter**.

1. Dans Ajouter une règle de sécurité entrante, entrez les valeurs suivantes : ![interface utilisateur graphique, application, description générée automatiquement](../media/add-inbound-security-rule.png).

   | **Paramètre**             | **Valeur**                 |
   | ----------------------- | ------------------------- |
   | Source                  | Quelconque                       |
   | Source port ranges      | *                         |
   | Destination             | Quelconque                       |
   | Service                 | Custom                    |
   | Plages de ports de destination | 3389                      |
   | Protocol                | Any                       |
   | Action                  | Autoriser                     |
   | Priority                | 120                       |
   | Nom                    | `Allow-RDP-All`            |

1. Ensuite, sélectionnez **Ajouter**.

> **Avertissement** : le port RDP 3389 est exposé à Internet. Ceci est recommandé uniquement pour les tests. Pour les environnements de production, nous vous recommandons d’utiliser une connexion VPN ou privée.

1. Sous **Paramètres**, sélectionnez **Sous-réseaux**.

1. Sélectionnez **+ Associer**.

1. Sous **Associer un sous-réseau**, sélectionnez **Réseau virtuel**, puis **CoreServicesVNet** sous **Choisir un réseau virtuel**.

1. Sous **Sous-réseau**, sélectionnez **Private** puis **OK**.

## Tâche 6 : Restreindre l’accès réseau à une ressource

Les étapes nécessaires pour restreindre l’accès réseau aux ressources créées par le biais des services Azure avec activation des points de terminaison varient d’un service à l’autre. Pour connaître les étapes à suivre, consultez la documentation relative à chacun des services. La suite de cet exercice comprend des étapes permettant de restreindre l’accès réseau pour un compte Stockage Azure, par exemple.

1. Dans le portail Azure, recherchez et sélectionnez `Storage accounts`.

1. Sélectionnez **Créer**.

1. Saisissez ou sélectionnez les informations suivantes, et acceptez les autres valeurs par défaut :

   | **Paramètre**    | **Valeur**                                                    |
   | -------------- | ------------------------------------------------------------ |
   | Abonnement   | Sélectionnez votre abonnement                                     |
   | Resource group | myResourceGroup                                              |
   | Nom           | Entrez contosostoragewestxx (où xx sont vos initiales, pour le rendre unique). |
   | Service principal | Azure Files                                                |
   | Performances    | StorageV2 standard (v2 universel)                      |
   | Emplacement       | Sélectionnez USA Est.                                               |
   | Réplication    | Stockage localement redondant (LRS)                              |

1. Sélectionnez **Vérifier**, puis **Créer**.

1. Une fois le compte de stockage déployé, sélectionnez **Accéder à la ressource**. 

## Tâche 7 : Créer un partage de fichiers dans le compte de stockage

1. Dans le compte de stockage, dans le panneau **Stockage des données**, sélectionnez **Partages de fichiers**.

1. Sélectionner **+Partage de fichiers**. 

1. Pour **Nom**, entrez **marketing**, puis sélectionnez **Suivant : Sauvegarde**.

1. Désélectionnez **Activer la sauvegarde**, comme illustré dans l’image suivante : 

1. Sélectionnez **Vérifier + créer**. Une fois la ressource validée, sélectionnez **Créer**.

## Tâche 8 : Restreindre l’accès réseau à un sous-réseau

Par défaut, les comptes de stockage acceptent les connexions réseau provenant des clients de n’importe quel réseau, y compris Internet. Refusez l’accès réseau à partir d’Internet et tous les autres sous-réseaux de tous les réseaux virtuels, à l’exception du sous-réseau Private du réseau virtuel CoreServicesVNet.

1. Sous **Sécurité + mise en réseau** pour le compte de stockage, sélectionnez **Mise en réseau**.

1. Sélectionnez **Gérer** dans la section **Accès au réseau public**.

Dans la section **Étendue d’accès public**, sélectionnez **Activé dans les réseaux sélectionnés**.

1. Sélectionnez **+ Ajouter un réseau virtuel existant**, puis **Ajouter un réseau virtuel existant**. 

   | **Paramètre**      | **Valeur**                    |
   | ---------------- | ---------------------------- |
   | Abonnement     | Sélectionnez votre abonnement.    |
   | Réseaux virtuels | **CoreServicesVNet** |
   | Sous-réseaux          | **Privé**.          |

1. Sélectionnez **Ajouter**, puis **Enregistrer**.

1. Sous **Sécurité et mise en réseau** pour le compte de stockage, sélectionnez **Clés d’accès**.

1. Utilisez **Afficher** pour la valeur **Key1**. Copiez la valeur, vous en aurez besoin ultérieurement. 

## Tâche 9 : Créer des machines virtuelles

Pour tester l’accès réseau à un compte de stockage, déployez une machine virtuelle sur chaque sous-réseau.

1. Sélectionnez l’icône Cloud Shell en haut à droite du portail Azure. Si nécessaire, configurez l’interpréteur de commandes.  
    + Sélectionnez **PowerShell**.
    + Sélectionnez **Aucun compte de stockage requis** et votre **abonnement**, puis sélectionnez **Appliquer**.
    + Attendez que le terminal crée et qu’une invite s’affiche. 

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Gérer des fichiers**. Dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **VMs.json** et **VMs.parameters.json** dans le répertoire de base de Cloud Shell. Ce module 07, exercice 05. 

1. Déployez les modèles ARM suivants pour créer les machines virtuelles nécessaires à cet exercice :

   >**Remarque** : Vous serez invité à fournir un mot de passe d’administrateur.

   ```powershell
   $RGName = "myResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile VMs.json -TemplateParameterFile VMs.parameters.json
   ```
  
1. Une fois le déploiement terminé, accédez à la page d’accueil du portail Azure, puis sélectionnez **Machines virtuelles**.

## Tâche 10 : Confirmer l’accès au compte de stockage

1. Dans le portail, recherchez et sélectionnez la machine virtuelle **ContosoPrivate**.

1. Sélectionnez **Se connecter**, **Se connecter**, puis **Télécharger le fichier RDP**. Si vous y êtes invité, confirmez le téléchargement.

1. Dans le dossier **Téléchargements**, ouvrez le fichier ContosoPrivate.rdp.

1. Sélectionnez **Se connecter** et indiquez le mot de passe de la machine virtuelle. Sélectionnez **Ok** et indiquez **Oui** à l’avertissement du certificat. 

1. Utilisez PowerShell pour créer un partage de fichiers. Remplacez <storage-account-key1-value> , <storage-account-name> (par exemple, contosostoragexx) par les valeurs lorsque vous avez créé le compte de stockage. 
    ```powershell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key1-value>" -AsPlainText -Force

    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey

    New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\marketing" -Credential $credential

    ```
1. Vérifiez que la machine virtuelle n’a pas de connectivité sortante. Vous ne recevez aucune réponse, car le groupe de sécurité réseau associé au sous-réseau Private n’autorise pas l’accès sortant à Internet.

    ```ping bing.com```

1. Fermez la session Bureau à distance sur la machine virtuelle ContosoPrivate.

### Vérifier que l’accès au compte de stockage est refusé

1. Retournez au Portail Azure.

1. Accédez à votre compte de stockage, sélectionnez **Partages de fichiers**, puis sélectionnez le partage de fichiers **marketing**. 

1. Sélectionnez **Parcourir** et notez l’erreur d’accès refusé. Votre erreur peut être différente.  L’accès est refusé, car votre ordinateur ne se trouve pas dans le sous-réseau Private du réseau virtuel CoreServicesVNet.

    ![Interface utilisateur graphique, texte, application, courrier électronique – Description générée automatiquement](../media/no-access.png)

## Nettoyer les ressources

>**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name 'myResourceGroup' -Force -AsJob
   ```

   >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.

## Développer votre apprentissage avec Copilot

Copilot peut vous aider à apprendre à utiliser les outils de script Azure. Copilot peut également aider dans des domaines non couverts dans le labo ou quand vous avez besoin de plus d’informations. Ouvrez un navigateur Edge et choisissez Copilot (en haut à droite), ou accédez à *copilot.microsoft.com*. Prenez quelques minutes pour essayer ces invites.
+ Quelle est la différence entre des points de terminaison de service Azure et des points de terminaison privés ?
+ Quels services Azure peuvent utiliser des points de terminaison de service ?
+ Quelles sont les étapes permettant de restreindre l’accès au stockage Azure à l’aide de points de terminaison de service ?

## En savoir plus grâce à l’apprentissage auto-rythmé

+ [Sécuriser et isoler l’accès aux ressources Azure en utilisant des groupes de sécurité réseau et des points de terminaison de service](https://learn.microsoft.com/training/modules/secure-and-isolate-with-nsg-and-service-endpoints/). Dans ce module, vous allez découvrir comment utiliser des points de terminaison de service de réseau virtuel pour contrôler le trafic réseau en provenance et à destination des services Azure.

## Points clés
+ Les points de terminaison de service de réseau virtuel étendent votre espace d’adressage privé dans Azure en fournissant une connexion directe à vos services Azure.
+ Les points de terminaison de service vous permettent de sécuriser vos ressources Azure uniquement pour votre réseau virtuel. Le trafic de service demeure sur le réseau principal Azure et n’accède pas à Internet.
+ Les points de terminaison de service Azure sont disponibles pour de nombreux services, tels que : stockage Azure, Azure SQL Database et Azure Cosmos DB.
+ Les points de terminaison de service de réseau virtuel ne sont pas, par défaut, accessibles à partir de réseaux locaux. Pour accéder aux ressources à partir d’un réseau local, utilisez des adresses IP NAT.
