---
demo:
  title: "Mod\_02\_: router vers des services partagés"
  module: Module 02 - Design and implement hybrid networking
---
Pensez à en faire la démonstration aux étudiants : 

[Démarrage rapide : Acheminer vers des services partagés à l’aide d’un modèle ARM – Azure Virtual WAN | Microsoft Docs](https://learn.microsoft.com/azure/virtual-wan/quickstart-route-shared-services-vnet-template)

Ce guide de démarrage rapide explique comment utiliser un modèle Azure Resource Manager (modèle ARM) pour configurer des routes afin d’accéder à un réseau virtuel de services partagés avec des charges de travail auxquelles vous voulez que chaque réseau virtuel et chaque branche (VPN/ER/P2S) accède. Voici des exemples de ces charges de travail partagées : des machines virtuelles avec des services comme des contrôleurs de domaine ou des partages de fichiers, ou des services Azure exposés en interne via un point de terminaison privé Azure.
Un modèle ARM est un fichier JSON (JavaScript Object Notation) qui définit l’infrastructure et la configuration de votre projet. Le modèle utilise la syntaxe déclarative. Dans la syntaxe déclarative, vous décrivez le déploiement souhaité sans écrire la séquence de commandes de programmation pour créer le déploiement.
Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton Déployer sur Azure. Le modèle s’ouvre dans le portail Azure.

