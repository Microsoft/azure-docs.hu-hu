---
title: 'Gyors útmutató: Android-alkalmazás létrehozása Azure Mapssal | Microsoft Azure '
description: 'Gyors útmutató: megtudhatja, hogyan hozhat létre Android-alkalmazást a Azure Maps Android SDK használatával.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: mvc
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 3c0f95c1252b6895b4604d14e5565395beab8952
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039648"
---
# <a name="quickstart-create-an-android-app-with-azure-maps"></a>Gyors útmutató: Android-alkalmazás létrehozása Azure Maps

Ez a cikk bemutatja, hogyan adhatja hozzá a Azure Mapst egy Android-alkalmazáshoz. Végigvezeti a következő alapvető lépéseken:

* Állítsa be a fejlesztési környezetet.
* Hozzon létre egy saját Azure Maps fiókot.
* Szerezze be az alkalmazásban használni kívánt elsődleges Azure Maps kulcsot.
* Hivatkozzon a projekt Azure Maps könyvtáraira.
* Azure Maps vezérlőelem hozzáadása az alkalmazáshoz.

## <a name="prerequisites"></a>Előfeltételek

1. Hozzon létre egy Azure Maps fiókot a [Azure Portalba](https://portal.azure.com)való bejelentkezéssel. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
2. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).
4. [Töltse le a Android Studio](https://developer.android.com/studio/) ingyen a Google-ból.

## <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

Hozzon létre egy új Azure Maps fiókot a következő lépésekkel:

1. Kattintson az [Azure Portal](https://portal.azure.com) bal felső sarkában az **Erőforrás létrehozása** gombra.
2. A *Keresés a piactéren* mezőbe írja be a következőt: **Azure Maps**.
3. Az *eredmények* közül válassza a **Azure Maps** lehetőséget. Kattintson a térkép alatt megjelenő **Létrehozás** gombra.
4. A **Maps-fiók létrehozása** lapon adja meg a következő értékeket:
    * A fiókhoz használni kívánt *előfizetés*.
    * A fiókhoz tartozó *erőforráscsoport* neve. Választhat, hogy *létrehoz egy új erőforráscsoportot*, vagy egy *meglévő erőforráscsoportot használ*.
    * Az új fiók *neve*.
    * A fiók *díjszabási szintje* .
    * Olvassa el a *licencfeltételeket* és az *adatvédelmi nyilatkozatot*, és jelölje be az azok elfogadását jelző jelölőnégyzetet.
    * Kattintson a **Létrehozás** gombra.

    ![Maps-fiók létrehozása a portálon](media/quick-android-map/create-account.png)

## <a name="get-the-primary-key-for-your-account"></a>A fiók elsődleges kulcsának lekérése

Miután sikeresen létrejött a Maps-fiókja, kérje le az elsődleges kulcsot, amely lehetővé teszi a Maps API-k lekérdezését.

1. Nyissa meg a Maps-fiókot a portálon.
2. A beállítások szakaszban válassza a **hitelesítés** lehetőséget.
3. Másolja ki az **elsődleges kulcsot** a vágólapra. Mentse a helyi gépre, hogy később felhasználhassa ebben az oktatóanyagban.

>[!NOTE]
> Ha az Azure Maps elsődleges kulcs helyett az Azure-előfizetési kulcsot használja, a Térkép nem jelenik meg megfelelően. Biztonsági okokból javasolt az elsődleges és másodlagos kulcsok közötti váltás. A kulcsok elforgatásához frissítse az alkalmazást a másodlagos kulcs használatára, telepítse, majd nyomja le az elsődleges kulcs melletti ciklus/frissítés gombot egy új elsődleges kulcs létrehozásához. A régi elsődleges kulcs le lesz tiltva. A kulcsok elforgatásával kapcsolatos további információkért lásd: [Azure Key Vault beállítása kulcsfontosságú rotációs és naplózási](../key-vault/secrets/tutorial-rotation-dual.md) szolgáltatással

![Elsődleges kulcs Azure Maps kulcs beolvasása Azure Portal](media/quick-android-map/get-key.png)

## <a name="create-a-project-in-android-studio"></a>Projekt létrehozása Android Studio

Először hozzon létre egy új projektet egy üres tevékenységgel. Android Studio projekt létrehozásához hajtsa végre a következő lépéseket:

1. **A projekt kiválasztása** területen válassza a **telefon és a tábla** lehetőséget. Az alkalmazás ezen az űrlapon fog futni.
2. A **telefon és a tábla** lapon válassza az **üres tevékenység** elemet, majd kattintson a **tovább** gombra.
3. A **projekt konfigurálása** területen válassza ki `API 21: Android 5.0.0 (Lollipop)` a minimális SDK-t. Ez a Azure Maps Android SDK által támogatott legkorábbi verzió.
4. Fogadja el az alapértelmezett értéket `Activity Name` , és `Layout Name` válassza a **Befejezés** lehetőséget.

A Android Studio telepítésével és új projekt létrehozásával kapcsolatos további segítségért tekintse meg a [Android Studio dokumentációját](https://developer.android.com/studio/intro/) .

![Projekt létrehozása az Android Studióban](media/quick-android-map/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Virtuális eszköz beállítása

Android Studio lehetővé teszi egy virtuális Android-eszköz beállítását a számítógépen. Ez segít az alkalmazás tesztelésében a fejlesztés során. Virtuális eszköz beállításához válassza az Android virtuális eszköz (AVD) kezelője ikont a projekt képernyő jobb felső sarkában, majd válassza a **virtuális eszköz létrehozása** lehetőséget. A AVD-kezelőt   >  az eszköztáron az eszközök **Android**  >  **AVD Manager** lehetőség kiválasztásával is elérheti. A **telefonok** kategóriában válassza a **Nexus 5x** elemet, majd kattintson a **tovább** gombra.

A AVD beállításával kapcsolatos további információkért tekintse meg az [Android Studio dokumentációját](https://developer.android.com/studio/run/managing-avds).

![Android-emulátor](media/quick-android-map/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>A Azure Maps Android SDK telepítése

Az alkalmazás létrehozásának következő lépése a Azure Maps Android SDK telepítése. Az SDK telepítéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a legfelső szintű **Build. gradle** fájlt, és adja hozzá a következő kódot a **minden projekt**, **adattárak** blokkolása szakaszhoz:

    ```gradle
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Frissítse az **alkalmazást/Build. gradle** , és adja hozzá a következő kódot:

    1. Győződjön meg arról, hogy a projekt **minSdkVersion** értéke 21 vagy újabb.

    2. Adja hozzá a következő kódot az Android szakaszhoz:

        ```gradle
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Frissítse a függőségek blokkot, és adjon hozzá egy új implementációs függőségi sort a legújabb Azure Maps Android SDK-hoz:

        ```gradle
        implementation "com.microsoft.azure.maps:mapcontrol:0.7"
        ```

        > [!Note]
        > A verziószámot a "0 +" értékre állítva beállíthatja, hogy a kód mindig a legújabb verzióra mutasson.

    4. Nyissa meg a **fájlt** az eszköztáron, majd kattintson a **szinkronizálás projekt Gradle-fájlokkal** elemre.
3. Térképi töredék hozzáadása a fő tevékenységhez (res \> elrendezési \> tevékenység \_main.xml):

    ```xml
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        />
    ```

::: zone pivot="programming-language-java-android"

4. A **MainActivity. Java** fájlban a következőket kell tennie:

    * importálások hozzáadása az Azure Maps SDK-hoz
    * a Azure Maps hitelesítési adatainak beállítása
    * a Térkép vezérlőelem példányának beolvasása a **onCreate** metódusban

    Ha a (z) és a (z) metódusok használatával globálisan szeretné beállítani a hitelesítési adatokat, akkor `AzureMaps` `setSubscriptionKey` `setAadProperties` nem kell minden nézetben felvennie a hitelesítési adatokat.

    A Térkép vezérlőelem saját életciklus-metódusokat tartalmaz az Android OpenGL-életciklusának kezeléséhez. Ezeket az életciklus-metódusokat közvetlenül a tartalmazó tevékenységből kell meghívni. Ahhoz, hogy az alkalmazás helyesen hívja meg a Map Control életciklusának módszereit, felül kell bírálnia a következő életciklus-metódusokat a Térkép vezérlőelemet tartalmazó tevékenységben. És meg kell hívnia a megfelelő leképezés-vezérlési módszert.

    * `onCreate(Bundle)`
    * `onDestroy()`
    * `onLowMemory()`
    * `onPause()`
    * `onResume()`
    * `onSaveInstanceState(Bundle)`
    * `onStart()`
    * `onStop()`

    Szerkessze a **MainActivity. Java** fájlt a következőképpen:

    ```java
    package com.example.myapplication;
    
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

    > [!NOTE]
    > Az előző lépések elvégzése után Android Studio figyelmeztetéseket kaphat a kóddal kapcsolatban. A figyelmeztetések feloldásához importálja a alkalmazásban hivatkozott osztályokat `MainActivity.java` .
    > Ezeket az osztályokat automatikusan importálhatja a `Alt`  +  `Enter` ( `Option`  +  `Return` Mac gépen) lehetőség kiválasztásával.

::: zone-end

::: zone pivot="programming-language-kotlin"

4. A **MainActivity. kt** fájlban a következőket kell tennie:

    * importálások hozzáadása az Azure Maps SDK-hoz
    * a Azure Maps hitelesítési adatainak beállítása
    * a Térkép vezérlőelem példányának beolvasása a **onCreate** metódusban

    Ha a (z) és a (z) metódusok használatával globálisan szeretné beállítani a hitelesítési adatokat, akkor `AzureMaps` `setSubscriptionKey` `setAadProperties` nem kell minden nézetben felvennie a hitelesítési adatokat.

    A Térkép vezérlőelem saját életciklus-metódusokat tartalmaz az Android OpenGL-életciklusának kezeléséhez. Ezeket az életciklus-metódusokat közvetlenül a tartalmazó tevékenységből kell meghívni. Ahhoz, hogy az alkalmazás helyesen hívja meg a Map Control életciklusának módszereit, felül kell bírálnia a következő életciklus-metódusokat a Térkép vezérlőelemet tartalmazó tevékenységben. És meg kell hívnia a megfelelő leképezés-vezérlési módszert.

    * `onCreate(Bundle)`
    * `onDestroy()`
    * `onLowMemory()`
    * `onPause()`
    * `onResume()`
    * `onSaveInstanceState(Bundle)`
    * `onStart()`
    * `onStop()`

    Szerkessze a **MainActivity. kt** fájlt a következőképpen:

    ```kotlin
    package com.example.myapplication;

    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import com.microsoft.azure.maps.mapcontrol.AzureMap
    import com.microsoft.azure.maps.mapcontrol.AzureMaps
    import com.microsoft.azure.maps.mapcontrol.MapControl
    import com.microsoft.azure.maps.mapcontrol.events.OnReady
    
    class MainActivity : AppCompatActivity() {
    
        companion object {
            init {
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
    
                //Alternatively use Azure Active Directory authenticate.
                //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
            }
        }
    
        var mapControl: MapControl? = null
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
            mapControl = findViewById(R.id.mapcontrol)
    
            mapControl?.onCreate(savedInstanceState)
    
            //Wait until the map resources are ready.
            mapControl?.onReady(OnReady { map: AzureMap -> })
        }
    
        public override fun onStart() {
            super.onStart()
            mapControl?.onStart()
        }
    
        public override fun onResume() {
            super.onResume()
            mapControl?.onResume()
        }
    
        public override fun onPause() {
            mapControl?.onPause()
            super.onPause()
        }
    
        public override fun onStop() {
            mapControl?.onStop()
            super.onStop()
        }
    
        override fun onLowMemory() {
            mapControl?.onLowMemory()
            super.onLowMemory()
        }
    
        override fun onDestroy() {
            mapControl?.onDestroy()
            super.onDestroy()
        }
    
        override fun onSaveInstanceState(outState: Bundle) {
            super.onSaveInstanceState(outState)
            mapControl?.onSaveInstanceState(outState)
        }
    }
    ```

    > [!NOTE]
    > Az előző lépések elvégzése után Android Studio figyelmeztetéseket kaphat a kóddal kapcsolatban. A figyelmeztetések feloldásához importálja a alkalmazásban hivatkozott osztályokat `MainActivity.kt` .
    > Ezeket az osztályokat automatikusan importálhatja a `Alt`  +  `Enter` ( `Option`  +  `Return` Mac gépen) lehetőség kiválasztásával.

::: zone-end

5. Az `Control` alkalmazás létrehozásához kattintson a Futtatás gombra, ahogy az az alábbi ábrán is látható (vagy nyomja meg a Mac billentyűt  +  `R` ).

    ![Kattintson a Run (Futtatás) gombra](media/quick-android-map/run-app.png)

Android Studio eltarthat néhány másodpercig az alkalmazás felépítéséhez. A Build befejezése után tesztelheti az alkalmazást az emulált Android-eszközön. Ehhez hasonló térképnek kell megjelennie:

![Azure Maps Android-alkalmazásokban](media/quick-android-map/quickstart-android-map.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

>[!WARNING]
> A [következő lépések](#next-steps) szakaszban felsorolt oktatóanyagok részletesen ismertetik, hogyan használhatja és konfigurálhatja a Azure Maps a fiókjával. Ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat, ha azt tervezi, hogy folytatja az oktatóanyagokat.

Ha nem tervezi tovább az oktatóanyagokat, hajtsa végre az alábbi lépéseket az erőforrások törléséhez:

1. Android Studio bezárásához és a létrehozott alkalmazás törléséhez.
2. Ha egy külső eszközön tesztelte az alkalmazást, távolítsa el az alkalmazást az eszközről.

Ha nem tervezi a folyamatos fejlesztést a Azure Maps Android SDK-val:

1. Navigáljon a Azure Portal lapra. A fő portál lapon válassza az **összes erőforrás** lehetőséget. Vagy kattintson a bal felső sarokban található menü ikonra. Válassza a **Minden erőforrás** elemet.
2. Kattintson a Azure Maps-fiókjára. A lap tetején kattintson a **Törlés** elemre.
3. Ha nem tervezi az Android-alkalmazások fejlesztésének folytatását, távolítsa el Android Studio.

További példákat a következő útmutatók tartalmaznak:

* [Hitelesítés kezelése Azure Maps](how-to-manage-authentication.md)
* [Térkép stílusainak módosítása Android-térképeken](set-android-map-styles.md)
* [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)
* [Vonalréteg hozzáadása](android-map-add-line-layer.md)
* [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta Azure Maps-fiókját, és létrehozott egy bemutató alkalmazást. Tekintse meg az alábbi oktatóanyagokat, amelyekben többet tudhat meg Azure Maps:

> [!div class="nextstepaction"]
> [GeoJSON-Azure Maps betöltése](tutorial-load-geojson-file-android.md)