```bash
npm install --save @nestjs/typeorm typeorm pg
npm install typeorm-naming-strategies
```

### Creating entities (tables)

```typescript
@Entity()
export class Users {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  firstName: string;

  @Column()
  lastName: string;

  @Column({
    unique: true,
  })
  email: string;

  @Column()
  countryCode: string;

  @Column({
    unique: true,
  })
  phoneNumber: string;

  @Column()
  password: string;

  @Column({ default: "UTC" })
  timeZone: string;

  @CreateDateColumn({ type: "timestamptz" })
  createdAt: Date;

  @UpdateDateColumn({ type: "timestamptz" })
  updatedAt: Date;

  @DeleteDateColumn({ type: "timestamptz", nullable: true })
  deletedAt: Date;

  @Column({ type: "timestamptz", nullable: true })
  lastSeen: Date;

  @Column({
    enum: UserRole,
    enumName: "user_roles",
  })
  role: UserRole;

  @OneToMany(() => AuthTokens, (authToken) => authToken.user)
  authTokens: AuthTokens[];
}
```

```typescript
@Entity()
export class AuthTokens {
  @PrimaryGeneratedColumn()
  id: string;

  @Column()
  accessToken: string;

  @Column()
  refreshToken: string;

  @ManyToOne(() => Users, (user) => user.authTokens)
  user: Users;
}
```

Then in the root module.

```typescript
@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: "postgres",
      host: "localhost",
      port: 5432,
      username: "bilal",
      password: "12345678",
      database: "trash_app_admin",
      entities: [Users, AuthTokens],
      synchronize: true,
      namingStrategy: new SnakeNamingStrategy(),
      logging: true,
      dropSchema: true,
    }),

    AuthModule,
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```
