---
title: "Créer le groupe de consommateurs et l’espace de noms Azure Event Hubs à l’aide d’un modèle | Microsoft Docs"
description: "Créer un espace de noms Event Hubs avec Event Hub et un groupe de consommateurs à l’aide de modèles Azure Resource Manager"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/07/2017
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 271eca35ad18725306410d1e8b81bdf06712db1f
ms.lasthandoff: 03/08/2017


---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Créer un espace de noms Event Hubs avec Event Hub et un groupe de consommateurs à l’aide d’un modèle Azure Resource Manager
Cet article montre comment utiliser un modèle Azure Resource Manager qui crée un espace de noms de type Event Hubs avec un Event Hub et un groupe de consommateurs. L’article montre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins

Pour en savoir plus sur la création de modèles, consultez [Création de modèles Azure Resource Manager][Authoring Azure Resource Manager templates].

Pour le modèle complet, consultez [Event Hub et modèle de groupe de consommateurs][Event Hub and consumer group template] sur GitHub.

> [!NOTE]
> Pour rechercher les derniers modèles, recherchez Event Hubs dans la galerie de [modèles de démarrage rapide Azure][Azure Quickstart Templates].
> 
> 

## <a name="what-will-you-deploy"></a>Qu'allez-vous déployer ?
Avec ce modèle, vous allez déployer un espace de noms Event Hubs avec un Event Hub et un groupe de consommateurs.

[Event Hubs](event-hubs-what-is-event-hubs.md) est un service de traitement des événements utilisé pour fournir des entrées d’événements et de télémétrie dans Azure à grande échelle, avec faible latence et fiabilité élevée.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres
Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée `Parameters` , qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient selon le projet que vous déployez, ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent identiques. Chaque valeur de paramètre dans le modèle définit les ressources déployées.

Le modèle définit les paramètres suivants.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName
Nom de l’espace de noms Event Hubs à créer.

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName
Nom du Event Hub créé dans l’espace de noms Event Hubs.

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName
Nom du groupe de consommateurs créé pour le Event Hub.

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion
Version d’API du modèle.

```json
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Ressources à déployer
Crée un espace de noms de type **Event Hubs**, avec un Event Hub et un groupe de consommateurs.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Interface de ligne de commande Azure
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Créer un Event Hub)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/

