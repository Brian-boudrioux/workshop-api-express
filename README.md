**Objectifs :**
- Installer un petit serveur Express.
- Implémenter un CRUD (Create, Read, Update, Delete) en mémoire (tableau).
- Gérer les codes de statut HTTP (200, 201, 204, 400, 404, 500).
- Tester l’API avec `curl` / Postman / Insomnia.

---

## Prérequis
- Node.js et npm installés.
- Connaissances de base en JavaScript et requêtes HTTP.
- Outil pour tester les requêtes (Postman / Insomnia / curl).

---

## Structure proposée du workshop
1. Initialisation du projet
2. Création du squelette Express
3. Stockage en mémoire
4. Routes CRUD — guide étape par étape
5. Tests pratiques (curl)
6. Exercices et pistes d'amélioration
7. Points clés / corrigé succinct

---

## 1 — Initialisation (rapide)
```bash
mkdir express-crud-workshop
cd express-crud-workshop
npm init -y
npm install express
# Optionnel : nodemon pour le dev
npm install --save-dev nodemon
```

Dans `package.json` (scripts) tu peux ajouter :
```json
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js"
}
```

---

## 2 — Squelette du serveur
Crée un fichier `index.js` et initialise Express (extrait minimal) :

```js
const express = require('express');
const app = express();

// parse JSON bodies
app.use(express.json());

// point de test
app.get('/', (req, res) => res.send('API CRUD Express'));

// démarrage
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server on ${PORT}`));
```

> Remarque : on n'ajoute pas de middlewares personnalisés dans cet atelier (sauf `express.json()`).

---

## 3 — Stockage en mémoire
Simule une "base" avec un tableau et un compteur d'ID :

```js
let items = [
  { id: 1, name: 'Item A', description: 'Description A' },
  { id: 2, name: 'Item B', description: 'Description B' }
];
let nextId = 3;
```

Ce stockage vit en mémoire : il est réinitialisé à chaque redémarrage du serveur.

---

## 4 — Routes CRUD — guide étape par étape (avec exemples courts)

> Pour chaque route on donne : but, comportement attendu, validation et exemple `curl`.

### A — GET `/items` — lister tous les items
- **But :** retourner le tableau complet.
- **Statut attendu :** `200 OK`
- **Exemple d'implémentation (squelette) :**
```js
app.get('/items', (req, res) => {
  res.status(200).json(items);
});
```
- **Test curl :**
```bash
curl -i http://localhost:3000/items
```

### B — GET `/items/:id` — récupérer un item par id
- **But :** renvoyer l'objet correspondant à `id`.
- **Comportement :**
  - Si trouvé → `200` + objet JSON.
  - Si non trouvé → `404` + message d'erreur JSON.
- **Squelette :**
```js
app.get('/items/:id', (req, res) => { ... });
```
- **Test curl :**
```bash
curl -i http://localhost:3000/items/1
```

### C — POST `/items` — créer un nouvel item
- **But :** ajouter une ressource dans `items`.
- **Validation minimale :**
  - `name` présent et de type `string`.
- **Comportement :**
  - Si payload valide → créer l'objet, `201 Created`.
  - Si invalid → `400 Bad Request`.
- **Squelette :**
```js
app.post('/items', (req, res) => { ... });
```
- **Test curl :**
```bash
curl -i -X POST http://localhost:3000/items   -H "Content-Type: application/json"   -d '{"name":"Nouvel item","description":"..."}'
```

### D — PUT `/items/:id` — mettre à jour un item
- **But :** remplacer ou mettre à jour un objet existant.
- **Comportement :**
  - Si trouvé et valide → `200 OK`.
  - Si non trouvé → `404`.
  - Si payload invalide → `400`.
- **Squelette :**
```js
app.put('/items/:id', (req, res) => { ... });
```
- **Test curl :**
```bash
curl -i -X PUT http://localhost:3000/items/2   -H "Content-Type: application/json"   -d '{"name":"Item modifié"}'
```

### E — DELETE `/items/:id` — supprimer un item
- **But :** retirer un élément du tableau.
- **Comportement :**
  - Si trouvé → `204 No Content`.
  - Si non trouvé → `404`.
- **Squelette :**
```js
app.delete('/items/:id', (req, res) => { ... });
```
- **Test curl :**
```bash
curl -i -X DELETE http://localhost:3000/items/2
```

---

## 5 — Tests pratiques & scénarios
1. Lister les items (`GET /items`).
2. Récupérer `/items/1`.
3. Créer un item sans `name` → `400`.
4. Créer un item valide → `201`.
5. Modifier un item → `200`.
6. Supprimer un item → `204`.

---

## 6 — Exercices pour aller plus loin
- Séparer les routes et les données dans des fichiers distincts.
- Ajouter une validation de longueur minimale pour `name`.
- Ajouter la pagination (`?limit=10&offset=0`).
- Ajouter un champ `createdAt`.
- Tester automatiquement les routes avec Jest / Mocha.

---

## 7 — Points clés
- `200` → succès GET / PUT  
- `201` → création réussie  
- `204` → suppression sans contenu  
- `400` → payload invalide  
- `404` → ressource non trouvée  
- `500` → erreur interne

---

## 8 — Commandes utiles
```bash
# Lancer le serveur
npm run dev

# Tester avec postman
