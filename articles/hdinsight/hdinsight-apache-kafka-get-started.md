---
title: "Prise en main d’Apache Kafka sur HDInsight | Microsoft Docs"
description: "Découvrez les principes de base de la création et de l’utilisation de Kafka sur HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 1c7a95354358d91859ce9d2019cef93b6662811f
ms.lasthandoff: 03/25/2017

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>Prise en main d’Apache Kafka (version préliminaire) sur HDInsight

[Apache Kafka](https://kafka.apache.org) est une plateforme de diffusion en continu distribuée open source, disponible avec HDInsight. Elle est souvent utilisée comme répartiteur de messages, car elle fournit des fonctionnalités similaires à une file d’attente de messages de publication/d’abonnement. Dans ce document, vous allez apprendre à créer un Kafka sur un cluster HDInsight, puis à envoyer et à recevoir des données à partir d’une application Java.

> [!NOTE]
> Il existe actuellement deux versions de Kafka disponibles avec HDInsight ; 0.9.0 (HDInsight 3.4) et 0.10.0 (HDInsight 3.5). Les étapes décrites dans ce document supposent que vous utilisez Kafka sur HDInsight 3.5.

## <a name="prerequisite"></a>Configuration requise

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Vous devez disposer de ce qui suit pour suivre jusqu’au bout ce didacticiel Apache Kafka :

* **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Des connaissances en SSH et SCP**. Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html), ou un équivalent, par exemple, OpenJDK.

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>Création d’un cluster Kafka

Utilisez les étapes suivantes pour créer un Kafka sur un cluster HDInsight :

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez **+ et Nouveau**, **Intelligence et analyse**, puis **HDInsight**.
   
    ![Création d'un cluster HDInsight](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. Dans le panneau **De base**, entrez les informations suivantes :

    * **Nom du cluster** : nom du cluster HDInsight.
    * **Abonnement** : sélectionnez l'abonnement souhaité.
    * **Nom d’utilisateur de connexion du cluster** et **Mot de passe de connexion du cluster** : les informations de connexion lors de l’accès au cluster sur HTTPS. Vous utilisez ces informations d’identification pour accéder aux services tels que l’interface utilisateur Ambari Web ou l’API REST.
    * **Nom d’utilisateur Secure Shell (SSH)** : information de connexion utilisée lors de l’accès au cluster sur SSH. Par défaut, le mot de passe est le même que le mot de passe de connexion de cluster.
    * **Groupe de ressources** : groupe de ressources dans lequel créer le cluster.
    * **Emplacement** : la région Azure dans laquelle créer le cluster.
   
    ![Sélectionnez un abonnement](./media/hdinsight-apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Sélectionnez le **Type de cluster**, puis définissez les valeurs suivantes sur le panneau **Configuration du cluster** :
   
    * **Type de cluster** : Kafka

    * **Version** : Kafka 0.10.0 (HDI 3.5)

    * **Niveau cluster** : standard
     
    Enfin, utilisez le bouton **Sélectionner** pour enregistrer les paramètres.
     
    ![Sélectionner un type de cluster](./media/hdinsight-apache-kafka-get-started/set-hdinsight-cluster-type.png)

    > [!NOTE]
    > Si votre abonnement Azure n’a pas accès à la version préliminaire Kafka, des instructions sur la façon d’y accéder sont affichées. Les instructions affichées sont similaires à l’image suivante :
    >
    > ![Aperçu du message : si vous souhaitez déployer un cluster Apache Kafka géré sur HDInsight, envoyez-nous un courrier électronique pour demander l’accès à la version préliminaire](./media/hdinsight-apache-kafka-get-started/no-kafka-preview.png)

4. Après avoir sélectionné le type de cluster, utilisez le bouton __Sélectionner__ pour définir le type de cluster. Ensuite, utilisez le bouton __Suivant__ bouton pour terminer la configuration de base.

5. À partir du panneau **Stockage**, sélectionnez ou créer un compte de stockage. Pour les étapes de ce document, laissez les autres champs de ce panneau sur leurs valeurs par défaut. Utilisez le bouton __Suivant__ pour enregistrer la configuration de stockage.

    ![Définir les paramètres de compte de stockage pour HDInsight](./media/hdinsight-apache-kafka-get-started/set-hdinsight-storage-account.png)

6. Dans le panneau **Résumé**, passez en revue la configuration du cluster. Utilisez les liens __Modifier__ pour modifier les éventuels paramètres incorrects. Pour finir, cliquez sur le bouton __Créer__ pour créer le cluster.
   
    ![Résumé de la configuration du cluster](./media/hdinsight-apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > La création du cluster peut prendre jusqu’à 20 minutes.

## <a name="connect-to-the-cluster"></a>Connexion au cluster

À partir de votre cluster, utilisez SSH pour la connexion au cluster. Si vous utilisez Linux, Unix, MacOS ou Bash sur Windows 10, utilisez la commande suivante :

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

Remplacez **SSHUSER** par le nom d’utilisateur SSH que vous avez fourni lors de la création du cluster. Remplacez **CLUSTERNAME** par le nom de votre cluster.

Lorsque vous y êtes invité, entrez le mot de passe utilisé pour le compte SSH.

Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

##<a id="getkafkainfo"></a>Obtention des informations sur Zookeeper et l’hôte du répartiteur

Lorsque vous travaillez avec Kafka, vous devez connaître deux valeurs d’hôte : les hôtes *Zookeeper* et de *répartiteur*. Ces hôtes sont utilisés avec l’API Kafka et la plupart des utilitaires fournis avec Kafka.

Utilisez les étapes suivantes pour créer des variables d’environnement contenant des informations sur l’hôte. Ces variables d’environnement sont utilisées dans les étapes de ce document.

1. À partir d’une connexion SSH avec le cluster, utilisez la commande suivante pour installer l’utilitaire `jq`. Cet utilitaire est utilisé pour analyser des documents JSON et est utile lors de la récupération des informations sur l’hôte du répartiteur :
   
    ```bash
    sudo apt -y install jq
    ```

2. utilisez les commandes suivantes pour définir les variables d’environnement avec les informations récupérées à partir d’Ambari. Remplacez __KAFKANAME__ par le nom du cluster Kafka. Remplacez __PASSWORD__ par le mot de passe de connexion (admin) que vous avez utilisé lors de la création du cluster.

    ```bash
    export KAFKAZKHOSTS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

    export KAFKABROKERS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Le texte suivant constitue un exemple du contenu de `$KAFKAZKHOSTS` :
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    Le texte suivant constitue un exemple du contenu de `$KAFKABROKERS` :
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
    > [!WARNING]
    > Les informations renvoyées par cette session ne sont pas toujours précises. Si vous redimensionnez le cluster, de nouveaux répartiteurs sont ajoutés ou supprimés. Si une défaillance se produit et si un nœud est remplacé, le nom d’hôte du nœud peut changer. 
    > 
    > Vous devez récupérer les informations des hôtes Zookeeper et du répartiteur peu de temps avant de les utiliser pour vous assurer de leur validité.

## <a name="create-a-topic"></a>Création d'une rubrique

Kafka stocke les flux de données dans des catégories appelées *rubriques*. À partir d’une connexion SSH à un nœud principal de cluster, utilisez un script fourni avec Kafka pour créer une rubrique :

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Cette commande se connecte à Zookeeper en utilisant les informations d’hôte stockées dans `$KAFKAZKHOSTS`, puis crée une rubrique Kafka nommée **test**. Vous pouvez vérifier que la rubrique a été créée à l’aide du script suivant pour répertorier les rubriques :

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

La sortie de cette commande répertorie les rubriques Kafka, contenant la rubrique **test**.

## <a name="produce-and-consume-records"></a>Produire et consommer des enregistrements

Kafka stocke les *enregistrements* dans des rubriques. Les enregistrements sont produits par des *producteurs*, et utilisés par des *consommateurs*. Les producteurs récupèrent des enregistrements à partir des *répartiteurs* Kafka. Chaque nœud Worker dans votre cluster HDInsight est un répartiteur Kafka.

Pour stocker les enregistrements dans la rubrique test créée précédemment, puis les lire à l’aide d’un consommateur, procédez comme suit :

1. À partir de la session SSH, utilisez un script fourni avec Kafka pour écrire des enregistrements dans la rubrique :
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    L’invite ne s’affichera plus après cette commande. À la place, tapez quelques messages de texte, puis utilisez la combinaison **Ctrl + C** pour arrêter l’envoi à la rubrique. Chaque ligne est envoyée en tant qu’enregistrement distinct.

2. Utilisez un script fourni avec Kafka pour lire les enregistrements à partir de la rubrique :
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
    ```
   
    Cela permet de récupérer les enregistrements à partir de la rubrique et de les afficher. L’utilisation de `--from-beginning` indique au consommateur de démarrer à partir du début du flux, de manière à récupérer tous les enregistrements.

3. Utilisez la combinaison __Ctrl + C__ pour arrêter le consommateur.

## <a name="producer-and-consumer-api"></a>API de producteur et de consommateur

Vous pouvez également produire et consommer des enregistrements par programme à l’aide des [API Kafka](http://kafka.apache.org/documentation#api). Utilisez les étapes suivantes pour télécharger et générer un producteur et un consommateur basés sur Java :

1. Téléchargez les exemples à partir de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Pour l’exemple de producteur/consommateur, utilisez le projet dans le répertoire `Producer-Consumer`. Il contient les classes suivantes :
   
    * **Exécuter** : démarre le consommateur ou le producteur.

    * **Producer** : stocke 1 000 000 d’enregistrements dans la rubrique.

    * **Consumer** : lit les enregistrements à partir de la rubrique.

2. À partir de la ligne de commande dans votre environnement de développement, modifiez les répertoires d’accès à l’emplacement du répertoire `Producer-Consumer` de l’exemple, puis utilisez la commande suivante pour créer un package jar :
   
    ```
    mvn clean package
    ```
   
    Cette commande crée un répertoire nommé `target`, qui contient un fichier nommé `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Utilisez les commandes suivantes pour copier le fichier `kafka-producer-consumer-1.0-SNAPSHOT.jar` dans votre cluster HDInsight :
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Remplacez **SSHUSER** par l’utilisateur SSH pour votre cluster, et remplacez **CLUSTERNAME** par le nom de votre cluster. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.

4. Une fois que la commande `scp` a fini de copier le fichier, connectez-vous au cluster à l’aide de SSH, puis utilisez les éléments suivants pour écrire des enregistrements dans la rubrique test créée précédemment.
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS
    ```
   
    Cette commande démarre le producteur et écrit des enregistrements. Un compteur s’affiche afin de voir le nombre d’enregistrements qui ont été écrits.

    > [!NOTE]
    > Si vous recevez une erreur de refus d’autorisation, pour rendre le fichier exécutable, utilisez la commande suivante : ```chmod +x kafka-producer-consumer.jar```

5. Une fois le processus terminé, utilisez la commande suivante pour lire à partir de la rubrique :
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    Les enregistrements lus et le nombre d’enregistrements s’affichent. Il est possible qu’un peu plus de 1 000 000 d’enregistrements soient consignés, car vous avez envoyé plusieurs enregistrements à la rubrique à l’aide d’un script dans une étape précédente.

6. Utilisez __Ctrl + C__ pour quitter le consommateur.

### <a name="multiple-consumers"></a>Consommateurs multiples

Un concept important avec Kafka est que les consommateurs utilisent un groupe de consommateurs (défini par un id de groupe) lors de la lecture d’enregistrements. L’utilisation du même groupe avec plusieurs consommateurs permet des lectures à charge équilibrée à partir d’une rubrique. Chaque consommateur dans le groupe reçoit une partie des enregistrements. Pour voir ce processus en action, procédez comme suit :

1. Ouvrez une nouvelle session SSH vers le cluster, afin de disposer de deux sessions. Dans chaque session, utilisez les éléments suivants pour démarrer un consommateur avec le même id de groupe de consommateurs :
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```

    > [!NOTE]
    > Étant donné qu’il s’agit d’une nouvelle session SSH, vous devez utiliser les commandes dans la section [Obtention des informations sur Zookeeper et l’hôte du répartiteur](#getkafkainfo) pour définir `$KAFKABROKERS`.

2. Observez chaque session compter les enregistrements reçus de la rubrique. Le total des deux sessions doit être identique à celui reçu précédemment à partir d’un seul consommateur.

La consommation par les clients au sein du même groupe est gérée par le biais des partitions de la rubrique. Pour la rubrique `test` créée précédemment, il y a huit partitions. Si vous ouvrez huit sessions SSH et lancez un consommateur dans toutes les sessions, chaque consommateur lit les enregistrements à partir d’une seule partition de la rubrique.

> [!IMPORTANT]
> Il ne peut pas y avoir plus d’instances de consommateurs dans un groupe de consommateurs que de partitions. Dans cet exemple, un groupe de consommateurs peut contenir jusqu’à 8 consommateurs puisque c’est le nombre de partitions de la rubrique. Vous pouvez également disposer de plusieurs groupes de consommateurs, chacun ne dépassant pas 8 consommateurs.

Les enregistrements stockés dans Kafka sont stockés dans l’ordre de réception dans une partition. Pour obtenir la livraison chronologique des enregistrements *dans une partition*, créez un groupe de consommateurs où le nombre d’instances de consommateurs correspond au nombre de partitions. Pour obtenir la livraison chronologique des enregistrements *dans la rubrique*, créez un groupe de consommateurs avec une seule instance de consommateur.

## <a name="streaming-api"></a>API de diffusion en continu

L’API de diffusion en continu a été ajoutée à Kafka dans la version 0.10.0 ; les versions antérieures s’appuient sur Apache Spark ou Storm pour le traitement des flux de données.

1. Si ce n’est pas déjà fait, téléchargez les exemples de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) dans votre environnement de développement. Pour l’exemple de diffusion en continu, utilisez le projet dans le répertoire `streaming`.
   
    Ce projet contient uniquement une classe, `Stream`, qui lit les enregistrements à partir de la rubrique `test` créée précédemment. Il compte les mots lus et émet chaque mot et quantité dans une rubrique nommée `wordcounts`. La rubrique `wordcounts` est créée dans une étape ultérieure de cette section.

2. À partir de la ligne de commande dans votre environnement de développement, modifiez les répertoires d’accès à l’emplacement du répertoire `Streaming`, puis utilisez la commande suivante pour créer un package jar :
   
    ```
    mvn clean package
    ```
   
    Cette commande crée un répertoire nommé `target`, qui contient un fichier nommé `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Utilisez les commandes suivantes pour copier le fichier `kafka-streaming-1.0-SNAPSHOT.jar` dans votre cluster HDInsight :
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Remplacez **SSHUSER** par l’utilisateur SSH pour votre cluster, et remplacez **CLUSTERNAME** par le nom de votre cluster. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.

4. Une fois que la commande `scp` a fini de copier le fichier, connectez-vous au cluster à l’aide de SSH, puis utilisez la commande suivante pour créer la rubrique `wordcounts` :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

5. Ensuite, démarrez le processus de diffusion en continu à l’aide de la commande suivante :
   
    ```bash
    ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
    ```
   
    Cette commande démarre le processus de diffusion en continu en arrière-plan.

6. Utilisez la commande suivante pour envoyer des messages à la rubrique `test`. Ces messages sont traités par l’exemple de diffusion en continu :
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &
    ```

7. Utilisez la commande suivante pour afficher la sortie écrite dans la rubrique `wordcounts` par le processus de diffusion en continu :
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
    ```
   
    > [!NOTE]
    > Pour afficher les données, vous devez indiquer au consommateur d’imprimer la clé et le désérialiseur à utiliser pour la clé et la valeur. Le nom de la clé est le mot, et la valeur de clé contient le nombre.
   
    Le résultat ressemble au texte suivant :
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
        a       13805
        snow    13637
   
    > [!NOTE]
    > Le nombre augmente à chaque fois qu’un mot est rencontré.

7. Utilisez la combinaison __Ctrl + C__ pour quitter le client, puis utilisez la commande `fg` pour passer la tâche de diffusion en continu de l’arrière-plan au premier plan. Utilisez la combinaison __Ctrl + C__ pour le quitter également.

## <a name="delete-the-cluster"></a>Suppression du cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris les bases de l’utilisation d’Apache Kafka sur HDInsight. Consultez les articles suivants pour en savoir plus sur l’utilisation de Kafka :

* [Documentation Apache Kafka](http://kafka.apache.org/documentation.html) à l’adresse kafka.apache.org.
* [Utilisation de MirrorMaker pour créer un réplica de Kafka sur HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Use Apache Storm with Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)
* [Use Apache Spark with Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md) (Utilisation d’Apache Spark avec Kafka sur HDInsight)


