<properties
	pageTitle="Optimiser votre environnement avec la solution d’évaluation de SQL dans Log Analytics | Microsoft Azure"
	description="La solution d’évaluation de SQL permet d’évaluer les risques et l’intégrité de vos environnements de serveurs à intervalles réguliers."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Optimiser votre environnement avec la solution d’évaluation de SQL dans Log Analytics


La solution d’évaluation de SQL permet d’évaluer les risques et l’intégrité de vos environnements de serveurs à intervalles réguliers. Cet article vous aidera à installer la solution pour vous permettre de prendre les mesures correctives pour régler des problèmes potentiels.

Cette solution fournit une liste hiérarchisée de recommandations propres à votre infrastructure de serveurs déployée. Les recommandations sont classées en six domaines pour vous aider à rapidement mesurer les risques et prendre les mesures correctives appropriées.

Les recommandations proposées sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Chaque recommandation explique pourquoi un problème peut vous concerner et comment implémenter les modifications suggérées.

Vous pouvez choisir les domaines les plus importants pour votre organisation et suivre votre progression vers un environnement sans risque et intègre.

Une fois la solution ajoutée et l’évaluation terminée, le résumé des informations de domaines s'affiche sur le tableau de bord **Évaluation de SQL** de l'infrastructure de votre environnement. Les sections suivantes expliquent comment utiliser les informations du tableau de bord **Évaluation de SQL**, dans lequel vous pouvez afficher et exécuter les actions recommandées pour votre infrastructure de serveur SQL.

![image de la vignette d’évaluation de SQL](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![image du tableau de bord d’évaluation de SQL](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## Installation et configuration de la solution
L’évaluation de SQL est compatible avec toutes les versions actuellement prises en charge de SQL Server pour les éditions Standard, Developer et Enterprise.

Utilisez les informations suivantes pour installer et configurer la solution.

- La solution d'évaluation de SQL nécessite l’installation de .NET Framework 4 sur chaque ordinateur qui dispose d'un agent OMS.
- Lorsque vous utilisez l'agent Operations Manager avec SQL Assessment, vous devez utiliser un compte d’identification Operations Manager. Consultez la rubrique [Comptes d’identification Operations Manager pour OMS](#operations-manager-run-as-accounts-for-oms) ci-dessous pour plus d’informations.

    >[AZURE.NOTE] L'agent MMA ne prend pas en charge les comptes d’identification Operations Manager.

- Ajoutez la solution d'évaluation de SQL à votre espace de travail OMS en utilisant le processus décrit dans la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Aucune configuration supplémentaire n’est requise.

>[AZURE.NOTE] Une fois que vous avez ajouté la solution, le fichier AdvisorAssessment.exe est ajouté aux serveurs comportant des agents. Les données de configuration sont lues puis envoyées au service OMS dans le cloud pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données.

## Détails de l'évaluation SQL de la collection de données

Le tableau suivant présente les méthodes de collecte de données pour les agents, indique si Operations Manager (SCOM) est requis, et précise la fréquence à laquelle les données sont collectées par un agent.

| plateforme | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
|---|---|---|---|---|---|---|
|Windows|![Oui](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Oui](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Non](./media/log-analytics-sql-assessment/oms-bullet-red.png)|	![Non](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![Oui](./media/log-analytics-sql-assessment/oms-bullet-green.png)|	7 jours|

## Comptes d’identification Operations Manager pour OMS

Log Analytics dans OMS utilise le groupe de gestion et l’agent d’Operations Manager pour collecter et envoyer des données au service OMS. OMS utilise les packs d’administration pour les charges de travail afin de fournir des services à valeur ajoutée. Chaque charge de travail nécessite des privilèges spécifiques à la charge de travail pour exécuter les packs d’administration dans un contexte de sécurité différent, comme un compte de domaine. Vous devez fournir les informations d’identification en configurant un compte d’identification Operations Manager.

Utilisez les informations suivantes pour définir le compte d'identification Operations Manager pour l'évaluation de SQL.

### Définir le compte d’identification pour l’évaluation de SQL

 Si vous utilisez déjà le pack d’administration de SQL Server, vous devez utiliser ce compte d’identification.

#### Configuration du compte d’identification SQL dans la console Operations

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.

2. Sous **Configuration d'identification**, cliquez sur **Profils**, puis ouvrez **Profil d'identification de l'évaluation SQL OMS**.

3. Dans la page **Comptes d’identification**, cliquez sur **Ajouter**.

4. Sélectionnez un compte d'identification Windows qui contient les informations d'identification nécessaires pour SQL Server ou cliquez sur **Nouveau** pour en créer un.
	>[AZURE.NOTE] Le type de compte d’identification doit être Windows. Le compte d’identification doit également faire partie du groupe Administrateurs local sur tous les serveurs Windows hébergeant des instances de SQL Server.

5. Cliquez sur **Enregistrer**.

6. Modifiez puis exécutez l’exemple T-SQL suivant sur chaque instance de SQL Server afin d’accorder les autorisations minimales requises pour que le compte d’identification puisse effectuer l’évaluation de SQL. Toutefois, vous n’avez pas besoin de faire cela si un compte d’identification fait déjà partie du rôle de serveur sysadmin sur les instances de SQL Server.

```
	---
	    -- Replace <UserName> with the actual user name being used as Run As Account.
	    USE master
	
	    -- Create login for the user, comment this line if login is already created.
	    CREATE LOGIN [<UserName>] FROM WINDOWS
	
	    -- Grant permissions to user.
	    GRANT VIEW SERVER STATE TO [<UserName>]
	    GRANT VIEW ANY DEFINITION TO [<UserName>]
	    GRANT VIEW ANY DATABASE TO [<UserName>]
	
	    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
	    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
	    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### Configuration du compte d’identification SQL à l’aide de Windows Powershell

Ouvrez une fenêtre PowerShell et exécutez le script suivant après l’avoir mis à jour avec vos informations :

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## Hiérarchisation des recommandations

Une valeur de pondération déterminant l'importance relative de la recommandation est attribuée à chaque recommandation. Seules les dix recommandations les plus importantes sont affichées.

### Calcul des pondérations

Les pondérations sont des agrégations de valeurs basées sur trois facteurs clés :

- La *probabilité* qu'une anomalie identifiée cause des problèmes. Une plus grande probabilité attribue un score global supérieur à la recommandation.

- L'*impact* de l'anomalie sur votre organisation si elle devait causer des problèmes. Un plus grand impact attribue un score global supérieur à la recommandation.

- L'*effort* requis pour implémenter la recommandation. Un plus grand effort attribue un score global inférieur à la recommandation.

La pondération de chaque recommandation est exprimée en pourcentage du score total disponible pour chaque domaine. Par exemple, si une recommandation dans le domaine de la sécurité et de la conformité a un score de 5 %, l'implémentation de cette recommandation augmentera votre score global de sécurité et conformité de 5 %.

### Domaines

**Sécurité et conformité** : ce domaine présente les recommandations relatives aux menaces de sécurité potentielles et violations de stratégies d’entreprise, ainsi qu’aux exigences techniques, juridiques et réglementaires.

**Disponibilité et continuité opérationnelle** : ce domaine présente les recommandations relatives à la disponibilité du service, la résilience de votre infrastructure et la protection de votre activité.

**Performances et évolutivité** : ce domaine présente les recommandations pour aider votre organisation à développer son infrastructure informatique, s’assurer que votre environnement informatique répond aux besoins de performances actuels et est en mesure de répondre à l’évolution des besoins d’infrastructure.

**Mise à niveau, migration et déploiement** : ce domaine présente les recommandations pour vous aider à mettre à niveau, migrer et déployer SQL Server dans votre infrastructure existante.

**Opérations et surveillance** : ce domaine présente des recommandations relatives à la rationalisation de vos opérations informatiques, la mise en place une maintenance préventive et l’optimisation des performances.

**Gestion des modifications et configuration** : ce domaine présente des recommandations pour vous aider à protéger vos opérations quotidiennes, vous assurer que les modifications n’affectent pas votre infrastructure, établir des procédures de contrôle des modifications, et effectuer le suivi et l’audit des configurations système.

### Faut-il viser un score de 100 % dans chaque domaine ?

Pas nécessairement. Les recommandations sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Toutefois, chaque infrastructure de serveur étant différente, certaines recommandations peuvent être plus ou moins adaptées à votre système. Par exemple, il se peut que certaines recommandations de sécurité soient moins appropriées si vos ordinateurs virtuels ne sont pas connectés à Internet. Certaines recommandations de disponibilité peuvent être moins pertinentes pour les services qui fournissent des rapports et des données ad hoc de faible priorité. Les problèmes importants pour une entreprise bien établie peuvent l'être moins pour une start-up. Nous vous conseillons donc d'identifier tout d'abord vos domaines prioritaires, puis d'observer l'évolution de vos résultats au fil du temps.

Chaque recommandation inclut une justification de son importance. Servez-vous de cette explication pour évaluer si la mise en œuvre de la recommandation est importante pour vous, en fonction de la nature de vos services informatiques et des besoins de votre organisation.

## Utilisation des recommandations des domaines d'évaluation

Avant de pouvoir utiliser une solution d’évaluation dans OMS, vous devez avoir installé cette solution. Pour plus d’informations sur l’installation des solutions, consultez la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Une fois le pack installé, vous pouvez afficher un résumé des recommandations à l’aide de la vignette Évaluation SQL de la page Vue d’ensemble d’OMS.

Consultez le résumé des évaluations de conformité pour votre infrastructure, puis explorez les recommandations.

### Pour afficher les recommandations relatives à un domaine et prendre des mesures correctives

1. Sur la page **Vue d’ensemble**, cliquez sur la vignette **Évaluation SQL**.
2. Sur la page **Évaluation SQL**, passez en revue les informations de résumé dans l’un des panneaux relatifs à un domaine, puis cliquez sur l’un d’entre eux pour afficher les recommandations y afférentes.
3. Les pages relatives au domaine répertorient les recommandations prioritaires pour votre environnement. Cliquez sur une recommandation sous **Objets affectés** pour en afficher les détails et comprendre pourquoi elle apparaît. 
![image des recommandations d'évaluation de SQL](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. Vous pouvez effectuer les actions correctives suggérées dans **Mesures conseillées**. Une fois l'élément traité, les évaluations ultérieures indiqueront que des mesures ont été prises et votre score de conformité augmentera. Les éléments corrigés apparaissent comme **objets passés**.

## Ignorer les recommandations

Si vous souhaitez ignorer, des recommandations, vous pouvez créer un fichier texte qu’OMS utilisera pour empêcher les recommandations d'apparaître dans les résultats de votre évaluation.

### Pour identifier les recommandations que vous ignorerez

1.	Connectez-vous à votre espace de travail et ouvrez Recherche de journal. Utilisez la requête suivante pour répertorier les recommandations qui ont échoué pour les ordinateurs de votre environnement.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Voici une capture d’écran montrant la requête Recherche de journal :
     ![recommandations ayant échoué](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.	Choisissez les recommandations que vous souhaitez ignorer. Vous utiliserez les valeurs RecommendationId dans la procédure suivante.


### Pour créer et utiliser un fichier texte IgnoreRecommendations.txt

1.	Créez un fichier nommé IgnoreRecommendations.txt.
2.	Collez ou saisissez chaque valeur RecommendationId de chaque recommandation qu’OMS doit ignorer sur une ligne distincte, puis enregistrez et fermez le fichier.
3.	Placez le fichier dans le dossier suivant sur chaque ordinateur sur lequel OMS doit ignorer les recommandations.
    - Sur les ordinateurs avec Microsoft Monitoring Agent (connectés directement ou via Operations Manager) : *lecteur\_système*:\\Program Files\\Microsoft Monitoring Agent\\Agent
    - Sur le serveur d’administration Operations Manager : *lecteur\_système*:\\Program Files\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### Pour vérifier que les recommandations sont ignorées

1.	Une fois les prochaines évaluations planifiées exécutées, par défaut tous les 7 jours, les recommandations spécifiées sont marquées Ignored (ignorées) et n'apparaissent pas dans le tableau de bord d'évaluation.
2.	Vous pouvez utiliser les requêtes Recherche de journal suivantes pour répertorier toutes les recommandations ignorées.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.	Si vous décidez ultérieurement d’afficher les recommandations ignorées, supprimez tous les fichiers IgnoreRecommendations.txt, ou supprimez les valeurs RecommendationID de ces fichiers.

## FAQ sur les solutions d'évaluation de SQL

*Quelle est la fréquence d'exécution des évaluations ?*
- L'évaluation s'exécute tous les 7 jours.

*Est-il possible de configurer la fréquence d'exécution de l'évaluation ?*
- Pas pour l'instant.

*Si un autre serveur est découvert après l'ajout d'une solution d'évaluation de SQL, ce serveur sera-t-il évalué ?*
- Oui, une fois découverts, les nouveaux serveurs sont évalués tous les 7 jours.

*Si un serveur est désactivé, est-il retiré du processus d'évaluation ?*
- Si un serveur n'envoie pas de données pendant 3 semaines, il est supprimé.

*Quel est le nom du processus qui effectue la collecte de données ?*
- AdvisorAssessment.exe

*Combien de temps la collecte de données prend-elle ?*
- La collecte de données sur le serveur prend environ 1 heure. Cela peut prendre plus de temps sur les serveurs dotés d'un grand nombre d'instances SQL ou de bases de données.

*Quels types de données sont collectés ?*
- Les types de données suivants sont collectés :
    - WMI
    - Registre
    - Compteurs de performances
    - Vues de gestion dynamique (DMV) SQL

*Est-il possible de configurer les périodes de collecte de données ?*
- Pas pour l'instant.

*Pourquoi faut-il configurer un compte d'identification ?*
- Pour SQL Server, un petit nombre de requêtes SQL est exécuté. Pour permettre cette exécution, vous devez utiliser un compte d'identification avec les autorisations AFFICHER L'ÉTAT DU SERVEUR. En outre, pour les requêtes WMI, des informations d'identification d'administrateur local sont requises.

*Pourquoi afficher uniquement les 10 premières recommandations ?*
- Au lieu d'exploiter une liste de tâches trop importante, nous vous recommandons de vous concentrer sur les recommandations prioritaires. Une fois que vous les aurez suivies, de nouvelles recommandations apparaîtront. Si vous préférez afficher la liste détaillée des recommandations, utilisez la recherche de journal d'OMS.

*Est-il possible d'ignorer une recommandation ?*
- Oui, consultez la section [Ignorer les recommandations](#ignore-recommendations) ci-dessus.



## Étapes suivantes

- [Recherche des journaux](log-analytics-log-searches.md) pour afficher les données d’évaluation de SQL détaillées et des recommandations.

<!---HONumber=AcomDC_0504_2016-->