---
title: "Exemples d’interface de ligne de commande Azure - App Service | Microsoft Docs"
description: "Exemples d’interface de ligne de commande - App Service"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 03eea5d275b2b1539e2707b7e2739b5931e61858
ms.lasthandoff: 03/10/2017


---
# <a name="azure-cli-samples"></a>Exemples d’interface de ligne de commande Azure

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure.

| | |
|-|-|
|**Créer une application**||
| [Créer une application web et déployer le code à partir de GitHub](./scripts/app-service-cli-deploy-github.md)| Crée une application web Azure et déploie le code à partir d’un référentiel GitHub public. |
| [Créer une application web avec un déploiement continu à partir de GitHub](./scripts/app-service-cli-continuous-deployment-github.md)| Crée une application web Azure avec la publication continue à partir d’un référentiel GitHub dont vous êtes le propriétaire. |
| [Créer une application web et déployer le code à partir d’un référentiel Git](./scripts/app-service-cli-deploy-local-git.md) | Crée une application web Azure et configure la transmission de code de type push à partir d’un référentiel Git local. |
| [Créer une application web et déployer le code dans un environnement intermédiaire](./scripts/app-service-cli-deploy-staging-environment.md) | Crée une application web Azure avec un emplacement de déploiement pour les modifications de code intermédiaires. |
| [Créer une application web ASP.NET Core dans un conteneur Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md)| Crée une application web Azure sur Linux et charge une image Docker à partir de Docker Hub. |
|**Configurer l’application**||
| [Mapper un domaine personnalisé à une application web](./scripts/app-service-cli-configure-custom-domain.md)| Crée une application web Azure et mappe un nom de domaine personnalisé. |
| [Lier un certificat SSL personnalisé à une application web](./scripts/app-service-cli-configure-ssl-certificate.md)| Crée une application web Azure et lie le certificat SSL d’un nom de domaine personnalisé à celle-ci. |
|**Mettre à l’échelle une application**||
| [Mettre à l’échelle une application web manuellement](./scripts/app-service-cli-scale-manual.md) | Crée une application web Azure et la met à l’échelle entre 2 instances. |
| [Mettre à l’échelle une application web dans le monde entier avec une architecture haute disponibilité](./scripts/app-service-cli-scale-high-availability.md) | Crée deux applications web Azure dans deux régions géographiques différentes et les rend disponibles par le biais d’un point de terminaison unique à l’aide d’Azure Traffic Manager. |
|**Connecter l’application aux ressources**||
| [Connecter une application web à une instance SQL Database](./scripts/app-service-cli-app-service-sql.md)| Crée une application web Azure et une instance SQL Database, puis ajoute la chaîne de connexion de base de données aux paramètres d’application. |
| [Connecter une application web à un compte de stockage](./scripts/app-service-cli-app-service-storage.md)| Crée une application web Azure et un compte de stockage, puis ajoute la chaîne de connexion de stockage aux paramètres d’application. |
| [Connecter une application web à un cache redis](./scripts/app-service-cli-app-service-redis.md) | Crée une application web Azure et un cache redis, puis ajoute les détails de connexion redis aux paramètres d’application. |
| [Connecter une application web à un DocumentDB](./scripts/app-service-cli-app-service-documentdb.md) | Crée une application web Azure et un DocumentDB, puis ajoute les détails de connexion DocumentDB aux paramètres d’application. |
|**Surveiller l’application**||
| [Analyser une application web avec les journaux de serveur web](./scripts/app-service-cli-monitor.md) | Crée une application web Azure, active sa journalisation et télécharge les journaux sur votre ordinateur local. |
| | |
