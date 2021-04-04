---
title: Tanúsítványok kezelése a Azure Automationban
description: Ez a cikk azt ismerteti, hogyan használhatók a tanúsítványok a runbookok és a DSC-konfigurációkhoz való hozzáféréshez.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: cbf9eb6c97dcceeca5e86e8bef47a39fb685792f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97734810"
---
# <a name="manage-certificates-in-azure-automation"></a>Tanúsítványok kezelése a Azure Automationban

Azure Automation a tanúsítványokat biztonságos módon tárolja a runbookok és a DSC-konfigurációk számára, a [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) parancsmag használatával Azure Resource Manager erőforrásokhoz. A biztonságos tanúsítványtároló lehetővé teszi, hogy olyan runbookok és DSC-konfigurációkat hozzon létre, amelyek tanúsítványokat használnak a hitelesítéshez, illetve hozzáadhatják őket az Azure-hoz vagy harmadik féltől származó erőforrásokhoz.

>[!NOTE]
>Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Az eszközök titkosítása és automatizálása az egyes Automation-fiókokhoz generált egyedi kulcs használatával történik. Az Automation tárolja a kulcsot a rendszer által felügyelt Key Vault szolgáltatásban. A biztonságos eszköz tárolása előtt az Automation betölti a kulcsot a Key Vault, majd a használatával titkosítja az eszközt.

## <a name="powershell-cmdlets-to-access-certificates"></a>A tanúsítványok eléréséhez szükséges PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok automatizálási tanúsítványokat hoznak létre és kezelhetnek a PowerShell-lel. Az az [modulok](modules.md#az-modules)részét képezik.

|Parancsmag |Leírás|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Egy runbook vagy DSC-konfigurációban használandó tanúsítvány információinak beolvasása. A tanúsítványt csak a belső parancsmag használatával kérheti le `Get-AutomationCertificate` .|
|[Új – AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Új tanúsítvány létrehozása az Automationben.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Eltávolít egy tanúsítványt az Automationből.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Beállítja egy meglévő tanúsítvány tulajdonságait, beleértve a tanúsítványfájl feltöltését és a **. pfx** fájl jelszavának beállítását.|

Az [Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) parancsmag használható a megadott felhőalapú szolgáltatáshoz tartozó szolgáltatási tanúsítvány feltöltésére is.

## <a name="internal-cmdlets-to-access-certificates"></a>A tanúsítványok elérésére szolgáló belső parancsmagok

A következő táblázatban található belső parancsmag a runbookok lévő tanúsítványok elérésére szolgál. Ez a parancsmag a globális modulhoz tartozik `Orchestrator.AssetManagement.Cmdlets` . További információ: [belső parancsmagok](modules.md#internal-cmdlets).

| Belső parancsmag | Leírás |
|:---|:---|
|`Get-AutomationCertificate`|Lekéri egy runbook vagy DSC-konfigurációban használandó tanúsítványt. Egy [System. Security. kriptográfiai. X509Certificates. x509certificate2)](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) objektumot ad vissza.|

> [!NOTE]
> Ne használjon változókat a `Name` `Get-AutomationCertificate` RUNBOOK vagy DSC-konfigurációban található paraméterben. Ezek a változók megnehezítik a runbookok-vagy DSC-konfigurációk és az Automation-változók közötti függőségek felderítését a tervezési időszakban.

## <a name="python-functions-to-access-certificates"></a>Python-függvények a tanúsítványok eléréséhez

A következő táblázatban található függvény használatával férhet hozzá a Python 2 és 3 runbook lévő tanúsítványokhoz. A Python 3 runbookok jelenleg előzetes verzióban érhető el.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_certificate` | Adatokat kér le egy tanúsítvány tárgyáról. |

> [!NOTE]
> Az `automationassets` Asset functions eléréséhez importálnia kell a modult a Python-runbook elején.

## <a name="create-a-new-certificate"></a>Új tanúsítvány létrehozása

Új tanúsítvány létrehozásakor fel kell töltenie egy. cer vagy. pfx fájlt az Automation szolgáltatásba. Ha a tanúsítványt exportálható jelöli meg, akkor átviheti azt az Automation-tanúsítványtárolóból. Ha nem exportálható, akkor csak a runbook vagy DSC-konfiguráción belüli aláíráshoz használható. Az Automation megköveteli, hogy a tanúsítvány rendelkezzen a szolgáltató **Microsoft Enhanced RSA és AES titkosítási szolgáltatóval**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Új tanúsítvány létrehozása a Azure Portal

1. Az Automation-fiókban a bal oldali ablaktáblán válassza a **tanúsítványok** lehetőséget a **megosztott erőforrás** területen.
1. A **tanúsítványok** lapon válassza a **tanúsítvány hozzáadása** lehetőséget.
1. A **név** mezőbe írja be a tanúsítvány nevét.
1. Egy **. cer** vagy **. pfx** fájl tallózásához a **tanúsítványfájl feltöltése** területen válassza **a fájl kiválasztása** lehetőséget. Ha **. pfx** -fájlt választ, adjon meg egy jelszót, és jelezze, hogy exportálható-e.
1. Válassza a **Létrehozás** lehetőséget az új tanúsítvány-eszköz mentéséhez.

### <a name="create-a-new-certificate-with-powershell"></a>Új tanúsítvány létrehozása a PowerShell-lel

Az alábbi példa bemutatja, hogyan hozhat létre egy új Automation-tanúsítványt, és hogyan jelölheti meg exportálható. Ez a példa egy meglévő **. pfx** fájlt importál.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Új tanúsítvány létrehozása Resource Manager-sablonnal

Az alábbi példa bemutatja, hogyan helyezhet üzembe egy tanúsítványt az Automation-fiókjában egy Resource Manager-sablonnal a PowerShell használatával:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path and filename>'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>' #A name of your choosing
$ResourceGroupName = '<resource group name>' #The one that holds your automation account
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName $ResourceGroupName -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Tanúsítvány beszerzése

Tanúsítvány lekéréséhez használja a belső `Get-AutomationCertificate` parancsmagot. Nem használhatja a [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) parancsmagot, mert információt ad vissza a tanúsítvány adategységéről, de nem maga a tanúsítvány.

### <a name="textual-runbook-examples"></a>Szöveges runbook-példák

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi példa bemutatja, hogyan adhat hozzá egy tanúsítványt egy felhőalapú szolgáltatáshoz egy runbook. Ebben a példában a jelszót egy titkosított Automation-változóból kéri le a rendszer.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Az alábbi példa bemutatja, hogyan érheti el a tanúsítványokat a Python 2 runbookok.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3](#tab/python3)

Az alábbi példa bemutatja, hogyan érheti el a tanúsítványokat a Python 3 runbookok (előzetes verzió).

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>Példa grafikus runbook

Adjon hozzá egy tevékenységet a belső `Get-AutomationCertificate` parancsmaghoz egy grafikus runbook. ehhez kattintson a jobb gombbal a tanúsítványra a könyvtár ablaktáblán, és válassza a **Hozzáadás a vászonhoz** lehetőséget.

![Képernyőfelvétel a tanúsítvány hozzáadásáról a vászonhoz](../media/certificates/automation-certificate-add-to-canvas.png)

Az alábbi képen egy példa látható a tanúsítvány grafikus runbook való használatára.

![Képernyőkép egy grafikus szerzői műveletről](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a tanúsítványok eléréséhez használt parancsmagokról, tekintse meg a [modulok kezelése a Azure Automationban](modules.md)című témakört.
* A runbookok kapcsolatos általános információkért lásd: [a Runbook végrehajtása Azure Automation](../automation-runbook-execution.md).
* A DSC-konfigurációk részletes ismertetését lásd: [Azure Automation állapot konfigurációjának áttekintése](../automation-dsc-overview.md).
