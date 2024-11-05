# documentation-prisma-cli

https://www.prisma.io/docs/orm/prisma-schema/introspection

`prisma db pull` 
-  introspects or "pulls" model definitions from DB and puts them in the
    `prisma.schema` file
-  introspection workflow:
        1) change DB schema using plain SQL 
        2) run `prisma db pull` to update the `prisma.schema`
        3) run `prisma generate` to update the Prisma Client
    
`prisma generate`
-  updates the Prisma client using the `prisma.schema`
    

