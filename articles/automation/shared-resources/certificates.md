---
title: Tanúsítványok kezelése a Azure Automation
description: Ez a cikk azt mutatja be, hogyan lehet tanúsítványokkal dolgozni a runbookok és A DSC-konfigurációk által való hozzáféréshez.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 788f15cd1edad228e695e6e87f5b630b8e4fdf55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834443"
---
# <a name="manage-certificates-in-azure-automation"></a>Tanúsítványok kezelése a Azure Automation

Azure Automation a Runbookok és DSC-konfigurációk által biztonságosan eltárolt tanúsítványokat a [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) parancsmag használatával a Azure Resource Manager erőforrásokhoz. A biztonságos tanúsítványtárolással olyan runbookokat és DSC-konfigurációkat hozhat létre, amelyek tanúsítványokat használnak a hitelesítéshez, vagy azure- vagy külső erőforrásokhoz adjuk őket.

>[!NOTE]
>A biztonsági Azure Automation tartalmaznak hitelesítő adatokat, tanúsítványokat, kapcsolatokat és titkosított változókat. Ezek az eszközök egy egyedi kulccsal vannak titkosítva és tárolva az Automationben, amely az egyes Automation-fiókokhoz jön létre. Az Automation a kulcsot a rendszer által felügyelt Key Vault tárolja. Biztonságos adateszköz tárolása előtt az Automation betölti a kulcsot a Key Vault, majd a segítségével titkosítja az eszközt.

## <a name="powershell-cmdlets-to-access-certificates"></a>PowerShell-parancsmagok a tanúsítványok eléréséhez

Az alábbi táblázatban található parancsmagok Automation-tanúsítványokat hoznak létre és kezelnek a PowerShell használatával. A modulokat az Az modulok [részeként szállítják.](modules.md#az-modules)

|Parancsmag |Leírás|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Lekéri egy runbookban vagy DSC-konfigurációban használható tanúsítvány adatait. Magát a tanúsítványt csak a belső parancsmag használatával `Get-AutomationCertificate` lehet lekérni.|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Létrehoz egy új tanúsítványt az Automationben.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Eltávolít egy tanúsítványt az Automationből.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Beállítja egy meglévő tanúsítvány tulajdonságait, beleértve a tanúsítványfájl feltöltését és a **.pfx fájl jelszavának beállítását.**|

Az [Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) parancsmag a megadott felhőszolgáltatás szolgáltatás tanúsítványának feltöltését is lehetővé tért.

## <a name="internal-cmdlets-to-access-certificates"></a>Belső parancsmagok a tanúsítványok eléréséhez

Az alábbi táblázatban található belső parancsmag a runbookok tanúsítványaihoz való hozzáférésre használható. Ez a parancsmag a globális modulhoz is `Orchestrator.AssetManagement.Cmdlets` jár. További információ: [Belső parancsmagok.](modules.md#internal-cmdlets)

| Belső parancsmag | Description |
|:---|:---|
|`Get-AutomationCertificate`|Lekér egy tanúsítványt, amely runbookban vagy DSC-konfigurációban használható. Egy [System.Security.Cryptography.X509Certificates.X509Certificate2 objektumot ad](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) vissza.|

> [!NOTE]
> Ne használja a változókat a(z) paraméterében `Name` `Get-AutomationCertificate` runbook- vagy DSC-konfigurációban. Az ilyen változók a tervezéskor bonyolítják a runbookok vagy a DSC-konfigurációk és az Automation-változók közötti függőségek felderítését.

## <a name="python-functions-to-access-certificates"></a>Python-függvények a tanúsítványok eléréséhez

Használja az alábbi táblázatban található függvényt a Python 2- és 3-runbookban található tanúsítványok eléréséhez. A Python 3-runbookok jelenleg előzetes verzióban állnak rendelkezésre.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_certificate` | Lekéri egy tanúsítványeszköz adatait. |

> [!NOTE]
> Az adategység-függvények eléréséhez importálni kell a modult a `automationassets` Python-runbook elején.

## <a name="create-a-new-certificate"></a>Új tanúsítvány létrehozása

Új tanúsítvány létrehozásakor feltölt egy .cer vagy .pfx fájlt az Automationbe. Ha exportálhatóként megjelöli a tanúsítványt, akkor átveheti azt az Automation tanúsítványtárolóból. Ha nem exportálható, akkor csak runbook- vagy DSC-konfiguráción belüli bejelentkezéshez használható. Az Automatizálás megköveteli, hogy a tanúsítvány **microsoftos bővített RSA- és AES-titkosítási szolgáltatóval rendelkezik.**

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Hozzon létre egy új tanúsítványt a Azure Portal

1. Az Automation-fiókjában a bal oldali panelen válassza a **Tanúsítványok** lehetőséget a **Megosztott erőforrás alatt.**
1. A Tanúsítványok **lapon** válassza a **Tanúsítvány hozzáadása lehetőséget.**
1. A Név **mezőbe** írja be a tanúsítvány nevét.
1. .cer- vagy **.pfx-fájl** tallózáshoz a **Tanúsítványfájl** feltöltése alatt válassza **a Fájl kiválasztása lehetőséget.**  Ha **.pfx** fájlt választ, adjon meg egy jelszót, és jelezze, hogy exportálható-e.
1. Az **új tanúsítványeszköz** mentéshez válassza a Létrehozás lehetőséget.

### <a name="create-a-new-certificate-with-powershell"></a>Új tanúsítvány létrehozása a PowerShell használatával

Az alábbi példa bemutatja, hogyan hozhat létre egy új Automation-tanúsítványt, és hogyan jelölheti meg exportálhatóként. Ez a példa egy meglévő **.pfx fájlt importál.**

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath -Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Új tanúsítvány létrehozása Resource Manager sablonnal

Az alábbi példa bemutatja, hogyan helyezhet üzembe tanúsítványt az Automation-fiókjában egy Resource Manager PowerShellen keresztül:

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

## <a name="get-a-certificate"></a>Tanúsítvány lekérve

Tanúsítvány lekérése a belső `Get-AutomationCertificate` parancsmag használatával. A [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) parancsmag nem használható, mert a tanúsítványeszközre vonatkozó adatokat ad vissza, magát a tanúsítványt azonban nem.

### <a name="textual-runbook-examples"></a>Példák szöveges runbookra

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi példa bemutatja, hogyan adhat hozzá tanúsítványt egy felhőszolgáltatáshoz egy runbookban. Ebben a példában a jelszót egy titkosított automatizálási változóból olvassa be a rendszer.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Az alábbi példa bemutatja, hogyan férhet hozzá a Python 2-runbookok tanúsítványaihoz.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3](#tab/python3)

Az alábbi példa bemutatja, hogyan férhet hozzá a tanúsítványokhoz a Python 3-runbookok (előzetes verzió) használatával.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>Példa grafikus runbookra

Adjon hozzá egy tevékenységet a belső parancsmaghoz egy grafikus runbookhoz. Ehhez kattintson a jobb gombbal a tanúsítványra a Könyvtár `Get-AutomationCertificate` panelen, majd válassza a Hozzáadás a **vászonhoz lehetőséget.**

![Képernyőkép egy tanúsítvány vászonhoz való hozzáadásáról](../media/certificates/automation-certificate-add-to-canvas.png)

Az alábbi képen egy példa látható egy tanúsítvány grafikus runbookban való használatával.

![Grafikus tartalom készítésére példa képernyőképe](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>Következő lépések

* A tanúsítványok eléréséhez használt parancsmagokkal kapcsolatos további információkért lásd: Modulok kezelése a [Azure Automation.](modules.md)
* A runbookokkal kapcsolatos általános információkért lásd: [Runbook végrehajtása](../automation-runbook-execution.md)a Azure Automation.
* A DSC-konfigurációk részleteiért lásd a Azure Automation State Configuration [áttekintését.](../automation-dsc-overview.md)
