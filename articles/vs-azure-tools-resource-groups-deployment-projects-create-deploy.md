<properties
   pageTitle="Projets de groupe de ressources Azure avec Visual Studio | Microsoft Azure"
   description="Utilisez Visual Studio pour créer un projet de groupe de ressources Azure et déployer les ressources dans Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="tomfitz" />

# Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio

Avec Visual Studio et les [Kits de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/), vous pouvez créer un projet qui déploie votre infrastructure et votre code sur Azure. Par exemple, vous pouvez définir l’hôte web, le site web et la base de données de votre application, et déployer cette infrastructure parallèlement au code. Ou bien, vous pouvez définir une machine virtuelle, le réseau virtuel et le compte de stockage, puis déployer cette infrastructure parallèlement à un script exécuté sur la machine virtuelle. Le projet de déploiement du **Groupe de ressources Azure** vous permet de déployer toutes les ressources nécessaires en une seule opération reproductible. Pour plus d’informations sur le déploiement et la gestion des ressources, consultez [Présentation d’Azure Resource Manager](resource-group-overview.md).

Les projets de groupe de ressources Azure contiennent des modèles JSON Azure Resource Manager, qui définissent les ressources que vous déployez sur Azure. Pour en savoir plus sur les éléments du modèle Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md). Visual Studio vous permet de modifier ces modèles et fournit des outils qui simplifient l’utilisation des modèles.

Dans cette rubrique, vous déployez une application web et une base de données SQL. Toutefois, les étapes sont presque identiques pour tous les types de ressources. Vous pouvez tout aussi facilement déployer une machine virtuelle et ses ressources associées. Visual Studio fournit de nombreux modèles de démarrage différents pour déployer des scénarios courants.

Cet article présente Visual Studio 2015 Update 2 et le Kit de développement logiciel (SDK) Microsoft Azure pour .NET 2.9. Si vous utilisez Visual Studio 2013 avec le Kit de développement logiciel (SDK) Azure 2.9, votre expérience sera en grande partie la même. Vous pouvez également utiliser la version 2.6 ou les versions ultérieures du Kit de développement logiciel (SDK) Azure, mais l’interface utilisateur que vous verrez peut différer de celle illustrée dans cet article. Nous vous recommandons fortement d’installer la dernière version du [Kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/) avant de commencer à suivre les étapes.

## Créer un projet de groupe de ressources Azure

Au cours de cette procédure, vous allez apprendre à créer un projet de groupe de ressources Azure avec un modèle **Application web + SQL**.

1. Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**, puis choisissez **C#** ou **Visual Basic**. Choisissez ensuite **Cloud** et le projet de **Groupe de ressources Azure**.

    ![Projet de déploiement cloud](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Choisissez le modèle à déployer sur Azure Resource Manager. Notez qu’il existe de nombreuses options différentes selon le type de projet que vous voulez déployer. Pour cet exemple, sélectionnez le modèle **Application web + SQL**.

    ![Sélectionner un modèle](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    Le modèle que vous choisissez est seulement un point de départ ; vous pouvez ajouter et supprimer des ressources en fonction des besoins de votre scénario.

    >[AZURE.NOTE] Visual Studio extrait une liste des modèles disponibles en ligne. La liste peut évoluer.

    Visual Studio crée un projet de déploiement de groupe de ressources Azure pour une application web et une base de données SQL.

1. Pour voir les éléments créés, développez les nœuds du projet de déploiement.

    ![afficher les nœuds](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Puisque nous avons choisi le modèle Application web + SQL pour cet exemple, vous voyez les fichiers suivants.

    |Nom de fichier|Description|
    |---|---|
    |Deploy-AzureResourceGroup.ps1|Script PowerShell invoquant des commandes PowerShell à déployer dans Azure Resource Manager.<br />**Remarque** Visual Studio utilise ce script PowerShell pour déployer votre modèle. Les modifications apportées à ce script affectent également le déploiement dans Visual Studio. Par conséquent, modifiez ce script avec prudence.|
    |WebSiteSQLDatabase.json|Le modèle Resource Manager qui définit l’infrastructure que vous voulez déployer sur Azure, et les paramètres que vous pouvez fournir au cours du déploiement. Il définit également les dépendances entre les ressources de manière à ce que Resource Manager les déploie dans le bon ordre.|
    |WebSiteSQLDatabase.parameters.json|Un fichier de paramètres qui contient les valeurs requises par le modèle. Vous transmettez des valeurs de paramètres pour personnaliser chaque déploiement.|

    Tous les projets de déploiement de groupe de ressources Azure contiennent ces fichiers de base. D'autres projets peuvent contenir des fichiers supplémentaires pour prendre en charge d'autres fonctionnalités.

## Personnaliser le modèle de gestionnaire de ressources

Vous pouvez personnaliser un projet de déploiement en modifiant les modèles JSON qui décrivent les ressources à déployer. JSON signifie JavaScript Object Notation. Il s'agit d'un format de données sérialisées facile à utiliser. Les fichiers JSON utilisent un schéma que vous référencez au début de chaque fichier. Si vous souhaitez comprendre le schéma, vous pouvez le télécharger et l’analyser. Le schéma définit les éléments valides, les types et les formats des champs, les valeurs énumérées possibles, etc. Pour en savoir plus sur les éléments du modèle Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

Pour travailler sur votre modèle, ouvrez **WebSiteSQLDatabase.json**.

L’éditeur Visual Studio fournit des outils pour vous aider à modifier le modèle Resource Manager. La fenêtre **Structure JSON** permet de voir facilement les éléments définis dans votre modèle.

![afficher la structure JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Il vous suffit de sélectionner l’un des éléments de la structure pour accéder à cette partie du modèle et mettre en évidence le JSON correspondant.

![explorer JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Vous pouvez ajouter une ressource en sélectionnant le bouton **Ajouter une ressource** en haut de la fenêtre Structure JSON, ou en double-cliquant sur **ressources** et en sélectionnant **Ajouter une nouvelle ressource**.

![ajouter une ressource](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Pour ce didacticiel, sélectionnez **Compte de stockage** et donnez-lui un nom. Un nom de compte de stockage doit être composé uniquement de chiffres et de lettres en minuscule, et comporter moins de 24 caractères. Le projet ajoute une chaîne unique de 13 caractères au nom que vous fournissez, alors assurez-vous que votre nom ne comporte pas plus de 11 caractères.

![ajouter du stockage](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Notez qu’en plus de la ressource, un paramètre pour le compte de stockage de type et une variable pour le nom du compte de stockage ont été ajoutés.

![afficher la structure](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

Le paramètre **storageType** est prédéfini avec des types autorisés et un type par défaut. Vous pouvez laisser ces valeurs ou les modifier pour votre scénario. Si vous ne souhaitez autoriser personne à déployer un compte de stockage **Premium\_LRS** via ce modèle, supprimez simplement ce dernier des types autorisés.

    "storageType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

Visual Studio fournit également des fonctionnalités intellisense pour vous aider à comprendre quelles propriétés sont disponibles lors de la modification du modèle. Par exemple, pour modifier les propriétés de votre plan App Service, accédez à la ressource **HostingPlan** et ajoutez une valeur pour les **propriétés**. Notez qu’intellisense affiche les valeurs disponibles et fournit une description de cette valeur.

![afficher intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Vous pouvez définir **numberOfWorkers** sur 1.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## Déployer le projet de groupe de ressources sur Azure

Vous êtes maintenant prêt à déployer votre projet. Lorsque vous déployez un projet de groupe de ressources Azure, vous le déployez dans un groupe de ressources Azure. Un groupe de ressources est un regroupement logique de ressources qui partagent un cycle de vie commun.

1. Dans le menu contextuel du nœud du projet de déploiement, choisissez **Déployer** > **Nouveau déploiement**.

    ![Déployer, élément de menu Nouveau déploiement](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

    La boîte de dialogue **Déployer vers le groupe de ressources** s’affiche.

    ![Boîte de dialogue Déployer vers le groupe de ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. Dans la zone de liste déroulante **Groupe de ressources**, sélectionnez un groupe de ressources existant ou créez-en un. Pour créer un groupe de ressources, ouvrez la zone de liste déroulante **Groupe de ressources** et sélectionnez **Créer**.

    ![Boîte de dialogue Déployer vers le groupe de ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    La boîte de dialogue **Créer un groupe de ressources** s’affiche. Donnez à votre groupe un nom et un emplacement, puis sélectionnez le bouton **Créer**.

    ![Boîte de dialogue Créer un groupe de ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. Vous pouvez modifier les paramètres du déploiement en choisissant le bouton **Modifier les paramètres**. Fournissez les valeurs des paramètres et sélectionnez le bouton **Enregistrer**.

    ![Boîte de dialogue Modifier les paramètres](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
    L’option **Enregistrer les mots de passe en texte brut dans le fichier de paramètres** n’est pas sécurisée.

1. Sélectionnez le bouton **Déployer** pour déployer le projet dans Azure. Vous pouvez suivre la progression du déploiement dans la fenêtre **Sortie**. Le déploiement peut prendre quelques minutes. Sa durée dépend de votre configuration. Entrez le mot de passe d’administrateur de base de données dans la console PowerShell lorsque vous y êtes invité. Si la progression de votre déploiement est bloquée, cela peut être dû au fait que le processus attend la saisie du mot de passe dans la console PowerShell.

    >[AZURE.NOTE] Il se peut que Visual Studio vous invite à installer les applets de commande Azure PowerShell. Vous devez disposer des applets de commande Azure PowerShell pour déployer des groupes de ressources. Si vous y êtes invité, installez-les.
    
1. Une fois le déploiement terminé, vous devriez voir un message dans la fenêtre **Sortie**, quelque chose comme :

        ...
        15:19:19 - DeploymentName     : websitesqldatabase-0212-2318
        15:19:19 - CorrelationId      : 6cb43be5-86b4-478f-9e2c-7e7ce86b26a2
        15:19:19 - ResourceGroupName  : DemoSiteGroup
        15:19:19 - ProvisioningState  : Succeeded
        ...

1. Dans un navigateur, ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous à votre compte. Pour afficher le groupe de ressources, sélectionnez **Groupes de ressources** et le groupe de ressources vers lequel vous avez effectué le déploiement.

    ![sélectionner un groupe](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. Vous verrez toutes les ressources déployées.

    ![afficher des ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. Si vous modifiez et redéployez votre projet, choisissez le groupe de ressources existant dans le menu contextuel du projet de groupe de ressources Azure. Dans le menu contextuel, sélectionnez **Déployer**, puis le groupe de ressources que vous avez déployé.

    ![Groupe de ressources Azure déployé](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## Déployer le code avec votre infrastructure

À ce stade, vous avez déployé l’infrastructure de votre application, mais aucun code réel n’est déployé avec le projet. Cette rubrique montre comment déployer une application web et des tables de base de données SQL lors du déploiement. Si vous déployez une machine virtuelle au lieu d’une application web, vous devrez exécuter du code sur la machine dans le cadre du déploiement. Le processus de déploiement du code pour une application web ou pour la configuration d’une machine virtuelle est quasiment le même.

1. Dans votre solution Visual Studio, ajoutez **Application Web ASP.NET**.

    ![ajouter une application web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. Sélectionnez **MVC** et effacez le champ **Héberger dans le cloud** car le projet de groupe de ressources effectue cette tâche.

    ![sélectionner MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. Une fois que Visual Studio a créé votre application web, ajoutez une référence dans le projet de groupe de ressources pour le projet d’application web.

    ![ajouter une référence](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    En ajoutant une référence, vous liez le projet d’application web au projet de groupe de ressources et définissez automatiquement trois propriétés clés.
    
    - **Propriétés supplémentaires** contient l’emplacement intermédiaire du package de déploiement web qui est envoyé vers Azure Storage.
    - **Inclure le chemin d’accès au fichier** contient le chemin d’accès à l’emplacement de création du package. **Inclure les cibles** contient la commande que ce déploiement exécute.
    - La valeur par défaut **Build;Package** permet au déploiement de construire et créer un package de déploiement web (package.zip).
    
    Vous n’avez pas besoin de profil de publication, car le déploiement obtient les informations nécessaires à partir des propriétés pour créer le package.
    
      ![voir une référence](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
      
1. Ajoutez une ressource au modèle et cette fois sélectionnez **Web Deploy pour les applications web**.

    ![ajouter web deploy](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. Redéployez votre projet de groupe de ressources vers le groupe de ressources. Cette fois, il y a de nouveaux paramètres. Vous n’avez pas besoin de fournir des valeurs pour **\_artifactsLocation** ou **\_artifactsLocationSasToken** car Visual Studio les génère automatiquement. Définissez le dossier et le nom de fichier sur le chemin d’accès qui contient le package de déploiement.

    ![ajouter web deploy](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    Pour le **Compte de stockage des artefacts**, vous pouvez utiliser celui déployé avec ce groupe de ressources.
    
Une fois le déploiement terminé, parcourez le site et notez que l’application ASP.NET par défaut a été déployée.

![afficher l’application déployée](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## Étapes suivantes

- Pour plus d’informations sur la gestion des ressources via le portail, voir [Utilisation du portail Azure pour gérer vos ressources Azure](./azure-portal/resource-group-portal.md).
- Pour en savoir plus sur les modèles, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0810_2016-->