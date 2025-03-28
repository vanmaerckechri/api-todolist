# 📋 API REST - Todolist (Projet pédagogique)

Cette API REST permet de gérer des listes de tâches (todolists) pour des utilisateurs authentifiés via un jeton Bearer. Elle est conçue dans un but pédagogique pour manipuler des requêtes HTTP avec cURL côté client.

---

## 🔐 Authentification

Toutes les requêtes vers l'API doivent inclure un jeton privé via l'en-tête :

```
Authorization: Bearer VOTRE_CLE_PRIVEE
```

> Chaque utilisateur dispose d'une clé privée générée lors de l'inscription et disponible sur la page "profil".

---

## 📁 Structure des données

Une todolist est représentée au format `JSON` associé à un utilisateur. Chaque liste contient un tableau de tâches :

```json
[
  {
    "texte": "Acheter du pain",
    "fait": false
  },
  {
    "texte": "Appeler Mamie",
    "fait": true
  }
]
```

Les identifiants des tâches correspondent à leur position dans le tableau (index).

---

## 📙 Endpoints disponibles

### ➕ Créer une nouvelle todolist

- **POST** `/api/todolists/`

  - Données à envoyer :

    ```php
    ['listeNom' => 'ma-liste-01']
    ```

  - Le nom doit être alphanumérique ou contenir des tirets (aucun espace).


### 🔍 Récupérer les todolists

- **GET** `/api/todolists/`  
  Récupère toutes les todolists de l'utilisateur.

- **GET** `/api/todolists/{nomDeLaListe}`  
  Récupère une todolist précise.

  - Exemple : `/api/todolists/liste-de-courses`


### 📝 Modifier une todolist complète

- **PUT** `/api/todolists/{nomDeLaListe}`

  - Données à envoyer : tableau JSON remplaçant l'intégralité de la liste

    ```json
    [
      {
        "texte": "Changer le bac du chat",
        "fait": true
      },
      {
        "texte": "Se reposer",
        "fait": false
      }
    ]
    ```


### ❌ Supprimer une todolist

- **DELETE** `/api/todolists/{nomDeLaListe}`

  - Exemple : `/api/todolists/liste-de-courses`


### ➕ Ajouter une tâche à une todolist

- **POST** `/api/todolists/{nomDeLaListe}/todos/`

  - Données à envoyer (formulaire ou JSON) :

    ```php
    [
      'texte' => 'Aller chez le coiffeur',
      'fait' => false
    ]
    ```


### ✍️ Modifier une tâche d'une todolist

- **PATCH** `/api/todolists/{nomDeLaListe}/todos/{tacheId}`

  - Données à envoyer :

    ```json
    {
      "fait": true
    }
    ```

  - Exemple : `/api/todolists/liste-de-courses/todos/0`


### ❌ Supprimer une tâche d'une todolist

- **DELETE** `/api/todolists/{nomDeLaListe}/todos/{tacheId}`

  - Exemple : `/api/todolists/ma-liste/todos/2`

---

## ℹ️ Codes de réponse HTTP

- `200 OK` : Succès de l'opération.
- `201 Created` : Ressource créée avec succès.
- `400 Bad Request` : Données invalides ou manquantes.
- `401 Unauthorized` : Jeton Bearer manquant ou invalide.
- `404 Not Found` : Ressource introuvable.
- `409 Conflict` : Conflit (ex. : nom de liste déjà utilisé).
