---
demo:
  module: Module 01 - Introduction to Azure Virtual Networks
  title: "Mod\_01\_: résolution de noms\_DNS"
---
## Configurer Azure DNS

Dans cette démonstration, vous allez explorer Azure DNS.

**Informations de référence** : [Tutoriel : Héberger votre domaine et votre sous-domaine - Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)


**Création d’une zone DNS**

1. Accédez au portail Azure.

1. Recherchez le service **Zones DNS**.

1. Créez une **zone DNS** et expliquez l’objectif de la zone. Pour le nom, vous pouvez utiliser contoso.internal.com.

1.  Attendez que la zone DNS soit créée. Il peut être nécessaire  **d’actualiser** la page.

**Ajouter un jeu d’enregistrements DNS**

**Informations de référence** : [Tutoriel : Créer un enregistrement d’alias pour référencer un enregistrement de ressource de zone](https://learn.microsoft.com/azure/dns/tutorial-alias-rr)

1. Une fois votre zone créée, sélectionnez **+Jeu d’enregistrements**.

1. Utilisez la liste déroulante **Type** pour voir les différents types d’enregistrements. Expliquez comment les différents types d’enregistrements sont utilisés. Notez que les informations de l’enregistrement changent à mesure que vous sélectionnez différents types d’enregistrements.

1. Créez un enregistrement **A** à titre d’exemple. 

