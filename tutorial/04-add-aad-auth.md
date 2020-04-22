<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la [bibliothèque d’authentification Microsoft pour les angles](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) dans l’application.

1. Créez un fichier dans le `./src` répertoire nommé `oauth.ts` et ajoutez le code suivant.

    :::code language="typescript" source="../demo/graph-tutorial/src/oauth.ts.example":::

    Remplacez `YOUR_APP_ID_HERE` par l’ID de l’application dans le portail d’inscription des applications.

    > [!IMPORTANT]
    > Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `oauth.ts` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application.

1. Ouvrez `./src/app/app.module.ts` et ajoutez les instructions `import` suivantes en haut du fichier.

    ```TypeScript
    import { MsalModule } from '@azure/msal-angular';
    import { OAuthSettings } from '../oauth';
    ```

1. Ajoutez le `MsalModule` au `imports` tableau à l’intérieur `@NgModule` de la déclaration et INITIALISEz-le avec l’ID de l’application.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/app.module.ts" id="imports":::

## <a name="implement-sign-in"></a>Implémentation de la connexion

Dans cette section, vous allez créer un service d’authentification et mettre en œuvre la connexion et la déconnexion.

1. Exécutez la commande suivante dans votre interface CLI.

    ```Shell
    ng generate service auth
    ```

    En créant un service pour cela, vous pouvez facilement l’injecter dans les composants qui ont besoin d’accéder aux méthodes d’authentification.

1. Une fois la commande terminée, ouvrez `./src/app/auth.service.ts` le fichier et remplacez son contenu par le code suivant.

    ```TypeScript
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
            this.alertsService.add('Login failed', JSON.stringify(reason, null, 2));
          });

        if (result) {
          this.authenticated = true;
          // Temporary placeholder
          this.user = new User();
          this.user.displayName = "Adele Vance";
          this.user.email = "AdeleV@contoso.com";
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
            this.alertsService.add('Get token failed', JSON.stringify(reason, null, 2));
          });

        if (result) {
          // Temporary to display token in an error box
          this.alertsService.add('Token acquired', result.accessToken);
          return result.accessToken;
        }
        return null;
      }
    }
    ```

1. Ouvrez le `./src/app/nav-bar/nav-bar.component.ts` fichier et remplacez son contenu par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/nav-bar/nav-bar.component.ts" id="navBarSnippet" highlight="3,15-22,24,26-28,36-38,40-42":::

1. Ouvrez`./src/app/home/home.component.ts` et remplacez son contenu par ce qui suit.

    :::code language="typescript" source="snippets/snippets.ts" id="homeSnippet" highlight="3,12-19,21,23,25-27":::

Enregistrez vos modifications et actualisez le navigateur. Cliquez sur le bouton **cliquez ici pour vous connecter** et vous devez être redirigé vers `https://login.microsoftonline.com`. Connectez-vous avec votre compte Microsoft et acceptez les autorisations demandées. La page de l’application doit être actualisée, affichant le jeton.

### <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

Actuellement, le service d’authentification définit des valeurs constantes pour le nom d’affichage et l’adresse de messagerie de l’utilisateur. Maintenant que vous disposez d’un jeton d’accès, vous pouvez obtenir les détails de l’utilisateur à partir de Microsoft Graph pour que ces valeurs correspondent à l’utilisateur actuel.

1. Ouvrez `./src/app/auth.service.ts` et ajoutez l’instruction `import` suivante en haut du fichier.

    ```TypeScript
    import { Client } from '@microsoft/microsoft-graph-client';
    ```

1. Ajoutez une nouvelle fonction à la classe `AuthService` appelée `getUser`.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/auth.service.ts" id="getUserSnippet":::

1. Recherchez et supprimez le code suivant dans `getAccessToken` la méthode qui ajoute une alerte pour afficher le jeton d’accès.

    ```TypeScript
    // Temporary to display token in an error box
    this.alertsService.add('Token acquired', result);
    ```

1. Recherchez et supprimez le code suivant de `signIn` la méthode.

    ```TypeScript
    // Temporary placeholder
    this.user = new User();
    this.user.displayName = "Adele Vance";
    this.user.email = "AdeleV@contoso.com";
    ```

1. À la place, ajoutez le code suivant.

    ```TypeScript
    this.user = await this.getUser();
    ```

    Ce nouveau code utilise le kit de développement logiciel (SDK) Microsoft Graph pour obtenir les détails `User` de l’utilisateur, puis crée un objet à l’aide des valeurs renvoyées par l’appel de l’API.

1. Modifiez la `constructor` `AuthService` classe pour vérifier si l’utilisateur est déjà connecté et charger ses informations si c’est le cas. Remplacez le existant `constructor` par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/auth.service.ts" id="constructorSnippet" highlight="5-6":::

1. Supprimez le code temporaire de `HomeComponent` la classe. Ouvrez le `./src/app/home/home.component.ts` fichier et remplacez la fonction `signIn` existante par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/home/home.component.ts" id="signInSnippet" highlight="5-6":::

Maintenant, si vous enregistrez vos modifications et démarrez l’application, après vous être connecté, vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes connecté.

![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

Cliquez sur Avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **déconnexion** . Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.

![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>Stockage et actualisation des jetons

À ce stade, votre application a un jeton d’accès, qui est envoyé `Authorization` dans l’en-tête des appels d’API. Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph pour le compte de l’utilisateur.

Cependant, ce jeton est de courte durée. Le jeton expire une heure après son émission. Étant donné que l’application utilise la bibliothèque MSAL, vous n’avez pas besoin d’implémenter de logique d’actualisation ou de stockage de jetons. Le `MsalService` jeton est mis en cache dans le stockage du navigateur. La `acquireTokenSilent` méthode vérifie d’abord le jeton mis en cache et, s’il n’a pas expiré, il le renvoie. Si elle a expiré, elle effectue une demande silencieuse pour en obtenir une nouvelle.
