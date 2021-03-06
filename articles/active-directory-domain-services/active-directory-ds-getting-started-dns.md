---
title: "Services de domaine Azure AD : Activer les paramètres DNS pour le réseau virtuel Azure | Microsoft Docs"
description: Prise en main des services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 16a0019990d4839a15acb4dcac747147a6d55e1d
ms.lasthandoff: 03/07/2017


---
# <a name="azure-ad-domain-services---update-dns-settings-for-the-azure-virtual-network"></a>Azure AD Domain Services : Mettre à jour les paramètres DNS pour le réseau virtuel Azure
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tâche 4 : mettre à jour les paramètres DNS pour le réseau virtuel Azure
Dans les tâches de configuration précédentes, vous avez activé Azure AD Domain Services pour votre annuaire. La tâche suivante consiste à s’assurer que les ordinateurs du réseau virtuel peuvent se connecter et utiliser ces services. Mettez à jour les paramètres du serveur DNS de votre réseau virtuel afin qu’il pointe vers les deux adresses IP pour lesquelles Azure AD Domain Services est disponible sur le réseau virtuel.

> [!NOTE]
> Notez les adresses IP des services de domaine Azure AD affichées dans l’onglet **Configurer** de votre annuaire, après avoir activé les services de domaine Azure AD pour l’annuaire.
>
>

Effectuez les étapes de configuration suivantes pour mettre à jour le paramètre du réseau DNS pour le réseau virtuel sur lequel vous souhaitez activer Azure AD Domain Services.

1. Accédez au **portail Azure Classic** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Sélectionnez le nœud **Réseaux** dans le volet gauche.

    ![Nœud Réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. Dans l’onglet **Réseaux virtuels** , sélectionnez le réseau virtuel sur lequel vous avez activé les services de domaine Azure AD afin d’afficher ses propriétés.
4. Cliquez sur l'onglet **Configurer** .

    ![Nœud Réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. Dans la section **Serveurs DNS** , entrez les adresses IP des services de domaine Azure AD.
6. Vérifiez que vous entrez les adresses IP qui étaient affichées dans la section **Services de domaine** de l’onglet **Configurer** de votre annuaire.
7. Pour enregistrer les paramètres de serveur DNS pour ce réseau virtuel, cliquez sur **Enregistrer** dans le volet des tâches au bas de la page.

   ![Mettez à jour les paramètres du serveur DNS pour le réseau virtuel.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> Après la mise à jour des paramètres de serveur DNS pour le réseau virtuel, la mise à jour de la configuration DNS sur les machines virtuelles sur le réseau peut prendre du temps. Si une machine virtuelle ne peut pas se connecter au domaine, vous pouvez vider le cache DNS (par ex. 'ipconfig /flushdns') sur la machine virtuelle. Cette commande force une actualisation des paramètres DNS sur la machine virtuelle.
>
>

## <a name="task-5---enable-password-synchronization-to-azure-ad-domain-services"></a>Tâche 5 : activer la synchronisation du mot de passe pour les services de domaine Azure AD
La tâche de configuration suivante consiste à [activer la synchronisation de mot de passe pour Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).

