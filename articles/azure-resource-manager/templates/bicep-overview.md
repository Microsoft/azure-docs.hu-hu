---
title: Bicep nyelv a Azure Resource Manager-sablonokhoz
description: Leírja az infrastruktúra Azure-beli üzembe helyezéséhez Azure Resource Manager sablonokon keresztül.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 74028c682b48a492c2e8f13bef538d1694370cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955907"
---
# <a name="what-is-bicep-preview"></a>Mi az a bicep (előzetes verzió)?

A bicep az Azure-erőforrások deklaratív üzembe helyezéséhez használt nyelv. A Azure Resource Manager-sablonok (ARM-sablonok) fejlesztéséhez JSON helyett a bicep-t használhatja. A bicep rövid szintaxissal egyszerűsíti a szerzői műveleteket, és jobb támogatást nyújt a kódok újrafelhasználásához, és javítja a típus biztonságát. A bicep egy tartományi-specifikus nyelv (DSL), ami azt jelenti, hogy egy adott forgatókönyvhöz vagy tartományhoz van tervezve. Nem az alkalmazások írására szolgáló általános programozási nyelv.

A sablon létrehozásához használt JSON-szintaxis részletesen és bonyolult kifejezéssel is megkövetelhető. A bicep a JSON-sablonok bármelyik funkciójának elvesztése nélkül javítja a felhasználói élményt. Ez egy transzparens absztrakt a JSON for ARM-sablonokhoz. Mindegyik bicep-fájl egy standard ARM-sablonhoz lett lefordítva. Az ARM-sablonban érvényes erőforrástípusok, API-verziók és tulajdonságok egy bicep-fájlban érvényesek. A jelenlegi kiadásban néhány [ismert korlátozás](#known-limitations) szerepel.

A bicep jelenleg előzetes verzióban érhető el. A munka állapotának nyomon követéséhez tekintse meg a [bicep Project adattárát](https://github.com/Azure/bicep).

A bicep-ról a következő videóban olvashat bővebben.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Bevezetés

A bicep elindításához [telepítse az eszközöket](bicep-install.md).

Az eszközök telepítése után próbálja ki a [bicep oktatóanyagot](./bicep-tutorial-create-first-bicep.md). Az oktatóanyag-Sorozat végigvezeti a bicep felépítésen és képességein. A bicep-fájlok üzembe helyezése és az ARM-sablon átalakítása az egyenértékű bicep-fájlba.

Az egyenértékű JSON-és bicep-fájlok egymás melletti megtekintéséhez tekintse meg a [bicep játszóteret](https://aka.ms/bicepdemo).

Ha van egy meglévő ARM-sablonja, amelyet a bicep-re szeretne átalakítani, olvassa el a [ARM-sablonok konvertálása JSON és a bicep között](bicep-decompile.md)című témakört.

## <a name="bicep-improvements"></a>A bicep fejlesztése

A bicep könnyebb és tömörebb szintaxist biztosít az egyenértékű JSON-hoz képest. Nem használ `[...]` kifejezéseket. Ehelyett közvetlenül hívja meg a függvényeket, és értékeket kell beolvasnia a paraméterekből és változókból. Minden üzembe helyezett erőforráshoz egy szimbolikus nevet kell adni, amely megkönnyíti a sablonban lévő erőforrások hivatkozását.

A következő JSON például egy erőforrás-tulajdonságból származó kimeneti értéket ad vissza.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

A bicep megjelenő egyenértékű kimeneti kifejezés könnyebben írható. A következő példa ugyanazt a tulajdonságot adja vissza egy, a sablonban definiált erőforrás szimbolikus neve **publicIP** használatával:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

A szintaxis teljes összehasonlítását lásd: [a JSON és a bicep a sablonok összehasonlítása](compare-template-syntax.md).

A bicep automatikusan kezeli az erőforrások közötti függőségeket. Elkerülheti a beállítást, `dependsOn` Ha egy erőforrás szimbolikus neve egy másik erőforrás-deklarációban van használatban.

A bicep a projekt több modulra is feltörhet.

A bicep-fájl szerkezete rugalmasabb, mint a JSON-sablon. A fájlokban bárhol deklarálhatja a paramétereket, a változókat és a kimeneteket. A JSON-ban be kell jelentenie az összes paramétert, változót és kimenetet a sablon megfelelő részein belül.

A bicep VS Code-bővítmények széles körű érvényesítést és intellisenseot biztosítanak. Használhatja például a bővítmény IntelliSense eszközét az erőforrások tulajdonságainak lekéréséhez.

## <a name="known-limitations"></a>Ismert korlátozások

A következő korlátok jelenleg léteznek:

* A Mode vagy a Batch méret nem állítható be a másolási hurokban.
* A hurkok és a feltételek nem kombinálhatók.
* Az egysoros objektumok és tömbök, például `['a', 'b', 'c']` nem támogatottak.

## <a name="faq"></a>GYIK

**Miért érdemes új nyelvet létrehozni egy meglévő használata helyett?**

Új nyelv helyett a meglévő ARM-sablon nyelvének változatát is megtekintheti a bicep. A szintaxis módosult, de az alapvető funkciók és a futtatókörnyezet változatlan marad.

A bicep fejlesztése előtt egy meglévő programozási nyelvet használunk. Úgy döntöttünk, hogy a célközönség könnyebben megtanulható a bicep, nem pedig egy másik nyelvvel való Ismerkedés.

**Miért nem koncentrálhat Terraform vagy más, harmadik féltől származó infrastruktúrára, mint a kód ajánlatára?**

A különböző felhasználók a különböző konfigurációs nyelveket és eszközöket részesítik előnyben. Szeretnénk gondoskodni arról, hogy az összes ilyen eszköz nagyszerű élményt nyújtson az Azure-ban. A bicep ennek a tevékenységnek a része.

Ha elégedett a Terraform, nincs ok a váltásra. A Microsoft elkötelezett amellett, hogy Terraform az Azure-ban a legjobb megoldás.

Úgy véljük, hogy az ARM-sablonokat kiválasztott ügyfeleknél a bicep a szerzői műveletek terén is javul. A bicep olyan ügyfelek esetében is segít, akik nem vezették be az infrastruktúrát programkódként.

**Csak az Azure-hoz készült a bicep?**

A bicep egy olyan DSL, amely teljes körű megoldásokat helyez üzembe az Azure-ban. A cél az Azure-on kívüli API-kkal való együttműködéshez szükséges. Arra számítunk, hogy bővíthető pontokat biztosítunk ezekhez a forgatókönyvekhez.

**Mi történik a meglévő ARM-sablonokkal?**

Továbbra is pontosan úgy működnek, ahogy mindig rendelkeznek. Semmilyen módosítást nem kell végeznie. Továbbra is támogatjuk az alapul szolgáló ARM-sablon JSON-nyelvét. A bicep-fájlok a JSON-be lettek lefordítva, és a JSON-t az Azure-ba történő telepítésre küldik.

Ha elkészült, [átalakíthatja a JSON-fájlokat a bicep](bicep-decompile.md).

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a [bicep oktatóanyaggal](./bicep-tutorial-create-first-bicep.md).
