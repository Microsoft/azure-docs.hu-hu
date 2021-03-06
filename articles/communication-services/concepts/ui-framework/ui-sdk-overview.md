---
title: Az Azure kommunikációs szolgáltatások FELHASZNÁLÓIFELÜLET-keretrendszere – áttekintés
titleSuffix: An Azure Communication Services conceptual document
description: További tudnivalók az Azure kommunikációs szolgáltatás felhasználói felületi keretrendszeréről
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4ab1a157cdf3ef5017b227cd090379dcab91997e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105931556"
---
# <a name="azure-communication-services-ui-framework"></a>Azure kommunikációs szolgáltatások felhasználói felületi keretrendszere

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="Az előre elkészített és az egyéni kompozitok összehasonlítása":::

Az Azure kommunikációs szolgáltatás felhasználói felületi keretrendszerének köszönhetően könnyedén létrehozhat modern kommunikációs felhasználói élményt. Az éles környezetben felkészített felhasználói felületi összetevőkből álló könyvtárat biztosít az alkalmazásokhoz:

- **Összetett összetevők** – ezek az összetevők a gyakori kommunikációs forgatókönyveket megvalósító Kulcsrakész megoldások. Az alkalmazásokhoz gyorsan hozzáadhat videohívásokat vagy csevegési élményeket. Az összetett összetevők a Base Components használatával létrehozott nyílt forráskódú összetevők.
- **Alapösszetevők** – ezek az összetevők olyan nyílt forráskódú építőelemek, amelyek lehetővé teszik, hogy egyéni kommunikációs élményt hozzon létre. Az összetevők olyan hívási és csevegési funkciókhoz is elérhetők, amelyek kombinálhatók a tapasztalatok létrehozásához. 

Ezek a felhasználói felületi SDK-k mind a [Microsoft Fluent tervezési nyelvét](https://developer.microsoft.com/fluentui/) és eszközeit használják. A Fluent felhasználói felület olyan alapszintű réteget biztosít a felhasználói felületi keretrendszer számára, amelyet a Microsoft termékein teszteltek.

## <a name="differentiating-components-and-composites"></a>**Összetevők és kompozitok megkülönböztetése**

Az **Alapösszetevők** az Azure Communication Services SDK-ra épülnek, és olyan alapszintű műveleteket implementálnak, mint például az alapszintű SDK-k inicializálása, a videó renderelése, valamint az Elnémítás, a videó be-és kikapcsolására szolgáló felhasználói vezérlők biztosítása. Ezekkel az **Alapösszetevőkkel** saját egyéni elrendezési tapasztalatokat hozhat létre előre elkészített, éles használatra kész kommunikációs összetevők használatával.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="A felhasználói felületi keretrendszer összetevőjének áttekintése":::

Az **összetett összetevők** több **alapösszetevőt** egyesítenek, így teljesebb kommunikációs élményt hozhatnak létre. Ezek a magasabb szintű összetevők könnyen integrálhatók egy meglévő alkalmazásba, így teljes körű kommunikációs élményt vehetnek igénybe anélkül, hogy teljesen fel kellene építenie őket. A fejlesztők koncentrálhat a környező élmény és a kívánt folyamat kiépítésére az alkalmazásaikban, és a kommunikáció összetettségét összetett összetevőkbe helyezheti.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="Az összetett felhasználói felületi keretrendszer áttekintése":::

## <a name="what-ui-framework-is-best-for-my-project"></a>Melyik felhasználói felületi keretrendszer a legmegfelelőbb a projekthez?

A követelmények megismerése segít kiválasztani a megfelelő SDK-t:

- **Mekkora testreszabást szeretne?** Az Azure Communication Core SDK-k nem rendelkeznek UX-vel, és úgy vannak kialakítva, hogy a kívánt UX-et is létrehozhatja. A felhasználói felületi keretrendszer összetevői lehetővé teszik a felhasználói felületi eszközök számára a csökkentett Testreszabás költségeit.
- **Szüksége van a Meeting szolgáltatásokra?** Az Értekezletrendszer számos olyan egyedi képességgel rendelkezik, amely jelenleg nem érhető el az Azure Communication Services SDK-ban, például a homályos háttér és a Kiemelt állapot.
- **Milyen platformokat céloz meg?** A különböző platformok különböző képességekkel rendelkeznek.

A funkciók rendelkezésre állásával kapcsolatos részletek a különböző [felhasználói felületi SDK-k itt érhetők el](ui-sdk-features.md), de a legfontosabb kompromisszumok összegzése alább látható.

|SDK/SDK|Implementáció bonyolultsága|   Testreszabási képesség|  Hívó| Csevegés| [Csapatok együttműködése](./../teams-interop.md)
|---|---|---|---|---|---|---|
|Összetett összetevők|Alacsony|Alacsony|✔|✔|✕
|Alapösszetevők|Közepes|Közepes|✔|✔|✕
|Alapszintű SDK-k|Magas|Magas|✔|✔ |✔

## <a name="cost"></a>Költségek

Az Azure UI-keretrendszerek használata nem rendelkezik további Azure-díjszabással vagy-méréssel. Csak a mögöttes szolgáltatás használatáért kell fizetnie, ugyanazzal a hívási, csevegési és PSTN-mérőszámokkal.

## <a name="supported-use-cases"></a>Támogatott használati esetek

Hívó

- Csatlakozás az Azure Communication Services-híváshoz a csoport azonosítójával

Csevegés

- Csatlakozás az Azure kommunikációs szolgáltatásokhoz a szál azonosítójával

## <a name="supported-identities"></a>Támogatott identitások:

Az Azure kommunikációs szolgáltatások identitása szükséges a felhasználói felületi keretrendszer inicializálásához és a szolgáltatáshoz való hitelesítéshez. A hitelesítéssel kapcsolatos további információkért lásd: [hitelesítési](../authentication.md) és [hozzáférési jogkivonatok](../../quickstarts/access-tokens.md)


## <a name="recommended-architecture"></a>Ajánlott architektúra 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="Felhasználói felületi keretrendszer ajánlott architektúrája az ügyfél-kiszolgáló architektúrával ":::

Az összetett és az alap összetevőket egy Azure kommunikációs szolgáltatás hozzáférési jogkivonatának használatával inicializáljuk. A hozzáférési jogkivonatokat az Ön által kezelt megbízható szolgáltatáson keresztül kell beszerezni az Azure kommunikációs szolgáltatásokból. További információért lásd: rövid útmutató [: hozzáférési tokenek létrehozása](../../quickstarts/access-tokens.md) és [megbízható szolgáltatás oktatóanyaga](../../tutorials/trusted-service-tutorial.md) .

Ezek az SDK-k a hívás vagy csevegés kontextusához is szükségesek, amelyhez csatlakozni fognak. A felhasználói hozzáférési tokenekhez hasonlóan ezt a környezetet a saját megbízható szolgáltatásán keresztül kell terjeszteni az ügyfeleknek. Az alábbi lista összefoglalja az inicializálási és erőforrás-kezelési funkciókat, amelyeket működővé tenni kell.

| Contoso-feladatok                                 | Felhasználói felületi keretrendszer feladatai                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Hozzáférési jogkivonat megadása az Azure-ból                    | A megadott hozzáférési jogkivonat továbbítása az összetevők inicializálásához        |
| Frissítési függvény megadása                                 | Hozzáférési jogkivonat frissítése a fejlesztői által biztosított függvény használatával          |
| Csatlakozási információk beolvasása/továbbítása híváshoz vagy csevegéshez          | A hívási és csevegési információk továbbítása az összetevők inicializálásához |
| Felhasználói adatok beolvasása/továbbítása bármely egyéni adatmodellhez | Egyéni adatmodell továbbítása a renderelési összetevőkhöz          |
