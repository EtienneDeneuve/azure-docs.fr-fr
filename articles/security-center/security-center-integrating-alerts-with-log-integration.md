---
title: "Intégration des alertes de l’Azure Security Center avec les journaux Azure | Microsoft Docs"
description: "Cet article vous aidera à vous familiariser avec l’intégration des alertes du Centre de sécurité avec les journaux Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: fcd14b6b0afe08db82f2b8050beaf03ece7fd212
ms.lasthandoff: 03/24/2017


---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration"></a>Intégration des alertes de l’Azure Security Center avec les journaux Azure
De nombreuses équipes de sécurité et d’intervention utilisent une solution SIEM (Security Information and Event Management) comme point de départ pour le triage et l’examen des alertes de sécurité. Grâce à l’intégration des journaux Azure, les clients peuvent synchroniser quasiment en temps réel les alertes de l’Azure Security Center, ainsi que les événements de sécurité des machines virtuelles collectés par les journaux de diagnostics et d’audit Azure, avec leur solution SIEM ou Log Analytics.

L’intégration des journaux Azure fonctionne avec HP ArcSight, Splunk, IBM QRadar, entre autres.

## <a name="what-logs-can-i-integrate"></a>Quels journaux puis-je intégrer ?
Azure génère une journalisation complète pour chaque service. Ces journaux sont classés de la façon suivante :

* **Journaux de contrôle/gestion**, qui vous offrent une visibilité sur les opérations CREATE, UPDATE et DELETE d’Azure Resource Manager.
* **Journaux des plans de données**, qui vous offrent une visibilité sur les événements déclenchés lors de l’utilisation d’une ressource Azure. Il s’agit par exemple du journal des événements Windows : les journaux de sécurité et des applications dans une machine virtuelle.

L’intégration des journaux Azure prend actuellement en charge l’intégration de :

* Journaux des machines virtuelles Azure
* Journaux d’audit Azure
* Alertes Azure Security Center

## <a name="install-azure-log-integration"></a>Installer l’intégration des journaux Azure
Téléchargez [Intégration des journaux Azure](https://www.microsoft.com/download/details.aspx?id=53324).

Le service d’intégration des journaux Azure collecte les données de télémétrie à partir de l’ordinateur sur lequel il est installé.  Les données de télémétrie recueillies sont les suivantes :

* Les exceptions qui se produisent pendant l’exécution de l’intégration des journaux Azure
* Des métriques concernant le nombre de requêtes et d’événements traités
* Des statistiques sur les options de ligne de commande Azlog.exe utilisées

> [!NOTE]
> Vous pouvez désactiver la collecte des données de télémétrie en décochant cette option.
>
>

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Intégrer les alertes des journaux d’audit Azure et de Security Center
1. Ouvrez l’invite de commandes et **cd** dans **c:\Program Files\Microsoft Azure Log Integration**.
2. Exécutez la commande **azlog createazureid** pour créer un [Principal du service Azure Active Directory](../active-directory/active-directory-application-objects.md) dans les locataires Azure Active Directory (AD) qui hébergent les abonnements Azure.

    Vous êtes invité à entrer vos identifiants de connexion Azure.

   > [!NOTE]
   > Vous devez être le propriétaire ou un coadministrateur de l’abonnement.
   >
   >

    L’authentification à Azure s’effectue avec Azure AD.  La création d’un principal du service pour l’intégration de journaux Azure crée l’identité Azure AD qui aura un accès en lecture aux abonnements Azure.
3. Exécutez la commande **azlog authorize<SubscriptionID>** pour affecter un accès en lecture à l’abonnement au principal du service créé à l’étape 2. Si vous ne spécifiez pas de **SubscriptionID**, le principal du service se voit affecter le rôle Lecteur pour tous les abonnements auxquels vous avez accès.

   > [!NOTE]
   > Des avertissements peuvent s’afficher si vous exécutez la commande **authorize** immédiatement après la commande **createazureid**. Il existe un temps de latence entre la création du compte Azure AD et la disponibilité du compte pour une utilisation. Si vous patientez environ 10 secondes après l’exécution de la commande **createazureid** avant d’exécuter la commande **authorize**, vous ne devriez pas voir ces avertissements.
   >
   >
4. Vérifiez que les fichiers JSON de journaux d’audit sont présents dans les dossiers suivants :

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. Vérifiez que les alertes Security Center existent dans les dossiers suivants :

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. Pointez le connecteur de transfert de fichier SIEM standard vers le dossier approprié pour diriger les données vers l’instance SIEM. Reportez-vous aux [Configurations SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) correspondant à votre configuration SIEM.

Si vous avez des questions sur l’intégration des journaux Azure, envoyez un e-mail à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les journaux d’audit Azure et les définitions de propriétés, consultez :

* [Opérations d’audit avec Resource Manager](../azure-resource-manager/resource-group-audit.md)
* [Liste des événements de gestion dans un abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx) : pour récupérer les événements du journal d’audit.

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

