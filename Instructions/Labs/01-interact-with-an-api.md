---
lab:
  title: "Exercice\_: interagir avec une API minimale ASP.NET Core"
  module: 'Module: Interact with an ASP.NET Core minimal API'
---

Dans cet exercice, vous exécutez une API ASP.NET Core minimale localement et explorez l’API et le code sous-jacent. Vous publiez également l’API sur Azure App Service. 

À l’issue de cet exercice, vous pourrez :

* Naviguer dans une API documentée
* Déterminer les points de terminaison pour les opérations HTTP
* Identifier les exigences de l’API pour les opérations HTTP
* Publier une application sur Azure App Service

## Prérequis

Pour terminer l’exercice, vous devez avoir installé les éléments suivants sur votre système :

* [Visual Studio Code](https://code.visualstudio.com)
* [La dernière version du kit de développement logiciel .NET 7.0](https://dotnet.microsoft.com/download/dotnet/7.0)
* [L’extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code
* L’extension [Azure Resources](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups) pour Visual Studio Code.
* L’extension [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) pour Visual Studio Code.
* Compte Azure avec un abonnement actif. Si vous n’en avez pas, vous pouvez vous inscrire à une évaluation gratuite dans la page [https://azure.com/free](https://azure.com/free).

**Estimation de la durée de cet exercice** : 30 minutes.

## Informations sur l’API

L’API interagit avec une base de données en mémoire qui contient les champs suivants :

Champ | Type | Description
--- | --- | ---
`id` | entier | Clé pour les données
`name` | string | Nom du fruit
`instock` | booléen | Indique si le fruit est en stock

La documentation Swagger a été créée à l’aide du package Swashbuckle.

>**Remarque :** des exemples de données sont créés chaque fois que l’API est démarrée.


## Télécharger et exécuter le code de l’API Fruit

Dans cette section, vous allez :

* Télécharger le code de l’API
* Exécuter l’API localement
* Ouvrir la documentation de l’API dans un navigateur

### Tâche 1 : télécharger le code de l’API

1. Cliquez avec le bouton droit sur le lien suivant et sélectionnez l’option **Enregistrer le lien sous**. 

    * [code du projet FruitAPI](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitAPI.zip)

1. Lancez l’**Explorateur de fichiers** et accédez à l’emplacement où le fichier a été enregistré.

1. Décompressez le fichier dans son propre dossier.

#### Tâche 2 : exécuter l’API localement

1. Lancez Visual Studio Code et sélectionnez **Fichier**, puis **Ouvrir le dossier...** dans la barre de menus.

1. Accédez à l’emplacement où vous avez décompressé les fichiers projet et sélectionnez le dossier *FruitAPI*.

1. La structure du projet dans le volet **Explorateur** doit être similaire à la capture d’écran suivante. Si le volet **Explorateur** n’est pas visible, sélectionnez **Afficher**, puis **Explorateur** dans la barre de menus.

    ![Capture d’écran montrant la structure du projet FruitAPI.](media/api-project-structure.png)

1. Ouvrez un terminal en sélectionnant **Terminal**, puis **Nouveau terminal** ou utilisez le raccourci clavier **Ctrl + Maj + '**.

1. Dans le volet **Terminal**, exécutez la commande `dotnet` suivante :

    ```
    dotnet run
    ```

1. Voici un exemple du résultat que vous verrez dans le volet **Terminal**. Notez la ligne `Now listening on: http://localhost:5050` dans la sortie. Elle identifie l’hôte et le port de l’API.

    ```
    info: Microsoft.EntityFrameworkCore.Update[30100]
          Saved 3 entities to in-memory store.
    info: Microsoft.Hosting.Lifetime[14]
          Now listening on: http://localhost:5050
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: 
          <project location>
    ```

### Tâche 3 : ouvrir la documentation de l’API dans un navigateur

1. Pour afficher l’API, saisissez `http://localhost:5050` dans la barre d’adresse ou sélectionnez **Ctrl + Clic** sur le lien `Now listening on: http://localhost:5050` dans le **Terminal** affiché précédemment. La page affichera un message « Cette page localhost est introuvable ».

1. Ajoutez l’URL dans le navigateur avec `/swagger`. Le point de terminaison `/swagger` est généralement l’emplacement où vous trouverez la documentation d’une API Swagger. L’URL complète de la documentation Swagger est `http://localhost:5050/swagger`. Votre navigateur doit maintenant afficher une page web similaire à la capture d’écran suivante :

    ![Capture d’écran de la page de documentation de l’API.](media/api-home-page.png)

## Effectuer des opérations dans l’API

Dans cette section, vous allez :

* Effectuer plusieurs opérations sur les exemples de données
* Identifier les exigences en matière de points de terminaison et de données pour les opérations

### Tâche 1 : effectuer une opération `GET`

1. Développez l’opération **GET** dans la section **Get all fruit** en cliquant n’importe où dans la zone d’opération **GET**.

1. Explorez les sections de l’opération et notez les informations présentées dans le tableau suivant.

    | Section | Description |
    |---|--|
    | **Point de terminaison** | Affiché dans l’en-tête de l’opération. Le point de terminaison s’affiche sous la forme `/fruitlist`. L’URI complet est l’URL de base de l’API accompagnée du point de terminaison spécifié, `http://localhost:5050/fruitlist` dans notre exemple. |
    | **Paramètres** | La valeur « None » est nécessaire pour cette opération. |
    | **Type de media** | Spécifie le type de codage de média que l’opération retournera. |
    | **Exemple de valeur** | Affiche le schéma des données renvoyées par l’opération. Notez que cette opération renvoie un tableau JSON. |

1. Exécutez l’opération en sélectionnant le bouton **Essayer**, puis **Exécuter**.

1. La section **Réponses** de l’opération a été mise à jour avec de nouvelles informations. Notez ce qui suit :

    * **URL de la demande :** l’URL accessible dans l’opération.
    * **Réponse du serveur :** affiche le code de réussite de l’opération et le **Corps de la réponse** affiche les trois exemples d’enregistrements.

### Tâche 2 : effectuer une opération `POST`

1. Développez l’opération **POST** dans la section **Add fruit to list** en cliquant n’importe où dans la zone d’opération **POST**.

1. Explorez les sections de l’opération et notez les informations présentées dans le tableau suivant.

    | Section | Description |
    |---|--|
    | **Point de terminaison** | Le point de terminaison s’affiche sous la forme `/fruitlist`. L’URI complet est l’URL de base de l’API accompagnée du point de terminaison spécifié, `http://localhost:5050/fruitlist` dans notre exemple. |
    | **Paramètres** | La valeur « None » est nécessaire pour cette opération. |
    | **Corps de la demande** | Le **Corps de la requête** est requis, car l’API s’attend à ce que les données s’ajoutent à la liste et attendent le type de média `application/json`. |
    | **Exemple de valeur** | Affiche le schéma des données que l’API s’attend à recevoir. |  

1. Pour exécuter l’opération, sélectionnez le bouton **Essayer**. 

1. Remplacez le code JSON dans la zone de saisie de la section **Corps de la requête** par le code suivant :

    ```json
    {
        "id": 0,
        "name": "Pear",
        "instock": true
    }
    ```

    >**Remarque :** la base de données affecte sa propre valeur d’index lors de l’ajout de données. Il doit donc simplement y avoir une valeur dans le champ `id`.

1. La section **Réponses** de l’opération a été mise à jour avec de nouvelles informations. Notez ce qui suit :

    * **URL de la demande :** l’URL accessible dans l’opération.
    * **Réponse du serveur :** affiche le code de réussite de l’opération et le **Corps de la réponse** affiche l’enregistrement ajouté à la base de données.

1. Exécutez la commande `GET` dans la section **Get all fruit in list** et notez qu’un enregistrement pour *Poire* est désormais inclus.

### Tâche 3 : effectuer une opération `DELETE`

1. Développez l’opération **DELETE** dans la section **Delete fruit by Id** en cliquant n’importe où dans la zone d’opération **DELETE**.

1. Explorez les sections de l’opération et notez les informations présentées dans le tableau suivant.

    | Section | Description |
    |---|--|
    | **Point de terminaison** | Le point de terminaison s’affiche sous la forme `/fruitlist/{id}`. L’URI complet est l’URL de base de l’API accompagnée de la valeur `id` spécifiée pour la suppression. Par exemple, `http://localhost:5050/fruitlist/1` pointe vers l’enregistrement où `id` est égal à `1`.
    | **Paramètres** | Nécessite que la valeur `id` de l’enregistrement soit transmis dans l’URL de la requête. |

1. Pour exécuter l’opération, sélectionnez le bouton **Essayer**. 

1. Supprimez l’enregistrement `Apple` dans les exemples de données en entrant un `1` dans le champ `id` de la section **Parameters**, puis en sélectionnant **Exécuter**.

1. La section **Réponses** de l’opération a été mise à jour avec de nouvelles informations. Notez ce qui suit :

    * **URL de la demande :** l’URL accessible dans l’opération.
    * **Corps de la réponse :** affiche l’enregistrement supprimé.
    * **Code :**  affiche le code de réussite de l’opération.

1. Exécutez la commande `GET` dans la section **Get all fruit in list** et notez que l’enregistrement pour *Pomme* est maintenant supprimé.

Lorsque vous êtes prêt à passer à la section suivante de l’exercice :

* Fermez le navigateur et arrêtez l’API Fruit en sélectionnant **Ctrl + C** dans le terminal dans lequel l’API s’exécute.

## Publier l’API sur Azure App Service

Dans cette section, vous allez :

* Utiliser l’extension Azure Resources pour se connecter à Azure
* Utiliser l’extension Azure App Service pour publier l’API sur App Service

### Tâche 3 : se connecter à Azure

1. Sélectionnez l’extension Azure Resources pour ouvrir le volet.

    ![Capture d’écran montrant l’icône de l’extension Azure Resources et les options initiales.](media/01-azure-resources-ext.png)

1. Sélectionnez **Se connecter à Azure...**

    Une fenêtre de navigateur s’ouvre et vous invite à vous connecter à votre compte Azure. Vous pouvez fermer cette fenêtre une fois le processus de connexion terminé. 

1. Une fois la connexion effectuée, l’extension affiche une liste des abonnements disponibles dans votre compte. Un exemple est présenté dans la capture d’écran suivante.

    ![Capture d’écran montrant le contenu du panneau d’extension après la connexion.](media/01-azure-subscriptions.png)

### Tâche 4 : créer une application web

1. Sélectionnez **Ctrl+ Maj + P** pour ouvrir la palette de commandes et tapez **Créer une nouvelle application web** pour filtrer la liste et sélectionnez l’option **Azure App Service : créer une application web... (avancé)**. 

1. Si votre compte dispose de plusieurs abonnements, vous serez invité à sélectionner l’abonnement que vous souhaitez utiliser pour le déploiement. 

1. Entrez un nom global unique pour la nouvelle application web. Vous pouvez essayer `fruitapi-<name>` et remplacer `<name>` par votre nom ou vos initiales.

1. Sélectionnez **+Créer un groupe de ressources** et acceptez la valeur par défaut ou entrez `fruitapi-rg`.

1. Sélectionnez **.NET 7 (STS)** comme pile d’exécution.

1. Sélectionnez **Linux** comme système d’exploitation.

1. Sélectionnez un emplacement pour les nouvelles ressources près de chez vous.

1. Sélectionnez **Créer un plan App Service** et acceptez la valeur par défaut ou entrez un autre nom. 

1. Sélectionnez **Gratuit (F1) Essayer Azure gratuitement** pour le niveau tarifaire.

1. Sélectionnez **Ignorer pour le moment** quand vous êtes invité à sélectionner une nouvelle ressource Application Insights.

L’outil crée les ressources nécessaires dans Azure et compile le code.

### Tâche 3 : déployer l’application web et consulter le site en cours d’exécution

1. Une fois que les ressources sont créées et que le code a terminé la compilation, une fenêtre contextuelle s’affiche et vous invite à **Déployer**. Sélectionnez l’option **Déployer**. 

    Le système génère une version finale du code et la déploie sur les ressources que vous avez créées précédemment.

1. Une fois le déploiement terminé, une nouvelle fenêtre contextuelle s’affiche avec l’option **Parcourir le site web**. Sélectionnez **Parcourir le site web**.

1. Dans la nouvelle fenêtre du navigateur, ajoutez `/swagger` à la fin de l’URL. 

Félicitations, vous avez déployé avec succès l’API sur Azure App Service.

>**Remarque :** il est recommandé de supprimer les ressources Azure dont vous n’avez plus besoin. ––Vous pouvez supprimer toutes les ressources créées dans cette section de l’exercice en supprimant le groupe de ressources créé précédemment dans le portail Azure.

## Révision

Dans cet exercice, vous avez appris à :

* Naviguer dans une API documentée
* Déterminer les points de terminaison pour les opérations HTTP
* Identifier les exigences de l’API pour les opérations HTTP
* Publier une application sur Azure App Service 
