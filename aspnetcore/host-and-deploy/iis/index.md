---
title: "Héberger ASP.NET Core sur Windows avec IIS"
author: guardrex
description: "Découvrez comment héberger des applications ASP.NET Core sur Windows Server Internet Information Services (IIS)."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/iis/index
ms.openlocfilehash: b1ca9303c620597f7844c401048129044e99d7be
ms.sourcegitcommit: 7ac15eaae20b6d70e65f3650af050a7880115cbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Héberger ASP.NET Core sur Windows avec IIS

Par [Luke Latham](https://github.com/guardrex) et [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="supported-operating-systems"></a>Systèmes d'exploitation pris en charge

Les systèmes d’exploitation suivants sont pris en charge :

* Windows 7 ou version ultérieure
* SQL Server 2008 R2 ou version ultérieure&#8224;

&#8224;D’un point de vue conceptuel, la configuration d’IIS décrite dans ce document s’applique également à l’hébergement d’applications ASP.NET Core sur Nano Server IIS. Pour obtenir des instructions spécifiques à Nano Server, consultez le didacticiel [ASP.NET Core avec IIS sur Nano Server](xref:tutorials/nano-server).

Le [serveur HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement [WebListener](xref:fundamentals/servers/weblistener)) ne fonctionne pas dans une configuration de proxy inverse avec IIS. Utilisez le [serveur Kestrel](xref:fundamentals/servers/kestrel).

## <a name="application-configuration"></a>Configuration d’application

### <a name="enable-the-iisintegration-components"></a>Activer les composants IISIntegration

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Un fichier *Program.cs* standard appelle [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) pour commencer à configurer un hôte. `CreateDefaultBuilder` configure [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et active l’intégration d’IIS en configurant le chemin et le port de base pour le [module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) :

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Le module ASP.NET Core génère un port dynamique à assigner au processus backend. La méthode `UseIISIntegration` récupère le port dynamique et configure Kestrel pour écouter sur `http://locahost:{dynamicPort}/`. Les autres configurations d’URL sont alors remplacées, telles que les appels à `UseUrls` ou à [l’API d’écoute de Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration). L’utilisation du module évite donc les appels à `UseUrls` ou à l’API `Listen` de Kestrel. Si `UseUrls` ou `Listen` est appelé, Kestrel écoute sur le port spécifié lors de l’exécution de l’application sans IIS.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Incluez une dépendance envers le package [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) dans les dépendances de l’application. Utilisez l’intergiciel (middleware) d’intégration IIS en ajoutant la méthode d’extension [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) à [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) :

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

[UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) et [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) sont tous les deux requis. Le code qui appelle `UseIISIntegration` n’affecte pas la portabilité du code. Si l’application n’est pas exécutée derrière IIS (par exemple si l’application est exécutée directement sur Kestrel), `UseIISIntegration` n’effectue pas d’opérations.

Le module ASP.NET Core génère un port dynamique à assigner au processus backend. La méthode `UseIISIntegration` récupère le port dynamique et configure Kestrel pour écouter sur `http://locahost:{dynamicPort}/`. Les autres configurations d’URL sont alors remplacées, telles que les appels à `UseUrls`. L’utilisation du module rend donc inutile tout appel à `UseUrls`. Si `UseUrls` est appelé, Kestrel écoute sur le port spécifié lors de l’exécution de l’application sans IIS.

Si `UseUrls` est appelé dans une application ASP.NET Core 1.0, appelez-le **avant** d’appeler `UseIISIntegration` pour que le port configuré par le module ne soit pas remplacé. Cet ordre d’appel est inutile avec ASP.NET Core 1.1, car le paramètre du module remplace `UseUrls`.

---

Pour plus d’informations sur l’hébergement, consultez [Hébergement dans ASP.NET Core](xref:fundamentals/hosting).

### <a name="iis-options"></a>Options IIS

Pour configurer des options IIS, ajoutez une configuration de services pour [IISOptions](/dotnet/api/microsoft.aspnetcore.builder.iisoptions) dans [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices). Dans l’exemple suivant, le transfert des certificats clients à l’application pour remplissage `HttpContext.Connection.ClientCertificate` est désactivé :

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Option                         | Par défaut | Paramètre |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si `true`, l’Intergiciel (Middleware) d’intégration IIS définit le `HttpContext.User` authentifié par [Authentification Windows](xref:security/authentication/windowsauth). Si `false`, l’intergiciel (middleware) fournit uniquement une identité pour `HttpContext.User` et répond aux questions explicitement posées par `AuthenticationScheme`. L’authentification Windows doit être activée dans IIS pour que `AutomaticAuthentication` fonctionne. Pour plus d'informations, consultez la rubrique [Authentification Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Définit le nom d’affichage montré aux utilisateurs sur les pages de connexion. |
| `ForwardClientCertificate`     | `true`  | Si la valeur est `true` et que l’en-tête de requête `MS-ASPNETCORE-CLIENTCERT` est présent, `HttpContext.Connection.ClientCertificate` est renseigné. |

### <a name="webconfig-file"></a>fichier web.config

Le fichier *web.config* configure le [Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module). La création, la transformation et la publication de *web.config* sont gérées par le kit .NET Core Web SDK (`Microsoft.NET.Sdk.Web`). Le kit SDK est défini en haut du fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si un fichier *web.config* se trouve dans le projet, il est transformé avec le *processPath* et les *arguments* corrects pour configurer le [Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module), puis il est déplacé vers la [sortie publiée](xref:host-and-deploy/directory-structure).

Si un fichier *web.config* se trouve dans le projet, il est transformé avec le *processPath* et les *arguments* corrects pour configurer le Module ASP.NET Core, puis il est déplacé vers la sortie publiée. La transformation ne modifie pas les paramètres de configuration IIS dans le fichier.

Le fichier *web.config* peut fournir des paramètres de configuration IIS supplémentaires qui contrôlent les modules IIS actifs. Pour plus d’informations sur les modules IIS capables de traiter les demandes avec les applications ASP.NET Core, consultez la rubrique [Utilisation des modules IIS](xref:host-and-deploy/iis/modules).

Pour empêcher le Kit de développement logiciel (SDK) Web de transformer le fichier *web.config*, ajoutez la propriété **\<IsTransformWebConfigDisabled>** au fichier projet :

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Lorsque vous désactivez le Kit de développement logiciel (SDK) Web en transformant le fichier, le *processPath* et les *arguments* doivent être définis manuellement par le développeur. Pour plus d’informations, consultez les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

### <a name="webconfig-file-location"></a>emplacement du fichier web.config

Les applications ASP.NET Core sont hébergées dans un proxy inverse hébergé entre IIS et le serveur Kestrel. Pour créer le proxy inverse, le fichier *web.config* doit être présent dans le chemin de racine de contenu (généralement le chemin de base de l’application) de l’application déployée. Il s’agit du même emplacement que le chemin physique du site Web fourni à IIS. Le fichier *web.config* est nécessaire à la racine de l’application pour permettre la publication de plusieurs applications à l’aide de Web Deploy.

Il existe des fichiers sensibles sur le chemin physique de l’application, notamment *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (commentaires de documentation XML) et *\<assembly>.deps.json*. Lorsque le fichier *web.config* est présent et que le site démarre normalement, IIS ne traite pas ces fichiers sensibles s’ils sont requis. Si le fichier *web.config* est absent, nommé de manière incorrecte ou s’il est incapable de configurer le site pour un démarrage normal, IIS peut traiter des fichiers sensibles publiquement.

**Le fichier *web.config* doit être présent dans le déploiement en permanence, correctement nommé et capable de configurer le site pour un démarrage normal. Ne retirez jamais le fichier *web.config* d’un déploiement de production.**

## <a name="iis-configuration"></a>Configuration d’IIS

**Systèmes d’exploitation Windows Server**

Activez le rôle serveur **Serveur Web (IIS)** et établissez des services de rôle.

1. Utilisez l’Assistant **Ajouter des rôles et des fonctionnalités** par le biais du menu **Gérer** ou du lien dans **Gestionnaire de serveur**. À l’étape **Rôles de serveurs**, cochez la case **Serveur Web (IIS)**.

   ![Le rôle Serveur Web IIS est sélectionné à l’étape Sélectionner des rôles de serveurs.](index/_static/server-roles-ws2016.png)

1. Après l’étape **Fonctionnalités**, l’étape **Services de rôle** se charge pour le serveur Web (IIS). Sélectionnez les services de rôle IIS souhaités ou acceptez les services de rôle par défaut fournis.

   ![Les services de rôle par défaut sont sélectionnés à l’étape Sélectionner des services de rôle.](index/_static/role-services-ws2016.png)

   **Authentification Windows (facultatif)**  
   Pour activer l’authentification Windows, développez les nœuds suivants : **Serveur Web**  > **Sécurité**. Sélectionnez la fonctionnalité **Authentification Windows**. Pour plus d’informations, consultez [Authentification Windows\<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) et [Configurer l’authentification Windows](xref:security/authentication/windowsauth).

   **WebSockets (facultatif)**  
   WebSockets est pris en charge avec ASP.NET Core 1.1 ou version ultérieure. Pour activer les WebSockets, développez les nœuds suivants : **Serveur Web** > **Développement d’applications**. Sélectionnez la fonctionnalité **Protocole WebSocket**. Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).

1. Validez l’étape de **Confirmation** pour installer les services et le rôle de serveur web. Un redémarrage du serveur/d’IIS n’est pas nécessaire après l’installation du rôle **Serveur Web (IIS)**.

**Systèmes d’exploitation Windows Desktop**

Activez la **Console de gestion IIS** et les **Services World Wide Web**.

1. Accédez à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran).

1. Ouvrez le nœud **Internet Information Services**. Ouvrez le nœud **Outils de gestion Web**.

1. Cochez la case **Console de gestion IIS**.

1. Cochez la case **Services World Wide Web**.

1. Acceptez les fonctionnalités par défaut pour **Services World Wide Web** ou personnalisez les fonctionnalités d’IIS.

   **Authentification Windows (facultatif)**  
   Pour activer l’authentification Windows, développez les nœuds suivants : **Services World Wide Web** > **Sécurité**. Sélectionnez la fonctionnalité **Authentification Windows**. Pour plus d’informations, consultez [Authentification Windows\<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) et [Configurer l’authentification Windows](xref:security/authentication/windowsauth).

   **WebSockets (facultatif)**  
   WebSockets est pris en charge avec ASP.NET Core 1.1 ou version ultérieure. Pour activer les WebSockets, développez les nœuds suivants : **Services World Wide Web** > **Fonctionnalités de développement d’applications**. Sélectionnez la fonctionnalité **Protocole WebSocket**. Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).

1. Si l’installation d’IIS requiert un redémarrage, redémarrez le système.

![Console de gestion IIS et Services World Wide Web sont sélectionnés dans Fonctionnalités de Windows.](index/_static/windows-features-win10.png)

---

## <a name="install-the-net-core-windows-server-hosting-bundle"></a>Installer le bundle d’hébergement .NET Core Windows Server

1. Installez le [bundle d’hébergement .NET Core Windows Server](https://aka.ms/dotnetcore-2-windowshosting) sur le système hôte. Le bundle installe le Runtime .NET Core, la bibliothèque .NET Core et le [Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module). Le module crée le proxy inverse entre IIS et le serveur Kestrel. Si le système n’a pas de connexion Internet, obtenez et installez [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) avant d’installer le bundle d’hébergement .NET Core Windows Server.

   **Important !** Si l’offre groupée hôte est installée avant IIS, l’installation de l’offre groupée doit être réparée. Après avoir installé IIS, exécutez à nouveau le programme d’installation de l’offre groupée d’hébergement.
   
   Pour empêcher le programme d’installation d’installer des packages x86 sur un système d’exploitation x64, exécutez le programme d’installation à partir d’une invite de commandes administrateur avec le commutateur `OPT_NO_X86=1`.

1. Redémarrez le système ou exécutez **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes. Le redémarrage d’IIS, récupère une modification apportée au CHEMIN D’ACCÈS du système par le programme d’installation.

> [!NOTE]
> Pour plus d’informations sur la configuration partagée IIS, consultez [Module ASP.NET Core avec configuration partagée des services Internet (IIS)](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Installer Web Deploy lors de la publication avec Visual Studio

Quand vous déployez des applications sur un serveur avec [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy), installez la dernière version de Web Deploy sur le serveur. Pour installer Web Deploy, utilisez [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) ou obtenez un programme d’installation directement à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). La méthode recommandée est d’utiliser WebPI. WebPI offre une installation autonome et une configuration pour les fournisseurs d’hébergement.

## <a name="create-the-iis-site"></a>Créer le site IIS

1. Sur le système d’hébergement, créez un dossier pour contenir les fichiers et dossiers publiés de l’application. Une disposition du déploiement de l’application est décrite dans la rubrique [Structure des répertoires](xref:host-and-deploy/directory-structure).

1. Dans le nouveau dossier, créez un dossier *journaux* où stocker les journaux stdout du Module ASP.NET Core quand la journalisation stdout est activée. Si l’application est déployée avec un dossier *logs* dans la charge utile, ignorez cette étape. Pour obtenir des instructions sur l’activation de MSBuild et créer le dossier *journaux* automatiquement lorsque le projet est généré localement, consultez la rubrique [Structure des répertoires](xref:host-and-deploy/directory-structure).

   **Important !** Utilisez uniquement le journal stdout pour résoudre les échecs de démarrage de l’application. N’utilisez jamais la journalisation stdout pour la journalisation de l’application de routine. Il n’existe aucune limite quant à la taille du fichier journal ou au nombre de fichiers journaux créés. Pour plus d’informations sur le journal stdout, consultez [Création et redirection des journaux](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection). Pour plus d’informations sur la journalisation dans une application ASP.NET Core, consultez la rubrique [Journalisation](xref:fundamentals/logging/index).

1. Dans **Gestionnaire IIS**, ouvrez le nœud du serveur dans le panneau **Connexions**. Cliquez avec le bouton de droite sur le dossier **Sites**. Sélectionnez **Ajouter un site Web** dans le menu contextuel.

1. Spécifiez le **Nom du site** et définissez le **Chemin physique** sur le dossier de déploiement de l’application. Spécifiez la configuration **Liaison** et créez le site Web en sélectionnant **OK** :

   ![Spécifiez le nom du site, le chemin physique et le nom d’hôte à l’étape Ajouter un site Web.](index/_static/add-website-ws2016.png)

1. Sous le nœud du serveur, sélectionnez **Pools d’applications**.

1. Cliquez avec le bouton de droite sur le pool d’applications du site et sélectionnez **Paramètres de base** dans le menu contextuel.

1. Dans la fenêtre **Modifier le pool d’applications**, définissez la **version .NET CLR** sur **Aucun code managé** :

   ![Définissez Aucun code managé pour la version CLR .NET.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core s’exécute dans un processus séparé et gère l’exécution. ASP.NET Core ne repose pas sur le chargement du CLR de bureau. La configuration de la **version CLR .NET** sur **Aucun code managé** est facultative.

1. Vérifiez que l’identité de modèle de processus dispose des autorisations appropriées.

   Si l’identité par défaut du pool d’applications (**Modèle de processus** > **Identité**) **ApplicationPoolIdentity** est remplacée par une autre identité, vérifiez que la nouvelle identité dispose des autorisations nécessaires pour accéder au dossier de l’application, à la base de données et aux autres ressources nécessaires. Par exemple, le pool d’applications nécessite l’accès en lecture et en écriture aux dossiers dans lesquels l’application lit et écrit des fichiers.

**Configuration de l’authentification Windows (facultatif)**  
Pour plus d'informations, consultez la rubrique [Configurer l’authentification Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Déployer l’application

Déployez l’application vers le dossier créé sur le système d’hébergement. [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) est le mécanisme recommandé pour le déploiement.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy avec Visual Studio

Pour découvrir comment créer un profil de publication pour une utilisation avec Web Deploy, consultez la rubrique [Profils de publication Visual Studio pour le déploiement d’applications ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles). Si le fournisseur d’hébergement fournit un profil de publication ou prend en charge sa création, téléchargez son profil et importez-le à l’aide de la boîte de dialogue **Publier** de Visual Studio.

![Boîte de dialogue Publier](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy en dehors de Visual Studio

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) peut également être utilisé en dehors de Visual Studio à partir de la ligne de commande. Pour plus d’informations, consultez [Web Deployment Tool (Outil de déploiement Web)](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternatives à Web Deploy

Utilisez la méthode de votre choix, telle que la copie manuelle, Xcopy, Robocopy ou PowerShell, pour déplacer l’application vers le système d’hébergement.

## <a name="browse-the-website"></a>Parcourir le site web

![Le navigateur Microsoft Edge a chargé la page de démarrage IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Fichiers de déploiement verrouillés

Les fichiers dans le dossier de déploiement sont verrouillés quand l’application est en cours d’exécution. Les fichiers verrouillés ne peuvent pas être remplacés au cours du déploiement. Pour libérer des fichiers verrouillés dans un déploiement, arrêtez le pool d’applications à l’aide **d’une** des approches suivantes :

* Utilisez Web Deploy et référencez `Microsoft.NET.Sdk.Web` dans le fichier projet. Un fichier *app_offline.htm* est placé à la racine du répertoire de l’application web. Quand le fichier est présent, le module ASP.NET Core ferme l’application normalement et sert le fichier *app_offline.htm* pendant le déploiement. Pour plus d’informations, consultez les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module#appofflinehtm).
* Arrêtez manuellement le pool d’applications dans le Gestionnaire IIS sur le serveur.
* Utilisez PowerShell pour arrêter et redémarrer le pool d’applications (cette opération requiert PowerShell version 5 ou ultérieure) :

  ```PowerShell
  $webAppPoolName = 'APP_POOL_NAME'

  # Stop the AppPool
  if((Get-WebAppPoolState $webAppPoolName).Value -ne 'Stopped') {
      Stop-WebAppPool -Name $webAppPoolName
      while((Get-WebAppPoolState $webAppPoolName).Value -ne 'Stopped') {
          Start-Sleep -s 1
      }
      Write-Host `-AppPool Stopped
  }

  # Provide script commands here to deploy the app

  # Restart the AppPool
  if((Get-WebAppPoolState $webAppPoolName).Value -ne 'Started') {
      Start-WebAppPool -Name $webAppPoolName
      while((Get-WebAppPoolState $webAppPoolName).Value -ne 'Started') {
          Start-Sleep -s 1
      }
      Write-Host `-AppPool Started
  }
  ```

## <a name="data-protection"></a>Protection des données

La [pile de protection des données ASP.NET Core](xref:security/data-protection/index) est utilisée par plusieurs [intergiciels (middlewares)](xref:fundamentals/middleware/index) ASP.NET Core, y compris le middleware utilisé dans l’authentification. Même si les API de protection des données ne sont pas appelées par le code de l’utilisateur, la protection des données doit être configurée avec un script de déploiement ou dans un code utilisateur pour créer un [magasin de clés](xref:security/data-protection/implementation/key-management) de chiffrement persistantes. Si la protection des données n’est pas configurée, les clés sont conservées en mémoire et ignorées au redémarrage de l’application.

Si le Key Ring est stocké en mémoire, au redémarrage de l’application :

* tous les jetons d’authentification basés sur des cookies sont invalidés 
* Les utilisateurs doivent se reconnecter pour envoyer leur prochaine demande. 
* toutes les données protégées par le Key Ring ne peuvent plus être déchiffrées. Cela peut inclure des [jetons CSRF](xref:security/anti-request-forgery#how-does-aspnet-core-mvc-address-csrf) et [des cookies Tempdara MVC ASP.NET Core](xref:fundamentals/app-state#tempdata).

Pour configurer la protection des données sous IIS afin de rendre persistante le Key Ring, adoptez **l’une** des approches suivantes :

* **Créer des clés de Registre de la protection des données**

  Les clés de la protection des données utilisées par les applications ASP.NET Core sont stockées dans le registre externe aux applications. Afin de conserver les clés pour une application donnée, créez des clés de Registre pour le pool d’applications.

  Pour les installations IIS autonomes hors d’une batterie de serveurs, le [script PowerShell Provision-AutoGenKeys.ps1 de protection des données](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1) peut être utilisé pour chaque pool d’applications utilisé avec une application ASP.NET Core. Ce script crée une clé de Registre dans le Registre HKLM, accessible uniquement pour le compte du processus Worker du pool d’applications de l’application. Les clés sont chiffrées au repos à l’aide de DPAPI avec une clé à l’échelle de la machine.

  Dans les scénarios de batterie de serveurs Web, vous pouvez configurer une application afin qu’elle utilise un chemin UNC pour stocker son Key Ring de protection des données. Par défaut, les clés de protection des données ne sont pas chiffrées. Vérifiez que les autorisations de fichiers pour le partage réseau sont limitées au compte Windows sous lequel s’exécute l’application. Un certificat X509 peut être utilisé pour protéger les clés au repos. Mettez en œuvre un mécanisme permettant aux utilisateurs de charger des certificats : placez les certificats dans le magasin de certificats approuvés de l’utilisateur et vérifiez qu’ils sont disponibles sur toutes les machines où s’exécute l’application de l’utilisateur. Pour plus d’informations, consultez [Configuration de la Protection des données](xref:security/data-protection/configuration/overview).

* **Configurer le pool d’applications IIS pour charger le profil utilisateur**

  Ce paramètre se trouve dans la section **Modèle de processus** sous les **Paramètres avancés** du pool d’applications. Définissez Charger le profil utilisateur sur `True`. Cela permet de stocker les clés sous le répertoire du profil utilisateur et de les protéger à l’aide de DPAPI avec une clé propre au compte d’utilisateur utilisé par le pool d’applications.

* **Utiliser le système de fichiers comme magasin de Key Ring**

  Ajustez le code d’application pour [utiliser le système de fichiers comme magasin de porte-clés](xref:security/data-protection/configuration/overview). Utilisez un certificat X509 pour protéger le Key Ring et vérifiez qu’il s’agit d’un certificat approuvé. S’il s’agit d’un certificat auto-signé, placez-le dans le magasin Racine approuvée.

  Quand vous utilisez IIS dans une batterie de serveurs web :

  * Utilisez un partage de fichiers accessible par tous les ordinateurs.
  * Déployez un certificat X509 sur chaque ordinateur. Configurez la [protection des données dans le code](xref:security/data-protection/configuration/overview).

* **Définir une stratégie au niveau de l’ordinateur pour la protection des données**

  Le système de protection des données offre une prise en charge limitée de la définition d’une [stratégie au niveau de l’ordinateur](xref:security/data-protection/configuration/machine-wide-policy) par défaut pour toutes les applications qui utilisent les API de protection des données. Pour plus d’informations, consultez la [documentation de protection des données](xref:security/data-protection/index).

## <a name="sub-application-configuration"></a>Configuration de la sous-application

Les sous-applications ajoutées sous l’application racine ne doivent pas inclure le module ASP.NET Core en tant que gestionnaire. Si le module est ajouté en guise de gestionnaire dans le fichier *web.config* d’une sous-application, une *Erreur de serveur interne 500.19* faisant référence au fichier config défaillant est reçue lors de la tentative de navigation dans la sous-application.

L’exemple suivant présente un fichier *web.config* publié pour une sous-application ASP.NET Core :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\<assembly_name>.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Si vous hébergez une sous-application non-ASP.NET Core sous une application ASP.NET Core, supprimez explicitement le gestionnaire hérité dans le fichier *web.config* de la sous-application :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\<assembly_name>.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Pour plus d’informations sur la configuration du module ASP.NET Core, consultez la rubrique [Introduction au module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) et les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

## <a name="configuration-of-iis-with-webconfig"></a>Configuration d’IIS avec web.config

La configuration d’IIS est influencée par la section **\<system.webServer>** de *web.config* pour les fonctionnalités d’IIS qui s’appliquent à une configuration de proxy inverse. Si IIS est configuré au niveau du serveur pour utiliser la compression dynamique, l’élément **\<urlCompression>** dans le fichier *web.config* de l’application peut le désactiver.

Pour plus d’informations, consultez la [référence de configuration du \<system.webServer>](/iis/configuration/system.webServer/), la [référence de configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) et la rubrique[Utilisation des modules IIS avec ASP.NET Core](xref:host-and-deploy/iis/modules). Pour définir des variables d’environnement pour des applications individuelles exécutées dans des pools de données isolés, (prises en charge pour IIS 10.0 ou version ultérieure), consultez la section *AppCmd.exe command* de la rubrique [Variables d’environnement \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) dans la documentation de référence IIS.

## <a name="configuration-sections-of-webconfig"></a>Sections de configuration de web.config

Les sections de configuration des applications ASP.NET 4.x dans *web.config* ne sont pas utilisées par les applications ASP.NET Core pour la configuration :

* **\<system.web>**
* **\<appSettings>**
* **\<connectionStrings>**
* **\<location>**

Les applications ASP.NET Core sont configurées à l’aide d’autres fournisseurs de configuration. Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pools d'applications

Quand vous hébergez plusieurs sites Web sur un même serveur, isolez les applications les unes des autres en exécutant chaque application dans son propre pool d’applications. La boîte de dialogue **Ajouter un site Web** d’IIS applique cette configuration par défaut. Quand un **Nom du site** est fourni, le texte est automatiquement transféré vers la zone de texte **Pool d’applications**. Un nouveau pool d’applications est créé avec le nom du site une fois qu’il est ajouté.

## <a name="application-pool-identity"></a>Identité du pool d’applications

Un compte d’identité du pool d’applications permet à une application de s’exécuter sous un compte unique sans qu’il soit nécessaire de créer et de gérer des domaines ou des comptes locaux. Sur IIS 8.0 ou version ultérieure, le processus Worker d’administration IIS (WAS) crée un compte virtuel avec le nom du nouveau pool d’applications et exécute les processus Worker du pool d’applications sous ce compte par défaut. Dans la console de gestion IIS, sous **Paramètres avancés** pour le pool d’applications, vérifiez que **l’Identité** est configurée pour utiliser **ApplicationPoolIdentity** :

![Boîte de dialogue Paramètres avancés du pool applications](index/_static/apppool-identity.png)

Le processus de gestion IIS crée un identificateur sécurisé avec le nom du pool d’applications dans le système de sécurité Windows. Les ressources peuvent être sécurisées à l’aide de cette identité. Toutefois, cette identité n’est pas un compte d’utilisateur réel et n’apparaît pas dans la console de gestion d’utilisateurs Windows.

Si le processus Worker IIS a besoin d’un accès élevé à l’application, modifiez la liste de contrôle d’accès (ACL) du répertoire contenant l’application :

1. Ouvrez l’Explorateur Windows et accédez au répertoire.

1. Cliquez avec le bouton droit sur le répertoire et sélectionnez **Propriétés**.

1. Sous l’onglet **Sécurité**, sélectionnez le bouton **Modifier**, puis le bouton **Ajouter**.

1. Sélectionnez le bouton **Emplacements**, puis vérifiez que le système est sélectionné.

1. Entrez **IIS AppPool\\<app_pool_name>** dans la zone **Entrer les noms des objets à sélectionner**. Sélectionnez le bouton **Vérifier les noms**. Pour le *DefaultAppPool*, vérifiez les noms à l’aide de **IIS AppPool\DefaultAppPool**. Lorsque le bouton **Vérifier les noms** est sélectionné, une valeur de **DefaultAppPool** est indiquée dans la zone des noms d’objets. Il n’est pas possible d’entrer le nom du pool d’applications directement dans la zone des noms d’objets. Utilisez le format **IIS AppPool\\<app_pool_name>** lors de la vérification du nom de l’objet.

   ![Sélectionnez la boîte de dialogue utilisateurs ou groupes pour le dossier d’applications : ajoutez le nom du pool d’applications « DefaultAppPool » à « IIS AppPool\" dans la zone des noms d’objets avant de sélectionner « Vérifier les noms ».](index/_static/select-users-or-groups-1.png)

1. Sélectionnez **OK**.

   ![Sélectionnez la boîte de dialogue utilisateurs ou groupes pour le dossier d’applications : après avoir sélectionné « Vérifier les noms », le nom d’objet « DefaultAppPool » est indiqué dans la zone des noms d’objets.](index/_static/select-users-or-groups-2.png)

1. Les autorisations Lire &amp; exécuter doivent être accordées par défaut. Fournissez des autorisations supplémentaires si nécessaire.

L’accès peut également être octroyé par le biais d’une invite de commandes à l’aide de l’outil **ICACLS**. À l’aide de *DefaultAppPool* en exemple, la commande suivante est utilisée :

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Pour plus d’informations, consultez la rubrique [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Résoudre les problèmes liés à ASP.NET Core sur IIS](xref:host-and-deploy/iis/troubleshoot)
* [Informations de référence sur les erreurs courantes pour Azure App Service et IIS avec ASP.NET Core](xref:host-and-deploy/azure-iis-errors-reference)
* [Introduction au Module ASP.NET Core](xref:fundamentals/servers/aspnet-core-module)
* [Informations de référence sur la configuration du Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Utilisation de modules IIS avec ASP.NET Core](xref:host-and-deploy/iis/modules)
* [Présentation d’ASP.NET Core](../index.md)
* [Site officiel de Microsoft IIS](https://www.iis.net/)
* [Bibliothèque Microsoft TechNet : Windows Server](/windows-server/windows-server-versions)
