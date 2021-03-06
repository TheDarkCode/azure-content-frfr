<properties
	pageTitle="Organisation des ressources Azure à l’aide de balises | Microsoft Azure"
	description="Indique comment appliquer des balises afin d’organiser des ressources dédiées à la facturation et à la gestion."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="tomfitz"/>


# Organisation des ressources Azure à l'aide de balises

Resource Manager vous permet d'organiser logiquement les ressources en appliquant des balises. Les balises sont constituées de paires clé/valeur qui identifient les ressources avec les propriétés que vous définissez. Pour marquer des ressources comme appartenant à la même catégorie, appliquez la même balise à ces ressources.

Lorsque vous affichez des ressources avec une balise particulière, vous voyez les ressources de tous vos groupes de ressources. Vous n'êtes pas limité aux seules ressources d’un même groupe de ressources, ce qui vous permet d'organiser vos ressources de manière indépendante des relations de déploiement. Les balises peuvent être utiles si vous devez organiser les ressources à des fins de facturation ou de gestion.

Chaque balise que vous ajoutez à une ressource ou à un groupe de ressources est automatiquement ajoutée à la classification sur l'ensemble de l'abonnement. Vous pouvez également préremplir cette taxinomie pour votre abonnement en saisissant des noms et des valeurs de balises que vous souhaitez utiliser pour le balisage de vos ressources.

Chaque ressource ou groupe de ressources peut inclure un maximum de 15 balises. Le nom de balise est limité à 512 caractères, et la valeur de balise à 256 caractères.

> [AZURE.NOTE] Vous ne pouvez appliquer des balises qu’à des ressources qui prennent en charge les opérations de Resource Manager. Si vous avez créé une machine virtuelle, un réseau virtuel ou un stockage par le biais du modèle de déploiement classique (tel que via le portail Azure classique), vous ne pouvez pas appliquer de balise à cette ressource. Pour prendre en charge le balisage, redéployez ces ressources via Resource Manager. Toutes les autres ressources prennent en charge le balisage.

## Modèles

Pour marquer une ressource au cours du déploiement, ajoutez simplement l’élément **tags** à la ressource que vous déployez et indiquez le nom et la valeur. Le nom et la valeur de la balise n’ont pas besoin d’exister préalablement dans votre abonnement. Vous pouvez fournir 15 balises au maximum pour chaque ressource.

L’exemple suivant illustre un compte de stockage avec une balise.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

Actuellement, Resource Manager ne prend pas en charge le traitement d’un objet pour les valeurs et les noms de balise. Au lieu de cela, passez un objet pour les valeurs de balise, mais vous devez toujours spécifier les noms de balise, comme indiqué dans l'exemple suivant.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## Portail

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## Interface de ligne de commande Azure

Les balises se trouvent directement dans les ressources et les groupes de ressources. Pour afficher les balises existantes, il suffit de récupérer un groupe de ressources et ses ressources avec **azure group show**.

    azure group show -n tag-demo-group
    
Ce qui renvoie des métadonnées sur le groupe de ressources, y compris toutes les balises appliquées.
    
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/{guid}/resourceGroups/tag-demo-group
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production
    data:    Resources:
    data:
    data:      Id      : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    data:      Name    : tfsqlserver
    data:      Type    : servers
    data:      Location: eastus2
    data:      Tags    : Dept=Finance;Environment=Production
    ...

Pour obtenir les balises uniquement pour le groupe de ressources, servez-vous d’un utilitaire JSON comme [jq](http://stedolan.github.io/jq/download/).

    azure group show -n tag-demo-group --json | jq ".tags"
    
Ce qui renvoie les balises pour ce groupe de ressources.
    
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

Vous affichez les balises d’une ressource spécifique avec **azure resource show**.

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    
Ce qui renvoie les balises pour cette ressource.
    
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
L’exemple suivant montre comment récupérer toutes les ressources qui ont un nom de balise et une valeur.

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    
Ce qui renvoie les noms des ressources avec la même balise.
    
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

Les balises sont mises à jour en tant qu'ensemble. Pour ajouter une balise à une ressource qui comporte des balises existantes, récupérez toutes les balises existantes que vous souhaitez conserver. Pour définir la valeur des balises associées à un groupe de ressources, utilisez **azure group set** et indiquez toutes les balises du groupe de ressources.

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    
Un résumé du groupe de ressources avec les nouvelles balises est renvoyé.
    
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
Vous pouvez répertorier les balises existantes dans votre abonnement avec **azure tag list** et ajouter une balise avec **azure tag create**. Pour supprimer une balise de la classification de votre abonnement, commencez par supprimer la balise de toutes les ressources. Supprimez ensuite la balise avec **azure tag delete**.

## API REST

Le portail et PowerShell utilisent tous deux l'[API REST du Gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn848368.aspx) en arrière-plan. Si vous avez besoin intégrer le balisage dans un autre environnement, vous pouvez récupérer des balises avec une commande GET sur l'ID de ressource et mettre à jour l'ensemble des balises avec un appel PATCH.


## Balises et facturation

Dans le cas des services pris en charge, vous pouvez utiliser des balises pour regrouper vos données de facturation. Par exemple, les [machines virtuelles intégrées à Azure Resource Manager](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) vous permettent de définir et d’appliquer des balises pour organiser l’utilisation de la facturation pour les machines virtuelles. Si vous exécutez plusieurs machines virtuelles pour différentes organisations, vous pouvez recourir aux balises pour regrouper l’utilisation par centre de coûts. Vous pouvez également utiliser des balises pour catégoriser les coûts par environnement d’exécution ; par exemple, l’utilisation de la facturation pour les machines virtuelles en cours d’exécution dans l’environnement de production.

Vous pouvez récupérer des informations sur les balises par le biais des [API Resource Usage et RateCard](billing-usage-rate-card-overview.md) ou du fichier de valeurs séparées par des virgules (CSV). Vous téléchargez le fichier d’utilisation à partir du [portail de comptes Azure](https://account.windowsazure.com/) ou du [portail EA](https://ea.azure.com). Pour plus d’informations sur l’accès par programme aux informations de facturation, consultez [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](billing-usage-rate-card-overview.md). Pour plus d’informations sur les opérations de l’API REST, consultez [Informations de référence sur l’API REST Azure Billing](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Lorsque vous téléchargez le fichier CSV d’utilisation pour les services qui prennent en charge les balises avec la facturation, les balises s’affichent dans la colonne **Balises**. Pour plus d’informations, voir [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md).

![Voir les balises dans la facturation](./media/resource-group-using-tags/billing_csv.png)

## Étapes suivantes

- Vous pouvez appliquer des restrictions et des conventions sur votre abonnement avec des stratégies personnalisées. La stratégie que vous définissez peut exiger la définition d'une balise spécifique pour toutes les ressources. Pour plus d'informations, consultez [Utiliser le service Policy pour gérer les ressources et contrôler l'accès](resource-manager-policy.md).
- Pour plus d’informations sur l’utilisation d’Azure PowerShell lors du déploiement de ressources, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](./powershell-azure-resource-manager.md).
- Si vous n’avez jamais utilisé l’interface de ligne de commande Azure pour le déploiement de ressources, consultez [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](./xplat-cli-azure-resource-manager.md).
- Pour plus d’informations sur l’utilisation du portail, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](./azure-portal/resource-group-portal.md).

<!---HONumber=AcomDC_0817_2016-->