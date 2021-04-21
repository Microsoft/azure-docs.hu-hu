---
title: Bicep nyelv a Azure Resource Manager sablonokhoz
description: Az infrastruktúra Azure-ban való üzembe helyezésének bicep nyelvét ismerteti Azure Resource Manager sablonokon keresztül.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: af207e6ca88eab50fe6030883379c87c0ec05691
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773746"
---
# <a name="what-is-bicep-preview"></a>Mi az a Bicep (előzetes verzió)?

A Bicep az Azure-erőforrások deklaratív üzembe helyezésének nyelve. A sablonsablonok (ARM-sablonok) fejlesztéséhez JSON helyett a Bicep Azure Resource Manager használhatja. A Bicep leegyszerűsíti a szerzői élményt azáltal, hogy tömör szintaxist, a kód újrafelhasználásának jobb támogatását és a jobb típusbiztonságot biztosítja. A Bicep egy tartományspecifikus nyelv (DSL), ami azt jelenti, hogy egy adott forgatókönyvhöz vagy tartományhoz tervezték. Nem az alkalmazások írásának általános programnyelveként szolgál.

A sablon létrehozására vonatkozó JSON-szintaxis lehet részletes, és bonyolult kifejezést igényel. A Bicep anélkül javítja ezt a felhasználói élményt, hogy elveszítené egy JSON-sablon képességeit. Ez egy transzparens absztrakció az ARM-sablonok JSON-ján. Minden Bicep-fájl egy szabványos ARM-sablonra van lefordítva. Az ARM-sablonokban érvényes erőforrástípusok, API-verziók és tulajdonságok érvényesek egy Bicep-fájlban. Az aktuális [kiadásnak van](#known-limitations) néhány ismert korlátja.

A Bicep jelenleg előzetes verzióban érhető el. A munka állapotának nyomon követéséhez tekintse meg a [Bicep-projekt adattárát.](https://github.com/Azure/bicep)

A Bicepről az alábbi videóban olvashat bővebben.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Bevezetés

A Bicep-hez először telepítse [a következő eszközöket:](bicep-install.md).

Az eszközök telepítése után próbálja ki a [Bicep-oktatóanyagot.](./bicep-tutorial-create-first-bicep.md) Az oktatóanyag-sorozat végigvezeti a Bicep felépítésén és képességein. A Bicep-fájlokat üzembe kell helyeznie, és az ARM-sablont az egyenértékű Bicep-fájlvá kell konvertálni.

Az egyenértékű JSON- és Bicep-fájlok egymás mellett történő megtekintéséhez tekintse meg a [Bicep-játszótereket.](https://aka.ms/bicepdemo)

Ha van egy meglévő ARM-sablonja, amelyből Bicep-sablont szeretne létrehozni, tekintse meg az ARM-sablonok JSON és Bicep közötti [konvertálását.](bicep-decompile.md)

## <a name="benefits-of-bicep-versus-other-tools"></a>A Bicep és más eszközök előnyei

A Bicep a következő előnyöket biztosítja az egyéb lehetőségekhez:

* **Támogatás az összes erőforrástípushoz és API-verzióhoz:** A Bicep azonnal támogatja az Azure-szolgáltatások összes előzetes és általánosan elérhető verzióját. Amint egy erőforrás-szolgáltató új erőforrástípusokat és API-verziókat vezet be, használhatja őket a Bicep-fájlban. Az új szolgáltatások használata előtt nem kell megvárni az eszközök frissítését.
* **Szerzői élmény:** Ha a VS Code használatával hozza létre a Bicep-fájlokat, első osztályú szerzői élményt kap. A szerkesztő részletes típusbiztonságot, intelliSense-t és szintaxis-ellenőrzést biztosít.
* **Modularitás:** A modulokkal kezelhető részekre törheti a [Bicep-kódot.](bicep-modules.md) A modul egy kapcsolódó erőforráskészletet helyez üzembe. A modulok lehetővé teszik a kód újbóli felhasználását és a fejlesztés egyszerűsítését. A modult bármikor hozzáadhatja egy Bicep-fájlhoz, ha üzembe kell helyeznie ezeket az erőforrásokat.
* **Integráció az Azure-szolgáltatásokkal:** A Bicep olyan Azure-szolgáltatásokkal van integrálva, mint Azure Policy, a sablonok specifikációi és a Blueprints.
* **Nincs kezelve állapot- vagy állapotfájl:** Az Összes állapot az Azure-ban van tárolva. A felhasználók együttműködhetnek, és biztos lehet abban, hogy a frissítéseket a várt módon kezelik. A [what-if művelettel megtekintheti](template-deploy-what-if.md) a módosítások előnézetét a sablon üzembe helyezése előtt.
* **Nincs költség és nyílt forráskódú:** A Bicep teljesen ingyenes. A prémium szintű képességekért nem kell fizetnie. A Microsoft ügyfélszolgálata is támogatja.

## <a name="bicep-improvements"></a>Bicep-fejlesztések

A Bicep egyszerűbb és tömörebb szintaxist kínál az egyenértékű JSON-hoz képest. Nem használ `[...]` kifejezéseket. Ehelyett közvetlenül függvényeket hívhat meg, és paraméterekből és változókból is lekért értékeket. Minden üzembe helyezett erőforrásnak szimbolikus nevet ad, így könnyen hivatkozhat rá a sablonban.

Az alábbi JSON például egy kimeneti értéket ad vissza egy erőforrás-tulajdonságból.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

A Bicep megfelelő kimeneti kifejezését könnyebb megírni. Az alábbi példa ugyanazt a tulajdonságot adja vissza a **publicIP** szimbolikus név használatával a sablonban definiált erőforráshoz:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

A szintaxis teljes összehasonlítását lásd: [Comparing JSON and Bicep for templates (A JSON és a Bicep összehasonlítása sablonokhoz).](compare-template-syntax.md)

A Bicep automatikusan kezeli az erőforrások közötti függőségeket. Ha egy erőforrás szimbolikus nevét egy másik erőforrás-deklarációban használja, elkerülheti a `dependsOn` beállítást.

A Bicep-fájl szerkezete rugalmasabb, mint a JSON-sablon. Paramétereket, változókat és kimeneteket a fájlban bárhol deklarálhat. A JSON-ban deklarálni kell a sablon megfelelő szakaszaiban található összes paramétert, változót és kimenetet.

## <a name="known-limitations"></a>Ismert korlátozások

Jelenleg a következő korlátok létezik:

* Másolási hurkok esetén nem lehet beállítani a módot vagy a kötegméretet.
* A hurkok és feltételek nem kombinálhatóak.
* Az egysoros objektumok és tömbök, például a nem `['a', 'b', 'c']` támogatottak.

## <a name="faq"></a>GYIK

**Miért érdemes új nyelvet létrehozni meglévő helyett?**

A Bicep a meglévő ARM-sablonnyelv változatának is használhatja, nem pedig új nyelvnek. A szintaxis megváltozott, de az alapvető funkciók és a futásidő változatlanok maradnak.

A Bicep fejlesztése előtt egy meglévő programozási nyelv használatával tekintettünk át. Úgy döntöttünk, hogy a célközönség könnyebb lesz a Bicep elsajátításában, mint egy másik nyelvvel való ismerkedésben.

**Miért ne összpontosítson a Terraformra vagy más külső infrastruktúra kódajánlatra?**

A különböző felhasználók a különböző konfigurációs nyelveket és eszközöket részesítik előnyben. Szeretnénk biztosítani, hogy az összes eszköz nagyszerű élményt nyújtson az Azure-ban. A Bicep része ennek az erőfeszítésnek.

Ha elégedett a Terraform használatával, nincs ok a váltásra. A Microsoft elkötelezett amellett, hogy a Lehető legjobb Terraformot használja az Azure-ban.

Az ARM-sablonokat kijelölve a Bicep használata javítja a szerzői élményt. A Bicep segít az olyan ügyfelek áttérésében is, akik még nem fogadták el kódként az infrastruktúrát.

**A Bicep csak az Azure-ban van?**

A Bicep egy olyan DSL, amely teljes megoldások Azure-beli üzembe helyezésére összpontosít. Ennek a célnak az eléréséhez az Azure-n kívüli API-okkal kell dolgozni. Arra számítunk, hogy ezekhez a forgatókönyvekhez meg kell adni a szükséges extenzitási pontokat.

**Mi történik a meglévő ARM-sablonokkal?**

Továbbra is pontosan úgy működnek, mint mindig. Nem kell módosításokat tennie. Továbbra is támogatjuk a mögöttes ARM-sablon JSON-nyelvét. A BICEP-fájlok JSON-fájlba vannak fordítva, és ezt a JSON-t a rendszer elküldi az Azure-nak üzembe helyezésre.

Ha készen áll, átalakíthatja a [JSON-fájlokat Bicep formátumba.](bicep-decompile.md)

## <a name="next-steps"></a>Következő lépések

A [Bicep oktatóanyag – első lépések.](./bicep-tutorial-create-first-bicep.md)
