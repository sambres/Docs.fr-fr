---
title: "ASP.NET Core MVC avec EF Core - Héritage - 9 sur 10"
author: tdykstra
description: "Ce didacticiel vous indiquera comment implémenter l’héritage dans le modèle de données en utilisant Entity Framework Core dans une application ASP.NET Core."
manager: wpickett
ms.author: tdykstra
ms.date: 03/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 985cc38b10ef830b8274e40ad5f7050157fd4d86
ms.sourcegitcommit: 18d1dc86770f2e272d93c7e1cddfc095c5995d9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2018
---
# <a name="inheritance---ef-core-with-aspnet-core-mvc-tutorial-9-of-10"></a>Héritage - Didacticiel EF Core avec ASP.NET Core MVC (9 sur 10)

De [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET Core MVC à l’aide d’Entity Framework Core et de Visual Studio. Pour obtenir des informations sur la série de didacticiels, consultez [le premier didacticiel de la série](intro.md).

Dans le didacticiel précédent, vous avez géré les exceptions d’accès concurrentiel. Ce didacticiel vous indiquera comment implémenter l’héritage dans le modèle de données.

En programmation orientée objet, vous pouvez utiliser l’héritage pour faciliter la réutilisation du code. Dans ce didacticiel, vous allez modifier les classes `Instructor` et `Student` afin qu’elles dérivent d’une classe de base `Person` qui contient des propriétés telles que `LastName`, communes aux formateurs et aux étudiants. Vous n’ajouterez ni ne modifierez aucune page web, mais vous modifierez une partie du code et ces modifications seront automatiquement répercutées dans la base de données.

## <a name="options-for-mapping-inheritance-to-database-tables"></a>Options pour mapper l’héritage aux tables de base de données

Les classes `Instructor` et `Student` du modèle de données School ont plusieurs propriétés identiques :

![Classes Student et Instructor](inheritance/_static/no-inheritance.png)

Supposons que vous souhaitez éliminer le code redondant pour les propriétés partagées par les entités `Instructor` et `Student`. Ou vous souhaitez écrire un service capable de mettre en forme les noms sans se soucier du fait que le nom provienne d’un formateur ou d’un étudiant. Vous pouvez créer une classe de base `Person` qui contient uniquement les propriétés partagées, puis paramétrer les classes `Instructor` et `Student` pour qu’elles héritent de cette classe de base, comme indiqué dans l’illustration suivante :

![Classes Student et Instructor dérivant de la classe Person](inheritance/_static/inheritance.png)

Il existe plusieurs façons de représenter cette structure d’héritage dans la base de données. Vous pouvez avoir une table de personnes qui inclut des informations sur les étudiants et les formateurs dans une table unique. Certaines des colonnes pourraient s’appliquer uniquement aux formateurs (HireDate), certaines uniquement aux étudiants (EnrollmentDate) et certaines aux deux (LastName, FirstName). En règle générale, vous pouvez avoir une colonne de discriminateur pour indiquer le type que chaque ligne représente. Par exemple, la colonne de discriminateur peut avoir « Instructor » pour les formateurs et « Student » pour les étudiants.

![Exemple TPH (table par hiérarchie)](inheritance/_static/tph.png)

Ce modèle de génération d’une structure d’héritage d’entité à partir d’une table de base de données unique porte le nom d’héritage TPH (table par hiérarchie).

Une alternative consiste à faire en sorte que la base de données ressemble plus à la structure d’héritage. Par exemple, vous pouvez avoir uniquement les champs de nom dans la table Person, et des tables Instructor et Student distinctes avec les champs de date.

![Héritage TPT (table par type)](inheritance/_static/tpt.png)

Ce modèle consistant à créer une table de base de données pour chaque classe d’entité est appelé héritage TPT (table par type).

Une autre option encore consiste à mapper tous les types non abstraits à des tables individuelles. Toutes les propriétés d’une classe, y compris les propriétés héritées, sont mappées aux colonnes de la table correspondante. Ce modèle porte le nom d’héritage TPC (table par classe concrète). Si vous avez implémenté l’héritage TPC pour les classes Person, Student et Instructor comme indiqué précédemment, les tables Student et Instructor ne seraient pas différentes avant et après l’implémentation de l’héritage.

Les modèles d’héritage TPC et TPH fournissent généralement de meilleures performances que les modèles d’héritage TPT, car les modèles TPT peuvent entraîner des requêtes de jointure complexes.

Ce didacticiel montre comment implémenter l’héritage TPH. TPH est le seul modèle d’héritage pris en charge par Entity Framework Core.  Vous allez créer une classe `Person`, modifier les classes `Instructor` et `Student` à dériver de `Person`, ajouter la nouvelle classe à `DbContext` et créer une migration.

> [!TIP] 
> Pensez à enregistrer une copie du projet avant d’apporter les modifications suivantes.  Ensuite, si vous rencontrez des problèmes et devez recommencer, il sera plus facile de démarrer à partir du projet enregistré que d’annuler les étapes effectuées pour ce didacticiel ou de retourner au début de la série entière.

## <a name="create-the-person-class"></a>Créer la classe Person

Dans le dossier Models, créez Person.cs et remplacez le code du modèle par le code suivant :

[!code-csharp[Main](intro/samples/cu/Models/Person.cs)]

## <a name="make-student-and-instructor-classes-inherit-from-person"></a>Paramétrer les classes Student et Instructor pour qu’elles héritent de Person

Dans *Instructor.cs*, dérivez la classe Instructor de la classe Person et supprimez les champs de clé et de nom. Le code ressemblera à l’exemple suivant :

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

Apportez les mêmes modifications dans *Student.cs*.

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-the-person-entity-type-to-the-data-model"></a>Ajouter le type d’entité Person au modèle de données

Ajoutez le type d’entité Person à *SchoolContext.cs*. Les nouvelles lignes apparaissent en surbrillance.

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

C’est là tout ce dont Entity Framework a besoin pour configurer l’héritage TPH (table par hiérarchie). Comme vous le verrez, lorsque la base de données sera mise à jour, elle aura une table Person à la place des tables Student et Instructor.

## <a name="create-and-customize-migration-code"></a>Créer et personnaliser le code de migration

Enregistrez vos modifications et générez le projet. Ensuite, ouvrez la fenêtre de commande dans le dossier du projet et entrez la commande suivante :

```console
dotnet ef migrations add Inheritance
```

N’exécutez pas encore la commande `database update`. Cette commande entraîne une perte de données, car elle supprime la table Instructor et renomme la table Student en Person. Vous devez fournir un code personnalisé pour préserver les données existantes.

Ouvrez *Migrations/\<horodatage>_Inheritance.cs* et remplacez la méthode `Up` par le code suivant :

[!code-csharp[Main](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

Ce code prend en charge les tâches de mise à jour de base de données suivantes :

* Supprime les contraintes de clé étrangère et les index qui pointent vers la table Student.

* Renomme la table Instructor en Person et apporte les modifications nécessaires pour qu’elle stocke les données des étudiants :

* Ajoute une EnrollmentDate nullable pour les étudiants.

* Ajoute la colonne Discriminator pour indiquer si une ligne est pour un étudiant ou un formateur.

* Rend HireDate nullable étant donné que les lignes d’étudiant n’ont pas de dates d’embauche.

* Ajoute un champ temporaire qui sera utilisé pour mettre à jour les clés étrangères qui pointent vers les étudiants. Lorsque vous copiez des étudiants dans la table Person, ils obtiennent de nouvelles valeurs de clés primaires.

* Copie des données à partir de la table Student dans la table Person. Cela entraîne l’affectation de nouvelles valeurs de clés primaires aux étudiants.

* Corrige les valeurs de clés étrangères qui pointent vers les étudiants.

* Crée de nouveau les index et les contraintes de clé étrangère, désormais pointées vers la table Person.

(Si vous aviez utilisé un GUID à la place d’un entier comme type de clé primaire, les valeurs des clés primaires des étudiants n’auraient pas changé, et plusieurs de ces étapes auraient pu être omises.)

Exécutez la commande `database update` :

```console
dotnet ef database update
```

(Dans un système de production, vous apporteriez les modifications correspondantes à la méthode `Down` au cas où vous auriez à l’utiliser pour revenir à la version précédente de la base de données. Pour ce didacticiel, vous n’utiliserez pas la méthode `Down`.)

> [!NOTE] 
> Vous pouvez obtenir d’autres erreurs en apportant des modifications au schéma dans une base de données qui comporte déjà des données. Si vous obtenez des erreurs de migration que vous ne pouvez pas résoudre, vous pouvez changer le nom de la base de données dans la chaîne de connexion ou supprimer la base de données. Avec une nouvelle base de données, il n’y a pas de données à migrer et la commande de mise à jour de base de données a plus de chances de s’exécuter sans erreur. Pour supprimer la base de données, utilisez SSOX ou exécutez la commande CLI `database drop`.

## <a name="test-with-inheritance-implemented"></a>Tester avec l’héritage implémenté

Exécutez l’application et essayez différentes pages. Tout fonctionne comme avant.

Dans l’**Explorateur d’objets SQL Server**, développez **Data Connections/SchoolContext** puis **Tables**, et vous constatez que les tables Student et Instructor ont été remplacées par une table Person. Ouvrez le concepteur de la table Person et vous constatez qu’elle possède toutes les colonnes qui existaient dans les tables Student et Instructor.

![Table Person dans SSOX](inheritance/_static/ssox-person-table.png)

Cliquez avec le bouton droit sur la table Person, puis cliquez sur **Afficher les données de la table** pour voir la colonne de discriminateur.

![Table Person dans SSOX - données de la table](inheritance/_static/ssox-person-data.png)

## <a name="summary"></a>Récapitulatif

Vous avez implémenté l’héritage TPH (table par hiérarchie) pour les classes `Person`, `Student` et `Instructor`. Pour plus d’informations sur l’héritage dans Entity Framework Core, consultez [Héritage](https://docs.microsoft.com/ef/core/modeling/inheritance). Dans le prochain didacticiel, vous allez apprendre à gérer divers scénarios Entity Framework relativement avancés.

>[!div class="step-by-step"]
[Précédent](concurrency.md)
[Suivant](advanced.md)  
