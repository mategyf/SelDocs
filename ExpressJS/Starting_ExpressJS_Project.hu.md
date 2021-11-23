# ExpressJS web szerver alkalmazás kezdeti lépései

## Git repository

Az Express szerverünket vagy egy meglévő Git repositoryba, vagy egy újonnan létrehozott repoba helyezzük.
Mindkét esetben ajánlott külön alkönyvtárba helyezni az alkalmazász.

Például, amennyiben a Git repo neve `MyProject`, és az alkalmazásunké pedig `MyExpressApp`:

```bash
.
└── MyProject
  ├── .git
  ├── MyExpressApp
  └── ...
```

### Fájlok kihagyása a Git-ből

Erősen ajánlott a Git repository főkönyvtárába tenni egy `.gitignore` fájlt, mely meghatározza, hogy mely fájlokat, alkönyvtárokat NE töltsön fel a `git push` parancs a repónkba.

A Git repó főkönyvtára általában az, amely a `.git` rejtett mappát tartalmazza.

API-kulcsokat, jelszavakat, belépési azonosítókat és egyéb titkos információkat **TILOS feltölteni a repóba!**

```bash
touch babel.config.js
```

Ajánlott tartalom:

```gitignore
# VS, VS Code config
.vs/
.vscode/

# Package dependencies
node_modules/

# Production
build/
dist/

# env variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# logs
logs/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Filesystem files
desktop.ini
.DS_Store

```

<!-- Ajánlott tartalom a `.gitignore` fájlhoz: [Példa .gitignore](./examples/.gitignore.example).
(A fájlnév végéről töröld a "`.example`"-t) -->

## Node inicializálása

A Node project inicializálásához szükséges, hogy a Node.JS telepítve legyen a számítógépünkre. Csomagkezelésre `yarn`-t használunk.

Lépjünk be az Express applikáció alkönyvtárába:

```bash
cd MyExpressApp
```

és indítsuk el a projekt inicializálását:

```bash
yarn init -y
```

> A `-y` kapcsoló alapértelmezett beállításokat ad meg.

Az express-hez szükséges néhány npm csomag telepítése. Egy részüket nem szükséges az alkalmazás `dependencies` listájába tenni (mely az éles, production alkalmazásunk részeivé válnak), elég a fejlesztéskor használt `devDependencies` listára felvenni. Bizonyos csomagok szükségesek, ezek nélkül nem fog futni az alkalmazás.

Vannak melegen ajánlott csomagok, melyek egyrészt megkönnyítik életünket, másrészt megkönnyíti kollégáink életét, harmadrészt szép, clean kód írására sarkallnak minket.

Az ajánlott csomagok hasznos funkciókkal bővítik az alkalmazást.

## Szükséges Node csomagok telepítése

Az alap Express npm csomag, melyet szinte mindig CORS-szal együtt használunk:

```bash
yarn add express cors
```

## Melegen ajánlott csomagok

### Nodemon

A **nodemon** gyakorlatilag arra való, hogy fejlesztés közben mindig újraindítsa a szerverünket, ha a figyelt alkönyvtárakban (nálunk például `./src`) megváltozik egy kódfájl. Jellegéből adódóan a dev függőségekhez adjuk.

```bash
yarn add --dev nodemon
```

> Használata egyszerű: a `node` parancs helyett `nodemon`-t használunk (majd).

### Dotenv - környezeti változók kezelése

Webszerverünk egyes beállításait, hozzáférési kulcsait érdemes egy környezeti változókat definiáló fájlban tartani. Nevezzük `.env`-nek, és mindjárt adjunk is hozzá egy példa-fájlt.

```bash
touch .env .env.example
```

A lényeg, hogy az `.env` fájlunk, amennyiben a példaként megadott `.gitignore`-fájlt használjuk, nem kerül feltöltésre a Git repositoryba. Hogy programozó társainknak mégis legyen elképzelése, hogy milyen környezeti változókat kell megadniuk a saját oldalukon az alkalmazás működtetéséhez, a példa fájlba csak a változók neveit (vagy publikus tesztadatokat) hagyjunk meg.

> A példa kedvéért így néz ki egy .env fájl:
>
> ```file
> PORT=4000
> DB_CONNECT=mongodb://127.0.0.1:27017/MyDB
> JWT_KEY=12345678
> ```
>
> és a hozzá tartozó .env.example:
>
> ```file
> PORT=
> DB_CONNECT=
> JWT_KEY=
> ```

Ezen környezeti változók beolvasásához szükség van a **dotenv** npm csomagra, mégpedig production szinten.

```bash
yarn add dotenv
```

### Babel

A Babel-t arra használjuk, hogy produkciós kódunkat egyrészt kompatibilissá tegye (hogy mivel, azt külön meghatározhatjuk), másrészt hatékonyabbá és kisebb méretűvé változtassa. A dev függőségekhez adjuk.

```bash
yarn add --dev @babel/core @babel/node @babel/cli @babel/preset-env
```

> Egyik előnye, hogy kezeli az ES6 module-okat, így nincs szükség a `package.json`-ba berakni, hogy `"type": "module"`.
> Később konfiguráljuk, de [itt a leírása](https://babeljs.io/docs/en/).

### Tesztelés

Teszteléshez ajánlott a Supertest csomag, amely express végpontok integration tesztelését teszi lehetővé. Többféle keretrendszerrel (vagy akár anélkül is) működik, de most a népszerű Jest csomaggal együtt telepítsük. Mindkettőt természetesen a dev függőségek közé.

```bash
yarn add --dev supertest jest
```

> Részletesebben [a supertest-ről](https://www.npmjs.com/package/supertest). Ha bonyolultabb teszt kell, akkor a supertest alapja, a [superagent leírása](https://visionmedia.github.io/superagent/) segíthet. Általánosságban pedig a jest tesztelési kertetrendszer [leírása](https://jestjs.io/docs/getting-started) adhat támpontokat.

~~### babel-jest? types/jest?~~

```bash
yarn add --dev babel-jest @types/jest
```

### Linter, avagy a kódpiszkáló

A [linter](<https://en.wikipedia.org/wiki/Linter_(software)>) arra szolgál, hogy kódunkban kikeresse a szintaktikai vagy stílushibákat, bizonytalan és potenciálisan veszélyes elemeket, majd jelezze, sőt, megfelelően konfigurálva kijavítsa ezeket. A mai kódszerkesztők többsége alkalmas arra, hogy gépelés közben is használja ezeket a funkciókat, valós időben jelezve a hibákat.

A mi alkalmazásunkhor **eslint**-et használunk, az ún. **airbnb** plugin-nal. A dev függőségekbe kerülnek.

```bash
yarn add --dev eslint eslint-plugin-import eslint-plugin-jest eslint-restricted-globals eslint-config-airbnb-base
```

Valamiért az `eslint-plugin-jest` igényli a `typescript`-et. Nem szükséges, de egy warningot kiiktathatunk:

```bash
yarn add --dev typescript
```

### Logging

A webszerver működését ajánlott megfelelő részletességű logok vezetésével támogatni. Egy egyszerű megoldást nyújt a winston/morgan páros.

```bash
yarn add winston morgan
```

> Később beállítjuk. Leírás angolul: winston, morgan.

yarn add --dev formidable@3

## Adatbázisok

### MongoDB

```bash
yarn add mongoose
```

### T-SQL

```bash
yarn add mssql
```

## Egyéb csomagok

### Sztringek validálása

```bash
yarn add validator
```

### JSON Web Token-ek használata

```bash
yarn add jsonwebtoken bcrypt
```

## Konfigurációs fájlok, egyéb beállítások

### A package.json kiegészítése

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

### babel.config.js létrehozása

```bash
touch babel.config.js
```

Tartalma:

```js
module.exports = {
  presets: [
    [
      '@babel/preset-env',
      {
        targets: {
          node: 'current',
        },
      },
    ],
  ],
};
```

### .eslintrc létrehozása

```bash
touch .eslintrc
```

```json
{
  "root": true,
  "extends": ["eslint:recommended", "airbnb-base"],
  "parserOptions": {
    "ecmaVersion": 2021,
    "sourceType": "module"
  },
  "env": {
    "es6": true,
    "node": true
  },
  "globals": {
    "Atomics": "readonly",
    "SharedArrayBuffer": "readonly",
    "UniqueConstraintError": "readonly"
  },
  "overrides": [
    {
      "files": ["**/*.test.js"],
      "env": {
        "jest": true
      },
      "plugins": ["jest"]
    }
  ]
}
```
