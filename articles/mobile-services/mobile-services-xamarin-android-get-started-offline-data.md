---
title: Utilisation des données hors connexion dans Mobile Services (Xamarin Android) | Microsoft Docs
description: Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin Android application
documentationcenter: xamarin
author: lindydonna
editor: wesmc
manager: dwrede
services: mobile-services

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: donnam

---
# Utilisation de la synchronisation des données hors connexion dans Mobile Services
[!INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Pour la version Mobile Apps équivalente de cette rubrique, consultez l’article [Activation de la synchronisation hors connexion pour votre application mobile Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started-offline-data.md).
> 
> 

Cette rubrique décrit les fonctionnalités de synchronisation hors connexion d'Azure Mobile Services dans l'application de démarrage rapide todo list. La synchronisation hors connexion vous permet de créer facilement des applications qui sont utilisables même lorsque l'utilisateur final n'a pas accès au réseau.

La synchronisation hors connexion possède plusieurs utilisations potentielles :

* Améliorer la réactivité de l'application en mettant en cache les données de serveur localement sur l'appareil
* Rendre les applications résistantes à une connectivité réseau intermittente
* Permettre aux utilisateurs finaux de créer et de modifier des données même en l'absence d'accès au réseau, prenant ainsi en charge des scénarios avec une connectivité faible ou nulle
* Synchroniser des données sur plusieurs appareils et détecter des conflits lorsque le même enregistrement est modifié par deux appareils

> [!NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10&nbsp;services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>.
> 
> Si vous n'avez aucune expérience de Mobile Services, commencez par suivre entièrement le didacticiel [Prise en main de Mobile Services].
> 
> 

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Examiner le code de synchronisation de Mobile Services]
2. [Mettre à jour le comportement de synchronisation de l'application]
3. [Mettre à jour l'application pour reconnecter votre service mobile]

Ce didacticiel requiert les éléments suivants :

* Visual Studio avec Xamarin sur Windows ou Xamarin Studio sur Mac OS X. Des instructions d’installation complètes sont disponibles dans [Configurer et installer Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* Achèvement du didacticiel [Prise en main de Mobile Services].

## <a name="review-offline"></a>Examiner le code de synchronisation de Mobile Services
La synchronisation hors connexion d'Azure Mobile Services permet aux utilisateurs d'interagir avec une base de données locale lorsque le réseau n'est pas accessible. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous initialisez `MobileServiceClient.SyncContext` dans un magasin local. Ensuite, vous référencez votre table par le biais de l’interface `IMobileServiceSyncTable`. Cette section décrit le code associé à la synchronisation hors connexion dans `ToDoActivity.cs`.

1. Dans Visual Studio ou XamaRin Studio, ouvrez le projet que vous avez terminé dans le didacticiel [Prise en main de Mobile Services]. Ouvrez le fichier `ToDoActivity.cs`.
2. Notez que le type du membre `toDoTable` est `IMobileServiceSyncTable`. La synchronisation hors connexion utilise cette interface de table de synchronisation à la place de `IMobileServiceTable`. Lorsqu'une table de synchronisation est utilisée, toutes les opérations vont vers le magasin local et sont uniquement synchronisées avec le service distant à l'aide d'opérations push et pull explicites.
   
    La méthode `GetSyncTable()` permet d’obtenir une référence à une table de synchronisation. Pour supprimer la fonctionnalité de synchronisation hors connexion, vous utiliseriez plutôt `GetTable()`.
3. Avant de pouvoir effectuer des opérations de table, le magasin local doit être initialisé. Cette opération est effectuée dans la méthode `InitLocalStoreAsync` :
   
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);
   
            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }
   
            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();
   
            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
   
    Ceci crée un magasin local à l'aide de la classe `MobileServiceSQLiteStore`, qui est fournie dans le Kit de développement logiciel (SDK) Mobile Services. Vous pouvez également fournir une implémentation de magasin local différente en implémentant `IMobileServiceLocalStore`.
   
    La méthode `DefineTable` crée une table dans le magasin local qui correspond aux champs dans le type fourni, `ToDoItem` ici. Ce type n'a pas besoin d'inclure toutes les colonnes qui se trouvent dans la base de données distante, il est possible de stocker uniquement un sous-ensemble de colonnes.
   
    Cette surcharge de `InitializeAsync` utilise le gestionnaire de conflits par défaut qui échoue en cas de conflit. Pour obtenir un gestionnaire de conflits personnalisé, consultez le didacticiel [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services].
4. La méthode `SyncAsync` déclenche l'opération de synchronisation réelle :
   
        private async Task SyncAsync()
        {
            await client.SyncContext.PushAsync();
            await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
        }
   
    Tout d’abord, un appel à `IMobileServiceSyncContext.PushAsync()` est effectué. Cette méthode est membre de `IMobileServicesSyncContext` à la place de la table de synchronisation parce qu’elle envoie par Push les modifications sur toutes les tables. Seuls les enregistrements qui ont été modifiés d'une certaine façon en local (par le biais d'opérations CUD) seront envoyés au serveur.
   
    Ensuite, la méthode appelle `IMobileServiceSyncTable.PullAsync()` pour extraire les données d’une table sur le serveur vers l’application. Notez que s'il existe des modifications en attente dans le contexte de synchronisation, une opération pull commence toujours par émettre une opération push. Cela a pour but de garantir que toutes les tables dans le magasin local, ainsi que les relations, restent cohérentes. Ici, nous avons appelé l'opération push explicitement.
   
    Dans cet exemple, nous récupérons tous les enregistrements de la table `TodoItem` distante, mais il est également possible de filtrer les enregistrements en transmettant une requête. Le premier paramètre passé à `PullAsync()` est un ID de requête qui est utilisé pour la synchronisation incrémentielle. Il utilise l’horodatage `UpdatedAt` pour obtenir uniquement les enregistrements modifiés depuis la dernière synchronisation. L’ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application. Pour refuser la synchronisation incrémentielle, passez `null` comme ID de requête. Vous récupérerez ainsi tous les enregistrements de chaque opération pull potentiellement inefficace.
   
   > [!NOTE]
   > Pour supprimer des enregistrements du magasin local du périphérique lorsqu'ils ont été supprimés dans la base de données de votre service mobile, vous devez activer la [Suppression réversible]. Sinon, votre application doit appeler périodiquement `IMobileServiceSyncTable.PurgeAsync()` pour vider le magasin local.
   > 
   > 
   
    Notez que `MobileServicePushFailedException` peut survenir pour une opération push et une opération d'extraction. Le didacticiel suivant, [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services], montre comment gérer ces exceptions relatives à la synchronisation.
5. Dans la classe `ToDoActivity`, la méthode `SyncAsync()` est appelée après les opérations qui modifient les données, `AddItem()` et `CheckItem()`. Elle est également appelée à partir de `OnRefreshItemsSelected()`, pour que les utilisateurs obtiennent les données les plus récentes chaque fois qu'ils activent le bouton **Actualiser**. L'application exécute également une synchronisation au démarrage, puisque `ToDoActivity.OnCreate()` appelle `OnRefreshItemsSelected()`.
   
    Étant donné que `SyncAsync()` est appelée chaque fois que les données sont modifiées, cette application suppose que l'utilisateur est en ligne chaque fois qu'il modifie des données. Dans la section suivante, nous mettrons à jour l'application pour que les utilisateurs puissent effectuer des modifications même lorsqu'ils ne sont pas connectés.

## <a name="update-sync"></a>Mettre à jour le comportement de synchronisation de l'application
Dans cette section, vous allez modifier l'application pour qu'elle ne se synchronise pas lors de son démarrage ou lors des opérations d'insertion et de mise à jour, mais uniquement lorsque le bouton Actualiser est activé. Ensuite, vous allez rompre la connexion de l'application avec le service mobile pour simuler un scénario hors connexion. Lorsque vous ajoutez des éléments data, ils seront stockés dans le magasin local, sans être immédiatement synchronisés vers le service mobile.

1. Dans la classe `ToDoActivity`, modifiez les méthodes `AddItem()` et `CheckItem()` pour commenter les appels vers `SyncAsync()`.
2. Dans `ToDoActivity`, commentez les définitions des membres `applicationURL` et `applicationKey`. Ajoutez les lignes suivantes, qui font référence à une URL de service mobile incorrecte :
   
        const string applicationURL = @"https://your-mobile-service.azure-mobile.xxx/";
        const string applicationKey = @"AppKey";
3. Dans `ToDoActivity.OnCreate()`, supprimez l'appel vers `OnRefreshItemsSelected()` et remplacez-le par :
   
        // Load the items from the Mobile Service
        // OnRefreshItemsSelected (); // don't sync on app launch
        await RefreshItemsFromTableAsync(); // load UI only
4. Générez et exécutez l'application. Ajoutez de nouveaux éléments todo. Ces nouveaux éléments todo se trouvent uniquement dans le magasin local s'ils peuvent être transmis par Push vers le service mobile. L'application cliente se comporte comme si elle était connectée au service mobile et supportait toutes les opérations CRUD (Create, Read, Update et Delete, ou Créer, Lire, Mettre à jour et Supprimer).
5. Fermez l'application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.

## <a name="update-online-app"></a>Mettre à jour l'application pour reconnecter votre service mobile
Dans cette section, vous allez reconnecter l'application au service mobile. Cette opération simule le passage de l'application d'un état hors connexion à un état connecté avec le service mobile. Lorsque vous appuyez sur le bouton **Actualiser**, les données seront synchronisées avec votre service mobile.

1. Ouvrez `ToDoActivity.cs`. Supprimez l'URL de service mobile incorrecte et rajoutez la clé d'application et l'URL adéquates.
2. Régénérez et exécutez l'application. Notez que les données ont le même aspect que dans le scénario hors connexion, bien que l'application soit à présent connectée au service mobile. Cela s’explique par le fait que l’application utilise toujours la `IMobileServiceSyncTable` pointant vers le magasin local.
3. Connectez-vous au [portail Azure Classic] et examinez la base de données de votre service mobile. Si votre service utilise le backend JavaScript, vous pouvez accéder aux données à partir de l'onglet **Données** du service mobile.
   
    Si vous utilisez le backend .NET pour votre service mobile, dans Visual Studio, accédez à **Explorateur de serveurs** -> **Azure** -> **Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**.
   
    Notez que les données *n'ont pas* été synchronisées entre la base de données et le magasin local.
4. Dans l'application, appuyez sur le bouton Actualiser. `OnRefreshItemsSelected()` est appelée, qui appelle `SyncAsync()`. Les opérations push et pull sont effectuées, tout d'abord pour envoyer les éléments du magasin local vers le service mobile, puis pour récupérer les nouvelles données à partir du service.
5. Contrôlez la base de données de votre service mobile pour confirmer que les modifications ont été synchronisées.

## Résumé
[!INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Étapes suivantes
* [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]
* [Utilisation du client Xamarin Component pour Azure Mobile Services]

<!-- Anchors. -->
[Examiner le code de synchronisation de Mobile Services]: #review-offline
[Mettre à jour le comportement de synchronisation de l'application]: #update-sync
[Mettre à jour l'application pour reconnecter votre service mobile]: #update-online-app

<!-- Images -->


<!-- URLs. -->
[Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]: mobile-services-windows-store-dotnet-handling-conflicts-offline-data.md
[Prise en main de Mobile Services]: mobile-services-android-get-started.md
[Utilisation du client Xamarin Component pour Azure Mobile Services]: partner-xamarin-mobile-services-how-to-use-client-library.md
[Suppression réversible]: mobile-services-using-soft-delete.md

[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
[portail Azure Classic]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0727_2016-->