# App configuration, setting up loggers and handling errors properly.

source: <br>
[NestJs built in validation documentation](https://docs.nestjs.com/techniques/validation#using-the-built-in-validationpipe)


```bash
$ npm i --save class-validator class-transformer
```

```typescript
app.setGlobalPrefix('api/v1');

app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      forbidNonWhitelisted: true,
      transform: true,
      forbidUnknownValues: true,
    })
);

/*
When whitelist is set to true, this will automatically remove non-whitelisted properties (those without any decorator in the validation class).
Alternatively, you can stop the request from processing when non-whitelisted properties are present, and return an error response to the user. To enable this, set the forbidNonWhitelisted option property to true, in combination with setting whitelist to true.
*

/*
Hint
Since TypeScript does not store metadata about generics or interfaces, when you use them in your DTOs, ValidationPipe may not be able to properly validate incoming data. For this reason, consider using concrete classes in your DTOs.
*/

//Now passing a body to the constructor should handle validations.


import { IsEmail, IsNotEmpty } from 'class-validator';

export class CreateUserDto {
  @IsEmail()
  email: string;

  @IsNotEmpty()
  password: string;
}

```

# Setting up env.

```bash
npm install @nestjs/config
```

in the root module add the ConfigModule.

```typescript
export const configuration = () => (
    {
        port: process.env.PORT
    }
);


@Module({
    imports: [
        ConfigModule.forRoot({
            load: [configuration],
            isGlobal: true,
            envFilePath: '.env'
        })
    ]
})
```

## setup cors.

```typescript
app.enableCors({
    origin: (origin, callback) => {
        if (!origin) {
            return callback(null,true);
        }

        return callback(new Error('Blocked by cors'),false);
    },
    methods: ['GET'],
    credentials: true,
    allowedHeaders: ['Authorization'],
    preflightContinue: false,
    optionsSuccessStatus: 204,
})
```

## Exception handling and loggin
[NestJs exception handling Documentation](https://docs.nestjs.com/exception-filters)
[NestJs logging Documentation](https://docs.nestjs.com/techniques/logger)

Values in the array can be any combination of 
'log', 'fatal', 'error', 'warn', 'debug', and 'verbose'.

### Using the logger for application logging#

```typescript
import { Logger, Injectable } from '@nestjs/common';

@Injectable()
class MyService {
  private readonly logger = new Logger(MyService.name,  { timestamp: true });

  doSomething() {
    this.logger.log('Doing something...');
  }
}
```

### Custom implementation#
```bash
npm install morgan
npm install winston
npm install winston-daily-rotate-file
````



