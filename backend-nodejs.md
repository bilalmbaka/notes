# Table of Contents

* [Package management](#package)
* [PostgreSQL](#postgresql)
  - [Tables](#tables)
  - [Relations](#relations)
  - [Location](#location-types)
* [Typeorm](#typeorm)
  - [Handling location](#location-types)
* [Deployment](#deployment)
  - [Render](#render)
* [Push notifications](#push-notifications)
  - [Firebase](#firebase)



#

<br/>
<br/>
<br/>
<br/>

#
# PACAKGES

Importing a local package / project in another project. <br/>
In project a create an index.ts that will export all the required files for the importing
package so see them.

* add this to package.json
```json
{
  "name": "auth-lib",
  "version": "1.0.0",
  "main": "dist/index.js", //The entry point JavaScript file of the package, When someone imports auth-lib, Node loads this file.
  "types": "dist/index.d.ts" //The TypeScript type definitions entry point, allows TypeScript projects to get types and autocompletion.
}
```

* add the project to the importing project.
```bash
npm install ../path-to-auth-project
```

which produces.
```json
"auth-lib": "file:../yirapay-shared-resources"
```




#

<br/>
<br/>
<br/>
<br/>

#
# POSTGRESQL

## TABLES
- To list all tables
```sql
\d
```
- To see the schema of a given table
```sql
\d <table_name>
```

- Adding a new column to a table.
```sql
ALTER TABLE users
	ADD COLUMN role role NOT NULL DEFAULT 'user';
```

- Inserting a new record into the table
```sql
INSERT INTO recyclables (name, icon, price, weight_classification) 
VALUES (
  'aluminium', 
  'https://res.cloudinary.com/dxv10krxo/image/upload/v1772807670/aluminium_k3msbg.png', 
  1000, 
  'kg'
);
```

## RELATIONS

#### One-to-One
One-to-one is a relation where A contains only one 
instance of B, and B contains only one instance of A.

__Example__

```ts
import { Entity, PrimaryGeneratedColumn, Column } from "typeorm"

@Entity()
export class Profile {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    gender: string

    @Column()
    photo: string
}

import {
    Entity,
    PrimaryGeneratedColumn,
    Column,
    OneToOne,
    JoinColumn,
} from "typeorm"
import { Profile } from "./Profile"

@Entity()
export class User {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    name: string

    @OneToOne(() => Profile)
    @JoinColumn()
    profile: Profile
}
```

Here we added @OneToOne to the user and specified the 
target relation type to be Profile. We also added @JoinColumn 
which is required and must be set only on one side of the relation. 
The side you set @JoinColumn on, that side's table will contain a 
"relation id" and foreign keys to the target entity table.

Relations can be uni-directional and bi-directional. 
Uni-directional are relations with a relation decorator only on one side. Bi-directional are relations with decorators on both sides of a relation.


```ts
import { Entity, PrimaryGeneratedColumn, Column, OneToOne } from "typeorm"
import { User } from "./User"

@Entity()
export class Profile {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    gender: string

    @Column()
    photo: string

    @OneToOne(() => User, (user) => user.profile) // specify inverse side as a second parameter
    user: User
}

import {
    Entity,
    PrimaryGeneratedColumn,
    Column,
    OneToOne,
    JoinColumn,
} from "typeorm"
import { Profile } from "./Profile"

@Entity()
export class User {
    @PrimaryGeneratedColumn()
    id: number

    @Column()
    name: string

    @OneToOne(() => Profile, (profile) => profile.user) // specify inverse side as a second parameter
    @JoinColumn()
    profile: Profile
}
```

We just made our relation bi-directional. Note, 
inverse relation does not have a @JoinColumn. 
@JoinColumn must only be on one side of the relation 
- on the table that will own the foreign key.




#### Many-to-Many
Many-to-many is a relation where A contains multiple instances of B, 
and B contains multiple instances of A.

@JoinTable() is required for @ManyToMany relations. You must put 
@JoinTable on one (owning) side of relation.


## LOCATION TYPES

You don't store "Lat" and "Lng" as two separate numbers. 
Instead, you store them in a single column of type GEOGRAPHY(Point, 4326).

```sql
CREATE EXTENSION IF NOT EXISTS postgis;
```

```sql
ALTER TABLE users 
ADD COLUMN location GEOGRAPHY(Point, 4326);
```
__NOTE__: 4326 is the standard spatial ID (SRID) for GPS coordinates (WGS 84).

__Inserting data__

When saving a user's location, you use a helper function. 
Crucial: PostGIS uses (Longitude, Latitude) order.

```sql
UPDATE users 
SET location = ST_SetSRID(ST_MakePoint(6.3350, 5.6013), 4326) 
WHERE id = 'user_123';
```

__The "Find Users within 2km" Query__

```sql
SELECT id, name, ST_Distance(location, 'SRID=4326;POINT(6.339 5.608)'::geography) as distance_meters
FROM users
WHERE ST_DWithin(
    location, 
    'SRID=4326;POINT(6.339 5.608)'::geography, 
    2000 -- Distance in meters (2km)
);
```

__Indexing for Speed__

```sql
CREATE INDEX users_location_idx ON users USING GIST (location);
```




#

<br/>
<br/>
<br/>
<br/>

#
# TYPEORM

## HANDING LOCATION DATA USING POSTGRES.

__The column__

```ts
 @Column({
        type: 'geography',
        spatialFeatureType: 'Point',
        srid: 4326,
        nullable: true,
    })
    currentLocation: Point;
```
__Update the column__

```ts
var currentLocation:| undefined| {lat: number;lng: number;};

await this.userRepository.save({
                    id: user.id,
                    userName: dto.userName,
                    profilePicture: picture,
                    currentLocation: {
                        type: 'Point',
                        coordinates: [currentLocation?.lng, currentLocation?.lat],
                    },
                    currentAddress: address,
                } as UserEntity,
                {
                    listeners: dto.currentLocation ? true : false,
                },
  );
```


#

<br/>
<br/>
<br/>
<br/>

# 
# Deployment

## Render

package.json

```json
    "build": "tsc",
    "start": "node dist/index.js"
```

Then add this settings in render settings.



#

<br/>
<br/>
<br/>
<br/>

#
# Push notifications

## Firebase

### Setup the enviroment

Generate a private key file in JSON format, 

1. In the Firebase console, open Settings > Service Accounts.
2. Click Generate New Private Key, then confirm by clicking Generate Key.
3. Securely store the JSON file containing the key.

```bash
npm install firebase-admin
```

```bash
admin.initializeApp({
    credential: admin.credential.cert({
        projectId: this.configService.get('FIREBASE_PROJECT_ID'),
        privateKey: this.configService.get('FIREBASE_PRIVATE_KEY')
            ?.replace(/\\n/g, '\n'), // convert escaped \n to real newlines
        clientEmail: this.configService.get('FIREBASE_CLIENT_EMAIL'),
    })
});
```