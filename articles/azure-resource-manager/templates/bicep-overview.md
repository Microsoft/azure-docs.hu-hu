---
title: Bicep nyelv a Azure Resource Manager-sablonokhoz
description: Leírja az infrastruktúra Azure-beli üzembe helyezéséhez Azure Resource Manager sablonokon keresztül.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 6a2750dc99e82c9cf8c9b8b97d156d3a9fe30f31
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746073"
---
# <a name="what-is-bicep-preview"></a>Mi az a bicep (előzetes verzió)?

A bicep az Azure-erőforrások deklaratív üzembe helyezéséhez használt nyelv. Rövid szintaxissal egyszerűsíti a szerzői élményt, és jobb támogatást nyújt a modularitás és a kód újrafelhasználásához. A bicep egy tartományi-specifikus nyelv (DSL), ami azt jelenti, hogy egy adott forgatókönyvhöz vagy tartományhoz van tervezve. A bicep nem az alkalmazások írására szolgáló általános programozási nyelv.

A bicep a Azure Resource Manager-sablonok (ARM-sablonok) transzparens absztrakciója. Mindegyik bicep-fájl egy standard ARM-sablonhoz lett lefordítva. Az ARM-sablonban érvényes erőforrástípusok, API-verziók és tulajdonságok egy bicep-fájlban érvényesek.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-started"></a>Bevezetés

A bicep elindításához [telepítse az eszközöket](https://github.com/Azure/bicep/blob/main/docs/installing.md).

Az eszközök telepítése után próbálja ki a [bicep oktatóanyagot](./bicep-tutorial-create-first-bicep.md). Az oktatóanyag-Sorozat végigvezeti a bicep felépítésen és képességein. A bicep-fájlok üzembe helyezése és az ARM-sablon átalakítása az egyenértékű bicep-fájlba.

Az egyenértékű JSON-és bicep-fájlok egymás melletti megtekintéséhez tekintse meg a [bicep játszóteret](https://aka.ms/bicepdemo).

Ha van egy meglévő ARM-sablonja, amelyet a bicep-re szeretne átalakítani, tekintse meg a [JSON defordítása a bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="bicep-improvements"></a>A bicep fejlesztése

A bicep könnyebb és tömörebb szintaxist biztosít az egyenértékű JSON-hoz képest. Nem használ `[...]` kifejezéseket. Ehelyett közvetlenül a függvények hívása, a paraméterek és változók értékeinek beolvasása, valamint az erőforrások hivatkozása. A szintaxis teljes összehasonlítását lásd: [a JSON és a bicep a sablonok összehasonlítása](compare-template-syntax.md).

A bicep automatikusan kezeli az erőforrások közötti függőségeket. Elkerülheti a beállítást, `dependsOn` Ha egy erőforrás szimbolikus neve egy másik erőforrás-deklarációban van használatban.

A bicep a projekt több modulra is feltörhet.

A bicep-fájl szerkezete rugalmasabb, mint a JSON-sablon. A fájlokban bárhol deklarálhatja a paramétereket, a változókat és a kimeneteket. A JSON-ban be kell jelentenie az összes paramétert, változót és kimenetet a sablon megfelelő részein belül.

A bicep VS Code-bővítmény gazdagabb ellenőrzés és IntelliSense szolgáltatást nyújt. Például a bővítmény IntelliSense-vel rendelkezik az erőforrások tulajdonságainak beolvasásához.

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

Ha elkészült, [átalakíthatja a JSON-fájlokat a bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a [bicep oktatóanyaggal](./bicep-tutorial-create-first-bicep.md).
