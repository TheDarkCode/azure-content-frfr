<properties 
	pageTitle="mettre à jour Media Services après la substitution de clés d’accès de stockage" 
	description="Cet article vous donne des conseils sur la mise à jour de Media Services après la substitution de clés d’accès de stockage." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako"
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2016" 
	ms.author="milangada;cenkdin;juliako"/>

#Procédure : mettre à jour Media Services après la substitution de clés d’accès de stockage

Lorsque vous créez un compte Azure Media Services, vous êtes également invité à sélectionner un compte de stockage Azure qui est utilisé pour stocker votre contenu multimédia. Notez que vous pouvez [ajouter plusieurs comptes de stockage](meda-services-managing-multiple-storage-accounts.md) à votre compte Media Services.

Lors de la création d’un compte de stockage, Azure génère deux clés d’accès de stockage 512 bits, qui sont utilisées pour authentifier l’accès à votre compte de stockage. Pour sécuriser vos connexions de stockage, il est recommandé de régénérer et d’alterner périodiquement vos clés d’accès de stockage. Vous bénéficiez de deux clés d’accès (primaire et secondaire), ce qui vous permet de conserver vos connexions au compte de stockage à l’aide d’une clé d’accès lorsque vous régénérez l’autre clé. Cette procédure est également appelée « substitution des clés d’accès ».

Media Services dépend d'une clé de stockage qui lui est fournie. Plus précisément, les localisateurs utilisés pour diffuser en continu ou télécharger vos ressources dépendent de la clé d’accès de stockage spécifiée. Lors de la création d'un compte AMS, Media Services choisit une dépendance sur la clé d'accès de stockage principale par défaut, mais en tant qu'utilisateur, vous pouvez mettre à jour la clé de stockage d’AMS. Vous devez indiquer à Media Services quelle clé utiliser en suivant les étapes décrites dans cette rubrique. De même, lors de la substitution des clés d’accès de stockage, vous devez également veiller à mettre à jour vos localisateurs pour éviter toute interruption de votre service de diffusion en continu (cette étape est également décrite dans la rubrique).

>[AZURE.NOTE]Si vous possédez plusieurs comptes de stockage, vous devez effectuer cette procédure pour chacun d’eux.
>
>Avant d’appliquer les étapes décrites dans cette rubrique sur un compte de production, veillez à les tester sur un compte de pré-production.


## Étape 1 : régénérer la clé d’accès de stockage secondaire

Commencez par régénérer la clé de stockage secondaire. Par défaut, la clé secondaire n’est pas utilisée par Media Services. Pour savoir comment restaurer les clés de stockage, consultez la section [Affichage, copie et régénération de clés d’accès de stockage](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
##<a id="step2"></a>Étape 2 : mettre à jour Media Services pour utiliser la nouvelle clé de stockage secondaire

Mettez à jour Media Services pour utiliser la clé d’accès de stockage secondaire. Vous pouvez utiliser l’une des deux méthodes suivantes pour synchroniser la clé de stockage régénérée avec Media Services.

- Utilisez le portail Azure Classic : sélectionnez votre compte Media Service, puis cliquez sur l’icône « GÉRER LES CLÉS » en bas de la fenêtre du portail. Selon la clé de stockage que vous souhaitez synchroniser avec Media Services, sélectionnez le bouton de synchronisation de la clé primaire ou secondaire. Dans le cas présent, utilisez la clé secondaire.

- Utilisez l’API REST de gestion de Media Services.

L’exemple de code suivant montre comment construire la demande https://endpoint/*subscriptionId*/services/mediaservices/Accounts/*accountName*/StorageAccounts/*storageAccountName*/Key afin de synchroniser la clé de stockage spécifiée avec Media Services. Dans le cas présent, la valeur de la clé de stockage secondaire est utilisée. Pour plus d’informations, consultez la page [Procédure : utiliser l’API REST de gestion de Media Services](http://msdn.microsoft.com/library/azure/dn167656.aspx).
 
	public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
	{
	    var clientCert = GetCertificate(CertThumbprint);
	
	    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
	        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
	    request.Method = "PUT";
	    request.ContentType = "application/json; charset=utf-8";
	    request.Headers.Add("x-ms-version", "2011-10-01");
	    request.Headers.Add("Accept-Encoding: gzip, deflate");
	    request.ClientCertificates.Add(clientCert);
	
	
	    using (var streamWriter = new StreamWriter(request.GetRequestStream()))
	    {
	        streamWriter.Write(""");
	        streamWriter.Write(storageAccountKey);
	        streamWriter.Write(""");
	        streamWriter.Flush();
	    }
	
	    using (var response = (HttpWebResponse)request.GetResponse())
	    {
	        string jsonResponse;
	        Stream receiveStream = response.GetResponseStream();
	        Encoding encode = Encoding.GetEncoding("utf-8");
	        if (receiveStream != null)
	        {
	            var readStream = new StreamReader(receiveStream, encode);
	            jsonResponse = readStream.ReadToEnd();
	        }
	    }
	}

Après cette étape, mettez à jour les localisateurs existants (qui font l’objet d’une dépendance par rapport à l’ancienne clé de stockage), comme indiqué dans l’étape suivante.

>[AZURE.NOTE]Patientez 30 minutes avant d’effectuer des opérations avec Media Services (par exemple, avant de créer des localisateurs) afin d’éviter tout impact sur les tâches en cours.

##Étape 3 : mettre à jour les localisateurs 

>[AZURE.NOTE]Lors de la substitution des clés d’accès de stockage, vous devez veiller à mettre à jour vos localisateurs existants pour éviter toute interruption de votre service de diffusion en continu.

Attendez au moins 30 minutes après la synchronisation de la nouvelle clé de stockage avec AMS. Vous pouvez ensuite recréer vos localisateurs OnDemand afin qu’ils adoptent la dépendance par rapport à la nouvelle clé de stockage spécifiée tout en conservant l’URL existante.

Notez que lorsque vous mettez à jour (ou que vous recréez) un localisateur SAS, l’URL sera toujours modifiée.

>[AZURE.NOTE] Pour vous assurer de conserver les URL existantes de vos localisateurs OnDemand, vous devez supprimer le localisateur existant et en créer un avec le même ID.
 
L’exemple .NET suivant montre comment recréer un localisateur avec le même ID.
	
	private static ILocator RecreateLocator(CloudMediaContext context, ILocator locator)
	{
	    // Save properties of existing locator.
	    var asset = locator.Asset;
	    var accessPolicy = locator.AccessPolicy;
	    var locatorId = locator.Id;
	    var startDate = locator.StartTime;
	    var locatorType = locator.Type;
	    var locatorName = locator.Name;
	
	    // Delete old locator.
	    locator.Delete();
	
	    if (locator.ExpirationDateTime <= DateTime.UtcNow)
	    {
	        throw new Exception(String.Format(
	            "Cannot recreate locator Id={0} because its locator expiration time is in the past",
	            locator.Id));
	    }
	
	    // Create new locator using saved properties.
	    var newLocator = context.Locators.CreateLocator(
	        locatorId,
	        locatorType,
	        asset,
	        accessPolicy,
	        startDate,
	        locatorName);
	
	
	
	    return newLocator;
	}


##Étape 5 : régénérer la clé d’accès de stockage primaire

Régénérez la clé d’accès de stockage primaire. Pour savoir comment restaurer les clés de stockage, consultez la section [Affichage, copie et régénération de clés d’accès de stockage](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

##Étape 6 : mettre à jour Media Services pour utiliser la nouvelle clé de stockage primaire
	
Reprenez l’[étape 2](media-services-roll-storage-access-keys.md#step2) en synchronisant la nouvelle clé d’accès de stockage primaire avec le compte Media Services.

>[AZURE.NOTE]Patientez 30 minutes avant d’effectuer des opérations avec Media Services (par exemple, avant de créer des localisateurs) afin d’éviter tout impact sur les tâches en cours.

##Étape 7 : mettre à jour les localisateurs  

Au bout de 30 minutes, vous pouvez recréer vos localisateurs OnDemand afin qu’ils adoptent la dépendance par rapport à la nouvelle clé de stockage principale et qu’ils conservent l’URL existante.

Utilisez la même procédure que celle décrite dans l’[étape 3](media-services-roll-storage-access-keys.md#step-3-update-locators).


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###Remerciements 

Nous aimerions remercier les personnes suivantes qui ont contribué à la création de ce document : Cenk Dingiloglu, Milan Gada, Seva Titov.

<!---HONumber=AcomDC_0629_2016-->