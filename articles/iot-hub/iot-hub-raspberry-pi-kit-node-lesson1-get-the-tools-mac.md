---
title: "Connecter Raspberry Pi (Node) à Azure IoT - Leçon 1 : Obtenir des outils (macOS) | Microsoft Docs"
description: "Téléchargez et installez les outils et logiciels nécessaires pour le premier exemple d’application pour Pi sur macOS."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "développement iot, logiciel iot, logiciel internet des objets, installer python mac, installer git sur mac, exécuter gulp, installer node js mac"
ms.assetid: 2ea6d211-c0e8-4ade-ac69-d1c2261d78c4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: f157a68f502f018bab83a377367f92ce20f89adf
ms.lasthandoff: 01/24/2017


---
# <a name="get-the-tools-macos-1010"></a>Obtenir les outils (macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 ou version ultérieure](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Procédure à suivre
Téléchargez les outils de développement et le logiciel pour le premier exemple d’application pour votre Raspberry Pi 3. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :

* Installation de Git et Node.js.
  * [Git](https://git-scm.com) est un système de contrôle de versions distribué open source très populaire. L’exemple d’application de cet article est stocké sur Git.
  * [Node.js](https://nodejs.org/en/) est un runtime JavaScript avec un écosystème de packages riche.
* Comment utiliser NPM pour installer des outils de développement Node.js supplémentaires.
  * La version minimale requise de Node.js est 4.5 LTS.
  * [NPM](https://www.npmjs.com) est l’un des gestionnaires de packages pour Node.js.

## <a name="what-you-need"></a>Ce dont vous avez besoin
Pour effectuer cette opération, vous avez besoin des éléments suivants :

* Une connexion Internet pour télécharger les outils de développement et le logiciel.
* Un Mac exécutant macOS Yosemite (10.10) ou version ultérieure.

## <a name="install-git-and-nodejs"></a>Installation de Git et Node.js
Pour installer Git et Node.js, servez-vous de l’utilitaire de gestion de package [Homebrew](http://brew.sh) en procédant comme suit :

1. Installer Homebrew. Si vous avez déjà installé Homebrew, passez à l’étape 2.
   
   1. Appuyez sur `Cmd + Space` puis entrez `Terminal` pour ouvrir une fenêtre de terminal.
   2. Exécutez la commande suivante :
      
      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```
2. Installez de Git et Node.js en exécutant la commande suivante :
   
   ```bash
   brew install node git
   ```

## <a name="install-additional-nodejs-development-tools"></a>Installation des outils de développement Node.js supplémentaires
Utilisez [gulp.js](http://gulpjs.com) pour automatiser le déploiement de l’exemple d’application sur Pi. Utilisez [device-discovery-cli](https://github.com/Azure/device-discovery-cli) (interface de ligne de commande de découverte d’appareils) pour récupérer les informations réseau concernant vos appareils IoT.

Installez `gulp` et `device-discovery-cli` en exécutant la commande suivante dans la fenêtre de terminal :

```bash
npm install -g device-discovery-cli gulp
```

Si vous rencontrez des problèmes pour installer Node.js et ces outils de développement supplémentaires sur macOS, pour trouver des solutions aux problèmes courants, voir le [guide de dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="install-visual-studio-code"></a>Installation de Visual Studio Code
[Téléchargez](https://code.visualstudio.com/docs/setup/osx) et installez Visual Studio Code. Visual Studio Code est un éditeur de code source simple mais puissant pour Windows, Linux et Mac OS. Vous utiliserez cet éditeur ultérieurement dans ce didacticiel pour modifier l’exemple de code.

## <a name="summary"></a>Résumé
Vous avez installé les outils de développement et le logiciel nécessaire pour le premier exemple d’application. La tâche suivante consiste à créer, déployer et exécuter l’exemple d’application sur Pi.

## <a name="next-steps"></a>Étapes suivantes
[Création et déploiement de l’exemple d’application de clignotement](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)


