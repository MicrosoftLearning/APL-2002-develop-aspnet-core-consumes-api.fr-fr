---
lab:
  title: "Exercice\_: Implémenter des opérations HTTP dans ASP.NET Razor Pages"
  module: 'Module: Implement HTTP operations in ASP.NET Core Razor Pages'
---

Dans cet exercice, vous découvrirez comment ajouter le code à une application ASP.NET Core Razor Pages pour créer le client HTTP et effectuer les opérations `GET`, `POST`, `PUT` et `DELETE`. Ce code est ajouté aux fichiers code-behind *.cshtml.cs* . Le code permettant d’afficher les données dans les fichiers *.cshtml* est terminé.

## Objectifs

À l’issue de ce module, vous pourrez :

* implémenter `IHttpClientFactory` en tant que client HTTP
* implémenter des opérations HTTP dans ASP.NET Core Razor Pages

## Prérequis

Pour effectuer l’exercice, les éléments suivants doivent être installés dans votre système :

* [Visual Studio Code](https://code.visualstudio.com)
* [la dernière version du kit de développement logiciel (SDK) .NET 7.0](https://dotnet.microsoft.com/download/dotnet/7.0)
* l’[Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code

**Estimation de la durée de cet exercice** : 30 minutes

## Scénario de l’exercice

Cet exercice comporte deux composants :

* une application qui envoie des requêtes HTTP à une API L’application s’exécute sur `http://localhost:5010`
* une API qui répond aux requêtes HTTP L’API s’exécute sur `http://localhost:5050`

![Décoratif](media/02-architecture.png)


## Téléchargement du code

Dans cette section, vous allez télécharger le code de l’application web Fruit et de l’API Fruit. Vous allez également exécuter l’API Fruit localement afin qu’elle soit disponible pour l’application web.

### Tâche 1 : télécharger et exécuter le code de l’API

1. Cliquez avec le bouton droit sur le lien suivant, puis sélectionnez l’option **Enregistrer le lien**. 

    * [Code du projet FruitAPI](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitAPI.zip)

1. Ouvrez l’**Explorateur de fichiers** et accédez à l’emplacement où le fichier a été enregistré.

1. Décompressez le fichier dans son propre dossier.

1. Ouvrez **Terminal Windows** ou une **invite de commandes**, puis accédez à l’emplacement où vous avez extrait le code de l’API.

1. Dans le volet du **Terminal Windows**, exécutez la commande `dotnet` suivante :

    ```
    dotnet run
    ```

1. Voici un exemple de sortie générée. Notez la ligne `Now listening on: http://localhost:5050` dans la sortie. Elle indique l’hôte et le port de l’API.

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

1. Cliquez avec le bouton droit sur le lien suivant, puis sélectionnez l’option **Enregistrer le lien**. 

    * [Code du projet code-behind de l’application web Fruit](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitWebApp-codebehind.zip)

1. Ouvrez l’**Explorateur de fichiers** et accédez à l’emplacement où le fichier a été enregistré.

1. Décompressez le fichier dans son propre dossier.

1. Dans Visual Studio Code, sélectionnez l**Fichier**, puis **Ouvrir le dossier** dans le menu.

1. Accédez à l’emplacement où vous avez décompressé les fichiers projet et sélectionnez le *dossier FruitWebApp-codebehind*.

1. La structure du projet dans le volet **Explorateur** doit être similaire à la capture d’écran suivante. Si le volet **Explorateur**n’est pas visible, dans le menu, sélectionnez **Affichage**, puis **Explorateur**.

    ![Capture d’écran montrant la structure du projet de l’application web Fruit.](media/02-web-app-cb-struture.png)

>**Remarque :** prenez le temps de passer en revue le code dans chacun des fichiers en cours de modification tout au long de cet exercice. Le code contient de nombreux commentaires qui peuvent vous aider à comprendre la base de code.

## Implémenter du code pour le client HTTP et l’opération `GET`

L’application web Fruit affiche les exemples de données d’API sur la page d’accueil. Vous devez ajouter du code pour implémenter le client HTTP et l’opération `GET` afin que l’application web dispose de données à afficher sur la page d’accueil lorsque vous la générez et l’exécutez pour la première fois.

### Tâche 1 : implémenter le client HTTP

1. Sélectionnez le fichier *Program.cs* dans le volet **Explorateur** pour l’ouvrir dans l’éditeur.

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

### Tâche 2 : implémenter l’opération `GET`

1. Sélectionnez le fichier *Index.cshtml.cs* dans le volet **Explorateur** pour l’ouvrir dans l’éditeur.

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

1. Passez en revue le code du fichier *Index.cshtml.cs*. Notez où `IHttpClientFactory` a été ajouté sur la page avec l’injection de dépendances. Notez également que le modèle de données est lié à la page à l’aide de l’attribut `[BindProperty]`.

### Étape 3 : exécuter l’application web

1. Dans le menu Visual Studio Code, sélectionnez **Exécuter \| Commencer le débogage** ou appuyez sur **F5**. Une fois le projet terminé, une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution et l’affichage des exemples de données d’API, comme illustré dans la capture d’écran suivante.

    ![Capture d’écran de l’application web affichant les exemples de données.](media/02-web-app-get-sample-data.png)

    >**Remarque :** plus loin dans l’exercice, vous allez ajouter du code pour activer les fonctions d’ajout, de modification et de suppression de l’application web. 

    >**Remarque :** vous pouvez ignorer en toute sécurité l’invite ci-dessous si elle apparaît lorsque vous exécutez l’application.

    ![Capture d’écran de l’invite permettant d’installer un certificat auto-signé.](media/install-cert.png)

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet, puis, dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou appuyez sur **Maj + F5**.

## Implémenter du code pour le client HTTP et les opérations `POST`, `PUT` et `DELETE`

Dans cette section, vous allez ajouter du code au projet pour activer les fonctions **Ajouter à la liste**, **Modifier** et **Supprimer** dans l’application web. 

### Tâche 1 : implémenter l’opération `POST`

1. Sélectionnez le fichier *Add.cshtml.cs* dans le volet **Explorateur** pour l’ouvrir dans l’éditeur.

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

1. Enregistrez les modifications apportées à *Add.cshtml.cs* et passez en revue les commentaires présents dans le code.

1. Dans le menu Visual Studio Code, sélectionnez **Exécuter \| Commencer le débogage** ou appuyez sur **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution

1. Sélectionnez le bouton **Ajouter à la liste** et renseignez le formulaire généré. Ensuite, cliquez sur le bouton **Créer**.

1. Vérifiez que votre ajout apparaît en bas de la liste. Le message de réussite/échec situé en haut de la page vous avertira s’il y a un problème.

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet, puis, dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou appuyez sur **Maj + F5**.

### Tâche 1 : implémenter l’opération `PUT`

1. Sélectionnez le fichier *Edit.cshtml.cs* dans le volet **Explorateur** pour l’ouvrir dans l’éditeur.

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

1. Enregistrez les modifications apportées à *Edit.cshtml.cs* et passez en revue les commentaires présents dans le code.

1. Dans le menu Visual Studio Code, sélectionnez **Exécuter \| Commencer le débogage** ou appuyez sur **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution

1. Choisissez un élément dans la liste à modifier et sélectionnez le bouton **Modifier**. 
1. Modifiez le **Nom de fruit** et le champ **Disponible ?**, puis sélectionnez **Mettre à jour**.

1. Vérifiez que vos modifications apparaissent dans la liste. Le message de réussite/échec situé en haut de la page vous avertira s’il y a un problème.

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet, puis, dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou appuyez sur **Maj + F5**.

### Tâche 1 : implémenter l’opération `DELETE`

1. Sélectionnez le fichier *Delete.cshtml.cs* dans le volet **Explorateur** pour l’ouvrir dans l’éditeur.

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

1. Enregistrez les modifications apportées à *Delete.cshtml.cs* et passez en revue les commentaires présents dans le code.

1. Dans le menu Visual Studio Code, sélectionnez **Exécuter \| Commencer le débogage** ou appuyez sur **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution

1. Choisissez un élément dans la liste à supprimer et sélectionnez le bouton **Supprimer**. 

1. Modifiez le **Nom de fruit** et le champ **Disponible ?**, puis sélectionnez **Mettre à jour**.

1. Vérifiez que l’élément n’apparaît plus dans la liste. Le message de réussite/échec situé en haut de la page vous avertira s’il y a un problème.

Lorsque vous êtes prêt(e) à terminer l’exercice, suivez les étapes suivantes :

* Fermer le navigateur ou l’onglet, puis, dans Visual Studio Code, sélectionner **Exécuter \| Arrêter le débogage** ou appuyer sur **Maj + F5** 

* Arrêter l’API Fruit en entrant `Ctrl + C` dans le terminal dans lequel elle s’exécute

## Révision

Dans cet exercice, vous avez appris à effectuer les opérations suivantes :

* Implémenter `IHttpClientFactory` en tant que client HTTP
* Implémenter des opérations HTTP dans les fichiers code-behind d’ASP.NET Core Razor Pages
