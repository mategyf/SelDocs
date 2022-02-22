# Flutter alkalmazás frissítése a Google Play Console-on

## 1. Az alkalmazás frissítése

A parancssori utasításokat az app főkönyvtárában kell futtatni.

### 1.1. Verziószám frissítése

Növeljük az app verziószámát a pubspec.yaml fájlban:

```yaml
version: 1.0.0+1
```

Érdemes a semver elveit követni.

Ezután frissítsük a csomagot a `Pub get` gombbal vagy terminálból:

```bash
flutter pub get
```

### 1.2. Upload Key

Szükség van az applikáció upload key-jére, amit jó eséllyel az első feltöltéskor generáltunk. Két fájl kell, ellenőrizzük, hogy megvannak-e:

`<app_name>/android/app/upload-keystore.jks`
`<app_name>/android/key.properties`

Ezt a két fájlt **TILOS git-be feltölteni!**

### 1.3. Build-elés

```bash
flutter clean
flutter build appbundle
```

Az elkészült programcsomag a `build\app\outputs\bundle\release\app-release.aab` fájl lesz.

## 2. Feltöltés GooglePlay Console-ra

Lépjünk be a Play Console-ra, válasszuk ki az alkalmazást a "Minden alkalmazás" oldalon.

A bal oldali navigációs sávban lépjünk a megfelelő csatornára: a három tesztelési egyikére vagy az éles verzióra.

Jobb oldalt felül található egy "Kiadás szerkesztése" kék gomb, mellyel új kiadást hozhatunk létre. Középen lesz egy "Alkalmazáscsomagok" mező, ide kell behúzni a korábban build-elt app bundle-t.

Legörgetve a "kiadás adatai"-hoz a verziónevet automatikusan kitölti a rendszer, de a kibocsátási megjegyzéseket (changelog) magunknak kell megadni.

Ezek után az oldal alján "mentés", majd "Kiadás ellenőrzése" kék gomb.

Ezután frissül a lap, és megnézhetjük a hibaüzeneteket, figyelmeztetéseket.

Alul jobbra van a "Közzététel megkezdése..." kék gomb.
