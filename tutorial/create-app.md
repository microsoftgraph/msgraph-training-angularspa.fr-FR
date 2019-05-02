<!-- markdownlint-disable MD002 MD041 -->

Ouvrez votre interface de ligne de commande (CLI), accédez à un répertoire où vous disposez de droits pour créer des fichiers, puis exécutez les commandes suivantes pour installer l’outil [CLI angulaire](https://www.npmjs.com/package/@angular/cli) et créer une application angulaire.

```Shell
npm install -g @angular/cli
ng new graph-tutorial
```

La CLI angulaire demande davantage d’informations. Répondez aux invites comme suit.

```Shell
? Would you like to add Angular routing? Yes
? Which stylesheet format would you like to use? CSS
```

Une fois la commande terminée, accédez au `graph-tutorial` répertoire dans votre interface CLI et exécutez la commande suivante pour démarrer un serveur Web local.

```Shell
ng serve --open
```

Votre navigateur par défaut s' [https://localhost:4200/](https://localhost:4200) ouvre avec une page d’angle par défaut. Si votre navigateur ne s’ouvre pas, ouvrez-le [https://localhost:4200/](https://localhost:4200) et accédez à pour vérifier que la nouvelle application fonctionne.

Avant de poursuivre, installez des packages supplémentaires que vous utiliserez plus tard:

- [bootstrap](https://github.com/twbs/bootstrap) pour le style et les composants communs.
- [GN-bootstrap](https://github.com/ng-bootstrap/ng-bootstrap) pour l’utilisation de composants bootstrap à partir d’un angle.
- [fontawesome](https://github.com/FortAwesome/angular-fontawesome) pour utiliser des icônes fontawesome en tant qu’angles.
- [fontawesome-SVG-Core](https://github.com/FortAwesome/Font-Awesome), [Free-Regular-SVG-Icons](https://github.com/FortAwesome/Font-Awesome)et [Free-Solid-SVG-Icons](https://github.com/FortAwesome/Font-Awesome) pour les icônes fontawesome utilisées dans l’exemple.
- [moment](https://github.com/moment/moment) de mise en forme des dates et des heures.
- [MSAL-angulaire](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) pour l’authentification auprès d’Azure Active Directory et pour la récupération des jetons d’accès.
- [rxjs-compat](https://github.com/ReactiveX/rxjs/tree/master/compat), obligatoire pour le `msal-angular` package.
- [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.

Exécutez la commande suivante dans votre interface CLI.

```Shell
npm install bootstrap@4.3.1 @fortawesome/angular-fontawesome@0.3.0 @fortawesome/fontawesome-svg-core@1.2.15
npm install @fortawesome/free-regular-svg-icons@5.7.2 @fortawesome/free-solid-svg-icons@5.7.2
npm install moment@2.24.0 moment-timezone@0.5.23 @ng-bootstrap/ng-bootstrap@4.1.0
npm install @azure/msal-angular@0.1.2 rxjs-compat@6.4.0 @microsoft/microsoft-graph-client@1.4.0
```

## <a name="design-the-app"></a>Concevoir l’application

Commencez par ajouter les fichiers CSS de démarrage à l’application, ainsi que certains styles globaux. Ouvrez le `./src/styles.css` et ajoutez les lignes suivantes.

```CSS
@import "~bootstrap/dist/css/bootstrap.css";

/* Add padding for the nav bar */
body {
  padding-top: 4.5rem;
}

/* Style debug info in alerts */
.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

Ensuite, ajoutez les modules bootstrap et FontAwesome à l’application. Ouvrez `./src/app/app.module.ts` et ajoutez les instructions `import` suivantes en haut du fichier.

```TypeScript
import { NgbModule } from '@ng-bootstrap/ng-bootstrap';
import { FontAwesomeModule } from '@fortawesome/angular-fontawesome';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faExternalLinkAlt } from '@fortawesome/free-solid-svg-icons';
import { faUserCircle } from '@fortawesome/free-regular-svg-icons';
```

Ajoutez ensuite le code suivant après toutes les `import` instructions.

```TypeScript
library.add(faExternalLinkAlt);
library.add(faUserCircle);
```

Dans la `@NgModule` déclaration, remplacez le tableau `imports` existant par ce qui suit.

```TypeScript
imports: [
  BrowserModule,
  AppRoutingModule,
  NgbModule,
  FontAwesomeModule
]
```

Générez maintenant un composant angulaire pour la navigation supérieure sur la page. Dans votre interface CLI, exécutez la commande suivante.

```Shell
ng generate component nav-bar
```

Une fois la commande terminée, ouvrez le `./src/app/nav-bar/nav-bar.component.ts` fichier et remplacez son contenu par ce qui suit.

```TypeScript
import { Component, OnInit } from '@angular/core';

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
  user: any;

  constructor() { }

  ngOnInit() {
    this.showNav = false;
    this.authenticated = false;
    this.user = {};
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
      email: 'AdeleV@contoso.com'
    };
  }

  signOut(): void {
    // Temporary
    this.authenticated = false;
    this.user = {};
  }
}
```

Ouvrez le `./src/app/nav-bar/nav-bar.component.html` fichier et remplacez son contenu par ce qui suit.

```html
<nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
  <div class="container">
    <a routerLink="/" class="navbar-brand">Angular Graph Tutorial</a>
    <button class="navbar-toggler" type="button" (click)="toggleNavBar()" [attr.aria-expanded]="showNav"
    aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" [class.show]="showNav" id="navbarCollapse">
      <ul class="navbar-nav mr-auto">
        <li class="nav-item">
          <a routerLink="/" class="nav-link" routerLinkActive="active">Home</a>
        </li>
        <li *ngIf="authenticated" class="nav-item">
          <a routerLink="/calendar" class="nav-link" routerLinkActive="active">Calendar</a>
        </li>
      </ul>
      <ul class="navbar-nav justify-content-end">
        <li class="nav-item">
          <a class="nav-link" href="https://docs.microsoft.com/graph/overview" target="_blank">
            <fa-icon [icon]="[ 'fas', 'external-link-alt' ]" class="mr-1"></fa-icon>Docs
          </a>
        </li>
        <li *ngIf="authenticated" ngbDropdown placement="bottom-right" class="nav-item">
          <a ngbDropdownToggle id="userMenu" class="nav-link" href="javascript:undefined" role="button" aria-haspopup="true"
            aria-expanded="false">
            <div *ngIf="user.avatar; then userAvatar else defaultAvatar"></div>
            <ng-template #userAvatar>
              <img src="user.avatar" class="rounded-circle align-self-center mr-2" style="width: 32px;">
            </ng-template>
            <ng-template #defaultAvatar>
              <fa-icon [icon]="[ 'far', 'user-circle' ]" fixedWidth="true" size="lg"
                class="rounded-circle align-self-center mr-2"></fa-icon>
            </ng-template>
          </a>
          <div ngbDropdownMenu aria-labelledby="userMenu">
            <h5 class="dropdown-item-text mb-0">{{user.displayName}}</h5>
            <p class="dropdown-item-text text-muted mb-0">{{user.email}}</p>
            <div class="dropdown-divider"></div>
            <a class="dropdown-item" href="javascript:undefined" role="button" (click)="signOut()">Sign Out</a>
          </div>
        </li>
        <li *ngIf="!authenticated" class="nav-item">
          <a class="nav-link" href="javascript:undefined" role="button" (click)="signIn()">Sign In</a>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

Ensuite, créez une page d’accueil pour l’application. Exécutez la commande suivante dans votre interface CLI.

```Shell
ng generate component home
```

Une fois la commande terminée, ouvrez le `./src/app/home/home.component.ts` fichier et remplacez son contenu par ce qui suit.

```TypeScript
import { Component, OnInit } from '@angular/core';

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

Ouvrez ensuite le `./src/app/home/home.component.html` fichier et remplacez son contenu par ce qui suit.

```html
<div class="jumbotron">
  <h1>Angular Graph Tutorial</h1>
  <p class="lead">This sample app shows how to use the Microsoft Graph API from Angular</p>
  <div *ngIf="authenticated; then welcomeUser else signInPrompt"></div>
  <ng-template #welcomeUser>
    <h4>Welcome {{ user.displayName }}!</h4>
    <p>Use the navigation bar at the top of the page to get started.</p>
  </ng-template>
  <ng-template #signInPrompt>
    <a href="javascript:undefined" class="btn btn-primary btn-large" role="button" (click)="signIn()">Click here to sign in</a>
  </ng-template>
</div>
```

À présent, créez un service d’alerte que l’application peut utiliser pour afficher des messages à l’utilisateur. Commencez par créer une classe `Alert` simple. Créez un fichier dans le `./src/app` répertoire nommé `alert.ts` et ajoutez le code suivant.

```TypeScript
export class Alert {
  message: string;
  debug: string;
}
```

Dans votre interface CLI, exécutez la commande suivante.

```Shell
ng generate service alerts
```

Ouvrez le `./src/app/alerts.service.ts` fichier et remplacez son contenu par ce qui suit.

```TypeScript
import { Injectable } from '@angular/core';
import { Alert } from './alert';

@Injectable({
  providedIn: 'root'
})
export class AlertsService {

  alerts: Alert[] = [];

  add(message: string, debug: string) {
    this.alerts.push({message: message, debug: debug});
  }

  remove(alert: Alert) {
    this.alerts.splice(this.alerts.indexOf(alert), 1);
  }
}
```

À présent, générez un composant Alerts pour afficher les alertes. Dans votre interface CLI, exécutez la commande suivante.

```Shell
ng generate component alerts
```

Une fois la commande terminée, ouvrez le `./src/app/alerts/alerts.component.ts` fichier et remplacez son contenu par ce qui suit.

```TypeScript
import { Component, OnInit } from '@angular/core';
import { AlertsService } from '../alerts.service';
import { Alert } from '../alert';

@Component({
  selector: 'app-alerts',
  templateUrl: './alerts.component.html',
  styleUrls: ['./alerts.component.css']
})
export class AlertsComponent implements OnInit {

  constructor(private alertsService: AlertsService) { }

  ngOnInit() {
  }

  close(alert: Alert) {
    this.alertsService.remove(alert);
  }
}
```

Ouvrez ensuite le `./src/app/alerts/alerts.component.html` fichier et remplacez son contenu par ce qui suit.

```html
<div *ngFor="let alert of alertsService.alerts">
  <ngb-alert type="danger" (close)="close(alert)">
    <p>{{alert.message}}</p>
    <pre *ngIf="alert.debug" class="alert-pre border bg-light p-2"><code>{{alert.debug}}</code></pre>
  </ngb-alert>
</div>
```

Maintenant que les composants de base sont définis, mettez à jour l’application pour les utiliser. Tout d’abord, `./src/app/app-routing.module.ts` Ouvrez le fichier et `const routes: Routes = [];` remplacez la ligne par le code suivant.

```TypeScript
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
];
```

Ouvrez le fichier `./src/app/app.component.html` et remplacez l’intégralité de son contenu par ce qui suit.

```html
<app-nav-bar></app-nav-bar>
<main role="main" class="container">
  <app-alerts></app-alerts>
  <router-outlet></router-outlet>
</main>
```

Enregistrez toutes vos modifications et actualisez la page. À présent, l’application doit être très différente.

![Capture d’écran de la page d’accueil repensée](images/create-app-01.png)