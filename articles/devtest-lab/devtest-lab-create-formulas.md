---
title: "Créer des formules dans Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment créer des formules Azure DevTest Labs et les utiliser pour créer de nouvelles machines virtuelles."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 4f9f86c2cc106aa96f27293a43cd784e2fb6fe76
ms.lasthandoff: 03/17/2017


---
# <a name="create-azure-devtest-labs-formulas"></a>Créer des formules Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Cet article explique comment créer une formule à partir d’une base (une image personnalisée, une image Place de marché ou une autre formule) ou d’une machine virtuelle existante. 

## <a name="create-a-formula"></a>Créer une formule
Toute personne disposant des autorisations *Utilisateurs* de DevTest Labs est en mesure de créer des machines virtuelles en utilisant une formule comme base. Il existe deux façons de créer des formules : 

* À partir d’une base : quand vous voulez définir toutes les caractéristiques de la formule.
* À partir d’une machine virtuelle de laboratoire existante : quand vous voulez créer une formule à partir des paramètres d’une machine virtuelle existante.

Pour plus d’informations sur l’ajout des utilisateurs et des autorisations, consultez [Ajouter des propriétaires et des utilisateurs dans Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Créer une formule à partir d’une base
Les étapes suivantes vous guideront tout au long du processus de création d’une formule à partir d’une image personnalisée, image Marketplace ou autre formule.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste.

3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  

4. Dans le panneau du laboratoire, sélectionnez **Formules (bases réutilisables)**.
   
    ![Menu de formule](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Dans le panneau **Formules**, sélectionnez **+ Ajouter**.
   
    ![Ajouter une formule](./media/devtest-lab-create-formulas/add-formula.png)

6. Dans le panneau **Choisir une base** , sélectionnez la base (l’image personnalisée, l’image Marketplace ou la formule) à partir de laquelle vous voulez créer la formule.
   
    ![Liste de base](./media/devtest-lab-create-formulas/base-list.png)

7. Sur le panneau **Créer une formule** , spécifiez les valeurs suivantes :
   
    * **Nom de la formule** : entrez un nom pour votre formule. Cette valeur s’affiche dans la liste des images de base lorsque vous créez une machine virtuelle. Le nom est validé au fur et à mesure que vous le tapez. S’il n’est pas valide, un message indique les exigences d’un nom valide.
    * **Description** : entrez une description significative pour votre formule. Cette valeur est disponible dans le menu contextuel de la formule quand vous créez une machine virtuelle.
    * **Nom d’utilisateur** - Entrez un nom d’utilisateur qui obtient les privilèges d’administrateur.
    * **Mot de passe** - Entrez, ou sélectionnez dans la liste déroulante, une valeur qui est associée au secret (mot de passe) que vous souhaitez utiliser pour l’utilisateur spécifié. Pour plus d’informations sur les secrets, consultez [Azure DevTest Labs: Personal secret store](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/) (Azure DevTest Labs : magasin des secrets personnel).
    * **Type de disque de machine virtuelle** : sélectionnez HDD (disque dur) ou SSD (disque SSD) pour indiquer le type de disque de stockage autorisé pour les machines virtuelles approvisionnées à l’aide de cette image de base.
    * **Taille de la machine virtuelle** - Sélectionnez l’un des éléments prédéfinis qui spécifient les cœurs du processeur, la taille de la RAM et la taille du disque dur de la machine virtuelle à créer. 
    * **Artefacts** - Sélectionnez cette option pour ouvrir le panneau **Ajouter des artefacts**, dans lequel vous pouvez sélectionner et configurer les artefacts que vous souhaitez ajouter à l’image de base. Pour plus d’informations sur les artefacts, consultez [Gérer les artefacts de machine virtuelle dans Azure DevTest Labs](./devtest-lab-add-vm-with-artifacts.md).
    * **Paramètres avancés** - Sélectionnez cette option pour ouvrir le panneau **Avancé** dans lequel vous configurez les paramètres suivants :
        * **Réseau virtuel** - Spécifiez le réseau virtuel souhaité.
        * **Sous-réseau** - Spécifiez le sous-réseau souhaité.    
        * **Configuration de l’adresse IP** - Spécifiez si vous souhaitez que les adresses IP soient partagées, privées ou publiques. Pour plus d’informations sur les adresses IP partagées, consultez [Comprendre les adresses IP partagées dans Azure DevTest Labs](./devtest-lab-shared-ip.md).
        * **Make this machine claimable** (Rendre cette machine exigible) - Lorsqu’une machine est « exigible », cela signifie qu’aucune propriété ne lui sera affectée au moment de la création. En revanche, les utilisateurs du laboratoire seront en mesure de prendre possession de la machine (de la « revendiquer ») dans le panneau du laboratoire.     
    * **Image** : ce champ affiche le nom de l'image de base que vous avez sélectionnée dans le panneau précédent. 
     
       ![Créer une formule](./media/devtest-lab-create-formulas/create-formula.png)

8. sélectionnez **Créer** pour créer la formule.

9. Une fois la formule créée, elle s’affiche dans la liste sur le panneau **Formules**.

### <a name="create-a-formula-from-a-vm"></a>Créer une formule depuis une machine virtuelle
Les étapes suivantes vous guident dans le processus de création d’une formule à partir d’une machine virtuelle existante. 

> [!NOTE]
> Pour créer une formule à partir d’une machine virtuelle, la machine virtuelle doit avoir été créée après le 30 mars 2016. 
> 
> 

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  
4. Dans le panneau **Présentation** du laboratoire, sélectionnez la machine virtuelle à partir de laquelle vous souhaitez créer la formule.
   
    ![Machines virtuelles de labo](./media/devtest-lab-create-formulas/my-vms.png)
5. Dans le panneau de la machine virtuelle, cliquez sur **Créer une formule (base réutilisable)**.
   
    ![Créer une formule](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Dans le panneau **Créer une formule**, entrez un **Nom** et une **Description** pour votre nouvelle formule.
   
    ![Panneau Créer une formule](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Sélectionnez **OK** pour créer la formule.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Billets de blog connexes
* [Images personnalisées ou formules ?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Étapes suivantes
- [Add a VM to a lab in Azure DevTest Labs](devtest-lab-add-vm.md) (Ajouter une machine virtuelle à un laboratoire dans Azure DevTest Labs)
- [Gérer les formules DevTest Labs pour créer des machines virtuelles](devtest-lab-manage-formulas.md)
