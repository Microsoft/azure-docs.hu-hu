---
title: A saját alkalmazásában Azure IoT Central eszközök | Microsoft Docs
description: Operátorként megtudhatja, hogyan kezelheti az eszközöket a Azure IoT Central alkalmazásában. Megtudhatja, hogyan kezelheti az egyes eszközöket, és hogyan importálhatja és exportálhatja tömegesen az eszközöket az alkalmazásban.
author: dominicbetts
ms.author: dobett
ms.date: 10/08/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5bab4a7a90101d3749571e0f2d4179f0fce14296
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378635"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Eszközök kezelése a Azure IoT Central alkalmazásban

Ez a cikk azt ismerteti, hogy operátorként hogyan kezelheti az eszközöket a Azure IoT Central alkalmazásban. Operátorként a következőt használhatja:

- Az Eszközök **lapon** megtekintheti, hozzáadhatja és törölheti az Azure IoT Central csatlakoztatott eszközöket.
- Eszközök tömeges importálása és exportálása.
- Az eszközök naprakész leltárának fenntartása.
- Tartsa naprakészen az eszköz metaadatait az eszköztulajdonságokban tárolt értékek nézetekből való módosításával.
- Szabályozhatja az eszközök viselkedését egy adott eszköz adott beállításának a nézetekből való frissítésével.

További információ az egyéni eszközcsoportok kezeléséről: Oktatóanyag: Eszközcsoportok használata [az eszköz telemetriai adatainak elemzéséhez.](tutorial-use-device-groups.md)

## <a name="view-your-devices"></a>Eszközök megtekintése

Egy adott eszköz megtekintése:

1. A **bal oldali panelen** válassza az Eszközök lehetőséget. Itt láthatja az összes eszköz és az eszközsablonok listáját.

1. Válasszon egy eszközsablont.

1. Az Eszközök lap jobb oldali **panelén** megjelenik az eszközsablonból létrehozott eszközök listája. Válasszon ki egy adott eszközt az eszköz részleteit tartalmazó oldalának a megtekintése előtt:

    ![Eszközadatok lap](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Eszköz hozzáadása

Eszköz hozzáadása a Azure IoT Central alkalmazáshoz:

1. A **bal oldali panelen** válassza az Eszközök lehetőséget.

1. Válassza ki azt az eszközsablont, amelyből létre szeretné hozni az eszközt.

1. Válassza az + **Új lehetőséget.**

1. Kapcsolja be **vagy** ki  a Szimulált **kapcsolót.** A valós eszköz olyan fizikai eszközhöz való, amely a Azure IoT Central csatlakozik. A szimulált eszközök az Ön számára létrehozott mintaadatokat Azure IoT Central.

1. Válassza a **Létrehozás** lehetőséget.

1. Ez az eszköz megjelenik a sablon eszközlistán. Válassza ki az eszközt az eszköz összes nézetét tartalmazó eszközadatok oldalának kiválasztásához.

## <a name="import-devices"></a>Eszközök importálása

Ha nagy számú eszközt szeretne csatlakoztatni az alkalmazáshoz, tömegesen importálhat eszközöket egy CSV-fájlból. A CSV-fájlnak a következő oszlopfejlécekkel kell lennie:

| Oszlop | Leírás 
| - | - | 
| IOTC_DEVICEID | Az eszközazonosító egy egyedi azonosító, amely alapján az eszköz csatlakozni fog. Az eszközazonosító betűket, számokat és szóköz `-` nélküli karaktert tartalmazhat. |
| IOTC_DEVICENAME | Választható. Az eszköz neve egy rövid név, amely az alkalmazás egészében megjelenik. Ha nincs megadva, ez ugyanaz lesz, mint az eszköz azonosítója.   |

Eszközök tömeges regisztrálása az alkalmazásban:

1. A **bal oldali panelen** válassza az Eszközök lehetőséget.

1. A bal oldali panelen válassza ki azt az eszközsablont, amelyhez tömegesen létre szeretné hozni az eszközöket.

    > [!NOTE]
    > Ha még nincs eszközsablonja, importálhatja az  eszközöket a Minden eszköz alatt, és regisztrálhatja őket sablon nélkül. Az eszközök importálása után át lehet őket mirateálni egy sablonba.

1. Válassza az **Importálás** lehetőséget.

    ![Importálási művelet](./media/howto-manage-devices/bulkimport1a.png)


1. Válassza ki azt a CSV-fájlt, amely tartalmazza az importálni kívánt eszközelődök listáját.

1. Az eszköz importálása a fájl feltöltése után kezdődik. Az importálás állapotát az Eszközműveletek panelen követheti nyomon. Ez a panel automatikusan megjelenik az importálás elindulása után, vagy a jobb felső sarokban található harang ikonnal is elérhető.

1. Ha az importálás befejeződött, az Eszközműveletek panelen megjelenik a sikeres műveletet ikonra megjelenik egy üzenet.

    ![Sikeres importálás](./media/howto-manage-devices/bulkimport3a.png)

Ha az eszköz importálási művelete sikertelen, hibaüzenet jelenik meg az Eszközműveletek panelen. Létrejön egy naplófájl, amely az összes letölthető hibát naplóz.

## <a name="migrate-devices-to-a-template"></a>Eszközök áttelepítése sablonba

Ha úgy regisztrálja az eszközöket, hogy az importálást a **Minden** eszköz alatt kezdi, akkor az eszközök eszközsablon-társítás nélkül létrejönnek. Az eszközöknek egy sablonhoz kell társítva lennie az adatok és az eszköz egyéb részleteinek feltárására. Az eszközök sablonhoz való társításához kövesse az alábbi lépéseket:

1. A **bal oldali panelen** válassza az Eszközök lehetőséget.

1. A bal oldali panelen válassza a **Minden eszköz lehetőséget:**

    ![Nem társított eszközök](./media/howto-manage-devices/unassociateddevices1a.png)

1. A rács szűrője segítségével állapítsa  meg, hogy az Eszközsablon oszlopban lévő érték nincs-e **társítva** egyik eszközhöz sem.

1. Válassza ki a sablonhoz társítani kívánt eszközöket:

1. Válassza **az Áttelepítés lehetőséget:**

    ![Eszközök társítása](./media/howto-manage-devices/unassociateddevices2a.png)

1. Válassza ki a sablont az elérhető sablonok listájából, majd válassza az **Áttelepítés lehetőséget.**

1. A kiválasztott eszközök a választott eszközsablonhoz vannak társítva.

## <a name="export-devices"></a>Eszközök exportálása

Ha valós eszközt szeretne csatlakoztatni a IoT Central, szüksége lesz a kapcsolati sztringre. Az eszközadatok tömeges exportálása az eszközkapcsolati sztringek létrehozásához szükséges információk lekérte. Az exportálási folyamat létrehoz egy CSV-fájlt az összes kiválasztott eszköz eszközidentitásával, eszköznevével és kulcsával.

Eszközök tömeges exportálása az alkalmazásból:

1. A **bal oldali panelen** válassza az Eszközök lehetőséget.

1. A bal oldali panelen válassza ki azt az eszközsablont, amelyből exportálni szeretné az eszközöket.

1. Jelölje ki az exportálni kívánt eszközöket, majd válassza az **Exportálás műveletet.**

    ![Exportálás](./media/howto-manage-devices/export1a.png)

1. Megkezdődik az exportálási folyamat. Az állapotot az Eszközműveletek panelen követheti nyomon.

1. Amikor az exportálás befejeződik, megjelenik egy sikert mutató üzenet a létrehozott fájl letöltésére mutató hivatkozással együtt.

1. A Fájl **letöltése hivatkozásra kattintva** töltse le a fájlt a lemez egy helyi mappájába.

    ![Sikeres exportálás](./media/howto-manage-devices/export2a.png)

1. Az exportált CSV-fájl a következő oszlopokat tartalmazza: eszközazonosító, eszköznév, eszközkulcsok és X509-tanúsítvány-ujjlenyomatok:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

A kapcsolati sztringekkel és a valós eszközök a IoT Central alkalmazáshoz való csatlakoztatásával kapcsolatos további információkért lásd: Eszközkapcsolat a [Azure IoT Central.](concepts-get-connected.md)

## <a name="delete-a-device"></a>Eszköz törlése

Valós vagy szimulált eszköz törlése a Azure IoT Central alkalmazásból:

1. A **bal oldali panelen** válassza az Eszközök lehetőséget.

1. Válassza ki a törölni kívánt eszköz eszközsablonját.

1. A szűrőeszközökkel szűrheti és keresheti meg az eszközeit. Jelölje be a törölni kívánt eszközök melletti jelölőnégyzetet.

1. Válassza a **Törlés** elemet. A törlés állapotát az Eszközműveletek panelen követheti nyomon.

## <a name="change-a-property"></a>Tulajdonság módosítása

A felhőtulajdonságok az eszközhöz társított eszköz-metaadatok, például város és sorozatszám. A felhőtulajdonságok csak a IoT Central alkalmazásban léteznek, és nincsenek szinkronizálva az eszközökkel. Az írható tulajdonságok vezérelik az eszközök viselkedését, és segítségével távolról állíthatja be az eszköz állapotát, például egy termosztátos eszköz célhőmérsékletének beállításával.  Az eszköztulajdonságokat az eszköz adja meg, és csak olvashatók a IoT Central. Az eszköz Eszköz részletei  nézeteiben megtekintheti és frissítheti a tulajdonságokat.

1. A **bal oldali panelen** válassza az Eszközök lehetőséget.

1. Válassza ki annak az eszköznek az eszközsablonját, amelynek tulajdonságait módosítani szeretné, majd válassza ki a céleszközt.

1. Válassza ki azt a nézetet, amely az eszköz tulajdonságait  tartalmazza. Ez a nézet lehetővé teszi az értékek bevitelét, majd az oldal tetején található Mentés lehetőséget. Itt láthatja az eszköz tulajdonságait és azok aktuális értékeit. A felhőtulajdonságok és az írható tulajdonságok szerkeszthető mezőkkel rendelkeznek, az eszköztulajdonságok pedig csak olvashatók. Az írható tulajdonságok szinkronizálási állapota a mező alján látható. 

1. Módosítsa a tulajdonságokat a szükséges értékekre. Egyszerre több tulajdonságot is módosíthat, és egyszerre frissítheti őket.

1. Válassza a **Mentés** elemet. Ha írható tulajdonságokat mentett, a rendszer elküldi az értékeket az eszközre. Amikor az eszköz megerősíti az írható tulajdonság változását, az állapot visszatér **szinkronizált állapotúra.** Ha felhőtulajdonságokat mentett, az érték frissül.

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan kezelheti az eszközöket a Azure IoT Central-alkalmazásban, a[](howto-configure-rules.md) javasolt következő lépés az eszközök szabályainak konfigurálása.
