```bash
npm install typeorm
npm install pg
npm install reflect-metadata
npm install @types/node --save-dev
```

and import it somewhere in the global place of 
your app (for example in app.ts):

import "reflect-metadata"


then Install your database driver.

#### TypeScript configuration

Also, make sure you are using TypeScript version 
4.5 or higher, and you have enabled the following 
settings in tsconfig.json:

"emitDecoratorMetadata": true,
"experimentalDecorators": true,


### connecting to the database

```typescript
export const AppDataSource = new DataSource({
  type: "postgres",
  host: process.env.DB_HOST || "localhost",
  port: Number(process.env.DB_PORT) || 5432,
  username: process.env.DB_USER || "test",
  password: process.env.DB_PASSWORD || "test",
  database: process.env.DB_NAME || "test",
  synchronize: true,
  logging: true,
  namingStrategy: new SnakeNamingStrategy(),
  entities: [UserEntity, AuthTokenEntity, __dirname + "/../entities/**/*.ts"],
  subscribers: [],
  migrations: [],
});
```

then in index.js

```typescript
AppDataSource.initialize()
  .then(() => {
    app.listen(process.env.PORT, () => {
      console.log("Server is running on port " + process.env.PORT);
    });
  })
  .catch((error) => {
    new ErrorLogger().log(`Database connection failed ${error}`);
  });
```