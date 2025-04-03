# API REST - Todolist (Projet pédagogique)

Cette API REST permet de gérer des listes de tâches (todolists) pour des utilisateurs authentifiés via un jeton Bearer. Elle est conçue dans un but pédagogique pour manipuler des requêtes HTTP avec cURL côté client.

---

## Authentification

Toutes les requêtes vers l'API doivent inclure un jeton privé via l'en-tête :

```
Authorization: Bearer VOTRE_CLE_PRIVEE
```

> Chaque utilisateur dispose d'une clé privée générée lors de l'inscription sur le site web <a href="https://php-exo-curl.cvmdev.be" target="_blank" rel="noopener">php-exo-curl.cvmdev.be</a> et disponible sur la page "profil".

---

## Structure des données

### Structure des tâches

Une tâche est un tableau associatif contenant les clés suivantes :

- **id** - *int* : Identifiant logique de la tâche (initialisé par l'application et non modifiable).
- **position** - *int* : Position de la tâche dans la todolist (initialisé par l'application mais modifiable par la suite).
- **texte** - *string* : Texte détaillant la tâche (initialisé par l'utilisateur lors de la création et modifiable par la suite).
- **fait** - *bool* : Statut de la tâche (initialisé par l'application ou l'utilisateur et modifiable par la suite).

#### Exemple de la structure d'une tâche

```php
[
    "id": 0,
    "position": 1,
    "texte": "Acheter du pain",
    "fait": false
]
```

### Structure des todolists

Une todolist est un tableau pouvant accueillir les tâches.

#### Exemple de la structure d'une todolist

```php
[
  [
    "id": 0,
    "position": 1,
    "texte": "Acheter du pain",
    "fait": false
  ],
  [
    "id": 1,
    "position": 0,
    "texte": "Appeler Mamie",
    "fait": true
  ]
]
```

### Structure des réponses

Les réponses retournées par le serveur sont des tableaux associatifs au format `JSON`.
Chaque réponse contient au minimum 3 clés (**statut**, **codeHTTP** et **messsage**) :

- **statut** - *int* : Représente le succès (`1`) ou l’échec (`0`) de la requête.
- **codeHTTP** - *int* : Le code HTTP correspondant à la réponse (ex. `200` pour OK, `404` pour non trouvé, `400` pour une requête invalide…).
- **message** - *string* : Message de validation ou d’erreur permettant de comprendre le résultat de la requête (ex : `"Tâche ajoutée avec succès."`, `"La liste demandée est introuvable."`).

Certaines réponses peuvent également contenir des données supplémentaires selon le type d'opération.
Par exemple :

- **todolists** – *array* : Présent uniquement lors d’une requête de récupération de listes. Il s’agit d’un tableau associatif où chaque clé représente le nom d'une todolist, et chaque valeur est un tableau de tâches associées.

#### Codes de réponse HTTP

- `200 OK` : Succès de l'opération.
- `201 Created` : Ressource créée avec succès.
- `400 Bad Request` : Données invalides ou manquantes.
- `401 Unauthorized` : Jeton Bearer manquant ou invalide.
- `404 Not Found` : Ressource introuvable.
- `409 Conflict` : Conflit (ex. : nom de liste déjà utilisé).

---

## Endpoints disponibles

### Résumé des endpoints
- **GET** `/api/todolists/` : Récupérer toutes vos todolists.
- **GET** `/api/todolists/{nomDeLaListe}` : Récupérer la todolist {nomDeLaListe}.
- **POST** `/api/todolists/` : Créer une nouvelle todolist.
- **POST** `/api/todolists/{nomDeLaListe}/todos/` : Ajouter une tâche à la todolist {nomDeLaListe}.
- **PATCH** `/api/todolists/{nomDeLaListe}/todos/{tacheId}` : Modifier la tâche {tacheId} de la todolist {nomDeLaListe}.
- **PUT** `/api/todolists/{nomDeLaListe}` : Modifier complètement la todolist {nomDeLaListe}.
- **DELETE** `/api/todolists/{nomDeLaListe}/todos/{tacheId}` : Supprimer la tâche {tacheId} de la todolist {nomDeLaListe}.
- **DELETE** `/api/todolists/{nomDeLaListe}` : Supprimer la todolist {nomDeLaListe}.

### Récupérer toutes vos todolists

- **Requête** : **GET** `/api/todolists/`

- **Exemple** : **GET** `/api/todolists/`

### Récupérer une todolist

- **Requête** : **GET** `/api/todolists/{nomDeLaListe}`  

- **Exemple** : **GET** `/api/todolists/ma-liste-de-courses`

### Créer une nouvelle todolist

- **Requête** : **POST** `/api/todolists/`
- **Données à envoyer** : Tableau associatif contenant une clé "**listeNom**" à laquelle il faut attribuer le nom souhaité de la todolist. Attention, le nom ne peut contenir que des caractères alphanumériques et des tirets.

- **Exemple** :
    - **Requête** : **POST** `/api/todolists/`
    - **Données à envoyer** :
        ```php
        ["listeNom" => "liste-de-courses"]
        ```

### Ajouter une tâche à une todolist

- **Requête** : **POST** `/api/todolists/{nomDeLaListe}/todos/`

- **Données à envoyer** : Tableau associatif contenant deux clés :
    - **texte** - *string*  : Description de la tâche.
    - **fait** - *bool* : Statut de la tâche (facultatif : false par défaut).

- **Exemple** :
    - **Requête** : **POST** `/api/todolists/semaine-8/todos/`

    - **Données à envoyer** :
        ```php
        [
          "texte" => "Aller chez le coiffeur",
          "fait" => false
        ]
        ```

### Modifier une tâche d'une todolist

- **Requête** : **PATCH** `/api/todolists/{nomDeLaListe}/todos/{tacheId}`

- **Données à envoyer** : Un tableau associatif au format `JSON` pouvant devant contenir au moins l'une des clés modifiables (**"position"**, **"texte"** et **"fait"**).

- **Exemple** :
    - **Requête** : **PATCH** `/api/todolists/semaine-24/todos/2`

    - **Données à envoyer** :
        ```json
          {
            "fait": true
          }
        ```

### Modifier une todolist complète

- **Requête** : **PUT** `/api/todolists/{nomDeLaListe}`

- **Données à envoyer** : tableau associatif au format `JSON` remplaçant l'intégralité de la liste des tâches. Chaque tâche doit respecter les règles décrites dans la section "Ajouter une tâche à une todolist".

- **Exemple** :
    - **Requête** : **PUT** `/api/todolists/semaine-24`

    - **Données à envoyer** :
        ```json
        [
          {
            "texte": "Changer le bac du chat",
            "fait": true
          },
          {
            "texte": "Se reposer"
          }
        ]
        ```

### Supprimer une tâche d'une todolist

- **Requête** : **DELETE** `/api/todolists/{nomDeLaListe}/todos/{tacheId}`

- **Exemple** : **DELETE** `/api/todolists/semaine-24/todos/2`

### Supprimer une todolist

- **Requête** : **DELETE** `/api/todolists/{nomDeLaListe}`

- **Exemple** : **DELETE** `/api/todolists/liste-de-courses`
