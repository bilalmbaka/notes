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
   "expreimentalDecorations": true, <br/>
   "emitDecoratorMetadata": true, <br/>
   "esModuleInterop": true, <br/>
   "strictNullChecks": true, <br/>
   "forceConsistenCasingInFileNames": true, <br/>
   "verbetimModuleSyntax": false, <br/>
   "moduleResolution": "nodenext", <br/>
   "noUnusedLocals": true, <br/>
   "noUnusedParameters": true, <br/>
   "strict": false <br/>

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
