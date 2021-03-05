---
title: Az Azure FarmBeats hibaelhárítása
description: Ez a cikk az Azure-FarmBeats hibaelhárítását ismerteti.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: c45b6196b82682b37e253a33eed3940b68b4d61e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172984"
---
# <a name="troubleshoot-azure-farmbeats"></a>Az Azure FarmBeats hibaelhárítása

Ez a cikk az Azure FarmBeats kapcsolatos gyakori problémák megoldásait ismerteti. További segítségért forduljon a [Q&támogatási fórumhoz](/answers/topics/azure-farmbeats.html) , vagy küldjön nekünk e-mailt a következő címen: farmbeatssupport@microsoft.com .

> [!NOTE]
  > Ha áprilisban telepítette a FarmBeats, és a feladatok nem üres hibaüzenettel rendelkeznek, előfordulhat, hogy a telepítéshez nem rendeltek le batch-kvótát a kritikus állapotú és biztonsági szervezetek támogatásának rangsorolásához. További információt [itt](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) talál. A feladatok sikeres futtatásához le kell kérnie a virtuális gépek lefoglalását a Batch-fiókba.

## <a name="install-issues"></a>Telepítési problémák

  > [!NOTE]
  > Ha hiba miatt újraindítja a telepítést, győződjön meg róla, hogy törli az **erőforráscsoportot** , vagy törölje az összes erőforrást az erőforráscsoporthoz, mielőtt újra elindítja a telepítést.

### <a name="invalid-sentinel-credentials"></a>A Sentinel hitelesítő adatai érvénytelenek

A telepítés során megadott Sentinel hitelesítő adatok helytelenek. Indítsa újra a telepítést a megfelelő hitelesítő adatokkal.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Elérte a megadott előfizetéshez tartozó Batch-fiókok tartományi fiókra vonatkozó kvótáját

Növelje a kvótát, vagy törölje a nem használt batch-fiókokat, és indítsa újra a telepítést.

### <a name="invalid-resource-group-location"></a>Érvénytelen erőforráscsoport-hely

Győződjön meg arról, hogy az **erőforráscsoport** a telepítés során megadott **régióval** megegyező helyen található.

### <a name="other-install-issues"></a>Egyéb telepítési problémák

Vegye fel velünk a kapcsolatot az alábbi adatokkal:

- Az előfizetés azonosítója
- Erőforráscsoport neve
- A következő lépésekkel csatolja a naplófájlt a központi telepítési hibához:

    1. Navigáljon az **erőforráscsoporthoz** a Azure Portal.

    2. Válassza a **központi telepítések** lehetőséget a bal oldali **Beállítások** szakaszban.

    3. Minden olyan központi telepítésnél, amely **nem sikerült**, válassza ki a lehetőséget, majd töltse le az üzembe helyezés részleteit. Csatolja ezt a fájlt az e-mailhez.

## <a name="sensor-telemetry"></a>Érzékelő telemetria

### <a name="cant-view-telemetry-data"></a>Nem lehet megtekinteni a telemetria-fájlokat

**Tünet**: az eszközök vagy érzékelők üzembe helyezése megtörténik, és a FarmBeats-t csatlakoztatta az eszköz partneréhez, de nem tudja lekérni vagy megtekinteni a telemetria adatait a FarmBeats.

**Javító művelet**

1. Nyissa meg a FarmBeats erőforráscsoportot.
2. Válassza ki az **Event hub** -névteret ("érzékelő-partner-eh-Namespace-xxxx"), kattintson a "Event Hubs" elemre, majd keresse meg a partnerhez rendelt Event hub bejövő üzeneteinek számát.
3. A következő lehetőségek közül választhat:

   - Ha nincsenek *beérkező üzenetek*, forduljon az eszköz partneréhez.  
   - Ha vannak *Bejövő üzenetek*, vegye fel velünk a kapcsolatot a Datahub és a Gyorssegéd-naplókkal, valamint a rögzített telemetria.

A naplók letöltésének megismeréséhez lépjen a ["naplók manuális gyűjtése"](#collect-logs-manually) szakaszra.  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nem lehet megtekinteni a telemetria adatait az érzékelőkből származó múltbeli/adatfolyam-adatok betöltése után

**Tünet**: az eszközök vagy érzékelők üzembe helyezése megtörténik, és létrehozta az eszközöket/érzékelőket a FarmBeats-on és a betöltött telemetria a EventHub, de nem tudja lekérdezni vagy megtekinteni a telemetria-adatot a FarmBeats.

**Javító művelet**

1. Győződjön meg arról, hogy helyesen végrehajtotta a partner regisztrációját – ezt megteheti, ha a datahub henceg, a/partner API-ra navigálva elvégezheti a lekérést, és ellenőrizheti, hogy a partner regisztrálva van-e. Ha nem, kövesse az alábbi [lépéseket](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) a partner hozzáadásához.

2. Győződjön meg arról, hogy a megfelelő telemetria-üzenet formátumát használta:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Nem rendelkezik az Azure Event Hubs-beli kapcsolatok karakterláncával

**Javító művelet**

1. A Datahub hencegés területen lépjen a partner API-ra.
2. Válassza a **Letöltés**  >  **kipróbálható**  >  **végrehajtás** lehetőséget.

> [!NOTE]
> Annak az érzékelő-partnernek a partner-azonosítója, amelyre kíváncsi.

3. Lépjen vissza a partner API-ra, és válassza a **beolvasás/ \<ID>** lehetőséget.
4. Adja meg a partner AZONOSÍTÓját a 3. lépésben, majd válassza a **végrehajtás** lehetőséget.

   Az API-válasznak tartalmaznia kell a Event Hubs-kapcsolatok karakterláncát.

### <a name="device-appears-offline"></a>Az eszköz offline állapotban jelenik meg

**Tünetek**: az eszközök telepítve vannak, és a FarmBeats az eszköz partneréhez csatolták. Az eszközök online állapotba kerülnek, és telemetria adatokat küldenek, de offline állapotban jelennek meg.

**Javító művelet** A jelentéskészítési időköz nincs konfigurálva ehhez az eszközhöz. A jelentéskészítési időköz beállításához forduljon az eszköz gyártójához. 

### <a name="error-deleting-a-device"></a>Hiba történt az eszköz törlésekor

Egy eszköz törlésekor a következő gyakori hibák valamelyike merülhet fel:  

**Üzenet**: "az eszköz az érzékelőkre van hivatkozva: egy vagy több érzékelő van társítva az eszközhöz. Törölje az érzékelőket, majd törölje az eszközt. "  

**Jelentés**: az eszköz több, a farmban üzembe helyezett érzékelőhöz van társítva.

**Javító művelet**  

1. Törölje az eszközhöz a Gyorssegéden keresztül társított érzékelőket.  
2. Ha a szenzorokat egy másik eszközhöz szeretné hozzárendelni, kérje meg az eszköz partnerét, hogy tegye ugyanezt.  
3. Törölje az eszközt `DELETE API` hívással, és állítsa a Force paramétert *igaz* értékre.  

**Üzenet**: "az eszköz az eszközökön ParentDeviceId van hivatkozva: egy vagy több eszköz van társítva az eszközhöz alárendelt eszközként. Törölje, majd törölje az eszközt. "  

**Jelentés**: az eszközhöz más eszközök tartoznak.  

**Javító művelet**

1. Törölje az adott eszközhöz társított eszközöket.  
2. Törölje az adott eszközt.  

    > [!NOTE]
    > Az eszköz nem törölhető, ha az érzékelők társítva vannak hozzá. A kapcsolódó érzékelők törlésével kapcsolatos további információkért tekintse meg az érzékelők [adatainak beolvasása az érzékelő partnereinktől](get-sensor-data-from-sensor-partner.md)című szakaszt az érzékelő **törlése** című szakaszban.
    > A partnerek nem jogosultak az eszköz vagy az érzékelő törlésére. Csak rendszergazdák jogosultak a törlésre.

## <a name="issues-with-jobs"></a>Problémák a feladatokkal

### <a name="farmbeats-internal-error"></a>FarmBeats belső hiba

**Üzenet**: "FarmBeats belső hiba", további részletekért lásd a hibaelhárítási útmutatót. "

**Javító művelet** A probléma oka az, hogy az adatfolyamatban ideiglenes Hiba történt. Hozza létre újra a feladatot. Ha a hiba továbbra is fennáll, lépjen kapcsolatba velünk a hibaüzenettel/naplókkal.

## <a name="accelerator-troubleshooting"></a>Gyorssegéd hibaelhárítása

### <a name="access-control"></a>Hozzáférés-vezérlés

**Probléma**: a szerepkör-hozzárendelés hozzáadásakor hibaüzenetet kap.

**Üzenet**: "nem található egyező felhasználó."

**Javító művelet** Tekintse meg azt az e-mail-azonosítót, amelyhez szerepkör-hozzárendelést próbál hozzáadni. Az e-mail-AZONOSÍTÓnak pontosan egyeznie kell az AZONOSÍTÓval, amely regisztrálva van az adott felhasználó számára a Active Directoryban. Ha a hiba továbbra is fennáll, lépjen kapcsolatba velünk a hibaüzenettel/naplókkal.

### <a name="unable-to-log-in-to-accelerator"></a>Nem lehet bejelentkezni a Gyorssegédbe

**Üzenet**: "hiba: Ön nem jogosult a szolgáltatás meghívására. Az engedélyezéshez forduljon a rendszergazdához. "

**Javító művelet** Kérje meg a rendszergazdát, hogy engedélyezze a FarmBeats-telepítés elérését. Ezt a RoleAssignment API-k KÖZZÉTÉTELével vagy a Gyorssegéd **Beállítások** paneljének Access Control keresztül végezheti el.  

Ha már engedélyezte a hozzáférést, és ezzel a hibával szembesül, próbálkozzon újra az oldal frissítésével. Ha a hiba továbbra is fennáll, lépjen kapcsolatba velünk a hibaüzenettel/naplókkal.

![Az engedélyezési hibát bemutató képernyőkép.](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Gyorsító problémák  

**Probléma**: hiba történt a meghatározatlan ok miatt.

**Üzenet**: "hiba: ismeretlen hiba történt."

**Javító művelet** Ez a hiba akkor fordul elő, ha túl sokáig hagyja tétlenül a lapot. Frissítse az oldalt. Ha a hiba továbbra is fennáll, lépjen kapcsolatba velünk a hibaüzenettel/naplókkal.

**Probléma**: a FarmBeats-gyorsító nem jeleníti meg a legújabb verziót még a FarmBeatsDeployment frissítése után sem.

**Javító művelet** Ez a hiba a szolgáltatás munkavégző általi megőrzésének a böngészőben való megőrzése miatt fordul elő. Tegye a következőket:

1. Zárjunk be minden olyan böngésző fület, amelyen a Gyorssegéd meg van nyitva, és zárjuk be a böngészőablakot.
2. Indítsa el a böngésző új példányát, és töltse be újra a Gyorssegéd URI-JÁT. Ez a művelet betölti a Gyorssegéd új verzióját.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: rendszerképekkel kapcsolatos problémák

### <a name="wrong-username-or-password"></a>Helytelen Felhasználónév vagy jelszó

**Sikertelen feladatok üzenete**: "a teljes hitelesítés szükséges az erőforrás eléréséhez."

**Javító művelet**: tegye a következők egyikét:

- Frissítse a FarmBeats a megfelelő felhasználónévvel/jelszóval az alábbi lépésekkel, majd próbálja megismételni a feladatot.

  **Sentinel-Felhasználónév frissítése**

    1. Jelentkezzen be [Azure Portalba](https://portal.azure.com).
    2. A **keresőmezőbe** keresse meg a FarmBeats Datahub erőforráscsoportot.
    3. Válassza a Storage-fiók Storage * * * * *** > tárolók**  >  **Batch-PREP-Files**  >  **to_vm**  >  **config.ini**
    4. **Szerkesztés** kiválasztása
    5. A Felhasználónév frissítése a sentinel_account szakaszban

  **A Sentinel jelszavának frissítése**

    1. Jelentkezzen be [Azure Portalba](https://portal.azure.com).
    2. A **keresőmezőbe** keresse meg a FarmBeats Datahub erőforráscsoportot.
    3. Kulcstartó kiválasztása – * * * * *
    4. Hozzáférési szabályzatok kiválasztása a beállítások területen
    5. Válassza a **hozzáférési házirend hozzáadása** lehetőséget.
    6. A regisztráció sablon alapján történő konfigurálásához használja a **titkos kezelést** , és adja hozzá magát a résztvevőhöz
    7. Válassza a **Hozzáadás** lehetőséget, majd a **hozzáférési házirendek** lapon válassza a **Mentés** lehetőséget.
    8. A **Beállítások** területen válassza a **titkok** elemet.
    9. A **Sentinel-Password** kiválasztása
    10. Hozza létre az érték új verzióját, és engedélyezze azt.

- Futtassa újra a sikertelen feladatot, vagy futtassa a Satellite indexek feladatot egy 5 – 7 napos dátumtartomány esetében, majd ellenőrizze, hogy a művelet sikeres-e.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel hub: helytelen URL vagy hely nem érhető el

**Sikertelen feladatok üzenete**: "Hoppá, hiba történt. Az elérni próbált lap (átmenetileg) nem érhető el. "

**Javítási művelet**:

1. Nyissa meg a [Sentinel](https://scihub.copernicus.eu/dhus/) alkalmazást a böngészőben, és ellenőrizze, hogy a webhely elérhető-e.
2. Ha a webhely nem érhető el, ellenőrizze, hogy a tűzfal, a vállalati hálózat vagy más blokkoló szoftver megakadályozza-e a hozzáférést a webhelyhez, majd hajtsa végre a szükséges lépéseket a Sentinel URL-címének engedélyezéséhez. 
3. Futtassa újra a sikertelen feladatot, vagy futtassa a Satellite indexek feladatot egy 5 – 7 napos dátumtartomány esetében, majd győződjön meg arról, hogy a művelet sikeres-e.  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel-kiszolgáló: le karbantartásra

**Sikertelen feladatok üzenete**: "a Kopernikusz Open Access hub hamarosan vissza fog térni! Sajnos a kellemetlenségért elnézést végzünk. Hamarosan ismét elérhető lesz! " 

**Javítási művelet**:

Ez a probléma akkor fordulhat elő, ha a Sentinel-kiszolgálón bármilyen karbantartási tevékenység történik.

1. Ha bármilyen feladatra vagy folyamatra nem kerül sor, mert a karbantartás folyamatban van, egy kis idő elteltével küldje el újra a feladatot. 

   A tervezett vagy nem tervezett Sentinel karbantartási tevékenységekkel kapcsolatos információkért lépjen a [Kopernikusz Open Access hub Hírek](https://scihub.copernicus.eu/news/) webhelyére.  

2. Futtassa újra a sikertelen feladatot, vagy futtassa a Satellite indexek feladatot egy 5 – 7 napos dátumtartomány esetében, majd győződjön meg arról, hogy a művelet sikeres-e.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: elérte a kapcsolatok maximális számát

**Sikertelen feladatok üzenete**: "a felhasználó által elért két egyidejű folyamat maximális száma" \<username> .

**Jelentés**: Ha egy feladat meghiúsul, mert a kapcsolatok maximális száma elérte a kapcsolatot, a rendszer ugyanazt a Sentinel-fiókot használja több feladatban.

**Javítási művelet**: próbálkozzon a következők valamelyikével:

* Várjon, amíg a többi feladat befejeződik a sikertelen feladat újbóli futtatása előtt.
* Hozzon létre egy új Sentinel-fiókot, majd frissítse a Sentinel felhasználónevét és jelszavát a FarmBeats-ben.

### <a name="sentinel-server-refused-connection"></a>Sentinel-kiszolgáló: visszautasított kapcsolatok

**Sikertelen feladatok üzenete**: "a kiszolgáló visszautasította a következőt: http://172.30.175.69:8983/solr/dhus ."

**Javító művelet**: Ez a probléma akkor fordulhat elő, ha a Sentinel-kiszolgálón bármilyen karbantartási tevékenység történik.

1. Ha bármilyen feladatra vagy folyamatra nem kerül sor, mert a karbantartás folyamatban van, egy kis idő elteltével küldje el újra a feladatot.

   A tervezett vagy nem tervezett Sentinel karbantartási tevékenységekkel kapcsolatos információkért lépjen a [Kopernikusz Open Access hub Hírek](https://scihub.copernicus.eu/news/) webhelyére.  

2. Futtassa újra a sikertelen feladatot, vagy futtassa a Satellite indexek feladatot egy 5 – 7 napos dátumtartomány esetében, majd győződjön meg arról, hogy a művelet sikeres-e.

### <a name="soil-moisture-map-has-white-areas"></a>A talaj nedvességtartalmának térképe fehér terület

**Probléma**: a rendszer létrehozta a **talaj nedvességtartalmának leképezését** , de a Térkép többnyire fehér területtel rendelkezik.

**Javító művelet**: Ez a probléma akkor fordulhat elő, ha a leképezés kérésének idejére létrehozott szatellit indexek NDVI értéke kisebb, mint 0,3. További információért látogasson el [a Sentinel technikai útmutatóját](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Futtassa újra a feladatot egy másik dátumtartomány esetében, és ellenőrizze, hogy a NDVI értékei a szatellit indexekben nagyobbak-e, mint 0,3.

## <a name="collect-logs-manually"></a>Naplók manuális gyűjtése

[Azure Storage Explorer telepítése és üzembe helyezése]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Azure Data Factory-feladatokhoz tartozó naplók vagy App Service naplók gyűjtése a Datahub-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A **keresőmezőbe** keresse meg a FarmBeats Datahub erőforráscsoportot.
3. Az **erőforráscsoport** irányítópultján keresse meg a *datahublogs \** Storage-fiókot. Például: *datahublogsmvxmq*.  
4. A **Name (név** ) oszlopban válassza ki a Storage-fiókot a **Storage-fiók** irányítópultjának megtekintéséhez.
5. Az **open Azure Storage Explorer** alkalmazás megtekintéséhez az **datahubblogs \* *_ panelen válassza az _* Megnyitás az Explorerben lehetőséget** .
6. A bal oldali panelen válassza a **blob-tárolók** lehetőséget, majd válassza ki a Azure Data Factory naplók vagy **appinsights** app Service naplókhoz tartozó **feladatokhoz** tartozó naplókat.
7. Válassza a **Letöltés** lehetőséget, majd töltse le a naplókat a számítógép egy helyi mappájába.

    ![A letöltött naplófájlokat megjelenítő képernyőkép.](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Azure Data Factory a feladatokhoz tartozó naplók vagy App Service-naplók gyűjtése a Gyorssegédhez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A **keresőmezőbe** keresse meg a FarmBeats-gyorsító erőforráscsoportot.
3. Az **erőforráscsoport** irányítópultján keressen rá a *Storage \** Storage-fiókra. Például: *storagedop4k \**.
4. A **Storage-fiók** irányítópultjának megtekintéséhez válassza ki a Storage-fiókot a **Name (név** ) oszlopban.
5. A **tároló \* *_ ablaktábláján válassza az _* Megnyitás az Explorerben lehetőséget** a Azure Storage Explorer alkalmazás megnyitásához.
6. A bal oldali panelen válassza a **blob-tárolók** lehetőséget, majd válassza ki a Azure Data Factory naplók vagy **appinsights** app Service naplókhoz tartozó **feladatokhoz** tartozó naplókat.
7. Válassza a **Letöltés** lehetőséget, majd töltse le a naplókat a számítógép egy helyi mappájába.

## <a name="high-cpu-usage"></a>Magas processzorhasználat

**Hiba**: egy e-mail-riasztást kap, amely **nagy CPU-használati riasztásra** hivatkozik.

**Javítási művelet**:

1. Lépjen a FarmBeats Datahub-erőforráscsoporthoz.
2. Válassza ki az **app Service**-t.  
3. Lépjen a vertikális felskálázás [app Service díjszabása lapra](https://azure.microsoft.com/pricing/details/app-service/windows/), és válassza ki a megfelelő árképzési szintet.

## <a name="weather-data-job-failures"></a>Az időjárási adatokkal kapcsolatos sikertelen feladatok

**Hiba**: az időjárási adatok beolvasására, de a feladat meghibásodására vonatkozó feladatok futtatása

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>Naplók gyűjtése az időjárási adatfeldolgozási feladatok hibáinak elhárításához

1. Nyissa meg a FarmBeats erőforráscsoportot a Azure Portal.
2. Kattintson az erőforráscsoport részét képező Data Factory szolgáltatásra. A szolgáltatás "SKU: Datahub" címkével fog rendelkezni.

> [!NOTE]
> Ha meg szeretné tekinteni a szolgáltatások címkéit az erőforráscsoporthoz, kattintson az "Oszlopok szerkesztése" elemre, és adja hozzá a "címkék" kifejezést az erőforráscsoport nézethez.

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="A SKU: Datahub címkét kiemelő képernyőkép.":::

3. Az adatfeldolgozó áttekintés lapján kattintson a **Szerző és figyelő** elemre. Megnyílik egy új lap a böngészőben. Kattintson a **figyelés** elemre

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="Képernyőfelvétel: a figyelő menüpont kiemelése.":::

4. Ekkor megjelenik az időjárási feladatok végrehajtásának részét képező folyamat-futtatások listája. Kattintson arra a feladatokra, amelyhez naplókat szeretne gyűjteni
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="Képernyőfelvétel: a folyamat futási menüjének és a kijelölt feladatoknak a kiemelése.":::

5. A folyamat áttekintése lapon láthatja a tevékenységek futtatásának listáját. Jegyezze fel azoknak a tevékenységeknek a futtatási azonosítóit, amelyekhez naplókat szeretne gyűjteni
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="Képernyőfelvétel: a tevékenység-futtatások listájának megjelenítése.":::

6. Lépjen vissza az FarmBeats erőforráscsoporthoz a Azure Portalban, és kattintson a **datahublogs-XXXX** nevű Storage-fiókra.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="Képernyőfelvétel: a datahublogs-XXXX nevű Storage-fiók.":::

7. Kattintson a **tárolók**  ->  **adfjobs** elemre. A keresőmezőbe írja be a fenti 5. lépésben feljegyzett feladatok futtatási AZONOSÍTÓját.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="Projekt FarmBeats":::

8. A keresési eredmény tartalmazza azt a mappát, amely a feladatokhoz tartozó naplókat tartalmaz. Töltse le a naplókat, és küldje el, hogy farmbeatssupport@microsoft.com segítséget nyújtson a probléma hibakereséséhez.