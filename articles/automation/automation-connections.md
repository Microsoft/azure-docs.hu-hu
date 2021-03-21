---
title: Kapcsolatok kezelése Azure Automationban
description: Ez a cikk azt ismerteti, hogyan kezelheti a külső szolgáltatásokhoz vagy alkalmazásokhoz való Azure Automation kapcsolatokat, és hogyan dolgozhat velük a runbookok-ben.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 5f6494eb72084c683ddbb8b27a49acdb1fbc2bfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054263"
---
# <a name="manage-connections-in-azure-automation"></a>Kapcsolatok kezelése Azure Automationban

Az Azure Automation a következő információkat tartalmazza:. Ezek az információk egy külső szolgáltatáshoz vagy alkalmazáshoz runbook vagy DSC-konfigurációból való kapcsolódáshoz szükségesek. 

* A hitelesítéshez szükséges információk, például Felhasználónév és jelszó
* A kapcsolatok adatai, például az URL-cím vagy a port

A kapcsolati eszköz egy adott alkalmazáshoz való csatlakozáshoz szükséges összes tulajdonságot megtartja, így szükségtelenül több változót létrehozni. Egy helyen szerkesztheti a kapcsolatok értékeit, és egy runbook vagy DSC-konfigurációhoz is átadhatja a kapcsolatok nevét egyetlen paraméterben. A runbook vagy a konfiguráció a belső parancsmag használatával fér hozzá egy kapcsolat tulajdonságaihoz `Get-AutomationConnection` .

A kapcsolatok létrehozásakor meg kell adnia egy kapcsolattípus-típust. A kapcsolat típusa egy olyan sablon, amely a tulajdonságok készletét határozza meg. A Azure Automation egy metaadat-fájllal rendelkező integrációs modul használatával is hozzáadhat egy kapcsolattípus. A [Azure Automation API](/previous-versions/azure/reference/mt163818(v=azure.100)) -val is létrehozhat egy kapcsolattípust, ha az integrációs modul tartalmaz egy kapcsolattípust, és importálja az Automation-fiókjába. 

>[!NOTE]
>Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a rendszer titkosítja és tárolja Azure Automation az egyes Automation-fiókokhoz generált egyedi kulcs használatával. Azure Automation a kulcsot a rendszerfelügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt az Automation betölti a kulcsot Key Vault, majd a használatával titkosítja az eszközt. 

## <a name="connection-types"></a>Kapcsolattípusok

A Azure Automation a következő beépített kapcsolattípus-típusokat teszi elérhetővé:

* `Azure` – A klasszikus erőforrások kezeléséhez használt kapcsolatokat jelöli.
* `AzureServicePrincipal` – Az Azure-beli futtató fiók által használt kapcsolatokat jelöli.
* `AzureClassicCertificate` – A klasszikus Azure-beli futtató fiók által használt kapcsolatokat jelöli.

A legtöbb esetben nem kell létrehoznia egy kapcsolódási erőforrást, mert a [futtató fiók](automation-security-overview.md)létrehozásakor jön létre.

## <a name="powershell-cmdlets-to-access-connections"></a>PowerShell-parancsmagok a kapcsolatok eléréséhez

Az alábbi táblázatban található parancsmagok automatizálási kapcsolatokat hoznak létre és kezelhetnek a PowerShell-lel. Az az [modulok](shared-resources/modules.md#az-modules)részét képezik.

|Parancsmag|Leírás|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Adatokat kér le egy kapcsolatban.|
|[Új – AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Új kapcsolatot hoz létre.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Eltávolítja a meglévő kapcsolatokat.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Beállítja egy létező kapcsolat adott mezőjének az értékét.|

## <a name="internal-cmdlets-to-access-connections"></a>A kapcsolatok elérését szolgáló belső parancsmagok

A következő táblázatban található belső parancsmag a runbookok és a DSC-konfigurációk kapcsolatainak elérésére szolgál. Ez a parancsmag a globális modulhoz tartozik `Orchestrator.AssetManagement.Cmdlets` . További információ: [belső parancsmagok](shared-resources/modules.md#internal-cmdlets).

|Belső parancsmag|Description|
|---|---|
|`Get-AutomationConnection` | Lekéri a kapcsolatok különböző mezőinek értékeit, és visszaadja őket [szórótábla](/powershell/module/microsoft.powershell.core/about/about_hash_tables). Ezt a szórótábla a megfelelő parancsokkal használhatja a runbook vagy a DSC-konfigurációban.|

>[!NOTE]
>Ne használjon változókat a `Name` paraméterrel `Get-AutomationConnection` . Ebben az esetben a változók használata megnehezítheti a runbookok vagy DSC-konfigurációk és a kapcsolati erőforrások közötti függőségek felderítését a tervezési időszakban.

## <a name="python-functions-to-access-connections"></a>A kapcsolatok elérésére szolgáló Python-függvények

A következő táblázatban található függvény a Python 2 és 3 runbook lévő kapcsolatok elérésére szolgál. A Python 3 runbookok jelenleg előzetes verzióban érhető el.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_connection` | Egy-egy kapcsolatok beolvasása. Egy olyan szótárt ad vissza, amely a kapcsolatok tulajdonságaival rendelkezik. |

> [!NOTE]
> Az `automationassets` Asset functions eléréséhez importálnia kell a modult a Python-runbook tetején.

## <a name="create-a-new-connection"></a>Új kapcsolat létrehozása

### <a name="create-a-new-connection-with-the-azure-portal"></a>Új kapcsolatok létrehozása a Azure Portal

Új kapcsolatok létrehozása a Azure Portalban:

1. Az Automation-fiókban kattintson a **kapcsolatok** elemre a **megosztott erőforrások** területen.
2. Kattintson a **+ kapcsolat hozzáadása** lehetőségre a kapcsolatok lapon.
4. Az új kapcsolatok ablaktábla **típus** mezőjében válassza ki a létrehozandó kapcsolódási típust. A lehetőségek a következők:, `Azure` `AzureServicePrincipal` és `AzureClassicCertificate` . 
5. Az űrlap megjeleníti a kiválasztott kapcsolattípus tulajdonságait. Töltse ki az űrlapot, és kattintson a **Létrehozás** gombra az új kapcsolódás mentéséhez.

### <a name="create-a-new-connection-with-windows-powershell"></a>Új Windows PowerShell-kapcsolatok létrehozása

Hozzon létre egy új Windows PowerShell-kapcsolatokat a `New-AzAutomationConnection` parancsmag használatával. Ez a parancsmag egy olyan `ConnectionFieldValues` paramétert tartalmaz, amely egy szórótábla határozza meg a kapcsolattípus által meghatározott egyes tulajdonságok értékeit.

Az alábbi parancsokkal Alternatív megoldásként is létrehozhatja a futtató fiókot a portálon egy új kapcsolódási eszköz létrehozásához.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Az Automation-fiók létrehozásakor a szolgáltatás alapértelmezés szerint több globális modult is tartalmaz, valamint a kapcsolódási típust a `AzureServicePrincipal` `AzureRunAsConnection` kapcsolódási eszköz létrehozásához. Ha egy másik hitelesítési módszerrel rendelkező szolgáltatáshoz vagy alkalmazáshoz való csatlakozáshoz próbál új kapcsolati objektumot létrehozni, a művelet meghiúsul, mert a kapcsolat típusa még nincs definiálva az Automation-fiókban. Ha további információt szeretne a saját kapcsolattípus létrehozásáról az egyéni modulhoz, olvassa el [a kapcsolattípus hozzáadása](#add-a-connection-type)című témakört.

## <a name="add-a-connection-type"></a>Kapcsolattípus hozzáadása

Ha a runbook vagy a DSC-konfiguráció egy külső szolgáltatáshoz csatlakozik, akkor egy integrációs modul nevű [Egyéni modulban](shared-resources/modules.md#custom-modules) kell megadnia a kapcsolat típusát. Ez a modul tartalmaz egy metaadat-fájlt, amely megadja a kapcsolattípus tulajdonságait, és neve **&lt; ModuleName &gt;-Automation.js, a** tömörített **. zip** fájl modul mappájában található. Ez a fájl tartalmazza a kapcsolat azon mezőit, amelyek szükségesek a modul által reprezentált rendszerhez vagy szolgáltatáshoz való kapcsolódáshoz. A fájl használatával beállíthatja a mezők nevét, az adattípusokat, a titkosítási állapotot és a választható állapotot a kapcsolattípus számára. 

Az alábbi példa egy **. JSON** fájlformátumú sablon, amely a Felhasználónév és a jelszó tulajdonságait határozza meg egy egyéni kapcsolattípus számára, a következő néven `MyModuleConnection` :

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Runbook vagy DSC-konfigurációban lévő kapcsolatok beszerzése

Egy runbook vagy DSC-konfigurációban lévő, belső parancsmaggal létesített kapcsolatok beolvasása `Get-AutomationConnection` . Ez a parancsmag előnyben részesített a parancsmag felett, mert a kapcsolatra `Get-AzAutomationConnection` vonatkozó információk helyett a kapcsolati értékeket kéri le.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi példa bemutatja, hogyan használható a futtató fiók a runbook Azure Resource Manager erőforrásainak hitelesítéséhez. A futtató fiókot jelképező, a tanúsítványon alapuló egyszerű szolgáltatásra hivatkozó összekötőt használ.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

Az alábbi példa bemutatja, hogyan végezhet hitelesítést a futtató kiszolgáló használatával a Python 2 és 3 runbook.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resource manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

---

### <a name="graphical-runbook-examples"></a>Grafikus runbook-példák

Hozzáadhat egy tevékenységet a belső `Get-AutomationConnection` parancsmaghoz egy grafikus runbook. Kattintson a jobb gombbal a kapcsolódásra a grafikus szerkesztő könyvtár paneljén, és válassza a **Hozzáadás a vászonhoz** lehetőséget.

![Hozzáadás a vászonhoz](media/automation-connections/connection-add-canvas.png)

Az alábbi ábrán egy példa látható, hogy egy grafikus runbook egy kapcsolattípus használatával. Ez a példa a `Constant value` tevékenység adatkészletét használja `Get RunAs Connection` , amely a hitelesítéshez egy kapcsolatok objektumot használ. Itt szerepel egy [folyamat hivatkozása](automation-graphical-authoring-intro.md#use-links-for-workflow) , mert a `ServicePrincipalCertificate` paraméter-készlet egyetlen objektumot vár.

![kapcsolatok beolvasása](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a kapcsolatok eléréséhez használt parancsmagokról, tekintse meg a [modulok kezelése a Azure Automationban](shared-resources/modules.md)című témakört.
* A runbookok kapcsolatos általános információkért lásd: [a Runbook végrehajtása Azure Automation](automation-runbook-execution.md).
* A DSC-konfigurációk részletes ismertetését lásd: az [állapot-konfiguráció áttekintése](automation-dsc-overview.md).