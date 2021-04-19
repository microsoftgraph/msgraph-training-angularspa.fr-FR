<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="37da9-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l'application.</span><span class="sxs-lookup"><span data-stu-id="37da9-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="37da9-102">Pour cette application, vous allez utiliser la bibliothèque [cliente microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="37da9-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="37da9-103">Récupérer les événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="37da9-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="37da9-104">Ajoutez un nouveau service pour contenir tous vos appels Graph.</span><span class="sxs-lookup"><span data-stu-id="37da9-104">Add a new service to hold all of your Graph calls.</span></span> <span data-ttu-id="37da9-105">Exécutez la commande suivante dans votre CLI.</span><span class="sxs-lookup"><span data-stu-id="37da9-105">Run the following command in your CLI.</span></span>

    ```Shell
    ng generate service graph
    ```

    <span data-ttu-id="37da9-106">Tout comme pour le service d'authentification que vous avez créé précédemment, la création d'un service vous permet de l'injecter dans tous les composants qui ont besoin d'accéder à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="37da9-106">Just as with the authentication service you created earlier, creating a service for this allows you to inject it into any components that need access to Microsoft Graph.</span></span>

1. <span data-ttu-id="37da9-107">Une fois la commande terminée, ouvrez **./src/app/graph.service.ts** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="37da9-107">Once the command completes, open **./src/app/graph.service.ts** and replace its contents with the following.</span></span>

    ```typescript
    import { Injectable } from '@angular/core';
    import { Client } from '@microsoft/microsoft-graph-client';
    import * as MicrosoftGraph from '@microsoft/microsoft-graph-types';

    import { AuthService } from './auth.service';
    import { AlertsService } from './alerts.service';

    @Injectable({
      providedIn: 'root'
    })

    export class GraphService {

      private graphClient: Client;
      constructor(
        private authService: AuthService,
        private alertsService: AlertsService) {

        // Initialize the Graph client
        this.graphClient = Client.init({
          authProvider: async (done) => {
            // Get the token from the auth service
            const token = await this.authService.getAccessToken()
              .catch((reason) => {
                done(reason, null);
              });

            if (token)
            {
              done(null, token);
            } else {
              done("Could not get an access token", null);
            }
          }
        });
      }

      async getCalendarView(start: string, end: string, timeZone: string): Promise<MicrosoftGraph.Event[] | undefined> {
        try {
          // GET /me/calendarview?startDateTime=''&endDateTime=''
          // &$select=subject,organizer,start,end
          // &$orderby=start/dateTime
          // &$top=50
          const result =  await this.graphClient
            .api('/me/calendarview')
            .header('Prefer', `outlook.timezone="${timeZone}"`)
            .query({
              startDateTime: start,
              endDateTime: end
            })
            .select('subject,organizer,start,end')
            .orderby('start/dateTime')
            .top(50)
            .get();

          return result.value;
        } catch (error) {
          this.alertsService.addError('Could not get events', JSON.stringify(error, null, 2));
        }
        return undefined;
      }
    }
    ```

    <span data-ttu-id="37da9-108">Que fait ce code ?</span><span class="sxs-lookup"><span data-stu-id="37da9-108">Consider what this code is doing.</span></span>

    - <span data-ttu-id="37da9-109">Il initialise un client Graph dans le constructeur du service.</span><span class="sxs-lookup"><span data-stu-id="37da9-109">It initializes a Graph client in the constructor for the service.</span></span>
    - <span data-ttu-id="37da9-110">Il implémente `getCalendarView` une fonction qui utilise le client Graph de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="37da9-110">It implements a `getCalendarView` function that uses the Graph client in the following way:</span></span>
      - <span data-ttu-id="37da9-111">L’URL qui sera appelée est `/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="37da9-111">The URL that will be called is `/me/calendarview`.</span></span>
      - <span data-ttu-id="37da9-112">La `header` méthode inclut l'en-tête, qui place les heures de début et de fin des événements renvoyés dans le fuseau horaire préféré de `Prefer: outlook.timezone` l'utilisateur.</span><span class="sxs-lookup"><span data-stu-id="37da9-112">The `header` method includes the `Prefer: outlook.timezone` header, which causes the start and end times of the returned events to be in the user's preferred time zone.</span></span>
      - <span data-ttu-id="37da9-113">La méthode ajoute les paramètres et définit la fenêtre de temps `query` `startDateTime` pour `endDateTime` l'affichage Calendrier.</span><span class="sxs-lookup"><span data-stu-id="37da9-113">The `query` method adds the `startDateTime` and `endDateTime` parameters, defining the window of time for the calendar view.</span></span>
      - <span data-ttu-id="37da9-114">La méthode limite les champs renvoyés pour chaque événement à ceux que `select` l'affichage utilisera réellement.</span><span class="sxs-lookup"><span data-stu-id="37da9-114">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
      - <span data-ttu-id="37da9-115">La `orderby` méthode trie les résultats par heure de début.</span><span class="sxs-lookup"><span data-stu-id="37da9-115">The `orderby` method sorts the results by start time.</span></span>

1. <span data-ttu-id="37da9-116">Créez un composant Angular pour appeler cette nouvelle méthode et afficher les résultats de l'appel.</span><span class="sxs-lookup"><span data-stu-id="37da9-116">Create an Angular component to call this new method and display the results of the call.</span></span> <span data-ttu-id="37da9-117">Exécutez la commande suivante dans votre CLI.</span><span class="sxs-lookup"><span data-stu-id="37da9-117">Run the following command in your CLI.</span></span>

    ```Shell
    ng generate component calendar
    ```

1. <span data-ttu-id="37da9-118">Une fois la commande terminée, ajoutez le composant au tableau dans `routes` **./src/app/app-routing.module.ts**.</span><span class="sxs-lookup"><span data-stu-id="37da9-118">Once the command completes, add the component to the `routes` array in **./src/app/app-routing.module.ts**.</span></span>

    ```typescript
    import { CalendarComponent } from './calendar/calendar.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
      { path: 'calendar', component: CalendarComponent },
    ];
    ```

1. <span data-ttu-id="37da9-119">Ouvrez **./tsconfig.jset** ajoutez la propriété suivante à l'objet. `compilerOptions`</span><span class="sxs-lookup"><span data-stu-id="37da9-119">Open **./tsconfig.json** and add the following property to the `compilerOptions` object.</span></span>

    ```json
    "resolveJsonModule": true
    ```

1. <span data-ttu-id="37da9-120">Ouvrez **./src/app/calendar/calendar.component.ts** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="37da9-120">Open **./src/app/calendar/calendar.component.ts** and replace its contents with the following.</span></span>

    ```typescript
    import { Component, OnInit } from '@angular/core';
    import * as moment from 'moment-timezone';
    import { findIana } from 'windows-iana';
    import * as MicrosoftGraph from '@microsoft/microsoft-graph-types';

    import { AuthService } from '../auth.service';
    import { GraphService } from '../graph.service';
    import { AlertsService } from '../alerts.service';

    @Component({
      selector: 'app-calendar',
      templateUrl: './calendar.component.html',
      styleUrls: ['./calendar.component.css']
    })
    export class CalendarComponent implements OnInit {

      public events?: MicrosoftGraph.Event[];

      constructor(
        private authService: AuthService,
        private graphService: GraphService,
        private alertsService: AlertsService) { }

      ngOnInit() {
        // Convert the user's timezone to IANA format
        const ianaName = findIana(this.authService.user?.timeZone ?? 'UTC');
        const timeZone = ianaName![0].valueOf() || this.authService.user?.timeZone || 'UTC';

        // Get midnight on the start of the current week in the user's timezone,
        // but in UTC. For example, for Pacific Standard Time, the time value would be
        // 07:00:00Z
        var startOfWeek = moment.tz(timeZone).startOf('week').utc();
        var endOfWeek = moment(startOfWeek).add(7, 'day');

        this.graphService.getCalendarView(
          startOfWeek.format(),
          endOfWeek.format(),
          this.authService.user?.timeZone ?? 'UTC')
            .then((events) => {
              this.events = events;
              // Temporary to display raw results
              this.alertsService.addSuccess('Events from Graph', JSON.stringify(events, null, 2));
            });
      }
    }
    ```

<span data-ttu-id="37da9-121">Pour l'instant, cela restituera simplement le tableau des événements dans JSON sur la page.</span><span class="sxs-lookup"><span data-stu-id="37da9-121">For now this just renders the array of events in JSON on the page.</span></span> <span data-ttu-id="37da9-122">Enregistrez vos modifications, puis redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="37da9-122">Save your changes and restart the app.</span></span> <span data-ttu-id="37da9-123">Connectez-vous et cliquez **sur le lien** Calendrier dans la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="37da9-123">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="37da9-124">Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="37da9-124">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="37da9-125">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="37da9-125">Display the results</span></span>

<span data-ttu-id="37da9-126">Vous pouvez maintenant mettre à jour le composant pour afficher les événements `CalendarComponent` de manière plus conviviale.</span><span class="sxs-lookup"><span data-stu-id="37da9-126">Now you can update the `CalendarComponent` component to display the events in a more user-friendly manner.</span></span>

1. <span data-ttu-id="37da9-127">Supprimez le code temporaire qui ajoute une alerte à partir de la `ngOnInit` fonction.</span><span class="sxs-lookup"><span data-stu-id="37da9-127">Remove the temporary code that adds an alert from the `ngOnInit` function.</span></span> <span data-ttu-id="37da9-128">Votre fonction mise à jour doit ressembler à ceci.</span><span class="sxs-lookup"><span data-stu-id="37da9-128">Your updated function should look like this.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="ngOnInitSnippet":::

1. <span data-ttu-id="37da9-129">Ajoutez une fonction à la `CalendarComponent` classe pour mettre en forme un objet dans une chaîne `DateTimeTimeZone` ISO.</span><span class="sxs-lookup"><span data-stu-id="37da9-129">Add a function to the `CalendarComponent` class to format a `DateTimeTimeZone` object into an ISO string.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="formatDateTimeTimeZoneSnippet":::

1. <span data-ttu-id="37da9-130">Ouvrez **./src/app/calendar/calendar.component.html** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="37da9-130">Open **./src/app/calendar/calendar.component.html** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/calendar/calendar.component.html" id="calendarHtml":::

<span data-ttu-id="37da9-131">Cette opération pare la collection d'événements et ajoute une ligne de tableau pour chacun d'eux.</span><span class="sxs-lookup"><span data-stu-id="37da9-131">This loops through the collection of events and adds a table row for each one.</span></span> <span data-ttu-id="37da9-132">Enregistrez les modifications et redémarrez l'application.</span><span class="sxs-lookup"><span data-stu-id="37da9-132">Save the changes and restart the app.</span></span> <span data-ttu-id="37da9-133">Cliquez sur le **lien Calendrier** et l'application doit maintenant restituer une table des événements.</span><span class="sxs-lookup"><span data-stu-id="37da9-133">Click on the **Calendar** link and the app should now render a table of events.</span></span>

![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
