## Création d’un concentrateur d’événements

1. Connectez-vous au [portail Azure][], puis cliquez sur **Nouveau** en haut à gauche de l’écran.

2. Cliquez sur **Données et analyses**, puis cliquez sur **Event Hubs**.

	![][9]

3. Dans le panneau **Créer un espace de noms**, entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.

	![][1]

4. Lorsque vous avez vérifié la disponibilité de l’espace de noms, sélectionnez le niveau tarifaire (Basique ou Standard). Choisissez également un abonnement Azure, un groupe de ressources et l’emplacement où créer la ressource.

2. Cliquez sur **Créer** pour créer l’espace de noms.

6. Dans la liste d’espaces de noms Event Hubs, cliquez sur l’espace de noms créé.

	![][2]

7. Dans le panneau de l’espace de noms, cliquez sur **Event Hubs**.

	![][3]

8. Cliquez sur **Ajouter un Event Hub** en haut du panneau.

	![][4]

3. Saisissez un nom pour votre Event Hub, puis cliquez sur **Créer**.

	![][5]

4. Dans la liste d’Event Hubs, cliquez sur le nom d’Event Hub nouvellement créé.

	![][6]

5. Revenez au panneau de l’espace de noms (pas le panneau de l’Event Hub spécifique), cliquez sur **Stratégies d’accès partagé**, puis cliquez sur **RootManageSharedAccessKey**.

	![][7]

5. Cliquez sur le bouton Copier pour copier la chaîne de connexion **RootManageSharedAccessKey** dans le Presse-papiers. Enregistrez cette chaîne de connexion pour l’utiliser plus tard dans ce didacticiel.

	![][8]

Votre concentrateur d’événements est désormais créé et vous disposez des chaînes de connexion dont vous avez besoin pour envoyer et recevoir des événements. 

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-event-hub4.png
[5]: ./media/event-hubs-create-event-hub/create-event-hub5.png
[6]: ./media/event-hubs-create-event-hub/create-event-hub6.png
[7]: ./media/event-hubs-create-event-hub/create-event-hub7.png
[8]: ./media/event-hubs-create-event-hub/create-event-hub8.png
[9]: ./media/event-hubs-create-event-hub/create-event-hub9.png

[portail Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0907_2016-->