---
Exercise:
  title: M07 - Unité 5 Restreindre l’accès réseau aux ressources PaaS avec des points de terminaison de service de réseau virtuel
  module: Module 07 - Design and implement private access to Azure Services
---

# M07 - Unité 5 Restreindre l’accès réseau aux ressources PaaS avec des points de terminaison de service de réseau virtuel

## Scénario de l’exercice

Les points de terminaison de service de réseau virtuel permettent de restreindre l’accès réseau à certaines ressources du service Azure en n’autorisant leur accès qu’à partir d’un sous-réseau du réseau virtuel. Vous pouvez également supprimer l’accès Internet aux ressources. Les points de terminaison de service fournissent une connexion directe entre votre réseau virtuel et les services Azure pris en charge, ce qui vous permet d’utiliser l’espace d’adressage privé de votre réseau virtuel pour accéder aux services Azure. Le trafic destiné aux ressources Azure via les points de terminaison de service reste toujours sur le serveur principal de Microsoft Azure.

![Diagramme de l’architecture du point de terminaison de service.](../media/5-exercise-restrict-network-paas-resources-virtual-network-service-endpoints.png)

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
+ Tâche 11 : Nettoyer les ressources

**Remarque :** Une **[simulation de labo interactive](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Restrict%20network%20access%20to%20PaaS%20resources%20with%20virtual%20network%20service%20endpoints)** est disponible et vous permet de progresser à votre propre rythme. Il peut exister de légères différences entre la simulation interactive et le labo hébergé. Toutefois, les concepts et idées de base présentés sont identiques.

### Durée estimée : 35 minutes

## Tâche 1 : Créer un réseau virtuel

1. Connectez-vous au portail Azure.

1. Sur la page d’accueil du portail Azure, recherchez « réseau virtuel », puis sélectionnez **Réseau virtuel** dans les résultats de la recherche.

1. Sélectionnez **+** **Créer**.

1. Entrez ou sélectionnez les informations suivantes : ![interface utilisateur graphique, texte, application, description générée automatiquement](../media/create-virtual-network.png)

   | **Paramètre**    | **Valeur**                                     |
   | -------------- | --------------------------------------------- |
   | Abonnement   | Sélectionnez votre abonnement                      |
   | Resource group | (Nouveau) myResourceGroup                         |
   | Nom           | CoreServicesVNet                              |
   | Emplacement       | Sélectionnez **USA Est**.                            |

1. Sélectionnez l’onglet **Adresses IP** et entrez les valeurs suivantes (sélectionnez **par défaut** pour modifier le nom du sous-réseau) : ![interface utilisateur graphique, texte, application, adresse e-mail, description générée automatiquement](../media/create-virtual-network-ip.png).

   | **Paramètre**          | **Valeur**   |
   | -------------------- | ----------- |
   | Espace d’adressage        | 10.0.0.0/16 |
   | Nom du sous-réseau          | Public      |
   | Plage d’adresses de sous-réseau | 10.0.0.0/24 |

1. Sélectionnez l’onglet **Sécurité** et entrez les valeurs suivantes : ![interface utilisateur graphique, texte, application, adresse e-mail, description générée automatiquement](../media/ create-virtual-network-security.png).

   | **Paramètre**             | **Valeur** |
   | ----------------------- | --------- |
   | BastionHost             | Désactivé  |
   | Protection réseau DDoS | Désactivé  |
   | Pare-feu                | Désactivé  |

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

1. Sélectionnez **Enregistrer**.

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
   | Nom                    | Allow-Storage-All         |

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
   | Nom                    | Deny-Internet-All         |

1. Sélectionnez **Ajouter**.

## Tâche 5 : Autoriser l’accès pour les connexions RDP

Créez une règle de sécurité de trafic entrant qui autorise le trafic du protocole RDP (Remote Desktop Protocol) vers le sous-réseau en provenance de n’importe quel endroit. La règle remplace une règle de sécurité par défaut qui refuse tout le trafic entrant provenant d’Internet. Les connexions Bureau à distance sont autorisées sur le sous-réseau afin que la connectivité puisse être testée dans une étape ultérieure.

1. Sur ContosoPrivateNSG | Règles de sécurité de trafic sortant, sous **Paramètres**, sélectionnez **Règles de sécurité de trafic entrant**.

1. Sélectionnez **Ajouter**.

1. Dans Ajouter une règle de sécurité entrante, entrez les valeurs suivantes : ![interface utilisateur graphique, application, description générée automatiquement](../media/add-inbound-security-rule.png).

   | **Paramètre**             | **Valeur**                 |
   | ----------------------- | ------------------------- |
   | Source                  | Aucune                       |
   | Source port ranges      | *                         |
   | Destination             | Sélectionnez **VirtualNetwork** |
   | Service                 | Custom                    |
   | Plages de ports de destination | 3389                      |
   | Protocol                | Any                       |
   | Action                  | Autoriser                     |
   | Priority                | 120                       |
   | Nom                    | Allow-RDP-All             |

1. Ensuite, sélectionnez **Ajouter**.

> **Avertissement** : le port RDP 3389 est exposé à Internet. Ceci est recommandé uniquement pour les tests. Pour les environnements de production, nous vous recommandons d’utiliser une connexion VPN ou privée.

1. Sous **Paramètres**, sélectionnez **Sous-réseaux**.

1. Sélectionnez **+ Associer**.

1. Sous **Associer un sous-réseau**, sélectionnez **Réseau virtuel**, puis **CoreServicesVNet** sous **Choisir un réseau virtuel**.

1. Sous **Sous-réseau**, sélectionnez **Private** puis **OK**.

## Tâche 6 : Restreindre l’accès réseau à une ressource

Les étapes nécessaires pour restreindre l’accès réseau aux ressources créées par le biais des services Azure avec activation des points de terminaison varient d’un service à l’autre. Pour connaître les étapes à suivre, consultez la documentation relative à chacun des services. La suite de cet exercice comprend des étapes permettant de restreindre l’accès réseau pour un compte Stockage Azure, par exemple.

1. Dans le menu du portail Azure, sélectionnez Comptes de stockage.

1. Sélectionnez + Créer.

1. Saisissez ou sélectionnez les informations suivantes, et acceptez les autres valeurs par défaut :

   | **Paramètre**    | **Valeur**                                                    |
   | -------------- | ------------------------------------------------------------ |
   | Abonnement   | Sélectionnez votre abonnement                                     |
   | Resource group | myResourceGroup                                              |
   | Nom           | Entrez contosostoragewestxx (où xx sont vos initiales, pour le rendre unique). |
   | Performances    | StorageV2 standard (v2 universel)                      |
   | Emplacement       | Sélectionnez USA Est.                                               |
   | Réplication    | Stockage localement redondant (LRS)                              |

1. Sélectionnez **Vérifier**, puis **Créer**.

## Tâche 7 : Créer un partage de fichiers dans le compte de stockage

1. Une fois le compte de stockage créé, entrez le nom du compte de stockage dans le champ **Rechercher des ressources, services et documents** en haut du portail. Lorsque le nom de votre compte de stockage apparaît dans les résultats de la recherche, sélectionnez-le.
1. Sélectionnez **Partages de fichiers**, comme illustré par l’image suivante : ![interface utilisateur graphique, application, description générée automatiquement](../media/new-file-share-2.png).
1. Sélectionner **+Partage de fichiers**.
1. Entrez marketing sous **Nom**, puis sélectionnez **Suivant : sauvegarde**.
   ![Interface utilisateur graphique, application, description générée automatiquement](../media/new-file-share-basics.png)
1. Désélectionnez **Activer la sauvegarde**, comme illustré par l’image suivante : ![interface utilisateur graphique, application, description générée automatiquement](../media/new-file-share-backup.png).
1. Sélectionnez **Vérifier + créer**. Une fois la ressource validée, sélectionnez **Créer**.

## Tâche 8 : Restreindre l’accès réseau à un sous-réseau

Par défaut, les comptes de stockage acceptent les connexions réseau provenant des clients de n’importe quel réseau, y compris Internet. Refusez l’accès réseau à partir d’Internet et tous les autres sous-réseaux de tous les réseaux virtuels, à l’exception du sous-réseau Private du réseau virtuel CoreServicesVNet.

1. Sous **Sécurité + mise en réseau** pour le compte de stockage, sélectionnez **Mise en réseau**.

1. Sélectionnez **Activé à partir de réseaux virtuels et d’adresses IP sélectionnés**.

1. Sélectionnez **+Ajouter un réseau virtuel existant**.

1. Sous **Ajouter des réseaux**, sélectionnez les valeurs suivantes : ![interface utilisateur graphique, application, description générée automatiquement](../media/add-network-access.png).

   | **Paramètre**      | **Valeur**                    |
   | ---------------- | ---------------------------- |
   | Abonnement     | Sélectionnez votre abonnement.    |
   | Réseaux virtuels | Sélectionnez CoreServicesVNet **.** |
   | Sous-réseaux          | Sélectionnez **Privé**.          |

1. Sélectionnez **Ajouter**.

1. Sélectionnez **Enregistrer**.

1. Sous **Sécurité et mise en réseau** pour le compte de stockage, sélectionnez **Clés d’accès**.

1. Sélectionnez **Afficher les clés**. Notez la valeur **Clé**, car vous devrez l’entrer manuellement dans une étape ultérieure lors du mappage du partage de fichiers à une lettre de lecteur d’une machine virtuelle.

## Tâche 9 : Créer des machines virtuelles

Pour tester l’accès réseau à un compte de stockage, déployez une machine virtuelle sur chaque sous-réseau.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Charger/télécharger des fichiers**, dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **VMs.json** et **VMs.parameters.json** l’un après l’autre dans le répertoire racine de Cloud Shell à partir du dossier source **F:\Allfiles\Exercises\M07**.

1. Déployez les modèles ARM suivants pour créer les machines virtuelles nécessaires à cet exercice :

   >**Remarque** : Vous serez invité à fournir un mot de passe d’administrateur.

   ```powershell
   $RGName = "myResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile VMs.json -TemplateParameterFile VMs.parameters.json
   ```
  
1. Une fois le déploiement terminé, accédez à la page d’accueil du portail Azure, puis sélectionnez **Machines virtuelles**.

## Tâche 10 : Confirmer l’accès au compte de stockage

1. Une fois la création de la machine virtuelle ContosoPrivate terminée, ouvrez le panneau de la machine virtuelle en sélectionnant Accéder à la ressource. Sélectionnez le bouton Connecter, puis RDP.
   ![Interface utilisateur graphique, application, description générée automatiquement](../media/private-virtual-machine-connect.png)
1. Après avoir sélectionné le bouton Connecter et RDP, sélectionnez le bouton Télécharger le fichier RDP. Un fichier .rdp (Remote Desktop Protocol) est créé et téléchargé sur votre ordinateur.
1. Ouvrez le fichier .rdp téléchargé. Si vous y êtes invité, sélectionnez Connexion. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle. Vous devrez peut-être sélectionner Plus de choix, puis Utiliser un autre compte, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.
1. Cliquez sur **OK**.
1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez l’avertissement, sélectionnez Oui ou Continuer pour poursuivre le processus de connexion.
1. Sur la machine virtuelle ContosoPrivate, mappez le partage de fichiers Azure au lecteur Z à l’aide de PowerShell. Avant d’exécuter les commandes qui suivent, remplacez <storage-account-key>,  <storage-account-name> (par exemple contosostoragexx) et my-file-share (par exemple marketing) par les valeurs que vous avez fournies et récupérées dans la tâche Créer un compte de stockage.

```azurecli
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force

$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey

New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\marketing" -Credential $credential

```

Le partage de fichiers Azure est correctement mappé au lecteur Z.

1. Vérifiez que la machine virtuelle ne dispose d’aucune connexion sortante à Internet à partir d’une invite de commande :

 ping bing.com

Vous ne recevez aucune réponse, car le groupe de sécurité réseau associé au sous-réseau Private n’autorise pas l’accès sortant à Internet.

1. Fermez la session Bureau à distance sur la machine virtuelle ContosoPrivate.

### Vérifier que l’accès au compte de stockage est refusé

1. Entrez ContosoPublic dans la zone **Rechercher des ressources, services et documents** en haut du portail.

1. Lorsque **ContosoPublic** apparaît dans les résultats de la recherche, sélectionnez-le.

1. Répétez les étapes 1 à 6 de la tâche Vérifier l’accès au compte de stockage pour la machine virtuelle ContosoPublic.  

   ‎Après une brève attente, une erreur s’affiche : New-PSDrive : accès refusé. L’accès est refusé, car la machine virtuelle ContosoPublic est déployée dans le sous-réseau Public. Le sous-réseau Public ne dispose d’aucun point de terminaison de service activé pour le Stockage Azure. Le compte de stockage permet uniquement l’accès à partir du sous-réseau Private et non au sous-réseau Public.

1. Vérifiez que la machine virtuelle ne dispose d’aucune connexion sortante à Internet en entrant l’invite de commande :

 ping bing.com

1. Fermez la session Bureau à distance sur la machine virtuelle ContosoPublic.

1. Sur votre ordinateur, accédez au portail Azure.

1. Entrez le nom du compte de stockage que vous avez créé dans le champ **Rechercher des ressources, services et documents**. Lorsque le nom de votre compte de stockage apparaît dans les résultats de la recherche, sélectionnez-le.

1. Sélectionnez **Partages de fichiers**, puis sélectionnez le partage **marketing**.

1. L’erreur indiquée dans la capture d’écran suivante s’affiche :

    ![Interface utilisateur graphique, texte, application, e-mail, description générée automatiquement](../media/no-access.png)

 L’accès est refusé, car votre ordinateur ne se trouve pas dans le sous-réseau Private du réseau virtuel CoreServicesVNet.

> **Avertissement** : avant de continuer, vous devez supprimer toutes les ressources utilisées pour ce labo. Pour ce faire, dans le portail Azure, sélectionnez Groupes de ressources. Sélectionnez les groupes de ressources que vous avez créés. Dans le panneau du groupe de ressources, sélectionnez Supprimer un groupe de ressources, entrez le nom du groupe de ressources, puis sélectionnez Supprimer. Répétez le processus pour tous les groupes de ressources supplémentaires que vous avez créés. Sinon, vous risquez de provoquer des problèmes avec d’autres labos.

Résultats : vous avez maintenant terminé ce labo.

## Tâche 11 : Nettoyer les ressources

   >**Remarque** : N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées vous évitera d’encourir des frais inattendus.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Supprimez tous les groupes de ressources que vous avez créés dans les labos de ce module en exécutant la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name 'myResourceGroup' -Force -AsJob
   ```

    >**Remarque** : La commande s’exécute de façon asynchrone (tel que déterminé par le paramètre -AsJob). Vous pourrez donc exécuter une autre commande PowerShell immédiatement après au cours de la même session PowerShell, mais la suppression effective du groupe de ressources peut prendre quelques minutes.
