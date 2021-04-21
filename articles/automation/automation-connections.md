---
title: Kapcsolatok kezelése a Azure Automation
description: Ez a cikk bemutatja, hogyan kezelheti a Azure Automation szolgáltatásokhoz vagy alkalmazásokhoz való csatlakozást, és hogyan munkához a runbookokkal.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 4b5983d6ea4ea9065d1292a2c5ee0c094cea093b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834893"
---
# <a name="manage-connections-in-azure-automation"></a>Kapcsolatok kezelése a Azure Automation

Egy Azure Automation kapcsolati eszköz tartalmazza az alább felsorolt információkat. Erre az információra akkor van szükség, ha runbookból vagy DSC-konfigurációból szeretnénk kapcsolatot létesíteni egy külső szolgáltatással vagy alkalmazással. 

* A hitelesítéshez szükséges információk, például felhasználónév és jelszó
* Kapcsolati adatok, például URL-cím vagy port

A kapcsolati eszköz minden tulajdonságot együtt tart egy adott alkalmazáshoz való csatlakozáshoz, így nincs szükség több változó létrehozására. A kapcsolat értékeit egy helyen szerkesztheti, a kapcsolat nevét pedig egyetlen paraméterben használhatja egy runbooknak vagy DSC-konfigurációnak. A runbook vagy konfiguráció a belső parancsmaggal fér hozzá a kapcsolat `Get-AutomationConnection` tulajdonságaihoz.

A kapcsolat létrehozásakor meg kell adnia egy kapcsolattípust. A kapcsolat típusa egy olyan sablon, amely tulajdonságok egy halmazát definiálja. A metaadatfájllal egy integrációs modullal Azure Automation kapcsolattípust adhat hozzá a szolgáltatáshoz. A Azure Automation [API](/previous-versions/azure/reference/mt163818(v=azure.100)) használatával kapcsolattípus is létrehozható, ha az integrációs modul tartalmaz kapcsolattípust, és importálva van az Automation-fiókjába. 

>[!NOTE]
>A biztonsági Azure Automation tartalmaznak hitelesítő adatokat, tanúsítványokat, kapcsolatokat és titkosított változókat. Ezek az eszközök titkosítva vannak, Azure Automation tárolódnak egy egyedi kulccsal, amely az egyes Automation-fiókokhoz jön létre. Azure Automation a kulcsot a rendszer által felügyelt Key Vault. Biztonságos adateszköz tárolása előtt az Automation betölti a kulcsot a Key Vault majd a segítségével titkosítja az eszközt. 

## <a name="connection-types"></a>Kapcsolattípusok

Azure Automation a következő beépített kapcsolattípusokat teszi elérhetővé:

* `Azure` – A klasszikus erőforrások kezeléséhez használt kapcsolatot jelöli.
* `AzureServicePrincipal` – Az Azure-beli run as-fiók által használt kapcsolatot jelöli.
* `AzureClassicCertificate` – A klasszikus Azure-beli run as-fiók által használt kapcsolatot jelöli.

A legtöbb esetben nem kell kapcsolati erőforrást létrehoznia, mert az a fiók létrehozásakor [jön létre.](automation-security-overview.md)

## <a name="powershell-cmdlets-to-access-connections"></a>PowerShell-parancsmagok a kapcsolatok eléréséhez

Az alábbi táblázatban található parancsmagok Automation-kapcsolatokat hoznak létre és kezelnek a PowerShell használatával. A modulokat az Az modulok [részeként szállítják.](shared-resources/modules.md#az-modules)

|Parancsmag|Leírás|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Lekéri a kapcsolat adatait.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Új kapcsolatot hoz létre.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Eltávolít egy meglévő kapcsolatot.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Beállítja egy létező kapcsolat adott mezőjének az értékét.|

## <a name="internal-cmdlets-to-access-connections"></a>Belső parancsmagok a kapcsolatok eléréséhez

Az alábbi táblázatban található belső parancsmag a runbookok és a DSC-konfigurációk kapcsolatainak elérésére használható. Ez a parancsmag a globális modulhoz is `Orchestrator.AssetManagement.Cmdlets` jár. További információ: [Belső parancsmagok.](shared-resources/modules.md#internal-cmdlets)

|Belső parancsmag|Description|
|---|---|
|`Get-AutomationConnection` | Lekéri a kapcsolat különböző mezőinek értékeit, és kivonattáblázatként adja [vissza őket.](/powershell/module/microsoft.powershell.core/about/about_hash_tables) Ezután használhatja ezt a kivonattáblát a runbook vagy a DSC konfigurációjában a megfelelő parancsokkal.|

>[!NOTE]
>Kerülje a változók a `Name` paraméterrel való `Get-AutomationConnection` használatát. Ebben az esetben a változók használata megnehezítheti a runbookok vagy A DSC-konfigurációk és a kapcsolati eszközök közötti függőségek felderítését a tervezéskor.

## <a name="python-functions-to-access-connections"></a>Python-függvények a kapcsolatok eléréséhez

Az alábbi táblázatban található függvény a Python 2-es és 3-as runbook kapcsolatainak elérésére használható. A Python 3-runbookok jelenleg előzetes verzióban érhetőek el.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_connection` | Lekér egy kapcsolatot. Egy szótárt ad vissza a kapcsolat tulajdonságaival. |

> [!NOTE]
> Az adategység-függvények eléréséhez importálni kell a `automationassets` Python-runbook tetején található modult.

## <a name="create-a-new-connection"></a>Új kapcsolat létrehozása

### <a name="create-a-new-connection-with-the-azure-portal"></a>Hozzon létre egy új kapcsolatot a Azure Portal

Új kapcsolat létrehozása a Azure Portal:

1. Az Automation-fiókjában kattintson a **Kapcsolatok elemre a** Megosztott erőforrások **alatt.**
2. A **Kapcsolatok lapon kattintson a +** Kapcsolat hozzáadása elemre.
4. Az Új **kapcsolat** panel Típus mezőjében válassza ki a létrehozni kívánt kapcsolat típusát. A választható lehetőségek a `Azure` , a és a `AzureServicePrincipal` `AzureClassicCertificate` . 
5. Az űrlap a kiválasztott kapcsolattípus tulajdonságait mutatja be. Töltse ki az űrlapot, és kattintson a **Létrehozás gombra** az új kapcsolat mentéshez.

### <a name="create-a-new-connection-with-windows-powershell"></a>Új kapcsolat létrehozása a Windows PowerShell

Hozzon létre egy új kapcsolatot a Windows PowerShell a `New-AzAutomationConnection` parancsmaggal. Ez a parancsmag egy olyan paraméterrel rendelkezik, amely egy kivonattáblát vár, amely a kapcsolattípus által meghatározott egyes tulajdonságok `ConnectionFieldValues` értékeit határozza meg.

Az alábbi példaparancsokkal alternatív megoldásként létrehozhat egy új kapcsolati adateszközt a portálról a futtatott fiók létrehozásához.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Az Automation-fiók létrehozásakor az alapértelmezés szerint számos globális modult tartalmaz, valamint a kapcsolattípust a `AzureServicePrincipal` `AzureRunAsConnection` kapcsolati eszköz létrehozásához. Ha olyan új kapcsolati eszközt próbál létrehozni, amely egy másik hitelesítési módszerrel csatlakozik egy szolgáltatáshoz vagy alkalmazáshoz, a művelet meghiúsul, mert a kapcsolat típusa még nincs meghatározva az Automation-fiókban. További információ saját kapcsolattípus létrehozásáról egy egyéni modulhoz: [Kapcsolattípus hozzáadása.](#add-a-connection-type)

## <a name="add-a-connection-type"></a>Kapcsolattípus hozzáadása

Ha a runbook vagy a DSC konfigurációja egy külső szolgáltatáshoz [](shared-resources/modules.md#custom-modules) csatlakozik, meg kell határoznia egy kapcsolattípust egy integrációs modulnak nevezett egyéni modulban. Ez a modul egy metaadatfájlt tartalmaz, amely megadja a kapcsolattípus-tulajdonságokat, és **&lt; &gt; a(z) ModuleName-Automation.js** a(z) helyen található, a tömörített .zip fájl **modulmappában.** Ez a fájl tartalmazza a modul által képviselt rendszerhez vagy szolgáltatáshoz való csatlakozáshoz szükséges kapcsolat mezőit. Ezzel a fájllal beállíthatja a mezőneveket, az adattípusokat, a titkosítás állapotát és a kapcsolattípus opcionális állapotát. 

Az alábbi példa egy **.json** fájlformátumú sablon, amely egy nevű egyéni kapcsolattípus felhasználónevét és jelszótulajdonságát határozza `MyModuleConnection` meg:

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

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Kapcsolat létrehozása runbook- vagy DSC-konfigurációban

Kapcsolat lekérése runbook- vagy DSC-konfigurációban a belső `Get-AutomationConnection` parancsmaggal. Ezt a parancsmagot részesíti előnyben a parancsmag, mivel a kapcsolati adatok helyett a kapcsolatértékeket `Get-AzAutomationConnection` olvassa be.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi példa bemutatja, hogyan hitelesíthet a runbook Azure Resource Manager erőforrásokkal a runbookban. Egy kapcsolati eszközt használ, amely a tanúsítványalapú szolgáltatásnévre hivatkozó, a futtatásalapú fiókot képviseli.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

Az alábbi példa bemutatja, hogyan lehet hitelesítést végezni a Python 2-es és 3-as runbookok futtatás kapcsolatával.

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

A belső parancsmaghoz tevékenységeket adhat hozzá `Get-AutomationConnection` egy grafikus runbookhoz. Kattintson a jobb gombbal a kapcsolatra a grafikus szerkesztő Könyvtár panelen, és válassza a **Hozzáadás a vászonhoz lehetőséget.**

![hozzáadás a vászonhoz](media/automation-connections/connection-add-canvas.png)

Az alábbi képen egy példa látható egy kapcsolati objektum grafikus runbookban való használatra. Ez a példa a tevékenység `Constant value` adatkészletét `Get RunAs Connection` használja, amely egy kapcsolatobjektumot használ a hitelesítéshez. Itt [folyamathivatkozást](automation-graphical-authoring-intro.md#use-links-for-workflow) használunk, mivel `ServicePrincipalCertificate` a paraméterkészlet egyetlen objektumot vár.

![kapcsolatok lekért száma](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Következő lépések

* A kapcsolatok eléréséhez használt parancsmagokkal kapcsolatos további információkért lásd: Modulok kezelése a [Azure Automation.](shared-resources/modules.md)
* A runbookokkal kapcsolatos általános információkért lásd: [Runbook végrehajtása](automation-runbook-execution.md)a Azure Automation.
* A DSC-konfigurációk részleteiért lásd a State Configuration [áttekintését.](automation-dsc-overview.md)
