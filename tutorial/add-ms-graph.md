<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l'application. Pour cette application, vous allez utiliser la bibliothèque [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour passer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obtenir des événements de calendrier à partir d'Outlook

Commencez par créer une `Event` classe qui définit les champs que l'application affichera. Créez un fichier dans le `./src/app` répertoire appelé `event.ts` et ajoutez le code suivant.

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

Ensuite, ajoutez un nouveau service qui contiendra tous vos appels Graph. Tout comme avec le service d'authentification que vous avez créé précédemment, la création d'un service pour cela vous permet de l'injecter dans tous les composants qui ont besoin d'accéder à Microsoft Graph. Exécutez la commande suivante dans votre interface CLI.

```Shell
ng generate service graph
```

Une fois la commande terminée, ouvrez le `./src/app/graph.service.ts` fichier et remplacez son contenu par ce qui suit.

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

Examinez ce que fait ce code.

- Il initialise un client Graph dans le constructeur pour le service.
- Elle implémente une `getEvents` fonction qui utilise le client Graph de la manière suivante:
  - L'URL qui sera appelée est `/me/events`.
  - La `select` méthode limite les champs renvoyés pour chaque événement à ceux que l'affichage utilise réellement.
  - La `orderby` méthode trie les résultats en fonction de la date et de l'heure de leur création, avec l'élément le plus récent en premier.

À présent, créez un composant angulaire pour appeler cette nouvelle méthode et afficher les résultats de l'appel. Exécutez la commande suivante dans votre interface CLI.

```Shell
ng generate component calendar
```

Une fois la commande exécutée, ajoutez le composant au `routes` tableau dans `./src/app/app-routing.module.ts`.

```TypeScript
import { CalendarComponent } from './calendar/calendar.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'calendar', component: CalendarComponent }
];
```

Ouvrez le `./src/app/calendar/calendar.component.ts` fichier et remplacez son contenu par ce qui suit.

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

Pour le moment, cela restitue simplement le tableau d'événements dans JSON sur la page. Enregistrez vos modifications, puis redémarrez l’application. Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation. Si tout fonctionne, vous devez voir un vidage JSON des événements sur le calendrier de l'utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

À présent, vous pouvez `CalendarComponent` mettre à jour le composant pour afficher les événements de manière plus conviviale. Tout d'abord, supprimez le code temporaire qui ajoute une `ngOnInit` alerte à partir de la fonction. Votre fonction mise à jour doit ressembler à ce qui suit.

```TypeScript
ngOnInit() {
  this.graphService.getEvents()
    .then((events) => {
      this.events = events;
    });
}
```

À présent, ajoutez une fonction `CalendarComponent` à la classe pour `DateTimeTimeZone` mettre en forme un objet dans une chaîne ISO.

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

Enfin, ouvrez le `./src/app/calendar/calendar.component.html` fichier et remplacez son contenu par ce qui suit.

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

Cette méthode effectue une boucle dans la collection d'événements et ajoute une ligne de tableau pour chacun d'eux. Enregistrez les modifications et redémarrez l'application. Cliquez sur le lien **calendrier** et l'application doit maintenant afficher un tableau d'événements.

![Capture d'écran du tableau des événements](./images/add-msgraph-01.png)