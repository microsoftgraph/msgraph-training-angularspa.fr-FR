<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l'application. Pour cette application, vous allez utiliser la bibliothèque [cliente microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour effectuer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

1. Ajoutez un nouveau service pour contenir tous vos appels Graph. Exécutez la commande suivante dans votre CLI.

    ```Shell
    ng generate service graph
    ```

    Tout comme pour le service d'authentification que vous avez créé précédemment, la création d'un service vous permet de l'injecter dans tous les composants qui ont besoin d'accéder à Microsoft Graph.

1. Une fois la commande terminée, ouvrez **./src/app/graph.service.ts** et remplacez son contenu par ce qui suit.

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

    Que fait ce code ?

    - Il initialise un client Graph dans le constructeur du service.
    - Il implémente `getCalendarView` une fonction qui utilise le client Graph de la manière suivante :
      - L’URL qui sera appelée est `/me/calendarview`.
      - La `header` méthode inclut l'en-tête, qui place les heures de début et de fin des événements renvoyés dans le fuseau horaire préféré de `Prefer: outlook.timezone` l'utilisateur.
      - La méthode ajoute les paramètres et définit la fenêtre de temps `query` `startDateTime` pour `endDateTime` l'affichage Calendrier.
      - La méthode limite les champs renvoyés pour chaque événement à ceux que `select` l'affichage utilisera réellement.
      - La `orderby` méthode trie les résultats par heure de début.

1. Créez un composant Angular pour appeler cette nouvelle méthode et afficher les résultats de l'appel. Exécutez la commande suivante dans votre CLI.

    ```Shell
    ng generate component calendar
    ```

1. Une fois la commande terminée, ajoutez le composant au tableau dans `routes` **./src/app/app-routing.module.ts**.

    ```typescript
    import { CalendarComponent } from './calendar/calendar.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
      { path: 'calendar', component: CalendarComponent },
    ];
    ```

1. Ouvrez **./tsconfig.jset** ajoutez la propriété suivante à l'objet. `compilerOptions`

    ```json
    "resolveJsonModule": true
    ```

1. Ouvrez **./src/app/calendar/calendar.component.ts** et remplacez son contenu par ce qui suit.

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

Pour l'instant, cela restituera simplement le tableau des événements dans JSON sur la page. Enregistrez vos modifications, puis redémarrez l’application. Connectez-vous et cliquez **sur le lien** Calendrier dans la barre de navigation. Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

Vous pouvez maintenant mettre à jour le composant pour afficher les événements `CalendarComponent` de manière plus conviviale.

1. Supprimez le code temporaire qui ajoute une alerte à partir de la `ngOnInit` fonction. Votre fonction mise à jour doit ressembler à ceci.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="ngOnInitSnippet":::

1. Ajoutez une fonction à la `CalendarComponent` classe pour mettre en forme un objet dans une chaîne `DateTimeTimeZone` ISO.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="formatDateTimeTimeZoneSnippet":::

1. Ouvrez **./src/app/calendar/calendar.component.html** et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/src/app/calendar/calendar.component.html" id="calendarHtml":::

Cette opération pare la collection d'événements et ajoute une ligne de tableau pour chacun d'eux. Enregistrez les modifications et redémarrez l'application. Cliquez sur le **lien Calendrier** et l'application doit maintenant restituer une table des événements.

![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
