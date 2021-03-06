---
title: "Azure Notification Hubs - Forum aux Questions (FAQ)"
description: "FAQ sur la conception et l&quot;implémentation de solutions sur Notification Hubs"
services: notification-hubs
documentationcenter: mobile
author: ysxu
manager: erikre
keywords: notification push, notifications push, notifications push iOS, notifications push android, push ios, push android
editor: 
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2017
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9f0e7f071f1aa8fdd95c4959eae884afc60644e9
ms.lasthandoff: 03/25/2017


---
# <a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Notifications Push avec Azure Notification Hubs - Forum aux Questions
## <a name="general"></a>Généralités
### <a name="0--what-is-the-resource-structure-of-notification-hubs"></a>0.  Quelle est la structure des ressources des Notification Hubs ?

Les Notification Hubs ont deux niveaux de ressource : les concentrateurs et les espaces de noms. Un concentrateur est une ressource push unique qui peut contenir les informations push interplateformes d’une application. Un espace de noms est une collection de concentrateurs dans une région.

Le mappage général recommandé fait correspondre chaque espace de noms à une application. Dans l’espace de noms, vous pouvez avoir un concentrateur de production qui fonctionne avec votre application de production, un concentrateur de test qui fonctionne avec votre application de test, etc.

### <a name="1--what-is-the-price-model-for-notification-hubs"></a>1.  Quel modèle de prix s’applique au service Notification Hubs ?
Pour les détails les plus récents, consultez la page [Tarification de Notification Hubs]. La facturation de Notification Hubs se fait au niveau de l’espace de noms (la structure des ressources ci-dessus explique ce qu’est un espace de noms) et propose trois niveaux :

* **Gratuit** – Il s’agit d’un bon point de départ pour explorer les fonctionnalités push. Ce niveau n’est pas recommandé pour les applications de production. 500 appareils et 1 million de notifications push sont inclus par espace de noms par mois avec aucun contrat de niveau de service.
* **De base** – Ce niveau, ou le niveau Standard est recommandé pour les applications de production plus petites. Par défaut, 200 000 appareils et 10 millions de notifications push sont inclus pour chaque espace de nom ; des possibilités de développement par quotas sont disponibles.
* **Standard** – Ce niveau est recommandé pour les applications de production de moyenne à grande taille. Par défaut, 10 millions d’appareils et 10 millions de notifications push sont inclus pour chaque espace de nom ; des possibilités de développement par quotas sont disponibles, ainsi que des fonctionnalités performantes de télémétrie.

Voici certaines fonctionnalités très intéressantes du niveau Standard :
* *Télémétrie enrichie* – Les Notification Hubs proposent une télémétrie par message pour effectuer le suivi des demandes d’opérations push et les commentaires Platform Notification System à déboguer.
* *Architecture mutualisée* – Vous pouvez travailler avec les informations d’identification PNS (Platform Notification System) au niveau de l’espace de noms. Cela vous permet de facilement répartir les locataires dans différents concentrateurs dans le même espace de noms.
* *Notification push planifiée* – Vous pouvez planifier l’envoi de notifications à tout moment.

### <a name="2--what-is-the-notification-hubs-sla"></a>2.  Qu’est-ce que le contrat SLA de Notification Hubs ?
Pour les niveaux **De base** et **Standard** du service Notification Hubs, nous garantissons qu’au moins 99,9 % du temps les applications correctement configurées peuvent envoyer des notifications Push ou exécuter des opérations de gestion d’inscriptions en ce qui concerne un Notification Hub déployé au sein d’un niveau pris en charge. Pour plus d’informations sur notre contrat SLA, voir la page [Contrat SLA de Notification Hubs](https://azure.microsoft.com/support/legal/sla/notification-hubs/) .

> [!NOTE]
> Étant donné que les notifications push dépendent de PNS tiers (APNS d’Apple, FCM de Google, etc.), il n’existe aucun contrat de niveau de service pour la remise de ces messages. Une fois que les Notification Hubs effectuent les envois en lots aux systèmes PNS (avec contrat de niveau de service), il revient aux systèmes PNS de faire en sorte que les notifications push soient remises (aucun contrat de niveau de service).

### <a name="3--which-customers-are-using-notification-hubs"></a>3.  Quels sont vos clients du service Notification Hubs ?
Parmi nos nombreux clients actuels de Notification Hubs figurent les suivants :

* Sochi 2014 : des centaines de groupes d’intérêt, plus de trois millions d’appareils, plus de 150 millions de notifications transmises en deux semaines. [Étude de cas Sochi]
* Skanska : [étude de cas Skanska]
* Seattle Times : [étude de cas Seattle Times]
* Mural.ly : [étude de cas Mural.ly]
* 7Digital : [étude de cas 7Digital]
* Applications Bing : des dizaines de millions d’appareils, trois millions de notifications envoyées par jour.

### <a name="4-how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>4. Comment mettre à niveau ou rétrograder mon concentrateur ou mon espace de noms pour modifier son niveau ?
Accédez au [portail Azure], aux espaces de noms des Notification Hubs ou aux Notification Hubs, cliquez sur la ressource que vous voulez mettre à jour, et accédez à l’option Niveau de tarification dans le volet de navigation. Vous pouvez choisir le niveau souhaité. Notez cependant les points suivants :
* Le nouveau niveau de tarification s’applique à *tous* les concentrateurs de l’espace de noms que vous utilisez.
* Si vous passez à un niveau inférieur et que votre nombre d’appareils dépasse la limite du niveau que vous souhaitez appliquer, vous devrez supprimer des appareils afin de respecter la limite avant de procéder à la rétrogradation.


## <a name="design--development"></a>Conception et développement
### <a name="1--which-server-side-platforms-do-you-support"></a>1.  Quelles plateformes côté service prenez-vous en charge ?
Nous avons des kits de développement logiciel (SDK) sur serveur pour .NET, Java, Node.js, PHP et Python. En outre, les API de Notification Hubs reposent sur les interfaces REST. Vous pouvez donc choisir de travailler directement avec les API REST si vous utilisez différentes plateformes ou que vous ne souhaitez pas de dépendance supplémentaire. Pour plus de détails, consultez la page [NH – API REST].

### <a name="2--which-client-platforms-do-you-support"></a>2.  Quelles plateformes clientes prenez-vous en charge ?
Nous prenons en charge l’envoi de notifications Push aux plateformes [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (par Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md) et [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Pour en savoir plus, visitez notre page [Didacticiels de prise en main].

### <a name="3--do-you-support-smsemailweb-notifications"></a>3.  Prenez-vous en charge les notifications web, par courrier électronique et par SMS ?
Notification Hubs a été principalement conçu pour permettre l’envoi de notifications à des applications mobiles, et ne propose pas de fonctionnalités d’envoi par e-mail ou par SMS. Toutefois, les plateformes de fournisseurs tiers qui proposent ces fonctionnalités peuvent être intégrées avec Notification Hubs pour l'envoi des notifications Push natives à l'aide d’[Azure Mobile Apps].

Notification Hubs ne fournit pas non plus de service prêt à l’emploi de remise de notification Push dans le navigateur. Les clients peuvent choisir d’implémenter cela en utilisant SignalR par dessus les plateformes prises en charge côté serveur. Si vous souhaitez envoyer des notifications à des applications de navigateur dans le bac à sable Chrome, consultez le [didacticiel sur les applications Chrome].

### <a name="4----what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4.    Quel est le lien entre Azure Mobile Apps et Azure Notification Hubs, et quand dois-je utiliser un service ou l’autre ?
Si vous disposez d’un serveur principal d’applications mobiles et souhaitez seulement ajouter la fonctionnalité d’envoi de notifications Push, vous pouvez utiliser Azure Notification Hubs. Si vous souhaitez configurer un tout nouveau serveur principal d’applications mobiles, songez à utiliser Azure Mobile Apps. Une application mobile Azure configure automatiquement un hub de notification, qui permet d’envoyer facilement des notifications Push à partir du serveur principal d’applications mobiles. La tarification d’Azure Mobile Apps inclut les frais de base d’un hub de notification. Vous ne payez que si vous dépassez le nombre de notifications Push prévu dans l’abonnement. Pour plus de détails sur les coûts, consultez la page [Tarification de App Service].

### <a name="5----how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5.    Combien d’appareils puis-je prendre en charge si j’envoie des notifications Push via Notification Hubs ?
Pour plus d’informations sur le nombre d’appareils pris en charge, voir la [Tarification de Notification Hubs] .

Pour certains scénarios, si vous devez prendre en charge plus de 10 000 000 d’appareils inscrits, veuillez [nous contacter](https://azure.microsoft.com/overview/contact-us/) directement afin que nous vous aidions à mettre à l’échelle votre solution.

### <a name="6----how-many-push-notifications-can-i-send-out"></a>6.    Combien de notifications Push puis-je envoyer ?
Selon le niveau sélectionné, Azure est automatiquement mis à l’échelle en fonction du nombre de notifications transitant par le système.

> [!NOTE]
> Le coût d’utilisation global peut augmenter en fonction du nombre de notifications Push diffusées. Assurez-vous que vous comprenez les limites de niveau existantes décrites dans la page [Tarification de Notification Hubs] .
> 
> 

Nos clients utilisent Notification Hubs pour envoyer des millions de notifications Push quotidiennement. Vous n’avez rien de spécial à faire pour mettre à l’échelle la portée de vos notifications Push tant que vous utilisez Azure Notification Hubs.

### <a name="7----how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7.    Combien de temps faut-il pour que des notifications Push atteignent mon appareil ?
Azure Notification Hubs peut traiter au moins **un million d’envois de notifications Push par minute** dans un scénario d’utilisation normale, où la charge entrante est relativement homogène et régulière. Ce taux peut varier en fonction du nombre de balises, de la nature des envois entrants et d’autres facteurs externes.

Pendant le délai de livraison estimé, le service est en mesure de calculer les cibles par plateforme et d’acheminer des messages vers les services de remise de notifications Push correspondants sur la base des balises/expressions de balises inscrites. Ensuite, ce sont les services de notifications Push (PNS) qui sont responsables de l'envoi des notifications vers l'appareil.

Un PNS ne garantit pas de contrat SLA pour la remise de notifications. Toutefois, généralement, une grande majorité des notifications Push sont remises aux appareils cibles en quelques minutes (généralement dans les 10 minutes) à partir du moment de leur envoi à notre plateforme. Dans quelques cas particuliers, le délai de remise peut être plus long.

> [!NOTE]
> Azure Notification Hubs applique une stratégie consistant à supprimer toutes les notifications Push qui ne peuvent pas être remises au PNS dans un délai de 30 minutes. Cela peut se produire pour plusieurs raisons, la plus courante étant une limitation imposée par le PNS sur votre application.
> 
> 

### <a name="8----is-there-any-latency-guarantee"></a>8.    Y a-t-il une garantie de latence ?
En raison de la nature même des notifications Push, qui sont remises par un service de notifications Push spécifique d’une plateforme externe, il n’y a aucune garantie de latence. En règle générale, la majorité des notifications Push sont remises après quelques minutes.

### <a name="9----what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9.    Quels sont les points à prendre en compte lors de la conception d’une solution avec des espaces de noms et Notification Hubs ?
#### <a name="mobile-appenvironment"></a>Application mobile/Environnement
* Vous devez prévoir un hub de notification par application mobile et par environnement.
* Dans un scénario d’architecture mutualisée, chaque locataire doit disposer d’un concentrateur distinct.
* Vous ne devez jamais utiliser le même hub de notification pour les environnements de test et de production, au risque de rencontrer plus tard des problèmes lors de l'envoi des notifications. Par exemple, Apple fournit des points de terminaison Push pour les environnements de bac à sable (sandbox) et de production, chaque environnement étant défini avec des informations d'identification distinctes.
* Par défaut, vous pouvez envoyer des notifications de test à vos appareils inscrits via le portail Azure ou le composant intégré Azure dans Visual Studio. Le seuil est défini sur 10 appareils sélectionnés au hasard dans le pool d’inscription.

> [!NOTE]
> Si un hub initialement configuré avec un certificat de bac à sable (sandbox) d’Apple était ensuite reconfiguré pour utiliser un certificat de production d’Apple, les anciens jetons de l’appareil ne seraient plus valides avec le nouveau certificat et provoqueraient l’échec des notifications Push. C'est pour cela qu'il est préférable de séparer vos environnements de test et de production, et d'utiliser des hubs différents pour chaque environnement.
> 
> 

#### <a name="pns-credentials"></a>Informations d’identification du PNS
quand une application mobile est inscrite sur le portail des développeurs d'une plateforme (par exemple, Apple ou Google), vous obtenez un identifiant et des jetons de sécurité pour cette application. Le serveur principal de l'application doit fournir ces informations aux services de notifications Push de la plateforme en question pour pouvoir envoyer des notifications Push aux appareils. Ces jetons de sécurité peuvent être fournis sous la forme de certificats (par exemple, pour Apple iOS ou Windows Phone) ou de clés de sécurité (pour Google Android ou Windows, notamment). Ils doivent être configurés dans Notification Hubs. Leur configuration s'effectue généralement au niveau du hub de notification, mais peut aussi être réalisée au niveau de l'espace de noms dans un scénario d'architecture mutualisée.

#### <a name="namespaces"></a>Espaces de noms
Des espaces de noms peuvent être utilisés dans le cadre du regroupement de déploiement.  Dans un scénario d’architecture mutualisée, ils permettent aussi de représenter tous les Notification Hubs de tous les locataires de la même application.

#### <a name="geo-distribution"></a>Géo-distribution
La géo-distribution n’est pas toujours indispensable dans les scénarios de notification Push. Notez que les divers services de notifications Push (APNS, GCM, etc.) qui responsables de la remise des notifications aux appareils ne sont pas distribués de manière uniforme.

Si votre application est utilisée dans le monde entier, vous pouvez créer plusieurs hubs dans différents espaces de noms afin de bénéficier de la disponibilité du service Notification Hubs au sein des différentes régions Azure dans le monde.

> [!NOTE]
> Comme cela augmente les coûts de gestion, en particulier en ce qui concerne les inscriptions, la géo-distribution n’est pas recommandée et ne doit être implémentée qu’en cas de besoin explicite.
> 
> 

### <a name="10----should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10.    Dois-je effectuer les inscriptions à partir du service principal d’application ou directement via des appareils clients ?
Il est utilise de réaliser les inscriptions à partir du service principal d’application si vous devez authentifier le client avant de l’inscrire ou si ce service doit créer ou modifier des balises selon une logique d’application particulière. Pour plus d’informations, consultez les pages [Aide sur l’inscription auprès du serveur principal] et [Aide sur l’inscription auprès du serveur principal - 2].

### <a name="11----what-is-the-push-notification-delivery-security-model"></a>11.    Quel est le modèle de sécurité de remise de notification Push ?
Azure Notification Hubs utilise un modèle de sécurité basé sur une [signature d’accès partagé (SAP)](../storage/storage-dotnet-shared-access-signature-part-1.md). Vous pouvez utiliser les jetons SAP au niveau de l’espace de noms racine ou au niveau granulaire de Notification Hubs. Ces jetons SAP peuvent être définis avec des règles d'autorisation différentes, par exemple, des autorisations pour envoyer les messages, des autorisations pour écouter les notifications, etc.  Pour plus de détails, consultez le document [NH - Modèle de sécurité].

### <a name="12----how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12.    Comment gérer une charge utile sensible dans les notifications Push ?
Toutes les notifications sont remises à des appareils cibles par les services de notification Push (PNS) des plateformes. Quand un expéditeur envoie une notification à Azure Notification Hubs, nous traitons la notification, puis la transmettons au PNS correspondant.

Toutes les connexions entre l'expéditeur, Azure Notifications Hubs et le PNS utilisent le protocole HTTPS.

> [!NOTE]
> Azure Notifications Hubs n'enregistre en aucune façon la charge utile du message.
> 
> 

Pour l’envoi des charges utiles sensibles, nous recommandons toutefois d’utiliser un modèle Push sécurisé qui fonctionne comme suit : l’expéditeur remet à l’appareil une notification ping dotée d’un ID de message mais sans la charge utile sensible. Lorsque l’application sur l’appareil reçoit cette charge utile, elle peut appeler directement une API sécurisée pour récupérer les détails du message. Un guide sur la façon d’implémenter le modèle décrit ci-dessus est également disponible à la page [NH - Didacticiel sur les notifications Push sécurisées].

## <a name="operations"></a>Opérations
### <a name="1----what-is-the-disaster-recovery-dr-story"></a>1.    En quoi consiste la récupération d'urgence ?
Nous assurons la couverture de la récupération d’urgence des métadonnées (nom de hub de notification, chaîne de connexion et autres informations critiques). Quand un scénario de récupération d’urgence est déclenché, les données d’inscription sont le **seul segment** de l’infrastructure de Notification Hubs qui est perdu. Vous devez implémenter une solution pour réinsérer ces données dans votre nouveau concentrateur après la récupération.

* *Étape 1* - Créez un Notification Hub secondaire dans un autre centre de données. Vous pouvez le créer juste au moment de l’événement de récupération d’urgence ou en créer après le départ. L’option que vous choisissez fait peu de différence, car la configuration du hub de notification est un processus relativement rapide, de l’ordre de quelques secondes. En avoir un dès le début permet de limiter l’impact de l’événement de récupération d’urgence sur vos capacités de gestion. Nous recommandons donc fortement cette option.
* *Étape 2* - Alimentez le Notification Hub secondaire avec les inscriptions du Notification Hub principal. Il est déconseillé de tenter de conserver les inscriptions sur les deux hubs et de les maintenir synchronisées au fur et à mesure de l’arrivée de nouvelles inscriptions. Cela ne fonctionne généralement pas bien en raison de la nature même des inscriptions qui sont destinées à expirer côté PNS. Les inscriptions sont supprimées de Notification Hubs lorsque le PNS nous signale qu'elles ont expiré ou qu'elles ne sont plus valides.  

Nous recommandons d'utiliser un serveur principal d'applications qui :

* conserve un ensemble donné d’inscriptions de son côté pour pouvoir effectuer une insertion en bloc dans le hub de notification secondaire en cas de récupération d’urgence,

**OU**

* obtient une sauvegarde régulière des inscriptions du hub principal, puis effectue une insertion en bloc dans le hub de notification secondaire.

> [!NOTE]
> Pour plus d’informations sur la fonctionnalité d’exportation/importation des inscriptions disponible au niveau Standard, voir [Exportation et modification d’inscriptions en bloc] .
> 
> 

Si vous n’avez pas de serveur principal, quand l’application démarre sur les appareils cibles, elle effectue une nouvelle inscription dans le hub de notification secondaire et, finalement, le hub de notification secondaire a tous les appareils actifs inscrits.

L’inconvénient est qu’un laps de temps s’écoule pendant lequel les appareils sur lesquels les applications n’ont pas été ouvertes ne reçoivent pas de notification.

### <a name="2----is-there-any-audit-log-capability"></a>2.    Existe-t-il une fonctionnalité de journal d'audit ?
Toutes les opérations de gestion concernant Notification Hubs sont enregistrées dans les journaux des opérations qui sont exposés dans le [portail Azure Classic].

## <a name="monitoring--troubleshooting"></a>Surveillance et dépannage
### <a name="1----what-troubleshooting-capabilities-are-available"></a>1.    Quelles sont les fonctionnalités proposées pour faciliter la résolution des problèmes ?
Azure Notification Hubs fournit plusieurs fonctionnalités vous permettant de résoudre des problèmes courants, en particulier ceux liés à la suppression de notifications, qui sont les plus fréquents. Pour plus de détails, voir notre livre blanc [Azure Notification Hubs : instructions relatives au diagnostic] .

### <a name="2----what-telemetry-features-are-available"></a>2.    Quelles sont les fonctionnalités de télémétrie proposées ?
Azure Notification Hubs permet d’afficher des données de télémétrie dans le [portail Azure Classic]. Pour plus de détails sur les mesures disponibles, voir la page [Métriques] .

> [!NOTE]
> Quand des notifications sont réussies, cela signifie simplement que les notifications Push ont été remises au service de notification Push externe (par exemple, APNS pour Apple, GCM pour Google, etc.). Il incombe au PNS de remettre la notification aux appareils cibles. En règle générale, le PNS n’expose pas les métriques de remise aux tiers.  
> 
> 

Nous offrons également la possibilité d’exporter les données de télémétrie par programme (au niveau de service **Standard** ). Pour plus de détails, voir les [exemples de métriques de Notification Hubs] .

[portail Azure Classic]: https://manage.windowsazure.com
[Tarification de Notification Hubs]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Étude de cas Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[étude de cas Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[étude de cas Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[étude de cas Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[étude de cas 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH – API REST]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - Getting Started Tutorials]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[didacticiel sur les applications Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Aide sur l’inscription auprès du serveur principal]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Aide sur l’inscription auprès du serveur principal - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[NH - Modèle de sécurité]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - Didacticiel sur les notifications Push sécurisées]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Azure Notification Hubs : instructions relatives au diagnostic]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Métriques]: https://msdn.microsoft.com/library/dn458822.aspx
[exemples de métriques de Notification Hubs]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Exportation et modification d’inscriptions en bloc]: https://msdn.microsoft.com/library/dn790624.aspx
[portail Azure]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[Tarification de App Service]: https://azure.microsoft.com/pricing/details/app-service/

