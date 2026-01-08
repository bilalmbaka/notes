# HTTP module

Axios is a richly featured HTTP client package that is widely used. Nest wraps Axios
and exposes it via the built-in HttpModule. The HttpModule exports the HttpService
class, which exposes Axios-based methods to perform HTTP requests.
The library also transforms the resulting HTTP responses into Observables.

```bash
$ npm i --save @nestjs/axios axios
```

Once the installation process is complete, to use the HttpService, first import HttpModule.

```javascript
@Module({
  imports: [HttpModule],
  providers: [CatsService],
})
export class CatsModule {}
```

<br/>
<br/>

_Hint: HttpModule and HttpService are imported from @nestjs/axios package._

<br/>
<br/>

```javascript

@Injectable()
export class CatsService {
  constructor(private readonly httpService: HttpService) {}

  findAll(): Observable<AxiosResponse<Cat[]>> {
    return this.httpService.get('http://localhost:3000/cats');
  }
}

///All HttpService methods return an AxiosResponse wrapped in an Observable object.

```
