---
title: Folyamatos üzembe Azure Automation a Chocolateyval
description: Ez a cikk bemutatja, hogyan állíthatja be a folyamatos State Configuration a Chocolatey csomagkezelővel.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 717561614a3e42995bbce6746839fd9b7cbca37e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834857"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Folyamatos üzembe helyezés beállítása a Chocolatey segítségével

A DevOps-világban számos olyan eszköz van, amely segítséget nyújt a folyamatos integrációs folyamat különböző pontjaihoz. Azure Automation [State Configuration](automation-dsc-overview.md) a DevOps-csapatok által is elérhető lehetőségek új kiegészítői. 

Azure Automation egy felügyelt szolgáltatás a Microsoft Azure, amely lehetővé teszi különböző feladatok automatizálását runbookok, csomópontok és megosztott erőforrások, például hitelesítő adatok, ütemezések és globális változók használatával. Azure Automation State Configuration kiterjeszti ezt az automatizálási képességet, hogy PowerShell Desired State Configuration (DSC) eszközöket is tartalmaz. 1 remek [áttekintés.](automation-dsc-overview.md)

Ez a cikk bemutatja, hogyan állíthat be folyamatos üzembe helyezést (CD) Windows rendszerű számítógépeken. A technikát egyszerűen kiterjesztheti úgy, hogy a szerepkörben szükség szerint annyi Windows-számítógépet foglaljon bele, mint például egy webhely, és onnan további szerepkörökhöz is át kell mennie.

![IaaS virtuális gépek folyamatos üzembe helyezése](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Magas szinten

Sok minden történik itt, de szerencsére két fő folyamatra bontható:

- Kód írása és tesztelése, majd telepítési csomagok létrehozása és közzététele a rendszer fő és alverzióihoz.
- Olyan virtuális gépek létrehozása és kezelése, amelyek telepítik és végrehajtják a csomagokban a kódot.  

Miután mindkét alapvető folyamat létrejött, a csomag automatikusan frissíthető a virtuális gépeken az új verziók létrehozásakor és üzembe helyezésekor.

## <a name="component-overview"></a>Összetevő áttekintése

Az olyan csomagkezelők, mint [az apt-get,](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) jól ismertek a Linux világában, de a Windows világában nem annyira.
[Chocolatey](https://chocolatey.org/) egy ilyen dolog, Scott Hanselman blogja pedig nagyszerű bevezetés. [](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) A Chocolatey röviden összefoglalva lehetővé teszi, hogy a parancssor használatával csomagokat telepítsen egy központi adattárból egy Windows operációs rendszerre. Létrehozhatja és kezelheti a saját adattárát, a Chocolatey pedig bármilyen számú kijelölt adattárból telepíthet csomagokat.

[A PowerShell DSC](/powershell/scripting/dsc/overview/overview) egy PowerShell-eszköz, amely lehetővé teszi, hogy deklarálja a gép kívánt konfigurációját. Ha például telepíteni szeretné a Chocolateyt, az IIS-t, a 80-as portot és a webhely 1.0.0-s verzióját, a DSC Local Konfigurációkezelő (LCM) implementálja ezt a konfigurációt. A DSC lekért kiszolgáló a gépek konfigurációit tároló adattárat tartalmazza. Az egyes gépek LCM-e rendszeresen ellenőrzi, hogy a konfigurációja megegyezik-e a tárolt konfigurációval. Jelentést készít az állapotról, vagy megpróbálja a gépet a tárolt konfigurációhoz igazításba hozni. A lekért kiszolgáló tárolt konfigurációjának szerkesztésével egy gép vagy gépkészlet igazodik a módosított konfigurációhoz.

A DSC-erőforrások olyan kódmodulok, amelyek speciális képességekkel rendelkeznek, például hálózatkezelés, Active Directory vagy SQL Server. A Chocolatey DSC-erőforrás tudja, hogyan férhet hozzá egy NuGet-kiszolgálóhoz (többek között), hogyan tölthet le csomagokat, telepíthet csomagokat stb. Számos más DSC-erőforrás is van a [PowerShell-galéria.](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title) Ezeket a modulokat a saját Azure Automation State Configuration kell telepítenie a konfigurációk számára.

Resource Manager sablonok deklaratív módon biztosítják az infrastruktúra előállítását, például hálózatokat, alhálózatokat, hálózati biztonságot és útválasztást, terheléselosztásokat, hálózati adaptereket, virtuális gépeket stb. Az alábbi cikk [összehasonlítja](../azure-resource-manager/management/deployment-models.md) a Resource Manager-alapú (deklaratív) és az Azure Service Management (ASM vagy klasszikus) üzembe helyezési modelljét (imperatív). Ez a cikk az alapvető erőforrás-szolgáltatókat, a számítást, a tárolást és a hálózatot tartalmazza.

A virtuálisgép-Resource Manager egyik fő jellemzője, hogy képes virtuálisgép-bővítményt telepíteni a virtuális gépre annak kiépítése során. A virtuálisgép-bővítmények olyan speciális képességekkel rendelkeznek, mint az egyéni szkriptek futtatása, a víruskereső szoftverek telepítése és a DSC konfigurációs szkriptek futtatása. A virtuálisgép-bővítményeknek számos más típusa is létezik.

## <a name="quick-trip-around-the-diagram"></a>Gyors út a diagramon

A lap tetejétől kezdve megírja a kódot, létrehozza, teszteli, majd létrehoz egy telepítőcsomagot. A Chocolatey különböző típusú telepítési csomagokat képes kezelni, például MSI, MSU, ZIP. Ha a Chocolatey natív képességei nem teljes körűek, a PowerShell teljes körűen képes a tényleges telepítésre. Helyezze a csomagot egy elérhető helyre– egy csomagtárba. Ez a használati példa egy nyilvános mappát használ egy Azure Blob Storage-fiókban, de bárhol lehet. A Chocolatey natív módon működik a NuGet-kiszolgálókkal és néhány másikkal a csomag metaadatainak kezeléséhez. [Ez a cikk](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) a lehetőségeket ismerteti. A használati példa a NuGetet használja. A Nuspec a csomagok metaadatait tartalmazza. A Nuspec-adatok NuPkg-fájlba vannak lefordítva, és egy NuGet-kiszolgálón vannak tárolva. Amikor a konfiguráció név szerint kér egy csomagot, és egy NuGet-kiszolgálóra hivatkozik, a virtuális gépen található Chocolatey DSC-erőforrás megragadja és telepíti a csomagot. Emellett egy csomag adott verzióját is lekértheti.

A kép bal alsó részén található egy Azure Resource Manager sablon. Ebben a használati példában a virtuálisgép-bővítmény csomópontként regisztrálja a virtuális Azure Automation State Configuration lekért kiszolgálóval. A rendszer kétszer tárolja a konfigurációt a lekért kiszolgálón: egyszer egyszerű szövegként, egyszer pedig MOF-fájlként fordítva. A Azure Portal MOF egy csomópont-konfigurációt képvisel, nem pedig egy egyszerű konfigurációt. Ez a csomóponthoz társított összetevő, így a csomópont tudni fogja a konfigurációját. Az alábbi részletek a csomópont konfigurációjának csomóponthoz való hozzárendelését mutatják be.

A Nuspec létrehozása, fordítása és tárolása egy NuGet-kiszolgálón csak kis dolog. Ön pedig már kezeli a virtuális gépeket. 

A folyamatos üzembe helyezés következő lépéséhez először be kell állitja a lekért kiszolgálót, egyszer regisztrálja vele a csomópontokat, majd létrehozza és tárolja a kezdeti konfigurációt a kiszolgálón. A csomagok frissítése és az adattárban való üzembe helyezése során csak szükség szerint kell frissítenie a lekért kiszolgáló konfigurációját és csomópont-konfigurációját.

Ha nem egy sablonnal Resource Manager, ez nem gond. Vannak PowerShell-parancsok, amelyek segítenek regisztrálni a virtuális gépeket a lekért kiszolgálón. További információ: [Gépek](automation-dsc-onboarding.md)Azure Automation State Configuration.

## <a name="about-the-usage-example"></a>A használati példa

A cikkben szereplő használati példa egy, az Azure-katalógusból származó általános Windows Server 2012 R2 rendszerképből származó virtuális géppel kezdődik. Bármely tárolt képről kezdhet, majd onnan finomhangolást is kezdhet a DSC-konfigurációval.
A lemezképbe be van ékesedő konfiguráció módosítása azonban sokkal nehezebb, mint dinamikusan frissíteni a konfigurációt a DSC használatával.

Ennek a módszernek a virtuális gépekkel való Resource Manager nem kell használnia egy virtuálisgép-sablont és a virtuálisgép-bővítményt. A virtuális gépeknek pedig nem kell az Azure-ban lennie ahhoz, hogy CD-felügyelet alatt legyen. Mindössze a Chocolatey telepítése és az LCM konfigurálása szükséges a virtuális gépen, hogy tudja, hol található a lekért kiszolgáló.

Amikor egy éles környezetben található virtuális gépen frissít egy csomagot, a frissítés telepítése közben ki kell vennie a virtuális gépet a rotációból. Ennek módja nagyban változik. Ha például egy virtuális gép egy virtuális gép mögött Azure Load Balancer, hozzáadhat egy egyéni mintavételt. A virtuális gép frissítése során a mintavételi végpont 400-ast ad vissza. A módosításhoz szükséges finomhangolás a konfiguráción belül is lehet, mivel a módosítással vissza lehet váltani 200-asra a frissítés befejezése után.

A használati példa teljes forrása ebben a Visual Studio [GitHubon](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) található.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>1. lépés: A lekért kiszolgáló és az Automation-fiók beállítása

Hitelesített ( `Connect-AzAccount` ) PowerShell-parancssorban: (eltarthat néhány percig, amíg a lekért kiszolgáló be van állítva)

```azurepowershell-interactive
New-AzResourceGroup -Name MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount -ResourceGroupName MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES -Name MY-AUTOMATION-ACCOUNT
```

Automation-fiókját a következő régiók bármelyikében (más néven helyeken) helyezze el: USA 2. keleti régiója, USA déli középső régiója, US Gov Virginia, Nyugat-Európa, Délkelet-Ázsia, Kelet-Japán, Közép-India és Délkelet-Ausztrália, Közép-Kanada, Észak-Európa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>2. lépés: A virtuálisgép-bővítmény finomhangolása Resource Manager sablonhoz

A virtuális gépek regisztrációjának részletei (a PowerShell DSC virtuálisgép-bővítmény használatával), amely ebben az [Azure gyorsindítási sablonban található.](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)
Ez a lépés regisztrálja az új virtuális gépet a lekért kiszolgálóval a csomópontok State Configuration listájában. A regisztráció egy része meghatározza a csomóponton alkalmazni kívánt csomópont-konfigurációt. Ennek a csomópont-konfigurációnak még nem kell léteznie a lekért kiszolgálón, így nem gond, hogy a 4. lépésben ez első alkalommal történik meg. A 2. lépésben azonban el kell döntenünk a csomópont és a konfiguráció nevét. Ebben a használati példában a csomópont "isvbox", a konfiguráció pedig "ISVBoxConfig". Így a csomópont konfigurációjának neve (amely a DeploymentTemplate.jsmeg lesz adva) "ISVBoxConfig.isvbox".

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>3. lépés: A szükséges DSC-erőforrások hozzáadása a lekért kiszolgálóhoz

A PowerShell-galéria DSC-erőforrásoknak a saját fiókba való telepítéséhez Azure Automation rendszer.
Keresse meg a kívánt erőforrást, és kattintson az "Üzembe helyezés Azure Automation" gombra.

![PowerShell-galéria példa](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Egy másik technika, amely nemrég lett hozzáadva a Azure Portal lehetővé teszi új modulok lekért vagy meglévő modulok frissítését. Kattintson végig az Automation-fiók erőforrásán, az Assets (Eszközök) csempén, és végül a Modules (Modulok) csempén. A Katalógus tallózása ikonnal láthatja a modulok listáját a katalógusban, részletesen lefúrhat a részletekbe, és végső soron importálhatja az Automation-fiókjába. Ez nagyszerű módja annak, hogy a modulokat mindig naprakészen tartsunk. Az importálási funkció ellenőrzi a függőségeket más modulokkal, hogy semmi ne legyen szinkronizálva.

Van manuális módszer is, amely erőforrásonként csak egyszer használható, hacsak nem szeretné később frissíteni. A PowerShell-integrációs modulok írásának további információiért lásd: Integrációs modulok írása [a Azure Automation.](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

>[!NOTE]
>Egy Windows-számítógép PowerShell-integrációs moduljának mappastruktúrája kissé eltér a windowsos számítógép által várt Azure Automation. 

1. Telepítse [Windows Management Framework 5-ös](https://aka.ms/wmf5latest) (nem szükséges a Windows 10).

2. Telepítse az integrációs modult.

    ```azurepowershell-interactive
    Install-Module -Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Másolja a modulmappát a **c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** mappából egy ideiglenes mappába.

4. Minták és dokumentáció törlése a fő mappából.

5. Tömörítse a fő mappát, és nevezze el a ZIP-fájlt a mappa nevével.

6. Helyezze a ZIP-fájlt egy elérhető HTTP-helyre, például egy Azure Storage-fiók blobtárolójára.

7. Futtassa az alábbi parancsot.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME -ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

A mellékelt példa ezeket a lépéseket implementálja a cChoco és az xNetworking számára. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>4. lépés: A csomópont konfigurációjának hozzáadása a lekért kiszolgálóhoz

Nincs semmi különleges az első alkalommal, amikor először importálja a konfigurációt a lekért kiszolgálóra és fordítja le. Az ugyanannak a konfigurációnak minden későbbi importálásakor vagy fordításában ugyanúgy kell kinéznie. Minden alkalommal, amikor frissíti a csomagot, és éles környezetbe kell lek pusholnia, ezt a lépést a konfigurációs fájl helyességét követően kell megtennie – beleértve a csomag új verzióját is. Itt található a konfigurációs **fájlISVBoxConfig.ps1:**

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Itt található **azNew-ConfigurationScript.ps1** szkript (az Az modul használatára módosítva):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published -Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Ezek a lépések egy **új, ISVBoxConfig.isvbox** nevű csomópont-konfigurációt eredményeznek a lekért kiszolgálón. A csomópont konfigurációjának neve a következő: `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>5. lépés: Csomag metaadatainak létrehozása és karbantartása

A csomagtárban található összes csomaghoz szüksége lesz egy Nuspecre, amely leírja azt. Ezt a NuGet-kiszolgálón kell lefordítani és tárolni. Ezt a folyamatot itt [ismertetjük.](https://docs.nuget.org/create/creating-and-publishing-a-package) 

A MyGet.org  NuGet-kiszolgálóként is használható. Ezt a szolgáltatást megvásárolhatja, de ez egy ingyenes alapszintű termékváltozat. A [NuGet oldalon](https://www.nuget.org/)útmutatást talál a saját NuGet-kiszolgálójának a privát csomagokhoz való telepítéséhez.

## <a name="step-6-tie-it-all-together"></a>6. lépés: Az összes összekapcsolás

Minden alkalommal, amikor egy verzió megfelel a minőségbiztosítási rendszernek, és jóváhagyják az üzembe helyezést, a csomag létrejön, és a nuspec és az nupkg frissül, és üzembe lesz adva a NuGet-kiszolgálón. A konfigurációt (4. lépés) is frissíteni kell az új verziószámmal való megegyezéshez. Ezután el kell küldeni a lekért kiszolgálónak, és le kell fordítani.

Ettől a ponttól az adott konfigurációtól függő virtuális gépeken kell lekérte és telepítenie a frissítést. Ezek a frissítések egyszerűek – csak egy-két sor a PowerShellből. Az Azure DevOpsban ezek némelyike olyan buildfeladatokbe van beágyazva, amelyek egy buildben összefűzve egymáshoz vannak láncolva. Ez [a cikk](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) további részleteket tartalmaz. Ez a [GitHub-adattár](https://github.com/Microsoft/vso-agent-tasks) az elérhető buildfeladatokat részletezi.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Azure Automation DSC áttekintése](automation-dsc-overview.md)
* [Gépek bevezetése a DSC Azure Automation felügyeletéhez](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Következő lépések

- Áttekintésért lásd a Azure Automation State Configuration [áttekintését.](automation-dsc-overview.md)
- A szolgáltatás használatának első lépésekhez lásd: [Első lépések a Azure Automation State Configuration.](automation-dsc-getting-started.md)
- A DSC-konfigurációk célcsomópontokhoz való hozzárendelése érdekében való fordításával kapcsolatos további információkért lásd: DSC-konfigurációk fordítása a [Azure Automation State Configuration.](automation-dsc-compile.md)
- A PowerShell-parancsmagok referenciáiért lásd: [Az.Automation.](/powershell/module/az.automation)
- Díjszabási információkért lásd: [Azure Automation State Configuration díjszabása.](https://azure.microsoft.com/pricing/details/automation/)
