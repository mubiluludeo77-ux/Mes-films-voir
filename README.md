Application de Notation et liste de films à regarder
# MesFilms

Application **SwiftUI** pour gérer sa liste de films à voir : on parcourt la liste, on ouvre la fiche d'un film, on le note, on le marque comme vu ou favori, on ajoute ses propres films et on réorganise le tout avec des gestes natifs.

Le projet suit une architecture **MVVM** simple, sans dépendance externe.

## Fonctionnalités

- **Liste de films** avec icône (SF Symbols), titre, genre, année, note en étoiles, badge « vu » et étoile de favori
- **Fiche détail** : affiche zoomable, notation interactive (1 à 5 étoiles), bascules *Déjà vu* / *Favori*, notes personnelles
- **Ajout d'un film** via un formulaire (titre, genre, année, icône). Le bouton *Ajouter* reste désactivé tant que le titre et le genre sont vides
- **Suppression** par swipe, menu contextuel ou méthode du ViewModel
- **Réorganisation** par glisser-déposer (mode édition)
- **État vide** avec `ContentUnavailableView` quand la liste ne contient plus de film
- **Données d'exemple** préchargées (Inception, Parasite, Dune, etc.)

## Gestes disponibles

| Geste | Où | Action |
|---|---|---|
| Toucher une ligne | Liste | Ouvre la fiche détail |
| Double-tap | Ligne de la liste | Marque le film vu / non vu |
| Appui long | Ligne de la liste | Menu contextuel (favori, vu, supprimer) |
| Swipe vers la gauche | Ligne de la liste | Supprime le film |
| Swipe vers la droite | Ligne de la liste | Ajoute / retire des favoris |
| Bouton *Modifier* puis glisser | Liste | Réordonne les films |
| Double-tap | Affiche (détail) | Zoome / dézoome l'affiche |
| Tap sur une étoile | Détail | Attribue la note |

## Architecture

```
MesFilms/
├── MesFilms.xcodeproj
└── MesFilms/
    ├── MesFilmsApp.swift              # Point d'entrée (@main)
    ├── Models/
    │   └── Movie.swift                # Modèle + données d'exemple
    ├── ViewModels/
    │   └── MovieListViewModel.swift   # Logique métier (ObservableObject)
    ├── Views/
    │   ├── ContentView.swift          # Liste, navigation, gestes
    │   ├── MovieRowView.swift         # Ligne réutilisable
    │   ├── MovieDetailView.swift      # Fiche détail
    │   └── AddMovieView.swift         # Formulaire d'ajout (sheet)
    └── Assets.xcassets/               # Icône d'app et couleur d'accent
```

### Rôle de chaque couche

- **Modèle : `Movie`**. Une struct `Identifiable`, `Hashable` et `Codable`. `Identifiable` sert à `List`/`ForEach`, `Hashable` à `NavigationLink(value:)`, et `Codable` prépare une future sauvegarde JSON.
- **ViewModel : `MovieListViewModel`**. Détient le tableau `@Published var movies`. Les vues ne le modifient jamais directement et passent par ses méthodes : `add`, `delete`, `move`, `toggleWatched`, `toggleFavorite`, `updateRating`, `updateNotes`.
- **Vues**. `ContentView` possède le ViewModel (`@StateObject`) et le transmet aux autres vues (`@ObservedObject`). `MovieDetailView` relit le film depuis le ViewModel pour refléter les changements en temps réel.

La navigation utilise `NavigationStack` avec `navigationDestination(for: Movie.self)`.

## Prérequis

- **Xcode** récent (le projet utilise le format `objectVersion 77` et cible le SDK 26.5)
- **iOS / iPadOS / macOS 26.5** ou plus, selon les réglages actuels du projet
- **Swift 5**
- Aucune dépendance externe (pas de Swift Package, CocoaPods ni Carthage)

## Lancer le projet

1. Ouvrir `MesFilms.xcodeproj` dans Xcode.
2. Choisir un simulateur (ou un appareil) dans la barre d'outils.
3. Appuyer sur **⌘R**.

Si Xcode ne propose pas de signature automatique, sélectionner votre *Team* dans **Signing & Capabilities**. Le bundle identifier actuel est `v.MesFilms` et peut être modifié.

## Limites connues

- **Pas de persistance** : les films, notes et modifications sont perdus à la fermeture de l'app. Les données viennent de `Movie.sampleData`.
- Un film ajouté n'a pas de vraie affiche, seulement une icône SF Symbols au choix.
- L'interface est en français uniquement, sans `Localizable.strings`.
- Aucun test unitaire ni test d'interface n'est inclus.

## Pistes d'amélioration

- Sauvegarde locale via `Codable` + JSON, ou **SwiftData**
- Recherche et filtres (vus / non vus / favoris / genre)
- Passage à `@Observable` (Observation) à la place d'`ObservableObject`
- Récupération des affiches via une API (ex. TMDB)
- Tests unitaires du `MovieListViewModel`
- Localisation de l'interface
