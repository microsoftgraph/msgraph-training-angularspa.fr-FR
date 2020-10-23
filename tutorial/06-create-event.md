<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="391ea-101">Dans cette section, vous allez ajouter la possibilité de créer des événements dans le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="391ea-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="391ea-102">Ouvrez **./SRC/App/Graph.service.TS** et ajoutez la fonction suivante à la `GraphService` classe.</span><span class="sxs-lookup"><span data-stu-id="391ea-102">Open **./src/app/graph.service.ts** and add the following function to the `GraphService` class.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/graph.service.ts" id="AddEventSnippet":::

## <a name="create-a-new-event-form"></a><span data-ttu-id="391ea-103">Créer un formulaire d’événement</span><span class="sxs-lookup"><span data-stu-id="391ea-103">Create a new event form</span></span>

1. <span data-ttu-id="391ea-104">Créer un composant angulaire pour afficher un formulaire et appeler cette nouvelle fonction.</span><span class="sxs-lookup"><span data-stu-id="391ea-104">Create an Angular component to display a form and call this new function.</span></span> <span data-ttu-id="391ea-105">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="391ea-105">Run the following command in your CLI.</span></span>

    ```Shell
    ng generate component new-event
    ```

1. <span data-ttu-id="391ea-106">Une fois la commande exécutée, ajoutez le composant au `routes` tableau dans **./SRC/app/app-Routing.module.TS**.</span><span class="sxs-lookup"><span data-stu-id="391ea-106">Once the command completes, add the component to the `routes` array in **./src/app/app-routing.module.ts**.</span></span>

    ```typescript
    import { NewEventComponent } from './new-event/new-event.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
      { path: 'calendar', component: CalendarComponent },
      { path: 'newevent', component: NewEventComponent },
    ];
    ```

1. <span data-ttu-id="391ea-107">Créez un fichier dans le répertoire **./SRC/app/New-Event** nommé **New-Event. TS** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="391ea-107">Create a new file in the **./src/app/new-event** directory named **new-event.ts** and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/new-event/new-event.ts" id="NewEventSnippet":::

    <span data-ttu-id="391ea-108">Cette classe servira de modèle pour le nouveau formulaire d’événement.</span><span class="sxs-lookup"><span data-stu-id="391ea-108">This class will serve as the model for the new event form.</span></span>

1. <span data-ttu-id="391ea-109">Ouvrez **./SRC/app/New-Event/New-Event.Component.TS** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="391ea-109">Open **./src/app/new-event/new-event.component.ts** and replace its contents with the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/new-event/new-event.component.ts" id="NewEventComponentSnippet":::

1. <span data-ttu-id="391ea-110">Ouvrez **/src/app/new-event/new-event.component.html** et remplacez son contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="391ea-110">Open **./src/app/new-event/new-event.component.html** and replace its contents with the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/new-event/new-event.component.html" id="NewEventFormSnippet":::

1. <span data-ttu-id="391ea-111">Enregistrer les modifications et actualiser l’application.</span><span class="sxs-lookup"><span data-stu-id="391ea-111">Save the changes and refresh the app.</span></span> <span data-ttu-id="391ea-112">Sélectionnez le bouton **nouvel événement** sur la page calendrier, puis utilisez le formulaire pour créer un événement dans le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="391ea-112">Select the **New event** button on the calendar page, then use the form to create an event on the user's calendar.</span></span>

    ![Capture d’écran du nouveau formulaire d’événement](images/create-event.png)
