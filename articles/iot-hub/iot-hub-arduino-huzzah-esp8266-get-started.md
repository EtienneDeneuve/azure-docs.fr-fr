---
title: "ESP8266 vers cloud - Connecter la carte Feather HUZZAH ESP8266 à Azure IoT Hub | Microsoft Docs"
description: "Découvrez comment connecter un appareil Arduino, l’Adafruit Feather HUZZAH ESP8266, à Azure IoT Hub, service cloud Microsoft qui vous aide à gérer vos ressources IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 544f98afc1769f75bd4e06dc7b2bf8a1a0d91371
ms.lasthandoff: 03/30/2017


---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Connecter l’Adafruit Feather HUZZAH ESP8266 à Azure IoT Hub dans le cloud

![Connexion entre un capteur DHT22, une carte Feather HUZZAH ESP8266 et IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-will-do"></a>Procédure à suivre

Connectez l’Adafruit Feather HUZZAH ESP8266 à un IoT Hub. Ensuite, vous exécuterez un exemple d’application sur l’ESP8266 pour collecter des données de température et d’humidité provenant d’un capteur DHT22. Enfin, vous enverrez les données du capteur à votre IoT Hub.

> [!NOTE]
>Si vous utilisez d’autres cartes ESP8266, vous pouvez également suivre ces étapes pour les connecter à votre IoT Hub. Selon la carte ESP8266 que vous utilisez, il se peut que vous deviez reconfigurer le paramètre `LED_PIN`. Par exemple, si vous utilisez l’ESP8266 d’AI-Thinker, vous devrez peut-être remplacer la valeur `0` de ce paramètre par `2`. Vous n’avez pas encore de carte ? [Obtenez un starter kit Microsoft Azure IoT](http://azure.com/iotstarterkits).

## <a name="what-you-will-learn"></a>Contenu

* Création d’un IoT Hub et enregistrement d’un appareil pour la carte Feather HUZZAH ESP8266.
* Connexion du Feather HUZZAH ESP8266 au capteur et à votre ordinateur.
* Collecte des données du capteur en exécutant un exemple d’application sur la carte Feather HUZZAH ESP8266.
* Envoi des données du capteur à votre IoT Hub.

## <a name="what-you-will-need"></a>Éléments requis

![Composants requis pour le didacticiel](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Pour cette opération, vous aurez besoin des composants suivants de votre Kit de démarrage Feather HUZZAH ESP8266 :

* La carte Feather HUZZAH ESP8266.
* Un câble Micro USB Micro B vers USB Type A.

Vous aurez également besoin des éléments suivants pour votre environnement de développement :

* Un Mac ou un PC exécutant Windows ou Ubuntu.
* Un réseau sans fil auquel connecter la carte Feather HUZZAH ESP8266.
* Une connexion Internet pour télécharger l’outil de configuration.
* L’[IDE Arduino](https://www.arduino.cc/en/main/software) version 1.6.8 (ou ultérieure), les versions antérieures ne fonctionneront pas avec la bibliothèque AzureIoT.


Les éléments suivants sont facultatifs si vous n’avez pas de capteur. Vous avez également la possibilité d’utiliser des données de capteur simulées.

* Un capteur de température et d’humidité Adafruit DHT22.
* Une platine d’expérimentation.
* Des câbles de liaison M/M.

## <a name="create-an-iot-hub-and-register-a-device-for-feather-huzzah-esp8266"></a>Créer un IoT Hub et enregistrer un appareil pour la carte Feather HUZZAH ESP8266

### <a name="create-your-azure-iot-hub-in-the-azure-portal"></a>Créer votre Azure IoT Hub dans le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Cliquez sur **Nouveau** > **Internet des objets** > **IoT Hub**.

   ![Création du IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/3_iot-hub-creation.png)

1. Dans le volet **IoT Hub**, entrez les informations requises pour votre IoT Hub :

   ![Informations de base pour la création du IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/4_iot-hub-provide-basic-info.png)

   * **Nom** : Il s’agit du nom de votre IoT Hub. Si le nom saisi est valide, une coche verte s’affiche.
   * **Niveau de tarification et de mise à l’échelle** : sélectionnez le niveau F1 gratuit. Cette option suffit pour cette démonstration. Pour plus d’informations sur le niveau de tarification et de mise à l’échelle, consultez [cette page](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Groupe de ressources** : créez un groupe de ressources pour héberger l’IoT Hub ou utilisez-en un existant. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-portal.md).
   * **Emplacement** : sélectionnez l’emplacement le plus proche de l’endroit où vous créez l’IoT Hub.
   * **Épingler au tableau de bord** : cochez cette option pour pouvoir accéder facilement à votre IoT Hub à partir du tableau de bord.
1. Cliquez sur **Create**. La création du IoT Hub peut prendre plusieurs minutes. Vous pouvez suivre la progression dans le volet **Notifications**.

   ![Suivi de la progression de la création du IoT Hub dans le volet Notifications](media/iot-hub-arduino-huzzah-esp8266-get-started/5_iot-hub-monitor-creation-progress-notification-pane.png)

1. Une fois votre IoT Hub créé, cliquez dessus dans le tableau de bord. Notez la valeur **Hostname**, puis cliquez sur **Stratégies d’accès partagé**.

   ![Obtention du nom d’hôte de votre IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/6_iot-hub-get-hostname.png)

1. Dans le panneau **Stratégies d’accès partagé**, cliquez sur la stratégie **iothubowner**, puis copiez et notez la **chaîne de connexion** de votre IoT Hub qui sera utilisée ultérieurement. Pour plus d’informations, consultez [Contrôler l’accès à IoT Hub](iot-hub-devguide-security.md).

   ![Obtention de la chaîne de connexion de l’IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/7_iot-hub-get-connection-string.png)

### <a name="register-a-device-for-feather-huzzah-esp8266-in-your-iot-hub"></a>Enregistrer un appareil pour la carte Feather HUZZAH ESP8266 dans votre IoT Hub

Chaque IoT Hub a un registre des identités contenant des informations sur les appareils autorisés à se connecter au IoT Hub. Pour qu’un appareil puisse se connecter à un Hub, une entrée correspondant à cet appareil doit figurer dans le registre des identités de l’IoT Hub.

Dans cette section, vous utilisez l’outil d’interface de ligne de commande iothub-explorer pour enregistrer un appareil pour la carte Feather HUZZAH ESP8266 dans le registre des identités de votre IoT Hub.

> [!NOTE]
> iothub-explorer requiert Node.js 4.x ou version ultérieure pour fonctionner correctement.

Pour enregistrer un appareil pour la carte Feather HUZZAH ESP8266, procédez comme suit :

1. [Téléchargez](https://nodejs.org/en/download/) et installez la dernière version LTS de Node.js, y compris NPM.
1. Installez iothub-explorer à l’aide de NPM.

   * Windows 7 ou version ultérieure

     Lancez une invite de commandes en tant qu’administrateur. Installez iothub-explorer en exécutant la commande suivante :

     ```bash
     npm install -g iothub-explorer
     ```
   * Ubuntu 16.04 ou version ultérieure

     Ouvrez un terminal à l’aide du raccourci clavier Ctrl + Alt + T, puis exécutez la commande suivante :

     ```bash
     sudo npm install -g iothub-explorer
     ```
   * MacOS 10.1 ou une version ultérieure

     Ouvrez un terminal, puis exécutez la commande suivante :

     ```bash
     npm install -g iothub-explorer
     ```
1. Connectez-vous à votre IoT Hub en exécutant la commande suivante :

   ```bash
   iothub-explorer login [your iot hub connection string]
   ```
1. Enregistrez un nouvel appareil avec `deviceID` en tant que `new-device` et obtenez sa chaîne de connexion en exécutant la commande suivante :

   ```bash
   iothub-explorer create new-device --connection-string
   ```

Notez la chaîne de connexion de l’appareil enregistré.

> [!NOTE]
> Pour afficher la chaîne de connexion des appareils enregistrés, exécutez la commande `iothub-explorer list`.

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Connecter la carte Feather HUZZAH ESP8266 au capteur et à votre ordinateur

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Connecter un capteur de température et d’humidité DHT22 à la carte Feather HUZZAH ESP8266

Utilisez la platine d’expérimentation et les câbles de liaison pour effectuer la connexion comme suit. Si vous n’avez pas de capteur, ignorez cette section. Vous pourrez utiliser des données de capteur simulées à la place.

![Schéma des connexions](media/iot-hub-arduino-huzzah-esp8266-get-started/15_connections_on_breadboard.png)

Utilisez le câblage suivant pour les broches du capteur :

| Début (capteur)           | Fin (carte)           | Couleur du câble   |
| -----------------------  | ---------------------- | ------------: |
| VDD (broche 31F)            | 3V (broche 58H)           | Câble rouge     |
| DATA (broche 32F)           | GPIO 2 (broche 46A)       | Câble bleu    |
| GND (broche 34F)            | GND (broche 56I)          | Câble noir   |

Pour plus d’informations, consultez [Adafruit DHT22 sensor setup](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) (Configuration du capteur Adafruit DHT22) et [Adafruit Feather HUZZAH ESP8266 Pinouts](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts) (Disposition des broches de l’Adafruit Feather HUZZAH ESP8266).

Votre carte Feather HUZZAH ESP8266 devrait à présent être connectée à un capteur opérationnel.

![Connexion du capteur DHT22 à la carte Feather HUZZAH](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Connexion de la carte Feather HUZZAH ESP8266 à votre ordinateur

À l’aide du câble Micro USB vers USB Type A, connectez la carte Feather HUZZAH ESP8266 à votre ordinateur comme suit.

![Connexion de la carte Feather HUZZAH à votre ordinateur](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Ajouter des autorisations de port série (Ubuntu uniquement)

Si vous utilisez Ubuntu, assurez-vous que vous disposez des autorisations nécessaires pour utiliser le port USB de la carte Feather HUZZAH ESP826. Pour ajouter des autorisations de port série, procédez comme suit :

1. Dans un terminal, exécutez les commandes suivantes :

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Vous obtenez l’une des sorties suivantes :

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   Dans la sortie, `uucp` ou `dialout` correspond au nom du propriétaire du groupe du port USB.

1. Ajoutez l’utilisateur au groupe en exécutant la commande suivante :

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` est le nom du propriétaire du groupe que vous avez obtenu à l’étape précédente. `<username>` est le nom de votre utilisateur Ubuntu.

1. Déconnectez-vous de Ubuntu et connectez-vous à nouveau pour que la modification prenne effet.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Collecter les données du capteur et les envoyer à votre IoT Hub

Dans cette section, vous allez déployer et exécuter un exemple d’application sur la carte Feather HUZZAH ESP8266. L’exemple d’application fait clignoter la LED de la carte Feather HUZZAH ESP8266 et envoie les données de température et d’humidité collectées à partir du capteur DHT22 à votre IoT Hub.

### <a name="get-the-sample-application-from-github"></a>Obtenir l’exemple d’application à partir de GitHub

L’exemple d’application est hébergé sur GitHub. Clonez l’exemple de référentiel contenant l’exemple d’application à partir de GitHub. Pour cloner l’exemple de référentiel, procédez comme suit :

1. Ouvrez une invite de commandes ou une fenêtre de terminal.
1. Accédez au dossier à utiliser pour le stockage de l’exemple d’application.
1. Exécutez la commande suivante :

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Installez le package pour la carte Feather HUZZAH ESP8266 dans l’IDE Arduino :

1. Ouvrez le dossier où l’exemple d’application est stocké.
1. Ouvrez le fichier app.ino dans le dossier app de l’IDE Arduino.

   ![Ouverture de l’exemple d’application dans l’IDE Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/10_arduino-ide-open-sample-app.png)

1. Dans l’IDE Arduino, cliquez sur **Fichier** > **Préférences**.
1. Dans la boîte de dialogue **Préférences**, cliquez sur l’icône en regard de la zone de texte **URL de gestionnaire de cartes supplémentaires**.
1. Dans la fenêtre contextuelle, entrez l’URL suivante, puis cliquez sur **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Pointage vers l’URL d’un package dans l’IDE Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/11_arduino-ide-package-url.png)

1. Dans la boîte de dialogue **Préférences**, cliquez sur **OK**.
1. Cliquez sur **Outils** > **Type de carte** > **Gestionnaire de carte**, puis recherchez « esp8266 ».

   Le gestionnaire de cartes indique que ESP8266 est installé avec une version 2.2.0 ou ultérieure.

   ![Le package ESP8266 est installé](media/iot-hub-arduino-huzzah-esp8266-get-started/12_arduino-ide-esp8266-installed.png)

1. Cliquez sur **Outils** > **Type de carte** > **Adafruit HUZZAH ESP8266**.

### <a name="install-necessary-libraries"></a>Installer les bibliothèques nécessaires

1. Dans l’IDE Arduino, cliquez sur **Croquis** > **Inclure une bibliothèque** > **Gérer les bibliothèques**.
1. Recherchez les noms de bibliothèque suivants un par un. Pour chacune des bibliothèques trouvées, cliquez sur **Installer**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Vous n’avez pas de capteur DHT22 ?

L’exemple d’application permet de simuler des données de température et d’humidité si vous n’avez pas de capteur DHT22. Pour activer l’utilisation de données simulées par l’exemple d’application, procédez comme suit :

1. Ouvrez le fichier `config.h` dans le dossier `app`.
1. Recherchez la ligne de code suivante et remplacez la valeur `false` par `true` :
   ```c
   define SIMULATED_DATA true
   ```
   ![Configuration de l’exemple d’application pour utiliser des données simulées](media/iot-hub-arduino-huzzah-esp8266-get-started/13_arduino-ide-configure-app-use-simulated-data.png)

1. Enregistrez le fichier avec `Control-s`.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Déployer l’exemple d’application sur la carte Feather HUZZAH ESP8266

1. Dans l’IDE Arduino, cliquez sur **outil** > **Port**, puis cliquez sur le port série pour la carte Feather HUZZAH ESP8266.
1. Cliquez sur **Croquis** > **Téléverser** pour générer et déployer l’exemple d’application sur la carte Feather HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Entrer vos informations d’identification

Une fois le chargement terminé, suivez cette procédure pour entrer vos informations d’identification :

1. Dans l’IDE Arduino, cliquez sur **Outils** > **Moniteur série**.
1. Dans la fenêtre Moniteur série, vous pouvez remarquer la présence de deux listes déroulantes dans l’angle inférieur droit.
1. Pour la liste déroulante de gauche, sélectionnez **Pas de fin de ligne**.
1. Pour la liste déroulante de droite, sélectionnez **115200 baud**.
1. Dans la zone de saisie située en haut de la fenêtre Moniteur série, entrez les informations suivantes si elles vous sont demandées, puis cliquez sur **Envoyer**.
   * SSID Wi-Fi
   * Mot de passe Wi-Fi
   * Chaîne de connexion de l’appareil

> [!Note]
> Les informations d’identification sont stockées dans la mémoire EEPROM de la carte Feather HUZZAH ESP8266. Si vous cliquez sur le bouton de réinitialisation de la carte Feather HUZZAH ESP8266, l’exemple d’application vous demande si vous souhaitez effacer ces informations. Entrez `Y` pour les effacer. Vous serez alors à nouveau invité à fournir ces informations.

### <a name="verify-the-sample-application-is-running-successfully"></a>Vérifier que l’exemple d’application s’exécute correctement

Si vous voyez la sortie suivante dans la fenêtre Moniteur série et la LED clignoter sur la carte Feather HUZZAH ESP8266, l’exemple d’application s’exécute correctement.

![Sortie finale dans l’IDE Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement connecté une carte Feather HUZZAH ESP8266 à votre IoT Hub et envoyé les données de capteur collectées à votre IoT Hub. 

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

- [Gérer la messagerie de périphérique cloud avec iothub-explorer](iot-hub-explorer-cloud-device-messaging.md)
- [Enregistrer les messages IoT Hub dans le stockage de données Azure](iot-hub-store-data-in-azure-table-storage.md)
- [Visualiser des données de capteur en temps réel depuis Azure IoT Hub, à l’aide de Power BI](iot-hub-live-data-visualization-in-power-bi.md).
- [Utiliser Azure Web Apps pour visualiser les données de capteur en temps réel à partir d’Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

