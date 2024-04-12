---
Exercise:
  title: "M07 - Unité 6 Créer un point de terminaison privé Azure à l’aide d’Azure\_PowerShell"
  module: Module 07 - Design and implement private access to Azure Services
---

# M07 - Unité 6 Créer un point de terminaison privé Azure à l’aide d’Azure PowerShell

## Scénario de l’exercice

Démarrez avec Azure Private Link en utilisant un point de terminaison privé pour vous connecter en toute sécurité à une application web Azure. Il existe de nombreuses façons de créer des points de terminaison, notamment le portail, l’interface CLI, PowerShell, etc.

![Diagramme de l’architecture d’un point de terminaison privé.](../media/6-exercise-create-azure-private-endpoint-using-azure-powershell.png)


**Remarque :** Une **[simulation de labo interactive](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Create%20an%20Azure%20private%20endpoint%20using%20Azure%20PowerShell)** est disponible et vous permet de progresser à votre propre rythme. Il peut exister de légères différences entre la simulation interactive et le labo hébergé. Toutefois, les concepts et idées de base présentés sont identiques.

### Durée estimée : 45 minutes

Vous allez créer un point de terminaison privé pour une application web Azure et déployer une machine virtuelle pour tester la connexion privée.

Des points de terminaison privés peuvent être créés pour différents types de services Azure, tels qu’Azure SQL et Stockage Azure.

**Conditions préalables**

- Une application web Azure avec un niveau PremiumV2 ou un plan App Service supérieur déployé dans votre abonnement Azure.

- Les étapes ci-dessous vous guident tout au long de la création du groupe de ressources et de l’application web nécessaires.

1. Recherchez et ouvrez **parameters.json** dans le dossier M07. Ouvrez-le dans le Bloc-notes et recherchez la ligne "value": "GEN-UNIQUE". Remplacez la chaîne GEN-UNIQUE d’espace réservé par une valeur unique pour votre nom d’application web. Enregistrez cette modification.

1. Dans le portail Azure, ouvrez la session **PowerShell** dans le volet **Cloud Shell**.

1. Dans la barre d’outils du volet Cloud Shell, sélectionnez l’icône **Charger/télécharger des fichiers**, dans le menu déroulant, sélectionnez **Charger** et chargez les fichiers **template.json** et **parameters.json** dans le répertoire racine de Cloud Shell l’un après l’autre.

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet exemple. Exécutez ```Get-Module -ListAvailable Az``` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter ```Connect-AzAccount``` pour créer une connexion avec Azure.

Dans cet exercice, vous allez :

- Tâche 1 : Créer un groupe de ressources
- Tâche 2 : Créer un réseau virtuel et un hôte bastion
- Tâche 3 : Créer une machine virtuelle de test
- Tâche 4 : Créer un point de terminaison privé
- Tâche 5 : Configurer la zone DNS privée
- Tâche 6 : Tester la connectivité vers le point de terminaison privé
- Tâche 7 : Nettoyer les ressources

## Tâche 1 : créer un groupe de ressources et déployer l’application web utilisée

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec [New-AzResourceGroup](https://docs.microsoft.com/en-us/powershell/module/az.resources/new-azresourcegroup) :

```PowerShell
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
```

Déployez les modèles ARM suivants pour créer les applications web Azure PremiumV2-tier nécessaires à cet exercice :

   ```powershell
   $RGName = "CreatePrivateEndpointQS-rg"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile template.json -TemplateParameterFile parameters.json
   ```

Si une erreur s’affiche (par exemple, lors de la recherche de l’état de déploiement dans le portail) comme « Un site web portant le nom GEN-UNIQUE existe déjà », veillez à accéder aux conditions préalables mentionnées ci-dessus en ce qui concerne la modification du modèle.

## Tâche 2 : Créer un réseau virtuel et un hôte bastion

Vous allez créer un réseau virtuel, un sous-réseau et un hôte bastion.

L’hôte bastion sera utilisé pour se connecter de façon sécurisée à la machine virtuelle afin de tester le point de terminaison privé.

Créez un réseau virtuel et un hôte bastion avec :

- New-AzVirtualNetwork

- New-AzPublicIpAddress

- New-AzBastion

```PowerShell
## Create backend subnet config. ##

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##

$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##

$parameters1 = @{

 Name = 'MyVNet'

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Location = 'eastus'

 AddressPrefix = '10.0.0.0/16'

 Subnet = $subnetConfig, $bastsubnetConfig

}

$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##

$parameters2 = @{

 Name = 'myBastionIP'

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Location = 'eastus'

 Sku = 'Standard'

 AllocationMethod = 'Static'

}

$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##

$parameters3 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Name = 'myBastion'

 PublicIpAddress = $publicip

 VirtualNetwork = $vnet

}

New-AzBastion @parameters3
```

## Tâche 3 : Créer une machine virtuelle de test

Dans cette section, vous allez créer une machine virtuelle qui sera utilisée pour tester le point de terminaison privé.

Utilisez les commandes suivantes pour créer la machine virtuelle :

- Get-Credential (remarque : vous serez invité à fournir un mot de passe d’administrateur)

- New-AzNetworkInterface

- New-AzVM

- New-AzVMConfig

- Set-AzVMOperatingSystem

- Set-AzVMSourceImage

- Add-AzVMNetworkInterface

```PowerShell
## Set credentials for server admin and password. ##

$cred = Get-Credential

## Command to get virtual network configuration. ##

$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##

$parameters1 = @{

 Name = 'myNicVM'

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Location = 'eastus'

 Subnet = $vnet.Subnets[0]

}

$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##

$parameters2 = @{

 VMName = 'myVM'

 VMSize = 'Standard_DS1_v2'

}

$parameters3 = @{

 ComputerName = 'myVM'

 Credential = $cred

}

$parameters4 = @{

 PublisherName = 'MicrosoftWindowsServer'

 Offer = 'WindowsServer'

 Skus = '2019-Datacenter'

 Version = 'latest'

}

$vmConfig = New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##

New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig 


```

Azure fournit une adresse IP éphémère pour les machines virtuelles Azure qui n’ont pas d’adresse IP publique ou qui se trouvent dans le pool de back-ends d’un Azure Load Balancer de base interne. Le mécanisme d’adresse IP éphémère fournit une adresse IP sortante qui n’est pas configurable.

L’adresse IP éphémère est désactivée lorsqu’une adresse IP publique est attribuée à la machine virtuelle ou que celle-ci est placée dans le pool de back-ends d’un Standard Load Balancer avec ou sans règles de trafic sortant. Si une ressource de passerelle NAT de réseau virtuel Azure est affectée au sous-réseau de la machine virtuelle, l’adresse IP éphémère est désactivée.

Pour plus d’informations sur les connexions sortantes dans Azure, consultez Utilisation de SNAT (Source Network Address Translation) pour les connexions sortantes.

## Tâche 4 : Créer un point de terminaison privé

Dans cette section, vous allez créer le point de terminaison privé et une connexion avec :

- New-AzPrivateLinkServiceConnection

- New-AzPrivateEndpoint

```PowerShell
## Place web app into variable. This assumes that only one web app exists in the resource group. ##

$webapp = Get-AzWebApp -ResourceGroupName CreatePrivateEndpointQS-rg

## Create Private Endpoint connection. ##

$parameters1 = @{

 Name = 'myConnection'

 PrivateLinkServiceId = $webapp.ID

 GroupID = 'sites'

}

$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##

$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##

$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork

## Create private endpoint

$parameters2 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Name = 'myPrivateEndpoint'

 Location = 'eastus'

 Subnet = $vnet.Subnets[0]

 PrivateLinkServiceConnection = $privateEndpointConnection

}

New-AzPrivateEndpoint @parameters2 
```

## Tâche 5 : Configurer la zone DNS privée

Dans cette section, vous allez créer et configurer la zone DNS privée avec :

- New-AzPrivateDnsZone

- New-AzPrivateDnsVirtualNetworkLink

- New-AzPrivateDnsZoneConfig

- New-AzPrivateDnsZoneGroup

```PowerShell
## Place virtual network into variable. ##

$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##

$parameters1 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Name = 'privatelink.azurewebsites.net'

}

$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##

$parameters2 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 ZoneName = 'privatelink.azurewebsites.net'

 Name = 'myLink'

 VirtualNetworkId = $vnet.Id

}

$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##

$parameters3 = @{

 Name = 'privatelink.azurewebsites.net'

 PrivateDnsZoneId = $zone.ResourceId

}

$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##

$parameters4 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 PrivateEndpointName = 'myPrivateEndpoint'

 Name = 'myZoneGroup'

 PrivateDnsZoneConfig = $config

}

New-AzPrivateDnsZoneGroup @parameters4 
```

## Tâche 6 : Tester la connectivité vers le point de terminaison privé

Dans cette section, vous allez utiliser la machine virtuelle que vous avez créée à l’étape précédente pour vous connecter à l’application web via le point de terminaison privé.

1. Connectez-vous au [portail Azure](https://portal.azure.com/)

1. Dans le volet de navigation de gauche, sélectionnez **Groupes de ressources**.

1. Sélectionnez **CreatePrivateEndpointQS-rg**.

1. Sélectionnez **myVM**.

1. Dans la page de vue d’ensemble pour **myVM**, sélectionnez **Se connecter**, puis **Bastion**.

1. Sélectionnez le bouton bleu **Utiliser le bastion**.

1. Entrez le nom d’utilisateur et le mot de passe que vous avez utilisés lors de la création de la machine virtuelle.

1. Ouvrez Windows PowerShell sur le serveur après vous être connecté.

1. Entrez nslookup &lt;your- webapp-name&gt;.azurewebsites.net. Remplacez &lt;your-webapp-name&gt; par le nom de l’application web que vous avez créée dans les étapes précédentes. Vous recevez un message similaire à ce qui est montré ci-dessous :

  ```
  Server: UnKnown
  
  Address: 168.63.129.16
  
  Non-authoritative answer:
  
  Name: mywebapp8675.privatelink.azurewebsites.net
  
  Address: 10.0.0.5
  
  Aliases: mywebapp8675.azurewebsites.net 
  ```  

L’adresse IP privée **10.0.0.5** est retournée pour le nom de l’application web. Cette adresse se trouve dans le sous-réseau du réseau virtuel que vous avez créé précédemment.

1. Dans la connexion bastion à **myVM**, ouvrez Internet Explorer.
1. Entrez l’URL de votre application web, **https://&lt;your-webapp-name&gt;.azurewebsites.net**.
1. La page d’application web par défaut s’affichera si votre application n’a pas été déployée : ![capture d’écran de page dans Azure indiquant qu’un service d’application est opérationnel](../media/web-app-default-page.png)
1. Fermez la connexion à **myVM**.

## Tâche 7 : Nettoyer les ressources

Lorsque vous avez fini d’utiliser le point de terminaison privé et la machine virtuelle, supprimez le groupe de ressources et toutes les ressources qu’il contient avec [Remove-AzResourceGroup](https://docs.microsoft.com/en-us/powershell/module/az.resources/remove-azresourcegroup) :

```PowerShell
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force -AsJob
```
