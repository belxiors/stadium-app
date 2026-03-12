# stadium-app

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VS Code](https://code.visualstudio.com/) + [Vue (Official)](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Recommended Browser Setup

- Chromium-based browsers (Chrome, Edge, Brave, etc.):
  - [Vue.js devtools](https://chromewebstore.google.com/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
  - [Turn on Custom Object Formatter in Chrome DevTools](http://bit.ly/object-formatters)
- Firefox:
  - [Vue.js devtools](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)
  - [Turn on Custom Object Formatter in Firefox DevTools](https://fxdx.dev/firefox-devtools-custom-object-formatters/)

## Customize configuration

See [Vite Configuration Reference](https://vite.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```

### How did I handle creating the tree and its problems

- Create a map with key Ids and its items
- Create a map with parent as key and its children
- Iterate over all items and check the ones with parentId = null, lets start with them
- Call the create node function that:
  - Gets the item by id
  - Checks if I have seen this id before in this sequence. If that is so it is because this is a circular reference (isCircular = true)
  - Adds the id to the path
  - Gets the children of this item and calls again createNode
  - This goes until
    - There is no more children
    - OR it detects a circular reference

- Checks if there is items with missing parents (orphans)
  - If the parentId is defined and if there is no id in or byId map

- On all other items that we have not visited, iterates over the and defines them as 'detached'
