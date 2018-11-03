---
title: Az Azure SQL Database kapcsolati architektúra |} A Microsoft Docs
description: Ez a dokumentum ismerteti az Azure SQL Database kapcsolati architektúra az Azure-ban vagy az Azure-on kívül.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: 11133a24f4446478dcc7f38ed50eb36de8843442
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978401"
---
# <a name="azure-sql-database-connectivity-architecture"></a>Az Azure SQL Database kapcsolati architektúra

Ez a cikk ismerteti az Azure SQL Database kapcsolati architektúra, valamint azt ismerteti, hogyan a különböző összetevőket az Azure SQL Database-példány forgalmat működni. Ezek az Azure SQL Database kapcsolat összetevők függvény irányítani a hálózati forgalom az Azure-adatbázishoz csatlakozó ügyféltől Azure-ban és az Azure-on kívül csatlakozó ügyféltől. Ez a cikk a szkriptminták módosításához, hogyan történik a kapcsolat, és az alapértelmezett kapcsolat beállításainak módosításával kapcsolatos szempontokat is biztosít.

## <a name="connectivity-architecture"></a>Kapcsolati architektúra

Az alábbi ábra az Azure SQL Database kapcsolati architektúra magas szintű áttekintést nyújt.

![az architektúra áttekintése](./media/sql-database-connectivity-architecture/architecture-overview.png)

Az alábbi lépések bemutatják, hogyan létrejön a kapcsolat egy Azure SQL Database az Azure SQL Database software load balancer (SLB) és az Azure SQL Database-átjárón keresztül.

- Ügyfelek csatlakoznak a szoftveres Terheléselosztó, amely egy nyilvános IP-címmel rendelkezik, és a 1433-as portot figyeli.
- A szoftveres Terheléselosztó továbbítja a forgalmat az Azure SQL Database-átjáróra.
- Az átjáró, attól függően, a hatékony kapcsolódási szabályzat, az átirányítások vagy a proxyk a forgalom a megfelelő proxyt közbenső szoftverek.
- A proxy közbenső továbbítja a forgalmat a megfelelő Azure SQL Database-adatbázishoz.

> [!IMPORTANT]
> Ezek az összetevők mindegyike rendelkezik elosztott szolgáltatásmegtagadásos (DDoS-) szolgáltatás védelem beépített a hálózat és az app-réteget.

## <a name="connection-policy"></a>Kapcsolat-házirend

Az Azure SQL Database támogatja a kapcsolat egy SQL Database-kiszolgáló házirend-beállítás a következő három módon:

- **Átirányítási (ajánlott):** ügyfelek közvetlenül az adatbázist futtató csomópontot kapcsolatokat hozhat létre. Ahhoz, hogy a kapcsolat, az ügyfelek engedélyeznie kell a kimenő tűzfalszabályokat, hogy az összes Azure IP-címek a régióban (próbálja ki a hálózati biztonsági csoportok (NSG) a használatával [szolgáltatáscímkéket](../virtual-network/security-overview.md#service-tags)), nem csak az Azure SQL Database átjáró IP-címek. Mivel a csomagok adatbázissal, teljesítmény és a késés teljesítményűek.
- **Proxy:** ebben a módban az összes kapcsolat az Azure SQL Database-átjárókon keresztül webszolgáltatásokhoz használják. Ahhoz, hogy a kapcsolat, az ügyfél kimenő tűzfalszabályokat, amelyek lehetővé teszik a csak az Azure SQL Database átjáró IP-címek (általában két IP-címek régiónként) kell rendelkeznie. Ez a mód kiválasztása nagyobb késést és alacsonyabb átviteli sebességet a számítási feladatok természetétől függően eredményezhet. Erősen ajánlott az átirányítási kapcsolódási szabályzat keresztül a Proxy kapcsolódási szabályzat a legkisebb késés és a legnagyobb átviteli sebességet.
- **Alapértelmezett:** Ez a kapcsolódási szabályzat érvényben az összes kiszolgáló létrehozása után hacsak nem változtatja meg a kapcsolódási szabályzat vagy a Proxy vagy átirányítási. A tényleges házirend attól függ, hogy kapcsolatok származik (átirányítási) Azure-ban, vagy (Proxy) Azure-on kívül.

## <a name="connectivity-from-within-azure"></a>Csatlakozás Azure-ban

Csatlakozik Azure-ban egy kiszolgálón, November 10, 2018 után létrehozott kapcsolatok van-e, a kapcsolódási szabályzat **átirányítási** alapértelmezés szerint. Egy szabályzatot a **átirányítási** azt jelenti, hogy kapcsolatok az Azure SQL Database, a TCP-munkamenet az ügyfél-munkamenet létrehozása után a rendszer ezután átirányítja a proxy közbenső és a cél virtuális IP-címhez módosul, valamint az Azure Az SQL Database átjárója, amely a proxy közbenső szoftverek. Ezt követően minden további csomagokat folyamat közvetlenül keresztül, a proxy közbenső szoftverek, az Azure SQL Database átjárója kihagyásával. A következő ábra szemlélteti a forgalom áramlását.

![az architektúra áttekintése](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

> [!IMPORTANT]
> Ha a kapcsolódási szabályzat létrehozott SQL Database-kiszolgáló 2018. November 10. előtt volt beállítva az explicit módon **Proxy**. A Szolgáltatásvégpontok használatakor az erősen javasoljuk, hogy váltson a kapcsolódási szabályzat a **átirányítási** engedélyezéséhez a jobb teljesítmény érdekében. Ha módosítja a kapcsolódási szabályzat a **átirányítási**, nem lesz elegendő ahhoz, hogy engedélyezze a kimenő az alább felsorolt IP-címek az Azure SQL Database átjárója az NSG-t, engedélyeznie kell a kimenő célja: minden Azure SQL Database IP-címet. Ez az NSG-t (a hálózati biztonsági csoportok) Szolgáltatáscímkék segítségével valósítható meg. További információkért lásd: [Szolgáltatáscímkék](../virtual-network/security-overview.md#service-tags).

## <a name="connectivity-from-outside-of-azure"></a>Csatlakozás Azure-on kívül

Ha Azure-on kívülről csatlakozik, a kapcsolatok rendelkezik-e a kapcsolódási szabályzat, **Proxy** alapértelmezés szerint. Egy szabályzatot a **Proxy** azt jelenti, hogy a TCP-munkamenet létrehozását az Azure SQL Database-átjárón keresztül, és további csomagokat flow az átjárón keresztül. A következő ábra szemlélteti a forgalom áramlását.

![az architektúra áttekintése](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Az Azure SQL Database átjárója IP-címek

Csatlakozhat egy Azure SQL database a helyszíni erőforrásairól, szüksége, hogy a kimenő hálózati forgalom az Azure SQL Database-átjáróhoz, az az Azure-régióban. A kapcsolatokat csak nyissa meg az átjárón keresztül a Proxy módban, amely az alapértelmezett történő csatlakozás helyszíni erőforrásokhoz való csatlakozáskor.

Az alábbi táblázat az elsődleges és másodlagos IP-címek, adatok minden régió esetében az Azure SQL Database-átjáró. Az egyes régiókban vannak két IP-címet. Ezekben a régiókban az elsődleges IP-cím az átjáró aktuális IP-címe pedig a második IP-cím feladatátvételi IP-címet. A feladatátvétel a címet, amelyre nem lehet áthelyezni, hogy a szolgáltatás magas rendelkezésre állását a kiszolgáló címe. Ezekben a régiókban javasoljuk, hogy engedélyezi a kimenő mindkét IP-címet. A második IP-cím a Microsoft tulajdonában van, és nem figyel a függő szolgáltatások mindaddig, amíg aktívvá válik, az Azure SQL Database-kapcsolatok fogadására által.

| Régió neve | Elsődleges IP-cím | Másodlagos IP-cím |
| --- | --- |--- |
| Kelet-Ausztrália | 191.238.66.109 | 13.75.149.87 |
| Délkelet-Ausztrália | 191.239.192.109 | 13.73.109.251 |
| Dél-Brazília | 104.41.11.5 | |
| Közép-Kanada | 40.85.224.249 | |
| Kelet-Kanada | 40.86.226.166 | |
| USA középső régiója | 23.99.160.139 | 13.67.215.62 |
| Kelet-Kína 1 | 139.219.130.35 | |
| Kelet-Kína 2 | 40.73.82.1 | |
| Észak-Kína 1 | 139.219.15.17 | |
| Észak-Kína 2 | 40.73.50.0 | |
| Kelet-Ázsia | 191.234.2.139 | 52.175.33.150 |
| 1 USA keleti régiója | 191.238.6.43 | 40.121.158.30 |
| USA 2. keleti régiója | 191.239.224.107 | 40.79.84.180 * |
| Közép-Franciaország | 40.79.137.0 | 40.79.129.1 |
| Közép-Németország | 51.4.144.100 | |
| Németország, Északkelet | 51.5.144.179 | |
| Közép-India | 104.211.96.159 | |
| Dél-India | 104.211.224.146 | |
| Nyugat-India | 104.211.160.80 | |
| Kelet-Japán | 191.237.240.43 | 13.78.61.196 |
| Nyugat-Japán | 191.238.68.11 | 104.214.148.156 |
| Korea középső régiója | 52.231.32.42 | |
| Korea déli régiója | 52.231.200.86 |  |
| USA északi középső régiója | 23.98.55.75 | 23.96.178.199 |
| Észak-Európa | 191.235.193.75 | 40.113.93.91 |
| USA déli középső régiója | 23.98.162.75 | 13.66.62.124 |
| Délkelet-Ázsia | 23.100.117.95 | 104.43.15.0 |
| Egyesült Királyság északi régiója | 13.87.97.210 | |
| Egyesült Királyság déli régiója 1 | 51.140.184.11 | |
| Egyesült Királyság 2. déli régiója | 13.87.34.7 | |
| Az Egyesült Királyság nyugati régiója | 51.141.8.11 | |
| USA nyugati középső régiója | 13.78.145.25 | |
| Nyugat-Európa | 191.237.232.75 | 40.68.37.158 |
| USA nyugati RÉGIÓJA 1 | 23.99.34.75 | 104.42.238.205 |
| USA nyugati régiója, 2. | 13.66.226.202 | |
||||

\* **Megjegyzés:** *USA keleti RÉGIÓJA 2* is a harmadlagos IP-címmel rendelkezik `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Azure SQL Database-kapcsolat házirend módosítása

Az Azure SQL Database egy Azure SQL Database-kiszolgáló kapcsolódási szabályzat módosításához használja a [kap-házirend](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) parancsot.

- Ha a kapcsolat-házirend **Proxy**, az összes hálózati csomagok folyamatot az Azure SQL Database-átjárón keresztül. A beállítás csak az Azure SQL Database IP átjáró kimenő engedélyeznie kell. Beállítás használatával **Proxy** további beállítás késést rendelkezik **átirányítási**.
- Ha a kapcsolódási szabályzat beállítása **átirányítási**, az összes hálózati csomagok folyamat közvetlenül és a közbenső szoftver proxy. Ehhez a beállításhoz több IP-cím kimenő engedélyeznie kell.

## <a name="script-to-change-connection-settings-via-powershell"></a>Módosítja a kapcsolati beállításokat PowerShell-szkript

> [!IMPORTANT]
> A szkriptnek szüksége van a [Azure PowerShell-modul](/powershell/azure/install-azurerm-ps).
>

A következő PowerShell-parancsfájl bemutatja, hogyan módosíthatja a kapcsolódási szabályzat.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Módosítja a kapcsolati beállításokat az Azure CLI-szkript

> [!IMPORTANT]
> A szkriptnek szüksége van a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

A következő CLI-példaszkript bemutatja, hogyan módosíthatja a kapcsolódási szabályzat.

```azurecli-interactive
<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy

</pre>
```

## <a name="next-steps"></a>További lépések

- Egy Azure SQL Database-kiszolgálóhoz az Azure SQL Database kapcsolódási szabályzat módosításának módjával kapcsolatos információkért lásd: [kap-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Az ADO.NET 4.5 vagy újabb verzióját használó ügyfelek az Azure SQL Database-kapcsolat működéssel kapcsolatos információkért lásd: [az ADO.NET 4.5 szoftverrel 1433-Ason túli](sql-database-develop-direct-route-ports-adonet-v12.md).
- Általános fejlesztési áttekintő információkért lásd: [SQL Database-alapú alkalmazásfejlesztés áttekintése](sql-database-develop-overview.md).
