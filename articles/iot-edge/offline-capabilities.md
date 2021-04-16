---
title: Eszközök offline működtethető – Azure IoT Edge | Microsoft Docs
description: Annak a IoT Edge, hogyan működhetnek az eszközök és modulok internetkapcsolat nélkül hosszabb ideig, és hogyan teheti lehetővé a IoT Edge, hogy a normál IoT-eszközök offline is működtessenek.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c9412e2adeb9b43b4c61437fb41e68bc96b86afd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481837"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>A kiterjesztett offline képességek IoT Edge eszközök, modulok és gyermekeszközök esetén

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge támogatja a kiterjesztett offline műveleteket a IoT Edge eszközökön, és engedélyezi az offline műveleteket a nem IoT Edge eszközökön is. Amíg egy IoT Edge eszköznek egyetlen lehetősége van a IoT Hub-hoz való csatlakozásra, az eszköz és a gyermekeszközök továbbra is időszakosan vagy internetkapcsolat nélkül működhetnek.

## <a name="how-it-works"></a>Működés

Amikor egy IoT Edge offline módba lép, a IoT Edge-központ három szerepkört vesz fel. Először tárolja a felfelé irányuló üzeneteket, és menti őket, amíg az eszköz újra nem csatlakozik. Másodszor, az IoT Hub nevében eljárva hitelesíti a modulokat és a gyermekeszközöket, hogy azok továbbra is működhessenek. Harmadszor, lehetővé teszi a gyermekeszközök közötti kommunikációt, amely egyébként az IoT Hubon keresztül zajlana.

Az alábbi példa bemutatja, hogyan működik IoT Edge forgatókönyv offline módban:

1. **Eszközök konfigurálása**

   IoT Edge eszközökön automatikusan engedélyezve vannak az offline képességek. Ahhoz, hogy ezt a képességet más IoT-eszközökre is kiterjeszteni tudja, deklarálnunk kell egy szülő-gyermek kapcsolatot az eszközök között a IoT Hub. Ezután úgy konfigurálja a gyermekeszközöket, hogy megbíznak a hozzárendelt szülőeszközben, és az eszközről a felhőbe irányítják az eszközről a felhőbe való kommunikációt átjáróként a szülőn keresztül.

2. **Szinkronizálás IoT Hub**

   Az új IoT Edge telepítése után legalább IoT Edge az eszköznek online állapotban kell lennie, hogy szinkronizálni IoT Hub. Ebben a szinkronizálásban a IoT Edge eszköz le kapja a hozzárendelt gyermekeszközök adatait. A IoT Edge eszköz emellett biztonságosan frissíti a helyi gyorsítótárát az offline műveletek engedélyezéséhez, és lekéri a telemetriai üzenetek helyi tárolóbeállításait.

3. **Offline módban**

   A kapcsolat megszakadása IoT Hub, IoT Edge eszköz, az üzembe helyezett modulok és a gyermek IoT-eszközök határozatlan ideig működhetnek. A modulok és a gyermekeszközök úgy indíthatóak el és indíthatóak újra, hogy offline állapotban hitelesítik IoT Edge központot. A telemetria a IoT Hub helyileg van tárolva. A modulok vagy a gyermek IoT-eszközök közötti kommunikáció közvetlen metódusokkal vagy üzenetekkel tartható fenn.

4. **Újracsatlakozás és újraszinkronizáció IoT Hub**

   Miután helyreállt a kapcsolat IoT Hub, a IoT Edge eszköz újra szinkronizál. A helyileg tárolt üzenetek azonnal IoT Hub a szolgáltatásnak, de függnek a kapcsolat sebességtől, IoT Hub késéstől és a kapcsolódó tényezőktől. A kézbesítésük ugyanabban a sorrendben történt, mint a tárolásuk.

   A modulok és eszközök kívánt és jelentett tulajdonságai közötti különbségek egyeztetve vannak. A IoT Edge eszköz frissíti a hozzárendelt gyermek IoT-eszközök készletének módosításait.

## <a name="restrictions-and-limits"></a>Korlátozások és korlátozások

A cikkben ismertetett kiterjesztett offline képességek az [1.0.7-es IoT Edge](https://github.com/Azure/azure-iotedge/releases)vagy újabb verzióban érhetők el. A korábbi verziók az offline funkciók egy részkészletét is tartalmazják. A meglévő IoT Edge-eszközök, amelyek nem rendelkeznek kiterjesztett offline funkciókkal, nem frissíthetőek a futásidejű verzió módosításával, de ezeket a funkciókat új IoT Edge-eszközidentitással kell újrakonfigurálni.

Csak nem IoT Edge eszközöket lehet gyermekeszközökként hozzáadni.

IoT Edge eszközök és a hozzárendelt gyermekeszközök a kezdeti, egyszeres szinkronizálás után határozatlan ideig offline állapotban is működhetnek. Az üzenetek tárolása azonban az idő (TTL) beállításától és az üzenetek tárolásához rendelkezésre álló lemezterülettől függ.

## <a name="set-up-parent-and-child-devices"></a>Szülő- és gyermekeszközök beállítása

Ahhoz, IoT Edge eszköz kiterjeszteni a kiterjesztett offline képességeit a gyermek IoT-eszközökre, két lépést kell végrehajtania. Először deklarálja a szülő-gyermek kapcsolatokat a Azure Portal. Ezután hozzon létre megbízhatósági kapcsolatot a szülőeszköz és a gyermekeszközök között, majd konfigurálja úgy az eszköz–felhő kommunikációt, hogy átjáróként áthalad a szülőn.

### <a name="assign-child-devices"></a>Gyermekeszközök hozzárendelése

Gyermekeszköz lehet bármely olyan nem IoT Edge eszköz, amely ugyanabban a IoT Hub. A szülőeszközök több gyermekeszközt is tudnak, de egy gyermekeszköznek csak egy szülője lehet. Három lehetőség van a gyermekeszközök peremeszközre való beállítására: a Azure Portal, az Azure CLI vagy a IoT Hub Service SDK használatával.

A következő szakaszok példákat mutatnak be arra, hogyan deklarálhatja a szülő/gyermek kapcsolatot a IoT Hub IoT-eszközök számára. Ha új eszköz-identitásokat hoz létre a gyermekeszközökhöz, további információért lásd: [Lefelé](how-to-authenticate-downstream-device.md) irányuló eszköz hitelesítése az Azure IoT Hub létrehozásához.

#### <a name="option-1-iot-hub-portal"></a>1. lehetőség: IoT Hub portál

Új eszköz létrehozásakor deklarálhatja a szülő-gyermek kapcsolatot. Meglévő eszközök esetén a kapcsolatot a szülőeszköz vagy a gyermek IoT-eszköz eszköz IoT Edge adatok lapján deklarálhatja.

   ![Gyermekeszközök kezelése a IoT Edge eszköz részleteit tartalmazó lapon](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>2. lehetőség: A `az` parancssori eszköz használata

Az [Azure parancssori felületének](/cli/azure/) [IoT-bővítővel](https://github.com/azure/azure-iot-cli-extension) (0.7.0-s vagy újabb) való használatával a szülő-gyermek kapcsolatokat az [eszközidentitás alparancsokkal](/cli/azure/iot/hub/device-identity/) kezelheti. Az alábbi példa egy lekérdezéssel rendeli hozzá a hubon IoT Edge összes nem IoT Edge eszközt, hogy egy IoT Edge eszköz gyermekeszköze legyen.

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

Módosíthatja a [lekérdezést](../iot-hub/iot-hub-devguide-query-language.md) az eszközök egy másik részkészletének kiválasztásához. Ha nagy eszközkészletet ad meg, a parancs több másodpercet is igénybe vehet.

#### <a name="option-3-use-iot-hub-service-sdk"></a>3. lehetőség: A IoT Hub Service SDK használata

Végül programozott módon kezelheti a szülő-gyermek kapcsolatokat c#, Java vagy Node.js IoT Hub Service SDK használatával. Az alábbi [példában egy gyermekeszközt rendelünk hozzá a](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/e2e/test/iothub/service/RegistryManagerE2ETests.cs) C# SDK-val.

### <a name="set-up-the-parent-device-as-a-gateway"></a>A szülőeszköz beállítása átjáróként

A szülő-gyermek kapcsolatra fel lehet gondolni transzparens átjáróként, ahol a gyermekeszköz saját identitással rendelkezik a IoT Hub de a felhőn keresztül kommunikál a szülőn keresztül. A biztonságos kommunikációhoz a gyermekeszköznek képesnek kell lennie ellenőrizni, hogy a szülőeszköz megbízható forrásból származik-e. Ellenkező esetben külső felek rosszindulatú eszközöket állíthatnak be a szülők megszemélyesítésére és a kommunikáció elfogására.

Ennek a megbízhatósági kapcsolatnak az egyik módját a következő cikkek ismertetik részletesen:

* [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
* [Lefelé irányuló (gyermek) eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS-kiszolgálók megadása

A robusztusság javítása érdekében erősen ajánlott megadni a környezetben használt DNS-kiszolgálócímeket. A DNS-kiszolgáló beállítását a IoT Edge a hibaelhárítási cikk Az [Edge](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device) Agent modulja folyamatosan "üres konfigurációs fájlként" jelenti, és a modulok nem indulnak el az eszközön.

## <a name="optional-offline-settings"></a>Választható offline beállítások

Ha az eszközök offline állapotba IoT Edge, a szülőeszköz tárolja az eszközről a felhőbe küldött összes üzenetet, amíg a kapcsolat újra létre nem áll. Az IoT Edge központ modul kezeli az offline üzenetek tárolását és továbbítását. A hosszabb ideig offline állapotba állítható eszközök esetében optimalizálja a teljesítményt két központi IoT Edge konfigurálásával.

Először növelje az élő kapcsolat beállítását, hogy a IoT Edge hub elég hosszan tartsa az üzeneteket az eszköz újracsatlakoztatásához. Ezután adjon hozzá további lemezterületet az üzenettároláshoz.

### <a name="time-to-live"></a>Élettartam

Az élő beállítás az az idő (másodpercben), amely alatt az üzenet a lejárata előtt várakozhat a kézbesítésre. Az alapértelmezett érték 7200 másodperc (két óra). A maximális értéket csak egy egész szám típusú változó maximális értéke korlátozza, amely körülbelül 2 milliárd.

Ez a beállítás az ikermodulban IoT Edge kívánt tulajdonsága. Az üzembe helyezési jegyzékfájlban Azure Portal közvetlenül is konfigurálhatja.

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

### <a name="host-storage-for-system-modules"></a>Gazdatároló rendszermodulok számára

Az üzenetek és a modul állapotinformációi alapértelmezés szerint a IoT Edge helyi tároló fájlrendszerében vannak tárolva. A nagyobb megbízhatóság érdekében, különösen offline működés esetén, a gazdagépen és az eszközön is IoT Edge tárterületet. További információ: Hozzáférés a moduloknak egy eszköz [helyi tárterületéhez](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Következő lépések

További információ a transzparens átjárók beállításról a szülő-gyermek eszközkapcsolatok számára:

* [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
* [Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon](how-to-authenticate-downstream-device.md)
* [Lefelé irányuló eszköz csatlakoztatása Azure IoT Edge-átjáróhoz](how-to-connect-downstream-device.md)
