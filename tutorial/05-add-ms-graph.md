<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="fa0ca-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="fa0ca-102">Pour cette application, vous allez utiliser la bibliothèque [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour passer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="fa0ca-103">Récupérer les événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="fa0ca-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="fa0ca-104">Ajoutez un nouveau service qui contiendra tous vos appels Graph.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-104">Add a new service to hold all of your Graph calls.</span></span> <span data-ttu-id="fa0ca-105">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-105">Run the following command in your CLI.</span></span>

    ```Shell
    ng generate service graph
    ```

    <span data-ttu-id="fa0ca-106">Tout comme avec le service d’authentification que vous avez créé précédemment, la création d’un service pour cela vous permet de l’injecter dans tous les composants qui ont besoin d’accéder à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-106">Just as with the authentication service you created earlier, creating a service for this allows you to inject it into any components that need access to Microsoft Graph.</span></span>

1. <span data-ttu-id="fa0ca-107">Une fois la commande terminée, ouvrez **/src/App/Graph.service.TS** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-107">Once the command completes, open **./src/app/graph.service.ts** and replace its contents with the following.</span></span>

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
            let token = await this.authService.getAccessToken()
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

      async getCalendarView(start: string, end: string, timeZone: string): Promise<MicrosoftGraph.Event[]> {
        try {
          // GET /me/calendarview?startDateTime=''&endDateTime=''
          // &$select=subject,organizer,start,end
          // &$orderby=start/dateTime
          // &$top=50
          let result =  await this.graphClient
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
      }
    }
    ```

    <span data-ttu-id="fa0ca-108">Que fait ce code ?</span><span class="sxs-lookup"><span data-stu-id="fa0ca-108">Consider what this code is doing.</span></span>

    - <span data-ttu-id="fa0ca-109">Il initialise un client Graph dans le constructeur pour le service.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-109">It initializes a Graph client in the constructor for the service.</span></span>
    - <span data-ttu-id="fa0ca-110">Elle implémente une `getCalendarView` fonction qui utilise le client Graph de la manière suivante :</span><span class="sxs-lookup"><span data-stu-id="fa0ca-110">It implements a `getCalendarView` function that uses the Graph client in the following way:</span></span>
      - <span data-ttu-id="fa0ca-111">L’URL qui sera appelée est `/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-111">The URL that will be called is `/me/calendarview`.</span></span>
      - <span data-ttu-id="fa0ca-112">La `header` méthode inclut l' `Prefer: outlook.timezone` en-tête, ce qui entraîne des heures de début et de fin des événements renvoyés dans le fuseau horaire préféré de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-112">The `header` method includes the `Prefer: outlook.timezone` header, which causes the start and end times of the returned events to be in the user's preferred time zone.</span></span>
      - <span data-ttu-id="fa0ca-113">La `query` méthode ajoute les `startDateTime` `endDateTime` paramètres et, définissant la fenêtre de temps pour l’affichage Calendrier.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-113">The `query` method adds the `startDateTime` and `endDateTime` parameters, defining the window of time for the calendar view.</span></span>
      - <span data-ttu-id="fa0ca-114">La `select` méthode limite les champs renvoyés pour chaque événement à ceux que l’affichage utilise réellement.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-114">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
      - <span data-ttu-id="fa0ca-115">La `orderby` méthode trie les résultats par heure de début.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-115">The `orderby` method sorts the results by start time.</span></span>

1. <span data-ttu-id="fa0ca-116">Créez un composant angulaire pour appeler cette nouvelle méthode et afficher les résultats de l’appel.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-116">Create an Angular component to call this new method and display the results of the call.</span></span> <span data-ttu-id="fa0ca-117">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-117">Run the following command in your CLI.</span></span>

    ```Shell
    ng generate component calendar
    ```

1. <span data-ttu-id="fa0ca-118">Une fois la commande exécutée, ajoutez le composant au `routes` tableau dans **./SRC/app/app-Routing.module.TS**.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-118">Once the command completes, add the component to the `routes` array in **./src/app/app-routing.module.ts**.</span></span>

    ```typescript
    import { CalendarComponent } from './calendar/calendar.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
      { path: 'calendar', component: CalendarComponent }
    ];
    ```

1. <span data-ttu-id="fa0ca-119">Ouvrez **./SRC/App/Calendar/Calendar.Component.TS** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-119">Open **./src/app/calendar/calendar.component.ts** and replace its contents with the following.</span></span>

    ```typescript
    import { Component, OnInit } from '@angular/core';
    import * as moment from 'moment-timezone';
    import { findOneIana } from 'windows-iana';
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

      public events: MicrosoftGraph.Event[];

      constructor(
        private authService: AuthService,
        private graphService: GraphService,
        private alertsService: AlertsService) { }

      ngOnInit() {
        // Convert the user's timezone to IANA format
        const ianaName = findOneIana(this.authService.user.timeZone);
        const timeZone = ianaName!.valueOf() || this.authService.user.timeZone;

        // Get midnight on the start of the current week in the user's timezone,
        // but in UTC. For example, for Pacific Standard Time, the time value would be
        // 07:00:00Z
        var startOfWeek = moment.tz(timeZone).startOf('week').utc();
        var endOfWeek = moment(startOfWeek).add(7, 'day');

        this.graphService.getCalendarView(
          startOfWeek.format(),
          endOfWeek.format(),
          this.authService.user.timeZone)
            .then((events) => {
              this.events = events;
              // Temporary to display raw results
              this.alertsService.addSuccess('Events from Graph', JSON.stringify(events, null, 2));
            });
      }
    }
    ```

<span data-ttu-id="fa0ca-120">Pour le moment, cela restitue simplement le tableau d’événements dans JSON sur la page.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-120">For now this just renders the array of events in JSON on the page.</span></span> <span data-ttu-id="fa0ca-121">Enregistrez vos modifications, puis redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-121">Save your changes and restart the app.</span></span> <span data-ttu-id="fa0ca-122">Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-122">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="fa0ca-123">Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-123">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="fa0ca-124">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="fa0ca-124">Display the results</span></span>

<span data-ttu-id="fa0ca-125">À présent, vous pouvez mettre à jour le `CalendarComponent` composant pour afficher les événements de manière plus conviviale.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-125">Now you can update the `CalendarComponent` component to display the events in a more user-friendly manner.</span></span>

1. <span data-ttu-id="fa0ca-126">Supprimez le code temporaire qui ajoute une alerte à partir de la `ngOnInit` fonction.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-126">Remove the temporary code that adds an alert from the `ngOnInit` function.</span></span> <span data-ttu-id="fa0ca-127">Votre fonction mise à jour doit ressembler à ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-127">Your updated function should look like this.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="ngOnInitSnippet":::

1. <span data-ttu-id="fa0ca-128">Ajoutez une fonction à la `CalendarComponent` classe pour mettre en forme un `DateTimeTimeZone` objet dans une chaîne ISO.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-128">Add a function to the `CalendarComponent` class to format a `DateTimeTimeZone` object into an ISO string.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="formatDateTimeTimeZoneSnippet":::

1. <span data-ttu-id="fa0ca-129">Ouvrez **/src/app/calendar/calendar.component.html** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-129">Open **./src/app/calendar/calendar.component.html** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/src/app/calendar/calendar.component.html" id="calendarHtml":::

<span data-ttu-id="fa0ca-130">Cette méthode effectue une boucle dans la collection d’événements et ajoute une ligne de tableau pour chacun d’eux.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-130">This loops through the collection of events and adds a table row for each one.</span></span> <span data-ttu-id="fa0ca-131">Enregistrez les modifications et redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-131">Save the changes and restart the app.</span></span> <span data-ttu-id="fa0ca-132">Cliquez sur le lien **calendrier** et l’application doit maintenant afficher un tableau d’événements.</span><span class="sxs-lookup"><span data-stu-id="fa0ca-132">Click on the **Calendar** link and the app should now render a table of events.</span></span>

![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
