---
title: Az Azure IoT Central-alkalmazások eszköz-sablon verziószámozásának ismertetése | Microsoft Docs
description: Új verziók létrehozásával, valamint az élő csatlakoztatott eszközök hatása nélkül megismételheti az eszközök sablonjait
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: 2025b127a428afa478cfe839c7619df2d7d688d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90015959"
---
# <a name="create-a-new-device-template-version"></a>Új sablon-verzió létrehozása

*Ez a cikk a megoldás-építők és az eszközök fejlesztőire vonatkozik.*

Az eszköz sablonja tartalmaz egy sémát, amely leírja, hogyan működik az eszköz a IoT Central. Ezek az interakciók közé tartoznak a telemetria, a tulajdonságok és a parancsok. Az eszköz és a IoT Central alkalmazás is a séma közös megismerésére támaszkodik az információk cseréjére. Csak korlátozott módosításokat végezhet a sémában a szerződés megszakítása nélkül, ezért a legtöbb séma-módosításhoz szükség van az eszköz sablonjának új verziójára. Az eszköz verziószámozása lehetővé teszi, hogy a régebbi eszközök továbbra is a séma verziószámát használják, míg az újabb vagy frissített eszközök újabb verziójú sémát használnak.

Az eszközbeállítások sémája az eszköz képességeinek modellje (DCM) és a hozzá tartozó felületeken van meghatározva. Az eszközök sablonjai más információkat is tartalmaznak, például a felhő tulajdonságait, a testreszabások megjelenítését és a nézeteket. Ha módosítja az eszköz azon részeit, amelyek nem határozzák meg, hogy az eszköz hogyan cseréli az adatcserét IoT Central, nem szükséges a sablon verziója.

Ahhoz, hogy a kezelő használhassa a verziót, közzé kell tennie minden olyan sablont, amely a verzió frissítésére is szükség van. IoT Central leállítja, hogy a sablon első verziószámozása nélkül tegye közzé az eszköz sablonjában feltörhető módosításokat.

> [!NOTE]
> Az eszközök létrehozásával kapcsolatos további tudnivalókért tekintse meg az [eszköz sablonjának beállítása és kezelése](howto-set-up-template.md) című témakört.

## <a name="versioning-rules"></a>Verziószámozási szabályok

Ez a szakasz az eszközök sablonjaira vonatkozó verziószámozási szabályokat összegzi. A DCMs és a illesztőfelületek verziószáma is szerepel. A következő kódrészlet a DCM-et mutatja be egy környezeti érzékelő eszközhöz. A DCM két csatolóval rendelkezik: **DeviceInformation** és **EnvironmentalSensor**. A verziószámok a mezők végén láthatók `@id` . Ha meg szeretné tekinteni ezeket az információkat a IoT Central felhasználói felületen, válassza az **identitás megtekintése** lehetőséget az eszköz sablonjának szerkesztőjében.

```json
{
  "@id": "urn:contoso:sample_device:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "@id": "urn:contoso:sample_device:deviceinfo:1",
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": {
          "en": "Device Information"
        },
        "contents": [...
        ]
      }
    },
    {
      "@id": "urn:contoso:sample_device:sensor:1",
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:2",
        "@type": "Interface",
        "displayName": {
          "en": "Environmental Sensor"
        },
        "contents": [...
        ]
      }
    }
  ],
  "displayName": {
    "en": "Environment Sensor Capability Model"
  },
  "@context": [
    "http://azureiot.com/v1/contexts/IoTModel.json"
  ]
}
```

* A DCM közzététele után nem távolíthat el semmilyen felületet, még az eszköz sablonjának új verziójában sem.
* A DCM közzététele után hozzáadhat egy felületet, ha létrehoz egy új verziót az eszköz sablonjában.
* A DCM közzététele után lecserélheti a felületet egy újabb verzióra, ha az eszköz új verzióját hozza létre. Ha például az érzékelő v1-es eszköze a EnvironmentalSensor v1 felületet használja, létrehozhat egy, az EnvironmentalSensor v2 felületet használó Sensor v2-eszköz sablonját.
* Egy felület közzététele után nem távolíthatja el az interfészek tartalmát, még az eszköz sablonjának új verziójában sem.
* Egy felület közzétételét követően hozzáadhat elemeket egy felület tartalmához, ha új verziót hoz létre az adapterhez és az eszköz sablonhoz. Az illesztőfelületbe felvehető elemek közé tartoznak a telemetria, a tulajdonságok és a parancsok.
* Miután közzétett egy felületet, a nem sémán belüli módosításokat is elvégezheti az illesztőfelület meglévő elemein, ha új verziót hoz létre az adapter és az eszköz sablonjában. Egy illesztőfelület nem sémájának részei közé tartozik a megjelenítendő név és a szemantikai típus. Egy olyan illesztőfelület-elem sémájának részei, amelyet nem lehet módosítani, név, képesség típusa és séma.

Az alábbi szakaszokban néhány példát ismertetünk az IoT Centralban található eszközök sablonjainak módosítására.

## <a name="customize-the-device-template-without-versioning"></a>Az eszköz-sablon testreszabása verziószámozás nélkül

Az eszköz képességeinek bizonyos elemei szerkeszthetők, anélkül, hogy az eszköz sablonját és felületét kellene megadnia. Ilyen mezők például a megjelenítendő név, a szemantikai típus, a minimális érték, a maximális érték, a tizedesjegyek, a szín, az egység, a megjelenítési egység, a Megjegyzés és a leírás. A következő testreszabások egyikének hozzáadása:

1. Nyissa meg az **eszközök sablonjai** lapot.
1. Válassza ki a testreszabni kívánt sablont.
1. Válassza a **Testreszabás** lapot.
1. Itt jelennek meg az eszköz képességeinek modelljében meghatározott összes funkció. Az összes mezőt szerkesztheti, mentheti és használhatja, az eszköz sablonjának verziószámozása nélkül. Ha olyan mezőket szeretne szerkeszteni, amelyek csak olvashatók, akkor az eszköz sablonját kell megváltoztatnia. Válasszon ki egy szerkeszteni kívánt mezőt, és írjon be minden új értéket.
1. Kattintson a **Mentés** gombra. Mostantól ezek az értékek felülbírálják az eszköz sablonjában eredetileg mentett és az alkalmazáson keresztül használt összes értéket.

## <a name="version-a-device-template"></a>Eszköz sablonjának verziója

Az eszköz új verziójának létrehozása létrehoz egy Piszkozat-verziót a sablonból, amely az eszköz képességeinek modelljét szerkesztheti. A közzétett felületek minden esetben közzé maradnak, amíg az önálló verzió nem lesz. A közzétett illesztőfelületek módosításához először hozzon létre egy új sablon-verziót.

Csak akkor használja az eszköz sablonját, ha a testreszabások szakaszban nem szerkeszthető eszköz-képesség modell egy részét próbálja szerkeszteni.

Eszköz sablonjának verziója:

1. Nyissa meg az **eszközök sablonjai** lapot.
1. Válassza ki azt az eszközt, amelyen a verziót szeretné.
1. Kattintson a lap tetején található **Version (verzió** ) gombra, és adjon meg egy új nevet a sablonnak. IoT Central egy új nevet javasol, amelyet szerkeszthet.
1. Kattintson a **Létrehozás** lehetőségre.
1. Most az eszköz sablonja vázlat módban van. Láthatja, hogy a felületek még mindig zárolva vannak. A módosítani kívánt felületek verziója.

## <a name="version-an-interface"></a>Illesztőfelület verziója

Az interfészek verziószámozása lehetővé teszi a már létrehozott felületen belüli képességek hozzáadását, frissítését és eltávolítását.

Illesztőfelület verziója:

1. Nyissa meg az **eszközök sablonjai** lapot.
1. Válassza ki a Piszkozat módban lévő sablont.
1. Válassza ki azt a felületet, amelyet közzé szeretne tenni, és szerkeszteni kívánja a közzétett módban.
1. Kattintson a **verzió** gombra a csatoló oldal tetején.
1. Kattintson a **Létrehozás** lehetőségre.
1. Most az illesztőfelület vázlat módban van. A meglévő testreszabások és nézetek megszakítása nélkül hozzáadhat vagy szerkesztheti a felület képességeit.

## <a name="migrate-a-device-across-versions"></a>Eszköz migrálása a verziók között

Az eszköz sablonjának több verzióját is létrehozhatja. Idővel több csatlakoztatott eszközt fog használni ezekhez az eszközökhöz. Az eszközöket áttelepítheti az eszköz sablonjának egyik verziójából egy másikba. Az alábbi lépések bemutatják, hogyan telepíthet át egy eszközt:

1. Lépjen a **Device Explorer** lapra.
1. Válassza ki az eszközt, amelyet át kell telepítenie egy másik verzióra.
1. Válassza az **áttelepítés**lehetőséget.
1. Válassza ki azt a verziószámot, amelyen át szeretné telepíteni az eszközt, majd válassza az **áttelepítés**lehetőséget.

![Eszköz áttelepítésének módja](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Következő lépések

Ha Ön egy operátor vagy megoldás-szerkesztő, a következő lépés az [eszközök felügyeletének](./howto-manage-devices.md)megismerése.

Ha Ön egy eszköz fejlesztője, a következő lépés az [Azure IoT Edge-eszközök és az Azure-IoT Central](./concepts-iot-edge.md)beolvasása.
