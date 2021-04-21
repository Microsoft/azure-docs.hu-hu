---
title: Azure-beli virtuálisgép-bővítmények és -funkciók Linuxhoz
description: Megtudhatja, milyen bővítmények érhetők el a Linuxon lévő Azure-beli virtuális gépekhez, az alapján csoportosítva, hogy mit nyújtanak vagy fejlesztnek.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/30/2018
ms.openlocfilehash: bdbbc4c421b83fd041c7d900fb0edd01c4d636e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785090"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Virtuálisgép-bővítmények és funkciók Linux rendszeren

Az Azure virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha egy virtuális gépen például szoftver telepítésére, vírusvédelemre vagy egy szkript futtatására van szükség, erre felhasználható egy virtuálisgép-bővítmény. Az Azure virtuálisgép-bővítmények az Azure CLI, a PowerShell, az Azure Resource Manager-sablonok és az Azure Portal használatával futtathatók. A bővítmények egy kötegben hajthatók végre az új virtuális gépek üzembe helyezésével, vagy meglévő rendszereken is futtathatók.

Ez a cikk áttekintést nyújt a virtuálisgép-bővítményekről, az Azure-beli virtuálisgép-bővítmények használatának előfeltételeiről, valamint útmutatást nyújt a virtuálisgép-bővítmények észleléséhez, kezeléséhez és eltávolításához. Ez a cikk azért nyújt általános információkat, mert számos virtuálisgép-bővítmény érhető el, és mindegyik egy-egy egyedi konfigurációval rendelkezik. A bővítményspecifikus részletek az egyes bővítményekre vonatkozó dokumentumokban találhatók.

## <a name="use-cases-and-samples"></a>Esetek és minták használata

Számos különböző Azure-beli virtuálisgép-bővítmény érhető el, mindegyikhez adott esetben van szükség. Néhány példa:

- PowerShell célállapot-konfigurációk alkalmazása linuxos DSC-bővítvű virtuális gépre. További információ: [Azure Desired State konfigurációs bővítmény.](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)
- Virtuális gép monitorozásának konfigurálása a Microsoft Monitoring Agent virtuálisgép-bővítvével. További információ: Linux rendszerű [virtuális gép figyelése.](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)
- Konfigurálja az Azure-infrastruktúra monitorozását a Chef vagy a Datadog bővítvével. További információt a [Chef-dokumentumok](https://docs.chef.io/azure_portal.html) vagy a [Datadog blogban talál.](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)

A folyamatspecifikus bővítmények mellett windowsos és linuxos virtuális gépeken is elérhető egy egyéni szkriptbővítmény. A Linuxhoz készült egyéni szkriptbővítmény lehetővé teszi bármely Bash-szkript futtatását egy virtuális gépen. Az egyéni szkriptek olyan Azure-beli üzemelő példányok tervezéséhez hasznosak, amelyek a natív Azure-eszközök által biztosíthatónál nagyobb konfigurációt igényelnek. További információ: [Linux vm custom script extension (Linux vm egyéni szkriptbővítmény).](custom-script-linux.md)

## <a name="prerequisites"></a>Előfeltételek

A bővítmény virtuális gépen való kezeléshez telepítenie kell az Azure Linux-ügynököt. Egyes egyéni bővítmények előfeltételekkel, például erőforrás-hozzáféréssel vagy függőségekkel is rendelkezik.

### <a name="azure-vm-agent"></a>Azure virtuálisgép-ügynök

Az Azure-beli virtuálisgép-ügynök kezeli az Azure-beli virtuális gépek és az Azure-hálóvezérlő közötti interakciókat. A virtuálisgép-ügynök az Azure-beli virtuális gépek üzembe helyezésének és felügyeletének számos funkcióért felelős, beleértve a virtuálisgép-bővítmények futtatását is. Az Azure-beli virtuálisgép-ügynök előre telepítve van Azure Marketplace rendszerképeken, és manuálisan telepíthető a támogatott operációs rendszereken. A Linuxhoz használható Azure-beli virtuálisgép-ügynök Linux-ügynökként ismert.

További információ a támogatott operációs rendszerekről és telepítési utasításokról: [Azure-beli virtuálisgép-ügynök.](agent-linux.md)

#### <a name="supported-agent-versions"></a>Támogatott ügynökverziók

A lehető legjobb felhasználói élmény biztosítása érdekében az ügynöknek legalább verziója van. További információkért tekintse meg [ezt a cikket](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Támogatott operációs rendszer

A Linux-ügynök több operációs rendszeren fut, de a bővítmények keretrendszerének van egy korlátja a bővítményeket tároló operációs rendszerekre. További információkért tekintse meg [ezt a cikket](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Egyes bővítmények nem támogatottak az összes operációs rendszeren, és *az 51-es* hibakódot, a "Nem támogatott operációs rendszer" hibakódot bocsátják ki. A támogatásért tekintse meg az egyes bővítmények dokumentációját.

#### <a name="network-access"></a>Hálózati hozzáférés

A bővítménycsomagok az Azure Storage bővítménytárból tölthetők le, a bővítmény állapotfeltöltései pedig közzé vannak adva az Azure Storage-ban. Ha az [ügynökök](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) támogatott verzióját használja, nem kell engedélyeznie az Azure Storage-hoz való hozzáférést a virtuális gép régiójában, mivel az ügynök átirányíthatja a kommunikációt az Azure Fabric Controllerre az ügynökkommunikációhoz. Ha az ügynök nem támogatott verzióját használja, engedélyeznie kell a kimenő hozzáférést az Azure Storage-hoz abban a régióban a virtuális gépről.

> [!IMPORTANT]
> Ha a vendég tűzfal használatával letiltotta a *168.63.129.16-os* porthoz való hozzáférést, akkor a bővítmények a fentiektől függetlenül meghiúsulnak.

Az ügynökök csak bővítménycsomagok letöltésére és állapotjelentések letöltésére használhatók. Ha például egy bővítménytelepítésnek le kell töltenie egy szkriptet a GitHubról (egyéni szkript), vagy hozzá kell férni az Azure Storage-hoz (Azure Backup), akkor további tűzfal-/hálózati biztonsági csoportportokat kell megnyitnia. A különböző bővítményekre különböző követelmények vonatkoznak, mivel ezek saját jogon lévő alkalmazások. Az Azure Storage-hoz hozzáférést igénylő bővítmények esetében engedélyezheti a hozzáférést [](../../virtual-network/network-security-groups-overview.md#service-tags)a Storage Azure NSG-szolgáltatáscímkéivel.

Az ügynök forgalmi kérésének átirányításához a Linux-ügynök támogatja a proxykiszolgálót. Ez a proxykiszolgáló-támogatás azonban nem alkalmaz bővítményeket. Minden egyes bővítményt úgy kell konfigurálnia, hogy proxyval működjön.

## <a name="discover-vm-extensions"></a>Virtuálisgép-bővítmények felderítése

Az Azure-beli virtuális gépekhez számos különböző virtuális gépi bővítmény érhető el. A teljes lista az [az vm extension image list használatával megjelenik.](/cli/azure/vm/extension/image#az_vm_extension_image_list) Az alábbi példa felsorolja a *westus* helyen elérhető összes bővítményt:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Virtuálisgép-bővítmények futtatása

Az Azure-beli virtuálisgép-bővítmények meglévő virtuális gépeken futnak, ami akkor hasznos, ha konfigurációt kell változtatnia, vagy helyre kell állítania a kapcsolatot egy már üzembe helyezett virtuális gépen. A virtuálisgép-bővítmények sablontelepítésekkel együtt Azure Resource Manager csomagolhatóak. Ha bővítményeket használ Resource Manager sablonokkal, az Azure-beli virtuális gépek üzembe helyezhetők és konfigurálhatóak az üzembe helyezés utáni beavatkozás nélkül.

A következő metódusokkal futtathat bővítményeket egy meglévő virtuális gépen.

### <a name="azure-cli"></a>Azure CLI

Az Azure-beli virtuálisgép-bővítmények az az vm extension set paranccsal futtathatók egy meglévő [virtuális gépen.](/cli/azure/vm/extension#az_vm_extension_set) Az alábbi példa egy myResourceGroup nevű erőforráscsoport *myVM* nevű virtuális gépén futtatja az egyéni *szkriptbővítményt.* Cserélje le a példában található erőforráscsoport nevét, a virtuális gép nevét és a szkriptet a futtatáshoz (https: \/ /raw.githubusercontent.com/me/project/hello.sh) a saját adataira. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Ha a bővítmény megfelelően fut, a kimenet az alábbi példához hasonló:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

A virtuálisgép-bővítmények egy meglévő virtuális gépre a virtuális gép Azure Portal. Válassza ki a virtuális gépet a portálon, válassza a **Bővítmények,** majd a Hozzáadás **lehetőséget.** Válassza ki a kívánt bővítményt az elérhető bővítmények listájából, és kövesse a varázsló utasításait.

Az alábbi képen az egyéni Linux-szkriptbővítmény telepítése látható a Azure Portal:

![Egyéni szkriptbővítmény telepítése](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A virtuálisgép-bővítmények hozzáadhatóak egy Azure Resource Manager sablonhoz, és a sablon üzembe helyezéssel hajthatóak végre. Amikor sablonnal helyez üzembe egy bővítményt, teljes körűen konfigurált Azure-beli üzemelő példányokat hozhat létre. A következő JSON például egy olyan Resource Manager-sablonból származik, amely elosztott terhelésű virtuális gépeket helyez üzembe és Azure SQL Database, majd mindegyikre telepít egy .NET Core-alkalmazást. A virtuálisgép-bővítmény gondoskodik a szoftver telepítésével.

További információért tekintse meg a teljes [Resource Manager sablont.](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

További információ a sablonsablonok Resource Manager: [Szerzői Azure Resource Manager sablonok.](../windows/template-description.md#extensions)

## <a name="secure-vm-extension-data"></a>Virtuálisgép-bővítmény adatainak biztonságossá tere

Virtuálisgép-bővítmény futtatásakor szükség lehet olyan bizalmas adatok megadására, mint a hitelesítő adatok, a tárfiókok neve és a tárfiók hozzáférési kulcsai. Számos virtuálisgép-bővítmény tartalmaz olyan védett konfigurációt, amely titkosítja az adatokat, és csak a cél virtuális gépen belül titkosítja azokat. Mindegyik bővítmény egy adott védett konfigurációs sémával rendelkezik, és mindegyiket a bővítményspecifikus dokumentáció tartalmazza.

Az alábbi példa a Linuxhoz készült egyéni szkriptbővítmény egy példányát mutatja be. A végrehajtandó parancs hitelesítő adatok egy halmazát tartalmazza. Ebben a példában a végrehajtandó parancs nincs titkosítva:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

A tulajdonság védett konfigurációba  való **áthelyezésével** a parancs biztonságossá teheti a végrehajtási sztringet az alábbi példában látható módon:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>Hogyan frissülnek az ügynökök és bővítmények?

Az ügynökök és bővítmények ugyanazt a frissítési mechanizmust osztják meg. Egyes frissítésekhez nincs szükség további tűzfalszabályokra.

Ha elérhetővé válik egy frissítés, az csak akkor lesz telepítve a virtuális gépre, ha a bővítmények módosulnak, és a virtuálisgép-modell egyéb változásai, például:

- Adatlemezek
- Bővítmények
- Rendszerindítási diagnosztikai tároló
- Vendég operációs rendszer titkos kulcsok
- Virtuális gép mérete
- Hálózati profil

A közzétevők különböző időpontokban teszik elérhetővé a frissítéseket a régiók számára, így lehetséges, hogy a különböző régiókban található virtuális gépek különböző verziókban legyenek elérhetők.

#### <a name="agent-updates"></a>Ügynökfrissítések

A Linux rendszerű virtuálisgép-ügynök  egy csomagban tartalmazza a *kiépítési* ügynök kódját és a bővítménykezelő kódot, amely nem különíthető el. A kiépítési ügynököt akkor *tilthatja* le, ha a cloud-init használatával szeretne kiépítve az Azure-ban. Ehhez lásd: [a cloud-init használata.](../linux/using-cloud-init.md)

Az ügynökök támogatott verziói használhatnak automatikus frissítéseket. Az egyetlen frissíthető kód a *bővítménykezelési kód,* nem a kiépítési kód. A *kiépítési ügynök kódja* egyszer fut.

A *bővítménykezelési* kód felelős az Azure-hálóval való kommunikációért és a virtuálisgép-bővítmények olyan műveleteinek kezeléséért, mint a telepítések, az állapotjelentések, az egyes bővítmények frissítése és eltávolítása. A frissítések biztonsági javításokat, hibajavításokat és a bővítménykezelési kód *fejlesztéseit tartalmazzák.*

Az ügynök telepítésekor létrejön egy szülő démon. Ez a szülő ezután származtat egy gyermekfolyamatot, amely a bővítmények kezelésére használható. Ha elérhető frissítés az ügynökhöz, letölti, a szülő leállítja a gyermekfolyamatot, frissíti, majd újraindítja. Ha probléma van a frissítéssel, a szülőfolyamat visszaállítja az előző gyermekverziót.

A szülőfolyamat nem frissíthető automatikusan. A szülő csak disztribúciócsomag-frissítéssel frissíthető.

Annak ellenőrzéshez, hogy melyik verziót futtatja, ellenőrizze a `waagent` következőt:

```bash
waagent --version
```

A kimenet a következő példához hasonló:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.6.0
Goal state agent: 2.2.18
```

Az előző példakimenetben a szülő vagy a "csomag üzembe helyezett verziója" *a WALinuxAgent-2.2.17*

A "Célállapotügynök" az automatikus frissítési verzió.

Erősen ajánlott mindig automatikus frissítést használni az [autoUpdate.Enabled=y ügynökhöz.](./update-linux-agent.md) Ha ez a beállítás nincs engedélyezve, akkor manuálisan kell frissítenie az ügynököt, és nem kell hiba- és biztonsági javításokat kapnia.

#### <a name="extension-updates"></a>Bővítményfrissítések

Ha elérhető egy bővítményfrissítés, a Linux-ügynök letölti és frissíti a bővítményt. Az automatikus bővítményfrissítések kisebb *vagy* *gyorsjavítások.* A bővítményeket a bővítmény kiépítésekor választhatja ki, illetve le is irathatja az apróbb frissítéseket.  Az alábbi példa bemutatja, hogyan frissítheti automatikusan az alverziókat egy Resource Manager *autoUpgradeMinorVersion: true, '* érték használatával:

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

A legújabb kisebb kiadási hibajavítások lehozása érdekében javasoljuk, hogy mindig az automatikus frissítés lehetőséget válassza a bővítménytelepítések során. A biztonsággal vagy a kulcsokkal kapcsolatos hibajavításokat képző gyorsjavítások nem iratkoznak le.

### <a name="how-to-identify-extension-updates"></a>Bővítményfrissítések azonosítása

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Annak azonosítása, hogy a bővítmény autoUpgradeMinorVersion beállítással van-e beállítva egy virtuális gépen

A virtuálisgép-modellből láthatja, hogy a bővítmény az "autoUpgradeMinorVersion" verzióval lett-e kiépítve. Az ellenőrzéshez használja [az az vm show lehetőséget,](/cli/azure/vm#az_vm_show) és adja meg az erőforráscsoportot és a virtuális gép nevét az alábbiak szerint:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Az alábbi példa kimenetében látható, hogy *az autoUpgradeMinorVersion* true (igaz) értékre *van állítva:*

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Annak azonosítása, hogy mikor történt autoUpgradeMinorVersion

Annak megtekintéséhez, hogy mikor történt a bővítmény frissítése, tekintse át az ügynöknaplókat a virtuális gépen a */var/log/waagent.log oldalon.*

Az alábbi példában a virtuális gépen telepítve volt a *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025.* A *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027* számára elérhető volt egy gyorsjavítás:

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Ügynökengedélyek

A feladatainak végrehajtásához az ügynöknek gyökérként kell *futnia.*

## <a name="troubleshoot-vm-extensions"></a>Virtuálisgép-bővítmények hibaelhárítása

Minden virtuálisgép-bővítménynek lehetnek a bővítményre vonatkozó hibaelhárítási lépései. Ha például az egyéni szkriptbővítményt használja, a szkriptvégrehajtás részletei helyileg találhatók meg a virtuális gépen, ahol a bővítményt futtatta. A bővítményspecifikus hibaelhárítási lépéseket a bővítményspecifikus dokumentáció tartalmazza.

Az alábbi hibaelhárítási lépések az összes virtuálisgép-bővítményre érvényesek.

1. A Linux-ügynök naplóját a *bővítmény /var/log/waagent.log* fájlban való kiépítésekor végzett tevékenységben ellenőrizheti.

2. További részletekért tekintse meg a tényleges bővítménynaplókat a *\<extensionName> /var/log/azure/ fájlban*

3. A bővítményspecifikus dokumentáció hibaelhárítási szakaszaiban hibakódokat, ismert problémákat stb. találhat.

3. Nézze meg a rendszernaplókat. Ellenőrizze, hogy vannak-e olyan egyéb műveletek, amelyek megzavarták a bővítményt, például egy olyan alkalmazás hosszú ideig futó telepítése, amely kizárólagos csomagkezelői hozzáférést igényel.

### <a name="common-reasons-for-extension-failures"></a>A bővítményhibák gyakori okai

1. A bővítmények futtatása 20 percet tesz meg (a kivételek a CustomScript-bővítmények, a Chef és a DSC, amelyek 90 percből állnak). Ha az üzemelő példány túllépi ezt az időt, időtúllépésként lesz megjelölve. Ennek oka lehet az alacsony erőforrásigényű virtuális gépek, más virtuálisgép-konfigurációk/indítási feladatok, amelyek nagy mennyiségű erőforrást fogyasztanak, miközben a bővítményt próbálja kiépíteni.

2. A minimális előfeltételek nem teljesülnek. Egyes bővítmények függőségi viszonyban vannak a virtuálisgép-termékekkel, például a HPC-rendszerképekkel. A bővítményekhez szükség lehet bizonyos hálózatelérési követelményekre, például az Azure Storage-ral vagy a nyilvános szolgáltatásokkal való kommunikációra. További példa lehet a csomagtárakhoz való hozzáférés, a lemezterület elajáratának vagy a biztonsági korlátozásoknak a elérése.

3. Kizárólagos csomagkezelői hozzáférés. Bizonyos esetekben előfordulhat, hogy hosszú ideig futó virtuálisgép-konfiguráció és bővítménytelepítés ütközik, ahol mindkettőnek kizárólagos hozzáférésre van szüksége a csomagkezelőhöz.

### <a name="view-extension-status"></a>Bővítmény állapotának megtekintése

Miután futtatott egy virtuálisgép-bővítményt egy virtuális gépen, [az az vm get-instance-view használatával](/cli/azure/vm#az_vm_get_instance_view) adja vissza a bővítmény állapotát a következőképpen:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

A kimenet az alábbi példakimenethez hasonló:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

A bővítmény végrehajtási állapota a következő Azure Portal. Egy bővítmény állapotának megtekintéséhez válassza ki a virtuális gépet, válassza a **Bővítmények** lehetőséget, majd válassza ki a kívánt bővítményt.

### <a name="rerun-a-vm-extension"></a>Virtuálisgép-bővítmény újrafuttatva

Előfordulhatnak olyan esetek, amikor egy virtuálisgép-bővítményt újra kellfuttatni. A bővítményt újrafuttathatja az eltávolításával, majd újrafuttathatja a bővítményt egy ön által választott végrehajtási módszerrel. Bővítmény eltávolításához használja az [az vm extension delete parancsot](/cli/azure/vm/extension#az_vm_extension_delete) a következőképpen:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

A bővítményt a következőképpen Azure Portal el:

1. Válasszon ki egy virtuális gépet.
2. Válassza **a Bővítmények lehetőséget.**
3. Válassza ki a kívánt bővítményt.
4. Válassza az **Eltávolítás lehetőséget.**

## <a name="common-vm-extension-reference"></a>A virtuálisgép-bővítmény általános referenciája

| Bővítmény neve | Description | További információ |
| --- | --- | --- |
| Egyéni szkriptbővítmény Linuxhoz |Szkriptek futtatása Azure-beli virtuális gépen |[Egyéni szkriptbővítmény Linuxhoz](custom-script-linux.md) |
| Virtuálisgép-hozzáférési bővítmény |Azure-beli virtuális géphez való hozzáférés visszaszerzése |[Virtuálisgép-hozzáférési bővítmény](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics bővítmény |A Azure Diagnostics |[Azure Diagnostics bővítmény](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure-beli virtuálisgép-hozzáférési bővítmény |Felhasználók és hitelesítő adatok kezelése |[Virtuálisgép-hozzáférési bővítmény Linuxhoz](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Következő lépések

További információ a virtuálisgép-bővítményekről: [Azure-beli virtuálisgép-bővítmények és -funkciók áttekintése.](overview.md)