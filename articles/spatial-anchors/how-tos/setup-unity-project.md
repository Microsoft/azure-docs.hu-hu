---
title: Azure térbeli horgonyok telepítése Unity számára
description: Unity-projekt konfigurálása az Azure térbeli horgonyok használatára
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: e058186d8848256bf97d99ee1b8b1ddae7d78383
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550623"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Azure térbeli horgonyok konfigurálása Unity-projektben

Ez az útmutató bemutatja, hogyan kezdheti meg az Azure térbeli horgonyok SDK-t az Unity-projektben.

## <a name="requirements"></a>Követelmények

Az Azure térbeli horgonyok jelenleg a 2019,4 (LTS) Unity-t támogatják a következő konfigurációkkal.

* Az 2019,4-os egység az AR Foundation 3,1-mel támogatott az Azure térbeli 2.4.0 +-ben.

## <a name="configuring-a-project"></a>Projekt konfigurálása

### <a name="download-packages"></a>Csomagok letöltése
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>Csomagok importálása
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Csak Android esetén: a mainTemplate. gradle fájl konfigurálása

1. Lépjen a   >  **Project Settings**  >  **Player** szerkesztése menüpontra.
2. A **Player-beállítások** **ellenőr paneljén** válassza az **Android** ikont.
3. A **build (létrehozás** ) szakaszban jelölje be az **Egyéni Gradle-sablon** jelölőnégyzetet az egyéni Gradle-sablon létrehozásához a következő helyen: `Assets\Plugins\Android\mainTemplate.gradle` .
4. Nyissa meg a `mainTemplate.gradle` fájlt egy szövegszerkesztőben.
5. A `dependencies` szakaszban illessze be a következő függőségeket:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Ha elkészült, a `dependencies` szakasznak a következőhöz hasonlóan kell kinéznie:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató: horgonyok létrehozása és megkeresése az egységben](./create-locate-anchors-unity.md)
