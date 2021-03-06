---
title: "Téléchargement d’un certificat de gestion API Azure | Microsoft Docs"
description: "Découvrez comment télécharger le certificat d’API de gestion pour le portail Azure Classic."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 783390791f27a9bf8ea400403a8b0f866f8c5cee
ms.openlocfilehash: 0f1ae486340270cac5cfdb5d44485d05cb996388


---
# <a name="upload-an-azure-management-api-management-certificate"></a>Téléchargement d’un certificat de gestion API dans Azure Management
Les certificats de gestion vous permettent de vous authentifier auprès de l’API de gestion des services fournie par Azure. De nombreux programmes et outils (tels que Visual Studio ou le Kit de développement logiciel (SDK) Azure) utilisent ces certificats pour automatiser la configuration et le déploiement de divers services Azure. **Cela s’applique uniquement au portail Azure Classic**.

> [!WARNING]
> Soyez prudent ! Ces types de certificat permettent à toute personne qui s’authentifie par leur biais de gérer l’abonnement auquel ils sont associés.
>
>

D’autres informations sur les certificats Azure (y compris la création d’un certificat auto-signé) sont [disponibles](cloud-services/cloud-services-certs-create.md#what-are-management-certificates) si vous en avez besoin.

Vous pouvez également utiliser [Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/) pour authentifier le code client à des fins d’automatisation.

## <a name="upload-a-management-certificate"></a>Charger un certificat de gestion
Une fois le certificat de gestion créé (fichier .cer contenant uniquement la clé publique). Vous pouvez charger ce dernier dans le portail. Lorsque le certificat est disponible sur le portail, toute personne disposant d’un certification adéquat (clé privée) peut se connecter par le biais de l’API de gestion et accéder aux ressources de l’abonnement associé.

1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com).
2. Veillez à bien sélectionner l’abonnement auquel vous souhaitez associer un certificat. Appuyez sur le texte **Abonnements** dans l’angle supérieur droit du portail.

    ![Paramètres](./media/azure-api-management-certs/subscription.png)
3. Une fois l’abonnement approprié sélectionné, appuyez sur **Paramètres** sur le côté gauche du portail (vous devrez peut-être faire défiler la page vers le bas).

    ![Paramètres](./media/azure-api-management-certs/settings.png)
4. Appuyez sur l’onglet **Certificats de gestion** .

    ![Paramètres](./media/azure-api-management-certs/certificates-tab.png)
5. Appuyez sur le bouton **Télécharger** .

    ![Paramètres](./media/azure-api-management-certs/upload.png)
6. Complétez la boîte de dialogue et appuyez sur la **coche**Terminé.

    ![Paramètres](./media/azure-api-management-certs/upload-dialog.png)

## <a name="next-steps"></a>Étapes suivantes
Un certificat de gestion étant désormais associé à un abonnement, vous pouvez (après avoir installé le certificat correspondant localement) vous connecter par programmation à l’ [API REST Gestion des services](https://msdn.microsoft.com/library/azure/mt420159.aspx) et automatiser les différentes ressources Azure associées à cet abonnement.



<!--HONumber=Dec16_HO1-->


