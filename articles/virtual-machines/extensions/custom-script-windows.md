---
title: Egyéni Azure-szkriptbővítmény Windowshoz
description: Windows rendszerű virtuális gépek konfigurációs feladatainak automatizálása az egyéni szkriptbővítmény használatával
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 08/31/2020
ms.openlocfilehash: 6341e3abbf591d0e6e0395e17ccf15ec73a3ac43
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835451"
---
# <a name="custom-script-extension-for-windows"></a>Egyéniszkript-bővítmény Windows rendszerre

Az egyéni szkriptbővítmény szkripteket tölt le és hajt végre Azure-beli virtuális gépeken. Ez a bővítmény a telepítés utáni konfigurációhoz, a szoftvertelepítéshez, illetve egyéb konfigurációs vagy felügyeleti feladatokhoz hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az egyéni szkriptbővítmény integrálható Azure Resource Manager sablonokkal, és futtatható az Azure CLI, a PowerShell, Azure Portal vagy az Azure Virtual Machine REST API.

Ez a dokumentum részletesen bemutatja, hogyan használhatja az egyéni szkriptbővítményt az Azure PowerShell modullal, Azure Resource Manager sablonokkal, és részletesen bemutatja a Windows rendszerek hibaelhárítási lépéseit.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]  
> Ne használjon egyéni szkriptbővítményt a Update-AzVM a paraméterével azonos virtuális géphez való futtatásához, mivel az önmagában várakozik.  

### <a name="operating-system"></a>Operációs rendszer

A Windowshoz készült egyéni szkriptbővítmény a bővítmény által támogatott operációs rendszereken fog futni;

### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>Szkript helye

A bővítmény konfigurálható úgy, hogy az Azure Blob Storage hitelesítő adatait használja az Azure Blob Storage eléréséhez. A szkript helye bárhol lehet, amíg a virtuális gép az adott végponthoz, például a GitHubhoz vagy egy belső fájlkiszolgálóhoz irányítható.

### <a name="internet-connectivity"></a>Internetkapcsolat

Ha külsőleg kell letöltenie egy szkriptet, például a GitHubról vagy az Azure Storage-ból, további tűzfal- és hálózati biztonsági csoportportokat kell megnyitnia. Ha például a szkript az Azure Storage-ban található, engedélyezheti a hozzáférést [](../../virtual-network/network-security-groups-overview.md#service-tags)a Storage Azure NSG-szolgáltatáscímkéivel.

Vegye figyelembe, hogy a CustomScript-bővítmény nem tudja megkerülni a tanúsítványérvényesítést. Így ha biztonságos helyről, például egy önaírt tanúsítványt, előfordulhat, hogy az alábbihoz hasonló hibákat fog tartalmazni: "A távoli tanúsítvány érvénytelen *az érvényesítési eljárásnak megfelelően".* Győződjön meg arról, hogy a tanúsítvány megfelelően van telepítve a *virtuális gép "megbízható legfelső szintű hitelesítésszolgáltatók"* tárolójában.

Ha a szkript egy helyi kiszolgálón található, akkor előfordulhat, hogy további tűzfalat és hálózati biztonsági csoport portokat kell megnyitnia.

### <a name="tips-and-tricks"></a>Tippek és trükkök

* Ennek a bővítménynek a legnagyobb hibaarányát a szkript szintaktikai hibái okozza, tesztelje a szkript futását hiba nélkül, és helyezzen további naplózást a szkriptbe, hogy könnyebb legyen megtalálni a sikertelenséget.
* Idepotikus szkriptek írása. Ez biztosítja, hogy ha véletlenül újra futnak, az nem okoz rendszerváltozást.
* Gondoskodjon arról, hogy a szkriptek futtatásához ne legyen szükség felhasználói beavatkozásra.
* A szkript futtatásának engedélyezett időtartama 90 perc. Ha egy szkript ennél tovább fut, az a bővítmény üzembe helyezésének meghiúsulását eredményezi.
* A szkript ne váltson ki újraindításokat, mert ez a művelet problémákat okoz a telepítés alatt álló többi bővítményben. Újraindítás után a bővítmény nem fog tovább működni.
* Ha olyan szkriptje van, amely újraindítást okoz, majd alkalmazásokat telepít és szkripteket futtat, ütemezheti az újraindítást egy Ütemezett Windows-feladattal, vagy használhat olyan eszközöket, mint a DSC, a Chef vagy a Puppet bővítmény.
* Nem ajánlott olyan szkriptet futtatni, amely a virtuálisgép-ügynök leállítását vagy frissítését okozhatja. Ez átmeneti állapotban hagyhatja a bővítményt, ami időtúllépéshez vezet.
* A bővítmény egy szkriptet csak egyszer futtat. Ha egy szkriptet minden indításkor futtatni szeretne, létre kell hoznia egy ütemezett Windows-feladatot a bővítménnyel.
* Ha ütemezni szeretné, hogy mikor fusson egy szkript, hozzon létre egy ütemezett Windows-feladatot a bővítménnyel.
* Amikor a szkript fut, az Azure Portalon vagy a CLI-n a bővítmény „átmeneti” állapotát fogja látni. Ha gyakoribb állapotfrissítést szeretne egy futó szkripthez, létre kell hoznia a saját megoldását.
* Az egyéni szkriptbővítmény nem támogatja natív módon a proxykiszolgálókat, de használhat olyan fájlátviteli eszközt, amely támogatja a proxykiszolgálókat a szkriptben, például *Invoke-WebRequest*
* Vegye figyelembe, hogy vannak olyan nem alapértelmezett könyvtárhelyek, amelyekre a szkriptjei és a parancsai támaszkodhatnak, és amelyek rendelkeznek a helyzet kezeléséhez szükséges logikával.
* Az egyéni szkriptbővítmény a LocalSystem-fiók alatt fog futni
* Ha a *storageAccountName* és a *storageAccountKey* tulajdonságokat tervezi használni, ezeket a tulajdonságokat a *protectedSettings* beállításokban kell eltárolnunk.

## <a name="extension-schema"></a>Bővítményséma

Az egyéni szkriptbővítmény konfigurációja olyan adatokat ad meg, mint a szkript helye és a futtatandó parancs. Ezt a konfigurációt tárolhatja konfigurációs fájlokban, megadhatja a parancssorban, vagy megadhatja egy Azure Resource Manager sablonban.

A bizalmas adatokat védett konfigurációban tárolhatja, amely titkosítva van, és csak a virtuális gépen belül van visszafejtve. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs titkos okat tartalmaz, például jelszót vagy sas-fájlhivatkozást, amelyet védeni kell.

Ezeket az elemeket bizalmas adatként kell kezelni, és meg kell adni a bővítmények védett beállításainak konfigurációjában. Az Azure-beli virtuálisgép-bővítmény védett beállítási adatai titkosítva vannak, és csak a cél virtuális gépen vannak visszafejtve.

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
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {},
            "fileUris": [
                "script location"
            ]
        }
    }
}
```

> [!NOTE]
> A managedIdentity tulajdonság **nem használható** a storageAccountName vagy a storageAccountKey tulajdonsággal együtt

> [!NOTE]
> Egy virtuális gépre egyszerre csak egy bővítményverzió telepíthető, ugyanazon virtuális gép Resource Manager sablonban kétszer megadott egyéni szkript sikertelen lesz.

> [!NOTE]
> Ezt a sémát a VirtualMachine erőforráson belül vagy önálló erőforrásként is használhatjuk. Az erőforrás nevének a "virtualMachineName/extensionName" formátumban kell lennie, ha ezt a bővítményt önálló erőforrásként használja az ARM-sablonban.

### <a name="property-values"></a>Tulajdonságértékek

| Name | Érték / Példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| közzétevő | Microsoft.Compute | sztring |
| típus | CustomScriptExtension | sztring |
| typeHandlerVersion | 1.10 | int |
| fileUris (például) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| időbélyeg (például) | 123456789 | 32 bites egész szám |
| commandToExecute (például) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | sztring |
| storageAccountName (például) | examplestorageacct | sztring |
| storageAccountKey (például) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQf gmQj8hg== | sztring |
| managedIdentity (például) | { } vagy { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } vagy { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | JSON-objektum |

>[!NOTE]
>Ezek a tulajdonságnevek megkülönböztetik a kis- és nagybetűket. Az üzembe helyezési problémák elkerülése érdekében használja az itt látható neveket.

#### <a name="property-value-details"></a>Tulajdonságérték részletei

* `commandToExecute`: (**kötelező**, sztring) a végrehajtani kívánt belépésipont-szkript. Akkor használja inkább ezt a mezőt, ha a parancs titkos adatokat, például jelszavakat tartalmaz, vagy a fileUris bizalmas.
* `fileUris`: (nem kötelező, sztringtömb) a letölteni kívánt fájlok URL-címeit. Ha az URL-címek bizalmasak (például kulcsokat tartalmazó URL-címek), ezt a mezőt a protectedSettings beállításban kell megadni
* `timestamp` (nem kötelező, 32 bites egész szám) ezt a mezőt csak akkor használja, ha a mező értékének módosításával újrafuttatja a szkriptet.  Bármilyen egész szám elfogadható; csak az előző értéktől eltérőnek kell lennie.
* `storageAccountName`: (nem kötelező, sztring) a tárfiók neve. Ha tárolási hitelesítő adatokat ad meg, `fileUris` mindegyiknek Azure-blobok URL-címének kell lennie.
* `storageAccountKey`: (nem kötelező, sztring) a tárfiók hozzáférési kulcsa
* `managedIdentity`: (nem kötelező, json-objektum) a felügyelt [identitás](../../active-directory/managed-identities-azure-resources/overview.md) fájl(ok) letöltéséhez
  * `clientId`: (nem kötelező, sztring) a felügyelt identitás ügyfél-azonosítója
  * `objectId`: (nem kötelező, sztring) a felügyelt identitás objektumazonosítója

A következő értékek nyilvános vagy védett beállításokban is beállíthatók. A bővítmény elutasít minden olyan konfigurációt, amelyben az alábbi értékek nyilvános és védett beállításokban is meg vannak állítva.

* `commandToExecute`
* `fileUris`

A nyilvános beállítások használata hasznos lehet a hibakereséshez, de ajánlott védett beállításokat használni.

A nyilvános beállításokat a rendszer tiszta szövegként küldi el a virtuális gépnek, ahol a szkript végre lesz hajtva.  A védett beállítások titkosítása egy csak az Azure és a virtuális gép által ismert kulccsal történik. A beállításokat a rendszer az elküldésükkor menti a virtuális gépre, azaz ha a beállítások titkosítva vannak, titkosítva vannak mentve a virtuális gépen. A titkosított értékek visszafejtéséhez használt tanúsítványt a rendszer a virtuális gépen tárolja, és szükség esetén a beállítások visszafejtéséhez használja futásidőben.

####  <a name="property-managedidentity"></a>Tulajdonság: managedIdentity
> [!NOTE]
> Ezt a **tulajdonságot** csak a védett beállításokban kell megadni.

A CustomScript (1.10-es [](../../active-directory/managed-identities-azure-resources/overview.md) vagy újabb verziók) támogatja a felügyelt identitásokat a fájlok "fileUris" beállításban megadott URL-címekről való letöltéséhez. Lehetővé teszi a CustomScript számára az Azure Storage privát blobok vagy tárolók hozzáférését anélkül, hogy a felhasználónak olyan titkos kulcsokat kell átadnia, mint az SAS-jogkivonatok vagy a tárfiókkulcsok.

A funkció használatához a felhasználónak hozzá kell [](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) adni egy rendszer által hozzárendelt vagy felhasználó által hozzárendelt identitást ahhoz a virtuális géphez vagy VMSS-hez, ahol a CustomScriptet futtatnia kell, és hozzáférést kell adni a felügyelt identitásnak az Azure [Storage-tárolóhoz](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) vagy [-blobhoz.](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access)

Ha a rendszer által hozzárendelt identitást a cél vm/VMSS-en használni, állítsa a "managedidentity" mezőt egy üres JSON-objektumra. 

> Példa:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Ha a felhasználó által hozzárendelt identitást a cél vm/VMSS-en használni, konfigurálja a "managedidentity" mezőt a felügyelt identitás ügyfél-azonosítójával vagy objektumazonosítójával.

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
> A managedIdentity tulajdonság **nem használható** a storageAccountName vagy a storageAccountKey tulajdonsággal együtt

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure-beli virtuálisgép-bővítmények üzembe helyezhetők Azure Resource Manager sablonokkal. Az előző szakaszban részletezett JSON-séma használható egy Azure Resource Manager-sablonban az egyéni szkriptbővítmény futtatásához az üzembe helyezés során. Az alábbi minták az egyéni szkriptbővítmény használatát mutatják be:

* [Oktatóanyag: Virtuális gépi bővítmények üzembe helyezése Azure Resource Manager-sablonokkal](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Kétrétegű alkalmazás üzembe helyezése Windows rendszeren és Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>A PowerShell telepítése

A `Set-AzVMCustomScriptExtension` paranccsal hozzáadhatja az egyéni szkriptbővítményt egy meglévő virtuális géphez. További információ: [Set-AzVMCustomScriptExtension.](/powershell/module/az.compute/set-azvmcustomscriptextension)

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>További példák

### <a name="using-multiple-scripts"></a>Több szkript használata

Ebben a példában három szkriptet használ a kiszolgáló felépítéséhez. A **commandToExecute** az első szkriptet hívja meg, majd a többi hívásának lehetőségei vannak. Létrehozhat például egy fő parancsfájlt, amely a megfelelő hibakezeléssel, naplózással és állapotkezeléssel vezérli a végrehajtást. A szkriptek a helyi gépre vannak letöltve futtatáshoz. A parancsprogramban például a parancsprogramhoz való hozzáadásával hívja meg a et, és ismételje meg ezt a folyamatot a-ban `1_Add_Tools.ps1` `2_Add_Features.ps1`  `.\2_Add_Features.ps1` definiált többi szkript esetén `$settings` is.

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

### <a name="running-scripts-from-a-local-share"></a>Szkriptek futtatása helyi megosztásból

Ebben a példában előfordulhat, hogy helyi SMB-kiszolgálót szeretne használni a szkript helyéhez. Ezzel nem kell más beállításokat adnia, csak a **commandToExecute parancsot.**

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

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Egyéni szkriptek futtatása egynél több alkalommal a CLI használatával

Ha az egyéni szkriptbővítményt egynél több alkalommal szeretné futtatni, ezt a műveletet csak a következő feltételek teljesül teljesüle esetén használhatja:

* A extension **Name** paraméter megegyezik a bővítmény előző üzemelő példányának nevével.
* Frissítse a konfigurációt, különben a parancs nem lesz újra végrehajtva. Dinamikus tulajdonságot adhat hozzá a parancshoz, például egy időbélyeget.

A [ForceUpdateTag tulajdonságot true](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) (igaz) értékre is **beállíthatja.**

### <a name="using-invoke-webrequest"></a>A Invoke-WebRequest

Ha az [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) metódust használja a szkriptben, meg kell adnia a paramétert, különben a részletes állapot ellenőrzésekor a következő `-UseBasicParsing` hibaüzenetet kapja:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Az egyéni szkriptbővítmény méretezési csoporton való üzembe helyezéséhez lásd: [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)

## <a name="classic-vms"></a>A klasszikus virtuális gépeket

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Az egyéni szkriptbővítmény klasszikus virtuális gépeken való üzembe helyezéséhez használhatja a Azure Portal vagy a klasszikus Azure PowerShell parancsmagokat.

### <a name="azure-portal"></a>Azure Portal

Keresse meg a klasszikus virtuális gép erőforrását. A **Beállítások alatt válassza** a Bővítmények **lehetőséget.**

Kattintson **a + Hozzáadás gombra,** és az erőforrások listájában válassza az Egyéni **szkriptbővítmény lehetőséget.**

A Bővítmény **telepítése lapon** válassza ki a helyi PowerShell-fájlt, töltse ki az argumentumokat, majd kattintson az **OK gombra.**

### <a name="powershell"></a>PowerShell

A [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) parancsmag használatával hozzáadhatja az egyéni szkriptbővítményt egy meglévő virtuális géphez.

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

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítménytelepítések állapotával kapcsolatos adatokat a Azure Portal az új modullal Azure PowerShell le. Egy adott virtuális gép bővítményei üzembe helyezési állapotának adatokat a következő paranccsal lehet látni:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

A rendszer a bővítménykimenetet a cél virtuális gép alábbi mappájában található fájlokba naplózza.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

A rendszer letölti a megadott fájlokat a cél virtuális gép következő mappájába.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

ahol `<n>` a egy decimális egész szám, amely a bővítmény végrehajtásai között változhat.  Az érték megegyezik a bővítmény `1.*` tényleges, aktuális `typeHandlerVersion` értékével.  A tényleges könyvtár lehet például `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` .  

A parancs végrehajtásakor a bővítmény ezt a könyvtárat (például ) állítja `commandToExecute` `...\Downloads\2` be aktuális munkakönyvtárként. Ez a folyamat lehetővé teszi relatív elérési utak használatát a tulajdonságon keresztül letöltött fájlok `fileURIs` megkereséhez. Példákat az alábbi táblázatban talál.

Mivel az abszolút letöltési útvonal idővel változhat, jobb a relatív szkript/fájl elérési utakat választani a sztringben, `commandToExecute` amikor csak lehetséges. Például:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Az első URI-szegmens utáni elérésiút-információk a tulajdonságlistán keresztül letöltött `fileUris` fájlokhoz maradnak meg.  Ahogy az alábbi táblázatban látható, a letöltött fájlok le vannak leképezve a letöltési alkönyvtárakba, hogy tükrözzék az értékek `fileUris` szerkezetét.  

#### <a name="examples-of-downloaded-files"></a>Példák letöltött fájlokra

| URI a fileUrisban | Relatív letöltés helye | Abszolút letöltött hely <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Az abszolút könyvtárútvonalak a virtuális gép élettartama során változnak, de a CustomScript bővítmény egyetlen végrehajtásán belül nem.

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, lépjen kapcsolatba az Azure-szakértőkkel az [MSDN Azure-on](https://azure.microsoft.com/support/forums/)és a Stack Overflow fórumain. Emellett be is Azure-támogatás incidenst. A webhely Azure-támogatás [válassza](https://azure.microsoft.com/support/options/) a Támogatás et. További információt a Azure ügyfélszolgálata a gyakori [Microsoft Azure című témakörben talál.](https://azure.microsoft.com/support/faq/)
