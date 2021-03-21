---
title: Kérelmek feldolgozása & értesítések – Azure AD-jogosultságok kezelése
description: Ismerje meg a hozzáférési csomag kérelmezési folyamatát, valamint azt, hogy a rendszer mikor küldjön e-mail-értesítéseket Azure Active Directory jogosultságok kezelésében.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb84eae2db01180865db54250598f45c7b2ddca0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746235"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Kérelmek feldolgozása és e-mail-értesítések az Azure AD-jogosultságok kezelésében

Amikor egy felhasználó kérelmet küld egy hozzáférési csomagnak, a folyamat megkezdi a hozzáférési kérelem kézbesítését. Az Azure AD-jogosultságok kezelése e-mail-értesítéseket küld a jóváhagyóknak és a kérelmezőknek, ha a folyamat során fontos események történnek. Ez a cikk a kérelmek folyamatát és az elküldött e-mailes értesítéseket ismerteti.

## <a name="request-process"></a>Kérelem folyamata

Egy hozzáférési csomaghoz hozzáféréssel rendelkező felhasználónak hozzáférési kérelmet kell küldenie. A házirend konfigurációjától függően előfordulhat, hogy a kérelem jóváhagyást igényel. A kérések jóváhagyásakor a folyamat megkezdi a felhasználói hozzáférés hozzárendelését a hozzáférési csomagban lévő egyes erőforrásokhoz. Az alábbi ábrán a folyamat és a különböző állapotok áttekintése látható:

![Jóváhagyási folyamat diagramja](./media/entitlement-management-process/request-process.png)

| Állam | Leírás |
| --- | --- |
| Elküldve | A felhasználó kérelmet küld. |
| Jóváhagyás függőben | Ha egy hozzáférési csomagra vonatkozó házirend jóváhagyást igényel, a kérés függőben lévő jóváhagyásra kerül. |
| Lejárt | Ha egyetlen jóváhagyó sem hagyja jóvá a kérést a jóváhagyási kérelem időkorlátján belül, a kérelem lejár. Ha újra próbálkozik, a felhasználónak újra el kell küldenie a kérést. |
| Megtagadva | A jóváhagyó megtagadja a kérelmet. |
| Approved | A jóváhagyó jóváhagyja a kérelmet. |
| Szállít | A felhasználó **nem** rendelt hozzá hozzáférést a hozzáférési csomag összes erőforrásához. Ha ez egy külső felhasználó, előfordulhat, hogy a felhasználó még nem fér hozzá az erőforrás-címtárhoz. Az is előfordulhat, hogy nem fogadták el a hozzájárulási kérést. |
| Kézbesítve | A felhasználó hozzá lett rendelve a hozzáférési csomag összes erőforrásához. |
| Hozzáférés kiterjesztve | Ha a házirendben engedélyezve vannak a bővítmények, a felhasználó kibővítette a hozzárendelést. |
| A hozzáférés lejárt | A felhasználó hozzáférési csomaghoz való hozzáférése lejárt. Ha újra szeretné elérni a hozzáférést, a felhasználónak el kell küldenie egy kérést. |

## <a name="email-notifications"></a>E-mail-értesítések

Ha Ön jóváhagyó, akkor e-mail-értesítéseket küld, amikor egy hozzáférési kérelmet kell jóváhagynia. Értesítéseket is kap, amikor egy hozzáférési kérelem befejeződik. Ön is elküldte a kérelme állapotát jelző e-mail-értesítéseket, ha Ön a kérelmező.

A következő diagramok azt mutatják be, hogy az e-mail-értesítések küldése a jóváhagyóknak vagy a kérelmezőnek. A diagramokban megjelenő e-mail-értesítések megfelelő számának megkereséséhez hivatkozzon az [e-mail értesítések táblára](entitlement-management-process.md#email-notifications-table) .

### <a name="first-approvers-and-alternate-approvers"></a>Első jóváhagyók és alternatív jóváhagyók
Az alábbi ábrán az első jóváhagyók és a másodlagos jóváhagyók, valamint a kérési folyamat során kapott e-mail-értesítések láthatók:

![Első és másodlagos jóváhagyók folyamata](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Kérelmezők
Az alábbi ábrán a kérelmező és a kérési folyamat során kapott e-mail-értesítések láthatók:

![Kérelmező folyamatának folyamatábrája](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>2 – fázis jóváhagyása
A következő ábrán az 1. fázis és a 2. fázis-jóváhagyók, valamint a kérési folyamat során kapott e-mail-értesítések láthatók:

![kétfázisú jóváhagyási folyamat](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>E-mail értesítések táblázata
Az alábbi táblázat részletesebben ismerteti az e-mail-értesítéseket. Az e-mailek kezeléséhez használhatja a szabályokat. Az Outlookban például létrehozhat olyan szabályokat, amelyekkel áthelyezheti az e-maileket egy mappába, ha a tárgy szavakat tartalmaz a táblázatból:

| # | E-mail tárgya | Küldésekor | Címzett |
| --- | --- | --- | --- |
| 1 | Szükséges művelet: a továbbított kérelem jóváhagyása vagy megtagadása a *[date]* alapján | Ezt az e-mailt a rendszer elküldi az 1. szakaszhoz tartozó másodlagos jóváhagyóknak (miután a kérést megadták) a művelet elvégzéséhez. | 1. fázis – alternatív jóváhagyók |
| 2 | Szükséges művelet: a (z *) [date]* jóváhagyása vagy elutasítása. | Ezt az e-mailt a rendszer az első jóváhagyónak küldi el, ha az eszkaláció le van tiltva, a művelet elvégzéséhez. | Első jóváhagyó |
| 3 | Emlékeztető: a kérelem jóváhagyása vagy elutasítása *[date]* a *[kérelmező]* számára | Ezt az emlékeztető e-mailt a rendszer az első jóváhagyónak küldi el, ha az eszkaláció le van tiltva. Az e-mail arra kéri őket, hogy tegyenek lépéseket, ha nem. | Első jóváhagyó |
| 4 | A kérelem jóváhagyása vagy elutasítása *[Time]* a következő *időpontban: [date]* | Ezt az e-mailt a rendszer az első jóváhagyónak küldi el (ha az eszkaláció engedélyezve van) a művelet elvégzéséhez. | Első jóváhagyó |
| 5 | Szükséges művelet emlékeztető: a kérelem jóváhagyása vagy elutasítása *[date]* a *[kérelmező]* számára | Ezt az emlékeztető e-mailt a rendszer az első jóváhagyónak küldi el, ha engedélyezve van az eszkaláció. Az e-mail arra kéri őket, hogy tegyenek lépéseket, ha nem. | Első jóváhagyó |
| 6 | A kérelem lejárt a következőhöz: *[access_package]* | Ezt az e-mailt a rendszer a kérelem lejárta után az első jóváhagyónak és az 1. fázis alternatív jóváhagyóinak küldi el. | Első jóváhagyó, 1. fázis – alternatív jóváhagyók |
| 7 | Kérelem jóváhagyva a következőhöz: *[kérelmező]* – *[access_package]* | A rendszer ezt az e-mailt küldi el az első jóváhagyónak és az 1. fázis alternatív jóváhagyóinak a kérelem befejezésekor. | Első jóváhagyó, 1. fázis – alternatív jóváhagyók |
| 8 | Kérelem jóváhagyva a következőhöz: *[kérelmező]* – *[access_package]* | Ezt az e-mailt a rendszer a 2 fázisú kérelem első jóváhagyójának és 1. fázisának másodlagos jóváhagyójának küldi el az 1. szintű kérelem jóváhagyásakor. | Első jóváhagyó, 1. fázis – alternatív jóváhagyók |
| 9 | Kérelem elutasítva *[access_package]* | A rendszer ezt az e-mailt küldi a kérelmezőnek a kérelem megtagadásakor | Requestor (Kérelmező) |
| 10 | A kérelem lejárt a következőhöz: *[access_package]* . | Ezt az e-mailt a rendszer egy vagy két fázisra vonatkozó kérelem végén küldi el a kérelmezőnek. Az e-mail értesíti a kérelmezőt arról, hogy a kérelem lejárt. | Requestor (Kérelmező) |
| 11 | Szükséges művelet: a (z *) [date]* jóváhagyása vagy elutasítása. | Ezt az e-mailt a rendszer a második jóváhagyónak küldi el, ha az eszkaláció le van tiltva, a művelet elvégzéséhez. | Második jóváhagyó |
| 12 | Szükséges művelet emlékeztető: a kérelem jóváhagyása vagy elutasítása *[date]* | Ezt az emlékeztető e-mailt a rendszer a második jóváhagyónak küldi el, ha az eszkaláció le van tiltva. Az értesítés arra kéri őket, hogy tegyenek lépéseket, ha még nem. | Második jóváhagyó |
| 13 | Szükséges művelet: a kérelem jóváhagyása vagy elutasítása *[date]* a *[kérelmező]* számára | Ezt az e-mailt a rendszer a második jóváhagyónak küldi el, ha engedélyezve van az eszkaláció, hogy végrehajtsa a műveletet. | Második jóváhagyó |
| 14 | Szükséges művelet emlékeztető: a kérelem jóváhagyása vagy elutasítása *[date]* a *[kérelmező]* számára | Ezt az emlékeztető e-mailt a rendszer a második jóváhagyónak küldi el, ha az eszkaláció engedélyezve van. Az értesítés arra kéri őket, hogy tegyenek lépéseket, ha még nem. | Második jóváhagyó |
| 15 | Szükséges művelet: a továbbított kérelem jóváhagyása vagy megtagadása a *[date]* alapján | Ezt az e-mailt a rendszer elküldi a 2. fázisba, ha az eszkaláció engedélyezve van, a művelet elvégzéséhez. | 2. fázis – alternatív jóváhagyók |
| 16 | Kérelem jóváhagyva a következőhöz: *[kérelmező]* – *[access_package]* | Ezt az e-mailt a rendszer elküldi a második jóváhagyónak és 2. fázisban lévő alternatív jóváhagyónak a kérelem jóváhagyása után. | Második jóváhagyó, 2. fázis, alternatív jóváhagyók |
| 17 | A kérelem lejárt a következőhöz: *[access_package]* . | Ezt az e-mailt a rendszer a második jóváhagyónak vagy más jóváhagyóknak küldi el a kérelem lejárta után. | Második jóváhagyó, 2. fázis, alternatív jóváhagyók |
| 18 | Most már hozzáférhet *[access_package]* | Ezt az e-mailt a rendszer elküldi a végfelhasználóknak a hozzáférésük használatának megkezdéséhez. | Requestor (Kérelmező) |
| 19 | A *[access_package]* elérésének kiterjesztése a következőre: *[date]* | Ezt az e-mailt a rendszer a felhasználók hozzáférésének lejárta előtt küldi el a végfelhasználóknak. | Requestor (Kérelmező) |
| 20 | A hozzáférés befejeződött a következőhöz: *[access_package]* | Ezt az e-mailt a rendszer a felhasználók hozzáférésének lejárta után küldi el a végfelhasználóknak. | Requestor (Kérelmező) |

### <a name="access-request-emails"></a>Hozzáférési kérelem e-mail-címe

Ha egy kérelmező hozzáférési kérelmet küld a jóváhagyás megkövetelésére konfigurált hozzáférési csomaghoz, a Szabályzathoz hozzáadott összes jóváhagyó e-mailben értesítést fog kapni a kérelem részleteiről. Az e-mailben szereplő adatok a következők: a kérelmező neve szervezete és az üzleti indoklás; és a kért hozzáférési kezdési és befejezési dátum (ha meg van adni). A részletek a kérelem elküldésekor és a kérés érvényességének lejárta után is szerepelni fognak.

Az e-mail tartalmaz egy hivatkozás-jóváhagyót is, ha a hozzáférési kérelem jóváhagyásához vagy elutasításához szeretné elérni a hozzáférési kérelmet, kattintson a Tovább gombra. Az alábbi példa egy e-mailes értesítést küld az első jóváhagyónak vagy második jóváhagyónak (ha engedélyezve van a 2. szintű jóváhagyás) egy hozzáférési kérelem elvégzéséhez:

![Hozzáférési kérelem jóváhagyása a csomag e-mail-címéhez](./media/entitlement-management-shared/approver-request-email.png)

A jóváhagyók emlékeztető e-mailt is kaphatnak. Az e-mail arra kéri a jóváhagyót, hogy hozzon döntést a kérelemről. Íme egy példa e-mail-értesítésre, amelyet a jóváhagyó kap, hogy emlékeztesse őket a művelet elvégzésére:

![Emlékeztető hozzáférési kérelem e-mail címe](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Alternatív jóváhagyók kérése e-mailben

Ha a másodlagos jóváhagyók beállítás engedélyezve van, és a kérés még függőben van, a rendszer továbbítja. Az alternatív jóváhagyók e-mailt kapnak a kérelem jóváhagyásához vagy elutasításához. Engedélyezheti az alternatív jóváhagyókat az 1. és a 2. fázisban. Itt látható egy példa e-mailben a másodlagos jóváhagyók fogadására:

![Alternatív jóváhagyók e-mail kérése](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

A jóváhagyó és a másodlagos jóváhagyó is jóváhagyhatja vagy megtagadhatja a kérelmet.

### <a name="approved-or-denied-emails"></a>Jóváhagyott vagy megtagadott e-mailek

 Ha egy jóváhagyó egy kérelmező által küldött hozzáférési kérelmet kap, akkor jóváhagyhatja vagy megtagadhatja a hozzáférési kérelmet. A jóváhagyónak üzleti indoklást kell adnia döntéséhez. Az alábbi példa egy, a jóváhagyóknak és az alternatív jóváhagyóknak küldött e-mailt a kérelem jóváhagyása után:

![A csomag e-mail-címéhez való hozzáférésre jóváhagyott kérelem](./media/entitlement-management-process/approver-request-email-approved.png)

Egy hozzáférési kérelem jóváhagyása és a hozzájuk való hozzáférés kiépítés esetén e-mailben értesítést küld a kérelmezőnek, hogy hozzáférjenek a hozzáférési csomaghoz. Íme egy példa e-mail-értesítésre, amelyet a rendszer a kérelmezőnek küld, amikor hozzáférést kap egy hozzáférési csomaghoz:

![Jóváhagyott kérelmező hozzáférési kérelmének e-mail-címe](./media/entitlement-management-process/requestor-email-approved.png)

Hozzáférési kérelem elutasítása esetén e-mailben értesítést küldünk a kérelmezőnek. Íme egy példa e-mail-értesítésre, amelyet a rendszer a hozzáférési kérelmének megtagadásakor küld a kérelmezőnek:

![Kérelmező kérelmének megtagadott e-mail-címe](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>2. szintű jóváhagyási hozzáférési kérelem e-mail-címe

Ha a kétfázisú jóváhagyás engedélyezve van, legalább két jóváhagyónak jóvá kell hagynia a kérést, egyet az egyes szakaszoktól, mielőtt a kérelmező hozzáférhet a hozzáféréshez.

Az 1. fázisban az első jóváhagyó megkapja a hozzáférési kérelem e-mail-címét, és döntést hoz. Ha jóváhagyják a kérést, az 1. fázisban található minden első jóváhagyó és egy másik jóváhagyó (ha a eszkaláció engedélyezve van) értesítést kap az 1. fázis befejeződéséről. Az alábbi példa egy e-mailt küld az 1. lépésben elküldött értesítésről:

![2. szintű hozzáférési kérelem e-mail-címe](./media/entitlement-management-process/approver-request-email-2stage.png)

Miután az első vagy másodlagos jóváhagyó jóváhagyja a kérelmet az 1. fázisban, a 2. fázis kezdődik. A 2. fázisban a második jóváhagyó megkapja a hozzáférési kérelemre vonatkozó értesítő e-mailt. A második jóváhagyó vagy a 2. fázisban található másodlagos jóváhagyók (ha az eszkaláció engedélyezve van) úgy dönt, hogy jóváhagyja vagy elutasítja a kérést, az értesítési e-maileket az első és második jóváhagyónak, valamint az 1. és a 2. fázisban lévő összes másodlagos jóváhagyónak, valamint a kérelmezőnek kell elküldeni.

### <a name="expired-access-request-emails"></a>Lejárt hozzáférési kérelmekre vonatkozó e-mailek

A hozzáférési kérelmek lejárnak, ha egy jóváhagyó nem hagyta jóvá vagy nem utasította el a kérelmet. 

Ha a kérelem eléri a beállított lejárati dátumát, és lejár, akkor a jóváhagyók nem hagyják jóvá vagy nem tagadhatják meg a jóváhagyást. Itt láthatja az értesítésre vonatkozó e-mailt, amelyet a rendszer az összes első, második (ha 2 fázisú jóváhagyás engedélyezése esetén) és alternatív jóváhagyóként küldött el:

![A jóváhagyóak lejárt hozzáférési kérelem e-mail címe](./media/entitlement-management-process/approver-request-email-expired.png)

E-mail-értesítést is küld a kérelmezőnek, értesíti arról, hogy a hozzáférési kérelmük lejárt, és újra el kell küldenie a hozzáférési kérést. Az alábbi ábrán a kérelmező és az e-mailes értesítések jelennek meg, amikor a hozzáférés kiterjesztését kérik:

![A kérelmező kiterjeszti a hozzáférési folyamatot](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Itt látható egy példa e-mail-értesítésre, amelyet a kérelmező a hozzáférési kérelmének lejárta után küld el:

![Kérelmező lejárt hozzáférési kérelem e-mail címe](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomaghoz való hozzáférés kérése](entitlement-management-request-access.md)
- [Hozzáférési kérelmek jóváhagyása vagy megtagadása](entitlement-management-request-approve.md)
