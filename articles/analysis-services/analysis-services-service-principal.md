---
title: Az Azure Analysis Services feladatok automatizálása szolgáltatásnévvel | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre szolgáltatásnévvel a felügyeleti feladatok Azure Analysis Services automatizálásához.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 04bc6ecd6e0a32e9234d07e995a7e012b17e3bbe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769156"
---
# <a name="automation-with-service-principals"></a>Automatizálás szolgáltatásnevekkel

A szolgáltatásnevek olyan Azure Active Directory-alkalmazáserőforrások, amelyeket felügyelet nélküli erőforrás- vagy szolgáltatásszintű műveletek végrehajtásához hozhat létre a bérlőn belül. Ezek a felhasználói identitások egyedi, alkalmazásazonosítóval és jelszóval vagy tanúsítvánnyal való típusai.  A szolgáltatásnév csak a hozzárendelt szerepkörök és engedélyek által meghatározott feladatok végrehajtásához szükséges engedélyekkel rendelkezik. 

A Analysis Services szolgáltatásnév az Azure Automation, a PowerShell felügyelet nélküli módjával, az egyéni ügyfélalkalmazásokkal és a webalkalmazásokkal használható a gyakori feladatok automatizálásához. Például a kiszolgálók üzembe helyezése, a modellek üzembe helyezése, az adatfrissítés, a fel-/leskálás és a szüneteltetés/folytatás mind automatizálható szolgáltatásnév használatával. Az engedélyek a szolgáltatásnévhez szerepkörtagságon keresztül vannak hozzárendelve, a normál Azure AD UPN-fiókokhoz hasonlóan.

Analysis Services szolgáltatásnévvel felügyelt identitások által végrehajtott műveleteket is támogatja. További információ: Az [Azure-erőforrások és](../active-directory/managed-identities-azure-resources/overview.md) az Azure AD-hitelesítést támogató Azure-szolgáltatások felügyelt [identitása.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services)    

## <a name="create-service-principals"></a>Szolgáltatásnevek létrehozása
 
A szolgáltatásnév a powershell használatával Azure Portal a powershell használatával. További információ:

[Szolgáltatásnév létrehozása – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Szolgáltatásnév létrehozása – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Hitelesítő adatok és tanúsítványeszközök tárolása a Azure Automation

A szolgáltatásnév hitelesítő adatai és tanúsítványai biztonságosan tárolhatók Azure Automation Runbook-műveletekhez. További információ:

[Hitelesítő adateszközök a Azure Automation](../automation/shared-resources/credentials.md)   
[Az Azure Automation tanúsítvány adategységei](../automation/shared-resources/certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Szolgáltatásnév hozzáadása kiszolgálói rendszergazdai szerepkörhöz

Ahhoz, hogy szolgáltatásnévvel használ Analysis Services kiszolgálókezelési műveletekhez, hozzá kell azt adni a kiszolgáló-rendszergazdák szerepkörhöz. A szolgáltatásnéveket közvetlenül hozzá kell adni a kiszolgáló-rendszergazdai szerepkörhöz. A szolgáltatásnév biztonsági csoporthoz való hozzáadása, majd a biztonsági csoport kiszolgálói rendszergazdai szerepkörhöz való hozzáadása nem támogatott. További tudnivalókért [lásd: Szolgáltatásnév hozzáadása a kiszolgálói rendszergazdai szerepkörhöz.](analysis-services-addservprinc-admins.md)

## <a name="service-principals-in-connection-strings"></a>Szolgáltatásnév a kapcsolati sztringekban

Az egyszerű szolgáltatás alkalmazásazonosítója és jelszava vagy tanúsítványa ugyanúgy használható a kapcsolati sztringekban, mint az egyszerű felhasználónév.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule"></a>Az Az.AnalysisServices modul használata

Ha egyszerű szolgáltatást használ az Erőforrás-kezelési műveletekhez az [Az.AnalysisServices](/powershell/module/az.analysisservices)  modullal, használja a `Connect-AzAccount` parancsmagot. 

A következő példában az appID és egy jelszó használatával hajtunk végre vezérlősík-műveleteket a csak olvasható replikákra való szinkronizáláshoz és a fel- és felskálához:

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

A következő példában az appID és a jelszó használatával hajtunk végre egy modelladatbázis-frissítési műveletet:

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
[Szolgáltatásnév hozzáadása a kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md)  
[Munkaterület Power BI Premium- és adatkészlet-feladatok automatizálása szolgáltatásnévvel](/power-bi/admin/service-premium-service-principal)
