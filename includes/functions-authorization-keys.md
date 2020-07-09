---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 046bd8fcbb8fab50269c8d35da0956bdc63f2304
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85298599"
---
A függvények lehetővé teszik a kulcsok használatát, hogy a fejlesztés során megnehezíti a HTTP-függvény végpontjának elérését. Ha a http-hozzáférési szint nem a HTTP-triggert használó függvényre van beállítva `anonymous` , a kérelmeknek tartalmaznia kell egy API-hozzáférési kulcsot a kérelemben. 

Míg a kulcsok alapértelmezett biztonsági mechanizmust biztosítanak, érdemes lehet további lehetőségeket használni a HTTP-végpontok biztonságossá tételéhez az éles környezetben. Például általában nem jó megoldás a közös titkos kulcs nyilvános alkalmazásokban való terjesztésére. Ha a függvényt nyilvános ügyfél hívja meg, érdemes fontolóra venni egy másik secrity mechanizmus megvalósítását. További információ: [http-végpont biztonságossá tétele éles](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production)környezetben.

Amikor megújítja a funkcióbillentyűk értékeit, manuálisan újra kell terjesztenie a frissített kulcs értékeit a függvényt meghívó összes ügyfélre.  

#### <a name="authorization-scopes-function-level"></a>Engedélyezési hatókörök (Function-Level)

A függvény szintű kulcsok két hozzáférési hatókörrel rendelkeznek:

* **Function**: ezek a kulcsok csak azokra a függvényekre érvényesek, amelyekben definiálva vannak. API-kulcsként való használata esetén ezek csak a funkció elérését teszik lehetővé.

* **Gazdagép**: a gazdagép hatókörével rendelkező kulcsok használhatók a Function alkalmazásban található összes funkció eléréséhez. API-kulcsként való használata esetén ezek a függvények a Function alkalmazáson belüli bármelyik függvényhez hozzáférhetnek. 

A rendszer az egyes kulcsokat hivatkozásként nevezi el, és az alapértelmezett kulcs ("default") szerepel a függvény és a gazdagép szintjén. A függvények kulcsai elsőbbséget élveznek a gazdagép kulcsaival szemben. Ha két kulcs van definiálva ugyanazzal a névvel, a rendszer mindig a függvény kulcsát használja.

#### <a name="master-key-admin-level"></a>Főkulcs (rendszergazda szintű) 

Az egyes functions-alkalmazásokhoz rendszergazdai szintű gazda kulcs is tartozik `_master` . Amellett, hogy a gazdagép szintű hozzáférést biztosít az alkalmazás összes függvényéhez, a főkulcs rendszergazdai hozzáférést is biztosít a futásidejű REST API-khoz. Ezt a kulcsot nem lehet visszavonni. A hozzáférési szintjének beállításakor `admin` a kérelmeknek a főkulcsot kell használniuk; bármely más kulcs hozzáférési hibát eredményez.

> [!CAUTION]  
> A főkulcs által biztosított Function app emelt szintű engedélyei miatt ne ossza meg ezt a kulcsot harmadik felekkel, vagy terjessze azt natív ügyfélalkalmazások számára. A rendszergazdai hozzáférési szint kiválasztásakor legyen körültekintő.
