---
title: Exemples Azure PowerShell - App Service | Microsoft Docs
description: Exemples Azure PowerShell - App Service
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: c8167eaeff389a1bb77a8f13e522b1a2ce58aff8
ms.lasthandoff: 03/10/2017


---
# <a name="azure-powershell-samples"></a>Exemples Azure PowerShell

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide d’Azure PowerShell.

| | |
|-|-|
|**Créer une application**||
| [Créer une application web avec un déploiement à partir de GitHub](./scripts/app-service-powershell-deploy-github.md)| Crée une application web Azure qui extrait le code à partir de GitHub. |
| [Créer une application web avec un déploiement continu à partir de GitHub](./scripts/app-service-powershell-continuous-deployment-github.md)| Crée une application web Azure qui déploie du code en continu à partir de GitHub. |
| [Créer une application web et déployer du code avec FTP](./scripts/app-service-powershell-deploy-ftp.md) | Crée une application web Azure et télécharge des fichiers à partir d’un répertoire local via FTP. |
| [Créer une application web et déployer le code à partir d’un référentiel Git](./scripts/app-service-powershell-deploy-local-git.md) | Crée une application web Azure et configure la transmission de code de type push à partir d’un référentiel Git local. |
| [Créer une application web et déployer le code dans un environnement intermédiaire](./scripts/app-service-powershell-deploy-staging-environment.md) | Crée une application web Azure avec un emplacement de déploiement pour les modifications de code intermédiaires. |
|**Configurer l’application**||
| [Mapper un domaine personnalisé à une application web](./scripts/app-service-powershell-configure-custom-domain.md)| Crée une application web Azure et mappe un nom de domaine personnalisé. |
| [Lier un certificat SSL personnalisé à une application web](./scripts/app-service-powershell-configure-ssl-certificate.md)| Crée une application web Azure et lie le certificat SSL d’un nom de domaine personnalisé à celle-ci. |
|**Mettre à l’échelle une application**||
| [Mettre à l’échelle une application web manuellement](./scripts/app-service-powershell-scale-manual.md) | Crée une application web Azure et la met à l’échelle entre 2 instances. |
| [Mettre à l’échelle une application web dans le monde entier avec une architecture haute disponibilité](./scripts/app-service-powershell-scale-high-availability.md) | Crée deux applications web Azure dans deux régions géographiques différentes et les rend disponibles par le biais d’un point de terminaison unique à l’aide d’Azure Traffic Manager. |
|**Connecter l’application aux ressources**||
| [Connecter une application web à une instance SQL Database](./scripts/app-service-powershell-connect-to-sql.md)| Crée une application web Azure et une instance SQL Database, puis ajoute la chaîne de connexion de base de données aux paramètres d’application. |
| [Connecter une application web à un compte de stockage](./scripts/app-service-powershell-connect-to-storage.md)| Crée une application web Azure et un compte de stockage, puis ajoute la chaîne de connexion de stockage aux paramètres d’application. |
|**Surveiller l’application**||
| [Analyser une application web avec les journaux de serveur web](./scripts/app-service-powershell-monitor.md) | Crée une application web Azure, active sa journalisation et télécharge les journaux sur votre ordinateur local. |
| | |
