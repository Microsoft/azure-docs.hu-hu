---
title: A Azure Automation State Configuration
description: Ez a cikk bemutatja, hogyan állíthat be gépeket a felügyeleti Azure Automation State Configuration.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: d338c5f34d49663345582198ff53ba50a2919d7e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829421"
---
# <a name="enable-azure-automation-state-configuration"></a>A Azure Automation State Configuration

Ez a témakör azt ismerteti, hogyan állíthatja be a gépeket a felügyeleti Azure Automation State Configuration. A szolgáltatás részleteiért tekintse meg a Azure Automation State Configuration [áttekintését.](automation-dsc-overview.md)

## <a name="enable-azure-vms"></a>Azure-beli virtuális gépek engedélyezése

Azure Automation State Configuration segítségével egyszerűen engedélyezheti az Azure-beli virtuális gépeket a konfigurációkezeléshez a Azure Portal, Azure Resource Manager vagy a PowerShell használatával. A háttérben, és anélkül, hogy rendszergazda távolról csatlakozik egy virtuális géphez, az Azure-beli virtuális Desired State Configuration regisztrálja a virtuális gépet az Azure Automation State Configuration. Mivel az Azure-bővítmény aszinkron módon fut, a folyamat előrehaladásának nyomon követéséhez szükséges lépések a Virtuális gép [telepítésének állapotának ellenőrzése területen találhatóak.](#check-status-of-vm-setup)

> [!NOTE]
>A DSC Linux-csomóponton való üzembe helyezése a **/tmp mappát** használja. Az olyan `nxautomation` modulokat, mint például a rendszer ideiglenesen letölti ellenőrzésre, mielőtt a megfelelő helyre telepítené őket. A modulok megfelelő telepítésének biztosításához a Linuxhoz használható Log Analytics-ügynöknek olvasási/írási engedélyekkel kell rendelkeznie a **/tmp mappában.**<br><br>
>A Linuxhoz használható Log Analytics-ügynök felhasználóként `omsagent` fut. Ha írási >a `omsagent` felhasználónak, futtassa a `setfacl -m u:omsagent:rwx /tmp` parancsot.

### <a name="enable-a-vm-using-azure-portal"></a>Virtuális gép engedélyezése a Azure Portal

Azure-beli virtuális gép State Configuration a [Azure Portal:](https://portal.azure.com/)

1. Lépjen arra Azure Automation fiókra, amelyben engedélyezni szeretné a virtuális gépeket. 

2. A State Configuration válassza a **Csomópontok lapot,** majd kattintson a Hozzáadás **gombra.**

3. Válassza ki az engedélyezni szükséges virtuális gépet.

4. Ha a gépen nincs telepítve a PowerShell célállapot-bővítménye, és az energiaállapot fut, kattintson a **Csatlakozás elemre.**

5. A **Regisztráció alatt** adja meg a [PowerShell DSC helyi Konfigurációkezelő a](/powershell/scripting/dsc/managing-nodes/metaConfig) saját esetéhez szükséges értékeket. Megadhatja a virtuális géphez hozzárendelni szükséges csomópontkonfigurációt is.

![virtuális gép engedélyezése](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Virtuális gép engedélyezése Azure Resource Manager sablonokkal

A virtuális gépeket telepítheti és engedélyezheti a State Configuration a Azure Resource Manager használatával. Tekintse [meg a](https://azure.microsoft.com/resources/templates/101-automation-configuration/) Desired State Configuration által felügyelt kiszolgálót, és tekintse meg azt a példasablont, amely lehetővé teszi egy meglévő virtuális gép State Configuration. Ha virtuálisgép-méretezési készletet fog kezelni, tekintse meg a virtuálisgép-méretezési készlet konfigurációja által felügyelt virtuálisgép-méretezési [Azure Automation.](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)

### <a name="enable-machines-using-powershell"></a>Gépek engedélyezése a PowerShell használatával

A [PowerShellBen a Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) parancsmag használatával engedélyezheti a virtuális gépeket a State Configuration. 

> [!NOTE]
>A parancsmag jelenleg csak Windows rendszerű gépeken van megvalósítva, mivel csak a `Register-AzAutomationDscNode` Windows-bővítményt aktiválja.

### <a name="register-vms-across-azure-subscriptions"></a>Virtuális gépek regisztrálása Azure-előfizetések között

A másik Azure-előfizetésből származó virtuális gépek regisztrálásának legjobb módja, ha a DSC-bővítményt egy Azure Resource Manager sablonban használja. Példákat a Desired State Configuration [sablonokat Azure Resource Manager tartalmaz.](../virtual-machines/extensions/dsc-template.md)

A sablonban paraméterként használni kívánt regisztrációs kulcs és regisztrációs URL-cím megkereséhez lásd: Gépek biztonságos engedélyezése [regisztrációval.](#enable-machines-securely-using-registration)

## <a name="enable-physicalvirtual-windows-machines"></a>Fizikai/virtuális Windows rendszerű gépek engedélyezése

Engedélyezheti a helyszínen vagy más felhőkörnyezetekben (például AWS EC2-példányok) futó Windows-kiszolgálókat a Azure Automation State Configuration. A kiszolgálóknak kimenő [hozzáféréssel kell rendelkezik az Azure-hoz.](automation-dsc-overview.md#network-planning)

1. Győződjön meg arról, hogy a [WMF 5](https://aka.ms/wmf5latest) legújabb verziója telepítve van a gépeken, hogy engedélyezze a State Configuration. Emellett a WMF 5-öt telepíteni kell a gépek engedélyezéséhez használt számítógépre.
1. Kövesse a [Generate DSC metaconfigurations (DSC-metakonfigurációk](#generate-dsc-metaconfigurations) létrehozása) részt a szükséges DSC-metakonfigurációkat tartalmazó mappa létrehozásához. 
1. A következő parancsmag használatával távolról alkalmazhatja a PowerShell DSC metakonfigurációit az engedélyezni szükséges gépekre. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Ha nem tudja távolról alkalmazni a PowerShell DSC metakonfigurációit, másolja a **metakonfigurációk** mappát az engedélyezni kívánt gépekre. Ezután adjon hozzá egy kódot a [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) helyi hívásához a gépeken.
1. A Azure Portal vagy parancsmagok használatával ellenőrizze, hogy a gépek State Configuration csomópontként jelennek-e meg a Azure Automation fiókjában.

## <a name="enable-physicalvirtual-linux-machines"></a>Fizikai/virtuális Linux rendszerű gépek engedélyezése

Engedélyezheti a helyszínen vagy más felhőkörnyezetekben futó Linux-kiszolgálókat a State Configuration. A kiszolgálóknak kimenő [hozzáféréssel kell rendelkezik az Azure-hoz.](automation-dsc-overview.md#network-planning)

1. Győződjön meg arról, hogy a [Linuxhoz Desired State Configuration PowerShell](https://github.com/Microsoft/PowerShell-DSC-for-Linux) legújabb verziója telepítve van a gépeken a State Configuration.
2. Ha a [PowerShell DSC helyi Konfigurációkezelő](/powershell/scripting/dsc/managing-nodes/metaConfig4) alapértelmezett érték megfelel az Ön esetében használt esetnek, és szeretné engedélyezni a gépeket, hogy azok lekértek és jelentéssel is State Configuration:

   - Az egyes engedélyezni szükséges Linux-gépeken a használatával engedélyezze a gépet a `Register.py` PowerShell DSC helyi Konfigurációkezelő alapértelmezett értékekkel.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Az Automation-fiók regisztrációs kulcsának és regisztrációs URL-címének megkereséhez lásd: Gépek biztonságos engedélyezése [regisztrációval.](#enable-machines-securely-using-registration)

3. Ha a PowerShell DSC helyi Konfigurációkezelő (LCM) alapértelmezett beállítása nem egyezik meg a használt esethez képest, vagy engedélyezni szeretné a csak jelentéssel Azure Automation State Configuration gépek használatát, kövesse a 4–7. lépést. Egyéb esetben folytassa közvetlenül a 7. lépéssel.

4. Kövesse a [DSC-metakonfigurációk](#generate-dsc-metaconfigurations) létrehozása szakaszban található utasításokat a szükséges DSC-metakonfigurációkat tartalmazó mappa létrehozásához.

5. Győződjön meg arról, hogy a [WMF 5](https://aka.ms/wmf5latest) legújabb verziója telepítve van arra a számítógépre, amely lehetővé teszi a gépek számára a State Configuration.

6. Adja hozzá a következő kódot a PowerShell DSC metakonfigurációk távoli alkalmazásához az engedélyezni szükséges gépeken.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Ha nem tudja távolról alkalmazni a PowerShell DSC-metakonfigurációkat, másolja a távoli gépeknek megfelelő metakonfigurációkat a 4. lépésben leírt mappából a Linux rendszerű gépekre.

8. Adja hozzá az egyes Linux-gépek helyi hívását engedélyező kódot `Set-DscLocalConfigurationManager.py` a State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. A Azure Portal vagy parancsmagok használatával győződjön meg arról, hogy az engedélyeznie kell a gépeket az Azure Automation-fiókjában regisztrált DSC-csomópontokként.

## <a name="generate-dsc-metaconfigurations"></a>DSC-metakonfigurációk létrehozása

Ha bármely gépet engedélyezni szeretné State Configuration, létrehozhat egy [DSC-metakonfigurációt.](/powershell/scripting/dsc/managing-nodes/metaConfig) Ez a konfiguráció arra utasítja a DSC-ügynököt, hogy lekérte és/vagy jelentést Azure Automation State Configuration. DSC-metakonfigurációt hozhat létre a Azure Automation State Configuration PowerShell DSC-konfigurációval vagy a Azure Automation PowerShell-parancsmagokkal.

> [!NOTE]
> A DSC-metakonfigurációk tartalmazzák az Automation-fiókban található gépek felügyeletének engedélyezéséhez szükséges titkos adatokat. Ügyeljen arra, hogy megfelelően védje a létrehozott DSC-metakonfigurációkat, vagy törölje őket a használat után.

A metakonfigurációk proxytámogatását a [helyi](/powershell/scripting/dsc/managing-nodes/metaconfig)Konfigurációkezelő vezérli, amely Windows PowerShell DSC-motor. Az LCM az összes célcsomóponton fut, és a feladata a DSC metakonfigurációs parancsfájlban található konfigurációs erőforrások hívása. A metakonfigurációkban a proxytámogatást úgy használhatja, hogy szükség szerint tartalmazza a és a tulajdonság definícióját a `ProxyURL` `ProxyCredential` , és `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` `ReportServerWeb` blokkokban. Az URL-beállításra példa a `ProxyURL = "http://172.16.3.6:3128";` következő: . A `ProxyCredential` tulajdonság egy objektumra van `PSCredential` állítva, [](shared-resources/credentials.md)a Hitelesítő adatok kezelése az Azure Automation. 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC-metakonfigurációk létrehozása DSC-konfigurációval

1. Nyissa meg a VSCode-et (vagy a kedvenc szerkesztőjét) rendszergazdaként egy gépen a helyi környezetben. A gépre a [WMF 5](https://aka.ms/wmf5latest) legújabb verziójának kell telepítve lennie.
1. Másolja helyileg az alábbi szkriptet. Ez a szkript egy PowerShell DSC-konfigurációt tartalmaz metakonfigurációk létrehozásához, és egy parancsot a metakonfiguráció létrehozásának létrehozására.

    > [!NOTE]
    > State Configuration csomópont-konfigurációk nevei megkülönböztetik a kis- és nagybetűket a Azure Portal. Ha az eset nem egyező, a csomópont nem  fog a Csomópontok lapon ördülni.

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

1. Adja meg az Automation-fiók regisztrációs kulcsát és URL-címét, valamint az engedélyezni kívánt gépek nevét. Az összes többi paraméter megadása nem kötelező. Az Automation-fiók regisztrációs kulcsának és regisztrációs URL-címének megkereséhez lásd: Gépek biztonságos engedélyezése [regisztrációval.](#enable-machines-securely-using-registration)

1. Ha azt szeretné, hogy a gépek dsc-állapotinformációkat adjanak Azure Automation State Configuration, de a lekért konfigurációt vagy a PowerShell-modulokat ne, állítsa a paramétert true `ReportOnly` (igaz) állapotra.

1. Ha nincs beállítva, a gépek DSC-állapotinformációkat jelentnek a Azure Automation State Configuration konfigurációs vagy `ReportOnly` PowerShell-moduloknak. Ennek megfelelően állítsa be a paramétereket a `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` , és `ReportServerWeb` blokkokban.

1. Futtassa a szkriptet. Most már van egy **DscMetaConfigs** nevű munkakönyvtármappája, amely a gépek (rendszergazdaként) engedélyezni kívánt PowerShell DSC-metakonfigurációit tartalmazza.

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>DSC-metakonfigurációk létrehozása Azure Automation parancsmagokkal

Ha a PowerShell DSC LCM alapértelmezett beállítása megfelel az Ön által használt esetnek, és szeretné engedélyezni, hogy a gépek a Azure Automation State Configuration-ból és a jelentésből is lekértek, a szükséges DSC-metakonfigurációkat egyszerűbben létrehozhatja a Azure Automation-parancsmagok használatával.

1. Nyissa meg a PowerShell-konzolt vagy a VSCode-et rendszergazdaként a helyi környezetben található gépen.
2. Csatlakozzon a Azure Resource Manager [Connect-AzAccount használatával.](/powershell/module/Az.Accounts/Connect-AzAccount)
3. Töltse le az engedélyezni kívánt gépek PowerShell DSC-metakonfigurációit abból az Automation-fiókból, amelyben a csomópontokat beállítja.

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

1. Most már van egy **DscMetaConfigs** mappája, amely tartalmazza a gépek számára engedélyezni kívánt PowerShell DSC metakonfigurációkat (rendszergazdaként).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Gépek biztonságos engedélyezése regisztrációval

A WMF 5 DSC regisztrációs protokollal biztonságosan engedélyezheti a gépeket egy Azure Automation fiókhoz. Ez a protokoll lehetővé teszi, hogy egy DSC-csomópont hitelesítést hitelesítsen egy PowerShell DSC lekért vagy jelentési kiszolgálón, beleértve a Azure Automation State Configuration. A csomópont regisztrál a kiszolgálóval a regisztrációs URL-címen, és egy regisztrációs kulccsal hitelesíti magát. A regisztráció során a DSC-csomópont és a DSC lekérő/jelentési kiszolgáló egyeztet egy egyedi tanúsítványt a csomópont számára a kiszolgálóval való hitelesítéshez a regisztrációt követően. Ez a folyamat megakadályozza, hogy az engedélyezett csomópontok megszemélyesülnek egymással, például ha egy csomópontot feltörnek, és rosszindulatúan viselkednek. A regisztrációt követően a rendszer nem használja újra a regisztrációs kulcsot a hitelesítéshez, és törli a csomópontról.

Az alkalmazásregisztrációs protokollhoz szükséges State Configuration a  Kulcsok  alatt, a fiókbeállítások alatt Azure Portal. 

![Azure Automation-kulcsok és URL-cím](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- A regisztrációs URL-cím a Kulcsok lap URL-címe mezője.
- A regisztrációs kulcs az  Elsődleges hozzáférési kulcs mező vagy a **Másodlagos** hozzáférési kulcs mező értéke a Kulcsok lapon. Bármelyik kulcs használható.

A biztonság érdekében bármikor újragenerálhatja egy Automation-fiók elsődleges és másodlagos hozzáférési kulcsait a Kulcsok lapon. A kulcsgenerálás megakadályozza, hogy a jövőbeli csomópontregisztrációk korábbi kulcsokat használjanak.

## <a name="re-register-a-node"></a>Csomópont újra regisztrálása

Miután DSC-csomópontként regisztrált egy gépet a Azure Automation State Configuration, több oka is lehet annak, hogy a jövőben újra kell regisztrálnia a csomópontot.

- **Tanúsítvány megújítása.** A Windows Server Windows Server 2019 előtti verziói esetén minden csomópont automatikusan egyeztet egy egyedi tanúsítványt a hitelesítéshez, amely egy év után lejár. Ha egy tanúsítvány megújítás nélkül jár le, a csomópont nem tud kommunikálni a Azure Automation és meg van `Unresponsive` jelölve. A PowerShell DSC regisztrációs protokollja jelenleg nem tudja automatikusan megújítani a tanúsítványokat, amikor azok lejárnak, és egy év után újra kell regisztrálnia a csomópontokat. A regisztrálás előtt győződjön meg arról, hogy minden csomóponton WMF 5 RTM fut. 

    Az újraregisztráció a tanúsítvány lejárati idejétől számított 90 napon belül vagy a tanúsítvány lejárati idejét követően bármikor új tanúsítvány generálása és használata történik. A probléma megoldását a Windows Server 2019 és újabb verziók tartalmazzák.

- **A DSC LCM értékeinek változásai.** Előfordulhat, hogy módosítania kell a Csomópont kezdeti regisztrációja során beállított [PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) LCM-értékeket, `ConfigurationMode` például: . Jelenleg ezeket a DSC-ügynökértékeket csak az újraregisztrációval módosíthatja. Az egyetlen kivétel a csomóponthoz rendelt Csomópont-konfiguráció érték. Ezt a DSC-Azure Automation módosíthatja.

Újra regisztrálhat egy csomópontot, ahogyan kezdetben regisztrálta a csomópontot a jelen dokumentumban ismertetett módszerek bármelyikével. A csomópont regisztrációját nem kell Azure Automation State Configuration a csomópont újra regisztrálása előtt.

## <a name="check-status-of-vm-setup"></a>A virtuális gép beállítási állapotának ellenőrzése

State Configuration segítségével egyszerűen engedélyezheti az Azure-beli Windows rendszerű virtuális gépeket a konfigurációkezeléshez. A háttérben az Azure-beli virtuális Desired State Configuration bővítmény használatával regisztrálja a virtuális gépet a Azure Automation State Configuration. Mivel az Azure vm Desired State Configuration bővítmény aszinkron módon fut, fontos lehet nyomon követni a folyamat előrehaladását és a végrehajtás hibaelhárítását.

> [!NOTE]
> Az Azure vm Desired State Configuration bővítményt használó Azure-beli Windows rendszerű virtuális gépek State Configuration engedélyezése akár egy órát is Azure Automation, amíg az Azure Automation regisztráltként mutatja a virtuális gépeket. Ezt a késést az azure-beli virtuális gép Desired State Configuration bővítménye telepítette a WMF 5-ös State Configuration.

Az Azure-beli virtuális gép Desired State Configuration megtekintése:

1. A Azure Portal keresse meg az engedélyezett virtuális gépet.
2. Kattintson **a Bővítmények elemre** a Beállítások **alatt.** 
3. Most válassza a **DSC** vagy **a DSCForLinux lehetőséget** az operációs rendszertől függően. 
4. További részletekért kattintson a Részletes állapot **megtekintése elemre.**

## <a name="next-steps"></a>Következő lépések

- Első lépések: [Első lépések a Azure Automation State Configuration.](automation-dsc-getting-started.md)
- A DSC-konfigurációk célcsomópontokhoz való hozzárendelése érdekében való fordításával kapcsolatos további információkért lásd: DSC-konfigurációk fordítása a [Azure Automation State Configuration.](automation-dsc-compile.md)
- A PowerShell-parancsmagok referenciáját lásd: [Az.Automation](/powershell/module/az.automation).
- Díjszabási információkért lásd: [Azure Automation State Configuration díjszabása.](https://azure.microsoft.com/pricing/details/automation/)
- A folyamatos üzembe helyezési folyamatban Azure Automation State Configuration példát a Folyamatos üzembe helyezés beállítása a [Chocolateyvel.](automation-dsc-cd-chocolatey.md)
- A hibaelhárítással kapcsolatos információkért lásd: [Hibaelhárítás Azure Automation State Configuration.](./troubleshoot/desired-state-configuration.md)
