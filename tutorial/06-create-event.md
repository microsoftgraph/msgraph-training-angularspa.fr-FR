<!-- markdownlint-disable MD002 MD041 -->

Dans cette section, vous allez ajouter la possibilité de créer des événements dans le calendrier de l’utilisateur.

1. Ouvrez **./SRC/App/Graph.service.TS** et ajoutez la fonction suivante à la `GraphService` classe.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/graph.service.ts" id="AddEventSnippet":::

## <a name="create-a-new-event-form"></a>Créer un formulaire d’événement

1. Créer un composant angulaire pour afficher un formulaire et appeler cette nouvelle fonction. Exécutez la commande suivante dans votre interface CLI.

    ```Shell
    ng generate component new-event
    ```

1. Une fois la commande exécutée, ajoutez le composant au `routes` tableau dans **./SRC/app/app-Routing.module.TS**.

    ```typescript
    import { NewEventComponent } from './new-event/new-event.component';

    const routes: Routes = [
      { path: '', component: HomeComponent },
      { path: 'calendar', component: CalendarComponent },
      { path: 'newevent', component: NewEventComponent },
    ];
    ```

1. Créez un fichier dans le répertoire **./SRC/app/New-Event** nommé **New-Event. TS** et ajoutez le code suivant.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/new-event/new-event.ts" id="NewEventSnippet":::

    Cette classe servira de modèle pour le nouveau formulaire d’événement.

1. Ouvrez **./SRC/app/New-Event/New-Event.Component.TS** et remplacez son contenu par le code suivant.

    :::code language="typescript" source="../demo/graph-tutorial/src/app/new-event/new-event.component.ts" id="NewEventComponentSnippet":::

1. Ouvrez **/src/app/new-event/new-event.component.html** et remplacez son contenu par le code suivant.

    :::code language="html" source="../demo/graph-tutorial/src/app/new-event/new-event.component.html" id="NewEventFormSnippet":::

1. Enregistrer les modifications et actualiser l’application. Sélectionnez le bouton **nouvel événement** sur la page calendrier, puis utilisez le formulaire pour créer un événement dans le calendrier de l’utilisateur.

    ![Capture d’écran du nouveau formulaire d’événement](images/create-event.png)
