<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l'application de l'exercice précédent pour prendre en charge l'authentification avec Azure AD. Cette étape est nécessaire pour obtenir le jeton d'accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la bibliothèque d'authentification [Microsoft pour Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) dans l'application.

1. Créez un fichier dans le répertoire **./src** nommé **oauth.ts** et ajoutez le code suivant.

    :::code language="typescript" source="../demo/graph-tutorial/src/oauth.example.ts":::

    `YOUR_APP_ID_HERE`Remplacez-le par l'ID de l'application à partir du portail d'inscription des applications.

    > [!IMPORTANT]
    > Si vous utilisez un contrôle source tel que Git, il est temps d'exclure le fichier **oauth.ts** du contrôle source afin d'éviter toute fuite accidentelle de votre ID d'application.

1. Ouvrez **./src/app/app.module.ts** et ajoutez les `import` instructions suivantes en haut du fichier.

    ```typescript
    import { IPublicClientApplication,
             PublicClientApplication,
             BrowserCacheLocation } from '@azure/msal-browser';
    import { MsalModule,
             MsalService,
             MSAL_INSTANCE } from '@azure/msal-angular';
    import { OAuthSettings } from '../oauth';
    ```

1. Ajoutez la fonction suivante sous les `import` instructions.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/app.module.ts" id="MSALFactorySnippet":::

1. Ajoutez `MsalModule` le tableau à `imports` l'intérieur de la `@NgModule` déclaration.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/app.module.ts" id="ImportsSnippet" highlight="6":::

1. Ajoutez le `MSALInstanceFactory` tableau et le tableau à `MsalService` `providers` l'intérieur de la `@NgModule` déclaration.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/app.module.ts" id="ProvidersSnippet" highlight="2-6":::

## <a name="implement-sign-in"></a>Implémentation de la connexion

Dans cette section, vous allez créer un service d'authentification et implémenter la sign-in et la sign-out.

1. Exécutez la commande suivante dans votre CLI.

    ```Shell
    ng generate service auth
    ```

    En créant un service pour cela, vous pouvez facilement l'injecter dans tous les composants qui ont besoin d'accéder aux méthodes d'authentification.

1. Une fois la commande terminé, ouvrez **./src/app/auth.service.ts** et remplacez son contenu par le code suivant.

    ```typescript
    import { Injectable } from '@angular/core';
    import { AccountInfo } from '@azure/msal-browser';
    import { MsalService } from '@azure/msal-angular';

    import { AlertsService } from './alerts.service';
    import { OAuthSettings } from '../oauth';
    import { User } from './user';

    @Injectable({
      providedIn: 'root'
    })

    export class AuthService {
      public authenticated: boolean;
      public user?: User;

      constructor(
        private msalService: MsalService,
        private alertsService: AlertsService) {

        this.authenticated = false;
        this.user = undefined;
      }

      // Prompt the user to sign in and
      // grant consent to the requested permission scopes
      async signIn(): Promise<void> {
        const result = await this.msalService
          .loginPopup(OAuthSettings)
          .toPromise()
          .catch((reason) => {
            this.alertsService.addError('Login failed',
              JSON.stringify(reason, null, 2));
          });

        if (result) {
          this.msalService.instance.setActiveAccount(result.account);
          this.authenticated = true;
          // Temporary placeholder
          this.user = new User();
          this.user.displayName = 'Adele Vance';
          this.user.email = 'AdeleV@contoso.com';
          this.user.avatar = '/assets/no-profile-photo.png';
        }
      }

      // Sign out
      async signOut(): Promise<void> {
        await this.msalService.logout().toPromise();
        this.user = undefined;
        this.authenticated = false;
      }

      // Silently request an access token
      async getAccessToken(): Promise<string> {
        const result = await this.msalService
          .acquireTokenSilent({
            scopes: OAuthSettings.scopes
          })
          .toPromise()
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
        return '';
      }
    }
    ```

1. Ouvrez **./src/app/nav-bar/nav-bar.component.ts** et remplacez son contenu par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/nav-bar/nav-bar.component.ts" id="navBarSnippet" highlight="3,15-22,24,34-36,38-40":::

1. Ouvrez **./src/app/home/home.component.ts** et remplacez son contenu par ce qui suit.

    :::code language="typescript" source="snippets/snippets.ts" id="homeSnippet" highlight="3,13-20,22,26-33":::

Enregistrez vos modifications et actualisez le navigateur. Cliquez sur **le bouton Cliquer ici pour vous inscrire** et vous devez être redirigé vers `https://login.microsoftonline.com` . Connectez-vous avec votre compte Microsoft et consentez aux autorisations demandées. La page de l'application doit s'actualiser et afficher le jeton.

### <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

Pour l'instant, le service d'authentification définit des valeurs constantes pour le nom d'affichage et l'adresse e-mail de l'utilisateur. Maintenant que vous avez un jeton d'accès, vous pouvez obtenir des détails utilisateur à partir de Microsoft Graph afin que ces valeurs correspondent à l'utilisateur actuel.

1. Ouvrez **./src/app/auth.service.ts** et ajoutez les `import` instructions suivantes en haut du fichier.

    ```typescript
    import { Client } from '@microsoft/microsoft-graph-client';
    import * as MicrosoftGraph from '@microsoft/microsoft-graph-types';
    ```

1. Ajoutez une nouvelle fonction à la classe `AuthService` appelée `getUser`.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/auth.service.ts" id="GetUserSnippet":::

1. Recherchez et supprimez le code suivant dans la méthode qui ajoute une `getAccessToken` alerte pour afficher le jeton d'accès.

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

    Ce nouveau code utilise le SDK Microsoft Graph pour obtenir les détails de l'utilisateur, puis crée un objet à l'aide des valeurs renvoyées par `User` l'appel d'API.

1. Modifiez la classe pour vérifier si l'utilisateur est déjà connecté et chargez ses `constructor` `AuthService` détails si c'est le cas. Remplacez `constructor` l'existant par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/auth.service.ts" id="ConstructorSnippet" highlight="5-7":::

1. Supprimez le code temporaire de la `HomeComponent` classe. Ouvrez **./src/app/home/home.component.ts** et remplacez la fonction `signIn` existante par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/home/home.component.ts" id="SignInSnippet":::

Maintenant, si vous enregistrez vos modifications et démarrez l'application, après vous être connectez, vous devez revenir sur la page d'accueil, mais l'interface utilisateur doit changer pour indiquer que vous êtes connecté.

![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

Cliquez sur l'avatar de l'utilisateur dans le coin supérieur droit pour accéder au lien **de** connexion. Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.

![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>Stockage et actualisation des jetons

À ce stade, votre application dispose d'un jeton d'accès, qui est envoyé dans l'en-tête des `Authorization` appels d'API. Il s'agit du jeton qui permet à l'application d'accéder à Microsoft Graph au nom de l'utilisateur.

Cependant, ce jeton est de courte durée. Le jeton expire une heure après son émission. Étant donné que l'application utilise la bibliothèque MSAL, vous n'avez pas besoin d'implémenter de logique de stockage ou d'actualisation de jeton. Le `MsalService` jeton est mis en cache dans le stockage du navigateur. La méthode vérifie d'abord le jeton mis en cache et, s'il `acquireTokenSilent` n'a pas expiré, elle le renvoie. Si elle a expiré, elle effectue une demande silencieuse pour en obtenir une nouvelle.
