---
title: "Cœur de ASP.NET MVC avec EF Core - modèle de données - 5, 10"
author: tdykstra
description: "Dans ce didacticiel, vous ajoutez des entités et relations et personnalisez le modèle de données en spécifiant la mise en forme, la validation et les règles de mappage de base de données."
keywords: "Annotations de données ASP.NET Core, Entity Framework Core,"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 0dd63913-a041-48b6-96a4-3aeaedbdf5d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 7d216bc07d0a8d739f0cecbc5b571b6144c13e61
ms.sourcegitcommit: 5355c96a1768e5a1d5698a98c190e7addcc4ded5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2017
---
# <a name="creating-a-complex-data-model---ef-core-with-aspnet-core-mvc-tutorial-5-of-10"></a><span data-ttu-id="fdecc-104">Création d’un modèle de données complexes - Core EF avec le didacticiel d’ASP.NET MVC de base (5 sur 10)</span><span class="sxs-lookup"><span data-stu-id="fdecc-104">Creating a complex data model - EF Core with ASP.NET Core MVC tutorial (5 of 10)</span></span>

<span data-ttu-id="fdecc-105">Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fdecc-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fdecc-106">L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET MVC de base à l’aide d’Entity Framework Core et Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fdecc-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="fdecc-107">Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).</span><span class="sxs-lookup"><span data-stu-id="fdecc-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="fdecc-108">Dans les didacticiels précédents, vous avez travaillé avec un modèle de données simple composé de trois entités.</span><span class="sxs-lookup"><span data-stu-id="fdecc-108">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="fdecc-109">Dans ce didacticiel, vous allez ajouter des entités et relations, et vous allez personnaliser le modèle de données en spécifiant la mise en forme, la validation et les règles de mappage de base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-109">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="fdecc-110">Lorsque vous avez terminé, les classes d’entité composent le modèle de données qui est indiqué dans l’illustration suivante :</span><span class="sxs-lookup"><span data-stu-id="fdecc-110">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Diagramme d’entité](complex-data-model/_static/diagram.png)

## <a name="customize-the-data-model-by-using-attributes"></a><span data-ttu-id="fdecc-112">Personnaliser le modèle de données à l’aide d’attributs</span><span class="sxs-lookup"><span data-stu-id="fdecc-112">Customize the Data Model by Using Attributes</span></span>

<span data-ttu-id="fdecc-113">Dans cette section, vous allez apprendre à personnaliser le modèle de données à l’aide des attributs qui spécifient la mise en forme, validation et les règles de mappage de base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-113">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="fdecc-114">Puis dans plusieurs des sections suivantes, vous allez créer le modèle de données School complète en ajoutant des attributs aux classes que vous avez déjà créé et créer de nouvelles classes pour les autres types d’entités dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="fdecc-114">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="fdecc-115">L’attribut de type de données</span><span class="sxs-lookup"><span data-stu-id="fdecc-115">The DataType attribute</span></span>

<span data-ttu-id="fdecc-116">Pour les dates d’inscription étudiant, toutes les pages web actuellement affichent l’heure, ainsi que la date, même si tout vous intéressent pour ce champ est la date.</span><span class="sxs-lookup"><span data-stu-id="fdecc-116">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="fdecc-117">À l’aide des attributs d’annotations de données, vous pouvez apporter une modification qui permet de corriger le format d’affichage dans chaque vue qui affiche les données de code.</span><span class="sxs-lookup"><span data-stu-id="fdecc-117">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="fdecc-118">Pour voir un exemple de procédure, vous allez ajouter un attribut à la `EnrollmentDate` propriété dans la `Student` classe.</span><span class="sxs-lookup"><span data-stu-id="fdecc-118">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="fdecc-119">Dans *Models/Student.cs*, ajouter un `using` instruction pour le `System.ComponentModel.DataAnnotations` espace de noms et ajoutez `DataType` et `DisplayFormat` des attributs à la `EnrollmentDate` la propriété, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="fdecc-119">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

<span data-ttu-id="fdecc-120">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-120">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]</span></span>

<span data-ttu-id="fdecc-121">Le `DataType` attribut est utilisé pour spécifier un type de données qui est plus spécifique que le type intrinsèque de la base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-121">The `DataType` attribute is used to specify a data type that is more specific than the database intrinsic type.</span></span> <span data-ttu-id="fdecc-122">Dans ce cas, nous voulons uniquement le suivi de la date, pas la date et l’heure.</span><span class="sxs-lookup"><span data-stu-id="fdecc-122">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="fdecc-123">Le `DataType` énumération fournit de nombreux types de données, telles que Date, Time, numéro de téléphone, devise, EmailAddress et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="fdecc-123">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="fdecc-124">Le `DataType` attribut peut également permettre à l’application fournir automatiquement les fonctionnalités spécifiques au type.</span><span class="sxs-lookup"><span data-stu-id="fdecc-124">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="fdecc-125">Par exemple, un `mailto:` lien peut être créé pour `DataType.EmailAddress`, et un sélecteur de date peut être fourni pour `DataType.Date` dans les navigateurs qui prennent en charge de HTML5.</span><span class="sxs-lookup"><span data-stu-id="fdecc-125">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="fdecc-126">Le `DataType` émet un attribut HTML 5 `data-` attributs (données prononcé tiret) qui peuvent de comprendre les navigateurs HTML 5.</span><span class="sxs-lookup"><span data-stu-id="fdecc-126">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="fdecc-127">Le `DataType` attributs ne fournissent pas de validation.</span><span class="sxs-lookup"><span data-stu-id="fdecc-127">The `DataType` attributes do not provide any validation.</span></span>

<span data-ttu-id="fdecc-128">`DataType.Date`ne spécifie pas le format de la date qui s’affiche.</span><span class="sxs-lookup"><span data-stu-id="fdecc-128">`DataType.Date` does not specify the format of the date that is displayed.</span></span> <span data-ttu-id="fdecc-129">Par défaut, le champ de données s’affiche selon les formats par défaut en fonction CultureInfo du serveur.</span><span class="sxs-lookup"><span data-stu-id="fdecc-129">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="fdecc-130">Le `DisplayFormat` attribut est utilisé pour spécifier explicitement le format de date :</span><span class="sxs-lookup"><span data-stu-id="fdecc-130">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="fdecc-131">Le `ApplyFormatInEditMode` paramètre spécifie que la mise en forme doit également être appliquée lorsque la valeur est affichée dans une zone de texte pour la modification.</span><span class="sxs-lookup"><span data-stu-id="fdecc-131">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="fdecc-132">(Vous pouvez que pour certains champs--par exemple, pour les valeurs de devise, vous pouvez le symbole de devise dans la zone de texte pour le modifier.)</span><span class="sxs-lookup"><span data-stu-id="fdecc-132">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="fdecc-133">Vous pouvez utiliser la `DisplayFormat` attribut par lui-même, mais il est généralement judicieux d’utiliser le `DataType` également d’attribut.</span><span class="sxs-lookup"><span data-stu-id="fdecc-133">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="fdecc-134">Le `DataType` attribut donne la sémantique des données par opposition à comment effectuer le rendu sur un écran et qu’il offre les avantages suivants que vous n’obtenez pas avec `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="fdecc-134">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="fdecc-135">Le navigateur peut activer des fonctionnalités HTML5 (par exemple pour afficher un contrôle de calendrier, le symbole monétaire approprié de paramètres régionaux, les liens de courrier électronique, certains côté client d’entrée validation, etc..).</span><span class="sxs-lookup"><span data-stu-id="fdecc-135">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="fdecc-136">Par défaut, le navigateur renvoie les données en utilisant le format approprié en fonction de vos paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="fdecc-136">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="fdecc-137">Pour plus d’informations, consultez la [ \<d’entrée > documentation de l’application d’assistance de balise](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="fdecc-137">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="fdecc-138">Réexécutez la page d’Index des étudiants et notez que fois ne sont plus affichés pour les dates d’inscription.</span><span class="sxs-lookup"><span data-stu-id="fdecc-138">Run the Students Index page again and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="fdecc-139">Le même aura la valeur true pour n’importe quelle vue qui utilise le modèle de Student.</span><span class="sxs-lookup"><span data-stu-id="fdecc-139">The same will be true for any view that uses the Student model.</span></span>

![Affichage des dates sans heures dans la page d’index les étudiants](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="fdecc-141">L’attribut StringLength</span><span class="sxs-lookup"><span data-stu-id="fdecc-141">The StringLength attribute</span></span>

<span data-ttu-id="fdecc-142">Vous pouvez également spécifier des règles de validation de données et les messages d’erreur de validation à l’aide d’attributs.</span><span class="sxs-lookup"><span data-stu-id="fdecc-142">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="fdecc-143">Le `StringLength` attribut définit la longueur maximale de la base de données et fournit des côté client et côté serveur validation pour ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="fdecc-143">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET MVC.</span></span> <span data-ttu-id="fdecc-144">Vous pouvez également spécifier la longueur de chaîne minimale dans cet attribut, mais la valeur minimale n’a aucun impact sur le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-144">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="fdecc-145">Supposons que vous souhaitez vous assurer que les utilisateurs n’entrent pas plus de 50 caractères pour un nom.</span><span class="sxs-lookup"><span data-stu-id="fdecc-145">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="fdecc-146">Pour ajouter cette limitation, ajoutez `StringLength` des attributs à la `LastName` et `FirstMidName` propriétés, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="fdecc-146">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

<span data-ttu-id="fdecc-147">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-147">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]</span></span>

<span data-ttu-id="fdecc-148">Le `StringLength` attribut ne sera pas empêcher un utilisateur d’entrer un espace blanc pour un nom.</span><span class="sxs-lookup"><span data-stu-id="fdecc-148">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="fdecc-149">Vous pouvez utiliser la `RegularExpression` attribut à appliquer des restrictions à l’entrée.</span><span class="sxs-lookup"><span data-stu-id="fdecc-149">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="fdecc-150">Par exemple, le code suivant requiert le premier caractère en majuscules et les autres caractères alphabétique :</span><span class="sxs-lookup"><span data-stu-id="fdecc-150">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z''-'\s]*$")]
```

<span data-ttu-id="fdecc-151">Le `MaxLength` attribut fournit des fonctionnalités similaires à la `StringLength` d’attribut, mais ne fournit pas côté client validation.</span><span class="sxs-lookup"><span data-stu-id="fdecc-151">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="fdecc-152">Le modèle de base de données a maintenant changé d’une manière qui nécessite une modification dans le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-152">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="fdecc-153">Migrations vous permet de mettre à jour le schéma sans perdre de données que vous avez peut-être ajoutés à la base de données à l’aide de l’interface utilisateur de l’application.</span><span class="sxs-lookup"><span data-stu-id="fdecc-153">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="fdecc-154">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="fdecc-154">Save your changes and build the project.</span></span> <span data-ttu-id="fdecc-155">Ouvrez la fenêtre de commande dans le dossier du projet, puis entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="fdecc-155">Then open the command window in the project folder and enter the following commands:</span></span>

```console
dotnet ef migrations add MaxLengthOnNames
```

```console
dotnet ef database update
```

<span data-ttu-id="fdecc-156">Le `migrations add` commande vous avertit qu’une perte de données peut-être se produire, car la modification la plus courte pour les deux colonnes de longueur maximale.</span><span class="sxs-lookup"><span data-stu-id="fdecc-156">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="fdecc-157">Migrations crée un fichier nommé  *\<timeStamp > _MaxLengthOnNames.cs*.</span><span class="sxs-lookup"><span data-stu-id="fdecc-157">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="fdecc-158">Ce fichier contient le code dans la `Up` méthode qui met à jour la base de données correspond au modèle de données en cours.</span><span class="sxs-lookup"><span data-stu-id="fdecc-158">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="fdecc-159">Le `database update` commande exécution de ce code.</span><span class="sxs-lookup"><span data-stu-id="fdecc-159">The `database update` command ran that code.</span></span>

<span data-ttu-id="fdecc-160">L’horodateur le préfixe au nom de fichier migrations est utilisée par Entity Framework pour classer les migrations.</span><span class="sxs-lookup"><span data-stu-id="fdecc-160">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="fdecc-161">Vous pouvez créer plusieurs migrations avant d’exécuter la commande de base de données de mise à jour, puis toutes les migrations sont appliquées dans l’ordre dans lequel ils ont été créés.</span><span class="sxs-lookup"><span data-stu-id="fdecc-161">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="fdecc-162">Exécuter la page de création et entrez le nom de plus de 50 caractères.</span><span class="sxs-lookup"><span data-stu-id="fdecc-162">Run the Create page, and enter either name longer than 50 characters.</span></span> <span data-ttu-id="fdecc-163">Lorsque vous cliquez sur Créer, la validation côté client affiche un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="fdecc-163">When you click Create, client side validation shows an error message.</span></span>

![Page affichant les erreurs de longueur de chaîne d’index les étudiants](complex-data-model/_static/string-length-errors.png)

### <a name="the-column-attribute"></a><span data-ttu-id="fdecc-165">L’attribut de colonne</span><span class="sxs-lookup"><span data-stu-id="fdecc-165">The Column attribute</span></span>

<span data-ttu-id="fdecc-166">Vous pouvez également utiliser des attributs pour contrôler la façon dont les classes et les propriétés sont mappées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-166">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="fdecc-167">Supposons que vous aviez utilisé le nom `FirstMidName` pour le prénom, car le champ peut également contenir un deuxième prénom.</span><span class="sxs-lookup"><span data-stu-id="fdecc-167">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="fdecc-168">Mais vous souhaitez que la colonne de base de données nommé `FirstName`, car les utilisateurs qui doit écrire des requêtes ad-hoc par rapport à la base de données sont habitués à ce nom.</span><span class="sxs-lookup"><span data-stu-id="fdecc-168">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="fdecc-169">Pour effectuer ce mappage, vous pouvez utiliser la `Column` attribut.</span><span class="sxs-lookup"><span data-stu-id="fdecc-169">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="fdecc-170">Le `Column` attribut spécifie que lorsque la base de données est créé, la colonne de la `Student` table qui mappe à la `FirstMidName` propriété sera nommée `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="fdecc-170">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="fdecc-171">En d’autres termes, lorsque votre code fait référence à `Student.FirstMidName`, les données proviennent ou être mis à jour dans le `FirstName` colonne de la `Student` table.</span><span class="sxs-lookup"><span data-stu-id="fdecc-171">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="fdecc-172">Si vous ne spécifiez pas les noms de colonnes, elles reçoivent le même nom que le nom de propriété.</span><span class="sxs-lookup"><span data-stu-id="fdecc-172">If you don't specify column names, they are given the same name as the property name.</span></span>

<span data-ttu-id="fdecc-173">Dans le *Student.cs* , ajoutez un `using` instruction pour `System.ComponentModel.DataAnnotations.Schema` et ajoutez l’attribut de nom de colonne à la `FirstMidName` la propriété, comme indiqué dans le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="fdecc-173">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

<span data-ttu-id="fdecc-174">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-174">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]</span></span>

<span data-ttu-id="fdecc-175">L’ajout de la `Column` attribut modifie la sauvegarde du modèle le `SchoolContext`, donc il ne correspond pas à la base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-175">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="fdecc-176">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="fdecc-176">Save your changes and build the project.</span></span> <span data-ttu-id="fdecc-177">Ouvrez la fenêtre de commande dans le dossier du projet, puis entrez les commandes suivantes pour créer une autre migration :</span><span class="sxs-lookup"><span data-stu-id="fdecc-177">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```console
dotnet ef migrations add ColumnFirstName
```

```console
dotnet ef database update
```

<span data-ttu-id="fdecc-178">Dans **l’Explorateur d’objets SQL Server**, ouvrez le Concepteur de tables Student en double-cliquant sur le **étudiant** table.</span><span class="sxs-lookup"><span data-stu-id="fdecc-178">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![Table d’étudiants dans SSOX après la migration](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="fdecc-180">Avant d’appliquer les deux premières migrations, les colonnes de nom sont de type nvarchar (max).</span><span class="sxs-lookup"><span data-stu-id="fdecc-180">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="fdecc-181">Il s’agit de nvarchar (50) et le nom de colonne est devenue FirstMidName FirstName.</span><span class="sxs-lookup"><span data-stu-id="fdecc-181">They are now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="fdecc-182">Si vous essayez de compiler avant de terminer la création de toutes les classes d’entité dans les sections suivantes, vous pouvez obtenir les erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="fdecc-182">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="final-changes-to-the-student-entity"></a><span data-ttu-id="fdecc-183">Modifications finales à l’entité Student</span><span class="sxs-lookup"><span data-stu-id="fdecc-183">Final changes to the Student entity</span></span>

![Entité Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="fdecc-185">Dans *Models/Student.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="fdecc-185">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="fdecc-186">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="fdecc-186">The changes are highlighted.</span></span>

<span data-ttu-id="fdecc-187">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-187">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="fdecc-188">L’attribut requis</span><span class="sxs-lookup"><span data-stu-id="fdecc-188">The Required attribute</span></span>

<span data-ttu-id="fdecc-189">Le `Required` attribut rend les champs obligatoires de propriétés de nom.</span><span class="sxs-lookup"><span data-stu-id="fdecc-189">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="fdecc-190">Le `Required` attribut n’est pas nécessaire pour les types non nullable tels que des types valeur (DateTime, int, double, float, etc..).</span><span class="sxs-lookup"><span data-stu-id="fdecc-190">The `Required` attribute is not needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="fdecc-191">Les types qui ne peut pas être null sont traitées automatiquement en tant que champs requis.</span><span class="sxs-lookup"><span data-stu-id="fdecc-191">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="fdecc-192">Vous pouvez supprimer la `Required` d’attribut et les remplacer par un paramètre de longueur minimale pour le `StringLength` attribut :</span><span class="sxs-lookup"><span data-stu-id="fdecc-192">You could remove the `Required` attribute and replace it with a minimum length parameter for the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="fdecc-193">L’attribut d’affichage</span><span class="sxs-lookup"><span data-stu-id="fdecc-193">The Display attribute</span></span>

<span data-ttu-id="fdecc-194">Le `Display` attribut spécifie que la légende pour les zones de texte doit être « Prénom », « Last Name », « Nom complet » et « Date d’inscription », au lieu du nom de propriété dans chaque instance (qui n’a pas les mots de la division d’espace).</span><span class="sxs-lookup"><span data-stu-id="fdecc-194">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="fdecc-195">La propriété FullName calculée</span><span class="sxs-lookup"><span data-stu-id="fdecc-195">The FullName calculated property</span></span>

<span data-ttu-id="fdecc-196">`FullName`est une propriété calculée qui retourne une valeur qui est obtenue par concaténation de deux autres propriétés.</span><span class="sxs-lookup"><span data-stu-id="fdecc-196">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="fdecc-197">Par conséquent, il a uniquement un accesseur get et non `FullName` colonne sera générée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-197">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="fdecc-198">Créer l’entité Instructor</span><span class="sxs-lookup"><span data-stu-id="fdecc-198">Create the Instructor Entity</span></span>

![Entité Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="fdecc-200">Créer *Models/Instructor.cs*, en remplaçant le code du modèle avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="fdecc-200">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

<span data-ttu-id="fdecc-201">[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-201">[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]</span></span>

<span data-ttu-id="fdecc-202">Notez que plusieurs propriétés sont identiques dans les entités Student et formateur.</span><span class="sxs-lookup"><span data-stu-id="fdecc-202">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="fdecc-203">Dans le [implémentation de l’héritage](inheritance.md) didacticiel plus loin dans cette série, vous devez refactoriser ce code pour éliminer la redondance.</span><span class="sxs-lookup"><span data-stu-id="fdecc-203">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="fdecc-204">Vous pouvez placer plusieurs attributs sur une seule ligne, afin que vous pouvez également écrire le `HireDate` attributs comme suit :</span><span class="sxs-lookup"><span data-stu-id="fdecc-204">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="fdecc-205">Les propriétés de navigation CourseAssignments et OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="fdecc-205">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="fdecc-206">Le `CourseAssignments` et `OfficeAssignment` propriétés sont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="fdecc-206">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="fdecc-207">Formateur pouvez animer n’importe quel nombre de cours, de sorte que `CourseAssignments` est défini comme une collection.</span><span class="sxs-lookup"><span data-stu-id="fdecc-207">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="fdecc-208">Si une propriété de navigation peut contenir plusieurs entités, son type doit être une liste dans laquelle les entrées peuvent être ajoutées, supprimées et mis à jour.</span><span class="sxs-lookup"><span data-stu-id="fdecc-208">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="fdecc-209">Vous pouvez spécifier `ICollection<T>` ou un type tel que `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="fdecc-209">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="fdecc-210">Si vous spécifiez `ICollection<T>`, EF crée un `HashSet<T>` collection par défaut.</span><span class="sxs-lookup"><span data-stu-id="fdecc-210">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="fdecc-211">La raison pour laquelle ils sont `CourseAssignment` entités sont expliquées ci-dessous dans la section sur les relations plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="fdecc-211">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="fdecc-212">L’état des règles d’entreprise Contoso University qu’un formateur peut avoir uniquement au plus un bureau, donc la `OfficeAssignment` propriété contient une seule entité OfficeAssignment (qui peut être null si aucune office n’est affectée).</span><span class="sxs-lookup"><span data-stu-id="fdecc-212">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="fdecc-213">Créer l’entité OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="fdecc-213">Create the OfficeAssignment entity</span></span>

![Entité OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="fdecc-215">Créer *Models/OfficeAssignment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="fdecc-215">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

<span data-ttu-id="fdecc-216">[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-216">[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]</span></span>

### <a name="the-key-attribute"></a><span data-ttu-id="fdecc-217">L’attribut de clé</span><span class="sxs-lookup"><span data-stu-id="fdecc-217">The Key attribute</span></span>

<span data-ttu-id="fdecc-218">Il existe une relation un-à-zéro-ou-un entre le formateur et les entités OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="fdecc-218">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="fdecc-219">Une attribution existe uniquement en relation avec le formateur, à qu'il est assigné, et par conséquent sa clé primaire est également sa clé étrangère vers l’entité Instructor.</span><span class="sxs-lookup"><span data-stu-id="fdecc-219">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="fdecc-220">Mais Entity Framework ne peut pas reconnaître automatiquement InstructorID comme clé primaire de cette entité, car son nom ne suit pas la convention d’affectation de noms ID ou classnameID.</span><span class="sxs-lookup"><span data-stu-id="fdecc-220">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="fdecc-221">Par conséquent, le `Key` attribut est utilisé pour identifier en tant que la clé :</span><span class="sxs-lookup"><span data-stu-id="fdecc-221">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="fdecc-222">Vous pouvez également utiliser le `Key` si l’entité n’a pas sa propre clé primaire, mais que vous souhaitez la propriété donner un nom autre que classnameID ou ID d’attribut</span><span class="sxs-lookup"><span data-stu-id="fdecc-222">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="fdecc-223">Par défaut, EF traite la touche non généré à la base de données, car la colonne est pour une relation d’identification.</span><span class="sxs-lookup"><span data-stu-id="fdecc-223">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="fdecc-224">La propriété de navigation de formateur</span><span class="sxs-lookup"><span data-stu-id="fdecc-224">The Instructor navigation property</span></span>

<span data-ttu-id="fdecc-225">Entité Instructor a nullable `OfficeAssignment` propriété de navigation (parce qu’un formateur n’est peut-être pas une assignation office), et l’entité OfficeAssignment a non-nullable `Instructor` propriété de navigation (car une attribution ne peut pas exister sans un formateur-- `InstructorID` non-null).</span><span class="sxs-lookup"><span data-stu-id="fdecc-225">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="fdecc-226">Lorsqu’une entité Instructor possède une entité OfficeAssignment associée, chaque entité a une référence à l’autre dans sa propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="fdecc-226">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="fdecc-227">Vous pouvez placer un `[Required]` attribut sur la propriété de navigation du formateur pour spécifier qu’il doit y avoir un formateur connexe, mais vous n’êtes pas obligé de le faire, car le `InstructorID` clé étrangère (qui est également la clé pour cette table) est non nullable.</span><span class="sxs-lookup"><span data-stu-id="fdecc-227">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="fdecc-228">Modifier l’entité de cours</span><span class="sxs-lookup"><span data-stu-id="fdecc-228">Modify the Course Entity</span></span>

![Entité de cours](complex-data-model/_static/course-entity.png)

<span data-ttu-id="fdecc-230">Dans *Models/Course.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="fdecc-230">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="fdecc-231">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="fdecc-231">The changes are highlighted.</span></span>

<span data-ttu-id="fdecc-232">[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-232">[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]</span></span>

<span data-ttu-id="fdecc-233">L’entité de cours possède une propriété de clé étrangère `DepartmentID` qui pointe vers l’entité de service associée et il a un `Department` propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="fdecc-233">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="fdecc-234">Entity Framework ne requiert pas vous permet d’ajouter une propriété de clé étrangère à votre modèle de données lorsque vous disposez d’une propriété de navigation pour une entité associée.</span><span class="sxs-lookup"><span data-stu-id="fdecc-234">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="fdecc-235">EF crée des clés étrangères dans la base de données partout où elles sont nécessaires et crée automatiquement [occulter les propriétés](https://docs.microsoft.com/ef/core/modeling/shadow-properties) pour eux.</span><span class="sxs-lookup"><span data-stu-id="fdecc-235">EF automatically creates foreign keys in the database wherever they are needed and creates [shadow properties](https://docs.microsoft.com/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="fdecc-236">Mais ayant la clé étrangère dans le modèle de données peut rendre les mises à jour plus simple et plus efficace.</span><span class="sxs-lookup"><span data-stu-id="fdecc-236">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="fdecc-237">Par exemple, lorsque vous lisez une entité de cours à modifier, l’entité du service est null si vous ne le charge pas, par conséquent, lorsque vous mettez à jour l’entité de cours, vous devez tout d’abord récupérer l’entité du service.</span><span class="sxs-lookup"><span data-stu-id="fdecc-237">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="fdecc-238">Lorsque la propriété de clé étrangère `DepartmentID` est inclus dans le modèle de données, vous n’avez pas besoin récupérer l’entité du service avant de mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="fdecc-238">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="fdecc-239">L’attribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="fdecc-239">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="fdecc-240">Le `DatabaseGenerated` d’attribut avec le `None` paramètre sur le `CourseID` propriété spécifie que les valeurs de clé primaire sont fournies par l’utilisateur au lieu de générés par la base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-240">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="fdecc-241">Par défaut, Entity Framework suppose que les valeurs de clés primaires sont générées par la base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-241">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="fdecc-242">C’est ce que vous souhaitez dans la plupart des scénarios.</span><span class="sxs-lookup"><span data-stu-id="fdecc-242">That's what you want in most scenarios.</span></span> <span data-ttu-id="fdecc-243">Toutefois, pour les entités de cours, vous allez utiliser un numéro spécifié par l’utilisateur comme une série de 1000 pour un service, une série de 2000 pour un autre service et ainsi de suite.</span><span class="sxs-lookup"><span data-stu-id="fdecc-243">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="fdecc-244">Le `DatabaseGenerated` attribut peut également être utilisé pour générer des valeurs par défaut, dans le cas des colonnes de base de données utilisées pour enregistrer la date d’une ligne a été créée ou mis à jour.</span><span class="sxs-lookup"><span data-stu-id="fdecc-244">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="fdecc-245">Pour plus d’informations, consultez [généré des propriétés](https://docs.microsoft.com/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="fdecc-245">For more information, see [Generated Properties](https://docs.microsoft.com/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="fdecc-246">Propriétés de navigation et de clé étrangère</span><span class="sxs-lookup"><span data-stu-id="fdecc-246">Foreign key and navigation properties</span></span>

<span data-ttu-id="fdecc-247">Les propriétés de clé étrangère et les propriétés de navigation dans l’entité de cours reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="fdecc-247">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="fdecc-248">Un cours est affecté à un service, donc il est un `DepartmentID` clé étrangère et un `Department` propriété de navigation pour les raisons mentionnées ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="fdecc-248">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="fdecc-249">Un cours peut avoir n’importe quel nombre d’élèves inscrits, par conséquent, le `Enrollments` propriété de navigation est une collection :</span><span class="sxs-lookup"><span data-stu-id="fdecc-249">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="fdecc-250">Un cours peut être dirigée par des instructeurs plusieurs, donc la `CourseAssignments` propriété de navigation est une collection (le type `CourseAssignment` est expliquée [ultérieurement](#many-to-many-relationships)) :</span><span class="sxs-lookup"><span data-stu-id="fdecc-250">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-the-department-entity"></a><span data-ttu-id="fdecc-251">Créer l’entité du service</span><span class="sxs-lookup"><span data-stu-id="fdecc-251">Create the Department entity</span></span>

![Entité de service](complex-data-model/_static/department-entity.png)


<span data-ttu-id="fdecc-253">Créer *Models/Department.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="fdecc-253">Create *Models/Department.cs* with the following code:</span></span>

<span data-ttu-id="fdecc-254">[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-254">[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="fdecc-255">L’attribut de colonne</span><span class="sxs-lookup"><span data-stu-id="fdecc-255">The Column attribute</span></span>

<span data-ttu-id="fdecc-256">Précédemment, vous avez utilisé le `Column` attribut pour modifier le mappage de nom de colonne.</span><span class="sxs-lookup"><span data-stu-id="fdecc-256">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="fdecc-257">Dans le code de l’entité du service, le `Column` attribut sert à modifier SQL type correspondance de données afin que la colonne sera définie à l’aide du type de money SQL Server dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="fdecc-257">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="fdecc-258">Mappage de colonnes n’est généralement pas nécessaire, car l’Entity Framework choisit le type de données SQL Server approprié en fonction du type CLR que vous définissez pour la propriété.</span><span class="sxs-lookup"><span data-stu-id="fdecc-258">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="fdecc-259">Le CLR `decimal` type correspond à un serveur SQL Server `decimal` type.</span><span class="sxs-lookup"><span data-stu-id="fdecc-259">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="fdecc-260">Mais dans ce cas vous savez que la colonne organiserons des montants en devise et le type de données money est plus adapté.</span><span class="sxs-lookup"><span data-stu-id="fdecc-260">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="fdecc-261">Propriétés de navigation et de clé étrangère</span><span class="sxs-lookup"><span data-stu-id="fdecc-261">Foreign key and navigation properties</span></span>

<span data-ttu-id="fdecc-262">Les propriétés de navigation et de clé étrangère reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="fdecc-262">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="fdecc-263">Un service peut ou ne peut pas avoir un administrateur, et un administrateur est toujours un formateur.</span><span class="sxs-lookup"><span data-stu-id="fdecc-263">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="fdecc-264">Par conséquent la `InstructorID` propriété n’est incluse en tant que clé étrangère à l’entité Instructor, et un point d’interrogation est ajouté après le `int` désignation pour marquer la propriété Nullable de type.</span><span class="sxs-lookup"><span data-stu-id="fdecc-264">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="fdecc-265">La propriété de navigation est nommée `Administrator` , mais sa valeur est une entité Instructor :</span><span class="sxs-lookup"><span data-stu-id="fdecc-265">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="fdecc-266">Un service peut avoir de nombreux cours, est une propriété de navigation du cours :</span><span class="sxs-lookup"><span data-stu-id="fdecc-266">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="fdecc-267">Par convention, Entity Framework permet la suppression en cascade pour les clés étrangères non nullables et pour les relations plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="fdecc-267">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="fdecc-268">Cela peut entraîner des règles de suppression en cascade circulaire, ce qui provoquent une exception lorsque vous essayez d’ajouter une migration.</span><span class="sxs-lookup"><span data-stu-id="fdecc-268">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="fdecc-269">Par exemple, si vous n’avez pas défini la propriété Department.InstructorID Nullable, EF serait configurer une règle de suppression en cascade pour supprimer le formateur lorsque vous supprimez le service, qui n’est pas ce que vous voulez effectuer.</span><span class="sxs-lookup"><span data-stu-id="fdecc-269">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the instructor when you delete the department, which is not what you want to have happen.</span></span> <span data-ttu-id="fdecc-270">Si vos règles d’entreprise nécessaire le `InstructorID` propriété soit non nullable, vous devez utiliser l’instruction API fluent suivante pour désactiver la suppression en cascade sur la relation :</span><span class="sxs-lookup"><span data-stu-id="fdecc-270">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-the-enrollment-entity"></a><span data-ttu-id="fdecc-271">Modifier l’entité de l’inscription</span><span class="sxs-lookup"><span data-stu-id="fdecc-271">Modify the Enrollment entity</span></span>

![Entité de l’inscription](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="fdecc-273">Dans *Models/Enrollment.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="fdecc-273">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

<span data-ttu-id="fdecc-274">[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-274">[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="fdecc-275">Propriétés de navigation et de clé étrangère</span><span class="sxs-lookup"><span data-stu-id="fdecc-275">Foreign key and navigation properties</span></span>

<span data-ttu-id="fdecc-276">Les propriétés de clé étrangère et les propriétés de navigation reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="fdecc-276">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="fdecc-277">Un enregistrement d’inscription est un cours unique, donc il est un `CourseID` propriété de clé étrangère et un `Course` propriété de navigation :</span><span class="sxs-lookup"><span data-stu-id="fdecc-277">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="fdecc-278">Un enregistrement d’inscription est pour un étudiant unique, donc il est un `StudentID` propriété de clé étrangère et un `Student` propriété de navigation :</span><span class="sxs-lookup"><span data-stu-id="fdecc-278">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="fdecc-279">Relations plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="fdecc-279">Many-to-Many Relationships</span></span>

<span data-ttu-id="fdecc-280">Il existe une relation plusieurs-à-plusieurs entre les entités étudiant et cours et l’entité d’inscription fonctionne comme une table de jointure plusieurs-à-plusieurs *avec une charge utile* dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-280">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="fdecc-281">« Avec une charge utile » signifie que la table Enrollment contient des données supplémentaires en plus des clés étrangères des tables jointes (dans ce cas, une clé primaire et une propriété de niveau).</span><span class="sxs-lookup"><span data-stu-id="fdecc-281">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="fdecc-282">L’illustration suivante montre l’aspect de ces relations dans un diagramme d’entité.</span><span class="sxs-lookup"><span data-stu-id="fdecc-282">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="fdecc-283">(Ce diagramme a été généré à l’aide de l’Entity Framework Power Tools pour EF 6.x ; création du diagramme ne fait pas partie de ce didacticiel, il est uniquement utilisé ici à titre d’illustration.)</span><span class="sxs-lookup"><span data-stu-id="fdecc-283">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Cours de l’étudiant plusieurs à plusieurs relations](complex-data-model/_static/student-course.png)

<span data-ttu-id="fdecc-285">Chaque ligne de relation comporte un 1 à une extrémité et un astérisque (*) à l’autre, qui indique une relation un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="fdecc-285">Each relationship line has a 1 at one end and an asterisk (*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="fdecc-286">Si la table Enrollment n’a pas été incluent des informations de catégorie, il faudrait uniquement contenir les deux clés étrangères CourseID et StudentID.</span><span class="sxs-lookup"><span data-stu-id="fdecc-286">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="fdecc-287">Dans ce cas, il serait une table de jointure plusieurs-à-plusieurs sans la charge utile (ou une table de jonction pure) dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-287">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="fdecc-288">Les entités du formateur et de cours ont ce type de relation plusieurs-à-plusieurs, et l’étape suivante consiste à créer une classe d’entité pour fonctionner comme une table de jonction sans la charge utile.</span><span class="sxs-lookup"><span data-stu-id="fdecc-288">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="fdecc-289">(EF 6.x prend en charge n’est pas le cas de tables de jointure implicite pour les relations plusieurs-à-plusieurs, mais EF Core.</span><span class="sxs-lookup"><span data-stu-id="fdecc-289">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core does not.</span></span> <span data-ttu-id="fdecc-290">Pour plus d’informations, consultez la [discussion dans le référentiel GitHub de noyaux EF](https://github.com/aspnet/EntityFramework/issues/1368).)</span><span class="sxs-lookup"><span data-stu-id="fdecc-290">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span> 

## <a name="the-courseassignment-entity"></a><span data-ttu-id="fdecc-291">L’entité CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="fdecc-291">The CourseAssignment entity</span></span>

![Entité de CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="fdecc-293">Créer *Models/CourseAssignment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="fdecc-293">Create *Models/CourseAssignment.cs* with the following code:</span></span>

<span data-ttu-id="fdecc-294">[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-294">[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]</span></span>

### <a name="join-entity-names"></a><span data-ttu-id="fdecc-295">Joindre des noms d’entité</span><span class="sxs-lookup"><span data-stu-id="fdecc-295">Join entity names</span></span>

<span data-ttu-id="fdecc-296">Une table de jointure est requis dans la base de données pour la relation plusieurs-à-plusieurs de formateur à cours, et il doit être représenté par un jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="fdecc-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="fdecc-297">Il est courant de nommer une entité de jointure `EntityName1EntityName2`, ce qui serait dans ce cas `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="fdecc-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="fdecc-298">Toutefois, nous vous recommandons de choisir un nom qui décrit la relation.</span><span class="sxs-lookup"><span data-stu-id="fdecc-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="fdecc-299">Modèles de données démarrent simple et la croissance, avec des jointures non-charge fréquemment mise en route charges utiles ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="fdecc-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="fdecc-300">Si vous démarrez avec un nom descriptif d’entité, vous n’aurez pas à le modifier ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="fdecc-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="fdecc-301">Dans l’idéal, l’entité de jointure aurait son propre nom (word éventuellement unique) naturelle dans le domaine d’entreprise.</span><span class="sxs-lookup"><span data-stu-id="fdecc-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="fdecc-302">Par exemple, la documentation et les clients pu être liées par le biais des évaluations.</span><span class="sxs-lookup"><span data-stu-id="fdecc-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="fdecc-303">Pour cette relation, `CourseAssignment` est un meilleur choix que `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="fdecc-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="fdecc-304">Clé composite</span><span class="sxs-lookup"><span data-stu-id="fdecc-304">Composite key</span></span>

<span data-ttu-id="fdecc-305">Étant donné que les clés étrangères ne sont pas nullables et ensemble identifie de façon unique identifier chaque ligne de la table, il n’est pas nécessaire pour une clé primaire distincte.</span><span class="sxs-lookup"><span data-stu-id="fdecc-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there is no need for a separate primary key.</span></span> <span data-ttu-id="fdecc-306">Le *InstructorID* et *CourseID* propriétés doivent fonctionner comme une clé primaire composite.</span><span class="sxs-lookup"><span data-stu-id="fdecc-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="fdecc-307">La seule façon pour identifier les clés primaires composites EF est à l’aide de la *API fluent* (il ne peut pas être effectuée à l’aide d’attributs).</span><span class="sxs-lookup"><span data-stu-id="fdecc-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="fdecc-308">Vous allez apprendre à configurer la clé primaire composite dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="fdecc-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="fdecc-309">La clé composite permet de s’assurer que vous pouvez avoir plusieurs lignes pour un cours et plusieurs lignes pour un formateur, vous ne peut pas avoir plusieurs lignes pour le même formateur et de cours.</span><span class="sxs-lookup"><span data-stu-id="fdecc-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="fdecc-310">Le `Enrollment` jointure entité définit sa propre clé primaire, les doublons de ce type sont possibles.</span><span class="sxs-lookup"><span data-stu-id="fdecc-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="fdecc-311">Pour éviter ces doublons, vous pourriez ajouter un index unique sur les champs de clé étrangères, ou configurez `Enrollment` avec une clé primaire composite similaire à `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="fdecc-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="fdecc-312">Pour plus d’informations, consultez [index](https://docs.efproject.net/en/latest/modeling/indexes.html).</span><span class="sxs-lookup"><span data-stu-id="fdecc-312">For more information, see [Indexes](https://docs.efproject.net/en/latest/modeling/indexes.html).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="fdecc-313">Mettre à jour le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="fdecc-313">Update the database context</span></span>

<span data-ttu-id="fdecc-314">Ajoutez le code en surbrillance suivant à la *Data/SchoolContext.cs* fichier :</span><span class="sxs-lookup"><span data-stu-id="fdecc-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

<span data-ttu-id="fdecc-315">[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-315">[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]</span></span>

<span data-ttu-id="fdecc-316">Ce code ajoute de nouvelles entités et configure la clé primaire de l’entité CourseAssignment composite.</span><span class="sxs-lookup"><span data-stu-id="fdecc-316">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="fdecc-317">Alternative d’API Fluent aux attributs</span><span class="sxs-lookup"><span data-stu-id="fdecc-317">Fluent API alternative to attributes</span></span>

<span data-ttu-id="fdecc-318">Le code dans le `OnModelCreating` méthode de la `DbContext` classe utilise le *API fluent* pour configurer le comportement EF.</span><span class="sxs-lookup"><span data-stu-id="fdecc-318">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="fdecc-319">L’API est appelée « fluent », car il est souvent utilisée par tirage une série d’appels de méthode ensemble dans une instruction unique, comme dans cet exemple à partir de la [EF documentations](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration):</span><span class="sxs-lookup"><span data-stu-id="fdecc-319">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="fdecc-320">Dans ce didacticiel, vous utilisez l’API fluent uniquement pour le mappage de base de données que vous ne pouvez pas faire avec des attributs.</span><span class="sxs-lookup"><span data-stu-id="fdecc-320">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="fdecc-321">Toutefois, vous pouvez également utiliser l’API fluent pour spécifier la majeure partie de la mise en forme, les règles de validation et mappage que vous pouvez effectuer à l’aide d’attributs.</span><span class="sxs-lookup"><span data-stu-id="fdecc-321">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="fdecc-322">Certains attributs, tels que `MinimumLength` ne peut pas être appliqué avec l’API fluent.</span><span class="sxs-lookup"><span data-stu-id="fdecc-322">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="fdecc-323">Comme mentionné précédemment, `MinimumLength` ne change pas le schéma, elle s’applique uniquement une règle de validation côté client et le serveur.</span><span class="sxs-lookup"><span data-stu-id="fdecc-323">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="fdecc-324">Certains développeurs préfèrent utiliser l’API fluent exclusivement afin qu’ils peuvent conserver leurs classes d’entité « propre ».</span><span class="sxs-lookup"><span data-stu-id="fdecc-324">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="fdecc-325">Vous pouvez combiner des attributs et des API fluent si vous voulez, et il existe quelques personnalisations peuvent uniquement être effectuées à l’aide des API fluent, mais en général la pratique recommandée consiste à choisir l’une de ces deux approches et utilisez ce constamment autant que possible.</span><span class="sxs-lookup"><span data-stu-id="fdecc-325">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="fdecc-326">Si vous n’utilisez pas les deux, notez que partout où il existe un conflit, API Fluent remplace les attributs.</span><span class="sxs-lookup"><span data-stu-id="fdecc-326">If you do use both, note that wherever there is a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="fdecc-327">Pour plus d’informations sur les attributs et l’API fluent, consultez [méthodes de configuration](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration).</span><span class="sxs-lookup"><span data-stu-id="fdecc-327">For more information about attributes vs. fluent API, see [Methods of configuration](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="fdecc-328">Entité diagramme montrant les relations</span><span class="sxs-lookup"><span data-stu-id="fdecc-328">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="fdecc-329">L’illustration suivante montre le diagramme Entity Framework Power Tools créer pour le modèle School terminé.</span><span class="sxs-lookup"><span data-stu-id="fdecc-329">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Diagramme d’entité](complex-data-model/_static/diagram.png)

<span data-ttu-id="fdecc-331">Outre les lignes de relation un-à-plusieurs (1 à \*), vous pouvez voir ici la ligne de relation d’un-à-zéro-ou-un (1 à 0.. 1) entre les entités du formateur et OfficeAssignment et la ligne zéro-ou-relation un-à-plusieurs (0.. 1 à *) entre le Entités du formateur et de service.</span><span class="sxs-lookup"><span data-stu-id="fdecc-331">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to *) between the Instructor and Department entities.</span></span>

## <a name="seed-the-database-with-test-data"></a><span data-ttu-id="fdecc-332">Valeur initiale de la base de données de Test</span><span class="sxs-lookup"><span data-stu-id="fdecc-332">Seed the Database with Test Data</span></span>

<span data-ttu-id="fdecc-333">Remplacez le code dans le *Data/DbInitializer.cs* fichier avec le code suivant afin de fournir des données de la valeur de départ pour les nouvelles entités que vous avez créé.</span><span class="sxs-lookup"><span data-stu-id="fdecc-333">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

<span data-ttu-id="fdecc-334">[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-334">[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]</span></span>

<span data-ttu-id="fdecc-335">Comme vous l’avez vu dans le premier didacticiel, la majeure partie de ce code crée des objets d’entité simplement et charge les exemples de données dans les propriétés requises pour le test.</span><span class="sxs-lookup"><span data-stu-id="fdecc-335">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="fdecc-336">Notez la façon dont sont gérées les relations plusieurs-à-plusieurs : le code crée des relations en créant des entités dans le `Enrollments` et `CourseAssignment` joindre des jeux d’entités.</span><span class="sxs-lookup"><span data-stu-id="fdecc-336">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="fdecc-337">Ajouter une migration</span><span class="sxs-lookup"><span data-stu-id="fdecc-337">Add a migration</span></span>

<span data-ttu-id="fdecc-338">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="fdecc-338">Save your changes and build the project.</span></span> <span data-ttu-id="fdecc-339">Ouvrez la fenêtre de commande dans le dossier du projet, puis entrez la `migrations add` commande (ne le faites pas la commande de base de données de mise à jour encore) :</span><span class="sxs-lookup"><span data-stu-id="fdecc-339">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```console
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="fdecc-340">Vous obtenez un avertissement concernant les pertes de données possibles.</span><span class="sxs-lookup"><span data-stu-id="fdecc-340">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="fdecc-341">Si vous avez essayé d’exécuter le `database update` commande à ce stade (ne le faites pas encore), vous obtenez l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="fdecc-341">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="fdecc-342">L’instruction ALTER TABLE est en conflit avec la contrainte de clé étrangère « FK_dbo. Course_dbo. Department_DepartmentID ».</span><span class="sxs-lookup"><span data-stu-id="fdecc-342">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="fdecc-343">Le conflit s’est produite dans la base de données « ContosoUniversity », table « dbo. Département », colonne « DepartmentID ».</span><span class="sxs-lookup"><span data-stu-id="fdecc-343">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="fdecc-344">Parfois, lorsque vous exécutez des migrations avec des données existantes, vous devez insérer des données de stub dans la base de données pour répondre aux contraintes de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="fdecc-344">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="fdecc-345">Le code généré dans le `Up` méthode ajoute une clé étrangère DepartmentID non nullable pour la table Course.</span><span class="sxs-lookup"><span data-stu-id="fdecc-345">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="fdecc-346">S’il existe déjà lignes dans la table Course lorsque le code s’exécute, le `AddColumn` opération échoue, car SQL Server ne sait pas quelle valeur à placer dans la colonne ne peut pas être null.</span><span class="sxs-lookup"><span data-stu-id="fdecc-346">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="fdecc-347">Pour ce didacticiel, vous allez exécuter la migration sur une base de données, mais dans une application de production, vous devez effectuer la migration de gérer les données existantes, afin des instructions suivantes montrent un exemple de procédure à suivre.</span><span class="sxs-lookup"><span data-stu-id="fdecc-347">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="fdecc-348">Pour rendre cette migration fonctionne avec des données existantes, que vous devez modifier le code pour permettre à la nouvelle colonne à une valeur par défaut et créer un service de stub nommé « Temp » en tant que le service par défaut.</span><span class="sxs-lookup"><span data-stu-id="fdecc-348">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="fdecc-349">Par conséquent, cours lignes existantes sont tous les liées au service « Temp » après le `Up` s’exécute la méthode.</span><span class="sxs-lookup"><span data-stu-id="fdecc-349">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="fdecc-350">Ouvrez le *{timestamp}_ComplexDataModel.cs* fichier.</span><span class="sxs-lookup"><span data-stu-id="fdecc-350">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span> 

* <span data-ttu-id="fdecc-351">Commentez la ligne de code qui ajoute la colonne DepartmentID de la table Course.</span><span class="sxs-lookup"><span data-stu-id="fdecc-351">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  <span data-ttu-id="fdecc-352">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-352">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]</span></span>

* <span data-ttu-id="fdecc-353">Ajoutez le code en surbrillance suivant après le code qui crée la table Department :</span><span class="sxs-lookup"><span data-stu-id="fdecc-353">Add the following highlighted code after the code that creates the Department table:</span></span>

  <span data-ttu-id="fdecc-354">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]</span><span class="sxs-lookup"><span data-stu-id="fdecc-354">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]</span></span>

<span data-ttu-id="fdecc-355">Dans une application de production, vous devez écrire le code ou des scripts pour ajouter des lignes de service et de relations avec les lignes de cours pour les nouvelles lignes de service.</span><span class="sxs-lookup"><span data-stu-id="fdecc-355">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="fdecc-356">Vous ne sont plus devez alors le service « Temp » ou la valeur par défaut sur la colonne Course.DepartmentID.</span><span class="sxs-lookup"><span data-stu-id="fdecc-356">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="fdecc-357">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="fdecc-357">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string-and-update-the-database"></a><span data-ttu-id="fdecc-358">Modifier la chaîne de connexion et de mettre à jour de la base de données</span><span class="sxs-lookup"><span data-stu-id="fdecc-358">Change the connection string and update the database</span></span>

<span data-ttu-id="fdecc-359">Vous avez maintenant nouveau code la `DbInitializer` classe qui ajoute des données de départ pour les nouvelles entités à une base de données vide.</span><span class="sxs-lookup"><span data-stu-id="fdecc-359">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="fdecc-360">Pour rendre EF à créer une base de données vide, modifier le nom de la base de données dans la chaîne de connexion dans *appsettings.json* ContosoUniversity3 ou un autre nom que vous avez utilisé sur l’ordinateur que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="fdecc-360">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="fdecc-361">Enregistrer les modifications à votre *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fdecc-361">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="fdecc-362">Comme alternative à la modification du nom de la base de données, vous pouvez supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-362">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="fdecc-363">Utilisez **l’Explorateur d’objets SQL Server** (SSOX) ou le `database drop` commande CLI :</span><span class="sxs-lookup"><span data-stu-id="fdecc-363">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
> ```console
> dotnet ef database drop
> ```

<span data-ttu-id="fdecc-364">Une fois que vous avez modifié le nom de la base de données ou supprimés de la base de données, exécuter le `database update` dans la fenêtre de commande à exécuter les migrations.</span><span class="sxs-lookup"><span data-stu-id="fdecc-364">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="fdecc-365">Exécuter l’application pour provoquer le `DbInitializer.Initialize` méthode à exécuter et de remplir la nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-365">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="fdecc-366">Ouvrez la base de données dans SSOX comme vous l’avez fait précédemment, puis développez le **Tables** nœud pour voir que toutes les tables ont été créés.</span><span class="sxs-lookup"><span data-stu-id="fdecc-366">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="fdecc-367">(Si vous avez toujours SSOX ouvrir à partir de l’état antérieur, cliquez sur le bouton Actualiser).</span><span class="sxs-lookup"><span data-stu-id="fdecc-367">(If you still have SSOX open from the earlier time, click the Refresh button.)</span></span>

![Tables de SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="fdecc-369">Exécutez l’application pour déclencher le code de l’initialiseur qui alimente la base de données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-369">Run the application to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="fdecc-370">Avec le bouton droit le **CourseAssignment** de table et sélectionnez **des données d’affichage** pour vérifier qu’il comporte des données.</span><span class="sxs-lookup"><span data-stu-id="fdecc-370">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![Données CourseAssignment dans SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="summary"></a><span data-ttu-id="fdecc-372">Résumé</span><span class="sxs-lookup"><span data-stu-id="fdecc-372">Summary</span></span>

<span data-ttu-id="fdecc-373">Vous avez maintenant un modèle de données plus complexe et de la base de données correspondante.</span><span class="sxs-lookup"><span data-stu-id="fdecc-373">You now have a more complex data model and corresponding database.</span></span> <span data-ttu-id="fdecc-374">Dans ce didacticiel, vous en apprendrez davantage sur la façon d’accéder aux données associées.</span><span class="sxs-lookup"><span data-stu-id="fdecc-374">In the following tutorial, you'll learn more about how to access related data.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="fdecc-375">[Précédent](migrations.md)
[Suivant](read-related-data.md)</span><span class="sxs-lookup"><span data-stu-id="fdecc-375">[Previous](migrations.md)
[Next](read-related-data.md)</span></span>  