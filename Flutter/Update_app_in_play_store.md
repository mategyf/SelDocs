# Flutter alkalmazás frissítése a Google Play Console-on

## 1. Az alkalmazás frissítése

A parancssori utasításokat az app főkönyvtárában kell futtatni.

### 1.1. Verziószám frissítése

Növeljük az app verziószámát a pubspec.yaml fájlban:

```yaml
version: 1.0.0+1
```

> A verziószám két (egymástól független) részből tevődik össze: a `versionName`, jelen esetben 1.0.0, és a `+` jel után a `versionCode`. A `versionName` egy sztring, melyet gyakorlatilag egyénileg választhatunk meg, de érdemes a [Semver](https://semver.org/) elveit követni. A `versionCode` egy integer, melyet minden egymás utáni változatban növelnünk kell, nem lehet kisebb értéket adni neki. Ez utóbbiból tudja majd a rendszer, hogy egy verzió frissebb-e egy másiknál.
>
> A Play áruház felhasználói számára csak a `versionName` lesz látható.

Ezután frissítsük a csomagot a `Pub get` gombbal vagy terminálból:

```bash
flutter pub get
```

### 1.2. Upload Key

Szükség van az applikáció `upload key`-jére, amit, jó eséllyel, az első feltöltéskor generáltunk. Két fájl kell, ellenőrizzük, hogy megvannak-e:

`<app_name>/android/app/upload-keystore.jks`

`<app_name>/android/key.properties`

Ezt a két fájlt _**TILOS git-be feltölteni!**_

### 1.3. Build-elés

```bash
flutter clean
```

```bash
flutter build appbundle
```

Az elkészült programcsomag a `build\app\outputs\bundle\release\app-release.aab` fájl lesz.

## 2. Feltöltés GooglePlay Console-ra

Lépjünk be a Play Console-ra, válasszuk ki az alkalmazást a "Minden alkalmazás" oldalon.

A bal oldali navigációs sávban lépjünk a megfelelő csatornára: a három tesztelési egyikére vagy az éles verzióra.

Jobb oldalt felül található egy "Új kiadás létrehozása" kék gomb, mellyel új kiadást hozhatunk létre. Középen lesz egy "Alkalmazáscsomagok" mező, ide kell behúzni a korábban build-elt app bundle-t.

Várjuk meg, amíg feldolgozza a rendszer a csomagot.

Legörgetve a "kiadás adatai"-hoz a verziónevet automatikusan kitölti a rendszer, de a kibocsátási megjegyzéseket (changelog) magunknak kell megadni.

Ezek után az oldal alján "mentés", majd "Kiadás ellenőrzése" kék gomb.

Ezután frissül a lap, és megnézhetjük a hibaüzeneteket, figyelmeztetéseket.
Megtekinthetünk néhány statisztikát, és beállíthatjuk a `fokozatos közzététel` paramétereit. Ez utóbbi lehetővé teszi, hogy az aktív felhasználók csak egy bizonyos százalékának kerüljön kiadásra az új változat.

Alul jobbra van a "Közzététel megkezdése..." kék gomb.

A közzététel megkezdése után a kiadás "ellenőrzés alatt" státuszba kerül, amíg a Google el nem végzi az ellenőrzést. Amint ez megtörtént, az app elérhető lesz a Play Store-ban (vagy a tesztelési csatornán).

## Korábbi kiadás átminősítése

Amint rámegyünk valamely tesztelési csatornára, a "kiadások" mező alján található egy "kiadás előresorolása" legördülő menü. ennek segítségével egy tesztelési csatornán lévő kliadást átminősíthetünk, előresorolhatunk egy magasabb tesztelési csatornára vagy az éles csatornára. 