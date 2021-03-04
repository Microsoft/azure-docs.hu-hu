---
title: Bevezetés az Android Map Control használatába | Microsoft Azure térképek
description: Ismerkedjen meg a Azure Maps Android SDK-val. Ismerje meg, hogyan hozhat létre projektet a Android Studioban, hogyan telepítheti az SDK-t, és hogyan hozhat létre interaktív térképet.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5888a5f34ef65fc1015b6e73af1d03368a8329b2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098519"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Ismerkedés a Azure Maps Android SDK-val

A Azure Maps Android SDK az Androidhoz készült vektoros Térkép-könyvtár. Ez a cikk végigvezeti a Azure Maps Android SDK telepítésének és a Térkép betöltésének folyamatán.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a gyors útmutató [: Android-alkalmazás létrehozása](quick-android-map.md) dokumentum lépéseit kell végrehajtania.

## <a name="localizing-the-map"></a>A Térkép honosítása

A Azure Maps Android SDK három különböző módszert biztosít a Térkép nyelvének és regionális nézetének beállításához. A következő kód bemutatja, hogyan állíthatja be a nyelvet francia ("fr-FR") és a regionális nézetbe az "Auto" értékre.

Az első lehetőség, hogy átadja a nyelvet, és megtekinti a regionális információkat az `AzureMaps` osztályba a statikus `setLanguage` és a `setView` metódusok globális használatával. Ezzel a beállítással megadhatja az alkalmazásban betöltött összes Azure Maps vezérlő alapértelmezett nyelvi és regionális nézetét.

::: zone pivot="programming-language-java-android"

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Alternatively use Azure Active Directory authenticate.
    //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

A második lehetőség, hogy átadja a nyelvet, és megtekinti az információkat a Térkép vezérlőelem XML-kódjában.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

A harmadik lehetőség az, hogy programozott módon állítsa be a Térkép nyelvét és regionális nézetét a Maps `setStyle` metódus használatával. Ezt bármikor megteheti a Térkép nyelvének és regionális nézetének módosításához.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl.onReady(OnReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
})
```

::: zone-end

Itt látható egy példa arra, hogy Azure Maps a "fr-FR" és a regionális nézet beállítása "Auto" értékre.

![Azure Maps, a feliratokat ábrázoló Térkép ábrázolása francia nyelven](media/how-to-use-android-map-control-library/android-localization.png)

A támogatott nyelvek és regionális nézetek teljes listáját [itt](supported-languages.md)dokumentáljuk.

## <a name="navigating-the-map"></a>Navigálás a térképen

A Térkép több különböző módon nagyítható, megpárolt, elforgatható és felhelyezhető. A következő részletek a Térkép különböző módjaira mutatnak.

**A Térkép nagyítása**

* A térképhez koppintson a két ujjal és a csípéssel együtt a nagyításhoz vagy az ujjak egymáshoz közelítéséhez.
* Egy szint nagyításához koppintson duplán a térképre.
* Dupla koppintással két ujjal nagyíthatja ki a térképet egy szinttel.
* Koppintás kétszer; a második koppintásnál tartsa az ujját a térképen, és húzza a nagyításhoz vagy a kicsinyítéshez a nagyításhoz.

**A Térkép pásztázása**

* Érintse meg a térképet, és húzza a kívánt irányba.

**A Térkép elforgatása**

* Érintse meg a térképet két ujjal, és forgassa el.

**A Térkép feldobása**

* Érintse meg a térképet két ujjal, és húzza őket egymáshoz.

## <a name="azure-government-cloud-support"></a>Felhő-támogatás Azure Government

A Azure Maps Android SDK támogatja a Azure Government-felhőt. A Azure Maps Android SDK ugyanazon Maven-tárházból érhető el. A Azure Maps platform Azure Government Cloud-verziójához való kapcsolódáshoz a következő feladatokat kell elvégezni.

Ugyanazon a helyen, ahol meg van adva a Azure Maps-hitelesítés részletei, adja hozzá a következő kódrészletet, hogy tájékoztassa a térképet a Azure Maps kormányzati felhő tartományának használatáról.

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

Ügyeljen arra, hogy a Térkép és a szolgáltatások hitelesítése során a Azure Government Cloud platformon Azure Maps hitelesítés részleteit használja.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan adhat hozzá átfedéses információkat a térképen:

> [!div class="nextstepaction"]
> [Hitelesítés kezelése Azure Maps](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Térkép stílusainak módosítása Android-térképeken](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)
