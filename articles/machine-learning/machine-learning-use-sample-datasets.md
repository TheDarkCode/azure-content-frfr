<properties
	pageTitle="Utilisation des exemples de jeux de données dans Machine Learning Studio | Microsoft Azure"
	description="Descriptions des jeux de données utilisés dans les exemples de modèles inclus dans ML Studio. Vous pouvez utiliser ces exemples de jeux de données pour vos expériences."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="garye"/>


# Utilisation des exemples de jeux de données dans Azure Machine Learning Studio

[top]: #machine-learning-sample-datasets

Quand vous créez un espace de travail dans Azure Machine Learning, vous disposez par défaut d'un certain nombre d'exemples de jeux de données et d'expériences. Plusieurs de ces exemples de jeu de données sont utilisés par les exemples de modèles dans la [galerie Azure Cortana Intelligence](http://gallery.cortanaintelligence.com/), tandis que d’autres illustrent les différents types de données utilisés en général dans l’apprentissage automatique.

Certains de ces jeux de données sont disponibles dans le stockage d'objets blob Azure. Le tableau ci-dessous fournit un lien direct vers ces jeux de données. Vous pouvez utiliser ces jeux de données dans vos expériences à l’aide du module [Importer les données][import-data].

Les autres exemples de jeux de données sont répertoriés sous **Jeux de données enregistrés** dans la palette de modules à gauche du canevas de l'expérience quand vous ouvrez ou créez une expérience dans ML Studio. Vous pouvez utiliser un de ces jeux de données dans votre propre expérience en le faisant glisser sur le canevas de l'expérience.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<table>

<tr>
  <th align=left>Nom du jeu de données</th>
  <th align=left>Description du jeu de données</th>
</tr>

<tr>
  <td valign=top>Jeu de données Adult Census Income Binary Classification</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-adult-census-income](../../includes/machine-learning-sample-dataset-adult-census-income.md)]
  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Jeu de données des codes de l'aéroport</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../../includes/machine-learning-sample-dataset-airport-codes.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur le prix des véhicules automobiles (brutes)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-price](../../includes/machine-learning-sample-dataset-automobile-price.md)]
  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Jeu de données de location de vélos de l'UCI</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]
  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Image RVB de Bill Gates</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur le don de sang</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-blood-donation](../../includes/machine-learning-sample-dataset-blood-donation.md)]
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Critiques de livres d'Amazon</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur le cancer du sein</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-data](../../includes/machine-learning-sample-dataset-breast-cancer-data.md)]
  </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Caractéristiques du cancer du sein
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-features](../../includes/machine-learning-sample-dataset-breast-cancer-features.md)]
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Informations sur le cancer du sein</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-info](../../includes/machine-learning-sample-dataset-breast-cancer-info.md)]
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>Étiquettes de l'appétence CRM partagées</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>Étiquettes de l'attrition CRM partagées</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>Jeu de données CRM partagé</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>Étiquettes de vente incitative CRM partagées</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur la régression de l'efficacité énergétique</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-energy-efficiency-regression](../../includes/machine-learning-sample-dataset-energy-efficiency-regression.md)]
  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Données relatives aux vols retardés</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../../includes/machine-learning-sample-dataset-flight-delays-data.md)]
</td>
</tr>

<tr>
  <td valign=top>Performance concernant les vols à l'heure (brutes)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-on-time-performance](../../includes/machine-learning-sample-dataset-flight-on-time-performance.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur les feux de forêt</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-forest-fires](../../includes/machine-learning-sample-dataset-forest-fires.md)]
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Jeu de données d'UCI pour une carte de crédit allemande</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Titres de films IMDB</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-imdb-movie-titles](../../includes/machine-learning-sample-dataset-imdb-movie-titles.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur deux classes d'iris</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-iris-two-class](../../includes/machine-learning-sample-dataset-iris-two-class.md)]
  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets de films</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-movie-tweets](../../includes/machine-learning-sample-dataset-movie-tweets.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur la quantité de litres au&#160;100 pour différents véhicules automobiles</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-mpg](../../includes/machine-learning-sample-dataset-automobile-mpg.md)]
  </td>
</tr>

<tr>
  <td valign=top>Jeu de données sur la classification binaire du diabète chez les indiens Pima</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-pima-indians-diabetes](../../includes/machine-learning-sample-dataset-pima-indians-diabetes.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur les clients de restaurant</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-customer-data](../../includes/machine-learning-sample-dataset-restaurant-customer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur les caractéristiques de restaurants</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-feature-data](../../includes/machine-learning-sample-dataset-restaurant-feature-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Notations de restaurants</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-ratings](../../includes/machine-learning-sample-dataset-restaurant-ratings.md)]
  </td>
</tr>

<tr>
  <td valign=top>Jeu de données multiclasse de recuit d'acier</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-steel-annealing](../../includes/machine-learning-sample-dataset-steel-annealing.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur les télescopes</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-telescope-data](../../includes/machine-learning-sample-dataset-telescope-data.md)]
  </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Jeu de données météorologiques</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../../includes/machine-learning-sample-dataset-weather-dataset.md)]
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Jeu de données Wikipedia concernant le SP&#160;500</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-direct-marketing](../../includes/machine-learning-sample-dataset-direct-marketing.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-test](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-test.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-train](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-train.md)]
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../../includes/machine-learning-sample-dataset-network-intrusion.md)]
  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-rcv1-v2-topics](../../includes/machine-learning-sample-dataset-rcv1-v2-topics.md)]
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../../includes/machine-learning-sample-dataset-student-performance.md)]
  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0615_2016-->