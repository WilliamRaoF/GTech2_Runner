# Bonnes pratiques spécifiques au C++

---

### 1. Introduction

Le **C++** est un langage puissant mais complexe, combinant programmation procédurale, orientée objet et générique.  
Sa flexibilité impose des **règles strictes de discipline** afin d’éviter les erreurs coûteuses et de garantir un code performant et maintenable.

Les bonnes pratiques en C++ concernent :
- La **structure du projet** (fichiers, en-têtes, dépendances)
- Les **normes de codage**
- La **gestion de la mémoire**
- L’**utilisation des fonctionnalités modernes** du langage

---

### 2. Structure typique d’un projet C++

Un projet C++ bien structuré suit une séparation claire entre les **fichiers d’en-tête** (`.h` ou `.hpp`) et les **fichiers source** (`.cpp`).

```

mon_projet/
│
├── include/                # Fichiers d'en-tête (.h / .hpp)
│   ├── player.hpp
│   ├── enemy.hpp
│   └── utils.hpp
│
├── src/                    # Fichiers source (.cpp)
│   ├── main.cpp
│   ├── player.cpp
│   ├── enemy.cpp
│   └── utils.cpp
│
├── tests/                  # Tests unitaires (Catch2, GoogleTest)
│
├── build/                  # Dossier de compilation
│
├── CMakeLists.txt          # Fichier de configuration du projet
└── README.md

````

**Bonnes pratiques structurelles :**
- Séparer **interface (.hpp)** et **implémentation (.cpp)**.
- Inclure les fichiers d’en-tête **seulement quand nécessaire**.
- Utiliser un **système de build** (CMake, Meson, Premake).
- Garder les dépendances **modulaires et indépendantes**.

---

### 3. Normes de code C++

#### a. Style et conventions

| Élément | Convention | Exemple |
|----------|-------------|----------|
| Nom de classe | PascalCase | `PlayerManager` |
| Nom de fonction | camelCase | `calculateDamage()` |
| Variable | snake_case | `player_health` |
| Constante | UPPER_CASE | `MAX_SPEED` |
| Macro | Préfixe en majuscules | `#define DEBUG_MODE` |
| Fichier | snake_case | `player_controller.cpp` |

#### b. Organisation des includes

1. Inclure d’abord l’en-tête correspondant au fichier courant.  
2. Puis les bibliothèques standard (`<iostream>`, `<vector>`, etc.).  
3. Ensuite les bibliothèques tierces.  
4. Enfin les autres en-têtes du projet.

Exemple :
```cpp
#include "player.hpp"
#include <iostream>
#include <vector>
#include "utils.hpp"
````

#### c. Protéger les inclusions multiples

Toujours encapsuler les fichiers `.h` avec des **include guards** ou `#pragma once` :

```cpp
#ifndef PLAYER_HPP
#define PLAYER_HPP

class Player {
public:
    void move();
private:
    int health;
};

#endif
```

ou simplement :

```cpp
#pragma once
```

---

### 4. Gestion de la mémoire

#### a. Principe fondamental : RAII

**RAII (Resource Acquisition Is Initialization)** signifie que toute ressource (mémoire, fichier, socket, etc.) doit être acquise et libérée automatiquement via un objet.

Exemple :

```cpp
std::vector<int> numbers = {1, 2, 3}; // Géré automatiquement
```

#### b. Éviter `new` et `delete` manuels

Préférer les **smart pointers** :

* `std::unique_ptr<T>` : propriété exclusive
* `std::shared_ptr<T>` : partage de propriété
* `std::weak_ptr<T>` : référence non-propriétaire

```cpp
#include <memory>

std::unique_ptr<Player> player = std::make_unique<Player>();
```

#### c. Pas de fuites mémoire

Utiliser des outils comme :

* **Valgrind**
* **AddressSanitizer**
* **Visual Leak Detector (Windows)**

---

### 5. Utilisation du C++ moderne (C++11 et +)

Les versions récentes du C++ offrent de nombreuses améliorations :

* **Auto type deduction** : `auto value = 42;`
* **Range-based loops** : `for (auto& item : list)`
* **Lambda functions**
* **Smart pointers**
* **Move semantics** : `std::move()`
* **Constexpr** : évaluation à la compilation
* **Enum class** : énumérations fortement typées

**Bonnes pratiques :**

* Éviter le C++ “classique” (malloc, raw pointers, etc.).
* Utiliser les containers de la STL (`std::vector`, `std::map`, etc.).
* Éviter les macros sauf nécessité absolue.
* Préférer `constexpr` et `const` à des valeurs magiques.

---

### 6. Gestion des erreurs

* Utiliser les **exceptions** pour les erreurs critiques.
* Éviter les codes de retour magiques (`-1`, `0`, etc.).
* Toujours valider les entrées utilisateur.
* Utiliser des assertions (`assert()`) pour vérifier les invariants internes.

Exemple :

```cpp
if (health <= 0)
    throw std::runtime_error("Health must be positive");
```

---

### 7. Performances et optimisation

* Toujours **mesurer avant d’optimiser**.
* Utiliser des **références** plutôt que des copies lorsque possible.
* Passer les gros objets par **const référence** :

  ```cpp
  void process(const std::string& data);
  ```
* Éviter les allocations répétées.
* Préférer les algorithmes STL (`std::sort`, `std::find`, etc.) à du code manuel.
* Activer les **optimisations du compilateur** (`-O2`, `-O3`).

---

### 8. Compilation et outils

#### a. Outils essentiels

| Outil                     | Utilité                        |
| ------------------------- | ------------------------------ |
| **CMake**                 | Configuration multi-plateforme |
| **Clang / GCC / MSVC**    | Compilateurs                   |
| **Valgrind / ASan**       | Détection de fuites mémoire    |
| **cppcheck / clang-tidy** | Analyse statique               |
| **Doxygen**               | Génération de documentation    |
| **GoogleTest / Catch2**   | Tests unitaires                |

#### b. Compilation modulaire

Compiler par unités logiques :

```
g++ -c src/player.cpp -o build/player.o
g++ -c src/enemy.cpp -o build/enemy.o
g++ build/player.o build/enemy.o -o bin/game
```

---

### 9. Bonnes pratiques générales C++

1. Toujours initialiser les variables.
2. Utiliser `const` dès que possible.
3. Éviter les conversions implicites dangereuses.
4. Favoriser les fonctions courtes et cohérentes.
5. Respecter le principe du “Zero Cost Abstraction”.
6. Écrire un code clair avant de viser la performance.
7. Tester systématiquement après chaque modification.
8. Lire et respecter le **C++ Core Guidelines** (Bjarne Stroustrup).

---

### 10. Conclusion

Le C++ exige une approche **disciplinée** : il récompense la rigueur et sanctionne la négligence.
Un bon développeur C++ sait :

* Structurer proprement son projet.
* Maîtriser la mémoire et les pointeurs intelligents.
* Utiliser les fonctionnalités modernes du langage.
* Garantir un code sûr, lisible et performant.

Le respect de ces pratiques fait la différence entre un code qui “marche” et un code **professionnel et durable**.

---

### Ressources recommandées

* *Effective Modern C++* — Scott Meyers
* *C++ Core Guidelines* — Bjarne Stroustrup & Herb Sutter
* *The Cherno — C++ Series* (YouTube)
* *cppreference.com* — Documentation officielle de la STL
* *Google C++ Style Guide*
