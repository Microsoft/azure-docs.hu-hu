---
title: SQL-ismeretek engedélyezése
description: SQL-ismeretek engedélyezése Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 5ab51fc4ea64dfd678f5c9acfc80b5e380782153
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609408"
---
# <a name="enable-sql-insights-preview"></a>SQL-ismeretek engedélyezése (előzetes verzió)
Ez a cikk bemutatja, hogyan engedélyezheti az SQL- [információkat](sql-insights-overview.md) az SQL-környezetek figyeléséhez. A figyelés egy olyan Azure-beli virtuális gépről történik, amely kapcsolódik az SQL-környezetekhez, és dinamikus felügyeleti nézeteket (DMV) használ a figyelési adatok gyűjtéséhez. Megadhatja, hogy milyen adatkészleteket gyűjtsön, és a gyűjtés gyakoriságát figyelési profil használatával.

## <a name="create-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása
Az SQL-elemzések egy vagy több [log Analytics-munkaterületen](../logs/data-platform-logs.md#log-analytics-workspaces)tárolják az adattárakat.  Az SQL-adatfelismerések engedélyezéséhez [létre kell hoznia egy munkaterületet](../logs/quick-create-workspace.md) , vagy ki kell választania egy meglévőt. Egyetlen munkaterület több megfigyelési profillal is használható, de a munkaterületnek és a profiloknak ugyanabban az Azure-régióban kell lenniük. Az SQL-információ funkcióinak engedélyezéséhez és eléréséhez az [log Analytics közreműködő szerepkörrel](../logs/manage-access.md) kell rendelkeznie a munkaterületen. 

## <a name="create-monitoring-user"></a>Figyelő felhasználó létrehozása 
Szüksége lesz egy felhasználóra a figyelni kívánt SQL-példányokon. Kövesse az alábbi eljárásokat az SQL-alapú központi telepítések különböző típusaihoz.

### <a name="azure-sql-database"></a>Azure SQL-adatbázis
Nyissa meg a Azure SQL Databaset a Azure Portal [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) vagy a [query Editor (előzetes verzió)](../../azure-sql/database/connect-query-portal.md) használatával.

Futtassa a következő szkriptet a szükséges engedélyekkel rendelkező felhasználó létrehozásához. Cserélje le a *felhasználót* a felhasználónévvel és a *mystrongpassword* jelszóval.

```
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Hozzon létre egy Graf felhasználói szkriptet." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Ellenőrizze, hogy létrejött-e a felhasználó.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Győződjön meg arról, hogy a felhasználó parancsfájlt ellenőriz." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

### <a name="azure-sql-managed-instance"></a>Felügyelt Azure SQL-példány
Jelentkezzen be az Azure SQL felügyelt példányára, és használja a [SSMS](../../azure-sql/database/connect-query-ssms.md) vagy hasonló eszközt a következő parancsfájl futtatásához a figyelési felhasználó létrehozásához a szükséges engedélyekkel. Cserélje le a *felhasználót* a felhasználónévvel és a *mystrongpassword* jelszóval.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
Jelentkezzen be SQL Server rendszerű Azure-beli virtuális gépre, és [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) vagy hasonló eszköz használatával futtassa a következő parancsfájlt a figyelő felhasználó létrehozásához a szükséges engedélyekkel. Cserélje le a *felhasználót* a felhasználónévvel és a *mystrongpassword* jelszóval.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

## <a name="create-azure-virtual-machine"></a>Azure-beli virtuális gép létrehozása 
Létre kell hoznia egy vagy több Azure-beli virtuális gépet, amely az SQL figyelésére szolgáló adatok gyűjtésére szolgál majd.  

> [!NOTE]
>  A [figyelési profilok](#create-sql-monitoring-profile) határozzák meg, hogy milyen adatokat kell összegyűjtenie a figyelni kívánt különböző SQL-típusoktól. A figyelési virtuális gépekhez csak egy figyelési profil tartozhat. Ha több figyelési profilra van szüksége, mindegyikhez létre kell hoznia egy virtuális gépet.

### <a name="azure-virtual-machine-requirements"></a>Az Azure virtuális gépek követelményei
Az Azure-beli virtuális gépekre az alábbi követelmények vonatkoznak.

- Operációs rendszer: Ubuntu 18,04 
- Ajánlott Azure-beli virtuális gépek méretei: Standard_B2s (2 processzor, 4 GiB memória) 
- Támogatott régiók: [az Azure monitor-ügynök által támogatott bármely régió](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> A Standard_B2s (2 processzor, 4 GiB memória) virtuális gép mérete legfeljebb 100 kapcsolódási karakterláncot fog támogatni. Egyetlen virtuális géphez legfeljebb 100 kapcsolatot kell kiosztania.

A virtuális gépeket az SQL-rendszerekkel megegyező VNET kell helyezni, hogy hálózati kapcsolatokon keresztül is összegyűjtsék a figyelési adatokat. Ha a figyelési virtuális gépet az Azure-beli virtuális gépeken futó SQL figyelésére vagy Azure-beli felügyelt példányra használja, érdemes lehet a figyelési virtuális gépet egy alkalmazás-biztonsági csoportba vagy ugyanazon virtuális hálózatba helyezni, mint az erőforrásokat, így nem szükséges nyilvános hálózati végpontot biztosítani az SQL Server figyeléséhez. 

## <a name="configure-network-settings"></a>A hálózati beállítások konfigurálása
A figyelési virtuális gép minden egyes típusú SQL-típust biztosít az SQL biztonságos eléréséhez.  Az alábbi részekben a beállítások az SQL típusa alapján jelennek meg.

### <a name="azure-sql-databases"></a>Azure SQL Databases  

[Oktatóanyag – Azure SQL Serverhez való kapcsolódás Azure Private-végponttal – Azure Portal](../../private-link/tutorial-private-endpoint-sql-portal.md) példa arra, hogyan lehet beállítani egy privát végpontot, amelyet az adatbázis eléréséhez használhat.  Ha ezt a módszert használja, gondoskodnia kell arról, hogy a figyelési virtuális gépek ugyanabban a VNET és alhálózatban legyenek, mint amelyet a privát végponthoz használni fog.  Ezután létrehozhatja a privát végpontot az adatbázison, ha még nem tette meg. 

Ha [tűzfal-beállítást](../../azure-sql/database/firewall-configure.md) használ a SQL Databasehoz való hozzáférés biztosításához, hozzá kell adnia egy tűzfalszabály használatát a figyelési virtuális gép nyilvános IP-címéről való hozzáférés biztosításához. A tűzfal beállításait a portál **Azure SQL Database áttekintés** lapjáról érheti el. 

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Kiszolgálói tűzfal beállítása" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Tűzfalbeállítások." lightbox="media/sql-insights-enable/firewall-settings.png":::

### <a name="azure-sql-managed-instances"></a>Felügyelt Azure SQL-példányok 

Ha a figyelési virtuális gép az SQL MI erőforrásaival megegyező VNet fog esni, tekintse meg a következőt: [kapcsolat ugyanabban a VNet](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet). Ha a figyelési virtuális gép a különböző VNet található, mint az SQL MI erőforrásai, akkor tekintse meg a következőt: [kapcsolat egy másik VNet belül](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet).


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Azure-beli virtuális gép és Azure SQL-alapú virtuális gép  
Ha a figyelési virtuális gép ugyanabban a VNet van, mint az SQL-alapú virtuális gép erőforrásai, tekintse meg a következőt: [kapcsolódás SQL Server virtuális hálózaton belül](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network). Ha a figyelési virtuális gép az SQL-alapú virtuális gép erőforrásainak különböző VNet lesz, akkor tekintse meg  [a Kapcsolódás az interneten keresztül SQL Server](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet).

## <a name="store-monitoring-password-in-key-vault"></a>Figyelési Jelszó tárolása Key Vault
Az SQL-felhasználói kapcsolatok jelszavait egy Key Vault kell tárolnia ahelyett, hogy közvetlenül a figyelési profilhoz tartozó kapcsolatok karakterláncait beírja.

Ha az SQL-figyeléshez beállította a profilt, a következő engedélyek egyikére lesz szüksége a használni kívánt Key Vault erőforráshoz:

- Microsoft.Authorization/roleAssignments/write 
- Microsoft. Authorization/roleAssignments/törlési engedélyek, például a felhasználói hozzáférés rendszergazdája vagy a tulajdonos 

A rendszer automatikusan létrehoz egy új hozzáférési szabályzatot a SQL Monitoring profil létrehozásakor, amely a megadott Key Vault használja. Használja az *összes hálózatról Key Vault hálózati beállítások elérésének engedélyezése lehetőséget* .


## <a name="create-sql-monitoring-profile"></a>SQL-figyelési profil létrehozása
Az SQL-adatok megnyitásához válassza az **SQL (előzetes verzió)** lehetőséget a Azure Portal **Azure monitor** menüjének elemzése szakaszában.  Kattintson az **új profil létrehozása** elemre. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Hozzon létre új profilt." lightbox="media/sql-insights-enable/create-new-profile.png":::

A profil tárolja azokat az adatokat, amelyeket az SQL-rendszerekből kíván gyűjteni.  A következő speciális beállításokkal rendelkezik: 

- Azure SQL Database 
- Felügyelt Azure SQL-példányok 
- Virtuális gépeken futó SQL Server  

Létrehozhat például egy SQL-alapú *termelés* nevű profilt, egy másik SQL- *előkészítési* lehetőséget pedig különböző beállításokkal az adatgyűjtés gyakoriságához, a gyűjtött adatokhoz, valamint azt a munkaterületet, ahová az adatokat küldeni szeretné. 

A rendszer [adatgyűjtési szabályként](../agents/data-collection-rule-overview.md) tárolja a profilt a kiválasztott előfizetésben és erőforráscsoporthoz. Minden profilnak a következőkre van szüksége:

- név. A létrehozás után nem szerkeszthető.
- Tartózkodási hely beállításnál. Ez egy Azure-régió.
- Log Analytics munkaterület a figyelési adatgyűjtés tárolására.
- A gyűjteni kívánt SQL-figyelési adatok gyakoriságának és típusának gyűjtési beállításai.

> [!NOTE]
> A profil helyének ugyanabban a helyen kell lennie, mint az Log Analytics munkaterületnek, amelyet a figyelési adatként el szeretne küldeni.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Profil részletei." lightbox="media/sql-insights-enable/profile-details.png":::

Kattintson a **figyelési profil létrehozása** elemre, miután megadta a figyelési profil adatait. A profil üzembe helyezése akár egy percet is igénybe vehet.  Ha nem látja az új profilt a **figyelési profil** kombinált listájában, kattintson a frissítés gombra, és az üzembe helyezés befejezését követően meg kell jelennie.  Miután kiválasztotta az új profilt, válassza a **profil kezelése** fület, és vegyen fel egy figyelési gépet, amely hozzá lesz rendelve a profilhoz.

### <a name="add-monitoring-machine"></a>Figyelő gép hozzáadása
Válassza a **figyelési gép hozzáadása** lehetőséget a helyi panel megnyitásához, hogy kiválassza az SQL-példányok figyelésére szolgáló virtuális gépet, és adja meg a kapcsolódási karakterláncokat.

Válassza ki az előfizetést és a figyelési virtuális gép nevét. Ha Key Vaultt használ a felhasználó jelszavának tárolására a figyeléshez, válassza ki a Key Vault erőforrásokat ezekkel a titkos kulcsokkal, és adja meg a kapcsolódási karakterláncokban használandó URL-címet és titkos nevet. A különböző SQL-környezetek kapcsolati karakterláncának azonosítására vonatkozó részleteket lásd a következő szakaszban.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Figyelő gép hozzáadása." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Kapcsolatok karakterláncok hozzáadása 
A kapcsolódási karakterlánc azt a felhasználónevet adja meg, amelyet az SQL-kereséseknek használniuk kell az SQL-be történő bejelentkezéskor a dinamikus felügyeleti nézetek futtatásához. Ha Key Vault használ a figyelési felhasználó jelszavának tárolására, adja meg a használni kívánt titok URL-címét és nevét. 

A kapcsolatok karakterlánca eltérő lehet az SQL-erőforrások típusainál:

#### <a name="azure-sql-databases"></a>Azure SQL Databases 
Adja meg a kapcsolatok karakterláncát a (z) formában:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

A részleteket az adatbázis **kapcsolati karakterláncok** menüjéből szerezheti be.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="SQL Database-alapú kapcsolatok karakterlánca" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Egy olvasható másodlagos elem figyeléséhez adja meg a kulcs-érték értéket `ApplicationIntent=ReadOnly` a kapcsolódási karakterláncban.


#### <a name="azure-virtual-machines-running-sql-server"></a>SQL Server rendszert futtató Azure-beli virtuális gépek 
Adja meg a kapcsolatok karakterláncát a (z) formában:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Ha a figyelési virtuális gép ugyanabban a VNET található, használja a kiszolgáló magánhálózati IP-címét.  Ellenkező esetben használja a nyilvános IP-címet. Ha Azure SQL-alapú virtuális gépet használ, megtekintheti, hogy melyik portot szeretné használni az erőforrás **Biztonság** lapján.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="SQL virtuális gép biztonsága" lightbox="media/sql-insights-enable/sql-vm-security.png":::

Egy olvasható másodlagos elem figyeléséhez adja meg a kulcs-érték értéket `ApplicationIntent=ReadOnly` a kapcsolódási karakterláncban.


### <a name="azure-sql-managed-instances"></a>Felügyelt Azure SQL-példányok 
Adja meg a kapcsolatok karakterláncát a (z) formában:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Az adatok beolvasása a felügyelt példány **kapcsolati karakterláncok** menüjéből.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="SQL felügyelt példányának kapcsolatainak karakterlánca" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Egy olvasható másodlagos elem figyeléséhez adja meg a kulcs-érték értéket `ApplicationIntent=ReadOnly` a kapcsolódási karakterláncban.



## <a name="profile-created"></a>Profil létrehozva 
Válassza a **figyelés virtuális gép hozzáadása** lehetőséget, hogy a virtuális GÉPET az SQL-környezetből származó adatok gyűjtésére konfigurálja. Ne térjen vissza az **Áttekintés** lapra.  Néhány percen belül az állapot oszlopnak a "gyűjtés" értékre kell váltania, meg kell jelennie a figyelni kívánt rendszerek adatának.

Ha nem látja az információkat, tekintse meg az SQL-elemzések [hibaelhárítása](sql-insights-troubleshoot.md) című témakört a probléma azonosításához. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profil létrehozva" lightbox="media/sql-insights-enable/profile-created.png":::

## <a name="next-steps"></a>Következő lépések

- Az [SQL-információk hibaelhárítása](sql-insights-troubleshoot.md) , ha az SQL-információk nem működnek megfelelően, miután engedélyezte az SQL-információkat.
