---
title: Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai
description: Ismerje meg, hogyan cserélheti be vagy térítheti vissza az Azure Reservations foglalásait. A foglalások cseréjéhez vagy visszatérítéséhez tulajdonosi hozzáféréssel kell rendelkeznie a foglalásrendeléshez.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: bd16bbbe33876a3c44b20c5d1756b83814f9b17d
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601950"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai

Az Azure Reservations biztosítja a rugalmasságot a változó igények kielégítéséhez. Megadhatja az azonos típusú foglalások foglalását. Például több számítási foglalást is visszaadhat, beleértve az Azure dedikált gazdagépet, az Azure VMware-megoldást és az Azure Virtual Machines egyszerre. Más szóval a foglalási termékek egymással megváltoztathatók, ha ugyanaz a foglalási típus. Egy másik példában több SQL Database-foglalási típust is cserélhet, beleértve a felügyelt példányokat és rugalmas készlet egymással.

Nem válthat azonban eltérő foglalásokat. Nem cserélhet például SQL Databasehoz Cosmos DB foglalást.

A foglalások cseréjével egy másik régióban is megvásárolhatja a hasonló típusú más foglalásokat. Például megadhat egy, az USA nyugati régiójában található foglalást, amely Nyugat-Európában található.

Ha kicserél egy foglalást, az adott időszakot egy évig, három évre módosíthatja.

A foglalásokért visszatérítést is kérhet, de a visszamondott foglalási kötelezettségvállalások teljes összege a számlázási hatókörben (például Nagyvállalati Szerződés, Microsoft-ügyfélszerződés és Microsoft-partnerszerződés) nem haladhatja meg az 50 000 USD összeget az elmúlt 12 hónapra vonatkozóan.

Az Azure Databricks fenntartott kapacitás, az Azure VMware Solution by CloudSimple-foglalás, az Azure Red Hat Open Shift-foglalás, a Red Hat-csomagok és a SUSE Linux-csomagok nem jogosultak visszatérítésre.

Az önkiszolgáló csere- és lemondási lehetőség nem érhető el az Egyesült Államok kormányának nagyvállalati szerződésével rendelkező ügyfelei számára. Az Egyesült Államok kormányának (US Government) egyéb előfizetési típusai támogatottak, például a használatalapú fizetés és a Felhőszolgáltató (CSP).

> [!NOTE]
> - **Meglévő foglalások cseréjéhez vagy visszatérítéséhez tulajdonosi hozzáféréssel kell rendelkeznie a foglalási rendeléshez**. Elvégezhető [a foglalást kezelő felhasználók hozzáadása vagy módosítása](./manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
> - A Microsoft jelenleg nem számol fel korai felmondási díjat a foglalásokhoz kapcsolódó visszatérítések után. Előfordulhat, hogy a visszatérítések a jövőben díjkötelesek lesznek. Jelenleg még nem határoztuk meg a díj bevezetésének dátumát.

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>Meglévő foglalások cseréje vagy visszatérítése

A foglalás cseréjét az [Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) végezheti el.

1. Válassza ki a visszatéríteni kívánt foglalásokat, majd válassza az **Átváltás** lehetőséget.  
    [![Példa kép a visszaadni kívánt foglalásokról](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. Válassza ki a megvásárolni kívánt virtuálisgép-terméket, és adja meg a mennyiséget. Győződjön meg arról, hogy az új vásárlás végösszege nagyobb, mint a visszatérítés végösszege. [A vásárlás előtt határozza meg a megfelelő méretet.](../../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)  
    [![Példa kép a cserével vásárolni kívánt virtuálisgép-termékről](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. Tekintse át és fejezze be a tranzakciót.  
    [![Példa kép a cserével vásárolni kívánt virtuálisgép-termékről, a csere teljesítésével](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

Egy foglalás visszatérítéséhez lépjen a **Foglalás részleteire**, és válassza a **Visszatérítés** lehetőséget.

## <a name="exchange-multiple-reservations"></a>Több foglalás cseréje

Egy műveletben hasonló típusú foglalásokat adhat vissza.

A foglalások cseréjekor az új beszerzési pénznem összegének nagyobbnak kell lennie a visszatérítési összegnél. Ha az új vásárlási összeg kisebb a visszatérítési összegnél, hibaüzenet jelenik meg. Ha a hiba jelenik meg, csökkentse a visszaadni kívánt mennyiségét, vagy növelje a megvásárolni kívánt mennyiséget.

1. Jelentkezzen be az Azure Portalra, és lépjen a **Foglalások** területre.
1. A foglalások listájában jelölje be az Exchange-hez használni kívánt foglalások jelölőnégyzetét.
1. Az oldal tetején válassza az **Exchange** lehetőséget.
1. Ha szükséges, módosítsa az egyes foglalások visszaadott mennyiségét.
1. Ha kijelöli az automatikus kitöltés visszaküldési mennyiségét, az **összes** megadásával kitöltheti a listát az egyes foglalásokhoz tartozó teljes mennyiséggel, vagy **optimalizálhatja a kihasználtságot (7 napos)** a lista egy olyan mennyiséggel való kitöltéséhez, amely a használat utolsó hét napja alapján optimalizálja a kihasználtságot. **Válassza az alkalmaz lehetőséget**.
1. A lap alján válassza a **Tovább: vásárlás** lehetőséget.
1. A vásárlás lapon válassza ki azokat az elérhető termékeket, amelyeket szeretne cserélni. Több különböző típusú terméket is kijelölhet.
1. A megvásárolni kívánt termék kiválasztása ablaktáblán válassza ki a kívánt termékeket, majd válassza a **Hozzáadás a kosárhoz** lehetőséget, majd válassza a **Bezárás** lehetőséget.
1. Ha elkészült, kattintson a **Tovább gombra: felülvizsgálat**.
1. Tekintse át a foglalások visszaadását és a megvásárolni kívánt új foglalásokat, majd válassza az **Exchange megerősítése** lehetőséget.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Nem prémium szintű tároló cseréje prémium tárolóra

Ha egy olyan virtuálisgép-méretre rendelkezik foglalással, amely nem támogatja a Premium Storage-ot, becserélheti egy olyan egyenértékű VM-méretre, amely támogatja azt. Például egy _F1_ méretűt egy _F1s_ méretűre. A csere végrehajtásához lépjen a Foglalás részleteire, és válassza a **Csere** lehetőséget. A csere nem állítja vissza a fenntartott példány időtartamát, és nem hoz létre új tranzakciót.

## <a name="how-transactions-are-processed"></a>A tranzakciók feldolgozása

Először a Microsoft megszakítja a meglévő foglalást, és visszatéríti az adott foglalásra vonatkozó arányosított összeget. Ha van csere, akkor a rendszer feldolgozza az új vásárlást. A Microsoft a visszatérítések feldolgozását az alábbi módszerek valamelyike szerint végzi, a fióktípustól és a fizetés módjától függően:

### <a name="enterprise-agreement-customers"></a>Nagyvállalati Szerződéssel rendelkező ügyfelek

A pénz hozzáadódik a csereprogramok és visszatérítések Azure-előrefizetéséhez (korábbi nevén pénzügyi keret), ha az eredeti vásárlás is ilyen módon történt. Ha a foglalást használó Azure-előrefizetés időtartama már nem aktív, akkor a kredit hozzáadódik a jelenlegi Nagyvállalati Szerződéshez tartozó Azure-előrefizetés időtartamához. A jóváírás a visszatérítés napjától kezdve 90 napig érvényes. A fel nem használt jóváírás 90 nap után lejár.

Ha az eredeti vásárlás túlhasználatként történt, a Microsoft ismét megnyitja és módosítja az eredeti, a foglalás vásárlásáról készült számlát és az összes későbbi számlát. A Microsoft kiállít egy jóváírási emlékeztetőt a visszatérítésekről.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Használatalapú fizetéses számlás fizetések és a CSP-program

A rendszer az eredeti foglalás vásárlási számláját érvényteleníti, és létrehoz egy új számlát a visszatérítésről. Csere esetén az új számla tartalmazza a visszatérítést és az új vásárlást. A visszatérítés összegét a rendszer a vásárláshoz igazítja. Ha csak a foglalás lett visszatérítve, akkor az arányosított összeg a Microsoftnál marad, és egy későbbi foglalás vásárlásakor lesz beszámítva. Ha használatalapú fizetéses díjszabás mellett vásárolt foglalást, és később átvált egy CSP-re, a foglalás pótdíj nélkül visszaváltható, majd ismét megvásárolható.

### <a name="pay-as-you-go-credit-card-customers"></a>Használatalapú fizetéses hitelkártyás ügyfelek

A rendszer az eredeti számlát érvényteleníti, és létrehoz egy új számlát. A pénz az eredeti vásárláshoz használt hitelkártyára lesz visszatérítve. Ha azóta módosította a kártyát, [forduljon az ügyfélszolgálathoz.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="cancel-exchange-and-refund-policies"></a>Lemondással, cserével és visszatérítéssel kapcsolatos szabályzatok

Az Azure-ban a következő szabályzatok érvényesek a lemondásokra, cserékre és visszatérítésekre.

**Csereszabályzatok**

- Egyszerre több létező foglalást is becserélhet egy ugyanolyan típusú új foglalás vásárlásakor. Az adott típusú foglalásokat nem lehet másik típusúra cserélni. Például nem lehet egy virtuális gép foglalását egy SQL-foglalás megvásárlására becserélni. A cserék során a foglalások bármely tulajdonsága módosítható, így a család, a sorozat, a verzió, a termékváltozat, a régió, a mennyiség és az időtartam is.
- A cseréket csak foglalások tulajdonosai dolgozhatják fel. További tudnivalók: [A foglalást kezelő felhasználók hozzáadása vagy módosítása](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
- A cserét a rendszer visszatérítésként és újravásárlásként dolgozza fel – vagyis külön tranzakciókat hoz létre a lemondáshoz és az új foglalás vásárlásához. A becserélt foglalások arányosított foglalási összegét visszatéríti a rendszer. Az új vásárlásért pedig teljes díjat kell fizetni. Az arányosított foglalási összeg a visszaadott foglalás nap szerint kiszámított maradványértéke.
- A foglalások akkor is becserélhetők vagy visszatéríthetők, ha a megvásárlásukhoz használt Nagyvállalati Szerződés lejárt, és új szerződésként lett megújítva.
- Az új foglalás élettartamra vonatkozó kötelezettségvállalásának a visszatérített foglalásra vonatkozó megmaradt kötelezettségvállalással egyenlőnek vagy annál nagyobb összegűnek kell lennie. Példa: egy hároméves, havi 100 USD összegű foglalás esetén, amelyet a 18. fizetés után becserélnek, az új foglalás élettartamára vonatkozó kötelezettségvállalásnak 1800 USD vagy nagyobb összegűnek kell lennie (havonta vagy előre fizetve).
- A csere keretében megvásárolt új foglalás új időtartamot kap, amelynek a csere időpontja lesz a kezdete.
- A cserékre semmilyen pótdíj vagy éves korlát nem vonatkozik.

**Visszatérítési szabályzatok**

- Jelenleg nem számítunk fel korai felmondási díjat, de lehetséges, hogy a jövőbeli lemondásokért 12%-os korai felmondási díj lesz felszámítva.
- Egy számlázási profil vagy egy regisztráció esetében a lemondott kötelezettségvállalás teljes összege nem haladhatja meg az 50 000 USD-t az elmúlt 12 hónapra vonatkozóan. Például egy hároméves, havi 100 USD összegű, a 18. hónapban visszatérítendő foglalás esetén a lemondott kötelezettségvállalás értéke 1800 USD. A visszatérítés után az új visszatérítési korlát 48 200 USD lesz. Ezen visszatérítéstől számított 365 nap múlva a 48 200 USD korlátot megnöveljük 1800 USD-vel, így az új keret 50 000 USD lesz. A számlázási profilhoz vagy az EA-regisztrációhoz tartozó minden más foglaláslemondás ugyanezt a keretet csökkenti, és ugyanez a feltöltési logika érvényesül.
- Az Azure nem dolgozza fel azokat a visszatérítéseket, amelyek egy számlázási profil vagy EA-regisztráció esetében meghaladják az 50 000 USD-t az elmúlt 12 hónapra vonatkozóan.
    - Az Exchange-ből eredő visszatérítések nem számítanak bele a visszatérítési korlátba.
- A visszatérítést a rendszer a vételár vagy a foglalás jelenlegi ára közül az alacsonyabb összeg alapján számítja ki.
- A visszatérítéseket csak a foglalásrendelések tulajdonosai dolgozhatják fel. További tudnivalók: [A foglalást kezelő felhasználók hozzáadása vagy módosítása](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- A foglalások kezelésének megismerése érdekében tekintse meg [Az Azure Reservations kezelése](manage-reserved-vm-instance.md) szakaszt.
- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
    - [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
    - [A Reservations kezelése az Azure-ban](manage-reserved-vm-instance.md)
    - [A foglalási kedvezmény alkalmazásának ismertetése](../manage/understand-vm-reservation-charges.md)
    - [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
    - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
    - [A Reservations díjában nem szereplő Windows-szoftverköltségek](reserved-instance-windows-software-costs.md)
    - [Azure Reservations a CSP programban](/partner-center/azure-reservations)