---
title: "Résolution de problèmes d’inscription à Azure | Microsoft Docs"
description: "Fournit des informations sur la résolution de certains problèmes d’inscription à Azure."
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a50b4fb4b19acf270aaf94fea613e745ec2af649
ms.lasthandoff: 03/22/2017

---
# <a name="troubleshoot-sign-up-issues-for-azure"></a>Résoudre les problèmes d’inscription pour Azure
Si vous ne pouvez pas vous inscrire à Azure, utilisez les conseils de cet article pour résoudre les problèmes courants. Si vous rencontrez un problème avec votre carte de crédit lors de l’inscription, consultez la page [Votre carte bancaire est refusée lors de l’inscription à Azure](billing-credit-card-fails-during-azure-sign-up.md). Si vous disposez d’un compte Azure, mais ne parvenez pas à vous connecter, consultez la page [Impossible de me connecter pour gérer mon abonnement Azure](billing-cannot-login-subscription.md).

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>La barre de progression se bloque dans la section « Vérification d’identité par carte »

Pour terminer la vérification d’identité par carte, les cookies tiers doivent être autorisés pour votre navigateur.

![Capture d’écran du blocage dans la section Vérification d’identité par carte pendant l’inscription](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

Effectuez la procédure suivante pour mettre à jour les paramètres de cookies de votre navigateur.

1. Si vous utilisez Chrome, accédez à **Paramètres** > **Afficher les paramètres avancés** > **Confidentialité** > **Paramètres de contenu**. Décochez **Bloquer les cookies et les données de site tiers**.
2. Si vous utilisez Edge, accédez à **Paramètres** > **Afficher les paramètres avancés** > **Cookies**. Sélectionnez **Ne pas bloquer les cookies**.
3. Actualisez la page d’inscription à Azure, puis vérifiez si le problème est résolu.
4. Si l’actualisation n’a pas résolu le problème, quittez et redémarrez votre navigateur, puis réessayez.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Aucun message texte ni appel lors de la vérification du compte à l’inscription
Si vous avez sélectionné **Envoyer un message texte**, il peut falloir jusqu'à quatre minutes pour que votre code texte arrive sur votre téléphone. Évidemment, pour la vérification d’identité, vous devez saisir un numéro de téléphone qui permet de recevoir des messages SMS ou, pour l’option **M’appeler**, des appels. Le numéro de téléphone que vous entrez est utilisé uniquement pour la vérification d’identité et n’est pas stocké sous forme de numéro de contact pour le compte.

Voici quelques conseils :
* Vous ne pouvez pas utiliser un numéro de téléphone VoIP pour le processus de vérification par téléphone.
* Vérifiez par deux fois le numéro de téléphone saisi, y compris l’indicatif du pays que vous avez sélectionné dans le menu déroulant.
* Si votre téléphone ne reçoit pas les messages texte (SMS), utilisez l’option **M’appeler**.
* Si l’étape de vérification par téléphone échoue lorsque vous essayez les options **Envoyer un message texte** et **M’appeler**, utilisez un autre numéro de téléphone.

Lorsque vous obtenez le message texte ou recevez l’appel, entrez le code que vous avez reçu dans la zone de texte.

## <a name="credit-card-declined-or-not-accepted"></a>Carte de crédit refusée
Les cartes de crédit ou de débit virtuelles ou prépayées ne sont pas acceptées comme mode de paiement pour les abonnements Azure. Pour voir les autres raisons qui peuvent conduire au refus de votre carte de crédit, consultez [Votre carte bancaire est refusée lors de l’inscription à Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="free-trial-is-not-available"></a>« L'essai gratuit n'est pas disponible »
Avez-vous utilisé un abonnement Azure auparavant ? Les conditions d’utilisation d’Azure n’autorisent l’activation que d’une seule évaluation gratuite pour tout utilisateur n’ayant jamais utilisé Azure. Si vous avez déjà souscrit un autre type d’abonnement Azure, vous ne pouvez pas activer une version d'évaluation gratuite. Inscrivez-vous à un [abonnement de type paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/).

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Je n’arrive pas à activer un plan d’avantages Azure de type MSDN, BizSpark, BizSparkPlus ou MPN
Assurez-vous que vous utilisez les bonnes informations de connexion. Consultez ensuite le programme d’avantages pour vous assurer que vous êtes éligible. 

* MSDN
  * Vérifiez l’état de votre éligibilité dans votre [page de compte MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  * Si vous ne pouvez pas vérifier votre état, contactez l’un des [Centres de service client des abonnements MSDN](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* BizSpark
  * Connectez-vous au [portail BizSpark](https://www.microsoft.com/bizspark/default.aspx#start-two) et vérifiez l’état de votre éligibilité pour BizSpark et BizSpark Plus.
  * Si vous ne pouvez pas vérifier votre éligibilité, vous pouvez [contacter l’équipe BizSpark](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)
* MPN
  * Connectez-vous au [portail MPN](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) et vérifiez l’état de votre éligibilité. Vous pouvez bénéficier d’avantages supplémentaires si vous disposez des [compétences de plateforme cloud](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx) appropriées.
  * Si vous ne pouvez pas vérifier votre état, contactez le [support MPN](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx).

## <a name="cant-activate-new-azure-in-open-subscription"></a>Je n’arrive pas à activer un nouvel abonnement Azure dans Open
Pour pouvoir créer un nouvel abonnement Azure dans Open, vous devez disposer d’une clé d’activation de service en ligne (OSA) valide associée à au moins un jeton Azure dans Open. Si vous ne disposez d’aucune clé OSA, contactez l’un des partenaires Microsoft répertoriés dans [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

