---
lab:
  title: "Exercice\_: implémenter des opérations HTTP dans ASP.NET Core Razor Pages"
  module: 'Module: Implement HTTP operations in ASP.NET Core Razor Pages'
---

Dans cet exercice, vous allez découvrir comment ajouter le code nécessaire à une application ASP.NET Core Razor Pages pour créer le client HTTP et effectuer les opérations `GET`, `POST`, `PUT` et `DELETE`. Ce code est ajouté aux fichiers code-behind *.cshtml.cs*. Le code permettant d’afficher les données dans les fichiers *.cshtml* est terminé.

## Objectifs

À la fin de cet exercice, vous serez en mesure de :

* Implémenter `IHttpClientFactory` en tant que client HTTP
* Implémenter des opérations HTTP dans ASP.NET Core Razor Pages

## Prérequis

Pour terminer l’exercice, vous devez avoir installé les éléments suivants sur votre système :

* [Visual Studio Code](https://code.visualstudio.com)
* [La dernière version du kit de développement logiciel .NET 7.0](https://dotnet.microsoft.com/download/dotnet/7.0)
* [L’extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code

**Estimation de la durée de cet exercice** : 30 minutes.

## Scénario de l’exercice

Cet exercice comporte deux composants :

* Une application qui envoie des requêtes HTTP à une API L’application s’exécute sur `http://localhost:5010`.
* Une API qui répond aux requêtes HTTP. L’API s’exécute sur `http://localhost:5050`.

![Décoratif](media/02-architecture.png)


## Téléchargement du code

Dans cette section, vous téléchargez le code de l’application web Fruit et de l’API Fruit. Vous exécutez également l’API Fruit localement afin qu’elle soit disponible pour l’application web.

### Tâche 1 : télécharger et exécuter le code de l’API

1. Cliquez avec le bouton droit sur le lien suivant et sélectionnez l’option **Enregistrer le lien sous**. 

    * [code du projet FruitAPI](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitAPI.zip)

1. Lancez l'**Explorateur de fichiers** et accédez à l’emplacement où le fichier a été enregistré.

1. Décompressez le fichier dans son propre dossier.

1. Ouvrez le **Terminal Windows** ou une **Invite de commandes** et accédez à l’emplacement où vous avez extrait le code de l’API.

1. Dans le volet **Terminal Windows**, exécutez la commande `dotnet` suivante :

    ```
    dotnet run
    ```

1. Voici un exemple de sortie. Notez la ligne `Now listening on: http://localhost:5050` dans la sortie. Elle identifie l’hôte et le port de l’API.

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

>**Remarque :** laissez l’API Fruit en cours d’exécution tout au long de l’exercice. 

### Tâche 2 : télécharger et ouvrir le projet d’application web

1. Cliquez avec le bouton droit sur le lien suivant et sélectionnez l’option **Enregistrer le lien sous**. 

    * [Code du projet code-behind de l’application web Fruit](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitWebApp-codebehind.zip)

1. Lancez l'**Explorateur de fichiers** et accédez à l’emplacement où le fichier a été enregistré.

1. Décompressez le fichier dans son propre dossier.

1. Lancez Visual Studio Code et sélectionnez **Fichier**, puis **Ouvrir le dossier...** dans la barre de menus.

1. Accédez à l’emplacement où vous avez décompressé les fichiers projet et sélectionnez le dossier *FruitWebApp-codebehind*.

1. La structure du projet dans le volet **Explorateur** doit être similaire à la capture d’écran suivante. Si le volet **Explorateur** n’est pas visible, sélectionnez **Afficher**, puis **Explorateur** dans la barre de menus.

    ![Capture d’écran montrant la structure du projet de l’application web Fruit.](media/02-web-app-cb-struture.png)

>**Remarque :** prenez le temps de passer en revue le code dans chacun des fichiers en cours de modification tout au long de cet exercice. Le code est fortement commenté et peut vous aider à comprendre la base de code.

## Implémenter du code pour le client HTTP et l’opération `GET`

L’application web Fruit affiche les exemples de données de l’API sur la page d’accueil. Vous devez ajouter du code pour implémenter le client HTTP et l’opération `GET` afin que l’application web dispose de données à afficher sur la page d’accueil lorsque vous la créez et l’exécutez pour la première fois.

### Tâche 1 : implémenter le client HTTP

1. Sélectionnez le fichier *Program.cs* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `// Begin HTTP client code` et `// End of HTTP client code`.

    ```csharp
    // Add IHttpClientFactory to the container and set the name of the factory
    // to "FruitAPI". The base address for API requests is also set.
    builder.Services.AddHttpClient("FruitAPI", httpClient =>
    {
        httpClient.BaseAddress = new Uri("http://localhost:5050/fruitlist/");
    });
    ```

1. Enregistrez les modifications apportées à *Program.cs*.

### Tâche 2 : implémenter l’opération GET

1. Sélectionnez le fichier *Index.cshtml.cs* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `// Begin GET operation code` et `// End GET operation code`.

    ```csharp
    // OnGet() is async since HTTP requests should be performed async
      public async Task OnGet()
      {
          // Create the HTTP client using the FruitAPI named factory
          var httpClient = _httpClientFactory.CreateClient("FruitAPI");

          // Perform the GET request and store the response. The empty parameter
          // in GetAsync doesn't modify the base address set in the client factory 
          using HttpResponseMessage response = await httpClient.GetAsync("");

          // If the request is successful deserialize the results into the data model
          if (response.IsSuccessStatusCode)
          {
              using var contentStream = await response.Content.ReadAsStreamAsync();
              FruitModels = await JsonSerializer.DeserializeAsync<IEnumerable<FruitModel>>(contentStream);
          }
      }
    ```

1. Enregistrez les modifications apportées à *Index.cshtml.cs*.

1. Vérifiez le code dans le fichier *Index.cshtml.cs*. Notez où `IHttpClientFactory` est ajouté à la page avec l’injection de dépendances. Notez également que le modèle de données est lié à la page à l’aide de l’attribut `[BindProperty]`.

### Tâche 3 : exécuter l’application web

1. Dans le menu supérieur de Visual Studio Code, sélectionnez **Exécuter \| Démarrer le débogage** ou sélectionnez **F5**. Une fois la création du projet terminée, une fenêtre de navigateur doit se lancer avec l’application Web en cours d’exécution et afficher les exemples de données de l’API, comme indiqué dans la capture d’écran suivante.

    ![Capture d’écran de l’application web affichant les exemples de données.](media/02-web-app-get-sample-data.png)

    >**Remarque :** plus loin dans l’exercice, vous ajouterez du code pour activer les fonctionnalités d’ajout, de modification et de suppression de l’application web. 

    >**Remarque :** vous pouvez ignorer en toute sécurité l’invite ci-dessous si elle apparaît lorsque vous exécutez l’application.

    ![Capture d’écran de l’invite permettant d’installer un certificat auto-signé.](media/install-cert.png)

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet de navigation, puis dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**.

## Implémenter du code pour les opérations POST, PUT et DELETE

Dans cette section, vous allez ajouter du code au projet pour activer les fonctionnalités **Ajouter à la liste**, **Modifier** et **Supprimer** dans l’application web. 

### Tâche 1 : implémenter l’opération POST

1. Sélectionnez le fichier *Add.cshtml.cs* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `// Begin POST operation code` et `// End POST operation code`.

    ```csharp
    public async Task<IActionResult> OnPost()
    {
        // Serialize the information to be added to the database
        var jsonContent = new StringContent(JsonSerializer.Serialize(FruitModels),
            Encoding.UTF8,
            "application/json");
    
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = _httpClientFactory.CreateClient("FruitAPI");
    
        // Execute the POST request and store the response. The parameters in PostAsync 
        // direct the POST to use the base address and passes the serialized data to the API
        using HttpResponseMessage response = await httpClient.PostAsync("", jsonContent);
    
        // Return to the home (Index) page and add a temporary success/failure 
        // message to the page.
        if (response.IsSuccessStatusCode)
        {
            TempData["success"] = "Data was added successfully.";
            return RedirectToPage("Index");
        }
        else
        {
            TempData["failure"] = "Operation was not successful";
            return RedirectToPage("Index");
        }
    }
    ```

1. Enregistrez les modifications apportées à *Add.cshtml.cs* et passez en revue les commentaires dans le code.

1. Dans le menu supérieur de Visual Studio Code, sélectionnez **Exécuter \| Démarrer le débogage** ou sélectionnez **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution.

1. Sélectionnez le bouton **Ajouter à la liste** et renseignez le formulaire généré. Ensuite, cliquez sur le bouton **Créer**.

1. Vérifiez que votre ajout apparaît en bas de la liste. Le message de réussite/d’échec en haut de la page vous avertira en cas de problème.

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet de navigation, puis dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**.

### Tâche 1 : implémenter l’opération PUT

1. Sélectionnez le fichier *Edit.cshtml.cs* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `// Begin PUT operation code` et `// End PUT operation code`.

    ```csharp
    public async Task<IActionResult> OnPost()
        {
            // Serialize the information to be edited in the database
            var jsonContent = new StringContent(JsonSerializer.Serialize(FruitModels),
                Encoding.UTF8,
                "application/json");
    
            // Create the HTTP client using the FruitAPI named factory
            var httpClient = _httpClientFactory.CreateClient("FruitAPI");
    
            // Execute the PUT request and store the response. The parameters in PutAsync 
            // appends the item Id to the base address and passes the serialized data to the API
            using HttpResponseMessage response = await httpClient.PutAsync(FruitModels.id.ToString(), jsonContent);
    
            // Return to the home (Index) page and add a temporary success/failure 
            // message to the page.
            if (response.IsSuccessStatusCode)
            {
                TempData["success"] = "Data was edited successfully.";
                return RedirectToPage("Index");
            }
            else
            {
                TempData["failure"] = "Operation was not successful";
                return RedirectToPage("Index");
            }
    
        }
    ```

1. Enregistrez les modifications apportées à *Edit.cshtml.cs* et passez en revue les commentaires dans le code.

1. Dans le menu supérieur de Visual Studio Code, sélectionnez **Exécuter \| Démarrer le débogage** ou sélectionnez **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution.

1. Choisissez un élément dans la liste à modifier et sélectionnez le bouton **Modifier**. 
1. Modifiez le **Nom du fruit** et le champ **Disponible ?**, puis sélectionnez **Mettre à jour**.

1. Vérifiez que vos modifications apparaissent dans la liste. Le message de réussite/d’échec en haut de la page vous avertira en cas de problème.

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet de navigation, puis dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**.

### Tâche 1 : implémenter l’opération DELETE

1. Sélectionnez le fichier *Delete.cshtml.cs* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `// Begin DELETE operation code` et `// End DELETE operation code`.

    ```csharp
    public async Task<IActionResult> OnPost()
    {
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = _httpClientFactory.CreateClient("FruitAPI");
    
        // Appends the data Id for deletion to the base address and performs the operation
        using HttpResponseMessage response = await httpClient.DeleteAsync(FruitModels.id.ToString());
    
        // Return to the home (Index) page and add a temporary success/failure 
        // message to the page.
        if (response.IsSuccessStatusCode)
        {
            TempData["success"] = "Data was deleted successfully.";
            return RedirectToPage("Index");
        }
        else
        {
            TempData["failure"] = "Operation was not successful";
            return RedirectToPage("Index");
        }
    
    }
    ```

1. Enregistrez les modifications apportées à *Delete.cshtml.cs* et passez en revue les commentaires dans le code.

1. Dans le menu supérieur de Visual Studio Code, sélectionnez **Exécuter \| Démarrer le débogage** ou sélectionnez **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution.

1. Choisissez un élément dans la liste à supprimer et sélectionnez le bouton **Supprimer**. 

1. Modifiez le **Nom du fruit** et le champ **Disponible ?**, puis sélectionnez **Mettre à jour**.

1. Vérifiez que l’élément n’apparaît plus dans la liste. Le message de réussite/d’échec en haut de la page vous avertira en cas de problème.

Lorsque vous êtes prêt à terminer l’exercice :

* Fermez le navigateur ou l’onglet de navigation et dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**. 

* Arrêtez l’API Fruit en appuyant sur **Ctrl + C** sur le terminal dans lequel elle s’exécute.

## Révision

Dans cet exercice, vous avez appris à :

* Implémenter `IHttpClientFactory` en tant que client HTTP
* Implémenter des opérations HTTP dans les fichiers code-behind ASP.NET Core Razor Pages
