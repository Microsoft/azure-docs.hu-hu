---
title: Oktatóanyag – Azure IoT hub manuális feladatátvétele | Microsoft Docs
description: Oktatóanyag – megtudhatja, hogyan végezheti el az IoT hub manuális feladatátvételét egy másik régióba, és ellenőrizze, hogy működik-e, majd küldje vissza az eredeti régióba, és próbálkozzon újra.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 3b6bc972f5c26c78ffff81f5bab8c2812cf2cb11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98622913"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Oktatóanyag: manuális feladatátvétel végrehajtása egy IoT hub esetében

A manuális feladatátvétel az IoT Hub szolgáltatás egyik funkciója, amelynek segítségével elvégezhető egy hub műveleteinek [feladatátvétele](https://en.wikipedia.org/wiki/Failover) az elsődleges régióból a megfelelő, földrajzilag párosított másodlagos Azure-régióba. A manuális feladatátvétel regionális szintű katasztrófák vagy hosszabb időtartamú szolgáltatáskimaradás alkalmával hajtható végre. Emellett végrehajthat tervezett feladatátvételt is a vészhelyreállítási képességek tesztelésére, bár ehhez inkább egy tesztelési célú, és nem egy éles környezetben futó IoT hub használatát javasoljuk. A manuális feladatátvétel funkció az ügyfelek számára ingyenes, a 2017. május 18. után létrehozott IoT hubok esetében díjmentesen vehető igénybe.

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * IoT hub létrehozása az Azure Portal használatával. 
> * Feladatátvétel végrehajtása. 
> * A másodlagos helyen futó hub megtekintése.
> * Feladat-visszavétel végrehajtása az IoT hub műveleteinek visszahelyezésére az elsődleges helyre. 
> * A központ a megfelelő helyen való megfelelő működésének ellenőrzése.

A manuális feladatátvételsel és a Microsoft által kezdeményezett feladatátvételsel kapcsolatos további információkért lásd: a [régiók közötti](iot-hub-ha-dr.md#cross-region-dr)vész-helyreállítás IoT hub.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az oktatóanyagban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com). 

2. Kattintson a **+ Erőforrás létrehozása** elemre, és válassza az **Eszközök internetes hálózata**, majd az **IoT Hub** elemet.

   ![IoT hub létrehozását ábrázoló képernyőkép](./media/tutorial-manual-failover/create-hub-01.png)

3. Válassza az **alapok** lapot. Adja meg a következő mezőket.

    **Előfizetés**: Válassza ki a használni kívánt Azure-előfizetést.

    **Erőforráscsoport**: Kattintson az **Új létrehozása** gombra, majd adja a **ManlFailRG** nevet az erőforráscsoportnak.

    **Régió**: válasszon ki egy régiót a közelben. Ebben az oktatóanyagban a következőt használjuk: `West US 2`. A feladatátvétel csak földrajzilag párosított Azure-régiók között hajtható végre. Az USA 2. nyugati régiója, a WestCentralUS.
    
   **IoT hub neve**: Adja meg az IoT hub nevét. A hub nevének globálisan egyedinek kell lennie. 

   ![IoT hub létrehozásához az Alapvető beállítások panelt ábrázoló képernyőkép](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Kattintson a **Felülvizsgálat + létrehozás** elemre. (Az alapértelmezett méret- és léptékbeállításokat használja.) 

4. Tekintse át az információkat, majd kattintson a **Létrehozás** elemre az IoT hub létrehozásához. 

   ![IoT hub létrehozásának utolsó lépését ábrázoló képernyőkép](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Manuális feladatátvétel végrehajtása

Vegye figyelembe, hogy naponta legfeljebb két feladatátvétel és két feladat-visszavétel hajtható végre minden egyes IoT hub esetében.

1. Kattintson az **Erőforráscsoportok** elemre, majd válassza ki a **ManlFailRG** erőforráscsoportot. Kattintson a hubra az erőforrások listájában. 

1. A IoT Hub panel **Beállítások** területén kattintson a **feladatátvétel** elemre.

   ![IoT Hub-tulajdonságok panel képernyőképe](./media/tutorial-manual-failover/trigger-failover-01.png)

1. A manuális feladatátvétel panelen az **aktuális helyet** és a **feladatátvételi helyet** látja. Az aktuális hely mindig azt a helyet adja meg, amelyben a központ éppen aktív. A feladatátvétel helye a szabványos [Azure-beli geo-párosítási régió](../best-practices-availability-paired-regions.md) , amely az aktuális helyre párosítva van. A helyek értékei nem módosíthatók. Ebben az oktatóanyagban az aktuális hely, `West US 2` a feladatátvételi hely pedig `West Central US` .

   ![A Manuális feladatátvétel panel képernyőképe](./media/tutorial-manual-failover/trigger-failover-02.png)

1. A manuális feladatátvétel panel felső részén kattintson a **feladatátvétel indítása** elemre. 

1. A megerősítő ablaktáblán adja meg az IoT hub nevét annak megerősítéséhez, hogy a feladatátvételt kívánja használni. Ezután a feladatátvétel elindításához kattintson a **feladatátvétel** elemre.

   A manuális feladatátvétel végrehajtásához szükséges idő a hubhoz regisztrált eszközök számával arányos. Például 100 000 eszköz esetén a feladatátvétel 15 percig tarthat, ötmillió eszköz esetén azonban egy órát vagy többet is igénybe vehet.

   ![A manuális feladatátvétel megerősítő ablaktábláját ábrázoló képernyőfelvétel](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Amíg a manuális feladatátvételi folyamat fut, megjelenik egy szalagcím, amely jelzi, hogy egy manuális feladatátvétel folyamatban van. 

   ![Folyamatban lévő manuális feladatátvétel képernyőképe](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Ha bezárta a IoT Hub ablaktáblát, és ismét megnyitja az erőforráscsoport ablaktáblán, akkor megjelenik egy szalagcím, amely azt jelzi, hogy a központ egy manuális feladatátvétel közepén található. 

   ![A folyamatban lévő IoT Hub feladatátvételt ábrázoló képernyőfelvétel](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   A művelet befejezése után a manuális feladatátvételi oldalon található aktuális és feladatátvételi régiók tükrözöttek, és a központ ismét aktív lesz. Ebben a példában az aktuális hely most, `WestCentralUS` a feladatátvételi hely pedig most `West US 2` . 

   ![Befejeződött feladatátvétel képernyőképe](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   Az Áttekintés oldalon egy szalagcím is látható, amely azt jelzi, hogy a feladatátvétel befejeződött, és a IoT Hub fut `West Central US` .

   ![A feladatátvételt bemutató képernyőkép az Áttekintés oldalon](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Feladat-visszavétel végrehajtása 

A manuális feladatátvétel végrehajtását követően a hub feladatait visszahelyezheti az eredeti elsődleges régióba – ezt feladat-visszavételnek nevezzük. Ha épp most hajtotta végre a feladatátvételt, körülbelül egy órát várnia kell, mielőtt kezdeményezhetné a feladat-visszavételt. Ha ennél hamarabb próbál feladat-visszavételt végrehajtani, egy hibaüzenet jelenik meg.

A feladat-visszavétel a manuális feladatátvételhez hasonlóan történik. A lépések a következők: 

1. Feladat-visszavétel végrehajtásához lépjen vissza az IoT hubhoz tartozó IoT Hub panelre.

2. A IoT Hub panel **Beállítások** területén kattintson a **feladatátvétel** elemre. 

3. A manuális feladatátvétel panel felső részén kattintson a **feladatátvétel indítása** elemre. 

4. A megerősítő panelen adja meg az IoT hub nevét annak megerősítéséhez, hogy a feladat-visszavételt szeretné. Ezután a feladat-visszavétel indításához kattintson az OK gombra. 

   ![Manuális feladat-visszavételi kérelem képernyőképe](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Itt is a feladatátvétel végrehajtását ismertető szakaszban ismertetett szalagcímek jelennek meg. A feladat-visszavétel befejezése után a rendszer ismét `West US 2` az aktuális helyként és `West Central US` a feladatátvételi helyként jeleníti meg az eredetileg beállított helyet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Az oktatóanyaghoz létrehozott erőforrások eltávolításához törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben ez eltávolítja az IoT hubot és magát az erőforráscsoportot is. 

1. Kattintson az **Erőforráscsoportok** lehetőségre. 

2. Keresse meg és válassza ki a **ManlFailRG** erőforráscsoportot. Kattintson rá a megnyitásához. 

3. Kattintson az **Erőforráscsoport törlése** lehetőségre. Amikor a rendszer kéri, írja be az erőforráscsoport nevét, és kattintson a **Törlés** elemre a megerősítéshez. 

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag azt ismertette, hogyan konfigurálhat és hajthat végre manuális feladatátvételt, és hogyan kérhet feladat-visszavételt a következő feladatok végrehajtásával:

> [!div class="checklist"]
> * IoT hub létrehozása az Azure Portal használatával. 
> * Feladatátvétel végrehajtása. 
> * A másodlagos helyen futó hub megtekintése.
> * Feladat-visszavétel végrehajtása az IoT hub műveleteinek visszahelyezésére az elsődleges helyre. 
> * A központ a megfelelő helyen való megfelelő működésének ellenőrzése.

A következő oktatóanyag az IoT-eszközök állapotának kezelését mutatja be. 

> [!div class="nextstepaction"]
> [IoT-eszköz állapotának kezelése](tutorial-device-twins.md)
