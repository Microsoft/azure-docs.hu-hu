---
title: Modellek elemzése és ellenőrzése
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan elemezheti a DTDL-modelleket az elemző függvénytár használatával.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 155566a125485fda326f9f5e02d4aead0ffe30e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100560742"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Modellek elemzése és ellenőrzése a DTDL Parser Library-vel

Az Azure Digital Twins [modelljei](concepts-models.md) a JSON-ld-alapú digitális Twins Definition Language (DTDL) használatával vannak meghatározva. **Javasoljuk, hogy az Azure Digital Twins-példányba való feltöltés előtt ellenőrizze a modelleket offline állapotba.**

Ennek elvégzéséhez .NET ügyféloldali DTDL-elemzési függvénytár van megadva a következő NuGet: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

Az elemző függvénytárat közvetlenül a C#-kódban is használhatja, vagy használhatja a Language-agnosztikus kód minta projektjét, amely az elemző könyvtárra épül: [**DTDL validator minta**](/samples/azure-samples/dtdl-validator/dtdl-validator).

## <a name="use-the-dtdl-validator-sample"></a>A DTDL-érvényesítő minta használata

A [**DTDL-érvényesítő**](/samples/azure-samples/dtdl-validator/dtdl-validator) olyan minta projekt, amely a DTDL érvényességének ellenőrzésére képes a modell dokumentumainak ellenőrzéséhez. A szolgáltatás a .NET Parser Library-re épül, és nyelvtől független. A minta hivatkozásnál a *zip letöltése* gombra kattintva kérheti le.

A forráskód példákat mutat be az elemző függvénytár használatára. Az érvényesítő mintát parancssori segédprogramként használhatja a DTDL-fájlok címtár-fájának ellenőrzéséhez. Emellett interaktív módot is biztosít.

A DTDL-érvényesítő minta mappájába tekintse meg a *readme.MD* -fájlt, amely útmutatást nyújt a minta önálló végrehajtható fájlba való csomagolásához.

Miután létrehozott egy önálló csomagot, és hozzáadta a végrehajtható fájlt az elérési úthoz, a következő paranccsal futtathatja a validatort egy konzolon a gépen:

```cmd/sh
DTDLValidator
```

Az alapértelmezett beállításokkal a minta az `*.json` aktuális könyvtárban és az összes alkönyvtárban található fájlokat fogja keresni. Azt is megteheti, hogy a következő lehetőséget is hozzáadja a minta kereséséhez a jelzett könyvtárban és a kiterjesztésű fájlok összes alkönyvtárában *. dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

A `-i` minta beállítását az interaktív mód megadására is felveheti:

```cmd/sh
DTDLValidator -i
```

A mintával kapcsolatos további információkért tekintse meg a forráskódot, vagy futtassa a parancsot `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>A .NET Parser Library használata 

A [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) függvénytár modell hozzáférést biztosít a DTDL-definícióhoz, amely lényegében a DTDL C#-reflexiójának megfelelőként működik. Ez a könyvtár az [Azure Digital Twins SDK](how-to-use-apis-sdks.md)-val függetlenül is használható, különösen a DTDL érvényesítéséhez egy vizualizációban vagy szövegszerkesztőben. A modell-definíciós fájlok érvényességének biztosításához hasznos, mielőtt feltölti őket a szolgáltatásba.

Az elemző függvénytár használatához a DTDL-dokumentumok készletét adja meg. Ezeket a modelleket általában a szolgáltatásból kell lekérnie, de előfordulhat, hogy helyileg is elérhetővé válik, ha az ügyfél felelős volt a szolgáltatásnak az első helyen való feltöltéséhez. 

Itt látható az elemző használatának általános munkafolyamata:
1. Egy vagy több DTDL-dokumentum beolvasása a szolgáltatásból.
2. Adja át a visszaadott, memóriában tárolt DTDL-dokumentumokat az elemzőnek.
3. Az elemző ellenőrzi az átadott dokumentumok készletét, és részletes hibaüzeneteket ad vissza. Ez a képesség szerkesztői helyzetekben hasznos.
4. A dokumentumkészlet által tartalmazott modellek elemzésének folytatásához használja az elemző API-kat. 

Az elemző képességei a következők:
* Szerezze be az összes megvalósított modell felületét (az interfész `extends` szakaszának tartalmát).
* A modellben deklarált összes tulajdonság, telemetria, parancs, összetevő és kapcsolat beolvasása. Ez a parancs a definíciókban szereplő összes metaadatot is beolvassa, és figyelembe veszi az öröklést ( `extends` szakaszt).
* Az összes összetett modell definíciójának beolvasása.
* Annak megállapítása, hogy egy modell kiosztható-e egy másik modellből.

> [!NOTE]
> A [IoT Plug and Play-(PNP-)](../iot-pnp/overview-iot-plug-and-play.md) eszközök kis szintaxist használnak a funkciók leírására. Ez a szintaxis az Azure digitális Ikrekben használt DTDL szemantikailag kompatibilis részhalmaza. Az elemző függvénytár használatakor nem kell tudnia, hogy melyik szintaxis-változatot használták a DTDL létrehozásához a digitális Twin számára. Alapértelmezés szerint az elemző a PnP és az Azure Digital Twins szintaxisának is ugyanazt a modellt fogja visszaadni.

### <a name="code-with-the-parser-library"></a>Kód az elemző könyvtárral

Az elemzési függvénytárat közvetlenül is használhatja olyan dolgokhoz, mint például a saját alkalmazásban lévő modellek ellenőrzése, vagy dinamikus, modell-vezérelt felhasználói felület, irányítópultok és jelentések létrehozása.

Az alábbi elemzési kód támogatásához vegye fontolóra az Azure Digital Twins-példányban definiált több modellt:

:::code language="json" source="~/digital-twins-docs-samples/models/coffeeMaker-coffeeMakerInterface-coffeeBar.json":::

Az alábbi kód bemutatja, hogyan használható az elemző függvénytár a következő definíciók megjelenítéséhez a C#-ban:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/parseModels.cs":::

## <a name="next-steps"></a>Következő lépések

Ha befejezte a modellek írását, tekintse meg az DigitalTwinsModels API-k használatával történő feltöltését ismertető témakört (és végezze el a többi felügyeleti műveletet):
* [*Útmutató: DTDL-modellek kezelése*](how-to-manage-model.md)