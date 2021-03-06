<properties
   pageTitle="Migrer vers une base de données SQL à l’aide de la réplication transactionnelle | Microsoft Azure"
   description="Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, réplication transactionnelle"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/23/2016"
   ms.author="carlrab"/>

# Migrer une base de données SQL Server vers une Base de données SQL Azure à l’aide de la réplication transactionnelle

> [AZURE.SELECTOR]
- [Assistant Migration SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exporter vers un fichier BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importer depuis un fichier BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Réplication transactionnelle](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Dans cet article, vous apprendrez à migrer une base de données SQL Server compatible vers une Base de données SQL Azure avec un temps d’arrêt minimal grâce à la réplication transactionnelle de SQL Server.

## Explication de l’architecture de la réplication transactionnelle

Quand vous ne pouvez pas vous permettre de sortir votre base de données SQL Server de la production pendant la migration, vous pouvez utiliser la réplication transactionnelle SQL Server comme solution de migration. Pour utiliser cette solution, vous configurez votre Base de données SQL Azure en tant qu’abonné à l’instance de SQL Server locale que vous souhaitez migrer. Le distributeur de réplication transactionnelle locale synchronise les données nécessaires de la base de données locale (l’éditeur) alors que de nouvelles transactions continuent d’avoir lieu.

Vous pouvez également utiliser la réplication transactionnelle pour migrer un sous-ensemble de votre base de données locale. La publication que vous répliquez vers une base de données SQL Azure peut être limitée à un sous-ensemble des tables dans la base de données en cours de réplication. Pour chaque table répliquée, vous pouvez limiter les données à un sous-ensemble de lignes et/ou un sous-ensemble de colonnes.

Avec la réplication transactionnelle, toutes les modifications apportées à vos données ou à votre schéma apparaissent dans votre Base de données SQL Azure. Une fois la synchronisation terminée, lorsque vous êtes prêt à migrer, modifiez la chaîne de connexion de vos applications de façon à ce qu’elle pointe vers votre Base de données SQL Azure. Une fois que la réplication transactionnelle a vidé toutes les modifications restant sur votre base de données locale et que toutes vos applications pointent vers Base de données Azure, vous pouvez désinstaller la réplication transactionnelle. Votre Base de données SQL Azure est maintenant votre système de production.

 ![Diagramme SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## Conditions requises de la réplication transactionnelle

La réplication transactionnelle est une technologie intégrée à SQL Server depuis SQL Server 6.5. Il s’agit d’une technologie mature et éprouvée que la plupart des administrateurs de bases de données maîtrisent parfaitement. Avec [SQL Server 2016](https://www.microsoft.com/fr-FR/cloud-platform/sql-server), vous pouvez désormais configurer votre Base de données SQL Azure en tant qu’[abonnée de réplication transactionnelle](https://msdn.microsoft.com/library/mt589530.aspx) à votre publication locale. L’expérience de configuration dans Management Studio est identique à la configuration d’un abonné de réplication transactionnelle sur un serveur local. Ce scénario est pris en charge quand le serveur de publication et le serveur de distribution sont au moins de l’une des versions de SQL Server suivantes :

 - SQL Server 2016 et versions ultérieures
 - SQL Server 2014 SP1 CU3 et versions ultérieures
 - SQL Server 2014 RTM CU10 et versions ultérieures
 - SQL Server 2012 SP2 CU8 et versions ultérieures
 - SQL Server 2012 SP3 et versions ultérieures


> [AZURE.IMPORTANT] Utilisez la dernière version de SQL Server Management Studio pour rester synchronisé avec les mises à jour de Microsoft Azure et de Base de données SQL. Les versions antérieures de SQL Server Management Studio ne peuvent pas configurer Base de données SQL en tant qu’abonné. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## Étapes suivantes

- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/fr-FR/cloud-platform/sql-server)

## Ressources supplémentaires

- [Réplication transactionnelle](https://msdn.microsoft.com/library/mt589530.aspx)
- [Base de données SQL V12](sql-database-v12-whats-new.md)
- [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
- [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->