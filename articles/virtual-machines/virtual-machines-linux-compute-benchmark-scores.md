<properties
 pageTitle="Calcul des scores de test d’évaluation pour les machines virtuelles Linux | Microsoft Azure"
 description="Comparer les scores de test d’évaluation de calcul CoreMark pour les machines virtuelles Azure exécutant Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="07/18/2016"
 ms.author="danlep"/>

# Calcul des scores de test d’évaluation pour les machines virtuelles Linux

Les scores de test d’évaluation CoreMark suivants montrent les performances de calcul pour la gamme de machines virtuelles hautes performances d’Azure exécutant Ubuntu. Les scores de test d’évaluation de calcul sont également disponibles pour [les machines virtuelles Windows](virtual-machines-windows-compute-benchmark-scores.md).




## Série A - Calcul intensif


Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | Processeur Intel Xeon E5-2670 0 à 2,6 GHz| 179 | 110 294 | 554
Standard\_A9 | 16 | 2 | Processeur Intel Xeon E5-2670 0 à 2,6 GHz| 189 | 210 816| 2 126
Standard\_A10 | 8 | 1 | Processeur Intel Xeon E5-2670 0 à 2,6 GHz| 188 | 110 025 | 1 045
Standard\_A11 | 16 | 2 | Processeur Intel Xeon E5-2670 0 à 2,6 GHz| 188 | 210 727| 2 073

## Série Dv2


Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 140 | 14 852 | 780
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 133 | 29 467 | 1 863
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 139 | 56 205 | 1 167
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 126 | 108 543 | 3 446
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 à 2,4 GHz | 126 | 205 332 | 9 998
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 125 | 28 598 | 1 510
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 131 | 55 673 | 1 418
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 140 | 107 986 | 3 089
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 à 2,4 GHz | 140 | 208 186 | 8 839
Standard\_D15\_v2 | 20 | 2 | Intel Xeon E5-2673 v3 à 2,4 GHz |28 | 268 560 | 4 667

## Série F

Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_F1 | 1 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 154 | 15 602 | 787
Standard\_F2 | 2 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 126 | 29 519 | 1 233
Standard\_F4 | 4 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 147 | 58 709 | 1 227
Standard\_F8 | 8 | 1 | Intel Xeon E5-2673 v3 à 2,4 GHz | 224 | 112 772 | 3 006
Standard\_F16 | 16 | 2 | Intel Xeon E5-2673 v3 à 2,4 GHz | 42 | 218 571 | 5 113



## Série G


Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1 | 2 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 83 | 31 310 | 2 891
Standard\_G2 | 4 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 84 | 60 112 | 3 537
Standard\_G3 | 8 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 84 | 107 522 | 4 537
Standard\_G4 | 16 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 83 | 195 116 | 5 024
Standard\_G5 | 32 | 2 | Intel Xeon E5-2698B v3 à 2 GHz | 84 | 360 329 | 14 212

## Série GS


Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Itérations/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 84 | 28 613 | 1 884
Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 83 | 54 348 | 3 474
Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 83 | 104 564 | 1 834
Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 à 2 GHz | 84 | 194 111 | 4 735
Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 à 2 GHz | 84 | 357 396 | 16 228


## À propos de CoreMark

Les chiffres Linux ont été calculés en exécutant [CoreMark](http://www.eembc.org/coremark/faq.php) sur Ubuntu. CoreMark a été configuré avec le nombre de threads défini sur le nombre de processeurs virtuels et la concurrence définie sur PThreads. Le nombre cible d’itérations a été ajusté en fonction des performances attendues pour fournir un temps d’exécution d’au moins 20 secondes (généralement beaucoup plus long), avec le score final représentant le nombre d’itérations terminées divisé par le nombre de secondes nécessaires à l’exécution du test. Chaque test a été exécuté au moins sept fois sur chaque machine virtuelle. Les tests ont été exécutés en octobre 2015 sur plusieurs machines virtuelles dans chaque région Azure publique où la machine virtuelle était prise en charge à la date d’exécution.
## Étapes suivantes



* Pour plus d’informations sur les capacités de stockage, les détails du disque et d’autres considérations relatives à la sélection parmi les tailles de machines virtuelles, consultez [Tailles des machines virtuelles](virtual-machines-linux-sizes.md).

* Pour exécuter les scripts CoreMark sur les machines virtuelles Linux, téléchargez le [pack de script CoreMark](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip).

<!---HONumber=AcomDC_0727_2016-->