<span data-ttu-id="5d668-101">**Remarque :** l’appel à `AddIdentity` configure les paramètres de schéma par défaut.</span><span class="sxs-lookup"><span data-stu-id="5d668-101">**Note:** The call to `AddIdentity` configures the default scheme settings.</span></span> <span data-ttu-id="5d668-102">Le `AddAuthentication(string defaultScheme)` jeux de surcharge la `DefaultScheme` propriété ; et le `AddAuthentication(Action<AuthenticationOptions> configureOptions)` surcharge affecte uniquement les propriétés que vous définissez explicitement.</span><span class="sxs-lookup"><span data-stu-id="5d668-102">The `AddAuthentication(string defaultScheme)` overload sets the `DefaultScheme` property; and, the `AddAuthentication(Action<AuthenticationOptions> configureOptions)` overload sets only the properties you explicitly set.</span></span> <span data-ttu-id="5d668-103">Une de ces surcharges doit uniquement être appelée qu’une seule fois lors de l’ajout de plusieurs fournisseurs d’authentification.</span><span class="sxs-lookup"><span data-stu-id="5d668-103">Either of these overloads should only be called once when adding multiple authentication providers.</span></span> <span data-ttu-id="5d668-104">Les appels suivants à ce dernier ont la possibilité de remplacement de tous configurés précédemment [AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) propriétés.</span><span class="sxs-lookup"><span data-stu-id="5d668-104">Subsequent calls to it have the potential of overriding any previously configured [AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) properties.</span></span>