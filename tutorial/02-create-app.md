<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="28b53-101">Dans cette section, vous allez créer un projet Angular projet.</span><span class="sxs-lookup"><span data-stu-id="28b53-101">In this section, you'll create a new Angular project.</span></span>

1. <span data-ttu-id="28b53-102">Ouvrez votre interface de ligne de commande (CLI), accédez à un répertoire dans lequel vous avez le droit de créer des fichiers, puis exécutez les commandes suivantes pour installer l'outil [CLI Angular](https://www.npmjs.com/package/@angular/cli) et créer une application Angular.</span><span class="sxs-lookup"><span data-stu-id="28b53-102">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following commands to install the [Angular CLI](https://www.npmjs.com/package/@angular/cli) tool and create a new Angular app.</span></span>

    ```Shell
    npm install -g @angular/cli@11.2.9
    ng new graph-tutorial
    ```

1. <span data-ttu-id="28b53-103">L Angular CLI vous invite à fournir plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="28b53-103">The Angular CLI will prompt for more information.</span></span> <span data-ttu-id="28b53-104">Répondez aux invites comme suit.</span><span class="sxs-lookup"><span data-stu-id="28b53-104">Answer the prompts as follows.</span></span>

    ```Shell
    ? Do you want to enforce stricter type checking and stricter bundle budgets in the workspace? Yes
    ? Would you like to add Angular routing? Yes
    ? Which stylesheet format would you like to use? CSS
    ```

1. <span data-ttu-id="28b53-105">Une fois la commande finale, modifiez le répertoire de votre CLI et exécutez la commande suivante pour démarrer `graph-tutorial` un serveur web local.</span><span class="sxs-lookup"><span data-stu-id="28b53-105">Once the command finishes, change to the `graph-tutorial` directory in your CLI and run the following command to start a local web server.</span></span>

    ```Shell
    ng serve --open
    ```

1. <span data-ttu-id="28b53-106">Votre navigateur par défaut s'ouvre [https://localhost:4200/](https://localhost:4200) avec une page de Angular par défaut.</span><span class="sxs-lookup"><span data-stu-id="28b53-106">Your default browser opens to [https://localhost:4200/](https://localhost:4200) with a default Angular page.</span></span> <span data-ttu-id="28b53-107">Si votre navigateur ne s'ouvre pas, ouvrez-le et recherchez-le pour vérifier [https://localhost:4200/](https://localhost:4200) que la nouvelle application fonctionne.</span><span class="sxs-lookup"><span data-stu-id="28b53-107">If your browser doesn't open, open it and browse to [https://localhost:4200/](https://localhost:4200) to verify that the new app works.</span></span>

## <a name="add-node-packages"></a><span data-ttu-id="28b53-108">Ajouter des packages de nœuds</span><span class="sxs-lookup"><span data-stu-id="28b53-108">Add Node packages</span></span>

<span data-ttu-id="28b53-109">Avant de passer à la suite, installez des packages supplémentaires que vous utiliserez ultérieurement :</span><span class="sxs-lookup"><span data-stu-id="28b53-109">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="28b53-110">[bootstrap](https://github.com/twbs/bootstrap) pour les styles et les composants courants.</span><span class="sxs-lookup"><span data-stu-id="28b53-110">[bootstrap](https://github.com/twbs/bootstrap) for styling and common components.</span></span>
- <span data-ttu-id="28b53-111">[ng-bootstrap](https://github.com/ng-bootstrap/ng-bootstrap) pour utiliser des composants Bootstrap à partir de Angular.</span><span class="sxs-lookup"><span data-stu-id="28b53-111">[ng-bootstrap](https://github.com/ng-bootstrap/ng-bootstrap) for using Bootstrap components from Angular.</span></span>
- <span data-ttu-id="28b53-112">[moment de](https://github.com/moment/moment) mise en forme des dates et heures.</span><span class="sxs-lookup"><span data-stu-id="28b53-112">[moment](https://github.com/moment/moment) for formatting dates and times.</span></span>
- [<span data-ttu-id="28b53-113">windows-iana</span><span class="sxs-lookup"><span data-stu-id="28b53-113">windows-iana</span></span>](https://github.com/rubenillodo/windows-iana)
- <span data-ttu-id="28b53-114">[msal-angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) pour l'authentification auprès Azure Active Directory et la récupération des jetons d'accès.</span><span class="sxs-lookup"><span data-stu-id="28b53-114">[msal-angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) for authenticating to Azure Active Directory and retrieving access tokens.</span></span>
- <span data-ttu-id="28b53-115">[microsoft-graph-client pour](https://github.com/microsoftgraph/msgraph-sdk-javascript) effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="28b53-115">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>

1. <span data-ttu-id="28b53-116">Exécutez les commandes suivantes dans votre CLI.</span><span class="sxs-lookup"><span data-stu-id="28b53-116">Run the following commands in your CLI.</span></span>

    ```Shell
    npm install bootstrap@4.6.0 @ng-bootstrap/ng-bootstrap@9.1.0
    npm install @azure/msal-browser@2.14.0 @azure/msal-angular@2.0.0-beta.4
    npm install moment-timezone@0.5.33 windows-iana@5.0.2
    npm install @microsoft/microsoft-graph-client@2.2.1 @microsoft/microsoft-graph-types@1.35.0
    ```

1. <span data-ttu-id="28b53-117">Exécutez la commande suivante dans votre CLI pour ajouter Angular package de localisation (requis par ng-bootstrap).</span><span class="sxs-lookup"><span data-stu-id="28b53-117">Run the following command in your CLI to add the Angular localization package (required by ng-bootstrap).</span></span>

    ```Shell
    ng add @angular/localize
    ```

## <a name="design-the-app"></a><span data-ttu-id="28b53-118">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="28b53-118">Design the app</span></span>

<span data-ttu-id="28b53-119">Dans cette section, vous allez créer l'interface utilisateur de l'application.</span><span class="sxs-lookup"><span data-stu-id="28b53-119">In this section you'll create the user interface for the app.</span></span>

1. <span data-ttu-id="28b53-120">Ouvrez **./src/styles.css** et ajoutez les lignes suivantes.</span><span class="sxs-lookup"><span data-stu-id="28b53-120">Open **./src/styles.css** and add the following lines.</span></span>

    :::code language="css" source="../demo/graph-tutorial/src/styles.css":::

1. <span data-ttu-id="28b53-121">Ajoutez le module Bootstrap à l'application.</span><span class="sxs-lookup"><span data-stu-id="28b53-121">Add the Bootstrap module to the app.</span></span> <span data-ttu-id="28b53-122">Ouvrez **./src/app/app.module.ts** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="28b53-122">Open **./src/app/app.module.ts** and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="28b53-123">Créez un fichier dans **le dossier ./src/app** nommé **user.ts** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="28b53-123">Create a new file in the **./src/app** folder named **user.ts** and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/user.ts" id="UserSnippet":::

1. <span data-ttu-id="28b53-124">Générez un Angular pour la navigation supérieure sur la page.</span><span class="sxs-lookup"><span data-stu-id="28b53-124">Generate an Angular component for the top navigation on the page.</span></span> <span data-ttu-id="28b53-125">Dans votre CLI, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="28b53-125">In your CLI, run the following command.</span></span>

    ```Shell
    ng generate component nav-bar
    ```

1. <span data-ttu-id="28b53-126">Une fois la commande terminée, ouvrez **./src/app/nav-bar/nav-bar.component.ts** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="28b53-126">Once the command completes, open **./src/app/nav-bar/nav-bar.component.ts** and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="28b53-127">Ouvrez **./src/app/nav-bar/nav-bar.component.html** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="28b53-127">Open **./src/app/nav-bar/nav-bar.component.html** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/nav-bar/nav-bar.component.html" id="navHtml":::

1. <span data-ttu-id="28b53-128">Créez une page d'accueil pour l'application.</span><span class="sxs-lookup"><span data-stu-id="28b53-128">Create a home page for the app.</span></span> <span data-ttu-id="28b53-129">Exécutez la commande suivante dans votre CLI.</span><span class="sxs-lookup"><span data-stu-id="28b53-129">Run the following command in your CLI.</span></span>

    ```Shell
    ng generate component home
    ```

1. <span data-ttu-id="28b53-130">Une fois la commande terminée, ouvrez **./src/app/home/home.component.ts** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="28b53-130">Once the command completes, open **./src/app/home/home.component.ts** and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="28b53-131">Ouvrez **./src/app/home/home.component.html** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="28b53-131">Open **./src/app/home/home.component.html** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/home/home.component.html" id="homeHtml":::

1. <span data-ttu-id="28b53-132">Créez une classe `Alert` simple.</span><span class="sxs-lookup"><span data-stu-id="28b53-132">Create a simple `Alert` class.</span></span> <span data-ttu-id="28b53-133">Créez un fichier dans le répertoire **./src/app** nommé **alert.ts** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="28b53-133">Create a new file in the **./src/app** directory named **alert.ts** and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alert.ts" id="AlertSnippet":::

1. <span data-ttu-id="28b53-134">Créez un service d'alerte que l'application peut utiliser pour afficher des messages à l'utilisateur.</span><span class="sxs-lookup"><span data-stu-id="28b53-134">Create an alert service that the app can use to display messages to the user.</span></span> <span data-ttu-id="28b53-135">Dans votre CLI, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="28b53-135">In your CLI, run the following command.</span></span>

    ```Shell
    ng generate service alerts
    ```

1. <span data-ttu-id="28b53-136">Ouvrez **./src/app/alerts.service.ts** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="28b53-136">Open **./src/app/alerts.service.ts** and replace its contents with the following.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alerts.service.ts" id="alertsService":::

1. <span data-ttu-id="28b53-137">Générer un composant d'alertes pour afficher les alertes.</span><span class="sxs-lookup"><span data-stu-id="28b53-137">Generate an alerts component to display alerts.</span></span> <span data-ttu-id="28b53-138">Dans votre CLI, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="28b53-138">In your CLI, run the following command.</span></span>

    ```Shell
    ng generate component alerts
    ```

1. <span data-ttu-id="28b53-139">Une fois la commande terminée, ouvrez **./src/app/alerts/alerts.component.ts** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="28b53-139">Once the command completes, open **./src/app/alerts/alerts.component.ts** and replace its contents with the following.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/alerts/alerts.component.ts" id="AlertsComponentSnippet":::

1. <span data-ttu-id="28b53-140">Ouvrez **./src/app/alerts/alerts.component.html** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="28b53-140">Open **./src/app/alerts/alerts.component.html** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/alerts/alerts.component.html" id="AlertHtml":::

1. <span data-ttu-id="28b53-141">Ouvrez **./src/app/app-routing.module.ts** et remplacez la ligne `const routes: Routes = [];` par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="28b53-141">Open **./src/app/app-routing.module.ts** and replace the `const routes: Routes = [];` line with the following code.</span></span>

    ```typescript
    import { HomeComponent } from './home/home.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
    ];
    ```

1. <span data-ttu-id="28b53-142">Ouvrez **./src/app/app.component.html** et remplacez tout son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="28b53-142">Open **./src/app/app.component.html** and replace its entire contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/app.component.html" id="AppHtml":::

1. <span data-ttu-id="28b53-143">Ajoutez un fichier image de votre choix nommé **no-profile-photo.png** dans le répertoire **./src/assets.**</span><span class="sxs-lookup"><span data-stu-id="28b53-143">Add an image file of your choosing named **no-profile-photo.png** in the **./src/assets** directory.</span></span> <span data-ttu-id="28b53-144">Cette image est utilisée comme photo de l'utilisateur lorsque l'utilisateur n'a pas de photo dans Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="28b53-144">This image will be used as the user's photo when the user has no photo in Microsoft Graph.</span></span>

<span data-ttu-id="28b53-145">Enregistrez toutes vos modifications et actualisez la page.</span><span class="sxs-lookup"><span data-stu-id="28b53-145">Save all of your changes and refresh the page.</span></span> <span data-ttu-id="28b53-146">L'application doit maintenant avoir une apparence très différente.</span><span class="sxs-lookup"><span data-stu-id="28b53-146">Now, the app should look very different.</span></span>

![Capture d’écran de la page d’accueil repensée](images/create-app-01.png)
