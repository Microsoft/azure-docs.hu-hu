---
title: Azure Automation runbookok futtatása hibrid Runbook-feldolgozón
description: Ez a cikk azt ismerteti, hogyan futtathatók a runbookok a helyi adatközpontban vagy más Felhőbeli szolgáltatónál a hibrid Runbook-feldolgozóval.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: 6d1f504458aed440464015a34479d75992fe5c45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149375"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Runbookok futtatása hibrid runbook-feldolgozón

A [hibrid Runbook-feldolgozón](automation-hybrid-runbook-worker.md) futó runbookok általában a helyi számítógépen vagy a munkavégző üzembe helyezett helyi környezetben lévő erőforrásokkal kezelik az erőforrásokat. A Azure Automation runbookok általában az Azure-felhőben lévő erőforrásokat kezelik. Annak ellenére, hogy eltérő módon használják őket, a runbookok, amely egy hibrid Runbook-feldolgozón futó Azure Automation és runbookok fut, azonos a struktúrában.

Ha hibrid Runbook-feldolgozón futtat egy runbook, akkor a runbook a munkavégzőt futtató gépen kell szerkesztenie és tesztelni. A gazdagépen található összes PowerShell-modul és hálózati hozzáférés szükséges a helyi erőforrások kezeléséhez. Ha teszteli a runbook a hibrid Runbook Worker gépen, feltöltheti azt a Azure Automation-környezetbe, ahol futtatható a feldolgozón.

## <a name="plan-runbook-job-behavior"></a>Runbook-feladatok működésének megtervezése

A Azure Automation a feladatokat a hibrid Runbook-feldolgozókon eltérően kezeli az Azure-beli munkaterületeken futó feladatoktől. Ha hosszan futó runbook rendelkezik, győződjön meg arról, hogy az esetleges újraindításra rugalmas. A feladat működésével kapcsolatos részletekért lásd: [hibrid Runbook Worker Jobs](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

A hibrid Runbook-feldolgozók feladatai a helyi rendszerfiók alatt futnak **Windows rendszeren vagy** a Linuxon futó **nxautomation** -fiókban. Linux esetében ellenőrizze, hogy a **nxautomation** -fiók hozzáfér-e ahhoz a helyhez, ahol a runbook-modulok vannak tárolva. Ha az [install-Module](/powershell/module/powershellget/install-module) parancsmagot használja, ügyeljen arra, hogy a paraméter AllUsers megadásával `Scope` biztosítsa, hogy a **nxautomation** -fiók hozzáférhessen. A Linux PowerShell-lel kapcsolatos további információkért lásd: [ismert problémák a PowerShell számára a nem Windows platformokon](/powershell/scripting/whats-new/what-s-new-in-powershell-70).

## <a name="configure-runbook-permissions"></a>Runbook engedélyeinek konfigurálása

Adja meg a runbook a hibrid Runbook-feldolgozón való futtatásához szükséges engedélyeket a következő módokon:

* Adja meg a runbook a saját hitelesítését a helyi erőforrásokhoz.
* A hitelesítés konfigurálása [Az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)használatával.
* Adjon meg egy futtató fiókot, amely minden runbookok felhasználói környezetet biztosít.

### <a name="use-runbook-authentication-to-local-resources"></a>Runbook-hitelesítés használata helyi erőforrásokhoz

Ha olyan runbook készít elő, amely saját hitelesítést biztosít az erőforrásokhoz, használja a runbook [hitelesítő adatait](./shared-resources/credentials.md) és [tanúsítványait](./shared-resources/certificates.md) . Több parancsmag is rendelkezésre áll, amelyek lehetővé teszik a hitelesítő adatok megadását, hogy a runbook különböző erőforrásokhoz lehessen hitelesíteni. Az alábbi példa egy runbook egy részét mutatja be, amely újraindítja a számítógépet. Hitelesítő adatokat kér le a hitelesítő adatokból és a számítógép nevét egy változó objektumból, majd ezeket az értékeket használja a `Restart-Computer` parancsmaggal.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

[InlineScript](automation-powershell-workflow.md#use-inlinescript) -tevékenységet is használhat. `InlineScript` lehetővé teszi a kódok blokkjának futtatását egy másik számítógépen, hitelesítő adatokkal.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Runbook-hitelesítés használata felügyelt identitásokkal

Az Azure-beli virtuális gépeken futó hibrid Runbook-feldolgozók felügyelt identitásokat használhatnak az Azure-erőforrásokhoz való hitelesítéshez. Az Azure-erőforrások felügyelt identitások használata a futtató fiókok helyett a következő előnyöket nyújtja:

* Exportálja a futtató tanúsítványt, majd importálja a hibrid Runbook-feldolgozóba.
* Újítsa meg a futtató fiók által használt tanúsítványt.
* Kezeli a futtató kapcsolatok objektumát a runbook-kódban.

A következő lépésekkel felügyelt identitást használhat egy hibrid Runbook-feldolgozón az Azure-erőforrásokhoz:

1. Hozzon létre egy Azure-beli virtuális gépet.
1. Felügyelt identitások konfigurálása az Azure-erőforrásokhoz a virtuális gépen. Lásd: [felügyelt identitások konfigurálása egy virtuális gépen az Azure-erőforrásokhoz a Azure Portal használatával](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
1. Adjon hozzáférést a virtuális géphez egy erőforráscsoporthoz a Resource Managerben. A [Resource Manager eléréséhez tekintse meg a Windows rendszerű virtuális gépekhez rendelt felügyelt identitás használatát](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)ismertető témakört.
1. Telepítse a hibrid Runbook-feldolgozót a virtuális gépre. Lásd: [Windows Hybrid Runbook Worker telepítése](automation-windows-hrw-install.md) vagy [Linux Hybrid Runbook Worker üzembe helyezése](automation-linux-hrw-install.md).
1. Frissítse a runbook, [hogy a AzAccount parancsmagot](/powershell/module/az.accounts/connect-azaccount) használja a `Identity` paraméterrel az Azure-erőforrásokban való hitelesítéshez. Ez a konfiguráció csökkenti a futtató fiók használatának és a társított Fiókkezelés elvégzésének szükségességét.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` a hibrid Runbook-feldolgozók számára a rendszer által hozzárendelt identitást és egyetlen felhasználó által hozzárendelt identitást használ. Ha több felhasználó által hozzárendelt identitást használ a hibrid Runbook-feldolgozón, a Runbook meg kell adnia a `AccountId` paramétert `Connect-AzAccount` egy adott felhasználó által hozzárendelt identitás kiválasztásához.

### <a name="use-runbook-authentication-with-run-as-account"></a>Runbook-hitelesítés használata futtató fiókkal

Ahelyett, hogy a runbook saját hitelesítéssel lássa el a helyi erőforrásokat, megadhat egy futtató fiókot egy hibrid Runbook-feldolgozói csoport számára. Futtató fiók megadásához meg kell adnia egy olyan [hitelesítőadat-eszközt](./shared-resources/credentials.md) , amely hozzáfér a helyi erőforrásokhoz. Ezek az erőforrások közé tartoznak a tanúsítványtárolók, és minden runbookok ezen hitelesítő adatok alatt fut a csoport egy hibrid Runbook-feldolgozóján.

- A hitelesítő adatok felhasználónevének a következő formátumok egyikében kell lennie:

   * tartomány \ Felhasználónév
   * username@domain
   * username (a helyszíni számítógép helyi számítógépén lévő fiókok esetében)

- Az **export-RunAsCertificateToHybridWorker PowerShell-** runbook használatához telepítenie kell az az modulokat a helyi gépre Azure Automation.

#### <a name="use-a-credential-asset-to-specify-a-run-as-account"></a>Hitelesítő eszköz használata futtató fiók megadásához

A következő eljárással adhatja meg a futtató fiókot egy hibrid Runbook Worker csoport számára:

1. Hozzon létre egy [hitelesítő eszközt](./shared-resources/credentials.md) a helyi erőforrásokhoz való hozzáféréssel.
1. Nyissa meg az Automation-fiókot a Azure Portal.
1. Válassza a **hibrid feldolgozói csoportok** lehetőséget, majd válassza ki az adott csoportot.
1. Válassza a **minden beállítás** elemet, majd a **hibrid feldolgozói csoport beállításait**.
1. Módosítsa a **Futtatás** **alapértelmezett** értékét az **Egyéni** értékre.
1. Válassza ki a hitelesítő adatokat, majd kattintson a **Mentés** gombra.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Futtató fiók tanúsítványának telepítése

Az erőforrások Azure-ban való üzembe helyezéséhez szükséges automatizált felépítési folyamat részeként szükség lehet a helyszíni rendszerekhez való hozzáférésre, hogy támogassa a feladatait vagy lépéseit a telepítési sorozatban. Ha a futtató fiók használatával szeretne hitelesítést biztosítani az Azure-ban, telepítenie kell a futtató fiók tanúsítványát.

>[!NOTE]
>Ez a PowerShell-runbook jelenleg nem Linux rendszerű gépeken fut. Csak Windows rendszerű gépeken fut.
>

A következő PowerShell-runbook, az **export-RunAsCertificateToHybridWorker néven exportálja** a futtató tanúsítványt a Azure Automation-fiókjából. A runbook letölti és importálja a tanúsítványt a helyi számítógép tanúsítványtárolójában egy olyan hibrid Runbook-feldolgozón, amely ugyanahhoz a fiókhoz csatlakozik. Miután befejezte ezt a lépést, a runbook ellenőrzi, hogy a feldolgozó sikeresen tud-e hitelesíteni az Azure-ban a futtató fiók használatával.

>[!NOTE]
>Ez a PowerShell-runbook nem úgy van kialakítva, hogy az Automation-fiókon kívül fusson a célszámítógépen.
>

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>A PowerShell-runbookok esetében a `Add-AzAccount` és a `Add-AzureRMAccount` álneve a következőhöz: `Connect-AzAccount` . Ha nem látja a könyvtár elemeit, `Connect-AzAccount` használhatja a `Add-AzAccount` vagy az Automation-fiókjában található modulokat is.

A futtató fiók előkészítésének befejezése:

1. Mentse az **export-RunAsCertificateToHybridWorker** runbook a számítógépre **. ps1** kiterjesztéssel.
1. Importálja az Automation-fiókjába.
1. Szerkessze a runbook, és módosítsa a változó értékét a `Password` saját jelszavára.
1. Tegye közzé a runbook.
1. Futtassa a runbook, amely a futtató fiók használatával futtatja és hitelesíti a runbookok a hibrid Runbook-munkavégző csoportot. 
1. Vizsgálja meg a folyamat adatfolyamát, és figyelje meg, hogy a tanúsítvány importálására tett kísérlet a helyi számítógép tárolójába, majd több sor után. Ez a viselkedés attól függ, hogy hány Automation-fiókot határoz meg az előfizetésében, valamint a hitelesítés sikerességének fokát.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Aláírt runbookok használata Windows hibrid Runbook-feldolgozón

Egy Windows Hybrid Runbook Worker-feldolgozót úgy konfigurálhat, hogy csak az aláírt runbookok futtassa.

> [!IMPORTANT]
> Miután konfigurálta a hibrid Runbook-feldolgozót, hogy csak az aláírt runbookok futtassa, az aláíratlan runbookok nem hajtható végre a feldolgozón.

### <a name="create-signing-certificate"></a>Aláíró tanúsítvány létrehozása

A következő példa létrehoz egy önaláírt tanúsítványt, amely a runbookok aláírására használható. Ez a kód létrehozza a tanúsítványt, és exportálja azt, hogy a hibrid Runbook-feldolgozó később is importálja. Az ujjlenyomatot is visszaadja a rendszer a tanúsítványra hivatkozó későbbi használatra.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
    -Subject "CN=contoso.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
    -KeyExportPolicy Exportable `
    -KeyUsage DigitalSignature `
    -Type CodeSigningCert

# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Tanúsítvány importálása és a feldolgozók konfigurálása aláírás-ellenőrzéshez

Másolja a létrehozott tanúsítványt egy csoport minden hibrid Runbook-feldolgozója számára. Futtassa a következő szkriptet a tanúsítvány importálásához és a feldolgozók konfigurálásához az aláírás-érvényesítés használatára a runbookok-on.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Runbookok aláírása a tanúsítvány használatával

Ha a hibrid Runbook-feldolgozók csak aláírt runbookok használatára vannak konfigurálva, a hibrid Runbook-feldolgozón használni kívánt runbookok alá kell írniuk. A következő PowerShell-kód használatával írja alá ezeket a runbookok.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Egy runbook aláírása után importálnia kell az Automation-fiókjába, és közzé kell tennie az aláírási blokkban. A runbookok importálásával kapcsolatos további információkért lásd: [Runbook importálása](manage-runbooks.md#import-a-runbook).

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Aláírt runbookok használata linuxos hibrid Runbook-feldolgozón

Ahhoz, hogy az aláírt runbookok működjön, egy linuxos hibrid Runbook-feldolgozónak rendelkeznie kell a [GPG](https://gnupg.org/index.html) végrehajtható fájllal a helyi gépen.

> [!IMPORTANT]
> Miután konfigurálta a hibrid Runbook-feldolgozót, hogy csak az aláírt runbookok futtassa, az aláíratlan runbookok nem hajtható végre a feldolgozón.

A konfiguráció végrehajtásához a következő lépéseket kell végrehajtania:

* GPG-kulcstartó és-kulcspár létrehozása
* A kulcstartó elérhetővé tétele a hibrid Runbook Worker számára
* Ellenőrizze, hogy be van-e kapcsolva az aláírás ellenőrzése
* Runbook aláírása

### <a name="create-a-gpg-keyring-and-keypair"></a>GPG-kulcstartó és-kulcspár létrehozása

A GPG kulcstartó és a kulcspár létrehozásához használja a hibrid Runbook Worker [nxautomation-fiókot](automation-runbook-execution.md#log-analytics-agent-for-linux).

1. A sudo alkalmazás használatával jelentkezzen be a **nxautomation** -fiókkal.

    ```bash
    sudo su – nxautomation
    ```

1. A **nxautomation** használata után létrehozza a GPG-kulcspárt. A GPG végigvezeti Önt a lépéseken. Meg kell adnia a nevet, az e-mail-címet, a lejárati időt és a hozzáférési kódot. Ezután várjon, amíg nincs elég entrópia a gépen a kulcs generálásához.

    ```bash
    sudo gpg --generate-key
    ```

1. Mivel a GPG-könyvtár a sudo-val lett létrehozva, a következő parancs használatával módosítania kell a tulajdonosát a **nxautomation** .

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>A kulcstartó elérhetővé tétele a hibrid Runbook Worker számára

Miután létrehozta a kulcstartót, tegye elérhetővé a hibrid Runbook Worker számára. Módosítsa a **Home/nxautomation/State/Worker. conf** Settings fájlt, hogy a fájl szakaszban szerepeljen a következő mintakód `[worker-optional]` .

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Ellenőrizze, hogy be van-e kapcsolva az aláírás ellenőrzése

Ha az aláírás-ellenőrzés le van tiltva a gépen, azt be kell kapcsolni a következő sudo parancs futtatásával. Cserélje le a helyére a `<LogAnalyticsworkspaceId>` munkaterület-azonosítót.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Runbook aláírása

Miután konfigurálta az aláírás-ellenőrzést, a következő GPG-paranccsal írja alá a runbook.

```bash
gpg –-clear-sign <runbook name>
```

Az aláírt runbook neve **<runbook name> . ASC**.

Most már feltöltheti az aláírt runbook Azure Automation, és végrehajthatja, mint egy normál runbook.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Runbook elindítása hibrid Runbook-feldolgozón

[Runbook elindítása Azure Automation](start-runbooks.md) ismerteti a runbook indításának különböző módszereit. A hibrid Runbook-feldolgozók runbook indítása olyan **futtatási** lehetőséget használ, amely lehetővé teszi a hibrid runbook-feldolgozói csoport nevének megadását. Ha meg van adva egy csoport, a csoport egyik feldolgozója kéri le és futtatja a runbook. Ha a runbook nem adja meg ezt a beállítást, Azure Automation a szokásos módon futtatja a runbook.

Amikor elindít egy runbook a Azure Portalban, megjelenik a **Futtatás** lehetőség, amelyen kiválaszthatja az **Azure** -t vagy a **hibrid feldolgozót**. Ha a **hibrid feldolgozót** választja, kiválaszthatja a hibrid Runbook Worker csoportot egy legördülő listából.

Amikor a PowerShell használatával indítja el a runbook, használja a `RunOn` paramétert a [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) parancsmaggal. Az alábbi példa a Windows PowerShell használatával indítja el a **test-runbook** nevű Runbook egy MyHybridGroup nevű hibrid runbook Worker csoporton.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Naplózás

A hibrid runbook-feldolgozón futó runbookok kapcsolatos problémák elhárítása érdekében a naplókat a következő helyen tárolja helyileg:

* Windows rendszeren a `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` következő helyen: a feladatok futtatásának részletes naplózása. A magas szintű runbook feladatok állapotára vonatkozó események az **alkalmazás és a szolgáltatások Logs\Microsoft-Automation\Operations-** eseménynaplóba íródnak.

* Linux rendszeren a felhasználó hibrid feldolgozói naplókat talál a következő helyen: `/home/nxautomation/run/worker.log` , és a System runbook Worker naplói a következő címen találhatók: `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>Következő lépések

* Ha a runbookok nem fejeződik be sikeresen, tekintse át a [runbook-végrehajtási hibák](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)hibaelhárítási útmutatóját.
* További információ a PowerShellről, beleértve a nyelvi referenciákat és a tanulási modulokat is: [PowerShell-dokumentumok](/powershell/scripting/overview).
* A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
