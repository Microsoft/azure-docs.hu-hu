---
title: Az Azure IoT Hub-eszközök ikrek ismertetése | Microsoft Docs
description: Fejlesztői útmutató – az eszközök ikrek használatával szinkronizálhatók az állapot-és konfigurációs adatokat IoT Hub és az eszközei között
author: nehsin
manager: philmea
ms.author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: fff4b9157c30203f47c65a74b211e3dbf6426d92
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093036"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Az IoT Hub eszközön található ikrek megismerése és használata

Az *ikrek* olyan JSON-dokumentumok, amelyek az eszköz állapotával kapcsolatos információkat tárolnak, beleértve a metaadatokat, a konfigurációkat és a feltételeket. Az IoT Hub minden egyes rácsatlakoztatott eszközhöz fenntart egy ikereszközt. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez a cikk a következőket ismerteti:

* A két eszköz szerkezete: *címkék*, *kívánt* és *jelentett tulajdonságok*.
* Az eszközön futó alkalmazások és a háttérbeli műveletek az eszközökön is elvégezhetők.

Az eszközökhöz tartozó ikrek használata:

* Az eszközre jellemző metaadatok tárolása a felhőben. Például egy árusító gép üzembe helyezési helye.

* A jelenlegi állapotadatok, például a rendelkezésre álló képességek és kikötések jelentése az eszköz alkalmazásában. Egy eszköz például a IoT hubhoz csatlakozik a mobil-vagy WiFi-kapcsolaton keresztül.

* Szinkronizálja a hosszan futó munkafolyamatok állapotát az eszköz alkalmazás és a háttérbeli alkalmazás között. Ha például a megoldás háttérbe állítása meghatározza a telepítendő új belső vezérlőprogram-verziót, és az eszköz alkalmazás a frissítési folyamat különböző szakaszait jelenti.

* Az eszköz metaadatait, konfigurációját vagy állapotát kérdezheti le.

Tekintse meg az [eszközről a felhőbe irányuló kommunikációs útmutatót](iot-hub-devguide-d2c-guidance.md) a jelentett tulajdonságok, az eszközről a felhőbe irányuló üzenetek vagy a fájlfeltöltés használatával kapcsolatos útmutatásért.

Tekintse át a [felhőből az eszközre irányuló kommunikációs útmutatót](iot-hub-devguide-c2d-guidance.md) , amely útmutatást nyújt a kívánt tulajdonságok, közvetlen metódusok vagy a felhőből az eszközre irányuló üzenetek használatához.

Ha szeretné megtudni, hogyan kapcsolódnak az eszközök az Azure IoT Plug and Play eszközhöz használt eszköz modelljéhez, tekintse meg a [IoT Plug and Play digitális ikrek ismertetése](../iot-pnp/concepts-digital-twin.md)című témakört.

## <a name="device-twins"></a>Eszköz ikrek

A Device Twins az eszközhöz kapcsolódó információkat tárolja:

* Az eszköz és a háttér végpontja az eszközre vonatkozó feltételek és konfiguráció szinkronizálására használható.

* A megoldás háttérbeli futtatása a hosszú ideig futó műveletek lekérdezésére és megcélzására használható.

Az eszközök különálló életciklusa a megfelelő [eszköz identitásához](iot-hub-devguide-identity-registry.md)van csatolva. Az eszköz-ikrek implicit módon jönnek létre, és törlődnek az eszköz identitásának létrehozásakor vagy törlésekor IoT Hub.

Az eszköz Twin egy JSON-dokumentum, amely a következőket tartalmazza:

* **Címkék**. A megoldás hátterében lévő JSON-dokumentum egy szakasza, amelyből beolvasható és írható. A címkék nem láthatók az eszköz alkalmazásaiban.

* **Kívánt tulajdonságok**. A jelentett tulajdonságokkal együtt használható az eszköz konfigurációjának vagy feltételeinek szinkronizálásához. A megoldás háttérbe állítása megadhatja a kívánt tulajdonságokat, és az eszköz alkalmazás is elolvashatja őket. Az alkalmazás a kívánt tulajdonságok változásairól is fogadhat értesítéseket.

* **Jelentett tulajdonságok**. A kívánt tulajdonságokkal együtt használható az eszköz konfigurációjának vagy feltételeinek szinkronizálásához. Az alkalmazás beállíthatja a jelentett tulajdonságokat, és a megoldás hátterében olvashat és lekérdezheti azokat.

* **Eszköz identitásának tulajdonságai** Az eszköz kettős JSON-dokumentumának gyökere a [személyazonossági beállításjegyzékben](iot-hub-devguide-identity-registry.md)tárolt megfelelő eszköz identitásának írásvédett tulajdonságait tartalmazza. A `connectionStateUpdatedTime` Tulajdonságok `generationId` nem lesznek belefoglalva.

![Képernyőfelvétel az eszközök Twin tulajdonságairól](./media/iot-hub-devguide-device-twins/twin.png)

Az alábbi példa egy eszköz kettős JSON-dokumentumot mutat be:

```json
{
    "deviceId": "devA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

A root objektumban az eszköz identitásának tulajdonságai, valamint a `tags` és mindkettő és a `reported` Tulajdonságok tároló objektumai `desired` . A `properties` tároló tartalmaz néhány írásvédett elemet ( `$metadata` , `$etag` és `$version` ) az [eszköz kettős metaadatai](iot-hub-devguide-device-twins.md#device-twin-metadata) és az [optimista Egyidejűség](iot-hub-devguide-device-twins.md#optimistic-concurrency) szakaszokban.

### <a name="reported-property-example"></a>Jelentett tulajdonság – példa

Az előző példában az eszköz Twin egy olyan `batteryLevel` tulajdonságot tartalmaz, amelyet az eszköz alkalmazás jelentett. Ez a tulajdonság lehetővé teszi az eszközök lekérdezését és működését az utolsó jelentett akkumulátor szintjének megfelelően. Egyéb példák például az eszköz alkalmazás-jelentési eszköz képességei vagy kapcsolódási lehetőségei.

> [!NOTE]
> A jelentett tulajdonságok leegyszerűsítik az olyan forgatókönyveket, amelyekben a megoldás hátterét egy tulajdonság utolsó ismert értéke érdekli. Az [eszközről a felhőbe](iot-hub-devguide-messages-d2c.md) irányuló üzenetek használata, ha a megoldás hátterének időbélyeg-események (például idősorozat) formájában kell feldolgoznia az eszköz telemetria.

### <a name="desired-property-example"></a>Példa a kívánt tulajdonságra

Az előző példában a `telemetryConfig` megoldás háttérbe állítása és a jelentett tulajdonságok a megfelelő eszközre, az eszközön pedig az eszköz telemetria-konfigurációjának szinkronizálására szolgálnak. Például:

1. A megoldás háttérbe állítása a kívánt tulajdonságot a kívánt konfigurációs értékkel állítja be. Itt látható a dokumentum azon része, amely a kívánt tulajdonságot beállítja:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Az eszköz alkalmazás azonnal értesítést kap a változásról, ha csatlakozik, vagy az első újracsatlakozáskor. Az alkalmazás ezután jelentést készít a frissített konfigurációról (vagy a tulajdonságot használó hiba feltételéről `status` ). Itt látható a jelentett tulajdonságok része:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. A megoldás háttérbe állításával több eszközön is nyomon követheti a konfigurációs művelet eredményeit az eszközök [lekérésével](iot-hub-devguide-query-language.md) .

> [!NOTE]
> Az előző kódrészletek az olvashatóságra optimalizált példák, amelyek az eszköz konfigurációjának és állapotának az egyik módszerét jelentik. A IoT Hub nem határoz meg konkrét sémát az eszköz Twin kívánt és jelentett tulajdonságaihoz az eszközök ikrekben.
> 

Az ikrek használatával a hosszan futó műveleteket, például a belső vezérlőprogram frissítéseit is szinkronizálhatja. Ha további információra van szükség arról, hogyan használhatók a tulajdonságok a hosszú ideig futó műveletek szinkronizálásához és nyomon követéséhez az eszközök között, tekintse meg a [kívánt tulajdonságok használata az eszközök konfigurálásához](tutorial-device-twins.md)

## <a name="back-end-operations"></a>Háttérbeli műveletek

A megoldás háttérrendszer a következő, HTTPS protokollon keresztül elérhető atomi műveletek használatával működik az eszközön:

* **A Twin eszköz beolvasása azonosító alapján**. Ez a művelet visszaadja az eszköz kettős dokumentumát, beleértve a címkéket és a kívánt és jelentett rendszertulajdonságokat.

* **Részben frissítse az eszköz ikerét**. Ez a művelet lehetővé teszi, hogy a megoldás háttérbe kerüljön, hogy részben frissítse a címkéket vagy a kívánt tulajdonságokat egy különálló eszközön. A részleges frissítés JSON-dokumentumként van megadva, amely bármilyen tulajdonságot feltesz vagy frissít. Az `null` eltávolításra beállított tulajdonságok törlődnek. Az alábbi példa egy új kívánt tulajdonságot hoz létre az értékkel `{"newProperty": "newValue"}` , felülírja a meglévő `existingProperty` értékét `"otherNewValue"` , és eltávolítja azt `otherOldProperty` . A meglévő kívánt tulajdonságok vagy címkék nem módosulnak:

   ```json
   {
       "properties": {
           "desired": {
               "newProperty": {
                   "nestedProperty": "newValue"
               },
               "existingProperty": "otherNewValue",
               "otherOldProperty": null
           }
       }
   }
   ```

* A **kívánt tulajdonságok cseréje**. Ez a művelet lehetővé teszi a megoldás háttérbe lépését, hogy teljesen felülírja az összes meglévő kívánt tulajdonságot, és új JSON-dokumentumot cseréljen `properties/desired` .

* **Címkék cseréje** Ez a művelet lehetővé teszi a megoldás háttérbe lépését, hogy teljesen felülírja az összes meglévő címkét, és új JSON-dokumentumot cseréljen a alkalmazásra `tags` .

* **Kettős értesítések fogadása**. Ez a művelet lehetővé teszi a megoldás háttérbeli értesítését, ha a Twin módosítva van. Ehhez a IoT-megoldásnak létre kell hoznia egy útvonalat, és az adatforrást meg kell egyeznie a *twinChangeEvents* értékkel. Alapértelmezés szerint nem léteznek ilyen útvonalak, ezért a rendszer nem küld külön értesítéseket. Ha a változás sebessége túl magas, vagy más okokból, például belső hibák esetén, a IoT Hub csak egy értesítést küldhet, amely az összes módosítást tartalmazza. Ezért, ha az alkalmazásnak az összes közbenső állapot megbízható naplózására és naplózására van szüksége, az eszközről a felhőbe irányuló üzeneteket kell használnia. A kettős értesítési üzenet tartalmazza a tulajdonságokat és a törzset.

  - Tulajdonságok

    | Name | Érték |
    | --- | --- |
    $content típusa | application/json |
    $iothub – enqueuedtime |  Az értesítés elküldésének ideje |
    $iothub – üzenet – forrás | twinChangeEvents |
    $content – kódolás | UTF-8 |
    deviceId | Az eszköz azonosítója |
    hubName | IoT Hub neve |
    operationTimestamp | A művelet [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) időbélyege |
    iothub – üzenet – séma | twinChangeNotification |
    opType | "replaceTwin" vagy "updateTwin" |

    Az üzenetrendszer tulajdonságai előtaggal vannak ellátva a `$` szimbólummal.

  - Törzs
        
    Ez a szakasz a JSON-formátum összes kettős módosítását tartalmazza. Ugyanazt a formátumot használja, mint a javítás, a különbséggel, hogy az összes különálló szakaszt tartalmazhatja: címkék, tulajdonságok. jelentett, Properties. desired, és hogy tartalmazza a "$metadata" elemeket. Példa:

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Az összes korábbi művelet támogatja az [optimista párhuzamosságot](iot-hub-devguide-device-twins.md#optimistic-concurrency) , és megköveteli a **ServiceConnect** engedélyt a [IoT hub hozzáférés-vezérlésének szabályozása alapján](iot-hub-devguide-security.md).

Ezen műveletek mellett a megoldás hátterében a következőket teheti:

* Az eszköz-ikrek lekérdezése az SQL-szerű [IoT hub lekérdezési nyelv](iot-hub-devguide-query-language.md)használatával.

* Műveleteket hajthat végre nagy mennyiségű eszközön a [feladatok](iot-hub-devguide-jobs.md)használatával.

## <a name="device-operations"></a>Eszköz műveletei

Az eszköz-alkalmazás a következő atomi műveletek használatával működik az eszközön:

* A **Twin eszköz beolvasása**. A művelet visszaadja az eszköz kettős dokumentumát (beleértve a kívánt és jelentett rendszertulajdonságokat is) a jelenleg csatlakoztatott eszközhöz. (A címkék nem láthatók az eszköz alkalmazásai számára.)

* **Jelentett tulajdonságok részleges frissítése**. Ez a művelet lehetővé teszi a jelenleg csatlakoztatott eszköz jelentett tulajdonságainak részleges frissítését. Ez a művelet ugyanazt a JSON-frissítési formátumot használja, amelyet a megoldás hátterében a kívánt tulajdonságok részleges frissítése használ.

* A **kívánt tulajdonságok megfigyelése**. A jelenleg csatlakoztatott eszköz dönthet úgy, hogy értesítést küld a kívánt tulajdonságok frissítéseiről, amikor azok történnek. Az eszköz ugyanazt a frissítést (részleges vagy teljes cserét) kapja, amelyet a megoldás hátterében hajt végre.

Az összes fenti művelethez szükség van a **DeviceConnect** engedélyre, ahogy az a [IoT hub hozzáférés-vezérlési szolgáltatásban](iot-hub-devguide-security.md)van meghatározva.

Az [Azure IoT-eszközök SDK](iot-hub-devguide-sdks.md) -k megkönnyítik az előző műveletek használatát számos nyelvről és platformról. További információ a kívánt tulajdonságok szinkronizálásához IoT Hub primitívek részleteiről: [eszköz újrakapcsolási folyamata](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Címkék és tulajdonságok formátuma

A címkék, a kívánt tulajdonságok és a jelentett tulajdonságok a JSON-objektumok a következő korlátozásokkal:

* **Kulcsok**: a JSON-objektumokban lévő összes kulcs UTF-8 kódolású, kis-és nagybetűket megkülönböztető, és legfeljebb 1 kb hosszúságú lehet. Az engedélyezett karakterek kizárják a UNICODE vezérlő karaktereket (a C0 és a C1 szegmenst), valamint a, és az SP-t `.` `$` .

* **Értékek**: a JSON-objektumokban lévő összes érték a következő JSON-típusokkal rendelkezhet: logikai, szám, karakterlánc, objektum. A tömbök is támogatottak.

    * Az egész számok minimális értéke-4503599627370496 és a 4503599627370495-es maximális érték lehet.

    * A karakterlánc-értékek UTF-8 kódolással rendelkeznek, és legfeljebb 4 KB-os hosszúságú lehet.

* **Mélység**: a címkékben, a kívánt tulajdonságokban és a jelentett tulajdonságok között a JSON-objektumok maximális mélysége 10. A következő objektum például érvényes:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="device-twin-size"></a>Eszköz kettős mérete

IoT Hub kényszeríti a 8 KB-os mérethatárt a értékre `tags` , és egy 32 KB-os mérethatárt a és a értékre `properties/desired` `properties/reported` . Ezek az összegek kizárólag a (z `$etag` ),, és rendszerű írásvédett elemekre vonatkoznak `$version` `$metadata/$lastUpdated` .

A Twin méret kiszámítása a következőképpen történik:

* A JSON-dokumentum minden tulajdonságához IoT Hub a kumulatív számításokat, és hozzáadja a tulajdonság kulcsának és értékének hosszát.

* A tulajdonságmezők UTF8-kódolású karakterláncnak tekintendők.

* Az egyszerű tulajdonságértékek UTF8-kódolású karakterláncoknak, numerikus értékeknek (8 bájt) vagy logikai értékeknek (4 bájt) tekintendők.

* Az UTF8-kódolású karakterláncok méretét az összes karakter számlálásával számítjuk ki, a UNICODE vezérlőkarakterek kivételével (szegmens C0 és C1).

* Az összetett tulajdonságértékek (beágyazott objektumok) kiszámítása az általuk tartalmazott tulajdonságértékek és tulajdonságértékek összesített mérete alapján történik.

A IoT Hub elutasítja az összes olyan műveletet, amely a `tags` határértéknél nagyobb méretet `properties/desired` vagy `properties/reported` dokumentumot eredményezne.

## <a name="device-twin-metadata"></a>Eszköz – Twin metaadatok

IoT Hub karbantartja az összes JSON-objektum utolsó frissítésének időbélyegét az eszközök Twin-beli kívánt és jelentett tulajdonságaiban. Az időbélyegek UTC szerint vannak elfoglalva, és [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) formátumban vannak kódolva `YYYY-MM-DDTHH:MM:SS.mmmZ` .

Például:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Ezek az információk minden szinten megmaradnak (nem csak a JSON-struktúra levelei) az objektumok kulcsait eltávolító frissítések megőrzése érdekében.

## <a name="optimistic-concurrency"></a>Optimista Egyidejűség

Címkék, kívánt és jelentett tulajdonságok az optimista párhuzamosságok támogatásával.
A címkékhez ETag ( [RFC7232](https://tools.ietf.org/html/rfc7232)) tartozik, amely a címke JSON-ábrázolását jelöli. A konzisztencia biztosításához használhatja a megoldás Etagek a feltételes frissítési műveletekben.

A kívánt eszközök és a jelentett tulajdonságok nem rendelkeznek Etagek, de a `$version` növekményes érték garantált. A ETag hasonlóan a frissítési fél is használhatja a verziót a frissítések konzisztenciájának betartatására. Tegyük fel például, hogy egy alkalmazás egy jelentett tulajdonsághoz vagy egy kívánt tulajdonsághoz a megoldás hátteréhez tartozó eszköz.

A verziók akkor is hasznosak, ha egy megfigyelő ügynöknek (például a kívánt tulajdonságokat megfigyelő eszköz alkalmazásnak) össze kell egyeztetni a versenyeket egy lekérési művelet és egy frissítési értesítés eredménye között. Az [eszköz újrakapcsolási folyamat szakasza](iot-hub-devguide-device-twins.md#device-reconnection-flow) további információkat tartalmaz.

## <a name="device-reconnection-flow"></a>Eszköz újrakapcsolási folyamata

IoT Hub nem őrzi meg a leválasztott eszközökhöz tartozó, a kívánt tulajdonságok frissítési értesítéseit. Ez azt eredményezi, hogy egy csatlakozó eszköznek le kell kérnie a teljes kívánt tulajdonságokat tartalmazó dokumentumot a frissítési értesítések előfizetése mellett. Tekintettel arra, hogy a verseny a frissítési értesítések és a teljes lekérések között is fennáll, a következő folyamatot kell biztosítani:

1. Az eszközbeállítások egy IoT hubhoz csatlakozik.
2. Az eszköz-alkalmazás előfizet a kívánt tulajdonságok frissítési értesítéseire.
3. Az eszköz beolvassa a teljes dokumentumot a kívánt tulajdonságokhoz.

Az eszköz alkalmazás figyelmen kívül hagyhatja az összes olyan értesítést, amely a `$version` teljes beolvasott dokumentum verziójánál kisebb vagy egyenlő. Ez a megközelítés azért lehetséges, mert IoT Hub garantálja, hogy a verziók mindig növekményt biztosítanak.

> [!NOTE]
> Ez a logika már implementálva van az [Azure IoT Device SDK](iot-hub-devguide-sdks.md)-ban. Ez a leírás csak akkor hasznos, ha az eszköz nem tudja használni az Azure IoT Device SDK-kat, és a MQTT felületét közvetlenül kell programoznia.
> 

## <a name="additional-reference-material"></a>További referenciaanyagok

A IoT Hub Fejlesztői útmutatóban található további témakörök a következők:

* A [IoT hub endpoints](iot-hub-devguide-endpoints.md) cikk a különböző végpontokat ismerteti, amelyeket az egyes IoT hub a futásidejű és a felügyeleti műveletek számára tesz elérhetővé.

* A [szabályozás és kvóták](iot-hub-devguide-quotas-throttling.md) cikk a IoT hub szolgáltatásra vonatkozó kvótákat és a szolgáltatás használatakor várható szabályozási viselkedést ismerteti.

* Az [Azure IoT eszköz és szolgáltatás SDK-](iot-hub-devguide-sdks.md) k című cikk felsorolja azokat a különböző nyelvi SDK-kat, amelyek a IoT hub használatával kommunikáló eszköz-és szolgáltatás-alkalmazások fejlesztéséhez használhatók.

* Az [ikrek, feladatok és üzenet-útválasztási cikk IoT hub lekérdezési nyelve](iot-hub-devguide-query-language.md) a IoT hub lekérdezési nyelvet írja le, amellyel lekérheti az adatokat az eszközökről az ikrekről és a feladatokról IoT hub.

* A [IOT hub MQTT-támogatási](iot-hub-mqtt-support.md) cikk további információkat nyújt a MQTT protokoll IoT hub támogatásáról.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az eszközökről szóló ikreket, az alábbi IoT Hub fejlesztői útmutató témaköreiben találhat további információkat:

* [Az ikrek megismerése és használata IoT Hub](iot-hub-devguide-module-twins.md)
* [Közvetlen metódus meghívása egy eszközön](iot-hub-devguide-direct-methods.md)
* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

A cikkben ismertetett fogalmak némelyikének kipróbálásához tekintse meg a következő IoT Hub oktatóanyagokat:

* [Az eszköz Twin használata](iot-hub-node-node-twin-getstarted.md)
* [Az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)
* [Eszközfelügyelet VS Code-hoz készült Azure IoT-eszközökkel](iot-hub-device-management-iot-toolkit.md)
