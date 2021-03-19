---
title: Azure IoT Central-kezelői útmutató
description: Az Azure IoT Central egy IoT-alkalmazásplatform, amely megkönnyíti IoT-megoldások létrehozását. Ez a cikk áttekintést nyújt a IoT Central operátori szerepköréről.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/17/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: a5c307b8c3c186fd7dff2084c728f02b8165b861
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609234"
---
# <a name="iot-central-operator-guide"></a>IoT Central operátori útmutató

Ez a cikk áttekintést nyújt a IoT Central operátori szerepköréről. Az operátor kezeli az alkalmazáshoz csatlakoztatott eszközöket.

Az _operátor_ kezeli az alkalmazáshoz csatlakoztatott eszközöket.

Operátorként a következőket teheti:

* Az **eszközök** lapon megtekintheti, hozzáadhatja és törölheti az Azure IoT Central-alkalmazáshoz csatlakoztatott eszközöket.
* Az eszközök tömeges importálása és exportálása.
* Naprakész leltárt készíthet az eszközökről.
* Tartsa naprakészen az eszköz metaadatait úgy, hogy megváltoztatja az eszköz tulajdonságai között tárolt értékeket a nézetekben.
* Az eszközök viselkedésének szabályozása egy adott eszközre vonatkozó beállítások frissítésével a nézetekben.

## <a name="iot-central-homepage"></a>IoT Central kezdőlapja

A [IoT Central kezdőlapja](https://aka.ms/iotcentral-get-started) az a hely, ahol további információt talál a IoT Central elérhető legfrissebb hírekről és szolgáltatásokról, új alkalmazások létrehozásáról, valamint a meglévő alkalmazás megnyitásáról.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central Kezdőlap képernyőképe":::

## <a name="view-your-devices"></a>Eszközök megtekintése

Egy adott eszköz megtekintése:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán. Itt látható az összes eszköz és az eszköz sablonjainak listája.

1. Válassza ki az eszköz sablonját.

1. Az **eszközök** lap jobb oldali ablaktábláján megjelenik az adott eszköz sablonjában létrehozott eszközök listája. Válasszon ki egy egyéni eszközt az eszköz adatai lap megjelenítéséhez:

    ![Az eszköz részletei lap képernyőképe](./media/overview-iot-central-operator/device-list.png)

## <a name="add-a-device"></a>Eszköz hozzáadása

Eszköz hozzáadása az Azure IoT Central-alkalmazáshoz:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki azt az eszközt, amelyből eszközt szeretne létrehozni.

1. Válassza az + **új** lehetőséget.

1. A **szimulált** váltógomb be- **vagy** **kikapcsolása**. Egy valós eszköz az Azure IoT Central-alkalmazáshoz csatlakoztatott fizikai eszközhöz. Egy szimulált eszközön az Azure IoT Central által létrehozott mintaadatok láthatók.

1. Válassza a **Létrehozás** lehetőséget.

1. Ez az eszköz most megjelenik az eszköz listájában ehhez a sablonhoz. Válassza ki az eszközt az eszköz részletei lap megjelenítéséhez, amely az eszköz összes nézetét tartalmazza.

## <a name="import-devices"></a>Eszközök importálása

Ha nagy számú eszközt szeretne összekötni az alkalmazással, tömegesen importálhatja az eszközöket egy CSV-fájlból. A CSV-fájlnak a következő oszlopokkal és fejlécekkel kell rendelkeznie:

* **IOTC_DeviceID** – az eszköz azonosítója betűket, számokat és a karaktert is tartalmazhat `-` .
* **IOTC_DeviceName** – ez az oszlop nem kötelező.

Eszközök tömeges regisztrálása az alkalmazásban:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. A bal oldali panelen válassza ki azt az eszközt, amelyhez tömegesen szeretné létrehozni az eszközöket.

    > [!NOTE]
    > Ha még nincs telepítve az eszközön, akkor a **minden eszköz** területen importálhatja az eszközöket, és sablon nélkül regisztrálhatja őket. Az eszközök importálása után áttelepítheti azokat egy sablonba.

1. Válassza az **Importálás** lehetőséget.

    ![Az importálási művelet képernyőképe](./media/overview-iot-central-operator/bulk-import-1-a.png)


1. Válassza ki az importálni kívánt eszköz-azonosítókat tartalmazó CSV-fájlt.

1. Az eszköz importálása a fájl feltöltése után elindul. Az importálási állapotot az eszköz operatív paneljén követheti nyomon. Ez a panel automatikusan megjelenik az importálás megkezdése után, vagy a jobb felső sarokban található harang ikonra kattintva érheti el.

1. Miután az importálás befejeződik, megjelenik egy sikeres üzenet az eszköz működési paneljén.

    ![Az importálás sikerességét bemutató képernyőkép](./media/overview-iot-central-operator/bulk-import-3-a.png)

Ha az eszköz importálási művelete meghiúsul, hibaüzenet jelenik meg az eszköz működési paneljén. Egy naplófájl rögzíti az összes hibát, amelyet letöltheti.

## <a name="migrate-devices-to-a-template"></a>Eszközök migrálása sablonba

Ha az eszközök **minden** eszköz alatt történő importálásával regisztrálja az eszközöket, akkor a rendszer az eszközöket sablon társítása nélkül hozza létre. Az eszközöket hozzá kell rendelni egy sablonhoz az eszköz adatainak és egyéb adatainak megismeréséhez. Kövesse az alábbi lépéseket az eszközök sablonhoz való hozzárendeléséhez:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. A bal oldali panelen válassza a **minden eszköz** lehetőséget:

    ![Képernyőfelvétel a nem társított eszközökről](./media/overview-iot-central-operator/unassociated-devices-2-a.png)

1. A rácson lévő szűrő használatával állapítsa meg, hogy az **eszköz sablonja** oszlopban szereplő érték nem **tartozik** -e egyetlen eszközhöz sem.

1. Válassza ki a sablonnal társítandó eszközöket:

1. **Áttelepítés** kiválasztása:

    ![Képernyőfelvétel az eszközök hozzárendeléséről](./media/overview-iot-central-operator/unassociated-devices-1-a.png)

1. Válassza ki a sablont az elérhető sablonok listájából, majd válassza az **áttelepítés** lehetőséget.

1. A kiválasztott eszközök társítva vannak a választott eszköz sablonnal.

## <a name="export-devices"></a>Eszközök exportálása

Ha egy valós eszközt szeretne csatlakoztatni a IoT Centralhoz, szüksége lesz a kapcsolati karakterláncra. Az eszköz adatainak tömeges exportálásával lekérheti az eszköz kapcsolati karakterláncok létrehozásához szükséges információkat. Az exportálási folyamat létrehoz egy CSV-fájlt az eszköz identitásával, az eszköz nevével és kulcsaival az összes kiválasztott eszközhöz.

Eszközök tömeges exportálása az alkalmazásból:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. A bal oldali panelen válassza ki azt az eszközt, amelyről exportálni szeretné az eszközöket.

1. Válassza ki az exportálni kívánt eszközöket, majd válassza ki az **Exportálás** műveletet.

    ![Az Exportálás képernyőképe](./media/overview-iot-central-operator/export-1-a.png)

1. Elindul az exportálási folyamat. Az állapotot az eszköz működési paneljén követheti nyomon.

1. Ha az Exportálás befejeződik, megjelenik egy üzenet, amely a generált fájl letöltésére szolgáló hivatkozással együtt jelenik meg.

1. A fájl **letöltése** hivatkozásra kattintva letöltheti a fájlt a lemez helyi mappájába.

    ![Az Exportálás sikerességét bemutató képernyőkép](./media/overview-iot-central-operator/export-2-a.png)

1. Az exportált CSV-fájl a következő oszlopokat tartalmazza: eszköz azonosítója, eszköznév, eszköz kulcsa és X509 tanúsítvány ujjlenyomatai megfelelnek:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

További információ a kapcsolati karakterláncokról és a valódi eszközök IoT Central alkalmazáshoz való csatlakoztatásáról: [Device connectivity in Azure IoT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Eszköz törlése

Valós vagy szimulált eszköz törlése az Azure IoT Central alkalmazásból:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki a törölni kívánt eszköz eszközének sablonját.

1. Az eszközök szűréséhez és kereséséhez használja a szűrő eszközöket. Jelölje be a törölni kívánt eszközök melletti jelölőnégyzetet.

1. Válassza a **Törlés** elemet. A törlés állapotát az eszköz operatív paneljén követheti nyomon.

## <a name="change-a-property"></a>Tulajdonság módosítása

A felhő tulajdonságai az eszközhöz társított eszköz-metaadatok, például a város és a sorozatszám. A felhő tulajdonságai csak a IoT Central alkalmazásban léteznek, és nem szinkronizálhatók az eszközeivel. Az írható tulajdonságok vezérlik az eszköz viselkedését, és lehetővé teszik az eszköz állapotának távoli beállítását, például egy termosztátos eszköz megcélzott hőmérsékletének beállításával.  Az eszköz tulajdonságait az eszköz állítja be, és a IoT Centralon belül csak olvasható. Az eszköz **részletes** nézetében megtekintheti és frissítheti a tulajdonságokat.

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki annak az eszköznek a sablonját, amelynek tulajdonságait módosítani kívánja, majd válassza ki a kívánt eszközt.

1. Válassza ki azt a nézetet, amely az eszköz tulajdonságait tartalmazza, ez a nézet lehetővé teszi az értékek bevitelét és a **Mentés** lehetőséget a lap tetején. Itt láthatja az eszköz tulajdonságait és aktuális értékeit. A felhő tulajdonságai és az írható tulajdonságok szerkeszthető mezőkkel rendelkeznek, az eszköz tulajdonságai pedig csak olvashatók. Az írható tulajdonságok esetében a mező alján láthatja a szinkronizálási állapotukat. 

1. Módosítsa a tulajdonságokat a szükséges értékekre. Egyszerre több tulajdonságot is módosíthat, és egyszerre frissítheti őket.

1. Válassza a **Mentés** elemet. Ha az írható tulajdonságokat mentette, az értékeket a rendszer elküldi az eszközre. Amikor az eszköz megerősíti az írható tulajdonság módosítását, az állapot visszakerül a **szinkronizált** értékre. Ha mentett egy Felhőbeli tulajdonságot, az érték frissül.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan kezelheti az eszközöket az Azure IoT Central alkalmazásban, a javasolt következő lépés az, hogy megtudja, hogyan [konfigurálhatja az eszközök szabályait](howto-configure-rules.md) .