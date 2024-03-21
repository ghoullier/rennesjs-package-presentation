---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides, markdown enabled
title: Retour d'experience sur la gestion de paquet npm
# apply any unocss classes to the current slide
class: text-center
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# https://sli.dev/guide/drawing
drawings:
  persist: false
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
transition: slide-left
mdc: true
layout: cover
---

# Retour d'experience sur la gestion de paquet JavaScript

<div class="pt-12 flex">
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/6a/JavaScript-logo.png/600px-JavaScript-logo.png" style="margin: auto; max-height: 200px;">
</div>

---
layout: image-right
image: https://cover.sli.dev
---

# Qui suis-je ?

- Grégory Houllier
- Staff Software Engineer @ContentSquare
- Co-Foundateur de @RennesJS
- X/Twitter/Bluesky: @ghoullier

---

# C'est quoi un paquet npm ?

- Format de publication de code en JavaScript
- Permet de partager du code, des outils, des librairies, des applications
- Solution de-facto pour le partage de code en JavaScript (mais pas standardisée)
    - 🪦 Bower 🦜🪶
- Matérialisé par une archive `package.tgz` contenant du code et un fichier `package.json`


---

# 🩻 Discection d'un package.json

`npm init --yes`

```json
{
  "name": "rennesjs-hello-world",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

<br>

**😭 Pas de support des `commentaires` ni de l'extension `jsonc`**

---

# Metadata

- name
- version
- description
- keywords
- author
- license
- ...

---
transition: slide-up
---

# Scripts

Permet de définir des commandes à exécuter

```json
{
  "scripts": {
    "dev": "vite dev",
    "build": "vite build",
    "preview": "vite preview",
    "test": "vitest"
  }
}
```

Il est possible d'y executer n'importe quelle commande shell (mais attention à la portabilité)

```json
{
  "scripts": {
    "hello": "echo \"Hello World\""
  }
}
```

---
transition: slide-up
---

# Execution via votre package manager

> `(pkg-manager) run (script-name)`

`npm run test`

`yarn run test` / `yarn test`

`pnpm run test` / `pnpm test`

`bun run test`

Possibilité de passer des arguments à vos scripts

`npm run test -- --watch`

`yarn test --watch`

---
transition: slide-up
---

# Scripts hooks

## npm

```json
{
  "scripts": {
    "dependencies": "Appelé apres la modification des node_modules",
    "prepare": "Appelé avant publish et pack",
    "prepublishOnly": "Appelé avant publish",
    "prepack": "Appelé avant pack",
    "postpack": "Appelé avant pack",
    "postinstall": "Appelé apres l'installation du package"
  }
}
```

## yarn

```json
{
  "scripts": {
    "prepack": "...",
    "postpack": "...",
    "postinstall": "...",
    "prepublish": "..."
  }
}
```

---

# ⚠️ Limitations

- Pas de possibilité d'executer des scripts en parallèle ni de dépendances entre eux
- Support des scritps `pre` et `post` uniquement via `npm`

```json
{
  "scripts": {
    "prehello": "echo \"run prehello\"",
    "hello": "echo \"hello\"",
    "posthello": "echo \"run prehello\""
  }
}
```

---
transition: slide-up
---

# 📦 Dependencies

```json
{
    "dependencies": {
        "zod": "^3.22.4"
    },
    "devDependencies": {
        "vite": "^5.2.2"
    },
    "optionalDependencies": {
        "lodash": "^4.17.21"
    },
    "peerDependencies": {
        "react": "^18.2.0"
    }
}
```

---
transition: slide-up
---

# 📦 Dependencies

## `dependencies`

Dépendances directe installées avec votre paquet.

```json
{
    "dependencies": {
        "zod": "^3.22.4"
    }
}
```

---
transition: slide-up
---

# 📦 Dependencies

## `devDependencies`

Dépendances requises le développement de votre paquet: test, build, lint, formatteur, ...

```json
{
    "devDependencies": {
        "vite": "^5.2.2"
    }
}
```

📝 Seul les devDependencies de votre package.json sont installés, celle de vos dépendances ne le sont pas.

💡 Vous pouvez ne pas les installer en production avec le flag `--production`

---
transition: slide-up
---

# 📦 Dependencies

## `peerDependencies`

Dépendances requise mais qui seront installées dans le context d'installation de votre package.

```json
{
    "peerDependencies": {
        "react": "^18.2.0"
    }
}
```

Cas d'utilisation: plugin, librairie, ...

---
transition: slide-up
---

# 📦 Dependencies

## `optionalDependencies`

Dépendances optionnelles (peut utilisé)

```json
{
    "optionalDependencies": {
        "lodash": "^4.17.21"
    }
}
```

```javascript
try {
    const lodash = require('lodash');
    // use lodash
} catch {
    // Fallback
}
```

---
transition: slide-up
---

# Semver

Définie une version sous la forme `X.Y.Z`

- `^` Major version fixé (`>=X` et `<X+1`)
- `~` Major et minor version fixé (`>=X.Y` et `<X.Y+1`)
- `latest` Dernière version

```json
{
    "dependencies": {
        "zod": "^3.22.4"
    }
}
```

Il est possible de fixer une version précise ou une version qui ne respecte pas semver

```json
{
    "dependencies": {
        "typescript": "5.5.0-dev.20240321",
        "@types/node": "ts5.5"
    }
}
```

---

# Semver

## 😃 Avantages

- Définie une plage de version
- Permet de ne mettre à jour que le `lockfile` sans modifier le fichier `package.json`

## 🤬 Inconvénients

- Contrainte pour les mainteneurs de librairies
- Certaines librairies ne respectent pas les règles de semver (👋 TypeScript)
- Le typage fait partie de votre API, modifier un type peut introduire des breaking changes sans modification du comportement de votre librairie

---
transition: slide-up
---

# Entry points

```json
{
    "main": "dist/index.js",
    "module": "dist/index.mjs",
    "types": "dist/index.d.ts",
    "bin": {
        "rennesjs-hello-world": "dist/cli.js"
    },
    "exports": {
        ".": {
            "types": "./dist/index.d.ts",
            "import": "./dist/index.mjs",
            "require": "./dist/index.js"
        }
    },
    "files": [
        "dist"
    ]
}
```

---
transition: slide-up
---

# Entry points

### Legacy

- `main` Point d'entrée pour les applications NodeJS
- `module` Point d'entrée pour les applications ESM
- `types`/`typings` Point d'entrée pour les types TypeScript

### Moderne

- `bin` Point d'entrée pour les applications CLI
- `exports` Mapping des points d'entrée pour les applications `ESM` et `CommonJS`

```json
{
    "exports": {
        ".": {
            "types": "./dist/index.d.ts",
            "import": "./dist/index.mjs",
            "require": "./dist/index.js"
        }
    }
}
```

---
transition: slide-up
---

# Les modules non-standard

Repose sur des fonctions disponibles au runtime

### CommonJS (NodeJS/Bundlers)

```javascript
const { z } = require('zod');

const schema = z.object({
    name: z.string()
})

module.exports =  { schema  }
```

### AMD (RequireJS)

```javascript
define(['zod'], function(z) {
    const schema = z.object({
        name: z.string()
    })
    return { schema }
})
```

---
transition: slide-up
---

# Les modules en ESM

Standardisation de la syntax des import/export de modules en JavaScript depuis ES2015.

```javascript
import { z } from 'zod';

const schema = z.object({
    name: z.string()
})

export { schema  }
```

Les imports dynamiques sont supportés à partir d'ES2020 et sont nécésairement asynchrone. Top-level await est introduit en ES2022.

```javascript
await import(`./${name}.js`)
```

---
transition: slide-up
---

# Le support des modules ESM

### Browsers

A partir de 2018, tout les navigateurs (evergreen) supportent les modules ESM.

```javascript
import * as React from "https://esm.sh/react@18"
```

<div class="pt-12 text-center">
    <img src="https://raw.githubusercontent.com/alrra/browser-logos/master/src/main-desktop-browser-logos.png" style="margin: auto; max-height: 15vh;">
</div>

---
transition: slide-up
---

# Le support des modules ESM

### NodeJS

Support des modules ESM depuis NodeJS 12 (2019), mais au prix de **grosses** limitations.

- Extension `.mjs` requise pour les projets existants
- Nouvelle propriété `type: module` dans le `package.json` pour activer `ESM` dans les fichiers `.js`

```json
{
    "type": "module"
}
```

- Ajout de l'extension `.cjs` pour conserver l'API `CommonJS`
- 😭 Impossibilité de mélanger les deux syntaxes dans un même fichier
- 😭 Impossibilité de `require` un module ESM depuis un module `CommonJS`

---
transition: slide-up
---

# Le support des modules ESM

### NodeJS

Ces limitations sont des irritants pour l'ensemble de la communauté JavaScript.

- Support toujours incomplet dans de nombreux outils (ESLint, Jest, ...)
- Utilisation de bundlers pour contourner les limitations
    - `webpack`
    - `rollup`
    - `vite`
    - `esbuild`
- Apparition de nouvelles runtime
    - `deno`
    - `bun`

---
transition: slide-up
---

# 📊 État de l'écossytème

<div class="pt-12 flex">
    <img src="https://pbs.twimg.com/media/GGx8R7dWQAAuB3g?format=jpg&name=medium" style="margin: auto; max-height: 30vh;">
</div>
<div class="pt-12 text-center">
    <a href="https://github.com/wooorm/npm-esm-vs-cjs">github.com/wooorm/npm-esm-vs-cjs</a>
</div>

---
transition: slide-up
layout: image-right
image: ./images/commonjs-is-hurting-javascript-cover.png
---

# `Deno` vs `Bun`

### `Deno`

- TypeScript
- ESM Only
- [CommonJS is hurting JavaScript](https://deno.com/blog/commonjs-is-hurting-javascript)

### `Bun`

- TypeScript
- CommonJS, ESM
- [CommonJS is not going away](https://bun.sh/blog/commonjs-is-not-going-away)

---

# Le support des modules ESM

### Des lueurs d'espoir ?

<div class="pt-12 flex">
    <img src="https://pbs.twimg.com/card_img/1769671222990983168/UaFlWcjY?format=jpg&name=medium" style="margin: auto; max-height: 30vh;">
</div>
<div class="pt-12 text-center">
    <a href="https://github.com/nodejs/node/pull/51977">github.com/nodejs/node/pull/51977</a>
</div>

---

# Comment publier un paquet npm ?

Utiliser des outils pour valider vos paquets avant publication

### `Lint`

- [eslint-plugin-package-json](https://www.npmjs.com/package/eslint-plugin-package-json): plugin eslint pour valider votre `package.json`
- [publint](https://www.npmjs.com/package/publint): Valide la configuration de vos entry points
- [@arethetypeswrong/cli](https://www.npmjs.com/package/@arethetypeswrong/cli): Valide la configuration de vos types

<br>

### `Publish`

- [dnt](https://github.com/denoland/dnt): Permet la publication de module `Deno` vers `npm`
- [np](https://www.npmjs.com/package/np): Permet de publier votre paquet en mode interactif avec des options de validation
- [clean-publish](https://www.npmjs.com/package/clean-publish): Supprime les propriétes inutiles de votre `package.json`

---
transition: slide-up
---

# Pourquoi garder ses dépendances à jour ?

<br>

Mettre à jour vos dépendances vous permet de bénéficier des dernières fonctionnalités, des correctifs de sécurité.


Maintenir de vos dépendances à jour est un investissement rentable pour tout les projets sur lesquels vous devez intervenir.

---
transition: slide-up
---

# Comment garder ses dépendances à jour ?

<br>

### Manuellement

- `yarn upgrade-interactive`
- [npm-check-updates](https://www.npmjs.com/package/npm-check-updates)

<br>

### Automatiquement

- [dependabot](https://github.com/dependabot): Automated dependency updates built into GitHub:
- [renovate](https://github.com/renovatebot/renovate): Universal dependency automation tool.

---

# En vrai comment ça marche?

<br>

## 🪄 Mini demo time

- [github.com/ghoullier/number-safe-parse](https://github.com/ghoullier/number-safe-parse)
- [github.com/ghoullier/bun-typescript-template](https://github.com/ghoullier/bun-typescript-template)

---
transition: slide-up
---

# Package Registry

Serveur de stockage de paquets

## npm

- Acquis par GitHub en 2020
- cli open-source mais serveur closed-source

<br>

### Statistiques

- 2.7 millions de paquets
- 60 milliards de téléchargements par semaine
- 260 milliards de téléchargements par mois
- Utilisé par plus 17 milions de developpeurs

---
transition: slide-up
---

# Il n' a pas que `npm` dans la vie

- github registry
- artifactory
- nexus
- verdaccio
- jsrio

---

# `jsr.io`

The open-source package registry for modern JavaScript and TypeScript

## Fonctionnalités

- Client et Serveur OpenSource
- ESM Only
- Transpilation on-the-fly
- Compatibilité avec npm

---
layout: image-right
image: https://cover.sli.dev
---

# 🙏 Merci

## Questions ?

<div class="pt-12 flex">
    <img src="/images/qr-code.png" style="margin: auto; max-height: 30vh;">
</div>
