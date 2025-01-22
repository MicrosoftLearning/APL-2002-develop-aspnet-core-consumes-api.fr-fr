---
lab:
  title: "Exercice\_: rendre les réponses API dans les applications web ASP.NET Core Blazor"
  module: 'Module: Render API responses in ASP.NET Core Blazor Web apps'
---

Dans cet exercice, vous apprendrez à ajouter du code à une application web ASP.NET Core Blazor pour afficher les résultats des opérations HTTP. Ce code est ajouté aux fichiers *.razor*. Le code qui effectue les opérations dans les fichiers *.razor.cs* est terminé.

## Objectifs

À l’issue de ce module, vous pourrez :

* Implémenter la syntaxe Razor dans une application
* Intégrer le code C# à la syntaxe Razor

## Prérequis

Pour effectuer l’exercice, les éléments suivants doivent être installés dans votre système :

* [Visual Studio Code](https://code.visualstudio.com)
* [La dernière version du kit de développement logiciel (SDK) .NET 8.0](https://dotnet.microsoft.com/download/dotnet/8.0)
* L’[Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code

**Estimation de la durée de cet exercice** : 30 minutes

## Scénario de l’exercice

Cet exercice comporte deux composants :

* une application web qui envoie des requêtes HTTP à une API L’application s’exécute sur `http://localhost:5010`
* une API qui répond aux requêtes HTTP L’application s’exécute sur `http://localhost:5050`

![Décoratif](media/02-architecture.png)

## Téléchargement du code

Dans cette section, vous allez télécharger le code de l’application web Fruit et de l’API Fruit. Vous allez également exécuter l’API Fruit localement afin qu’elle soit disponible pour l’application web.

### Tâche 1 : télécharger et exécuter le code de l’API

1. Cliquez avec le bouton droit sur le lien suivant, puis sélectionnez l’option **Enregistrer le lien**. 

    * [Code du projet FruitAPI](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitAPI.zip)

1. Ouvrez l’**Explorateur de fichiers** et accédez à l’emplacement où le fichier a été enregistré.

1. Décompressez le fichier dans son propre dossier.

1. Ouvrez **Terminal Windows**, ou une **invite de commandes**, puis accédez à l’emplacement où vous avez extrait le code de l’API.

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

    * [Code du projet d’affichage de l’application web Fruit](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitWebApp-render.zip)

1. Ouvrez l’**Explorateur de fichiers** et accédez à l’emplacement où le fichier a été enregistré.

1. Décompressez le fichier dans son propre dossier.

1. Dans Visual Studio Code, sélectionnez l**Fichier**, puis **Ouvrir le dossier** dans le menu.

1. Accédez à l’emplacement où vous avez décompressé les fichiers projet et sélectionnez le *dossier FruitWebApp-render*.

1. La structure du projet dans le volet **Explorateur** doit être similaire à la capture d’écran suivante. Si le volet **Explorateur**n’est pas visible, dans le menu, sélectionnez **Affichage**, puis **Explorateur**.

    ![Capture d’écran montrant la structure du projet de l’application web Fruit.](media/03-web-app-render-structure.png)

>**Remarque :** prenez le temps de passer en revue le code dans chacun des fichiers en cours de modification tout au long de cet exercice. Le code contient de nombreux commentaires qui peuvent vous aider à comprendre la base de code.

## Implémenter le code de rendu des données sur la page d’accueil

L’application web Fruit affiche les exemples de données d’API sur la page d’accueil. Vous devez ajouter du code pour itérer dans les exemples de données retournés par l’opération HTTP `GET` effectuée dans le fichier code-behind.

### Tâche 1 : ajouter du code pour afficher des données dans un tableau

1. Sélectionnez le fichier *Home.razor* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `@* Begin render API data code block *@` et `@* End render API data code block *@`.

    ```razor
    <tbody>
        @*  The Razor explicit expression @foreach is used to iterate through the
            data returned to the data model from the HTTP operations. *@
        @foreach (var obj in _fruitList ?? [])
        {
            <tr>
                @* Display the name of the fruit. *@
                <td width="50%">@obj.name</td>
                @*  The following if statment changes the true/false of instock to Yes/No. *@
                @{
                    if (@obj.instock)
                    {
                        <td width="20%" class="text-md-center">
                            Yes
                        </td>
                    }
                    else
                    {
                        <td width="20%" class="text-md-center">
                            No
                        </td>
                    }
                }
                <td width="30%" class="text-center">
                    @* The following div renders the Edit and Delete buttons that pass the Id 
                        to a function that handles the navigation and passes the Id to the page. *@
                    <div class="w-75 btn-group btn-group-sm" role="group" style="text-align:center">
                        <button class="btn btn-primary  mx-2" @onclick="() => EditButton(obj.id)">
                            Edit
                        </button>
                        <button class="btn btn-danger mx-2" @onclick="() => DeleteButton(obj.id)">
                            Delete
                        </button>
                    </div>
                </td>
            </tr>
        }
    </tbody>
    ```

1. Enregistrez les modifications dans *Home.razor* et examinez les commentaires dans le code.

1. Dans le menu Visual Studio Code, sélectionnez **Exécuter \| Commencer le débogage** ou appuyez sur **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution

1. Vérifiez que la page Index affiche les exemples de données de l’API.

    >**Remarque :** les fonctions **Ajouter à la liste**, **Modifier** et **Supprimer** ne fonctionneront pas tant que vous n’ajouterez pas de code pour elles, plus loin dans cet exercice.

    >**Remarque :** vous pouvez ignorer en toute sécurité l’invite ci-dessous si elle apparaît lorsque vous exécutez l’application.

    ![Capture d’écran de l’invite permettant d’installer un certificat auto-signé.](media/install-cert.png)

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet de navigation, puis dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**.

## Implémenter du code pour gérer la fonctionnalité **Ajouter à la liste**

Les opérations d’ajout, de modification et de suppression sont chacune gérées sur une page *.razor* distincte du projet. Dans cette section, vous ajoutez du code pour créer un formulaire dans le fichier *Add.razor* afin de permettre l’ajout de données à la liste.

### Tâche 1 : ajouter du code pour créer le formulaire Ajouter des données

1. Sélectionnez le fichier *Add.razor* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `@* Begin render Add code block *@` et `@* End render Add code block *@`.

    ```csharp
    @* Data is added using a Razor form, the data model is bound to the form.*@
    <EditForm OnSubmit="Submit" FormName="AddFruit" Model="_fruitList">
        @*  The _fruitList.id is here so the full data model is represented on the page.
            The database behind the API will assign the id. *@
        <InputNumber hidden="true" @bind-Value="_fruitList!.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Add Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label class="h5"></label><br />
                @* Empty text box for the name of the fruit to be added. *@
                <InputText @bind-Value="_fruitList!.name" />
            </div>
            <div class="mb-3">
                <label class="h5"></label><br />
                @* Render the true/false instock state from the record in an editable checkbox. *@
                <InputCheckbox @bind-Value="_fruitList!.instock" style="width:20px; height:20px" />
                <label class="h7">Check the box if it's available.</label>
            </div>
            @* Submit the addition or return to the Index page if the Add is cancelled.*@
            <button @onclick="() => Submit()" class="btn btn-primary" style="width:150px;">Create</button>
            <a class="btn btn-secondary" style="width:150px;" href="/">Cancel</a>
        </div>
    </EditForm>
    ```
    
1. Enregistrez les modifications apportées à *Add.razor*, et examinez les commentaires dans le code.

1. Dans le menu Visual Studio Code, sélectionnez **Exécuter \| Commencer le débogage** ou appuyez sur **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution

1. Sélectionnez **Ajouter à la liste** sur la page.

1. Saisissez le nom d’un fruit que vous souhaitez ajouter à la liste et cochez la case pour indiquer qu’il est disponible.

1. Sélectionnez **Créer** pour ajouter l’entrée à la liste. Vous reviendrez ensuite à la page d’accueil. Vérifiez que votre entrée a été ajoutée à la liste.

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet de navigation, puis dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**.

## Implémenter du code pour gérer la fonctionnalité **Modifier**

Dans cette section, vous allez ajouter du code pour créer un formulaire dans le fichier *Edit.cshtml* afin d’activer la modification des données dans la liste.

### Tâche 1 : ajouter du code pour le formulaire Modifier des données

1. Sélectionnez le fichier *Edit.razor* dans le volet **Explorrateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `@* Begin render Edit code block *@` et `@* End render Edit code block *@`.

    ```csharp
    @* Data is edited using a Razor form, the data model is bound to the form.*@
    <EditForm OnSubmit="Submit" FormName="EditFruit" Model="_fruitList">
        @*  The id for the data record is hidden because it needs to be available to the 
            code-behind processing, but it's not displayed. *@
        <InputNumber hidden="true" @bind-Value="_fruitList!.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Edit Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Render the name of the fruit in an editable text box. *@
                <InputText @bind-Value="_fruitList!.name" />
            </div>
            <div class="mb-3">
                <label  class="h5"></label><br/>
                @* Render the true/false instock state from the record in an editable checkbox. *@
                <InputCheckbox @bind-Value="_fruitList!.instock" style="width:20px; height:20px" />
                <label class="h7"><i class="bi bi-arrow-left"></i>  Check the box if available.</label>
            </div>
            @* Submit the changes or return to the Index page if the Edit is cancelled.*@
            <button type="submit" class="btn btn-danger " style="width:150px;">Save</button>
            <a class="btn btn-secondary" style="width:150px;" href="/">Cancel</a>
        </div>
    </EditForm>
    ```

1. Enregistrez les modifications dans *Edit.razor*, et examinez les commentaires dans le code.

1. Dans le menu Visual Studio Code, sélectionnez **Exécuter \| Commencer le débogage** ou appuyez sur **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution

1. Choisissez un élément dans la liste à modifier et sélectionnez **Modifier** dans cette rangée.

1. Modifiez le nom du fruit et utilisez la case pour modifier son état de disponibilité.

1. Sélectionnez **Mettre à jour** pour enregistrer vos modifications. Vous reviendrez ensuite à la page d’accueil. Vérifiez que votre modification s’affiche dans la liste.

1. Pour continuer avec l’exercice, fermez le navigateur ou l’onglet de navigation, puis dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**.

## Implémenter du code pour gérer la fonctionnalité **Supprimer**

Dans cette section, vous allez ajouter du code pour créer un formulaire dans le fichier *Delete.cshtml* afin d’activer la suppression de données dans la liste.

### Tâche 1 : ajouter du code pour le formulaire Supprimer des données

1. Sélectionnez le fichier *Delete.razor* dans le volet **Explorateur** pour l’ouvrir et le modifier.

1. Ajoutez le code suivant entre les commentaires `@* Begin render Delete code block *@` et `@* End render Delete code block *@`.

    ```csharp
    @* Data is deleted using a Razor form, the data model is bound to the form.*@
    <EditForm OnSubmit="Submit" FormName="DeleteFruit" Model="_fruitList">
        @*  The id for the data record is hidden because it needs to be available to the 
            code-behind processing, but it's not displayed. *@
        <InputNumber hidden="true" @bind-Value="_fruitList!.id" />
        <div class="border p-3 mt-4" style="width:50%">
            <div class="row pb-2">
                <h2 class="text-primary pl-3">Delete Fruit</h2>
                <hr />
            </div>
            <div class="mb-3">
                <label asp-for="FruitModels.name" class="h5"></label><br/>
                @* Render the name of the fruit in a non-editable text box. *@
                <InputText @bind-Value="_fruitList!.name" Disabled/>
            </div>
            <div class="mb-3">
                <label  class="h5"></label><br/>
                @* Render the true/false instock state from the record in a non-editable checkbox. *@
                <InputCheckbox @bind-Value="_fruitList!.instock" style="width:20px; height:20px" Disabled  />
                <label class="h7">Check the box if available.</label>
            </div>
            @* Submit the changes or return to the Index page if the delete is cancelled.*@
            <button type="submit" class="btn btn-danger " style="width:150px;">Delete</button>
            <a class="btn btn-secondary" style="width:150px;" href="/">Cancel</a>
        </div>
    </EditForm>
    ```

1. Enregistrez les modifications apportées à *Delete.razor* et examinez les commentaires dans le code.

1. Dans le menu Visual Studio Code, sélectionnez **Exécuter \| Commencer le débogage** ou appuyez sur **F5**. Une fois le projet terminé, la création d’une fenêtre de navigateur doit être lancée avec l’application web en cours d’exécution

1. Choisissez un élément dans la liste à supprimer et sélectionnez **Supprimer** dans cette rangée.

1. Sélectionnez **Supprimer**. Vous reviendrez ensuite à la page d’accueil. Vérifiez que l’élément que vous avez supprimé n’est plus affiché dans la liste.

Lorsque vous êtes prêt(e) à terminer l’exercice, suivez les étapes suivantes :

* Fermez le navigateur ou l’onglet de navigation et dans Visual Studio Code, sélectionnez **Exécuter \| Arrêter le débogage** ou effectuez **Maj + F5**. 

* Arrêtez l’API Fruit en appuyant sur **Ctrl + C** sur le terminal dans lequel elle s’exécute.

## Révision

Dans cet exercice, vous avez appris à effectuer les opérations suivantes :

* implémenter des mots-clés Razor dans une application
* intégrer du code C# à la syntaxe Razor Pages
