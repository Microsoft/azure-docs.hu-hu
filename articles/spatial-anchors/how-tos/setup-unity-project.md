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
ms.openlocfilehash: 56532c17c91e6c703a6acd7990bbae47cd248165
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "99576565"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Azure térbeli horgonyok konfigurálása Unity-projektben

Ez az útmutató bemutatja, hogyan kezdheti meg az Azure térbeli horgonyok SDK-t az Unity-projektben.

## <a name="project-requirements"></a>A projektre vonatkozó követelmények

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Projekt konfigurálása

Mielőtt belefoglalja az Azure térbeli horgonyok SDK-t az Unity-projektbe, ügyeljen arra, hogy a [szükséges](#project-requirements) csomagokat a Unity Package Manageren keresztül telepítse.

### <a name="download-asa-packages"></a>ASA-csomagok letöltése
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>ASA-csomagok importálása
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
