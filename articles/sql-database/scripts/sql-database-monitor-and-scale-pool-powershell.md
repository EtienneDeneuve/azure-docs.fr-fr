---
title: "Script Azure PowerShell - Surveillance et mise à l’échelle d’un pool élastique SQL | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Surveillance et mise à l’échelle d’un pool élastique SQL Database à l’aide de PowerShell"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: dd1866368301f75f2df82a5b775d9c7ae0eafb44
ms.lasthandoff: 03/30/2017

---

# <a name="monitor-and-scale-a-sql-database-elastic-pool-using-powershell"></a>Surveillance et mise à l’échelle d’un pool élastique SQL Database à l’aide de PowerShell

Cet exemple de script PowerShell surveille les mesures de performances d’un pool élastique, l’adapte à un niveau de performances supérieur et crée une règle d’alerte sur l’une des mesures de performances. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1 "Surveillance et mise à l’échelle d’une instance SQL Database unique")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | Crée un serveur logique qui héberge une base de données ou un pool élastique. |
| [New-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlelasticpool) | Crée un pool élastique au sein d’un serveur logique. |
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | Crée une base de données au sein d’un serveur logique en tant que base de données unique ou regroupée. |
| [Get-AzureRmMetric](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.5.0/get-azurermmetric) | Affiche les données de taille de la base de données.|
| [Add-AzureRMMetricAlertRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.5.0/add-azurermmetricalertrule) | Ajoute ou met à jour une règle d’alerte basée sur une mesure. |
| [Set-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqlelasticpool) | Met à jour les propriétés du pool élastique |
| [Add-AzureRMMetricAlertRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.5.0/add-azurermmetricalertrule) | Définit une règle d’alerte pour surveiller automatiquement les DTU à l’avenir. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell sur la page [Scripts PowerShell Azure SQL Database](../sql-database-powershell-samples.md).

