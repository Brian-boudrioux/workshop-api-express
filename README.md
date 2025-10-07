# 1️⃣ Initialisation du projet

```bash
mkdir express-crud-workshop
cd express-crud-workshop
npm init -y
npm install express
npm install --save-dev nodemon
```

Modifier le fichier **package.json** pour inclure les scripts :

```json
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js"
}
```

Lancer le serveur avec :

```bash
npm run dev
```

---

# 2️⃣ Création du serveur Express

Créer un fichier **index.js** à la racine du projet :

```js
const express = require('express');
const app = express();

app.use(express.json()); // Permet de lire le JSON dans les requêtes

app.get('/', (req, res) => {
  res.send('API CRUD Express — Ready!');
});

const PORT = 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

Tester dans le navigateur :  
👉 [http://localhost:3000](http://localhost:3000)

---

# 3️⃣ Mise en place du stockage en mémoire

Ajouter une collection temporaire pour stocker les données :

```js
let items = [
  { id: 1, name: 'Item A', description: 'Description du premier item' },
  { id: 2, name: 'Item B', description: 'Deuxième item' }
];
let nextId = 3;
```

---

# 4️⃣ Implémentation du CRUD

## 🔹 Récupérer tous les éléments (GET)

```js
app.get('/items', (req, res) => {
  res.status(200).json(items);
});
```

**Test Postman :**  
- **Méthode :** GET  
- **URL :** `http://localhost:3000/items`  
- **Résultat attendu :** liste JSON des items existants.

---

## 🔹 Récupérer un élément par ID (GET /items/:id)

```js
app.get('/items/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const item = items.find(i => i.id === id);

  if (!item) {
    return res.status(404).json({ error: 'Item non trouvé' });
  }

  res.status(200).json(item);
});
```

**Test Postman :**  
- **Méthode :** GET  
- **URL :** `http://localhost:3000/items/1`  
- **Résultat attendu :** l’objet correspondant à l’ID.

---

## 🔹 Créer un nouvel élément (POST)

```js
app.post('/items', (req, res) => {
  const { name, description } = req.body;

  if (!name) {
    return res.status(400).json({ error: 'Le champ "name" est requis.' });
  }

  const newItem = {
    id: nextId++,
    name,
    description: description || ''
  };

  items.push(newItem);
  res.status(201).json(newItem);
});
```

**Test Postman :**  
- **Méthode :** POST  
- **URL :** `http://localhost:3000/items`  
- **Body (JSON) :**
```json
{
  "name": "Nouvel item",
  "description": "Item ajouté via Postman"
}
```
- **Résultat attendu :** `201 Created` avec l’objet créé.

---

## 🔹 Modifier un élément existant (PUT)

```js
app.put('/items/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const index = items.findIndex(i => i.id === id);

  if (index === -1) {
    return res.status(404).json({ error: 'Item non trouvé' });
  }

  const { name, description } = req.body;
  if (!name) {
    return res.status(400).json({ error: 'Le champ "name" est requis.' });
  }

  items[index] = { id, name, description: description || '' };
  res.status(200).json(items[index]);
});
```

**Test Postman :**  
- **Méthode :** PUT  
- **URL :** `http://localhost:3000/items/1`  
- **Body (JSON) :**
```json
{
  "name": "Item modifié",
  "description": "Nouvelle description"
}
```
- **Résultat attendu :** `200 OK` avec l’objet mis à jour.

---

## 🔹 Supprimer un élément (DELETE)

```js
app.delete('/items/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const index = items.findIndex(i => i.id === id);

  if (index === -1) {
    return res.status(404).json({ error: 'Item non trouvé' });
  }

  items.splice(index, 1);
  res.status(204).send();
});
```

**Test Postman :**  
- **Méthode :** DELETE  
- **URL :** `http://localhost:3000/items/1`  
- **Résultat attendu :** `204 No Content` (aucune réponse JSON).

---

# 5️⃣ Tableau récapitulatif des routes

| Méthode | URL | Description | Code attendu |
|----------|-----|--------------|---------------|
| GET | `/items` | Récupère tous les items | 200 |
| GET | `/items/:id` | Récupère un item spécifique | 200 / 404 |
| POST | `/items` | Crée un nouvel item | 201 / 400 |
| PUT | `/items/:id` | Met à jour un item | 200 / 400 / 404 |
| DELETE | `/items/:id` | Supprime un item | 204 / 404 |

---

# 6️⃣ Améliorations possibles

- Ajouter une validation plus stricte sur les champs.  
- Implémenter une route `/search` pour filtrer par nom.  
- Persister les données dans un fichier JSON.  
- Gérer des erreurs plus complexes (500, champs manquants, etc.).  

---

# 🧩 Structure finale du projet

```
express-crud-workshop/
├─ package.json
├─ index.js
└─ README.md
```

---

# ✅ Résumé

Tu as mis en place une API Express simple avec :
- Un stockage en mémoire
- 5 routes CRUD complètes
- Gestion des status codes HTTP
- Tests via Postman
