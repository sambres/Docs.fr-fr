---
title: Pages Razor avec Entity Framework Core - Didacticiel 1 sur 8
author: rick-anderson
description: "Montre comment créer une application Pages Razor à l’aide d’Entity Framework Core."
manager: wpickett
ms.author: riande
ms.date: 11/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/intro
ms.openlocfilehash: 091f34da347d52ba8e3e87779ddc4aeb790c2800
ms.sourcegitcommit: 18d1dc86770f2e272d93c7e1cddfc095c5995d9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2018
---
# <a name="getting-started-with-razor-pages-and-entity-framework-core-using-visual-studio-1-of-8"></a>Bien démarrer avec les pages Razor et Entity Framework Core à l’aide de Visual Studio (1 sur 8)

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET Core 2.0 MVC à l’aide d’Entity Framework (EF) Core 2.0 et Visual Studio 2017.

L’exemple d’application est un site web pour une université Contoso fictive. Il comprend des fonctionnalités telles que l’admission des étudiants, la création des cours et les affectations des formateurs. Cette page est la première d’une série de didacticiels qui expliquent comment générer l’exemple d’application Contoso University.

[Télécharger ou afficher l’application complète.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Télécharger les instructions](xref:tutorials/index#how-to-download-a-sample).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

Connaissance des [Pages Razor](xref:mvc/razor-pages/index). Les programmeurs débutants doivent lire [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start) avant de démarrer cette série.

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous rencontrez un problème que vous ne parvenez pas à résoudre, vous trouverez généralement la solution en comparant votre code à la [phase terminée](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots) ou au [projet terminé](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu-final). Pour obtenir une liste d’erreurs courantes et comment les résoudre, consultez [la section Dépannage du dernier didacticiel de la série](xref:data/ef-mvc/advanced#common-errors). Si vous n’y trouvez pas ce dont vous avez besoin, vous pouvez publier une question sur [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Cette série de didacticiels s’appuie sur les actions effectuées dans les didacticiels précédents. Pensez à enregistrer une copie du projet après avoir fini chaque didacticiel. Si vous rencontrez des problèmes, vous pourrez ainsi recommencer à partir du didacticiel précédent, au lieu de revenir au début. Vous pouvez également télécharger une [phase terminée](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots) et recommencer à l’aide de cette phase terminée.

## <a name="the-contoso-university-web-app"></a>L’application web Contoso University

L’application générée dans ces didacticiels est le site web de base d’une université.

Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs. Voici quelques-uns des écrans créés dans le didacticiel.

![Page d’index des étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

Le style de l’interface utilisateur de ce site est proche de ce qui est généré par les modèles prédéfinis. Le didacticiel est axé sur Core EF avec des pages Razor, et non sur l’interface utilisateur.

## <a name="create-a-razor-pages-web-app"></a>Créer une application web Pages Razor

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Créez une application web ASP.NET Core. Nommez le projet **ContosoUniversity**. Il est important de nommer le projet *ContosoUniversity* afin que les espaces de noms correspondent quand le code est copié/collé.
 ![Nouvelle application web ASP.NET Core](intro/_static/np.png)
* Sélectionnez **ASP.NET Core 2.0** dans la liste déroulante, puis sélectionnez **Application web**.
 ![Application web (pages Razor)](../../mvc/razor-pages/index/_static/np2.png)

Appuyez sur **F5** pour exécuter l’application en mode débogage ou sur **Ctrl+F5** pour l’exécuter sans attachement du débogueur

## <a name="set-up-the-site-style"></a>Définir le style de site

Quelques changements permettent de définir le menu, la disposition et la page d’accueil du site.

Ouvrez *Pages/_Layout.cshtml* et apportez les modifications suivantes :

* Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ». Il y a trois occurrences.

* Ajoutez des entrées de menu pour **Students**, **Courses**, **Instructors** et **Departments**, et supprimez l’entrée de menu **Contact**.

Les modifications sont mises en surbrillance. (Tout le balisage n’est *pas* affiché.)

[!code-html[](intro/samples/cu/Pages/_Layout.cshtml?highlight=6,29,35-38,47&range=1-50)]

Dans *Pages/Index.cshtml*, remplacez le contenu du fichier par le code suivant afin de remplacer le texte sur ASP.NET et MVC par le texte concernant cette application :

[!code-html[](intro/samples/cu/Pages/Index.cshtml)]

Appuyez sur CTRL+F5 pour exécuter le projet. La page d’accueil s’affiche avec les onglets créés dans les didacticiels suivants :

![Page d’accueil de Contoso University](intro/_static/home-page.png)

## <a name="create-the-data-model"></a>Créer le modèle de données

Créez des classes d’entités pour l’application Contoso University. Commencez par les trois entités suivantes :

![Diagramme de modèle de données Cours-Inscription-Étudiant](intro/_static/data-model-diagram.png)

Il existe une relation un-à-plusieurs entre les entités `Student` et `Enrollment`. Il existe une relation un-à-plusieurs entre les entités `Course` et `Enrollment`. Un étudiant peut s’inscrire à autant de cours qu’il le souhaite. Un cours peut avoir une quantité illimitée d’élèves inscrits.

Dans les sections suivantes, une classe pour chacune de ces entités est créée.

### <a name="the-student-entity"></a>L’entité Student

![Diagramme de l’entité Student](intro/_static/student-entity.png)

Créez un dossier *Models*. Dans le dossier *Models*, créez un fichier de classe nommé *Student.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe. Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire.

La propriété `Enrollments` est une propriété de navigation. Les propriétés de navigation établissent des liaisons à d’autres entités qui sont associées à cette entité. Ici, la propriété `Enrollments` d’un `Student entity` contient toutes les entités `Enrollment` associées à ce `Student`. Par exemple, si une ligne Student dans la base de données a deux lignes Enrollment associées, la propriété de navigation `Enrollments` contient ces deux entités `Enrollment`. Une ligne `Enrollment` associée est une ligne qui contient la valeur de clé primaire de cette étudiant dans la colonne `StudentID`. Par exemple, supposez que l’étudiant avec ID=1 a deux lignes dans la table `Enrollment`. La table `Enrollment` a deux lignes avec `StudentID` = 1. `StudentID` est une clé étrangère dans la table `Enrollment` qui spécifie l’étudiant dans la table `Student`.

Si une propriété de navigation peut contenir plusieurs entités, la propriété de navigation doit être un type de liste, tel que `ICollection<T>`. Vous pouvez spécifier `ICollection<T>`, ou un type tel que `List<T>` ou `HashSet<T>`. Quand vous utilisez `ICollection<T>`, EF Core crée une collection `HashSet<T>` par défaut. Les propriétés de navigation qui contiennent plusieurs entités proviennent de relations plusieurs-à-plusieurs et un-à-plusieurs.

### <a name="the-enrollment-entity"></a>L’entité Enrollment

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

Dans le dossier *Models*, créez un fichier *Enrollment.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

La propriété `EnrollmentID` est la clé primaire. Cette entité utilise le modèle `classnameID` au lieu de `ID` comme l’entité `Student`. En général, les développeurs choisissent un modèle et l’utilisent dans tout le modèle de données. Dans un prochain didacticiel, nous verrons qu’utiliser ID sans classname simplifie l’implémentation de l’héritage dans le modèle de données.

La propriété `Grade` est un `enum`. Le point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade` est nullable. Une note (Grade) qui a la valeur null est différent d’une note égale à zéro : null signifie qu’une note n’est pas connue ou n’a pas encore été affectée.

La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`. Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`. L’entité `Student` diffère de la propriété de navigation `Student.Enrollments`, qui contient plusieurs entités `Enrollment`.

La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`. Une entité `Enrollment` est associée à une entité `Course`.

EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`. Par exemple, `StudentID` pour la propriété de navigation `Student`, puisque la clé primaire de l’entité `Student` est `ID`. Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`. Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.

### <a name="the-course-entity"></a>L’entité Course

![Diagramme de l’entité Course](intro/_static/course-entity.png)

Dans le dossier *Models*, créez un fichier *Course.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

La propriété `Enrollments` est une propriété de navigation. Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.

L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.

## <a name="create-the-schoolcontext-db-context"></a>Créer le contexte de base de données SchoolContext

La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données spécifié est la classe de contexte de base de données. Le contexte de données est dérivé de `Microsoft.EntityFrameworkCore.DbContext`. Il spécifie les entités qui sont incluses dans le modèle de données. Dans ce projet, la classe se nomme `SchoolContext`.

Dans le dossier du projet, créez un dossier nommé *Data*.

Dans le dossier *Data*, créez *SchoolContext.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Ce code crée une propriété `DbSet` pour chaque jeu d’entités. Dans la terminologie d’EF Core :

* Un jeu d’entités correspond généralement à une table de base de données.
* Une entité correspond à une ligne dans la table.

`DbSet<Enrollment>` et `DbSet<Course>` peut être omis. EF Core les inclut implicitement, car l’entité `Student` référence l’entité `Enrollment`, et l’entité `Enrollment` référence l’entité `Course`. Pour ce didacticiel, conservez `DbSet<Enrollment>` et `DbSet<Course>` dans le `SchoolContext`.

Quand la base de données est créée, EF Core crée des tables dont les noms sont identiques à ceux de la propriété `DbSet`. Les noms des propriétés pour les collections sont généralement au pluriel (Students plutôt que Student). Les développeurs ne sont pas tous d’accord sur la nécessité d’utiliser des noms de tables au pluriel. Pour ces didacticiels, le comportement par défaut est remplacé en spécifiant des noms de tables au singulier dans le DbContext. Pour spécifier des noms de tables au singulier, ajoutez le code en surbrillance suivant :

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a>Inscrire le contexte avec l’injection de dépendances

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont affectés aux composants qui les nécessitent (par exemple les Pages Razor) par le biais de paramètres de constructeur. Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le didacticiel.

Pour inscrire `SchoolContext` en tant que service, ouvrez *Startup.cs* et ajoutez les lignes en surbrillance à la méthode `ConfigureServices`.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

Le nom de la chaîne de connexion est passé au contexte en appelant une méthode sur un objet `DbContextOptionsBuilder`. Pour le développement local, le [système de configuration ASP.NET Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du fichier *appsettings.json*.

Ajoutez des instructions `using` pour les espaces de noms `ContosoUniversity.Data` et `Microsoft.EntityFrameworkCore`. Générez le projet.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Ouvrez le fichier *appsettings.json* et ajoutez une chaîne de connexion comme indiqué dans le code suivant :

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

La chaîne de connexion précédente utilise `ConnectRetryCount=0` pour empêcher le blocage de [SQLClient](https://docs.microsoft.com/dotnet/framework/data/adonet/ef/sqlclient-for-the-entity-framework).

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

La chaîne de connexion spécifie une base de données SQL Server LocalDB. LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, LocalDB crée des fichiers de base de données *.mdf* dans le répertoire `C:/Users/<user>`.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Ajouter du code pour initialiser la base de données avec des données de test

EF Core crée une base de données vide. Dans cette section, une méthode *Seed* est écrite afin de la renseigner avec des données de test.

Dans le dossier *Data*, créez un fichier de classe nommé *DbInitializer.cs* et ajoutez le code suivant :

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Le code vérifie s’il existe des étudiants dans la base de données. S’il n’y en a pas, la base de données est amorcée avec des données de test. Il charge les données de test dans des tableaux plutôt que des collections `List<T>` afin d’optimiser les performances.

La méthode `EnsureCreated` crée automatiquement la base de données pour le contexte de base de données. Si la base de données existe, `EnsureCreated` retourne sans modifier la base de données.

Dans *Program.cs*, modifiez la méthode `Main` pour effectuer les opérations suivantes :

* Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendances.
* Appeler la méthode seed et la passer au contexte.
* Supprimer le contexte une fois la méthode seed terminée.

Le code suivant montre le fichier *Program.cs* mis à jour.

[!code-csharp[Main](intro/samples/cu/ProgramOriginal.cs?name=snippet)]

Lors de la première exécution de l’application, la base de données est créée et amorcée avec des données de test. Quand le modèle de données est mis à jour :
* Supprimez la base de données.
* Mettez à jour la méthode seed.
* Exécutez l’application, et une nouvelle base de données amorcée est créée. 

Dans les didacticiels suivants, la base de données est mise à jour quand le modèle de données change, sans supprimer et recréer la base de données.

<a name="pmc"></a>
## <a name="add-scaffold-tooling"></a>Ajouter des outils de génération de modèles automatique

Dans cette section, nous utilisons la console du Gestionnaire de package pour ajouter le package de génération de code web Visual Studio. Ce package est nécessaire à l’exécution du moteur de génération de modèles automatique.

Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package**.

Dans la console du Gestionnaire de package, entrez les commandes suivantes :

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Utils
```

La commande précédente ajoute les packages NuGet au fichier *.csproj :

[!code-csharp[Main](intro/samples/cu/ContosoUniversity1_csproj.txt?highlight=7-8)]

<a name="scaffold"></a>
## <a name="scaffold-the-model"></a>Effectuer la génération de modèles automatique pour le modèle

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).
* Exécutez les commandes suivantes :


 ```console
dotnet restore
dotnet aspnet-codegenerator razorpage -m Student -dc SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
 ```
 
Si l’erreur suivante est générée :

```text
Unhandled Exception: System.IO.FileNotFoundException: 
Could not load file or assembly 
'Microsoft.VisualStudio.Web.CodeGeneration.Utils, 
Version=2.0.0.0, Culture=neutral, PublicKeyToken=adb9793829ddae60'.
The system cannot find the file specified.
```

Réexécutez la commande et laissez un commentaire au bas de la page.

Si vous obtenez l’erreur :
  ```
No executable found matching command "dotnet-aspnet-codegenerator"
  ```

Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).


Générez le projet. La build génère des erreurs telles que celle-ci :

 `1>Pages\Students\Index.cshtml.cs(26,38,26,45): error CS1061: 'SchoolContext' does not contain a definition for 'Student'`

 Remplacez globalement `_context.Student` par `_context.Students` (autrement dit, ajoutez un « s » à `Student`). 7 occurrences sont trouvées et mises à jour. Nous espérons résoudre [ce bogue](https://github.com/aspnet/Scaffolding/issues/633) dans la prochaine version.

[!INCLUDE[model4tbl](../../includes/RP/model4tbl.md)]

 <a name="test"></a>
### <a name="test-the-app"></a>Tester l’application

Exécutez l’application et sélectionnez le lien **Students**. Le lien **Students** figure en haut de la page, mais cela dépend de la largeur du navigateur. Si le lien **Students** n’est pas visible, cliquez sur l’icône de navigation dans le coin supérieur droit.

![Page d’accueil étroite de Contoso University](intro/_static/home-page-narrow.png)

Testez les liens **Create**, **Edit** et **Details**.

## <a name="view-the-db"></a>Afficher la base de données

Quand l’application est lancée, `DbInitializer.Initialize` appelle `EnsureCreated`. `EnsureCreated` détecte si la base de données existe et en crée une si nécessaire. Si la base de données ne contient aucun étudiant, la méthode `Initialize` en ajoute.

Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.
Dans SSOX, cliquez sur **(localdb)\MSSQLLocalDB > Bases de données > ContosoUniversity1**.

Développez le nœud **Tables**.

Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.

Les fichiers de base de données *.mdf* et *.ldf* se trouvent dans le dossier *C:\Utilisateurs\\<yourusername>*.

`EnsureCreated` est appelée au démarrage de l’application, ce qui active le flux de travail suivant :

* Supprimez la base de données.
* Modification du schéma de base de données (par exemple, ajout d’un champ `EmailAddress`).
* Exécution de l’application.

`EnsureCreated` crée une base de données avec la colonne `EmailAddress`.

## <a name="conventions"></a>Conventions

La quantité de code écrite pour qu’EF Core crée une base de données complète est minime en raison de l’utilisation des conventions, ou des hypothèses émises par EF Core.

* Les noms des propriétés `DbSet` sont utilisés comme noms de tables. Pour les entités non référencées par une propriété `DbSet`, les noms des classes d’entités sont utilisés comme noms de tables.

* Les noms des propriétés d’entités sont utilisées comme noms de colonnes.

* Les propriétés d’entités nommées ID ou classnameID sont reconnues comme des propriétés de clé primaire.

* Une propriété est interprétée comme propriété de clé étrangère si elle se nomme *<navigation property name><primary key property name>* (par exemple `StudentID` pour la propriété de navigation `Student`, puisque la clé primaire de l’entité `Student` est `ID`). Les propriétés de clé étrangère peuvent se nommer *<primary key property name>* (par exemple `EnrollmentID`, puisque la clé primaire de l’entité `Enrollment` est `EnrollmentID`).

Le comportement conventionnel peut être remplacé. Par exemple, les noms des tables peuvent être spécifiés explicitement, comme indiqué plus haut dans ce didacticiel. Les noms des colonnes peuvent être définis explicitement. Les clés primaires et les clés étrangères peuvent être définies explicitement.

## <a name="asynchronous-code"></a>Code asynchrone

La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.

Un serveur web a un nombre limité de threads disponibles, et dans les situations de forte charge tous les threads disponibles peuvent être en cours d’utilisation. Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés. Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent réellement aucun travail, car ils attendent que des E/S se terminent. Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin que le serveur puisse l’utiliser pour traiter d’autres requêtes. Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.

Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution. Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.

Dans le code suivant, le mot clé `async`, la valeur de retour `Task<T>`, le mot clé `await` et la méthode `ToListAsync` provoquent l’exécution asynchrone du code.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Le mot clé `async` fait en sorte que le compilateur :

  * Génère des rappels pour les parties du corps de méthode.
  * Crée automatiquement l’objet [Task](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task?view=netframework-4.7) qui est retourné. Pour plus d’informations, consultez [Type de retour Task](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Le type de retour implicite `Task` représente le travail en cours.

* Le mot clé `await` fait en sorte que le compilateur fractionne la méthode en deux parties. La première partie se termine par l’opération qui a démarré en mode asynchrone. La deuxième partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.

* `ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.

Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :

* Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone. Cela comprend `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`, mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.

* Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle. 

* Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.

Pour plus d’informations sur les méthodes asynchrones dans .NET, consultez [Vue d’ensemble d’async](https://docs.microsoft.com/dotnet/articles/standard/async).

Dans le didacticiel suivant, nous allons examiner les opérations CRUD de base (créer, lire, mettre à jour, supprimer).

>[!div class="step-by-step"]
[Suivant](xref:data/ef-rp/crud)
