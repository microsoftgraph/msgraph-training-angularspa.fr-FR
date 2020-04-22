<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a593f-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="a593f-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="a593f-102">Pour cette application, vous allez utiliser la bibliothèque [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour passer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="a593f-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="a593f-103">Récupérer les événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="a593f-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="a593f-104">Créez un fichier dans le `./src/app` répertoire appelé `event.ts` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="a593f-104">Create a new file in the `./src/app` directory called `event.ts` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/event.ts" id="eventClasses":::

1. <span data-ttu-id="a593f-105">Ajoutez un nouveau service qui contiendra tous vos appels Graph.</span><span class="sxs-lookup"><span data-stu-id="a593f-105">Add a new service to hold all of your Graph calls.</span></span> <span data-ttu-id="a593f-106">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="a593f-106">Run the following command in your CLI.</span></span>

    ```Shell
    ng generate service graph
    ```

    <span data-ttu-id="a593f-107">Tout comme avec le service d’authentification que vous avez créé précédemment, la création d’un service pour cela vous permet de l’injecter dans tous les composants qui ont besoin d’accéder à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="a593f-107">Just as with the authentication service you created earlier, creating a service for this allows you to inject it into any components that need access to Microsoft Graph.</span></span>

1. <span data-ttu-id="a593f-108">Une fois la commande terminée, ouvrez le `./src/app/graph.service.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a593f-108">Once the command completes, open the `./src/app/graph.service.ts` file and replace its contents with the following.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/graph.service.ts" id="graphServiceSnippet":::

    <span data-ttu-id="a593f-109">Que fait ce code ?</span><span class="sxs-lookup"><span data-stu-id="a593f-109">Consider what this code is doing.</span></span>

    - <span data-ttu-id="a593f-110">Il initialise un client Graph dans le constructeur pour le service.</span><span class="sxs-lookup"><span data-stu-id="a593f-110">It initializes a Graph client in the constructor for the service.</span></span>
    - <span data-ttu-id="a593f-111">Elle implémente une `getEvents` fonction qui utilise le client Graph de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="a593f-111">It implements a `getEvents` function that uses the Graph client in the following way:</span></span>
      - <span data-ttu-id="a593f-112">L’URL qui sera appelée est `/me/events`.</span><span class="sxs-lookup"><span data-stu-id="a593f-112">The URL that will be called is `/me/events`.</span></span>
      - <span data-ttu-id="a593f-113">La `select` méthode limite les champs renvoyés pour chaque événement à ceux que l’affichage utilise réellement.</span><span class="sxs-lookup"><span data-stu-id="a593f-113">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
      - <span data-ttu-id="a593f-114">La `orderby` méthode trie les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.</span><span class="sxs-lookup"><span data-stu-id="a593f-114">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="a593f-115">Créez un composant angulaire pour appeler cette nouvelle méthode et afficher les résultats de l’appel.</span><span class="sxs-lookup"><span data-stu-id="a593f-115">Create an Angular component to call this new method and display the results of the call.</span></span> <span data-ttu-id="a593f-116">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="a593f-116">Run the following command in your CLI.</span></span>

    ```Shell
    ng generate component calendar
    ```

1. <span data-ttu-id="a593f-117">Une fois la commande exécutée, ajoutez le composant au `routes` tableau dans `./src/app/app-routing.module.ts`.</span><span class="sxs-lookup"><span data-stu-id="a593f-117">Once the command completes, add the component to the `routes` array in `./src/app/app-routing.module.ts`.</span></span>

    ```TypeScript
    import { CalendarComponent } from './calendar/calendar.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
      { path: 'calendar', component: CalendarComponent }
    ];
    ```

1. <span data-ttu-id="a593f-118">Ouvrez le `./src/app/calendar/calendar.component.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a593f-118">Open the `./src/app/calendar/calendar.component.ts` file and replace its contents with the following.</span></span>

    ```TypeScript
    import { Component, OnInit } from '@angular/core';
    import * as moment from 'moment-timezone';

    import { GraphService } from '../graph.service';
    import { Event, DateTimeTimeZone } from '../event';
    import { AlertsService } from '../alerts.service';

    @Component({
      selector: 'app-calendar',
      templateUrl: './calendar.component.html',
      styleUrls: ['./calendar.component.css']
    })
    export class CalendarComponent implements OnInit {

      public events: Event[];

      constructor(
        private graphService: GraphService,
        private alertsService: AlertsService) { }

      ngOnInit() {
        this.graphService.getEvents()
          .then((events) => {
            this.events = events;
            // Temporary to display raw results
            this.alertsService.add('Events from Graph', JSON.stringify(events, null, 2));
          });
      }
    }
    ```

<span data-ttu-id="a593f-119">Pour le moment, cela restitue simplement le tableau d’événements dans JSON sur la page.</span><span class="sxs-lookup"><span data-stu-id="a593f-119">For now this just renders the array of events in JSON on the page.</span></span> <span data-ttu-id="a593f-120">Enregistrez vos modifications, puis redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="a593f-120">Save your changes and restart the app.</span></span> <span data-ttu-id="a593f-121">Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="a593f-121">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="a593f-122">Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a593f-122">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="a593f-123">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="a593f-123">Display the results</span></span>

<span data-ttu-id="a593f-124">À présent, vous pouvez `CalendarComponent` mettre à jour le composant pour afficher les événements de manière plus conviviale.</span><span class="sxs-lookup"><span data-stu-id="a593f-124">Now you can update the `CalendarComponent` component to display the events in a more user-friendly manner.</span></span>

1. <span data-ttu-id="a593f-125">Supprimez le code temporaire qui ajoute une alerte à `ngOnInit` partir de la fonction.</span><span class="sxs-lookup"><span data-stu-id="a593f-125">Remove the temporary code that adds an alert from the `ngOnInit` function.</span></span> <span data-ttu-id="a593f-126">Votre fonction mise à jour doit ressembler à ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a593f-126">Your updated function should look like this.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="ngOnInitSnippet":::

1. <span data-ttu-id="a593f-127">Ajoutez une fonction à la `CalendarComponent` classe pour mettre en `DateTimeTimeZone` forme un objet dans une chaîne ISO.</span><span class="sxs-lookup"><span data-stu-id="a593f-127">Add a function to the `CalendarComponent` class to format a `DateTimeTimeZone` object into an ISO string.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="formatDateTimeTimeZoneSnippet":::

1. <span data-ttu-id="a593f-128">Ouvrez le `./src/app/calendar/calendar.component.html` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="a593f-128">Open the `./src/app/calendar/calendar.component.html` file and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/calendar/calendar.component.html" id="calendarHtml":::

<span data-ttu-id="a593f-129">Cette méthode effectue une boucle dans la collection d’événements et ajoute une ligne de tableau pour chacun d’eux.</span><span class="sxs-lookup"><span data-stu-id="a593f-129">This loops through the collection of events and adds a table row for each one.</span></span> <span data-ttu-id="a593f-130">Enregistrez les modifications et redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="a593f-130">Save the changes and restart the app.</span></span> <span data-ttu-id="a593f-131">Cliquez sur le lien **calendrier** et l’application doit maintenant afficher un tableau d’événements.</span><span class="sxs-lookup"><span data-stu-id="a593f-131">Click on the **Calendar** link and the app should now render a table of events.</span></span>

![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
