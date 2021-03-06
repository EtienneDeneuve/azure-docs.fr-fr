---
title: Mise en miroir Apache Kafka sur un cluster HDInsight | Microsoft Docs
description: "Découvrez comment utiliser la fonctionnalité de mise en miroir de Kafka afin de conserver un réplica Kafka sur un cluster HDInsight par la mise en miroir de sujets vers un cluster secondaire."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 281901045723266128db9069a6f244acb183ae80
ms.lasthandoff: 03/25/2017

---
# <a name="use-mirrormaker-to-create-a-replica-of-a-kafka-on-hdinsight-cluster-preview"></a>MirrorMaker permet de créer un réplica Kafka sur un cluster HDInsight (version préliminaire)

Apache Kafka inclut une fonctionnalité de mise en miroir, ce qui vous permet de répliquer des sujets d’un cluster Kafka vers un autre. Par exemple, il est possible de répliquer les enregistrements au sein du cluster Kafka dans différentes régions Azure.

La mise en miroir peut être exécutée sous la forme d’un processus continu ou être utilisée par intermittence comme une méthode de migration des données d’un cluster à l’autre.

> [!WARNING]
> La mise en miroir ne doit pas être considérée comme un moyen de bénéficier d’une tolérance de pannes. Le décalage aux éléments d’un sujet diffère entre le cluster source et le cluster de destination : les clients ne peuvent donc pas utiliser indifféremment les deux.
> 
> Si la tolérance de pannes vous préoccupe, définissez la réplication pour les sujets au sein de votre cluster. Pour plus d’informations, consultez [Prise en main de Kafka sur HDInsight](hdinsight-apache-kafka-get-started.md).

## <a name="prerequisites"></a>Composants requis

* Un réseau virtuel Azure : le cluster source et le cluster de destination Kafka doivent être capables de communiquer directement entre eux. HDInsight n’expose pas d’API Kafka publiquement sur Internet : le cluster source et le cluster de destination doivent donc se trouver sur le même réseau virtuel Azure.

* Deux clusters Kafka : ce document utilise un modèle Azure Resource Manager afin de créer deux clusters Kafka sur HDInsight au sein d’un réseau virtuel Azure.

## <a name="how-does-mirroring-work"></a>Comment la mise en miroir fonctionne-t-elle ?

La mise en miroir se fait à l’aide de l’outil MirrorMaker (qui fait partie d’Apache Kafka) : cet outil permet d’utiliser les enregistrements de sujets du cluster source et de créer une copie locale sur le cluster de destination. MirrorMaker utilise un (ou plusieurs) *consommateurs* qui lisent le cluster source, ainsi qu’un *producteur* qui écrit dans le cluster local (de destination).

Le diagramme suivant illustre le processus de mise en miroir :

![Diagramme du processus de mise en miroir](./media/hdinsight-apache-kafka-mirroring/kafka-mirroring.png)

Le cluster source et le cluster de destination peuvent comporter un nombre différent de nœuds et de partitions, et les décalages dans les sujets ne sont pas forcément les mêmes. La mise en miroir conserve la valeur clé utilisée pour le partitionnement afin de préserver l’ordre d’enregistrement en fonction de la clé.

### <a name="mirroring-between-networks"></a>Mise en miroir entre les réseaux

Si vous avez besoin d’effectuer une mise en miroir entre des clusters Kafka sur des réseaux différents, vous devez tenir compte des points suivants :

* **Passerelles** : les réseaux doivent être capables de communiquer au niveau TCPIP.

* **Résolution de noms** : les clusters Kafka de chaque réseau doivent être capables de se connecter entre eux à l’aide de noms d’hôte. Cela peut nécessiter la configuration d’un serveur DNS (Domain Name System) dans chaque réseau pour l’acheminement des demandes vers les autres réseaux. 
  
    Lorsque vous créez un réseau virtuel Azure, au lieu d’utiliser le DNS automatique fourni avec le réseau, vous devez spécifier un serveur DNS personnalisé et l’adresse IP du serveur. Après avoir créé le réseau virtuel, vous devez ensuite créer une machine virtuelle Azure qui utilise cette adresse IP, puis y installer et configurer le logiciel DNS.
  
    > [!WARNING]
    > Créez et configurez le serveur DNS personnalisé avant d’installer HDInsight sur le réseau virtuel. Aucune configuration supplémentaire n’est requise pour HDInsight dans le cadre de l’utilisation du serveur DNS configuré pour le réseau virtuel.

Pour plus d’informations sur la connexion de deux réseaux virtuels Azure, consultez [Configurer une connexion de réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Création de clusters Kafka

Apache Kafka sur HDInsight ne donne pas accès au service Kafka par le biais de l’Internet public. Tout ce qui communique avec Kafka doit se trouver sur le même réseau virtuel Azure que les nœuds du cluster Kafka. Pour cet exemple, le cluster source et le cluster de destination Kafka sont situés dans un réseau virtuel Azure. Le diagramme suivant illustre le flux de communication entre les clusters :

![Diagramme du cluster source et du cluster de destination Kafka dans un réseau virtuel Azure](./media/hdinsight-apache-kafka-mirroring/spark-kafka-vnet.png)

> [!NOTE]
> Bien que Kafka soit lui-même limité à la communication au sein du réseau virtuel, d’autres services sur le cluster comme SSH et Ambari sont accessibles sur Internet. Pour plus d’informations sur les ports publics disponibles avec HDInsight, consultez [Ports et URI utilisés par HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Même si vous pouvez créer un réseau virtuel Azure et des clusters Kafka manuellement, il est plus facile d’utiliser un modèle Azure Resource Manager. Utilisez les étapes suivantes pour déployer un réseau virtuel Azure et des clusters Kafka sur votre abonnement Azure.

1. Utilisez le bouton suivant pour vous connecter à Azure et ouvrir le modèle dans le portail Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Le modèle Azure Resource Manager se trouve à l’adresse **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet.json**.

2. Utilisez les informations suivantes pour remplir les entrées sur le panneau **déploiement personnalisé** :
    
    ![Déploiement HDInsight personnalisé](./media/hdinsight-apache-kafka-mirroring/parameters.png)
    
    * **Groupe de ressources** : créez un groupe de ressources ou sélectionnez-en un. Ce groupe contient le cluster HDInsight.

    * **Emplacement** : choisissez un emplacement proche de vous géographiquement. Cet emplacement doit correspondre à l’emplacement dans la section __PARAMÈTRES__.
     
    * **Nom de cluster de base** : cette valeur sera utilisée comme nom de base pour les clusters Kafka. Par exemple, l’entrée de **hdi** crée des clusters nommés **source-hdi** et **dest-hdi**.

    * **Nom d’utilisateur du cluster** : nom utilisateur Admin pour le cluster source et le cluster de destination Kafka.

    * **Mot de passe du cluster** : mot de passe Admin pour le cluster source et le cluster de destination Kafka.

    * **Nom d’utilisateur SSH** : utilisateur SSH permettant de créer des clusters sources et des clusters de destination Kafka.

    * **Mot de passe SSH** : mot de passe de l’utilisateur SSH pour les clusters sources et les clusters de destination Kafka.

    * **Emplacement** : région dans laquelle les clusters seront créés.

3. Passez en revue les **termes et conditions**, puis cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**.

4. Pour finir, cochez **Épingler au tableau de bord**, puis sélectionnez **Acheter**. La création des clusters prend environ 20 minutes.

Une fois les ressources créées, vous êtes redirigé vers un panneau pour le groupe de ressources contenant les clusters et le tableau de bord Web.

![Volet du groupe de ressources pour les clusters et le réseau virtuel](./media/hdinsight-apache-kafka-mirroring/groupblade.png)

> [!IMPORTANT]
> Les noms des clusters HDInsight sont **source-BASENAME** et **dest-BASENAME**, où BASENAME est le nom que vous avez fourni pour le modèle. Vous utilisez ces noms dans les étapes ultérieures, lors de la connexion aux clusters.

## <a name="create-topics"></a>Création de sujets

1. Connectez-vous au cluster **source** à l’aide de SSH :
   
        ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
   
    Remplacez **sshuser** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **BASENAME** par le nom de base que vous avez utilisé lors de la création du cluster.
   
    Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilisez la commande suivante pour rechercher les hôtes Zookeeper, définir la variable `SOURCE_ZKHOSTS`, puis créer plusieurs sujets nommés `testtopic` :
   
    ```bash
    SOURCE_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. Exécutez la commande suivante pour vérifier la création du sujet :
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```
   
    La réponse contient `testtopic`.

4. Utilisez la commande suivante afin d’afficher les informations sur l’hôte Zookeeper pour ce cluster (**source**) :
   
    ```bash
    echo $SOURCE_ZKHOSTS
    ```
   
 Cette commande renvoie des informations semblables au texte suivant :
   
       zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk6-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181
   
 Enregistrez ces informations. Elles seront utilisées dans la section suivante.

## <a name="configure-mirroring"></a>Configuration de la mise en miroir

1. Connectez-vous au cluster **de destination** à l’aide d’une autre session SSH :
   
        ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
   
    Remplacez **sshuser** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **BASENAME** par le nom de base que vous avez utilisé lors de la création du cluster.
   
    Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilisez la commande suivante pour créer un fichier `consumer.properties` décrivant comment communiquer avec le cluster **source** :
   
    ```bash
    nano consumer.properties
    ```
   
    Utilisez les données suivantes comme contenu du fichier `consumer.properties` :
   
        zookeeper.connect=SOURCE_ZKHOSTS
        group.id=mirrorgroup
   
    Remplacez **SOURCE_ZKHOSTS** par les informations sur l’hôte Zookeeper du cluster **source**.
   
    Ce fichier détaille les informations de consommateur à utiliser lors de la lecture du cluster source Kafka. Pour plus d’informations sur la configuration du consommateur, consultez [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) (Configurations de consommateur) sur kafka.apache.org.
   
    Utilisez **Ctrl+X**, **Y**, puis Entrée pour enregistrer le fichier.

3. Avant de configurer le producteur qui communique avec le cluster de destination, vous devez trouver les hôtes du répartiteur pour le cluster **de destination**. Pour récupérer ces informations, utilisez les commandes suivantes :
   
    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    echo $DEST_BROKERHOSTS
    ```
   
    Ces commandes renvoient des informations semblables aux informations suivantes :
   
        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. Utilisez la commande suivante pour créer un fichier `producer.properties` décrivant comment communiquer avec le cluster **de destination** :

    ```bash
    nano producer.properties
    ```

    Utilisez les données suivantes comme contenu du fichier `producer.properties` :
   
        bootstrap.servers=DEST_BROKERS
        compression.type=none
   
    Remplacez **DEST_BROKERS** par les informations sur le répartiteur de l’étape précédente. 
   
    Pour plus d’informations sur la configuration du producteur, consultez [Producer Configs](https://kafka.apache.org/documentation#producerconfigs) (Configurations de producteur) sur kafka.apache.org.

## <a name="start-mirrormaker"></a>Lancement de MirrorMaker

1. Dans la connexion SSH sur le cluster **de destination**, utilisez la commande suivante pour lancer le processus MirrorMaker :
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```
   
    Les paramètres utilisés dans cet exemple sont :
   
    * **--consumer.config** : spécifie le fichier qui contient les propriétés du consommateur. Ces propriétés permettent de créer un consommateur qui lit le cluster *source* Kafka.

    * **--producer.config** : spécifie le fichier qui contient les propriétés du producteur. Ces propriétés permettent de créer un producteur qui écrit dans le cluster *de destination* Kafka.

    * **--whitelist** : liste de sujets que MirrorMaker réplique du cluster source vers le cluster de destination.
    
    * **--num.streams** : nombre de threads de consommateur à créer.
     
    Au démarrage, MirrorMaker renvoie des informations semblables au texte suivant :
        
    ```{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}  metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}  metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. From the SSH connection to the **source** cluster, use the following command to start a producer and send messages to the topic:
    
    ```bash
    sudo apt -y install jq
    SOURCE_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

 Lorsque vous arrivez sur une ligne vide avec un curseur, tapez quelques messages texte. Ceux-ci sont envoyés à la rubrique sur le cluster **source**. Lorsque vous avez terminé, utilisez **Ctrl + C** pour terminer le processus de production.

3. Dans la connexion SSH au cluster **de destination**, utilisez **Ctrl + C** pour mettre fin au processus MirrorMaker. Puis, utilisez les commandes suivantes pour vérifier que la rubrique `testtopic` a été créée et que les données de la rubrique ont été répliquées vers ce miroir :
    
    ```bash
    DEST_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $DEST_ZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```
    
  La liste des rubriques inclut désormais `testtopic`, qui est créé lorsque MirrorMaster reflète la rubrique du cluster source vers la destination. Les messages récupérés de la rubrique sont les mêmes que ceux entrés dans le cluster source.

## <a name="delete-the-cluster"></a>Suppression du cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Étant donné que les étapes décrites dans ce document créent deux clusters dans le même groupe de ressources Azure, vous pouvez supprimer le groupe de ressources du portail Azure. La suppression du groupe source supprime toutes les ressources créées par la suite dans ce document, le réseau virtuel Azure et le compte de stockage utilisé par les clusters.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à utiliser MirrorMaker pour créer un réplica d’un cluster Kafka. Utilisez les liens suivants pour découvrir d’autres façons de travailler avec Kafka :

* [Documentation d’Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) sur cwiki.apache.org.
* [Prise en main d’Apache Kafka sur HDInsight](hdinsight-apache-kafka-get-started.md)
* [Use Apache Spark with Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md) (Utilisation d’Apache Spark avec Kafka sur HDInsight)
* [Use Apache Storm with Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)


