---
title: Farmok kezelése
description: A farmok felügyeletének ismertetése
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 050b3b4d67eda9b6c9b4621c014e3e6baad34053
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173800"
---
# <a name="manage-farms"></a>Farmok kezelése

A farmokat az Azure FarmBeats kezelheti. Ez a cikk bemutatja, hogyan hozhat létre farmokat, telepíthet eszközöket, érzékelőket és heréket, amelyek segítségével kezelheti a farmokat.

## <a name="create-farms"></a>Farmok létrehozása

Ehhez a következő lépések szükségesek:

1. Jelentkezzen be a farm-Gyorssegédbe, a **farmok** lap jelenik meg.
    A **Farms** lap megjeleníti a farmok listáját, ha azokat már létrehozták az előfizetésben.

    Itt látható a minta képe:

    ![A farmok lapot megjelenítő képernyőkép.](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Válassza a **farm létrehozása** elemet, és adja meg a **nevet**, a **növényeket** és a **címeket**.
3. A **Farm határának megadása**(kötelező mező) területen válassza a **megjelölés térképen** vagy a **GeoJSON-kód beillesztése** lehetőséget.

A farm határának definiálásának két módja van:

1. **Megjelölés térképen**: a Térkép vezérlőelem használatával rajzolhatja meg és jelölheti meg a farm határát. A szegélyek megjelöléséhez a térképen a diagram  ![ szegélyeit ábrázoló ceruza ikon látható, ](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) és a pontos határokat kell megjelölnie.

    ![Képernyőkép, amely megjeleníti a rajzolt határokat egy térképen.](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **GeoJson-kód beillesztése**: a GeoJson formátuma a földrajzi adatstruktúrák kódolása JavaScript Object Notation (JSON) használatával. Ezzel a beállítással megjelenítheti azt a szövegmezőt, amelyben meg lehet adni egy GeoJSON karakterláncot a farm határainak megjelöléséhez. GeoJSON-kódot a GeoJSON.io is létrehozhat.
Az elemleírások segítségével kitöltheti az információkat.

    ![A farm létrehozása képernyő GeoJson-kód beillesztése lehetőségét kiemelő képernyőkép.](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Farm létrehozásához válassza a **Submit (Küldés** ) lehetőséget. Létrejön egy új Farm, és megjelenik a **farmok** oldalon.

## <a name="view-farm"></a>Farm megtekintése

A farm listája lap a létrehozott farmok listáját jeleníti meg. Válasszon ki egy Farmt a listájának megtekintéséhez:

 - **Eszközök száma – a** farmon belül üzembe helyezett eszközök számát és állapotát jeleníti meg.
 - **Térkép** – a farm leképezése a farmon üzembe helyezett eszközökkel.
 - **Telemetria** – a telemetria a farmon üzembe helyezett érzékelőkből jeleníti meg.
 - **Legújabb precíziós térképek** – a legújabb Satellite indexeket (Evi, NDWI), a hő és az érzékelő elhelyezési térképét jeleníti meg.

## <a name="edit-farm"></a>Farm szerkesztése

A **Farms** lap a létrehozott farmok listáját jeleníti meg.

1.  Válasszon ki egy farmot a farm megtekintéséhez és szerkesztéséhez.
2.  Válassza a **Farm szerkesztése** elemet a farm adatainak szerkesztéséhez. A **Farm részleteit** tartalmazó ablakban szerkesztheti a **név**, **a növények**, a **címek** és a **Farm határainak** meghatározása mezőket.

    ![A Project Farm veri](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Kattintson a **Submit (Küldés** ) elemre a szerkesztett adatok mentéséhez.

## <a name="delete-farm"></a>Farm törlése

A **Farms** lap a létrehozott farmok listáját jeleníti meg. Farm törléséhez kövesse az alábbi lépéseket:

1.  A farm adatainak törléséhez válasszon ki egy farmot a listából.
2.  Válassza a **Farm törlése** elemet a farm törléséhez.

    ![Képernyőkép, amely a farm törlése képernyőt jeleníti meg, és kiemeli a Törlés gombot.](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Farm törlésekor a farmhoz társított eszközöket és térképeket nem törli a rendszer. Az eszközhöz és a térképekhez kapcsolódó Farm-információk nem lesznek relevánsak. Továbbra is megtekintheti az eszközöket, a telemetria és a térképeket a FarmBeats szolgáltatásból.


## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozta a farmot, megtudhatja, hogyan érheti el a farmján az [érzékelők adatait](get-sensor-data-from-sensor-partner.md) .
