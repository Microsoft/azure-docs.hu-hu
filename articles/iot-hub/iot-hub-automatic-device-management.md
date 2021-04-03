---
title: Automatikus eszközkezelés az Azure IoT Hub-vel | Microsoft Docs
description: Az Azure IoT Hub automatikus konfigurációinak használata több IoT-eszköz és-modul felügyeletéhez
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 0e017f4df413d6db528bb99756646859d9a74aea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545395"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>IoT-eszközök és -modulok automatikus felügyelete az Azure Portal használatával

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Az Azure-IoT Hub automatikus eszközkezelés számos olyan ismétlődő és összetett feladatot automatizál, amely nagy méretű eszköz-flották felügyeletét végzi. Az automatikus eszközkezelés lehetővé teszi, hogy a tulajdonságok alapján csoportosítsa az eszközöket, Definiáljon egy kívánt konfigurációt, majd hagyja IoT Hub frissíteni az eszközöket, amikor azok hatókörbe kerülnek. Ezt a frissítést _automatikus eszköz-konfigurációval_ vagy _automatikus modul-konfigurációval_ végezheti el, így összefoglalhatja a befejezést és a megfelelőséget, kezelheti az egyesítést és az ütközéseket, valamint a konfigurációkat egy szakaszos megközelítésben.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Az automatikus eszközkezelés úgy működik, hogy az ikrek vagy a modul-modulok egy készletét frissíti a kívánt tulajdonságokkal, és összefoglalja a két jelentett tulajdonságon alapuló összegzést.  Új osztályt és JSON-dokumentumot vezet be, *amelynek neve* három részből áll:

* A **célként megadott feltétel** az ikrek vagy a modul-modulok frissítésének hatókörét határozza meg. A célként megadott feltétel két címkén és/vagy jelentett tulajdonságon alapuló lekérdezésként van megadva.

* A **célként megadott tartalom** határozza meg a kívánt tulajdonságokat, amelyeket hozzá kell adni, vagy frissíteni kell a megcélzott eszköz ikrek vagy moduljában. A tartalom a módosítani kívánt tulajdonságok szakaszának elérési útját tartalmazza.

* A **metrikák** határozzák meg a különböző konfigurációs állapotok, például a **sikeres**, **folyamatban lévő** és **hiba** összegzésének számát. Egyéni metrikák vannak megadva lekérdezésként a Twin jelentett tulajdonságainál.  A rendszermetrikák az alapértelmezett mérőszámok, amelyek a kettős frissítési állapotot mérik, például a megcélozott ikrek számát és a sikeresen frissített ikrek számát.

Az automatikus konfigurációk az első alkalommal futnak a konfiguráció létrehozása után, majd öt percenként. A metrikák lekérdezései minden alkalommal futnak, amikor az automatikus konfiguráció fut.

## <a name="implement-twins"></a>Az ikrek megvalósítása

Az eszközök automatikus konfigurálásához az eszközökön az ikrek szinkronizálása szükséges a felhő és az eszközök között.  További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](iot-hub-devguide-device-twins.md).

Az automatikus modul-konfigurációkhoz az ikrek modul használatával kell szinkronizálni a felhő és a modulok közötti állapotot. További információ: az [ikrek megismerése és használata IoT Hubban](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Címkék használata az ikrek célzásához

A konfiguráció létrehozása előtt meg kell adnia, hogy mely eszközöket vagy modulokat kívánja érinteni. Az Azure IoT Hub azonosítja az eszközöket, és címkék használatával azonosítja az eszközt a Twin-ben, és a modulban található címkék használatával azonosítja a modulokat. Minden eszköz vagy modul több címkével is rendelkezhet, és bármilyen módon meghatározhatja a megoldását. Ha például különböző helyen felügyeli az eszközöket, adja hozzá a következő címkéket egy eszközhöz a Twin:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

1. A [Azure Portal](https://portal.azure.com)nyissa meg az IoT hubot. 

2. Válassza az **IoT** lehetőséget.

3. Válassza az **eszköz konfigurációjának hozzáadása** vagy a **modul konfigurációjának hozzáadása** lehetőséget.

   ![Eszköz konfigurációjának vagy modul-konfigurációjának hozzáadása](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

A konfiguráció létrehozásához öt lépés szükséges. A következő szakasz végigvezeti a műveletet. 

### <a name="name-and-label"></a>Név és címke

1. Adjon egy egyedi nevet a konfigurációnak, amely akár 128 kisbetűt is tartalmazhat. Kerülje a szóközöket, és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /` .

2. Címkék hozzáadása a konfigurációk nyomon követése érdekében. A címkék **név**, **érték** párok, amelyek leírják a konfigurációt. Például `HostPlatform, Linux` vagy `Version, 3.0.1`.

3. A **tovább** gombra kattintva lépjen a következő lépésre. 

### <a name="specify-settings"></a>Beállítások megadása

Ez a szakasz azt a tartalmat határozza meg, amelyet meg kell adni a célként megadott eszköz vagy modul ikrekben. Az egyes beállításokhoz két bemenet van megadva. Az első az a kettős elérési út, amely a megadott Twin kívánt tulajdonságok között a JSON szakasz elérési útja.  A második a szakaszba beszúrandó JSON-tartalom. 

Megadhatja például a kettős elérési utat, `properties.desired.chiller-water` majd megadhatja a következő JSON-tartalmat: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![A kettős elérési út és a tartalom beállítása](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Az egyes beállításokat megadhatja úgy is, hogy megadja a teljes dupla elérési utat, és a szögletes zárójelek nélkül biztosítja az értéket. Például a kettős elérési úttal `properties.desired.chiller-water.temperature` állítsa be a tartalmat a következőre: `66` . Ezután hozzon létre egy új, dupla beállítást a Pressure tulajdonsághoz. 

Ha két vagy több konfiguráció ugyanazt a különálló elérési utat célozza meg, akkor a legmagasabb prioritású konfigurációból származó tartalom lesz érvényes (a 4. lépésben megadott prioritást kell megadni).

Ha el szeretne távolítani egy meglévő tulajdonságot, a tulajdonság értékét állítsa be `null` .

További beállításokat adhat hozzá, ha az **eszköz hozzáadása** vagy a **modul különálló** beállítása lehetőséget választja.

### <a name="specify-metrics-optional"></a>Metrikák megadása (nem kötelező)

A metrikák a konfigurációs tartalom alkalmazása után egy eszköz vagy modul által visszajelentett különböző állapotok összesítő számát tartalmazzák. Létrehozhat például egy mérőszámot a függőben lévő beállítások változásaihoz, egy mérőszámot a hibákhoz, valamint egy mérőszámot a sikeres beállítások módosításához.

Mindegyik konfiguráció legfeljebb öt egyéni metrikával rendelkezhet. 

1. Adja meg a **metrika nevének** nevét.

2. Adja meg a **mérőszám feltételeinek** lekérdezését.  A lekérdezés az eszköz Twin jelentett tulajdonságain alapul.  A metrika a lekérdezés által visszaadott sorok számát jelöli.

Például:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Felvehet egy záradékot, amelyet a konfiguráció alkalmaz, például: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Ha mérőszámot épít be a konfigurált modulok jelentésére, válassza `moduleId` a from elemet `devices.modules` . Például:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Céleszközök

Az ikrek címkék tulajdonságával megcélozhatja azokat az eszközöket vagy modulokat, amelyeknek meg kell kapniuk ezt a konfigurációt. A Twin jelentett tulajdonságokat is megcélozhatja.

Az automatikus eszköz-konfigurációk csak az eszköz kettős címkéit célozzák meg, az automatikus modul-konfigurációk pedig csak a modul Twin címkéit tudják megcélozni. 

Mivel több konfiguráció is megcélozhatja ugyanazt az eszközt vagy modult, minden konfigurációnak prioritási számra van szüksége. Ütközés esetén a legmagasabb prioritású WINS-konfigurációt. 

1. Adjon meg egy pozitív egész számot a konfigurációs **prioritáshoz**. A legmagasabb numerikus érték a legmagasabb prioritást veszi figyelembe. Ha két konfigurációban ugyanaz a prioritási szám, akkor a legutóbb létrehozott WINS. 

2. Adja meg a **cél feltételt** annak meghatározásához, hogy mely eszközök vagy modulok lesznek megcélozva ezzel a konfigurációval. A feltétel a kettős címkék vagy a Twin jelentett tulajdonságok alapján történik, és egyeznie kell a kifejezés formátumával. 

   Az eszközök automatikus konfigurálásához csak a címkét vagy a jelentett tulajdonságot adhatja meg célként. Például `tags.environment='test'` vagy `properties.reported.chillerProperties.model='4000x'`. Megadhatja `*` az összes eszköz célzását. 
   
   Az automatikus modul konfigurálásához használjon lekérdezést az IoT hub-ban regisztrált modulok címkéi vagy jelentett tulajdonságainak megadásához. Például `from devices.modules where tags.environment='test'` vagy `from devices.modules where properties.reported.chillerProperties.model='4000x'`. A helyettesítő karakter nem használható az összes modul megcélzására. 

3. A **tovább** gombra kattintva lépjen be az utolsó lépésbe.

### <a name="review-configuration"></a>Konfiguráció áttekintése

Tekintse át a konfigurációs információkat, majd kattintson a **Submit (Küldés**) gombra.

## <a name="monitor-a-configuration"></a>Konfiguráció figyelése

A konfiguráció részleteinek megtekintéséhez és az azt futtató eszközök figyeléséhez kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)nyissa meg az IoT hubot. 

2. Válassza az **IoT** lehetőséget.

3. Ellenőrizze a konfigurációs listát. Az egyes konfigurációk esetében az alábbi adatokat tekintheti meg:

   * **Azonosító** – a konfiguráció neve.

   * **Cél feltétel** – a megcélzott eszközök vagy modulok definiálásához használt lekérdezés.

   * **Prioritás** – a konfigurációhoz rendelt prioritási szám.

   * **Létrehozás időpontja** – a konfiguráció létrehozásának időbélyegzője. Ez az időbélyeg a kapcsolatok megszakítására szolgál, ha két konfiguráció azonos prioritással rendelkezik. 

   * **Rendszermetrikák** – IoT hub alapján kiszámított metrikák, amelyeket a fejlesztők nem tudnak testreszabni. A célként megadott érték határozza meg a célként megadott feltételnek megfelelő eszközök ikrek számát. A konfiguráció által módosított eszköz-ikrek számát adja meg, amely tartalmazhat részleges módosításokat abban az esetben, ha egy különálló, magasabb prioritású konfiguráció módosításokat is végez. 

   * **Egyéni metrikák** – a fejlesztő által a kettős jelentett tulajdonságok alapján lekérdezéssel megadott metrikák.  Egy konfigurációban legfeljebb öt egyéni metrika definiálható. 
   
4. Válassza ki a figyelni kívánt konfigurációt.  

5. Ellenőrizze a konfiguráció részleteit. A lapok használatával megtekintheti a konfigurációt fogadó eszközök részletes adatait.

   * **Cél feltétel** – a célként megadott feltételnek megfelelő eszközök vagy modulok. 

   * **Metrikák** – a rendszermetrikák és az egyéni metrikák listája.  Megtekintheti az egyes mérőszámokhoz tartozó eszközök vagy modulok listáját, ha kiválasztja a metrikát a legördülő menüből, majd kiválasztja az **eszközök megtekintése** vagy a **modulok megtekintése** lehetőséget.

   * **Device Twin beállítások** vagy **modul Twin beállítások** – a konfiguráció által beállított Twin beállítások. 

   * **Konfigurációs címkék** – a konfiguráció leírásához használt kulcs-érték párok.  A címkék nem befolyásolják a funkcionalitást. 

## <a name="modify-a-configuration"></a>Konfiguráció módosítása

Konfiguráció módosításakor a módosítások azonnal replikálódnak az összes megadott eszközre vagy modulra. 

Ha frissíti a célként megadott feltételt, a következő frissítések történnek:

* Ha egy iker nem felelt meg a régi cél feltételnek, de megfelel az új célként megadott feltételnek, és ez a konfiguráció az adott Twin legmagasabb prioritása, akkor ezt a konfigurációt alkalmazza a rendszer. 

* Ha a jelenlegi konfigurációt jelenleg már nem teljesíti a megcélzott feltételnek, a rendszer eltávolítja a konfiguráció beállításait, és a Twin érték a következő legmagasabb prioritású konfigurációval lesz módosítva. 

* Ha a jelenlegi konfiguráció jelenleg nem felel meg a megcélzott feltételnek, és nem teljesíti a többi konfiguráció célját, akkor a konfiguráció beállításai el lesznek távolítva, és a különálló módosítások nem lesznek elérhetők. 

A konfiguráció módosításához kövesse az alábbi lépéseket: 

1. A [Azure Portal](https://portal.azure.com)nyissa meg az IoT hubot. 

2. Válassza az **IoT** lehetőséget. 

3. Válassza ki a módosítani kívánt konfigurációt. 

4. Frissítse a következő mezőket: 

   * Cél feltétel 
   * Címkék 
   * Prioritás 
   * Mérőszámok

4. Kattintson a **Mentés** gombra.

5. Kövesse a [konfiguráció figyelése](#monitor-a-configuration) című szakasz lépéseit a változások megtekintéséhez. 

## <a name="delete-a-configuration"></a>Konfiguráció törlése

Ha töröl egy konfigurációt, minden eszköz-ikrek a következő legmagasabb prioritású konfigurációt veszi igénybe. Ha az eszközök nem teljesítik a többi konfiguráció célját, akkor a rendszer nem alkalmazza a többi beállítást. 

1. A [Azure Portal](https://portal.azure.com)nyissa meg az IoT hubot. 

2. Válassza az **IoT** lehetőséget. 

3. A jelölőnégyzet használatával válassza ki a törölni kívánt konfigurációt. 

4. Válassza a **Törlés** elemet.

5. A rendszer kérni fogja a megerősítést.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja és figyelheti a IoT-eszközök méretét. Az alábbi hivatkozásokat követve további információkat tudhat meg az Azure IoT Hub kezeléséről:

* [IoT Hub-eszközidentitások csoportos kezelése](iot-hub-bulk-identity-mgmt.md)
* [Az IoT hub figyelése](monitor-iot-hub.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)

Ha szeretné megtekinteni a IoT Hub Device Provisioning Service használatát a nulla érintéses, igény szerinti kiépítés engedélyezéséhez, olvassa el a következő témakört: 

* [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml)