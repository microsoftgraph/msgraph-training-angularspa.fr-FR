<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d2350-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l'application.</span><span class="sxs-lookup"><span data-stu-id="d2350-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="d2350-102">Pour cette application, vous allez utiliser la bibliothèque [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour passer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="d2350-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="d2350-103">Obtenir des événements de calendrier à partir d'Outlook</span><span class="sxs-lookup"><span data-stu-id="d2350-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="d2350-104">Commencez par créer une `Event` classe qui définit les champs que l'application affichera.</span><span class="sxs-lookup"><span data-stu-id="d2350-104">Start by creating an `Event` class that defines the fields that the app will display.</span></span> <span data-ttu-id="d2350-105">Créez un fichier dans le `./src/app` répertoire appelé `event.ts` et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="d2350-105">Create a new file in the `./src/app` directory called `event.ts` and add the following code.</span></span>

```TypeScript
// For a full list of fields, see
// https://docs.microsoft.com/graph/api/resources/event?view=graph-rest-1.0
export class Event {
  subject: string;
  organizer: Recipient;
  start: DateTimeTimeZone;
  end: DateTimeTimeZone;
}

// https://docs.microsoft.com/graph/api/resources/recipient?view=graph-rest-1.0
export class Recipient {
  emailAddress: EmailAddress;
}

// https://docs.microsoft.com/graph/api/resources/emailaddress?view=graph-rest-1.0
export class EmailAddress {
  name: string;
  address: string;
}

// https://docs.microsoft.com/graph/api/resources/datetimetimezone?view=graph-rest-1.0
export class DateTimeTimeZone {
  dateTime: string;
  timeZone: string;
}
```

<span data-ttu-id="d2350-106">Ensuite, ajoutez un nouveau service qui contiendra tous vos appels Graph.</span><span class="sxs-lookup"><span data-stu-id="d2350-106">Next, add a new service to hold all of your Graph calls.</span></span> <span data-ttu-id="d2350-107">Tout comme avec le service d'authentification que vous avez créé précédemment, la création d'un service pour cela vous permet de l'injecter dans tous les composants qui ont besoin d'accéder à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="d2350-107">Just as with the authentication service you created earlier, creating a service for this allows you to inject it into any components that need access to Microsoft Graph.</span></span> <span data-ttu-id="d2350-108">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="d2350-108">Run the following command in your CLI.</span></span>

```Shell
ng generate service graph
```

<span data-ttu-id="d2350-109">Une fois la commande terminée, ouvrez le `./src/app/graph.service.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="d2350-109">Once the command completes, open the `./src/app/graph.service.ts` file and replace its contents with the following.</span></span>

```TypeScript
import { Injectable } from '@angular/core';
import { Client } from '@microsoft/microsoft-graph-client';

import { AuthService } from './auth.service';
import { Event } from './event';
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

  async getEvents(): Promise<Event[]> {
    try {
      let result =  await this.graphClient
        .api('/me/events')
        .select('subject,organizer,start,end')
        .orderby('createdDateTime DESC')
        .get();

      return result.value;
    } catch (error) {
      this.alertsService.add('Could not get events', JSON.stringify(error, null, 2));
    }
  }
}
```

<span data-ttu-id="d2350-110">Examinez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="d2350-110">Consider what this code is doing.</span></span>

- <span data-ttu-id="d2350-111">Il initialise un client Graph dans le constructeur pour le service.</span><span class="sxs-lookup"><span data-stu-id="d2350-111">It initializes a Graph client in the constructor for the service.</span></span>
- <span data-ttu-id="d2350-112">Elle implémente une `getEvents` fonction qui utilise le client Graph de la manière suivante:</span><span class="sxs-lookup"><span data-stu-id="d2350-112">It implements a `getEvents` function that uses the Graph client in the following way:</span></span>
  - <span data-ttu-id="d2350-113">L'URL qui sera appelée est `/me/events`.</span><span class="sxs-lookup"><span data-stu-id="d2350-113">The URL that will be called is `/me/events`.</span></span>
  - <span data-ttu-id="d2350-114">La `select` méthode limite les champs renvoyés pour chaque événement à ceux que l'affichage utilise réellement.</span><span class="sxs-lookup"><span data-stu-id="d2350-114">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
  - <span data-ttu-id="d2350-115">La `orderby` méthode trie les résultats en fonction de la date et de l'heure de leur création, avec l'élément le plus récent en premier.</span><span class="sxs-lookup"><span data-stu-id="d2350-115">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="d2350-116">À présent, créez un composant angulaire pour appeler cette nouvelle méthode et afficher les résultats de l'appel.</span><span class="sxs-lookup"><span data-stu-id="d2350-116">Now create an Angular component to call this new method and display the results of the call.</span></span> <span data-ttu-id="d2350-117">Exécutez la commande suivante dans votre interface CLI.</span><span class="sxs-lookup"><span data-stu-id="d2350-117">Run the following command in your CLI.</span></span>

```Shell
ng generate component calendar
```

<span data-ttu-id="d2350-118">Une fois la commande exécutée, ajoutez le composant au `routes` tableau dans `./src/app/app-routing.module.ts`.</span><span class="sxs-lookup"><span data-stu-id="d2350-118">Once the command completes, add the component to the `routes` array in `./src/app/app-routing.module.ts`.</span></span>

```TypeScript
import { CalendarComponent } from './calendar/calendar.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'calendar', component: CalendarComponent }
];
```

<span data-ttu-id="d2350-119">Ouvrez le `./src/app/calendar/calendar.component.ts` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="d2350-119">Open the `./src/app/calendar/calendar.component.ts` file and replace its contents with the following.</span></span>

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

  private events: Event[];

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

<span data-ttu-id="d2350-120">Pour le moment, cela restitue simplement le tableau d'événements dans JSON sur la page.</span><span class="sxs-lookup"><span data-stu-id="d2350-120">For now this just renders the array of events in JSON on the page.</span></span> <span data-ttu-id="d2350-121">Enregistrez vos modifications, puis redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="d2350-121">Save your changes and restart the app.</span></span> <span data-ttu-id="d2350-122">Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="d2350-122">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="d2350-123">Si tout fonctionne, vous devez voir un vidage JSON des événements sur le calendrier de l'utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d2350-123">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="d2350-124">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="d2350-124">Display the results</span></span>

<span data-ttu-id="d2350-125">À présent, vous pouvez `CalendarComponent` mettre à jour le composant pour afficher les événements de manière plus conviviale.</span><span class="sxs-lookup"><span data-stu-id="d2350-125">Now you can update the `CalendarComponent` component to display the events in a more user-friendly manner.</span></span> <span data-ttu-id="d2350-126">Tout d'abord, supprimez le code temporaire qui ajoute une `ngOnInit` alerte à partir de la fonction.</span><span class="sxs-lookup"><span data-stu-id="d2350-126">First, remove the temporary code that adds an alert from the `ngOnInit` function.</span></span> <span data-ttu-id="d2350-127">Votre fonction mise à jour doit ressembler à ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="d2350-127">Your updated function should look like this.</span></span>

```TypeScript
ngOnInit() {
  this.graphService.getEvents()
    .then((events) => {
      this.events = events;
    });
}
```

<span data-ttu-id="d2350-128">À présent, ajoutez une fonction `CalendarComponent` à la classe pour `DateTimeTimeZone` mettre en forme un objet dans une chaîne ISO.</span><span class="sxs-lookup"><span data-stu-id="d2350-128">Now add a function to the `CalendarComponent` class to format a `DateTimeTimeZone` object into an ISO string.</span></span>

```TypeScript
formatDateTimeTimeZone(dateTime: DateTimeTimeZone): string {
  try {
    return moment.tz(dateTime.dateTime, dateTime.timeZone).format();
  }
  catch(error) {
    this.alertsService.add('DateTimeTimeZone conversion error', JSON.stringify(error));
  }
}
```

<span data-ttu-id="d2350-129">Enfin, ouvrez le `./src/app/calendar/calendar.component.html` fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="d2350-129">Finally, open the `./src/app/calendar/calendar.component.html` file and replace its contents with the following.</span></span>

```html
<h1>Calendar</h1>
<table class="table">
  <thead>
    <th scope="col">Organizer</th>
    <th scope="col">Subject</th>
    <th scope="col">Start</th>
    <th scope="col">End</th>
  </thead>
  <tbody>
    <tr *ngFor="let event of events">
      <td>{{event.organizer.emailAddress.name}}</td>
      <td>{{event.subject}}</td>
      <td>{{formatDateTimeTimeZone(event.start) | date:'short' }}</td>
      <td>{{formatDateTimeTimeZone(event.end) | date: 'short' }}</td>
    </tr>
  </tbody>
</table>
```

<span data-ttu-id="d2350-130">Cette méthode effectue une boucle dans la collection d'événements et ajoute une ligne de tableau pour chacun d'eux.</span><span class="sxs-lookup"><span data-stu-id="d2350-130">This loops through the collection of events and adds a table row for each one.</span></span> <span data-ttu-id="d2350-131">Enregistrez les modifications et redémarrez l'application.</span><span class="sxs-lookup"><span data-stu-id="d2350-131">Save the changes and restart the app.</span></span> <span data-ttu-id="d2350-132">Cliquez sur le lien **calendrier** et l'application doit maintenant afficher un tableau d'événements.</span><span class="sxs-lookup"><span data-stu-id="d2350-132">Click on the **Calendar** link and the app should now render a table of events.</span></span>

![Capture d'écran du tableau des événements](./images/add-msgraph-01.png)