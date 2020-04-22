<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b2f34-101">Ce didacticiel vous apprend à créer une application de page unique angulaire qui utilise Microsoft Graph pour récupérer des informations de calendrier pour un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b2f34-101">This tutorial teaches you how to build an Angular single-page app that uses Microsoft Graph to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="b2f34-102">Si vous préférez télécharger simplement le didacticiel terminé, vous pouvez télécharger ou cloner le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-angularspa).</span><span class="sxs-lookup"><span data-stu-id="b2f34-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-angularspa).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b2f34-103">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="b2f34-103">Prerequisites</span></span>

<span data-ttu-id="b2f34-104">Avant de commencer ce didacticiel, [node. js](https://nodejs.org) doit être installé sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="b2f34-104">Before you start this tutorial, you should have [Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="b2f34-105">Si vous n’avez pas node. js, consultez le lien précédent pour obtenir les options de téléchargement.</span><span class="sxs-lookup"><span data-stu-id="b2f34-105">If you do not have Node.js, visit the previous link for download options.</span></span>

<span data-ttu-id="b2f34-106">Vous devez également disposer d’un compte Microsoft personnel disposant d’une boîte aux lettres sur Outlook.com ou d’un compte professionnel ou scolaire Microsoft.</span><span class="sxs-lookup"><span data-stu-id="b2f34-106">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="b2f34-107">Si vous n’avez pas de compte Microsoft, vous disposez de deux options pour obtenir un compte gratuit :</span><span class="sxs-lookup"><span data-stu-id="b2f34-107">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="b2f34-108">Vous pouvez vous [inscrire pour obtenir un nouveau compte Microsoft personnel](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="b2f34-108">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="b2f34-109">Vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.</span><span class="sxs-lookup"><span data-stu-id="b2f34-109">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="b2f34-110">Ce didacticiel a été écrit avec le nœud version 12.16.1.</span><span class="sxs-lookup"><span data-stu-id="b2f34-110">This tutorial was written with Node version 12.16.1.</span></span> <span data-ttu-id="b2f34-111">Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.</span><span class="sxs-lookup"><span data-stu-id="b2f34-111">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="watch-the-tutorial"></a><span data-ttu-id="b2f34-112">Regarder le didacticiel</span><span class="sxs-lookup"><span data-stu-id="b2f34-112">Watch the tutorial</span></span>

<span data-ttu-id="b2f34-113">Ce module a été enregistré et est disponible dans le canal YouTube de développement Office.</span><span class="sxs-lookup"><span data-stu-id="b2f34-113">This module has been recorded and is available in the Office Development YouTube channel.</span></span>

<!-- markdownlint-disable MD033 MD034 -->
<br/>

> [!VIDEO https://www.youtube-nocookie.com/embed/KUPRTTOUzz8]
<!-- markdownlint-enable MD033 MD034 -->

## <a name="feedback"></a><span data-ttu-id="b2f34-114">Commentaires</span><span class="sxs-lookup"><span data-stu-id="b2f34-114">Feedback</span></span>

<span data-ttu-id="b2f34-115">Veuillez fournir des commentaires sur ce didacticiel dans le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-angularspa).</span><span class="sxs-lookup"><span data-stu-id="b2f34-115">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-angularspa).</span></span>
