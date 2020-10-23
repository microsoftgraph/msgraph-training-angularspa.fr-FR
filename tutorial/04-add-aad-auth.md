<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la [bibliothèque d’authentification Microsoft pour les angles](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) dans l’application.

1. Créez un fichier dans le répertoire **./SRC** nommé **OAuth. TS** et ajoutez le code suivant.

    :::code language="typescript" source="../demo/graph-tutorial/src/oauth.example.ts":::

    Remplacez `YOUR_APP_ID_HERE` par l’ID de l’application dans le portail d’inscription des applications.

    > [!IMPORTANT]
    > Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le fichier **. TS OAuth** du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application.

1. Ouvrez **./SRC/app/app.module.TS** et ajoutez les `import` instructions suivantes en haut du fichier.

    ```typescript
    import { MsalModule } from '@azure/msal-angular';
    import { OAuthSettings } from '../oauth';
    ```

1. Ajoutez le `MsalModule` au tableau à l' `imports` intérieur de la `@NgModule` déclaration et initialisez-le avec l’ID de l’application.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/app.module.ts" id="imports" highlight="6-11":::

## <a name="implement-sign-in"></a>Implémentation de la connexion

Dans cette section, vous allez créer un service d’authentification et mettre en œuvre la connexion et la déconnexion.

1. Exécutez la commande suivante dans votre interface CLI.

    ```Shell
    ng generate service auth
    ```

    En créant un service pour cela, vous pouvez facilement l’injecter dans les composants qui ont besoin d’accéder aux méthodes d’authentification.

1. Une fois la commande terminée, ouvrez **/src/App/auth.service.TS** et remplacez son contenu par le code suivant.

    ```typescript
    import { Injectable } from '@angular/core';
    import { MsalService } from '@azure/msal-angular';

    import { AlertsService } from './alerts.service';
    import { OAuthSettings } from '../oauth';
    import { User } from './user';

    @Injectable({
      providedIn: 'root'
    })

    export class AuthService {
      public authenticated: boolean;
      public user: User;

      constructor(
        private msalService: MsalService,
        private alertsService: AlertsService) {

        this.authenticated = false;
        this.user = null;
      }

      // Prompt the user to sign in and
      // grant consent to the requested permission scopes
      async signIn(): Promise<void> {
        let result = await this.msalService.loginPopup(OAuthSettings)
          .catch((reason) => {
            this.alertsService.addError('Login failed', JSON.stringify(reason, null, 2));
          });

        if (result) {
          this.authenticated = true;
          // Temporary placeholder
          this.user = new User();
          this.user.displayName = 'Adele Vance';
          this.user.email = 'AdeleV@contoso.com';
          this.user.avatar = '/assets/no-profile-photo.png';
        }
      }

      // Sign out
      signOut(): void {
        this.msalService.logout();
        this.user = null;
        this.authenticated = false;
      }

      // Silently request an access token
      async getAccessToken(): Promise<string> {
        let result = await this.msalService.acquireTokenSilent(OAuthSettings)
          .catch((reason) => {
            this.alertsService.addError('Get token failed', JSON.stringify(reason, null, 2));
          });

        if (result) {
          // Temporary to display token in an error box
          this.alertsService.addSuccess('Token acquired', result.accessToken);
          return result.accessToken;
        }

        // Couldn't get a token
        this.authenticated = false;
        return null;
      }
    }
    ```

1. Ouvrez **./SRC/App/nav-bar/NAV-bar.Component.TS** et remplacez son contenu par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/nav-bar/nav-bar.component.ts" id="navBarSnippet" highlight="3,15-22,24,26-28,36-38,40-42":::

1. Ouvrez **./SRC/App/Home/Home.Component.TS** et remplacez son contenu par ce qui suit.

    :::code language="typescript" source="snippets/snippets.ts" id="homeSnippet" highlight="3,12-19,21,23,25-32":::

Enregistrez vos modifications et actualisez le navigateur. Cliquez sur le bouton **cliquez ici pour vous connecter** et vous devez être redirigé vers `https://login.microsoftonline.com` . Connectez-vous avec votre compte Microsoft et acceptez les autorisations demandées. La page de l’application doit être actualisée, affichant le jeton.

### <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

Actuellement, le service d’authentification définit des valeurs constantes pour le nom d’affichage et l’adresse de messagerie de l’utilisateur. Maintenant que vous disposez d’un jeton d’accès, vous pouvez obtenir les détails de l’utilisateur à partir de Microsoft Graph pour que ces valeurs correspondent à l’utilisateur actuel.

1. Ouvrez **./SRC/App/auth.service.TS** et ajoutez les `import` instructions suivantes en haut du fichier.

    ```typescript
    import { Client } from '@microsoft/microsoft-graph-client';
    import * as MicrosoftGraph from '@microsoft/microsoft-graph-types';
    ```

1. Ajoutez une nouvelle fonction à la classe `AuthService` appelée `getUser`.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/auth.service.ts" id="getUserSnippet":::

1. Recherchez et supprimez le code suivant dans la `getAccessToken` méthode qui ajoute une alerte pour afficher le jeton d’accès.

    ```typescript
    // Temporary to display token in an error box
    this.alertsService.addSuccess('Token acquired', result);
    ```

1. Recherchez et supprimez le code suivant de la `signIn` méthode.

    ```typescript
    // Temporary placeholder
    this.user = new User();
    this.user.displayName = "Adele Vance";
    this.user.email = "AdeleV@contoso.com";
    this.user.avatar = '/assets/no-profile-photo.png';
    ```

1. À la place, ajoutez le code suivant.

    ```typescript
    this.user = await this.getUser();
    ```

    Ce nouveau code utilise le kit de développement logiciel (SDK) Microsoft Graph pour obtenir les détails de l’utilisateur, puis crée un `User` objet à l’aide des valeurs renvoyées par l’appel de l’API.

1. Modifiez la `constructor` classe pour `AuthService` vérifier si l’utilisateur est déjà connecté et charger ses informations si c’est le cas. Remplacez le existant `constructor` par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/auth.service.ts" id="constructorSnippet" highlight="5-6":::

1. Supprimez le code temporaire de la `HomeComponent` classe. Ouvrez **./SRC/App/Home/Home.Component.TS** et remplacez la `signIn` fonction existante par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/home/home.component.ts" id="signInSnippet":::

Maintenant, si vous enregistrez vos modifications et démarrez l’application, après vous être connecté, vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes connecté.

![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

Cliquez sur Avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **déconnexion** . Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.

![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>Stockage et actualisation des jetons

À ce stade, votre application a un jeton d’accès, qui est envoyé dans l' `Authorization` en-tête des appels d’API. Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph pour le compte de l’utilisateur.

Cependant, ce jeton est de courte durée. Le jeton expire une heure après son émission. Étant donné que l’application utilise la bibliothèque MSAL, vous n’avez pas besoin d’implémenter de logique d’actualisation ou de stockage de jetons. Le `MsalService` jeton est mis en cache dans le stockage du navigateur. La `acquireTokenSilent` méthode vérifie d’abord le jeton mis en cache et, s’il n’a pas expiré, il le renvoie. Si elle a expiré, elle effectue une demande silencieuse pour en obtenir une nouvelle.
