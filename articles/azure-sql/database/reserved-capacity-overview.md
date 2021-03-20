---
title: Számítási költségek mentése fenntartott kapacitással
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Megtudhatja, hogyan vásárolhat Azure SQL Database és az SQL felügyelt példányának fenntartott kapacitását a számítási költségek mentéséhez.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 10/13/2020
ms.openlocfilehash: cacd43502a01352c24f8fcfd85b12aac781dccbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602512"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Fenntartott kapacitással rendelkező erőforrások költségeinek mentése – Azure SQL Database & SQL felügyelt példánya
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

A Azure SQL Database és az SQL felügyelt példányával pénzt takaríthat meg, ha kifizeti a számítási erőforrások foglalását az utólagos elszámolású árakhoz képest. A fenntartott kapacitással a SQL Database és/vagy az SQL felügyelt példányainak egy vagy három éves időszakra vonatkozó kötelezettségvállalását kell elvégeznie, hogy a számítási költségek jelentős kedvezményt kapjanak. A fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a központi telepítési típust, a teljesítménnyel kapcsolatos szintet és a kifejezést.

A foglalást nem kell hozzárendelni egy adott adatbázishoz vagy felügyelt példányhoz. A már futó vagy újonnan üzembe helyezett meglévő központi telepítések automatikusan megkapják a kedvezményt. A foglalás megvásárlásával véglegesíti a számítási költségek felhasználását egy vagy három év alatt. A foglalás megvásárlása után a foglalási attribútumoknak megfelelő számítási díjakra már nem számítunk fel díjat az utólagos elszámolású díjszabásban. 

A foglalás az elsődleges és a számlázható másodlagos számítási replikára is vonatkozik, de nem fedi le a szolgáltatáshoz kapcsolódó szoftver-, hálózatkezelési és tárolási díjakat. A foglalási időszak végén a számlázási juttatás lejár, és az adatbázis vagy a felügyelt példány díját az utólagos elszámolású díjszabás szerint számoljuk el. A foglalások nem újítják meg automatikusan. A díjszabással kapcsolatos információkért tekintse meg a [fenntartott kapacitás ajánlatát](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Fenntartott kapacitást a [Azure Portal](https://portal.azure.com)is vásárolhat. A foglalásért fizethet [előre vagy havi részletekben](../../cost-management-billing/reservations/prepare-buy-reservation.md). Fenntartott kapacitás vásárlása:

- Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
- Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie. Fenntartott kapacitás.

Ha többet szeretne megtudni arról, hogy a vállalati ügyfelek és az utólagos elszámolású ügyfelek milyen díjat számítanak fel a foglalások beszerzésére, tekintse meg az [Azure foglalás használatának ismertetése a nagyvállalati beléptetéssel](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) és [Az Azure foglalás használatának](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)ismertetése az utólagos elszámolású előfizetéshez.

> [!NOTE]
> A fenntartott kapacitás megvásárlása nem foglalja le előre a használatra vonatkozó infrastruktúra-erőforrásokat (virtuális gépeket vagy csomópontokat).

## <a name="determine-correct-size-before-purchase"></a>A megfelelő méret meghatározása a vásárlás előtt

A foglalás méretének a meglévő vagy hamarosan üzembe helyezett adatbázis vagy a felügyelt példány egy adott régión belüli teljes számítási mennyiségén kell alapulnia, és ugyanazt a teljesítményszint és hardver generációt kell használnia.

Tegyük fel például, hogy egy általános célt futtat, Gen5 – 16 virtuális mag rugalmas készletet és két üzleti szempontból kritikus Gen5 – 4 virtuális mag önálló adatbázist. Továbbá Tételezzük fel, hogy a következő hónapban szeretne üzembe helyezni egy további általános célú Gen5 – 16 virtuális mag rugalmas készletet és egy üzleti szempontból kritikus Gen5 – 32 virtuális mag rugalmas készletet. Azt is tegyük fel, hogy tudnia kell, hogy legalább 1 évig szüksége lesz ezekre az erőforrásokra. Ebben az esetben a 32 (2x16) virtuális mag 1 éves foglalást kell vásárolnia egyetlen adatbázishoz/rugalmas készlethez, általános célú Gen5 és 40 (2x4 + 32) virtuális mag 1 éves foglalás az önálló adatbázis/rugalmas készlet üzleti szempontból kritikus fontosságú – Gen5.

## <a name="buy-reserved-capacity"></a>Fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd a **foglalások** ablaktáblán válassza a **SQL Database** lehetőséget a SQL Database új foglalásának megvásárlásához.
4. Töltse ki a kötelező mezőket. A SQL Database és az SQL felügyelt példányának meglévő adatbázisai, amelyek megfelelnek a kiválasztott attribútumoknak, a fenntartott kapacitás kedvezményét kapják meg. A kedvezményt megkapó adatbázisok vagy felügyelt példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.

    ![A fenntartott kapacitás vásárlásának elküldése előtt képernyőkép](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    A következő táblázat a kötelező mezőket ismerteti.
    
    | Mező      | Leírás|
    |------------|--------------|
    |Előfizetés|A kapacitás foglalásának megfizetéséhez használt előfizetés. Az előfizetéshez tartozó fizetési módot a foglalás előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek (az MS-AZR-0017P vagy MS-AZR-0148P) vagy az utólagos elszámolású díjszabásnak (az MS-AZR-0003P vagy az MS-AZR-0023P) megfelelő egyedi szerződésnek kell lennie. Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó Azure-előrefizetés (korábbi nevén pénzügyi keret) egyenlegéből lesznek levonva, illetve túlhasználatként lesznek számlázva. Az utólagos elszámolású előfizetések esetében az előfizetés díjait a bankkártya vagy a számla fizetési módja alapján számítjuk fel.|
    |Hatókör       |A virtuális mag foglalási hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a lehetőséget választja <br/><br/>**Megosztva** a virtuális mag foglalási kedvezményt a számlázási környezetben lévő előfizetésekben futó adatbázisra vagy felügyelt példányra alkalmazza a rendszer. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.<br/><br/>**Egyszeri előfizetés** esetén a virtuális mag foglalási kedvezményt az előfizetésben lévő adatbázisokra vagy felügyelt példányokra alkalmazza a rendszer. <br/><br/>**Egy erőforráscsoport**, a foglalási kedvezmény a kiválasztott előfizetésben található adatbázisok vagy felügyelt példányok példányaira, valamint az adott előfizetéshez tartozó kiválasztott erőforráscsoporthoz lesz alkalmazva.|
    |Region      |Az Azure-régió, amelyre a kapacitás foglalása vonatkozik.|
    |Központi telepítés típusa|Az az SQL-erőforrástípus, amelyre a foglalást megvásárolni szeretné.|
    |Teljesítményszint|Az adatbázisok vagy a felügyelt példányok szolgáltatási szintje. |
    |Időszak        |Egy vagy három év.|
    |Mennyiség    |A kapacitás-foglalás keretében megvásárolt számítási erőforrások mennyisége. A mennyiség a kiválasztott Azure-régióban és a lefoglalt teljesítményszint virtuális mag, és a számlázási kedvezményt kapja. Ha például futtatja vagy tervezi több adatbázis futtatását az USA keleti régiójában lévő Gen5 16 virtuális mag teljes számítási kapacitásával, akkor a mennyiséget 16-ra kell beállítania, hogy maximalizálja az összes adatbázis előnyeit. |

1. Tekintse át a kapacitás foglalásának költségeit a costs ( **költségek** ) szakaszban.
1. Válassza a **Beszerzés** lehetőséget.
1. Válassza a **foglalás megtekintése** lehetőséget a vásárlás állapotának megtekintéséhez.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Virtuális mag méretének rugalmassága

a virtuális mag méretének rugalmassága segít a teljesítmény-és a régión belüli vertikális felskálázásban, a fenntartott kapacitási juttatás elvesztése nélkül. A fenntartott kapacitással rugalmasan helyezheti át a gyakran használt adatbázisokat a rugalmas készletekbe (ugyanabban a régióban és teljesítményszint) a szokásos műveletek részeként anélkül, hogy elveszítené a fenntartott kapacitást. Ha egy nem alkalmazott puffert tart fenn a foglalásban, akkor a teljesítménybeli tüskéket hatékonyan kezelheti a költségkeret túllépése nélkül.

## <a name="limitation"></a>Korlátozás

SQL Databaseban nem foglalhat le DTU-alapú (alapszintű, standard vagy prémium) adatbázisokat.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A virtuális mag foglalási kedvezmény automatikusan érvényes azon adatbázisok vagy felügyelt példányok számára, amelyek megfelelnek a kapacitás foglalási hatókörének és attribútumainak. A kapacitás foglalásának hatókörét a [Azure Portal](https://portal.azure.com), a PowerShell, az Azure CLI vagy az API használatával frissítheti.

A kapacitás-foglalás kezelésével kapcsolatos további információkért lásd a [fenntartott kapacitások kezelése](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)című témakört.

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Az Azure Reservations kezelése](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Az Azure Reservations-kedvezmény ismertetése](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](/partner-center/azure-reservations)