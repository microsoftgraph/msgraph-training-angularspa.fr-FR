<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser la bibliothèque [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour passer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

1. Ajoutez un nouveau service qui contiendra tous vos appels Graph. Exécutez la commande suivante dans votre interface CLI.

    ```Shell
    ng generate service graph
    ```

    Tout comme avec le service d’authentification que vous avez créé précédemment, la création d’un service pour cela vous permet de l’injecter dans tous les composants qui ont besoin d’accéder à Microsoft Graph.

1. Une fois la commande terminée, ouvrez **/src/App/Graph.service.TS** et remplacez son contenu par ce qui suit.

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

    Que fait ce code ?

    - Il initialise un client Graph dans le constructeur pour le service.
    - Elle implémente une `getCalendarView` fonction qui utilise le client Graph de la manière suivante :
      - L’URL qui sera appelée est `/me/calendarview`.
      - La `header` méthode inclut l' `Prefer: outlook.timezone` en-tête, ce qui entraîne des heures de début et de fin des événements renvoyés dans le fuseau horaire préféré de l’utilisateur.
      - La `query` méthode ajoute les `startDateTime` `endDateTime` paramètres et, définissant la fenêtre de temps pour l’affichage Calendrier.
      - La `select` méthode limite les champs renvoyés pour chaque événement à ceux que l’affichage utilise réellement.
      - La `orderby` méthode trie les résultats par heure de début.

1. Créez un composant angulaire pour appeler cette nouvelle méthode et afficher les résultats de l’appel. Exécutez la commande suivante dans votre interface CLI.

    ```Shell
    ng generate component calendar
    ```

1. Une fois la commande exécutée, ajoutez le composant au `routes` tableau dans **./SRC/app/app-Routing.module.TS**.

    ```typescript
    import { CalendarComponent } from './calendar/calendar.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
      { path: 'calendar', component: CalendarComponent }
    ];
    ```

1. Ouvrez **./SRC/App/Calendar/Calendar.Component.TS** et remplacez son contenu par ce qui suit.

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

Pour le moment, cela restitue simplement le tableau d’événements dans JSON sur la page. Enregistrez vos modifications, puis redémarrez l’application. Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation. Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

À présent, vous pouvez mettre à jour le `CalendarComponent` composant pour afficher les événements de manière plus conviviale.

1. Supprimez le code temporaire qui ajoute une alerte à partir de la `ngOnInit` fonction. Votre fonction mise à jour doit ressembler à ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="ngOnInitSnippet":::

1. Ajoutez une fonction à la `CalendarComponent` classe pour mettre en forme un `DateTimeTimeZone` objet dans une chaîne ISO.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="formatDateTimeTimeZoneSnippet":::

1. Ouvrez **/src/app/calendar/calendar.component.html** et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/src/app/calendar/calendar.component.html" id="calendarHtml":::

Cette méthode effectue une boucle dans la collection d’événements et ajoute une ligne de tableau pour chacun d’eux. Enregistrez les modifications et redémarrez l’application. Cliquez sur le lien **calendrier** et l’application doit maintenant afficher un tableau d’événements.

![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
