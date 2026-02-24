Do not confuse OpenAPI with Swagger. OpenAPI is an 
specification that says how APIs are documented. 
Swagger is a collection of tools to implement the specification.



```bash
npm install swagger-jsdoc
npm install swagger-ui-express
```

The idea is to

1. Document your routes with jsdoc using the swagger YAML notation 
(as you can see in the previous route).

2. Let swagger-jsdoc to autogenerate a swagger.json file with all the documentation you set in your files.

