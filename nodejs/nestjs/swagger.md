```bash
npm install @nestjs/swagger
npm install swagger-ui-express
npm install -D @types/swagger-ui-express
```

## Initialization.

```typescript
import {DocumentBuilder,SwaggerModule} from '@nestjs/swagger';


const config = new DocumentBuilder()
    .setTitle('Documentation')
    .setDescription('Documentation description')
    .setVersion('1.0').
    addBearerAuth(
        {
            type: 'http',
            scheme: 'bearer',
            bearerFormat: 'JWT',
            description: 'Enter jwt',
            in: 'header'
        },
        'Reference-name'
    )
    .addTag('auth', 'the auth endpoints')
    .build();

    //Where app is the node entry app
    const document = SwaggerModule.createDocument(app,config);

    //Set where the documentation should be accessible from.
    app.setGlobalPrefix('api/v1');
    SwaggerModule.setup('api/v1/docs',app,document,{
        swaggerOptions: {
            useGlobalPrefix: true,
            persistAuthorizatoin: true,
            customSiteTitle: "App documentation"
        }
    })

```


## Adding a route.

```typescript
//The tag name
@ApiTags('auth')
@Controller('auth')
export class AuthController {
    constructor(...) {}

    @Post('signup')
    @HttpCode(HttpStatus.OK)

    //This generates a description the endpoin
    @ApiOperation(
        {
            summary: 'Signup',
            description: 'Signs up a new user'
        }
    )
}

```


# Generating documentation

```typescript
    @ApiProperty({
        description: 'firstName',
        example: 'jane'
    })
    email: string;
```