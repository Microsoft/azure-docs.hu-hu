---
title: Az Azure Reservations használatának Nagyvállalati Szerződés és Microsoft Ügyfélszerződés
description: Megtudhatja, hogyan olvashatja el a használati adatokat, hogy megtudja, hogyan vonatkozik az Azure-foglalás Nagyvállalati Szerződés és Microsoft Ügyfélszerződés használatra.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: banders
ms.openlocfilehash: edc1a80687f768723a3a218ecaa1f6459cd484ed
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763558"
---
# <a name="get-enterprise-agreement-and-microsoft-customer-agreement-reservation-costs-and-usage"></a>Foglalási Nagyvállalati Szerződés és Microsoft Ügyfélszerződés költségeinek és használatának le- és lefoglalása

A foglalási költségek és a használat továbbfejlesztett adatai Nagyvállalati Szerződés (EA) és Microsoft Ügyfélszerződés (MCA) használathoz a Cost Managementben. Ez a cikk a következőkben nyújt segítséget:

- Lekérheti a foglalás megvásárlására vonatkozó adatokat
- Megtudhatja, melyik előfizetés, erőforráscsoport vagy erőforrás használta a foglalást
- Jóváírást kérhet a foglalási kihasználtság alapján
- Kiszámíthatja a foglalási megtakarításokat
- Lekérheti a foglalás kihasználatlansági adatait
- Amortizálhatja a foglalási költségeket

A Marketplace díjai a használati adatokban vannak összevonva. A díjakat megtekintheti belső használat, Marketplace-használat és egyetlen adatforrásból való vásárlások szerint.

## <a name="reservation-charges-in-azure-usage-data"></a>Az Azure használati adatainak foglalási díjai

Az adatok két különböző adathalmazra vannak felosztva: _Tényleges költség_ és _Amortizált költség_. A két adathalmaz különbségei a következők:

**Tényleges költség** – Olyan adatokat szolgáltat, amelyeket összevethet a havi számlájával. Ezek az adatok tartalmazzák a foglalás vételárát és a foglalás alkalmazásának részleteit. Az adatokból megtudhatja, hogy egy adott napon mely előfizetés, erőforráscsoport vagy erőforrás részesült foglalási kedvezményben. A foglalási kedvezményben részesülő használat tényleges ára (EffectivePrice) nulla.

**Amortizált költség** – Ez az adathalmaz hasonlít a Tényleges költség adathalmazra, leszámítva, hogy foglalási kedvezményben részesülő tényleges ár (EffectivePrice) a foglalás arányosított költségével egyezik meg (nulla helyett). Ezzel megtudhatja a foglalási fogyasztás előfizetésenkénti, erőforráscsoportonkénti, vagy erőforrásonkénti pénzben kifejezett értékét, és segít abban, hogy a foglalási használatot belsőleg számolja el költséghelyi elszámolással. Az adathalmaz tartalmaz továbbá fel nem használt foglalási órákat. Az adathalmaz nem tartalmaz foglalásvásárlási adatokat.

A két adathalmaz összehasonlítása:

| Adatok | Tényleges költség adathalmaz | Amortizált költség adathalmaz |
| --- | --- | --- |
| Foglalásvásárlások | Ebben a nézetben elérhető.<br><br>  Az adatok lekéréséhez szűrjön ChargeType = &quot;Purchase&quot; (díjtípus = vásárlás) szerint. <br><br> Tekintse meg a ReservationID vagy a ReservationName változót annak eldöntéséhez, hogy a díj melyik foglalásra vonatkozik.  | Ennél a nézetnél nem alkalmazható. <br><br> A vásárlási költségek nincsenek feltüntetve az amortizált adatok között. |
| EffectivePrice | Az érték foglalási kedvezményben részesülő használat esetén nulla. | Az érték a foglalás óránkénti költségét jelzi kedvezményben részesülő használat esetén. |
| Fel nem használt foglalás (A nap során fel nem használt foglalás órában kifejezett időtartamát és a veszteség pénzben kifejezett értékét adja meg) | Ebben a nézetben nem alkalmazható. | Ebben a nézetben elérhető.<br><br> Az adatok lekéréséhez szűrjön ChargeType = &quot;UnusedReservation&quot; (díjtípus = fel nem használt foglalás) szerint.<br><br>  Ha meg szeretné tudni, hogy mely foglalás volt alulhasználva, tekintse meg a ReservationID vagy a ReservationName változót. Ez megmutatja, a foglalás mekkora része maradt kihasználatlanul a nap során.  |
| UnitPrice (az árlistában szereplő erőforrás ára) | Elérhető | Elérhető |

Az Azure használati adataiban elérhető egyéb információk módosultak:

- Termék- és mérőinformációk – az Azure nem cseréli le az eredetileg fogyasztott mérőt a ReservationId (Foglalásazonosító) és a ReservationName (Foglalásnév) változókkal, ahogyan azt korábban tette.
- ReservationId (Foglalásazonosító) és ReservationName (Foglalásnév) – ezek saját mezőként szerepelnek az adatokban. Korábban ez csak az AdditionalInfo (További információk) alatt volt elérhető.
- ProductOrderId – A foglalás rendelési azonosítója, saját mezőként hozzáadva.
- ProductOrderName – A megvásárolt foglalás termékneve.
- Időtartam – 12 hónap vagy 36 hónap.
- RINormalizationRatio – Elérhető az AdditionalInfo (További információk) alatt. Azt az arányt fejezi ki, hogy a foglalás milyen arányban jelenik meg a használati adatokban. Ha a példány méretrugalmassága engedélyezve van a foglalásban, akkor más méretekre is alkalmazható. Az érték azt az arányt mutatja, hogy a foglalás milyen arányban jelent meg a használati adatokban.

[Lásd a mező definícióját](/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Azure fogyasztási és foglalási használati adatok lekérése API használatával

Az adatokat lekérheti az API használatával, vagy letöltheti azokat az Azure Portalról.

Az új adatok lekéréséhez hívja meg a [Usage Details API-t](/rest/api/consumption/usagedetails/list). A terminológiával kapcsolatos részletek megismeréséhez tekintse meg a [használattal kapcsolatos kifejezéseket](../understand/understand-usage.md) ismertető cikket.

Itt látható egy példahívás a Usage Details API-hoz:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Ha többet szeretne tudni az {enrollmentId} és {billingPeriodId} változókról, tekintse meg a [Használati adatok – lista](/rest/api/consumption/usagedetails/list) API-cikket.

A mérőket és szűrőket tartalmazó következő táblázat segítséget nyújt a gyakori foglalási problémák megoldásához.

| **Az API-adatok típusa** | API-hívásművelet |
| --- | --- |
| **Összes díj (használat és vásárlások)** | a {metric} kicserélése ActualCostra |
| **Foglalási kedvezményben részesült használat** | a {metric} kicserélése ActualCostra<br><br>a {filter} kicserélése a következőre: properties/reservationId%20ne%20 |
| **Foglalási kedvezményben nem részesült használat** | a {metric} kicserélése ActualCostra<br><br>a {filter} kicserélése a következőre: properties/reservationId%20eq%20 |
| **Amortizált díjak (használat és vásárlások)** | a {metric} kicserélése AmortizedCostra |
| **Jelentés a nem használt foglalásokról** | a {metric} kicserélése AmortizedCostra<br><br>a {filter} kicserélése a következőre: properties/ChargeType%20eq%20'UnusedReservation' |
| **Foglalásvásárlások** | a {metric} kicserélése ActualCostra<br><br>a {filter} kicserélése a következőre: properties/ChargeType%20eq%20'Purchase'  |
| **Visszatérítések** | a {metric} kicserélése ActualCostra<br><br>a {filter} kicserélése a következőre: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Az új használati adatokat tartalmazó CSV-fájl letöltése

Ha Ön EA-rendszergazda, letöltheti az új használati adatokat tartalmazó CSV-fájlt a Azure Portal. Ezek az adatok nem érhetők el az EA Portalról (ea.azure.com). Az új adatok megtekintéséhez le kell töltenie a felhasználási adatokat tartalmazó fájlt az Azure Portalról (portal.azure.com).

Lépjen a [Költségkezelés + számlázás](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts) menüponthoz az Azure Portalon.

1. Válassza ki a számlázási fiókot.
2. Kattintson a **Felhasználás + díjak** lehetőségre.
3. Kattintson a **Letöltés** gombra.  
![Példa a használati adatokat tartalmazó CSV-fájl letöltési helyére az Azure Portalról](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. A **Felhasználás + díjak letöltése** lehetőségben az **Adatforgalom részletei 2. verzió** területen válassza ki az **Összes díj (használat és vásárlások)** lehetőséget, majd kattintson a Letöltés lehetőségre. Ismételje meg ezt az **Amortizált díjakkal (használat és vásárlások)** .


## <a name="common-cost-and-usage-tasks"></a>A költségekkel és a használattal kapcsolatos gyakori feladatok

A következő szakaszokban gyakori feladatok láthatók, amelyeket a legtöbben a foglalási költségeik és használati adataik megtekintésére használnak.

### <a name="get-reservation-purchase-costs"></a>Foglalásvásárlási költségek lekérése

A foglalásvásárlási költségek elérhetők a Tényleges költségadatokban. Szűrjön _ChargeType = Purchase_ (díjtípus = vásárlás) szerint. A ProductOrderID (Termékrendelési azonosító) alapján állapítható meg, hogy melyik foglalási rendeléshez tartozik a vásárlás.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Az alulhasznált foglalások mennyiségének és költségeinek lekérése

Kérje le az amortizált költségeket, és szűrjön _ChargeType_ _= UnusedReservation_ (díjtípus = fel nem használt foglalás) szerint. Megkapja a napi fel nem használt foglalások mennyiségét és költségeit. Az adatokat szűrheti foglalás vagy foglalási rendelés szerint a _ReservationId_ (Foglalásazonosító), illetve a _ProductOrderId_ (Termékrendelési azonosító) mezők használatával. Ha egy foglalás kihasználtsága 100%-os, az érték mennyisége 0.

### <a name="amortize-reservation-costs"></a>Amortizálhatja a foglalási költségeket

Kérje le az amortizált költségadatokat, és szűrjön a foglalási rendelésre a _ProductOrderID_ (Termékrendelési azonosító) használatával a foglalás napi amortizációs költségeinek lekéréséhez.

### <a name="chargeback-for-a-reservation"></a>Költséghelyi elszámolás a foglaláshoz

A foglalási használatot költséghelyi elszámolással más szervezetekhez számolhatja el előfizetés, erőforráscsoport vagy címke alapján. Az amortizált költségadatok a foglalás kihasználtságának pénzben kifejezett értékét adják meg a következő adattípusoknál:

- Erőforrások (például virtuális gépek)
- Erőforráscsoport
- Címkék
- Előfizetés

### <a name="get-the-blended-rate-for-chargeback"></a>Kevert összeg lekérése a költséghelyi elszámoláshoz

A kevert összeg meghatározásához kérje le az amortizált költségadatokat, és összesítse a teljes költséget. A virtuális gépeknél használhatja a MeterName (Mérő neve) vagy a ServiceType (Szolgáltatástípus) információkat az AdditionalInfo (További információk) JSON-adatokból. A kevert összeg meghatározásához ossza el a teljes költséget a felhasznált mennyiséggel.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Az optimális foglalási használat naplózása a virtuálisgép-példányok méretrugalmassága érdekében

Szorozza össze a kapott értéket az AdditionalInfo alatt található _RINormalizationRatio_ értékkel. Az eredmények jelzik, hogy hány órányi foglalási használat jelent meg a használati adatokban.

### <a name="determine-reservation-savings"></a>Foglalási megtakarítások meghatározása

Kérje le az amortizált költségadatokat, majd a fenntartott példány szerint szűrje az adatokat. Ezután:

1. Kérje le a becsült használatalapú fizetéses költségeket. Szorozza össze a _UnitPrice_ (egységár) értéket a _Quantity_ (Mennyiség) értékkel a becsült használatalapú fizetéses költségek meghatározásához, ha nem vonatkozott foglalási kedvezmény a használatra.
2. Kérje le a foglalási költségeket. Összesítse a _Költség_ értékeket a fenntartott példányért fizetett, pénzben kifejezett érték kiszámításához. Ez tartalmazza a foglalás használt és nem használt költségeit.
3. Vonja ki a foglalási költségeket a becsült használatalapú fizetéses költségekből a becsült megtakarítások kiszámításához.

Ne feledje, hogy ha kihasználatlan foglalása van, akkor a _ChargeType_ _UnusedReservation_ bejegyzését is számításba kell venni. Teljes kihasználtságú foglalás mellett érheti el a maximális megtakarítást. Minden egyes _UnusedReservation_ csökkenti a megtakarítás mértékét.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Foglalásvásárlások és amortizációs költségek a költségelemzésben

A foglalási költségek elérhetők a [költségelemzésben](https://aka.ms/costanalysis). Alapértelmezés szerint a költségelemzés a **Tényleges költséget** jeleníti meg, amely a számlán is meg fog jelenni. Azon foglalásvásárlások lebontásának megtekintéséhez, amelyek a kedvezményt felhasználó erőforrásokhoz társulnak, váltson az **Amortizált költségre**:

![Példa, hogy hol választható ki az amortizált költség a költségelemzésen belül](./media/understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Csoportosítás díjtípus szerint a használat, a vásárlások és a visszatérítések, illetve foglalás szerint a foglalás és az igény szerinti díjak lebontásának megtekintéséhez. Ne feledje, hogy a tényleges költségeknél csak a vásárlások vannak feltüntetve foglalási költségként, az amortizált költségeknél azonban a költségek azokhoz az erőforrásokhoz vannak elosztva, amelyek kedvezményben részesültek. Az amortizált költségeknél egy új díjtípus is megjelenik **UnusedReservation** (Fel nem használt foglalás) néven.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../../virtual-machines/prepay-reserved-vm-instances.md)
- [A foglalási kedvezmény alkalmazásának ismertetése](../manage/understand-vm-reservation-charges.md)
- [A Reservations díjában nem szereplő Windows-szoftverköltségek](reserved-instance-windows-software-costs.md)
- [Szolgáltatásnév használata a költségadatok lekértségéhez](../manage/assign-roles-azure-service-principals.md)
- [Cost Management-exportálások használata](../costs/tutorial-export-acm-data.md)
