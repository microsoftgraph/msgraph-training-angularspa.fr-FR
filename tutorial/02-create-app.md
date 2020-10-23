<!-- markdownlint-disable MD002 MD041 -->

Dans cette section, vous allez créer un nouveau projet angulaire.

1. Ouvrez votre interface de ligne de commande (CLI), accédez à un répertoire où vous disposez de droits pour créer des fichiers, puis exécutez les commandes suivantes pour installer l’outil [CLI angulaire](https://www.npmjs.com/package/@angular/cli) et créer une application angulaire.

    ```Shell
    npm install -g @angular/cli@10.1.7
    ng new graph-tutorial
    ```

1. La CLI angulaire demande davantage d’informations. Répondez aux invites comme suit.

    ```Shell
    ? Would you like to add Angular routing? Yes
    ? Which stylesheet format would you like to use? CSS
    ```

1. Une fois la commande terminée, accédez au `graph-tutorial` répertoire dans votre interface CLI et exécutez la commande suivante pour démarrer un serveur Web local.

    ```Shell
    ng serve --open
    ```

1. Votre navigateur par défaut s’ouvre [https://localhost:4200/](https://localhost:4200) avec une page d’angle par défaut. Si votre navigateur ne s’ouvre pas, ouvrez-le et accédez à [https://localhost:4200/](https://localhost:4200) pour vérifier que la nouvelle application fonctionne.

## <a name="add-node-packages"></a>Ajouter des packages de nœuds

Avant de poursuivre, installez des packages supplémentaires que vous utiliserez plus tard :

- [bootstrap](https://github.com/twbs/bootstrap) pour le style et les composants communs.
- [GN-bootstrap](https://github.com/ng-bootstrap/ng-bootstrap) pour l’utilisation de composants bootstrap à partir d’un angle.
- [moment](https://github.com/moment/moment) de mise en forme des dates et des heures.
- [Windows-IANA](https://github.com/rubenillodo/windows-iana)
- [MSAL-angulaire](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) pour l’authentification auprès d’Azure Active Directory et pour la récupération des jetons d’accès.
- [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.

1. Exécutez les commandes suivantes dans votre interface CLI.

    ```Shell
    npm install bootstrap@4.5.3 @ng-bootstrap/ng-bootstrap@7.0.0 msal@1.4.2 @azure/msal-angular@1.1.1
    npm install moment@2.29.1 moment-timezone@0.5.31 windows-iana@4.2.1
    npm install @microsoft/microsoft-graph-client@2.1.0 @microsoft/microsoft-graph-types@1.24.0
    ```

1. Exécutez la commande suivante dans votre interface CLI pour ajouter le package de localisation angulaire (requis par le GN-bootstrap).

    ```Shell
    ng add @angular/localize
    ```

## <a name="design-the-app"></a>Concevoir l’application

Dans cette section, vous allez créer l’interface utilisateur de l’application.

1. Ouvrez **./SRC/styles.CSS** et ajoutez les lignes suivantes.

    :::code language="css" source="../demo/graph-tutorial/src/styles.css":::

1. Ajoutez le module bootstrap à l’application. Ouvrez **./SRC/app/app.module.TS** et remplacez son contenu par ce qui suit.

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

1. Créez un fichier dans le dossier **./SRC/App** nommé **User. TS** et ajoutez le code suivant.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/user.ts" id="user":::

1. Générer un composant angulaire pour la navigation supérieure sur la page. Dans votre interface CLI, exécutez la commande suivante.

    ```Shell
    ng generate component nav-bar
    ```

1. Une fois la commande terminée, ouvrez **/src/App/nav-bar/NAV-bar.Component.TS** et remplacez son contenu par ce qui suit.

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
      showNav: boolean;
      // Is a user logged in?
      authenticated: boolean;
      // The user
      user: User;

      constructor() { }

      ngOnInit() {
        this.showNav = false;
        this.authenticated = false;
        this.user = null;
      }

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
          avatar: null
        };
      }

      signOut(): void {
        // Temporary
        this.authenticated = false;
        this.user = null;
      }
    }
    ```

1. Ouvrez **/src/app/nav-bar/nav-bar.component.html** et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/src/app/nav-bar/nav-bar.component.html" id="navHtml":::

1. Créez une page d’accueil pour l’application. Exécutez la commande suivante dans votre interface CLI.

    ```Shell
    ng generate component home
    ```

1. Une fois la commande terminée, ouvrez **/src/App/Home/Home.Component.TS** et remplacez son contenu par ce qui suit.

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
      authenticated: boolean;
      // The user
      user: any;

      constructor() { }

      ngOnInit() {
        this.authenticated = false;
        this.user = {};
      }

      signIn(): void {
        // Temporary
        this.authenticated = true;
        this.user = {
          displayName: 'Adele Vance',
          email: 'AdeleV@contoso.com'
        };
      }
    }
    ```

1. Ouvrez **/src/app/home/home.component.html** et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/src/app/home/home.component.html" id="homeHtml":::

1. Créez une `Alert` classe simple. Créez un fichier dans le répertoire **./SRC/App** nommé **Alert. TS** et ajoutez le code suivant.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alert.ts" id="alert":::

1. Créer un service d’alerte que l’application peut utiliser pour afficher des messages à l’utilisateur. Dans votre interface CLI, exécutez la commande suivante.

    ```Shell
    ng generate service alerts
    ```

1. Ouvrez **./SRC/App/alerts.service.TS** et remplacez son contenu par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alerts.service.ts" id="alertsService":::

1. Générer un composant Alerts pour afficher les alertes. Dans votre interface CLI, exécutez la commande suivante.

    ```Shell
    ng generate component alerts
    ```

1. Une fois la commande terminée, ouvrez **/src/App/Alerts/alerts.Component.TS** et remplacez son contenu par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alerts/alerts.component.ts" id="alertComponent":::

1. Ouvrez **/src/app/alerts/alerts.component.html** et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/src/app/alerts/alerts.component.html" id="alertHtml":::

1. Ouvrez **./SRC/app/app-Routing.module.TS** et remplacez la `const routes: Routes = [];` ligne par le code suivant.

    ```typescript
    import { HomeComponent } from './home/home.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
    ];
    ```

1. Ouvrez **/src/app/app.component.html** et remplacez l’intégralité de son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/src/app/app.component.html" id="appHtml":::

1. Ajoutez un fichier image de votre choix nommé **no-profile-photo.png** dans le répertoire **./SRC/Assets** . Cette image sera utilisée comme photo de l’utilisateur lorsque l’utilisateur n’aura pas de photo dans Microsoft Graph.

Enregistrez toutes vos modifications et actualisez la page. À présent, l’application doit être très différente.

![Capture d’écran de la page d’accueil repensée](images/create-app-01.png)
