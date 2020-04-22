<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f3ef7-101">Dans cette section, vous allez créer un nouveau projet angulaire.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-101">In this section, you'll create a new Angular project.</span></span>

1. <span data-ttu-id="f3ef7-102">Ouvrez votre interface de ligne de commande (CLI), accédez à un répertoire où vous disposez de droits pour créer des fichiers, puis exécutez les commandes suivantes pour installer l’outil [CLI angulaire](https://www.npmjs.com/package/@angular/cli) et créer une application angulaire.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-102">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following commands to install the [Angular CLI](https://www.npmjs.com/package/@angular/cli) tool and create a new Angular app.</span></span>

    ```Shell
    npm install -g @angular/cli@9.0.6
    ng new graph-tutorial
    ```

1. <span data-ttu-id="f3ef7-103">La CLI angulaire demande davantage d’informations.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-103">The Angular CLI will prompt for more information.</span></span> <span data-ttu-id="f3ef7-104">Répondez aux invites comme suit.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-104">Answer the prompts as follows.</span></span>

    ```Shell
    ? Would you like to add Angular routing? Yes
    ? Which stylesheet format would you like to use? CSS
    ```

1. <span data-ttu-id="f3ef7-105">Une fois la commande terminée, accédez au `graph-tutorial` répertoire dans votre interface CLI et exécutez la commande suivante pour démarrer un serveur Web local.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-105">Once the command finishes, change to the `graph-tutorial` directory in your CLI and run the following command to start a local web server.</span></span>

    ```Shell
    ng serve --open
    ```

1. <span data-ttu-id="f3ef7-106">Votre navigateur par défaut s' [https://localhost:4200/](https://localhost:4200) ouvre avec une page d’angle par défaut.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-106">Your default browser opens to [https://localhost:4200/](https://localhost:4200) with a default Angular page.</span></span> <span data-ttu-id="f3ef7-107">Si votre navigateur ne s’ouvre pas, ouvrez-le [https://localhost:4200/](https://localhost:4200) et accédez à pour vérifier que la nouvelle application fonctionne.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-107">If your browser doesn't open, open it and browse to [https://localhost:4200/](https://localhost:4200) to verify that the new app works.</span></span>

## <a name="add-node-packages"></a><span data-ttu-id="f3ef7-108">Ajouter des packages de nœuds</span><span class="sxs-lookup"><span data-stu-id="f3ef7-108">Add Node packages</span></span>

<span data-ttu-id="f3ef7-109">Avant de poursuivre, installez des packages supplémentaires que vous utiliserez plus tard :</span><span class="sxs-lookup"><span data-stu-id="f3ef7-109">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="f3ef7-110">[bootstrap](https://github.com/twbs/bootstrap) pour le style et les composants communs.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-110">[bootstrap](https://github.com/twbs/bootstrap) for styling and common components.</span></span>
- <span data-ttu-id="f3ef7-111">[GN-bootstrap](https://github.com/ng-bootstrap/ng-bootstrap) pour l’utilisation de composants bootstrap à partir d’un angle.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-111">[ng-bootstrap](https://github.com/ng-bootstrap/ng-bootstrap) for using Bootstrap components from Angular.</span></span>
- <span data-ttu-id="f3ef7-112">[fontawesome](https://github.com/FortAwesome/angular-fontawesome) pour utiliser des icônes fontawesome en tant qu’angles.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-112">[angular-fontawesome](https://github.com/FortAwesome/angular-fontawesome) to use FontAwesome icons in Angular.</span></span>
- <span data-ttu-id="f3ef7-113">[fontawesome-SVG-Core](https://github.com/FortAwesome/Font-Awesome), [Free-Regular-SVG-Icons](https://github.com/FortAwesome/Font-Awesome)et [Free-Solid-SVG-Icons](https://github.com/FortAwesome/Font-Awesome) pour les icônes fontawesome utilisées dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-113">[fontawesome-svg-core](https://github.com/FortAwesome/Font-Awesome), [free-regular-svg-icons](https://github.com/FortAwesome/Font-Awesome), and [free-solid-svg-icons](https://github.com/FortAwesome/Font-Awesome) for the FontAwesome icons used in the sample.</span></span>
- <span data-ttu-id="f3ef7-114">[moment](https://github.com/moment/moment) de mise en forme des dates et des heures.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-114">[moment](https://github.com/moment/moment) for formatting dates and times.</span></span>
- <span data-ttu-id="f3ef7-115">[MSAL-angulaire](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) pour l’authentification auprès d’Azure Active Directory et pour la récupération des jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-115">[msal-angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) for authenticating to Azure Active Directory and retrieving access tokens.</span></span>
- <span data-ttu-id="f3ef7-116">[Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-116">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>

1. <span data-ttu-id="f3ef7-117">Exécutez les commandes suivantes dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-117">Run the following commands in your CLI.</span></span>

    ```Shell
    npm install bootstrap@4.4.1 @fortawesome/angular-fontawesome@0.6.0 @fortawesome/fontawesome-svg-core@1.2.27
    npm install @fortawesome/free-regular-svg-icons@5.12.1 @fortawesome/free-solid-svg-icons@5.12.1
    npm install moment@2.24.0 moment-timezone@0.5.28 @ng-bootstrap/ng-bootstrap@6.0.0
    npm install msal@1.2.1 @azure/msal-angular@1.0.0-beta.4 @microsoft/microsoft-graph-client@2.0.0
    ```

1. <span data-ttu-id="f3ef7-118">Exécutez la commande suivante dans votre interface CLI pour ajouter le package de localisation angulaire (requis par le GN-bootstrap).</span><span class="sxs-lookup"><span data-stu-id="f3ef7-118">Run the following command in your CLI to add the Angular localization package (required by ng-bootstrap).</span></span>

    ```Shell
    ng add @angular/localize
    ```

## <a name="design-the-app"></a><span data-ttu-id="f3ef7-119">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="f3ef7-119">Design the app</span></span>

<span data-ttu-id="f3ef7-120">Dans cette section, vous allez créer l’interface utilisateur de l’application.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-120">In this section you'll create the user interface for the app.</span></span>

1. <span data-ttu-id="f3ef7-121">Ouvrez le `./src/styles.css` et ajoutez les lignes suivantes.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-121">Open the `./src/styles.css` and add the following lines.</span></span>

    :::code language="css" source="../demo/graph-tutorial/src/styles.css":::

1. <span data-ttu-id="f3ef7-122">Ajoutez les modules bootstrap et FontAwesome à l’application.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-122">Add the Bootstrap and FontAwesome modules to the app.</span></span> <span data-ttu-id="f3ef7-123">Ouvrez `./src/app/app.module.ts` et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-123">Open `./src/app/app.module.ts` and replace its contents with the following.</span></span>

    ```TypeScript
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { NgbModule } from '@ng-bootstrap/ng-bootstrap';
    import { FontAwesomeModule, FaIconLibrary } from '@fortawesome/angular-fontawesome';
    import { faExternalLinkAlt } from '@fortawesome/free-solid-svg-icons';
    import { faUserCircle } from '@fortawesome/free-regular-svg-icons';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { NavBarComponent } from './nav-bar/nav-bar.component';
    import { HomeComponent } from './home/home.component';
    import { AlertsComponent } from './alerts/alerts.component';

    @NgModule({
      declarations: [
        AppComponent,
        NavBarComponent,
        HomeComponent,
        AlertsComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        NgbModule,
        FontAwesomeModule
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule {
      constructor(library: FaIconLibrary) {
        // Register the FontAwesome icons used by the app
        library.addIcons(faExternalLinkAlt, faUserCircle);
      }
     }
    ```

1. <span data-ttu-id="f3ef7-124">Créez un fichier dans le `./src/app` dossier nommé `user.ts` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-124">Create a new file in the `./src/app` folder named `user.ts` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/user.ts" id="user":::

1. <span data-ttu-id="f3ef7-125">Générer un composant angulaire pour la navigation supérieure sur la page.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-125">Generate an Angular component for the top navigation on the page.</span></span> <span data-ttu-id="f3ef7-126">Dans votre interface CLI, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-126">In your CLI, run the following command.</span></span>

    ```Shell
    ng generate component nav-bar
    ```

1. <span data-ttu-id="f3ef7-127">Une fois la commande terminée, ouvrez le `./src/app/nav-bar/nav-bar.component.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-127">Once the command completes, open the `./src/app/nav-bar/nav-bar.component.ts` file and replace its contents with the following.</span></span>

    ```TypeScript
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

1. <span data-ttu-id="f3ef7-128">Ouvrez le `./src/app/nav-bar/nav-bar.component.html` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-128">Open the `./src/app/nav-bar/nav-bar.component.html` file and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/nav-bar/nav-bar.component.html" id="navHtml":::

1. <span data-ttu-id="f3ef7-129">Créez une page d’accueil pour l’application.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-129">Create a home page for the app.</span></span> <span data-ttu-id="f3ef7-130">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-130">Run the following command in your CLI.</span></span>

    ```Shell
    ng generate component home
    ```

1. <span data-ttu-id="f3ef7-131">Une fois la commande terminée, ouvrez le `./src/app/home/home.component.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-131">Once the command completes, open the `./src/app/home/home.component.ts` file and replace its contents with the following.</span></span>

    ```TypeScript
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

1. <span data-ttu-id="f3ef7-132">Ouvrez le `./src/app/home/home.component.html` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-132">Open the `./src/app/home/home.component.html` file and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/home/home.component.html" id="homeHtml":::

1. <span data-ttu-id="f3ef7-133">Créez une classe `Alert` simple.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-133">Create a simple `Alert` class.</span></span> <span data-ttu-id="f3ef7-134">Créez un fichier dans le `./src/app` répertoire nommé `alert.ts` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-134">Create a new file in the `./src/app` directory named `alert.ts` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alert.ts" id="alert":::

1. <span data-ttu-id="f3ef7-135">Créer un service d’alerte que l’application peut utiliser pour afficher des messages à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-135">Create an alert service that the app can use to display messages to the user.</span></span> <span data-ttu-id="f3ef7-136">Dans votre interface CLI, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-136">In your CLI, run the following command.</span></span>

    ```Shell
    ng generate service alerts
    ```

1. <span data-ttu-id="f3ef7-137">Ouvrez le `./src/app/alerts.service.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-137">Open the `./src/app/alerts.service.ts` file and replace its contents with the following.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alerts.service.ts" id="alertsService":::

1. <span data-ttu-id="f3ef7-138">Générer un composant Alerts pour afficher les alertes.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-138">Generate an alerts component to display alerts.</span></span> <span data-ttu-id="f3ef7-139">Dans votre interface CLI, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-139">In your CLI, run the following command.</span></span>

    ```Shell
    ng generate component alerts
    ```

1. <span data-ttu-id="f3ef7-140">Une fois la commande terminée, ouvrez le `./src/app/alerts/alerts.component.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-140">Once the command completes, open the `./src/app/alerts/alerts.component.ts` file and replace its contents with the following.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alerts/alerts.component.ts" id="alertComponent":::

1. <span data-ttu-id="f3ef7-141">Ouvrez le `./src/app/alerts/alerts.component.html` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-141">Open the `./src/app/alerts/alerts.component.html` file and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/alerts/alerts.component.html" id="alertHtml":::

1. <span data-ttu-id="f3ef7-142">Ouvrez le `./src/app/app-routing.module.ts` fichier et remplacez la `const routes: Routes = [];` ligne par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-142">Open the `./src/app/app-routing.module.ts` file and replace the `const routes: Routes = [];` line with the following code.</span></span>

    ```typescript
    import { HomeComponent } from './home/home.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
    ];
    ```

1. <span data-ttu-id="f3ef7-143">Ouvrez le fichier `./src/app/app.component.html` et remplacez l’intégralité de son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-143">Open the `./src/app/app.component.html` file and replace its entire contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/app.component.html" id="appHtml":::

<span data-ttu-id="f3ef7-144">Enregistrez toutes vos modifications et actualisez la page.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-144">Save all of your changes and refresh the page.</span></span> <span data-ttu-id="f3ef7-145">À présent, l’application doit être très différente.</span><span class="sxs-lookup"><span data-stu-id="f3ef7-145">Now, the app should look very different.</span></span>

![Capture d’écran de la page d’accueil repensée](images/create-app-01.png)
