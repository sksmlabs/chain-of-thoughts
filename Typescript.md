
```
// Single line initialization
npm init -y && npm i -D typescript ts-node @types/node nodemon && npx tsc --init
```
----
```
// Initialize Project
npm init -y
```
```
// Install Typescript
npm install --save-dev typescript
```
```
Install types/node
npm i --save-dev @types/node
```
```
// Generate Config
npx tsc --init
```
```
// Switch project to ESM

// in package.json
{
  "type": "module"
}

// in tsconfig.json
{
  "compilerOptions": {
    "module": "esnext",
    "moduleResolution": "node",
    "target": "ES2020",
    "verbatimModuleSyntax": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
  }
}
```
```
// Add src
mkdir src
```
---
### Prisma DB

```
npx prisma init --db
```
```
// Create inital schema
// This will create the database and run the migrations.
npx prisma migrate dev --name init
```
```
// Prisma reads your prisma/schema.prisma
// Generate Client
// By default Prisma generates into node_modules/@prisma/client.
npx prisma generate
```
