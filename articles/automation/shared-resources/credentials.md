---
title: Hitelesítő adatok kezelése az Azure Automationben
description: Ez a cikk azt ismerteti, hogyan hozhatók létre hitelesítőadat-eszközök, és hogyan használhatók runbook vagy DSC-konfigurációban.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 9b9e42d55a982aeb55d7c9e26f7b1a6cbca32e0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052804"
---
# <a name="manage-credentials-in-azure-automation"></a>Hitelesítő adatok kezelése az Azure Automationben

Az Automation hitelesítőadat-adategység olyan objektumot tartalmaz, amely biztonsági hitelesítő adatokat tartalmaz, például egy felhasználónevet és egy jelszót. A runbookok és a DSC-konfigurációk olyan parancsmagokat használnak, amelyek elfogadnak egy [PSCredential](/dotnet/api/system.management.automation.pscredential) objektumot a hitelesítéshez. Azt is megteheti, hogy kinyeri az objektum felhasználónevét és jelszavát `PSCredential` , hogy a hitelesítést igénylő alkalmazások vagy szolgáltatások számára biztosítson.

>[!NOTE]
>Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a rendszer titkosítja és tárolja Azure Automation az egyes Automation-fiókokhoz generált egyedi kulcs használatával. Azure Automation a kulcsot a rendszerfelügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt az Automation betölti a kulcsot Key Vault, majd a használatával titkosítja az eszközt. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>A hitelesítő adatok eléréséhez használt PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok automatizálási hitelesítő adatokat hoznak létre és kezelhetnek a PowerShell-lel. Az az [modulok](modules.md#az-modules)részét képezik.

| Parancsmag | Leírás |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |A hitelesítő adatokkal kapcsolatos metaadatokat tartalmazó [CredentialInfo](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) objektum beolvasása. A parancsmag nem kérdezi le `PSCredential` magát az objektumot.  |
| [Új – AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |Létrehoz egy új Automation-hitelesítő adatot. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Eltávolít egy Automation-hitelesítő adatot. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |Egy meglévő Automation-hitelesítő adat tulajdonságainak beállítása. |

## <a name="other-cmdlets-used-to-access-credentials"></a>A hitelesítő adatok eléréséhez használt egyéb parancsmagok

A következő táblázatban található parancsmagok a runbookok és a DSC-konfigurációkhoz tartozó hitelesítő adatok elérésére szolgálnak. 

| Parancsmag | Leírás |
|:--- |:--- |
| `Get-AutomationPSCredential` |Lekéri egy `PSCredential` runbook vagy DSC-konfigurációban használandó objektumot. Leggyakrabban a parancsmag helyett ezt a [belső parancsmagot](modules.md#internal-cmdlets) kell használnia, `Get-AzAutomationCredential` mivel az utóbbi csak a hitelesítő adatokat kéri le. Ezek az információk általában nem hasznosak egy másik parancsmagnak való továbbításra. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |Hitelesítő adat beolvasása a Felhasználónév és a jelszó megadásával. Ez a parancsmag az alapértelmezett Microsoft. PowerShell. Security modul része. Lásd: [alapértelmezett modulok](modules.md#default-modules).|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | Egy hitelesítőadat-eszköz létrehozása. Ez a parancsmag az alapértelmezett Azure-modul része. Lásd: [alapértelmezett modulok](modules.md#default-modules).|

Ha objektumokat szeretne beolvasni `PSCredential` a kódban, importálnia kell a `Orchestrator.AssetManagement.Cmdlets` modult. További információkért lásd: [modulok kezelése Azure Automationban](modules.md).

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Kerülje a változók használatát a `Name` paraméterében `Get-AutomationPSCredential` . A használatuk megnehezítheti a runbookok vagy DSC-konfigurációk és a hitelesítő adatok közötti függőségek felderítését a tervezési időszakban.

## <a name="python-functions-that-access-credentials"></a>A hitelesítő adatokhoz hozzáférő Python-függvények

A következő táblázatban szereplő függvény a hitelesítő adatoknak a Python 2 és 3 runbook való elérésére szolgál. A Python 3 runbookok jelenleg előzetes verzióban érhető el.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_credential` | A hitelesítő adatokkal kapcsolatos adatok beolvasása. |

> [!NOTE]
> Importálja a `automationassets` modult a Python-runbook tetején az eszköz funkcióinak eléréséhez.

## <a name="create-a-new-credential-asset"></a>Új hitelesítőadat-eszköz létrehozása

Létrehozhat egy új hitelesítőadat-eszközt a Azure Portal vagy a Windows PowerShell használatával.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Új hitelesítőadat-eszköz létrehozása a Azure Portal

1. Az Automation-fiókban a bal oldali ablaktáblán válassza a **hitelesítő adatok** lehetőséget a **megosztott erőforrások** területen.
2. A **hitelesítő adatok** lapon válassza a **hitelesítő adatok hozzáadása** elemet.
3. Az új hitelesítő adatok ablaktáblán adjon meg egy megfelelő hitelesítő nevet az elnevezési szabványoknak megfelelően.
4. Írja be a hozzáférési azonosítót a **Felhasználónév** mezőbe.
5. A jelszó mezőben adja meg a titkos kulcs elérési kulcsát.

    ![Új hitelesítő adat létrehozása](../media/credentials/credential-create.png)

6. Ha a multi-Factor Authentication jelölőnégyzet be van jelölve, törölje azt.
7. Kattintson a **Létrehozás** gombra az új hitelesítőadat-eszköz mentéséhez.

> [!NOTE]
> A Azure Automation nem támogatja a többtényezős hitelesítést használó felhasználói fiókokat.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Új hitelesítőadat-eszköz létrehozása a Windows PowerShell használatával

Az alábbi példa bemutatja, hogyan hozhat létre új Automation hitelesítőadat-eszközt. A `PSCredential` rendszer először a névvel és jelszóval hozza létre az objektumot, majd a hitelesítő adatok létrehozásához használja. Ehelyett a parancsmaggal kérheti `Get-Credential` a felhasználótól, hogy adjon meg egy nevet és egy jelszót.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Hitelesítőadat-eszköz beszerzése

A runbook vagy DSC-konfiguráció a belső parancsmaggal kérdezi le a hitelesítőadat-eszközt `Get-AutomationPSCredential` . Ez a parancsmag egy olyan `PSCredential` objektumot kap, amelyet a hitelesítő adatokhoz szükséges parancsmaggal használhat. A hitelesítőadat-objektum tulajdonságait egyenként is lekérheti. Az objektum a Felhasználónév és a biztonságos jelszó tulajdonságaival rendelkezik. 

> [!NOTE]
> A `Get-AzAutomationCredential` parancsmag nem kér le olyan `PSCredential` objektumot, amely hitelesítésre használható. Csak a hitelesítő adatokkal kapcsolatos információkat tartalmazza. Ha hitelesítő adatokat kell használnia egy runbook, azt a használatával kell lekérnie `PSCredential` objektumként `Get-AutomationPSCredential` .

Azt is megteheti, hogy a [GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) metódus használatával lekéri a jelszó nem védett verzióját képviselő [NetworkCredential](/dotnet/api/system.net.networkcredential) objektumot.

### <a name="textual-runbook-example"></a>Szöveges runbook példa

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi példa bemutatja, hogyan használhatók PowerShell-hitelesítő adatok egy runbook. Lekéri a hitelesítő adatokat, és hozzárendeli a felhasználónevet és a jelszót a változókhoz.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Az Azure-ban való hitelesítéshez hitelesítő adatokat is használhat a [AzAccount](/powershell/module/az.accounts/connect-azaccount)használatával. A legtöbb esetben a [futtató fiókot](../automation-security-overview.md#run-as-accounts) kell használnia, és a [Get-AzAutomationConnection](../automation-connections.md)használatával kell lekérnie a kapcsolatokat.

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Az alábbi példa egy példát mutat be a hitelesítő adatok elérésére a Python 2 runbookok.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

A következő példa egy példát mutat be a hitelesítő adatok elérésére a Python 3 runbookok (előzetes verzió).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>Példa grafikus runbook

Hozzáadhat egy tevékenységet a belső `Get-AutomationPSCredential` parancsmaghoz egy grafikus runbook. ehhez kattintson a jobb gombbal a hitelesítő adatokra a grafikus szerkesztő könyvtár paneljén, és válassza a **Hozzáadás a vászonhoz** lehetőséget.

![Hitelesítőadat-parancsmag hozzáadása a vászonhoz](../media/credentials/credential-add-canvas.png)

Az alábbi képen egy példa látható a hitelesítő adatok grafikus runbook való használatára. Ebben az esetben a hitelesítő adatok hitelesítési adatokat biztosítanak az Azure-erőforrások runbook az Azure [ad-ben való hitelesítéshez Azure Automationban](../automation-use-azure-ad.md). Az első tevékenység lekéri az Azure-előfizetéshez hozzáférő hitelesítő adatokat. A fiók kapcsolódási tevékenysége ezután ezt a hitelesítő adatot használja az azt követő tevékenységek hitelesítésének biztosításához. Itt egy [folyamatra mutató hivatkozást](../automation-graphical-authoring-intro.md#use-links-for-workflow) kell használni, mivel `Get-AutomationPSCredential` egyetlen objektumot vár.  

![Hitelesítő adatok munkafolyamata kapcsolati hivatkozással – példa](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Hitelesítő adatok használata DSC-konfigurációban

Habár a Azure Automation DSC-konfigurációja a hitelesítő adatokkal is működhet a használatával `Get-AutomationPSCredential` , a hitelesítő adatokat a paraméterek segítségével is átadhatják. További információ: [konfigurációk fordítása Azure Automation DSC-ben](../automation-dsc-compile.md#credential-assets).

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a tanúsítványok eléréséhez használt parancsmagokról, tekintse meg a [modulok kezelése a Azure Automationban](modules.md)című témakört.
* A runbookok kapcsolatos általános információkért lásd: [a Runbook végrehajtása Azure Automation](../automation-runbook-execution.md).
* A DSC-konfigurációk részletes ismertetését lásd: [Azure Automation állapot konfigurációjának áttekintése](../automation-dsc-overview.md).