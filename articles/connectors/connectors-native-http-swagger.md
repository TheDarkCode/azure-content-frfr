
<properties
	pageTitle="Ajout de l'action HTTP + Swagger dans des applications logiques | Microsoft Azure"
	description="Vue d’ensemble de l’action et des opérations HTTP + Swagger"
	services=""
	documentationCenter=""
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Prise en main de l’action HTTP + Swagger

Avec l’action HTTP + Swagger, vous pouvez créer un connecteur de première classe pour n’importe quel point de terminaison REST avec un [document Swagger](https://swagger.io). Vous pouvez également étendre une application logique pour appeler n’importe quel point de terminaison REST avec une expérience de conception Logic App de première classe.

Pour commencer à utiliser l’action HTTP + Swagger dans une application logique, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Utiliser HTTP + Swagger comme un déclencheur ou d’une action

Le déclencheur et l’action HTTP + Swagger fonctionnement comme une [action HTTP](connectors-native-http.md) mais en fournissant une meilleure expérience de conception en affichant la forme de l’API et émet dans l’outil de création à partir des [métadonnées Swagger](https://swagger.io). En outre, vous pouvez utiliser HTTP + Swagger en tant que déclencheur. Si vous souhaitez implémenter un déclencheur d’interrogation, celui-ci doit suivre le modèle d’interrogation décrit dans la rubrique [Création d’une API personnalisée à utiliser avec Logic Apps](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).

[En savoir plus sur les déclencheurs et actions de l’application logique.](connectors-overview.md)

Voici un exemple d’utilisation de l’opération HTTP + Swagger en tant qu’action dans un flux de travail dans une application logique.

1. Sélectionnez le bouton **Nouvelle étape**.
2. Sélectionnez **Ajouter une action**.
3. Dans la zone de recherche Action , tapez **Swagger** pour répertorier l’action HTTP + Swagger.

	![Sélectionnez une action HTTP + Swagger](./media/connectors-native-http-swagger/using-action-1.png)

4. Entrez l’URL d'un document Swagger :
	- Pour fonctionner à partir du concepteur d'applications logiques, l’URL doit être un point de terminaison HTTPS et avoir CORS activé.
	- Si le document Swagger ne répond pas à ce critère, vous pouvez utiliser [Azure Storage avec CORS activé](#hosting-swagger-from-storage) pour stocker le document.
5. Cliquez sur **Suivant** pour lire et effectuer le rendu du document Swagger.
6. Ajoutez tout paramètre nécessaire à l’appel HTTP.

	![Exécuter l’action HTTP](./media/connectors-native-http-swagger/using-action-2.png)

1. Cliquez sur **Enregistrer** dans le coin supérieur gauche de la barre d’outils, et votre application logique est à la fois enregistrée et publiée (activation).

### Héberger un document Swagger à partir d'Azure Storage

Vous pouvez faire référence à un document Swagger qui n’est pas hébergé ou qui ne répond pas aux exigences de sécurité et cross-origin pour le concepteur. Pour résoudre ce problème, vous pouvez stocker le document swagger dans Azure Storage et activer CORS pour référencer le document.

Voici les étapes pour créer, configurer et stocker des documents swagger dans Azure Storage :

1. [Créez un compte de stockage Azure avec un stockage d'objets blob Azure](../storage/storage-create-storage-account.md). (Pour cela, définissez les autorisations sur **Accès public**.)
2. Activez CORS sur l’objet blob. Vous pouvez utiliser [ce script PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) pour configurer ce paramètre automatiquement.
3. Téléchargez le fichier Swagger dans l’objet blob. Vous pouvez le faire via le [portail Azure](https://portal.azure.com) ou à partir d’un outil tel que [Azure Storage Explorer](http://storageexplorer.com/).
1. Référencez un lien HTTPS vers le document dans le stockage d'objets blob Azure. (Le lien suit le format `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`.)



## Détails techniques

Vous trouverez ci-dessous les détails des déclencheurs et des actions que ce connecteur HTTP + Swagger prend en charge.

## Déclencheurs HTTP + Swagger

Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs.](connectors-overview.md) Le connecteur HTTP + Swagger a un déclencheur.

|Déclencheur|Description|
|---|---|
|HTTP + Swagger|Exécuter un appel HTTP et obtenir le contenu de la réponse|

## Actions HTTP + Swagger

Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions.](connectors-overview.md) Le connecteur HTTP + Swagger a une action possible.

|Action|Description|
|---|---|
|HTTP + Swagger|Exécuter un appel HTTP et obtenir le contenu de la réponse|

### Détails de l’action

Le connecteur HTTP + Swagger est créé avec une action possible. Vous trouverez ci-dessous plus d’informations sur chacune des actions, leurs champs obligatoires et facultatifs, et les détails des résultats correspondants associés à leur utilisation.

#### HTTP + Swagger

Faites une demande HTTP sortante avec assistance des métadonnées Swagger. Un astérisque (*) signifie un champ obligatoire.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|Method (Méthode)*|statique|Verbe HTTP à utiliser.|
|URI*|URI|URI de la requête HTTP.|
|En-têtes|headers|Un objet JSON d’en-têtes HTTP à inclure.|
|Corps|body|Le texte de la requête HTTP.|
|Authentification|authentication|Authentification à utiliser pour la requête. [Pour plus d'informations, consultez HTTP](./connectors-native-http.md#authentication).|

**Détails des résultats**

Réponse HTTP

|Nom de la propriété|Type de données|Description|
|---|---|---|
|En-têtes|objet|En-têtes de réponse|
|Corps|objet|Objet Réponse|
|Code d’état|int|Code d'état HTTP|

### Réponses HTTP

Lorsque vous exécutez des appels de diverses actions, vous pouvez obtenir certaines réponses. La table ci-dessous indique les réponses correspondantes et leurs descriptions.

|Name|Description|
|---|---|
|200|OK|
|202|Acceptée|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|

---

## Étapes suivantes

Essayez la plateforme et [créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) dès maintenant. Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0810_2016-->