# vue-horizontal-tree
A horizontal tree diagram builder made with Vue.js and HTML Drag&amp;Drop API
## info
This is a horizontal tree diagram builder. It uses the native HTML Drag&Drop API to construct the diagram.

Notes:
- Any node can be added as a child of any other node (minus signs are the drop zones for a node to be dropped).
- If You drag the root node and make it a child of another node, then the left of the root node should become a new root node.
- Each node can have only 2 children.
- Is tested only on Chrome and Firefox, and only on wide screens for now
## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration