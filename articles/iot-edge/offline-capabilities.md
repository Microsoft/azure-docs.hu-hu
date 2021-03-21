---
title: Eszközök offline működtetése – Azure IoT Edge | Microsoft Docs
description: Ismerje meg, hogy IoT Edge eszközök és modulok hogyan működhetnek az internetkapcsolat nélküli, hosszabb ideig, valamint azt, hogy a IoT Edge hogyan is képes a normál IoT-eszközök offline működtetésére.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c748034145781f639da244b16e3df7053da3d5d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489965"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>A IoT Edge eszközök, modulok és alárendelt eszközök kibővített offline képességeinek megismerése

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge támogatja a kibővített offline műveleteket a IoT Edge-eszközökön, és lehetővé teszi az offline műveletek használatát a nem IoT Edge alárendelt eszközökön is. Ha egy IoT Edge eszközhöz egyetlen lehetőség van a IoT Hubhoz való kapcsolódásra, az eszköz és az összes alárendelt eszköz továbbra is időnként vagy nem internetkapcsolattal működhet.

## <a name="how-it-works"></a>Működés

Amikor egy IoT Edge eszköz offline módba kerül, a IoT Edge hub három szerepkört vesz igénybe. Először tárolja az összes olyan üzenetet, amely feljebb kerülne, és az eszköz újracsatlakoztatása után menti azokat. Másodszor, az IoT Hub nevében eljárva hitelesíti a modulokat és a gyermekeszközöket, hogy azok továbbra is működhessenek. Harmadszor, lehetővé teszi a gyermekeszközök közötti kommunikációt, amely egyébként az IoT Hubon keresztül zajlana.

Az alábbi példa azt szemlélteti, hogyan működik egy IoT Edge-forgatókönyv kapcsolat nélküli módban:

1. **Eszközök konfigurálása**

   IoT Edge eszközök automatikusan offline képességekkel rendelkeznek. Ha ezt a képességet más IoT-eszközökre szeretné kiterjeszteni, a IoT Hub lévő eszközök között szülő-gyermek kapcsolatot kell deklarálnia. Ezt követően konfigurálja úgy a gyermek eszközöket, hogy megbízzanak a hozzárendelt fölérendelt eszközön, és irányítsa az eszközről a felhőbe irányuló kommunikációt a szülőn keresztül átjáróként.

2. **Szinkronizálás IoT Hub**

   Legalább egyszer a IoT Edge futtatókörnyezet telepítését követően a IoT Edge eszköznek online állapotban kell lennie a IoT Hubval való szinkronizáláshoz. Ebben a szinkronizálásban a IoT Edge eszköz beolvassa a hozzá rendelt összes alárendelt eszköz adatait. A IoT Edge eszköz emellett a helyi gyorsítótárat is biztonságosan frissíti az offline műveletek engedélyezéséhez és a telemetria-üzenetek helyi tárolásához szükséges beállítások lekéréséhez.

3. **Kapcsolat nélküli üzemmód**

   A IoT Hub, a IoT Edge eszköz, az üzembe helyezett modulok és az összes gyermek IoT-eszköz korlátlanul működhet. A modulok és a gyermek eszközök elindíthatók és újraindíthatók, ha kapcsolat nélküli üzemmódban végeznek hitelesítést az IoT Edge hub használatával. A telemetria kötött felfelé irányuló IoT Hub helyileg van tárolva. A modulok és a gyermek IoT közötti kommunikáció közvetlen metódusokkal vagy üzenetekkel tartható karban.

4. **Újrakapcsolás és Újraszinkronizálás IoT Hub**

   Miután visszaállította a IoT Hubt, a IoT Edge eszköz újra szinkronizál. A helyileg tárolt üzenetek a IoT Hub azonnal érkeznek, de a kapcsolat sebességétől, IoT Hub késéssel és a kapcsolódó tényezőktől függenek. Ezeket a rendszer ugyanabban a sorrendben továbbítja, amelyben tárolták őket.

   A modulok és az eszközök kívánt és jelentett tulajdonságai közötti különbségek egyeztetése megtörtént. A IoT Edge eszköz frissíti a hozzárendelt gyermek IoT-eszközökön végrehajtott összes változást.

## <a name="restrictions-and-limits"></a>Korlátozások és korlátozások

A jelen cikkben ismertetett kibővített offline képességek [IoT Edge 1.0.7 vagy újabb verzióban](https://github.com/Azure/azure-iotedge/releases)érhetők el. A korábbi verziók az offline funkciók egy részhalmazát alkotják. A bővített offline képességekkel nem rendelkező meglévő IoT Edge-eszközök nem frissíthetők a futtatókörnyezet verziójának módosításával, de a funkciók megszerzéséhez újra kell konfigurálni egy új IoT Edge-eszköz identitását.

Csak a nem IoT Edge eszközök adhatók hozzá alárendelt eszközökként.

Az eszközök és a hozzájuk rendelt alárendelt eszközök IoT Edge a kezdeti, egyszeri szinkronizálást követően a kapcsolat nélküli üzemmódban nem működnek. Az üzenetek tárolása azonban az élettartam (TTL) beállítástól és az üzenetek tárolására rendelkezésre álló lemezterülettől függ.

## <a name="set-up-parent-and-child-devices"></a>Szülő és gyermek eszközök beállítása

Ahhoz, hogy egy IoT Edge eszköz kiterjessze kibővített offline képességeit a gyermek IoT-eszközökre, két lépést kell elvégeznie. Először deklarálja a szülő-gyermek kapcsolatokat a Azure Portalban. Másodszor, hozzon létre egy megbízhatósági kapcsolatot a fölérendelt eszköz és az alárendelt eszközök között, majd konfigurálja az eszközről a felhőbe irányuló kommunikációt, hogy az a szülő átjáróként haladjon át.

### <a name="assign-child-devices"></a>Alárendelt eszközök kiosztása

A gyermek eszközök bármely nem IoT Edge eszköz, amely ugyanahhoz a IoT Hub van regisztrálva. A fölérendelt eszközök több alárendelt eszközzel is rendelkezhetnek, de egy alárendelt eszköznek csak egy szülője van. Három lehetőség van a gyermek eszközök peremhálózati eszközre való beállítására: a Azure Portalon keresztül az Azure CLI használatával vagy az IoT Hub Service SDK használatával.

A következő szakaszokban példákat talál arra, hogyan deklarálhatja a szülő-gyermek kapcsolatot IoT Hub a meglévő IoT-eszközökhöz. Ha új eszköz-identitásokat hoz létre a gyermek eszközökhöz, további információt a következő témakörben talál: [alárendelt eszköz hitelesítése az Azure IoT hub](how-to-authenticate-downstream-device.md) .

#### <a name="option-1-iot-hub-portal"></a>1. lehetőség: IoT Hub portál

Új eszköz létrehozásakor deklarálhatja a szülő-gyermek kapcsolatot. Vagy a meglévő eszközök esetében deklarálhatja a kapcsolatot a szülő IoT Edge eszköz vagy a gyermek IoT eszköz részletek lapján.

   ![Gyermek eszközök kezelése a IoT Edge eszköz részletei lapról](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>2. lehetőség: a `az` parancssori eszköz használata

Az [Azure parancssori felület](/cli/azure/) és a IoT- [bővítmény](https://github.com/azure/azure-iot-cli-extension) (v 0.7.0 vagy újabb) használatával az [eszköz-identitás](/cli/azure/ext/azure-iot/iot/hub/device-identity) alparancsokkal kezelheti a szülő-gyermek kapcsolatokat. Az alábbi példa egy lekérdezést használ a központban lévő összes nem IoT Edge eszköz hozzárendeléséhez IoT Edge eszköz alárendelt eszközeinek.

```azurecli
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name
```

A [lekérdezés](../iot-hub/iot-hub-devguide-query-language.md) módosításával kiválaszthatja az eszközök különböző részhalmazát. Ha nagy mennyiségű eszközt ad meg, a parancs több másodpercig is eltarthat.

#### <a name="option-3-use-iot-hub-service-sdk"></a>3. lehetőség: a IoT Hub Service SDK használata

Végül pedig a C#, a Java vagy a Node.js IoT Hub Service SDK használatával felügyelheti a szülő-gyermek kapcsolatokat. Az alábbi példa egy alárendelt eszköz C# SDK-val történő [hozzárendelését szemlélteti](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/e2e/test/iothub/service/RegistryManagerE2ETests.cs) .

### <a name="set-up-the-parent-device-as-a-gateway"></a>A szülő eszköz beállítása átjáróként

Úgy gondolja, hogy egy szülő/gyermek kapcsolat transzparens átjáróként van, ahol a gyermek eszköz saját identitással rendelkezik IoT Hub de a szülőn keresztül kommunikál a felhőben. A biztonságos kommunikációhoz a gyermek eszköznek képesnek kell lennie annak ellenőrzésére, hogy a fölérendelt eszköz megbízható forrásból származik-e. Ellenkező esetben a harmadik felek rosszindulatú eszközöket állíthatnak be a szülők megszemélyesítésére és a kommunikáció elfogására.

A megbízhatósági kapcsolat létrehozásának egyik módját a következő cikkekben részletesen ismertetjük:

* [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
* [Alárendelt (gyermek) eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS-kiszolgálók meghatározása

A robusztusság javítása érdekében erősen ajánlott megadnia a környezetben használt DNS-kiszolgáló címét. Ha IoT Edge DNS-kiszolgálóját szeretné beállítani, tekintse meg a következő témakört: az [Edge Agent modul felbontása folyamatosan jelentést készít az "üres konfigurációs fájlról", és a hibaelhárítási cikkben egyetlen modul sem indul el](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device) .

## <a name="optional-offline-settings"></a>Választható Offline beállítások

Ha az eszközök offline állapotba kerülnek, a IoT Edge szülő eszköz az összes eszközről a felhőbe irányuló üzenetet tárolja a kapcsolat újbóli létrehozása előtt. Az IoT Edge hub modul kezeli az offline üzenetek tárolását és továbbítását. Az olyan eszközök esetében, amelyek hosszabb ideig offline állapotba kerülhetnek, optimalizálja a teljesítményt két IoT Edge hub-beállítás konfigurálásával.

Először is növelje az élő beállítás élettartamát, hogy az IoT Edge hub elég sokáig őrizze meg az üzeneteket, hogy az eszköz újrakapcsolódjon. Ezután adjon hozzá további lemezterületet az üzenetek tárolásához.

### <a name="time-to-live"></a>Élettartam

Az élettartam beállítása az az időtartam (másodpercben), ameddig egy üzenet várakozik a lejárata előtt. Az alapértelmezett érték 7200 másodperc (két óra). A maximális értéket csak egy egész szám változó maximális értéke korlátozza, ami körülbelül 2 000 000 000.

Ez a beállítás a IoT Edge hub kívánt tulajdonsága, amelyet a különálló modul tárol. A Azure Portal vagy közvetlenül az üzembe helyezési jegyzékben is konfigurálhatja.

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Host Storage rendszermodulokhoz

Az üzenetek és a modul állapotára vonatkozó információk alapértelmezés szerint az IoT Edge hub helyi tárolójában tárolódnak. A jobb megbízhatóság érdekében, különösen a kapcsolat nélküli üzemmódban, a gazdagép IoT Edge eszközön is kioszthatja a tárolót. További információkért lásd: [modulok hozzáférésének biztosítása az eszköz helyi tárolójához](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Következő lépések

További információ a szülő/gyermek eszköz kapcsolatainak transzparens átjáró beállításáról:

* [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
* [Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon](how-to-authenticate-downstream-device.md)
* [Lefelé irányuló eszköz csatlakoztatása Azure IoT Edge-átjáróhoz](how-to-connect-downstream-device.md)
