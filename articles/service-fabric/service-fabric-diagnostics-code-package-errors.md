---
title: A kódok gyakori hibáinak diagnosztizálása Service Fabric használatával
description: Útmutató az Azure-Service Fabric
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 400651b240f0518a85b9deb7a7293a77a88b0861
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591715"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>A kódok gyakori hibáinak diagnosztizálása Service Fabric használatával

Ez a cikk azt mutatja be, hogy a kód-csomagok váratlanul leállnak. Betekintést nyújt a gyakori hibakódok lehetséges okaira, valamint a hibaelhárítási lépésekkel.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Mikor szakad meg a folyamat vagy a tároló váratlanul?

Amikor az Azure Service Fabric a kód elindítására vonatkozó kérést kap, a környezet előkészítése a helyi rendszeren az alkalmazás és a szolgáltatás jegyzékfájljában megadott beállításoknak megfelelően kezdődik. Ezek a készítmények tartalmazhatják a hálózati végpontokat vagy erőforrásokat, a tűzfalszabályok konfigurálását vagy az erőforrás-irányítási kényszerek beállítását. 

A környezet megfelelő konfigurálását követően a Service Fabric megkísérli a kód kiépítését. Ez a lépés akkor tekinthető sikeresnek, ha az operációs rendszer vagy a tároló futásidejű jelentése szerint a folyamat vagy a tároló sikeresen aktiválva lett. Ha az aktiválás sikertelen, a következőhöz hasonló állapotú üzenetnek kell megjelennie az SFX-ben:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

A kód sikeres aktiválása után Service Fabric megkezdi az élettartamának figyelését. Ezen a ponton egy folyamat vagy tároló bármikor leállhat számos okból. Előfordulhat például, hogy nem tudta inicializálni a DLL-t, vagy az operációs rendszer elfogyott az asztali tárterületen. Ha a kód csomagja meg lett szakítva, a következő állapotadatok jelennek meg az SFX-ben:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Ebben az állapotfigyelő üzenetben a kilépési kód az egyetlen nyom, amelyet a folyamat vagy a tároló biztosít a leállításának okát illetően. A verem bármely szintjéből létrehozható. Előfordulhat például, hogy ez a kilépési kód egy operációsrendszer-hibához vagy egy .NET-problémához kapcsolódik, vagy a kód felváltotta azt. Ez a cikk kiindulási pontként használható a megszűnési kódok és a lehetséges megoldások forrásának diagnosztizálásához. Ne feledje azonban, hogy ezek általános megoldásokat jelentenek a gyakori forgatókönyvekhez, és előfordulhat, hogy nem vonatkoznak a megjelenő hibára.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Honnan tudhatom meg, hogy Service Fabric megszakította-e a kód csomagot?

Előfordulhat, hogy a Service Fabric a különböző okok miatt a kód leállításához felelős. Dönthet például úgy, hogy a csomagot egy másik csomóponton helyezi el terheléselosztási célokra. Ha a következő táblázatban látható kilépési kódokat látja, ellenőrizheti, hogy Service Fabric leállította-e a kódot.

>[!NOTE]
> Ha a folyamat vagy a tároló a következő táblázatban szereplő kódoktól eltérő kilépési kóddal leáll, Service Fabric nem felelős a megszakításért.

Kilépési kód | Description
--------- | -----------
7147 | Azt jelzi, hogy Service Fabric szabályosan leállítja a folyamatot vagy a tárolót úgy, hogy a CTRL + C jelet küldi el.
7148 | Azt jelzi, hogy Service Fabric megszakította a folyamatot vagy a tárolót. Előfordulhat, hogy ez a hibakód azt jelzi, hogy a folyamat vagy a tároló nem válaszolt kellő időben a CTRL + C jel elküldése után, és meg kellett szakítani.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Egyéb gyakori hibakódok és lehetséges javítások

Kilépési kód | Hexadecimális érték | Rövid leírás | Gyökérok | Lehetséges javítás
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Ez a hiba esetenként azt jelenti, hogy a gép elfogyott az asztali tárolóhelyen. Ez különösen akkor valószínű, ha számos olyan folyamata van, amely a csomóponton futó alkalmazáshoz tartozik. | Ha a program nem úgy lett felépítve, hogy válaszoljon a CTRL + C jelekre, akkor engedélyezheti a **EnableActivateNoWindow** beállítást a fürt jegyzékfájljában. Ha engedélyezi ezt a beállítást, a kód csomagja nem grafikus felhasználói felülettel fog futni, és nem kap CTRL + C jeleket. Ez a művelet csökkenti az egyes folyamatok által felhasznált asztali tárolóhelyek mennyiségét is. Ha a kód csomagjának a CTRL + C jeleket kell kapnia, növelheti a csomópont asztali kupacjának méretét.
3762504530 | 0xe0434352 | N/A | Ez az érték a felügyelt kódból (azaz .NET) származó kezeletlen kivétel hibakódját jelöli. | Ez a kilépési kód azt jelzi, hogy az alkalmazás olyan kivételt váltott ki, amely kezeletlen marad, és amely megszakította a folyamatot. A hiba kiváltásának első lépéseként hibakeresést végezhet az alkalmazás naplófájljaiban és a fájlok kiírásakor.

## <a name="next-steps"></a>Következő lépések

* További információ a [gyakori forgatókönyvek diagnosztizálásáról](service-fabric-diagnostics-common-scenarios.md).
* Tekintse át a Azure Monitor naplók részletes áttekintését és a [Azure monitor áttekintését ismertető témakört](../azure-monitor/overview.md).
* További információ az észlelési és diagnosztikai támogatásokról Azure Monitor naplók [riasztásáról](../azure-monitor/alerts/alerts-overview.md) .
* Ismerkedjen meg Azure Monitor naplók részeként kínált [naplóbeli keresési és lekérdezési](../azure-monitor/logs/log-query-overview.md) funkciókkal.
