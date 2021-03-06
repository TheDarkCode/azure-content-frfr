<properties 
   pageTitle="Créer un accord de partenariat commercial dans Azure App Service | Microsoft Azure" 
   description="Créer des accords de partenariat commercial" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
	ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/23/2016"
   ms.author="rajram"/>

# Création d'un accord de partenariat commercial   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Les partenaires commerciaux sont des entités impliquées dans des communications B2B (entreprise à entreprise). Lorsque deux partenaires établissent une relation, il est question d’un *accord*. L’accord défini est basé sur le type de communication dont les deux partenaires souhaitent bénéficier. Il est propre au protocole ou au transport. Les différents protocoles et transports B2B pris en charge par Azure App Service sont les suivants :

- AS2 (Applicability Statement 2)
- EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport (UN/EDIFACT))
- X12 (ASC X12)

### Applications API BizTalk qui prennent en charge les scénarios B2B
Les applications API suivantes activent ces fonctionnalités à l'aide d'une expérience enrichie et intuitive dans le portail Azure :


## Gestion des partenaires commerciaux BizTalk
- Création et gestion de partenaires, de profils et d’identités
- Stockage et gestion de schémas EDI
- Stockage et gestion de certificats (utilisés dans le protocole AS2)
- Création et gestion d'accords AS2
- Création et gestion d'accords EDIFACT (comprend le traitement par lots côté envoi)
- Création et gestion d'accords X12 (comprend le traitement par lots côté envoi)

![][1]


## Connecteur AS2
- Exécute les accords AS2 tel que défini dans l'instance d'application API de Gestion des partenaires commerciaux connexe
- Expose les informations de traitement/suivi AS2 pour le dépannage


## BizTalk EDIFACT
- Exécute les accords EDIFACT tel que défini dans l'instance d'application API de Gestion des partenaires commerciaux connexe
- Expose les informations de traitement/suivi EDIFACT pour le dépannage
- Fournit la gestion de l'état des lots (démarrage et arrêt) tel que défini dans les accords EDIFACT dans l'instance d'application API de Gestion des partenaires commerciaux connexe


## BizTalk X12
- Exécute les accords X12 tel que défini dans l'instance d'application API de Gestion des partenaires commerciaux connexe
- Expose les informations de traitement/suivi X12 pour le dépannage
- Fournit la gestion de l'état des lots (démarrage et arrêt) tel que défini dans les accords X12 dans l'instance d'application API de Gestion des partenaires commerciaux connexe

Comme indiqué précédemment, les applications API AS2, X12 et EDIFACT nécessitent une application API de Gestion des partenaires commerciaux pour fonctionner comme prévu.


## Mise en route
Pour créer des accords de partenariat commercial

1. Créez une instance du connecteur **Gestion des partenaires commerciaux BizTalk**. Cela nécessite une base de données SQL vide. Avant de commencer, veillez à disposer d'une base de données vide et prête à l'emploi.
2. Téléchargez les schémas et les certificats requis par les accords. Pour ce faire, parcourez l’instance de Gestion des partenaires commerciaux créée et parcourez pas à pas la partie « Schémas » et/ou « Certificats ».
3. Accédez à l’instance de Gestion des partenaires commerciaux créée et parcourez pas à pas la partie **Partenaires**.
4. Créez des partenaires selon les besoins. Modifiez également les profils selon les besoins et ajoutez les identités nécessaires.
5. Maintenant, utilisez la partie **Accords** pour créer des accords. Quand vous créez un accord, vous devez sélectionner le protocole à utiliser. Les options de configuration restantes sont basées sur le protocole que vous avez sélectionné.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 

<!---HONumber=AcomDC_0824_2016-->