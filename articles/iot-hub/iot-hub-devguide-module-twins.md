---
title: Az Azure IoT Hub-modul ikrek ismertetése | Microsoft Docs
description: Fejlesztői útmutató – a modulok ikrek használatával szinkronizálhatók az állapot-és konfigurációs adatokat IoT Hub és az eszközök között
author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: nehsin
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6e728eaf8335a102e38a3b4b07ab5e504d452294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91996462"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Az ikrek megismerése és használata IoT Hub

Ez a cikk azt feltételezi, hogy elolvasta [az eszközökön található ikreket, és az IoT hub először használja](iot-hub-devguide-device-twins.md) . IoT Hub minden eszköz identitása alatt létrehozhat akár 50 modul-identitást is. Minden modul identitása implicit módon létrehoz egy külön modult. Az eszközökhöz hasonlóan az ikrek olyan JSON-dokumentumok, amelyek a modul állapotával kapcsolatos információkat tárolnak, beleértve a metaadatokat, a konfigurációkat és a feltételeket. Az Azure IoT Hub minden olyan modulhoz külön modult tart fenn, amelyhez IoT Hub csatlakozik. 

Az eszköz oldalon a IoT Hub eszköz SDK-k lehetővé teszik, hogy olyan modulokat hozzon létre, amelyekben mindegyik egy független kapcsolódást nyit meg a IoT Hubhoz. Ez a funkció lehetővé teszi, hogy külön névtereket használjon az eszköz különböző összetevőihez. Például van egy olyan automatából, amely három különböző érzékelővel rendelkezik. Minden érzékelőt a vállalat különböző részlegei szabályoznak. Mindegyik érzékelőhöz létrehozhat egy modult. Így az egyes részlegek csak feladatokat vagy közvetlen metódusokat küldhetnek az általuk vezérelt érzékelőknek, elkerülve az ütközéseket és a felhasználói hibákat.

 A modul identitása és a modul Twin ugyanazokat a funkciókat biztosítja, mint az eszköz identitása és az eszközök Twin, de finomabb részletességgel. Ez a finomabb részletesség lehetővé teszi, hogy a képes eszközök, például az operációs rendszer alapú eszközök vagy a belső vezérlőprogram eszközei több összetevőt kezelnek, az egyes összetevők konfigurációjának és feltételeinek elkülönítéséhez. A modul identitása és a modul ikrek a moduláris szoftveres összetevőkkel rendelkező IoT-eszközök használatakor a probléma kezelésének elkülönítését biztosítják. Célunk, hogy az összes eszköz Twin-funkcióját támogassa a modul Twin szintjén, az általános elérhetőségi moduloknál. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez a cikk a következőket ismerteti:

* A modul szerkezete (Twin): *címkék*, *kívánt* és *jelentett tulajdonságok*.
* A modulok és a háttér végén elvégezhető műveletek az ikrek modulján is elvégezhetők.

Tekintse meg az [eszközről a felhőbe irányuló kommunikációs útmutatót](iot-hub-devguide-d2c-guidance.md) a jelentett tulajdonságok, az eszközről a felhőbe irányuló üzenetek vagy a fájlfeltöltés használatával kapcsolatos útmutatásért.

Tekintse át a [felhőből az eszközre irányuló kommunikációs útmutatót](iot-hub-devguide-c2d-guidance.md) , amely útmutatást nyújt a kívánt tulajdonságok, közvetlen metódusok vagy a felhőből az eszközre irányuló üzenetek használatához.

## <a name="module-twins"></a>Ikrek modul

A modul ikrek a modulhoz kapcsolódó információkat tárolják:

* Az eszközön található modulok és a IoT Hub a modul feltételeinek és konfigurációjának szinkronizálására használhatók.

* A megoldás háttérbeli futtatása a hosszú ideig futó műveletek lekérdezésére és megcélzására használható.

A különálló modulok életciklusa a megfelelő [modul-identitáshoz](iot-hub-devguide-identity-registry.md)van csatolva. A modulok az ikrek számára implicit módon jönnek létre, és törlődnek a modul identitásának létrehozásakor vagy törlésekor IoT Hub.

A Twin modul egy JSON-dokumentum, amely a következőket tartalmazza:

* **Címkék**. A megoldás hátterében lévő JSON-dokumentum egy szakasza, amelyből beolvasható és írható. A címkék nem láthatók az eszközön található moduloknál. A címkék lekérdezés céljára vannak beállítva.

* **Kívánt tulajdonságok**. A modul konfigurációjának vagy feltételeinek szinkronizálása a jelentett tulajdonságokkal együtt történik. A megoldás háttérbe állítása megadhatja a kívánt tulajdonságokat, a modul alkalmazás pedig elolvashatja őket. A modul alkalmazás a kívánt tulajdonságok változásairól is fogadhat értesítéseket.

* **Jelentett tulajdonságok**. A modul konfigurációjának vagy feltételeinek szinkronizálásához a kívánt tulajdonságokkal együtt használható. A modul-alkalmazás beállíthatja a jelentett tulajdonságokat, és a megoldás hátterének olvasására és lekérdezésére is lehetőség van.

* **Modul identitásának tulajdonságai** A modul kettős JSON-dokumentumának gyökerében az [Identity registryben](iot-hub-devguide-identity-registry.md)tárolt megfelelő modul identitásának írásvédett tulajdonságai szerepelnek.

![Az eszközök Twin architektúrájának ábrázolása](./media/iot-hub-devguide-device-twins/module-twin.jpg)

Az alábbi példa egy modul kettős JSON-dokumentumát mutatja be:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
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

A root objektumban a modul identitásának tulajdonságai, valamint a `tags` és mindkettő és a `reported` Tulajdonságok tároló objektumai `desired` . A `properties` tároló tartalmaz néhány írásvédett elemet ( `$metadata` , `$etag` és `$version` ), amelyet a [modul Twin metadata](iot-hub-devguide-module-twins.md#module-twin-metadata) és [optimista párhuzamossági](iot-hub-devguide-device-twins.md#optimistic-concurrency) szakaszokban ismertetünk.

### <a name="reported-property-example"></a>Jelentett tulajdonság – példa

Az előző példában a modul 2. része egy olyan `batteryLevel` tulajdonságot tartalmaz, amelyet a modul alkalmazás jelentett. Ez a tulajdonság lehetővé teszi a modulok lekérdezését és működését a legutolsó jelentett akkumulátor-szint alapján. Egyéb példák a modul alkalmazás jelentési moduljának képességei vagy csatlakozási lehetőségei.

> [!NOTE]
> A jelentett tulajdonságok leegyszerűsítik az olyan forgatókönyveket, amelyekben a megoldás hátterét egy tulajdonság utolsó ismert értéke érdekli. Az [eszközről a felhőbe](iot-hub-devguide-messages-d2c.md) irányuló üzenetek használata, ha a megoldás hátterének időbélyeg-események (például idősorozat) formájában kell feldolgoznia a modul telemetria.

### <a name="desired-property-example"></a>Példa a kívánt tulajdonságra

Az előző példában a `telemetryConfig` megoldás háttérbe állítása és a jelentett tulajdonságok alapján a rendszer a modul telemetria-konfigurációját szinkronizálja. Például:

1. A megoldás háttérbe állítása a kívánt tulajdonságot a kívánt konfigurációs értékkel állítja be. Itt látható a dokumentum azon része, amely a kívánt tulajdonságot beállítja:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. A modul alkalmazás azonnal értesítést kap a változásról, ha csatlakozik, vagy az első újracsatlakozáskor. A modul alkalmazás ezután jelentést készít a frissített konfigurációról (vagy a tulajdonságot használó hiba állapotáról `status` ). Itt látható a jelentett tulajdonságok része:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. A megoldás háttérbe állításával számos modulban nyomon követheti a konfigurációs művelet eredményét az ikrek modul [lekérdezésével](iot-hub-devguide-query-language.md) .

> [!NOTE]
> Az előző kódrészletek az olvashatóságra optimalizált példák, amelyek az egyik módszer a modulok konfigurációjának és állapotának kódolására szolgálnak. A IoT Hub nem határoz meg külön sémát a modulhoz, és az ikrek modulban jelentett tulajdonságokat jelent.
> 
> 

## <a name="back-end-operations"></a>Háttérbeli műveletek
A megoldás háttérrendszer a különálló modulon működik a következő, HTTPS protokollon keresztül elérhető atomi műveletek használatával:

* **Beolvassa a modult a Twin azonosító alapján**. Ez a művelet visszaadja a modul dupla dokumentumát, beleértve a címkéket és a kívánt és jelentett rendszertulajdonságokat.

* **Részleges frissítési modul Twin**. Ez a művelet lehetővé teszi, hogy a megoldás hátterében részben frissítse a címkéket vagy a kívánt tulajdonságokat egy különálló modulban. A részleges frissítés JSON-dokumentumként van megadva, amely bármilyen tulajdonságot feltesz vagy frissít. Az `null` eltávolításra beállított tulajdonságok törlődnek. Az alábbi példa egy új kívánt tulajdonságot hoz létre az értékkel `{"newProperty": "newValue"}` , felülírja a meglévő `existingProperty` értékét `"otherNewValue"` , és eltávolítja azt `otherOldProperty` . A meglévő kívánt tulajdonságok vagy címkék nem módosulnak:

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

* **Kettős értesítések fogadása**. Ez a művelet lehetővé teszi a megoldás háttérbeli értesítését, ha a Twin módosítva van. Ehhez a IoT-megoldásnak létre kell hoznia egy útvonalat, és az adatforrást meg kell egyeznie a *twinChangeEvents* értékkel. Alapértelmezés szerint a rendszer nem küld külön értesítéseket, azaz nem léteznek ilyen útvonalak. Ha a változás sebessége túl magas, vagy más okokból, például belső hibák esetén, a IoT Hub csak egy értesítést küldhet, amely az összes módosítást tartalmazza. Ezért, ha az alkalmazásnak az összes közbenső állapot megbízható naplózására és naplózására van szüksége, az eszközről a felhőbe irányuló üzeneteket kell használnia. A kettős értesítési üzenet tartalmazza a tulajdonságokat és a törzset.

  - Tulajdonságok

    | Name | Érték |
    | --- | --- |
    $content típusa | application/json |
    $iothub – enqueuedtime |  Az értesítés elküldésének ideje |
    $iothub – üzenet – forrás | twinChangeEvents |
    $content – kódolás | UTF-8 |
    deviceId | Az eszköz azonosítója |
    moduleId | A modul azonosítója |
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

Az összes korábbi művelet támogatja az [optimista párhuzamosságot](iot-hub-devguide-device-twins.md#optimistic-concurrency) , és megköveteli a **ServiceConnect** engedélyt, ahogyan azt a [hozzáférés vezérlése IoT hub](iot-hub-devguide-security.md) cikk határozza meg.

Ezen műveletek mellett a megoldás háttérbe állítása is lekérdezheti az ikrek modult az SQL-szerű [IoT hub lekérdezési nyelv](iot-hub-devguide-query-language.md)használatával.

## <a name="module-operations"></a>Modul műveletei

A modul-alkalmazás a következő atomi műveletek használatával működik a különálló modulon:

* **Modul beolvasása Twin**. Ez a művelet visszaadja a modul dupla dokumentumát (beleértve a címkéket és a kívánt és jelentett rendszertulajdonságokat) a jelenleg csatlakoztatott modulhoz.

* **Jelentett tulajdonságok részleges frissítése**. Ez a művelet lehetővé teszi a jelenleg csatlakoztatott modul jelentett tulajdonságainak részleges frissítését. Ez a művelet ugyanazt a JSON-frissítési formátumot használja, amelyet a megoldás hátterében a kívánt tulajdonságok részleges frissítése használ.

* A **kívánt tulajdonságok megfigyelése**. A jelenleg csatlakoztatott modul dönthet úgy, hogy értesítést küld a kívánt tulajdonságok frissítéseiről, amikor azok történnek. A modul a megoldás hátterében futtatott frissítés (részleges vagy teljes csere) azonos formáját kapja.

Az összes fenti művelethez szükség van a **ModuleConnect** engedélyre, ahogy [azt a hozzáférés-vezérlés IoT hub](iot-hub-devguide-security.md) cikk határozza meg.

Az [Azure IoT-eszközök SDK](iot-hub-devguide-sdks.md) -k megkönnyítik az előző műveletek használatát számos nyelvről és platformról.

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

## <a name="module-twin-size"></a>Modul mérete (Twin)

IoT Hub kényszeríti a 8 KB-os mérethatárt a értékre `tags` , és egy 32 KB-os mérethatárt a és a értékre `properties/desired` `properties/reported` . Ezek az összegek kizárólag a (z `$etag` ),, és rendszerű írásvédett elemekre vonatkoznak `$version` `$metadata/$lastUpdated` .

A Twin méret kiszámítása a következőképpen történik:

* A JSON-dokumentum minden tulajdonságához IoT Hub a kumulatív számításokat, és hozzáadja a tulajdonság kulcsának és értékének hosszát.

* A tulajdonságmezők UTF8-kódolású karakterláncnak tekintendők.

* Az egyszerű tulajdonságértékek UTF8-kódolású karakterláncoknak, numerikus értékeknek (8 bájt) vagy logikai értékeknek (4 bájt) tekintendők.

* Az UTF8-kódolású karakterláncok méretét az összes karakter számlálásával számítjuk ki, a UNICODE vezérlőkarakterek kivételével (szegmens C0 és C1).

* Az összetett tulajdonságértékek (beágyazott objektumok) kiszámítása az általuk tartalmazott tulajdonságértékek és tulajdonságértékek összesített mérete alapján történik.

IoT Hub elutasítja az összes olyan műveletet, amely a határértéknél nagyobb mértékben növelné a dokumentumok méretét.

## <a name="module-twin-metadata"></a>Modul – Twin metaadatok

IoT Hub megtartja az utolsó frissítés időbélyegét minden egyes JSON-objektumhoz a modul Twin kívánt és jelentett tulajdonságaiban. Az időbélyegek UTC szerint vannak elfoglalva, és [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) formátumban vannak kódolva `YYYY-MM-DDTHH:MM:SS.mmmZ` .
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
                    "sendFrequency": "5m",
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

A 2. és a bejelentett tulajdonságok nem rendelkeznek Etagek, de a `$version` növekményes érték garantált. A ETag hasonlóan a frissítési fél is használhatja a verziót a frissítések konzisztenciájának betartatására. Például egy jelentett tulajdonsághoz tartozó modul-alkalmazás, vagy a kívánt tulajdonsághoz tartozó megoldás háttérbeli vége.

A verziók akkor is hasznosak, ha egy megfigyelő ügynök (például a kívánt tulajdonságokat megfigyelő modul alkalmazás) összeegyezteti a versenyeket egy lekérési művelet és egy frissítési értesítés eredménye között. Az [eszköz újrakapcsolási folyamatának](iot-hub-devguide-device-twins.md#device-reconnection-flow) szakasza további információkat tartalmaz. 

## <a name="next-steps"></a>Következő lépések

A cikkben ismertetett fogalmak némelyikének kipróbálásához tekintse meg a következő IoT Hub oktatóanyagokat:

* [Ismerkedés a IoT Hub modul identitásával és a Twin modullal a .NET back end és a .NET eszköz használatával](iot-hub-csharp-csharp-module-twin-getstarted.md)
