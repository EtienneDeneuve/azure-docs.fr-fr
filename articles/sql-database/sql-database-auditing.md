---
title: "Prise en main de l’audit Azure SQL Database | Documents Microsoft"
description: "Prise en main de l’audit de base de données SQL"
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: giladm
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: fdb80e3379adfa9d65d6e5891cb701cee86eb1b9
ms.lasthandoff: 03/17/2017


---
# <a name="get-started-with-sql-database-auditing"></a>Prise en main de l’audit de base de données SQL
L’audit Azure SQL Database suit les événements de base de données et les écrit dans un journal d’audit dans votre compte Stockage Azure.

L’audit peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données et à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité.

L’audit permet et facilite le respect de normes de conformité, mais il ne garantit pas cette conformité. Pour plus d’informations sur les programmes Azure prenant en charge la conformité aux normes, voir le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/support/trust-center/compliance/).

* [Vue d’ensemble de l’audit Azure SQL Database]
* [Configuration de l’audit pour votre base de données]
* [Analyse des journaux et des rapports d’audit]

## <a id="subheading-1"></a>Vue d’ensemble de l’audit Azure SQL Database
L’audit de bases de données SQL permet :

* **La rétention** d’une piste d’audit d’événements sélectionnés. Définissez les catégories d'actions et d'événements de base de données à auditer.
* **La génération de rapports** sur les activités de la base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d'activités et d'événements.
* **L'analyse** des rapports. Vous pouvez repérer les événements suspects, les activités inhabituelles et les tendances.

Il existe deux **méthodes d’audit** :

* **Audit d’objets blob** : les journaux sont écrits dans Stockage Blob Azure. Il s’agit d’une méthode d’audit plus récente, qui fournit des **performances plus élevées**, prend en charge **l’audit avec une granularité plus élevée au niveau des objets** et est **plus économique**. L’audit d’objets blob remplacera l’audit de table.
* **Audit de table (déconseillé)** : les journaux sont écrits dans Stockage Table Azure.

> [!IMPORTANT]
> L’introduction du nouvel audit d’objets blob apporte une modification majeure dans la façon dont la stratégie d’audit de serveur est héritée par la base de données. Consultez la section [Différences entre les objets blob et les tables dans l’héritage de la stratégie d’audit du serveur](#subheading-8) pour plus de détails.

Vous pouvez configurer l’audit pour différents types de catégories d’événements, comme expliqué dans la section [Configuration de l’audit de votre base de données](#subheading-2).

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

Une stratégie d’audit peut être définie pour une base de données spécifique ou en tant que stratégie de serveur par défaut. Une stratégie d’audit de serveur par défaut s’applique aux bases de données existantes et à celles qui sont nouvellement créées sur un serveur.

## <a id="subheading-2"></a>Configuration de l’audit pour votre base de données
La section suivante décrit la configuration de l’audit à l’aide du portail Azure.

### <a id="subheading-2-1">Audit d’objets blob</a>
1. Ouvrez le [portail Azure](https://portal.azure.com) à l’adresse https://portal.azure.com.
2. Accédez au panneau des paramètres de Base de données SQL/SQL Server que vous voulez auditer. Dans le panneau Paramètres, sélectionnez **Audit et détection des menaces**.

    <a id="auditing-screenshot"></a>
    ![Volet de navigation][1]
3. Dans le panneau de configuration de l’audit de la base de données, vous pouvez cocher la case **Hériter des paramètres du serveur** pour indiquer que cette base de données doit être auditée en fonction des paramètres de son serveur. SI cette option est sélectionnée, vous voyez un lien **Afficher les paramètres d’audit du serveur** qui vous permet d’afficher ou de modifier les paramètres d’audit du serveur à partir de ce contexte.

    ![Volet de navigation][2]
4. Si vous préférez activer l’audit Objet blob sur le niveau base de données (en plus ou au lieu de l’audit au niveau serveur), **décochez** l’option **Hériter des paramètres du serveur**, définissez Audit sur **ACTIVÉ** et choisissez le type d’audit **Objet blob**.

   > Si l’audit Objet blob du serveur est activé, l’audit configuré pour la base de données existe parallèlement à l’audit Objet blob du serveur.  

    ![Volet de navigation][3]
5. Sélectionnez **Détails du stockage** pour ouvrir le panneau Stockage des journaux d’audit. Sélectionnez le compte de stockage Azure où les journaux sont enregistrés et la période de rétention après laquelle les anciens journaux sont supprimés, puis cliquez sur **OK** dans le bas. **Conseil :** utilisez le même compte de stockage pour toutes les bases de données auditées afin de profiter au mieux des modèles de rapport d’audit.

    <a id="storage-screenshot"></a>
    ![Volet de navigation][4]
6. Si vous voulez personnaliser les événements audités, vous pouvez le faire via PowerShell ou l’API REST. Pour plus d’informations, consultez la section [Automation (PowerShell / API REST)](#subheading-7).
7. Une fois que vous avez configuré vos paramètres d’audit, vous pouvez activer la nouvelle fonctionnalité **Détection des menaces** (préversion) et configurer les adresses e-mail de réception des alertes de sécurité. La détection des menaces vous permet de recevoir des alertes proactives sur des activités anormales de la base de données qui peuvent indiquer des menaces de sécurité potentielles. Pour en savoir plus, consultez [Prise en main de la détection des menaces](sql-database-threat-detection-get-started.md) .
8. Cliquez sur **Save**.

### <a id="subheading-2-2">Audit de table</a> (déconseillé)

> Avant de configurer **l’audit des tables**, vérifiez que vous utilisez bien un [« client de niveau inférieur »](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md). En outre, si vous avez des paramètres de pare-feu stricts, notez que le [point de terminaison IP de votre base de données change](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) lors de l’activation de l’audit Table.


1. Ouvrez le [portail Azure](https://portal.azure.com) à l’adresse https://portal.azure.com.
2. Accédez au panneau des paramètres de Base de données SQL/SQL Server que vous voulez auditer. Dans le panneau Paramètres, sélectionnez **Audit et détection des menaces** (*[voir la capture d’écran dans la section Audit Objet blob](#auditing-screenshot)*).
3. Dans le panneau de configuration de l’audit de la base de données, vous pouvez cocher la case **Hériter des paramètres du serveur** pour indiquer que cette base de données doit être auditée en fonction des paramètres de son serveur. SI cette option est sélectionnée, vous voyez un lien **Afficher les paramètres d’audit du serveur** qui vous permet d’afficher ou de modifier les paramètres d’audit du serveur à partir de ce contexte.

    ![Volet de navigation][2]
4. Si vous préférez ne pas hériter des paramètres d’audit du serveur, **décochez** l’option **Hériter des paramètres d’audit du serveur**, définissez Audit sur **ACTIVÉ** et choisissez le type d’audit **Table**.

    ![Volet de navigation][3-tbl]
5. Sélectionnez **Détails du stockage** pour ouvrir le panneau Stockage des journaux d’audit. Sélectionnez le compte de stockage Azure où les journaux sont enregistrés et la période de rétention après laquelle les anciens journaux sont supprimés. **Conseil :** Utilisez le même compte de stockage pour toutes les bases de données auditées afin de bénéficier au mieux des modèles de rapport d’audit (*[voir la capture d’écran de la section Audit Objet blob](#storage-screenshot)*).
6. Cliquez sur **Événements audités (Audited Events)** pour personnaliser les événements à auditer. Dans le panneau **Journalisation par événement**, cliquez sur **Succès** et sur **Échec** pour journaliser tous les événements, ou choisissez des catégories d’événements individuelles.

   > La personnalisation des événements audités peut également être faite via PowerShell ou l’API REST. Pour plus d’informations, consultez la section [Automation (PowerShell / API REST)](#subheading-7).

    ![Volet de navigation][5]
7. Une fois que vous avez configuré vos paramètres d’audit, vous pouvez activer la nouvelle fonctionnalité **Détection des menaces** (préversion) et configurer les adresses e-mail de réception des alertes de sécurité. La détection des menaces vous permet de recevoir des alertes proactives sur des activités anormales de la base de données qui peuvent indiquer des menaces de sécurité potentielles. Pour en savoir plus, consultez [Prise en main de la détection des menaces](sql-database-threat-detection-get-started.md) .
8. Cliquez sur **Save**.


## <a id="subheading-8"></a>Différences entre les objets blob et les tables dans l’héritage de la stratégie d’audit du serveur

###<a name="ablob-auditinga"></a><a>Audit d’objets blob</a>

1. Si **l’audit d’objets blob du serveur est activé**, il **s’applique toujours à la base de données** (par exemple, la base de données qui sera auditée), indépendamment des éléments suivants :
    - Les paramètres d’audit de base de données.
    - Que la case à cocher « Hériter des paramètres du serveur » soit activée ou non dans le panneau de la base de données.

2. L’activation de l’audit d’objets blob de la base de données, en plus de l’activation sur le serveur, ne remplacera **pas** ou ne modifiera aucun des paramètres de l’audit d’objets blob du serveur : les deux audits coexisteront. En d’autres termes, la base de données sera auditée deux fois en parallèle (une fois par la stratégie du serveur et une autre fois par la stratégie de la base de données).

    > [!NOTE]
    > Vous devez éviter d’activer à la fois l’audit d’objets blob du serveur et l’audit d’objets blob de la base de données, sauf si :
    > * Vous devez utiliser un autre *compte de stockage* ou une autre *période de rétention* pour une base de données spécifique.
    > * Vous souhaitez auditer d’autres types ou catégories d’événements pour une base de données spécifique pour les autres bases de données sur ce serveur (par exemple, si des insertions de table doivent être auditées uniquement pour une base de données spécifique).
    > <br><br>
    > Sinon, il est **recommandé d’activer uniquement l’audit d’objets blob au niveau du serveur** et de laisser désactivé l’audit au niveau de la base de données pour toutes les bases de données.

###<a name="atable-auditinga-deprecated"></a><a>Audit de table</a> (déconseillé)

Si **l’audit de table au niveau du serveur est activé**, il s’applique uniquement à la base de données si la case à cocher « Hériter des paramètres du serveur » est activée dans le panneau de la base de données (cette option est activée par défaut pour toutes les bases de données qui viennent d’être créées).

- Si la case est *cochée*, toutes les modifications apportées aux paramètres d’audit dans la base de données **remplaceront** les paramètres du serveur pour cette base de données.

- Si la case n’est *pas cochée* et que l’audit au niveau de la base de données est désactivé, la base de données ne sera pas auditée.

## <a id="subheading-3"></a>Analyse des journaux et des rapports d’audit
Les journaux d’audit sont agrégés dans le compte de stockage Azure choisi lors de la configuration.

Vous pouvez explorer les fichiers journaux d’audit avec un outil comme [l’Explorateur de stockage Azure](http://storageexplorer.com/).

Consultez ci-dessous les spécificités de l’analyse des journaux d’audit **Objet blob** et **Table**.

### <a id="subheading-3-1">Audit d’objets blob</a>
Les journaux d’audit d’objets blob sont enregistrés sous la forme d’une collection de fichiers d’objets blob dans un conteneur nommé « **sqldbauditlogs** ».

Pour plus d’informations sur la hiérarchie des dossiers de stockage des journaux d’audit Objet blob, sur la convention de nommage des objets blob et sur le format des journaux, consultez les [informations de référence sur le format des journaux d’audit Objet blob (téléchargement de fichiers doc)](https://go.microsoft.com/fwlink/?linkid=829599).

Il existe plusieurs méthodes pour afficher des journaux d’audit Objet blob :

1. Via le [portail Azure](https://portal.azure.com) : ouvrez la base de données correspondante. En haut du panneau **Audit et détection des menaces** de la base de données, cliquez sur **Afficher les journaux d’audit**.

    ![Volet de navigation][10]

    Un panneau **Enregistrements d’audit** s’ouvre, dans lequel vous pouvez afficher les journaux.

    - Vous pouvez choisir d’afficher des dates spécifiques en cliquant sur **Filtrer** en haut du panneau Enregistrements d’audit
    - Vous pouvez basculer entre les enregistrements d’audit qui ont été créés par la stratégie de serveur ou par l’audit de stratégie de base de données

    ![Volet de navigation][11]
2. Téléchargez les fichiers journaux à partir de votre conteneur d’objets blob Stockage Azure via le portail ou avec un outil comme [l’Explorateur de stockage Azure](http://storageexplorer.com/).

    Une fois que vous avez téléchargé localement le fichier journal, vous pouvez double-cliquer sur le fichier pour ouvrir, afficher et analyser les journaux dans SSMS.

    Autres méthodes :

   * Vous pouvez **télécharger plusieurs fichiers simultanément** via l’Explorateur de stockage Azure : cliquez sur un sous-dossier spécifique (par exemple un sous-dossier qui inclut tous les fichiers journaux pour une date spécifique) et choisissez « Enregistrer sous » pour enregistrer dans un dossier local.

       Après avoir téléchargé plusieurs fichiers (ou toute une journée, comme décrit ci-dessus), vous pouvez les fusionner localement comme suit :

       **Ouvrez SSMS -> Fichier -> Ouvrir -> Fusionner les événements étendus -> Choisissez tous les fichiers à fusionner**
   * Par programmation :

     * Lecteur des événements étendus de la **bibliothèque C#** ([plus d’informations ici](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/))
     * Interrogation des fichiers d’événements étendus avec **PowerShell** ([plus d’informations ici](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/))

3. Nous avons créé un **exemple d’application** qui s’exécute dans Azure et utilise les API publiques OMS pour envoyer les journaux d’audit SQL à OMS à des fins d’utilisation via le tableau de bord OMS ([plus d’informations ici](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)).

### <a id="subheading-3-2">Audit de table</a> (déconseillé)
Les journaux d’audit Table sont enregistrés sous la forme d’une collection de tables Stockage Azure avec un préfixe **SQLDBAuditLogs**.

Pour plus d’informations sur le format des journaux d’audit Table, consultez les [informations de référence sur les formats des journaux d’audit Table](http://go.microsoft.com/fwlink/?LinkId=506733).

Il existe plusieurs méthodes pour afficher des journaux d’audit Table :

1. Via le [portail Azure](https://portal.azure.com) : ouvrez la base de données correspondante. En haut du panneau **Audit et détection des menaces** de la base de données, cliquez sur **Afficher les journaux d’audit**.

    ![Volet de navigation][10]

    Un panneau **Enregistrements d’audit** s’ouvre, dans lequel vous pouvez afficher les journaux.

    * Vous pouvez choisir d’afficher des dates spécifiques en cliquant sur **Filtrer** en haut du panneau Enregistrements d’audit
    * Vous pouvez télécharger et afficher les journaux d’audit au format Excel en cliquant sur **Ouvrir dans Excel** en haut du panneau Enregistrements d’audit

    ![Volet de navigation][12]

2. Par ailleurs, un modèle de rapport préconfiguré est disponible sous forme de [feuille de calcul Excel téléchargeable](http://go.microsoft.com/fwlink/?LinkId=403540) pour vous aider à analyser rapidement les données des journaux. Pour utiliser le modèle sur vos journaux d’audit, il vous faut Excel 2013 ou une version ultérieure et Power Query, téléchargeable [ici](http://www.microsoft.com/download/details.aspx?id=39379).

    Vous pouvez aussi importer vos journaux d’audit dans le modèle Excel directement depuis votre compte de stockage Azure à l’aide de Power Query. Vous pouvez explorer ensuite vos enregistrements d’audit et créer des tableaux de bord et des rapports outre les données du journal.

    ![Volet de navigation][9]

## <a id="subheading-5"></a>Pratiques d’utilisation dans un environnement de production
<!--The description in this section refers to screen captures above.-->

### <a id="subheading-6">Audit des bases de données géorépliquées</a>
Quand vous utilisez des bases de données géorépliquées, il est possible de configurer l’audit sur la base de données primaire, sur la base de données secondaire ou sur les deux, selon le type d’audit.

**Audit Table** : vous pouvez configurer une stratégie distincte, au niveau base de données ou au niveau serveur, pour chacune des deux bases de données (principale et secondaire), comme décrit dans la section [Configuration de l’audit pour votre base de données](#subheading-2-2).

**Audit Objet blob** : suivez ces instructions :

1. **Base de données primaire** : activez **Audit Objet blob** sur le serveur ou sur la base de données elle-même, comme décrit dans la section [Configuration de l’audit de votre base de données](#subheading-2-1).
2. **Base de données secondaire** : l’audit Objet blob ne peut pas être activé/désactivé depuis les paramètres d’audit de la base de données primaire.

   * Activez **Audit Objet blob** sur la **base de données primaire**, comme décrit dans la section [Configuration de l’audit de votre base de données](#subheading-2-1). L’audit Objet blob doit être activé sur la *base de données primaire elle-même*, et non pas sur le serveur.
   * Une fois que l’audit Objet blob est activé sur la base de données primaire, il devient également activé sur la base de données secondaire.

    > [!IMPORTANT]
    > Par défaut, les **paramètres de stockage** pour la base de données secondaire sont identiques à ceux de la base de données primaire, ce qui provoque un **trafic entre régions**. Vous pouvez éviter cela en activant l’audit Objet blob sur le **serveur secondaire** et en configurant un **stockage local** dans les paramètres de stockage du serveur secondaire (ceci remplace l’emplacement de stockage pour la base de données secondaire et fait que chaque base de données enregistre les journaux d’audit dans un stockage local).  

<br>

### <a id="subheading-6">Régénération des clés de stockage</a>
Dans un environnement de production, vous allez probablement actualiser périodiquement vos clés de stockage. Lors de l’actualisation de vos clés, vous devez réenregistrer la stratégie d’audit. Pour ce faire, procédez comme suit :

1. Dans le panneau des détails de stockage, faites passer la **clé d’accès de stockage** de *Principale**Secondaire*, puis cliquez sur **OK** dans le bas. Cliquez sur **ENREGISTRER** en haut du panneau de configuration de l’audit.

    ![Volet de navigation][6]
2. Revenez au volet de configuration du stockage, puis **régénérez** la *clé d’accès primaire*.

    ![Volet de navigation][8]
3. Revenez au panneau de configuration de l’audit, faites passer la **clé d’accès de stockage** de *Secondaire* à *Principale*, puis cliquez sur **OK** dans le bas. Cliquez sur **ENREGISTRER** en haut du panneau de configuration de l’audit.
4. Revenez au panneau de configuration du stockage, puis **régénérez** la *clé d’accès secondaire* (en préparation du cycle suivant d’actualisation des clés).

## <a id="subheading-7"></a>Automation (PowerShell / API REST)
Vous pouvez également configurer l’audit dans Azure SQL Database en utilisant les outils d’automatisation suivants :

1. **Applets de commande PowerShell**

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]
2. **API REST - Audit d’objets blob**

   * [Create or Update Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [Create or Update Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [Get Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [Get Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [Get Server Blob Auditing Operation Result](https://msdn.microsoft.com/library/azure/mt771862.aspx)
3. **API REST - Audit de table (déconseillé)**

   * [Create or Update Database Auditing Policy](https://msdn.microsoft.com/library/azure/mt604471.aspx)
   * [Create or Update Server Auditing Policy](https://msdn.microsoft.com/library/azure/mt604383.aspx)
   * [Get Database Auditing Policy](https://msdn.microsoft.com/library/azure/mt604381.aspx)
   * [Get Server Auditing Policy](https://msdn.microsoft.com/library/azure/mt604382.aspx)

<!--Anchors-->
[Vue d’ensemble de l’audit Azure SQL Database]: #subheading-1
[Configuration de l’audit pour votre base de données]: #subheading-2
[Analyse des journaux et des rapports d’audit]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png

[101]: https://msdn.microsoft.com/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/library/azure/mt619353(v=azure.200).aspx

