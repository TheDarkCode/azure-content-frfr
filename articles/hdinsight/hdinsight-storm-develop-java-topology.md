<properties
   pageTitle="Développement de topologies basées sur Java pour Apache Storm | Microsoft Azure"
   description="Apprenez à créer des topologies Storm dans Java en créant une topologie de statistiques simple."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/01/2016"
   ms.author="larryfr"/>

#Développement de topologies Java pour une application de base de comptage du nombre de mots avec Apache Storm et Maven sur HDInsight

Découvrez un processus de base pour créer une topologie Java pour Apache Storm sur HDInsight à l’aide de Maven. Nous vous guiderons dans le processus de création d’une application de statistiques de base avec Maven et Java. Les instructions concernent l’utilisation d’Eclipse, mais vous pouvez utiliser l’éditeur de texte de votre choix.

Après avoir suivi les étapes décrites dans ce document, vous disposerez d’une topologie de base que vous pourrez déployer sur Apache Storm sur HDInsight.

> [AZURE.NOTE] Une version complète de cette topologie est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

##Configuration requise

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Kit de développeur Java (JDK) version 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a> : Maven est un système de génération de projet pour les projets Java.

* Un éditeur de texte comme le Bloc-notes, <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, <a href="http://www.sublimetext.com/" target="_blank">Sublime Text</a>, <a href="https://atom.io/" target="_blank">Atom.io</a>, <a href="http://brackets.io/" target="_blank">Brackets.io</a>. Ou un environnement de développement intégré (IDE), tel que <a href="https://eclipse.org/" target="_blank">Eclipse</a> (version Luna ou ultérieure).

	> [AZURE.NOTE] Votre éditeur ou IDE peut avoir des fonctionnalités spécifiques pour l’utilisation avec Maven, qui ne sont pas traitées dans ce document. Pour plus d’informations sur les capacités de votre environnement d’édition, consultez la documentation du produit que vous utilisez.

##Configuration des variables d’environnement

Les variables d’environnement suivantes peuvent être définies lors de l’installation de Java et du JDK. Toutefois, vous devez vérifier qu’elles existent et qu’elles contiennent les valeurs correctes pour votre système.

* **JAVA\_HOME** : doit pointer vers le répertoire d’installation de l’environnement d’exécution Java (JRE). Par exemple, sur une distribution Unix ou Linux, il doit avoir une valeur semblable à `/usr/lib/jvm/java-7-oracle`. Sous Windows, il a une valeur semblable à `c:\Program Files (x86)\Java\jre1.7`

* **PATH** :doit contenir les chemins d’accès suivants :

	* **JAVA\_HOME** (ou le chemin d’accès équivalent)

	* **JAVA\_HOME\\bin** (ou le chemin d’accès équivalent)

	* Le répertoire d’installation de Maven

##Création d’un projet Maven

À partir de la ligne de commande, utilisez le code suivant pour créer un nouveau projet Maven nommé **WordCount** :

	mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

Cela créera un répertoire nommé **WordCount** à l’emplacement actuel, qui contient un projet Maven de base.

Le répertoire **WordCount** contiendra les éléments suivants :

* **pom.xml** : contient les paramètres du projet Maven.

* **src\\main\\java\\com\\microsoft\\example** : contient le code de votre application.

* **ssrc\\test\\java\\com\\microsoft\\example** : contient des tests pour votre application. Pour cet exemple, nous n’allons pas créer de tests.

###Suppression de l’exemple de code

Étant donné que nous allons créer notre application, supprimez les fichiers d’application et de test générés :

*  **src\\test\\java\\com\\microsoft\\example\\AppTest.Java**

*  **src\\main\\java\\com\\microsoft\\example\\App.Java**

##Ajout de dépendances

Puisqu’il s’agit d’une topologie Storm, vous devez ajouter une dépendance pour les composants Storm. Ouvrez le fichier **pom.xml** et ajoutez le code suivant dans la section **&lt;dependencies>** :

	<dependency>
	  <groupId>org.apache.storm</groupId>
	  <artifactId>storm-core</artifactId>
      <!-- Storm 0.10.0 is for HDInsight 3.3 and 3.4.
           To find the version information for earlier HDInsight cluster
           versions, see https://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/ -->
	  <version>0.10.0</version>
	  <!-- keep storm out of the jar-with-dependencies -->
	  <scope>provided</scope>
	</dependency>

Au moment de la compilation, Maven utilise ces informations pour rechercher **storm-core** dans le référentiel Maven. Il recherche d’abord dans le référentiel sur votre ordinateur local. Si les fichiers ne s’y trouvent pas, il les télécharge à partir du référentiel Maven public et les stocke dans le référentiel local.

> [AZURE.NOTE] Notez la ligne `<scope>provided</scope>` dans la section que nous avons ajoutée. Elle indique à Maven d’exclure **storm-core** de tous les fichiers Jar que nous créons, étant donné qu’il sera fourni par le système. Ainsi, les packages que vous créez seront un peu plus petits et cela garantit qu'ils utiliseront les bits **storm-core** inclus dans le cluster Storm sur HDInsight.

##Configuration de build

Les plug-ins Maven permettent de personnaliser les étapes de la génération du projet, telles que la façon dont le projet est compilé ou l’empaquetage dans un fichier jar. Ouvrez le fichier **pom.xml** et ajoutez le code suivant directement au-dessus de la ligne `</project>`.

	<build>
	  <plugins>
	  </plugins>
      <resources>
      </resources>
	</build>

Cette section est utilisée pour ajouter des plug-ins, des ressources et d’autres options de configuration de build. Pour obtenir une référence complète du fichier __pom.xml__, consultez [http://maven.apache.org/pom.html](http://maven.apache.org/pom.html).

###Ajout de plug-ins

Pour les topologies Storm, le <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">plug-in Exec Maven </a> est utile, car il vous permet d'exécuter facilement la topologie localement sur votre environnement de développement. Ajoutez le code suivant à la section `<plugins>` du fichier **pom.xml** pour inclure le plug-in Exec Maven :

	<plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.4.0</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

Le <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">plug-in du compilateur Maven Apache</a> est un autre plug-in utile, car il sert à modifier les options de compilation. La raison principale pour laquelle nous en avons besoin est la modification de la version de Java que Maven utilise pour la source et la cible de votre application. Nous voulons la version 1.7.

Ajoutez le code suivant dans la section `<plugins>` du fichier **pom.xml** pour inclure le plug-in du compilateur Maven Apache et définir la version source et cible comme étant la version 1.7.

	<plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

###Configure resources

La section des ressources vous permet d’inclure des ressources autres que du code comme les fichiers de configuration requis par les composants de la topologie. Pour cet exemple, ajoutez ce qui suit dans la section `<resources>` du fichier **pom.xml**.

    <resource>
        <directory>${basedir}/resources</directory>
        <filtering>false</filtering>
        <includes>
          <include>log4j2.xml</include>
        </includes>
    </resource>

Ceci ajoute le répertoire des ressources à la racine du projet (`${basedir}`), comme un emplacement qui contient des ressources, et inclut le fichier nommé __log4j2.xml__. Ce fichier est utilisé pour configurer les informations qui sont enregistrées par la topologie.

##Création de la topologie

Une topologie Storm basée sur Java comprend trois composants que vous devez créer (ou référencer) en tant que dépendance.

* **Les spouts** : lisent les données provenant de sources externes et émettent des flux de données dans la topologie.

* **Les bolts** : effectuent le traitement des flux de données émis par les spouts ou les autres bolts et émettent un ou plusieurs flux.

* **La topologie** : définit l’organisation des spouts et des bolts et fournit le point d’entrée pour la topologie.

###Création du spout

Afin de réduire les besoins de configuration de sources de données externes, le spout suivant émet des phrases aléatoires. Il s’agit d’une version modifiée d’un spout fourni dans les ([exemples Storm-Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter)).

> [AZURE.NOTE] Pour obtenir un exemple de spout effectuant des lectures à partir d’une source de données externe, consultez un des exemples suivants :
>
> * [TwitterSampleSpout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java) : un exemple de spout qui lit à partir de Twitter
>
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka) : un spout qui lit à partir de Kafka

Pour le spout, créez un nouveau fichier nommé **RandomSentenceSpout.java** dans le répertoire **src\\main\\java\\com\\microsoft\\example** et utilisez ce qui suit comme contenu :

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

Prenez un moment pour lire les commentaires du code afin de comprendre le fonctionnement de ce spout.

> [AZURE.NOTE] Bien que cette topologie utilise un seul spout, d’autres peuvent en avoir plusieurs, qui alimentent la topologie avec des données provenant de sources différentes.

###Création des bolts

Les bolts gèrent le traitement des données. Dans cette topologie, nous en avons deux :

* **SplitSentence** : fractionne les phrases émises par **RandomSentenceSpout** en mots.

* **WordCount** : compte le nombre d’occurrences de chaque mot.

> [AZURE.NOTE] Les bolts peuvent tout faire : des calculs, la persistance, la communication avec des composants externes, etc.

Créez deux nouveaux fichiers, **SplitSentence.java** et **WordCount.Java** dans le répertoire **src\\main\\java\\com\\microsoft\\example**. Utilisez les données suivantes comme contenu des fichiers :

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    // For logging
    import org.apache.logging.log4j.Logger;
    import org.apache.logging.log4j.LogManager;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
      //Create logger for this class
      private static final Logger logger = LogManager.getLogger(WordCount.class);
      
      //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
          //Get the word contents from the tuple
          String word = tuple.getString(0);
          //Have we counted any already?
          Integer count = counts.get(word);
          if (count == null)
            count = 0;
          //Increment the count and store it
          count++;
          counts.put(word, count);
          //Emit the word and the current count
          collector.emit(new Values(word, count));
          //Log information
          logger.info("Emitting a count of " + count + " for word " + word);
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
          declarer.declare(new Fields("word", "count"));
        }
      }

Prenez un moment pour lire les commentaires du code afin de comprendre le fonctionnement de chaque bolt.

###Définition de la topologie

La topologie lie les spouts et les bolts dans un graphique, qui définit la circulation des données entre les composants. Elle fournit également des indicateurs de parallélisme que Storm utilise lors de la création des instances de composants au sein du cluster.

Voici un schéma de base du graphique des composants de cette topologie.

![schéma montrant la disposition des spouts et bolts](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

Pour implémenter la topologie, créez un fichier nommé **WordCountTopology.java** dans le répertoire **src\\main\\java\\com\\microsoft\\example**. Utilisez le s données suivantes comme contenu du :

	package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        //Set to false to disable debug information
        // when running in production mode.
        conf.setDebug(false);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

Prenez un moment pour lire les commentaires du code afin de comprendre comment la topologie est définie, puis envoyée au cluster.

###Configuration de la journalisation

Storm utilise Apache Log4j pour journaliser les informations. Si vous ne configurez pas la journalisation, la topologie émettra un grand nombre d’informations de diagnostic, qui peuvent être difficiles à lire. Pour contrôler ce qui est enregistré, créez un fichier nommé __log4j2.xml__ dans le répertoire des __ressources__. Utilisez les données suivantes comme contenu du fichier.

    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
    </Configuration>

Cela permet de configurer un nouvel enregistreur d’événements pour la classe __com.microsoft.example__, qui inclut les composants de cet exemple de topologie. Le niveau est défini pour effectuer le suivi de ce journal, ce qui permet de capturer les informations de journalisation émises par les composants dans cette topologie. Si vous réexaminez le code pour ce projet, vous remarquerez que seul le fichier WordCount.java implémente la journalisation. Il enregistre le nombre de chaque mot.

La section `<Root level="error">` configure le niveau racine de journalisation (tout ce qui n’est pas dans __com.microsoft.example__,) pour consigner uniquement les informations d’erreur.

> [AZURE.IMPORTANT] Bien que cela réduise considérablement les informations consignées lors du test d’une topologie dans votre environnement de développement, cela ne supprime pas toutes les informations de débogage générées lors de l’exécution sur un cluster de production. Pour réduire ces informations, vous devez également définir le débogage sur false dans la configuration envoyée au cluster. Consultez le code WordCountTopology.java dans ce document pour obtenir un exemple.

Pour plus d’informations sur la configuration de la journalisation pour Log4j, consultez [http://logging.apache.org/log4j/2.x/manual/configuration.html](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [AZURE.NOTE] Storm version 0.10.0 utilise Log4j 2.x. Les versions antérieures de Storm utilisaient Log4j 1.x, qui utilisait un autre format pour la configuration du journal. Pour plus d’informations sur la configuration antérieure, consultez [http://wiki.apache.org/logging-log4j/Log4jXmlFormat](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).

##Test local de la topologie

Après avoir enregistré les fichiers, utilisez la commande suivante pour tester la topologie localement.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

Pendant son exécution, la topologie affiche les informations de démarrage. Elle commence ensuite à afficher des lignes similaires à ce qui suit, lorsque des phrases sont émises par le spout et traitées par les bolts.

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 51 for word i
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 51 for word at
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 51 for word with
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 51 for word nature
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 51 for word two
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 51 for word am

Selon la consignation du bolt WordCount, nous pouvons voir que le mot « apple » a été émis 53 fois. Le décompte continuera d’augmenter tant que la topologie est en cours d’exécution, étant donné que les mêmes phrases sont émises au hasard indéfiniment et que le compteur n’est jamais réinitialisé.

##Trident

Trident est une abstraction de haut niveau fournie par Storm. Il prend en charge le traitement avec état. Le principal avantage de Trident est qu’il peut garantir que chaque message qui entre dans la topologie n’est traité qu’une seule fois, ce qui est difficile à réaliser dans une topologie Java brute, qui garantit que les messages seront traités au moins une fois. Il existe aussi d'autres différences, comme les composants intégrés pouvant être utilisés, plutôt que de créer des bolts. Les bolts sont entièrement remplacés par des composants moins génériques comme les filtres, les projections et les fonctions.

Les applications Trident peuvent être créées à l’aide de projets Maven. Les étapes de base sont les mêmes que celles présentées plus haut dans cet article, seul le code est différent.

Pour plus d’informations sur Trident, consultez la <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Présentation de l’API Trident</a>.

Pour obtenir un exemple d’application Trident, consultez les [Rubriques de tendances Twitter avec Apache Storm sur HDInsight](hdinsight-storm-twitter-trending.md).

##Étapes suivantes

Vous avez appris à créer une topologie Storm à l’aide de Java. Apprenez maintenant à effectuer les actions suivantes :

* [Déploiement et gestion des topologies Apache Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology.md)

* [Développement de topologies C# pour Apache Storm dans HDInsight à l'aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Vous trouverez davantage d’exemples de topologies Storm en vous rendant sur [Exemples de topologies Storm sur HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=AcomDC_0803_2016-->