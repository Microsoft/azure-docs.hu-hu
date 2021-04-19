---
title: Az eszközsablonok verziószámozásának ismertetése a Azure IoT Central alkalmazások | Microsoft Docs
description: Az eszközsablonok iterálása új verziók létrehozásával és az élő csatlakoztatott eszközök befolyásolása nélkül
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 03d4b0e43c9f692b90f4ab5d4d136dac92b74de6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715045"
---
# <a name="create-a-new-device-template-version"></a>Új eszközsablon-verzió létrehozása

*Ez a cikk a megoldáskészítőkre és az eszközfejlesztőkre vonatkozik.*

Az eszközsablonok olyan sémát tartalmaznak, amely leírja, hogyan kommunikálnak az eszközök IoT Central. Ezek az interakciók telemetriai adatokat, tulajdonságokat és parancsokat tartalmaznak. Az eszköz és a IoT Central is a séma közös megértésére támaszkodik az információk cseréjéhez. A sémát csak korlátozott mértékben módosíthatja a szerződés megszegése nélkül, ezért a legtöbb sémamódosításhoz az eszközsablon új verziójára van szükség. Az eszközsablon verziószámozása lehetővé teszi, hogy a régebbi eszközök továbbra is a már érthető sémaverziót használják, az újabb vagy frissített eszközök pedig egy újabb sémaverziót használjanak.

Az eszközsablonok sémája az eszközmodellben és annak interfészeiben van meghatározva. Az eszközsablonok más információkat is tartalmaznak, például a felhőtulajdonságokat, a megjelenítési testreszabásokat és a nézeteket. Ha módosítja az eszközsablonnak azokat a részeit, amelyek nem határozzák meg az eszköz és a IoT Central közötti adatcserét, nem kell verziószámmal látnia a sablont.

Mindig közzé kell tennie egy frissített eszközsablont, mielőtt az operátor használni tudja. IoT Central a sablon első verziószámozása nélkül nem lehet közzétenni a használékadó változásokat az eszközsablonon.

> [!NOTE]
> További információ az eszközsablonok létrehozásáról: [Eszközsablon beállítása és kezelése](howto-set-up-template.md)

## <a name="versioning-rules"></a>Verziószámozási szabályok

Ez a szakasz az eszközsablonok verziószámozási szabályait foglalja össze. Mind az eszközmodellek, mind a felületek verziószámmal rendelkezik. Az alábbi kódrészlet egy termosztátos eszköz eszközmodellét mutatja be. Az eszközmodell egyetlen felülettel rendelkezik. A verziószámot a mező végén `@id` láthatja.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

Ha ezt az információt a felhasználói felületen IoT Central meg, válassza az Identitás **megtekintése lehetőséget** az eszközsablon-szerkesztőben:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="Egy felület identitásának megtekintése a verziószám megtekintéséhez":::

Az alábbi lista azokat a szabályokat sorolja fel, amelyek meghatározzák, hogy mikor kell új verziót létrehoznia:

* Az eszközmodell közzététele után még az eszközsablon új verziójában sem távolíthat el felületeket.
* Az eszközmodell közzététele után hozzáadhat egy felületet, ha az eszközsablon új verzióját hozza létre.
* Az eszközmodell közzététele után lecserélheti a felületet egy újabb verzióra, ha az eszközsablon új verzióját hozza létre. Ha például az érzékelő v1-es eszközsablonja a termosztát v1 interfészt használja, létrehozhat egy érzékelő v2-es eszközsablont, amely a termosztát v2 interfészt használja.
* A felület közzététele után a felület tartalma még az eszközsablon új verziójában sem távolítható el.
* A felület közzététele után elemeket adhat hozzá a felület tartalmához, ha a felület és az eszközsablon új verzióját hozza létre. A felülethez hozzáadható elemek közé tartozik a telemetria, a tulajdonságok és a parancsok.
* A felület közzététele után nem sémamódosításokat is eszközsablonok esetén módosíthat a felület meglévő elemein. Egy felületelem nem séma részei közé tartozik a megjelenített név és a szemantikai típus. Egy felületelem nem változtatható sémarésze a név, a képesség típusa és a séma.

A következő szakaszok bemutatnak néhány példát az eszközsablonok módosítására a IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Az eszközsablon testreszabása verziószámozás nélkül

Az eszköz képességeinek bizonyos elemei az eszközsablon és a felületek verziószámozása nélkül szerkeszthetők. Ilyen mező például a megjelenített név, a szemantikai típus, a minimális érték, a maximális érték, a tizedesjegyek, a szín, az egység, a megjelenítési egység, a megjegyzés és a leírás. A következő testreszabások egyikének hozzáadásához:

1. Ugrás az **Eszközsablonok lapra.**
1. Válassza ki a testreszabni kívánt eszközsablont.
1. Válassza a **Testreszabás** lapot.
1. Itt található az eszközmodellben meghatározott összes képesség. Ezeket a mezőket szerkesztheti, mentheti és használhatja anélkül, hogy verziószámra lenne szüksége az eszközsablonhoz. Ha csak olvasható mezőket szeretne szerkeszteni, a módosításhoz verziószámmal kell módosítania az eszközsablont. Válassza ki a szerkeszteni kívánt mezőt, és adja meg az új értékeket.
1. Kattintson a **Mentés** gombra. Ezek az értékek most felülírnak mindent, ami eredetileg az eszközsablonban lett mentve, és az alkalmazáson keresztül lett használva.

## <a name="version-a-device-template"></a>Eszközsablon verziószáma

Az eszközsablon új verziójának létrehozásával létrejön a sablon vázlata, amelyben szerkeszthető az eszközmodell. A közzétett felületek közzététele addig tart, amíg külön verziószámmal nem rendelkezik. Közzétett felület módosításához először hozzon létre egy új eszközsablon-verziót.

Az eszközsablont csak akkor verziószámba kell venni, ha az eszközmodell egy olyan részét próbálja szerkeszteni, amely nem szerkeszthető a testreszabások szakaszban.

Eszközsablon verziószámba való verziója:

1. Ugrás az **Eszközsablonok oldalra.**
1. Válassza ki a verziószámként használni kívánt eszközsablont.
1. Válassza **az oldal** tetején található Verzió gombot, és adjon új nevet a sablonnak. IoT Central új nevet javasol, amelyet szerkeszthet.
1. Válassza a **Létrehozás** lehetőséget.
1. Az eszközsablon most piszkozat módban van. Láthatja, hogy az adapterek továbbra is zárolva vannak. A módosítani kívánt csatoló verziószáma.

## <a name="version-an-interface"></a>Felület verziószáma

A felületek verziószámozása lehetővé teszi képességek hozzáadását és frissítését a már létrehozott felületen.

Az interfész verziószámba való verziószáma:

1. Ugrás az **Eszközsablonok oldalra.**
1. Válassza ki a piszkozat módban található eszközsablont.
1. Válassza ki azt a közzétett módban elérhető felületet, amelyről verziószámokat szeretne szerkeszteni.
1. Válassza **a Verzió** gombot a felület oldalának tetején.
1. Válassza a **Létrehozás** lehetőséget.
1. A felület most piszkozat módban van. A meglévő testreszabások és nézetek feltörése nélkül adhat hozzá vagy szerkeszthet képességeket a felülethez.

## <a name="migrate-a-device-across-versions"></a>Eszköz áttelepítése verziók között

Az eszközsablon több verzióját is létrehozhatja. Idővel több csatlakoztatott eszközt fog használni ezekkel az eszközsablonokkal. Az eszközöket az eszközsablon egyik verziójából egy másikba is át lehet mirateálni. Az alábbi lépések az eszközök áttelepítését ismertetik:

1. Lépjen az **Eszközök** oldalra.
1. Válassza ki azt az eszközt, amelyről egy másik verzióra kell átemelni.
1. Válassza **az Áttelepítés lehetőséget:** :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="Válassza ki azt a lehetőséget, hogy elindítsa"::: az eszköz áttelepítését
1. Válassza ki azt az eszközsablont, amely tartalmazza azt a verziószámot, amelybe az eszközt át szeretné mikalálni, majd válassza az **Áttelepítés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

Ha Ön operátor vagy megoldáskészítő, a következő javasolt lépés az eszközök [kezelésének elsajátíta.](./howto-manage-devices.md)

Ha Ön eszközfejlesztő, a következő javasolt lépés a Azure IoT Edge és a [Azure IoT Central.](./concepts-iot-edge.md)
