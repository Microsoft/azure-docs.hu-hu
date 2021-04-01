---
title: Azure Analysis Services feladatok automatizálása egyszerű szolgáltatásokkal | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egyszerű szolgáltatásnevet Azure Analysis Services felügyeleti feladatok automatizálásához.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b04b9ababfe0e4c2a60d14044b9d3ee120837dc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491043"
---
# <a name="automation-with-service-principals"></a>Automatizálás szolgáltatásnevekkel

A szolgáltatásnevek olyan Azure Active Directory-alkalmazáserőforrások, amelyeket felügyelet nélküli erőforrás- vagy szolgáltatásszintű műveletek végrehajtásához hozhat létre a bérlőn belül. Egyedi *felhasználói identitást* használnak az alkalmazás azonosítója és jelszava vagy tanúsítványa alapján. Egy egyszerű szolgáltatásnév csak azokat az engedélyeket kapja meg, amelyek a hozzárendelt szerepkörök és engedélyek által meghatározott feladatok végrehajtásához szükségesek. 

Analysis Services az egyszerű szolgáltatásokkal automatizálható a Azure Automation, a PowerShell felügyelet nélküli módban, az egyéni ügyfélalkalmazások és a webalkalmazások használata a gyakori feladatok automatizálásához. Például a kiszolgálók kiépítése, a modellek üzembe helyezése, az Adatfrissítés, a vertikális fel-és leskálázás, valamint a Szüneteltetés/folytatás is automatizálható az egyszerű szolgáltatásnév használatával. Az engedélyek az egyszerű Azure AD UPN-fiókokhoz hasonlóan szerepkör-tagságon keresztül vannak hozzárendelve a szolgáltatásokhoz.

A Analysis Services a felügyelt identitások által végrehajtott műveleteket is támogatja az egyszerű szolgáltatásokkal. További információt az Azure [ad-hitelesítést támogató](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services)Azure-erőforrások és Azure-szolgáltatások [felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md) című témakörben talál.    

## <a name="create-service-principals"></a>Szolgáltatásnevek létrehozása
 
Az egyszerű szolgáltatások a Azure Portal vagy a PowerShell használatával hozhatók létre. További információ:

[Szolgáltatásnév létrehozása – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Szolgáltatásnév létrehozása – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>A hitelesítő adatok és a tanúsítványok eszközeinek tárolása Azure Automation

Az egyszerű szolgáltatás hitelesítő adatai és tanúsítványai biztonságosan tárolhatók Azure Automation runbook-műveletekhez. További információ:

[Hitelesítőadat-eszközök a Azure Automation](../automation/shared-resources/credentials.md)   
[Az Azure Automation tanúsítvány adategységei](../automation/shared-resources/certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Egyszerű szolgáltatások hozzáadása a kiszolgálói rendszergazdai szerepkörhöz

Ahhoz, hogy egy egyszerű szolgáltatást Analysis Services kiszolgálófelügyeleti műveletekhez lehessen használni, hozzá kell adnia azt a kiszolgálói rendszergazdák szerepkörhöz. Az egyszerű szolgáltatásokat közvetlenül a kiszolgálói rendszergazdai szerepkörhöz kell hozzáadni. Egyszerű szolgáltatásnév hozzáadása egy biztonsági csoporthoz, és a biztonsági csoport hozzáadása a kiszolgálói rendszergazdai szerepkörhöz nem támogatott. További információ: [egyszerű szolgáltatásnév hozzáadása a kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Egyszerű szolgáltatások a kapcsolódó sztringekben

A szolgáltatás egyszerű appID és jelszava vagy tanúsítványa ugyanúgy használható a kapcsolatok sztringekben, mint az egyszerű felhasználónév.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule"></a>Az az. AnalysisServices modul használata

Ha az az [. AnalysisServices](/powershell/module/az.analysisservices)  modullal erőforrás-kezelési műveletekhez egyszerű szolgáltatásnevet használ, használja a `Connect-AzAccount` parancsmagot. 

A következő példában a appID és a Password (jelszó) használatával vezérelheti a vezérlési sík műveleteit a csak olvasási replikák szinkronizálásához, és felskálázást végezhet:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>Az SQLServer modul használata

A következő példában a appID és a jelszó használatával végezhető el a modell adatbázis-frissítési művelete:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO és ADOMD 

Az ügyfél- és a webalkalmazások összekapcsolása esetén a NuGet [AMO- és ADOMD-ügyfélkódtárainak](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) 15.0.2 és újabb telepíthető csomagjai támogatják a szolgáltatásneveket a kapcsolati sztringek esetén a következő szintaxissal: `app:AppID` és jelszó, vagy `cert:thumbprint`. 

Az alábbi példában az `appID` és egy `password` segítségével végezheti el egy modelladatbázis frissítési műveletét:

```csharp
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Következő lépések
[Bejelentkezés Azure PowerShell](/powershell/azure/authenticate-azureps)   
[Frissítés a Logic Apps használatával](analysis-services-refresh-logic-app.md)  
[Frissítés az Azure Automationnel](analysis-services-refresh-azure-automation.md)  
[Egyszerű szolgáltatásnév hozzáadása a kiszolgáló-rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md)  
[Power BI Premium munkaterület és adatkészlet-feladatok automatizálása egyszerű szolgáltatásokkal](/power-bi/admin/service-premium-service-principal)