# React dokumentáció

## Egyéb

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

https://create-react-app.dev/docs/deployment/#building-for-relative-paths
