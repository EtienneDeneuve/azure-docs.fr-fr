---
title: "Événements dans les microservices Azure basés sur acteur | Microsoft Docs"
description: "Présentation des événements pour Reliable Actors Service Fabric"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: amanbha
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: 92df9505416c5b4326f007dbf4b920f2c7ec3bd8
ms.lasthandoff: 01/24/2017


---
# <a name="actor-events"></a>Événements d’acteurs
Les événements d’acteur sont un moyen d’envoyer des notifications Meilleur effort de l’acteur aux clients. Les événements d’acteur sont conçus pour la communication acteur-client et ne doivent pas être utilisés pour la communication acteur-acteur.

L’extrait de code suivant montre comment utiliser les événements d’acteur dans votre application.

Définissez une interface qui décrit les événements publiés par l'acteur. Cette interface doit être dérivée de l'interface `IActorEvents` . Les arguments des méthodes doivent être [sérialisables en contrat de données](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Les méthodes doivent retourner une valeur nulle, car les notifications d’événement sont unidirectionnelles et Meilleur effort.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Déclarez les événements publiés par l'acteur dans l'interface d'acteur.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

Côté client, implémentez le gestionnaire d'événements.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

Sur le client, créez un proxy pour l'acteur qui publie l'événement et s'abonne à ses événements.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

En cas de basculement, l’acteur peut basculer sur un nœud ou processus différent. Le proxy de l’acteur gère les abonnements actifs et s’y réabonne automatiquement. Vous pouvez contrôler l’intervalle de réabonnement via l’API `ActorProxyEventExtensions.SubscribeAsync<TEvent>` . Pour vous désabonner, utilisez l’API `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` .

Sur l'acteur, publiez simplement les événements à mesure qu'ils se produisent. Le runtime Actors envoie la notification aux abonnés à l’événement, le cas échéant.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## <a name="next-steps"></a>Étapes suivantes
* [Réentrance des acteurs](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostics et surveillance des performances d’acteur](service-fabric-reliable-actors-diagnostics.md)
* [Documentation de référence de l’API d’acteur](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exemple de code](https://github.com/Azure/servicefabric-samples)

