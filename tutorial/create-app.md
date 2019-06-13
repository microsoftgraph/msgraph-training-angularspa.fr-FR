<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a0864-101">Ouvrez votre interface de ligne de commande (CLI), accédez à un répertoire où vous disposez de droits pour créer des fichiers, puis exécutez les commandes suivantes pour installer l’outil [CLI angulaire](https://www.npmjs.com/package/@angular/cli) et créer une application angulaire.</span><span class="sxs-lookup"><span data-stu-id="a0864-101">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following commands to install the [Angular CLI](https://www.npmjs.com/package/@angular/cli) tool and create a new Angular app.</span></span>

```Shell
npm install -g @angular/cli
ng new graph-tutorial
```

<span data-ttu-id="a0864-102">La CLI angulaire demande davantage d’informations.</span><span class="sxs-lookup"><span data-stu-id="a0864-102">The Angular CLI will prompt for more information.</span></span> <span data-ttu-id="a0864-103">Répondez aux invites comme suit.</span><span class="sxs-lookup"><span data-stu-id="a0864-103">Answer the prompts as follows.</span></span>

```Shell
? Would you like to add Angular routing? Yes
? Which stylesheet format would you like to use? CSS
```

<span data-ttu-id="a0864-104">Une fois la commande terminée, accédez au `graph-tutorial` répertoire dans votre interface CLI et exécutez la commande suivante pour démarrer un serveur Web local.</span><span class="sxs-lookup"><span data-stu-id="a0864-104">Once the command finishes, change to the `graph-tutorial` directory in your CLI and run the following command to start a local web server.</span></span>

```Shell
ng serve --open
```

<span data-ttu-id="a0864-105">Votre navigateur par défaut s' [https://localhost:4200/](https://localhost:4200) ouvre avec une page d’angle par défaut.</span><span class="sxs-lookup"><span data-stu-id="a0864-105">Your default browser opens to [https://localhost:4200/](https://localhost:4200) with a default Angular page.</span></span> <span data-ttu-id="a0864-106">Si votre navigateur ne s’ouvre pas, ouvrez-le [https://localhost:4200/](https://localhost:4200) et accédez à pour vérifier que la nouvelle application fonctionne.</span><span class="sxs-lookup"><span data-stu-id="a0864-106">If your browser doesn't open, open it and browse to [https://localhost:4200/](https://localhost:4200) to verify that the new app works.</span></span>

<span data-ttu-id="a0864-107">Avant de poursuivre, installez des packages supplémentaires que vous utiliserez plus tard:</span><span class="sxs-lookup"><span data-stu-id="a0864-107">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="a0864-108">[bootstrap](https://github.com/twbs/bootstrap) pour le style et les composants communs.</span><span class="sxs-lookup"><span data-stu-id="a0864-108">[bootstrap](https://github.com/twbs/bootstrap) for styling and common components.</span></span>
- <span data-ttu-id="a0864-109">[GN-bootstrap](https://github.com/ng-bootstrap/ng-bootstrap) pour l’utilisation de composants bootstrap à partir d’un angle.</span><span class="sxs-lookup"><span data-stu-id="a0864-109">[ng-bootstrap](https://github.com/ng-bootstrap/ng-bootstrap) for using Bootstrap components from Angular.</span></span>
- <span data-ttu-id="a0864-110">[fontawesome](https://github.com/FortAwesome/angular-fontawesome) pour utiliser des icônes fontawesome en tant qu’angles.</span><span class="sxs-lookup"><span data-stu-id="a0864-110">[angular-fontawesome](https://github.com/FortAwesome/angular-fontawesome) to use FontAwesome icons in Angular.</span></span>
- <span data-ttu-id="a0864-111">[fontawesome-SVG-Core](https://github.com/FortAwesome/Font-Awesome), [Free-Regular-SVG-Icons](https://github.com/FortAwesome/Font-Awesome)et [Free-Solid-SVG-Icons](https://github.com/FortAwesome/Font-Awesome) pour les icônes fontawesome utilisées dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="a0864-111">[fontawesome-svg-core](https://github.com/FortAwesome/Font-Awesome), [free-regular-svg-icons](https://github.com/FortAwesome/Font-Awesome), and [free-solid-svg-icons](https://github.com/FortAwesome/Font-Awesome) for the FontAwesome icons used in the sample.</span></span>
- <span data-ttu-id="a0864-112">[moment](https://github.com/moment/moment) de mise en forme des dates et des heures.</span><span class="sxs-lookup"><span data-stu-id="a0864-112">[moment](https://github.com/moment/moment) for formatting dates and times.</span></span>
- <span data-ttu-id="a0864-113">[MSAL-angulaire](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) pour l’authentification auprès d’Azure Active Directory et pour la récupération des jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="a0864-113">[msal-angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) for authenticating to Azure Active Directory and retrieving access tokens.</span></span>
- <span data-ttu-id="a0864-114">[rxjs-compat](https://github.com/ReactiveX/rxjs/tree/master/compat), obligatoire pour le `msal-angular` package.</span><span class="sxs-lookup"><span data-stu-id="a0864-114">[rxjs-compat](https://github.com/ReactiveX/rxjs/tree/master/compat), required for the `msal-angular` package.</span></span>
- <span data-ttu-id="a0864-115">[Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="a0864-115">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>

<span data-ttu-id="a0864-116">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="a0864-116">Run the following command in your CLI.</span></span>

```Shell
npm install bootstrap@4.3.1 @fortawesome/angular-fontawesome@0.3.0 @fortawesome/fontawesome-svg-core@1.2.17
npm install @fortawesome/free-regular-svg-icons@5.8.1 @fortawesome/free-solid-svg-icons@5.8.1
npm install moment@2.24.0 moment-timezone@0.5.25 @ng-bootstrap/ng-bootstrap@4.1.2
npm install @azure/msal-angular@0.1.2 rxjs-compat@6.5.1 @microsoft/microsoft-graph-client@1.6.0
```

## <a name="design-the-app"></a><span data-ttu-id="a0864-117">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="a0864-117">Design the app</span></span>

<span data-ttu-id="a0864-118">Commencez par ajouter les fichiers CSS de démarrage à l’application, ainsi que certains styles globaux.</span><span class="sxs-lookup"><span data-stu-id="a0864-118">Start by adding the Bootstrap CSS files to the app, as well as some global styles.</span></span> <span data-ttu-id="a0864-119">Ouvrez le `./src/styles.css` et ajoutez les lignes suivantes.</span><span class="sxs-lookup"><span data-stu-id="a0864-119">Open the `./src/styles.css` and add the following lines.</span></span>

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

<span data-ttu-id="a0864-120">Ensuite, ajoutez les modules bootstrap et FontAwesome à l’application.</span><span class="sxs-lookup"><span data-stu-id="a0864-120">Next, add the Bootstrap and FontAwesome modules to the app.</span></span> <span data-ttu-id="a0864-121">Ouvrez `./src/app/app.module.ts` et ajoutez les instructions `import` suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="a0864-121">Open `./src/app/app.module.ts` and add the following `import` statements to the top of the file.</span></span>

```TypeScript
import { NgbModule } from '@ng-bootstrap/ng-bootstrap';
import { FontAwesomeModule } from '@fortawesome/angular-fontawesome';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faExternalLinkAlt } from '@fortawesome/free-solid-svg-icons';
import { faUserCircle } from '@fortawesome/free-regular-svg-icons';
```

<span data-ttu-id="a0864-122">Ajoutez ensuite le code suivant après toutes les `import` instructions.</span><span class="sxs-lookup"><span data-stu-id="a0864-122">Then add the following code after all of the `import` statements.</span></span>

```TypeScript
library.add(faExternalLinkAlt);
library.add(faUserCircle);
```

<span data-ttu-id="a0864-123">Dans la `@NgModule` déclaration, remplacez le tableau `imports` existant par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a0864-123">In the `@NgModule` declaration, replace the existing `imports` array with the following.</span></span>

```TypeScript
imports: [
  BrowserModule,
  AppRoutingModule,
  NgbModule,
  FontAwesomeModule
]
```

<span data-ttu-id="a0864-124">Générez maintenant un composant angulaire pour la navigation supérieure sur la page.</span><span class="sxs-lookup"><span data-stu-id="a0864-124">Now generate an Angular component for the top navigation on the page.</span></span> <span data-ttu-id="a0864-125">Dans votre interface CLI, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="a0864-125">In your CLI, run the following command.</span></span>

```Shell
ng generate component nav-bar
```

<span data-ttu-id="a0864-126">Une fois la commande terminée, ouvrez le `./src/app/nav-bar/nav-bar.component.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a0864-126">Once the command completes, open the `./src/app/nav-bar/nav-bar.component.ts` file and replace its contents with the following.</span></span>

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

<span data-ttu-id="a0864-127">Ouvrez le `./src/app/nav-bar/nav-bar.component.html` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a0864-127">Open the `./src/app/nav-bar/nav-bar.component.html` file and replace its contents with the following.</span></span>

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

<span data-ttu-id="a0864-128">Ensuite, créez une page d’accueil pour l’application.</span><span class="sxs-lookup"><span data-stu-id="a0864-128">Next, create a home page for the app.</span></span> <span data-ttu-id="a0864-129">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="a0864-129">Run the following command in your CLI.</span></span>

```Shell
ng generate component home
```

<span data-ttu-id="a0864-130">Une fois la commande terminée, ouvrez le `./src/app/home/home.component.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a0864-130">Once the command completes, open the `./src/app/home/home.component.ts` file and replace its contents with the following.</span></span>

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

<span data-ttu-id="a0864-131">Ouvrez ensuite le `./src/app/home/home.component.html` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a0864-131">Then open the `./src/app/home/home.component.html` file and replace its contents with the following.</span></span>

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

<span data-ttu-id="a0864-132">À présent, créez un service d’alerte que l’application peut utiliser pour afficher des messages à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a0864-132">Now create an alert service that the app can use to display messages to the user.</span></span> <span data-ttu-id="a0864-133">Commencez par créer une classe `Alert` simple.</span><span class="sxs-lookup"><span data-stu-id="a0864-133">Start by creating a simple `Alert` class.</span></span> <span data-ttu-id="a0864-134">Créez un fichier dans le `./src/app` répertoire nommé `alert.ts` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="a0864-134">Create a new file in the `./src/app` directory named `alert.ts` and add the following code.</span></span>

```TypeScript
export class Alert {
  message: string;
  debug: string;
}
```

<span data-ttu-id="a0864-135">Dans votre interface CLI, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="a0864-135">In your CLI, run the following command.</span></span>

```Shell
ng generate service alerts
```

<span data-ttu-id="a0864-136">Ouvrez le `./src/app/alerts.service.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a0864-136">Open the `./src/app/alerts.service.ts` file and replace its contents with the following.</span></span>

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

<span data-ttu-id="a0864-137">À présent, générez un composant Alerts pour afficher les alertes.</span><span class="sxs-lookup"><span data-stu-id="a0864-137">Now generate an alerts component to display alerts.</span></span> <span data-ttu-id="a0864-138">Dans votre interface CLI, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="a0864-138">In your CLI, run the following command.</span></span>

```Shell
ng generate component alerts
```

<span data-ttu-id="a0864-139">Une fois la commande terminée, ouvrez le `./src/app/alerts/alerts.component.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a0864-139">Once the command completes, open the `./src/app/alerts/alerts.component.ts` file and replace its contents with the following.</span></span>

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

<span data-ttu-id="a0864-140">Ouvrez ensuite le `./src/app/alerts/alerts.component.html` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a0864-140">Then open the `./src/app/alerts/alerts.component.html` file and replace its contents with the following.</span></span>

```html
<div *ngFor="let alert of alertsService.alerts">
  <ngb-alert type="danger" (close)="close(alert)">
    <p>{{alert.message}}</p>
    <pre *ngIf="alert.debug" class="alert-pre border bg-light p-2"><code>{{alert.debug}}</code></pre>
  </ngb-alert>
</div>
```

<span data-ttu-id="a0864-141">Maintenant que les composants de base sont définis, mettez à jour l’application pour les utiliser.</span><span class="sxs-lookup"><span data-stu-id="a0864-141">Now with those basic components defined, update the app to use them.</span></span> <span data-ttu-id="a0864-142">Tout d’abord, `./src/app/app-routing.module.ts` Ouvrez le fichier et `const routes: Routes = [];` remplacez la ligne par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="a0864-142">First, open the `./src/app/app-routing.module.ts` file and replace the `const routes: Routes = [];` line with the following code.</span></span>

```TypeScript
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
];
```

<span data-ttu-id="a0864-143">Ouvrez le fichier `./src/app/app.component.html` et remplacez l’intégralité de son contenu par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a0864-143">Open the `./src/app/app.component.html` file and replace its entire contents with the following.</span></span>

```html
<app-nav-bar></app-nav-bar>
<main role="main" class="container">
  <app-alerts></app-alerts>
  <router-outlet></router-outlet>
</main>
```

<span data-ttu-id="a0864-144">Enregistrez toutes vos modifications et actualisez la page.</span><span class="sxs-lookup"><span data-stu-id="a0864-144">Save all of your changes and refresh the page.</span></span> <span data-ttu-id="a0864-145">À présent, l’application doit être très différente.</span><span class="sxs-lookup"><span data-stu-id="a0864-145">Now, the app should look very different.</span></span>

![Capture d’écran de la page d’accueil repensée](images/create-app-01.png)
