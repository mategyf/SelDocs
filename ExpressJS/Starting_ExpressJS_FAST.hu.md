# Gyorsított eljárás

Töltsd le oda, ahol létre fog jönni a `backend` könyvtár és bontsd ki:

## Lépj be az alkönyvtárba

```bash
cd backend
```

## Projekt inicializálás

```bash
yarn init -y
```

## Függőségek

```bash
yarn add express cors dotenv winston morgan
```

```bash
yarn add --dev nodemon @babel/core @babel/node @babel/cli @babel/preset-env supertest jest eslint eslint-plugin-import eslint-plugin-jest eslint-restricted-globals eslint-config-airbnb-base typescript formidable@3
```

## A package.json kiegészítése

```json
  "scripts": {
    "lint": "eslint . --ext .js",
    "lint:fix": "eslint . --ext .js --fix",
    "test": "jest --watchAll --runInBand",
    "test:non-seq": "jest --watchAll",
    "test:ci": "jest --ci --collectCoverage --reporters=default",
    "test:cover": "jest --collectCoverage",
    "start": "nodemon --exec babel-node -r ./node_modules/dotenv/config src/index.js",
    "build": "babel src -d dist",
    "serve": "node -r dotenv/config dist/index.js",
    "serve:prod": "set NODE_ENV=production&&node -r dotenv/config dist/index.js"
  },
```

```json
  "jest": {
    "testEnvironment": "node",
    "coveragePathIgnorePatterns": [
      "/node_modules/"
    ]
  }
```

## Opcionális

### Validáció

```bash
yarn add validator
```

### JWT használat

```bash
yarn add jsonwebtoken bcrypt
```
