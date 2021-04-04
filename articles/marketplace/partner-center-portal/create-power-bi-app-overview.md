---
title: Power BI alkalmazás-létrehozás áttekintése – Microsoft AppSource
description: Ez a cikk a Power BI-alkalmazások Microsoft AppSource való közzétételének magas szintű lépéseit ismerteti. A Power BI alkalmazásnak meg kell felelnie a kereskedelmi piactéren közzétett technikai és üzleti követelményeknek is.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 04/02/2020
ms.openlocfilehash: b02c5bbf2e6accbea9c7a33dc26c4e89e46bb697
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97679146"
---
# <a name="power-bi-app-creation-overview"></a>Power BI alkalmazás-létrehozás áttekintése

Ez a cikk azt ismerteti, hogyan tehet közzé egy Power BI alkalmazást a Microsoft [AppSource](https://appsource.microsoft.com/). A Power BI alkalmazáscsomag testreszabható tartalmat, beleértve az adatkészleteket, a jelentéseket és az irányítópultokat. Ezután az AppSource használatával más Power BI platformokkal is használhatja az alkalmazást, végrehajthatja a fejlesztő által engedélyezett módosításokat és testreszabásokat, és összekapcsolhatók a saját adataival.

## <a name="publishing-benefits"></a>Közzétételi előnyök

A kereskedelmi piactéren való közzététel előnyei:

- Népszerűsítse vállalatát a Microsoft Brand használatával.
- Az Azure Marketplace-en keresztül akár több mint 100 000 000 Microsoft 365 és Dynamics 365 felhasználó is elérhető a AppSource és több mint 200 000 szervezeten keresztül.
- Kiváló minőségű érdeklődőket fogadhat ezekből a Piactérről.
- A szolgáltatásait a Microsoft és a kereskedelmi csoportok támogatják.

## <a name="overview"></a>Áttekintés

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="Power BI alkalmazás közzétételének lépései – áttekintés" border="false":::

Ezek a legfontosabb közzétételi lépések:

1. Hozza létre az alkalmazást Power BIban. A csomag telepítési hivatkozását fogja kapni, amely az ajánlat fő technikai eszköze. Az ajánlat a partner Centerben való létrehozása előtt küldje el a tesztelési csomagot előzetes gyártásra. Részletekért lásd: [Mik azok a Power bi alkalmazások?](/power-bi/service-template-apps-overview)
2. Adja hozzá a marketing-anyagokat, például a hivatalos nevet, a leírást és az emblémákat.
3. Adja meg az ajánlat jogi és támogatási dokumentumait, például a használati feltételeket, az adatvédelmi szabályzatot, a támogatási szabályzatot és a felhasználói súgót.
4. Ajánlat létrehozása – a partner Center használatával szerkesztheti a részleteket, beleértve az ajánlat leírását, a marketing-anyagokat, a jogi információkat, a támogatási információkat és az eszközök specifikációit.
5. Küldje el közzétételre.
6. Figyelje a folyamatot a partner Centerben, ahol a AppSource bevezetési csapata teszteli, ellenőrzi és tanúsítja az alkalmazást.
7. A minősítés után tekintse át az alkalmazást a tesztelési környezetben, és szabadítsa fel. Ez a lista a AppSource ("élő") jelenik meg.
8. A Power BIban küldje el a csomagot éles környezetben. Részletekért lásd: [a Power bi alkalmazás kiadásának kezelése](/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Előkészületek

Tekintse át az alábbi hivatkozásokat, amelyek sablonokat, tippeket és mintákat biztosítanak.

- [Power BI-alkalmazás létrehozása](/power-bi/service-template-apps-create)
- [Tippek Power BI-alkalmazások készítéséhez](/power-bi/service-template-apps-tips)
- [Példák](/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Követelmények

Ahhoz, hogy közzé lehessen tenni a kereskedelmi piactéren, a Power BI alkalmazás ajánlatának meg kell felelnie a következő technikai és üzleti követelményeknek.

### <a name="technical-requirements"></a>Technikai követelmények

A legfontosabb technikai eszköznek egy [Power bi alkalmazásnak](/power-bi/connect-data/service-template-apps-overview)kell lennie. Ez az elsődleges adatkészletek, jelentések vagy irányítópultok gyűjteménye. Emellett az opcionálisan csatlakoztatott szolgáltatásokat és beágyazott adatkészleteket is tartalmazza, korábbi nevén a [Content Pack](/power-bi/service-organizational-content-pack-introduction). Az ilyen típusú alkalmazások fejlesztésével kapcsolatos további információkért lásd: [Mi a Power bi alkalmazás?](/power-bi/connect-data/service-template-apps-overview).

#### <a name="get-an-installation-web-address"></a>Telepítési webcímek beszerzése

[Power bi](https://powerbi.microsoft.com/) -környezetben csak Power bi alkalmazást hozhat létre.

1. Jelentkezzen be [Power bi Pro licenccel](/power-bi/service-admin-purchasing-power-bi-pro).
2. Az alkalmazás létrehozása és tesztelése Power BIban.
3. Amikor megkapja az alkalmazás-telepítési webcímet, adja hozzá a partner Center **technikai konfigurációs** lapjához.

Miután létrehozta és tesztelte az alkalmazást Power BIban, mentse az alkalmazás-telepítési webcímet, mivel szüksége lesz rá [egy Power bi app-ajánlat létrehozásához](create-power-bi-app-offer.md).

### <a name="business-requirements"></a>Üzleti követelmények

Az üzleti követelmények közé tartoznak az eljárási, szerződéses és jogi kötelezettségek. A következőket kell tennie:

- Regisztrált kereskedelmi Piactéri közzétevő. Ha nincs regisztrálva, kövesse a [kereskedelmi Piactéri fiók létrehozása a partner Centerben](create-account.md)című témakör lépéseit.
- Olyan tartalmat adjon meg, amely megfelel az ajánlat AppSource-on való listázásának feltételeinek. További információkért lásd: [alkalmazás listázása a AppSource-on? A következőképpen](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- Fogadja el a következőt: és kövesse a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement).

## <a name="next-step"></a>Következő lépés

- [Power BI app-ajánlat létrehozása a partner Centerben](create-power-bi-app-offer.md)
