---
title: A kívánt állapot konfigurálása az Azure-hoz – áttekintés
description: Megtudhatja, hogyan használhatja a Microsoft Azure bővítmény kezelőjét a PowerShell kívánt állapotának konfigurálásához (DSC). A cikk az előfeltételeket, az architektúrát és a parancsmagokat tartalmazza.
services: virtual-machines
documentationcenter: ''
author: mgoedtel
manager: evansma
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 07/13/2020
ms.author: magoedte
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: dcdc325633aff5ab828cb1c82f4bb2d8becee967
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560038"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Az Azure Desired State Configuration-bővítménykezelő bemutatása

Az Azure-beli virtuálisgép-ügynök és a hozzá tartozó bővítmények Microsoft Azure infrastrukturális szolgáltatások részét képezik. A virtuálisgép-bővítmények olyan szoftver-összetevők, amelyek kibővítik a virtuális gépek funkcióit, és egyszerűbbé teszik a különböző VM

Az Azure desired State Configuration (DSC) bővítmény elsődleges használati esete a virtuális gép beindítása a [Azure Automation State Configuration (DSC) szolgáltatásba](../../automation/automation-dsc-overview.md).
A szolgáltatás olyan [előnyöket](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) biztosít, amelyek magukban foglalják a virtuális gépek konfigurációjának folyamatos felügyeletét és az egyéb operatív eszközökkel való integrációt, például az Azure-figyelést.
Ha a bővítmény használatával regisztrálja a virtuális gépeket a szolgáltatásban, rugalmas megoldást kínál, amely még az Azure-előfizetések között is működik.

A DSC-bővítményt a Automation DSC szolgáltatástól függetlenül is használhatja.
Ez azonban csak a virtuális gépre küldi a konfigurációt.
A virtuális gépen kívül nem érhető el folyamatos jelentéskészítés.

Ez a cikk mindkét forgatókönyvről tartalmaz információkat: a DSC-bővítmény használata az Automation bevezetéséhez, valamint a DSC-bővítmény használata a konfigurációk virtuális gépekhez való hozzárendeléséhez az Azure SDK használatával.

## <a name="prerequisites"></a>Előfeltételek

- **Helyi gép**: az Azure virtuálisgép-bővítmény használatához a Azure Portal vagy a Azure PowerShell SDK-t kell használnia.
- **Vendég ügynök**: a DSC-konfiguráció által KONFIGURÁLT Azure virtuális gépnek olyan operációs rendszernek kell lennie, amely támogatja a Windows Management FRAMEWORK (WMF) 4,0-es vagy újabb verzióját. A támogatott operációsrendszer-verziók teljes listájáért tekintse meg a [DSC-bővítmény verziójának előzményeit](../../automation/automation-dsc-extension-history.md).

## <a name="terms-and-concepts"></a>Feltételek és fogalmak

Ez az útmutató a következő fogalmak ismeretét feltételezi:

- **Configuration**: egy DSC-konfigurációs dokumentum.
- **Csomópont**: a DSC-konfiguráció célja. Ebben a dokumentumban a *csomópont* mindig egy Azure-beli virtuális gépre hivatkozik.
- **Konfigurációs** adatfájl: olyan. psd1-fájl, amely egy konfiguráció környezeti adattal rendelkezik.

## <a name="architecture"></a>Architektúra

Az Azure DSC bővítmény az Azure virtuálisgép-ügynök keretrendszerét használja az Azure-beli virtuális gépeken futó DSC-konfigurációk továbbítására, meghozatalára és jelentésére. A DSC-bővítmény elfogadja a konfigurációs dokumentumot és a paraméterek egy készletét. Ha nincs megadva fájl, a rendszer egy [alapértelmezett konfigurációs parancsfájlt](#default-configuration-script) ágyaz be a bővítménybe. Az alapértelmezett konfigurációs parancsfájl csak a metaadatok [helyi Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)való beállítására szolgál.

Ha a bővítményt első alkalommal hívja meg, a a következő logika használatával telepíti a WMF-verziót:

- Ha az Azure-beli VM operációs rendszer Windows Server 2016, akkor nem történik művelet. A Windows Server 2016 már telepítve van a PowerShell legújabb verziója.
- Ha a **wmfVersion** tulajdonság meg van adva, a WMF verziója telepítve van, kivéve, ha ez a verzió nem kompatibilis a virtuális gép operációs rendszerével.
- Ha nem ad meg **wmfVersion** -tulajdonságot, a rendszer a WMF legújabb verzióját telepíti.

A WMF telepítéséhez újraindítás szükséges. Az újraindítás után a bővítmény letölti a **modulesUrl** tulajdonságban megadott. zip fájlt, ha meg van adva. Ha ez a hely az Azure Blob Storage-ban található, megadhat egy SAS-tokent a **sasToken** tulajdonságban a fájl eléréséhez. Miután letöltötte és kicsomagolta a. zip fájlt, a **configurationFunction** -ben definiált konfigurációs függvény egy. mof ([Managed Object Format](/windows/win32/wmisdk/managed-object-format--mof-)) fájl létrehozásához fut. A bővítmény Ezután `Start-DscConfiguration -Force` a generált. MOF fájllal fut. A bővítmény rögzíti a kimenetet, és az Azure status channelbe írja azt.

### <a name="default-configuration-script"></a>Alapértelmezett konfigurációs parancsfájl

Az Azure DSC bővítmény olyan alapértelmezett konfigurációs parancsfájlt tartalmaz, amelyet a virtuális gép a Azure Automation DSC szolgáltatásba való bevezetéséhez kíván használni. A parancsfájl paramétereinek igazítása a [helyi Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)konfigurálható tulajdonságaival történik. A parancsfájl paramétereinek megtekintéséhez tekintse meg az [alapértelmezett konfigurációs parancsfájlt](dsc-template.md#default-configuration-script) a [kívánt állapotú konfigurációs bővítménnyel Azure Resource Manager sablonokkal](dsc-template.md). A teljes parancsfájl esetében tekintse meg az Azure rövid útmutató [sablonját a githubon](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Információk a Azure Automation State Configuration (DSC) szolgáltatással való regisztráláshoz

Ha a DSC-bővítmény használatával regisztrálja a csomópontot az állapot-konfigurációs szolgáltatással, három értéket kell megadnia.

- RegistrationUrl – a Azure Automation fiók HTTPS-címe
- RegistrationKey – a csomópontok szolgáltatásban való regisztrálásához használt közös titok
- NodeConfigurationName – annak a csomópont-konfigurációnak (MOF) a neve, amelyet a szolgáltatástól a kiszolgálói szerepkör konfigurálásához szeretne lekérni

Ezek az információk megtekinthetők a Azure Portalban, vagy használhatja a PowerShellt is.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

A csomópont-konfiguráció neveként ellenőrizze, hogy a csomópont konfigurációja létezik-e az Azure-beli állapot konfigurációjában.  Ha nem, akkor a bővítmény üzembe helyezése hibát ad vissza.  Győződjön meg arról is, hogy a *csomópont-konfiguráció* nevét használja, és nem a konfigurációt.
A konfiguráció egy olyan parancsfájlban van definiálva, amely [a csomópont-konfiguráció (MOF-fájl) fordítására](../../automation/automation-dsc-compile.md)szolgál.
A név mindig a konfiguráció, majd egy `.` `localhost` adott számítógépnév és egy adott számítógép neve lesz.

## <a name="dsc-extension-in-resource-manager-templates"></a>DSC-bővítmény Resource Manager-sablonokban

A legtöbb esetben a Resource Manager-alapú telepítési sablonok a várt módon működnek a DSC bővítménnyel. További információt és példákat a DSC-bővítmény Resource Manager-alapú üzembe helyezési sablonokba való felvételéről a következő témakörben talál: a [kívánt állapot konfigurációs bővítménye Azure Resource Manager-sablonokkal](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>DSC-bővítmény PowerShell-parancsmagjai

A DSC-bővítmény kezeléséhez használt PowerShell-parancsmagok a legmegfelelőbbek az interaktív hibaelhárítási és adatgyűjtési helyzetekben. A parancsmagokkal a DSC-bővítmények üzembe helyezését, közzétételét és figyelését is elvégezheti. A DSC-bővítményhez tartozó parancsmagok még nem frissültek az [alapértelmezett konfigurációs parancsfájllal](#default-configuration-script)való működéshez.

A **publish-AzVMDscConfiguration** parancsmag egy konfigurációs fájlt vesz igénybe, megkeresi a függő DSC-erőforrásokat, majd létrehoz egy. zip fájlt. A. zip fájl tartalmazza a konfiguráció megalkotásához szükséges konfigurációs és DSC-erőforrásokat. A parancsmag a *-OutputArchivePath* paraméter használatával helyileg is létrehozhatja a csomagot. Ellenkező esetben a parancsmag közzéteszi a. zip-fájlt a blob Storage-ba, majd egy SAS-jogkivonattal biztosítja azt.

A parancsmag által létrehozott. ps1 konfigurációs parancsfájl a. zip fájlban található az archív mappa gyökerében. A modul mappája az erőforrások archív mappájába kerül.

A **set-AzVMDscExtension** parancsmag azokat a beállításokat adja meg, amelyekhez a PowerShell DSC-bővítményének szüksége van egy virtuálisgép-konfigurációs objektumra.

A **Get-AzVMDscExtension** parancsmag egy adott virtuális gép DSC-bővítményének állapotát kérdezi le.

A **Get-AzVMDscExtensionStatus** parancsmag lekérdezi a DSC-bővítmény kezelője által MEGhozott DSC-konfiguráció állapotát. Ez a művelet egyetlen virtuális gépen vagy virtuális gépek egy csoportjára is elvégezhető.

A **Remove-AzVMDscExtension** parancsmag egy adott virtuális gépről eltávolítja a bővítmény kezelőjét. Ez a parancsmag *nem* távolítja el a konfigurációt, nem távolítja el a WMF-t, vagy módosítja az alkalmazott beállításokat a virtuális gépen. Csak a bővítmény kezelőjét távolítja el.

A Resource Manager DSC bővítmény parancsmagokkal kapcsolatos fontos információk:

- Azure Resource Manager parancsmagok szinkronban vannak.
- Az *ResourceGroupName*, a *VMName*, a *ArchiveStorageAccountName*, a *Version* és a *Location* paraméterek mindegyike kötelező.
- A *ArchiveResourceGroupName* egy opcionális paraméter. Ezt a paramétert akkor adhatja meg, ha a Storage-fiók egy másik erőforráscsoporthoz tartozik, mint ahol a virtuális gép létre lett hozva.
- Az automatikus **frissítés** kapcsoló használatával automatikusan frissítheti a bővítmény kezelőjét a legújabb verzióra, ha az elérhető. Ez a paraméter azt eredményezheti, hogy a virtuális gépen újraindul a WMF új verziója.

### <a name="get-started-with-cmdlets"></a>Ismerkedés a parancsmagokkal

Az Azure DSC bővítmény a DSC-konfigurációs dokumentumokat használva közvetlenül konfigurálhatja az Azure-beli virtuális gépeket az üzembe helyezés során. Ez a lépés nem regisztrálja a csomópontot az Automation szolgáltatásban. A *csomópont nincs központilag* felügyelve.

Az alábbi példa egy konfiguráció egyszerű példáját mutatja be. Mentse a konfigurációt helyileg iisInstall.ps1.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

A következő parancsok a megadott virtuális gépen helyezik el a iisInstall.ps1 szkriptet. A parancsok a konfigurációt is végrehajtják, majd visszaküldik a jelentést az állapotról.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

Az Azure CLI használatával telepítheti a DSC-bővítményt egy meglévő virtuális gépre.

Windows rendszerű virtuális gépek esetén:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Linux rendszerű virtuális gépek esetén:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Azure Portal funkció

A DSC beállítása a portálon:

1. Nyissa meg a virtuális gépet.
2. A **BEÁLLÍTÁSOK** területen válassza a **Bővítmények** elemet.
3. Az új létrehozott oldalon válassza a **+ Hozzáadás** lehetőséget, majd válassza a **PowerShell kívánt állapotának konfigurálása** lehetőséget.
4. Kattintson a **Létrehozás** gombra a kiegészítő információ lap alján.

A portál a következő adatokat gyűjti össze:

- **Konfigurációs modulok vagy szkriptek**: Ez a mező kötelező (az űrlap nem frissült az [alapértelmezett konfigurációs parancsfájl](#default-configuration-script)esetében). A konfigurációs moduloknak és parancsfájloknak olyan. ps1 fájlra van szükségük, amely tartalmaz egy. ps1 konfigurációs parancsfájllal rendelkező. ps1 parancsfájlt vagy. zip fájlt. Ha. zip fájlt használ, az összes függő erőforrást szerepelnie kell a. zip modul mappáiban. A. zip-fájlt a Azure PowerShell SDK-ban található **publish-AzureVMDscConfiguration-OutputArchivePath** parancsmag használatával hozhatja létre. A rendszer feltölti a. zip-fájlt a felhasználói blob Storage-ba, és egy SAS-token védi.

- **Modul – a konfiguráció minősített neve**: több konfigurációs függvényt is hozzáadhat egy. ps1 fájlban. Adja meg a Configuration. ps1 parancsfájl nevét, majd a \\ konfigurációs függvény nevét. Ha például a. ps1 parancsfájl neve configuration.ps1, és a konfiguráció **IisInstall**, adja meg **configuration.ps1 \iisinstall**.

- **Konfigurációs argumentumok**: Ha a konfigurációs függvény argumentumokat fogad, adja meg őket a következő formátumban: **argumentName1 = érték1, argumentName2 = érték2**. Ez a formátum a PowerShell-parancsmagok vagy a Resource Manager-sablonok által elfogadott konfigurációs argumentumok eltérő formátuma.

- **Konfigurációs adatok PSD1 fájlja**: Ha a konfigurációban konfigurációs adatfájlra van szükség a alkalmazásban `.psd1` , ezzel a mezővel kiválaszthatja az adatfájlt, és feltöltheti a felhasználói blob-tárolóba. A konfigurációs adatfájlt egy SAS-jogkivonat védi a blob Storage-ban.

- **WMF-verzió**: a Windows Management FRAMEWORK (WMF) azon verzióját adja meg, amelyet telepíteni kell a virtuális gépre. Ha ezt a tulajdonságot a legújabbra állítja, a a WMF legújabb verzióját telepíti. Jelenleg a tulajdonság egyetlen lehetséges értéke 4,0, 5,0, 5,1 és Latest. Ezek a lehetséges értékek a frissítések tárgya. Az alapértelmezett érték a **legújabb**.

- **Adatgyűjtés**: meghatározza, hogy a bővítmény gyűjti-e a telemetria. További információ: az [Azure DSC bővítmény adatgyűjtése](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).

- **Verzió**: a telepítendő DSC-bővítmény verzióját adja meg. További információ a verziókról: a [DSC bővítmény korábbi verziói](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- **Alverzió automatikus frissítése**: Ez a mező a parancsmagok **AutoUpdate** kapcsolóját képezi le, és lehetővé teszi, hogy a bővítmény automatikusan frissítsen a legújabb verzióra a telepítés során. Az **Igen** utasítás utasítja a bővítmény kezelőjét a legújabb elérhető verzió használatára, és a **nem** kényszeríti a telepítésre megadott **verziót** . Ha a nem lehetőséget **választja, a nem érték nem** ugyanaz, mint a **nem** **gombra** .

## <a name="logs"></a>Naplók

A bővítmény naplófájljai a következő helyen tárolódnak: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Következő lépések

- A PowerShell DSC-vel kapcsolatos további információkért nyissa meg a [PowerShell Dokumentációs központját](/powershell/scripting/dsc/overview/overview).
- Vizsgálja [meg a DSC-bővítmény Resource Manager-sablonját](dsc-template.md).
- Ha további funkciókat szeretne kezelni a PowerShell DSC használatával, és további DSC-erőforrásokhoz, keresse fel a [PowerShell-galériát](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- A bizalmas paraméterek konfigurációkba való átadásával kapcsolatos részletekért lásd: [a hitelesítő adatok biztonságos kezelése a DSC-bővítmény kezelőjével](dsc-credentials.md).
