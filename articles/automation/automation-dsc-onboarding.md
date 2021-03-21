---
title: Azure Automation állapot konfigurációjának engedélyezése
description: Ez a cikk azt ismerteti, hogyan állíthat be gépeket Azure Automation állapot-konfigurációval való felügyelethez.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c0dc68bd7dacf0cd7f4be9732d45831e2dbb712c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897003"
---
# <a name="enable-azure-automation-state-configuration"></a>Azure Automation állapot konfigurációjának engedélyezése

Ez a témakör azt ismerteti, hogyan állíthatja be a gépeket Azure Automation állapot-konfigurációval való felügyeletre. A szolgáltatás részleteiért lásd: [Azure Automation állapot-konfiguráció áttekintése](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Azure-beli virtuális gépek engedélyezése

Azure Automation állapot-konfigurációval egyszerűen engedélyezheti az Azure-beli virtuális gépeket a konfiguráció felügyeletéhez a Azure Portal, a Azure Resource Manager sablonok vagy a PowerShell használatával. A motorháztető alatt, és anélkül, hogy a rendszergazdának távoli GÉPEN kellene lennie, az Azure-beli virtuális gép kívánt állapota konfigurációs bővítmény regisztrálja Azure Automation állapot konfigurációjában a virtuális gépet. Mivel az Azure-bővítmény aszinkron módon fut, az előrehaladásának nyomon követéséhez szükséges lépések a [virtuális gép beállításának állapotának ellenőrzéséhez](#check-status-of-vm-setup)nyújtanak útmutatást.

> [!NOTE]
>A DSC Linux-csomópontra történő telepítése a **/tmp** mappát használja. Azokat a modulokat, `nxautomation` amelyek a megfelelő helyekre való telepítése előtt ideiglenesen letöltődnek az ellenőrzéshez. A modulok megfelelő telepítésének biztosításához a linuxos Log Analytics-ügynöknek írási/olvasási jogosultságra van szüksége a **/tmp** mappához.<br><br>
>A Linux rendszerhez készült Log Analytics-ügynök `omsagent` felhasználóként fut. Ha >írási engedélyt szeretne adni a `omsagent` felhasználónak, futtassa a parancsot `setfacl -m u:omsagent:rwx /tmp` .

### <a name="enable-a-vm-using-azure-portal"></a>Virtuális gép engedélyezése Azure Portal használatával

Azure-beli virtuális gép engedélyezése a [Azure Portalon](https://portal.azure.com/)keresztül:

1. Navigáljon ahhoz a Azure Automation-fiókhoz, amelyben engedélyezni szeretné a virtuális gépeket. 

2. Az állapot konfigurációja lapon válassza a **csomópontok** lapot, majd kattintson a **Hozzáadás** gombra.

3. Válassza ki az engedélyezni kívánt virtuális gépet.

4. Ha a gépen nincs telepítve a PowerShell desired State bővítmény, és a tápellátási állapot fut, kattintson a **kapcsolat** gombra.

5. A **regisztráció** alatt adja meg a [PowerShell DSC helyi Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig) a használati esethez szükséges értékeket. Opcionálisan megadhat egy csomópont-konfigurációt, amelyet a virtuális géphez szeretne rendelni.

![virtuális gép engedélyezése](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Virtuális gép engedélyezése Azure Resource Manager-sablonok használatával

Azure Resource Manager-sablonok használatával telepítheti és engedélyezheti a virtuális gépek állapotának konfigurálását. Tekintse meg a [kiszolgáló által felügyelt, a kívánt állapot-konfigurációs szolgáltatást](https://azure.microsoft.com/resources/templates/101-automation-configuration/) egy olyan sablonhoz, amely lehetővé teszi egy meglévő virtuális gép állapotának konfigurálását. Ha virtuálisgép-méretezési csoport kezelésére van szüksége, tekintse meg a [Azure Automation által felügyelt virtuálisgép-méretezési csoport konfigurációjának](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)példa sablonját.

### <a name="enable-machines-using-powershell"></a>Gépek engedélyezése a PowerShell-lel

A PowerShellben a [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) parancsmag használatával engedélyezheti a virtuális gépek számára az állapot konfigurációját. 

> [!NOTE]
>A `Register-AzAutomationDscNode` parancsmag jelenleg csak Windows rendszerű gépeken van implementálva, mivel csak a Windows-bővítményt indítja el.

### <a name="register-vms-across-azure-subscriptions"></a>Virtuális gépek regisztrálása Azure-előfizetések között

A virtuális gépek más Azure-előfizetésből való regisztrálásának legjobb módja a DSC-bővítmény használata egy Azure Resource Manager telepítési sablonban. Példákat a [Azure Resource Manager sablonokkal rendelkező, a kívánt állapotú konfigurációs bővítményben](../virtual-machines/extensions/dsc-template.md)talál.

A sablonban paraméterként használandó regisztrációs kulcs és regisztrációs URL-cím megkereséséhez lásd: a [gépek biztonságos engedélyezése a regisztráció használatával](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Fizikai/virtuális Windows-gépek engedélyezése

Engedélyezheti a helyszíni vagy más felhőalapú környezetekben (például AWS EC2-példányokban) futó Windows-kiszolgálókat, hogy Azure Automation az állapot konfigurációját. A kiszolgálóknak kimenő hozzáféréssel kell rendelkezniük az [Azure-hoz](automation-dsc-overview.md#network-planning).

1. Ellenőrizze, hogy a [WMF 5](https://aka.ms/wmf5latest) legújabb verziója telepítve van-e a gépeken az állapot-konfiguráció engedélyezéséhez. Emellett a WMF 5-öt telepíteni kell azon a számítógépen, amelyet a gépek engedélyezéséhez használ.
1. Kövesse a DSC- [Metaconfigurations létrehozása](#generate-dsc-metaconfigurations) című témakör útmutatását, és hozzon létre egy mappát, amely tartalmazza a szükséges DSC-metaconfigurations. 
1. Az alábbi parancsmag használatával a PowerShell DSC-metaconfigurations távolról is alkalmazhatja a gépekre az engedélyezéshez. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Ha nem tudja távolról alkalmazni a PowerShell DSC-metaconfigurations, másolja a **metaconfigurations** mappát az engedélyezni kívánt gépekre. Ezután adjon hozzá kódot a [set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) helyi hívásához a gépeken.
1. A Azure Portal vagy a parancsmagok használatával ellenőrizze, hogy a gépek a Azure Automation-fiókban regisztrált állapot-konfigurációs csomópontként jelennek-e meg.

## <a name="enable-physicalvirtual-linux-machines"></a>Fizikai/virtuális Linux rendszerű gépek engedélyezése

Engedélyezheti a helyszíni vagy más felhőalapú környezetekben futó Linux-kiszolgálókat az állapot konfigurálásához. A kiszolgálóknak kimenő hozzáféréssel kell rendelkezniük az [Azure-hoz](automation-dsc-overview.md#network-planning).

1. Győződjön meg arról, hogy a Linux rendszerhez készült [PowerShell kívánt állapot-konfiguráció](https://github.com/Microsoft/PowerShell-DSC-for-Linux) legújabb verziója telepítve van a gépeken az állapot-konfiguráció engedélyezéséhez.
2. Ha a [POWERSHELL DSC helyi Configuration Manager alapértelmezett](/powershell/scripting/dsc/managing-nodes/metaConfig4) beállításai megfelelnek a használati esetnek, és engedélyezni szeretné a gépeket, hogy mind lekérik és jelentsenek az állapot-konfigurációra:

   - Az engedélyezéshez minden Linux-gépen `Register.py` engedélyezze a számítógépet a POWERSHELL DSC helyi Configuration Manager alapértelmezett értékeivel.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Az Automation-fiók regisztrációs kulcsának és regisztrációs URL-címének megkereséséhez lásd: a [gépek biztonságos engedélyezése a regisztrációval](#enable-machines-securely-using-registration).

3. Ha a PowerShell DSC helyi Configuration Manager (LCD ChipOnGlas) alapértelmezett értéke nem felel meg a használati esetnek, vagy engedélyezni szeretné, hogy csak az állapot-konfigurációt Azure Automationó gépek legyenek bejelentve, kövesse az 4-7. lépést. Ellenkező esetben folytassa közvetlenül a 7. lépéssel.

4. Kövesse a [DSC Metaconfigurations létrehozása](#generate-dsc-metaconfigurations) szakasz utasításait, és hozzon létre egy mappát, amely tartalmazza a szükséges DSC-metaconfigurations.

5. Győződjön meg arról, hogy a [WMF 5](https://aka.ms/wmf5latest) legújabb verziója van telepítve a számítógépeken az állapot-konfiguráció engedélyezéséhez használt számítógépen.

6. Adja hozzá a következő kódot a PowerShell DSC-metaconfigurations Távoli gépre való alkalmazásához az engedélyezéshez.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Ha nem tudja távolról alkalmazni a PowerShell DSC-metaconfigurations, másolja a távoli gépeknek megfelelő metaconfigurations a 4. lépés a Linux rendszerű gépekhez című témakörben leírt mappából.

8. Adja hozzá a kódot a helyi híváshoz az `Set-DscLocalConfigurationManager.py` egyes Linux-gépeken az állapot-konfiguráció engedélyezéséhez.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Az Azure Portal vagy a parancsmagok használatával győződjön meg arról, hogy az engedélyezni kívánt gépek mostantól a Azure Automation-fiókban regisztrált DSC-csomópontként jelennek meg.

## <a name="generate-dsc-metaconfigurations"></a>DSC-metaconfigurations előállítása

A gépek állapot-konfigurálásának engedélyezéséhez létrehozhat egy DSC- [metaconfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig). Ez a konfiguráció azt jelzi, hogy a DSC-ügynök lekéri és/vagy jelentést készít Azure Automation állapot-konfigurációról. A DSC-metaconfiguration a PowerShell DSC-konfiguráció vagy a Azure Automation PowerShell-parancsmagok használatával hozhatja Azure Automation állapot-konfigurációhoz.

> [!NOTE]
> A DSC-metaconfigurations azokat a titkokat tartalmazzák, amelyek szükségesek a gép automatizálási fiókban való engedélyezéséhez a felügyelethez. Ügyeljen arra, hogy megfelelően megvédje a létrehozott DSC-metaconfigurations, vagy törölje azokat a használat után.

A metaconfigurations proxy-támogatását a [helyi Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)vezérli, amely a Windows PowerShell DSC-motor. Az LCD ChipOnGlas az összes cél csomóponton fut, és felelős a DSC metaconfiguration-szkriptben található konfigurációs erőforrások meghívásához. A metaconfiguration a-ben, a-ben, a- `ProxyURL` `ProxyCredential` ben `ConfigurationRepositoryWeb` és a `ResourceRepositoryWeb` blokkokban igény szerint, a és a tulajdonságokat is beleértve `ReportServerWeb` . Példa az URL-beállításra `ProxyURL = "http://172.16.3.6:3128";` . A `ProxyCredential` tulajdonság egy `PSCredential` objektumra van beállítva, a [hitelesítő adatok kezelése Azure Automationban](shared-resources/credentials.md)című témakörben leírtak szerint. 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC-metaconfigurations létrehozása DSC-konfiguráció használatával

1. Nyissa meg a VSCode (vagy a kedvenc szerkesztőjét) rendszergazdaként a helyi környezetben található gépen. A gépnek telepítve kell lennie a [WMF 5](https://aka.ms/wmf5latest) legújabb verziójával.
1. Másolja a következő parancsfájlt helyileg. Ez a szkript egy PowerShell DSC-konfigurációt tartalmaz a metaconfigurations létrehozásához, valamint egy olyan parancsot, amely kirúgja a metaconfiguration létrehozását.

    > [!NOTE]
    > Az állapot-konfigurációs csomópontok konfigurációs nevei megkülönböztetik a kis-és nagybetűket a Azure Portal. Ha az eset nem egyezik, a csomópont nem jelenik meg a **csomópontok** lapon.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Adja meg az Automation-fiókjához tartozó regisztrációs kulcsot és URL-címet, valamint az engedélyezni kívánt gépek nevét. Az összes többi paraméter megadása nem kötelező. Az Automation-fiók regisztrációs kulcsának és regisztrációs URL-címének megkereséséhez lásd: a [gépek biztonságos engedélyezése a regisztrációval](#enable-machines-securely-using-registration).

1. Ha azt szeretné, hogy a gépek jelentést készítsenek a DSC-állapotadatok Azure Automation állapot konfigurációjában, de ne legyenek lekéréses konfiguráció vagy PowerShell-modulok, állítsa igaz értékre a `ReportOnly` paramétert.

1. Ha `ReportOnly` nincs beállítva, a gépek a DSC-állapotinformációkat Azure Automation állapot-konfigurációra és lekéréses konfigurációra vagy PowerShell-modulokra jelentik. Adja meg a paramétereket ennek megfelelően a `ConfigurationRepositoryWeb` , `ResourceRepositoryWeb` és `ReportServerWeb` blokkokban.

1. Futtassa a szkriptet. Most már rendelkeznie kell egy **DscMetaConfigs** nevű munkakönyvtár-mappával, amely tartalmazza a PowerShell DSC-metaconfigurations, amely lehetővé teszi a gépek engedélyezését (rendszergazdaként).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>DSC-metaconfigurations előállítása Azure Automation-parancsmagok használatával

Ha a PowerShell DSC LCD alapértékei megfelelnek a használati esetnek, és engedélyezni szeretné a gépek lekérését és jelentését Azure Automation állapot konfigurálására, egyszerűen létrehozhatja a szükséges DSC-metaconfigurations az Azure Automation-parancsmagok használatával.

1. Nyissa meg a PowerShell-konzolt vagy a VSCode rendszergazdaként egy gépen a helyi környezetben.
2. Kapcsolódás Azure Resource Manager a [AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount)használatával.
3. Töltse le a PowerShell DSC-metaconfigurations azon gépekhez, amelyeket engedélyezni kíván az Automation-fiókból, amelyben csomópontokat állít be.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Ekkor rendelkeznie kell egy **DscMetaConfigs** -mappával, amely tartalmazza a PowerShell DSC-metaconfigurations, amely lehetővé teszi a gépek engedélyezését (rendszergazdaként).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>A gépek biztonságos engedélyezése a regisztrációval

A WMF 5 DSC regisztrációs protokollon keresztül biztonságosan engedélyezheti a gépeket egy Azure Automation-fiókhoz. Ez a protokoll lehetővé teszi a DSC-csomópontok hitelesítését egy PowerShell DSC-lekéréses vagy jelentéskészítő kiszolgálón, beleértve a Azure Automation állapot konfigurációját. A csomópont regisztrálja a kiszolgálót a regisztrációs URL-címen, és egy regisztrációs kulccsal hitelesíti magát. A regisztráció során a DSC-csomópont és a DSC-lekérési/jelentéskészítő kiszolgáló egyeztet egy egyedi tanúsítvánnyal ahhoz, hogy a csomópont hitelesítve legyen a regisztráció után. Ez a folyamat megakadályozza, hogy az engedélyezett csomópontok megszemélyesítsék egymást, például ha egy csomópontot feltörtek, és rosszindulatúan viselkednek. A regisztrációt követően a regisztrációs kulcsot nem használja újra a hitelesítéshez, és a rendszer törli a csomópontról.

Az állapot-konfigurációs regisztrációs protokollhoz szükséges adatokat a Azure Portalban található **Fiókbeállítások** **területen érheti el.** 

![Azure Automation-kulcsok és URL-cím](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- A regisztrációs URL-cím a kulcsok lap URL-mezője.
- A regisztrációs kulcs az **elsődleges hozzáférési kulcs** mező vagy a kulcsok lapon található **másodlagos elérési kulcs** mező értéke. Bármelyik kulcsot használhatja.

A nagyobb biztonság érdekében a kulcsok oldalon bármikor újragenerálhatja egy Automation-fiók elsődleges és másodlagos hozzáférési kulcsait. A kulcs újragenerálása megakadályozza a későbbi csomópontok regisztrációját a korábbi kulcsok használatával.

## <a name="re-register-a-node"></a>Csomópont újbóli regisztrálása

Miután Azure Automation állapot konfigurációjában egy gépet DSC-csomópontként regisztrált, több oka lehet annak, hogy a későbbiekben újra regisztrálnia kell a csomópontot.

- **Tanúsítvány megújítása.** A Windows Server 2019 előtti verziói esetében minden egyes csomópont automatikusan egyeztet egy egyedi tanúsítványt a hitelesítéshez, amely egy év után lejár. Ha egy tanúsítvány megújítása nélkül lejár, a csomópont nem tud kommunikálni Azure Automation és meg van jelölve `Unresponsive` . Jelenleg a PowerShell DSC regisztrációs protokoll nem tudja automatikusan megújítani a tanúsítványokat, amikor közelednek a lejárathoz, és egy év múlva újra regisztrálnia kell a csomópontokat. Az újbóli regisztrálás előtt győződjön meg arról, hogy minden csomóponton WMF 5 RTM fut. 

    Az újbóli regisztráció a tanúsítvány lejárati idejének 90 vagy kevesebb nap múlva, vagy a tanúsítvány lejárati idejének lejárta után bármikor új tanúsítványt hoz létre és használ. A probléma megoldása a Windows Server 2019-es és újabb verzióiban is megtalálható.

- **A DSC LCD-értékek változásai.** Előfordulhat, hogy módosítania kell a [POWERSHELL DSC LCD-értékeket](/powershell/scripting/dsc/managing-nodes/metaConfig4) a csomópont kezdeti regisztrálása során, például: `ConfigurationMode` . Jelenleg csak a DSC-ügynök értékeit módosíthatja újbóli regisztráció útján. Az egyetlen kivétel a csomóponthoz rendelt csomópont-konfigurációs érték. Ezt közvetlenül Azure Automation DSC-ben is megváltoztathatja.

A csomópontot úgy is újra regisztrálhatja, ahogy először regisztrálta a csomópontot, a jelen dokumentumban ismertetett módszerek bármelyikének használatával. Az újbóli regisztrálás előtt nem kell megszüntetnie a csomópont regisztrációját Azure Automation állapot-konfigurációból.

## <a name="check-status-of-vm-setup"></a>A virtuális gép beállítás állapotának ellenõrzése

Az állapot-konfigurációval egyszerűen engedélyezheti az Azure Windows rendszerű virtuális gépeket a konfigurálási felügyelethez. A motorháztető alatt az Azure VM desired State Configuration bővítmény a virtuális gép Azure Automation állapot-konfigurációval való regisztrálására szolgál. Mivel az Azure-beli virtuális gép kívánt állapotának konfigurációs bővítménye aszinkron módon fut, fontos lehet az előrehaladás nyomon követése és a végrehajtásuk hibaelhárítása.

> [!NOTE]
> Az Azure-beli virtuális gépeknek az Azure-beli, a kívánt állapot-konfiguráció bővítményt használó állapot-konfigurációhoz való engedélyezésének bármely módja akár egy órát is igénybe vehet, hogy Azure Automation a virtuális gépek regisztrálva jelenjenek meg. Ezt a késleltetést a virtuális gépekre vonatkozó, az Azure-beli kívánt állapot konfigurációs bővítményének a WMF 5-ös verziójának telepítése okozza, amely a virtuális gépek állapotának konfigurálásához szükséges.

Az Azure-beli virtuális gép kívánt állapotára vonatkozó konfigurációs bővítmény állapotának megtekintése:

1. A Azure Portal navigáljon az engedélyezett virtuális géphez.
2. Kattintson a **bővítmények** elemre a **Beállítások** területen. 
3. Most válassza a **DSC** vagy a **DSCForLinux** lehetőséget az operációs rendszertől függően. 
4. További részletekért kattintson a **részletes állapot megtekintése** lehetőségre.

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának első lépései](automation-dsc-getting-started.md)című témakört.
- Ha szeretne többet megtudni a DSC-konfigurációk fordításáról, hogy hozzá lehessen rendelni őket a célcsoportokhoz, tekintse meg [a DSC-konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md)című témakört.
- A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
- A folyamatos üzembe helyezési folyamat során Azure Automation állapot konfigurációjának használatával kapcsolatban lásd: a [folyamatos üzembe helyezés beállítása a csokoládéval](automation-dsc-cd-chocolatey.md).
- Hibaelhárítási információk: [Azure Automation állapot-konfiguráció hibaelhárítása](./troubleshoot/desired-state-configuration.md).
