# Bureau de Change — Taux de change XPF

Application Vue.js affichant en temps réel les taux de change basés sur le Franc Pacifique (XPF), avec mise à jour automatique toutes les heures.

Accessible via ce site : https://exchangeratecurrencymoney.netlify.app
---

## Aperçu

- Saisie d'un montant en XPF et conversion instantanée vers 12 devises
- Drapeaux des pays via [Flag Pedia](https://flagpedia.net)
- Mise à jour automatique toutes les heures (setInterval)
- Affichage de la date et heure de la dernière mise à jour
- Deux implémentations Vue.js : **Options API** et **Composition API**
- Interface responsive (CSS Grid + media queries)

---

## Prérequis

- [Node.js](https://nodejs.org) ≥ 14
- npm ≥ 6

---

## Installation

```bash
npm install
```

---

## Configuration de la clé API

L'application utilise [ExchangeRate-API](https://www.exchangerate-api.com) pour récupérer les taux de change.

1. Créez un compte gratuit sur [exchangerate-api.com](https://www.exchangerate-api.com) pour obtenir une clé API
2. Créez un fichier `.env.local` à la racine du projet :

```env
VUE_APP_EXCHANGE_API_KEY=VOTRE_CLE_API
```

> Si la clé est absente, l'application utilise automatiquement le fichier local `public/rates.json` (recommandé pendant le développement pour ne pas consommer les 1500 appels mensuels du plan gratuit).

---

## Lancer en développement

```bash
npm run serve
```

L'application est accessible sur [http://localhost:8080](http://localhost:8080).

---

## Compiler pour la production

```bash
npm run build
```

Les fichiers sont générés dans le dossier `dist/`.

---

## Utilisation

| Route | Description |
|---|---|
| `/` | Version **Options API** |
| `/composition` | Version **Composition API** |

1. Ouvrir l'application dans un navigateur
2. Saisir un montant en XPF dans le champ en haut
3. Les montants convertis se mettent à jour instantanément pour chaque devise
4. La date de dernière mise à jour est affichée avec un indicateur vert

---

## Devises affichées

| Code | Devise | Pays |
|---|---|---|
| AUD | Dollar australien | Australie |
| NZD | Dollar néo-zélandais | Nouvelle-Zélande |
| CAD | Dollar canadien | Canada |
| USD | Dollar américain | États-Unis |
| FJD | Dollar fidjien | Fidji |
| SGD | Dollar de Singapour | Singapour |
| THB | Baht thaïlandais | Thaïlande |
| CHF | Franc suisse | Suisse |
| EUR | Euro | France / Zone euro |
| GBP | Livre sterling | Royaume-Uni |
| JPY | Yen japonais | Japon |
| VUV | Vatu | Vanuatu |

---

## Structure du projet

```
src/
├── components/
│   ├── ExchangeBoard.vue           # Options API
│   └── ExchangeBoardComposition.vue # Composition API
├── views/
│   ├── HomeView.vue                # Route /
│   └── CompositionView.vue         # Route /composition
├── router/
│   └── index.js
├── App.vue
└── main.js
public/
└── rates.json                      # Données locales (fallback dev)
```

---

## Technologies

- [Vue.js 3](https://vuejs.org) — Options API & Composition API
- [Vue Router 4](https://router.vuejs.org)
- [Vue CLI 5](https://cli.vuejs.org)
- [ExchangeRate-API](https://www.exchangerate-api.com) — taux de change
- [Flag Pedia](https://flagpedia.net) — drapeaux des pays
- CSS Grid & Flexbox — mise en page responsive
