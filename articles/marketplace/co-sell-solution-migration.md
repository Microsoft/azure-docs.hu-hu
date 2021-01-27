---
title: Közös értékesítésű megoldások migrálása a OCP GTM a partner Centerbe Microsoft AppSource
description: Ismerje meg, hogyan telepítheti át a OCP GTM közös értékesítési megoldásait a partneri központba Microsoft AppSource).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 12/07/2020
ms.openlocfilehash: 84d98eedb3ea6f3faaeaecf832811f9f4c78c7c5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880355"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>A OCP GTM közös értékesítési megoldásainak áttelepítése a kereskedelmi piactérre

A Microsoft áthelyezi a közzétételi folyamatot. A [kereskedelmi piactér](overview.md) egyszerűsített ajánlatot tesz közzé a Microsoft három csatornán keresztül történő közös értékesítésével, központosítva az ajánlatok létrehozását és felügyeletét a partner Centerben, ahol Ön már kezeli a Microsofttal való kapcsolatát.

A kereskedelmi piactéren regisztrált Microsoft-partnerként a következőket teheti:

- Az ajánlatait központilag teheti közzé, és a Microsoft közvetlen vásárlói, partneri és értékesítői csatornákon keresztül értékesítheti.
- Gondoskodjon arról, hogy ajánlatai a megfelelő online áruházban legyenek –[Microsoft AppSource](https://appsource.microsoft.com) vagy az [Azure Marketplace](https://azure.microsoft.com)-en – az ajánlat képességeivel összhangban lévő több millió Felhőbeli ügyfél eléréséhez.
- Az üzleti céljaihoz illeszkedő ajánlatokkal közösen értékesítheti saját közzétételi élményét.
- Az ajánlat közzétételét a partner centeren belül igazíthatja, ahol már felügyeli a Microsoft-kapcsolatát, és közös értékesítési lehetőségeket is biztosít.
- A [Piactéri juttatások](partner-center-portal/marketplace-rewards.md)feloldása.

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>A Microsofttal való közös értékesítés folytatásának előfeltételei

Győződjön meg arról, hogy aktív Microsoft Partner Network tagsággal rendelkezik, és regisztrálva van a kereskedelmi piactéren a partner Centerben.

- Csatlakoztassa [a Microsoft Partner Network](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)díjmentesen. Partnerként exkluzív erőforrásokhoz, programokhoz, eszközökhöz és kapcsolatokhoz férhet hozzá vállalkozása növekedéséhez.
- Ha nem rendelkezik fiókkal a kereskedelmi piactéren, [Regisztráljon most](partner-center-portal/create-account.md) , hogy folytassa a Microsofttal való közös értékesítést, és hozzáfér a teljes körű közzétételi élményhez.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Frissítések közzététele a közös értékesítésre kész állapot eléréséhez

Ahhoz, hogy a megoldás a Microsoft-értékesítők és-partnerek számára is elérhető legyen, meg kell felelnie a [közös értékesítésre kész követelményeknek](marketplace-co-sell.md). Ahhoz, hogy a Microsoft-értékesítő incentivized, a megoldásnak meg kell felelnie az [ösztönző jogosultsági követelményeknek](marketplace-co-sell.md). Végezze el ezeket a követelményeket a partner Center közös értékesítés lapján [(lásd a jelen témakör](#cosell-tab) későbbi részében).

> [!NOTE]
> A kereskedelmi piactéren a megoldásait a közzétételi folyamat során "ajánlatoknak" nevezzük.

Miután regisztrált a kereskedelmi piactéren, készítse elő a megoldásait a OCP GTM.

Kövesse az alábbi lépéseket a megoldások OCP GTM való importálása előtt:

1. Látogasson el a vállalat [közzétevői listájára](https://partner.microsoft.com/dashboard/account/v3/publishers/list). Ez magában foglalja a fiók tulajdonosát, feletteseit és fejlesztőit, akik hozzáférés-közzétételi jogosultsággal rendelkeznek. További információ a [partneri központ felhasználói szerepköreiről](./partner-center-portal/manage-account.md#define-user-roles-and-permissions).
2. Kérje meg az egyik felsorolt partnert, hogy a felhasználók a kereskedelmi piactéren *vezetőként* vagy *fejlesztőként* [vegyenek fel felhasználókat](https://partner.microsoft.com/dashboard/account/usermanagement) , mivel csak ezek a szerepkörök szerkeszthetik és tehetik közzé a megoldásokat.
3. Működjön együtt a fejlesztővel, hogy a megoldásokat a OCP GTM-fiókjából a kereskedelmi piactérre helyezze át.
4. Döntse el, hogy a következők közül melyiket kívánja elvégezni:
    1. Egyesítse ezt a megoldást egy hasonló ajánlattal a kereskedelmi piactéren.
    1. Telepítse át ezt a megoldást a OCP GTM a kereskedelmi piactérre.
    1. A megoldás elvetése.

## <a name="migrate-your-solutions-from-ocp-gtm"></a>Megoldások migrálása a OCP GTM

1. Az áttelepítés megkezdése [itt](https://partner.microsoft.com/solutions/migration#).
2. Válassza az **Áttekintés** lapot, majd **kattintson ide a kezdéshez**.

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="A partneri központ áttekintése lap, Áttekintés lap.":::

3. Az áttelepítés megkezdéséhez válassza a **megoldások** fület, amely MEGJELENÍTI az MPN-azonosítóhoz társított összes megoldást.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="A partneri központ áttekintése lap, megoldások lap.":::

    > [!NOTE]
    > Ezen a lapon megtekintheti, hogy nincs-e olyan megoldás, amely a kereskedelmi piactérre való áttérésre van függőben. A Microsofttal való közös értékesítés folytatásához gondoskodjon arról, hogy az áttelepített megoldások közzé legyenek téve a kereskedelmi piactéren.

    A megoldás állapotáról az eszköztippek áttekintésével tájékozódhat. Az összes függőben lévő megoldás művelet a **művelet** területen található.<a name="beginmigration"></a>

4. Válassza az **áttelepítés megkezdése** (lásd a fenti képen) lehetőséget az áttelepíteni kívánt megoldáshoz, majd válasszon a következő lehetőségek közül:

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="A három áttelepítési lehetőség.":::

### <a name="merge-solution-with-a-similar-offer"></a>Megoldás egyesítése hasonló ajánlattal

Akkor válassza ezt a lehetőséget, ha a megoldás már közzé van téve a kereskedelmi piactéren, és a kettőt egyetlen ajánlatba kell egyesíteni. Ezzel elkerülheti az ismétlődő ajánlatok létrehozását.

1. Azonosítsa a meglévő ajánlatot.
    1. Válassza **ezt a megoldást szeretném egyesíteni a kereskedelmi piactér hasonló ajánlatával** (lásd a [fenti](#beginmigration) **művelethez szükséges műveletet** ).
    1. Az **1. művelet** lapon az élő kereskedelmi piactéren elérhető ajánlatok láthatók, hogy a OCP GTM-megoldás társítható. Ha rendelkezik ilyennel, válassza ki az élő ajánlatot a listából. Ha nem áll rendelkezésre a választható ajánlatok listája, adja meg Microsoft AppSource vagy az Azure Marketplace-ről az ügyfél felé irányuló címet (URL-címet).
        [![Az egyesítési folyamat 1. művelet lapja.](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. Válassza a **Folytatás** lehetőséget.
1. Az egyesítés igénylése.
    1. A **2. művelet** lapon megtekintheti, hogy az Ön által azonosított OCP-GTM megoldás egyesítését szeretné-e kérni. Az egyesítés igényléséhez válassza a **mentés & Kapcsolatfelvétel az ügyfélszolgálattal** lehetőséget, amely megnyitja a partner támogatási lapját a böngészőben.
    1. Válassza a **probléma részleteinek** megadása lehetőséget, és adja meg a következőt: az [ ![ egyesítési folyamat 2. művelet lapja.](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. Válassza a **Küldés** lehetőséget. A partner-támogatási csapat két munkanapon belül felveszi Önnel a kapcsolatot.
    1. A partneri támogatással együttműködve biztosíthatja az ajánlat sikeres egyesítését, hogy élő ajánlatként legyen közzétéve.

### <a name="migrate-this-solution-from-ocp-gtm"></a>A megoldás migrálása a OCP GTM

Akkor válassza ezt a lehetőséget, ha olyan OCP GTM-megoldásra van szüksége, amely még nem rendelkezik a kereskedelmi piactéren közzétett megfelelő ajánlattal. Ezt a megoldást közzé kell tenni a kereskedelmi piactéren a Microsofttal való együttműködés folytatásához, és a megoldás áttelepítésével időt takaríthat meg, ha megtartja az információkat és a OCP GTM származó anyagokat. Ehhez a beállításhoz ki kell választania egy ajánlat típusát.

1. Válassza **ezt a megoldást szeretném áttelepíteni a OCP GTM-ről a kereskedelmi piactérre** (lásd a [fenti](#beginmigration) **művelethez szükséges lépéseket** ), majd **folytassa** a műveletet.
1. Az **1. művelet** lapon válassza ki az [ajánlat típusát](publisher-guide-by-offer-type.md), majd **folytassa** a műveletet.

    [![Az áttelepítési folyamat 1. művelet lapja.](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. A **2. művelet** lapon válassza ki a [közzétevői profilt](partner-center-portal/create-account.md) a megadott listából. Ha nem rendelkezik közzétevői fiókkal, hozzon létre egyet a [partner Centerben](https://partner.microsoft.com/solutions/migration), és válassza ki itt.

    [![Az áttelepítési folyamat 2. művelet lapja.](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. A Piszkozat **létrehozása** lehetőséget választva áttelepítheti a megoldást a kereskedelmi piactérre vázlatként. Még nem lesz aktív.
1. Folytassa a közzétételi folyamatot a partner Centerben. Ha segítségre van a partner Centerben való közzétételsel kapcsolatban, tekintse [meg az ajánlat élőben az alábbi kereskedelmi piactéren](#make-your-offer-live-in-the-marketplace) című témakört.

### <a name="discard-this-solution"></a>A megoldás elvetése

Akkor válassza ezt a lehetőséget, ha a OCP GTM-megoldásokban lévő megoldások már nem relevánsak. A rendszer megkéri, hogy erősítse meg az elvet, és később is visszavonhatja azt.

1. Jelölje be **a megoldás elvetése** (lásd a [fenti](#beginmigration) **művelet szükséges** ), majd a **Folytatás** lehetőséget.
2. Válassza az **Elvetés** lehetőséget.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="Erősítse meg az elvetést.":::

3. Az Elvetés visszavonásához válassza az **Elvetés visszavonása** lehetőséget.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="A visszavonás elvetése hivatkozás.":::

4. Ha további segítségre van szüksége, akkor a **Segítség kérése** lapon lépjen kapcsolatba a partner támogatási csapatával.

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="A Segítség kérése lapon található támogatás hivatkozása.":::

## <a name="make-your-offer-live-in-the-marketplace"></a>Ajánlat élőben a piactéren

Ha úgy választotta, hogy az ajánlatot a kereskedelmi piactérre telepíti át, a rendszer vázlatként megérkezik. Továbbra is közzé kell tennie ajánlatát, hogy élő legyen a kereskedelmi piactéren; Ez megtartja a közös értékesítés állapotát, az ösztönzőket és a hivatkozó folyamatokat.

Az ajánlat közzétételéhez szükséges információkkal kapcsolatos részletes utasításokért olvassa el a megfelelő [közzétételi útmutatót](publisher-guide-by-offer-type.md). Az összefoglalás az alábbiakban olvasható.

1. A partner központ **Áttekintés** lapján válassza ki a draft-ajánlat nevét.

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="Az ajánlat áttekintése oldal a partner Centerben.":::

<a name="cosell-tab"></a>

2. Végezze el a szükséges információkat az egyes lapokon. Ha kívánja, fejezze be az **viszonteladás a CSP** -n keresztül lapot (a bal oldali navigációs menüben) a Cloud Solution Provider (CSP) programon keresztüli viszonteladáshoz. A **további** hivatkozások és az elemleírások végigvezetik a követelményeken és a részleteken.

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="Az ajánlat áttekintése telepítési lapok a partner Centerben.":::

3. A Microsoft Seller szolgáltatással kapcsolatos néhány részlet a OCP GTM-megoldásból lett másolva. Fejezze be a többi szükséges információt a **közös értékesítés a Microsoft** lapon, hogy az ajánlat közös értékesítésre készen álljon. Ha elkészült, válassza **a felülvizsgálat és közzététel** lehetőséget. További információ: a [közös értékesítéssel kapcsolatos anyagok beküldése a partner Centerben](marketplace-co-sell.md#how-to-submit-co-sell-materials-in-partner-center).

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="Az ajánlat közös értékesítésének lapja a partner Centerben.":::

4. Az összes elküldött információ áttekintése után a **Közzététel** lehetőségre kattintva elküldheti a vázlatos ajánlatát a minősítési felülvizsgálathoz.

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="Az ajánlat áttekintése lap és a közzététel gomb a partner Centerben.":::

5. Nyomon követheti a Küldés állapotát az **Áttekintés** lapon.

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="Az ajánlat közzététele állapotjelző sáv a partner Center áttekintés lapján.":::

6. A rendszer értesíti Önt, ha a minősítési felülvizsgálat elkészült. Ha a művelettel kapcsolatos visszajelzések megadására van szükség, keresse meg, majd válassza a **Közzététel** lehetőséget az újraminősítés kezdeményezéséhez.
7. Ha az ajánlata megfelel a minősítésnek, előzetesen tekintse meg az ajánlatot a megadott hivatkozással, és végezze el a kívánt végső módosításokat. Ha elkészült, válassza az **élő indítás** lehetőséget (lásd a fenti gombot) az ajánlat közzétételéhez a releváns kereskedelmi Piactéri kirakatokban. A Live szolgáltatásban az ajánlat az eredeti OCP GTM-megoldással megőrzi a közös értékesítési állapotát.

## <a name="next-steps"></a>További lépések

- [Viszonteladás CSP-partnereken keresztül](cloud-solution-providers.md)
- [Közös értékesítésű anyagok beküldése a partner Centerben](marketplace-co-sell.md#how-to-submit-co-sell-materials-in-partner-center)
- A [Gyakori kérdések](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) megtekintése (PDF)