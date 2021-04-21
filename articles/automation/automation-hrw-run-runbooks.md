---
title: Runbookok Azure Automation hibrid runbook-feldolgozón
description: Ez a cikk azt ismerteti, hogyan futtathat runbookokat a helyi adatközpontban vagy más felhőszolgáltatón futó gépeken a hibrid runbook-feldolgozóval.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6501fd26a5c7966c4cde596df40346b106c57cce
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834785"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Runbookok futtatása hibrid runbook-feldolgozón

A hibrid runbook-feldolgozón futó [runbookok](automation-hybrid-runbook-worker.md) általában a helyi számítógépen vagy a feldolgozót üzembe helyező helyi környezet erőforrásain kezelik az erőforrásokat. A runbookok Azure Automation általában az Azure-felhőben kezelik az erőforrásokat. Annak ellenére, hogy másképp használják őket, a Azure Automation környezetben futó runbookok és a hibrid runbook-feldolgozón futó runbookok struktúrája azonos.

Amikor egy hibrid runbook-feldolgozón futtatott runbookot hoz meg, szerkesztenie és tesztelni kell a runbookot a feldolgozót tartalmazó gépen. A gazdagép rendelkezik a helyi erőforrások kezeléséhez szükséges összes PowerShell-modullal és hálózati hozzáféréssel. Miután teszteli a runbookot a hibrid runbook-feldolgozó gépen, feltöltheti azt a Azure Automation-környezetbe, ahol futtatható a feldolgozón.

## <a name="plan-runbook-job-behavior"></a>Runbook-feladat viselkedésének megterve

Azure Automation a hibrid runbook-dolgozókon futó feladatokat másképp kezeli, mint az Azure-védőfalon futó feladatokat. Ha hosszú ideig futó runbookja van, győződjön meg arról, hogy az rugalmas az újraindításhoz. A feladat viselkedésével kapcsolatos részletekért lásd: [Hibrid runbook-feldolgozó feladatok.](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs)

A hibrid runbook-dolgozókhoz  a feladatok a windowsos helyi rendszerfiókban vagy Linuxon az **nxautomation fiókban** futnak. Linux rendszeren ellenőrizze, hogy **az nxautomation-fiók** rendelkezik-e hozzáféréssel ahhoz a helyhez, ahol a Runbook-modulok tárolva vannak. Az [Install-Module](/powershell/module/powershellget/install-module) parancsmag használata esetén ügyeljen arra, hogy az AllUsers paramétert adja meg annak biztosításához, hogy `Scope` **az nxautomation** fiók hozzáféréssel rendelkezik. További információ a Linuxon elérhető PowerShellről: A PowerShell ismert problémái [nem Windows-platformokon.](/powershell/scripting/whats-new/what-s-new-in-powershell-70)

## <a name="configure-runbook-permissions"></a>Runbook-engedélyek konfigurálása

A hibrid runbook-feldolgozón való futtatás engedélyeinek meghatározása a runbookhoz a következő módokon:

* A runbooknak saját hitelesítést kell adnia a helyi erőforrásokhoz.
* Konfigurálja az [Azure-erőforrások felügyelt identitásokkal való hitelesítését.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
* Adjon meg egy olyan fiókot, amely felhasználói környezetet biztosít az összes runbook számára.

### <a name="use-runbook-authentication-to-local-resources"></a>Runbook-hitelesítés használata helyi erőforrásokhoz

Ha olyan runbookot készít elő, amely [](./shared-resources/credentials.md) saját [](./shared-resources/certificates.md) hitelesítést biztosít az erőforrásokhoz, használjon hitelesítő adatokat és tanúsítványeszközöket a runbookban. Több parancsmag is rendelkezésre áll, amelyek lehetővé teszik hitelesítő adatok megadását, hogy a runbook különböző erőforrásokhoz hitelesíthető legyen. Az alábbi példa egy számítógépet újraindító runbook egy részét mutatja be. Lekéri a hitelesítő adatokat egy hitelesítő adateszközből és a számítógép nevéből egy változóeszközből, majd ezeket az értékeket használja a `Restart-Computer` parancsmaggal.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

[InlineScript-tevékenységet is](automation-powershell-workflow.md#use-inlinescript) használhat. `InlineScript` A lehetővé teszi kódblokkok futtatását egy másik számítógépen hitelesítő adatokkal.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Runbook-hitelesítés használata felügyelt identitásokkal

Az Azure-beli virtuális gépeken futó hibrid runbook-dolgozók felügyelt identitásokat használhatnak az Azure-erőforrásokban való hitelesítéshez. Az Azure-erőforrások felügyelt identitásának használata a futó fiókok helyett előnyt biztosít, mert nem kell:

* Exportálja a runbook-feldolgozót, majd importálja a hibrid runbook-feldolgozóba.
* Újítsa meg a futó fiók által használt tanúsítványt.
* Kezelje a runbook kódban a runbook kapcsolati objektumát.

A következő lépésekkel felügyelt identitást használhat egy hibrid runbook-feldolgozón található Azure-erőforrásokhoz:

1. Azure-beli virtuális gép létrehozása.
1. A virtuális gépen található Azure-erőforrások felügyelt identitásának konfigurálása. Lásd: [Azure-erőforrások felügyelt identitásának](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)konfigurálása virtuális gépen a Azure Portal.
1. Adjon hozzáférést a virtuális gépnek egy erőforráscsoporthoz a Resource Manager. Lásd: [Hozzáférés a Windows VM-beli, rendszer által](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)hozzárendelt felügyelt identitással a Resource Manager.
1. Telepítse a hibrid runbook-feldolgozót a virtuális gépre. Lásd: [Windows rendszerű hibrid runbook-feldolgozó üzembe helyezése vagy](automation-windows-hrw-install.md) Linux rendszerű hibrid [runbook-feldolgozó üzembe helyezése.](automation-linux-hrw-install.md)
1. Frissítse a runbookot úgy, hogy a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmagot használja az paraméterrel az `Identity` Azure-erőforrásokon való hitelesítéshez. Ez a konfiguráció csökkenti a futtatási fiók használatának és a társított fiókkezelés elvégzésének szükségét.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` A egy hibrid runbook-feldolgozóval működik, amely rendszer által hozzárendelt identitást és egyetlen felhasználó által hozzárendelt identitást használ. Ha több felhasználó által hozzárendelt identitást használ a hibrid runbook-feldolgozón, a runbooknak meg kell adnia a paraméterét egy adott felhasználó által hozzárendelt `AccountId` `Connect-AzAccount` identitás kiválasztásához.

### <a name="use-runbook-authentication-with-run-as-account"></a>Runbook-hitelesítés használata a run as-fiókkal

Ahelyett, hogy a runbook saját hitelesítést biztosítson a helyi erőforrásokhoz, megadhat egy runbook-feldolgozócsoporthoz egy runbook-feldolgozó csoportot. A futó fiók megadásához meg kell határoznia egy hitelesítő [adateszközt,](./shared-resources/credentials.md) amely hozzáféréssel rendelkezik a helyi erőforrásokhoz. Ezek az erőforrások tanúsítványtárolókat tartalmaznak, és az összes runbook ezen hitelesítő adatok alatt fut a csoport hibrid runbook-feldolgozóján.

- A hitelesítő adatok felhasználónevének az alábbi formátumok egyikében kell lennie:

   * Tartomány\felhasználónév
   * username@domain
   * username (helyi fiókok esetén a helyszíni számítógépen)

- Az **Export-RunAsCertificateToHybridWorker PowerShell-runbookhoz** telepítenie kell az Az modulokat a Azure Automation gépre.

#### <a name="use-a-credential-asset-to-specify-a-run-as-account"></a>Hitelesítő adateszköz használata a futó fiók megadásához

A hibrid runbook-feldolgozó csoporthoz az alábbi eljárással adhatja meg a futtatáshoz használt fiókot:

1. Hozzon [létre egy hitelesítő adateszközt,](./shared-resources/credentials.md) amely hozzáféréssel rendelkezik a helyi erőforrásokhoz.
1. Nyissa meg az Automation-fiókot a Azure Portal.
1. Válassza **a Hibrid feldolgozói csoportok lehetőséget,** majd válassza ki az adott csoportot.
1. Válassza **a Minden beállítás,** majd a **Hibrid feldolgozócsoport beállításai lehetőséget.**
1. Módosítsa a Run As (Futtatás **mint) értékét** Default **(Alapértelmezett) értékről** **Custom (Egyéni) értékre.**
1. Válassza ki a hitelesítő adatokat, majd kattintson a **Mentés gombra.**

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>A futó fiók tanúsítványának telepítése

Az azure-beli erőforrások üzembe helyezésének automatizált buildfolyamata részeként szükség lehet a helyszíni rendszerekhez való hozzáférésre az üzembehelyezés-sorozat egy feladatának vagy lépéseinek támogatásához. Az Azure-beli, a futtatásként használt fiókkal való hitelesítéshez telepítenie kell a run as account tanúsítványt.

>[!NOTE]
>Ez a PowerShell-runbook jelenleg nem fut Linux rendszerű gépeken. Csak Windows rendszerű gépeken fut.
>

A következő, **Export-RunAsCertificateToHybridWorker nevű PowerShell-runbook** exportálja a Azure Automation tanúsítványt. A runbook letölti és importálja a tanúsítványt a helyi számítógép tanúsítványtárolójában egy hibrid runbook-feldolgozón, amely ugyanhez a fiókhoz csatlakozik. A lépés befejezése után a runbook ellenőrzi, hogy a feldolgozó sikeresen tud-e hitelesítést végezni az Azure-ban a futtatott fiókkal.

>[!NOTE]
>Ez a PowerShell-runbook nem az Automation-fiókon kívül, a célgépen szkriptként való futtatására szolgál.
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
>A PowerShell-runbookok és `Add-AzAccount` `Add-AzureRMAccount` a a `Connect-AzAccount` aliasai. A kódtár elemeinek keresésekor, ha nem látja a elemet, használhatja a elemet, vagy frissítheti a modulokat az `Connect-AzAccount` `Add-AzAccount` Automation-fiókjában.

A futó fiók előkészítésének befejezése:

1. Mentse az **Export-RunAsCertificateToHybridWorker** runbookot a számítógépre **.ps1 kiterjesztéssel.**
1. Importálja az Automation-fiókjába.
1. Szerkessze a runbookot, és módosítsa a változó értékét `Password` a saját jelszavára.
1. Tegye közzé a runbookot.
1. Futtassa a runbookot, és célozza meg azt a hibrid runbook-feldolgozó csoportot, amely futtatja és hitelesíti a runbookokat a futtatott fiókkal. 
1. Vizsgálja meg a feladatstreamet, és ellenőrizze, hogy jelentéseket készít-e a tanúsítvány helyi géptárolóba való importálásának kísérletről, amelyet több sor követ. Ez a viselkedés attól függ, hogy hány Automation-fiókot határoz meg az előfizetésben, és hogy a hitelesítés mennyire sikeres.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Aláírt runbookok használata windowsos hibrid runbook-feldolgozón

A Windows rendszerű hibrid runbook-feldolgozókat konfigurálhatja úgy, hogy csak aláírt runbookokat futtassanak.

> [!IMPORTANT]
> Miután konfigurált egy hibrid runbook-feldolgozót, hogy csak aláírt runbookokat futtason, az aláíratlan runbookok végrehajtása nem sikerül a feldolgozón.

### <a name="create-signing-certificate"></a>Aláíró tanúsítvány létrehozása

Az alábbi példa egy önaláírt tanúsítványt hoz létre, amely runbookok aláírására használható. Ez a kód létrehozza és exportálja a tanúsítványt, hogy a hibrid runbook-feldolgozó később importálni tudja. Az ujjlenyomatot később a tanúsítványra való hivatkozáshoz is visszaadja.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Tanúsítvány importálása és a dolgozók konfigurálása aláírás-ellenőrzéshez

Másolja a létrehozott tanúsítványt egy csoport minden hibrid runbook-feldolgozójébe. Az alábbi szkript futtatásával importálja a tanúsítványt, és konfigurálja a dolgozókat, hogy aláírás-ellenőrzést használjanak a runbookok esetében.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Runbookok aláírása a tanúsítvánnyal

Ha a hibrid runbook-feldolgozók csak aláírt runbookok használatára vannak konfigurálva, olyan runbookokat kell aláírnia, amelyek a hibrid runbook-feldolgozón használhatók. A runbookok aláírásához használja a következő PowerShell-mintakódot.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Ha egy runbook alá lett írva, importálni kell az Automation-fiókjába, és közzé kell tenni az aláírási blokkkal. A runbookok importálásának elsajátításért [lásd: Runbook importálása.](manage-runbooks.md#import-a-runbook)

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Aláírt runbookok használata Linux rendszerű hibrid runbook-feldolgozón

Az aláírt runbookok csak akkor használhatók, ha egy Linux rendszerű hibrid runbook-feldolgozónak a helyi gépen futtatható [GPG-t](https://gnupg.org/index.html) kell használnia.

> [!IMPORTANT]
> Miután konfigurált egy hibrid runbook-feldolgozót, hogy csak aláírt runbookokat futtason, az aláíratlan runbookok végrehajtása nem sikerül a feldolgozón.

A konfiguráció végrehajtásához a következő lépéseket kell végrehajtania:

* GPG-kulcstartó és -kulcspair létrehozása
* Tegye elérhetővé a kulcstartót a hibrid runbook-feldolgozó számára
* Annak ellenőrzése, hogy az aláírás érvényesítése be van-e va
* Runbook aláírása

### <a name="create-a-gpg-keyring-and-keypair"></a>GPG-kulcstartó és -kulcspair létrehozása

A GPG-kulcstartó és a kulcspair létrehozásához használja a hibrid runbook-feldolgozó [nxautomation fiókját.](automation-runbook-execution.md#log-analytics-agent-for-linux)

1. A sudo alkalmazással jelentkezzen be **nxautomation-fiókként.**

    ```bash
    sudo su - nxautomation
    ```

1. Az **nxautomation** használata után hozza létre a GPG-kulcstartót. A GPG végigvezeti a lépéseken. Meg kell adnia a nevet, az e-mail-címet, a lejárati időt és a jelszót. Ezután várjon, amíg a kulcs előállításához elegendő energia áll a gépen.

    ```bash
    sudo gpg --generate-key
    ```

1. Mivel a GPG-könyvtár a sudo használatával lett létrehozva, a tulajdonosát **nxautomation-ra** kell módosítania a következő paranccsal.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Tegye elérhetővé a kulcstartót a hibrid runbook-feldolgozó számára

A kulcstartó létrehozása után tegye elérhetővé a hibrid runbook-feldolgozó számára. Módosítsa a **home/nxautomation/state/worker.conf** beállításfájlt úgy, hogy az alábbi példakódot tartalmazza a `[worker-optional]` fájlszakaszban.

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Annak ellenőrzése, hogy az aláírás érvényesítése be van-e va

Ha az aláírás érvényesítése le van tiltva a gépen, az alábbi sudo parancs futtatásával kell bekapcsolni. Cserélje le `<LogAnalyticsworkspaceId>` a helyére a munkaterület azonosítóját.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Runbook aláírása

Az aláírás-ellenőrzés konfigurálása után a következő GPG-paranccsal írja alá a runbookot.

```bash
gpg --clear-sign <runbook name>
```

Az aláírt runbook neve **<runbook name> .asc.**

Most már feltöltheti az aláírt runbookot a Azure Automation, és normál runbookként futtathatja.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Runbook futtatása hibrid runbook-feldolgozón

[Runbook indítása a](start-runbooks.md) Azure Automation runbookok indításának különböző módszereit ismerteti. Egy runbook hibrid runbook-feldolgozón való indítása a Run **on** (Futtatás) lehetőséget használja, amely lehetővé teszi egy hibrid runbook-feldolgozó csoport nevének megadását. Ha meg van adva egy csoport, a csoport egyik dolgozója lekéri és futtatja a runbookot. Ha a runbook nem adja meg ezt a beállítást, Azure Automation a szokásos módon futtatja a runbookot.

Amikor elindít egy runbookot a Azure Portal, használhatja a Futtatás **lehetőséget,** amelyhez kiválaszthatja az **Azure-t** vagy a **hibrid feldolgozót.** Ha a Hibrid feldolgozó lehetőséget **választja,** a legördülő menüből kiválaszthatja a Hibrid runbook-feldolgozó csoportot.

Amikor a PowerShell használatával indít el egy runbookot, használja a paramétert a `RunOn` [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) parancsmaggal. Az alábbi példa a Windows PowerShell egy **Test-Runbook nevű runbookot** indít el egy MyHybridGroup nevű hibrid runbook-feldolgozó csoporton.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Naplózás

A hibrid runbook-feldolgozón futó runbookok hibáinak elhárítása érdekében a naplók helyileg, a következő helyen vannak tárolva:

* Windows rendszeren a `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` feladat futásidejű folyamatának részletes naplózásához. A runbook magas szintű feladatállapot-eseményei az alkalmazás- és **szolgáltatásnaplók\Microsoft-Automation\Operations eseménynaplóba** vannak írva.

* Linux rendszeren a felhasználó hibrid feldolgozói naplói a következő oldalon találhatók: , a rendszer runbook-feldolgozói naplói pedig itt `/home/nxautomation/run/worker.log` találhatók: `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>Következő lépések

* Ha a runbookok nem teljesülnek sikeresen, tekintse át a runbookvégrehajtási hibákra vonatkozó [hibaelhárítási útmutatót.](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)
* További információ a PowerShellről, beleértve a nyelvi referenciákat és a tanulási modulokat: [PowerShell Docs](/powershell/scripting/overview).
* A PowerShell-parancsmagok referenciáját lásd: [Az.Automation](/powershell/module/az.automation).
