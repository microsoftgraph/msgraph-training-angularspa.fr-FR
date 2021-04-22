# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="c735d-101">Comment exécuter le projet terminé</span><span class="sxs-lookup"><span data-stu-id="c735d-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c735d-102">Configuration requise</span><span class="sxs-lookup"><span data-stu-id="c735d-102">Prerequisites</span></span>

<span data-ttu-id="c735d-103">Pour exécuter le projet terminé dans ce dossier, vous devez :</span><span class="sxs-lookup"><span data-stu-id="c735d-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="c735d-104">[Node.js](https://nodejs.org) installé sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="c735d-104">[Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="c735d-105">Si vous n'avez pas Node.js, consultez le lien précédent pour obtenir les options de téléchargement.</span><span class="sxs-lookup"><span data-stu-id="c735d-105">If you do not have Node.js, visit the previous link for download options.</span></span> <span data-ttu-id="c735d-106">(**Remarque : ce** didacticiel a été écrit avec Node version 14.15.0.</span><span class="sxs-lookup"><span data-stu-id="c735d-106">(**Note:** This tutorial was written with Node version 14.15.0.</span></span> <span data-ttu-id="c735d-107">Les étapes de ce guide peuvent fonctionner avec d'autres versions, mais cela n'a pas été testé.)</span><span class="sxs-lookup"><span data-stu-id="c735d-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="c735d-108">[Angular CLI](https://cli.angular.io/) installé sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="c735d-108">[Angular CLI](https://cli.angular.io/) installed on your development machine.</span></span>
- <span data-ttu-id="c735d-109">Soit un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, soit un compte scolaire ou scolaire Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c735d-109">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="c735d-110">Si vous n'avez pas de compte Microsoft, deux options s'offrent à vous pour obtenir un compte gratuit :</span><span class="sxs-lookup"><span data-stu-id="c735d-110">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="c735d-111">Vous pouvez [vous inscrire à un nouveau compte Microsoft personnel.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)</span><span class="sxs-lookup"><span data-stu-id="c735d-111">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="c735d-112">Vous pouvez vous inscrire au programme pour les développeurs [Office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement Office 365 gratuit.</span><span class="sxs-lookup"><span data-stu-id="c735d-112">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="c735d-113">Inscrire une application web auprès du Centre d'administration Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="c735d-113">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="c735d-114">Ouvrez un navigateur et accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="c735d-114">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="c735d-115">Connectez-vous à l’aide d’un **compte personnel** (compte Microsoft) ou d’un **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="c735d-115">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="c735d-116">Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis sélectionnez **Inscriptions d’applications** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="c735d-116">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="c735d-117">Une capture d’écran des inscriptions d’applications</span><span class="sxs-lookup"><span data-stu-id="c735d-117">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="c735d-118">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="c735d-118">Select **New registration**.</span></span> <span data-ttu-id="c735d-119">Sur la page **Inscrire une application**, définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="c735d-119">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="c735d-120">Définissez le **Nom** sur `Angular Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="c735d-120">Set **Name** to `Angular Graph Tutorial`.</span></span>
    - <span data-ttu-id="c735d-121">Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="c735d-121">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="c735d-122">Sous **URI de redirection**, définissez la première flèche déroulante sur `Single-page application (SPA)`, et la valeur sur `http://localhost:4200`.</span><span class="sxs-lookup"><span data-stu-id="c735d-122">Under **Redirect URI**, set the first drop-down to `Single-page application (SPA)` and set the value to `http://localhost:4200`.</span></span>

    ![Capture d’écran de la page Inscrire une application](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="c735d-124">Choisissez **Inscrire**.</span><span class="sxs-lookup"><span data-stu-id="c735d-124">Choose **Register**.</span></span> <span data-ttu-id="c735d-125">Dans la page didacticiel **Angular Graph,** copiez la valeur de l'ID **d'application (client)** et enregistrez-la. Vous en aurez besoin à l'étape suivante.</span><span class="sxs-lookup"><span data-stu-id="c735d-125">On the **Angular Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Une capture d’écran de l’ID d’application de la nouvelle inscription d'application](/tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a><span data-ttu-id="c735d-127">Configurer l’exemple</span><span class="sxs-lookup"><span data-stu-id="c735d-127">Configure the sample</span></span>

1. <span data-ttu-id="c735d-128">Renommons `oauth.ts.example` le fichier `oauth.ts` .</span><span class="sxs-lookup"><span data-stu-id="c735d-128">Rename the `oauth.ts.example` file to `oauth.ts`.</span></span>
1. <span data-ttu-id="c735d-129">Modifiez `oauth.ts` le fichier et a apporter les modifications suivantes.</span><span class="sxs-lookup"><span data-stu-id="c735d-129">Edit the `oauth.ts` file and make the following changes.</span></span>
    1. <span data-ttu-id="c735d-130">Remplacez `YOUR_APP_ID_HERE` par **l'ID d'application** que vous avez obtenu à partir du portail d'inscription des applications.</span><span class="sxs-lookup"><span data-stu-id="c735d-130">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="c735d-131">Dans votre interface de ligne de commande, accédez à ce répertoire et exécutez la commande suivante pour installer les conditions requises.</span><span class="sxs-lookup"><span data-stu-id="c735d-131">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    npm install
    ```

## <a name="run-the-sample"></a><span data-ttu-id="c735d-132">Exécution de l’exemple</span><span class="sxs-lookup"><span data-stu-id="c735d-132">Run the sample</span></span>

1. <span data-ttu-id="c735d-133">Exécutez la commande suivante dans votre CLI pour démarrer l'application.</span><span class="sxs-lookup"><span data-stu-id="c735d-133">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    ng serve
    ```

1. <span data-ttu-id="c735d-134">Ouvrez un navigateur et accédez à `http://localhost:4200`.</span><span class="sxs-lookup"><span data-stu-id="c735d-134">Open a browser and browse to `http://localhost:4200`.</span></span>
