---
title: IP-tűzfalszabályok
description: A kiszolgálói szintű IP-tűzfalszabályok konfigurálása Azure SQL Database vagy Azure szinapszis Analytics-tűzfalon található adatbázishoz. A hozzáférés kezelése és az adatbázis szintű IP-tűzfalszabályok konfigurálása SQL Databasehoz.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: bbad7dcaa1d92df4969c88e4ba86a62987509e39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632799"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database és az Azure szinapszis IP-tűzfalszabályok
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Ha egy új kiszolgálót hoz létre Azure SQL Database vagy az Azure szinapszis Analytics *portra beállított mysqlserver* nevű szolgáltatását, például egy kiszolgálói szintű tűzfal blokkolja a kiszolgáló nyilvános végpontjának hozzáférését (amely a *mysqlserver.database.Windows.net* címen érhető el). Az egyszerűség kedvéért a *SQL Database* az SQL Database és az Azure szinapszis Analytics használatára is vonatkozik.

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik az *Azure SQL felügyelt példányaira*. További információ a hálózati konfigurációról: [az alkalmazás összekötése az Azure SQL felügyelt példányával](../managed-instance/connect-application-instance.md).
>
> Az Azure szinapszis csak a kiszolgálói szintű IP-tűzfalszabályok használatát támogatja. Az adatbázis-szintű IP-tűzfalszabályok nem támogatottak.

## <a name="how-the-firewall-works"></a>A tűzfal működése

Az internetről és az Azure-ról érkező csatlakozási kísérleteknek át kell haladnia a tűzfalon, mielőtt elérnék a kiszolgálót vagy az adatbázist, az alábbi ábrán látható módon.

   ![Tűzfal-konfigurációs diagram][1]

### <a name="server-level-ip-firewall-rules"></a>Kiszolgálószintű IP-tűzfalszabályok

Ezek a szabályok hozzáférést biztosítanak az ügyfelek számára a teljes kiszolgálóhoz, azaz a kiszolgáló által felügyelt összes adatbázishoz. A szabályokat a *Master* adatbázisban tárolja a rendszer. Egy kiszolgálóhoz legfeljebb 128 kiszolgálószintű IP-tűzfalszabály adható meg. Ha engedélyezte, **hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a kiszolgálóhoz** beállításhoz, ez a kiszolgáló egyetlen tűzfalszabályként számít.
  
A kiszolgálói szintű IP-tűzfalszabályok a Azure Portal, a PowerShell vagy a Transact-SQL utasítások használatával konfigurálhatók.

- A portál vagy a PowerShell használatához az előfizetés tulajdonosának vagy az előfizetés közreműködőinek kell lennie.
- A Transact-SQL használatához csatlakoznia kell a *főadatbázishoz* kiszolgálói szintű rendszerbiztonsági azonosítóként vagy Azure Active Directory rendszergazdaként. (A kiszolgálói szintű IP-tűzfalszabályok először egy Azure szintű engedélyekkel rendelkező felhasználónak kell létrehoznia.)

> [!NOTE]
> Alapértelmezés szerint a Azure Portal új logikai SQL-kiszolgáló létrehozásakor az **Azure-szolgáltatások és-erőforrások hozzáférésének engedélyezése ezen a kiszolgálón** beállítás a **nem** értékre van állítva.

### <a name="database-level-ip-firewall-rules"></a>Adatbázis szintű IP-tűzfalszabályok

Az adatbázis-szintű IP-tűzfalszabályok lehetővé teszik az ügyfelek számára bizonyos (biztonságos) adatbázisok elérését. Létrehozhatja az egyes adatbázisok szabályait ( *beleértve a* főadatbázist is), és azokat az egyes adatbázisokban tárolja.
  
- A fő-és a felhasználói adatbázisokhoz csak az adatbázis-szintű IP-tűzfalszabályok hozhatók létre és kezelhetők a Transact-SQL-utasítások használatával, és csak az első kiszolgálói szintű tűzfal konfigurálása után.
- Ha olyan IP-címtartományt ad meg az adatbázis-szintű IP-tűzfalszabály számára, amely a kiszolgálói szintű IP-tűzfalszabályok tartományán kívül esik, csak azok az ügyfelek férhetnek hozzá az adatbázishoz, amelyek IP-címmel rendelkeznek az adatbázis-szintű tartományon.
- Az adatbázishoz legfeljebb 128 adatbázis-szintű IP-tűzfalszabályok tartozhat. Az adatbázis-szintű IP-tűzfalszabályok konfigurálásával kapcsolatos további információkért tekintse meg a jelen cikk későbbi, [sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)című példáját.

### <a name="recommendations-for-how-to-set-firewall-rules"></a>A tűzfalszabályok beállításával kapcsolatos javaslatok

Az adatbázis szintű IP-tűzfalszabályok használatát javasoljuk, ha lehetséges. Ez a gyakorlat fokozza a biztonságot és az adatbázis hordozhatóságát. A rendszergazdákra vonatkozó kiszolgálói szintű IP-tűzfalszabályok használata. Akkor is használja őket, ha sok olyan adatbázisa van, amely azonos hozzáférési követelményekkel rendelkezik, és nem szeretné külön konfigurálni az egyes adatbázisokat.

> [!NOTE]
> Az üzletmenet folytonossága és a hordozható adatbázisok közötti kapcsolatról [a vészhelyreállítás hitelesítési követelményeit](active-geo-replication-security-configure.md)ismertető cikkből tájékozódhat.

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Kiszolgálószintű és adatbázisszintű IP-tűzfalszabályok

*Egy adatbázis felhasználóinak teljesen el kell különíteni egy másik adatbázisból?*

Ha *Igen*, az adatbázis-szintű IP-tűzfalszabályok használatával engedélyezze a hozzáférést. Ez a módszer elkerüli a kiszolgálói szintű IP-tűzfalszabályok használatát, ami lehetővé teszi a tűzfalon keresztüli hozzáférését az összes adatbázishoz. Ez csökkenti a védelem mélységét.

*Az IP-címekhez tartozó felhasználóknak hozzáférésre van szükségük az összes adatbázishoz?*

Ha *Igen*, a kiszolgálói szintű IP-tűzfalszabályok használatával csökkentse az IP-tűzfalszabályok konfigurálásához szükséges időt.

*Az IP-tűzfalszabályok konfigurálását végző személy vagy csapat csak a Azure Portalon, a PowerShellen vagy a REST APIon keresztül fér hozzá?*

Ha igen, a kiszolgálói szintű IP-tűzfalszabályok használatát kell használnia. Az adatbázis szintű IP-tűzfalszabályok csak Transact-SQL használatával konfigurálhatók.  

*Az a személy vagy csapat, aki úgy konfigurálja az IP-tűzfalszabályok szabályait, hogy az adatbázis szintjén magas szintű engedélyekkel rendelkezik?*

Ha igen, használja a kiszolgálói szintű IP-tűzfalszabályok használatát. Az adatbázis-szintű IP-tűzfalszabályok Transact-SQL-en keresztüli konfigurálásához legalább a *vezérlési adatbázis* engedélyére van szükség az adatbázis szintjén.  

*Az IP-tűzfalszabályok konfigurálását vagy naplózását végző személy vagy csoport központilag kezeli az IP-tűzfalszabályok több (akár száz) adatbázisra vonatkozó szabályait?*

Ebben az esetben az ajánlott eljárásokat az Ön igényei és környezete határozza meg. A kiszolgálói szintű IP-tűzfalszabályok könnyebben konfigurálhatók, de a parancsfájlok az adatbázis szintjén is konfigurálhatók a szabályok. Ha pedig kiszolgálói szintű IP-tűzfalszabályok használatát is használja, előfordulhat, hogy az adatbázis-szintű IP-tűzfalszabályok naplózása lehetőségre kattintva ellenőrizheti, hogy a *felhasználók rendelkeznek-* e az adatbázis-szintű IP-tűzfalszabályok létrehozásához szükséges engedélyekkel.

*Használhatok a kiszolgálói szintű és az adatbázis szintű IP-tűzfalszabályok együttes használatát?*

Igen. Egyes felhasználók, például a rendszergazdák kiszolgálói szintű IP-tűzfalszabályok szükségesek. Más felhasználók, például egy adatbázis-alkalmazás felhasználóinak adatbázis-szintű IP-tűzfalszabályok szükségesek.

### <a name="connections-from-the-internet"></a>Internetkapcsolatok

Amikor egy számítógép az internetről próbál csatlakozni a kiszolgálóhoz, a tűzfal először ellenőrzi a kérelem eredeti IP-címét a kapcsolódási kérelmeket tartalmazó adatbázis adatbázis-szintű IP-tűzfalszabályok szabályai alapján.

- Ha a cím az adatbázis szintű IP-tűzfalszabályok által megadott tartományon belül van, akkor a rendszer a szabályt tartalmazó adatbázishoz adja meg a kapcsolódási engedélyt.
- Ha a cím nem az adatbázis szintű IP-tűzfalszabályok tartományán belül van, a tűzfal ellenőrzi a kiszolgálói szintű IP-tűzfalszabályok szabályait. Ha a cím olyan tartományon belül van, amely a kiszolgálói szintű IP-tűzfalszabályok esetében szerepel, akkor a rendszer megadta a kapcsolatokat. A kiszolgálói szintű IP-tűzfalszabályok a kiszolgáló által kezelt összes adatbázisra érvényesek.  
- Ha a cím nem olyan tartományon belül van, amely az adatbázis vagy a kiszolgáló szintű IP-tűzfalszabályok egyikében sem szerepel, a kapcsolati kérelem sikertelen lesz.

> [!NOTE]
> A helyi számítógép Azure SQL Databaseának eléréséhez győződjön meg arról, hogy a tűzfal a hálózaton és a helyi számítógépen engedélyezi a kimenő kommunikációt a 1433-es TCP-porton.

### <a name="connections-from-inside-azure"></a>Kapcsolatok az Azure-on belül

Ha engedélyezni szeretné az Azure-ban üzemeltetett alkalmazások számára az SQL Serverhez való csatlakozást, engedélyezni kell az Azure-kapcsolatokat. Az Azure-kapcsolatok engedélyezéséhez olyan tűzfalszabály szükséges, amelyben a kezdő és a záró IP-cím értéke 0.0.0.0.

Ha egy Azure-alkalmazás megpróbál csatlakozni a kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e a tűzfalszabály meglétének ellenőrzésével. Ez közvetlenül a Azure Portal panelről kapcsolható be, ha átváltja az **Azure-szolgáltatások és-erőforrások engedélyezése a kiszolgálóhoz** a **tűzfalon és a virtuális hálózatok** beállításaiban való elérését.  Ha a ON értékre van állítva, a létrehoz egy bejövő tűzfalszabály a **AllowAllWindowsIP** nevű IP 0.0.0.0-0.0.0.0 számára. Ha nem a portált használja, a PowerShell vagy az Azure CLI használatával hozzon létre egy olyan tűzfalszabályot, amelynek kezdő és záró IP-címe a 0.0.0.0 értékre van állítva. 

> [!IMPORTANT]
> Ez a beállítás úgy konfigurálja a tűzfalat, hogy engedélyezze az Azure összes kapcsolatát, beleértve a más ügyfelek előfizetései által létesített kapcsolatokat is. Ha ezt a beállítást választja, győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei csak a jogosult felhasználókra korlátozzák a hozzáférést.

## <a name="permissions"></a>Engedélyek

Az Azure SQL Serverre vonatkozó IP-tűzfalszabályok létrehozásához és kezeléséhez az alábbi szerepkörök egyikével kell rendelkeznie:

- a [SQL Server közreműködő](../../role-based-access-control/built-in-roles.md#sql-server-contributor) szerepkörben
- az [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) szerepkörben
- Az Azure-SQL Server tartalmazó erőforrás tulajdonosa

## <a name="create-and-manage-ip-firewall-rules"></a>IP-tűzfalszabályok létrehozása és kezelése

Az első kiszolgálói szintű tűzfalbeállítások létrehozásához használja a [Azure Portal](https://portal.azure.com/) vagy programozott módon az [Azure PowerShell](/powershell/module/az.sql), az [azure CLI](/cli/azure/sql/server/firewall-rule)vagy egy Azure [REST API](/rest/api/sql/firewallrules/createorupdate)használatával. Ezen metódusok vagy Transact-SQL használatával további kiszolgálói szintű IP-tűzfalszabályok hozhatók létre és kezelhetők.

> [!IMPORTANT]
> Az adatbázis-szintű IP-tűzfalszabályok csak a Transact-SQL használatával hozhatók létre és kezelhetők.

A teljesítmény javítása érdekében a kiszolgálószintű IP-tűzfalszabályokat átmenetileg adatbázisszinten is gyorsítótárazza a rendszer. A gyorsítótár frissítésével kapcsolatban lásd: [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql).

> [!TIP]
> Az adatbázis- [naplózás](../../azure-sql/database/auditing-overview.md) használatával naplózhatja a kiszolgáló szintű és az adatbázis szintű tűzfal változásait.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>A kiszolgálói szintű IP-tűzfalszabályok kezelése a Azure Portal használatával

Ha egy kiszolgálói szintű IP-tűzfalszabály beállítását szeretné beállítani a Azure Portalban, lépjen az adatbázis vagy a kiszolgáló áttekintés lapjára.

> [!TIP]
> Oktatóanyagért lásd: [adatbázis létrehozása a Azure Portal használatával](single-database-create-quickstart.md).

#### <a name="from-the-database-overview-page"></a>Az adatbázis – áttekintés oldalon

1. Kiszolgálóoldali IP-tűzfalszabály beállításához az adatbázis áttekintése lapon válassza a **kiszolgáló tűzfal beállítása** az eszköztáron lehetőséget, majd az alábbi képen látható módon.

    ![Kiszolgáló IP-tűzfalának szabálya](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    Ekkor megnyílik a kiszolgáló **tűzfalbeállítások** lapja.

2. Válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron, és adja hozzá a használt számítógép IP-címét, majd kattintson a **Mentés** gombra. Az aktuális IP-címhez kiszolgálói szintű IP-tűzfalszabály jön létre.

    ![Kiszolgálói szintű IP-tűzfalszabály beállítása](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>A kiszolgáló áttekintése lapon

Megnyílik a kiszolgáló Áttekintés lapja. Megjeleníti a teljes kiszolgálónevet (például *mynewserver20170403.database.Windows.net*), és további konfigurálási lehetőségeket biztosít.

1. Ha ezen a lapon szeretné beállítani a kiszolgálói szintű szabályt, válassza a bal oldalon található **Beállítások** menüből a **tűzfal** lehetőséget.

2. Válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron, és adja hozzá a használt számítógép IP-címét, majd kattintson a **Mentés** gombra. Az aktuális IP-címhez kiszolgálói szintű IP-tűzfalszabály jön létre.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Az IP-tűzfalszabályok kezelése a Transact-SQL használatával

| Katalógus nézet vagy tárolt eljárás | Level | Leírás |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Kiszolgáló |Megjeleníti az aktuális kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Kiszolgáló |Kiszolgálói szintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Kiszolgáló |Eltávolítja a kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |Adatbázis |Az adatbázis-szintű IP-tűzfalszabályok aktuális szabályait jeleníti meg |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |Adatbázis |Az adatbázis-szintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Adatbázisok |Adatbázis-szintű IP-tűzfalszabályok eltávolítása |

Az alábbi példa a meglévő szabályokat tekinti át, lehetővé teszi a kiszolgáló *contoso* IP-címeinek tartományát, és töröl egy IP-tűzfalszabály-szabályt:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Ezután adjon hozzá egy kiszolgálói szintű IP-tűzfalszabály-szabályt.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

A kiszolgálói szintű IP-tűzfalszabály törléséhez hajtsa végre az *sp_delete_firewall_rule* tárolt eljárást. A következő példa törli a szabály *ContosoFirewallRule*:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>A kiszolgáló szintű IP-tűzfalszabályok kezelése a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a fejlesztés most már az az. SQL modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

| Parancsmag | Level | Leírás |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Kiszolgáló |Az aktuális kiszolgálószintű tűzfalszabályokat adja vissza |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Kiszolgáló |Új kiszolgálószintű tűzfalszabály létrehozása |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Kiszolgáló |Meglévő kiszolgálószintű tűzfalszabály tulajdonságainak frissítése |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok eltávolítása |

A következő példa a PowerShell használatával állítja be a kiszolgálói szintű IP-tűzfalszabály beállításait:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> A $servername adja meg a kiszolgáló nevét, ne a teljes DNS-nevet, például adja meg a **mysqldbserver** -t a **mysqldbserver.database.Windows.net** helyett.
>
> Ha PowerShell-példákat szeretne használni a rövid útmutató kontextusában, tekintse meg az adatbázis [létrehozása](powershell-script-content-guide.md) és az [önálló adatbázis létrehozása és a kiszolgálói szintű IP-tűzfalszabályok konfigurálása a PowerShell használatával](scripts/create-and-configure-database-powershell.md)című témakört.

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Kiszolgálói szintű IP-tűzfalszabályok kezelése a CLI használatával

| Parancsmag | Level | Leírás |
| --- | --- | --- |
|[az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Kiszolgáló|Kiszolgálói IP-tűzfalszabály létrehozása|
|[az SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Kiszolgáló|A kiszolgálón található IP-tűzfalszabályok listája|
|[az SQL Server Firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Kiszolgáló|Egy IP-tűzfalszabály részleteit jeleníti meg|
|[az SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Kiszolgáló|IP-tűzfalszabály frissítése|
|[az SQL Server Firewall-Rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Kiszolgáló|IP-tűzfalszabály törlése|

A következő példa a parancssori felületet használja a kiszolgálói szintű IP-tűzfalszabály beállításához:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> A $servername adja meg a kiszolgáló nevét, ne a teljes DNS-nevet, például adja meg a **mysqldbserver** -t a **mysqldbserver.database.Windows.net** helyett.
>
> Egy rövid útmutató kontextusában például a következő témakörben talál: adatbázis [létrehozása – Azure CLI](az-cli-script-samples-content-guide.md) , [egyetlen adatbázis létrehozása és egy kiszolgálói szintű IP-tűzfalszabály konfigurálása az Azure CLI használatával](scripts/create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>A kiszolgálói szintű IP-tűzfalszabályok kezelésére szolgáló REST API használata

| API | Level | Leírás |
| --- | --- | --- |
| [Tűzfalszabályok listázása](/rest/api/sql/firewallrules/listbyserver) |Kiszolgáló |Megjeleníti az aktuális kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [Tűzfalszabályok létrehozása vagy frissítése](/rest/api/sql/firewallrules/createorupdate) |Kiszolgáló |Kiszolgálói szintű IP-tűzfalszabályok létrehozása vagy frissítése |
| [Tűzfalszabályok törlése](/rest/api/sql/firewallrules/delete) |Kiszolgáló |Eltávolítja a kiszolgálói szintű IP-tűzfalszabályok szabályait |
| [Tűzfalszabályok beolvasása](/rest/api/sql/firewallrules/get) | Kiszolgáló | Kiszolgálói szintű IP-tűzfalszabályok beolvasása |

## <a name="troubleshoot-the-database-firewall"></a>Az adatbázistűzfal hibaelhárítása

Vegye figyelembe a következő szempontokat, amikor a Azure SQL Databasehoz való hozzáférés nem a várt módon működik.

- **Helyi tűzfal konfigurációja:**

  Ahhoz, hogy a számítógép hozzáférhessen Azure SQL Databasehoz, előfordulhat, hogy létre kell hoznia egy tűzfal-kivételt a számítógépen a 1433-es TCP-porthoz. Előfordulhat, hogy az Azure-felhő határain belül szeretne kapcsolatokat létesíteni. további portok megnyitására van lehetőség. További információ: a [1433-es és a Azure SQL Database-es porton túli portok](adonet-v12-develop-direct-route-ports.md)"SQL Database: külső és belső" szakasza, 4,5 ADO.net.

- **Hálózati címfordítás:**

  Hálózati címfordítás (NAT) miatt a számítógépe által az Azure SQL Databasehoz való kapcsolódáshoz használt IP-cím a számítógép IP-konfigurációs beállításainak IP-címétől eltérő lehet. A számítógép által az Azure-hoz való kapcsolódáshoz használt IP-cím megtekintése:
    1. Jelentkezzen be az Portalra.
    1. Lépjen a **configure (Konfigurálás** ) lapra az adatbázist futtató kiszolgálón.
    1. Az **aktuális ügyfél IP-címe** az **engedélyezett IP-címek** szakaszban jelenik meg. Válassza a **Hozzáadás** az **engedélyezett IP-címekhez** lehetőséget, hogy a számítógép hozzáférjen a kiszolgálóhoz.

- **Az engedélyezési lista módosításai még nem kerültek érvénybe:**

  A Azure SQL Database tűzfal konfigurációjának változásainak érvénybe léptetéséhez akár öt perc is eltelhet.

- **A bejelentkezés nincs engedélyezve, vagy helytelen jelszót használt:**

  Ha a bejelentkezés nem rendelkezik jogosultsággal a kiszolgálón, vagy helytelen a jelszó, a rendszer megtagadja a kapcsolódást a kiszolgálóval. A tűzfalszabályok létrehozása csak *lehetőséget* biztosít az ügyfeleknek, hogy megpróbáljon csatlakozni a kiszolgálóhoz. Az ügyfélnek továbbra is meg kell adnia a szükséges biztonsági hitelesítő adatokat. A bejelentkezések előkészítésével kapcsolatos további információkért lásd: [adatbázis-hozzáférés szabályozása és megadása](logins-create-manage.md).

- **Dinamikus IP-cím:**

  Ha olyan internetkapcsolattal rendelkezik, amely dinamikus IP-címzést használ, és problémát tapasztal a tűzfalon keresztül, próbálkozzon az alábbi megoldások egyikével:
  
  - Kérje meg az internetszolgáltatót a kiszolgálót elérő ügyfélszámítógépekhez rendelt IP-címtartomány megadására. Adja hozzá az IP-címtartományt IP-tűzfalszabályként.
  - Az ügyfélszámítógépek helyett statikus IP-címzést kell lekérnie. Adja hozzá az IP-címeket IP-tűzfalszabályokként.

## <a name="next-steps"></a>Következő lépések

- Ellenőrizze, hogy a vállalati hálózati környezet lehetővé teszi-e a bejövő kommunikációt az Azure-adatközpontok által használt számítási IP-címtartományok (beleértve az SQL-tartományokat is). Előfordulhat, hogy ezeket az IP-címeket fel kell vennie az engedélyezési listára. Lásd: [Microsoft Azure adatközpont IP-tartományai](https://www.microsoft.com/download/details.aspx?id=41653).  
- Tekintse meg a gyors üzembe helyezési útmutatót, amelyből megtudhatja [, hogyan hozhat létre Azure SQL Database](single-database-create-quickstart.md)
- A nyílt forráskódú vagy harmadik féltől származó alkalmazásokból Azure SQL Database adatbázishoz való csatlakozással kapcsolatos segítségért tekintse meg a következő témakört: ügyfél-útmutató- [kód minták Azure SQL Database](connect-query-content-reference-guide.md#libraries).
- További információ a megnyitható további portokról: a [ADO.NET 4,5-es és a 1433 SQL Database-es porton túli portok](adonet-v12-develop-direct-route-ports.md) "SQL Database: külső vs belső" szakasza.
- A Azure SQL Database biztonság áttekintését lásd: [az adatbázis biztonságossá tétele](security-overview.md).

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
