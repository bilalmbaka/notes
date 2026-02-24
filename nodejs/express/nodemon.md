```bash
npm install --save-dev npx
npm install --save-dev nodemon
npm install --save-dev tsx
```

### nodemon.json
```json
{
    "verbose": true,
    "watch": ["src"],
    "ext": "ts",
    "ignore": ["dist"],
    "exec": "tsx src/index.ts"
}
```

### in package.json

```json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "nodemon",
    "start": "tsx src/index.ts"
},
```