<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="9b74b-101">Dans cet exercice, vous allez étendre l'application de l'exercice précédent pour prendre en charge l'authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="9b74b-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="9b74b-102">Cette étape est nécessaire pour obtenir le jeton d'accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="9b74b-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="9b74b-103">Dans cette étape, vous allez intégrer la bibliothèque d'authentification [Microsoft pour Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) dans l'application.</span><span class="sxs-lookup"><span data-stu-id="9b74b-103">In this step you will integrate the [Microsoft Authentication Library for Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) into the application.</span></span>

1. <span data-ttu-id="9b74b-104">Créez un fichier dans le répertoire **./src** nommé **oauth.ts** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="9b74b-104">Create a new file in the **./src** directory named **oauth.ts** and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/oauth.example.ts":::

    <span data-ttu-id="9b74b-105">`YOUR_APP_ID_HERE`Remplacez-le par l'ID de l'application à partir du portail d'inscription des applications.</span><span class="sxs-lookup"><span data-stu-id="9b74b-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="9b74b-106">Si vous utilisez un contrôle source tel que Git, il est temps d'exclure le fichier **oauth.ts** du contrôle source afin d'éviter toute fuite accidentelle de votre ID d'application.</span><span class="sxs-lookup"><span data-stu-id="9b74b-106">If you're using source control such as git, now would be a good time to exclude the **oauth.ts** file from source control to avoid inadvertently leaking your app ID.</span></span>

1. <span data-ttu-id="9b74b-107">Ouvrez **./src/app/app.module.ts** et ajoutez les `import` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="9b74b-107">Open **./src/app/app.module.ts** and add the following `import` statements to the top of the file.</span></span>

    ```typescript
    import { IPublicClientApplication,
             PublicClientApplication,
             BrowserCacheLocation } from '@azure/msal-browser';
    import { MsalModule,
             MsalService,
             MSAL_INSTANCE } from '@azure/msal-angular';
    import { OAuthSettings } from '../oauth';
    ```

1. <span data-ttu-id="9b74b-108">Ajoutez la fonction suivante sous les `import` instructions.</span><span class="sxs-lookup"><span data-stu-id="9b74b-108">Add the following function below the `import` statements.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/app.module.ts" id="MSALFactorySnippet":::

1. <span data-ttu-id="9b74b-109">Ajoutez `MsalModule` le tableau à `imports` l'intérieur de la `@NgModule` déclaration.</span><span class="sxs-lookup"><span data-stu-id="9b74b-109">Add the `MsalModule` to the `imports` array inside the `@NgModule` declaration.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/app.module.ts" id="ImportsSnippet" highlight="6":::

1. <span data-ttu-id="9b74b-110">Ajoutez le `MSALInstanceFactory` tableau et le tableau à `MsalService` `providers` l'intérieur de la `@NgModule` déclaration.</span><span class="sxs-lookup"><span data-stu-id="9b74b-110">Add the `MSALInstanceFactory` and `MsalService` to the `providers` array inside the `@NgModule` declaration.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/app.module.ts" id="ProvidersSnippet" highlight="2-6":::

## <a name="implement-sign-in"></a><span data-ttu-id="9b74b-111">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="9b74b-111">Implement sign-in</span></span>

<span data-ttu-id="9b74b-112">Dans cette section, vous allez créer un service d'authentification et implémenter la sign-in et la sign-out.</span><span class="sxs-lookup"><span data-stu-id="9b74b-112">In this section you'll create an authentication service and implement sign-in and sign-out.</span></span>

1. <span data-ttu-id="9b74b-113">Exécutez la commande suivante dans votre CLI.</span><span class="sxs-lookup"><span data-stu-id="9b74b-113">Run the following command in your CLI.</span></span>

    ```Shell
    ng generate service auth
    ```

    <span data-ttu-id="9b74b-114">En créant un service pour cela, vous pouvez facilement l'injecter dans tous les composants qui ont besoin d'accéder aux méthodes d'authentification.</span><span class="sxs-lookup"><span data-stu-id="9b74b-114">By creating a service for this, you can easily inject it into any components that need access to authentication methods.</span></span>

1. <span data-ttu-id="9b74b-115">Une fois la commande terminé, ouvrez **./src/app/auth.service.ts** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="9b74b-115">Once the command finishes, open **./src/app/auth.service.ts** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="9b74b-116">Ouvrez **./src/app/nav-bar/nav-bar.component.ts** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9b74b-116">Open **./src/app/nav-bar/nav-bar.component.ts** and replace its contents with the following.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/nav-bar/nav-bar.component.ts" id="navBarSnippet" highlight="3,15-22,24,34-36,38-40":::

1. <span data-ttu-id="9b74b-117">Ouvrez **./src/app/home/home.component.ts** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9b74b-117">Open **./src/app/home/home.component.ts** and replace its contents with the following.</span></span>

    :::code language="typescript" source="snippets/snippets.ts" id="homeSnippet" highlight="3,13-20,22,26-33":::

<span data-ttu-id="9b74b-118">Enregistrez vos modifications et actualisez le navigateur.</span><span class="sxs-lookup"><span data-stu-id="9b74b-118">Save your changes and refresh the browser.</span></span> <span data-ttu-id="9b74b-119">Cliquez sur **le bouton Cliquer ici pour vous inscrire** et vous devez être redirigé vers `https://login.microsoftonline.com` .</span><span class="sxs-lookup"><span data-stu-id="9b74b-119">Click the **Click here to sign in** button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="9b74b-120">Connectez-vous avec votre compte Microsoft et consentez aux autorisations demandées.</span><span class="sxs-lookup"><span data-stu-id="9b74b-120">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="9b74b-121">La page de l'application doit s'actualiser et afficher le jeton.</span><span class="sxs-lookup"><span data-stu-id="9b74b-121">The app page should refresh, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="9b74b-122">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="9b74b-122">Get user details</span></span>

<span data-ttu-id="9b74b-123">Pour l'instant, le service d'authentification définit des valeurs constantes pour le nom d'affichage et l'adresse e-mail de l'utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9b74b-123">Right now the authentication service sets constant values for the user's display name and email address.</span></span> <span data-ttu-id="9b74b-124">Maintenant que vous avez un jeton d'accès, vous pouvez obtenir des détails utilisateur à partir de Microsoft Graph afin que ces valeurs correspondent à l'utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="9b74b-124">Now that you have an access token, you can get user details from Microsoft Graph so those values correspond to the current user.</span></span>

1. <span data-ttu-id="9b74b-125">Ouvrez **./src/app/auth.service.ts** et ajoutez les `import` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="9b74b-125">Open **./src/app/auth.service.ts** and add the following `import` statements to the top of the file.</span></span>

    ```typescript
    import { Client } from '@microsoft/microsoft-graph-client';
    import * as MicrosoftGraph from '@microsoft/microsoft-graph-types';
    ```

1. <span data-ttu-id="9b74b-126">Ajoutez une nouvelle fonction à la classe `AuthService` appelée `getUser`.</span><span class="sxs-lookup"><span data-stu-id="9b74b-126">Add a new function to the `AuthService` class called `getUser`.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/auth.service.ts" id="GetUserSnippet":::

1. <span data-ttu-id="9b74b-127">Recherchez et supprimez le code suivant dans la méthode qui ajoute une `getAccessToken` alerte pour afficher le jeton d'accès.</span><span class="sxs-lookup"><span data-stu-id="9b74b-127">Locate and remove the following code in the `getAccessToken` method that adds an alert to display the access token.</span></span>

    ```typescript
    // Temporary to display token in an error box
    this.alertsService.addSuccess('Token acquired', result);
    ```

1. <span data-ttu-id="9b74b-128">Recherchez et supprimez le code suivant de la `signIn` méthode.</span><span class="sxs-lookup"><span data-stu-id="9b74b-128">Locate and remove the following code from the `signIn` method.</span></span>

    ```typescript
    // Temporary placeholder
    this.user = new User();
    this.user.displayName = "Adele Vance";
    this.user.email = "AdeleV@contoso.com";
    this.user.avatar = '/assets/no-profile-photo.png';
    ```

1. <span data-ttu-id="9b74b-129">À la place, ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="9b74b-129">In its place, add the following code.</span></span>

    ```typescript
    this.user = await this.getUser();
    ```

    <span data-ttu-id="9b74b-130">Ce nouveau code utilise le SDK Microsoft Graph pour obtenir les détails de l'utilisateur, puis crée un objet à l'aide des valeurs renvoyées par `User` l'appel d'API.</span><span class="sxs-lookup"><span data-stu-id="9b74b-130">This new code uses the Microsoft Graph SDK to get the user's details, then creates a `User` object using values returned by the API call.</span></span>

1. <span data-ttu-id="9b74b-131">Modifiez la classe pour vérifier si l'utilisateur est déjà connecté et chargez ses `constructor` `AuthService` détails si c'est le cas.</span><span class="sxs-lookup"><span data-stu-id="9b74b-131">Change the `constructor` for the `AuthService` class to check if the user is already logged in and load their details if so.</span></span> <span data-ttu-id="9b74b-132">Remplacez `constructor` l'existant par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9b74b-132">Replace the existing `constructor` with the following.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/auth.service.ts" id="ConstructorSnippet" highlight="5-7":::

1. <span data-ttu-id="9b74b-133">Supprimez le code temporaire de la `HomeComponent` classe.</span><span class="sxs-lookup"><span data-stu-id="9b74b-133">Remove the temporary code from the `HomeComponent` class.</span></span> <span data-ttu-id="9b74b-134">Ouvrez **./src/app/home/home.component.ts** et remplacez la fonction `signIn` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9b74b-134">Open **./src/app/home/home.component.ts** and replace the existing `signIn` function with the following.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/home/home.component.ts" id="SignInSnippet":::

<span data-ttu-id="9b74b-135">Maintenant, si vous enregistrez vos modifications et démarrez l'application, après vous être connectez, vous devez revenir sur la page d'accueil, mais l'interface utilisateur doit changer pour indiquer que vous êtes connecté.</span><span class="sxs-lookup"><span data-stu-id="9b74b-135">Now if you save your changes and start the app, after sign-in you should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

<span data-ttu-id="9b74b-137">Cliquez sur l'avatar de l'utilisateur dans le coin supérieur droit pour accéder au lien **de** connexion.</span><span class="sxs-lookup"><span data-stu-id="9b74b-137">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="9b74b-138">Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="9b74b-138">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="9b74b-140">Stockage et actualisation des jetons</span><span class="sxs-lookup"><span data-stu-id="9b74b-140">Storing and refreshing tokens</span></span>

<span data-ttu-id="9b74b-141">À ce stade, votre application dispose d'un jeton d'accès, qui est envoyé dans l'en-tête des `Authorization` appels d'API.</span><span class="sxs-lookup"><span data-stu-id="9b74b-141">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="9b74b-142">Il s'agit du jeton qui permet à l'application d'accéder à Microsoft Graph au nom de l'utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9b74b-142">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="9b74b-143">Cependant, ce jeton est de courte durée.</span><span class="sxs-lookup"><span data-stu-id="9b74b-143">However, this token is short-lived.</span></span> <span data-ttu-id="9b74b-144">Le jeton expire une heure après son émission.</span><span class="sxs-lookup"><span data-stu-id="9b74b-144">The token expires an hour after it is issued.</span></span> <span data-ttu-id="9b74b-145">Étant donné que l'application utilise la bibliothèque MSAL, vous n'avez pas besoin d'implémenter de logique de stockage ou d'actualisation de jeton.</span><span class="sxs-lookup"><span data-stu-id="9b74b-145">Because the app is using the MSAL library, you do not have to implement any token storage or refresh logic.</span></span> <span data-ttu-id="9b74b-146">Le `MsalService` jeton est mis en cache dans le stockage du navigateur.</span><span class="sxs-lookup"><span data-stu-id="9b74b-146">The `MsalService` caches the token in the browser storage.</span></span> <span data-ttu-id="9b74b-147">La méthode vérifie d'abord le jeton mis en cache et, s'il `acquireTokenSilent` n'a pas expiré, elle le renvoie.</span><span class="sxs-lookup"><span data-stu-id="9b74b-147">The `acquireTokenSilent` method first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="9b74b-148">Si elle a expiré, elle effectue une demande silencieuse pour en obtenir une nouvelle.</span><span class="sxs-lookup"><span data-stu-id="9b74b-148">If it is expired, it makes a silent request to obtain a new one.</span></span>
