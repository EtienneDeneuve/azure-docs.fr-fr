---
title: Topologies Apache Storm avec Visual Studio et C# | Microsoft Docs
description: "Apprenez à créer des topologies Storm dans C# en créant une topologie de statistiques simple dans Visual Studio à l’aide des outils HDInsight pour Visual Studio."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 380d804f-a8c5-4b20-9762-593ec4da5a0d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: c2a92e3be7616d241eba3c6690c8f10326d8004c
ms.lasthandoff: 03/07/2017


---
# <a name="develop-c-topologies-for-apache-storm-on-hdinsight-using-hadoop-tools-for-visual-studio"></a>Développement de topologies C# pour Apache Storm sur HDInsight à l’aide des outils Hadoop pour Visual Studio

Apprenez à créer une topologie Storm C# à l’aide des outils HDInsight pour Visual Studio. Ce document vous guide dans le processus de création d’un projet Storm dans Visual Studio, de test local et de déploiement vers Apache Storm sur un cluster HDInsight.

Vous apprendrez également à créer des topologies hybrides qui utilisent des composants Java et C#.

> [!IMPORTANT]
> Bien que les étapes décrites dans ce document reposent sur un environnement de développement Windows avec Visual Studio, le projet compilé peut être soumis à un cluster HDInsight basé sur Linux ou sur Windows. __Seuls les clusters basés sur Linux créés après le 28/10/2016 prennent en charge les topologies SCP.NET__.
> 
> Pour utiliser une topologie C# avec un cluster basé sur Linux, vous devez mettre à jour le package NuGet de Microsoft.SCP.Net.SDK utilisé par votre projet vers la version 0.10.0.6 ou une version supérieure. La version du package doit également correspondre à la version principale de Storm installée sur HDInsight. Par exemple, Storm sur les versions 3.3 et 3.4 de HDInsight utilise Storm 0.10.x, tandis que HDInsight 3.5 utilise Storm 1.0.x.
> 
> Les topologies C# sur les clusters basés sur Linux doivent utiliser .NET 4.5, et utiliser Mono pour s’exécuter sur le cluster HDInsight. La plupart des éléments fonctionnent, vous devez toutefois consulter le document [Compatibilité Mono](http://www.mono-project.com/docs/about-mono/compatibility/) pour identifier les éventuelles incompatibilités.


## <a name="prerequisites"></a>Composants requis

* [Java](https://java.com) 1.7 ou version ultérieure sur votre environnement de développement. Java sert à empaqueter la topologie lorsqu’elle est envoyée au cluster HDInsight.

  * La variable d’environnement **JAVA_HOME** doit pointer vers le répertoire qui contient Java.
  * Le répertoire **%JAVA_HOME%/bin** doit se trouver dans le chemin d’accès

* L’une des versions suivantes de Visual Studio :
  
  * Visual Studio 2012 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)
  * Visual Studio 2013 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)
  * Visual Studio 2015 ou [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)
  * Visual Studio 2017 (toute édition)

* Azure SDK 2.9.5 ou version ultérieure

* HDInsight Tools pour Visual Studio : consultez la page [Prise en main de HDInsight Tools pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) pour installer et configurer les outils HDInsight pour Visual Studio.
  
  > [!NOTE]
  > Les outils HDInsight pour Visual Studio ne sont pas pris en charge sur Visual Studio Express.

* Cluster Apache Storm sur HDInsight : consultez [Prise en main d'Apache Storm sur HDInsight](hdinsight-apache-storm-tutorial-get-started.md) pour connaître les étapes de création d'un cluster.

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="templates"></a>Modèles

Les outils HDInsight pour Visual Studio fournissent les modèles suivants :

| Type de projet | Illustre le |
| --- | --- |
| Application Storm |Un projet de topologie Storm vide |
| Exemple d’enregistreur SQL Azure Storm |Écriture dans la base de données SQL Azure |
| Exemple de lecteur DocumentDB Storm |Lecture d’Azure DocumentDB |
| Exemple d’enregistreur DocumentDB Storm |Écriture dans Azure DocumentDB |
| Exemple de lecteur EventHub Storm |Lecture d’Azure Event Hubs |
| Exemple d’enregistreur EventHub Storm |Écriture dans Azure Event Hubs |
| Exemple de lecteur HBase Storm |Lecture des clusters HBase sur HDInsight |
| Exemple d’enregistreur HBase Storm |Écriture dans les clusters HBase sur HDInsight |
| Exemple Storm hybride |Utilisation d’un composant Java |
| Exemple Storm |Une topologie de base des statistiques |

Dans les étapes de ce document, vous allez utiliser le type de projet Application Storm de base pour créer une topologie.

### <a name="hbase-templates-notes"></a>Remarques sur les modèles HBase

Les modèles de lecteur et d’enregistreur HBase utilisent l’API REST HBase pour communiquer avec un cluster HBase sur HDInsight, et non l’API Java HBase.

### <a name="eventhub-templates-notes"></a>Remarques sur les modèles EventHub

> [!IMPORTANT]
> Le composant spout EventHub basé sur Java inclus avec le modèle de lecteur EventHub ne fonctionne pas avec Storm sur HDInsight version 3.5. Au lieu de cela, utilisez le composant spout EventHub issu de [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar).

Pour un exemple de topologie qui utilise ce composant et fonctionne avec Storm sur HDInsight 3.5, consultez [https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Création d’une topologie C#

1. Si vous n’avez pas encore installé la dernière version des outils HDInsight pour Visual Studio, consultez [Prise en main de HDInsight Tools pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Ouvrez Visual Studio, sélectionnez **Fichier ** > **Nouveau**, puis **Projet**.

3. Dans l’écran **Nouveau projet**, développez **Installé** > **Modèles**, puis sélectionnez **Azure Data Lake**. Dans la liste des modèles, sélectionnez **Application Storm**. En bas de la boîte de dialogue, entrez **Statistiques** comme nom d’application.
   
    ![image](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4. Une fois le projet créé, vous devez avoir les fichiers suivants :
   
   * **Program.cs** : ce fichier définit la topologie de votre projet. Par défaut, une topologie consistant en un seul spout et un seul bolt est créée.

   * **Spout.cs**: un spout d’exemple émettant des nombres aléatoires.

   * **Bolt.cs**: un bolt d’exemple qui conserve un décompte des nombres émis par le spout.
     
     Dans le cadre de la création du projet, le [package SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) le plus récent est téléchargé à partir de NuGet.
     
     [!INCLUDE [scp.net version important](../../includes/hdinsight-storm-scpdotnet-version.md)]

Dans les sections suivantes, vous allez modifier ce projet en une application de statistiques de base.

### <a name="implement-the-spout"></a>Implémenter le spout

1. Ouvrez **Spout.cs**. Les spouts sont utilisés pour lire des données dans une topologie à partir d’une source externe. Les principaux composants d’un spout sont :
   
   * **NextTuple**: appelé par Storm lorsque le spout est autorisé à émettre de nouveaux tuples.

   * **Accusé** (topologie transactionnelle uniquement) : gère les accusés de réception initiés par d’autres composants dans la topologie, pour les tuples envoyés depuis le spout. Un accusé de réception de tuple permet au spout de savoir qu’il a été correctement traité par les composants en aval.

   * **Fail** (topologie transactionnelle uniquement) : gère les tuples qui ne parviennent pas à traiter d’autres composants de la topologie. L’implémentation d’une méthode Fail permet d’émettre le tuple à nouveau afin qu’il puisse être traité une nouvelle fois.

2. Remplacez le contenu de la classe **Spout** par le texte suivant. Ce spout émet une phrase au hasard dans la topologie.
    
    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```
   
    Prenez un moment pour lire les commentaires afin de comprendre ce que fait ce code.

### <a name="implement-the-bolts"></a>Implémentation des bolts

1. Supprimez le fichier **Bolt.cs** du projet.

2. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Ajouter** > **Nouvel élément**. Dans la liste, sélectionnez **Bolt Storm**, entrez **Splitter.cs** comme nom. Répétez ce processus pour créer un deuxième bolt nommé **Counter.cs**.
   
   * **Splitter.cs**: implémente un bolt qui fractionne les phrases en mots et émet un nouveau flux de mots.

   * **Counter.cs**: implémente un bolt qui compte chaque mot et émet un nouveau flux de mots ainsi que le décompte de chaque mot.
     
     > [!NOTE]
     > Bien que ces bolts lisent et écrivent dans des flux, vous pouvez également utiliser un bolt pour communiquer avec une base de données, un service, etc.

3. Ouvrez **Splitter.cs**. Il n’a qu’une seule méthode par défaut : **Execute**. Elle est appelée lorsque le bolt reçoit un tuple à traiter. Ici, vous pouvez lire et traiter des tuples entrants et émettre des tuples sortants.

4. Remplacez le contenu de la classe **Splitter** par le code suivant :
    
    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```
   
    Prenez un moment pour lire les commentaires afin de comprendre ce que fait ce code.

5. Ouvrez le fichier **Counter.cs** et remplacez le code existant par le suivant :
    
    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```
   
    Prenez un moment pour lire les commentaires afin de comprendre ce que fait ce code.

### <a name="define-the-topology"></a>Définition de la topologie

Les spouts et les bolts sont organisés dans un graphique, qui définit la circulation des données entre les composants. Dans cette topologie, le graphique est le suivant :

![image de l’organisation des composants](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Des phrases sont émises par le spout, puis distribuées à des instances du bolt de fractionnement. Le bolt de fractionnement fractionne les phrases en mots, qui sont distribués au bolt de décompte.

Étant donné que le nombre de mots est conservé localement dans l’instance de décompte Counter, il convient de s’assurer que des mots donnés sont transmis à la même instance de bolt de décompte Counter, afin de garantir que chaque instance suit un mot donné. Comme le bolt de fractionnement ne gère aucun état, il n’est pas important de savoir quelle instance du fractionnement reçoit quelle phrase.

Ouvrez **Program.cs**. La méthode importante ici est **GetTopologyBuilder**, qui est utilisée pour définir la topologie qui est envoyée à Storm. Remplacez le contenu de **GetTopologyBuilder** par le code suivant pour implémenter la topologie décrite précédemment :

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

Prenez un moment pour lire les commentaires afin de comprendre ce que fait ce code.

## <a name="submit-the-topology"></a>Envoi de la topologie

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Envoyer à Storm sur HDInsight**.
   
   > [!NOTE]
   > Si vous y êtes invité, entrez les informations d’identification de connexion pour votre abonnement Azure. Si vous disposez de plusieurs abonnements, connectez-vous à celui qui contient votre cluster Storm dans HDInsight.

2. Sélectionnez votre Storm sur le cluster HDInsight dans la liste déroulante **Cluster Storm**, puis sélectionnez **Envoyer**. Vous pouvez contrôler si l’envoi est réussi ou non à l’aide de la fenêtre **Sortie** .

3. Une fois la topologie envoyée avec succès, les **Topologies Storm** du cluster doivent apparaître. Sélectionnez la topologie **WordCount** à partir de la liste pour afficher des informations sur la topologie en cours d’exécution.
   
   > [!NOTE]
   > Vous pouvez également afficher les **Topologies Storm** à partir de **l’Explorateur de serveurs** en développant **Azure** > **HDInsight**, puis en cliquant avec le bouton droit sur Storm dans un cluster HDInsight et en sélectionnant **Afficher les topologies Storm**.

    Pour afficher des informations sur les composants de la topologie, double-cliquez sur le composant dans le diagramme.

4. À partir de l’affichage **Résumé de la topologie**, cliquez sur **Supprimer** pour arrêter la topologie.
   
   > [!NOTE]
   > Les topologies Storm poursuivent leur exécution jusqu'à ce qu'elles soient désactivées ou que le cluster soit supprimé.

## <a name="transactional-topology"></a>Topologie transactionnelle

La précédente ci-dessus est non transactionnelle. Les composants de la topologie n’implémentent aucune fonctionnalité de relecture des messages. Pour un exemple de topologie transactionnelle, créez un projet et sélectionnez **Exemple Storm** comme type de projet.

Les topologies transactionnelles implémentent les opérations suivantes pour prendre en charge la relecture des données :

* **Mise en cache des métadonnées**: le spout doit stocker les métadonnées relatives aux données émises, afin que les données puissent être récupérées et émises à nouveau en cas de défaillance. Dans la mesure où les données émises par l’exemple sont petites, les données brutes de chaque tuple sont stockées dans un dictionnaire pour la relecture.

* **Accusé** : chaque bolt de la topologie peut appeler `this.ctx.Ack(tuple)` afin de signaler qu’il a traité un tuple avec succès. Lorsque tous les bolts ont signalé le traitement du tuple, la méthode `Ack` du spout est appelée. La méthode `Ack` permet au spout de supprimer les données mises en cache pour la relecture.

* **Échec** : chaque bolt peut appeler `this.ctx.Fail(tuple)` pour indiquer que le traitement d’un tuple a échoué. L’échec se propage à la méthode `Fail` du spout, où le tuple peut être relu à l’aide des métadonnées mises en cache.

* **ID de séquence** : lors de l’émission d’un tuple, un ID de séquence unique peut être spécifié. Cette valeur identifie le tuple pour le traitement de la relecture (accusé de réception et échec). Par exemple, le spout dans le projet **Exemple Storm** utilise les éléments suivants lors de l’émission de données :
  
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);
  
    Ce code émet un tuple contenant une phrase dans le flux par défaut, avec la valeur d’ID de séquence contenue dans **lastSeqId**. Dans cet exemple, **lastSeqId** est incrémenté pour chaque tuple émis.

Comme illustré dans le project **Exemple Storm** , un composant peut être défini comme transactionnel ou non lors de l’exécution, en fonction de la configuration.

## <a name="hybrid-topology"></a>Topologie hybride

Les outils HDInsight pour Visual Studio peuvent également être utilisés pour créer des topologies hybrides, où certains composants sont en C# et d’autres en Java.

Pour un exemple de topologie hybride, créez un projet, puis sélectionnez **Exemple Storm hybride**. Cet exemple de type illustre les concepts suivants :

* **Spout Java** et **bolt C#** : définis dans **HybridTopology_javaSpout_csharpBolt**
  
    * Une version transactionnelle est définie dans **HybridTopologyTx_javaSpout_csharpBolt**

* **C# spout** et **Java bolt** : définis dans **HybridTopology_csharpSpout_javaBolt**
  
    * Une version transactionnelle est définie dans **HybridTopologyTx_csharpSpout_javaBolt**
  
  > [!NOTE]
  > Cette version montre également comment utiliser le code Clojure à partir d’un fichier texte en tant que composant Java.


Pour basculer entre la topologie utilisée lorsque le projet est soumis, il suffit de déplacer l’instruction `[Active(true)]` sur la topologie que vous souhaitez utiliser avant de l’envoyer au cluster.

> [!NOTE]
> Tous les fichiers Java requis sont fournis, dans le cadre de ce projet dans le dossier **JavaDependency** .


Les éléments suivants sont utilisés lors de la création et de la soumission d'une topologie hybride :

* **JavaComponentConstructor** doit être utilisé pour créer une instance de la classe Java pour un spout ou un bolt.

* **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** doit être utilisé pour sérialiser des données dans et à l’extérieur des composants Java à partir d’objets Java vers JSON.

* Lors de l’envoi de la topologie au serveur, vous devez utiliser l’option **Configurations supplémentaires** pour spécifier les **chemins d’accès des fichiers Java**. Le chemin d’accès spécifié doit être le répertoire qui contient les fichiers JAR contenant vos classes Java.

### <a name="azure-event-hubs"></a>Hubs d’événements Azure

SCP.Net 0.9.4.203 introduit une classe et une méthode inédites propres à l’utilisation du spout Event Hubs (spout Java qui lit à partir d’Event Hubs). Quand vous créez une topologie qui utilise un spout Event Hubs, utilisez les méthodes suivantes :

* Classe **EventHubSpoutConfig** : crée un objet qui contient la configuration du composant de spout.

* Méthode **TopologyBuilder.SetEventHubSpout** : ajoute le composant spout Event Hubs à la topologie.

> [!NOTE]
> Vous devez toujours utiliser CustomizedInteropJSONSerializer pour sérialiser les données produites par le spout.

## <a id="configurationmanager"></a>Utilisation de ConfigurationManager

N’utilisez pas ConfigurationManager pour récupérer des valeurs de configuration des composants bolt et spout. Cela est susceptible d’entraîner une exception de pointeur null. À la place, la configuration de votre projet est transmise dans la topologie Storm comme une paire clé/valeur dans le contexte de topologie. Chaque composant qui repose sur des valeurs de configuration doit les récupérer à partir du contexte lors de l’initialisation.

Le code suivant montre comment récupérer ces valeurs :

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Si vous utilisez une méthode `Get` pour retourner une instance de votre composant, vous devez vous assurer qu’il transmet les paramètres `Context` et `Dictionary<string, Object>` du constructeur. L’exemple suivant est une méthode `Get` de base qui transmet correctement ces valeurs :

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Mise à jour de SCP.NET

Les dernières versions de SCP.NET prennent en charge la mise à niveau du package via NuGet. Vous recevez une notification de mise à niveau dès qu’une nouvelle mise à jour est disponible. Pour rechercher manuellement une mise à niveau, procédez comme suit :

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**.

2. Dans le Gestionnaire de package, sélectionnez **Mises à jour**. Si une mise à jour est disponible, elle est affichée. Cliquez sur le bouton **Mettre à jour** du package pour l'installer.

> [!IMPORTANT]
> Si votre projet a été créé avec une version précédente de SCP.NET qui n’utilisait pas NuGet, vous devez effectuer les étapes suivantes pour procéder à la mise à jour vers une version plus récente :
> 
> 1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**.
> 2. Dans le champ **Rechercher**, recherchez, puis ajoutez **Microsoft.SCP.Net.SDK** au projet.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="null-pointer-exceptions"></a>Exceptions de pointeur null

Lorsque vous utilisez une topologie C# avec un cluster HDInsight basé sur Linux, les composants bolt et spout qui utilisent ConfigurationManager pour lire les paramètres de configuration au moment de l’exécution peuvent retourner des exceptions de pointeur null.

La configuration de votre projet est transmise dans la topologie Storm comme une paire clé/valeur dans le contexte de topologie. Elle peut être récupérée à partir de l’objet dictionnaire qui est transmis à vos composants quand ils sont initialisés.

Pour plus d’informations, consultez la section [ConfigurationManager](#configurationmanager) du présent document.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Lorsque vous utilisez une topologie C# avec un cluster HDInsight basé sur Linux, vous pouvez rencontrer l’erreur suivante :

    System.TypeLoadException: Failure has occurred while loading a type.

Cette erreur se produit quand vous utilisez un fichier binaire qui n’est pas compatible avec la version de .NET prise en charge par Mono.

Pour les clusters HDInsight basés sur Linux, vous devez vous assurer que votre projet utilise des fichiers binaires compilés pour .NET 4.5.

### <a name="test-a-topology-locally"></a>Test local d’une topologie

Bien qu’il soit facile de déployer une topologie sur un cluster, dans certains cas, il sera peut-être nécessaire de tester une topologie localement. Utilisez les étapes suivantes pour exécuter et tester localement l’exemple de topologie de ce didacticiel localement dans votre environnement de développement.

> [!WARNING]
> Le test local fonctionne uniquement pour les topologies de base en C#. Vous ne pouvez pas employer le test local pour les topologies hybrides ou celles qui utilisent plusieurs flux de données.

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Propriétés**. Dans les propriétés du projet, modifiez le **Type de sortie** sur **Application Console**.
   
    ![type de sortie](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)
   
   > [!NOTE]
   > N’oubliez pas de modifier le **Type de sortie** sur **Bibliothèque de classes** avant de déployer la topologie sur un cluster.

2. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet, puis sélectionnez **Ajouter** > **Nouvel élément**. Sélectionnez **Classe** et entrez **LocalTest.cs** comme nom de classe. Enfin, cliquez sur **Ajouter**.

3. Ouvrez **LocalTest.cs** et ajoutez l’instruction **using** suivante en haut du fichier :
    
    ```csharp
    using Microsoft.SCP;
    ```

4. Utilisez le code suivant comme contenu de la classe **LocalTest** :
    
    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Prenez un moment pour lire les commentaires du code. Ce code utilise **LocalContext** pour exécuter les composants dans l’environnement de développement, en conservant le flux de données entre les composants dans des fichiers texte sur le disque local.

1. Ouvrez **Program.cs** et ajoutez le code suivant à la méthode **Main** :
    
    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. Enregistrez les modifications, puis utilisez **F5** ou sélectionnez **Débogage** > **Démarrer le débogage** pour démarrer le projet. Une fenêtre de console doit apparaître et enregistrer les statuts avec la progression des tests. Lorsque le message **Tests terminés** s’affiche, appuyez sur n’importe quelle touche pour fermer la fenêtre.

3. Utilisez **Windows Explorer** pour accéder au répertoire qui contient votre projet, par exemple, **C:\Users\<votre_nom_utilisateur > \Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Dans ce répertoire, ouvrez **Bin**, puis cliquez sur **Débogage**. Vous devez voir les fichiers texte produits lors de l’exécution de tests : sentences.txt, counter.txt et splitter.txt. Ouvrez chaque fichier texte et inspectez les données.
   
   > [!NOTE]
   > Les chaînes de données sont conservées sous forme de tableau de valeurs décimales dans ces fichiers. Par exemple, le résultat \[[97,103,111]] dans le fichier **splitter.txt** correspond au mot « and ».

> [!NOTE]
> Veillez à définir le **Type de projet** sur **Bibliothèque de classes** avant le déploiement vers un Storm sur un cluster HDInsight.

### <a name="log-information"></a>Enregistrement d’informations

Vous pouvez facilement enregistrer des informations à partir de vos composants de topologie à l’aide de `Context.Logger`. Par exemple, le texte suivant crée une entrée de journal d’information :

```csharp
Context.Logger.Info("Component started");
```

Les informations enregistrées peuvent être affichées dans le **journal du service Hadoop**, qui se trouve dans **l’Explorateur de serveurs**. Développez l’entrée pour votre Storm sur le cluster HDInsight , puis le **journal du service Hadoop**. Enfin, sélectionnez le fichier journal à afficher.

> [!NOTE]
> Les journaux sont stockés dans le compte de stockage Azure utilisé par votre cluster. Vous devez vous connecter à l’abonnement Azure contenant le compte de stockage pour afficher les journaux dans Visual Studio.

### <a name="view-error-information"></a>Afficher les informations relatives aux erreurs

Pour afficher les erreurs qui se sont produites dans une topologie en cours d’exécution, procédez comme suit :

1. À partir de **l’Explorateur de serveurs**, cliquez avec le bouton droit sur le Storm d’un cluster HDInsight et sélectionnez **Afficher les topologies Storm**.

2. Pour les **Spouts** et **Bolts**, la colonne **Dernière erreur** contient les informations sur la dernière erreur qui s’est produite.

3. Sélectionnez **l’Id de spout** ou **l’Id de bolt** pour le composant ayant une erreur répertoriée. Sur la page de détails, d’autres informations sont affichées dans la section **Erreurs** en bas de la page.

4. Pour obtenir plus d’informations, sélectionnez un **Port** à partir de la section **Exécuteurs** de la page afin d’afficher le journal de travail Storm pour les dernières minutes.

### <a name="errors-submitting-topologies"></a>Erreurs lors de la soumission des topologies

Si vous rencontrez des erreurs lors de la soumission d’une topologie à HDInsight, vous trouverez des journaux pour les composants côté serveur qui gèrent la soumission de topologie sur votre cluster HDInsight. Pour extraire ces journaux, utilisez la commande suivante à partir d’une ligne de commande :

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Remplacez __sshuser__ par le compte d’utilisateur SSH du cluster. Remplacez __clustername__ par le nom du cluster HDInsight. Si vous avez utilisé un mot de passe pour le compte SSH, vous êtes invité à le saisir. La commande télécharge le fichier dans le répertoire à partir duquel elle est exécutée.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir un exemple de traitement des données à partir des Event Hubs, consultez [Traitement des événements Azure Event Hubs avec Storm sur HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md).

Pour obtenir un exemple de topologie C# qui fractionne les données de flux en plusieurs flux de données, consultez la rubrique [Exemple c# Storm](https://github.com/Blackmist/csharp-storm-example).

Pour plus d’informations sur la création de topologies C#, accédez à [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Pour plus d’informations sur l’utilisation de HDInsight et pour obtenir davantage d’exemples Storm sur HDinsight, consultez les documents suivants :

**Microsoft SCP.NET**

* [Guide de programmation SCP](hdinsight-storm-scp-programming-guide.md)

**Apache Storm sur HDInsight**

* [Déploiement et analyse des topologies avec Apache Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology.md)
* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop sur HDInsight**

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase sur HDInsight**

* [Prise en main de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started.md)


