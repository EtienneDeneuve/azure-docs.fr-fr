---
title: "Automatisation DocumentDB - Gestion des régions | Microsoft Docs"
description: "Utilisez l’interface Azure CLI 1.0 et Azure Resource Manager pour gérer les régions dans un compte de base de données DocumentDB. DocumentDB est une base de données NoSQL basée sur le cloud pour les données JSON."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 7f765c17-8549-4108-9475-46394fc3a218
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 19ba92fae63cadef20cd5414fa23bf4821ab6d56
ms.lasthandoff: 03/21/2017


---
# <a name="automate-documentdb-account-region-management-using-azure-cli-10-and-azure-resource-manager-templates"></a>Automatiser la gestion des régions de comptes DocumentDB à l’aide de l’interface Azure CLI 1.0 et de modèles Azure Resource Manager

Cet article vous montre comment ajouter/supprimer une région dans votre compte Azure DocumentDB à l’aide des commandes de l’interface Azure CLI 1.0 et de modèles Azure Resource Manager. La gestion des régions peut également s’effectuer via le [portail Azure](documentdb-portal-global-replication.md). Notez que les commandes du didacticiel suivant ne vous permettent pas de modifier les priorités de basculement des différentes régions. Seules les régions de lecture peuvent être ajoutées ou supprimées. La région d’écriture d’un compte de base de données (priorité de basculement de 0) ne peut pas être ajoutée/retirée.

Les comptes de base de données DocumentDB sont actuellement la seule ressource DocumentDB qui peut être créée/modifiée à l’aide de [modèles Resource Manager et de l’interface Azure CLI  1.0](documentdb-automation-resource-manager-cli.md).

## <a name="getting-ready"></a>Préparation

Pour pouvoir utiliser l’interface Azure CLI 1.0 avec les groupes de ressources Azure, vous devez disposer de la version appropriée de cette interface et d’un compte Azure. Si vous ne disposez pas de l’interface Azure CLI 1.0, [installez-la](../cli-install-nodejs.md).

### <a name="update-your-azure-cli-10-version"></a>Mettre à jour votre version d’Azure CLI 1.0

À l’invite de commandes, entrez `azure --version` pour savoir si vous avez déjà installé la version 0.10.4 ou une version ultérieure. Vous pouvez être invité à participer à une collecte de données de l’interface Microsoft Azure CLI 1.0 à cette étape. Le cas échéant, sélectionnez « y » ou « n » pour accepter ou refuser.

    azure --version
    0.10.4 (node: 4.2.4)

Si vous disposez d’une version antérieure à la version 0.10.4, vous devez [installer l’interface Azure CLI 1.0](../cli-install-nodejs.md) ou la mettre à jour en utilisant l’un des programmes d’installation natifs. Vous pouvez également utiliser **npm** en entrant `npm update -g azure-cli` pour la mettre à jour ou `npm install -g azure-cli` pour l’installer.

### <a name="set-your-azure-account-and-subscription"></a>Configurer votre compte et votre abonnement Microsoft Azure

Si vous ne possédez pas encore d’abonnement Azure, mais que vous avez un abonnement Visual Studio, vous pouvez activer vos [avantages abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ou vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

Vous devez disposer d’un compte professionnel ou scolaire, ou bien d’une identité du compte Microsoft, pour utiliser des modèles de gestion des ressources Azure. Si vous avez l’un de ces comptes, tapez la commande suivante.

    azure login

Ce qui génère la sortie suivante : 

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> Si vous n’avez pas de compte Azure, un message d’erreur s’affiche, indiquant que vous avez besoin d’un autre type de compte. Pour en créer un à partir de votre compte Azure actuel, consultez [Création d’une identité professionnelle ou scolaire dans Azure Active Directory](../virtual-machines/virtual-machines-windows-create-aad-work-id.md).

Ouvrez [https://aka.ms/devicelogin](https://aka.ms/devicelogin) dans un navigateur, puis entrez le code fourni dans la sortie de commande.

![Capture d’écran présentant l’écran de connexion d’appareil de l’interface Microsoft Azure CLI 1.0](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

Une fois que vous avez entré le code, sélectionnez l’identité que vous voulez utiliser dans le navigateur, puis indiquez votre nom d’utilisateur et votre mot de passe, si nécessaire.

![Capture d’écran montrant où sélectionner le compte d’identité Microsoft associé à l’abonnement que vous voulez utiliser](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

L’écran de confirmation suivant s’affiche lorsque vous êtes connecté avec succès. Vous pouvez alors fermer la fenêtre du navigateur.

![Capture d’écran présentant la confirmation de la connexion à l’interface de ligne de commande interplateforme Microsoft Azure](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

L’interface de commande fournit également la sortie suivante.

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

Outre la méthode de connexion interactive décrite ici, d’autres méthodes de connexion à l’interface Azure CLI 1.0 sont disponibles. Pour plus d’informations sur les autres méthodes et sur la gestion de plusieurs inscriptions, consultez [Connectez-vous à Azure à partir de l’interface de ligne de commande (CLI) Azure](../xplat-cli-connect.md).

### <a name="switch-to-azure-cli-10-resource-group-mode"></a>Passer en mode de groupe de ressources Azure CLI 1.0

Par défaut, l’interface Azure CLI 1.0 démarre en mode de gestion de service (mode **asm**). Tapez la commande suivante pour basculer en mode de groupe de ressources.

    azure config mode arm

Ce qui fournit la sortie suivante :

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

Le cas échéant, vous pouvez revenir à la configuration des commandes par défaut en tapant `azure config mode asm`.

### <a name="create-or-retrieve-your-resource-group"></a>Créer ou récupérer votre groupe de ressources

Pour créer un compte DocumentDB, vous avez tout d’abord besoin d’un groupe de ressources. Si vous connaissez déjà le nom du groupe de ressources que vous voulez utiliser, passez à l’ [étape 2](#create-documentdb-account-cli). 

Pour passer en revue la liste de tous vos groupes de ressources actuels, exécutez la commande suivante et notez le nom du groupe de ressources que vous voulez utiliser : 

    azure group list

Pour créer un groupe de ressources, exécutez la commande suivante, et spécifiez le nom du nouveau groupe de ressources à créer, ainsi que la région dans laquelle créer le groupe de ressources : 

    azure group create <resourcegroupname> <resourcegrouplocation>

 - `<resourcegroupname>` accepte uniquement les caractères alphanumériques, les points, les traits de soulignement, le caractère « - » et les parenthèses, et ne peut pas se terminer par un point. 
 - `<resourcegrouplocation>` doit être l’une des régions dans lesquelles DocumentDB est disponible. La liste actuelle des régions est présentée sur la [page Régions Azure](https://azure.microsoft.com/regions/#services).

Exemple d’entrée :

    azure group create new_res_group westus

Ce qui génère la sortie suivante :

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Si vous rencontrez des erreurs, consultez la section [Dépannage](#troubleshooting). 

## <a name="understanding-azure-resource-manager-templates-and-resource-groups"></a>Présentation des groupes de ressources et des modèles Azure Resource Manager

La plupart des applications sont basées sur une combinaison de différents types de ressources (par exemple, un ou plusieurs comptes DocumentDB, des comptes de stockage, un réseau virtuel ou un réseau de distribution de contenu). L’API de gestion des services Azure par défaut et le portail Azure présentaient ces éléments en suivant une approche au cas par cas, ce qui nécessitait un déploiement et une gestion propres à chaque service (ou l’obtention d’outils supplémentaires pour le faire) et interdisait l’usage d’une seule unité logique de déploiement.

*modèles Azure Resource Manager* permettent de regrouper le déploiement et la gestion de ces différentes ressources en une seule unité logique de déploiement de manière déclarative. Au lieu de devoir utiliser une suite de commandes pour indiquer à Azure les éléments à déployer les uns après les autres, il vous suffit de décrire l’intégralité de votre déploiement dans un fichier JSON, comprenant toutes vos ressources, ainsi que les configurations et les paramètres de déploiement leur étant associés, puis d’indiquer à Azure de déployer ces ressources en tant que groupe.

Pour plus d’informations sur les groupes de ressources Azure et sur ce qu’ils peuvent faire pour vous, consultez l’article [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Si vous êtes intéressé par la création de modèles, consultez [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).


## <a id="add-region-documentdb-account"></a>Travail : Ajouter une région à un compte DocumentDB

DocumentDB peut [distribuer vos données globalement][distribute-globally] entre diverses [régions Azure](https://azure.microsoft.com/regions/#services). Cette section vous explique comment ajouter une région de lecture à un compte DocumentDB existant à l’aide de l’interface Azure CLI 1.0 et de modèles Resource Manager. Cette opération peut être accomplie à l’aide de l’interface Azure CLI 1.0 avec ou sans modèles Resource Manager.

### <a id="add-region-documentdb-account-cli"></a> Ajouter une région à un compte DocumentDB à l’aide de l’interface Azure CLI 1.0, sans modèles Resource Manager

Ajoutez une région à un compte DocumentDB existant dans un groupe de ressources existant ou nouveau, en saisissant la commande ci-dessous à l’invite. Notez que le tableau des emplacements doit correspondre à la configuration de région actuelle au sein du compte Document DB, à l’exception de la nouvelle région à ajouter. L’exemple ci-dessous représente une commande permettant d’ajouter une seconde région au compte.

Faites correspondre les valeurs de priorité de basculement à la configuration existante. L’une des régions doit avoir le paramètre failoverPriority défini sur 0 pour que cette région soit conservée en tant que [région d’écriture pour le compte DocumentDB][scaling-globally]. Les valeurs de priorité de basculement doivent être uniques parmi les emplacements, et la valeur de priorité de basculement la plus élevée doit être inférieure au nombre total de régions. La nouvelle région, qui est une région de lecture, doit présenter une valeur de priorité de basculement supérieure à 0.

> [!TIP]
> Si vous exécutez cette commande dans Azure PowerShell ou Windows PowerShell, une erreur relative à un jeton inattendu s’affiche. Exécutez plutôt cette commande à l’invite de commandes Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<newdatabaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

 - `<resourcegroupname>` accepte uniquement les caractères alphanumériques, les points, les traits de soulignement, le caractère « - » et les parenthèses, et ne peut pas se terminer par un point.
 - `<resourcegrouplocation>` correspond à la région du groupe de ressources actuel.
 - `<ip-range-filter>` Spécifie le jeu d’adresses IP ou des plages d’adresses IP au format CIDR pour les ajouter à la liste d’adresses IP clientes autorisées pour un compte de base de données particulier. Les plages/adresses IP doivent être séparées par des virgules et ne doivent pas contenir d’espaces. Pour plus d’informations, consultez [Prise en charge du pare-feu dans DocumentDB](documentdb-firewall-support.md).
 - `<databaseaccountname>` accepte uniquement les minuscules, les chiffres et le caractère « - », et doit comporter entre 3 et 50 caractères.
 - `<databaseaccountlocation>` doit être l’une des régions dans lesquelles DocumentDB est disponible. La liste actuelle des régions est présentée sur la [page Régions Azure](https://azure.microsoft.com/regions/#services).
 - `<newdatabaseaccountlocation>` est la nouvelle région à ajouter ; il doit s’agit d’une des régions dans lesquelles DocumentDB est généralement disponible. La liste actuelle des régions est présentée sur la [page Régions Azure](https://azure.microsoft.com/regions/#services).


Exemple d’entrée pour l’ajout de la région des États-Unis de l’Est en tant que région de lecture dans le compte DocumentDB : 

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"},{\"locationName\":\"eastus\",\"failoverPriority\":\"1\"}"]}"

Ce qui génère la sortie suivante car votre nouveau compte est configuré :

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

Si vous rencontrez des erreurs, consultez la section [Dépannage](#troubleshooting). 

Une fois que la commande a renvoyé un résultat, le compte est dans l’état **Création** pendant quelques minutes, avant de passer à l’état **En ligne** dans lequel il est prêt à être utilisé. Vous pouvez vérifier l’état du compte sur le panneau [Comptes DocumentDB](https://portal.azure.com)du **portail Azure** .

### <a id="add-region-documentdb-account-cli-arm"></a> Ajouter une région à un compte DocumentDB à l’aide de l’interface Azure CLI 1.0, avec des modèles Resource Manager

Les instructions de cette section indiquent comment ajouter une région à un compte DocumentDB à l’aide d’un modèle Azure Resource Manager et d’un fichier de paramètres facultatifs, tous deux étant des fichiers JSON. L’utilisation d’un modèle vous permet de décrire exactement ce que vous le voulez et de le répéter sans erreurs.

Faites correspondre les valeurs de priorité de basculement à la configuration existante. L’une des régions doit avoir le paramètre failoverPriority défini sur 0 pour que cette région soit conservée en tant que [région d’écriture pour le compte DocumentDB][scaling-globally]. Les valeurs de priorité de basculement doivent être uniques parmi les emplacements, et la valeur de priorité de basculement la plus élevée doit être inférieure au nombre total de régions. La nouvelle région, qui est une région de lecture, doit présenter une valeur de priorité de basculement supérieure à 0.

Créez un fichier de modèle local similaire à celui ci-dessous, qui corresponde à votre configuration actuelle de région DocumentDB. Le tableau des emplacements doit comporter l’ensemble des régions existantes dans le compte de base de données, avec la nouvelle région à ajouter. Nommez le fichier azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            },
            "locationName2": {
                "type": "string"
            },
            "newLocationName": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        },
                        {
                            "failoverPriority": 1,
                            "locationName": "[parameters('locationName2')]"
                        },
                        {
                            "failoverPriority": 2,
                            "locationName": "[parameters('newLocationName')]"
                        }
                    ]
                }
            }
        ]
    }

Le fichier de modèle ci-dessus représente un exemple dans laquelle une nouvelle région est ajoutée à un compte DocumentDB comportant déjà deux régions.

Vous pouvez entrer les valeurs de paramètres dans la ligne de commande, ou créer un fichier de paramètre pour spécifier la valeur.

Pour créer un fichier de paramètres, copiez le contenu suivant dans un nouveau fichier et nommez ce fichier azuredeploy.parameters.json. Si vous prévoyez de spécifier le nom du compte de base de données à l’invite de commandes, vous pouvez continuer sans créer ce fichier.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            },
            "locationName2": {
                "value": "eastus"
            },
            "newLocationName": {
                "value": "northeurope"
            }
        }
    }

Dans le fichier azuredeploy.parameters.json, vous devez mettre à jour le champ de valeur `"databaseAccountName"` avec le nom de la base de données que vous souhaitez utiliser, puis enregistrer le fichier. `"databaseAccountName"` accepte uniquement les minuscules, les chiffres et le caractère « - », et doit comporter entre 3 et 50 caractères. Mettez à jour les champs des valeurs `"locationName1"` et `"locationName2"` avec les régions hébergeant votre compte DocumentDB. Mettez à jour le champ de valeur `"newLocationName"` avec la région que vous souhaitez ajouter.

Pour créer un compte DocumentDB dans votre groupe de ressources, exécutez la commande suivante et indiquez le chemin du fichier de modèle, le chemin du fichier de paramètres ou la valeur du paramètre, le nom du groupe de ressources dans lequel effectuer le déploiement et un nom du déploiement (-n est facultatif). 

Pour utiliser un fichier de paramètres :

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` est le chemin d’accès au fichier azuredeploy.json créé à l’étape 1. Si votre nom de chemin comporte des espaces, placez ce paramètre entre guillemets doubles.
 - `<PathToParameterFile>` est le chemin d’accès au fichier azuredeploy.parameters.json créé à l’étape 1. Si votre nom de chemin comporte des espaces, placez ce paramètre entre guillemets doubles.
 - `<resourcegroupname>` est le nom du groupe de ressources existant auquel vous souhaitez ajouter un compte de base de données DocumentDB. 
 - `<deploymentname>` est le nom facultatif du déploiement.

Exemple d’entrée : 

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

OU, pour spécifier le paramètre de nom de compte de base de données sans un fichier de paramètres et être plutôt invité à entrer la valeur, exécutez la commande suivante :

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Exemple d’entrée qui affiche l’invite et une entrée pour un compte de base de données nommé samplearmacct :

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

Comme le compte est configuré, vous recevez les informations suivantes : 

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    + 
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    data:    newLocationName      String  eastus
    data:    databaseAccountName  String  samplearmacct
    info:    group deployment create command OK

Si vous rencontrez des erreurs, consultez la section [Dépannage](#troubleshooting).  

Une fois que la commande a renvoyé un résultat, le compte est dans l’état **Création** pendant quelques minutes, avant de passer à l’état **En ligne** dans lequel il est prêt à être utilisé. Vous pouvez vérifier l’état du compte sur le panneau [Comptes DocumentDB](https://portal.azure.com)du **portail Azure** .

## <a id="remove-region-documentdb-account"></a>Travail : Supprimer une région d’un compte DocumentDB

DocumentDB peut [distribuer vos données globalement][distribute-globally] entre diverses [régions Azure](https://azure.microsoft.com/regions/#services). Cette section vous explique comment supprimer une région d’un compte DocumentDB existant à l’aide de l’interface Azure CLI 1.0 et de modèles Resource Manager. Cette opération peut être accomplie à l’aide de l’interface Azure CLI 1.0 avec ou sans modèles Resource Manager.

### <a id="remove-region-documentdb-account-cli"></a> Supprimer une région d’un compte DocumentDB à l’aide de l’interface Azure CLI 1.0, sans modèles Resource Manager

Pour supprimer une région d’un compte DocumentDB existant, la commande ci-dessous peut être exécutée avec l’interface Azure CLI 1.0. Le tableau des emplacements doit comporter uniquement les régions restantes. **L’emplacement omis sera supprimé du compte DocumentDB**. Exécutez la commande suivante dans l’invite de commande.

L’une des régions doit avoir le paramètre failoverPriority défini sur 0 pour que cette région soit conservée en tant que [région d’écriture pour le compte DocumentDB][scaling-globally]. Les valeurs de priorité de basculement doivent être uniques parmi les emplacements, et la valeur de priorité de basculement la plus élevée doit être inférieure au nombre total de régions. 

> [!TIP]
> Si vous exécutez cette commande dans Azure PowerShell ou Windows PowerShell, une erreur relative à un jeton inattendu s’affiche. Exécutez plutôt cette commande à l’invite de commandes Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

 - `<resourcegroupname>` accepte uniquement les caractères alphanumériques, les points, les traits de soulignement, le caractère « - » et les parenthèses, et ne peut pas se terminer par un point.
 - `<resourcegrouplocation>` correspond à la région du groupe de ressources actuel.
 - `<ip-range-filter>` Spécifie le jeu d’adresses IP ou des plages d’adresses IP au format CIDR pour les ajouter à la liste d’adresses IP clientes autorisées pour un compte de base de données particulier. Les plages/adresses IP doivent être séparées par des virgules et ne doivent pas contenir d’espaces. Pour plus d’informations, consultez [Prise en charge du pare-feu dans DocumentDB](documentdb-firewall-support.md).
 - `<databaseaccountname>` accepte uniquement les minuscules, les chiffres et le caractère « - », et doit comporter entre 3 et 50 caractères.
 - `<databaseaccountlocation>` doit être l’une des régions dans lesquelles DocumentDB est disponible. La liste actuelle des régions est présentée sur la [page Régions Azure](https://azure.microsoft.com/regions/#services).

Exemple d’entrée : 

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"}"]}"

Ce qui génère la sortie suivante car votre nouveau compte est configuré :

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

Si vous rencontrez des erreurs, consultez la section [Dépannage](#troubleshooting). 

Une fois que la commande a renvoyé un résultat, le compte est dans l’état **Mise à jour** pendant quelques minutes, avant de passer à l’état **En ligne** dans lequel il est prêt à être utilisé. Vous pouvez vérifier l’état du compte sur le panneau [Comptes DocumentDB](https://portal.azure.com)du **portail Azure** .

### <a id="remove-region-documentdb-account-cli-arm"></a> Supprimer une région d’un compte DocumentDB à l’aide de l’interface Azure CLI 1.0, avec des modèles Resource Manager

Les instructions de cette section indiquent comment supprimer une région à un compte DocumentDB à l’aide d’un modèle Azure Resource Manager et d’un fichier de paramètres facultatifs, tous deux étant des fichiers JSON. L’utilisation d’un modèle vous permet de décrire exactement ce que vous le voulez et de le répéter sans erreurs.

L’une des régions doit avoir le paramètre failoverPriority défini sur 0 pour que cette région soit conservée en tant que [région d’écriture pour le compte DocumentDB][scaling-globally]. Les valeurs de priorité de basculement doivent être uniques parmi les emplacements, et la valeur de priorité de basculement la plus élevée doit être inférieure au nombre total de régions. 

Créez un fichier de modèle local similaire à celui ci-dessous, qui corresponde à votre configuration actuelle de région DocumentDB. Le tableau des emplacements doit comporter uniquement les régions restantes. **L’emplacement omis sera supprimé du compte DocumentDB**. Nommez le fichier azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

Vous pouvez entrer les valeurs de paramètres dans la ligne de commande, ou créer un fichier de paramètre pour spécifier la valeur.

Pour créer un fichier de paramètres, copiez le contenu suivant dans un nouveau fichier et nommez ce fichier azuredeploy.parameters.json. Si vous prévoyez de spécifier le nom du compte de base de données à l’invite de commandes, vous pouvez continuer sans créer ce fichier. Veillez à ajouter les paramètres nécessaires, définis dans votre modèle Azure Resource Manager.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

Dans le fichier azuredeploy.parameters.json, vous devez mettre à jour le champ de valeur `"databaseAccountName"` avec le nom de la base de données que vous souhaitez utiliser, puis enregistrer le fichier. `"databaseAccountName"` accepte uniquement les minuscules, les chiffres et le caractère « - », et doit comporter entre 3 et 50 caractères. Mettez à jour le champ de valeur `"locationName1"` avec les régions dans lesquelles conserver le compte DocumentDB après le retrait de la région.

Pour créer un compte DocumentDB dans votre groupe de ressources, exécutez la commande suivante et indiquez le chemin du fichier de modèle, le chemin du fichier de paramètres ou la valeur du paramètre, le nom du groupe de ressources dans lequel effectuer le déploiement et un nom du déploiement (-n est facultatif). 

Pour utiliser un fichier de paramètres :

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` est le chemin d’accès au fichier azuredeploy.json créé à l’étape 1. Si votre nom de chemin comporte des espaces, placez ce paramètre entre guillemets doubles.
 - `<PathToParameterFile>` est le chemin d’accès au fichier azuredeploy.parameters.json créé à l’étape 1. Si votre nom de chemin comporte des espaces, placez ce paramètre entre guillemets doubles.
 - `<resourcegroupname>` est le nom du groupe de ressources existant auquel vous souhaitez ajouter un compte de base de données DocumentDB. 
 - `<deploymentname>` est le nom facultatif du déploiement.

Exemple d’entrée : 

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

OU, pour spécifier le paramètre de nom de compte de base de données sans un fichier de paramètres et être plutôt invité à entrer la valeur, exécutez la commande suivante :

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Exemple d’entrée qui affiche l’invite et une entrée pour un compte de base de données nommé samplearmacct :

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

Comme le compte est configuré, vous recevez les informations suivantes : 

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    + 
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

Si vous rencontrez des erreurs, consultez la section [Dépannage](#troubleshooting).  

Une fois que la commande a renvoyé un résultat, le compte est dans l’état **Mise à jour** pendant quelques minutes, avant de passer à l’état **En ligne** dans lequel il est prêt à être utilisé. Vous pouvez vérifier l’état du compte sur le panneau [Comptes DocumentDB](https://portal.azure.com)du **portail Azure** .

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez des erreurs semblables à `Deployment provisioning state was not successful` lors de la création de votre compte de base de données ou du groupe de ressources, vous pouvez résoudre le problème de plusieurs manières. 

> [!NOTE]
> La saisie de caractères non valides dans le nom du compte de base de données ou la spécification d’un emplacement dans lequel DocumentDB n’est pas disponible générera des erreurs de déploiement. Les noms de comptes de base de données ne peuvent contenir que des minuscules, des chiffres et le caractère « - », et doivent comporter entre 3 et 50 caractères. Tous les emplacements de compte de base de données valides sont répertoriés sur la [page Régions Azure](https://azure.microsoft.com/regions/#services).

- Si votre sortie contient le message suivant : `Error information has been recorded to C:\Users\wendy\.azure\azure.err`, passez en revue les informations relatives à l’erreur dans le fichier azure.err.

- Vous pouvez trouver des informations utiles dans le fichier journal du groupe de ressources. Pour afficher le fichier journal, exécutez la commande suivante :

        azure group log show <resourcegroupname> --last-deployment

    Exemple d’entrée :       azure group log show new_res_group --last-deployment

    Pour plus d’informations, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md) .

- Les informations relatives aux erreurs sont également disponibles dans le portail Azure, comme illustré dans la capture d’écran suivante. Pour accéder aux informations relatives aux erreurs : cliquez sur Groupes de ressources dans la barre de lancement, sélectionnez le groupe de ressources concerné par l’erreur, puis, dans la zone Éléments principaux du panneau Groupe de ressources, cliquez sur la date du dernier déploiement. Ensuite, dans le panneau Historique du déploiement, sélectionnez le déploiement ayant échoué, puis, dans le panneau Déploiement, cliquez sur le détail de l’opération marqué d’un point d’exclamation rouge. Le message d’état correspondant au déploiement ayant échoué s’affiche dans le panneau Détails de l’opération. 

    ![Capture d’écran du portail Azure montrant comment accéder au message d’erreur de déploiement](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png) 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un compte DocumentDB, l’étape suivante consiste à créer une base de données DocumentDB. Pour créer une base de données, utilisez au choix :

- Le portail Azure, tel que décrit dans la rubrique [Guide pratique pour créer une base de données et une collection DocumentDB à l’aide du portail Azure](documentdb-create-collection.md).
- les exemples .NET C# du projet [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) dans le dépôt [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) sur GitHub ;
- les [kits de développement logiciel (SDK) de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB possède les kits de développement logiciel (SDK) de .NET, Java, Python, Node.js et de l’API JavaScript. 

Après avoir créé la base de données, vous devez lui [ajouter une ou plusieurs collections](documentdb-create-collection.md), puis [ajouter des documents](documentdb-view-json-document-explorer.md) aux collections. 

Une fois que chaque collection contient des documents, vous pouvez utiliser [DocumentDB SQL](documentdb-sql-query.md) pour [exécuter des requêtes](documentdb-sql-query.md#ExecutingSqlQueries) sur vos documents à l’aide de [l’Explorateur de requête](documentdb-query-collections-query-explorer.md) du portail, de [l’API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de l’un des [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Pour en savoir plus sur DocumentDB, explorez les ressources suivantes :

-    [Parcours d’apprentissage de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-    [Modèle de ressource et concepts de DocumentDB](documentdb-resources.md)

Pour connaître les modèles supplémentaires utilisables, consultez [Modèles de démarrage rapide Microsoft Azure](https://azure.microsoft.com/documentation/templates/).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet

