# Új npm csomag létrehozása

Most egy scoped, public csomagot hozunk létre.

## Felhasználó megadása

Kell egy npmjs felhasználó. Ha szervezethez akarjuk kötni, akkor egy szervezet is.

Bejelentkezés az npm felhasználóval a terminálban:

```bash
npm login
```

> Ez megkérdezi a felhasználónevet, a jelszót és az e-mail címet, ami publikus lesz (?!??), mert csak.

Elvileg csak egyszer kell bejelentkezni, onnan tudni fogja, ki vagyunk. Kilépéshez az `npm logout`, a felhasználónév leellenőrzéséhez pedig az `npm whoami` parancsokat lehet használni.

## Projekt inicializálása

Létre kell hozni egy alkönyvtárat az npm csomag számára. Szerencsés, ha a neve egyezik a csomag nevével, és érdemes egy saját git repository-val társítani. Vagy létrehozni `git init`-tel, vagy a githubon létrehozni és `git clone`-nal leklónozni.

Ne felejtsünk el bele `.gitignore` [fájl](../defaults/.gitignore.example)-t rakni!

Az alkönyvtárban inicializálni kell a csomagot, megadva a scope-ot (felhasználói scope esetén az npm felhsználónév, szervezet esetén a szervezet npm-beli neve)

```bash
npm init --scope=@my-user
```

1. A csomag neve az alkönyvtár nevét használja alapból, olyasmi lesz, hogy "@my-user/my-npm-package".
2. A verziószámot rendszerint 1.0.0-nak szokták megadni az első publikált változatnak.
3. A leírást érdemes néhány szóban megfogalmazni.
4. Az entry point azt adja meg, hogy mely fájl fogja a csomagunk gyökerét képezni. Itt azt adjuk meg, hogy:

   ```bash
   lib/index.js
   ```

5. Teszt parancsot nem szükséges meghatározni, később szerkeszthetjük közvetlenül is.
6. Meg lehet adni a fentebb létrehozott git repót.
7. Keywords: az npm csomagok közti keresés megkönnyítésére lehet kulcsszavakat megadni. Ráér később.
8. Szerző: megadhatjuk a szervezetet vagy felhasználót, de a json-ban könnyebb szerkeszteni.
9. Elfogadhatjuk az ISC licencet, amennyiben nyílt forrású lesz a csomag.
10. Fogadjuk el, ha minden rendben.

Ezután létrejön a `package.json`.

> A paramétereket lehet utólag is módosítani a `package.json`-ban.

## Babel

A csomagokat érdemes ES6 szabvány szerint írni, viszont emiatt rengeteg bonyodalom van a modulok importálása és exportálása körül. A megoldás a babel használata az npm csomag összerakásához.

Ez úgy történik, hogy a csomagot minden publikálás (és tesztelés) előtt le kell build-elni a `/lib` alkönyvtárba.

> Npm csomagok esetén rendszerint a `/lib` alkönyvtárat használjuk, de másikkal is működik.

### Telepítés

Először is telepítsük a babel-t:

```bash
yarn add --dev @babel/core @babel/node @babel/cli @babel/preset-env
```

Kell egy konfigurációs fájl a csomag főkönyvtárában:

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

### A package.json beállítása

A `package.json`-t a következőkkel kell bővíteni:

A `scripts` részbe:

```json
    "build": "babel src -d lib",
```

> Ez a `/src` alkönyvtár tartalmát build-eli a `/lib` alkönyvtárba.

Egy külön `files` részbe:

```json
  "files": [
    "/lib"
  ],
```

> Ez határozza meg, hogy mely fájlokat fog az npm publikáláskor használni. Ha ezt nem adnánk meg, szükséges lenne kizárni az olyan alkönyvtárakat, mint a `node_modules` vagy az `.env` fájlokat. Lehet létrehozni külön `.npmignore` fájlt, de hiányában az npm a `.gitignore`-t használja.

Ellenőrizzük, hogy a `.gitignore` fájlunkban szerepel-e a `/lib` alkönyvtár kizárása.

`.gitignore`:

```bash
lib/
```

## Kódolás, import/export

Végre nekiállhatunk a kód megírásának. Tegyük szokás szerint egy `/src` alkönyvtárba.

Az src alkönyvtárban szükség lesz egy `index.js` fájlra, amely exportálja a csomagunk importálható objektumait (amennyiben a `package.json` "main" mezőjében azt definiáltuk).

### Default export

Amennyiben default exportot használunk:

```javascript
import logger from './logger';

export default logger;
```

az alkalmazásban, amely felhasználja, a következőképp lehet importálni:

```javascript
import logger from '@mategyf/express-logger';
// ...
```

### Named export

...
...

Amint kész a program, build-elni kell. Ezt **minden változtatás után** végre kell hajtani, értelem szerűen.

```bash
yarn build
```

## Tesztelés

A csomagot fontos előre letesztelni, mert **publikálás után már csak újabb verziószámmal lehet felülírni**, így érdemes kipróbálni, hogy minden rendben működik-e.

A teszteléshez persze szükség van egy alkalmazásra, amely használja az új csomagunkat. Ehhez az alkalmazáshoz manuálisan hozzáadjuk a csomagot, teljes elérési úttal:

```bash
yarn add /path/to/my-npm-package
```

Azt az alkönyvtárat adjuk meg, ahol a csomag package.json-ja van. A csomag minden változtatása után szükség lesz frissíteni az importálást.

> Működik relatív útvonallal is, pl. `yarn add ../my-npm-package`.

Miután egyszer hozzáadtuk a csomagot a teszt alkalmazáshoz, a csomag frissítése után a fenti paranccsal, vagy a

```bash
yarn upgrade
```

paranccsal lehet a build-elt csomagot frissíteni.

### **A tesztelés iterációi tehát így néznek ki:**

1. Kód módosítása, javítása, ezek mentése
2. Az npm csomag alkönyvtárában `yarn build`
3. A tesztelő alkalmazás alkönyvtárában `yarn upgrade`
4. Az alkalmazás futtatása vagy ellenőrzése, hiba esetén vissza 1-re.

## Publikálás

A csomagot ezek után lehet az npm repository-jába publikálni.

```bash
npm publish --access public
```

> Ez egy publikus kiadás lesz. A privát kiadások előfizetéshez kötöttek.
<!--  -->
> A publikálást visszavonni csak a kiadástól számított 72 óráig lehet, vagy ha megfelel bizonyos kritériumoknak.

## Verzióváltás és verziószámok

Publikálás után csak és kizárólag verziószám frissítés után lehet javítást, fejlesztést újra kiadni. A verziószámok kiadásához érdemes [az npm javaslatát](https://docs.npmjs.com/about-semantic-versioning) követni.

Parancssorból így lehet verziószámot növelni:

```bash
npm version <update_type>
```

ahol <update_type> `patch`, `minor` vagy `major` lehet.

Az npm alapvetően a [Semantic Versioning](https://semver.org/) használatát javasolja.
