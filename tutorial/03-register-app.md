<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez créer une inscription d'application web Azure AD à l'aide du Centre d'administration Azure Active Directory.

1. Ouvrez un navigateur et accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com). Connectez-vous à l’aide d’un **compte personnel** (compte Microsoft) ou d’un **compte professionnel ou scolaire**.

1. Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis sélectionnez **Inscriptions d’applications** sous **Gérer**.

    ![Une capture d’écran des inscriptions d’applications ](./images/aad-portal-app-registrations.png)

1. Sélectionnez **Nouvelle inscription**. Sur la page **Inscrire une application**, définissez les valeurs comme suit.

    - Définissez le **Nom** sur `Angular Graph Tutorial`.
    - Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
    - Sous **URI de redirection**, définissez la première flèche déroulante sur `Single-page application (SPA)`, et la valeur sur `http://localhost:4200`.

    ![Capture d’écran de la page Inscrire une application](./images/aad-register-an-app.png)

1. Sélectionner **Inscription**. Dans la page didacticiel **Angular Graph,** copiez la valeur de l'ID **d'application (client)** et enregistrez-la. Vous en aurez besoin à l'étape suivante.

    ![Une capture d’écran de l’ID d’application de la nouvelle inscription d'application](./images/aad-application-id.png)
