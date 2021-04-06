---
title: Azure API Management-példány kapacitása | Microsoft Docs
description: Ez a cikk ismerteti, hogy mi a kapacitás mérőszáma, és hogyan lehet megalapozott döntéseket hozni az Azure API Management-példányok skálázásához.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: c7c002b57f2220ac0a9fba43a8081b2a4ed800e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93081059"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Az Azure API Management-példányok kapacitása

A **kapacitás** a legfontosabb [Azure monitor mérőszám](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) ahhoz, hogy megalapozott döntéseket hozhasson a API Management-példányok nagyobb terheléshez való méretezése érdekében. Az építkezés összetett, és bizonyos viselkedést ró.

Ez a cikk a **kapacitást** és a működésének módját ismerteti. Bemutatja, hogyan érheti el a Azure Portal **kapacitás** metrikáit, és hogy mikor érdemes megfontolni a API Management-példány méretezését vagy frissítését.

> [!IMPORTANT]
> Ebből a cikkből megtudhatja, hogyan figyelheti és méretezheti az Azure API Management-példányát a kapacitás mérőszáma alapján. Ugyanakkor fontos tisztában lenni azzal, hogy mi történik, ha egy adott API Management-példány ténylegesen *elérte* a kapacitását. Az Azure API Management nem alkalmaz semmilyen szolgáltatási szintű szabályozást, hogy megakadályozza a példányok fizikai túlterhelését. Ha egy példány eléri a fizikai kapacitását, az a túlterhelt webkiszolgálóhoz hasonlóan fog működni, amely nem tudja feldolgozni a bejövő kérelmeket: a késés növekedni fog, a kapcsolatok megszakadnak, időtúllépési hibák lépnek fel, stb. Ez azt jelenti, hogy az API-ügyfeleknek fel kell készülniük arra, hogy hasonló módon kezeljék ezt a lehetőséget, mint bármely más külső szolgáltatáshoz (például újrapróbálkozási szabályzatok alkalmazásával).

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek követéséhez a következőket kell tennie:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Egy APIM-példány. További információ: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Mi az a kapacitás?

![A kapacitás metrikáját bemutató diagram.](./media/api-management-capacity/capacity-ingredients.png)

A **kapacitás** egy API Management-példány terhelésének kijelzője. Az erőforrások felhasználását (CPU, memória) és a hálózati üzenetsor hosszát tükrözi. A processzor-és memóriahasználat az erőforrások felhasználását mutatja:

+ API Management adatközpont-szolgáltatásokat, például a kérelmek feldolgozását, amely tartalmazhat továbbítási kéréseket vagy házirendet futtat.
+ API Management a felügyeleti sík szolgáltatásai, például az Azure Portalon vagy az ARM-on keresztül alkalmazott felügyeleti műveletek, illetve a [fejlesztői portálról](api-management-howto-developer-portal.md)érkező betöltések.
+ Kiválasztott operációsrendszer-folyamatok, beleértve az új kapcsolatokon a TLS-kézfogások költségeit érintő folyamatokat.

A teljes **kapacitás** a saját értékeit egy API Management példány minden egységének átlaga.

Bár a **Kapacitási metrika** úgy lett kialakítva, hogy problémákba ütközik a API Management-példánnyal, vannak olyan esetek, amikor problémák nem tükröződnek a **kapacitás metrikájának** változásaiban.

## <a name="capacity-metric-behavior"></a>Kapacitás metrikájának viselkedése

Az építkezés miatt a valós életben számos változó befolyásolhatja a valós **kapacitást** , például:

+ kapcsolatok mintázata (új kapcsolatok a kérelemben és a meglévő kapcsolatok újrafelhasználása)
+ kérelem és válasz mérete
+ az egyes API-k és a kérelmeket küldő ügyfelek száma alapján konfigurált szabályzatok.

A kérések összetettebb műveletei, annál nagyobb a **kapacitás** -felhasználás. Az összetett átalakítási házirendek például sokkal több PROCESSZORt használnak, mint egy egyszerű kérelem továbbítása. A háttérbeli szolgáltatásokra adott válaszok lassan növekedni fognak.

> [!IMPORTANT]
> A **kapacitás** nem a feldolgozott kérelmek számának közvetlen mértéke.

![Kapacitás metrikájának tüskék](./media/api-management-capacity/capacity-spikes.png)

A **kapacitás** időszakosan vagy nullánál nagyobb lehet, még akkor is, ha nincsenek feldolgozva kérelmek. Ez a rendszer-vagy platform-specifikus műveletek miatt fordul elő, és nem kell figyelembe vennie a példányok méretezésének eldöntése során.

Az alacsony **kapacitású metrika** nem feltétlenül jelenti azt, hogy a API Management-példány nem tapasztal problémát.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>A kapacitás vizsgálata az Azure Portal használatával
  
![Kapacitási mérőszám](./media/api-management-capacity/capacity-metric.png)  

1. Navigáljon a [Azure Portal](https://portal.azure.com/)APIM-példányához.
2. Válassza a **Metrikák** lehetőséget.
3. A lila szakaszban válassza ki a **kapacitás** metrikája lehetőséget a rendelkezésre álló metrikák közül, és hagyja meg az alapértelmezett **AVG** összesítést.

    > [!TIP]
    > A helytelen értelmezések elkerülése érdekében mindig tekintse meg a **kapacitás** metrikájának részletezését.

4. A zöld szakaszban válassza a **hely** lehetőséget a metrika dimenzió szerinti felosztásához.
5. Válasszon egy kívánt időkeretet a szakasz felső sávján.

    Beállíthat egy metrikai riasztást, amely értesíti, ha váratlan esemény történik. Például értesítést kaphat, ha a APIM-példány több mint 20 percen belül túllépte a várt maximális kapacitást.

    >[!TIP]
    > Beállíthatja, hogy a riasztások segítségével jelezze, ha a szolgáltatás alacsony kapacitású, vagy ha Azure Monitor automatikus skálázási funkciót használ az Azure API Management-egység automatikus hozzáadásához. A skálázási művelet körülbelül 30 percet vesz igénybe, ezért a szabályokat ennek megfelelően kell megterveznie.  
    > Csak a fő hely skálázása engedélyezett.

## <a name="use-capacity-for-scaling-decisions"></a>Kapacitás használata méretezési döntések meghozatalához

A **kapacitás** azt a mérőszámot határozza meg, amely szerint a API Management-példány skálázása nagyobb terheléshez igazodik. Megfontolandó szempontok:

+ Hosszú távú trendet és átlagot vizsgálunk.
+ Figyelmen kívül hagyva a hirtelen tüskéket, amelyek valószínűleg nem kapcsolódnak a terhelés növekedéséhez (lásd a "kapacitás metrikájának viselkedése" szakaszt a magyarázathoz).
+ A példány frissítése vagy méretezése, ha a **kapacitás** értéke hosszabb ideig meghaladja a 60%-ot vagy a 70%-ot (például 30 perc). A különböző értékek jobban működhetnek a szolgáltatásban vagy a forgatókönyvben.

>[!TIP]  
> Ha előre tudja megbecsülni a forgalmat, tesztelje a APIM-példányt a várt számítási feladatokon. A kérések terhelését fokozatosan növelheti a bérlőn, és megfigyelheti, hogy a kapacitás metrikájának mekkora értéke felel meg a maximális terhelésnek. Az előző szakasz lépéseit követve Azure Portal használatával megtudhatja, mennyi kapacitást használ a rendszer egy adott időpontban.

## <a name="next-steps"></a>Következő lépések

- [Azure API Management Service-példány méretezése vagy frissítése](upgrade-and-scale.md)
- [Optimalizálja és mentse a Felhőbeli kiadásait](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)