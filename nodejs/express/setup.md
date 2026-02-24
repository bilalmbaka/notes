```bash
npm init
tsc --init . --target es2023 --module nodenext
```

this edit the tsconfig.json

1. set rootDir and outDir
2. add these

    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,    
    "esModuleInterop": true,
    "strictNullChecks": true,
    "forceConsistentCasingInFileNames": true,

3. set verbatimModuleSyntax to false.
4. set   "moduleResolution": "NodeNext",

```bash
npm install http-status-codes
```


```bash
npm install typeorm
npm install pg
npm install reflect-metadata
npm install @types/node --save-dev
npm install axios
npm install cors
npm install dotenv
npm install jsonwebtoken
npm install morgan
npm install typeorm-naming-strategies
npm install winston
npm install winston-daily-rotate-file
npm install --save-dev @types/cors @types/express @types/nodemon
npm install --save-dev tsx
npm install --save-dev @types/nodemon
npm install --save-dev nodemon
npm install --save-dev prettier
```