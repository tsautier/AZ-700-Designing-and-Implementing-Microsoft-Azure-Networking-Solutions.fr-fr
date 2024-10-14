---
demo:
  title: 'VNET Peering            '
  module: Module 01 - Introduction to Azure Virtual Networks
---
## Configurer VNET Peering

**Remarque :**  Pour cette démonstration, vous aurez besoin de deux réseaux virtuels.

**Informations de référence** : [Connecter des réseaux virtuels avec le peering VNet - Tutoriel](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal)

**Configurer le peering de réseaux virtuels sur le premier réseau virtuel**

1. Dans le **portail Azure**, sélectionnez le premier réseau virtuel. Examinez la valeur du peering. 

1. Sous **Paramètres**, sélectionnez **Peerings** et **+ Ajoutez** un nouveau peering.

1. Configurez le peering du deuxième réseau virtuel. Utilisez les icônes d’informations pour passer en revue les différents paramètres. 

1. Une fois le peering terminé, passez en revue **l’état du peering**. 

**Confirmer le peering de réseaux virtuels sur le deuxième réseau virtuel**

1. Dans le **portail Azure**, sélectionnez le second réseau virtuel.

1. Sous **Paramètres**, sélectionnez **Peerings**.

1. Notez qu’un appairage a été créé automatiquement. Notez que **l’état du peering** est **Connecté**.

1. Dans le labo, les étudiants vont créer un peering et tester la connexion entre les machines virtuelles. 
