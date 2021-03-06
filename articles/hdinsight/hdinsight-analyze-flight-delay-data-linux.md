<properties 
	pageTitle="Analyse des données sur les retards de vol avec Hive dans HDInsight basé sur Linux | Microsoft Azure" 
	description="Découvrez comment utiliser Hive pour analyser les données sur les retards de vol à l’aide de HDInsight basé sur Linux, puis exporter les données vers une base de données SQL à l’aide de Sqoop." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/25/2016" 
	ms.author="larryfr"/>

#Analyse des données sur les retards de vol avec Hive dans HDInsight

Découvrez comment analyser les données sur les retards de vol à l'aide de Hive sur HDInsight Linux, puis exporter les données vers Azure SQL Database à l'aide de Sqoop.

> [AZURE.NOTE] Bien que des éléments individuels de ce document puissent être utilisés avec des clusters HDInsight Windows (Python et Hive, par exemple), plusieurs étapes sont spécifiques aux clusters Linux. Pour les étapes fonctionnant avec un cluster Windows, consultez la rubrique [Analyse des données sur les retards de vol avec Hive dans HDInsight](hdinsight-analyze-flight-delay-data.md)

###Composants requis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Un cluster HDInsight__. Consultez la rubrique [Prise en main de Hadoop avec Hive dans HDInsight sur Linux](hdinsight-hadoop-linux-tutorial-get-started.md) pour connaître les étapes de création d’un cluster HDInsight Linux.

- __Base de données SQL Azure__. Vous allez utiliser une base de données SQL Azure comme magasin de données cible. Si vous n'avez pas déjà une base de données SQL, consultez [Didacticiel sur la base de données SQL : Créer une base de données SQL en quelques minutes](../sql-database/sql-database-get-started.md).

- __Interface de ligne de commande Azure__. Si vous n'avez pas installé l'interface de ligne de commande Azure, consultez la rubrique [Installation et configuration de l'interface de ligne de commande Azure](../xplat-cli-install.md) pour connaître la procédure.

	[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]


##Téléchargement des données de vol

1. Accédez à [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].
2. Sur la page, sélectionnez les valeurs suivantes :

    | Nom | Valeur |
    | ---- | ---- |
    | Filtre année | 2013 |
    | Filtre période | Janvier |
    | Champs | Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Effacez tous les autres champs |

3. Cliquez sur **Télécharger**.

##Téléchargement des données

1. Utilisez la commande suivante pour télécharger le fichier zip dans le nœud principal du cluster HDInsight :

		scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Remplacez __FILENAME__ par le nom du fichier zip. Remplacez __USERNAME__ par la connexion SSH du cluster HDInsight. Remplacez CLUSTERNAME par le nom du cluster HDInsight.
	
	> [AZURE.NOTE] Si vous utilisez un mot de passe pour authentifier votre connexion SSH, vous serez invité à le saisir. Si vous utilisez une clé publique, vous devrez peut-être utiliser le paramètre `-i` et spécifier le chemin d’accès de la clé privée correspondante. Par exemple, `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Une fois le téléchargement terminé, connectez-vous au cluster à l'aide de SSH :

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Pour plus d’informations sur l’utilisation de SSH avec HDInsight sous Linux, consultez les articles suivants :
	
	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
	
3. Une fois connecté, procédez comme suit pour décompresser le fichier .zip :

		unzip FILENAME.zip
	
	Ceci extraira un fichier .csv d'environ 60 Mo.
	
4. Pour créer un répertoire sur WASB (le magasin de données distribuées utilisé par HDInsight), procédez comme suit et copiez le fichier :

	hdfs dfs -mkdir -p /tutorials/flightdelays/data hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
	
##Création et exécution du HiveQL

Suivez la procédure suivante pour importer des données à partir du fichier CSV dans une table Hive nommée __Delays__.

1. Pour créer et modifier un fichier nommé __flightdelays.hql__, utilisez l’instruction suivante :

		nano flightdelays.hql
		
	Utilisez les données suivantes comme contenu de ce fichier :
	
		DROP TABLE delays_raw;
		-- Creates an external table over the csv file
		CREATE EXTERNAL TABLE delays_raw (
			YEAR string,
			FL_DATE string,
			UNIQUE_CARRIER string,
			CARRIER string,
			FL_NUM string,
			ORIGIN_AIRPORT_ID string,
			ORIGIN string,
			ORIGIN_CITY_NAME string,
			ORIGIN_CITY_NAME_TEMP string,
			ORIGIN_STATE_ABR string,
			DEST_AIRPORT_ID string,
			DEST string,
			DEST_CITY_NAME string,
			DEST_CITY_NAME_TEMP string,
			DEST_STATE_ABR string,
			DEP_DELAY_NEW float,
			ARR_DELAY_NEW float,
			CARRIER_DELAY float,
			WEATHER_DELAY float,
			NAS_DELAY float,
			SECURITY_DELAY float,
			LATE_AIRCRAFT_DELAY float)
		-- The following lines describe the format and location of the file
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE
		LOCATION '/tutorials/flightdelays/data';
		
		-- Drop the delays table if it exists
		DROP TABLE delays;
		-- Create the delays table and populate it with data
		-- pulled in from the CSV file (via the external table defined previously)
		CREATE TABLE delays AS
		SELECT YEAR AS year,
		    FL_DATE AS flight_date,
		    substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
		    substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
		    substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
		    ORIGIN_AIRPORT_ID AS origin_airport_id,
		    substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
		    substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
		    substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
		    DEST_AIRPORT_ID AS dest_airport_id,
		    substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
		    substring(DEST_CITY_NAME,2) AS dest_city_name,
		    substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
		    DEP_DELAY_NEW AS dep_delay_new,
		    ARR_DELAY_NEW AS arr_delay_new,
		    CARRIER_DELAY AS carrier_delay,
		    WEATHER_DELAY AS weather_delay,
		    NAS_DELAY AS nas_delay,
		    SECURITY_DELAY AS security_delay,
		    LATE_AIRCRAFT_DELAY AS late_aircraft_delay
		FROM delays_raw;
		
2. Appuyez sur __Ctrl + X__, puis sur __Y__ pour enregistrer le fichier.

3. Pour démarrer Hive et exécuter le fichier __flightdelays.hql__, procédez comme suit :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -f flightdelays.hql
		
	> [AZURE.NOTE] Dans cet exemple, `localhost` est utilisé, car vous êtes connecté au nœud principal du cluster HDInsight, où s’exécute HiveServer2.

4. Utilisez la commande suivante pour ouvrir une session Beeline interactive :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

5. Lorsque vous recevrez l’invite `jdbc:hive2://localhost:10001/>`, utilisez l’instruction suivante pour récupérer les données à partir des données relatives aux retards de vol qui ont été importées.

		INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		SELECT regexp_replace(origin_city_name, '''', ''),
			avg(weather_delay)
		FROM delays
		WHERE weather_delay IS NOT NULL
		GROUP BY origin_city_name;

	Vous récupérerez ainsi la liste des villes qui ont enregistré des retards liés aux conditions météo, ainsi que le temps de retard moyen, qui seront enregistrés dans `/tutorials/flightdelays/output`. Sqoop lira ensuite les données à partir de cet emplacement et les exportera vers Azure SQL Database.

6. Pour quitter Beeline, entrez `!quit` à l’invite de commandes.

## Création d’une base de données SQL

Si vous disposez déjà d’une base de données SQL, vous devez obtenir le nom du serveur. Vous le trouverez sur le [portail Azure](https://portal.azure.com) en sélectionnant __Bases de données SQL__, puis en filtrant sur le nom de la base de données que vous souhaitez utiliser. Le nom du serveur est répertorié dans la colonne __SERVEUR__.

Si vous n’avez pas de base de données SQL, consultez les informations dans le [Didacticiel sur la base de données SQL : Création d’une base de données SQL en quelques minutes](../sql-database/sql-database-get-started.md) pour en créer une. Vous devez enregistrer le nom du serveur utilisé pour la base de données.

##Création d’une table de base de données SQL

> [AZURE.NOTE] Il existe de nombreuses façons de se connecter à la base de données SQL pour créer une table. Les étapes suivantes utilisent [FreeTDS](http://www.freetds.org/) à partir du cluster HDInsight.

1. Utilisez le protocole SSH pour vous connecter au cluster HDInsight Linux et exécutez les étapes suivantes à partir de la session SSH.

3. Utilisez la commande suivante pour installer FreeTDS :

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Une fois FreeTDS installé, utilisez la commande suivante pour vous connecter au serveur de base de données SQL. Remplacez __serverName__ par le nom du serveur de base de données SQL. Remplacez __adminLogin__ et __adminPassword__ par les informations d’identification de la base de données SQL. Remplacez __databaseName__ par le nom de la base de données.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>

    Le résultat ressemble à ce qui suit :

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. À l’invite de commandes `1>`, entrez les lignes suivantes :

        CREATE TABLE [dbo].[delays](
		[origin_city_name] [nvarchar](50) NOT NULL,
		[weather_delay] float,
		CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
		([origin_city_name] ASC))
		GO

    Une fois entrée l’instruction `GO`, les instructions précédentes sont évaluées. Ceci créera une table nommée __delays__ avec un index cluster (requis par SQL Database).

    Vérifiez que la table a été créée à l’aide des éléments suivants :

        SELECT * FROM information_schema.tables
        GO

    Le résultat ressemble à ce qui suit :

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        databaseName       dbo     delays      BASE TABLE

8. Pour quitter l’utilitaire psql, entrez `exit` à l’invite de commandes `1>`.
	
##Exportation de données avec Sqoop

2. Utilisez la commande suivante pour vérifier que Sqoop peut voir votre base de données SQL :

		sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

	Cette commande doit renvoyer une liste des bases de données, notamment la base de données que vous avez créée précédemment dans la table des retards.

3. Utilisez la commande suivante pour exporter des données à partir de hivesampletable dans la table mobiledata :

		sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasbs:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

	Sqoop doit se connecter à la base de données SQL, à la base de données contenant la table des retards, puis exporter des données à partir de wasbs:///tutorials/flightdelays/output (l’emplacement où nous avons stocké précédemment la sortie de la requête Hive) vers la table des retards.

4. Une fois la commande terminée, utilisez les éléments suivants pour vous connecter à la base de données à l’aide de TSQL :

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

	Une fois que vous êtes connecté, utilisez les instructions suivantes pour vérifier que les données ont été exportées dans la table mobiledata :
	
		SELECT * FROM delays
		GO

	Vous devez voir une liste des données dans la table. Tapez `exit` pour quitter l’utilitaire tsql.

##<a id="nextsteps"></a>Étapes suivantes

Vous savez à présent télécharger un fichier vers le stockage d’objets blob Azure, renseigner une table Hive à l’aide des données du stockage d’objets blob Azure, exécuter des requêtes Hive et utiliser Sqoop pour exporter des données entre HDFS et une base de données SQL Azure. Pour en savoir plus, consultez les articles suivants :

* [Prise en main de HDInsight][hdinsight-get-started]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation d’Oozie avec HDInsight][hdinsight-use-oozie]
* [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-mapreduce]
* [Développement de programmes de diffusion en continu Hadoop Python pour HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 

<!---HONumber=AcomDC_0727_2016-->