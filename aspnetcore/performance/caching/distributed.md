---
title: "Utilisation avec un cache distribué dans ASP.NET Core"
author: ardalis
description: "Apprenez à utiliser ASP.NET Core distribué mise en cache pour améliorer les performances de l’application et d’évolutivité, en particulier dans un environnement de batterie de serveurs cloud ou de serveur."
manager: wpickett
ms.author: riande
ms.date: 02/14/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: performance/caching/distributed
ms.openlocfilehash: 635c61cbb72a6a9eb822307bbc80936ee73bedc8
ms.sourcegitcommit: 7ac15eaae20b6d70e65f3650af050a7880115cbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="working-with-a-distributed-cache-in-aspnet-core"></a>Utilisation avec un cache distribué dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

Les caches distribués peuvent améliorer les performances et l’évolutivité des applications ASP.NET Core, en particulier lorsqu'elles sont hébergées dans un environnement de batteries de serveurs ou dans le "cloud". Cet article explique comment travailler avec les implémentations et les abstractions de cache distribué intégrées d’ASP.NET Core

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-is-a-distributed-cache"></a>Qu’est un cache distribué

Un cache distribué est partagé par plusieurs serveurs d’application (consultez [principes fondamentaux de la mise en cache](memory.md#caching-basics)). Les informations contenues dans le cache ne sont pas stockées dans la mémoire des serveurs de site web individuel, et les données mises en cache sont disponibles pour tous les serveurs de l’application. Cela présente plusieurs avantages :

1. Les données mises en cache sont cohérentes sur tous les serveurs web. Les résultats affichés pour les utilisateurs ne diffèrent pas selon le serveur web qui gère leur demande.

2. Les données mises en cache survivent au redémarrage d'un serveur web et aux redéploiements. Les serveurs Web individuels peuvent être supprimés ou ajoutés sans impact sur le cache.

3. Le magasin de données source a moins de demandes qui lui sont soumises (par rapport à plusieurs caches en mémoire ou pas de cache du tout).

> [!NOTE]
> Si vous utilisez un cache distribué sur SQL Server, certains de ces avantages ne sont vrais que si une instance distincte de la base de données est utilisée pour le cache et une autre pour les données sources de l’application.

Comme n’importe quel type de cache, un cache distribué peut considérablement améliorer la réactivité d’une application, car en général, les données peuvent être récupérées beaucoup plus rapidement à partir du cache qu’à partir d’une base de données relationnelle (ou un service web).

La configuration du cache est spécifique à l’implémentation. Cet article décrit comment configurer les caches distribués Redis et SQL Server. Indépendamment de l’implémentation sélectionnée, l’application interagit avec le cache à l’aide d’une interface `IDistributedCache` commune.

## <a name="the-idistributedcache-interface"></a>L’Interface IDistributedCache

L'interface `IDistributedCache` inclut des méthodes synchrones et asynchrones. L’interface autorise des éléments ajoutés, la récupération et la suppression de l’implémentation de cache distribué. L'interface `IDistributedCache` comprend les méthodes suivantes :

**Get, GetAsync**

Prend une clé de chaîne et récupère un élément mis en cache comme un `byte[]` si trouvé dans le cache.

**Set, SetAsync**

Ajoute un élément (comme `byte[]`) dans le cache à l’aide d’une clé de chaîne.

**Refresh, RefreshAsync**

Actualise un élément dans le cache en fonction de sa clé, réinitialise son délai d’expiration s'il est décalée (le cas échéant).

**Remove, RemoveAsync**

Supprime une entrée de cache en fonction de sa clé.

Pour utiliser l'interface `IDistributedCache` :

   1. Ajouter les packages NuGet nécessaires à votre fichier projet.

   2. Configurez l’implémentation spécifique de l'objet `IDistributedCache` dans votre classe `Startup` avec la (méthode) `ConfigureServices` et ajoutez-la dans le conteneur.

   3. À partir de l’application [intergiciel (middleware)](xref:fundamentals/middleware/index) ou des classes de contrôleur MVC, demandez une instance de `IDistributedCache` à partir du constructeur. L’instance est assurée par l' [Injection de dépendance](../../fundamentals/dependency-injection.md) (DI).

> [!NOTE]
> Il est inutile d’utiliser une durée de vie de type Singleton ou délimitée dans l’étendue pour les instances `IDistributedCache` (au moins pour les implémentations intégrées). Vous pouvez également créer une instance à chaque fois que nécessaire (au lieu d’utiliser l'[Injection de dépendance](../../fundamentals/dependency-injection.md)), mais cela peut rendre votre code plus difficile à tester et ne respecte pas le [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/).

L’exemple suivant montre comment utiliser une instance de `IDistributedCache` dans un composant d’intergiciel (middleware) simple :

[!code-csharp[](./distributed/sample/src/DistCacheSample/StartTimeHeader.cs?highlight=15,18,21,27,28,29,30,31)]

Dans le code ci-dessus, la valeur mise en cache est lue, mais jamais écrite. Dans cet exemple, la valeur est définie uniquement lorsqu’un serveur démarre et ne change pas. Dans un scénario multiserveur, le dernier serveur à démarrer remplace toutes les valeurs précédentes qui ont été définies par les autres serveurs. Les méthodes `Get` et `Set` utilisent le type `byte[]`. Par conséquent, la valeur de chaîne doit être convertie à l’aide de `Encoding.UTF8.GetString` (pour `Get`) et `Encoding.UTF8.GetBytes` (pour `Set`).

Le code suivant à partir de *Startup.cs* affiche la valeur :

[!code-csharp[](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=2,4,5,6&range=58-66)]

> [!NOTE]
> Étant donné que `IDistributedCache` est configuré dans la méthode `ConfigureServices`, il est disponible pour la méthode `Configure` en tant que paramètre. Ajoutez-le en tant que paramètre pour permettre à l’instance configurée d'être fournie via DI.

## <a name="using-a-redis-distributed-cache"></a>À l’aide d’un cache Redis distribué

[Redis](https://redis.io/) est un magasin de données en mémoire open source, qui est souvent utilisé comme un cache distribué. Vous pouvez l’utiliser localement, et vous pouvez configurer un [Cache Redis Azure](https://azure.microsoft.com/services/cache/) pour vos applications hébergées par Azure ASP.NET Core. Votre application ASP.NET Core configure l’implémentation de cache à l’aide d'une instance `RedisDistributedCache`.

Vous configurez l’implémentation de Redis dans `ConfigureServices` et y accéder dans le code de votre application en demandant une instance de `IDistributedCache` (voir le code ci-dessus).

Dans l’exemple de code, une implémentation `RedisCache` est utilisée lorsque le serveur est configuré pour un environnement `Staging`. Par conséquent, la méthode `ConfigureStagingServices` configure le `RedisCache`:

[!code-csharp[](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=8,9,10,11,12,13&range=27-40)]

> [!NOTE]
> Pour installer Redis sur votre ordinateur local, installez le package chocolatey [https://chocolatey.org/packages/redis-64/](https://chocolatey.org/packages/redis-64/) et exécutez `redis-server` à partir d’une invite de commandes.

## <a name="using-a-sql-server-distributed-cache"></a>À l’aide d’un serveur SQL Server de cache distribué

L’implémentation de "SqlServerCache" autorise le cache distribué à utiliser une base de données SQL Server comme magasin de sauvegarde. Pour créer une table SQL Server, vous pouvez utiliser l’outil "sql-cache", l’outil crée une table avec le nom et le schéma que vous spécifiez.

Pour utiliser l’outil "sql-cache", ajoutez `SqlConfig.Tools` à l'élément `<ItemGroup>` du fichier *.csproj* et exécutez la restauration dotnet.

[!code-xml[](./distributed/sample/src/DistCacheSample/DistCacheSample.csproj?range=23-25)]

Tester SqlConfig.Tools en exécutant la commande suivante

```none
C:\DistCacheSample\src\DistCacheSample>dotnet sql-cache create --help
   ```

L'outil sql-cache affiche l'aide relative à l'utilisation, aux options et aux commande, vous pouvez désormais créer des tables dans sql server, en exécutant la commande « sql-cache-create » :

```none
C:\DistCacheSample\src\DistCacheSample>dotnet sql-cache create "Data Source=(localdb)\v11.0;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
   info: Microsoft.Extensions.Caching.SqlConfig.Tools.Program[0]
       Table and index were created successfully.
   ```

Le tableau présente le schéma suivant :

![Table de Cache de SQL Server](distributed/_static/SqlServerCacheTable.png)

Comme toutes les implémentations de cache, votre application doit obtenir et définir des valeurs de cache à l’aide d’une instance de `IDistributedCache`, et non de `SqlServerCache`. L’exemple implémente `SqlServerCache` dans l'environnement `Production` (par conséquent, il est configuré dans `ConfigureProductionServices`).

[!code-csharp[](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=7,8,9,10,11,12&range=42-56)]

> [!NOTE]
> `ConnectionString` (et, éventuellement, `SchemaName` et `TableName`) doivent généralement être stockés en dehors du contrôle de code source (par exemple, usersecrets.), car ils peuvent contenir des informations d’identification.

## <a name="recommendations"></a>Recommandations

Lorsque vous décidez quelle implémentation de `IDistributedCache` est adaptée à votre application, choisissez entre Redis et SQL Server en fonction de votre infrastructure existante et de votre environnement, vos exigences de performances et l'expérience de votre équipe. Si votre équipe est plus familiarisé avec Redis, il constitue un excellent choix. Si votre équipe préfère SQL Server, vous pouvez aussi vous confier à l’implémentation. Notez qu’une solution de mise en cache traditionnelle stocke les données en mémoire, ce qui permet la récupération rapide des données. Vous devez stocker les données couramment utilisées dans un cache et stocker la totalité des données dans un magasin persistant de back-end tels que SQL Server ou le stockage Azure. Le Cache redis est une solution de mise en cache qui vous donne un débit élevé et une faible latence par rapport au Cache SQL.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Cache dans Azure redis](https://azure.microsoft.com/documentation/services/redis-cache/)
* [Base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)
* [Mise en cache en mémoire](xref:performance/caching/memory)
* [Détecter les modifications à l’aide de jetons de modification](xref:fundamentals/primitives/change-tokens)
* [Mise en cache des réponses](xref:performance/caching/response)
* [Intergiciel de mise en cache des réponses](xref:performance/caching/middleware)
* [Tag Helper de cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [Tag Helper de cache distribué](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)
