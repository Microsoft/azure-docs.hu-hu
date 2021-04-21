---
title: IP-tűzfalszabályok
description: Konfigurálja a kiszolgálószintű IP-tűzfalszabályokat a tűzfalon Azure SQL Database vagy Azure Synapse Analytics adatbázishoz. Kezelheti a hozzáférését, és adatbázisszintű IP-tűzfalszabályokat konfigurálhatja a SQL Database.
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
ms.openlocfilehash: 200df14e7d18c4bdfb903bef46c169f6f7bf5ca5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781774"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database IP Azure Synapse tűzfalszabályok beállítása és beállítása
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Amikor például létrehoz egy *mysqlserver* nevű új kiszolgálót Azure SQL Database vagy Azure Synapse Analytics-ban, egy kiszolgálószintű tűzfal minden hozzáférést letilt a kiszolgáló nyilvános végpontjára (amely a következő helyen *érhető el: mysqlserver.database.windows.net).* Az egyszerűség *kedvéért SQL Database* a rendszer a SQL Database és a Azure Synapse Analytics.

> [!IMPORTANT]
> Ez a cikk *nem vonatkozik* a *Azure SQL Managed Instance.* További információ a hálózati konfigurációról: [Az alkalmazás csatlakoztatása a Azure SQL Managed Instance.](../managed-instance/connect-application-instance.md)
>
> Azure Synapse kiszolgálószintű IP-tűzfalszabályokat támogat. Nem támogatja az adatbázisszintű IP-tűzfalszabályokat.

## <a name="how-the-firewall-works"></a>A tűzfal működése

Az internetről és az Azure-ból származó csatlakozási kísérleteknek át kell haladni a tűzfalon, mielőtt elérnék a kiszolgálót vagy az adatbázist, ahogy az alábbi ábrán látható.

   ![Tűzfalkonfigurációs diagram][1]

### <a name="server-level-ip-firewall-rules"></a>Kiszolgálószintű IP-tűzfalszabályok

Ezek a szabályok hozzáférést biztosítanak az ügyfelek számára a teljes kiszolgálóhoz, azaz a kiszolgáló által felügyelt összes adatbázishoz. A szabályok a master adatbázisban *vannak tárolva.* Egy kiszolgálóhoz legfeljebb 128 kiszolgálószintű IP-tűzfalszabály adható meg. Ha engedélyezve van **az Allow Azure Services and resources to access this server (Azure-szolgáltatások** és -erőforrások hozzáférésének engedélyezése a kiszolgálóhoz) beállítás engedélyezve van, ez a kiszolgáló egyetlen tűzfalszabályának számít.
  
A kiszolgálószintű IP-tűzfalszabályokat a következő utasításokkal konfigurálhatja: Azure Portal, PowerShell vagy Transact-SQL.

- A portál vagy a PowerShell csak akkor használható, ha az előfizetés tulajdonosa vagy közreműködője.
- A Transact-SQL-t kiszolgálószintű  fő bejelentkezésként vagy rendszergazdaként kell csatlakoztatnia a master Azure Active Directory. (A kiszolgálószintű IP-tűzfalszabályt először egy Azure-szintű engedélyekkel rendelkező felhasználónak kell létrehoznia.)

> [!NOTE]
> Alapértelmezés szerint az új logikai SQL-kiszolgáló létrehozása során a Azure Portal A kiszolgáló **elérésének** engedélyezése az Azure-szolgáltatásoknak és -erőforrásoknak beállítás beállítása **Nem.**

### <a name="database-level-ip-firewall-rules"></a>Adatbázisszintű IP-tűzfalszabályok

Az adatbázisszintű IP-tűzfalszabályok lehetővé teszik az ügyfelek számára bizonyos (biztonságos) adatbázisokhoz való hozzáférést. Minden adatbázishoz (beleértve a *master* adatbázist is) létre kell hoznia a szabályokat, és az egyes adatbázisokban vannak tárolva.
  
- Adatbázisszintű IP-tűzfalszabályokat csak a fő- és felhasználói adatbázisokhoz hozhat létre és kezelhet Transact-SQL-utasításokkal, és csak az első kiszolgálószintű tűzfal konfigurálása után.
- Ha olyan IP-címtartományt ad meg az adatbázisszintű IP-tűzfalszabályban, amely kívül esik a kiszolgálószintű IP-tűzfalszabály tartományán, csak azok az ügyfelek férhetnek hozzá az adatbázishoz, amelyek IP-címekkel rendelkezik az adatbázis szintjén.
- Egy adatbázishoz legfeljebb 128 adatbázisszintű IP-tűzfalszabály lehet. Az adatbázisszintű IP-tűzfalszabályok konfigurálásával kapcsolatos további információkért tekintse meg a cikk későbbi, példáját, és tekintse meg a sp_set_database_firewall_rule [(Azure SQL Database) cikket.](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Javaslatok a tűzfalszabályok beállításával kapcsolatban

Javasoljuk, hogy amikor csak lehetséges, használjon adatbázisszintű IP-tűzfalszabályokat. Ez a gyakorlat fokozza a biztonságot és az adatbázis hordozhatóságát. Kiszolgálószintű IP-tűzfalszabályok használata rendszergazdák számára. Akkor is használhatja őket, ha számos adatbázisra ugyanazok a hozzáférési követelmények vonatkoznak, és nem szeretné egyenként konfigurálni az egyes adatbázisokat.

> [!NOTE]
> Az üzletmenet folytonossága és a hordozható adatbázisok közötti kapcsolatról [a vészhelyreállítás hitelesítési követelményeit](active-geo-replication-security-configure.md)ismertető cikkből tájékozódhat.

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Kiszolgálószintű és adatbázisszintű IP-tűzfalszabályok

*Egy adatbázis felhasználóit teljesen el kell különíteni egy másik adatbázistól?*

Ha *igen,* akkor adatbázisszintű IP-tűzfalszabályok használatával adjon hozzáférést. Ezzel a módszerrel elkerülheti a kiszolgálószintű IP-tűzfalszabályokat, amelyek engedélyezik a tűzfalon keresztüli hozzáférést az összes adatbázishoz. Ez csökkentené a védelem mélységét.

*Az IP-címek felhasználóinak az összes adatbázishoz hozzá kell férni?*

Ha *igen,* használjon kiszolgálószintű IP-tűzfalszabályokat az IP-tűzfalszabályok konfiguráláskor szükséges számának csökkentéséhez.

*Az IP-tűzfalszabályokat konfiguráló személy vagy csapat csak a Azure Portal, a PowerShell vagy a REST API?*

Ha igen, kiszolgálószintű IP-tűzfalszabályokat kell használnia. Az adatbázisszintű IP-tűzfalszabályok csak a Transact-SQL használatával konfigurálhatóak.  

*Az IP-tűzfalszabályokat konfiguráló személy vagy csapat nem rendelkezik magas szintű engedéllyel az adatbázis szintjén?*

Ha igen, használjon kiszolgálószintű IP-tűzfalszabályokat. Az *adatbázisszintű* IP-tűzfalszabályok Transact-SQL-sel való konfigurálása érdekében legalább ADATBÁZIS-vezérlési adatbázis engedélyre van szükség.  

*Az IP-tűzfalszabályokat konfiguráló vagy naplózó személy vagy csapat központilag kezeli sok (akár több száz) adatbázis IP-tűzfalszabályait?*

Ebben a forgatókönyvben az ajánlott eljárásokat az igényei és a környezete határozza meg. A kiszolgálószintű IP-tűzfalszabályok konfigurálása egyszerűbb lehet, de a parancsfájlok használatával adatbázisszinten konfigurálhatóak a szabályok. Még ha kiszolgálószintű IP-tűzfalszabályokat is használ, szükség lehet az adatbázisszintű IP-tűzfalszabályok naplózásra annak ellenőrzéséhez, hogy az adatbázisra *vonatkozó CONTROL* engedéllyel rendelkező felhasználók létrehoznak-e adatbázisszintű IP-tűzfalszabályokat.

*Használhatok kiszolgáló- és adatbázisszintű IP-tűzfalszabályokat is?*

Igen. Egyes felhasználóknak, például a rendszergazdáknak kiszolgálószintű IP-tűzfalszabályokra lehet szükségük. Más felhasználóknak, például egy adatbázis-alkalmazás felhasználóinak adatbázisszintű IP-tűzfalszabályokra lehet szükségük.

### <a name="connections-from-the-internet"></a>Internetkapcsolatok

Amikor egy számítógép megpróbál csatlakozni a kiszolgálóhoz az internetről, a tűzfal először ellenőrzi a kérés eredeti IP-címét a kapcsolat által kért adatbázis adatbázisszintű IP-tűzfalszabályai között.

- Ha a cím az adatbázisszintű IP-tűzfalszabályokban megadott tartományon belül található, a rendszer kapcsolatot létesít a szabályt tartalmazó adatbázissal.
- Ha a cím nem az adatbázisszintű IP-tűzfalszabályok tartományába esik, a tűzfal ellenőrzi a kiszolgálószintű IP-tűzfalszabályokat. Ha a cím a kiszolgálószintű IP-tűzfalszabályokban található tartományba esik, a rendszer a kapcsolatot is megszabadja. A kiszolgálószintű IP-tűzfalszabályok a kiszolgáló által kezelt összes adatbázisra vonatkoznak.  
- Ha a cím nem az adatbázisszintű vagy a kiszolgálószintű IP-tűzfalszabályok egyikében sem található tartományon belül, a kapcsolódási kérés sikertelen lesz.

> [!NOTE]
> Az adatok Azure SQL Database számítógépéről való eléréséhez győződjön meg arról, hogy a hálózaton és a helyi számítógépen található tűzfal engedélyezi a kimenő kommunikációt az 1433-as TCP-porton keresztül.

### <a name="connections-from-inside-azure"></a>Kapcsolatok az Azure-ból

Ahhoz, hogy az Azure-ban üzemeltetett alkalmazások csatlakoznak az SQL Serverhez, engedélyezni kell az Azure-kapcsolatokat. Az Azure-kapcsolatok engedélyezéséhez olyan tűzfalszabálynak kell lennie, amely 0.0.0.0-s kezdő és záró IP-címeket használ.

Amikor egy Azure-beli alkalmazás megpróbál csatlakozni a kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezettek-e. Ehhez ellenőrizze, hogy létezik-e ez a tűzfalszabály. Ez közvetlenül a Azure Portal panelen kapcsolható be. Ehhez a Tűzfalak és  virtuális hálózatok beállításai között kapcsolja be az **Azure-szolgáltatások** és -erőforrások hozzáférésének engedélyezése a **kiszolgálóhoz** beállítást. Az ON beállítás egy bejövő tűzfalszabályt hoz létre a 0.0.0.0 – 0.0.0.0 IP-címhez **AllowAllWindowsIP névvel.** Ha nem a portált használja, a PowerShell vagy az Azure CLI használatával hozzon létre egy tűzfalszabályt 0.0.0.0-s kezdő és záró IP-címmel. 

> [!IMPORTANT]
> Ez a beállítás úgy konfigurálja a tűzfalat, hogy minden Azure-kapcsolatot engedélyezzen, beleértve a más ügyfelek előfizetéseiből származó kapcsolatokat is. Ha ezt a lehetőséget választja, győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei csak a jogosult felhasználókra korlátozzák a hozzáférést.

## <a name="permissions"></a>Engedélyek

Az Azure SQL Serverre vonatkozó IP-tűzfalszabályok létrehozásához és kezeléséhez az alábbi szerepkörök egyikével kell rendelkeznie:

- a [közreműködői SQL Server szerepkörben](../../role-based-access-control/built-in-roles.md#sql-server-contributor)
- az [SQL Security Manager szerepkörben](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- az erőforrást tartalmazó erőforrás tulajdonosa, Azure SQL kiszolgáló

## <a name="create-and-manage-ip-firewall-rules"></a>IP-tűzfalszabályok létrehozása és kezelése

Az első kiszolgálószintű tűzfalbeállítást a [](https://portal.azure.com/) Azure Portal vagy programozott módon hozhatja létre [az Azure PowerShell,](/powershell/module/az.sql) [az Azure CLI](/cli/azure/sql/server/firewall-rule)vagy egy Azure [REST API.](/rest/api/sql/firewallrules/createorupdate) Ezekkel a módszerekkel vagy a Transact-SQL-rel további kiszolgálószintű IP-tűzfalszabályokat hozhat létre és kezelhet.

> [!IMPORTANT]
> Az adatbázisszintű IP-tűzfalszabályok csak a Transact-SQL használatával hozhatóak létre és kezelhetők.

A teljesítmény javítása érdekében a kiszolgálószintű IP-tűzfalszabályokat átmenetileg adatbázisszinten is gyorsítótárazza a rendszer. A gyorsítótár frissítésével kapcsolatban lásd: [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql).

> [!TIP]
> Az [Adatbázis-naplózás használatával naplólhatja](../../azure-sql/database/auditing-overview.md) a kiszolgáló- és adatbázisszintű tűzfal módosításait.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>A Azure Portal a kiszolgálószintű IP-tűzfalszabályok kezeléséhez

Kiszolgálószintű IP-tűzfalszabály beállításához a Azure Portal az adatbázis vagy a kiszolgáló áttekintő oldalára.

> [!TIP]
> Oktatóanyagért lásd: [Adatbázis létrehozása a Azure Portal.](single-database-create-quickstart.md)

#### <a name="from-the-database-overview-page"></a>Az adatbázis áttekintő oldalán

1. Kiszolgálószintű IP-tűzfalszabály beállításához az adatbázis áttekintő oldalán válassza a **Kiszolgálói** tűzfal beállítása lehetőséget az eszköztáron, ahogy az alábbi képen is látható.

    ![Kiszolgáló IP-tűzfalszabálya](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    Megnyílik **a** kiszolgáló Tűzfalbeállítások lapja.

2. Válassza **az Ügyfél IP-címének** hozzáadása lehetőséget az eszköztáron a használt számítógép IP-címének hozzáadásához, majd válassza a **Mentés lehetőséget.** Létrejön egy kiszolgálószintű IP-tűzfalszabály az aktuális IP-címhez.

    ![Kiszolgálószintű IP-tűzfalszabály beállítása](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>A kiszolgáló áttekintő oldalán

Megnyílik a kiszolgáló áttekintő oldala. Megjeleníti a teljes kiszolgálónevet (például mynewserver20170403.database.windows.net *),* és további konfigurációs lehetőségeket biztosít.

1. Kiszolgálószintű szabály ezen az oldalon való  beállításhoz  válassza a tűzfal lehetőséget a bal oldali Beállítások menüben.

2. Válassza **az Ügyfél IP-címének** hozzáadása lehetőséget az eszköztáron a használt számítógép IP-címének hozzáadásához, majd válassza a **Mentés lehetőséget.** Létrejön egy kiszolgálószintű IP-tűzfalszabály az aktuális IP-címhez.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>IP-tűzfalszabályok kezelése Transact-SQL használatával

| Katalógusnézet vagy tárolt eljárás | Level | Description |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Kiszolgáló |Megjeleníti az aktuális kiszolgálószintű IP-tűzfalszabályokat |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Kiszolgáló |Kiszolgálószintű IP-tűzfalszabályokat hoz létre vagy frissíti |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Kiszolgáló |Eltávolítja a kiszolgálószintű IP-tűzfalszabályokat |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |Adatbázis |Megjeleníti az aktuális adatbázisszintű IP-tűzfalszabályokat |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |Adatbázis |Létrehozza vagy frissíti az adatbázisszintű IP-tűzfalszabályokat |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Adatbázisok |Eltávolítja az adatbázisszintű IP-tűzfalszabályokat |

Az alábbi példa áttekinti a meglévő szabályokat, engedélyezi egy IP-címtartományt a *Contoso* kiszolgálón, és töröl egy IP-tűzfalszabályt:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Ezután adjon hozzá egy kiszolgálószintű IP-tűzfalszabályt.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Kiszolgálószintű IP-tűzfalszabály törléséhez hajtsa végre sp_delete_firewall_rule *tárolt* eljárást. A következő példa törli a *ContosoFirewallRule szabályt:*

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Kiszolgálószintű IP-tűzfalszabályok kezelése a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modult továbbra is támogatja a Azure SQL Database, de most már minden fejlesztés az Az.Sql modulhoz való. A parancsmagokért lásd: [AzureRM.Sql.](/powershell/module/AzureRM.Sql/) Az Az és az AzureRm modul parancsának argumentumai lényegesen azonosak.

| Parancsmag | Level | Description |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Kiszolgáló |Az aktuális kiszolgálószintű tűzfalszabályokat adja vissza |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Kiszolgáló |Új kiszolgálószintű tűzfalszabály létrehozása |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Kiszolgáló |Meglévő kiszolgálószintű tűzfalszabály tulajdonságainak frissítése |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Kiszolgáló |Kiszolgálószintű tűzfalszabályok eltávolítása |

Az alábbi példa a PowerShell használatával hoz létre kiszolgálószintű IP-tűzfalszabályt:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> A $servername a kiszolgálónevet adja meg, ne a teljes DNS-nevet, például adja meg a **mysqldbserver** nevet az **mysqldbserver.database.windows.net**
>
> A rövid útmutatóval kapcsolatos PowerShell-példákért lásd: [Create DB - PowerShell](powershell-script-content-guide.md) and Create a single database and configure a server-level IP firewall rule using PowerShell (Adatbázis létrehozása – PowerShell és Egyedi adatbázis létrehozása, valamint kiszolgálószintű [IP-tűzfalszabály konfigurálása a PowerShell használatával).](scripts/create-and-configure-database-powershell.md)

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Kiszolgálószintű IP-tűzfalszabályok kezelése a CLI használatával

| Parancsmag | Level | Description |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Kiszolgáló|Létrehoz egy kiszolgálói IP-tűzfalszabályt|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Kiszolgáló|Felsorolja a kiszolgálón található IP-tűzfalszabályokat|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Kiszolgáló|Megjeleníti az IP-tűzfalszabály részleteit|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Kiszolgáló|Ip-tűzfalszabály frissítése|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Kiszolgáló|Töröl egy IP-tűzfalszabályt|

Az alábbi példa a CLI-t használja kiszolgálószintű IP-tűzfalszabály beállításhoz:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> A $servername a kiszolgáló nevét adja meg, ne a teljes DNS-nevet, például adja meg a **mysqldbserver** nevet az **mysqldbserver.database.windows.net**
>
> A rövid útmutatóval kapcsolatos CLI-példáért lásd: Adatbázis létrehozása – [Azure CLI](az-cli-script-samples-content-guide.md) és Create a single database (Adatbázis létrehozása) és Create a single database (Egyetlen adatbázis létrehozása) és configure a server-level IP firewall rule using the Azure CLI (Kiszolgálószintű IP-tűzfalszabály konfigurálása az [Azure CLI használatával).](scripts/create-and-configure-database-cli.md)

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Kiszolgálószintű REST API-tűzfalszabályok kezelése kiszolgálószintű tűzfalszabályok használatával

| API | Level | Description |
| --- | --- | --- |
| [Tűzfalszabályok felsorolása](/rest/api/sql/firewallrules/listbyserver) |Kiszolgáló |Megjeleníti az aktuális kiszolgálószintű IP-tűzfalszabályokat |
| [Tűzfalszabályok létrehozása vagy frissítése](/rest/api/sql/firewallrules/createorupdate) |Kiszolgáló |Kiszolgálószintű IP-tűzfalszabályokat hoz létre vagy frissíti |
| [Tűzfalszabályok törlése](/rest/api/sql/firewallrules/delete) |Kiszolgáló |Eltávolítja a kiszolgálószintű IP-tűzfalszabályokat |
| [Tűzfalszabályok lekérte](/rest/api/sql/firewallrules/get) | Kiszolgáló | Lekért kiszolgálószintű IP-tűzfalszabályokat |

## <a name="troubleshoot-the-database-firewall"></a>Az adatbázistűzfal hibaelhárítása

Vegye figyelembe a következő pontokat, ha a Azure SQL Database nem a várt módon viselkedik.

- **Helyi tűzfal konfigurációja:**

  Mielőtt a számítógépe hozzáfér Azure SQL Database, előfordulhat, hogy létre kell hoznia egy tűzfalki kivételt a számítógépén az 1433-as TCP-porthoz. Az Azure-felhőhatáron belüli kapcsolatok létesítése érdekében előfordulhat, hogy további portokat kell megnyitnia. További információkért lásd az 1433-as portokon túli portok a [4.5-ös és SQL Database 4.5-ös](adonet-v12-develop-direct-route-ports.md)és ADO.NET Azure SQL Database.

- **Hálózati címfordítás:**

  A hálózati címfordítás (NAT) miatt a számítógép által a Azure SQL Database-hoz való csatlakozásra használt IP-cím különbözhet a számítógép IP-konfigurációs beállításaiban megadott IP-címtől. A számítógép által az Azure-hoz való csatlakozáshoz használt IP-cím megtekintése:
    1. Jelentkezzen be az Portalra.
    1. Az **adatbázist tartalmazó** kiszolgálón kattintson a Konfigurálás lapra.
    1. Az **Aktuális ügyfél IP-címe** az Engedélyezett **IP-címek szakaszban jelenik** meg. Válassza **a Hozzáadás lehetőséget** az Engedélyezett **IP-címek beállításhoz,** hogy a számítógép hozzáfér a kiszolgálóhoz.

- **Az engedélyezőlista módosításai még nem lépnek életbe:**

  A tűzfal konfigurációjának módosításainak életbe Azure SQL Database akár öt perces késés is lehet.

- **A bejelentkezés nem engedélyezett, vagy helytelen jelszót használt:**

  Ha egy bejelentkezés nem rendelkezik engedélyekkel a kiszolgálón, vagy a jelszó helytelen, a rendszer megtagadja a kiszolgálóval való kapcsolatot. A tűzfalbeállítások létrehozása csak  arra biztosít lehetőséget az ügyfeleknek, hogy megpróbálnak csatlakozni a kiszolgálóhoz. Az ügyfélnek továbbra is meg kell adnia a szükséges biztonsági hitelesítő adatokat. További információ a bejelentkezések előkészítéséről: Adatbázis-hozzáférés szabályozása [és megadása.](logins-create-manage.md)

- **Dinamikus IP-cím:**

  Ha dinamikus IP-címzést használó internetkapcsolattal rendelkezik, és nem tudja áthaladni a tűzfalon, próbálkozzon az alábbi megoldások egyikével:
  
  - Kérje meg az internetszolgáltatót, hogy a kiszolgálóhoz hozzáférő ügyfélszámítógépek IP-címtartományát. Adja hozzá ezt az IP-címtartományt IP-tűzfalszabályként.
  - Az ügyfélszámítógépek számára statikus IP-címzést kell használni. Adja hozzá az IP-címeket IP-tűzfalszabályokként.

## <a name="next-steps"></a>Következő lépések

- Győződjön meg arról, hogy a vállalati hálózati környezet engedélyezi a bejövő kommunikációt az Azure-adatközpontok által használt számítási IP-címtartományok (beleértve az SQL-tartományokat is). Előfordulhat, hogy ezeket az IP-címeket hozzá kell adni az engedélyező listához. Lásd: [Microsoft Azure adatközpont IP-tartományai.](https://www.microsoft.com/download/details.aspx?id=41653)  
- Tekintse meg az egyetlen adatbázis a következőben való létrehozását [Azure SQL Database:](single-database-create-quickstart.md).
- Ha nyílt forráskódú vagy külső alkalmazásokból Azure SQL Database egy adatbázishoz való csatlakozáshoz, tekintse meg az Ügyfél gyorsindítási kódminái a [Azure SQL Database.](connect-query-content-reference-guide.md#libraries)
- Az esetleg meg kell nyitnia szükséges további portokkal kapcsolatos további információkért tekintse meg az 1433-as portokon túli portok SQL Database: Külső és belső portok" című szakaszát az [ADO.NET 4.5-ös](adonet-v12-develop-direct-route-ports.md) és SQL Database
- Az adatbázis biztonságának Azure SQL Database lásd: [Az adatbázis biztonságossá tétele.](security-overview.md)

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
