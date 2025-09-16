PRACTICE QUESTIONS
--------------------
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

-------------
CHAPTER 1
SUPBASE DB
-------------

const { data, error } = await request.supabase
  .from(Tables.notifications)
  .select('*, createdBy:created_by(*)')
  .or('user_ids.is.null,user_ids.cs.{H}');

reads all tables where user_ids is null or user_ids contains H

const { data, error } = await request.supabase
  .from(Tables.notifications)
  .select('*, createdBy:created_by(*)')
  .or('user_ids.is.null,user_ids.ov.{H}');
  
  
  
  
  
-------------------
CHAPTER 2
NESTJS
--------------------

# create a new scaffold project
$ nest new project-name

To quickly create a CRUD controller with built-in validation, 
you can use the CLI's CRUD generator: 
$ nest g resource [name]

@Request(), @Req()	req
@Response(), @Res()*	res
@Next()	next
@Session()	req.session
@Param(key?: string)	req.params / req.params[key]
@Body(key?: string)	req.body / req.body[key]
@Query(key?: string)	req.query / req.query[key]
@Headers(name?: string)	req.headers / req.headers[name]
@Ip()	req.ip
@HostParam()	req.hosts

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



****************
2.1 Middleware
****************

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

******************
2.2 Database
******************
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
      entities: [], //or all directories with entities inside     entities: ["entity/*.js"],
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

One-to-one	Every row in the primary table has one and only one associated 
row in the foreign table. Use the @OneToOne() decorator to define this type of relation.

One-to-many / Many-to-one	Every row in the primary table has one or more 
related rows in the foreign table. Use the @OneToMany() and @ManyToOne() 
decorators to define this type of relation.

Many-to-many	Every row in the primary table has many related rows in the foreign table, 
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


--------------------
CHATPER 3
POSTGRESQL
--------------------

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
$ \dt (or \dt public.*, (public is the schema))

change default schema
$ SET search_path TO hr;

descibe a table
$ \d table_name

descibe a table with extra information
$ \d+ table_name











-----------------
Misc
-----------------
Prettier

1. in vscode install extension (Prettier-code formatter)
2. in .vscode/settings.json
    {
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "[javascript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    }
    }
3. npm install --save-dev prettier

