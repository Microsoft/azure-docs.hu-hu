---
title: Az Azure Reservations kezelése
description: További tudnivalókat az Azure Reservations kezelésével foglalkozó részben talál. Lásd a foglalási hatókör módosításának, a foglalás felosztásának, illetve a kihasználtság optimalizálásának lépéseit.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 03/29/2021
ms.author: banders
ms.openlocfilehash: 1523ca77b06281b84ed57fbc0e2e49ffede85fb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934342"
---
# <a name="manage-reservations-for-azure-resources"></a>Foglalások kezelése az Azure-erőforrásoknál

Egy Azure-foglalás megvásárlása után előfordulhat, hogy a foglalást egy másik előfizetésre szeretné alkalmazni, módosítani szeretné, ki kezelheti a foglalást, vagy módosítaná a foglalás hatókörét. Egy foglalást ketté is oszthat annak érdekében, hogy egy másik előfizetéshez vásárolt példányokat alkalmazhasson.

Ha Azure Reserved Virtual Machine Instances-példányokat vásárolt, módosíthatja a foglalás optimalizálási beállítását. A foglalási kedvezmény vonatkozhat az ugyanahhoz a sorozathoz tartozó virtuális gépekre, vagy fenntartható az adatközpont kapacitása egy adott virtuálisgép-mérethez. Érdemes úgy optimalizálni a foglalásokat, hogy teljesen ki legyenek használva.

*A foglalás kezeléséhez szükséges engedély eltér az előfizetés engedélyeitől.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>A foglalási rendelés és a foglalás

Egy foglalás megvásárlásakor két objektum jön létre: A **foglalási rendelés** és a **foglalás**.

A vásárláskor a foglalási rendelés alá egy foglalás tartozik. Az olyan műveletek, mint például a felosztás, az egyesítés, a részleges visszatérítés vagy a csere, új foglalásokat hoznak létre a **foglalási rendelés** alatt.

Egy foglalási rendelés megtekintéséhez lépjen a **Foglalás** elemre > válassza ki a foglalást, majd a **foglalási rendelés azonosítóját**.

![Példa egy foglalási rendelés részleteire, köztük a foglalási rendelés azonosítójával ](./media/manage-reserved-vm-instance/reservation-order-details.png)

A foglalások az engedélyeket a foglalási rendelésektől öröklik. A foglalások cseréjéhez vagy visszafizetéséhez a felhasználót hozzá kell adni a foglalási rendeléshez.

## <a name="change-the-reservation-scope"></a>Foglalás hatókörének módosítása

 A foglalási kedvezmény vonatkozhat virtuális gépekre, SQL-adatbázisokra, Azure Cosmos DB-adatbázisokra és egyéb erőforrásokra, amelyek megfelelnek a foglalásnak, és a foglalás hatókörében futnak. A számlázási környezet a foglalás megvásárlásához használt előfizetéstől függ.

Egy foglalás hatókörének frissítése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válassza ki a foglalást.
4. Válassza a **Beállítások** > **Konfiguráció** lehetőséget.
5. Módosítsa a hatókört.

Ha a közös hatókört módosítja egyetlen hatókörre, akkor csak egy olyan előfizetést választhat ki, amelyben Ön a tulajdonos. Csak a foglalással megegyező számlázási környezetben található előfizetések választhatók ki.

A hatókör csak a használatalapú fizetéses díjszabású különálló előfizetésekre (MS-AZR-0003P vagy MS-AZR-0023P ajánlatok), az MS-AZR-0017P vagy az MS-AZR-0148P vállalati ajánlatra, illetve a CSP előfizetési típusokra vonatkozik.

## <a name="who-can-manage-a-reservation-by-default"></a>Alapértelmezés szerint kik kezelhetik a foglalásokat?

Alapértelmezés szerint a következő felhasználók tekinthetik meg és kezelhetik a foglalásokat:

- A foglalást és a fiók tulajdonosát a számlázási előfizetés megvásárlása után az Azure RBAC hozzáférést kap a foglalási rendeléshez.
-  A Nagyvállalati Szerződés és a Microsoft ügyfél-szerződés számlázási közreműködői a Cost Management + számlázási > foglalási tranzakciókban lévő összes foglalást kezelhetik > válassza a kék szalagcímet.

Két lehetősége van, ha más személyeknek is engedélyezni szeretné a foglalások kezelését:

- Hozzáférés-kezelés delegálása egy egyedi foglalási rendeléshez:
    1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
    1. Válassza a **Minden szolgáltatás** > **Foglalások** lehetőséget azoknak a foglalásoknak a megtekintéséhez, amelyekhez hozzáféréssel rendelkezik.
    1. Válassza ki azt a foglalást, amelyhez való hozzáférést delegálni szeretné más felhasználóknak.
    1. A foglalási adatoknál válassza ki a foglalási rendelést.
    1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
    1. Válassza a **Szerepkör-hozzárendelés hozzáadása** > **Szerepkör** > **Tulajdonos** lehetőséget. Ha korlátozott hozzáférést szeretne biztosítani, válasszon egy másik szerepkört.
    1. Írja be annak a felhasználónak az e-mail-címét, akit tulajdonosként kíván hozzáadni.
    1. Válassza ki a felhasználót, majd válassza a **Mentés** lehetőséget.

- Adjon hozzá egy felhasználót számlázási rendszergazdaként egy Nagyvállalati Szerződéshez vagy egy Microsoft Ügyfélszerződéshez:
    - Nagyvállalati Szerződés esetén vegye fel a felhasználókat _vállalati rendszergazda_ szerepkörbe az összes foglalási rendelés megtekintéséhez és kezeléséhez, amelyekre a Nagyvállalati Szerződés vonatkozik. A _Vállalati rendszergazda (csak olvasási)_ szerepkörrel rendelkező felhasználók csak megtekinthetik a foglalásokat. A részleg rendszergazdái és a fióktulajdonosai nem tekinthetik meg a foglalásokat _kivéve_, ha kifejezetten a hozzáférés-vezérlés (IAM) használatával lettek hozzáadva. További információért lásd: [Az Azure Enterprise szerepköreinek kezelése](../manage/understand-ea-roles.md).

        _A vállalati rendszergazdák tulajdonukba vehetik a foglalási rendeléseket, és a hozzáférés-vezérlés (IAM) használatával más felhasználókat is hozzáadhatnak a foglalásokhoz._
    - Microsoft Ügyfélszerződés esetén a számlázási profil tulajdonosi szerepkörével vagy a számlázási profil közreműködői szerepkörrel rendelkező felhasználók a számlázási profillal végzett összes foglalásvásárlást kezelhetik. A számlázási profil olvasói és a számlakezelők a számlázási profillal kifizetett összes foglalást megtekinthetik. A foglalásokat azonban nem módosíthatják.
    További információkért lásd [a számlázási profil szerepköreit és azok feladatait](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks) ismertető cikket.

### <a name="how-billing-administrators-view-or-manage-reservations"></a>A foglalások megtekintése és kezelése a számlázási rendszergazdák számára

1. Válassza ki a **Cost Management + Billing** lehetőséget, majd a lap bal oldalán a **Foglalási tranzakciók** lehetőséget.
2. Ha rendelkezik a szükséges számlázási engedélyekkel, akkor megtekintheti és kezelheti a foglalásokat. Ha egy foglalást sem lát, győződjön meg róla, hogy azzal az Azure AD-bérlővel jelentkezett be, amelyen a foglalás létre lett hozva.

## <a name="split-a-single-reservation-into-two-reservations"></a>Egy foglalás felosztása két foglalásba

 Miután egy foglalásban egynél több erőforrás-példányt vásárol, érdemes lehet a foglalásban lévő példányokat különböző előfizetésekhez hozzárendelni. Alapértelmezés szerint minden példánynak egy hatóköre van – egyetlen előfizetés, erőforráscsoport vagy közös. Tegyük fel, hogy foglalást vásárolt 10 virtuálisgép-példányhoz, és megadta, hogy a foglalási hatókör legyen az A előfizetés, most pedig módosítani szeretné a foglalások hatókörét, hogy hét virtuálisgép-példánynak az A előfizetés, a maradék háromnak pedig a B előfizetés legyen a hatóköre. A foglalás felosztása ezt lehetővé teszi. A foglalás felosztása után a rendszer törli az eredeti foglalásazonosítót, és két új foglalást hoz létre. A felosztás nem befolyásolja a foglalási rendelést – a felosztással nem jön létre új kereskedelmi tranzakció, és az új foglalások a felosztott foglaláséval megegyező befejezési dátummal rendelkeznek.

 A foglalások kettéosztásához használhatja a PowerShellt, a parancssori felületet, vagy az API-t.

### <a name="split-a-reservation-by-using-powershell"></a>Foglalások felosztása a PowerShell használatával

1. Kérje le a foglalási rendelés azonosítóját a következő parancs futtatásával:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Kérje le a foglalás részleteit:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Ossza ketté a foglalást, és ossza el a példányokat:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. A hatókört az alábbi parancs futtatásával frissítheti:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](exchange-and-refund-azure-reservations.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>A Reserved VM Instances optimalizálási beállításának módosítása

 Amikor megvásárol egy fenntartott virtuálisgép-példányt, kiválaszthatja, hogy a példány méretének rugalmassága vagy a példány kapacitása legyen-e a prioritás. A rugalmas példányméret a foglalás kedvezményét az ugyanabban a [virtuálisgép-méretcsoportban](../../virtual-machines/reserved-vm-instance-size-flexibility.md) található egyéb virtuális gépekre is alkalmazza. A kapacitás prioritása kijelöli az üzemelő példányok számára legfontosabb adatközpont-kapacitást. Ezzel a lehetőséggel biztos lehet benne, hogy szükség esetén nem lesz akadálya újabb virtuálisgép-példányok indításának.

Alapértelmezés szerint ha a foglalás hatóköre közös, a példány méretének rugalmassága be van kapcsolva. Az adatközpont kapacitása nincs priorizálva az üzemelő virtuálisgép-példányok számára.

Az egyszeres hatókörű foglalások a kapacitás priorizálására is optimalizálhatók a virtuálisgép-példányok méretének rugalmassága helyett.

A foglalás optimalizálási beállításának frissítése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válassza ki a foglalást.
4. Válassza a **Beállítások** > **Konfiguráció** lehetőséget.
  ![A Konfiguráció elemet bemutató példa](./media/manage-reserved-vm-instance/add-product03.png)
5. Módosítsa az **Optimalizálás** beállítást.
  ![Az Optimalizálás beállítást bemutató példa](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>Foglalás kihasználtságának optimalizálása

Az Azure-foglalások használatából eredő megtakarítások kizárólag a tartós erőforrás-használatból származnak. Ha megvásárol egy foglalást, akkor azért fizet, ami tulajdonképpen a 100%-ig lehetséges erőforrás-használat egy egy- vagy hároméves időszak alatt. Igyekezzen maximálisan kihasználni a foglalást, hogy ezzel minél több megtakarítást érhessen el. Az alábbi szakaszok leírják, hogyan érdemes monitorozni a foglalásokat, és optimalizálni a használatukat.

### <a name="view-reservation-use-in-the-azure-portal"></a>Foglalás kihasználtságának megtekintése az Azure Portalon

A foglalás kihasználtságának megtekintésére az egyik mód az Azure Portal használata.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **Minden szolgáltatás** > [**Reservations**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) lehetőséget, és figyelje meg egy adott foglalás **Kihasználtság (%)** mutatóját.  
  ![Kép a foglalások listájáról](./media/manage-reserved-vm-instance/reservation-list.png)
3. Válasszon ki egy foglalást.
4. Tekintse át a foglalás kihasználtságának időre vetített trendjét.
  ![Kép egy foglalás kihasználtságáról ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Foglalás kihasználtságának megtekintése egy API-val

Ha Ön Nagyvállalati Szerződéssel rendelkező (EA-) ügyfél, programozott módon megtekintheti, hogyan vannak kihasználva a szervezeten belüli foglalások. A nem használt foglalásokról a használati adatokon keresztül értesülhet. A foglalási díjak áttekintésekor vegye figyelembe, hogy az adatok fel vannak osztva a tényleges költségekre és az amortizációs költségekre. A tényleges költség olyan adatokat szolgáltat, amelyeket összevethet a havi számlájával. Emellett tartalmazza a foglalás vételárát és a foglalás alkalmazásának részleteit is. Az amortizált költség hasonló a tényleges költséghez, azzal a különbséggel, hogy a foglalások használatának érvényben levő díjait időarányosan jeleníti meg. Az amortizált költség adataiban az is megjelenik, hogy a foglalás mennyi ideig nem volt kihasználva. A nagyvállalati ügyfeleknek szánt használati adatokkal kapcsolatos további információkért tekintse meg a [Nagyvállalati Szerződés foglalási költségeinek és kihasználtságának lekérése](understand-reserved-instance-usage-ea.md) szakaszt.

Egyéb előfizetéstípusok esetén használja a [Foglalások összegzése – Felsorolás foglalási rendelés és foglalás szerint](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation) API-t.

### <a name="optimize-your-reservation"></a>Foglalás optimalizálása

Ha azt tapasztalja, hogy a szervezet foglalásai nincsenek kihasználva:

- Ellenőrizze, hogy a szervezet által létrehozott virtuális gépek megfelelnek-e a foglalásban szereplő virtuálisgép-méretnek.
- Ellenőrizze, hogy a példányméret rugalmassága be van-e kapcsolva. További információk: [Foglalások kezelése – A Reserved VM Instances optimalizálási beállításának módosítása](#change-optimize-setting-for-reserved-vm-instances).
- Módosítsa a foglalás hatókörét _közösre_, hogy az szélesebb körre vonatkozzon. További információk: [Foglalás hatókörének módosítása](#change-the-reservation-scope).
- Fontolja meg, hogy becseréli a nem használt mennyiséget. További információk: [Lemondások és cserék](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok a foglalások az Azure-ban?](save-compute-costs-reservations.md)

Szolgáltatáscsomag vásárlása:
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../../azure-sql/database/reserved-capacity-overview.md)
- [Előre fizetés fenntartott Azure Cosmos DB-kapacitással rendelkező Azure Cosmos DB-erőforrásokért](../../cosmos-db/cosmos-db-reserved-capacity.md)

Szoftvercsomag vásárlása:
- [Előre fizetés Azure Reservations-beli Red Hat-szoftvercsomagokért](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Előre fizetés Azure Reservations-beli SUSE-szoftvercsomagokért](../../virtual-machines/linux/prepay-suse-software-charges.md)

A kedvezmények és a kihasználtság ismertetése:
- [A virtuális gépek foglalására vonatkozó kedvezmény alkalmazásának ismertetése](../manage/understand-vm-reservation-charges.md)
- [A Red Hat Enterprise Linux szoftvercsomag kedvezményei alkalmazásának ismertetése](understand-rhel-reservation-charges.md)
- [A SUSE Linux Enterprise szoftvercsomag kedvezményei alkalmazásának ismertetése](understand-suse-reservation-charges.md)
- [Az egyéb foglalási kedvezmények alkalmazásának ismertetése](understand-reservation-charges.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
- [A Reservations díjában nem szereplő Windows-szoftverköltségek](reserved-instance-windows-software-costs.md)
