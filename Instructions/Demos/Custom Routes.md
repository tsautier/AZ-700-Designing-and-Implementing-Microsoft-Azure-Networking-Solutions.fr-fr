---
demo:
  module: Module 01 - Introduction to Azure Virtual Networks
  title: "Module\_01\_: itinéraires personnalisés"
---
## Configurer le routage et les points de terminaison réseau

Dans cette démonstration, nous allons découvrir comment créer une table de routage, définir une route personnalisée et associer la route à un sous-réseau.

**Remarque :**  Cette démonstration nécessite un réseau virtuel avec au moins un sous-réseau.

**Informations de référence** : [Router le trafic réseau - Tutoriel - Portail Azure](https://learn.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal#create-a-route-table)

**Créer une table de routage**

1. Quand vous en avez le temps, passez en revue le diagramme du tutoriel. Expliquez pourquoi vous devez créer une route définie par l’utilisateur. 

1. Accédez au portail Azure.

1. Recherchez et sélectionnez **Tables de routage**. Expliquez quand la **propagation des routes de passerelle** doit être utilisée. 

1. Créez une table de routage et expliquez les paramètres inhabituels. 

1. Attendez que la nouvelle table de routage soit déployée.

**Ajouter un itinéraire**

1.  Sélectionnez votre nouvelle table de routage, puis  **Routes**.

1.  Créez une nouvelle **route**. Décrivez les différents **types de tronçons** disponibles. 

1.  Créez la nouvelle route, puis attendez que la ressource soit déployée.
 
**Associer une table de routage à un sous-réseau**

1.  Accédez au sous-réseau que vous souhaitez associer à la table de routage.

1.  Sélectionnez **Table de routage**, puis choisissez votre nouvelle table de routage. 

1.  **Enregistrez** vos modifications.


