---
title: Talaj nedvesség-hő előállítása
description: A cikk bemutatja, hogyan hozhatja ki a hő a talaj nedvességtartalmát az Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: dd8d688355e0f71ce77cdbc6012d788c7b16d825
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173783"
---
# <a name="generate-soil-moisture-heatmap"></a>Talaj nedvesség-hő előállítása

A talaj nedvességtartalma az a víz, amelyet a rendszer a talaj részecskék közötti térközben tárol.A talaj nedvesség-hő segít megérteni a nedvesség adatait bármilyen mélységben és nagy felbontásban a farmokon belül. Pontos és felhasználható nedvesség-hő létrehozásához az érzékelők egységes üzembe helyezése szükséges az azonos szolgáltatótól. A különböző szolgáltatók eltérőek lehetnek a talaj nedvességtartalmának mérésével, valamint a kalibrálási különbségekkel. A hő az adott mélységben üzembe helyezett érzékelők használatával generáljuk.

Ez a cikk azt ismerteti, hogyan hozható létre a talaj nedvességtartalmának hő a farmhoz az Azure FarmBeats-gyorsító használatával. Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

- [Farmok létrehozása](#create-a-farm)
- [Érzékelők kiosztása farmokhoz](#get-soil-moisture-sensor-data-from-partner)
- [Talaj nedvesség-hő előállítása](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze a következőket:  

- Azure-előfizetés.
- Az Azure FarmBeats futó példánya.
- A farm számára legalább három nedvesség-érzékelő érhető el.

## <a name="create-a-farm"></a>Farm létrehozása

A farm olyan földrajzi terület, amelynek az a célja, hogy hő hozzon létre. Farmokat a Farms [API](https://aka.ms/FarmBeatsDatahubSwagger) -val vagy a FarmsBeats- [gyorsító kezelőfelületén](manage-farms-in-azure-farmbeats.md#create-farms) hozhat létre.

## <a name="deploy-sensors"></a>Érzékelők üzembe helyezése

A farmon fizikailag üzembe kell helyezni a talaj nedvességtartalmának érzékelőit. Bármely jóváhagyott partnertől, a [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) és a [Teralytic](https://teralytic.com/)megvásárolhatja a talaj nedvességtartalmának érzékelőit. Koordinálja az érzékelő szolgáltatóját, hogy elvégezze a fizikai telepítést a farmon.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>A talaj nedvesség-érzékelői adatainak beolvasása a partnertől

Ahogy az érzékelők elkezdik a folyamatos átvitelt, a partneri adatirányítópultba helyezik az adattovábbítást, lehetővé teszik az Azure FarmBeats való adatgyűjtést. Ezt megteheti a partner alkalmazásból.

Ha például megvásárolta a Davis szenzorokat, bejelentkezik az időjárási kapcsolati fiókjába, és megadja a szükséges hitelesítő adatokat az Azure FarmBeats való adattovábbítás engedélyezéséhez. A szükséges hitelesítő adatok beszerzéséhez kövesse az [érzékelők adatainak beolvasása](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)című témakör utasításait.

Miután megadta a hitelesítő adatait, és kiválasztja a **beküldés** lehetőséget a partneri alkalmazásban, az adatok áthelyezhetők az Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>A talaj nedvességtartalmának érzékelők kiosztása a farmhoz

Miután összekapcsolta a Sensor-fiókját az Azure FarmBeats, hozzá kell rendelnie a talajhoz tartozó nedvesség-érzékelőket a farmhoz.

1.  A kezdőlapon válassza a **farmok** lehetőséget a menüből, a **farmok** listája lap jelenik meg.
2.  Válassza a **MyFarm**  >  **eszközök hozzáadása** elemet.
3.  Megjelenik az **eszközök hozzáadása** ablak. Válassza ki a farm nedvesség-érzékelőkhöz kapcsolódó összes eszközt.

    ![Képernyőkép, amely az eszközök hozzáadása képernyőt jeleníti meg.](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Válassza az **eszközök hozzáadása** lehetőséget.     

## <a name="generate-soil-moisture-heatmap"></a>Talaj nedvesség-hő előállítása

Ez a lépés egy olyan feladatot vagy hosszan futó műveletet hoz létre, amely a farmhoz tartozó hő fog létrehozni.

1.  A kezdőlapon kattintson a **farmok** elemre a bal oldali navigációs menüből a farmok oldal megtekintéséhez.
2.  Válassza a **MyFarm** lehetőséget.
3.  A **Farm részletei** lapon válassza a **pontos leképezés készítése** lehetőséget.
4.  A legördülő menüben válassza a **talaj nedvesség** elemet.
5.  A **talaj nedvességtartalma** ablakban válassza ki **ezt a hetet**.
6.  A **felszín nedvesség** - **érzékelő kiválasztása mértéke** mezőben adja meg a térképhez használni kívánt mértéket.
    Az érzékelő mértékének megkereséséhez a **szenzorokban** válassza ki a talaj nedvességtartalmának érzékelőjét. Az **érzékelő tulajdonságainál** használja a **mérték neve** értéket.

    ![Képernyőkép, amely a talaj nedvességtartalmának képernyőjét mutatja.](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Válassza a **leképezések előállítása** lehetőséget.
    Megjelenik egy megerősítő üzenet, amely a feladatok részleteit jeleníti meg. További információ: feladat állapota a feladatokban.

    >[!NOTE]
    > A feladatokhoz három-négy óra szükséges.

### <a name="download-the-soil-moisture-heatmap"></a>A talaj nedvességtartalmának hő letöltése

Ehhez a következő lépések szükségesek:

1. A **feladatok** lapon tekintse meg az utolsó eljárás során létrehozott feladat **állapotát** .
2. Ha a feladatok állapota **sikeres**, válassza a menü **térképek** elemét.
3. Keresse meg a térképen a következő formátumban: <talaj-moisture_MyFarm_YYYY-hh-nn>.
4. Válasszon ki egy térképet a **Name (név** ) oszlopban, megjelenik egy előugró ablak, amely a kiválasztott Térkép előnézetét jeleníti meg.
5. Válassza a **Letöltés** lehetőséget. A Térkép le van töltve, és a számítógép helyi mappájába lesz tárolva.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen létrehozta a talaj nedvességtartalmának hő, megtudhatja, hogyan [hozhatja létre az érzékelő elhelyezését](generate-maps-in-azure-farmbeats.md#sensor-placement-map) és hogyan végezheti el a [korábbi telemetria-információkat](ingest-historical-telemetry-data-in-azure-farmbeats.md) 
