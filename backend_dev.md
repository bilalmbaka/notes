## PRACTICE QUESTIONS

1. create a nestjs scaffold app.
2. using supabase read all tables where user_ids is null or user_ids contains H.
3. install supabase and typescript.
4. how to connect to postgresql as root.
5. create a new db postgresql.
6. create a new user in postgresql.
7. grant all priviledge and change ownership of table to a user in postgresql.
8. view all databases in postgresql.
9. delete a database in postgresql.
10. login as new user in postgresql.
11. create a table in postgresql.

npm install -g @nestjs/cli
npm install -g typescript
npm list @nestjs/cli

---

CHAPTER 1
SUPBASE DB

---

const { data, error } = await request.supabase
.from(Tables.notifications)
.select('_, createdBy:created_by(_)')
.or('user_ids.is.null,user_ids.cs.{H}');

reads all tables where user_ids is null or user_ids contains H

const { data, error } = await request.supabase
.from(Tables.notifications)
.select('_, createdBy:created_by(_)')
.or('user_ids.is.null,user_ids.ov.{H}');

---

CHAPTER 2
NESTJS

---

# create a new scaffold project

$ nest new project-name

To quickly create a CRUD controller with built-in validation,
you can use the CLI's CRUD generator:
$ nest g resource [name]

@Request(), @Req() req
@Response(), @Res()\* res
@Next() next
@Session() req.session
@Param(key?: string) req.params / req.params[key]
@Body(key?: string) req.body / req.body[key]
@Query(key?: string) req.query / req.query[key]
@Headers(name?: string) req.headers / req.headers[name]
@Ip() req.ip
@HostParam() req.hosts

To ignore non whitelisted data in param

app.useGlobalPipes(
new ValidationPipe({
whitelist: true,
}),
);

To auto convert the types in a query param

@Post()
@UsePipes(new ValidationPipe({ transform: true }))
async create(@Body() createCatDto: CreateCatDto) {
this.catsService.create(createCatDto);
}

app.useGlobalPipes(
new ValidationPipe({
transform: true,
}),
);

@Get(':id')
findOne(
@Param('id', ParseIntPipe) id: number,
@Query('sort', ParseBoolPipe) sort: boolean,
) {
console.log(typeof id === 'number'); // true
console.log(typeof sort === 'boolean'); // true
return 'This action returns a user';
}

---

2.1 Middleware

---

https://docs.nestjs.com/middleware

You implement custom Nest middleware in either a function,
or in a class with an @Injectable() decorator.
The class should implement the NestMiddleware interface, while the
function does not have any special requirements. Let's start by
implementing a simple middleware feature using the class method.

import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
use(req: Request, res: Response, next: NextFunction) {
console.log('Request...');
next();
}
}

---

2.2 Database

---

https://docs.nestjs.com/techniques/database

$ npm install --save @nestjs/typeorm typeorm pg

import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
imports: [
TypeOrmModule.forRoot({
type: 'postgres',
host: 'localhost',
port: 3306,
username: 'root',
password: 'root',
database: 'test',
entities: [], //or all directories with entities inside entities: ["entity/*.js"],
synchronize: true, //Setting synchronize: true shouldn't be used in production - otherwise you can lose production data.
}),
],
})
export class AppModule {}

Then to use it in a route

import { DataSource } from 'typeorm';

@Module({
imports: [TypeOrmModule.forRoot(), UsersModule],
})
export class AppModule {
constructor(private dataSource: DataSource) {}
}

#Repository pattern

import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class User {
@PrimaryGeneratedColumn()
id: number;

@Column()
firstName: string;

@Column()
lastName: string;

@Column({ default: true })
isActive: boolean;
}

#Relations
Relations are associations established between two or more tables.
Relations are based on common fields from each table, often involving
primary and foreign keys.

There are three types of relations:

One-to-one Every row in the primary table has one and only one associated
row in the foreign table. Use the @OneToOne() decorator to define this type of relation.

One-to-many / Many-to-one Every row in the primary table has one or more
related rows in the foreign table. Use the @OneToMany() and @ManyToOne()
decorators to define this type of relation.

Many-to-many Every row in the primary table has many related rows in the foreign table,
and every record in the foreign table has many related rows in the primary table.
Use the @ManyToMany() decorator to define this type of relation.

example:

import { Entity, Column, PrimaryGeneratedColumn, OneToMany } from 'typeorm';
import { Photo } from '../photos/photo.entity';

@Entity()
export class User {
@PrimaryGeneratedColumn()
id: number;

@Column()
firstName: string;

@Column()
lastName: string;

@Column({ default: true })
isActive: boolean;

@OneToMany(type => Photo, photo => photo.user)
photos: Photo[];
}

#TypeORM Transactions

@Injectable()
export class UsersService {
constructor(private dataSource: DataSource) {}
}

async createMany(users: User[]) {
const queryRunner = this.dataSource.createQueryRunner();

await queryRunner.connect();
await queryRunner.startTransaction();
try {
await queryRunner.manager.save(users[0]);
await queryRunner.manager.save(users[1]);

    await queryRunner.commitTransaction();

} catch (err) {
// since we have errors lets rollback the changes we made
await queryRunner.rollbackTransaction();
} finally {
// you need to release a queryRunner which was manually instantiated
await queryRunner.release();
}
}

or

async createMany(users: User[]) {
await this.dataSource.transaction(async manager => {
await manager.save(users[0]);
await manager.save(users[1]);
});
}

#or
const user = await this.userRepository
.createQueryBuilder('user')
.where('(user.username = :username AND user.password = :password)', {
username: loginDto.identifier,
password: loginDto.password,
})
.orWhere('(user.email = :email AND user.password = :password)', {
email: loginDto.identifier,
password: loginDto.password,
})
.getOne();

const user = await this.userRepository.findOne({
where: [
{ username: loginDto.identifier, password: loginDto.password },
{ email: loginDto.identifier, password: loginDto.password },
],
});

#search

```
            if (dto.id) {
                where.push({ id: ILike(`%${dto.id}%`) });
            }

            if (dto.userName) {
                where.push({ userName: ILike(`%${dto.userName}%`) });
            }

            if (dto.email) {
                where.push({ email: ILike(`%${dto.email}%`) });
            }

            const users = await this.userRepository.find({
                where: where.length > 0 ? where : {},
                skip: Number(dto.startAt ?? '0'),
                take: Number(dto.endAt ?? '20'),
                order: {
                    email: 'ASC',
                    userName: 'ASC',
                },
            });

```

or

using query builder

```
const startAt = Number(dto.startAt ?? 0);
const endAt = Number(dto.endAt ?? 20);

const qb = this.userRepository.createQueryBuilder('user');

// only add filters if values exist
if (dto.id) {
  qb.orWhere('user.id ILIKE :id', { id: `%${dto.id}%` });
}

if (dto.userName) {
  qb.orWhere('user.userName ILIKE :userName', { userName: `%${dto.userName}%` });
}

if (dto.email) {
  qb.orWhere('user.email ILIKE :email', { email: `%${dto.email}%` });
}

// ordering
qb.orderBy('user.email', 'ASC')
  .addOrderBy('user.userName', 'ASC');

// pagination
qb.skip(startAt).take(endAt - startAt);

const users = await qb.getMany();
```

```
            const qb = this.locationRepository
                .createQueryBuilder('location')
                .leftJoinAndSelect('location.pictures', 'pictures')
                .leftJoinAndSelect('location.videos', 'videos')
                .leftJoinAndSelect('location.category', 'category')
                .leftJoinAndSelect('location.createdBy', 'createdBy')
                .leftJoinAndSelect('location.updatedBy', 'updatedBy')
                .leftJoinAndSelect('location.requestedBy', 'requestedBy')
                .leftJoinAndSelect('location.approvedBy', 'approvedBy')
                .leftJoinAndSelect('location.deletedBy', 'deletedBy')
                .leftJoinAndSelect('location.reviews', 'reviews')
                .skip(Number(filter.startAt ?? '0'))
                .take(Number(filter.endAt ?? '20'))
                .orderBy('location.createdAt', 'ASC');

            if (user.role != UserRole.user) {
                qb.withDeleted();
            }

            if (user.role === UserRole.user) {
                filter.approvalStatus = undefined;
                filter.requester = undefined;
                filter.approver = undefined;
                filter.deleter = undefined;
                qb.andWhere('location.approved = :approved', { approved: true });
            }

            if (user.role == UserRole.moderator) {
                filter.approver = undefined;
                filter.deleter = undefined;
            }

            if (filter.name) {
                qb.andWhere('location.name ILIKE :name', { name: `%${filter.name}%` });
            }

            if (filter.address) {
                qb.andWhere('location.address ILIKE :address', { address: `%${filter.address}%` });
            }

            if (filter.category) {
                qb.andWhere('category.id = :catId', { catId: filter.category });
            }

            if (filter.approvalStatus !== undefined) {
                qb.andWhere('location.approved = :approvalStatus', {
                    approvalStatus: filter.approvalStatus,
                });
            }

            if (filter.requester) {
                qb.andWhere('requestedBy.id = :requesterId', { requesterId: filter.requester });
            }

            if (filter.approver) {
                qb.andWhere('approvedBy.id = :approverId', { approverId: filter.approver });
            }

            if (filter.deleter) {
                qb.andWhere('deletedBy.id = :deleterId', { deleterId: filter.deleter });
            }

            //
            // ✅ Distance filter (using geography point with SRID 4326)
            //
            if (filter.sourceLocation && filter.distanceFromSource) {
                const { lat, lng } = JSON.parse(filter.sourceLocation as string); // assume object { lng, lat }

                qb.andWhere(
                    `ST_DWithin(
        geography(location.point),
     geography(ST_SetSRID(ST_MakePoint(:lng, :lat), 4326)),
       :radius
     )`,
                    { lng, lat, radius: filter.distanceFromSource },
                );

                qb.addSelect(
                    `ST_Distance(
     geography(location.point),
     geography(ST_SetSRID(ST_MakePoint(:lng, :lat), 4326))
   )`,
                    'distance',
                );

                // Order by distance (closest → furthest)
                qb.orderBy('distance', 'ASC');
            }

            const { entities, raw } = await qb.getRawAndEntities();
```

###2.3 Guards

```
@Injectable()
export class AuthenticatedUserGuard implements CanActivate {
    constructor(
        @InjectRepository(AccessTokenEntity)
        private accessTokenRepository: Repository<AccessTokenEntity>,

        private authTokenService: AuthTokenService,
    ) {}

    async canActivate(context: ExecutionContext): Promise<boolean> {
        try {
            const request = context.switchToHttp().getRequest();
            const authHeader = request.headers.authorization;

            if (!authHeader || !authHeader.startsWith('Bearer ')) {
                throw new UnauthorizedException('Missing or invalid authorization header');
            }

            const token = authHeader.substring(7); // Remove 'Bearer ' prefix

            const decoded = this.authTokenService.decode(token);

            if (this.authTokenService.hasExpired(token)) {
                throw new ForbiddenException();
            }

            //Fetch the user information.

            const authenticatedUser = await this.accessTokenRepository.findOne({
                relations: {
                    user: true,
                },
                where: {
                    accessToken: token,
                },
                order: {},
            });

            if (!authenticatedUser || authenticatedUser.user.emailVerified == false) {
                throw new ForbiddenException();
            }

            if (authenticatedUser.user.disabled) {
                throw new ForbiddenException(
                    'Your account has been disabled' +
                        (authenticatedUser.user.disabledReason
                            ? ` because of ${authenticatedUser.user.disabledReason}`
                            : ''),
                );
            }

            request.user = authenticatedUser.user;

            return true;
        } catch (e) {
            console.log('Error in auth guard', e);

            if (e instanceof TokenExpiredError) {
                throw new ForbiddenException();
            }

            throw e;
        }
    }
}
```

### This example shows how to handle file uploads.

    @Post('request-location')
    @HttpCode(HttpStatus.CREATED)
    @UseInterceptors(
        FileFieldsInterceptor(
            [
                {
                    name: 'pictures',
                    maxCount: 5,
                },
                {
                    name: 'videos',
                    maxCount: 5,
                },
            ],

            {
                storage: diskStorage({
                    destination: './uploads/',
                    filename: (req, file, cb) => {
                        // keep original name
                        cb(null, file.originalname);
                    },
                }),
                limits: { fileSize: 10 * 1024 * 1024 }, // applies per file
                dest: './uploads/',
                fileFilter: (req, file, cb) => {
                    if (file.mimetype.startsWith('image/') && file.fieldname === 'pictures') {
                        if (file.size > 5 * 1024 * 1024) {
                            return cb(new BadRequestException('image file too large'), false);
                        }

                        cb(null, true);
                    } else if (file.mimetype.startsWith('video/') && file.fieldname === 'videos') {
                        cb(null, true);
                    } else {
                        cb(
                            new BadRequestException(
                                `Only valid ${file.fieldname} files are allowed`,
                            ),
                            false,
                        );
                    }
                },
            },
        ),
    )

    //Documentation
    @ApiConsumes('multipart/form-data')
    @ApiOperation({})
    @ApiExtraModels(LocationModel)
    @ApiBody({
        type: CreateLocationDTO,
    })
    @ApiResponse({
        schema: {
            type: 'object',
            properties: new Status().toDoc(Helpers.swaggerDocPath('LocationModel')),
        },
    })
    createLocation(
        @AuthUser() user: UserEntity,
        @Body() body: CreateLocationDTO,
        @UploadedFiles()
        files: {
            pictures?: any;
            videos?: any;
        },
    ): Promise<ResponseDto<LocationModel>> {
        return this.locationsService.requestLocation(user, body, files.pictures, files.videos);
    }

### 2.4 Interceptors

This run after guards, unlike middlewares which run before guards/interceptors.

```
@Injectable()
export class LastSeenInterceptor implements NestInterceptor {
    constructor(
        @InjectRepository(UserEntity)
        private readonly userRepository: Repository<UserEntity>,
    ) {}

    intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
        const request = context.switchToHttp().getRequest();

        // 🔑 Do your logic here (e.g. update last_seen in DB)
        // If request.user is set by AuthGuard, you can use it
        if (request.user) {
            this.userRepository.update(request.user.id, {
                lastSeen: new Date(),
            });
        }

        return next.handle().pipe(
            tap(() => {
                console.log('last seen logged at', new Date().toISOString());
            }),
        );
    }
}
```

```
@Module({
    imports: [
        ConfigModule.forRoot({
            isGlobal: true,
        }),
        TypeOrmModule.forRoot({
            type: 'postgres' as const,
            port: Number(process.env.DB_PORT),
            username: process.env.DB_USERNAME,
            password: process.env.DB_PASSWORD,
            database: process.env.DB_NAME,
            host: process.env.DB_HOST,
            synchronize: true,
            ssl: false,
            namingStrategy: new SnakeNamingStrategy(),
            entities: [UserEntity, AccessTokenEntity, AssetsEntity],
        }),
        TypeOrmModule.forFeature([UserEntity]),

        AuthModule,
        PriviledgeUserModule,
        UsersModule,
        AssetsModule,
        ShareLocationModule,
        ShareLocationWebsocketGatewayModule,
    ],
    controllers: [AppController],
    providers: [
        AppService,
        {
            provide: APP_INTERCEPTOR,
            useClass: LastSeenInterceptor, // 👈 global interceptor
        },
    ],
})
export class AppModule {}
```

###Socket.io

$ npm i --save @nestjs/websockets @nestjs/platform-socket.io

---

CHATPER 3
POSTGRESQL

---

create a db
$ create database <db_name>;

create a db with owner
$ create database <db_name> owner <username>;

create a new user
$ create user <username> with password '<password>';

grant all priviledge on a db to a user
$ grant all privileges on database <db_name> to <user_name>;
$ GRANT ALL PRIVILEGES ON SCHEMA public TO bilal; //make sure you login as supaadmin to only that db

change owner of database
$ alter database <db_name> owner to <owner>

login as a different user
$ psql -U <user_name> -d <db_name> -h localhost (Take not of the -U capitalization)

list all tables
$ \dt (or \dt public.\*, (public is the schema))

change default schema
$ SET search_path TO hr;

descibe a table
$ \d table_name

descibe a table with extra information
$ \d+ table_name

#relations
One-to-One

Means:
One user can only ever be referred by exactly one other user, and that referrer can only ever refer exactly one user.

This would enforce a strict one-to-one pairing (like a user and their passport).

Many-to-One
Means:
Many users can share the same referrer, but each user still has at most one referrer.

This fits referral programs:

Bob → referred by Alice

Charlie → referred by Alice

Alice herself might also be referred by Dave.

➡️ Example:
One user can invite many others, but each invited user only points back to one referrer.
This is exactly the referrals[] ↔ referredBy pattern.

One-to-Many
It means: “One user (the referrer) can have many referrals (other users).”

This side does not create any column in the table; it just tells TypeORM how to navigate the relation in code.

example:

```sql
CREATE TABLE IF NOT EXISTS public.users
(
    id uuid NOT NULL DEFAULT uuid_generate_v4(),
    created_at timestamp with time zone NOT NULL DEFAULT now(),
    updated_at timestamp with time zone NOT NULL DEFAULT now(),
    deleted_at timestamp with time zone,
    email character varying COLLATE pg_catalog."default" NOT NULL,
    user_name character varying COLLATE pg_catalog."default",
    password character varying COLLATE pg_catalog."default" NOT NULL,
    access_tokens text COLLATE pg_catalog."default" NOT NULL DEFAULT '[]'::text,
    refresh_tokens text COLLATE pg_catalog."default" NOT NULL DEFAULT '[]'::text,
    email_verified boolean NOT NULL DEFAULT false,
    disabled boolean NOT NULL DEFAULT false,
    disabled_reason character varying COLLATE pg_catalog."default",
    otp character varying COLLATE pg_catalog."default",
    otp_sent_at timestamp with time zone,
    last_seen timestamp with time zone,
    referrer_id character varying COLLATE pg_catalog."default" NOT NULL,
    referred_by_id uuid,
    CONSTRAINT "PK_a3ffb1c0c8416b9fc6f907b7433" PRIMARY KEY (id),
    CONSTRAINT "UQ_074a1f262efaca6aba16f7ed920" UNIQUE (user_name),
    CONSTRAINT "UQ_97672ac88f789774dd47f7c8be3" UNIQUE (email),
    CONSTRAINT "FK_a78a00605c95ca6737389f6360b" FOREIGN KEY (referred_by_id)
        REFERENCES public.users (id) MATCH SIMPLE
        ON UPDATE NO ACTION
        ON DELETE SET NULL
)

TABLESPACE pg_default;

ALTER TABLE IF EXISTS public.users
    OWNER to bilal;
```

adding a contraint

```
    CONSTRAINT no_self_referral CHECK (id IS DISTINCT FROM referred_by_id)
```

```
ALTER TABLE public.users
ADD CONSTRAINT no_self_referral
CHECK (id IS DISTINCT FROM referred_by_id);
```

#A simple update

```
update public.users set email='jhondoe@gmail.com' where id='12';

```

##view all enums
$\dT

##view sepcific enum
$\dT+ <enumname>

##delete an enum
$DROP TYPE users_role_enum;

##geometry types.

> sudo apt install postgis
> sudo -u postgres psql
> $ CREATE EXTENSION IF NOT EXISTS postgis
> $ \dT

<br>
<br>
<br>
<br>

# CHAPTER 4

# HTTP REQUEST LOGGING.

<br>
<br>
<br>
<br>
## Misc

Prettier

1. in vscode install extension (Prettier-code formatter)
https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode
2. in .vscode/settings.json
   {
   "editor.formatOnSave": true,
   "editor.defaultFormatter": "esbenp.prettier-vscode",
   "[javascript]": {
   "editor.defaultFormatter": "esbenp.prettier-vscode"
   }
   }
3. npm install --save-dev prettier

# CHAPTER 5

# RAILWAY

## Using the CLI

```bash
> npm i -g @railway/cli
```

```bash
> railway login
```

or browserless

```bash
> railway login --browserless
```

### SSH into Railway

First login via railway login then

```bash
> railway link
```

```bash
> railway ssh
```
