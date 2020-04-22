<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser la bibliothèque [Microsoft-Graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) pour passer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

1. Créez un fichier dans le `./src/app` répertoire appelé `event.ts` et ajoutez le code suivant.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/event.ts" id="eventClasses":::

1. Ajoutez un nouveau service qui contiendra tous vos appels Graph. Exécutez la commande suivante dans votre interface CLI.

    ```Shell
    ng generate service graph
    ```

    Tout comme avec le service d’authentification que vous avez créé précédemment, la création d’un service pour cela vous permet de l’injecter dans tous les composants qui ont besoin d’accéder à Microsoft Graph.

1. Une fois la commande terminée, ouvrez le `./src/app/graph.service.ts` fichier et remplacez son contenu par ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/graph.service.ts" id="graphServiceSnippet":::

    Que fait ce code ?

    - Il initialise un client Graph dans le constructeur pour le service.
    - Elle implémente une `getEvents` fonction qui utilise le client Graph de la manière suivante :
      - L’URL qui sera appelée est `/me/events`.
      - La `select` méthode limite les champs renvoyés pour chaque événement à ceux que l’affichage utilise réellement.
      - La `orderby` méthode trie les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.

1. Créez un composant angulaire pour appeler cette nouvelle méthode et afficher les résultats de l’appel. Exécutez la commande suivante dans votre interface CLI.

    ```Shell
    ng generate component calendar
    ```

1. Une fois la commande exécutée, ajoutez le composant au `routes` tableau dans `./src/app/app-routing.module.ts`.

    ```TypeScript
    import { CalendarComponent } from './calendar/calendar.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
      { path: 'calendar', component: CalendarComponent }
    ];
    ```

1. Ouvrez le `./src/app/calendar/calendar.component.ts` fichier et remplacez son contenu par ce qui suit.

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

Pour le moment, cela restitue simplement le tableau d’événements dans JSON sur la page. Enregistrez vos modifications, puis redémarrez l’application. Connectez-vous, puis cliquez sur le lien **calendrier** dans la barre de navigation. Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

À présent, vous pouvez `CalendarComponent` mettre à jour le composant pour afficher les événements de manière plus conviviale.

1. Supprimez le code temporaire qui ajoute une alerte à `ngOnInit` partir de la fonction. Votre fonction mise à jour doit ressembler à ce qui suit.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="ngOnInitSnippet":::

1. Ajoutez une fonction à la `CalendarComponent` classe pour mettre en `DateTimeTimeZone` forme un objet dans une chaîne ISO.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/calendar/calendar.component.ts" id="formatDateTimeTimeZoneSnippet":::

1. Ouvrez le `./src/app/calendar/calendar.component.html` fichier et remplacez son contenu par ce qui suit.

    :::code language="html" source="../demo/graph-tutorial/src/app/calendar/calendar.component.html" id="calendarHtml":::

Cette méthode effectue une boucle dans la collection d’événements et ajoute une ligne de tableau pour chacun d’eux. Enregistrez les modifications et redémarrez l’application. Cliquez sur le lien **calendrier** et l’application doit maintenant afficher un tableau d’événements.

![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
