---
title: Oktatóanyag – Android-alkalmazás migrálása | Microsoft Azure térképek
description: Útmutató Android-alkalmazások Google Mapsből Microsoft Azure Maps rendszerbe való áttelepítéséhez
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 7f3d32a782c653b3be8b3d6f6714bc9065a73518
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042895"
---
# <a name="tutorial-migrate-an-android-app-from-google-maps"></a>Oktatóanyag: Android-alkalmazás migrálása a Google Mapsből

A Azure Maps Android SDK egy olyan API-felülettel rendelkezik, amely hasonló a web SDK-hoz. Ha ezekkel az SDK-k valamelyikével fejlesztett ki, akkor az egyes fogalmak, ajánlott eljárások és architektúrák közül sokat alkalmazunk. Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Térkép betöltése
> * Térkép honosítása
> * Jelölők, vonalláncok és sokszögek hozzáadása.
> * Csempe rétegének átfedése
> * Forgalomadatok megjelenítése

A Azure Maps Android SDK az API 21 minimális Android-verzióját támogatja: Android 5.0.0 (nyalóka).

Az összes példa a javában van megadva. a Kotlin azonban a Azure Maps Android SDK-val is használhatja.

Az Android SDK Azure Maps használatával történő fejlesztésével kapcsolatos további információkért tekintse meg a [Azure Maps Android SDK útmutatói](how-to-use-android-map-control-library.md)útmutatóit.

## <a name="prerequisites"></a>Előfeltételek

1. Hozzon létre egy Azure Maps fiókot a [Azure Portalba](https://portal.azure.com)való bejelentkezéssel. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
2. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).

## <a name="load-a-map"></a>Térkép betöltése

A Google-t vagy Azure Mapst használó Android-alkalmazásokban hasonló lépésekből álló Térkép betöltése. Az SDK használatakor a következőkre van szükség:

* Egy API-vagy előfizetési kulcs beszerzése a platformhoz való hozzáféréshez.
* Adjon hozzá néhány XML-t egy tevékenységhez, hogy meghatározza, hol kell megjeleníteni a térképet, és hogy hogyan kell meghatározni.
* Bírálja felül a Térkép nézetet tartalmazó tevékenység összes életciklus-módszerét a Map osztály megfelelő módszereire. Különösen a következő módszereket kell felülbírálnia:
  * `onCreate(Bundle)`
  * `onStart()`
  * `onResume()`
  * `onPause()`
  * `onStop()`
  * `onDestroy()`
  * `onSaveInstanceState(Bundle)`
  * `onLowMemory()`
* Várjon, amíg a Térkép készen áll a hozzáférésre és a program futtatására.

### <a name="before-google-maps"></a>Előtte: Google Maps

Ha az Androidhoz készült Google Maps SDK-val szeretne térképet megjeleníteni, a következő lépéseket kell végrehajtania:

1. Győződjön meg arról, hogy a Google Play-szolgáltatások telepítve vannak.
2. Vegyen fel egy függőséget a Google Maps szolgáltatáshoz a modul  **gradle. Build** fájljához:

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. Vegyen fel egy Google Maps API-kulcsot a  **Google \_ Maps \_api.xml** fájl alkalmazás szakaszában.

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. Térképi töredék hozzáadása a fő tevékenységhez:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

5. A **MainActivity. Java** fájlban importálnia kell a Google Maps SDK-t. Továbbítsa az összes életciklus-metódust a Térkép nézetet tartalmazó tevékenységből a Map osztály megfelelő értékeire. Példány beolvasása `MapView` a leképezési töredékből a `getMapAsync(OnMapReadyCallback)` metódus használatával. A `MapView` automatikusan inicializálja a Maps rendszer és a nézetet. Szerkessze a **MainActivity. Java** fájlt a következőképpen:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Egy alkalmazás futtatásakor a Térkép vezérlőelem a következő képen látható módon töltődik be.

![Egyszerű Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>Utána: Azure Maps

Ha az Androidhoz készült Azure Maps SDK-val szeretne térképet megjeleníteni, a következő lépéseket kell elvégeznie:

1. Nyissa meg a legfelső szintű **Build. gradle** fájlt, és adja hozzá a következő kódot a **minden projekt** blokk szakaszhoz:

    ```java
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Frissítse az **alkalmazást/Build. gradle** , és adja hozzá a következő kódot:

    1. Győződjön meg arról, hogy a projekt **minSdkVersion** értéke 21 vagy újabb.

    2. Adja hozzá a következő kódot az Android szakaszhoz:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Módosítsa a függőségek blokkját. Új implementációs függőségi vonal hozzáadása a legújabb Azure Maps Android SDK-hoz:

        ```Java
        implementation "com.microsoft.azure.maps:mapcontrol:0.7"
        ```

        > [!Note]
        > A verziószámot a "0 +" értékre állítva beállíthatja, hogy a kód mindig a legújabb verzióra mutasson.

    4. Nyissa meg a **fájlt** az eszköztáron, majd kattintson a **szinkronizálás projekt Gradle-fájlokkal** elemre.

3. Térképi töredék hozzáadása a fő tevékenységhez (erőforrások pwd \> elrendezési \> tevékenység \_main.xml):

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. A **MainActivity. Java** fájlban a következőket kell tennie:

    * A Azure Maps SDK importálása
    * A Azure Maps hitelesítési adatainak beállítása
    * A Térkép vezérlőelem példányának beolvasása a **onCreate** metódusban

     Állítsa be a hitelesítési adatokat az `AzureMaps` osztályban a `setSubscriptionKey` vagy `setAadProperties` metódusok használatával. Ezt a globális frissítést, minden nézethez hozzá kell adnia a hitelesítési adatokat.

    A Térkép vezérlőelem saját életciklus-metódusokat tartalmaz az Android OpenGL-életciklusának kezeléséhez. Ezeket a metódusokat közvetlenül a befoglalt tevékenységből kell meghívni. A Térkép vezérlőelem életciklus-módszereinek helyes meghívásához felül kell bírálnia a következő életciklus-metódusokat a Térkép vezérlőelemet tartalmazó tevékenységben. Hívja meg a megfelelő leképezés-vezérlési módszert.

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    Szerkessze a **MainActivity. Java** fájlt a következőképpen:

    ```Java
    package com.example.myapplication;
    
    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    
    public class MainActivity extends AppCompatActivity {
    
    static {
        AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    }

    MapControl mapControl;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        //Wait until the map resources are ready.
        mapControl.onReady(map -> {
            //Add your post map load code here.

        });
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    protected void onStart(){
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }}
    ```

Ha futtatja az alkalmazást, a Térkép vezérlőelem a következő képen látható módon lesz betöltve.

![Egyszerű Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)

Figyelje meg, hogy a Azure Maps-vezérlő támogatja a nagyítást, és több globális nézetet biztosít.

> [!TIP]
> Ha Android-emulátort használ egy Windows rendszerű gépen, előfordulhat, hogy a Térkép nem jelenik meg az OpenGL-vel és a szoftveresen felgyorsított grafikus megjelenítéssel való ütközések miatt. A következőkben néhány személy számára a probléma megoldásához is dolgozott. Nyissa meg a AVD-kezelőt, és válassza ki a szerkeszteni kívánt virtuális eszközt. Görgessen le a **Konfiguráció ellenőrzése** panelen. Az **emulált teljesítmény** szakaszban állítsa a **grafikus** beállítást a **hardver** elemre.

## <a name="localizing-the-map"></a>A Térkép honosítása

A honosítás akkor fontos, ha a célközönség több országban vagy régióban is elterjedt, vagy különböző nyelveket beszél.

### <a name="before-google-maps"></a>Előtte: Google Maps

Adja hozzá a következő kódot a `onCreate` metódushoz a Térkép nyelvének beállításához. A Térkép környezeti nézetének beállítása előtt hozzá kell adni a kódot. A "FR" nyelvi kód korlátozza a nyelvet franciára.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Itt látható egy példa arra, hogy a Google Maps a "FR" nyelvre van állítva.

![Google Maps – honosítás](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps"></a>Utána: Azure Maps

A Azure Maps három különböző módszert biztosít a Térkép nyelvének és regionális nézetének beállításához. Az első lehetőség, hogy átadja a nyelv és a regionális nézet adatait a `AzureMaps` osztálynak. Ez a beállítás `setLanguage` globálisan a statikus és a `setView` metódusokat használja. Ez azt jelenti, hogy az alapértelmezett nyelv és regionális nézet az alkalmazásban betöltött összes Azure Maps-vezérlőn be van állítva. Ez a példa franciául állítja be a "fr-FR" nyelvi kódot.

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

A második lehetőség, hogy átadja a nyelvet, és megtekinti az adatokat a Térkép vezérlőelem XML-kódjával.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

A harmadik lehetőség az, hogy a Maps metódussal program a nyelv és a regionális Térkép nézetet használja `setStyle` . Ez a beállítás a kód végrehajtásának bármikor frissíti a nyelvet és a regionális nézetet.

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

Itt látható egy példa arra, hogy Azure Maps a "fr-FR" nyelvre beállított nyelvet.

![Honosítás Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)

Tekintse át a [támogatott nyelvek](supported-languages.md)teljes listáját.

## <a name="setting-the-map-view"></a>A Térkép nézet beállítása

A dinamikus térképek a Azure Maps és a Google Maps szolgáltatásban programozott módon áthelyezhetők az új földrajzi helyekre a megfelelő módszerek meghívásával. Tegyük fel, hogy a Térkép műhold antennát jelenít meg, középre kell állítani a térképet egy koordinátákkal rendelkező helyen, és módosítani kell a nagyítási szintet. Ebben a példában a következő szélességet fogjuk használni: 35,0272, hosszúság:-111,0225, és a nagyítási szint 15.

### <a name="before-google-maps"></a>Előtte: Google Maps

A Google Maps Map vezérlő kamerája programozott módon áthelyezhető a metódus használatával `moveCamera` . A `moveCamera` metódus lehetővé teszi a Térkép középpontjának és a nagyítási szint megadását. A `setMapType` metódus megváltoztatja a Térkép típusát.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

![Google Maps-set nézet](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> A Google Maps a méretekben 256 képpont méretű csempéket használ, míg Azure Maps nagyobb 512 képpont csempét használ. Ez csökkenti a Azure Maps által igényelt hálózati kérések számát, ha a Google Maps szolgáltatással ugyanazt a térképi területet szeretné betölteni. Ahhoz, hogy ugyanaz a megtekinthető terület elérhető legyen térképként a Google Maps-ben, ki kell vonnia a Google Maps-ben használt nagyítási szintet Azure Maps használatakor.

### <a name="after-azure-maps"></a>Utána: Azure Maps

Amint azt korábban említettük, a Azure Maps ugyanazon megtekinthető terület kivonásával a Google Maps által használt nagyítási szintet. Ebben az esetben használjon 14 nagyítási szintet.

A Térkép kezdeti nézete beállítható a Térkép vezérlőelem XML-attribútumaiban.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

A Térkép nézetet a Maps és a metódusok használatával lehet programozni `setCamera` `setStyle` .

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

![Azure Maps nézet beállítása](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**További források:**

* [Támogatott térképstílusok](supported-map-styles.md)

## <a name="adding-a-marker"></a>Jelölő hozzáadása

A pontok adatai gyakran a térképen látható képpel jelennek meg. Ezeket a képeket jelölőknek, pushpins, PIN-kódoknak vagy szimbólumoknak nevezzük. Az alábbi példák a térképre mutató, a szélesség: 51,5, a földrajzi hosszúság:-0,2.

### <a name="before-google-maps"></a>Előtte: Google Maps

A Google Maps használatával a jelölők a Maps metódussal vehetők fel `addMarker` .

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

![Google Maps-jelölő](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>Utána: Azure Maps

Azure Maps az adatforráshoz az adatforráshoz való hozzáadásával adja meg az adatpontot a térképen. Ezt követően az adatforrás csatlakoztatása egy szimbólum réteghez Az adatforrás optimalizálja a Térkép vezérlőelem térbeli adatainak kezelését. A szimbólum réteg azt adja meg, hogyan lehet megjeleníteni a pontok információit képként vagy szövegként.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

![Azure Maps jelölő](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>Egyéni jelölő hozzáadása

Az egyéni rendszerképeket egy térképen ábrázoló pontok ábrázolására lehet használni. Az alábbi példákban található Térkép egyéni rendszerképet használ egy pont megjelenítésére a térképen. A pont a következő szélességben: 51,5 és hosszúság:-0,2. A horgony kitolja a jelölő pozícióját, hogy a gombostű ikon a térképen a megfelelő pozícióval legyen igazítva.

![sárga gombostű-rendszerkép](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png

Mindkét példában megjelenik a fenti rendszerkép az alkalmazások erőforrásainak megrajzolható mappájába.

### <a name="before-google-maps"></a>Előtte: Google Maps

A Google Maps használatával a jelölők egyéni rendszerképeket is használhatnak. Egyéni rendszerképek betöltése a jelölő `icon` kapcsolójának használatával. Ha a kép pontját a koordinátahöz szeretné igazítani, használja a `anchor` kapcsolót. A horgony a rendszerkép méreteihez képest. Ebben az esetben a horgony 0,2 egység széles, és 1 egység magas.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

![Google Maps – egyéni jelölő](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>Utána: Azure Maps

Azure Maps támogatja az egyéni rendszerképeket, de először a lemezképet be kell tölteni a Térkép erőforrásaiba, és hozzá kell rendelni egy egyedi azonosítót. Ezt követően a szimbólum rétegnek hivatkoznia kell erre az AZONOSÍTÓra. A kapcsolóval állítsa át a szimbólumot úgy, hogy az a képen a megfelelő pontra legyen igazítva `iconOffset` . Az ikon eltolása képpontban megadva. Alapértelmezés szerint az eltolás a rendszerkép alsó középpontjához képest relatív értékre van állítva, de az eltolás értéke a `iconAnchor` kapcsolóval módosítható. Ez a példa a `iconAnchor` beállítást állítja be `"center"` . Egy ikon eltolásával helyezi át a képet öt képpontos jobbra és 15 képpont-ig, hogy az illeszkedjen a gombostű-rendszerkép pontjához.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

![Azure Maps egyéni jelölő](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>Vonallánc hozzáadása

A vonalláncok egy vonal vagy elérési út ábrázolására szolgálnak a térképen. Az alábbi példák bemutatják, hogyan hozhat létre szaggatott vonalláncot a térképen.

### <a name="before-google-maps"></a>Előtte: Google Maps

A Google Maps segítségével az osztály használatával jelenítheti meg a vonalláncot `PolylineOptions` . Adja hozzá a vonalláncot a térképhez a `addPolyline` metódus használatával. Állítsa be az ecsetvonás színét a `color` kapcsoló használatával. Állítsa be a körvonal vastagságát a `width` kapcsoló használatával. Vegyen fel egy ecsetvonás-kötőjel tömböt a `pattern` kapcsoló használatával.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

![Google Maps-vonallánc](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>Utána: Azure Maps

Azure Maps a vonalláncok neve `LineString` vagy `MultiLineString` objektumai. Adja hozzá ezeket az objektumokat egy adatforráshoz, és jelenítse meg őket egy sor réteg használatával. Állítsa be a körvonal vastagságát a `strokeWidth` kapcsoló használatával. Vegyen fel egy ecsetvonás-kötőjel tömböt a `strokeDashArray` kapcsoló használatával.

A Azure Maps web SDK-ban a körvonal szélessége és a kötőjel tömb "képpont" egysége megegyezik a Google Maps szolgáltatásban megadottal. Mindkettő ugyanazokat az értékeket fogadja el, hogy ugyanazokat az eredményeket hozza létre.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

![Azure Maps vonallánc](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>Sokszög hozzáadása

A sokszögek egy területnek a térképen való ábrázolására szolgálnak. A következő példák bemutatják, hogyan hozhat létre sokszöget. Ez a sokszög egy háromszöget képez a Térkép középpontjának koordinátái alapján.

### <a name="before-google-maps"></a>Előtte: Google Maps

A Google Maps használatával sokszöget jelenít meg a `PolygonOptions` osztály segítségével. Adja hozzá a sokszöget a térképhez a `addPolygon` metódus használatával. Adja meg a kitöltés és a körvonal színét a `fillColor` és a `strokeColor` kapcsolók használatával. Állítsa be a körvonal vastagságát a `strokeWidth` kapcsoló használatával.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

![Google Maps – sokszög](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>Utána: Azure Maps

A Azure Maps-ben adjon hozzá `Polygon` és `MultiPolygon` objektumokat egy adatforráshoz, és jelenítse meg őket a térképen rétegek használatával. Sokszög területének megjelenítése egy sokszög rétegben. Sokszög körvonalának megjelenítése egy vonal réteg használatával. Az ecsetvonás színének és szélességének beállítása a `strokeColor` és `strokeWidth` kapcsolók használatával.

A Azure Maps web SDK körvonal szélessége és kötőjel Array "pixel" egysége a Google Maps-ben a megfelelő egységekkel illeszkedik. Mindkettő elfogad ugyanazokat az értékeket, és ugyanazokat az eredményeket eredményezi.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

![Azure Maps sokszög](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>Csempe rétegének átfedése

 A csempék rétegeit olyan rétegbeli képekre használhatja, amelyeket kisebb csempés képekre bontottak, amelyek illeszkednek a Maps csempe rendszerhez. Ez a módszer a rétegek és a nagyméretű adathalmazok átfedésének gyakori módja. A csempék rétegeit képátfedésként nevezzük a Google Maps-ben.

Az alábbi példák egy időjárási radar csempe réteget fedik fel az Iowa State University Iowa környezeti Mesonet. A csempék mérete 256 képpont.

### <a name="before-google-maps"></a>Előtte: Google Maps

A Google Maps segítségével egy csempe réteg helyezhető a Térkép tetejére. Használja a `TileOverlayOptions` osztályt. Adja hozzá a csempe réteget a térképhez a `addTileLauer` metódus használatával. A csempék félig áttetszővé tételéhez a `transparency` beállítás értéke 0,2, vagy 20% transzparens.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

![Google Maps csempe réteg](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps"></a>Utána: Azure Maps

A térképhez hasonló módon adhat hozzá egy csempe réteget, mint bármely más réteget. Olyan formázott URL-cím, amely x, y és nagyítási helyőrzőket tartalmaz; `{x}`, `{y}` `{z}` vagy annak a rétegnek a használatával kell eldönteni, hogy hová fér hozzá a csempék. Emellett a rétegek Azure Maps támogatás `{quadkey}` , `{bbox-epsg-3857}` és `{subdomain}` helyőrzők között. A csempék félig áttetszővé tételéhez a rendszer 0,8-es opacitási értéket használ. A fedettség és az átlátszóság, bár hasonló, fordított értékeket használjon. A két lehetőség közötti átalakításhoz vonja ki az értékét az első számból.

> [!TIP]
> A Azure Maps-ben a rétegek a többi réteg alá kerülnek, beleértve az alapszintű leképezési rétegeket is. Emellett gyakran érdemes a Térkép feliratai alatt megjeleníteni a csempéket, hogy könnyen olvashatóak legyenek. A `map.layers.add` metódus egy második paramétert használ, amely annak a rétegnek az azonosítója, amelyben az alábbi új réteget be kell szúrni. A térképi feliratok alá tartozó csempe réteg beszúrásához a következő kódot használhatja: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

![Azure Maps csempe réteg](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>Forgalom megjelenítése

A Azure Maps és a Google Maps egyaránt rendelkezik a forgalmi adatok átfedésére szolgáló lehetőségekkel.

### <a name="before-google-maps"></a>Előtte: Google Maps

A Google Maps szolgáltatásban a forgalmi flow-adatok a térképen helyezhetők el úgy, hogy igaz értéket adunk a Térkép `setTrafficEnabled` metódusának.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

![Google Maps-forgalom](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>Utána: Azure Maps

Azure Maps számos különböző lehetőséget biztosít a forgalom megjelenítéséhez. A forgalmi incidensek, például a közúti bezárások és a balesetek ikonként is megjeleníthetők a térképen. A térképen a forgalmi folyamat és a színkódolt utak is elhelyezhetők. A színek úgy módosíthatók, hogy megjelenjenek a közzétett sebességi korláthoz képest, a normál várható késéshez képest, vagy az abszolút késleltetéssel. Az incidensek Azure Maps percenként frissülnek, és két percenként frissülnek a flow-adatforgalom.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

![Azure Maps forgalom](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincs kitakarítható erőforrás.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps áttelepítéséről:

> [!div class="nextstepaction"]
> [Androidos alkalmazás migrálása](migrate-from-google-maps-android-app.md)
