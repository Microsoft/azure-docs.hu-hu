---
title: Egyéni Azure-szkriptek bővítménye Windowshoz
description: A Windowsos virtuális gépek konfigurációs feladatainak automatizálása az egyéni szkriptek bővítményének használatával
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 08/31/2020
ms.openlocfilehash: 13b4c4ef50ea37cabe30474d339acb19176cef97
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553901"
---
# <a name="custom-script-extension-for-windows"></a>Egyéniszkript-bővítmény Windows rendszerre

Az egyéni szkriptek bővítménye letölti és végrehajtja a parancsfájlokat az Azure Virtual Machines szolgáltatásban. Ez a bővítmény az üzembe helyezési konfiguráció, a Szoftvertelepítés vagy bármely egyéb konfigurációs vagy felügyeleti feladat után hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az egyéni szkriptek bővítménye Azure Resource Manager-sablonokkal integrálódik, és az Azure CLI, a PowerShell, a Azure Portal vagy az Azure virtuális gép REST API használatával futtatható.

Ez a dokumentum részletesen ismerteti, hogyan használhatók az egyéni szkriptek bővítmények a Azure PowerShell modullal, Azure Resource Manager sablonokkal és a Windows rendszerekre vonatkozó hibaelhárítási lépésekkel.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]  
> Ne használja az egyéni szkriptek bővítményét, hogy ugyanazzal a virtuális géppel, mint a paraméterrel Update-AzVM fusson, mivel az megvárja magát.  

### <a name="operating-system"></a>Operációs rendszer

A Windowshoz készült egyéni parancsfájl-bővítmény az OSs támogatott bővítményét fogja futtatni;

### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>Parancsfájl helye

Beállíthatja, hogy a bővítmény az Azure Blob Storage hitelesítő adatait használja az Azure Blob Storage eléréséhez. A parancsfájl helye bárhol lehet, feltéve, hogy a virtuális gép átirányítja az adott végpontra, például a GitHubra vagy egy belső fájlkiszolgálóra.

### <a name="internet-connectivity"></a>Internetkapcsolat

Ha külsőleg le kell töltenie egy parancsfájlt, például a GitHubról vagy az Azure Storage-ból, akkor további tűzfal-és hálózati biztonsági csoportok portjait kell megnyitnia. Ha például a szkript az Azure Storage-ban található, az Azure NSG Service-címkék használatával engedélyezheti a hozzáférést a [tároláshoz](../../virtual-network/network-security-groups-overview.md#service-tags).

Vegye figyelembe, hogy a CustomScript-bővítmény nem tudja megkerülni a tanúsítványok érvényesítését. Tehát ha egy biztonságos helyről tölt le, például:. önaláírt tanúsítvány esetén előfordulhat, hogy a következőhöz hasonló hibák léptek fel: *"a távoli tanúsítvány érvénytelen az érvényesítési eljárás szerint"*. Győződjön meg arról, hogy a tanúsítvány megfelelően van telepítve a virtuális gép *"megbízható legfelső szintű hitelesítésszolgáltatók"* tárolójába.

Ha a parancsfájl egy helyi kiszolgálón található, akkor továbbra is szükség lehet további tűzfal-és hálózati biztonsági csoport portjainak megnyitására.

### <a name="tips-and-tricks"></a>Tippek és trükkök

* Ennek a bővítménynek a legnagyobb meghibásodási aránya a parancsfájl szintaktikai hibái miatt történik, és a parancsfájl futtatása hiba nélkül fut, és a parancsfájlba további naplózást is végez, így könnyebben megtalálhatja a sikertelenség helyét.
* Idempotens parancsfájlokat írhat. Ez biztosítja, hogy ha véletlenül újra futnak, a rendszer nem fog változásokat okozni.
* Gondoskodjon arról, hogy a szkriptek futtatásához ne legyen szükség felhasználói beavatkozásra.
* A szkript futtatásának engedélyezett időtartama 90 perc. Ha egy szkript ennél tovább fut, az a bővítmény üzembe helyezésének meghiúsulását eredményezi.
* A szkript ne váltson ki újraindításokat, mert ez a művelet problémákat okoz a telepítés alatt álló többi bővítményben. Újraindítás után a bővítmény nem fog tovább működni.
* Ha olyan szkripttel rendelkezik, amely újraindítást eredményez, majd telepíti az alkalmazásokat, és futtatja a parancsfájlokat, ütemezheti az újraindítást egy Windows ütemezett feladattal, vagy használhat olyan eszközöket, mint a DSC, a Chef vagy a Puppet Extensions.
* Nem ajánlott olyan parancsfájlt futtatni, amely a virtuálisgép-ügynök leállítását vagy frissítését eredményezi. Ez a bővítmény átmeneti állapotba hagyható, ami időtúllépést eredményez.
* A bővítmény egy szkriptet csak egyszer futtat. Ha egy szkriptet minden indításkor futtatni szeretne, létre kell hoznia egy ütemezett Windows-feladatot a bővítménnyel.
* Ha ütemezni szeretné, hogy mikor fusson egy szkript, hozzon létre egy ütemezett Windows-feladatot a bővítménnyel.
* Amikor a szkript fut, az Azure Portalon vagy a CLI-n a bővítmény „átmeneti” állapotát fogja látni. Ha gyakoribb állapotfrissítést szeretne egy futó szkripthez, létre kell hoznia a saját megoldását.
* Az egyéni szkriptek bővítménye nem támogatja natív módon a proxykiszolgálót, azonban használhat olyan fájlátviteli eszközt, amely támogatja a parancsfájlban lévő proxykiszolgálót, például a *Meghívási Webkérést*
* Vegye figyelembe, hogy vannak olyan nem alapértelmezett könyvtárhelyek, amelyekre a szkriptjei és a parancsai támaszkodhatnak, és amelyek rendelkeznek a helyzet kezeléséhez szükséges logikával.
* Az egyéni szkriptek bővítménye a LocalSystem fiókban fog futni.
* Ha azt tervezi, hogy a *storageAccountName* és a *storageAccountKey* tulajdonságot használja, ezeknek a tulajdonságoknak közös elhelyezésű kell lenniük a *protectedsettingsfromkeyvault*.

## <a name="extension-schema"></a>Bővítményséma

Az egyéni szkriptek bővítményének konfigurációja meghatározza a parancsfájl helyét és a futtatandó parancsot. Ezt a konfigurációt a konfigurációs fájlokban tárolhatja, megadhatja a parancssorban, vagy megadhatja egy Azure Resource Manager sablonban.

A bizalmas adatokat egy védett konfigurációban tárolhatja, amely titkosítva van, és csak a virtuális gépen végez visszafejtést. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs titkos kódokat, például jelszót tartalmaz.

Ezeket az elemeket bizalmas adatokként kell kezelni, és meg kell adni a bővítmények által védett beállítások konfigurációjában. Az Azure virtuálisgép-bővítmény védett beállítási adatbeállításai titkosítottak, és csak a célként megadott virtuális gépen lettek visszafejtve.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> a managedIdentity tulajdonság **nem** használható a storageAccountName vagy a storageAccountKey tulajdonsággal együtt.

> [!NOTE]
> Egy adott időpontban csak egy bővítmény telepíthető egy virtuális gépre, így az azonos virtuális géphez tartozó Resource Manager-sablonban kétszer is megadható az egyéni parancsfájl.

> [!NOTE]
> Ezt a sémát a VirtualMachine-erőforráson belül vagy önálló erőforrásként is használhatja. Az erőforrás nevének "virtualMachineName/extensionName" formátumúnak kell lennie, ha ez a bővítmény önálló erőforrásként van használatban az ARM-sablonban.

### <a name="property-values"></a>Tulajdonságértékek

| Name | Érték/példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| közzétevő | Microsoft.Compute | sztring |
| típus | CustomScriptExtension | sztring |
| typeHandlerVersion | 1.10 | int |
| fileUris (például) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| időbélyeg (például) | 123456789 | 32 bites egész szám |
| commandToExecute (például) | PowerShell – ExecutionPolicy korlátozás nélküli fájl configure-music-app.ps1 | sztring |
| storageAccountName (például) | examplestorageacct | sztring |
| storageAccountKey (például) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | sztring |
| managedIdentity (például) | {} vagy {"clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232"} vagy {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | JSON-objektum |

>[!NOTE]
>Ezek a tulajdonságok nevei megkülönböztetik a kis-és nagybetűket. Az üzembe helyezési problémák elkerüléséhez használja az itt látható neveket.

#### <a name="property-value-details"></a>Tulajdonság értékének részletei

* `commandToExecute`: (**kötelező**, karakterlánc) a belépési pont parancsfájlját végre kell hajtani. Akkor használja ezt a mezőt, ha a parancs titkos kódokat (például jelszavakat) tartalmaz, vagy ha a fileUris bizalmasak.
* `fileUris`: (opcionális, karakterlánc-tömb) a letölteni kívánt fájl (ok) URL-címei.
* `timestamp` (opcionális, 32 bites egész szám) Ez a mező csak a parancsfájl újrafuttatásának indítására használható a mező értékének módosításával.  Bármely egész érték elfogadható; csak az előző értéktől eltérő lehet.
* `storageAccountName`: (nem kötelező, karakterlánc) a Storage-fiók neve. Ha tárolási hitelesítő adatokat ad meg, az összes `fileUris` URL-címet az Azure-Blobok számára kell megadni.
* `storageAccountKey`: (nem kötelező, karakterlánc) a Storage-fiók elérési kulcsa
* `managedIdentity`: (nem kötelező, JSON-objektum) a fájl (ok) letöltésének [felügyelt identitása](../../active-directory/managed-identities-azure-resources/overview.md)
  * `clientId`: (nem kötelező, karakterlánc) a felügyelt identitás ügyfél-azonosítója
  * `objectId`: (nem kötelező, karakterlánc) a felügyelt identitás objektum-azonosítója

A következő értékek a nyilvános vagy a védett beállításokban állíthatók be, a bővítmény elveti az összes olyan konfigurációt, ahol az alábbi értékek a nyilvános és a védett beállításokban is be vannak állítva.

* `commandToExecute`

A nyilvános beállítások használata hasznos lehet a hibakereséshez, de javasoljuk, hogy a védett beállításokat használja.

A nyilvános beállításokat a rendszer tiszta szövegként küldi el arra a virtuális gépre, amelyen a parancsfájl végre lesz hajtva.  A védett beállítások titkosítása csak az Azure-ban és a virtuális gépen ismert kulcs használatával történik. A rendszer elküldte a beállításokat a virtuális gépre, vagyis ha a beállítások titkosítva lettek, a virtuális gépen titkosítva lesznek. A titkosított értékek visszafejtéséhez használt tanúsítványt a virtuális gépen tárolja a rendszer, és a beállításokat (ha szükséges) a futtatáskor használja.

####  <a name="property-managedidentity"></a>Tulajdonság: managedIdentity
> [!NOTE]
> Ezt a **tulajdonságot** csak a védett beállításokban kell megadni.

A CustomScript (1,10-es verzió) támogatja a [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md) a fájl (ok) letöltéséhez a "fileUris" beállításban megadott URL-címekről. Lehetővé teszi a CustomScript számára az Azure Storage privát Blobok vagy tárolók elérését anélkül, hogy a felhasználónak olyan titkokat kellene átadnia, mint például az SAS-tokenek vagy a Storage

Ennek a funkciónak a használatához a felhasználónak hozzá kell adnia egy [rendszerhez rendelt](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) vagy [felhasználó által hozzárendelt](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) identitást a virtuális géphez vagy VMSS, ahol a CustomScript várhatóan fut, és [biztosítania kell a felügyelt identitás elérését az Azure Storage-tárolóhoz vagy-blobhoz](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Ha a rendszer által hozzárendelt identitást szeretné használni a cél virtuális gépen/VMSS, állítsa a "managedidentity" mezőt egy üres JSON-objektumra. 

> Példa:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Ha a felhasználó által hozzárendelt identitást szeretné használni a cél virtuális gépen/VMSS, konfigurálja a "managedidentity" mezőt az ügyfél-AZONOSÍTÓval vagy a felügyelt identitás objektum-azonosítójával.

> Angol nyelvű Példák:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> a managedIdentity tulajdonság **nem** használható a storageAccountName vagy a storageAccountKey tulajdonsággal együtt.

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. Az előző szakaszban részletezett JSON-séma használható Azure Resource Manager sablonban az egyéni szkriptek bővítmény futtatásához az üzembe helyezés során. Az alábbi példák azt mutatják be, hogyan használható az egyéni szkriptek bővítménye:

* [Oktatóanyag: Virtuális gépi bővítmények üzembe helyezése Azure Resource Manager-sablonokkal](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Kétszintű alkalmazás üzembe helyezése a Windowsban és az Azure SQL DB-ben](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>A PowerShell telepítése

A `Set-AzVMCustomScriptExtension` parancs használatával hozzáadhatja az egyéni parancsfájl-bővítményt egy meglévő virtuális géphez. További információ: [set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>További példák

### <a name="using-multiple-scripts"></a>Több parancsfájl használata

Ebben a példában három parancsfájlt használ a kiszolgáló létrehozásához. A **commandToExecute** meghívja az első parancsfájlt, majd a mások meghívásának lehetőségeit. Rendelkezhet például egy olyan főparancsfájllal, amely a megfelelő hibák kezelésével, naplózásával és állapotának kezelésével vezérli a végrehajtást. A parancsfájlok a helyi gépre lesznek letöltve a futtatáshoz. Például ha a `1_Add_Tools.ps1` `2_Add_Features.ps1`  `.\2_Add_Features.ps1` szkripthez hozzáadja a parancsot, és megismétli ezt a folyamatot a ben definiált többi parancsfájlhoz `$settings` .

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings `
    -ProtectedSettings $protectedSettings;
```

### <a name="running-scripts-from-a-local-share"></a>Parancsfájlok futtatása helyi megosztásból

Ebben a példában érdemes helyi SMB-kiszolgálót használni a parancsfájl helyéhez. Ehhez a **commandToExecute** kivételével semmilyen egyéb beállítást nem kell megadnia.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Egyéni parancsfájlok futtatása többször, a CLI használatával

Ha többször is futtatni szeretné az egyéni parancsfájl-bővítményt, akkor ezt a műveletet csak a következő feltételekkel hajthatja végre:

* A kiterjesztés **neve** paraméter megegyezik a bővítmény előző telepítésével.
* Módosítsa a konfigurációt, ellenkező esetben a parancs nem lesz újra végrehajtva. Hozzáadhat egy dinamikus tulajdonságot a parancshoz, például egy időbélyeget.

Azt is megteheti, hogy a [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) tulajdonság értéke **true (igaz**).

### <a name="using-invoke-webrequest"></a>Invoke-WebRequest használata

Ha a szkriptben a [meghívás-Webkérést](/powershell/module/microsoft.powershell.utility/invoke-webrequest) használja, meg kell adnia a paramétert, `-UseBasicParsing` különben a részletes állapot ellenőrzésekor a következő hibaüzenetet fogja kapni:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

A méretezési csoport egyéni parancsfájl-bővítményének üzembe helyezéséhez lásd: [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)

## <a name="classic-vms"></a>A klasszikus virtuális gépeket

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Az egyéni szkriptek klasszikus virtuális gépeken való üzembe helyezéséhez használhatja a Azure Portal vagy a klasszikus Azure PowerShell parancsmagokat.

### <a name="azure-portal"></a>Azure Portal

Navigáljon a klasszikus VM-erőforráshoz. Válassza a **bővítmények** lehetőséget a **Beállítások** területen.

Kattintson a **+ Hozzáadás** elemre, majd az erőforrások listájában válassza az **Egyéni szkriptek bővítmény** lehetőséget.

A **bővítmény telepítése** lapon válassza ki a helyi PowerShell-fájlt, és töltse ki az argumentumokat, és kattintson **az OK** gombra.

### <a name="powershell"></a>PowerShell

Használja a [set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) parancsmagot, amellyel hozzáadhatja az egyéni szkriptek bővítményét egy meglévő virtuális géphez.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok beolvashatók a Azure Portalból, és az Azure PowerShell modul használatával. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

A bővítmény kimenete a cél virtuális gép következő mappájában található fájlokra van naplózva.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

A megadott fájlok a cél virtuális gép következő mappájába lesznek letöltve.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

ahol a `<n>` decimális egész szám, amely változhat a bővítmény végrehajtásai között.  Az `1.*` érték megegyezik a bővítmény tényleges, aktuális `typeHandlerVersion` értékével.  Például a tényleges könyvtár lehet `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` .  

A parancs végrehajtásakor `commandToExecute` a bővítmény beállítja ezt a könyvtárat (például `...\Downloads\2` ) az aktuális munkakönyvtárként. Ez a folyamat lehetővé teszi a relatív elérési utak használatát a tulajdonságon keresztül letöltött fájlok megkereséséhez `fileURIs` . Példákért tekintse meg az alábbi táblázatot.

Mivel az abszolút letöltési útvonal az idő múlásával változhat, érdemes lehet relatív parancsfájl-/fájlelérési utakat választani a `commandToExecute` karakterláncban, amikor csak lehetséges. Például:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Az elérési út adatai az első URI-szegmens megtartása után a `fileUris` Tulajdonságok listáján letöltött fájlok számára.  Ahogy az alábbi táblázatban is látható, a letöltött fájlok a letöltési alkönyvtárakba vannak leképezve, hogy tükrözzék az értékek szerkezetét `fileUris` .  

#### <a name="examples-of-downloaded-files"></a>Példák a letöltött fájlokra

| URI a fileUris | Relatív letöltési hely | Abszolút letöltött hely <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> az abszolút könyvtár elérési útjai megváltoznak a virtuális gép élettartama során, de nem a CustomScript-bővítmény egyetlen végrehajtásán belül.

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Egy Azure-támogatási incidens is benyújtható. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
