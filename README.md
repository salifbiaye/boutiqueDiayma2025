# Projet Diayma - Boutique en Ligne

**Auteur** : Salif Biaye  
**Date** : Novembre 2025  
**École** : ESP (Département Génie Informatique)

## 1. Structure du Projet

### Projets de la Solution

- **Diayma** (dossier : P2FixAnAppDotNetCode)
    - Application ASP.NET Core de gestion d'une boutique en ligne

### Version SDK .NET

- **.NET Core 2.0.9** (netcoreapp2.0)
- Framework: Microsoft.NETCore.App

---

## 2. Bugs Trouvés

### Bug 1 : Changement de langue en espagnol ne fonctionne pas

- **Sévérité** : Moyenne
- **Description** : La sélection de l'espagnol ne change pas la langue de l'interface
- **Pas à reproduire** :
    1. Aller sur la page d'accueil
    2. Cliquer sur "Español"
    3. L'interface reste en français
- **Cause supposée** : Les ressources espagnoles ne sont pas chargées correctement et les fichiers `.resx` sont manquants

### Bug 2 : Total et moyenne incorrects pour produits répétés (CRITIQUE)

- **Sévérité** : CRITIQUE (bug financier)
- **Description** : Quand on ajoute 2-3 fois le même produit, le total ne se multiplie pas
- **Pas à reproduire** :
    1. Ajouter un produit à 1000 FCFA
    2. Total affichée = 1000 
    3. Ajouter le même produit à nouveau
    4. Total devrait être 2000, mais reste 1000 
    5. Ajouter une 3e fois : devrait être 3000, mais ne change pas 
- **Cause supposée** : La multiplication (prix × quantité) n'est pas appliquée correctement dans **CartController.cs ligne 15** ou **CartSummaryViewComponent.cs ligne 12**
- **Fichiers concernés** :
    - `Controllers/CartController.cs`
    - `Components/CartSummaryViewComponent.cs`

---

## 3. Ordre d'Exécution (Traçage du Débogage)

### Flux complet avant affichage des produits à l'accueil

```
Program.Main() 
  ↓
Startup.ConfigureServices() 
  ↓
Startup.Configure() 
  ↓
ProductController.Index() 
  ↓
ProductService.GetAllProducts() 
  ↓
ProductRepository.GetAllProducts() 
  ↓
Vue Product/Index.cshtml
```

### Résumé du chemin d'exécution

1. **Program.Main()** : Démarre l'application et initialise l'hôte web
2. **Startup.ConfigureServices()** (Ligne 20) : Enregistre les services (ProductService, ProductRepository, CartService)
3. **Startup.Configure()** : Configure le routing avec la route par défaut `Product/Index`
4. **ProductController.Index()** (Ligne 15) : Récupère la liste des produits
5. **ProductService.GetAllProducts()** : Service métier qui appelle le repository
6. **ProductRepository.GetAllProducts()** : Accède aux données et retourne les produits
7. **Vue Product/Index.cshtml** : Affichage des produits à l'écran

### Points d'Arrêt Placés

-  **Startup.cs Ligne 20** : ConfigureServices()
-  **ProductController.cs Ligne 15** : Index()
-  **CartController.cs Ligne 15** : GetCart()
-  **CartSummaryViewComponent.cs Ligne 12** : InvokeAsync()
-  **OrderController.cs Ligne 17** : Méthodes de commande

---

## 4. Déploiement en Exécutable Windows

### Générer l'exécutable autonome

Pour créer un fichier `.exe` qui fonctionne sans nécessiter .NET Core installé :

```powershell
# Se placer dans le dossier du projet
cd P2FixAnAppDotNetCode

# Publier en version Release pour Windows 64-bit
dotnet publish -c Release -r win-x64 --self-contained true
```

### Lancer l'exécutable

```powershell
# Se placer dans le dossier de publication
cd bin\Release\netcoreapp2.0\win-x64\publish\

# Exécuter l'application
.\Diayma.exe
```

### Détails de la publication

- **Configuration** : Release (optimisée, plus rapide)
- **Runtime** : win-x64 (Windows 64-bit)
- **Self-contained** : true (inclut le runtime .NET)
- **Fichier généré** : `Diayma.exe`
- **Localisation** : `P2FixAnAppDotNetCode\bin\Release\netcoreapp2.0\win-x64\publish\`

L'exécutable peut être distribué et lancé sur n'importe quel ordinateur Windows 64-bit sans avoir besoin d'installer .NET Core.

---

## 5. Lien de téléchargement de l'exécutable

📥 **[Lien Google Drive de l'exécutable](https://drive.google.com/file/d/1QuA4t7oVDudVFZYpfrEkuidjhzT7IoJ8/view?usp=drive_link)**



---

