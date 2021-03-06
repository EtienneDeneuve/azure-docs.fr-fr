---
title: "Didacticiel : intégration d’Azure Active Directory à SkyDesk Email | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SkyDesk Email."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: fcea07a412de7b35931ff95b01fbe1276302f1ea
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Didacticiel : Intégration d’Azure Active Directory à SkyDesk Email
L’objectif de ce didacticiel est de vous montrer comment intégrer SkyDesk Email à Azure AD (Azure Active Directory).

L’intégration de SkyDesk Email à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à SkyDesk Email
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SkyDesk Email (via l’authentification unique) avec leur compte Azure AD
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec SkyDesk Email, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement SkyDesk Email pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SkyDesk Email à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-skydesk-email-from-the-gallery"></a>Ajout de SkyDesk Email à partir de la galerie
Pour configurer l’intégration de SkyDesk Email à Azure AD, vous devez ajouter SkyDesk Email disponible dans la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SkyDesk Email à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **SkyDesk Email**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_01.png)
7. Dans le volet de résultats, sélectionnez **SkyDesk Email**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L'objectif de cette section est de vous montrer comment configurer et tester l'authentification unique Azure AD avec SkyDesk Email avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SkyDesk Email équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur SkyDesk Email associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans SkyDesk Email.

Pour configurer et tester l’authentification unique Azure AD avec SkyDesk Email, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test SkyDesk Email](#creating-a-Skydesk-Email-test-user)** pour avoir un équivalent de Britta Simon dans SkyDesk Email lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application SkyDesk Email.

**Pour configurer l’authentification unique Azure AD avec SkyDesk Email, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **SkyDesk Email**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à SkyDesk Email**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_04.png) 

   1. Dans la zone de texte URL de connexion, tapez l’URL entrée par vos utilisateurs pour se connecter à votre application SkyDesk Email au format suivant: **« https://mail.skydesk.jp/portal/\<nom d’entreprise\>»**.
   2. Cliquez sur **Next**.

4. Dans la page de configuration **Configurer l’authentification unique sur SkyDesk Email** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_05.png) 
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
    b. Cliquez sur **Next**.
5. Pour activer l’authentification unique dans **SkyDesk Email**, procédez comme suit :
   1. Connectez-vous à votre compte SkyDesk Email en tant qu’administrateur.
   2. Dans le menu situé en haut, cliquez sur Setup, puis sélectionnez Org. 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)  
   3. Cliquez sur Domains dans le volet de gauche.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)
   4. Cliquez sur Add Domain.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)
   5. Entrez votre nom de domaine et vérifiez le domaine.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)
   6. Cliquez sur **Authentification SAML** dans le panneau de gauche.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)
6. Dans la page de boîte de dialogue **SAML Authentication** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
   > [!NOTE]
   > Pour utiliser l’authentification SAML, un **domaine vérifié** ou **l’URL du portail** doit être configuré. Vous pouvez définir l’URL du portail avec un nom unique.
   > 
   > 
   
    ![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)

   1. Dans le portail classique Azure AD, copiez la valeur **URL SSO SAML**, puis collez-la dans la zone de texte **Login URL (URL de connexion)**.
   2. Dans le portail classique Azure AD, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **Logout URL (URL de déconnexion)**.
   3. **Modifier l’URL de mot de passe** est facultative, vous pouvez donc laisser cette valeur vide.
   4. Cliquez sur **Get Key From File (Obtenir la clé à partir d’un fichier)** pour sélectionner votre certificat SkyDesk Email téléchargé, puis sur **Ouvrir** pour charger le certificat.
   5. Comme **Algorithme**, sélectionnez **RSA**.
   6. Cliquez sur **OK** pour enregistrer les modifications.

7. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
8. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_06.png) 
   
   1. Dans la zone de texte **First Name**, tapez **Britta**. 
   2. Dans la zone de texte **Last Name**, tapez **Simon**.  
   3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_08.png) 
   
   1. Notez la valeur du **Nouveau mot de passe**.
   2. Cliquez sur **Terminé**.   

### <a name="creating-a-skydesk-email-test-user"></a>Création d’un utilisateur de test SkyDesk Email
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SkyDesk Email.

1. Cliquez sur **User Access** (Accès utilisateur) dans le panneau de gauche de SkyDesk Email, puis entrez votre nom d’utilisateur. 

![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Si vous avez besoin de créer des utilisateurs en bloc, contactez l’équipe de support SkyDesk Email.
>


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à SkyDesk Email.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à SkyDesk Email, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Azure Classic, cliquez dans l’affichage de l’annuaire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **SkyDesk Email**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque SkyDesk Email dans le volet d’accès, vous devez être connecté automatiquement à votre application SkyDesk Email.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_205.png

