---
title: "Prévention des attaques de redirection ouvert dans une application ASP.NET Core"
author: ardalis
description: "Montre comment empêcher des attaques de redirection ouvert par rapport à une application ASP.NET Core"
manager: wpickett
ms.author: riande
ms.date: 07/07/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/preventing-open-redirects
ms.openlocfilehash: d6cd65a2516c4d5e41428f0c1f2dbbe913ac2123
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="preventing-open-redirect-attacks-in-an-aspnet-core-app"></a>Prévention des attaques de redirection ouverte dans une application ASP.NET Core

Une application web qui effectue une redirection vers une URL qui est spécifiée via la demande tels que les données de chaîne de requête ou d'un formulaire peuvent potentiellement être falsifiées en redirigant les utilisateurs vers une URL externe malveillante. Cette manipulation est appelée une attaque de redirection ouverte.

Chaque fois que votre logique d’application redirige vers une URL spécifiée, vous devez vérifier que l’URL de redirection n’a pas été falsifiée. ASP.NET Core intègre des fonctionnalités pour aider à protéger les applications contre les attaques de redirection ouverte (également appelée open redirection).

## <a name="what-is-an-open-redirect-attack"></a>Qu’est une attaque de redirection ouverte ?

Les applications Web redirigent fréquemment les utilisateurs vers une page de connexion lorsqu’ils accèdent aux ressources qui requièrent une authentification. Typiquement la redirection inclut un paramètre querystring `returnUrl`  afin que l’utilisateur peut être retourné à l’URL demandée à l’origine une fois qu’ils ont connecté avec succès. Une fois que l’utilisateur s’authentifie, il est redirigé vers l’URL qui était initialement demandée.

Étant donné que l’URL de destination est spécifié dans la chaîne de requête de la demande, un utilisateur malveillant peut falsifier la chaîne de requête. Une chaîne de requête falsifiée pourrait permettre au site pour rediriger l’utilisateur vers un site externe, malveillant. Cette technique est appelée une attaque de redirection (ou une redirection) ouverte.

<a name="an-example-attack"></a>Un exemple d'attaque

Un utilisateur malveillant peut développer une attaque conçue pour permettre l’accès utilisateur malveillant pour obtenir les informations d’identification d’un utilisateur ou les informations sensibles sur votre application. Pour lancer l’attaque, il convaint l’utilisateur de cliquer sur un lien vers la page de connexion de votre site, avec une valeur de chaîne de requête `returnUrl`  ajoutée à l’URL. Par exemple, l'exemple d’application [NerdDinner.com](http://nerddinner.com)  (écrite pour ASP.NET MVC) inclut une page de connexion ici : ``http://nerddinner.com/Account/LogOn?returnUrl=/Home/About``. L’attaque consiste à procéder comme suit :

1. Utilisateur clique sur un lien vers ``http://nerddinner.com/Account/LogOn?returnUrl=http://nerddiner.com/Account/LogOn`` (Notez que la deuxième URL est nerddi**n**er, pas nerddi**nn**er).
2. L’utilisateur parvient à se connecter.
3. L’utilisateur est redirigé (par le site) à ``http://nerddiner.com/Account/LogOn`` (site malveillant qui ressemble à un site réel).
4. L’utilisateur se connecte à nouveau (donnant malveillant leurs informations d’identification de site) et est redirigé vers le site réel.

L’utilisateur pensera probablement que leur première tentative de connexion a échoué, et l'autre a réussi. Ils ne savent pas que leurs informations d’identification ont été compromises.

![Processus d’attaque Redirection ouverte](preventing-open-redirects/_static/open-redirection-attack-process.png)

En plus des pages de connexion, certains sites fournissent des points de terminaison ou des pages de redirection. Imaginez que votre application comprend une page avec une redirection ouverte, ``/Home/Redirect``. Un attaquant pourrait créer, par exemple, un lien dans un message électronique qui accède à ``[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login``. Un utilisateur voit que l’URL commence par le nom de votre site. Faisant confiance, il cliquera sur le lien. La redirection ouverte envoie ensuite l’utilisateur vers le site de hameçonnage, qui est identique au vôtre, et l’utilisateur probablement se connectera pensant que c'est votre site.

## <a name="protecting-against-open-redirect-attacks"></a>Protection contre les attaques de redirection ouverte

Lorsque vous développez des applications web, traiter toutes les données fournies par l’utilisateur comme non fiables. Si votre application possède des fonctionnalités qui redirige l’utilisateur en fonction du contenu de l’URL, vérifiez que ces redirections sont effectuées uniquement localement dans votre application (ou à une URL connue, pas une URL qui peut être fournie dans la chaîne de requête).

### <a name="localredirect"></a>LocalRedirect

Utilisez la méthode helper ``LocalRedirect`` à partir de la classe de base de `Controller`:

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

``LocalRedirect``lève une exception si une URL non local est spécifiée. Dans le cas contraire, il se comporte comme la méthode ``Redirect``.

### <a name="islocalurl"></a>IsLocalUrl

Utilisez la méthode [IsLocalUrl](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iurlhelper#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) pour tester l’URL avant de rediriger les utlisateurs:

L’exemple suivant montre comment vérifier si une URL est locale avant de rediriger.

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

La méthde `IsLocalUrl` protège les utilisateurs d'être redirigés par inadvertance vers un site malveillant. Vous pouvez consigner les détails de l’URL qui ont été fourni lorsqu’une URL non local est fournie dans une situation dans laquelle vous vous attendiez à apeller une URL locale. La journalisation des redirection d'URL peuvent faciliter le diagnostic des attaques de redirection.
