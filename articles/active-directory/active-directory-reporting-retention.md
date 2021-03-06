---
title: "Stratégies de rétention des rapports Azure Active Directory | Microsoft Docs"
description: "Stratégies de rétention des données de rapport dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 47594d1d435ee92d57150a01a99765bab0e87846
ms.openlocfilehash: 2ac62c2c04975167b243b49bf2c536900e77df73
ms.lasthandoff: 02/03/2017


---
# <a name="azure-active-directory-report-retention-policies"></a>Stratégies de rétention des rapports Azure Active Directory
*Cette documentation fait partie du guide [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*


Cette rubrique fournit des réponses aux questions plus courantes en rapport avec la conservation des données pour les différents rapports d’activité dans Azure Active Directory. 

Comment obtenir la collection des données d’activité démarrées ?

| Édition d’Azure AD | Début de la collection |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Lorsque vous vous inscrivez pour un abonnement |
| Azure AD Gratuit | La première fois que vous ouvrez le [panneau Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou utilisez les [API de création de rapports](https://aka.ms/aadreports)  |


Quand vos données d’activité sont-elles disponibles dans le portail Azure ?

- **Immédiatement** : si vous avez déjà travaillé avec des rapports dans le portail Azure Classic
- **Dans les 2 heures** : si vous n’avez pas encore activé la création de rapports dans le portail Azure Classic

Comment obtenir la collection des signaux de sécurité démarrés ?  
Pour les signaux de sécurité, le processus de collection démarre lorsque vous choisissez d’utiliser Identity Protection Center. 

Pendant combien de temps les données collectées sont stockées ?

**Rapports d’activité**    

| Rapport | Azure AD Gratuit | Azure AD Premium P1 | Azure AD Premium P2 |
| :--    | :--           | :--                | :--                |
| Audit de répertoire | 7 jours | 30 jours | 30 jours |
| Activité de connexion |    7 jours | 30 jours | 30 jours |

**Signaux de sécurité**

| Rapport | Azure AD Gratuit | Azure AD Premium P1 | Azure AD Premium P2 |
| :--    | :--           | :--                | :--                |
| Connexions risquées | 7 jours | 30 jours | 90 jours |



