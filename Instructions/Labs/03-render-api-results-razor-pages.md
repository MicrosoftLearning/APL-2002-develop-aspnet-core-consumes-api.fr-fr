---
lab:
  title: "Exercice\_: afficher les réponses de l’API dans ASP.NET Core Razor\_Pages"
  module: 'Module: Render API responses in ASP.NET Core Razor Pages'
---

Dans cet exercice, vous allez apprendre à ajouter du code à une application ASP.NET Core Razor Pages pour rendre les résultats des opérations HTTP. Ce code est ajouté aux fichiers *.cshtml*. Le code qui effectue les opérations dans les fichiers *.cshtml.cs* est terminé.

## Objectifs

À la fin de cet exercice, vous serez en mesure de :

* Implémenter des mots-clés Razor dans une application
* Intégrer le code C# à la syntaxe Razor Pages

## Prérequis

Pour terminer l’exercice, vous devez avoir installé les éléments suivants sur votre système :

* [Visual Studio Code](https://code.visualstudio.com)
* [La dernière version du kit de développement logiciel .NET 7.0](https://dotnet.microsoft.com/download/dotnet/7.0)
* [L’extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code

**Estimation de la durée de cet exercice** : 30 minutes.

## Scénario de l’exercice

Cet exercice comporte deux composants :

* Une application web qui envoie des requêtes HTTP à une API. L’application s’exécute sur `http://localhost:5010`.
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

    * [Code du projet d’affichage de l’application web Fruit](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitWebApp-render.zip)

1. Lancez l'**Explorateur de fichiers** et accédez à l’emplacement où le fichier a été enregistré.

1. Décompressez le fichier dans son propre dossier.

1. Lancez Visual Studio Code et sélectionnez **Fichier**, puis **Ouvrir le dossier...** dans la barre de menus.

1. Accédez à l’emplacement où vous avez décompressé les fichiers du projet et sélectionnez le dossier *FruitWebApp-render*.

1. La structure du projet dans le volet **Explorateur** doit être similaire à la capture d’écran suivante. Si le volet **Explorateur** n’est pas visible, sélectionnez **Afficher**, puis **Explorateur** dans la barre de menus.

    ![Capture d’écran montrant la structure du projet de l’application web Fruit.](media/03-web-app-render-structure.png)

>**Remarque :** prenez le temps de passer en revue le code dans chacun des fichiers en cours de modification tout au long de cet exercice. Le code est fortement commenté et peut vous aider à comprendre la base de code.

## Implémenter du code pour afficher les données sur la page d’index

L’application web Fruit affiche les exemples de données de l’API sur la page d’accueil. Vous devez ajouter du code pour itérer dans les exemples de données retournés par l’opération HTTP `GET` effectuée dans le fichier code-behind.

### Tâche 1 : ajouter du code pour afficher des données dans une table

1. Sélectionnez le fichier *Index.cshtml* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `@* Begin render API data code block *@` et `@* End render API data code block *@`.

    ```csharp
    <tbody>
        
        @*  The Razor keyword @foreach is used to iterate through the
            data returned to the data model from the HTTP operations. *@
        @foreach (var obj in Model.FruitModels)
        {
            <tr>
                @* Display the name of the fruit. *@
                <td width="50%">@obj.name</td>
                @*  The following if statment is a Razor code block that changes the color 
                    and icon of the available indicator in the page rendering. *@
                @{
                    if (@obj.instock)
                    {
                        <td width="20%" class="text-md-center">
                            <i class="bi bi-check-circle" style="font-size: 1rem; color: green;"></i>&nbsp;Yes
                        </td>
                    }
                    else
                    {
                        <td width="20%" class="text-md-center">
                            <i class="bi bi-dash-circle" style="font-size: 1rem; color:red;"></i>&nbsp;No
                        </td>
                    }
                }
                <td width="30%" class="text-center">
                    @*  The following div contains information to handle the edit and delete functions. *@
                    <div class="w-75 btn-group btn-group-sm" role="group" style="text-align:center">
                        @* Routes to the Edit page and passes the id of the record. *@
                        <a asp-page="Edit" asp-route-id="@obj.id" class="btn btn-primary  mx-2">
                            <i class="bi bi-pencil-square"></i> Edit
                        </a>
                        @* Routes to the Delete page and passes the id of the record. *@
                        <a asp-page="Delete" asp-route-id="@obj.id" class="btn btn-danger mx-2">
                            <i class="bi bi-trash"></i> Delete
                        </a>
                    </div>
                </td>
            </tr>
        }
    </tbody>
    ```

1. Enregistrez les modifications apportées à *Index.cshtml* et passez en revue les commentaires dans le code.

1. Dans le menu supérieur de Visual Studio Code, sélectionnez **Exécuter \| Démarrer le débogage** ou sélectionnez **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution.

1. Vérifiez que la page Index affiche les exemples de données de l’API.

    >**Remarque :** les fonctions **Ajouter à la liste**, **Modifier** et **Supprimer** ne fonctionneront pas tant que vous n’ajouterez pas de code pour celles-ci plus loin dans cet exercice.

    >**Remarque :** vous pouvez ignorer en toute sécurité l’invite ci-dessous si elle apparaît lorsque vous exécutez l’application.

    ![Capture d’écran de l’invite permettant d’installer un certificat auto-signé.](media/install-cert.png)

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet de navigation, puis dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**.

## Implémenter du code pour gérer la fonctionnalité **Ajouter à la liste**

Les opérations d’ajout, de modification et de suppression sont gérées sur une page *.cshtml* distincte du projet. Dans cette section, vous allez ajouter du code pour créer un formulaire dans le fichier *Add.cshtml* pour activer l’ajout de données à la liste.

### Tâche 1 : ajouter du code pour créer le formulaire Ajouter des données

1. Sélectionnez le fichier *Add.cshtml* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `@* Begin render Add code block *@` et `@* End render Add code block *@`.

    ```csharp
    <form method="post">
        @*  The FruitModels.id is here so the full data model is represented on the page.
            The database behind the API will assign the id. *@
        <input hidden asp-for="FruitModels.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Add Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Empty text box for the name of the fruit to be added. *@
                <input type="text" asp-for="FruitModels.name" />
                <span asp-validation-for="FruitModels.name" class="text-danger"></span>
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.instock" class="h5"></label><br/>
                @* Render the true/false instock state from the record in an editable checkbox. *@
                <input type="checkbox" asp-for="FruitModels.instock" style="width:20px; height:20px" />
                <label class="h7"><i class="bi bi-arrow-left"></i>  Check the box if it's available.</label>
                <span asp-validation-for="FruitModels.instock" class="text-danger"></span>
            </div>
            @* Submit the addition or return to the Index page if the Add is cancelled.*@
            <button type="submit" class="btn btn-primary" style="width:150px;">Create</button>
            <a asp-page="Index" class="btn btn-secondary" style="width:150px;">Cancel</a>
        </div>
    </form>
    ```

1. Enregistrez les modifications apportées à *Add.cshtml* et passez en revue les commentaires dans le code.

1. Dans le menu supérieur de Visual Studio Code, sélectionnez **Exécuter \| Démarrer le débogage** ou sélectionnez **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution.

1. Cliquez sur **Ajouter à la liste** sur la page.

1. Entrez le nom d’un fruit que vous souhaitez ajouter à la liste et cochez la case pour indiquer qu’il est disponible.

1. Sélectionnez **Créer** pour ajouter l’entrée à la liste et revenir à la page d’accueil. Vérifiez que votre entrée a été ajoutée à la liste.

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet de navigation, puis dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**.

## Implémenter du code pour gérer la fonctionnalité **Modifier**

Dans cette section, vous allez ajouter du code pour créer un formulaire dans le fichier *Edit.cshtml* afin d’activer la modification des données dans la liste.

### Tâche 1 : ajouter du code pour le formulaire de modification

1. Sélectionnez le fichier *Edit.cshtml* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `@* Begin render Edit code block *@` et `@* End render Edit code block *@`.

    ```csharp
    <form method="post">
        @*  The id for the data record is hidden because it needs to be available to the 
            code-behind processing, but it's not displayed. *@
        <input hidden asp-for="FruitModels.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Edit Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Render the current name of the fruit in an editable text box. *@
                <input type="text" asp-for="FruitModels.name" />
                <span asp-validation-for="FruitModels.name" class="text-danger"></span>
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.instock" class="h5"></label><br/>
                @* Render the true/false instock state from the record in an editable checkbox. *@
                <input type="checkbox" asp-for="FruitModels.instock" style="width:20px; height:20px" />
                <label class="h7"><i class="bi bi-arrow-left"></i>  Check the box if available.</label>
                <span asp-validation-for="FruitModels.instock" class="text-danger"></span>
            </div>
            @* Submit the changes or return to the Index page if the edit is cancelled.*@
            <button type="submit" class="btn btn-primary" style="width:150px;">Update</button>
            <a asp-page="Index" class="btn btn-secondary" style="width:150px;">Cancel</a>
        </div>
    </form>
    ```

1. Enregistrez les modifications apportées à *Edit.cshtml* et passez en revue les commentaires dans le code.

1. Dans le menu supérieur de Visual Studio Code, sélectionnez **Exécuter \| Démarrer le débogage** ou sélectionnez **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution.

1. Choisissez un élément dans la liste à modifier et sélectionnez **Modifier** dans cette ligne.

1. Modifiez le nom du fruit et cochez la case pour modifier son état de disponibilité.

1. Sélectionnez **Mettre à jour** pour enregistrer vos modifications et vous serez redirigé vers la page d’accueil. Vérifiez que votre modification est affichée dans la liste.

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet de navigation, puis dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**.

## Implémenter du code pour gérer la fonctionnalité **Supprimer**

Dans cette section, vous allez ajouter du code pour créer un formulaire dans le fichier *Delete.cshtml* afin d’activer la suppression de données dans la liste.

### Tâche 1 : ajouter du code pour le formulaire de suppression

1. Sélectionnez le fichier *Delete.cshtml* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `@* Begin render Delete code block *@` et `@* End render Delete code block *@`.

    ```csharp
    <form method="post">
        @*  The id for the data record is hidden because it needs to be avaialable to the 
            code-behind processing, but it's not displayed. *@
        <input hidden asp-for="FruitModels.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Delete Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Render the name of the fruit in a non-editable text box. *@
                <input type="text" asp-for="FruitModels.name" disabled/>
                <span asp-validation-for="FruitModels.name" class="text-danger"></span>
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.instock" class="h5"></label><br/>
                @* Render the true/false instock state from the record in a non-editable checkbox. *@
                <input type="checkbox" asp-for="FruitModels.instock" style="width:20px; height:20px" disabled  />
                <span asp-validation-for="FruitModels.instock" class="text-danger"></span>
            </div>
            @* Submit the changes or return to the Index page if the delete is cancelled.*@
            <button type="submit" class="btn btn-danger " style="width:150px;">Delete</button>
            <a asp-page="Index" class="btn btn-secondary" style="width:150px;">Cancel</a>
        </div>
    </form>
    ```

1. Enregistrez les modifications apportées à *Delete.cshtml* et passez en revue les commentaires dans le code.

1. Dans le menu supérieur de Visual Studio Code, sélectionnez **Exécuter \| Démarrer le débogage** ou sélectionnez **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution.

1. Choisissez un élément dans la liste à supprimer et sélectionnez **Supprimer** dans cette ligne.

1. Sélectionnez **Supprimer** et vous serez dirigé vers la page d’accueil. Vérifiez que l’élément que vous avez supprimé n’est plus affiché dans la liste.

Lorsque vous êtes prêt à terminer l’exercice :

* Fermez le navigateur ou l’onglet de navigation et dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**. 

* Arrêtez l’API Fruit en appuyant sur **Ctrl + C** sur le terminal dans lequel elle s’exécute.

## Révision

Dans cet exercice, vous avez appris à :

* Implémenter des mots-clés Razor dans une application
* Intégrer le code C# à la syntaxe Razor Pages
