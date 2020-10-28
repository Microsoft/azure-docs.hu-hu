---
title: Bevezetés az Android Map Control használatába | Microsoft Azure térképek
description: Ismerkedjen meg a Azure Maps Android SDK-val. Ismerje meg, hogyan hozhat létre projektet a Android Studioban, hogyan telepítheti az SDK-t, és hogyan hozhat létre interaktív térképet.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: bd2c714c2e16cb8a7a9c530e8e458c33218b1c2b
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895494"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Ismerkedés a Azure Maps Android SDK-val

A Azure Maps Android SDK az Androidhoz készült vektoros Térkép-könyvtár. Ez a cikk végigvezeti a Azure Maps Android SDK telepítésének és a Térkép betöltésének folyamatán.

## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

A cikkben ismertetett eljárások elvégzéséhez először [létre kell hoznia egy Azure Maps fiókot](quick-demo-map-app.md#create-an-azure-maps-account) az S1 díjszabási szinten, és le kell [kérnie a fiók elsődleges kulcsát](quick-demo-map-app.md#get-the-primary-key-for-your-account) .

A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Android Studio letöltése

Töltse le Android Studio, és hozzon létre egy üres tevékenységgel rendelkező projektet a Azure Maps Android SDK telepítése előtt. A Google-tól ingyenesen [letöltheti Android Studioeit](https://developer.android.com/studio/) . 

## <a name="create-a-project-in-android-studio"></a>Projekt létrehozása Android Studio

Először hozzon létre egy új projektet egy üres tevékenységgel. Android Studio projekt létrehozásához hajtsa végre a következő lépéseket:

1. **A projekt kiválasztása** területen válassza a **telefon és a tábla** lehetőséget. Az alkalmazás ezen az űrlapon fog futni.
2. A **telefon és a tábla** lapon válassza az **üres tevékenység** elemet, majd kattintson a **tovább** gombra.
3. A **projekt konfigurálása** területen válassza ki `API 21: Android 5.0.0 (Lollipop)` a minimális SDK-t. Ez a Azure Maps Android SDK által támogatott legkorábbi verzió.
4. Fogadja el az alapértelmezett értéket `Activity Name` , és `Layout Name` válassza a **Befejezés** lehetőséget.

A Android Studio telepítésével és új projekt létrehozásával kapcsolatos további segítségért tekintse meg a [Android Studio dokumentációját](https://developer.android.com/studio/intro/) .

![Projekt létrehozása az Android Studióban ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Virtuális eszköz beállítása

Android Studio lehetővé teszi egy virtuális Android-eszköz beállítását a számítógépen. Ez segít az alkalmazás tesztelésében a fejlesztés során. Virtuális eszköz beállításához válassza az Android virtuális eszköz (AVD) kezelője ikont a projekt képernyő jobb felső sarkában, majd válassza a **virtuális eszköz létrehozása** lehetőséget. A AVD-kezelőt **Tools**  >  az eszköztáron az eszközök **Android**  >  **AVD Manager** lehetőség kiválasztásával is elérheti. A **telefonok** kategóriában válassza a **Nexus 5x** elemet, majd kattintson a **tovább** gombra.

A AVD beállításával kapcsolatos további információkért tekintse meg az [Android Studio dokumentációját](https://developer.android.com/studio/run/managing-avds).

![Android-emulátor](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>A Azure Maps Android SDK telepítése

Az alkalmazás létrehozásának következő lépése a Azure Maps Android SDK telepítése. Az SDK telepítéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a legfelső szintű **Build. gradle** fájlt, és adja hozzá a következő kódot a **minden projekt** , **adattárak** blokkolása szakaszhoz:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Frissítse az **alkalmazást/Build. gradle** , és adja hozzá a következő kódot:
    
    1. Győződjön meg arról, hogy a projekt **minSdkVersion** értéke 21 vagy újabb.

    2. Adja hozzá a következő kódot az Android szakaszhoz:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Frissítse a függőségek blokkot, és adjon hozzá egy új implementációs függőségi sort a legújabb Azure Maps Android SDK-hoz:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.4"
        ```
    
    4. Nyissa meg a **fájlt** az eszköztáron, majd kattintson a **szinkronizálás projekt Gradle-fájlokkal** elemre.
3. Térképi töredék hozzáadása a fő tevékenységhez (res \> elrendezési \> tevékenység \_main.xml):
    
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
    
    * importálások hozzáadása az Azure Maps SDK-hoz
    * a Azure Maps hitelesítési adatainak beállítása
    * a Térkép vezérlőelem példányának beolvasása a **onCreate** metódusban

    Ha a (z) és a (z) metódusok használatával globálisan szeretné beállítani a hitelesítési adatokat, akkor `AzureMaps` `setSubscriptionKey` `setAadProperties` nem kell minden nézetben felvennie a hitelesítési adatokat. 

    A Térkép vezérlőelem saját életciklus-metódusokat tartalmaz az Android OpenGL-életciklusának kezeléséhez. Ezeket az életciklus-metódusokat közvetlenül a tartalmazó tevékenységből kell meghívni. Ahhoz, hogy az alkalmazás helyesen hívja meg a Map Control életciklusának módszereit, felül kell bírálnia a következő életciklus-metódusokat a Térkép vezérlőelemet tartalmazó tevékenységben. És meg kell hívnia a megfelelő leképezés-vezérlési módszert. 

    * onCreate (csomag) 
    * onStart () 
    * onResume() 
    * onPause () 
    * onStop () 
    * onDestroy() 
    * onSaveInstanceState (csomag) 
    * onLowMemory() 

    Szerkessze a **MainActivity. Java** fájlt a következőképpen:
    
    ```java
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
        }
    }
    ```

## <a name="import-classes"></a>Osztályok importálása

Az előző lépések elvégzése után valószínűleg figyelmeztetést kap a Android Studioról a kódok némelyikéről. A figyelmeztetések feloldásához importálja a alkalmazásban hivatkozott osztályokat `MainActivity.java` .

Ezeket az osztályokat automatikusan importálhatja az ALT + ENTER billentyűkombináció kiválasztásával (lehetőség + visszatérés Mac gépen).

Az alkalmazás létrehozásához kattintson a Futtatás gombra, ahogy az az alábbi ábrán is látható (vagy nyomja le a Control + R billentyűt egy Mac gépen).

![Kattintson a Run (Futtatás) gombra](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio eltarthat néhány másodpercig az alkalmazás felépítéséhez. A Build befejezése után tesztelheti az alkalmazást az emulált Android-eszközön. Ehhez hasonló térképnek kell megjelennie:

<center>

![Azure Maps Android-alkalmazásokban](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>A Térkép honosítása

A Azure Maps Android SDK három különböző módszert biztosít a Térkép nyelvének és regionális nézetének beállításához. A következő kód bemutatja, hogyan állíthatja be a nyelvet francia ("fr-FR") és a regionális nézetbe az "Auto" értékre. 

Az első lehetőség, hogy átadja a nyelvet, és megtekinti a regionális információkat az `AzureMaps` osztályba a statikus `setLanguage` és a `setView` metódusok globális használatával. Ezzel a beállítással megadhatja az alkalmazásban betöltött összes Azure Maps vezérlő alapértelmezett nyelvi és regionális nézetét.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

A második lehetőség, hogy átadja a nyelvet, és megtekinti az információkat a Térkép vezérlőelem XML-kódjában.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A harmadik lehetőség az, hogy programozott módon állítsa be a Térkép nyelvét és regionális nézetét a Maps `setStyle` metódus használatával. Ezt bármikor megteheti a Térkép nyelvének és regionális nézetének módosításához.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Itt látható egy példa arra, hogy Azure Maps a "fr-FR" és a regionális nézet beállítása "Auto" értékre.

<center>

![Azure Maps, a feliratokat ábrázoló Térkép ábrázolása francia nyelven](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

A támogatott nyelvek és regionális nézetek teljes listáját [itt](supported-languages.md)dokumentáljuk.

## <a name="navigating-the-map"></a>Navigálás a térképen

A Térkép több különböző módon nagyítható, megpárolt, elforgatható és felhelyezhető. A következő részletek a Térkép különböző módjaira mutatnak.

**A Térkép nagyítása**

- A térképhez koppintson a két ujjal és a csípéssel együtt a nagyításhoz vagy az ujjak egymáshoz közelítéséhez.
- Egy szint nagyításához koppintson duplán a térképre.
- Dupla koppintással két ujjal nagyíthatja ki a térképet egy szinten.
- Koppintás kétszer; a második koppintásnál tartsa az ujját a térképen, és húzza a nagyításhoz vagy a kicsinyítéshez a nagyításhoz.

**A Térkép pásztázása**

- Érintse meg a térképet, és húzza a kívánt irányba.

**A Térkép elforgatása**

- Érintse meg a térképet két ujjal, és forgassa el.

**A Térkép feldobása**

- Érintse meg a térképet két ujjal, és húzza őket egymáshoz.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan adhat hozzá átfedéses információkat a térképen:

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása Android-térképhez](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Alakzatok hozzáadása Android-térképhez](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Térkép stílusainak módosítása Android-térképeken](./set-android-map-styles.md)