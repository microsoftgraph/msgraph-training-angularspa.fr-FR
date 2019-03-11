<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l'application de l'exercice précédent pour prendre en charge l'authentification avec Azure AD. Cela est nécessaire pour obtenir le jeton d'accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la [bibliothèque d'authentification Microsoft pour les angles](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) dans l'application.

Créez un fichier dans le `./src` répertoire nommé `oauth.ts` et ajoutez le code suivant.

```TypeScript
export const OAuthSettings = {
  appId: 'YOUR_APP_ID_HERE',
  scopes: [
    "user.read",
    "calendars.read"
  ]
};
```

Remplacez `YOUR_APP_ID_HERE` par l'ID de l'application dans le portail d'inscription des applications.

> [!IMPORTANT]
> Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d'exclure le `oauth.ts` fichier du contrôle de code source afin d'éviter une fuite accidentelle de votre ID d'application.

Ouvrez `./src/app/app.module.ts` et ajoutez les instructions `import` suivantes en haut du fichier.

```TypeScript
import { MsalModule } from '@azure/msal-angular';
import { OAuthSettings } from '../oauth';
```

Ajoutez ensuite le `MsalModule` au `imports` tableau à l'intérieur `@NgModule` de la déclaration et INITIALISEz-le avec l'ID de l'application.

```TypeScript
imports: [
  BrowserModule,
  AppRoutingModule,
  NgbModule,
  FontAwesomeModule,
  MsalModule.forRoot({
    clientID: OAuthSettings.appId
  })
],
```

## <a name="implement-sign-in"></a>Mettre en œuvre la connexion

Commencez par définir une classe `User` simple pour conserver les informations sur l'utilisateur que l'application affiche. Créez un fichier dans le `./src/app` dossier nommé `user.ts` et ajoutez le code suivant.

```TypeScript
export class User {
  displayName: string;
  email: string;
  avatar: string;
}
```

Créez maintenant un service d'authentification. En créant un service pour cela, vous pouvez facilement l'injecter dans les composants qui ont besoin d'accéder aux méthodes d'authentification. Exécutez la commande suivante dans votre interface CLI.

```Shell
ng generate service auth
```

Une fois la commande terminée, ouvrez `./src/app/auth.service.ts` le fichier et remplacez son contenu par le code suivant.

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
    let result = await this.msalService.loginPopup(OAuthSettings.scopes)
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
    let result = await this.msalService.acquireTokenSilent(OAuthSettings.scopes)
      .catch((reason) => {
        this.alertsService.add('Get token failed', JSON.stringify(reason, null, 2));
      });

    // Temporary to display token in an error box
    if (result) this.alertsService.add('Token acquired', result);
    return result;
  }
}
```

Maintenant que vous disposez du service d'authentification, il peut être injecté dans les composants qui se connectent. Commencez par le `NavBarComponent`. Ouvrez le `./src/app/nav-bar/nav-bar.component.ts` fichier et effectuez les modifications suivantes.

- Ajouter `import { AuthService } from '../auth.service';` en haut du fichier.
- Supprimez `authenticated` les `user` propriétés et de la classe, puis supprimez le code qui les `ngOnInit`définit.
- Injectez `AuthService` le en ajoutant le paramètre suivant à `constructor`: `private authService: AuthService`.
- Remplacez la méthode `signIn` existante par ce qui suit:

    ```TypeScript
    async signIn(): Promise<void> {
      await this.authService.signIn();
    }
    ```

- Remplacez la méthode `signOut` existante par ce qui suit:

    ```TypeScript
    signOut(): void {
      this.authService.signOut();
    }
    ```

Une fois que vous avez fini, le code doit ressembler à ce qui suit.

```TypeScript
import { Component, OnInit } from '@angular/core';

import { AuthService } from '../auth.service';

@Component({
  selector: 'app-nav-bar',
  templateUrl: './nav-bar.component.html',
  styleUrls: ['./nav-bar.component.css']
})
export class NavBarComponent implements OnInit {

  // Should the collapsed nav show?
  showNav: boolean;

  constructor(private authService: AuthService) { }

  ngOnInit() {
    this.showNav = false;
  }

  // Used by the Bootstrap navbar-toggler button to hide/show
  // the nav in a collapsed state
  toggleNavBar(): void {
    this.showNav = !this.showNav;
  }

  async signIn(): Promise<void> {
    await this.authService.signIn();
  }

  signOut(): void {
    this.authService.signOut();
  }
}
```

Étant donné que vous `authenticated` avez `user` supprimé les propriétés et sur la classe, vous devez également `./src/app/nav-bar/nav-bar.component.html` mettre à jour le fichier. Ouvrez ce fichier et effectuez les modifications suivantes.

- Remplacez toutes les instances de `authenticated` par `authService.authenticated`.
- Remplacez toutes les instances `user` de `authService.user`par.

Mettez à jour `HomeComponent` la classe. Apportez les mêmes modifications `./src/app/home/home.component.ts` que celles que vous avez apportées `NavBarComponent` à la classe avec les exceptions suivantes.

- Il n'existe `signOut` pas de méthode `HomeComponent` dans la classe.
- Remplacez la `signIn` méthode par une version légèrement différente. Ce code appelle `getAccessToken` pour obtenir un jeton d'accès, qui, pour le moment, génère le jeton en tant qu'erreur.

    ```TypeScript
    async signIn(): Promise<void> {
      await this.authService.signIn();

      // Temporary to display the token
      if (this.authService.authenticated) {
        let token = await this.authService.getAccessToken();
      }
    }
    ```

Lorsque vous avez fini, le fichier doit ressembler à ce qui suit.

```TypeScript
import { Component, OnInit } from '@angular/core';
import { AuthService } from '../auth.service';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {

  constructor(private authService: AuthService) { }

  ngOnInit() {
  }

  async signIn(): Promise<void> {
    await this.authService.signIn();

    // Temporary to display the token
    if (this.authService.authenticated) {
      let token = await this.authService.getAccessToken();
    }
  }
}
```

Enfin, effectuez les mêmes remplacements que ceux `./src/app/home/home.component.html` de la barre de navigation.

Enregistrez vos modifications et actualisez le navigateur. Cliquez sur le bouton **cliquez ici pour vous connecter** et vous devez être redirigé vers `https://login.microsoftonline.com`. Connectez-vous avec votre compte Microsoft et acceptez les autorisations demandées. La page de l'application doit être actualisée, affichant le jeton.

### <a name="get-user-details"></a>Obtenir les détails de l'utilisateur

Actuellement, le service d'authentification définit des valeurs constantes pour le nom d'affichage et l'adresse de messagerie de l'utilisateur. Maintenant que vous disposez d'un jeton d'accès, vous pouvez obtenir les détails de l'utilisateur à partir de Microsoft Graph pour que ces valeurs correspondent à l'utilisateur actuel. Ouvrez `./src/app/auth.service.ts` et ajoutez l'instruction `import` suivante en haut du fichier.

```TypeScript
import { Client } from '@microsoft/microsoft-graph-client';
```

Ajoutez une nouvelle fonction à la classe `AuthService` appelée `getUser`.

```TypeScript
private async getUser(): Promise<User> {
  if (!this.authenticated) return null;

  let graphClient = Client.init({
    // Initialize the Graph client with an auth
    // provider that requests the token from the
    // auth service
    authProvider: async(done) => {
      let token = await this.getAccessToken()
        .catch((reason) => {
          done(reason, null);
        });

      if (token)
      {
        done(null, token);
      } else {
        done("Could not get an access token", null);
      }
    }
  });

  // Get the user from Graph (GET /me)
  let graphUser = await graphClient.api('/me').get();

  let user = new User();
  user.displayName = graphUser.displayName;
  // Prefer the mail property, but fall back to userPrincipalName
  user.email = graphUser.mail || graphUser.userPrincipalName;

  return user;
}
```

Recherchez et supprimez le code suivant de `signIn` la méthode.

```TypeScript
// Temporary placeholder
this.user = new User();
this.user.displayName = "Adele Vance";
this.user.email = "AdeleV@contoso.com";
```

À la place, ajoutez le code suivant.

```TypeScript
this.user = await this.getUser();
```

Ce nouveau code utilise le kit de développement logiciel (SDK) Microsoft Graph pour obtenir les détails `User` de l'utilisateur, puis crée un objet à l'aide des valeurs renvoyées par l'appel de l'API.

Modifiez `constructor` à présent la `AuthService` classe pour vérifier si l'utilisateur est déjà connecté et charger ses informations si c'est le cas. Remplacez le existant `constructor` par ce qui suit.

```TypeScript
constructor(
  private msalService: MsalService,
  private alertsService: AlertsService) {

  this.authenticated = this.msalService.getUser() != null;
  this.getUser().then((user) => {this.user = user});
}
```

Enfin, supprimez le code temporaire de `HomeComponent` la classe. Ouvrez le `./src/app/home/home.component.ts` fichier et remplacez la fonction `signIn` existante par ce qui suit.

```TypeScript
async signIn(): Promise<void> {
  await this.authService.signIn();
}
```

Maintenant, si vous enregistrez vos modifications et démarrez l'application, après vous être connecté, vous devez revenir sur la page d'accueil, mais l'interface utilisateur doit changer pour indiquer que vous êtes connecté.

![Capture d'écran de la page d'accueil après la connexion](./images/add-aad-auth-01.png)

Cliquez sur Avatar de l'utilisateur dans le coin supérieur droit pour **** accéder au lien Déconnexion. Cliquez **** sur Déconnexion pour réinitialiser la session et revenir à la page d'accueil.

![Capture d'écran du menu déroulant avec le lien déConnexion](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>Stockage et actualisation des jetons

À ce stade, votre application a un jeton d'accès, qui est envoyé `Authorization` dans l'en-tête des appels d'API. Il s'agit du jeton qui permet à l'application d'accéder à Microsoft Graph pour le compte de l'utilisateur.

Toutefois, ce jeton est éphémère. Le jeton expire une heure après son émission. Étant donné que l'application utilise la bibliothèque MSAL, vous n'avez pas besoin d'implémenter de logique d'actualisation ou de stockage de jetons. Le `MsalService` jeton est mis en cache dans le stockage du navigateur. La `acquireTokenSilent` méthode vérifie d'abord le jeton mis en cache et, s'il n'a pas expiré, il le renvoie. Si elle a expiré, elle effectue une demande silencieuse pour en obtenir une nouvelle.