---
demo:
  title: Passerelles VPN
  module: Module 02 - Design and implement hybrid networking
---
Dans cette démonstration, nous allons explorer les passerelles de réseau virtuel.

**Remarque :**  Cette démonstration fonctionne mieux avec deux réseaux virtuels avec des sous-réseaux.

## Explorer le panneau Sous-réseau de passerelle
1. Pour l’un de vos réseaux virtuels, sélectionnez le panneau  **Sous-réseaux** .
1. Sélectionnez  **+ Sous-réseau de passerelle**. Notez que le nom du sous-réseau ne peut pas être modifié. Notez la  **plage d’adresses**  du sous-réseau de passerelle. L’adresse doit être contenue dans l’espace d’adressage du réseau virtuel.
1. N’oubliez pas que chaque réseau virtuel a besoin d’un sous-réseau de passerelle.
1. Fermez la page Ajouter un sous-réseau de passerelle. Vous n’avez pas besoin d’enregistrer vos modifications.

## Explorer le panneau Appareils connectés
1. Pour le réseau virtuel, sélectionnez le panneau  **Appareils connectés** .
1. Une fois qu’un sous-réseau de passerelle est déployé, il apparaît dans la liste des appareils connectés.

## Explorer l’ajout d’une passerelle de réseau virtuel
1. Recherchez  **Passerelles de réseau virtuel**.
1. Cliquez sur  **+ Ajouter**.
1. Passez en revue chaque paramètre pour la passerelle de réseau virtuel.
1. Utilisez les icônes d’informations pour en savoir plus sur les paramètres.
1. Notez les informations suivantes :  **type de passerelle**,  **type de VPN** et  **référence SKU**.
1. Notez la nécessité d’une  **adresse IP publique**.
1. N’oubliez pas que chaque réseau virtuel a besoin d’une passerelle de réseau virtuel.
1. Fermez Ajouter une passerelle de réseau virtuel. Vous n’avez pas besoin d’enregistrer vos modifications.
   
## Explorer l’ajout d’une connexion entre les réseaux virtuels
1. Recherchez des  **connexions**.
1. Cliquez sur  **+ Ajouter**.
1. Notez que le  **type de connexion**  peut être Réseau virtuel à réseau virtuel, Site à site (IPsec) ou ExpressRoute.
1. Fournissez suffisamment d’informations pour pouvoir cliquer sur le bouton  **OK** .
1. Dans la page  **Paramètres** , notez que vous devez sélectionner les deux réseaux virtuels différents.
1. Lisez les informations d’aide sur la case à cocher  **Établir une connectivité bidirectionnelle** .
1. Notez les informations de la  **clé partagée (PSK)** .
1. Fermez la page Ajouter une connexion. Vous n’avez pas besoin d’enregistrer vos modifications.
