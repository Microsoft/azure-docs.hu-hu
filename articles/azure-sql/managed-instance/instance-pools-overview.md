---
title: Mi az Azure SQL felügyelt példányok készlete?
titleSuffix: Azure SQL Managed Instance
description: Ismerje meg az Azure SQL felügyelt példány-készletek (előzetes verzió) szolgáltatását, amely kényelmes és költséghatékony megoldást kínál a kisebb SQL Server adatbázisok felhőbe való átméretezésére és több felügyelt példány kezelésére.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: bc345509db1c2a14afb0ae781eccad8f77395c18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97347064"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>Mi az Azure SQL felügyelt példányok készlete (előzetes verzió)?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányának példány-készletei kényelmes és költséghatékony megoldást biztosítanak a kisebb SQL Server példányok felhőbe való átméretezésére.

A példánykészletek lehetővé teszik a számítási erőforrások migrálási követelményeknek megfelelő, előzetes létrehozását. Ezt követően több egyedi felügyelt példányt is üzembe helyezhet, az előzetesen megadott számítási szintig. Ha például előre kiépít 8 virtuális mag, telepítheti a 2 2-virtuális mag és a 1 4-virtuális mag példányt, majd áttelepítheti az adatbázisokat ezekbe a példányokra. A példányok elérhetővé válása előtt a kisebb és kisebb számítási igényű munkaterheléseket gyakran egy nagyobb felügyelt példányba kell összevonni a felhőbe való Migrálás során. Az adatbázis-csoportok nagy példányra való áttelepítéséhez általában körültekintő kapacitás-tervezésre és erőforrás-szabályozásra, további biztonsági megfontolásokra és néhány további adatösszesítésre van szükség a példány szintjén.

A példányok emellett támogatják a natív VNet-integrációt is, így több példány-készletet és több példányt is telepíthet ugyanabban az alhálózatban.

## <a name="key-capabilities"></a>Főbb képességek

A példány-készletek a következő előnyöket nyújtják:

1. 2 virtuális mag-példányok üzemeltetése. *\* Csak a példányok készletében lévő példányok esetében*.
2. Kiszámítható és gyors példány üzembe helyezési ideje (legfeljebb 5 perc).
3. Minimális IP-cím kiosztása.

A következő ábra egy olyan példány-készletet ábrázol, amelynek több felügyelt példánya van üzembe helyezve egy virtuális hálózati alhálózaton belül.

![példány-készlet több példánnyal](./media/instance-pools-overview/instance-pools1.png)

A példány-készletek lehetővé teszik több példány üzembe helyezését ugyanazon a virtuális gépen, ahol a virtuális gép számítási mérete a készlethez lefoglalt virtuális mag teljes száma alapján történik. Ez az architektúra lehetővé teszi a virtuális gép több példányra történő *particionálását* , ami bármilyen támogatott méret lehet, beleértve a 2 virtuális mag (2 – virtuális mag példányok csak a készletekben található példányok esetében).

A kezdeti üzembe helyezést követően a készlet példányain lévő felügyeleti műveletek sokkal gyorsabban jelennek meg. Ennek az az oka, hogy a [virtuális fürt](connectivity-architecture-overview.md#high-level-connectivity-architecture) üzembe helyezése vagy kiterjesztése (a virtuális gépek dedikált készlete) nem része a felügyelt példány kiépítésének.

Mivel a készlet összes példánya ugyanazt a virtuális gépet használja, a teljes IP-foglalás nem függ az üzembe helyezett példányok számától, ami kényelmes a keskeny IP-tartománnyal rendelkező alhálózatokban való üzembe helyezéshez.

Az egyes készletek csak kilenc IP-címet foglalnak magukban (az alhálózat öt IP-címével együtt, amely a saját igényeihez van lefoglalva). További részletekért tekintse [meg az alhálózati méretre vonatkozó követelményeket az egyes példányok esetében](vnet-subnet-determine-size.md).

## <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

Az alábbi lista azokat a fő felhasználási eseteket tartalmazza, amelyekben a példány-készleteket figyelembe kell venni:

- *SQL Server példányok csoportjának* áttelepítése egy időben, ahol a többség kisebb méretű (például 2 vagy 4 virtuális mag).
- Olyan forgatókönyvek, amelyekben a *kiszámítható és a rövid példányok létrehozása vagy skálázása* fontos. Például egy új bérlő üzembe helyezése egy több-bérlős SaaS-alkalmazás környezetében, amely a példány szintű képességeket igényli.
- Olyan forgatókönyvek, amelyek esetében fontos a *rögzített költségek* vagy a költségkeretek *korlátozása* . Például egy rögzített (vagy ritkán változó) méretű megosztott fejlesztési-tesztelési vagy bemutató környezetek futtatásához, ahol szükség esetén rendszeresen telepítheti a felügyelt példányokat.
- Olyan forgatókönyvek esetén, ahol a VNet-alhálózatok *minimális IP-címeinek kiosztása* fontos. A készlet összes példánya egy virtuális gépet oszt meg, így a lefoglalt IP-címek száma alacsonyabb, mint az egyes példányok esetében.

## <a name="architecture"></a>Architektúra

A példány-készletek hasonló architektúrával rendelkeznek a normál (*egyetlen*) felügyelt példányokhoz. Az Azure-beli [virtuális hálózatokon belüli központi telepítések](../../virtual-network/virtual-network-for-azure-services.md) támogatásához, valamint az ügyfelek elkülönítésének és biztonságának biztosításához a példány-készletek a [virtuális fürtökre](connectivity-architecture-overview.md#high-level-connectivity-architecture)is támaszkodnak. A virtuális fürtök az ügyfél virtuális hálózati alhálózatán belül üzembe helyezett elkülönített virtuális gépek dedikált készletét jelölik.

A két üzemi modell közötti fő különbség az, hogy a példányok több SQL Server folyamat telepítését teszik lehetővé ugyanazon a virtuálisgép-csomóponton, amely a [Windows-feladatok objektumaira](/windows/desktop/ProcThread/job-objects)érvényes erőforrás, míg az egyes példányok mindig önállóak a virtuálisgép-csomópontokon.

A következő ábra egy példány-készletet és két, ugyanabban az alhálózatban üzembe helyezett példányt mutat be, és bemutatja a telepítési modellek fő architektúrájának részleteit:

![Példány-készlet és két különálló példány](./media/instance-pools-overview/instance-pools2.png)

Minden példány-készlet létrehoz egy különálló virtuális fürtöt a alatt. A készleten belüli példányok és az ugyanabban az alhálózatban telepített önálló példányok nem osztják meg SQL Server folyamatokhoz és átjáró-összetevőkhöz lefoglalt számítási erőforrásokat, ami biztosítja a teljesítmény kiszámíthatóságát.

## <a name="resource-limitations"></a>Erőforrás-korlátozások

A példánykészletekre és a készletekben lévő példányokra többféle erőforrás-korlátozás is vonatkozik:

- A példány-készletek csak Gen5 hardveren érhetők el.
- A készletben lévő felügyelt példányok dedikált PROCESSZORral és MEMÓRIÁval rendelkeznek, így az összes példány virtuális mag összesített száma nem lehet kisebb a készlethez lefoglalt virtuális mag számával.
- Az összes [példány-szintű korlát](resource-limits.md#service-tier-characteristics) a készleten belül létrehozott példányokra vonatkozik.
- A példányok szintjének korlátain kívül két korlátot is megszabhatnak *a példány-készlet szintjén*:
  - A tárterület teljes mérete (8 TB).
  - A felhasználónkénti adatbázisok száma összesen. Ez a korlát a készlet virtuális mag értékétől függ:
    - 8 a virtuális mag-készlet legfeljebb 200 adatbázist támogat,
    - a 16 virtuális mag-készlet legfeljebb 400 adatbázist támogat,
    - a 24 és a nagyobb virtuális mag-készlet legfeljebb 500 adatbázist támogat.
- A HRE-rendszergazda nem állítható be a példány-készleten belül üzembe helyezett példányokhoz, ezért a HRE-hitelesítés nem használható.

A tárolók teljes kiosztása és az adatbázisok száma az összes példánynál kisebbnek vagy egyenlőnek kell lennie a példány-készletek által elérhető korlátokkal.

- A példány-készletek 8, 16, 24, 32, 40, 64 és 80 virtuális mag támogatnak.
- A készleteken belüli felügyelt példányok támogatják a 2, 4, 8, 16, 24, 32, 40, 64 és 80 virtuális mag.
- A készletekben lévő felügyelt példányok támogatják a 32 GB és 8 TB közötti tárolási méreteket, a következők kivételével:
  - 2 a virtuális mag-példányok a 32 GB és 640 GB közötti méreteket támogatják.
  - 4 a virtuális mag-példányok a 32 GB és 2 TB közötti méreteket támogatják.
- A készletekben lévő felügyelt példányok legfeljebb 100 felhasználói adatbázisra korlátozhatók, kivéve azokat a 2 virtuális mag-példányokat, amelyek egy példányban akár 50 felhasználói adatbázist támogatnak.

A [szolgáltatási szintek tulajdonság](resource-limits.md#service-tier-characteristics) a példány-készlet erőforrásához van társítva, így a készletben lévő összes példánynak a készlet szolgáltatási szintjével megegyező szolgáltatási szintet kell tartalmaznia. Jelenleg csak a általános célú szolgáltatási szinten érhető el (az aktuális előzetes verzió korlátozásait lásd a következő szakaszban).

### <a name="public-preview-limitations"></a>A nyilvános előzetes verzió korlátozásai

A nyilvános előzetes verzió a következő korlátozásokkal rendelkezik:

- Jelenleg csak a általános célú szolgáltatási szintje érhető el.
- A példányok készletei nem méretezhetők a nyilvános előzetes verzióban, ezért fontos, hogy az üzembe helyezés előtt a kapacitás megtervezése megtörténjen.
- A példány-készlet létrehozásához és konfigurálásához Azure Portal támogatása még nem érhető el. A példányok összes művelete csak a PowerShell használatával támogatott. Egy előre létrehozott készletben a kezdeti példány üzembe helyezését csak a PowerShell támogatja. A készletbe való üzembe helyezést követően a felügyelt példányok a Azure Portal használatával frissíthetők.
- A készleten kívül létrehozott felügyelt példányok nem helyezhetők át egy meglévő készletbe, és a készleten belül létrehozott példányok nem helyezhetők át egyetlen példányon vagy másik készleten kívülre.
- A [foglalási kapacitás](../database/reserved-capacity-overview.md) példányának díjszabása nem érhető el.

## <a name="sql-features-supported"></a>Támogatott SQL-funkciók

A készletekben létrehozott felügyelt példányok ugyanazokat a [kompatibilitási szinteket és szolgáltatásokat támogatják, mint az egyetlen felügyelt példányon](sql-managed-instance-paas-overview.md#sql-features-supported).

A készletben üzembe helyezett összes felügyelt példány külön SQL Agent-példánnyal rendelkezik.

Azok a választható funkciók vagy szolgáltatások, amelyekhez bizonyos értékeket (például a példány szintű rendezést, az időzónát, az adatforgalom nyilvános végpontját, a feladatátvételi csoportokat) kell megadni, a példányok szintjén konfigurálhatók, és a készlet minden példánya esetében eltérőek lehetnek.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Bár a készletekben lévő felügyelt példányok dedikált virtuális mag és RAM-mal rendelkeznek, a helyi lemez (tempdb-használat) és a hálózati erőforrások megoszthatók. Ez nem valószínű, de lehetséges a *zajos szomszéd* hatása, ha a készletben több példány is magas erőforrás-felhasználással rendelkezik. Ha ezt a viselkedést észleli, érdemes lehet ezeket a példányokat egy nagyobb készletbe vagy egyetlen példányként telepíteni.

## <a name="security-considerations"></a>Biztonsági szempontok

Mivel a készletben üzembe helyezett példányok ugyanazt a virtuális gépet használják, érdemes lehet letiltani a magasabb biztonsági kockázatokat bevezető szolgáltatásokat, vagy a hozzáférési engedélyeket határozottan vezérelni ezekhez a funkciókhoz. Például: CLR-integráció, natív biztonsági mentés és visszaállítás, adatbázis-levelezés stb.

## <a name="instance-pool-support-requests"></a>Példány-készlet támogatási kérelmek

A [Azure Portal](https://portal.azure.com)lévő példány-készletek támogatási kérelmeit hozhatja létre és kezelheti.

Ha a példány-készlet üzembe helyezésével kapcsolatos problémákat tapasztal (létrehozás vagy törlés), akkor a **probléma altípusa** mezőben adja meg a **példányok készleteit** .

![Példány-készletek támogatási kérelem](./media/instance-pools-overview/support-request.png)

Ha egy készleten belül egyetlen felügyelt példánnyal vagy adatbázissal kapcsolatos problémát tapasztal, hozzon létre egy rendszeres támogatási jegyet az Azure SQL felügyelt példányához.

Ha nagyobb SQL-alapú felügyelt példányokat kíván létrehozni (példány-készletekkel vagy anélkül), előfordulhat, hogy nagyobb regionális kvótát kell megszereznie. További információ: [a kérelmek kvótájának növekedése Azure SQL Database](../database/quota-increase-request.md). A példányokhoz tartozó telepítési logika összehasonlítja *a készlet szintjének* teljes virtuális mag-felhasználását a kvóta alapján annak megállapítása érdekében, hogy engedélyezett-e új erőforrások létrehozása a kvóta további növelése nélkül.

## <a name="instance-pool-billing"></a>Példány-készlet számlázása

A példány-készletek a számítás és a tárolás egymástól független méretezését teszik lehetővé. Az ügyfelek a virtuális mag-ben mért készlet-erőforrással kapcsolatos számítási feladatokért és a gigabájtban mért minden egyes példányhoz társított tárterületért fizetnek (az első 32 GB díjmentes minden példány esetében).

a készlet virtuális mag díja attól függetlenül történik, hogy hány példányt telepítettek a készletbe.

A számítási díj (virtuális mag mérve) esetében két díjszabási lehetőség érhető el:

  1. *Licenccel együtt*: a SQL Server licencek díját is tartalmazza. Azok az ügyfelek, akik úgy döntenek, hogy nem alkalmazzák a meglévő SQL Server licenceket frissítési garanciával.
  2. *Azure Hybrid Benefit*: kedvezményes ár, amely SQL Server Azure Hybrid Benefit tartalmaz. Az ügyfelek a frissítési garanciával rendelkező meglévő SQL Server-licencek használatával dönthetik el ezt az árat. A jogosultsági és egyéb részleteket lásd: [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

A különböző díjszabási lehetőségek beállítása nem lehetséges a készletben lévő egyes példányok esetében. A szülő készletben lévő összes példánynak licenccel ellátott árat vagy Azure Hybrid Benefit árat kell tartalmaznia. A készlethez tartozó licencelési modell a készlet létrehozása után módosítható.

> [!IMPORTANT]
> Ha olyan licencet ad meg a példányhoz, amely eltér a készlettől, a rendszer a készlet árát fogja használni, és a példány szintjének értékét figyelmen kívül hagyja.

Ha a [fejlesztési és tesztelési juttatásra jogosult előfizetésekhez](https://azure.microsoft.com/pricing/dev-test/)hoz létre példány-készleteket, az Azure SQL felügyelt példányain a rendszer akár 55%-os kedvezményt is igénybe vehet.

A példányok készletének díjszabásával kapcsolatos részletes információkért tekintse meg az [SQL felügyelt példány díjszabása lap](https://azure.microsoft.com/pricing/details/sql-database/managed/) *példány-készletek* szakaszát.

## <a name="next-steps"></a>Következő lépések

- A példányok készletének megkezdéséhez lásd: [SQL felügyelt példányok készletei útmutató](instance-pools-configure.md).
- Az első felügyelt példány létrehozásával kapcsolatos információkért lásd: gyors [útmutató](instance-create-quickstart.md).
- A szolgáltatások és az összehasonlítások listájáért lásd: az [Azure SQL gyakori szolgáltatásai](../database/features-comparison.md).
- További információ a VNet konfigurálásáról: [SQL felügyelt példány VNet konfigurációja](connectivity-architecture-overview.md).
- Egy felügyelt példányt létrehozó gyors útmutató, valamint egy adatbázis biztonságimásolat-fájlból való visszaállítása: [felügyelt példány létrehozása](instance-create-quickstart.md).
- A Azure Database Migration Service áttelepítésre való használatával kapcsolatos oktatóanyagért lásd: [SQL felügyelt példányok áttelepítése Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md).
- Az SQL felügyelt példány-adatbázis teljesítményének speciális monitorozásához a beépített hibaelhárítási intelligenciával kapcsolatban lásd: az [Azure SQL felügyelt példány figyelése Azure SQL Analytics használatával](../../azure-monitor/insights/azure-sql.md).
- A díjszabással kapcsolatos információkért lásd: az [SQL felügyelt példányának díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).
