---
title: "Exemple de script Azure CLI - Créer une application web et déployer du code à partir d’un référentiel Git local | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une application web et déployer du code à partir d’un référentiel Git local"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 048f98aa-f708-44cb-9b9e-953f67dc6da8
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 10404f2814b167dfd54869f4b6e58f911adbdc8d
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Créer une application web et déployer du code à partir d’un référentiel Git local

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis déploie votre code d’application web dans un référentiel Git local.

Si nécessaire, installez l’interface Azure CLI en suivant les instructions du [Guide d’installation Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), puis exécutez `az login` pour créer une connexion avec Azure. En outre, votre code d’application doit être validé dans un dépôt Git local.

Cet exemple fonctionne dans une interface d’interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans le client Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[principal](../../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh?highlight=3-5 "Créer une application web et déployer du code à partir d’un référentiel Git")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crée une application web Azure. |
| [az appservice web deployment user set](https://docs.microsoft.com/cli/azure/appservice/web/deployment/user#set) | Définit les informations d’identification de déploiement au niveau des comptes pour App Service. |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config-local-git) | Crée une configuration de contrôle source pour un référentiel Git local. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Ouvre une application web Azure dans un navigateur. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).
