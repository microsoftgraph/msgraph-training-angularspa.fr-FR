<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="600c6-101">Dans cet exercice, vous allez créer une inscription de l'application Web Azure AD à l'aide du portail de registre d'applications (ARP).</span><span class="sxs-lookup"><span data-stu-id="600c6-101">In this exercise, you will create a new Azure AD web application registration using the Application Registry Portal (ARP).</span></span>

1. <span data-ttu-id="600c6-102">Ouvrez un navigateur et accédez au [portail d'inscription des applications](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="600c6-102">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="600c6-103">Connectez-vous à l'aide d'un compte **personnel** (alias Microsoft) ou **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="600c6-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="600c6-104">Sélectionnez **Ajouter une application** en haut de la page.</span><span class="sxs-lookup"><span data-stu-id="600c6-104">Select **Add an app** at the top of the page.</span></span>

    > [!NOTE]
    > <span data-ttu-id="600c6-105">Si vous voyez plus d'un bouton **Ajouter une application** sur la page, sélectionnez celui qui correspond à la liste **applications** convergées.</span><span class="sxs-lookup"><span data-stu-id="600c6-105">If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="600c6-106">Sur la page **inscrire votre application** , définissez le **nom** de l'application sur **Tutorial Graph Graph** et sélectionnez Create ( **créer**).</span><span class="sxs-lookup"><span data-stu-id="600c6-106">On the **Register your application** page, set the **Application Name** to **Angular Graph Tutorial** and select **Create**.</span></span>

    ![Capture d'écran de la création d'une nouvelle application dans le site Web du portail d'inscription des applications](./images/arp-create-app-01.png)

1. <span data-ttu-id="600c6-108">Sur la page **d'inscription du didacticiel de graphique angulaire** , dans la section **Propriétés** , copiez l' **ID d'application** , car vous en aurez besoin plus tard.</span><span class="sxs-lookup"><span data-stu-id="600c6-108">On the **Angular Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Capture d'écran de l'ID de l'application nouvellement créée](./images/arp-create-app-02.png)

1. <span data-ttu-id="600c6-110">Faites déFiler \*\*\*\* vers le bas jusqu'à la section plateformes.</span><span class="sxs-lookup"><span data-stu-id="600c6-110">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="600c6-111">Sélectionnez **Ajouter une plateforme**.</span><span class="sxs-lookup"><span data-stu-id="600c6-111">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="600c6-112">Dans la boîte de dialogue **Ajouter une plateforme** , sélectionnez **Web**.</span><span class="sxs-lookup"><span data-stu-id="600c6-112">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Capture d'écran création d'une plateforme pour l'application](./images/arp-create-app-03.png)

    1. <span data-ttu-id="600c6-114">Dans la zone plateforme **Web** , entrez `http://localhost:4200` pour les **URL**de redirection.</span><span class="sxs-lookup"><span data-stu-id="600c6-114">In the **Web** platform box, enter `http://localhost:4200` for the **Redirect URLs**.</span></span>

        ![Capture d'écran de la plateforme Web récemment ajoutée pour l'application](./images/arp-create-app-04.png)

1. <span data-ttu-id="600c6-116">Faites déFiler la page jusqu'en bas et sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="600c6-116">Scroll to the bottom of the page and select **Save**.</span></span>