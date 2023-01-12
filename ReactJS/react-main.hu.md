# React dokumentáció

## React app létrehozása: create-react-app és yarn 2+

1. `yarn create react-app myApp` (yes, is yarn 1.22 in my case)

2. rename the package.json for a while

3. `yarn init -2` #this will create another package.json

4. merge the old package.json with the newly created one

5. `yarn install`

Forrás: <https://stackoverflow.com/a/70342944>

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
