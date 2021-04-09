---
title: Azure-költségkeret
description: Ez a cikk az Azure-költségkeret működését és eltávolításának módját ismerteti.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.author: banders
ms.openlocfilehash: 47f1a1ed182420852682e908c7cd8b5e094bc085
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932556"
---
# <a name="azure-spending-limit"></a>Azure-költségkeret

Az Azure-költségkerettel elkerülheti a kreditek mennyiségét meghaladó kiadásokat. A költségkeret alapértelmezés szerint be van kapcsolva minden új ügyfél esetében, aki ingyenes Azure-fiókra regisztrál, illetve olyan előfizetési típusok esetében, amelyek több hónapnyi kreditet tartalmaznak. A költségkeret a kreditek mennyiségével egyenlő. A költségkeret mennyisége nem módosítható. Ha például regisztrált az ingyenes Azure-fiókra, a költségkerete 200 dollár, és nem módosíthatja 500 dollárra. Lehetősége van azonban a költségkeret eltávolítására. Tehát vagy nincs költségkerete, vagy a kreditmennyiségnek megfelelő költségkerete van. Ez megakadályozza a legtöbb kiadást. A költségkeret nem érhető el hűségszerződéses csomagokat és használatalapú fizetéses csomagokat tartalmazó előfizetésekhez. Tekintse meg [az Azure-előfizetések típusait és a költségkeret elérhetőségét](https://azure.microsoft.com/support/legal/offer-details/) ismertető cikket.

## <a name="reaching-a-spending-limit"></a>A költségkeret elérése

Ha a használat olyan költségeket eredményez, amelyek kimerítik a költségkeretet, akkor a rendszer letiltja az üzembe helyezett szolgáltatásokat a számlázási időszak fennmaradó idejére.

Ha például elkölti az ingyenes Azure-fiókhoz járó összes kreditet, akkor a rendszer eltávolítja az üzembe helyezett Azure-erőforrásokat az éles környezetből, valamint leállítja és felszabadítja az Azure-beli virtuális gépeket. A tárfiókokban lévő adatok írásvédett formában érhetők el.

Ha az előfizetési típus több hónapra vonatkozó kredittel rendelkezik, akkor a rendszer automatikusan újraengedélyezi az előfizetést a következő számlázási időszak elején. Ezután újból üzembe helyezheti az Azure-erőforrásokat, és teljes hozzáféréssel fog rendelkezni a tárfiókokhoz és az adatbázisokhoz.

A költségkeret elérésekor az Azure e-mailes értesítést küld. A költségkeretet elérő előfizetésekkel kapcsolatos értesítések megtekintéséhez jelentkezzen be az [Azure Portalra](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Ha ingyenes Azure-fiókra regisztrált, és eléri a költségkeretet, akkor a költségkeret eltávolításához és az előfizetés automatikus újbóli engedélyezéséhez [használatalapú fizetéses](upgrade-azure-subscription.md) díjszabásra válthat.

## <a name="remove-the-spending-limit-in-azure-portal"></a>A költségkeret eltávolítása az Azure Portalon

A költségkeretet bármikor eltávolíthatja mindaddig, amíg az Azure-előfizetéséhez érvényes fizetési mód van társítva. A több hónapnyi kreditet tartalmazó előfizetési típusok, például a Visual Studio Enterprise és Visual Studio Professional esetében törölheti a költségkeretet határozatlan időre vagy csak az aktuális számlázási időszakra vonatkozóan. Ha csak az aktuális számlázási időszakot választja, a következő számlázási időszak kezdetén a rendszer automatikusan engedélyezi a költségkeret.

Amennyiben ingyenes Azure-fiókkal rendelkezik, tekintse meg az [Azure-előfizetés frissítését](upgrade-azure-subscription.md) tárgyaló szakaszt a költségkeret eltávolításával kapcsolatban. Egyéb esetben kövesse az alábbi lépéseket a költségkeret eltávolításához:

<a id="remove"></a>

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![Képernyőkép a keresőmezőről a költségkezelés + számlázás keresőkifejezéssel ](./media/spending-limit/search-bar.png)

1. A **Saját előfizetések** listából válassza ki az előfizetését. Például *Visual Studio Enterprise*.

   ![Képernyőkép a saját előfizetések rácsának áttekintésével](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Ha bizonyos Visual Studio-előfizetéseit nem látja itt, előfordulhat, hogy valamikor módosította egy előfizetés könyvtárát. Az ilyen előfizetések esetében módosítania kell a könyvtárat az eredeti könyvtárra (arra, amelyben eredetileg regisztrált). Ezután ismételje meg a 2. lépést.

1. Az előfizetés áttekintésében kattintson a narancssárga szalagcímre a költségkeret eltávolításához.

    ![Képernyőkép a költségkeret eltávolítása szalagcímről](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. Válassza ki, hogy határozatlan időre vagy csak az aktuális számlázási időszakra szeretné-e eltávolítani a költségkeretet.

      ![Képernyőkép a költségkeret eltávolítása panelről](./media/spending-limit/remove-spending-limit-blade-x.png)

      | Beállítás | Hatás |
      | --- | --- |
      | A költségkeret eltávolítása határozatlan időre | A költségkeretet nem kapcsolódik vissza automatikusan a következő számlázási időszak kezdetekor. Ön azonban bármikor visszakapcsolhatja. |
      | A költségkeret eltávolítása az aktuális számlázási időszakra | A költségkeretet automatikusan visszakapcsolódik a következő számlázási időszak kezdetekor. |


1. Kattintson a **Fizetési mód kiválasztása** lehetőségre az előfizetés fizetési módjának kiválasztásához. Ez lesz az előfizetése aktív fizetési módja.

1. Kattintson a **Finish** (Befejezés) gombra.


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>A költségkeret eltávolításának lehetséges okai

A költségkeret megakadályozhatja bizonyos külső vagy Microsoft-szolgáltatások üzembe helyezését vagy használatát. A következő helyzetekben érdemes eltávolítania az előfizetéséhez tartozó költségkeretet.

-  Harmadik féltől származó rendszerképeket (például Oracle) vagy szolgáltatásokat (például Azure DevOps Services) szeretne üzembe helyezni. Ez a helyzet a költségkeret szinte azonnali eléréséhez és az előfizetés letiltásához vezet.
- Olyan szolgáltatásokkal rendelkezik, amelyek megszakadását szeretné elkerülni. Amikor eléri a költségkeretet, a rendszer eltávolítja az üzembe helyezett Azure-erőforrásokat az éles környezetből, valamint leállítja és felszabadítja az Azure-beli virtuális gépeket. Ha olyan szolgáltatásokkal rendelkezik, amelyek megszakadását szeretné elkerülni, el kell távolítania a költségkeretet.
- Olyan szolgáltatásokkal és erőforrásokkal rendelkezik, amelyek beállításait (például virtuális IP-címek) nem szeretné elveszíteni. A költségkeret elérésekor, valamint a szolgáltatások és erőforrások felszabadításakor ezek a beállítások elvesznek.

## <a name="turn-on-the-spending-limit-after-removing"></a>A költségkeret bekapcsolása eltávolítás után

Ez a funkció csak akkor érhető el, ha a költségkeretet határozatlan időre eltávolították az olyan előfizetési típusok esetében, amelyek több hónapnyi kreditet tartalmaznak. Ezzel a funkcióval automatikusan bekapcsolhatja a költségkeretet a következő számlázási időszak kezdetekor.


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![Képernyőkép a keresőmezőről a költségkezelés + számlázás keresőkifejezéssel ](./media/spending-limit/search-bar.png)

1. A **Saját előfizetések** listából válassza ki az előfizetését. Például *Visual Studio Enterprise*.

   ![Képernyőkép a saját előfizetések rácsának áttekintésével](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Ha bizonyos Visual Studio-előfizetéseit nem látja itt, előfordulhat, hogy valamikor módosította egy előfizetés könyvtárát. Az ilyen előfizetések esetében módosítania kell a könyvtárat az eredeti könyvtárra (arra, amelyben eredetileg regisztrált). Ezután ismételje meg a 2. lépést.

1. Az Előfizetés áttekintés lapon kattintson a lap tetején látható szalagcímre a költségkeret visszakapcsolásához.

## <a name="custom-spending-limit"></a>Egyéni költségkeret

Egyéni költségkeretek nem érhetők el.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>A költségkerettel nem kerülhető el minden költség

[Az Azure Marketplace-en közzétett egyes külső szolgáltatások](../understand/understand-azure-marketplace-charges.md) esetén nem használhatja az előfizetésével járó krediteket, és külön költségek jelentkeznek még akkor is, ha költségkeretet állított be. Ilyenek például a Visual Studio-licencek, a prémium szintű Azure Active Directory, a támogatási csomagok és a legtöbb külső szolgáltatás. Új külső szolgáltatás üzembe helyezésekor megjelenik egy figyelmeztetés, amely arról tájékoztatja, hogy a szolgáltatások számlázása külön történik:

![Marketplace vásárlási figyelmeztetése](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések
- Frissítsen egy [használatalapú fizetéses](upgrade-azure-subscription.md) díjszabású csomagra.
