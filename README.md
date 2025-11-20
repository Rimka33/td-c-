# TD C# et Technologies .NET - Visual Studio Débogage

## Réponses aux questions

### 1. Récupération et exécution du code
✅ Code récupéré depuis GitHub et exécuté dans Visual Studio. Fenêtre d'avertissements fermée.

### 2. Projets de la solution
La solution contient **1 projet** :
- **P2FixAnAppDotNetCode** : Application web ASP.NET Core

### 3. Version SDK .NET utilisée
La version SDK .NET utilisée est : **.NET Core 2.0** (netcoreapp2.0)

Fichier de configuration : `P2FixAnAppDotNetCode/Diayma.csproj`
```xml
<TargetFramework>netcoreapp2.0</TargetFramework>
```

### 4. Installation du SDK
Pour installer le SDK .NET Core 2.0, téléchargez-le depuis le site officiel Microsoft :
https://dotnet.microsoft.com/download/dotnet-core/2.0

### 5. Dépôt GitHub
✅ Dépôt GitHub créé pour stocker le code.

### 6. Bugs trouvés dans l'application

#### Bug 1 : Calcul incorrect du total du panier
**Fichier** : `P2FixAnAppDotNetCode/Models/Cart.cs`  
**Ligne** : 66  
**Problème** : La méthode `GetTotalValue()` calcule seulement la somme des prix des produits sans tenir compte de la quantité.

**Code actuel** :
```csharp
return GetCartLineList().Sum(x => x.Product.Price);
```

**Correction nécessaire** :
```csharp
return GetCartLineList().Sum(x => x.Product.Price * x.Quantity);
```

#### Bug 2 : Risque de NullReferenceException
**Fichier** : `P2FixAnAppDotNetCode/Models/Cart.cs`  
**Ligne** : 89  
**Problème** : La méthode `FindProductInCartLines()` peut causer une exception si aucun produit n'est trouvé, car `FirstOrDefault()` peut retourner null.

**Code actuel** :
```csharp
return GetCartLineList().Where(x => x.Product.Id == productId).FirstOrDefault().Product;
```

**Correction nécessaire** :
```csharp
var cartLine = GetCartLineList().Where(x => x.Product.Id == productId).FirstOrDefault();
return cartLine?.Product;
```

### 7. Points d'arrêt placés

Les points d'arrêt ont été placés sur les lignes suivantes :

a) **CartSummaryViewComponent** ligne 12
```12:12:P2FixAnAppDotNetCode/Components/CartSummaryViewComponent.cs
_cart = cart as Cart;
```

b) **ProductController** ligne 15
```15:15:P2FixAnAppDotNetCode/Controllers/ProductController.cs
_productService = productService;
```

c) **OrderController** ligne 17
```17:17:P2FixAnAppDotNetCode/Controllers/OrderController.cs
_cart = pCart;
```

d) **CartController** ligne 15
```15:15:P2FixAnAppDotNetCode/Controllers/CartController.cs
_cart = pCart;
```

e) **Startup** ligne 20
```20:20:P2FixAnAppDotNetCode/Startup.cs
Configuration = configuration;
```

### 8. Namespaces, classes et méthodes visités lors du débogage

Lors de l'affichage des produits sur l'écran d'accueil, voici le parcours d'exécution :

#### Démarrage de l'application

1. **Program.cs** - `Main(string[] args)`
   - Namespace : `P2FixAnAppDotNetCode`
   - Méthode : `Main()` → Appelle `BuildWebHost(args).Run()`

2. **Program.cs** - `BuildWebHost(string[] args)`
   - Méthode : `BuildWebHost()` → Configure l'hôte web et appelle `UseStartup<Startup>()`

3. **Startup.cs** - Constructeur
   - Namespace : `P2FixAnAppDotNetCode`
   - Classe : `Startup`
   - Ligne 20 : `Configuration = configuration;` (point d'arrêt)

4. **Startup.cs** - `ConfigureServices(IServiceCollection services)`
   - Enregistre les services (Cart, ProductService, OrderService, etc.)
   - Configure la localisation et MVC

5. **Startup.cs** - `Configure(IApplicationBuilder app, IHostingEnvironment env)`
   - Configure le pipeline HTTP
   - Définit la route par défaut : `{controller=Product}/{action=Index}/{id?}`

#### Traitement de la requête HTTP

6. **ProductController.cs** - Constructeur
   - Namespace : `P2FixAnAppDotNetCode.Controllers`
   - Classe : `ProductController`
   - Ligne 15 : `_productService = productService;` (point d'arrêt)
   - Injection de dépendances : `IProductService` et `ILanguageService`

7. **ProductController.cs** - `Index()`
   - Méthode : `Index()` → Appelle `_productService.GetAllProducts()`

8. **ProductService.cs** - `GetAllProducts()`
   - Namespace : `P2FixAnAppDotNetCode.Models.Services`
   - Classe : `ProductService`
   - Appelle `_productRepository.GetAllProducts().ToList()`

9. **ProductRepository.cs** - `GetAllProducts()`
   - Namespace : `P2FixAnAppDotNetCode.Models.Repositories`
   - Classe : `ProductRepository`
   - Retourne la liste des produits

10. **ProductController.cs** - `Index()` (suite)
    - Retourne `View(products)` avec la liste des produits

11. **CartSummaryViewComponent.cs** - Constructeur
    - Namespace : `P2FixAnAppDotNetCode.Components`
    - Classe : `CartSummaryViewComponent`
    - Ligne 12 : `_cart = cart as Cart;` (point d'arrêt)

12. **CartSummaryViewComponent.cs** - `Invoke()`
    - Méthode : `Invoke()` → Retourne la vue du résumé du panier

#### Affichage dans le navigateur

13. **Views/Product/Index.cshtml**
    - Vue Razor qui affiche la liste des produits

**Résumé du parcours** :
- **Namespaces** : `P2FixAnAppDotNetCode`, `P2FixAnAppDotNetCode.Controllers`, `P2FixAnAppDotNetCode.Models.Services`, `P2FixAnAppDotNetCode.Models.Repositories`, `P2FixAnAppDotNetCode.Components`
- **Classes principales** : `Program`, `Startup`, `ProductController`, `ProductService`, `ProductRepository`, `CartSummaryViewComponent`
- **Méthodes clés** : `Main()`, `BuildWebHost()`, `ConfigureServices()`, `Configure()`, `ProductController.Index()`, `ProductService.GetAllProducts()`, `ProductRepository.GetAllProducts()`, `CartSummaryViewComponent.Invoke()`

### 9. Déploiement en exécutable Windows

✅ **Déploiement effectué avec succès !**

**Commande utilisée** :
```bash
dotnet publish -c Release -r win-x64 --self-contained true -o publish
```

**Emplacement de l'exécutable** :
- Dossier : `P2FixAnAppDotNetCode/publish/`
- Fichier exécutable : `Diayma.exe`

**Pour exécuter l'application** :
1. Naviguer vers le dossier `P2FixAnAppDotNetCode/publish/`
2. Double-cliquer sur `Diayma.exe` ou exécuter depuis le terminal :
```bash
cd P2FixAnAppDotNetCode/publish
.\Diayma.exe
```

L'application sera accessible sur `http://localhost:5000` (ou le port configuré).

### 10. Lien vers l'exécutable

**Note** : L'exécutable se trouve dans le dossier `P2FixAnAppDotNetCode/publish/`. 

Pour partager l'exécutable :
1. Compresser le dossier `publish` en fichier ZIP
2. Uploader sur Google Drive, OneDrive, ou autre service de stockage
3. Partager le lien

**Lien à ajouter** : [À compléter avec le lien vers Google Drive, OneDrive, etc.]
