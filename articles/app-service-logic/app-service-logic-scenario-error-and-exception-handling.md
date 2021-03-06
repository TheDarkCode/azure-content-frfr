<properties
    pageTitle="Journalisation et gestion des erreurs dans Logic Apps | Microsoft Azure"
    description="Afficher un cas d’utilisation réel des fonctionnalités avancées de journalisation et de gestion des erreurs avec Logic Apps"
    keywords=""
    services="logic-apps"
    authors="hedidin"
    manager=""
    editor=""
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="b-hoedid"/>

# Journalisation et gestion des erreurs dans Logic Apps

Cet article décrit comment vous pouvez étendre une application logique pour assurer une meilleure prise en charge de la gestion des exceptions. Il s’agit d’un cas d’utilisation réel, et cet article répond à la question « Logic Apps prend-elle en charge la gestion des exceptions et des erreurs ? ».

>[AZURE.NOTE] La version actuelle de la fonctionnalité Logic Apps de Microsoft Azure App Service fournit un modèle standard pour les réponses d’action. Cela inclut les réponses de type validation interne et de type erreur retournées depuis une application API.

## Vue d’ensemble du cas d’utilisation et du scénario

Voici le cas d’utilisation sur lequel porte cet article. Un fameux organisme de santé nous a engagés pour développer une solution Azure afin de mettre en place un portail pour les patients à l’aide de Microsoft Dynamics CRM Online. L’organisme avait besoin d’envoyer des enregistrements de rendez-vous entre le portail patient Dynamics CRM Online et Salesforce. Il nous a été demandé d’utiliser la norme [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) pour tous les dossiers des patients.

Le projet comportait deux exigences principales :

 -  Une méthode pour journaliser les enregistrements envoyés à partir du portail Dynamics CRM Online.
 -  La possibilité de visualiser les erreurs susceptibles de se produire dans le flux de travail.


## Comment nous avons résolu le problème

>[AZURE.TIP] Vous pouvez visionner une vidéo à propos de ce projet sur le site [Integration User Group](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Groupe d’utilisateurs d’intégration").

Nous avons choisi [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "Document DB Azure") comme référentiel pour les enregistrements de journal et d’erreur (DocumentDB fait référence aux enregistrements en tant que documents). Comme Logic Apps dispose d’un modèle standard pour toutes les réponses, nous n’avons pas à créer un schéma personnalisé. Nous pouvons créer une application API pour **insérer** et **interroger** les enregistrements d’erreur et de journal. Nous pouvons également définir un schéma pour chaque enregistrement au sein de l’application API.

Une autre exigence consistait à vider les enregistrements au-delà d’une certaine date. DocumentDB possède une propriété [Durée de vie](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Durée de vie") (TTL, Time To Live), qui nous a permis de définir une valeur **Durée de vie** pour chaque enregistrement ou pour toute une collection. Ainsi, nous n’avons plus à supprimer manuellement les enregistrements dans DocumentDB.

### Création de l’application logique

La première étape consiste à créer l’application logique et à la charger dans le concepteur. Dans cet exemple, nous utilisons des applications logiques parent-enfant. Supposons que nous avons déjà créé le parent et que nous allons créer une application logique enfant.

Étant donné que nous allons journaliser les enregistrements provenant de Dynamics CRM Online, nous allons commencer par le haut. Nous devons utiliser un déclencheur de requête, car l’application logique parente déclenche cet enfant.

> [AZURE.IMPORTANT] Pour suivre ce didacticiel, vous devez créer une base de données DocumentDB et deux collections (Journalisation et Erreurs).

### Déclencheur d’application logique

Nous utilisons un déclencheur de requête comme indiqué dans l’exemple ci-dessous.

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### Étapes

Nous devons journaliser la source (requête) du dossier du patient à partir du portail Dynamics CRM Online.

1. Nous devons d’abord obtenir un nouvel enregistrement de rendez-vous de Dynamics CRM Online. Le déclencheur provenant de CRM nous fournit les paramètres **ID de patient CRM**, **Type d’enregistrement**, **Enregistrement nouveau ou mis à jour** (valeur booléenne nouvelle ou mise à jour) et **ID Salesforce**. **L’ID Salesforce** peut être défini sur la valeur Null, car il est utilisé uniquement pour une mise à jour. Nous allons obtenir l’enregistrement CRM à l’aide des paramètres CRM, **ID de patient** et **Type d’enregistrement**.
1. Nous devons ensuite ajouter l’opération **InsertLogEntry** de notre application API DocumentDB, comme indiqué sur les figures suivantes.


#### Vue du concepteur Insérer une entrée de journal

![Insérer une entrée de journal](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### Vue du concepteur Insérer une entrée d’erreur
![Insérer une entrée de journal](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### Recherche d’échec de création d’enregistrement

![Condition](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)


## Code source d’application logique

>[AZURE.NOTE]  Les extraits de code ci-dessous correspondent uniquement à des exemples. Comme le didacticiel est basé sur une implémentation actuellement en production, la valeur d’un **Nœud source** peut ne pas afficher les propriétés qui sont liées à la planification d’un rendez-vous.

### Journalisation
L’exemple de code d’application logique suivant indique comment gérer la journalisation.

#### Entrée de journal
Il s’agit du code source de l’application logique permettant d’insérer une entrée de journal.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### Consigner une requête

Il s’agit du message de journalisation de requête publié dans l’application API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
	    "date": "Fri, 10 Jun 2016 22:31:56 GMT",
	    "operation": "New Patient",
	    "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "providerId": "",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}"
    	}
    }

```


#### Consigner une réponse

Il s’agit du message de journalisation de réponse provenant de l’application API.

``` json
{
    "statusCode": 200,
    "headers": {
	    "Pragma": "no-cache",
	    "Cache-Control": "no-cache",
	    "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
	    "Server": "Microsoft-IIS/8.0",
	    "X-AspNet-Version": "4.0.30319",
	    "X-Powered-By": "ASP.NET",
	    "Content-Length": "964",
	    "Content-Type": "application/json; charset=utf-8",
	    "Expires": "-1"
    },
    "body": {
	    "ttl": 2592000,
	    "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
	    "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
	    "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
	    "_ts": 1465597936,
	    "_etag": ""0400fc2f-0000-0000-0000-575b3ff00000"",
	    "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "timestamp": "2016-06-10T22:31:56Z",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}",
	    "operation": "New Patient",
	    "salesforceId": "",
	    "expired": false
    }
}

```

Nous allons maintenant examiner les étapes de gestion des erreurs.


### Gestion des erreurs

L’exemple de code d’application logique suivant indique comment vous pouvez implémenter la gestion des erreurs.

#### Créer un enregistrement d’erreur

Il s’agit du code source de l’application logique permettant de créer un enregistrement d’erreur.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}  	       
```

#### Erreur d’insertion dans DocumentDB--Requête

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MasterID_mp__c":"","C_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","ONY_ID__c":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "statusCode": "400"
    }
}
```

#### Erreur d’insertion dans DocumentDB--Réponse


``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": ""0c00eaac-0000-0000-0000-575b3fdc0000"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c":"DO - Degree level is DO","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MterID_mp__c":"","Medicis_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","XXXXXXX":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### Réponse à l’erreur de Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### Renvoi de la réponse à l’application logique parente

Lorsque vous disposez de la réponse, vous pouvez la transmettre à l’application logique parente.

#### Retourner une réponse positive à l’application logique parente

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "	Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### Retourner une réponse d’erreur à l’application logique parente

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## Référentiel et portail DocumentDB

Notre solution a permis d’ajouter des fonctionnalités avec [DocumentDB](https://azure.microsoft.com/services/documentdb).

### Portail de gestion des erreurs

Pour afficher les erreurs, vous pouvez créer une application web MVC afin d’afficher les enregistrements d’erreur à partir de DocumentDB. Les opérations **Liste**, **Détails**, **Modifier** et **Supprimer** sont incluses dans la version actuelle.

> [AZURE.NOTE] Opération Modifier : DocumentDB effectue un remplacement de l’ensemble du document. Les enregistrements affichés dans les vues **Liste** et **Détail** représentent uniquement des exemples. Il ne s’agit pas d’enregistrements de rendez-vous de patients réels.

Voici quelques exemples des détails de notre application MVC créée à l’aide de l’approche décrite précédemment.

#### Liste de gestion des erreurs

![Liste d'erreurs](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)

#### Vue détaillée de gestion des erreurs

![Détails de l’erreur](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### Portail de gestion des journaux

Pour afficher les journaux, nous avons également créé une application web MVC. Voici quelques exemples des détails de notre application MVC créée à l’aide de l’approche décrite précédemment.

#### Exemple de vue détaillée de journal

![Vue détaillée de journal](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)

### Détails de l’application API

#### API de gestion des exceptions Logic Apps

Notre application API de gestion des exceptions Logic Apps en open source fournit les fonctionnalités ci-dessous.

Elle compte deux contrôleurs :

- **ErrorController** insère un enregistrement d’erreur (document) dans une collection DocumentDB.
- **LogController** insère un enregistrement de journal (document) dans une collection DocumentDB.

> [AZURE.TIP] Les deux contrôleurs utilisent des opérations `async Task<dynamic>`. Les opérations peuvent ainsi être résolues lors de l’exécution. Nous pouvons donc créer le schéma DocumentDB dans le corps de l’opération.

Chaque document de DocumentDB doit posséder un ID unique. Nous utilisons le paramètre `PatientId` et ajoutons un horodatage qui est converti en valeur d’horodatage Unix (double). Nous la tronquons pour supprimer la valeur fractionnaire.

Vous pouvez afficher le code source de notre API de contrôleur d’erreurs [dans GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Nous appelons l’API à partir d’une application logique à l’aide de la syntaxe suivante.

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

L’expression de l’exemple de code ci-dessus vérifie que l’état de l’enregistrement *Create\_NewPatientRecord* est défini sur **Failed**.

## Résumé

- Vous pouvez facilement implémenter la journalisation et la gestion des erreurs dans une application logique.
- Vous pouvez utiliser DocumentDB comme référentiel pour les enregistrements de journal et d’erreur (documents).
- Vous pouvez utiliser MVC pour créer un portail afin d’afficher les enregistrements de journal et d’erreur.

### Code source
Le code source de l’application API de gestion des exceptions Logic Apps est disponible dans ce [référentiel GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de gestion des exceptions Logic Apps").


## Étapes suivantes
- [View more Logic Apps examples and scenarios (Afficher d’autres exemples et scénarios Logic Apps)](app-service-logic-examples-and-scenarios.md)
- [Learn about Logic Apps monitoring tools (En savoir plus sur les outils de surveillance des applications logiques)](app-service-logic-monitor-your-logic-apps.md)
- [Création d’un modèle de déploiement d’applications logiques](app-service-logic-create-deploy-template.md)

<!---HONumber=AcomDC_0817_2016-->