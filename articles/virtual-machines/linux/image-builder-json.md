---
title: Azure rendszerkép-készítő sablon létrehozása (előzetes verzió)
description: Megtudhatja, hogyan hozhat létre sablont az Azure rendszerkép-készítővel való használatra.
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: aaaabe758b036335062907c8e5549ae876c63997
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594733"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Előzetes verzió: Azure rendszerkép-készítő sablon létrehozása 

Az Azure rendszerkép-készítő egy. JSON-fájllal továbbítja az adatokat a rendszerkép-szerkesztő szolgáltatásba. Ebben a cikkben a JSON-fájl részeit fogjuk felépíteni, így saját maga is létrehozhat. A teljes. JSON fájlokra vonatkozó példákat a következő témakörben tekintheti meg: [Azure rendszerkép-készítő GitHub](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts).

Ez az alapszintű sablon formátuma:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Típus és API-verzió

Az az `type` erőforrástípus, amelynek a következőnek kell lennie: `"Microsoft.VirtualMachineImages/imageTemplates"` . Az az `apiVersion` idő múlásával változik, ahogy az API megváltozik, de az előzetes verziónak kell lennie `"2020-02-14"` .

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Hely

A hely az a régió, ahol az egyéni rendszerkép létre lesz hozva. A rendszerkép-készítő előzetes verziójában a következő régiók támogatottak:

- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA 2. nyugati régiója
- Észak-Európa
- Nyugat-Európa


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Alapértelmezés szerint a rendszerkép-készítő egy "Standard_D1_v2" Build virtuális gépet használ, felülbírálhatja ezt, például ha egy GPU virtuális gép rendszerképét szeretné testre szabni, GPU-s virtuálisgép-méretre van szüksége. Ez nem kötelező.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Alapértelmezés szerint a képszerkesztő nem változtatja meg a rendszerkép méretét, a méretet a forrás rendszerképből fogja használni. **Csak** az operációsrendszer-lemez (Win és Linux) méretének növelésére van lehetőség, ez nem kötelező, és a 0 érték azt jelenti, hogy a forrás képével megegyező méret marad. Az operációsrendszer-lemez mérete nem csökkenthető a forrás rendszerképének méreténél kisebb méretre.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Ha nem ad meg VNET-tulajdonságokat, akkor a rendszerkép-szerkesztő létrehozza a saját VNET, a nyilvános IP-címet és a NSG. A nyilvános IP-cím a szolgáltatásnak a Build virtuális géppel folytatott kommunikációhoz használatos, azonban ha nem szeretne nyilvános IP-címet használni, vagy szeretné, hogy a rendszerkép-készítő hozzáférjen a meglévő VNET-erőforrásokhoz, például a konfigurációs kiszolgálókhoz (DSC, Chef, Puppet, Ansible), fájlmegosztás stb., akkor megadhat egy VNET. További információkért tekintse át a [hálózati dokumentációt](image-builder-networking.md), ez nem kötelező.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Címkék

Ezek a generált rendszerképhez megadható kulcs/érték párok.

## <a name="depends-on-optional"></a>Függ (nem kötelező)

Ez a választható szakasz használható annak biztosítására, hogy a folytatás előtt a függőségek befejeződik. 

```json
    "dependsOn": [],
```

További információ: az [erőforrás-függőségek meghatározása](../../azure-resource-manager/templates/define-resource-dependency.md#dependson).

## <a name="identity"></a>Identitás

Kötelező – ahhoz, hogy a rendszerkép-készítő jogosult legyen a képek olvasására/írására, az Azure Storage-ban lévő parancsfájlok olvasására, létre kell hoznia egy Azure User-Assigned-identitást, amely rendelkezik az egyes erőforrásokhoz szükséges engedélyekkel. A rendszerkép-szerkesztő engedélyeinek működéséről és a vonatkozó lépésekről a [dokumentációban](image-builder-user-assigned-identity.md)olvashat bővebben.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Rendszerkép-szerkesztő támogatása User-Assigned identitáshoz:
* Csak egyetlen identitást támogat
* Nem támogatja az egyéni tartományneveket

További információ: [Mi az az Azure-erőforrások felügyelt identitása?](../../active-directory/managed-identities-azure-resources/overview.md).
A szolgáltatás telepítésével kapcsolatos további információkért lásd: [felügyelt identitások konfigurálása](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)Azure-beli virtuális gépeken az Azure CLI használatával.

## <a name="properties-source"></a>Tulajdonságok: forrás

A `source` szakasz a rendszerkép-szerkesztő által használt forrás-rendszerképről tartalmaz információkat. A rendszerkép-készítő jelenleg csak natív módon támogatja a Hyper-V generációs (Gen1) 1 lemezképek létrehozását az Azure Shared Image Gallery (SIG) vagy a Managed Image használatával. Ha Gen2-lemezképeket szeretne létrehozni, akkor forrás-Gen2 lemezképet kell használnia, és a virtuális merevlemezre kell terjesztenie. Ezután létre kell hoznia egy felügyelt rendszerképet a VHD-ből, és be kell szúrnia a SIG-ba Gen2-képként.

Az API-nak szüksége van egy "forrás típusa" elemre, amely meghatározza a rendszerkép-Build forrását, jelenleg három típus létezik:
- PlatformImage – jelezte, hogy a forrás rendszerkép egy Piactéri rendszerkép.
- ManagedImage – ez a megoldás akkor használható, ha egy normál felügyelt rendszerképből indul.
- SharedImageVersion – ez akkor használatos, ha a rendszerkép verzióját megosztott képtárban használja forrásként.


> [!NOTE]
> Meglévő egyéni Windows-rendszerképek használatakor a Sysprep-parancsot akár 8 alkalommal is futtathatja egyetlen Windows-lemezképen, további információt a [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) dokumentációjában talál.

### <a name="platformimage-source"></a>PlatformImage forrása 
Az Azure Image Builder a Windows Servert és az ügyfelet, valamint a Linux Azure Marketplace-rendszerképeket is támogatja, [itt](../image-builder-overview.md#os-support) találja a teljes listát. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


A virtuális gép létrehozásához használt tulajdonságok ugyanazok, mint az AZ CLI használatával, a tulajdonságok lekéréséhez futtassa az alábbi parancsot: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

A verzióban a legújabbat is használhatja, a verzió kiértékelése a lemezkép létrehozásakor történik, nem pedig a sablon elküldésekor. Ha ezt a funkciót a megosztott képkatalógus céljával használja, elkerülheti a sablon ismételt elküldését, és elvégezheti a lemezképek összeállítását időközökben, így a képek újra létrejönnek a legfrissebb rendszerképekből.

#### <a name="support-for-market-place-plan-information"></a>A piacra helyezési tervre vonatkozó információk támogatása
Megadhatja a terv adatait is, például:
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>ManagedImage forrása

A forrás rendszerképét egy általánosított virtuális merevlemez vagy virtuális gép meglévő felügyelt rendszerképeként állítja be.

> [!NOTE]
> A forrásként felügyelt rendszerképnek támogatott operációs rendszernek kell lennie, és a rendszerképnek azonos régióval kell rendelkeznie, mint az Azure rendszerkép-készítő sablonja. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Az `imageId` értéknek a felügyelt rendszerkép ResourceId kell lennie. `az image list`Az elérhető lemezképek listázásához használható.


### <a name="sharedimageversion-source"></a>SharedImageVersion forrása
Beállítja a forrás rendszerképet egy meglévő rendszerkép-verzióra egy megosztott rendszerkép-gyűjteményben.

> [!NOTE]
> A forrásként felügyelt rendszerképnek támogatott operációs rendszernek kell lennie, és a rendszerképnek azonos régióval kell rendelkeznie, mint az Azure rendszerkép-készítő sablonja, ha nem, akkor replikálja a rendszerkép verzióját a rendszerkép-készítő sablon régiójába.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

A `imageVersionId` ResourceId kell lennie a rendszerkép verziójának. A képverziók listázásához használja [az az SIG rendszerkép-Version List](/cli/azure/sig/image-version#az-sig-image-version-list) lehetőséget.


## <a name="properties-buildtimeoutinminutes"></a>Tulajdonságok: buildTimeoutInMinutes

Alapértelmezés szerint a rendszerkép-szerkesztő 240 percig fog futni. Ezt követően az időtúllépést és leállást eredményez, függetlenül attól, hogy befejeződött-e a rendszerkép létrehozása. Ha az időtúllépés eléri a találatot, a következőhöz hasonló hibaüzenet jelenik meg:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Ha nem ad meg buildTimeoutInMinutes értéket, vagy 0 értékre állítja, akkor ez az alapértelmezett értéket fogja használni. Növelheti vagy csökkentheti az értéket a maximális 960mins (16hrs). A Windows esetében nem ajánlott a 60 percnél régebbi beállítást beállítani. Ha megtalálta az időtúllépést, tekintse át a [naplókat](image-builder-troubleshoot.md#customization-log), és ellenőrizze, hogy a testreszabási lépés a felhasználói adatbevitelre vár-e. 

Ha úgy találja, hogy a testreszabások befejezéséhez több időre van szüksége, állítsa be ezt a kívánt értékre, és egy kis terheléssel. De ne állítsa túl magasra, mert előfordulhat, hogy meg kell várnia az időtúllépést, mielőtt hibaüzenetet lát. 


## <a name="properties-customize"></a>Tulajdonságok: testreszabás

A rendszerkép-szerkesztő több "testreszabó" használatát is támogatja. A testreszabók olyan függvények, amelyek a rendszerkép testreszabására szolgálnak, például parancsfájlok futtatására vagy kiszolgálók újraindítására. 

A használatakor `customize` : 
- Több testreszabó is használható, de egyedieknek kell lenniük `name` .
- A testreszabók a sablonban megadott sorrendben hajthatók végre.
- Ha egy testreszabó nem sikerül, a teljes testreszabási összetevő sikertelen lesz, és hibát jelez.
- Nyomatékosan javasoljuk, hogy alaposan tesztelje a parancsfájlt, mielőtt használni lehetne a sablonban. A parancsfájl hibakeresése a saját virtuális gépen egyszerűbb lesz.
- Ne helyezzen bizalmas adatokat a parancsfájlokba. 
- A parancsfájl helyeinek nyilvánosan elérhetőnek kell lenniük, hacsak nem használ [MSI](./image-builder-user-assigned-identity.md)-t.

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
A Testreszabás szakasz egy tömb. Az Azure rendszerkép-szerkesztő szekvenciális sorrendben fut a testreszabók között. Bármely testreszabó hibája sikertelen lesz a létrehozási folyamat során. 

> [!NOTE]
> A beágyazott parancsok megtekinthetők a rendszerkép-sablon definíciójában és Microsoft ügyfélszolgálata a támogatási esetekkel való segítségnyújtás során. Ha bizalmas adatokkal rendelkezik, azt az Azure Storage-ban lévő parancsfájlokba helyezheti át, ahol a hozzáférés hitelesítés szükséges.
 
### <a name="shell-customizer"></a>Rendszerhéj-testreszabó

A rendszerhéj-testreszabó támogatja a rendszerhéj-parancsfájlok futtatását, ezeknek nyilvánosan elérhetőnek kell lenniük az IB-hez való hozzáféréshez.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Operációs rendszer támogatása: Linux 
 
Tulajdonságok testreszabása:

- **típus** – rendszerhéj 
- **név** – a Testreszabás követésének neve 
- **scriptUri** – URI a fájl helyéhez 
- beépített rendszerhéj-parancsok **beágyazott** tömbje, vesszővel elválasztva.
- **sha256Checksum** – a fájl sha256-ellenőrzőösszegének értéke, ezt helyileg létrehozhatja, majd a rendszerkép-szerkesztő ellenőrzőösszeget és érvényesítést végez.
    * A sha256Checksum létrehozása Mac/Linux rendszeren futó terminál használatával: `sha256sum <fileName>`

> [!NOTE]
> A beágyazott parancsokat a rendszer a rendszerkép-sablon definíciójának részeként tárolja, ezeket a képek definíciójának kiírásakor láthatja, és a hibaelhárítási célból a Microsoft ügyfélszolgálata is láthatók. Ha bizalmas parancsokkal vagy értékekkel rendelkezik, erősen ajánlott ezeket a parancsfájlokba áthelyezni, és felhasználói identitás használatával hitelesíteni az Azure Storage-ban.

#### <a name="super-user-privileges"></a>Felügyelői jogosultságok
A felügyelői jogosultságokkal futtatandó parancsok esetében a `sudo` következőt kell megadnia a parancsfájlokhoz, vagy használhatja azt beágyazott parancsokkal, például:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
Példa a sudo-t használó parancsfájlra, amely a scriptUri használatával hivatkozhat:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Windows újraindítási testreszabó 
Az újraindítási testreszabó lehetővé teszi egy Windows rendszerű virtuális gép újraindítását, és várjon, amíg online állapotba kerül, így olyan szoftvereket telepíthet, amelyek újraindítást igényelnek.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Operációs rendszer támogatása: Windows
 
Tulajdonságok testreszabása:
- **Típus**: WindowsRestart
- **restartCommand** – az újraindítás végrehajtásához szükséges parancs (nem kötelező). A mező alapértelmezett értéke: `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – a parancs segítségével ellenőrizze, hogy az újraindítás sikeres volt-e (nem kötelező). 
- **restartTimeout** – újraindítási időkorlát, amely a magnitúdó és az egység értékének megfelelően van megadva. Például: `5m` (5 perc) vagy `2h` (2 óra). Az alapértelmezett érték: "5m"

### <a name="linux-restart"></a>Linux újraindítása  
Nincs Linux-újraindítási testreszabó, azonban ha olyan illesztőprogramokat vagy összetevőket telepít, amelyek újraindítást igényelnek, akkor telepítheti őket, és a rendszerhéj-testreszabó használatával meghívhatják az újraindítást a 20min SSH-időtúllépéssel a Build virtuális géphez.

### <a name="powershell-customizer"></a>PowerShell-testreszabó 
A rendszerhéj-testreszabó támogatja a PowerShell-parancsfájlok és a beágyazott parancsok futtatását, a parancsfájloknak nyilvánosan elérhetőnek kell lenniük az IB-hez való hozzáféréshez.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Operációs rendszer támogatása: Windows és Linux

Tulajdonságok testreszabása:

- **típus** – PowerShell.
- **scriptUri** – URI a PowerShell-parancsfájl helyéhez. 
- **beágyazott** – a futtatandó beágyazott parancsok, vesszővel elválasztva.
- **validExitCodes** – választható, érvényes kódok, amelyek a parancsfájl/beágyazott parancsból visszaadhatók, így elkerülhető a parancsfájl/beágyazott parancs hibájának jelentése.
- **runElevated** – opcionális, logikai, támogatás emelt szintű engedélyekkel rendelkező parancsok és parancsfájlok futtatásához.
- **sha256Checksum** – a fájl sha256-ellenőrzőösszegének értéke, ezt helyileg létrehozhatja, majd a rendszerkép-szerkesztő ellenőrzőösszeget és érvényesítést végez.
    * A sha256Checksum létrehozása a Windows [Get-hash](/powershell/module/microsoft.powershell.utility/get-filehash) PowerShell használatával


### <a name="file-customizer"></a>Fájl-testreszabó

A fájl-testreszabó lehetővé teszi, hogy a rendszerkép-készítő letöltse a fájlt egy GitHub vagy egy Azure Storage szolgáltatásból. Ha olyan rendszerkép-létrehozási folyamattal rendelkezik, amely a Build-összetevőkre támaszkodik, akkor a fájl-testreszabó beállítható úgy, hogy letöltse a Build megosztásról, és áthelyezi az összetevőket a rendszerképbe.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Operációs rendszer támogatása: Linux és Windows 

Fájl-testreszabó tulajdonságai:

- **sourceUri** – elérhető tárolási végpont, ez lehet GitHub vagy Azure Storage. Csak egy fájlt tölthet le, nem egy teljes könyvtárat. Ha le kell töltenie egy könyvtárat, használjon tömörített fájlt, majd bontsa ki a tömörítést a rendszerhéj vagy a PowerShell-testreszabók használatával. 

> [!NOTE]
> Ha a sourceUri egy Azure Storage-fiók, függetlenül attól, hogy a blob nyilvánosan megjelölve van-e, meg kell adnia a felügyelt felhasználó identitásának engedélyeit a blobhoz való hozzáféréshez. A tárolási engedélyek megadásához tekintse meg ezt a [példát](./image-builder-user-assigned-identity.md#create-a-resource-group) .

- **cél** – ez a célhely teljes elérési útja és fájlneve. Minden hivatkozott útvonalnak és alkönyvtárnak léteznie kell, a rendszerhéj vagy a PowerShell-testreszabók használatával előre beállíthatja ezeket. Az elérési út létrehozásához használhatja a parancsfájl-testreszabók lehetőséget. 

Ezt a Windows-címtárak és a Linux-elérési utak támogatják, de vannak különbségek: 
- Linux operációs rendszer – az egyetlen elérési út a rendszerkép-készítő a/tmp.
- Windows – nincs elérésiút-korlátozás, de az elérési útnak léteznie kell.
 
 
Ha hiba történt a fájl letöltése vagy egy megadott címtárba való behelyezése során, a testreszabási lépés sikertelen lesz, és ez a customization. log fájlban lesz.

> [!NOTE]
> A fájl-testreszabó csak kisméretű fájlok letöltésére alkalmas, < 20 MB-ot. A nagyobb fájlok letöltéséhez parancsfájl vagy beágyazott parancs használatos, a fájlok letöltésére szolgáló kód (például:, Linux `wget` vagy `curl` Windows `Invoke-WebRequest` ).

### <a name="windows-update-customizer"></a>Windows Update testreszabása
Ez a testreszabó a Packer [közösségi Windows Update-kiépítési](https://packer.io/docs/provisioners/community-supported.html) csomagjára épül, amely egy nyílt forráskódú projekt, amelyet a csomagoló Közösség tart fenn. A Microsoft a rendszerkép-készítő szolgáltatással teszteli és érvényesíti a kiépítő szolgáltatást, és támogatja a problémák megoldását, a Microsoft pedig hivatalosan nem támogatja a nyílt forráskódú projektet. A Windows Update-létesítéssel kapcsolatos részletes dokumentációt és segítséget a Project adattárában talál.

```json
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS support: Windows
```

Tulajdonságok testreszabása:
- **típus**  – windowsupdate.
- **searchCriteria** – nem kötelező, meghatározza, hogy a rendszer milyen típusú frissítéseket telepítsen (ajánlott, fontos stb.), BrowseOnly = 0 és IsInstalled = 0 (ajánlott) az alapértelmezett érték.
- **szűrők** – nem kötelező, lehetővé teszi egy szűrő megadását a frissítések belefoglalásához vagy kizárásához.
- **updateLimit** – nem kötelező, meghatározza, hogy hány frissítést lehet telepíteni, alapértelmezett érték: 1000.
 
> [!NOTE]
> A Windows Update-testreszabó sikertelen lehet, ha fennáll a Windows újraindítása, vagy az alkalmazás telepítése még fut, ez a hiba általában a Testreszabás. naplóban jelenhet meg `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` . Határozottan javasoljuk, hogy vegye fel a Windows újraindítását, és/vagy engedélyezze az alkalmazások számára, hogy az [alvó](/powershell/module/microsoft.powershell.utility/start-sleep) vagy várakozási parancsokkal a beágyazott parancsokban vagy parancsfájlokban a Windows Update futtatása előtt elvégezze a telepítést.

### <a name="generalize"></a>Általánossá tétel 
Alapértelmezés szerint az Azure-rendszerkép-készítő a rendszerkép testreszabási fázisának végén a "megszüntetés" kódot is futtatja, hogy "általánosítsa" a képet. Az általánosítás egy olyan folyamat, amelyben a rendszerkép be van állítva, így több virtuális gép létrehozására is felhasználható. Windows rendszerű virtuális gépek esetén az Azure rendszerkép-készítő a Sysprept használja. Linux rendszeren az Azure rendszerkép-szerkesztő "waagent"-megszüntetést futtat. 

Előfordulhat, hogy a rendszerkép-szerkesztő felhasználói általánosított parancsai nem alkalmasak minden helyzetben, így az Azure rendszerkép-készítő lehetővé teszi, hogy szükség esetén testreszabja ezt a parancsot. 

Ha meglévő testreszabást telepít át, és különböző Sysprep/waagent parancsokat használ, használhatja a rendszerkép-készítő általános parancsait, és ha a virtuális gép létrehozása nem sikerül, használja a saját Sysprep-vagy waagent-parancsait.

Ha az Azure rendszerkép-készítő sikeresen létrehoz egy egyéni Windows-rendszerképet, és létrehoz egy virtuális gépet, akkor a virtuális gép létrehozása sikertelen lesz, vagy nem fejeződik be sikeresen, át kell tekintenie a Windows Server Sysprep dokumentációját, vagy támogatási kérelmet kell létrehoznia a Windows Server Sysprep Customer Services támogatási csapatával, aki a megfelelő Sysprep-használattal kapcsolatos hibakeresést és tanácsadást nyújt.


#### <a name="default-sysprep-command"></a>Alapértelmezett Sysprep-parancs
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>Alapértelmezett Linux-kiépítés parancs

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>A parancsok felülbírálása
A parancsok felülbírálásához a PowerShell vagy a rendszerhéj parancsfájl-kiépítő használatával hozza létre a megfelelő fájlnevet tartalmazó parancsfájlokat, és helyezze azokat a megfelelő könyvtárakba:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/DeprovisioningScript.sh

A képszerkesztő beolvassa ezeket a parancsokat, ezeket a rendszer kiírja a "customization. log" AIB-naplókba. Lásd: a naplók gyűjtésével [kapcsolatos hibaelhárítás](image-builder-troubleshoot.md#customization-log) .
 
## <a name="properties-distribute"></a>Tulajdonságok: terjesztés

Az Azure rendszerkép-szerkesztő három terjesztési célt támogat: 

- **managedImage** által felügyelt rendszerkép.
- **sharedImage** – közös rendszerkép-gyűjtemény.
- **VHD** – VHD egy Storage-fiókban.

A rendszerképeket az azonos konfigurációban lévő összes cél típusra terjesztheti.

> [!NOTE]
> Az alapértelmezett AIB Sysprep-parancs nem tartalmazza a "/Mode: VM" parancsot, azonban ez valószínűleg szükséges ahhoz, hogy olyan lemezképeket hozzon létre, amelyeken telepítve lesz a HyperV szerepkör. Ha ezt a parancs-argumentumot fel kell vennie, felül kell bírálnia a Sysprep parancsot.

Mivel több célhely is kiterjeszthető a szolgáltatásba, a rendszerkép-szerkesztő minden olyan terjesztési cél állapotát fenntartja, amely elérhető a lekérdezéssel `runOutputName` .  Az egy olyan `runOutputName` objektum, amelyről lekérdezheti a terjesztés utáni adatokat. Lekérdezheti például a virtuális merevlemez helyét, illetve azokat a régiókat, amelyeken a rendszerkép verziója replikálva lett, vagy a SIG-rendszerkép verziója létrejött. Ez az összes terjesztési cél tulajdonsága. A `runOutputName` -nek egyedinek kell lennie az egyes terjesztési célkitűzéseknél. Íme egy példa, amely egy megosztott képgyűjtemény-eloszlás lekérdezését szemlélteti:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

Kimenet:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Terjesztés: managedImage

A rendszerkép kimenete felügyelt rendszerkép-erőforrás lesz.

```json
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
```
 
Elosztás tulajdonságai:
- **típus** – managedImage 
- **imageId** – a célként megadott rendszerkép erőforrás-azonosítója, a várt formátum:/Subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **hely** – a felügyelt rendszerkép helye.  
- **runOutputName** – a terjesztés azonosítására szolgáló egyedi név.  
- **artifactTags** – opcionális felhasználó által megadott kulcs érték párok címkéi.
 
 
> [!NOTE]
> A cél erőforráscsoport léteznie kell.
> Ha azt szeretné, hogy a rendszerképet egy másik régióba lehessen terjeszteni, azzal megnöveli a központi telepítés idejét. 

### <a name="distribute-sharedimage"></a>Terjesztés: sharedImage 
Az Azure Shared Image Gallery egy új rendszerkép-kezelő szolgáltatás, amely lehetővé teszi a lemezkép-régiók replikálásának, verziószámozásának és az Egyéni rendszerképek megosztásának kezelését. Az Azure Image Builder támogatja a szolgáltatás terjesztését, így képeket oszthat meg a megosztott képtárak által támogatott régiókba. 
 
A megosztott képkatalógusok a következőket alkotják: 
 
- Gallery – több megosztott rendszerkép tárolója. Egy gyűjtemény egy régióban van üzembe helyezve.
- Képdefiníciók – a rendszerképek fogalmi csoportosítása. 
- Lemezkép-verziók – ez egy virtuális gép vagy méretezési csoport üzembe helyezésére szolgáló rendszerkép-típus. A lemezkép-verziók más régiókban is replikálhatók, ahol a virtuális gépeket telepíteni kell.
 
A lemezkép-katalógusba való terjesztés előtt létre kell hoznia egy gyűjteményt és egy rendszerkép-definíciót, lásd: [megosztott lemezképek](../shared-images-cli.md). 

```json
{
    "type": "SharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Megosztott képtárak tulajdonságainak terjesztése:

- **típus** – sharedImage  
- **galleryImageId** – a megosztott rendszerkép-katalógus azonosítója, amely két formátumban adható meg:
    * Automatikus verziószámozás – a Image Builder létrehoz egy monoton verziószámot, ez akkor hasznos, ha meg szeretné őrizni a lemezképek újraépítését ugyanabból a sablonból: a formátum a következő: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Explicit verziószámozás – a rendszerkép-készítő által használandó verziószámot átadhatja. A formátum: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** – a terjesztés azonosítására szolgáló egyedi név.  
- **artifactTags** – opcionális felhasználó által megadott kulcs érték párok címkéi.
- **replicationRegions** – a replikálási régiók tömbje. Az egyik régió az a régió, amelyben a katalógus üzembe van helyezve. A régiók hozzáadásával növekszik a felépítési idő, mivel a Build nem fejeződött be, amíg a replikáció be nem fejeződik.
- **excludeFromLatest** (nem kötelező) Ez lehetővé teszi, hogy a létrehozott rendszerkép-verziót ne használja a SIG-definíció legújabb verziójának, az alapértelmezett érték a "false".
- **tárfióktípus** (nem kötelező) a AIB támogatja az ilyen típusú tárolók megadását a létrehozandó rendszerkép-verzióhoz:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Ha a Képsablon és a hivatkozott adatok `image definition` nem ugyanazon a helyen találhatók, akkor további időt fog látni a rendszerképek létrehozásához. A rendszerkép-szerkesztő jelenleg nem rendelkezik `location` paraméterrel a rendszerkép-verzió erőforráshoz, ezért a szülőtől veszi át `image definition` . Ha például a rendszerkép definíciója a westus-ben van, és azt szeretné, hogy a rendszerkép verziója replikálva legyen a eastus-re, a rendszer a blobot a westus-be másolja, ebből a rendszerkép-verzió erőforrás a westus-ben, majd a replikálása a eastus. A további replikációs idő elkerüléséhez győződjön meg arról, hogy a `image definition` és a lemezkép sablonja ugyanazon a helyen található.


### <a name="distribute-vhd"></a>Terjesztés: VHD  
A kimenetet virtuális merevlemezre is kipróbálhatja. Ezután átmásolhatja a VHD-t, és felhasználhatja az Azure MarketPlace-en való közzétételhez, vagy használhatja Azure Stack.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
Operációs rendszer támogatása: Windows és Linux

VHD-paraméterek terjesztése:

- **típus** – VHD.
- **runOutputName** – a terjesztés azonosítására szolgáló egyedi név.  
- **címkék** – nem kötelező felhasználó által megadott kulcs-érték párok címkéi.
 
Az Azure rendszerkép-készítő nem teszi lehetővé a felhasználó számára a tárolási fiók helyének megadását, de lekérdezheti az állapotát a hely lekéréséhez `runOutputs` .  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> A virtuális merevlemez létrehozása után a lehető leghamarabb másolja át egy másik helyre. A virtuális merevlemezt az ideiglenes erőforráscsoport tárolja, amely akkor jön létre, amikor a rendszer elküldi a képsablont az Azure rendszerkép-szerkesztő szolgáltatásba. Ha törli a képsablont, akkor elveszíti a VHD-t. 

## <a name="image-template-operations"></a>Rendszerkép-sablon műveletei

### <a name="starting-an-image-build"></a>Rendszerkép létrehozásának indítása
A Build elindításához a "Run" meghívására van szükség a Képsablon erőforrásán, például a következő `run` parancsokra:

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>Rendszerkép-Build megszakítása
Ha olyan rendszerkép-buildet futtat, amely úgy véli, hogy helytelen, a felhasználói bevitelre vár, vagy ha úgy érzi, hogy soha nem fejeződik be, akkor megszakíthatja a fordítást.

A Build bármikor megszakítható. Ha megkezdődött a terjesztési fázis, akkor továbbra is megszakítható, de az esetlegesen nem befejezett rendszerképeket törölni kell. A Mégse parancs nem várja meg a megszakítás befejeződését, figyelje `lastrunstatus.runstate` a folyamat megszakítását, ezekkel az állapot- [parancsokkal](image-builder-troubleshoot.md#customization-log).


Példák a `cancel` parancsokra:

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>Következő lépések

Az [Azure rendszerkép-készítő githubon](https://github.com/azure/azvmimagebuilder)különböző forgatókönyvekhez készült minta. JSON fájlok találhatók.