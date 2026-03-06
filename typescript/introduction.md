### How to install typescript

```bash
npm install -g typescript
```


### How to compile and run the code

```bash
tsc hello.ts
```

### Setting up a new express project.

```bash
> npm init
> tsc --init . --target es2023 --module nodenext
```

__Then edit the generated tscconfig.json__
1. set rootDir and outDir
2. Add these
   "expreimentalDecorations": true,
   "emitDecoratorMetadata": true,
   "esModuleInterop": true,
   "strictNullChecks": true,
   "forceConsistenCasingInFileNames": true,
   "verbetimModuleSyntax": false,
   "moduleResolution": "nodenext",
   "noUnusedLocals": true,
   "noUnusedParameters": true,
   "strict": false

### Set up nodemon
```bash
> npm install --save-dev npx
> npm install --save-dev nodemon
> npm install --save-dex tsx
```

1. create nodemon.json
```json
{
  "verbose": true,
  "ext": "ts",
  "ignore": ["dist"],
  "exec": "tsx src/index.ts"
}
```

2. update the dev script in package.json
```json
{
  "dev": "nodemon"
}
```
