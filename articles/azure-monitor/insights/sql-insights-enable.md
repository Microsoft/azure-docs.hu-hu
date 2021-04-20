---
title: Az SQL Insights engedélyezése
description: SQL-elemzések engedélyezése a Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 012aa364fe9e379455b6b63f7c9e541d2d5b97ed
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726897"
---
# <a name="enable-sql-insights-preview"></a>SQL-elemzések engedélyezése (előzetes verzió)
Ez a cikk azt ismerteti, hogyan engedélyezheti [az SQL-elemzéseket](sql-insights-overview.md) az SQL-környezetek monitorozni. A figyelés egy Azure-beli virtuális gépről történik, amely kapcsolatot létesít az SQL üzemelő példányokkal, és dinamikus felügyeleti nézetek (DMV-k) használatával gyűjt figyelési adatokat. Monitorozási profillal szabályozhatja, hogy a rendszer milyen adatkészleteket és milyen gyakorisággal gyűjtsön.

> [!NOTE]
> Ha engedélyezni szeretné az SQL-elemzéseket a monitorozási profil és a virtuális gép Resource Manager-sablonnal való létrehozásával, tekintse meg az [SQL-Resource Manager sablonmintákat.](resource-manager-sql-insights.md)

## <a name="create-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása
Az SQL Insights egy vagy több [Log Analytics-munkaterületen tárolja az adatait.](../logs/data-platform-logs.md#log-analytics-workspaces)  Az SQL Insights engedélyezése előtt létre [](../logs/quick-create-workspace.md) kell hoznia egy munkaterületet, vagy ki kell választania egy meglévőt. Egyetlen munkaterület több monitorozási profillal is használható, de a munkaterületnek és a profiloknak ugyanabban az Azure-régióban kell lennie. Az SQL-elemzések funkcióinak engedélyezéséhez és eléréséhez a [Log Analytics közreműködői](../logs/manage-access.md) szerepkörének kell lennie a munkaterületen. 

## <a name="create-monitoring-user"></a>Figyelési felhasználó létrehozása 
Szüksége lesz egy felhasználóra a figyelni kívánt SQL-környezetben. Az sql-környezetek különböző típusaihoz kövesse az alábbi eljárásokat.

Az alábbi utasítások a figyelt SQL-típusonkénti folyamatot fedik le.  Ha ezt egyszerre több SQL-erőforráson futtatott szkripttel is el tudja érni, tekintse meg a következő [README](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation-README.txt) fájlt és [példaszk szkriptet.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation.ps1)


### <a name="azure-sql-database"></a>Azure SQL-adatbázis
Nyissa Azure SQL Database a SQL Server Management Studio vagy [Lekérdezésszerkesztő](../../azure-sql/database/connect-query-ssms.md) [(előzetes verzió)](../../azure-sql/database/connect-query-portal.md) Azure Portal.

Az alábbi szkript futtatásával hozzon létre egy felhasználót a szükséges engedélyekkel. Cserélje le a *user* (felhasználó) helyére a felhasználónevet, a *mystrongpassword* helyére pedig a jelszót.

```sql
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Telegraf felhasználói szkript létrehozása." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Ellenőrizze, hogy létrejött-e a felhasználó.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Ellenőrizze a telegraf felhasználói szkriptet." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc as authentication_type
from sys.database_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

### <a name="azure-sql-managed-instance"></a>Felügyelt Azure SQL-példány
Jelentkezzen be a Azure SQL Managed Instance, és [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) vagy hasonló eszközzel futtassa a következő szkriptet a monitorozási felhasználó létrehozásához a szükséges engedélyekkel. Cserélje le a *user* (felhasználó) helyére a username (felhasználónév) és a *mystrongpassword (mystrongpassword)* helyére a jelszót.

 
```sql
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
Jelentkezzen be az SQL Server futó Azure-beli virtuális gépre, és SQL Server Management Studio vagy hasonló eszközzel futtassa a következő szkriptet a monitorozási felhasználó létrehozásához a szükséges engedélyekkel. [](../../azure-sql/database/connect-query-ssms.md) Cserélje le a *user* (felhasználó) helyére a username (felhasználónév) és a *mystrongpassword (mystrongpassword)* helyére a jelszót.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

Ellenőrizze, hogy létrejött-e a felhasználó.

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type
from sys.server_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

## <a name="create-azure-virtual-machine"></a>Azure-beli virtuális gép létrehozása 
Létre kell hoznia egy vagy több Azure-beli virtuális gépet, amelyek az SQL figyelése érdekében gyűjtik az adatokat.  

> [!NOTE]
>  A [figyelési profilok](#create-sql-monitoring-profile) megszabadják, hogy milyen adatokat fog gyűjteni a figyelni kívánt SQL-típusokból. Minden figyelő virtuális géphez csak egy figyelési profil lehet társítva. Ha több figyelési profilra van szüksége, mindegyikhez létre kell hoznia egy virtuális gépet.

### <a name="azure-virtual-machine-requirements"></a>Az Azure virtuális gépek követelményei
Az Azure-beli virtuális gépekre a következő követelmények vonatkoznak.

- Operációs rendszer: Ubuntu 18.04 
- Ajánlott Azure-beli virtuális gépek mérete: Standard_B2s (2 processzor, 4 GiB memória) 
- Támogatott régiók: A Azure Monitor [által támogatott régiók](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> A Standard_B2s (2 processzor, 4 GiB memória) virtuális gép mérete legfeljebb 100 kapcsolati sztringet támogat. Ne foglalhat le 100-asnál több kapcsolatot egyetlen virtuális géphez.

Az SQL-erőforrások hálózati beállításaitól függően előfordulhat, hogy a virtuális gépeket ugyanabban a virtuális hálózatban kell elhelyezni, mint az SQL-erőforrásokat, hogy hálózati kapcsolatokat létesítsenek a monitorozási adatok gyűjtéséhez.  

## <a name="configure-network-settings"></a>A hálózati beállítások konfigurálása
Minden SQL-típus kínál metódusokat a monitorozási virtuális gép számára az SQL biztonságos eléréséhez.  Az alábbi szakaszok az SQL típusától függően ismertetik a lehetőségeket.

### <a name="azure-sql-databases"></a>Azure SQL Databases  

Az SQL-elemzések a Azure SQL Database nyilvános végponton keresztül és annak virtuális hálózatán keresztül is támogatják a hozzáférést.

A nyilvános végponton keresztüli hozzáféréshez hozzá  kell adni egy szabályt a Tűzfalbeállítások lap és az [IP-tűzfal beállításai szakaszban.](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules)  Virtuális hálózatról való hozzáférés megadásához [](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules) virtuális hálózati tűzfalszabályokat állíthat be, és beállíthatja a virtuális hálózati ügynökhöz [szükséges Azure Monitor számára.](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking)  [Ez a](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules) cikk a két tűzfalszabály-típus közötti különbségeket ismerteti.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Kiszolgálói tűzfal beállítása" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Tűzfalbeállítások." lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instances"></a>Felügyelt Azure SQL-példányok 

Ha a monitorozási virtuális gép ugyanabban a virtuális hálózatban lesz, mint az SQL MI-erőforrások, tekintse meg a Csatlakozás ugyanazon [a virtuális hálózaton belül című fejezetet.](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet) Ha a monitorozási virtuális gép az SQL MI-erőforrásoktól eltérő virtuális hálózaton lesz, tekintse meg a Csatlakozás [másik virtuális hálózaton belül című fejezetet.](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet)


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Azure-beli virtuális Azure SQL virtuális gép  
Ha a monitorozási virtuális gép ugyanabban a virtuális hálózatban van, mint az SQL virtuális gép erőforrásai, akkor lásd: Csatlakozás virtuális [SQL Server virtuális hálózaton belül.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network) Ha a monitorozási virtuális gép az SQL virtuálisgép-erőforrásoktól eltérő virtuális hálózaton lesz, tekintse meg a csatlakozás SQL Server [interneten keresztül című fejezetet.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet)

## <a name="store-monitoring-password-in-key-vault"></a>Figyelési jelszó tárolása a Key Vault
Az SQL-felhasználói kapcsolat jelszavait egy Key Vault, és nem kell közvetlenül a monitorozási profil kapcsolati sztringjére beírása.

Az SQL-monitorozási profil beállításainak megadásakor az alábbi engedélyek egyikére lesz szüksége a Key Vault használni kívánt erőforráson:

- Microsoft.Authorization/roleAssignments/write 
- Microsoft.Authorization/roleAssignments/delete engedélyek, például felhasználói hozzáférés-rendszergazda vagy tulajdonos 

A rendszer automatikusan létrehoz egy új hozzáférési szabályzatot a megadott SQL Monitoring használó Key Vault részeként. Használja *a Hozzáférés engedélyezése minden hálózatról beállítást* Key Vault hálózati beállításokhoz.


## <a name="create-sql-monitoring-profile"></a>SQL monitorozási profil létrehozása
Az SQL-elemzések megnyitásához válassza  az **SQL (előzetes verzió)** lehetőséget a Azure Monitor **menü** Insights Azure Portal. Kattintson **az Új profil létrehozása elemre.** 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Hozzon létre új profilt." lightbox="media/sql-insights-enable/create-new-profile.png":::

A profil tárolja az SQL-rendszerekből gyűjteni kívánt adatokat.  A következő speciális beállításokkal rendelkezik: 

- Azure SQL Database 
- Felügyelt Azure SQL-példányok 
- SQL Server virtuális gépeken futó virtuális gépek  

Létrehozhat például egy *SQL Production* nevű profilt, és egy másik, *SQL Staging* nevű profilt különböző beállításokkal az adatgyűjtés gyakoriságára, a gyűjtendő adatokra, valamint arra, hogy melyik munkaterületre küldje az adatokat. 

A profilt a rendszer adatgyűjtési [szabályerőforrásként](../agents/data-collection-rule-overview.md) tárolja a kiválasztott előfizetésben és erőforráscsoportban. Minden profilhoz a következőkre van szükség:

- név. Létrehozás után nem szerkeszthető.
- Tartózkodási hely beállításnál. Ez egy Azure-régió.
- Log Analytics-munkaterület a monitorozási adatok tárolására.
- Gyűjtési beállítások az sql-monitorozási adatok gyűjtési gyakoriságára és típusára.

> [!NOTE]
> A profil helyének ugyanazon a helyen kell lennie, mint a Log Analytics-munkaterületnek, amelybe a figyelési adatokat el tervezi küldeni.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Profiladatok." lightbox="media/sql-insights-enable/profile-details.png":::

Miután **megadta a** figyelési profil adatait, kattintson a Figyelési profil létrehozása elemre. A profil üzembe helyezése akár egy percet is igénybe vehet.  Ha nem látja az új profilt a Figyelési profil kombinált listában, kattintson a frissítés gombra, és az üzembe helyezés befejezése után meg kell jelenni.   Miután kiválasztotta az új profilt, a Profil kezelése lapon hozzáadhat egy figyelési gépet, amely a profilhoz lesz társítva. 

### <a name="add-monitoring-machine"></a>Monitorozási gép hozzáadása
Válassza **a Figyelési gép hozzáadása lehetőséget** egy helyi panel megnyitásához, ahol kiválaszthatja a beállítani kívánt virtuális gépet az SQL-példányok figyelése és a kapcsolati sztringek megadása érdekében.

Válassza ki a monitorozási virtuális gép előfizetését és nevét. Ha a Key Vault felhasználó jelszavát tárolja, válassza ki a titkos kódokkal Key Vault-erőforrásokat, és adja meg a kapcsolati sztringben használni kívánt URL-címet és titkos kódnevet. A következő szakaszban részletes információkat talál a különböző SQL-környezetek kapcsolati sztringjének azonosításáról.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Figyelési gép hozzáadása." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Kapcsolati sztringek hozzáadása 
A kapcsolati sztring határozza meg azt a felhasználónevet, amit az SQL-elemzéseknek használniuk kell az SQL-be való bejelentkezéskor a dinamikus felügyeleti nézetek futtatásához. Ha a monitorozási felhasználó jelszavát Key Vault használja, adja meg a használni kívánt titkos kód URL-címét és nevét. 

A kapcsolati sztring minden SQL-erőforrástípus esetén eltérő lehet:

#### <a name="azure-sql-databases"></a>Azure SQL Databases 
Adja meg a kapcsolati sztringet az alábbi formában:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

A részleteket az adatbázis **Kapcsolati sztringek** menüelemében olvashatja be.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="SQL Database kapcsolati sztring" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Olvasható másodlagos adatbázis figyelése esetén a kapcsolati sztringbe foglalja bele a `ApplicationIntent=ReadOnly` kulcs-értéket. Az SQL Insights egyetlen másodlagos adatbázis monitorozását támogatja. Az összegyűjtött adatok elsődleges vagy másodlagos címkével lesznek megjelölve. 


#### <a name="azure-virtual-machines-running-sql-server"></a>Virtuális gépet futtató Azure-beli SQL Server 
Adja meg a kapcsolati sztringet az alábbi formában:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Ha a monitorozási virtuális gép ugyanazon a virtuális hálózaton található, használja a kiszolgáló magánhálózati IP-címét.  Ellenkező esetben használja a nyilvános IP-címet. Ha virtuális gépet Azure SQL használ, az erőforrás Biztonság lapján láthatja, hogy melyik portot kell használnia. 

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="SQL virtuális gépek biztonsága" lightbox="media/sql-insights-enable/sql-vm-security.png":::


### <a name="azure-sql-managed-instances"></a>Felügyelt Azure SQL-példányok 
Adja meg a kapcsolati sztringet az űrlapon:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
A részleteket a felügyelt példány **Kapcsolati sztringek** menüelemében olvashatja be.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="SQL Managed Instance sztring létrehozása" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Olvasható másodlagos adatbázis figyelése esetén foglalja bele a kulcs-értéket `ApplicationIntent=ReadOnly` a kapcsolati sztringbe. Az SQL Insights támogatja egyetlen másodlagos adatbázis monitorozását, és az összegyűjtött adatok elsődleges vagy másodlagos címkével lesznek megjelölve. 


## <a name="monitoring-profile-created"></a>Monitorozási profil létrehozva 

A **monitorozási virtuális gép hozzáadása lehetőséget** választva konfigurálhatja a virtuális gépet az SQL-erőforrásokból való adatgyűjtésre. Ne térjen vissza az **Áttekintés lapra.**  Néhány percen belül a Status (Állapot) oszlopban a "Collecting" (Adatgyűjtés) szó olvasható. A monitorozásra kiválasztott SQL-erőforrások adatait kell látnia.

Ha nem lát adatokat, tekintse meg [az SQL-elemzések hibaelhárítása](sql-insights-troubleshoot.md) című témakört a probléma azonosításához. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profil létrehozva" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> Ha frissítenie kell a monitorozási profilt vagy a figyelési virtuális gépek kapcsolati sztringjét, ezt az SQL-elemzések Profil kezelése lapján **keresztül is meg lehet** tenni.  A frissítések mentése után a módosítások körülbelül 5 percen belül alkalmazva lesznek.

## <a name="next-steps"></a>Következő lépések

- Lásd: [SQL-elemzések hibaelhárítása,](sql-insights-troubleshoot.md) ha az SQL-elemzések nem működnek megfelelően az engedélyezése után.
