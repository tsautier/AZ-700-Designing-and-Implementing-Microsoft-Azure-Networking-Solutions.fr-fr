---
demo:
  module: Module 04 - Load balancing non-HTTPS traffic
  title: "Équilibreur de charge (module\_04)"
---
## Configurer Azure Load Balancer

Dans cette démonstration, nous allons apprendre à créer un équilibreur de charge public. 

**Référence** : [Démarrage rapide : Créer un équilibreur de charge public pour équilibrer la charge des machines virtuelles en utilisant le portail Azure](https://learn.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)

**Remarque :**  Cette démonstration nécessite un réseau virtuel avec au moins un sous-réseau. 

**Afficher la fonctionnalité Aidez-moi à choisir du portail**

1. Accédez au portail Azure.

1. Recherchez et sélectionnez **Équilibrage de charge - Aidez-moi à choisir**.

1. Utilisez l’Assistant pour parcourir les différents scénarios.
   
**Créer un équilibreur de charge**

1. Continuez dans le portail Azure.

1. Recherchez et sélectionnez **Équilibreur de charge**. **Créez** un équilibreur de charge. 

1. Sous l’onglet **Informations de base**, décidez de la **Référence SKU**, du **Type** et du **Niveau**.

1. Sous l’onglet **Configuration d’adresse IP frontale**, décidez d’utiliser une adresse IP publique ou non.

1. Sous l’onglet **Pools de back-ends**, sélectionnez le réseau virtuel avec la plage d’adresses IP.

1. Sous l’onglet **Règles de trafic entrant**, créez une règle d’équilibrage de charge. Décidez des paramètres tels que **Protocole**, **Ports**, **Sondes d’intégrité** et **Persistance de session**. 


