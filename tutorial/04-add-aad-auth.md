<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="0b10b-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="0b10b-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="0b10b-102">Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0b10b-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="0b10b-103">Dans cette étape, vous allez intégrer la [bibliothèque d’authentification Microsoft pour les angles](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="0b10b-103">In this step you will integrate the [Microsoft Authentication Library for Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) into the application.</span></span>

1. <span data-ttu-id="0b10b-104">Créez un fichier dans le répertoire **./SRC** nommé **OAuth. TS** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="0b10b-104">Create a new file in the **./src** directory named **oauth.ts** and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/oauth.example.ts":::

    <span data-ttu-id="0b10b-105">Remplacez `YOUR_APP_ID_HERE` par l’ID de l’application dans le portail d’inscription des applications.</span><span class="sxs-lookup"><span data-stu-id="0b10b-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="0b10b-106">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le fichier **. TS OAuth** du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application.</span><span class="sxs-lookup"><span data-stu-id="0b10b-106">If you're using source control such as git, now would be a good time to exclude the **oauth.ts** file from source control to avoid inadvertently leaking your app ID.</span></span>

1. <span data-ttu-id="0b10b-107">Ouvrez **./SRC/app/app.module.TS** et ajoutez les `import` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="0b10b-107">Open **./src/app/app.module.ts** and add the following `import` statements to the top of the file.</span></span>

    ```typescript
    import { MsalModule } from '@azure/msal-angular';
    import { OAuthSettings } from '../oauth';
    ```

1. <span data-ttu-id="0b10b-108">Ajoutez le `MsalModule` au tableau à l' `imports` intérieur de la `@NgModule` déclaration et initialisez-le avec l’ID de l’application.</span><span class="sxs-lookup"><span data-stu-id="0b10b-108">Add the `MsalModule` to the `imports` array inside the `@NgModule` declaration, and initialize it with the app ID.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/app.module.ts" id="imports" highlight="6-11":::

## <a name="implement-sign-in"></a><span data-ttu-id="0b10b-109">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="0b10b-109">Implement sign-in</span></span>

<span data-ttu-id="0b10b-110">Dans cette section, vous allez créer un service d’authentification et mettre en œuvre la connexion et la déconnexion.</span><span class="sxs-lookup"><span data-stu-id="0b10b-110">In this section you'll create an authentication service and implement sign-in and sign-out.</span></span>

1. <span data-ttu-id="0b10b-111">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="0b10b-111">Run the following command in your CLI.</span></span>

    ```Shell
    ng generate service auth
    ```

    <span data-ttu-id="0b10b-112">En créant un service pour cela, vous pouvez facilement l’injecter dans les composants qui ont besoin d’accéder aux méthodes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="0b10b-112">By creating a service for this, you can easily inject it into any components that need access to authentication methods.</span></span>

1. <span data-ttu-id="0b10b-113">Une fois la commande terminée, ouvrez **/src/App/auth.service.TS** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="0b10b-113">Once the command finishes, open **./src/app/auth.service.ts** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="0b10b-114">Ouvrez **./SRC/App/nav-bar/NAV-bar.Component.TS** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="0b10b-114">Open **./src/app/nav-bar/nav-bar.component.ts** and replace its contents with the following.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/nav-bar/nav-bar.component.ts" id="navBarSnippet" highlight="3,15-22,24,26-28,36-38,40-42":::

1. <span data-ttu-id="0b10b-115">Ouvrez **./SRC/App/Home/Home.Component.TS** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="0b10b-115">Open **./src/app/home/home.component.ts** and replace its contents with the following.</span></span>

    :::code language="typescript" source="snippets/snippets.ts" id="homeSnippet" highlight="3,12-19,21,23,25-32":::

<span data-ttu-id="0b10b-116">Enregistrez vos modifications et actualisez le navigateur.</span><span class="sxs-lookup"><span data-stu-id="0b10b-116">Save your changes and refresh the browser.</span></span> <span data-ttu-id="0b10b-117">Cliquez sur le bouton **cliquez ici pour vous connecter** et vous devez être redirigé vers `https://login.microsoftonline.com` .</span><span class="sxs-lookup"><span data-stu-id="0b10b-117">Click the **Click here to sign in** button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="0b10b-118">Connectez-vous avec votre compte Microsoft et acceptez les autorisations demandées.</span><span class="sxs-lookup"><span data-stu-id="0b10b-118">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="0b10b-119">La page de l’application doit être actualisée, affichant le jeton.</span><span class="sxs-lookup"><span data-stu-id="0b10b-119">The app page should refresh, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="0b10b-120">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="0b10b-120">Get user details</span></span>

<span data-ttu-id="0b10b-121">Actuellement, le service d’authentification définit des valeurs constantes pour le nom d’affichage et l’adresse de messagerie de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0b10b-121">Right now the authentication service sets constant values for the user's display name and email address.</span></span> <span data-ttu-id="0b10b-122">Maintenant que vous disposez d’un jeton d’accès, vous pouvez obtenir les détails de l’utilisateur à partir de Microsoft Graph pour que ces valeurs correspondent à l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="0b10b-122">Now that you have an access token, you can get user details from Microsoft Graph so those values correspond to the current user.</span></span>

1. <span data-ttu-id="0b10b-123">Ouvrez **./SRC/App/auth.service.TS** et ajoutez les `import` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="0b10b-123">Open **./src/app/auth.service.ts** and add the following `import` statements to the top of the file.</span></span>

    ```typescript
    import { Client } from '@microsoft/microsoft-graph-client';
    import * as MicrosoftGraph from '@microsoft/microsoft-graph-types';
    ```

1. <span data-ttu-id="0b10b-124">Ajoutez une nouvelle fonction à la classe `AuthService` appelée `getUser`.</span><span class="sxs-lookup"><span data-stu-id="0b10b-124">Add a new function to the `AuthService` class called `getUser`.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/auth.service.ts" id="getUserSnippet":::

1. <span data-ttu-id="0b10b-125">Recherchez et supprimez le code suivant dans la `getAccessToken` méthode qui ajoute une alerte pour afficher le jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="0b10b-125">Locate and remove the following code in the `getAccessToken` method that adds an alert to display the access token.</span></span>

    ```typescript
    // Temporary to display token in an error box
    this.alertsService.addSuccess('Token acquired', result);
    ```

1. <span data-ttu-id="0b10b-126">Recherchez et supprimez le code suivant de la `signIn` méthode.</span><span class="sxs-lookup"><span data-stu-id="0b10b-126">Locate and remove the following code from the `signIn` method.</span></span>

    ```typescript
    // Temporary placeholder
    this.user = new User();
    this.user.displayName = "Adele Vance";
    this.user.email = "AdeleV@contoso.com";
    this.user.avatar = '/assets/no-profile-photo.png';
    ```

1. <span data-ttu-id="0b10b-127">À la place, ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="0b10b-127">In its place, add the following code.</span></span>

    ```typescript
    this.user = await this.getUser();
    ```

    <span data-ttu-id="0b10b-128">Ce nouveau code utilise le kit de développement logiciel (SDK) Microsoft Graph pour obtenir les détails de l’utilisateur, puis crée un `User` objet à l’aide des valeurs renvoyées par l’appel de l’API.</span><span class="sxs-lookup"><span data-stu-id="0b10b-128">This new code uses the Microsoft Graph SDK to get the user's details, then creates a `User` object using values returned by the API call.</span></span>

1. <span data-ttu-id="0b10b-129">Modifiez la `constructor` classe pour `AuthService` vérifier si l’utilisateur est déjà connecté et charger ses informations si c’est le cas.</span><span class="sxs-lookup"><span data-stu-id="0b10b-129">Change the `constructor` for the `AuthService` class to check if the user is already logged in and load their details if so.</span></span> <span data-ttu-id="0b10b-130">Remplacez le existant `constructor` par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="0b10b-130">Replace the existing `constructor` with the following.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/auth.service.ts" id="constructorSnippet" highlight="5-6":::

1. <span data-ttu-id="0b10b-131">Supprimez le code temporaire de la `HomeComponent` classe.</span><span class="sxs-lookup"><span data-stu-id="0b10b-131">Remove the temporary code from the `HomeComponent` class.</span></span> <span data-ttu-id="0b10b-132">Ouvrez **./SRC/App/Home/Home.Component.TS** et remplacez la `signIn` fonction existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="0b10b-132">Open **./src/app/home/home.component.ts** and replace the existing `signIn` function with the following.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/home/home.component.ts" id="signInSnippet":::

<span data-ttu-id="0b10b-133">Maintenant, si vous enregistrez vos modifications et démarrez l’application, après vous être connecté, vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes connecté.</span><span class="sxs-lookup"><span data-stu-id="0b10b-133">Now if you save your changes and start the app, after sign-in you should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

<span data-ttu-id="0b10b-135">Cliquez sur Avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **déconnexion** .</span><span class="sxs-lookup"><span data-stu-id="0b10b-135">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="0b10b-136">Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="0b10b-136">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="0b10b-138">Stockage et actualisation des jetons</span><span class="sxs-lookup"><span data-stu-id="0b10b-138">Storing and refreshing tokens</span></span>

<span data-ttu-id="0b10b-139">À ce stade, votre application a un jeton d’accès, qui est envoyé dans l' `Authorization` en-tête des appels d’API.</span><span class="sxs-lookup"><span data-stu-id="0b10b-139">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="0b10b-140">Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph pour le compte de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0b10b-140">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="0b10b-141">Cependant, ce jeton est de courte durée.</span><span class="sxs-lookup"><span data-stu-id="0b10b-141">However, this token is short-lived.</span></span> <span data-ttu-id="0b10b-142">Le jeton expire une heure après son émission.</span><span class="sxs-lookup"><span data-stu-id="0b10b-142">The token expires an hour after it is issued.</span></span> <span data-ttu-id="0b10b-143">Étant donné que l’application utilise la bibliothèque MSAL, vous n’avez pas besoin d’implémenter de logique d’actualisation ou de stockage de jetons.</span><span class="sxs-lookup"><span data-stu-id="0b10b-143">Because the app is using the MSAL library, you do not have to implement any token storage or refresh logic.</span></span> <span data-ttu-id="0b10b-144">Le `MsalService` jeton est mis en cache dans le stockage du navigateur.</span><span class="sxs-lookup"><span data-stu-id="0b10b-144">The `MsalService` caches the token in the browser storage.</span></span> <span data-ttu-id="0b10b-145">La `acquireTokenSilent` méthode vérifie d’abord le jeton mis en cache et, s’il n’a pas expiré, il le renvoie.</span><span class="sxs-lookup"><span data-stu-id="0b10b-145">The `acquireTokenSilent` method first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="0b10b-146">Si elle a expiré, elle effectue une demande silencieuse pour en obtenir une nouvelle.</span><span class="sxs-lookup"><span data-stu-id="0b10b-146">If it is expired, it makes a silent request to obtain a new one.</span></span>
