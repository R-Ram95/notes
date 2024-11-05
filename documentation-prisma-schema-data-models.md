# documentation-prisma-schema-data-models

**Datasources**
-  where we define the connection to our database

``` prisma.schema
datasource db {
  provider = "postgresql"
  url      = "postgresql://johndoe:mypassword@localhost:5432/mydb?schema=public"
}
```

**Generators**
-  determines which assets are created when you run `prisma genera `

``` prisma.schema
generator client {
  provider = "prisma-client-js"
  output   = "./generated/prisma-client-js"
}
```

**Data Modelling**

1) Write data models and use `prisma migrate`
    [[documentation-prisma-migrate]]
2) Generate model using *introspection* 
    -  i.e. we have an existing DB and prisma generates the models based
    on the data in the DB
    [[documentation-prisma-introspection]]

    
