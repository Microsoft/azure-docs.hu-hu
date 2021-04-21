---
title: Hitelesítő adatok kezelése az Azure Automationben
description: Ez a cikk bemutatja, hogyan hozhat létre hitelesítő adateszközöket, és hogyan használhatja őket runbookban vagy DSC-konfigurációban.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6220a44e952aa4d9856ac5fc2077d254103d4a2c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834281"
---
# <a name="manage-credentials-in-azure-automation"></a>Hitelesítő adatok kezelése az Azure Automationben

Az Automation hitelesítő adatobjektuma tartalmaz egy objektumot, amely biztonsági hitelesítő adatokat tartalmaz, például egy felhasználónevet és egy jelszót. A runbookok és a DSC-konfigurációk olyan parancsmagokat használnak, amelyek [pscredential](/dotnet/api/system.management.automation.pscredential) objektumot fogadnak el a hitelesítéshez. Másik lehetőségként kinyerheti az objektum felhasználónevét és jelszavát, hogy egy hitelesítést igénylő alkalmazásnak vagy `PSCredential` szolgáltatásnak adja meg.

>[!NOTE]
>A biztonsági Azure Automation tartalmaznak hitelesítő adatokat, tanúsítványokat, kapcsolatokat és titkosított változókat. Ezek az eszközök titkosítva vannak, Azure Automation tárolódnak egy egyedi kulccsal, amely az egyes Automation-fiókokhoz jön létre. Azure Automation a kulcsot a rendszer által felügyelt Key Vault. Biztonságos adateszköz tárolása előtt az Automation betölti a kulcsot a Key Vault majd a segítségével titkosítja az eszközt. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>A hitelesítő adatok eléréséhez használt PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok létrehoznak és kezelnek Automation hitelesítő adatokat a PowerShell-rel. A modulokat az Az modulok [részeként szállítják.](modules.md#az-modules)

| Parancsmag | Leírás |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |Lekér egy [CredentialInfo objektumot,](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) amely a hitelesítő adatok metaadatait tartalmazza. A parancsmag nem magát az objektumot `PSCredential` olvassa be.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |Létrehoz egy új Automation-hitelesítő adatokat. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Eltávolít egy Automation-hitelesítő adatokat. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |Beállítja egy meglévő Automation-hitelesítő adat tulajdonságait. |

## <a name="other-cmdlets-used-to-access-credentials"></a>A hitelesítő adatok eléréséhez használt egyéb parancsmagok

Az alábbi táblázatban található parancsmagokkal férhet hozzá a runbookok és a DSC-konfigurációk hitelesítő adataihoz. 

| Parancsmag | Leírás |
|:--- |:--- |
| `Get-AutomationPSCredential` |Lekért `PSCredential` egy objektumot, amely runbookban vagy DSC-konfigurációban használható. Leggyakrabban ezt a belső [](modules.md#internal-cmdlets) parancsmagot érdemes használni a parancsmag helyett, mivel az utóbbi csak a hitelesítő adatokat `Get-AzAutomationCredential` olvassa be. Ezek az információk általában nem hasznosak, ha egy másik parancsmagnak ad át adatokat. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |Lekérdez egy hitelesítő adatokat, és kéri a felhasználónevet és a jelszót. Ez a parancsmag az alapértelmezett Microsoft.PowerShell.Security modul része. Lásd: [Alapértelmezett modulok.](modules.md#default-modules)|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | Létrehoz egy hitelesítő adateszközt. Ez a parancsmag az alapértelmezett Azure-modul része. Lásd: [Alapértelmezett modulok.](modules.md#default-modules)|

A `PSCredential` kódban lévő objektumok lekéréséhez importálni kell a `Orchestrator.AssetManagement.Cmdlets` modult. További információ: [Modulok kezelése a Azure Automation.](modules.md)

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> A paraméterében ne használja a `Name` `Get-AutomationPSCredential` változókat. A használatuk megnehezítheti a runbookok vagy a DSC-konfigurációk és a hitelesítőadat-eszközök közötti függőségek felderítését a tervezés során.

## <a name="python-functions-that-access-credentials"></a>A hitelesítő adatokhoz hozzáférő Python-függvények

Az alábbi táblázatban található függvény a Python 2-es és 3-as runbookban található hitelesítő adatok elérésére használható. A Python 3-runbookok jelenleg előzetes verzióban állnak rendelkezésre.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_credential` | Lekéri egy hitelesítő adateszköz adatait. |

> [!NOTE]
> Importálja a Python-runbook tetején található modult az `automationassets` adategység-függvények eléréséhez.

## <a name="create-a-new-credential-asset"></a>Új hitelesítőadat-eszköz létrehozása

Létrehozhat egy új hitelesítő adateszközt a Azure Portal vagy a Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Hozzon létre egy új hitelesítőeszközt a Azure Portal

1. Az Automation-fiókjában a bal oldali panelen válassza a **Credentials** (Hitelesítő adatok) lehetőséget a **Shared Resources (Megosztott erőforrások) alatt.**
2. A Hitelesítő **adatok lapon** válassza a Hitelesítő adatok **hozzáadása lehetőséget.**
3. Az Új hitelesítő adat panelen adjon meg egy megfelelő hitelesítőadat-nevet az elnevezési szabványoknak megfelelően.
4. Írja be a hozzáférési azonosítóját **a Felhasználónév mezőbe.**
5. Mindkét jelszómezőhöz adja meg a titkos hozzáférési kulcsot.

    ![Új hitelesítő adat létrehozása](../media/credentials/credential-create.png)

6. Ha a Többtényezős hitelesítés jelölőnégyzet be van jelölve, törölje a jelölést.
7. Az **új hitelesítő adateszköz** mentéshez kattintson a Létrehozás gombra.

> [!NOTE]
> Azure Automation nem támogatja a többtényezős hitelesítést használó felhasználói fiókokat.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Új hitelesítő adateszköz létrehozása a Windows PowerShell

Az alábbi példa bemutatja, hogyan hozhat létre új Automation hitelesítőadat-eszközt. A rendszer először létrehoz egy objektumot a névvel és a jelszóval, majd a használatával létrehozza `PSCredential` a hitelesítő objektumot. Ehelyett a parancsmag használatával egy név és jelszó begépelése `Get-Credential` kér a felhasználótól.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Hitelesítő adateszköz lekért adatai

Egy runbook vagy DSC-konfiguráció lekér egy hitelesítő eszközt a belső `Get-AutomationPSCredential` parancsmaggal. Ez a parancsmag lekért egy objektumot, amely egy hitelesítő adatokat igénylő `PSCredential` parancsmaggal használható. A hitelesítő objektum tulajdonságait egyenként is lekérheti. A objektum rendelkezik a felhasználónév és a biztonságos jelszó tulajdonságaival. 

> [!NOTE]
> A parancsmag nem a hitelesítéshez használható `Get-AzAutomationCredential` `PSCredential` objektumot olvassa be. Csak a hitelesítő adatokkal kapcsolatos információkat biztosít. Ha hitelesítő adatokat kell használnia egy runbookban, objektumként kell lekérnie a `PSCredential` `Get-AutomationPSCredential` használatával.

Másik lehetőségként a [GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) metódussal lekérhet egy [NetworkCredential](/dotnet/api/system.net.networkcredential) objektumot, amely a jelszó nem biztonságos verzióját jelöli.

### <a name="textual-runbook-example"></a>Példa szöveges runbookra

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi példa bemutatja, hogyan használhat PowerShell-hitelesítő adatokat egy runbookban. Lekéri a hitelesítő adatokat, és hozzárendeli a felhasználónevét és jelszavát a változókhoz.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Hitelesítő adatokat is használhat az Azure-beli hitelesítéshez a [Connect-AzAccount használatával.](/powershell/module/az.accounts/connect-azaccount) A legtöbb esetben érdemes egy run [as](../automation-security-overview.md#run-as-accounts) fiókot használni, és lekérni a kapcsolatot a [Get-AzAutomationConnection használatával.](../automation-connections.md)

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Az alábbi példa a hitelesítő adatok Python 2-runbookban való elérésére mutat példát.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

Az alábbi példa a hitelesítő adatok Python 3-runbookban (előzetes verzió) való elérésére mutat példát.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>Példa grafikus runbookra

A belső parancsmaghoz tevékenységeket adhat egy grafikus runbookhoz, ha a jobb gombbal a hitelesítő adatokra kattint a grafikus szerkesztő Könyvtár ablaktábláján, majd a Hozzáadás a vászonhoz `Get-AutomationPSCredential` **lehetőséget választva.**

![Hitelesítő parancsmag hozzáadása a vászonhoz](../media/credentials/credential-add-canvas.png)

Az alábbi képen egy példa látható a hitelesítő adatok grafikus runbookban való használatával. Ebben az esetben a hitelesítő adatok biztosítják a runbookok hitelesítését az Azure-erőforrásokhoz az Azure AD használata a Azure Automation azure-beli [hitelesítéshez témakörben leírtak szerint.](../automation-use-azure-ad.md) Az első tevékenység lekéri az Azure-előfizetéshez hozzáférő hitelesítő adatokat. A fiókkapcsolati tevékenység ezután ezt a hitelesítő adatokat használja az azt követően következő tevékenységek hitelesítéséhez. Itt [folyamathivatkozást](../automation-graphical-authoring-intro.md#use-links-for-workflow) használunk, `Get-AutomationPSCredential` mivel egyetlen objektumot vár.  

![Példa hitelesítőadat-munkafolyamat folyamathivatkozással](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Hitelesítő adatok használata DSC-konfigurációban

Míg a DSC-konfigurációk Azure Automation a használatával működnek a hitelesítő adateszközökkel, paraméterekkel is `Get-AutomationPSCredential` átadhatnak hitelesítő adateszközöket. További információ: [Konfigurációk fordítása a DSC Azure Automation ban.](../automation-dsc-compile.md#credential-assets)

## <a name="next-steps"></a>Következő lépések

* A tanúsítványok eléréséhez használt parancsmagokkal kapcsolatos további információkért lásd: Modulok kezelése a [Azure Automation.](modules.md)
* A runbookokkal kapcsolatos általános információkért lásd: [Runbook végrehajtása](../automation-runbook-execution.md)a Azure Automation.
* A DSC-konfigurációk részleteiért lásd a Azure Automation State Configuration [áttekintését.](../automation-dsc-overview.md)
