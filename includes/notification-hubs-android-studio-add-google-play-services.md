---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd202c98ed605209f5600965ecdb6c0b4c03c17e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67509100"
---
1. A Android Studio területen válassza a menü **eszközök** elemét, majd válassza az **SDK-kezelő** elemet. 
2. Válassza ki a projektben használt Android SDK cél verzióját. Ezután válassza a **csomag adatainak megjelenítése** lehetőséget. 

    ![Android SDK-kezelő – cél verziójának kiválasztása](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Válassza a **Google API**-k lehetőséget, ha még nincs telepítve.

    ![Android SDK-kezelő – kiválasztott Google API-k](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Váltson az **SDK-eszközök** lapra. Ha még nem telepítette a Google Play-szolgáltatásokat, válassza a **Google Play-szolgáltatások** lehetőséget az alábbi ábrán látható módon. Ezután válassza az **alkalmaz** elemet a telepítéshez. Jegyezze fel az SDK elérési útját, mert szükség lesz rá egy későbbi lépésben.

    ![Android SDK-kezelő – kiválasztott Google Play-szolgáltatások](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Ha megjelenik a **módosítás megerősítése** párbeszédpanel, kattintson az **OK gombra**. Az összetevő-telepítő telepíti a kért összetevőket. Az összetevők telepítése után válassza a **Befejezés** lehetőséget.
4. Az **új projektek beállításainak** bezárásához kattintson **az OK gombra** .  
5. Nyissa meg a Build. gradle fájlt az **alkalmazás** könyvtárában, majd adja hozzá a következő sort a elemhez `dependencies` . 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Kattintson a **szinkronizálás most** ikonra az eszköztáron.

    ![Szinkronizálás a Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Nyissa meg a AndroidManifest.xml fájlt, majd adja hozzá a következő címkét az *alkalmazás* címkéjéhez.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
