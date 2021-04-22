# <a name="how-to-run-the-completed-project"></a>Comment exécuter le projet terminé

## <a name="prerequisites"></a>Configuration requise

Pour exécuter le projet terminé dans ce dossier, vous devez :

- [Node.js](https://nodejs.org) installé sur votre ordinateur de développement. Si vous n'avez pas Node.js, consultez le lien précédent pour obtenir les options de téléchargement. (**Remarque : ce** didacticiel a été écrit avec Node version 14.15.0. Les étapes de ce guide peuvent fonctionner avec d'autres versions, mais cela n'a pas été testé.)
- [Angular CLI](https://cli.angular.io/) installé sur votre ordinateur de développement.
- Soit un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, soit un compte scolaire ou scolaire Microsoft.

Si vous n'avez pas de compte Microsoft, deux options s'offrent à vous pour obtenir un compte gratuit :

- Vous pouvez [vous inscrire à un nouveau compte Microsoft personnel.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)
- Vous pouvez vous inscrire au programme pour les développeurs [Office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement Office 365 gratuit.

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a>Inscrire une application web auprès du Centre d'administration Azure Active Directory

1. Ouvrez un navigateur et accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com). Connectez-vous à l’aide d’un **compte personnel** (compte Microsoft) ou d’un **compte professionnel ou scolaire**.

1. Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis sélectionnez **Inscriptions d’applications** sous **Gérer**.

    ![Une capture d’écran des inscriptions d’applications ](/tutorial/images/aad-portal-app-registrations.png)

1. Sélectionnez **Nouvelle inscription**. Sur la page **Inscrire une application**, définissez les valeurs comme suit.

    - Définissez le **Nom** sur `Angular Graph Tutorial`.
    - Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
    - Sous **URI de redirection**, définissez la première flèche déroulante sur `Single-page application (SPA)`, et la valeur sur `http://localhost:4200`.

    ![Capture d’écran de la page Inscrire une application](/tutorial/images/aad-register-an-app.png)

1. Choisissez **Inscrire**. Dans la page didacticiel **Angular Graph,** copiez la valeur de l'ID **d'application (client)** et enregistrez-la. Vous en aurez besoin à l'étape suivante.

    ![Une capture d’écran de l’ID d’application de la nouvelle inscription d'application](/tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a>Configurer l’exemple

1. Renommons `oauth.ts.example` le fichier `oauth.ts` .
1. Modifiez `oauth.ts` le fichier et a apporter les modifications suivantes.
    1. Remplacez `YOUR_APP_ID_HERE` par **l'ID d'application** que vous avez obtenu à partir du portail d'inscription des applications.
1. Dans votre interface de ligne de commande, accédez à ce répertoire et exécutez la commande suivante pour installer les conditions requises.

    ```Shell
    npm install
    ```

## <a name="run-the-sample"></a>Exécution de l’exemple

1. Exécutez la commande suivante dans votre CLI pour démarrer l'application.

    ```Shell
    ng serve
    ```

1. Ouvrez un navigateur et accédez à `http://localhost:4200`.
