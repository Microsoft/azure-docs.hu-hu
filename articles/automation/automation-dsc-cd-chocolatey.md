---
title: Azure Automation folyamatos üzembe helyezésének beállítása a chocolatey-vel
description: Ebből a cikkből megtudhatja, hogyan állíthatja be a folyamatos üzembe helyezést az állapot-konfigurációval és a csokis csomagkezelő segítségével.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: bb5f7b5e8214bd3b04bd7b9544ab4bc589f6c4bf
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896325"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Folyamatos üzembe helyezés beállítása a Chocolatey segítségével

A DevOps világában számos eszköz segíti a folyamatos integrációs folyamat különböző pontjainak támogatását. Azure Automation az [állapot-konfiguráció](automation-dsc-overview.md) a DevOps csapatok által alkalmazható lehetőségek örvendetes új kiegészítése. 

A Azure Automation Microsoft Azure felügyelt szolgáltatása, amely lehetővé teszi különböző feladatok automatizálását a runbookok, a csomópontok és a megosztott erőforrások, például a hitelesítő adatok, az ütemtervek és a globális változók használatával. Azure Automation állapot-konfiguráció kiterjeszti ezt az automatizálási képességet, hogy tartalmazza a PowerShell desired State Configuration (DSC) eszközeit. Íme egy nagyszerű [Áttekintés](automation-dsc-overview.md).

Ez a cikk bemutatja, hogyan állítható be a folyamatos üzembe helyezés (CD) a Windows rendszerű számítógépekre. Egyszerűen kiterjesztheti a technikát úgy, hogy a szerepkörbe, például egy webhelyre, és onnan további szerepkörökre is szükség legyen a Windows rendszerű számítógépek számára.

![IaaS virtuális gépek folyamatos üzembe helyezése](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Magas szinten

Itt is van egy kicsit, de szerencsére két fő folyamatra bontható:

- Kód írása és tesztelése, majd telepítési csomagok létrehozása és közzététele a rendszer fő és másodlagos verzióihoz.
- Virtuális gépek létrehozása és kezelése, amelyek a csomagokat telepítik és hajtják végre.  

Ha mindkét alapvető folyamat teljesült, egyszerűen automatikusan frissítheti a csomagot a virtuális gépeken új verziók létrehozásakor és üzembe helyezésekor.

## <a name="component-overview"></a>Az összetevők áttekintése

A Package managerek, például az [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) jól ismertek a Linux világában, de nem annyira a Windows világában.
A [chocolatey](https://chocolatey.org/) egy ilyen dolog, és Scott Jancsi [blogja](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) a témakörben nagyszerű bevezetést tartalmaz. Dióhéjban a chocolatey lehetővé teszi, hogy a parancssor használatával telepítse a csomagokat egy központi tárházból egy Windows operációs rendszerre. Saját tárházat hozhat létre és kezelhet, a chocolatey pedig tetszőleges számú tárházból telepíthet csomagokat.

A [POWERSHELL DSC](/powershell/scripting/dsc/overview/overview) egy PowerShell-eszköz, amely lehetővé teszi, hogy deklarálja a gép kívánt konfigurációját. Ha például azt szeretné, hogy a chocolatey telepítve legyen, telepítette az IIS-t, a 80-es portot, valamint a webhelyhez tartozó 1.0.0-verziót, a DSC helyi Configuration Manager (LCD) implementálja ezt a konfigurációt. A DSC lekérési kiszolgáló a gépek konfigurációinak tárházát tárolja. Az egyes gépeken lévő LCD ChipOnGlas rendszeresen ellenőrzi, hogy a konfigurációja megfelel-e a tárolt konfigurációnak. Vagy jelenthet állapotot, vagy megkísérli a gép visszahelyezését a tárolt konfigurációval való összehangolásra. A lekéréses kiszolgáló tárolt konfigurációjának szerkesztésével megadhatja, hogy a gép vagy a gépek készlete igazodjon a módosított konfigurációhoz.

A DSC-erőforrás olyan programkód modulja, amely adott képességekkel rendelkezik, például hálózatkezelés, Active Directory vagy SQL Server kezelésére. A chocolatey DSC-erőforrás tudja, hogyan férhet hozzá egy NuGet-kiszolgálóhoz (többek között), csomagokat tölthet le, csomagokat telepíthet stb. A [PowerShell-Galéria](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)számos más DSC-erőforrással rendelkezik. Ezeket a modulokat az Azure Automation állapot-konfiguráció lekérési kiszolgálójára telepíti a konfigurációk általi használatra.

A Resource Manager-sablonok deklaratív módszert biztosítanak az infrastruktúra (például hálózatok, alhálózatok, hálózati biztonság és útválasztás, terheléselosztó, hálózati adapterek, virtuális gépek stb.) létrehozásához. Íme egy [cikk](../azure-resource-manager/management/deployment-models.md) , amely összehasonlítja a Resource Manager-alapú üzemi modellt (deklaratív) az Azure Service Management (ASM vagy klasszikus) üzembe helyezési modellel (kötelező). Ez a cikk az alapvető erőforrás-szolgáltatókkal kapcsolatos vitát tartalmazza: számítás, tárolás és hálózat.

A Resource Manager-sablonok egyik kulcsfontosságú funkciója, hogy a virtuálisgép-bővítményt az üzembe helyezett virtuális gépre telepítse. A virtuálisgép-bővítmény speciális képességekkel rendelkezik, mint például az egyéni parancsfájlok futtatása, a víruskereső szoftver telepítése és a DSC konfigurációs parancsfájl futtatása. Számos más típusú virtuálisgép-bővítmény van.

## <a name="quick-trip-around-the-diagram"></a>Gyors utazás a diagram körül

A lap elejétől kezdve megírhatja a kódot, felépítheti, tesztelheti, majd létrehozhatja a telepítőcsomagot. A chocolatey különféle típusú telepítési csomagokat képes kezelni, például MSI, MSU vagy ZIP. A PowerShell a tényleges telepítést is lehetővé teszi, ha a csokis natív képességei nem naprakészek. Helyezze a csomagot egy olyan helyre, amely elérhető – egy csomag tárháza. Ez a használati példa egy nyilvános mappát használ egy Azure Blob Storage-fiókban, de bárhol lehet. A chocolatey natív módon működik a NuGet-kiszolgálókkal, és néhány más, a csomagok metaadatainak kezeléséhez. [Ez a cikk](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) a beállításokat ismerteti. A használati példa a NuGet-t használja. A Nuspec a csomagjaival kapcsolatos metaadatok. A Nuspec adatai egy NuPkg vannak lefordítva, és egy NuGet-kiszolgálón tárolódnak. Ha a konfiguráció név alapján kéri a csomagokat, és egy NuGet-kiszolgálóra hivatkozik, a virtuális gépen található chocolatey DSC-erőforrás megragadja a csomagot, és telepíti azt. A csomagok egy adott verzióját is kérheti.

A kép bal alsó részén van egy Azure Resource Manager sablon. Ebben a használati példában a virtuálisgép-bővítmény regisztrálja a virtuális gépet a Azure Automation állapot-konfiguráció lekérési kiszolgáló csomópontként. A konfigurációt kétszer tárolja a lekérési kiszolgáló: egyszer egyszerű szövegként, valamint egy MOF-fájlként lefordított módon. A Azure Portal a MOF a csomópont-konfigurációt jelképezi, és nem egy egyszerű konfigurációt. Ez a csomóponthoz társított összetevő, így a csomópont fogja tudni a konfigurációját. Az alábbi részletek azt mutatják be, hogyan rendelhető hozzá a csomópont-konfiguráció a csomóponthoz.

A Nuspec létrehozása, fordítása és tárolása egy NuGet-kiszolgálón kis dolog. A virtuális gépek már felügyelet alatt állnak. 

A folyamatos üzembe helyezés következő lépéseként egyszerre kell beállítani a lekérési kiszolgálót, regisztrálnia kell a csomópontjait, és a kezdeti konfigurációt a kiszolgálón kell létrehoznia és tárolnia. A csomagok frissítésekor és üzembe helyezése a tárházban csak szükség szerint kell frissítenie a konfigurációt és a csomópont-konfigurációt a lekérési kiszolgálón.

Ha nem a Resource Manager-sablonnal kezdődik, ez rendben van. A virtuális gépek a lekérési kiszolgálóval való regisztrálásához PowerShell-parancsok használhatók. További információ: [bevezetési gépek felügyeletre Azure Automation állapot-konfiguráció alapján](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Tudnivalók a használati példáról

A cikkben szereplő használati példa egy általános Windows Server 2012 R2 rendszerképet tartalmazó virtuális géppel kezdődik az Azure-katalógusból. Bármely tárolt rendszerképből elindíthatja, majd a DSC-konfigurációval is megkezdheti a csípését.
Azonban a rendszerképbe besütött konfiguráció módosítása sokkal nehezebb, mint a DSC-t használó konfiguráció dinamikus frissítése.

A virtuális gépekhez nem szükséges Resource Manager-sablont és a virtuálisgép-bővítményt használni. A virtuális gépeknek nem kell az Azure-ban lenniük a CD-felügyelet alatt. Mindez szükséges ahhoz, hogy a chocolatey telepítve legyen, és a virtuális merevlemezen legyen konfigurálva, hogy a lekéréses kiszolgáló hol található.

Ha éles virtuális gépen frissít egy csomagot, a frissítés telepítése közben el kell végeznie a virtuális gép elforgatását. Ez a művelet igen változó. Ha például egy virtuális gép egy Azure Load Balancer mögött található, egyéni mintavételt is hozzáadhat. A virtuális gép frissítésekor a mintavételi végpont 400-as értéket ad vissza. A módosítást okozó csípés lehet a konfiguráción belül, ahogy a csípés visszaválthat a 200-re, ha a frissítés befejeződött.

A használati példa teljes forrása ebben a [Visual Studio-projektben](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) a githubon.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>1. lépés: a lekérési kiszolgáló és az Automation-fiók beállítása

Hitelesített ( `Connect-AzAccount` ) PowerShell parancssorban: (eltarthat néhány percig, amíg a lekérési kiszolgáló be van állítva)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Az Automation-fiókját a következő régiókba helyezheti el (más néven helyként): USA 2. keleti régiója, USA déli középső régiója, US Gov Virginia, Nyugat-Európa, Délkelet-Ázsia, Kelet-Japán, Közép-India és Délkelet-Ausztrália, Közép-Kanada, Észak-Európa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>2. lépés: a virtuálisgép-bővítmény csípésének tétele a Resource Manager-sablonban

A virtuális gépek regisztrálásának részletei (a PowerShell DSC virtuálisgép-bővítmény használatával), amely ebben az [Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)-útmutatóban van megadva.
Ez a lépés regisztrálja az új virtuális gépet a lekérési kiszolgálóval az állapot konfigurációs csomópontjainak listájában. A regisztráció részeként meg kell adni a csomópontra alkalmazni kívánt csomópont-konfigurációt. Ennek a csomópont-konfigurációnak még nem kell megtörténnie a lekérési kiszolgálón, ezért a 4. lépés az első alkalommal történik. A 2. lépésben azonban meg kell határoznia a csomópont nevét és a konfiguráció nevét. Ebben a használati példában a csomópont "isvbox", a konfiguráció pedig "ISVBoxConfig". Így a csomópont-konfiguráció neve (DeploymentTemplate.json) a következő: "ISVBoxConfig. isvbox".

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>3. lépés: a szükséges DSC-erőforrások hozzáadása a lekérési kiszolgálóhoz

A PowerShell-galéria a DSC-erőforrások Azure Automation-fiókba való telepítéséhez lett kialakítva.
Navigáljon a kívánt erőforráshoz, és kattintson a "központi telepítés Azure Automation" gombra.

![PowerShell-galéria példa](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

A Azure Portal nemrégiben hozzáadott újabb módszer lehetővé teszi az új modulok lekérését vagy a meglévő modulok frissítését. Kattintson az Automation-fiók erőforrására, az eszközök csempére, végül pedig a modulok csempére. A tallózási katalógus ikon segítségével megtekintheti a katalógusban található modulok listáját, részletezheti a részleteket, és végül importálhatja az Automation-fiókjába. Ez nagyszerű módja annak, hogy a modulok naprakészek legyenek időről időre. Az importálási funkció pedig más modulokkal is ellenőrzi a függőségeket, így biztosítva, hogy semmi sincs szinkronban.

A manuális megközelítés is csak egyszer használható erőforrásként, kivéve, ha később szeretné frissíteni. A PowerShell-integrációs modulok létrehozásával kapcsolatos további információkért lásd: [integrációs modulok készítése a Azure Automationhoz](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>A Windows rendszerű számítógépek PowerShell-integrációs moduljának mappastruktúrát kissé eltér a Azure Automation által várt mappastruktúrát. 

1. Telepítse a [Windows Management Framework V5](https://aka.ms/wmf5latest) -et (a Windows 10 rendszerhez nem szükséges).

2. Telepítse az integrációs modult.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Másolja a Module mappát a **C:\Program Files\WindowsPowerShell\Modules\MODULE-Name** egy ideiglenes mappába.

4. Törölje a mintákat és a dokumentációt a fő mappából.

5. Zip a fő mappa, a ZIP-fájl elnevezése a mappa nevével.

6. Helyezze a ZIP-fájlt egy elérhető HTTP-helyre, például egy Azure Storage-fiók blob Storage-fiókjába.

7. Futtassa az alábbi parancsot.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

A példában szereplő példa a cChoco és a xNetworking vonatkozó lépéseket hajtja végre. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>4. lépés: a csomópont-konfiguráció hozzáadása a lekérési kiszolgálóhoz

A konfigurációnak a lekéréses kiszolgálóra való első importálásakor és a fordításakor semmi nem különleges. Ugyanaz a konfiguráció minden későbbi importálása vagy összeállítása pontosan ugyanaz lesz. Minden alkalommal, amikor frissíti a csomagot, és le kell küldenie az éles környezetbe, ezt a lépést a konfigurációs fájl helyességének biztosítása után hajtja végre – beleértve a csomag új verzióját is. A konfigurációs fájl **ISVBoxConfig.ps1**:

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

Itt látható a **New-ConfigurationScript.ps1** szkript (az az modul használatára lett módosítva):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Ezek a lépések egy új, **ISVBoxConfig. isvbox** nevű csomópont-konfigurációt eredményeznek a lekérési kiszolgálón. A csomópont-konfiguráció neve a következőképpen van felépítve: `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>5. lépés: csomag metaadatainak létrehozása és karbantartása

A Package repositoryba helyezett minden csomaghoz szüksége van egy Nuspec, amely leírja azt. A NuGet-kiszolgálón kell összeállítani és tárolni. Ez a folyamat [itt](https://docs.nuget.org/create/creating-and-publishing-a-package)olvasható. 

A **MyGet.org** NuGet-kiszolgálóként is használható. Megvásárolhatja ezt a szolgáltatást, de neked egy ingyenes kezdő SKU-t is használhat. A [NuGet](https://www.nuget.org/)-on a saját NuGet-kiszolgáló telepítésére vonatkozó útmutatást talál a privát csomagokhoz.

## <a name="step-6-tie-it-all-together"></a>6. lépés: az összes összevonása

Minden alkalommal, amikor egy verzió megfelel a QA-nek, és jóváhagyja az üzembe helyezést, a rendszer létrehozza a csomagot, és a nuspec és nupkg frissíti és telepíti a NuGet-kiszolgálóra. A konfigurációt (4. lépés) is frissíteni kell, hogy elfogadja az új verziószámot. Ezt követően el kell juttatni a lekérési kiszolgálónak, és le kell fordítani.

Ettől kezdve a konfigurációtól függ, hogy a virtuális gépek a frissítés lekéréséhez és telepítéséhez szükségesek-e. Ezek a frissítések egyszerűek – csak egy sor vagy két PowerShell. Az Azure DevOps esetében ezek némelyike olyan felépítési feladatokbe van ágyazva, amelyek összekapcsolhatók egy összeállításban. Ez a [cikk](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) további részleteket tartalmaz. Ez a [GitHub](https://github.com/Microsoft/vso-agent-tasks) -tárház a rendelkezésre álló Build-feladatokat részletezi.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Azure Automation DSC – áttekintés](automation-dsc-overview.md)
* [Bevezetési gépek a Azure Automation DSC általi felügyelethez](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Azure Automation állapot konfigurációjának áttekintése](automation-dsc-overview.md).
- A szolgáltatás használatának megkezdéséhez tekintse meg az [Azure Automation állapot konfigurációjának első lépéseivel foglalkozó](automation-dsc-getting-started.md)témakört.
- Ha szeretne többet megtudni a DSC-konfigurációk fordításáról, hogy hozzá lehessen rendelni őket a célcsoportokhoz, tekintse meg [a DSC-konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md)című témakört.
- A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
