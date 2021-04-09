---
title: Azure virtuálisgép-bővítmények és-funkciók Linux rendszerhez
description: Ismerje meg, hogy milyen bővítmények érhetők el a Linux rendszerű Azure-beli virtuális gépekhez, az általuk biztosított vagy a fejlesztésük szerint csoportosítva.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/30/2018
ms.openlocfilehash: 8ff7b2940ca9ce3e5dc5913c0e676a32857771e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046878"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Virtuálisgép-bővítmények és-funkciók Linux rendszerhez

Az Azure virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha egy virtuális gépen például szoftver telepítésére, vírusvédelemre vagy egy szkript futtatására van szükség, erre felhasználható egy virtuálisgép-bővítmény. Az Azure virtuálisgép-bővítmények az Azure CLI, a PowerShell, az Azure Resource Manager-sablonok és az Azure Portal használatával futtathatók. A bővítmények egy kötegben hajthatók végre az új virtuális gépek üzembe helyezésével, vagy meglévő rendszereken is futtathatók.

Ez a cikk áttekintést nyújt a virtuálisgép-bővítményekről, az Azure virtuálisgép-bővítmények használatára vonatkozó előfeltételekről, valamint útmutatást nyújt a virtuálisgép-bővítmények észleléséhez, kezeléséhez és eltávolításához. Ez a cikk általánosított információkat nyújt, mivel számos virtuálisgép-bővítmény elérhető, amelyek mindegyike potenciálisan egyedi konfigurációval rendelkezik. A bővítményekre vonatkozó részletek az egyes bővítményekre jellemző minden dokumentumban találhatók.

## <a name="use-cases-and-samples"></a>Használati esetek és minták

Számos különböző Azure-beli virtuálisgép-bővítmény érhető el, amelyek mindegyike egy adott használati esettel rendelkezik. Néhány példa:

- A PowerShell kívánt állapot-konfigurációinak alkalmazása a Linux rendszerhez készült DSC-bővítményt futtató virtuális gépre. További információ: az [Azure desired State Configuration bővítménye](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Egy virtuális gép figyelésének konfigurálása a Microsoft monitoring Agent virtuálisgép-bővítménnyel. További információ: [Linux rendszerű virtuális gép figyelése](/previous-versions/azure/virtual-machines/linux/tutorial-monitor).
- Konfigurálja az Azure-infrastruktúra figyelését a Chef vagy az Datadoggal bővítménnyel. További információ: [Chef docs](https://docs.chef.io/azure_portal.html) vagy [datadoggal blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

A folyamat-specifikus bővítmények mellett egyéni szkriptek is elérhetők a Windows-és Linux-alapú virtuális gépekhez. A Linuxhoz készült egyéni szkriptek lehetővé teszik a bash-parancsfájlok futtatását egy virtuális gépen. Az egyéni parancsfájlok olyan Azure-beli központi telepítések tervezésekor hasznosak, amelyek a natív Azure-eszközök által biztosított konfigurációt igénylik. További információ: [linuxos virtuális gép egyéni parancsfájl-bővítménye](custom-script-linux.md).

## <a name="prerequisites"></a>Előfeltételek

A bővítménynek a virtuális gépen való kezeléséhez telepítenie kell az Azure Linux-ügynököt. Egyes bővítmények előfeltételei, például az erőforrásokhoz vagy függőségekhez való hozzáférés.

### <a name="azure-vm-agent"></a>Azure virtuálisgép-ügynök

Az Azure-beli virtuálisgép-ügynök az Azure-beli virtuális gép és az Azure Fabric Controller közötti interakciókat kezeli. A virtuálisgép-ügynök feladata az Azure-beli virtuális gépek üzembe helyezésének és felügyeletének számos funkcionális aspektusa, beleértve a virtuálisgép-bővítmények futtatását is. Az Azure-beli virtuálisgép-ügynök előre telepítve van az Azure Marketplace-lemezképeken, és manuálisan is telepíthető a támogatott operációs rendszereken. A Linux rendszerhez készült Azure VM-ügynököt Linux-ügynöknek nevezzük.

További információ a támogatott operációs rendszerekről és a telepítési utasításokról: [Azure Virtual Machine Agent](agent-linux.md).

#### <a name="supported-agent-versions"></a>Támogatott ügynök-verziók

A lehető legjobb élmény biztosítása érdekében az ügynöknek minimális verziója van. További információkért tekintse meg [ezt a cikket](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Támogatott operációs rendszer

A Linux-ügynök több operációs rendszeren fut, de a kiterjesztések keretrendszere korláttal rendelkezik a bővítmények számára. További információkért tekintse meg [ezt a cikket](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Egyes bővítmények nem támogatottak az összes operációs rendszeren, és a *51-es hibakódot (nem támogatott operációs rendszert*) bocsátják ki. Az egyes bővítmények dokumentációjában tájékozódhat a támogatásról.

#### <a name="network-access"></a>Hálózati hozzáférés

A bővítmények letöltése az Azure Storage bővítmény-tárházból történik, és a bővítmény állapotának feltöltése az Azure Storage-ba kerül. Ha az ügynökök [támogatott](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verzióját használja, nem kell engedélyeznie az Azure Storage-hoz való hozzáférést a virtuálisgép-régióban, mert az ügynök használatával átirányíthatja a kommunikációt az Azure Fabric-vezérlővel az ügynök kommunikációja érdekében. Ha az ügynök nem támogatott verzióját használ, engedélyeznie kell a kimenő hozzáférést az adott régióban lévő Azure Storage-hoz a virtuális gépről.

> [!IMPORTANT]
> Ha letiltotta a *168.63.129.16* való hozzáférést a vendég tűzfal használatával, akkor a bővítmények a fentiektől függetlenül meghiúsulnak.

Az ügynököket csak a bővítmény-csomagok és a jelentéskészítési állapotok letöltésére lehet használni. Ha például egy bővítmény telepítéséhez le kell töltenie egy parancsfájlt a GitHubról (egyéni parancsfájlból), vagy hozzá kell férnie az Azure Storage-hoz (Azure Backup), akkor további tűzfal/hálózati biztonsági csoport portjait kell megnyitnia. A különböző kiterjesztések eltérő követelményekkel rendelkeznek, mivel ezek az alkalmazások a saját jogukban vannak. Az Azure Storage-hoz hozzáférést igénylő bővítmények esetében engedélyezheti a hozzáférést az Azure NSG Service-címkék használatával a [tároláshoz](../../virtual-network/network-security-groups-overview.md#service-tags).

Az ügynök forgalmi kéréseinek átirányításához a Linux-ügynöknek proxykiszolgáló-támogatással kell rendelkezniük. Azonban ez a proxykiszolgáló-támogatás nem alkalmazza a bővítményeket. Az egyes bővítményeket úgy kell konfigurálni, hogy a proxyval működjenek.

## <a name="discover-vm-extensions"></a>Virtuálisgép-bővítmények felderítése

Az Azure-beli virtuális gépekhez számos különböző virtuális gépi bővítmény érhető el. A teljes lista megtekintéséhez használja [az az VM Extension képek listáját](/cli/azure/vm/extension/image#az-vm-extension-image-list). Az alábbi példa felsorolja az összes elérhető bővítményt a *westus* helyen:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Virtuálisgép-bővítmények futtatása

Az Azure-beli virtuálisgép-bővítmények már meglévő virtuális gépeken futnak, ami akkor hasznos, ha a konfigurációt módosítani kell, vagy egy már telepített virtuális gépen kell helyreállítani a kapcsolatot. A virtuálisgép-bővítmények Azure Resource Manager sablonok üzembe helyezésével is elhelyezhetők. A bővítmények Resource Manager-sablonokkal való használatával az Azure-beli virtuális gépek üzembe helyezése és konfigurálása a telepítés utáni beavatkozás nélkül végezhető el.

A következő módszerek használhatók a bővítmények egy meglévő virtuális gépen való futtatásához.

### <a name="azure-cli"></a>Azure CLI

Az Azure virtuálisgép-bővítmények egy meglévő virtuális gépre is futtathatók az az [VM Extension set](/cli/azure/vm/extension#az-vm-extension-set) paranccsal. A következő példa egy *myVM* nevű virtuális gépen futtatja az egyéni szkriptek bővítményét egy *myResourceGroup* nevű erőforráscsoporthoz. Cserélje le az erőforráscsoport nevét, a virtuális gép nevét és a parancsfájlt a futtatáshoz (https: \/ /RAW.githubusercontent.com/Me/Project/Hello.sh) a saját adataival. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Ha a bővítmény megfelelően fut, a kimenet a következő példához hasonló:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

A virtuálisgép-bővítmények a Azure Portal használatával alkalmazhatók egy meglévő virtuális gépre. Válassza ki a virtuális gépet a portálon, válassza a **bővítmények**, majd a **Hozzáadás** lehetőséget. Válassza ki a kívánt bővítményt az elérhető bővítmények listájából, és kövesse a varázsló utasításait.

Az alábbi képen látható, hogyan telepíthető a Linux Custom script bővítmény a Azure Portalról:

![Egyéni szkriptek bővítményének telepítése](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A virtuálisgép-bővítmények hozzáadhatók egy Azure Resource Manager sablonhoz, és a sablon üzembe helyezésével hajthatók végre. Ha sablonnal telepít egy bővítményt, létrehozhat teljesen konfigurált Azure-telepítéseket. Például a következő JSON egy olyan Resource Manager-sablonból származik, amely elosztott terhelésű virtuális gépeket és Azure SQL Database telepít, majd telepíti a .NET Core-alkalmazást minden egyes virtuális gépre. A virtuálisgép-bővítmény gondoskodik a szoftver telepítéséről.

További információ: teljes [Resource Manager-sablon](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

A Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok](../windows/template-description.md#extensions)készítése.

## <a name="secure-vm-extension-data"></a>Virtuálisgép-bővítmények védelme

Virtuálisgép-bővítmény futtatásakor szükség lehet bizalmas adatok, például a hitelesítő adatok, a Storage-fiókok és a Storage-fiók hozzáférési kulcsainak megadására. Számos virtuálisgép-bővítmény tartalmaz egy védett konfigurációt, amely titkosítást végez, és csak a célként megadott virtuális gépen fejti vissza. Mindegyik bővítmény egy adott védett konfigurációs sémával rendelkezik, és mindegyik a bővítményekre vonatkozó dokumentációban van részletezve.

Az alábbi példa a Linux rendszerhez készült egyéni szkript-bővítmény egy példányát mutatja be. A végrehajtandó parancs a hitelesítő adatok egy készletét tartalmazza. Ebben a példában a végrehajtandó parancs nem titkosított:

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

Ha a **parancs áthelyezésével végrehajtja** a tulajdonságot a **védett** konfigurációra, a a következő példában látható módon védi a végrehajtási karakterláncot:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Hogyan frissülnek az ügynökök és a bővítmények?

Az ügynökök és a bővítmények ugyanazt a frissítési mechanizmust használják. Egyes frissítések esetében nincs szükség további tűzfalszabályok megírására.

Ha egy frissítés elérhető, akkor csak a virtuális gépre van telepítve, ha a bővítmények módosulnak, és más virtuálisgép-modell is változik, például:

- Adatlemezek
- Bővítmények
- Rendszerindítási diagnosztika tárolója
- Vendég operációs rendszer titkai
- Virtuális gép mérete
- Hálózati profil

A kiadók különböző időpontokban teszik elérhetővé a frissítéseket a régiók számára, így lehetséges, hogy különböző régiókban lévő virtuális gépeket különböző verziókban lehet.

#### <a name="agent-updates"></a>Ügynök frissítései

A Linux rendszerű virtuális gép ügynöke egy csomagban található *kiépítési ügynök kódját* és a *bővítmények kezelésére szolgáló kódot*  tartalmaz, amely nem választható el egymástól. Ha a Cloud-init használatával szeretné kiépíteni az Azure-t, letilthatja a *kiépítési ügynököt* . Ehhez lásd: [a Cloud-init használata](../linux/using-cloud-init.md).

Az ügynökök támogatott verziói az automatikus frissítéseket használhatják. Az egyetlen módosítható kód a *bővítmények kezelési* kódja, nem pedig a kiépítési kód. A *kiépítési ügynök kódja* egyszeri futtatású kód.

A *bővítmények kezelésére szolgáló kód* felelős az Azure-hálóval való kommunikációért, valamint a virtuálisgép-bővítmények műveleteinek, például a telepítés, a jelentéskészítési állapot, az egyes bővítmények frissítésének és eltávolításának kezeléséért. A frissítések biztonsági javításokat, hibajavításokat és továbbfejlesztett funkciókat tartalmaznak a *bővítmények kezelési kódjához*.

Ha az ügynök telepítve van, a rendszer egy szülő démont hoz létre. Ez a szülő ezután egy alárendelt folyamatot indít el, amely a bővítmények kezelésére szolgál. Ha van elérhető frissítés az ügynökhöz, az le van töltve, a szülő leállítja a gyermek folyamatát, frissíti, majd újraindítja. Ha probléma van a frissítéssel, a fölérendelt folyamat visszaállítja az előző alárendelt verziót.

A fölérendelt folyamat nem frissíthető automatikusan. A szülőt csak a disztribúciós csomag frissítésével lehet frissíteni.

A futtatott verziót a következőképpen tekintheti meg `waagent` :

```bash
waagent --version
```

A kimenet a következő példához hasonló:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.6.0
Goal state agent: 2.2.18
```

Az előző példában a szülő vagy a "Package központilag telepített verzió" *WALinuxAgent-2.2.17*

A "cél állapotjelző ügynök" az automatikus frissítés verziója.

Erősen ajánlott automatikusan frissíteni az ügynököt, az automatikus [frissítést. enabled = y](./update-linux-agent.md). Ha nem rendelkezik ezzel az engedélyezéssel, az ügynököt manuálisan kell frissítenie, és nem kell lekérnie a hibákat és a biztonsági javításokat.

#### <a name="extension-updates"></a>Bővítmények frissítései

Ha elérhető a bővítmény frissítése, a Linux-ügynök letölti és frissíti a bővítményt. Az automatikus bővítmény frissítései lehetnek *kisebbek* vagy *gyorsjavítások*. A bővítmények kiépítésekor engedélyezheti vagy letilthatja a bővítmények *másodlagos* frissítéseit. Az alábbi példa bemutatja, hogyan lehet automatikusan frissíteni a alverziókat egy Resource Manager-sablonban a *autoUpgradeMinorVersion ": true"*:

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

Ha a legújabb kiadási hibákat szeretné lekérni, javasoljuk, hogy mindig válassza az automatikus frissítés lehetőséget a bővítmények üzembe helyezésében. A biztonsági vagy a kulcs hibajavításait nem lehet kijelölni.

### <a name="how-to-identify-extension-updates"></a>Bővítmények frissítéseinek azonosítása

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Annak azonosítása, hogy a bővítmény a autoUpgradeMinorVersion-mel van-e beállítva egy virtuális gépen

A virtuálisgép-modellből megtekintheti, hogy a bővítmény a "autoUpgradeMinorVersion" használatával lett kiépítve. Az az [VM show](/cli/azure/vm#az-vm-show) paranccsal ellenőrizhető, hogy az erőforráscsoport és a virtuális gép nevét a következőképpen adja meg:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

A következő példa kimenete azt mutatja, hogy a *autoUpgradeMinorVersion* értéke *true (igaz*):

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Azonosítás a autoUpgradeMinorVersion előfordulásakor

Ha meg szeretné tekinteni, hogy mikor történt frissítés a bővítményre, tekintse át az ügynök naplóit a virtuális gépen a következő címen: */var/log/waagent.log*.

Az alábbi példában a virtuális gépnek telepítve van a *Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9025* . Gyorsjavítás érhető el a *Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9027*:

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

## <a name="agent-permissions"></a>Ügynök engedélyei

A feladatok végrehajtásához az ügynököt *root*-ként kell futtatni.

## <a name="troubleshoot-vm-extensions"></a>Virtuálisgép-bővítmények hibáinak megoldása

Az egyes virtuálisgép-bővítmények a bővítményre vonatkozó hibaelhárítási lépésekkel rendelkezhetnek. Ha például az egyéni szkriptek bővítményét használja, a parancsfájl-végrehajtás részletei helyileg találhatók azon a virtuális gépen, ahol a bővítményt futtatták. A bővítményekre vonatkozó hibaelhárítási lépések részletesen szerepelnek a bővítményekre vonatkozó dokumentációban.

Az alábbi hibaelhárítási lépések minden virtuálisgép-bővítményre érvényesek.

1. A Linux-ügynök naplójának vizsgálatához tekintse meg a tevékenységet, amikor a bővítményt kiépítte a */var/log/waagent.log* -ben

2. A */var/log/Azure/ \<extensionName>* kapcsolatos további részletekért olvassa el a tényleges kiterjesztési naplókat

3. A bővítményekre vonatkozó dokumentációs hibaelhárítási szakaszt a hibakódok, az ismert problémák stb. című szakaszban tekintheti meg.

3. Tekintse meg a rendszernaplókat. Keresse meg azokat a műveleteket, amelyek esetleg zavart okozhattak a bővítménnyel, például egy olyan alkalmazás hosszú futású telepítését, amely kizárólagos csomagkezelő-hozzáférést igényelt.

### <a name="common-reasons-for-extension-failures"></a>A bővítmények meghibásodásának gyakori okai

1. A bővítmények 20 percen keresztül futnak (kivételek a CustomScript Extensions, Chef és DSC, amelyek 90 perc). Ha az üzemelő példány túllépi az időkorlátot, időtúllépésként van megjelölve. Ennek oka az lehet, hogy az alacsony erőforrás-alapú virtuális gépek, más virtuálisgép-konfigurációk/indítási feladatok nagy mennyiségű erőforrást igényelnek, miközben a bővítmény megpróbál kiépíteni.

2. A minimális előfeltételek nem teljesülnek. Egyes bővítmények függőségekkel rendelkeznek a virtuális gépekhez, például a HPC-lemezképekhez. A bővítmények bizonyos hálózati hozzáférési követelményeket igényelhetnek, például az Azure Storage-hoz vagy a nyilvános szolgáltatásokhoz való kommunikációhoz. További példák lehetnek a csomagok tárházához való hozzáférés, a lemezterületek kifogyása vagy a biztonsági korlátozások.

3. Exkluzív csomagkezelő-hozzáférés. Bizonyos esetekben előfordulhat, hogy hosszú ideig futó virtuálisgép-konfigurációt és bővítmények telepítésének ütközését tapasztalja, ahol mindkettőnek kizárólagos hozzáféréssel kell rendelkeznie a csomagkezelő eszközhöz.

### <a name="view-extension-status"></a>Bővítmény állapotának megtekintése

Miután egy virtuálisgép-bővítményt futtatott egy virtuális gépen, az [az VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view) paranccsal adja vissza a bővítmény állapotát a következőképpen:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

A kimenet a következő példában látható kimenethez hasonló:

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

A bővítmény végrehajtási állapota a Azure Portalban is megtalálható. Egy bővítmény állapotának megtekintéséhez válassza ki a virtuális gépet, válassza a **bővítmények** lehetőséget, majd válassza ki a kívánt kiterjesztést.

### <a name="rerun-a-vm-extension"></a>Virtuálisgép-bővítmény újrafuttatása

Előfordulhatnak olyan esetek, amikor egy virtuálisgép-bővítményt újra kell futtatni. A bővítmény újrafuttatásához távolítsa el, majd futtassa újra a bővítményt egy tetszőleges végrehajtási módszer használatával. Egy bővítmény eltávolításához használja [az az VM Extension delete](/cli/azure/vm/extension#az-vm-extension-delete) parancsot a következő módon:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

A bővítményeket a következőképpen is eltávolíthatja a Azure Portalban:

1. Válasszon ki egy virtuális gépet.
2. Válassza a **bővítmények** lehetőséget.
3. Válassza ki a kívánt kiterjesztést.
4. Válassza az **Eltávolítás** lehetőséget.

## <a name="common-vm-extension-reference"></a>Általános virtuálisgép-bővítmény leírása

| Kiterjesztés neve | Leírás | További információ |
| --- | --- | --- |
| Egyéni parancsfájl-kiterjesztés Linux rendszerhez |Parancsfájlok futtatása Azure-beli virtuális gépeken |[Egyéni parancsfájl-kiterjesztés Linux rendszerhez](custom-script-linux.md) |
| Virtuálisgép-hozzáférési bővítmény |Azure-beli virtuális gép hozzáférésének visszanyerése |[Virtuálisgép-hozzáférési bővítmény](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics bővítmény |Azure Diagnostics kezelése |[Azure Diagnostics bővítmény](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM-hozzáférési bővítmény |Felhasználók és hitelesítő adatok kezelése |[VM-hozzáférési bővítmény Linux rendszerhez](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Következő lépések

További információ a virtuálisgép-bővítményekről: [Azure-beli virtuális gépek bővítményei és funkcióinak áttekintése](overview.md).