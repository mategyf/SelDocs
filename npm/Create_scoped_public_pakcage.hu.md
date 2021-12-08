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

> A scope-ot lehet utólag is módosítani a `package.json`-ban, a "name" mező szerkesztésével.

## Babel

A csomagokat érdemes ES6 szabvány szerint írni, viszont emiatt rengeteg bonyodalom van a modulok importálása és exportálása körül. A megoldás a babel használata az npm csomag összerakásához.

Ez úgy történik, hogy a csomagot minden publikálás (és tesztelés) előtt le kell build-elni a /lib alkönyvtárba.

> Npm csomagok esetén szokás a `/lib` alkönyvtár használata, de bármely másikkal is működik.

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

## Kódolás

Végre nekiállhatunk a kód megírásának. Tegyük szokás szerint egy `/src` alkönyvtárba.
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

Azt az alkönyvtárat adjuk meg, ahol a csomag package.json-ja van.

> Működik relatív útvonallal is, pl. `yarn add ../my-npm-package`.

**A tesztelés iterációi tehát így néznek ki:**

1. Kód módosítása, javítása, ezek mentése
2. Az npm csomag alkönyvtárában `yarn build`
3. A tesztelő alkalmazás alkönyvtárában `yarn add /path/to/my-npm-package`
4. Az alkalmazás futtatása vagy ellenőrzése, hiba esetén vissza 1-re.

## Publikálás

```bash
npm publish --access public
```

## Verzióváltás és verziószámok
