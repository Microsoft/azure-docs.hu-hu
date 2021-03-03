---
title: 'Rövid útmutató: Azure SQL felügyelt példány létrehozása (portál)'
description: Hozzon létre egy felügyelt példányt, a hálózati környezetet és az ügyfél virtuális gépet a rövid útmutató Azure Portal használatával való hozzáféréshez.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 1/29/2021
ms.openlocfilehash: d356cad1b4754875574e19be732fdf6481c61e22
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691212"
---
# <a name="quickstart-create-an-azure-sql-managed-instance"></a>Rövid útmutató: Azure SQL felügyelt példány létrehozása
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy [felügyelt Azure SQL-példányt](sql-managed-instance-paas-overview.md) a Azure Portal.

> [!IMPORTANT]
> Korlátozásokkal kapcsolatban lásd: [támogatott régiók](resource-limits.md#supported-regions) és [támogatott előfizetési típusok](resource-limits.md#supported-subscription-types).

## <a name="create-an-azure-sql-managed-instance"></a>Felügyelt Azure SQL-példány létrehozása

SQL felügyelt példány létrehozásához kövesse az alábbi lépéseket: 

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az **Azure SQL** kifejezést a keresőmezőbe.
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. Az Azure SQL felügyelt példányával kapcsolatos további információkat az **SQL felügyelt példányok** csempén látható **Részletek megjelenítése** lehetőség választásával tekintheti meg.
1. Válassza a **Létrehozás** lehetőséget.

   ![Felügyelt példány létrehozása](./media/instance-create-quickstart/create-azure-sql-managed-instance.png)

4. A szükséges és választható információk hozzáadásához használja az **Azure SQL felügyelt példány** létesítési űrlapjának létrehozásához használt lapokat. A következő szakaszok ismertetik ezeket a lapokat.

### <a name="basics-tab"></a>Alapbeállítások lap

- Adja meg az **alapok** lapon szükséges kötelező információkat. Ez az SQL-felügyelt példány kiépítéséhez szükséges minimális információ.

   ![Az SQL felügyelt példányainak létrehozásához szükséges alapismeretek lap](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-basics.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Előfizetés** | Az előfizetése. | Olyan előfizetés, amely engedélyt ad új erőforrások létrehozására. |
   | **Erőforráscsoport** | Új vagy meglévő erőforráscsoport.|Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Felügyelt példány neve** | Bármely érvényes név.|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Régió** |Az a régió, amelyben létre kívánja hozni a felügyelt példányt.|További információ a régiókkal kapcsolatban: [Azure-régiók](https://azure.microsoft.com/regions/).|
   | **Felügyelt példány rendszergazdai bejelentkezési neve** | Bármilyen érvényes Felhasználónév. | Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. Ne használja a "ServerAdmin" kulcsszót, mert ez egy fenntartott kiszolgálói szintű szerepkör.|
   | **Jelszó** | Bármilyen érvényes jelszó.| A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Válassza a **felügyelt példány beállítása** lehetőséget a számítási és tárolási erőforrások méretének és a díjszabási szintek áttekintéséhez. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát. Ha elkészült, kattintson az **alkalmaz** gombra a kijelölés mentéséhez. 

   ![Felügyelt példány űrlapja](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-configure-performance.png)

| Beállítás| Ajánlott érték | Leírás |
| ------ | --------------- | ----------- |
| **Szolgáltatási szintek** | Válasszon egyet a lehetőségek közül. | A forgatókönyv alapján válasszon a következő lehetőségek közül: </br> <ul><li>**Általános célú**: a legtöbb éles számítási feladathoz és az alapértelmezett beállításhoz.</li><li>**Üzletileg kritikus**: alacsony késésű számítási feladatokhoz tervezve, amelyek nagy rugalmassággal rendelkeznek a hibák és a gyors feladatátvétel érdekében.</li></ul><BR>További információ: [Azure SQL Database és az Azure SQL felügyelt példányok szolgáltatási szintjei](../../azure-sql/database/service-tiers-general-purpose-business-critical.md) , valamint [Az Azure SQL felügyelt példányok erőforrás-korlátainak áttekintése](../../azure-sql/managed-instance/resource-limits.md).|
| **Hardver létrehozása** | Válasszon egyet a lehetőségek közül. | A hardverek létrehozása általában meghatározza a számítási és a memóriabeli korlátokat, valamint azokat a jellemzőket, amelyek hatással vannak a munkaterhelés teljesítményére. A **Gen5** az alapértelmezett.|
| **Virtuális mag számítási modell** | Válasszon ki egy lehetőséget. | a virtuális mag pontos mennyiségű számítási erőforrást jelentenek, amelyek mindig kiépítve vannak a munkaterheléshez. Az alapértelmezett érték **nyolc virtuális mag** .|
| **Tárterület GB-ban** | Válasszon ki egy lehetőséget. | A tárterület mérete GB-ban, válassza a várt adatméret alapján lehetőséget. Ha a meglévő adatok helyszíni vagy különböző felhőalapú platformokon való áttelepítését végzik, tekintse meg [az áttelepítési Áttekintés: SQL Server az SQL felügyelt példányra](../../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-overview.md)című témakört.|
| **Azure Hybrid Benefit** | Ellenőrizze a beállítást, ha van ilyen. | Meglévő Azure-licencek kihasználásához. További információ: [Azure Hybrid Benefit-Azure SQL Database & SQL felügyelt példánya](../../azure-sql/azure-hybrid-benefit.md). |
| **Biztonsági mentési tár redundancia** | Válassza a **geo-redundáns biztonsági mentési tároló** elemet. | Tárhely-redundancia az Azure-ban a biztonsági mentési tárterülethez. Vegye figyelembe, hogy ez az érték később nem módosítható. A Geo-redundáns biztonsági mentési tár alapértelmezett és ajánlott, bár a zóna és a helyi redundancia nagyobb mértékű rugalmasságot és egyrégiós adattárolást tesz lehetővé. További információ: a [biztonsági másolatok tárolásának redundancia](../database/automated-backups-overview.md?tabs=managed-instance#backup-storage-redundancy).|


- Ha a felügyelt SQL-példány létrehozása előtt szeretné áttekinteni a beállításokat, válassza a **felülvizsgálat + létrehozás** lehetőséget. Vagy konfigurálja a hálózatkezelési beállításokat a következő lehetőség kiválasztásával **: hálózatkezelés**.

### <a name="networking-tab"></a>Hálózatkezelés lap

- Adja meg a választható adatokat a **hálózatkezelés** lapon. Ha kihagyja ezt az információt, a portál az alapértelmezett beállításokat fogja alkalmazni.

   !["Hálózatkezelés" lap felügyelt példány létrehozásához](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-networking.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Virtuális hálózat** | Válassza az **új virtuális hálózat létrehozása** vagy egy érvényes virtuális hálózat és alhálózat lehetőséget.| Ha egy hálózat vagy alhálózat nem érhető el, akkor azt módosítani kell, [hogy megfeleljen a hálózati követelményeknek](vnet-existing-add-subnet.md) , mielőtt kiválasztja az új felügyelt példány célhelyének. További információ az SQL felügyelt példányának hálózati környezetének konfigurálásával kapcsolatos követelményekről: [virtuális hálózat konfigurálása SQL felügyelt példányhoz](connectivity-architecture-overview.md). |
   | **Kapcsolat típusa** | Válasszon egy proxy és egy átirányítási kapcsolat típusa közül.|További információ a kapcsolatok típusairól: az [Azure SQL felügyelt példányának kapcsolattípus](../database/connectivity-architecture.md#connection-policy).|
   | **Nyilvános végpont**  | Válassza a **Letiltás** lehetőséget. | Ahhoz, hogy egy felügyelt példány elérhető legyen a nyilvános adatvégponton keresztül, engedélyeznie kell ezt a beállítást. | 
   | **Hozzáférés engedélyezése** (ha a **nyilvános végpont** engedélyezve van) | Válassza a **Nincs hozzáférés** lehetőséget  |A portál felhasználói felülete lehetővé teszi egy biztonsági csoport nyilvános végponttal való konfigurálását. </br> </br> A forgatókönyv alapján válasszon a következő lehetőségek közül: </br> <ul> <li>**Azure-szolgáltatások**: ezt a lehetőséget akkor javasoljuk, ha Power bi vagy egy másik több-bérlős szolgáltatásból csatlakozik. </li> <li> **Internet**: tesztelési célokra használható, ha gyorsan el szeretné végezni a felügyelt példányok üzembe helyezését. Éles környezetekhez nem ajánlott. </li> <li> **Nincs hozzáférés**: Ez a beállítás egy **megtagadási** biztonsági szabályt hoz létre. Módosítsa ezt a szabályt úgy, hogy egy nyilvános végponton keresztül elérhetővé váljon a felügyelt példány. </li> </ul> </br> A nyilvános végpontok biztonságával kapcsolatos további információkért tekintse meg az [Azure SQL felügyelt példány biztonságos használata nyilvános végponttal](public-endpoint-overview.md)című témakört.|

- A felügyelt példány létrehozása előtt válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások áttekintéséhez. Vagy konfigurálja a további egyéni beállításokat a **Tovább: további beállítások** lehetőség kiválasztásával.


### <a name="additional-settings"></a>További beállítások

- Adja meg a választható információkat a **További beállítások** lapon. Ha kihagyja ezt az információt, a portál az alapértelmezett beállításokat fogja alkalmazni.

   !["További beállítások" lap felügyelt példány létrehozásához](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-additional-settings.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Rendezés** | Válassza ki a felügyelt példányhoz használni kívánt rendezést. Ha SQL Serverból telepít át adatbázisokat, ellenőrizze a forrás rendezését a használatával, `SELECT SERVERPROPERTY(N'Collation')` és használja ezt az értéket.| További információ a rendezésekről: [a kiszolgáló rendezésének beállítása vagy módosítása](/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Időzóna** | Válassza ki azt az időzónát, amelyet a felügyelt példány figyelembe fog venni.|További információ: [időzóna](timezones-overview.md).|
   | **Használat feladatátvételi másodlagosként** | Válassza az **Igen** lehetőséget. | Ezzel a beállítással engedélyezheti a felügyelt példány másodlagos feladatátvételi csoportként való használatát.|
   | **Elsődleges SQL felügyelt példánya** (ha a **másodlagos feladatátvételi** beállítás értéke **Igen**) | Válasszon egy meglévő elsődleges felügyelt példányt, amely ugyanabban a DNS-zónában lesz csatlakoztatva, mint a létrehozandó felügyelt példány. | Ez a lépés lehetővé teszi a feladatátvételi csoport létrehozás utáni konfigurációját. További információ: [oktatóanyag: felügyelt példány hozzáadása feladatátvételi csoporthoz](failover-group-add-instance-tutorial.md).|

- A felügyelt példány létrehozása előtt válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások áttekintéséhez. Vagy konfigurálja az Azure-címkéket a következő lehetőség kiválasztásával **: címkék** (ajánlott).

### <a name="tags"></a>Címkék

- Címkék hozzáadása az erőforrásokhoz a Azure Resource Manager-sablonban (ARM-sablon). A [címkék](../../azure-resource-manager/management/tag-resources.md) segítségével logikailag rendszerezheti az erőforrásokat. A címke értékei a Cost-jelentésekben jelennek meg, és más felügyeleti tevékenységek számára is lehetővé teszik a címkézést. 

- Ügyeljen arra, hogy az új SQL felügyelt példányt a tulajdonos címkével lássa el, és azonosítsa, hogy ki hozta létre, és a környezeti címkével azonosítsa, hogy ez a rendszer éles, fejlesztési stb. További információ: [Az Azure-erőforrások elnevezési és címkézési stratégiájának fejlesztése](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
 
- A folytatáshoz válassza a **felülvizsgálat + létrehozás** lehetőséget.

## <a name="review--create"></a>Ellenőrzés és létrehozás

1. A felügyelt példány létrehozása előtt válassza a **felülvizsgálat + létrehozás** fület a lehetőségek áttekintéséhez.

   ![Felügyelt példányok áttekintésére és létrehozására szolgáló lap](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-review-create.png)

1. Válassza a **Létrehozás** lehetőséget a felügyelt példány kiépítés indításához.

> [!IMPORTANT]
> A felügyelt példányok üzembe helyezése hosszan futó művelet. Az alhálózat első példányának üzembe helyezése általában sokkal hosszabb időt vesz igénybe, mint egy meglévő felügyelt példányokkal rendelkező alhálózatba való üzembe helyezés. Az átlagos kiépítési idő tekintetében lásd: [Az Azure SQL felügyelt példányok kezelési műveleteinek áttekintése](management-operations-overview.md#duration).

## <a name="monitor-deployment-progress"></a>Központi telepítési folyamat figyelése

1. A központi telepítés állapotának megtekintéséhez kattintson az **értesítések** ikonra.

   ![SQL felügyelt példány központi telepítésének telepítési folyamata](./media/instance-create-quickstart/azure-sql-managed-instance-create-deployment-in-progress.png)

1. Válassza a **telepítés folyamatban** van az értesítésben az SQL felügyelt példány ablakának megnyitásához és a telepítési folyamat további figyeléséhez. 

> [!TIP]
> - Ha bezárta a webböngészőt, vagy áthelyezte az üzembe helyezési folyamat képernyőjéről, a felügyelt példány **áttekintő** oldalán vagy a PowerShell vagy az Azure CLI használatával figyelheti a kiépítési műveletet. További információ: [monitoring Operations](management-operations-monitor.md#monitor-operations). 
> - A kiépítési folyamat megszakítható Azure Portalon keresztül, vagy a PowerShell vagy az Azure CLI vagy más, a REST API használatával. Lásd: az [Azure SQL felügyelt példányok kezelési műveleteinek megszakítása](management-operations-cancel.md).

> [!IMPORTANT]
> - A felügyelt SQL-példányok létrehozásának megkezdése késleltethető olyan esetekben, amikor más befolyásoló műveletek is léteznek, például az azonos alhálózaton lévő más felügyelt példányok esetében a hosszan futó visszaállítási vagy méretezési műveletek. További információ: a [felügyeleti műveletek hatása](management-operations-overview.md#management-operations-cross-impact).
> - A felügyelt példányok létrehozási állapotának lekéréséhez **olvasási engedéllyel** kell rendelkeznie az erőforráscsoporthoz. Ha nem rendelkezik ezzel az engedéllyel vagy visszavonással, miközben a felügyelt példány létrehozása folyamatban van, ez azt eredményezheti, hogy az SQL felügyelt példánya nem látható az erőforráscsoport-telepítések listájában.
>

## <a name="view-resources-created"></a>Létrehozott erőforrások megtekintése

Felügyelt példány sikeres üzembe helyezése után a létrehozott erőforrások megtekintése:

1. Nyissa meg a felügyelt példányhoz tartozó erőforráscsoportot. 

   ![SQL felügyelt példány erőforrásai](./media/instance-create-quickstart/azure-sql-managed-instance-resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Hálózati beállítások megtekintése és finomhangolása

A hálózati beállítások finomhangolásához ellenőrizze a következőket:

1. Az erőforrások listájában válassza ki az útválasztási táblázatot a létrehozott felhasználó által definiált útválasztási tábla (UDR) objektum áttekintéséhez.

2. Az útválasztási táblázatban tekintse át azokat a bejegyzéseket, amelyek az SQL felügyelt példányának virtuális hálózatán belüli és onnan érkező forgalmat irányítják. Ha manuálisan hozza létre vagy konfigurálja az útválasztási táblázatot, hozza létre ezeket a bejegyzéseket az SQL felügyelt példány útválasztási táblázatában.

   ![SQL felügyelt példány alhálózatának bejegyzése helyire](./media/instance-create-quickstart/azure-sql-managed-instance-route-table-user-defined-route.png)

    Útvonalak módosításához vagy hozzáadásához nyissa meg az **útvonalakat** az útválasztási táblázat beállításai között.

3. Térjen vissza az erőforráscsoporthoz, és válassza ki a létrehozott hálózati biztonsági csoport (NSG) objektumot.

4. Tekintse át a bejövő és kimenő biztonsági szabályokat. 

   ![Biztonsági szabályok](./media/instance-create-quickstart/azure-sql-managed-instance-security-rules.png)

    A szabályok módosításához vagy hozzáadásához nyissa meg a **bejövő biztonsági szabályokat** és a **kimenő biztonsági szabályokat** a hálózati biztonsági csoport beállításainál.

> [!IMPORTANT]
> Ha nyilvános végpontot konfigurált az SQL felügyelt példányához, meg kell nyitnia a portokat, hogy engedélyezzék a hálózati forgalom számára a nyilvános internetről felügyelt példányhoz való kapcsolódást. További információ: [nyilvános végpont konfigurálása SQL felügyelt példányhoz](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Felügyelt SQL-példány kapcsolatadatainak a lekérése

A felügyelt SQL-példányhoz való kapcsolódáshoz kövesse az alábbi lépéseket az állomásnév és a teljes tartománynév (FQDN) lekéréséhez:

1. Térjen vissza az erőforráscsoporthoz, és válassza ki a létrehozott SQL felügyelt példány objektumot.

2. Az **Áttekintés** lapon keresse meg a **gazdagép** tulajdonságot. Másolja az állomásnevet a vágólapra a felügyelt példány számára a következő rövid útmutatóban való használathoz a **Másolás a vágólapra** gombra kattintva.

   ![Állomásnév](./media/instance-create-quickstart/azure-sql-managed-instance-host-name.png)

   A másolt érték egy teljes tartománynevet (FQDN) jelöl, amely az SQL felügyelt példányhoz való kapcsolódáshoz használható. A következőhöz hasonló példa: *your_host_name. a1b2c3d4e5f6. database. Windows. net*.

## <a name="next-steps"></a>Következő lépések

További információ a felügyelt SQL-példányokhoz való kapcsolódásról:
- Az alkalmazások csatlakozási lehetőségeinek áttekintését lásd: [alkalmazások csatlakoztatása SQL felügyelt példányhoz](connect-application-instance.md).
- Az Azure-beli virtuális gépekről az SQL felügyelt példányhoz való kapcsolódást bemutató rövid útmutató: Azure-beli [Virtuálisgép-kapcsolat konfigurálása](connect-vm-instance-configure.md).
- A pont – hely kapcsolat [konfigurálásával](point-to-site-p2s-configure.md)megtudhatja, hogyan CSATLAKOZHAT az SQL felügyelt példányához egy helyszíni ügyfélszámítógépről pont – hely kapcsolat használatával.

Meglévő SQL Server-adatbázis visszaállítása a helyszínről az SQL felügyelt példányára: 
- Az [áttelepítéshez](../../dms/tutorial-sql-server-to-managed-instance.md) használja a Azure Database Migration Service az adatbázis biztonságimásolat-fájljából való visszaállításhoz. 
- Az adatbázis-biztonságimásolat-fájlból történő visszaállításhoz használja a [T-SQL Restore parancsot](restore-sample-database-quickstart.md) .

Az SQL felügyelt példány-adatbázis teljesítményének speciális monitorozásához a beépített hibaelhárítási intelligenciával kapcsolatban lásd: az [Azure SQL felügyelt példány figyelése Azure SQL Analytics használatával](../../azure-monitor/insights/azure-sql.md).