---
demo:
  module: Module 05 - Load balancing HTTPS traffic
  title: "Application\_Gateway (module\_05)"
---
## Configurer Azure Application Gateway

Dans cette démonstration, nous allons apprendre à créer une instance Azure Application Gateway. 

**Référence** : [Démarrage rapide : Diriger le trafic web avec Azure Application Gateway - Portail Azure](https://learn.microsoft.com/azure/application-gateway/quick-create-portal)

**Référence** : [chiffrer le trafic réseau de bout en bout avec Azure Application Gateway](https://github.com/MicrosoftDocs/mslearn-end-to-end-encryption-with-app-gateway)

**Remarque** : Pour simplifier les choses, créez des réseaux virtuels et des sous-réseaux lorsque vous procédez à la configuration. 

**Créer l’instance Azure Application Gateway**

1. Accédez au portail Azure.

1. Recherchez et sélectionnez **Azure Application Gateway**.

1. **Créez** une nouvelle passerelle.

1. Sous l’onglet **Informations de base**, décidez du **Niveau**, de la **Mise à l’échelle automatique** et du **Nombre d’instances**.

1. Sous l’onglet **Front-ends**, décidez des types d’adresses IP.

1. Sous l’onglet **Back-ends**, décidez quand utiliser un pool de back-ends vide.

1. Sous l’onglet **Configuration**, décidez des règles de routage. Comparez avec les règles de l’équilibreur de charge.

1. Expliquez qu’une fois la passerelle créée, vous ajouterez des cibles de back-end et vous les testerez. 
