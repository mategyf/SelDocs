# React dokumentáció

## React app létrehozása: create-react-app és yarn 2+

A létrehozandó applikáció: *selester-webpage-frontend*

~~1. `yarn create react-app myApp` (yes, is yarn 1.22 in my case)~~

1. `npx create-react-app selester-webpage-frontend`

2. `cd selester-webpage-frontend`

3. rename the package.json for a while

4. `yarn init -2` #this will create another package.json

5. Töröld az alkönyvtárban létrehozott `.git` alkönyvtárat

6. merge the old package.json with the newly created one (A gyakorlatban annyit kell átmásolni, hogy `"packageManager": "yarn@3.3.1"`)

7. `yarn install`

Eredeti forrás: <https://stackoverflow.com/a/70342944>

## React kiszolgálása nem a gyökér URL-ről

Lehet olyan szituáció, hogy a React alkalmazás nem a webszerver elérési útvonalának a gyökerét használja, hanem egy relatív útvonalat.
Pl. az alkalmazás nem a

`www.example.com/`

url-ről kerül kiszolgálásra, hanem a

`www.exaple.com/some-path/`

url-ről.

Ehhez a package.json file-ban adjuk meg, hogy:

```json
"homepage": "/some-path"
```

A dokumentáció elvileg teljes elérést javasol, de tudtommal így is megy.

<https://create-react-app.dev/docs/deployment/#building-for-relative-paths>
