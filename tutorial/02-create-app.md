<!-- markdownlint-disable MD002 MD041 -->

Dans cette section, vous allez créer un projet Angular projet.

1. Ouvrez votre interface de ligne de commande (CLI), accédez à un répertoire dans lequel vous avez le droit de créer des fichiers, puis exécutez les commandes suivantes pour installer l'outil [CLI Angular](https://www.npmjs.com/package/@angular/cli) et créer une application Angular.

    ```Shell
    npm install -g @angular/cli@11.2.9
    ng new graph-tutorial
    ```

1. L Angular CLI vous invite à fournir plus d'informations. Répondez aux invites comme suit.

    ```Shell
    ? Do you want to enforce stricter type checking and stricter bundle budgets in the workspace? Yes
    ? Would you like to add Angular routing? Yes
    ? Which stylesheet format would you like to use? CSS
    ```

1. Une fois la commande finale, modifiez le répertoire de votre CLI et exécutez la commande suivante pour démarrer `graph-tutorial` un serveur web local.

    ```Shell
    ng serve --open
    ```

1. Votre navigateur par défaut s'ouvre [https://localhost:4200/](https://localhost:4200) avec une page de Angular par défaut. Si votre navigateur ne s'ouvre pas, ouvrez-le et recherchez-le pour vérifier [https://localhost:4200/](https://localhost:4200) que la nouvelle application fonctionne.

## <a name="add-node-packages"></a>Ajouter des packages de nœuds

Avant de passer à la suite, installez des packages supplémentaires que vous utiliserez ultérieurement :

- [bootstrap](https://github.com/twbs/bootstrap) pour les styles et les composants courants.
- [ng-bootstrap](https://github.com/ng-bootstrap/ng-bootstrap) pour utiliser des composants Bootstrap à partir de Angular.
- [moment de](https://github.com/moment/moment) mise en forme des dates et heures.
- [windows-iana](https://github.com/rubenillodo/windows-iana)
- [msal-angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) pour l'authentification auprès Azure Active Directory et la récupération des jetons d'accès.
- [microsoft-graph-client pour](https://github.com/microsoftgraph/msgraph-sdk-javascript) effectuer des appels à Microsoft Graph.

1. Exécutez les commandes suivantes dans votre CLI.

    ```Shell
    npm install bootstrap@4.6.0 @ng-bootstrap/ng-bootstrap@9.1.0
    npm install @azure/msal-browser@2.14.0 @azure/msal-angular@2.0.0-beta.4
    npm install moment-timezone@0.5.33 windows-iana@5.0.2
    npm install @microsoft/microsoft-graph-client@2.2.1 @microsoft/microsoft-graph-types@1.35.0
    ```

1. Exécutez la commande suivante dans votre CLI pour ajouter Angular package de localisation (requis par ng-bootstrap).

    ```Shell
    ng add @angular/localize
    ```

## <a name="design-the-app"></a>Concevoir l’application

Dans cette section, vous allez créer l'interface utilisateur de l'application.

1. Ouvrez **./src/styles.css** et ajoutez les lignes suivantes.

    :::code language="css" source="../demo/graph-tutorial/src/styles.css":::

1. Ajoutez le module Bootstrap à l'application. Ouvrez **./src/app/app.module.ts** et remplacez son contenu par ce qui suit.

    ```typescript
    import { BrowserModule } from '@angular/platform-browser';
    import { FormsModule } from '@angular/forms';
    import { NgModule } from '@angular/core';
    import { NgbModule } from '@ng-bootstrap/ng-bootstrap';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';

    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        FormsModule,
        AppRoutingModule,
        NgbModule
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```

1. Créez un fichier dans **le dossier ./src/app** nommé **user.ts** et ajoutez le code suivant.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/user.ts" id="UserSnippet":::

1. Générez un Angular pour la navigation supérieure sur la page. Dans votre CLI, exécutez la commande suivante.

    ```Shell
    ng generate component nav-bar
    ```

1. Une fois la commande terminée, ouvrez **./src/app/nav-bar/nav-bar.component.ts** et remplacez son contenu par ce qui suit.

    ```typescript
    import { Component, OnInit } from '@angular/core';

    import { User } from '../user';

    @Component({
      selector: 'app-nav-bar',
      templateUrl: './nav-bar.component.html',
      styleUrls: ['./nav-bar.component.css']
    })
    export class NavBarComponent implements OnInit {

      // Should the collapsed nav show?
      showNav: boolean = false;
      // Is a user logged in?
      authenticated: boolean = false;
      // The user
      user?: User = undefined;

      constructor() { }

      ngOnInit() { }

      // Used by the Bootstrap navbar-toggler button to hide/show
      // the nav in a collapsed state
      toggleNavBar(): void {
        this.showNav = !this.showNav;
      }

      signIn(): void {
        // Temporary
        this.authenticated = true;
        this.user = {
          displayName: 'Adele Vance',
          email: 'AdeleV@contoso.com',
          avatar: '',
          timeZone: ''
        };
      }

      signOut(): void {
        // Temporary
        this.authenticated = false;
        this.user = undefined;
      }
    }
    ```

1. Ouvrez **./src/app/nav-bar/nav-bar.component.html** et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/src/app/nav-bar/nav-bar.component.html" id="navHtml":::

1. Créez une page d'accueil pour l'application. Exécutez la commande suivante dans votre CLI.

    ```Shell
    ng generate component home
    ```

1. Une fois la commande terminée, ouvrez **./src/app/home/home.component.ts** et remplacez son contenu par ce qui suit.

    ```typescript
    import { Component, OnInit } from '@angular/core';

    import { User } from '../user';

    @Component({
      selector: 'app-home',
      templateUrl: './home.component.html',
      styleUrls: ['./home.component.css']
    })
    export class HomeComponent implements OnInit {

      // Is a user logged in?
      authenticated: boolean = false;
      // The user
      user?: User = undefined;

      constructor() { }

      ngOnInit() { }

      signIn(): void {
        // Temporary
        this.authenticated = true;
        this.user = {
          displayName: 'Adele Vance',
          email: 'AdeleV@contoso.com',
          avatar: '',
          timeZone: ''
        };
      }
    }
    ```

1. Ouvrez **./src/app/home/home.component.html** et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/src/app/home/home.component.html" id="homeHtml":::

1. Créez une classe `Alert` simple. Créez un fichier dans le répertoire **./src/app** nommé **alert.ts** et ajoutez le code suivant.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alert.ts" id="AlertSnippet":::

1. Créez un service d'alerte que l'application peut utiliser pour afficher des messages à l'utilisateur. Dans votre CLI, exécutez la commande suivante.

    ```Shell
    ng generate service alerts
    ```

1. Ouvrez **./src/app/alerts.service.ts** et remplacez son contenu par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alerts.service.ts" id="alertsService":::

1. Générer un composant d'alertes pour afficher les alertes. Dans votre CLI, exécutez la commande suivante.

    ```Shell
    ng generate component alerts
    ```

1. Une fois la commande terminée, ouvrez **./src/app/alerts/alerts.component.ts** et remplacez son contenu par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alerts/alerts.component.ts" id="AlertsComponentSnippet":::

1. Ouvrez **./src/app/alerts/alerts.component.html** et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/src/app/alerts/alerts.component.html" id="AlertHtml":::

1. Ouvrez **./src/app/app-routing.module.ts** et remplacez la ligne `const routes: Routes = [];` par le code suivant.

    ```typescript
    import { HomeComponent } from './home/home.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
    ];
    ```

1. Ouvrez **./src/app/app.component.html** et remplacez tout son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/src/app/app.component.html" id="AppHtml":::

1. Ajoutez un fichier image de votre choix nommé **no-profile-photo.png** dans le répertoire **./src/assets.** Cette image est utilisée comme photo de l'utilisateur lorsque l'utilisateur n'a pas de photo dans Microsoft Graph.

Enregistrez toutes vos modifications et actualisez la page. L'application doit maintenant avoir une apparence très différente.

![Capture d’écran de la page d’accueil repensée](images/create-app-01.png)
