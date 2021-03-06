---
title: Exemples Azure Event Hubs | Microsoft Docs
description: "Exemples de hubs d’événements"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: f3c8f6e52b8713bcdb58d55e8bbc2301a7c316e4
ms.lasthandoff: 03/09/2017

---

# <a name="event-hubs-samples"></a>Exemples de hubs d’événements 

Les exemples de hubs d’événements présentent des fonctionnalités clés de [Azure Event Hubs](/azure/event-hubs/). Cette article attribue une catégorie et décrit les exemples disponibles, avec des liens vers chacun d’eux.

Au moment de la rédaction de cet article, les exemples de hubs d’événements se trouvent dans différents emplacements :

- [Exemples de code développeur MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples)

Pour plus d’informations sur les différentes versions de .NET Framework, consultez [Infrastructures et cibles](/dotnet/articles/standard/frameworks).

D’autres exemples vont être ajoutés au fil du temps, alors consultez régulièrement cette page pour vérifier les mises à jour.

## <a name="net-standard"></a>.NET Standard

Les exemples suivants montrent comment envoyer et recevoir des événements à l’aide du [client Event Hubs](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) pour la [bibliothèque .NET Standard](/dotnet/articles/standard/library).

### <a name="send-events"></a>Envoyer des événements 

L’exemple de [prise en main de l’envoi](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleSender) montre comment écrire une application console .NET Core qui envoie des événements vers un hub d’événements.

### <a name="receive-events"></a>Recevoir des événements 

L’exemple de [prise en main de la réception avec l’hôte du processeur d’événements](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver) est une application console .NET Core qui reçoit des messages à partir d’un hub d’événements à l’aide de `Event Processor Host`.

## <a name="net-framework"></a>.NET Framework    

Ces exemples présentent d’autres fonctionnalités d’Azure Event Hubs, ciblant la [bibliothèque .NET Framework](https://msdn.microsoft.com/library/w0x726c2.aspx).
 
### <a name="notify-users-of-events-received"></a>Avertir les utilisateurs de la réception d’événements

L’exemple [AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) avertit les utilisateurs de données reçues à partir des capteurs ou d’autres systèmes.

### <a name="get-started-with-event-hubs"></a>Prise en main des hubs d’événements 

L’exemple de [prise en main des hubs d’événements](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) montre les capacités de base des hubs d’événements, telles que la création d’un hub d’événements, l’envoi d’événements vers un hub d’événements et la consommation d’événements à l’aide de [l’hôte du processeur d’événements](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

### <a name="scale-out-event-processing"></a>Traitement d’événement mis à l’échelle 

L’exemple de [traitement d’événement mis à l’échelle](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) montre comment utiliser [l’hôte du processeur d’événements](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) pour distribuer la charge de travail de la consommation de flux de hubs d’événements. Il montre comment implémenter les objets **EventProcessor** et **EventProcessorFactory** pour gérer le flux d’événements. 

###  <a name="pull-data-from-sql-into-an-event-hub"></a>Extraire des données de SQL dans un hub d’événements

L’exemple [d’extraction de données SQL](https://github.com/Azure-Samples/event-hubs-dotnet-import-from-sql) montre comment extraire des données d’une table SQL et les distribuer vers un hub d’événements pour les utiliser comme entrée de vos applications analytiques en aval.

### <a name="pull-web-data-into-an-event-hub"></a>Extraire des données web dans un hub d’événements 

L’exemple [d’importation des données à partir du web](https://github.com/Azure-Samples/event-hubs-dotnet-importfromweb) montre comment extraire des données à partir de flux publics (par exemple, le flux d’informations sur la circulation du Ministère des transports) et les distribuer vers un hub d’événements.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les versions de .NET Framework, visitez les liens suivants :

- [Infrastructures et cibles](/dotnet/articles/standard/frameworks)
- [.NET Framework 4.6 et 4.5](https://msdn.microsoft.com/library/w0x726c2.aspx)

Pour plus d’informations sur les hubs d’événements, consultez les articles suivants :

- [Vue d’ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md)
- [Create an Event Hub](event-hubs-create.md) (Créer un Event Hub)
- [FAQ sur les hubs d'événements](event-hubs-faq.md)
