

Azure est une excellente plateforme d’implémentation de configurations de développement/test ou de validation technique, car elle ne nécessite qu’un investissement réduit pour tester une approche spécifique d’implémentation de vos solutions. Toutefois, vous devez être en mesure de distinguer les pratiques pour un environnement de développement/test faciles à mettre en place des pratiques plus difficiles et détaillées pour l’implémentation entièrement fonctionnelle et prête pour la production d’une charge de travail informatique.

Ce guide identifie les nombreux domaines pour lesquels la planification est un élément essentiel au bon fonctionnement d’une charge de travail informatique dans Azure. Par ailleurs, la planification indique un ordre pour la création des ressources nécessaires. Même si une certaine flexibilité est possible, nous recommandons d’appliquer l’ordre indiqué dans cet article à la planification et la prise de décision.

Cet article est une adaptation du contenu du billet de blog [Instructions pour la mise en œuvre d’Azure](http://blogs.msdn.com/b/thecolorofazure/archive/2014/05/13/azure-implementation-guidelines.aspx). Merci à Santiago Cánepa et Hugo Salcedo (responsables du développement d’applications chez Microsoft) pour leur documentation d’origine.

> [AZURE.NOTE] Groupes d’affinités sont déconseillés. Leur utilisation n’est pas décrite ici. Pour en savoir plus, consultez [À propos des réseaux virtuels régionaux et des groupes d’affinités](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md).

## 1\. Conventions d’affectation de noms

Vous devez avoir une convention d’affectation de noms adaptée avant tout processus de création dans Azure. Une convention d’affectation de noms garantit que toutes les ressources ont un nom prévisible, afin de réduire la charge administrative associée à leur gestion.

Vous pouvez choisir de suivre un ensemble spécifique de conventions d’affectation de noms définies pour votre organisation, ou pour un compte ou abonnement Azure spécifique. Bien qu’il soit facile d’établir des règles implicites au sein d’entreprises lorsque vous travaillez avec des ressources Azure, ce modèle n’est pas très souple lorsqu’une équipe doit travailler sur un projet sur Azure.

Vous devez convenir d’un ensemble de conventions d’affectation de noms en amont. Certains facteurs sont à prendre en compte pour l’ensemble des règles en matière de conventions d’affectation de noms.

### Affixes

Lors de la création de certaines ressources, Azure utilise certains paramètres par défaut pour simplifier la gestion des ressources qui leur sont associées. Par exemple, lorsque vous créez la première machine virtuelle d’un nouveau service cloud, le portail Azure Classic tente d’utiliser le nom de la machine virtuelle comme nom de service cloud pour la machine virtuelle.

Par conséquent, il est utile d’identifier les types de ressources nécessitant un affixe. En outre, spécifiez clairement si l’affixe sera placé

- au début du nom (préfixe)
- à la fin du nom (suffixe)

Voici deux exemples de noms possibles pour un groupe de ressources hébergeant un moteur de calcul :

- Rg-CalculationEngine (préfixe)
- CalculationEngine-Rg (suffixe)

Les affixes peuvent faire référence à différents aspects des ressources spécifiques. Le tableau suivant présente des exemples généralement utilisés.

Aspect | Exemples | Remarques
--- | --- | ---
Environnement | dev, stg, prod | En fonction de l’objectif et du nom de chaque environnement.
Emplacement | usw (West US), use (East US 2) | En fonction de la région du centre de données et de l’organisation.
Composant, service ou produit Azure | Rg pour groupe de ressources, Svc pour service cloud, VNet pour réseau virtuel | En fonction du produit auquel la ressource est associée.
Rôle | sql, ora, sp, iis | En fonction du rôle de la machine virtuelle.
Instance | 01, 02, 03, etc. | Pour les ressources possédant plusieurs instances. Par exemple, des serveurs Web à charge équilibrée dans un service cloud.

Lors de l’établissement de conventions d’affectation de noms, assurez-vous qu’elles indiquent clairement les affixes à utiliser pour chaque type de ressource et à quelle position (suffixe ou préfixe).

### Dates

Dans de nombreux cas, il est important de déterminer la date de création à partir du nom d’une ressource. Nous recommandons le format de date AAAAMMJJ. Ce format permet non seulement d’enregistrer la date complète, mais également de trier simultanément par ordre alphabétique et par ordre chronologique deux ressources dont les noms diffèrent uniquement au niveau de la date.

### Ressources d’affectation de noms

Vous devez définir chaque type de ressource dans la convention d’affectation de noms, qui doit comprendre des règles définissant l’attribution de nom pour chaque ressource créée. Ces règles doivent s’appliquer à tous les types de ressources, par exemple :

- Abonnements
- Comptes
- Comptes de stockage
- Réseaux virtuels
- Sous-réseaux
- Groupes à haute disponibilité
- Groupes de ressources
- Services cloud
- Machines virtuelles
- Points de terminaison
- groupes de sécurité réseau ;
- contrôleur

Les noms doivent être descriptifs, afin de fournir suffisamment d’informations pour déterminer la ressource à laquelle ils font référence.

### Noms des ordinateurs

Lorsque les administrateurs créent une machine virtuelle, ils doivent spécifier un nom de machine virtuelle de 15 caractères maximum dans Microsoft Azure. Azure utilise ensuite le nom de la machine virtuelle comme nom de ressource de la machine virtuelle Azure. Azure utilise le même nom comme nom d’ordinateur pour le système d’exploitation installé sur la machine virtuelle. Toutefois, ces noms peuvent ne pas toujours être identiques.

Si une machine virtuelle est créée à partir d’un fichier d’image .vhd qui contient déjà un système d’exploitation, le nom de la machine virtuelle dans Azure peut différer du nom d’ordinateur du système d’exploitation de la machine virtuelle. Dans ce cas, la gestion de la machine virtuelle devient plus difficile. C’est pourquoi nous le déconseillons. Affectez à la ressource de la machine virtuelle Azure le nom d’ordinateur attribué au système d’exploitation de cette machine virtuelle.

Nous recommandons que le nom de la machine virtuelle Azure soit le même que le nom d’ordinateur du système d’exploitation sous-jacent. Pour cette raison, suivez les règles d’affectation de noms NetBIOS, comme décrit dans les [Conventions d’affectation de noms d’ordinateur Microsoft NetBIOS](https://support.microsoft.com/kb/188997/).

### Noms des comptes de stockage

Le nom des comptes de stockage sont régis par des règles spécifiques. Vous ne pouvez utiliser que des lettres minuscules et des chiffres. Pour plus d’informations, voir [Création d’un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account). En outre, le nom du compte de stockage, en association avec core.windows.net, doit être un nom DNS unique et globalement valide. Par exemple, si le compte de stockage est appelé mystorageaccount, les noms DNS suivants qui en résultent doivent être uniques :

- mystorageaccount.blob.core.windows.net
- mystorageaccount.table.core.windows.net
- mystorageaccount.queue.core.windows.net


### Noms des blocs de construction Azure

Les blocs de construction Azure sont des services de niveau applications, généralement offerts par Azure aux applications tirant parti de fonctionnalités PaaS, bien que les ressources IaaS puissent en exploiter certaines, notamment la base de données SQL, Traffic Manager, etc.

Ces services s’appuient sur un tableau d’artefacts créés et enregistrés dans Azure. Ils doivent être également pris en compte dans vos conventions d’affectation de noms.

### Récapitulatif des instructions d’implémentation pour les conventions d’affectation de noms

Décision :

- Quelles sont vos conventions d’affectation de noms pour les ressources Azure ?

Tâche :

- Définir les conventions d’affectation de noms en termes d’affixes, la hiérarchie, les valeurs de chaînes de caractères et d’autres stratégies pour les ressources Microsoft Azure.

## 2\. Abonnements et comptes

Pour utiliser Azure, vous avez besoin d’un ou de plusieurs abonnements Azure. Des ressources telles que des services cloud ou des machines virtuelles existent dans le contexte de ces abonnements.

- Les clients d’entreprises disposent généralement d’une inscription d’entreprise, qui est la ressource principale dans la hiérarchie et est associée à un ou plusieurs comptes.
- Pour les particuliers et les clients sans inscription d’entreprise, la ressource principale est le compte.
- Les abonnements sont associés à des comptes, et chaque compte peut être associé à un ou plusieurs abonnements. Azure enregistre les informations de facturation au niveau de l’abonnement.

La relation compte/abonnement étant limitée à deux niveaux de hiérarchie, il est important d’aligner la convention d’affectation de noms des comptes et des abonnements sur les besoins liés à la facturation. Par exemple, si une entreprise multinationale utilise Azure, elle peut choisir d’avoir un seul compte par région et des abonnements gérés au niveau régional.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

Par exemple, vous pouvez utiliser la structure suivante.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

En reprenant le même exemple, si une région décide d’avoir plusieurs abonnements associés à un groupe spécifique, la convention d’affectation de noms doit alors comprendre un code pour l’information supplémentaire dans le nom du compte ou de l’abonnement. Cette organisation permet le transfert de données de facturation pour générer de nouveaux niveaux de hiérarchie lors de l’établissement de rapports de facturation.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

Vous trouverez ci-dessous un exemple d’organisation.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

Microsoft fournit une facturation détaillée au moyen d’un fichier téléchargeable, pour un compte unique ou pour tous les comptes liés à un accord d’entreprise. Vous pouvez traiter ce fichier, par exemple, à l’aide de Microsoft Excel. Dans ce cas, le processus reçoit des données, partage les ressources qui encodent plusieurs niveaux de hiérarchie dans des colonnes distinctes et utilise un tableau croisé dynamique ou PowerPivot pour permettre la création de rapports dynamiques.

### Récapitulatif des instructions d’implémentation pour les abonnements et les comptes

Décision :

- Quel est l’ensemble d’abonnements et de comptes dont vous avez besoin pour héberger votre charge de travail ou votre infrastructure informatique ?

Tâche :

- Créer l’ensemble d’abonnements et de comptes à l’aide de votre convention d’affectation de noms.

## 3\. Stockage

Le stockage Azure fait partie intégrante de nombreuses solutions Azure. Le stockage Azure fournit des services pour le stockage des données de fichiers, les données non structurées et les messages. Il fait également partie de l’infrastructure de prise en charge des machines virtuelles.

Il existe deux types de comptes de stockage disponibles dans Azure. Un compte de stockage standard vous donne accès au stockage d’objets blob (utilisé pour le stockage de disques de machines virtuelles Azure), de tables, de files d’attente et de fichiers de stockage. Le stockage Premium est conçu pour des applications hautes performances, telles que les serveurs SQL dans un cluster AlwaysOn, et prend actuellement en charge uniquement les disques de machine virtuelle Azure.

Les comptes de stockage sont liés à des objectifs d’extensibilité. Pour vous familiariser avec les limites de stockage Azure actuelles, voir [Abonnement Microsoft Azure et limites, quotas et contraintes du service](../articles/azure-subscription-service-limits.md#storage-limits). Voir également [Objectifs de performance et d’extensibilité d’Azure Storage](../articles/storage/storage-scalability-targets.md).

Azure crée des machines virtuelles avec un disque de système d’exploitation, et éventuellement plusieurs disques de données facultatifs. Le disque de système d’exploitation et les disques de données sont des objets blob de pages Azure, tandis que le disque temporaire est stocké localement sur le nœud comprenant l’emplacement de la machine. Le disque temporaire est alors inapproprié pour les données qui doivent être conservées au cours d’un recyclage de système, car la machine peut être migrée en mode silencieux d’un nœud à l’autre, ce qui implique la perte de toutes les données de ce disque. Ne stockez rien sur le disque temporaire.

Les disques de système d’exploitation et les disques de données ont une taille maximale de 1 023 Go, étant donné que la taille maximale d’un objet blob est de 1 024 Go et qu’il doit contenir les métadonnées (pied de page) du fichier VHD (un Go compte 1 024<sup>3</sup> octets). Vous pouvez mettre en place un entrelacement de disques dans Windows pour dépasser cette limite.

### Disques agrégés par bandes
Outre la possibilité de créer des disques d’une taille supérieure à 1 023 Go dans plusieurs instances, l’entrelacement de disques améliore les performances en permettant à plusieurs objets blob de sauvegarder le stockage d’un seul volume. Avec l’agrégation par bandes, l’E/S requise pour écrire et lire des données à partir d’un seul disque logique est exécutée en parallèle.

Azure impose des limites quant à la quantité de disques de données et de bande passante disponible, selon la taille de la machine virtuelle. Pour en savoir plus, consultez la rubrique [Tailles de machines virtuelles](../articles/virtual-machines/virtual-machines-linux-sizes.md).

Si vous utilisez l’entrelacement pour les disques de données Azure, respectez les consignes suivantes :

- Les disques de données doivent toujours avoir la taille maximale (1 023 Go)
- Attachez le nombre maximum autorisé de disques de données pour la taille de machine virtuelle
- Utilisez la configuration des espaces de stockage
- Utilisez la configuration de l’entrelacement du stockage
- Évitez d’utiliser des options de mise en cache des disques de données Azure (Stratégie de mise en cache = Aucune)

Pour plus d’informations, voir la page [Espaces de stockage : une conception pour la performance](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx).

### Comptes de stockage multiples

L’utilisation de plusieurs comptes de stockage pour sauvegarder les disques associés à de nombreuses machines virtuelles garantit que les E/S agrégées de ces disques sont largement en-dessous des objectifs d’extensibilité pour chaque compte de stockage.

Nous recommandons de commencer par le déploiement d’une machine virtuelle par compte de stockage.

### Conception de la disposition du stockage

Pour mettre en œuvre ces stratégies de manière à mettre en œuvre le sous-système de disques des machines virtuelles avec des performances optimales, une charge de travail ou une infrastructure informatique tire généralement parti de plusieurs comptes de stockage. Ils hébergent de nombreux objets blob VHD. Dans certains cas, plusieurs objets blob sont associés à un seul volume sur une machine virtuelle.

Cette situation peut compliquer les tâches de gestion. La conception d’une stratégie solide pour le stockage, notamment la dénomination appropriée pour les disques sous-jacents et les objets BLOB associés, est un élément clé.

### Récapitulatif des instructions d’implémentation pour le stockage

Décisions :

- Avez-vous besoin d’un entrelacement pour créer des disques d’une taille supérieure à 500 téraoctets (To) ?
- Avez-vous besoin d’un entrelacement pour optimiser les performances de votre charge de travail ?
- Quel est l’ensemble de comptes de stockage dont vous avez besoin pour héberger votre charge de travail ou votre infrastructure informatique ?

Tâche :

- Créer l’ensemble de comptes de stockage à l’aide de votre convention d’affectation de noms. Vous pouvez utiliser le portail Azure, le portail Azure Classic ou l’applet de commande PowerShell **New-AzureStorageAccount**.

## 4\. Services cloud

Les services cloud sont un bloc de construction fondamental de la gestion des services Azure, à la fois pour les services PaaS et IaaS. Pour le PaaS, les services cloud représentent une association de rôles dont les instances peuvent communiquer entre elles. Les services cloud sont associés à une adresse IP virtuelle publique (VIP) et à un équilibrage de charge, qui prend le trafic entrant provenant d’Internet et équilibre la charge pour les rôles configurés de manière recevoir le trafic.

Dans le cas de l’IaaS, les services cloud offrent des fonctionnalités similaires, bien que, dans la plupart des cas, la fonctionnalité d’équilibrage de charge soit utilisée pour transférer le trafic vers des ports TCP ou UDP spécifiques à partir d’Internet vers les nombreuses machines virtuelles au sein de ce service cloud.

> [AZURE.NOTE] Les services cloud n’existent pas dans Azure Resource Manager. Pour découvrir les avantages de Resource Manager, voir la page [Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](../articles/virtual-machines/virtual-machines-windows-compare-deployment-models.md).

Les noms de service cloud sont particulièrement importants dans l’IaaS, car Azure les utilise en tant que partie intégrante de la convention d’affectation de noms pour les disques par défaut. Le nom de service cloud ne peut contenir que des lettres, des chiffres et des traits d’union. Le premier et le dernier caractère du champ doivent être une lettre ou un chiffre.

Azure expose les noms de service cloud, dans la mesure où ils sont associés à l’adresse IP virtuelle dans le domaine « cloudapp.net ». Pour une expérience utilisateur de l’application optimale, un surnom doit être configuré pour remplacer le nom de service cloud complet. Pour cela, utilisez un enregistrement CNAME dans votre DNS public qui mappe le nom DNS public de votre ressource (par exemple, www.contoso.com) sur le nom DNS du service cloud qui héberge la ressource (par exemple, le service cloud qui héberge les serveurs web pour www.contoso.com).

En outre, la convention d’affectation de noms utilisée pour les services cloud devra peut-être tolérer des exceptions, car les noms de service cloud doivent être uniques parmi tous les autres services cloud Microsoft Azure, quel que soit le locataire Microsoft Azure.

Limite importante des services cloud à prendre en compte : seule une opération de gestion de machine virtuelle peut être effectuée à la fois pour toutes les machines virtuelles dans le service cloud. Lorsque vous effectuez une opération de gestion de machine virtuelle sur une machine virtuelle dans le service cloud, vous devez attendre qu’elle soit terminée avant de pouvoir en effectuer une nouvelle sur une autre machine virtuelle. Par conséquent, vous ne devez conserver qu’un petit nombre de machines virtuelles dans un service cloud.

Les abonnements Azure peuvent prendre en charge 200 services cloud au maximum.

### Récapitulatif des instructions d’implémentation pour les services cloud

Décision :

- Quel est l’ensemble de services cloud dont vous avez besoin pour héberger votre charge de travail ou votre infrastructure informatique ?

Tâche :

- Créer l’ensemble de services cloud à l’aide de votre convention d’affectation de noms. Vous pouvez utiliser le portail Azure Classic ou l’applet de commande PowerShell **New-AzureService**.

## 5\. Réseaux virtuels

L’étape logique suivante consiste à créer les réseaux virtuels nécessaires pour prendre en charge les communications entre les machines virtuelles dans la solution. Bien qu’il soit possible d’héberger plusieurs machines virtuelles d’une charge de travail informatique dans un seul service cloud, des réseaux virtuels sont recommandés.

Les réseaux virtuels constituent un conteneur pour les machines virtuelles pour lesquelles vous pouvez également spécifier des sous-réseaux, un adressage personnalisé et des options de configuration de DNS. Les machines virtuelles au sein du même réseau virtuel peuvent communiquer directement avec d’autres ordinateurs dans le même réseau virtuel, quel que soit le service cloud dont elles sont membres. Cette communication reste privée au sein du réseau virtuel, sans avoir besoin de passer par des points de terminaison publics. Cette communication peut être établie par l’intermédiaire d’une adresse IP ou par un nom, à l’aide d’un serveur DNS installé dans le réseau virtuel ou sur site, si l’ordinateur virtuel est connecté au réseau d’entreprise.

### Connectivité de site
Si les utilisateurs et les ordinateurs sur site ne nécessitent pas de connectivité continue aux machines virtuelles dans un réseau virtuel Azure, créez un réseau virtuel uniquement dans le cloud.

![](./media/virtual-machines-common-infrastructure-service-guidelines/vnet01.png)

Cette solution s’applique généralement à des charges de travail Internet, telles qu’un serveur web Internet. Vous pouvez gérer ces machines virtuelles à l’aide de connexions Bureau à distance, de sessions PowerShell à distance, de connexions SSH (Secure Shell) et de connexions VPN de point à site.

Les réseaux virtuels ne se connectant pas à votre réseau local, les réseaux virtuels uniquement basés sur le cloud peuvent utiliser une partie de l’espace d’adressage IP privé.

Si les utilisateurs et ordinateurs locaux nécessitent une connectivité continue aux machines virtuelles dans un réseau virtuel Azure, créez un réseau virtuel intersite et connectez-le à votre réseau local avec une connexion ExpressRoute ou VPN de site à site.

![](./media/virtual-machines-common-infrastructure-service-guidelines/vnet02.png)

Dans cette configuration, le réseau virtuel Azure est essentiellement une extension cloud de votre réseau local.

Les réseaux virtuels intersite se connectant à votre réseau local, ils doivent utiliser une partie unique de l’espace d’adressage utilisé par votre organisation. Et l’infrastructure de routage doit prendre en charge le routage de trafic vers cette partie en le transmettant à votre périphérique VPN local.

Pour autoriser les paquets à circuler de votre réseau virtuel intersite vers votre réseau local, vous devez configurer le jeu de préfixes d’adresses locales pertinents dans le cadre de la définition du réseau local pour le réseau virtuel. En fonction de l’espace d’adressage du réseau virtuel et de l’ensemble des emplacements locaux concernés, le réseau local peut comprendre de nombreux préfixes d’adresse.

Vous pouvez convertir un réseau virtuel cloud en un réseau virtuel intersite, mais il nécessitera probablement une renumérotation de votre espace d’adressage de réseau virtuel, de vos sous-réseaux et de vos machines virtuelles qui utilisent des adresses IP statiques affectées par Azure, appelées IP dynamiques (DIP). Par conséquent, déterminez avec attention le type de réseaux virtuels dont vous avez besoin (cloud ou intersite) avant de les créer.

### Sous-réseaux
Les sous-réseaux vous permettent d’organiser des ressources apparentées, soit logiquement (par exemple, un sous-réseau pour les machines virtuelles associées à la même application), soit physiquement (par exemple, un sous-réseau par service cloud), ou d’employer des techniques d’isolation de sous-réseaux pour renforcer la sécurité.

Pour les réseaux virtuels intersite, vous devez concevoir des sous-réseaux avec les conventions que vous utilisez pour les ressources locales, en gardant à l’esprit qu’**Azure utilise toujours les trois premières adresses IP de l’espace d’adressage pour chaque sous-réseau**. Pour déterminer le nombre d’adresses nécessaires pour le sous-réseau, comptez le nombre de machines virtuelles dont vous avez besoin maintenant, estimez sa croissance future, puis utilisez le tableau suivant pour déterminer la taille du sous-réseau.

Nombre de machines virtuelles nécessaires | Nombre de bits hôte nécessaires | Taille du sous-réseau
--- | --- | ---
1 à 3 | 3 | /29
4 à 11 | 4 | /28
12 à 27 | 5 | /27
28 à 59 | 6 | /26
60 à 123 | 7 | /25

> [AZURE.NOTE] Pour des sous-réseaux locaux normaux, le nombre maximal d’adresses d’hôte pour un sous-réseau avec n bits hôte est 2<sup>n</sup> – 2. Pour un sous-réseau Azure, le nombre maximal d’adresses d’hôte pour un sous-réseau avec n bits hôte est 2<sup>n</sup> – 5 (2 plus 3 pour les adresses qu’Azure utilise sur chaque sous-réseau).

Si vous choisissez une taille de sous-réseau trop petite, vous devrez renuméroter et redéployer les machines virtuelles dans le sous-réseau.

### Récapitulatif des instructions d’implémentation pour les réseaux virtuels

Décisions :

- Quel type de réseau virtuel devez-vous pour héberger votre charge de travail ou votre infrastructure informatique (cloud ou intersite) ?
- Pour les réseaux virtuels intersite, de quelle taille d’espace adressage avez-vous besoin pour héberger les sous-réseaux et les machines virtuelles maintenant et pour une extension future raisonnable ?

Tâches :

- Définir l’espace d’adressage du réseau virtuel.
- Définir l’ensemble de sous-réseaux et l’espace d’adressage pour chacun.
- Pour les réseaux virtuels intersite, définir l’ensemble des espaces d’adressage de réseau local pour les emplacements locaux auxquels les machines virtuelles doivent accéder dans le réseau virtuel.
- Créer le réseau virtuel à l’aide de votre convention d’affectation de noms. Vous pouvez utiliser le portail Azure ou le portail Azure Classic.

## 6\. Groupes à haute disponibilité

Dans le PaaS Azure, les services cloud comprennent un ou plusieurs rôles qui exécutent du code d’application. Les rôles peuvent avoir une ou plusieurs instances de machine virtuelle provisionnées automatiquement par la structure. À un moment donné, Azure peut mettre à jour les instances de ces rôles, mais, comme elles font partie du même rôle, Azure ne les met pas à jour simultanément afin d’éviter une interruption de service pour le rôle.

Dans l’IaaS Azure, le concept de rôle n’est pas significatif, étant donné que chaque machine virtuelle IaaS représente un rôle avec une seule instance. Le concept de groupes à haute disponibilité a été introduit afin qu’Azure n’interrompe pas deux ordinateurs associés ou plus simultanément (par exemple, pour les mises à jour du système d’exploitation du nœud où ils se trouvent). Un groupe à haute disponibilité indique à Azure ne pas interrompre simultanément toutes les machines dans le même groupe à haute disponibilité afin d’éviter une interruption de service. Les membres de la machine virtuelle d’un groupe à haute disponibilité ont un contrat de niveau service stipulant une disponibilité de 99,95 %.

Les groupes à haute disponibilité doivent faire partie de la planification de la haute disponibilité de la solution. Un groupe à haute disponibilité est défini comme l’ensemble des machines virtuelles au sein d’un unique service cloud qui ont le même nom de groupe de disponibilité. Vous pouvez créer des groupes à haute disponibilité après la création de services cloud.

### Récapitulatif des instructions d’implémentation pour groupes à haute disponibilité

Décision :

- De combien de groupe à haute disponibilité avez-vous besoin pour les différents rôles et niveaux de votre charge de travail ou infrastructure informatique ?

Tâche :

- Définir l’ensemble des groupes à haute disponibilité à l’aide de votre convention d’affectation de noms. Vous pouvez associer une machine virtuelle à un groupe à haute disponibilité lorsque vous créez les machines virtuelles, ou vous pouvez associer une machine virtuelle à un groupe à haute disponibilité après l’avoir créée.

## 7\. Machines virtuelles

Dans le PaaS Azure, Azure gère des machines virtuelles et leurs disques associés. Vous devez créer et nommer des services cloud et des rôles, et Azure crée ensuite des instances associées à ces rôles. Dans le cas de l’IaaS Azure, c’est à vous de donner des noms aux services cloud, aux machines virtuelles et aux disques associés.

Pour réduire la charge administrative, le portail Azure Classic utilise le nom d’ordinateur comme suggestion pour le nom par défaut du service cloud associé (dans le cas où le client choisit de créer un service cloud lors de l’exécution de l’Assistant de création de machines virtuelles).

En outre, les disques de noms Azure et leurs objets blob VHD associés à l’aide d’une combinaison du nom du service cloud, du nom d’ordinateur et la date de création.

En général, le nombre de disques sera nettement supérieur à la quantité de machines virtuelles. Lorsque vous manipulez des machines virtuelles, veillez à éviter les disques orphelins. En outre, les disques peuvent être supprimés sans supprimer l’objet blob associé. Si c’est le cas, l’objet blob reste dans le compte de stockage jusqu’à sa suppression manuelle.

### Récapitulatif des instructions d’implémentation pour les machines virtuelles

Décision :

- Quel est le nombre de machines virtuelles que vous devez fournir pour l’infrastructure ou la charge de travail informatique ?

Tâches :

- Définir chaque nom de machine virtuelle à l’aide de votre convention d’affectation de noms.
- Créez vos machines virtuelles à l’aide du portail Azure, du portail Azure Classic, de l’applet de commande PowerShell **New-AzureVM**, de l’interface de ligne de commande Azure ou des modèles Resource Manager.

## Exemple d’une charge de travail informatique : le moteur d’analyse financière Contoso

La société Contoso Corporation a développé un moteur d’analyse financière de nouvelle génération avec les algorithmes propriétaires de pointe pour vous aider dans vos futures transactions financières. Elle souhaite proposer ce moteur à ses clients sous forme d’un ensemble de serveurs dans Azure constitué :

- de deux serveurs web IIS (ou plus) exécutant des services web personnalisés dans un niveau web ;
- de deux serveurs d’application IIS (ou plus) qui exécutent leurs calculs dans un niveau application ;
- d’un cluster SQL Server 2014 avec des groupes de disponibilité AlwaysOn (deux serveurs SQL et un témoin de nœud majoritaire) qui stocke les données d’historique et de calcul continu dans un niveau de base de données ;
- de deux contrôleurs de domaine Active Directory pour une forêt autonome et un domaine dans le niveau d’  
authentification requis par les clusters SQL Server ;
- de tous les serveurs se trouvant sur deux sous-réseaux, un sous-réseau frontal pour les serveurs web et un sous-réseau back-end pour les serveurs d’applications, un cluster SQL Server 2014 et des contrôleurs de domaine.

![](./media/virtual-machines-common-infrastructure-service-guidelines/example-tiers.png)

La charge du trafic Web entrant sécurisé des clients Contoso sur Internet doit être répartie sur les serveurs web. Le trafic de requêtes de calcul sous la forme de requêtes HTTP depuis les serveurs web doit être réparti sur les serveurs d’applications. En outre, le moteur doit être conçu pour la haute disponibilité.

La conception qui en résulte doit comprendre :

- un abonnement et un compte Azure ;
- des comptes de stockage ;
- un réseau virtuel avec deux sous-réseaux ;
- des groupes à haute disponibilité pour les groupes de serveurs avec un rôle similaire ;
- Machines virtuelles
- un seul groupe de ressources.

Tous les éléments ci-dessus sont conformes aux conventions d’affectation de noms de Contoso :

- Contoso utilise [Charge de travail informatique]-[Emplacement]-[Ressources Azure] comme préfixe. Pour cet exemple, « azfae » (Azure Financial Analysis Engine, moteur d’analyse financière Azure) est le nom de la charge de travail informatique et « use » (East US 2) est l’emplacement, car la plupart des clients initiaux de Contoso sont situés sur la côte est des États-Unis.
- Les comptes de stockage utilisent contosoazfaeusesa[description]. Notez que contoso a été ajouté au préfixe pour garantir l’unicité et que les noms de compte de stockage ne prennent pas en charge l’utilisation de traits d’union.
- Les réseaux virtuels utilisent AZFAE-USE-VN[numéro].
- Les groupes à haute disponibilité utilisent azfae-use-as-[rôle].
- Les noms de machine virtuelle utilisent azfae-use-vm-[nom de machine virtuelle].

### Abonnements et comptes Azure

Contoso utilise son abonnement d’entreprise, nommé Contoso Enterprise Subscription, pour fournir des informations de facturation pour cette charge de travail informatique.

### des comptes de stockage ;

Contoso a déterminé que deux comptes de stockage sont nécessaires :

- **contosoazfaeusesawebapp** pour le stockage standard de serveurs Web, de serveurs d'applications et de contrôleurs de domaine avec leurs disques de données supplémentaires ;
- **contosoazfaeusesasqlclust** pour le stockage premium de serveurs SQL Server en cluster et de leurs disques de données supplémentaires.

### Un réseau virtuel avec des sous-réseaux

Étant donné que le réseau virtuel n’a pas besoin d’une connectivité continue au réseau Contoso local, Contoso a décidé d’adopter un réseau virtuel cloud.

Un réseau virtuel cloud a été créé avec les paramètres suivants via le portail Azure :

- Nom : AZFAE-USE-VN01
- Emplacement : East US 2
- Espace d’adressage du réseau virtuel : 10.0.0.0/8
- Premier sous-réseau :
	- Nom : FrontEnd
	- Espace d’adressage : 10.0.1.0/24
- Second sous-réseau :
	- Nom : BackEnd
	- Espace d’adressage : 10.0.2.0/24

### Groupes à haute disponibilité

Pour assurer une haute disponibilité sur les quatre niveaux de son moteur d’analyse financière, Contoso a adopté quatre groupes à haute disponibilité :

- **azfae-use-as-dc** pour les contrôleurs de domaine ;
- **azfae-use-as-web** pour les serveurs web ;
- **azfae-use-as-app** pour les serveurs d’applications ;
- **azfae-use-as-sql** pour les serveurs du cluster SQL Server.

Ces groupes à haute disponibilité seront créés avec les machines virtuelles.

### Machines virtuelles

Contoso a donné les noms suivants à ses machines virtuelles :

- **azfae-use-vm-dc01** pour le premier contrôleur de domaine ;
- **azfae-use-vm-dc02** pour le second contrôleur de domaine ;
- **azfae-use-vm-web01** pour le premier serveur web ;
- **azfae-use-vm-web02** pour le second serveur Web ;
- **azfae-use-vm-app01** pour le premier serveur d’applications ;
- **azfae-use-vm-app02** pour le second serveur d’applications ;
- **azfae-use-vm-sql01** pour le premier serveur SQL dans le cluster SQL Server ;
- **azfae-use-vm-sql02** pour le second serveur SQL dans le cluster SQL Server ;
- **azfae-use-vm-sqlmn01** pour le témoin de nœud majoritaire dans le cluster SQL Server.

Voici la configuration obtenue.

![](./media/virtual-machines-common-infrastructure-service-guidelines/example-config.png)

Cette configuration comprend :

- un réseau virtuel cloud avec deux sous-réseaux (FrontEnd et BackEnd) ;
- deux comptes de stockage ;
- quatre groupes à haute disponibilité, un pour chaque niveau du moteur d’analyse financière ;
- les machines virtuelles pour les quatre niveaux ;
- un jeu d’équilibrage de charge externe pour le trafic Web basé sur HTTPS depuis Internet vers les serveurs web ;
- un jeu d’équilibrage de charge interne pour le trafic Web non crypté depuis les serveurs Web vers les serveurs d’applications.
- un seul groupe de ressources.

## Ressources supplémentaires

[Abonnement Microsoft Azure et limites, quotas et contraintes du service](../articles/azure-subscription-service-limits.md#storage-limits)

[Tailles de machines virtuelles](../articles/virtual-machines/virtual-machines-linux-sizes.md)

[Objectifs de performance et d’extensibilité d’Azure Storage](../articles/storage/storage-scalability-targets.md)

[Diagramme d’architecture de référence des extensions de centre de données](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84)


[Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](../articles/virtual-machines/virtual-machines-windows-compare-deployment-models.md)

<!---HONumber=AcomDC_0330_2016-->