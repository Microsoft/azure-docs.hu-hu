---
title: Azure-beli Image Builder létrehozása (előzetes verzió)
description: Megtudhatja, hogyan hozhat létre sablont az Azure Image Builder.
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: 77460d1675b806e04c72e5f46da0ec4274d99d41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762532"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Előzetes verzió: Azure Image Builder létrehozása 

Az Azure Image Builder .json-fájl használatával ad át adatokat a Image Builder szolgáltatásnak. Ebben a cikkben a json-fájl szakaszait fogjuk átveszni, hogy sajátokat építsen ki. A teljes .json-fájlok példáit az [Azure Image Builder GitHubon találja.](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts)

Ez az alapszintű sablonformátum:

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

A `type` az erőforrástípus, amelynek a következőnek kell lennie: `"Microsoft.VirtualMachineImages/imageTemplates"` . Az `apiVersion` API idővel változni fog, de előzetes `"2020-02-14"` verzióban kell lennie.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Hely

A hely az a régió, ahol az egyéni rendszerkép létre lesz hozva. A Image Builder előzetes verziója a következő régiókat támogatja:

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
Alapértelmezés szerint Image Builder "Standard_D1_v2" build vm-et fog használni, ezt felülírhatja, például ha egy GPU-alapú virtuális gép rendszerképét szeretné testreszabni, GPU virtuálisgép-méretre van szüksége. Ez nem kötelező.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Alapértelmezés szerint Image Builder nem módosítja a kép méretét, hanem a forrásként használt kép méretét fogja használni. Csak  az operációsrendszer-lemez méretét növelheti (Win és Linux), ez nem kötelező, és a 0 érték azt jelenti, hogy a forráslemezkép méretének meghagyása. Az operációsrendszer-lemez mérete nem csökkenthető a forráslemezkép méretének kisebbre.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Ha nem ad meg VNET-tulajdonságokat, a Image Builder a saját VNET-et, a nyilvános IP-címet és az NSG-t. A nyilvános IP-cím arra szolgál, hogy a szolgáltatás kommunikáljon a build virtuális gépével, de ha nem szeretne nyilvános IP-címet, vagy ha azt szeretné, hogy a Image Builder hozzáférjen a meglévő VNET-erőforrásokhoz, például konfigurációs kiszolgálókhoz (DSC, Chef, Puppet, Ansible), fájlmegosztáshoz stb., megadhat egy VNET-et. További információért tekintse át a [hálózati dokumentációt.](image-builder-networking.md)Ez nem kötelező.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Címkék

Ezek kulcs/érték párok, amelyek megadhatók a létrehozott rendszerképhez.

## <a name="depends-on-optional"></a>Függ (nem kötelező)

Ez a választható szakasz annak biztosítására használható, hogy a függőségek befejeződtek-e, mielőtt továbblépne. 

```json
    "dependsOn": [],
```

További információ: [Erőforrás-függőségek definiálása.](../../azure-resource-manager/templates/define-resource-dependency.md#dependson)

## <a name="identity"></a>Identitás

Kötelező – Ahhoz, Image Builder a rendszerképek olvasására/írására vonatkozó engedélyekkel rendelkezik, az Azure Storage-szkriptek olvasásához létre kell hoznia egy Azure User-Assigned-identitást, amely engedéllyel rendelkezik az egyes erőforrásokhoz. Az engedélyek Image Builder vonatkozó részletekért és a vonatkozó lépésekért tekintse át a [dokumentációt.](image-builder-user-assigned-identity.md)


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Image Builder identity User-Assigned támogatása:
* Csak egyetlen identitást támogat
* Nem támogatja az egyéni tartományneveket

További tudnivalókért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../../active-directory/managed-identities-azure-resources/overview.md).
További információ a szolgáltatás üzembe helyezéséről: Azure-erőforrások felügyelt identitásának konfigurálása [Azure-beli](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)virtuális gépen az Azure CLI használatával.

## <a name="properties-source"></a>Tulajdonságok: forrás

A `source` szakasz a forrásként használt rendszerképről tartalmaz információkat, amelyet a Image Builder. Image Builder jelenleg csak natív módon támogatja a Hyper-V generation (Gen1) 1 rendszerképek létrehozását az Azure Shared Image Gallery (SIG) vagy a Managed Image szolgáltatásban. Ha Gen2-rendszerképeket szeretne létrehozni, akkor egy 2. generációs forrásként használt rendszerképet kell használnia, és el kell terjesztenie a VHD-re. Ezt követően létre kell hoznia egy felügyelt rendszerképet a VHD-ről, és be kell azt injektálni a SIG-be Gen2-rendszerképként.

Az API-nak szüksége van egy SourceType típusra, amely meghatározza a rendszerkép-build forrását. Jelenleg három típus létezik:
- PlatformImage – azt jelzi, hogy a forrásként megadott rendszerkép egy Marketplace-rendszerkép.
- ManagedImage – ezt használja, ha normál felügyelt rendszerképből indul ki.
- SharedImageVersion – Ezt a rendszerképverziók forrásként való Shared Image Gallery használja.


> [!NOTE]
> Meglévő egyéni Windows-rendszerképek használata esetén legfeljebb 8 alkalommal futtathatja a Sysprep parancsot egyetlen Windows-rendszerképen. További információért tekintse meg a [sysprep dokumentációját.](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)

### <a name="platformimage-source"></a>PlatformImage forrás 
Az Azure Image Builder támogatja a Windows Servert és az [](../image-builder-overview.md#os-support) ügyfelet, valamint a Linux Azure Marketplace-rendszerképeket. A teljes listát itt láthatja. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


A tulajdonságok itt ugyanazok, amelyek a virtuális gépek létrehozásához használhatók az AZ CLI használatával, a tulajdonságok lekért létrehozásához futtassa az alábbi parancsot: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

A verzióban használhatja a "latest" (legújabb) verziót. A verzió a rendszerkép buildlésekor lesz kiértékelve, nem pedig a sablon beküldésekor. Ha ezt Shared Image Gallery funkciót a Shared Image Gallery-célhelyen használja, elkerülheti a sablon újbóli be- és újrafuttathatóságát, és időközönként újrafuttathatja a rendszerkép buildet, hogy a rendszerképeket a rendszer a legújabb rendszerképekből hozza létre újra.

#### <a name="support-for-market-place-plan-information"></a>Market Place Plan Information támogatása
A tervinformációk is megadhatók, például:
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
### <a name="managedimage-source"></a>ManagedImage-forrás

A forrásként használt rendszerképet egy általános vhD vagy virtuális gép meglévő felügyelt rendszerképeként állítja be.

> [!NOTE]
> A forrásként felügyelt rendszerképnek támogatott operációs rendszernek kell lennie, és a rendszerképnek és az Azure-beli virtuális Image Builder kell lennie. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

A `imageId` fájlnak a felügyelt rendszerkép ResourceId-nek kell lennie. Az `az image list` elérhető rendszerképek listához használja a következőt: .


### <a name="sharedimageversion-source"></a>SharedImageVersion forrás
Beállítja a forrásként egy meglévő rendszerképverziót egy Shared Image Gallery.

> [!NOTE]
> A forrásként felügyelt rendszerképnek támogatott operációs rendszernek kell lennie, és a rendszerképnek ugyanannak a régiónak kell lennie, mint az Azure Image Builder-sablonnak. Ha nem, replikálja a rendszerkép verzióját a Image Builder sablon régiójába.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`A-nek a rendszerkép verziójának ResourceId-nek kell lennie. Az [az sig image-version list használatával](/cli/azure/sig/image-version#az_sig_image_version_list) listába sorolja a rendszerkép verzióit.


## <a name="properties-buildtimeoutinminutes"></a>Tulajdonságok: buildTimeoutInMinutes

Alapértelmezés szerint a Image Builder 240 percig fog futni. Ezután időtúllépést és leállítást fog végrehajtani, függetlenül attól, hogy a rendszerkép builde befejeződött-e. Ha az időtúllépést észlelte, az ehhez hasonló hibaüzenet jelenik meg:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Ha nem ad meg buildTimeoutInMinutes értéket, vagy 0 értéket ad meg, ez az alapértelmezett értéket fogja használni. Az értéket a maximális 960 perc (16 óra) értékig növelheti vagy csökkentheti. Windows esetén nem javasoljuk, hogy 60 percnél alacsonyabbra ássa ezt a beállítást. Ha az időtúllépést látja, tekintse [](image-builder-troubleshoot.md#customization-log)át a naplókat, és ellenőrizze, hogy a testreszabási lépés a felhasználói bevitelhez hasonlóra vár-e. 

Ha úgy találja, hogy több időre van szüksége a testreszabások befejezéséhez, állítsa be a saját úgy gondolja, hogy szüksége van rá egy kis többletterheléssel. Ne állítsa azonban túl magasra, mert előfordulhat, hogy meg kell várnia az időtúllépést, mielőtt hibát lát. 


## <a name="properties-customize"></a>Tulajdonságok: testreszabás

Image Builder "testreszabók" több is támogatottak. A testreszabók olyan függvények, amelyek testre szabják a rendszerképet, például szkriptek futtatása vagy kiszolgálók újraindítása. 

A használata `customize` esetén: 
- Több testreszabó is használható, de egyedinek kell `name` lennie.
- A testreszabók a sablonban megadott sorrendben futnak le.
- Ha egy testreszabó meghibásodik, akkor a teljes testreszabási összetevő meghibásodik, és hibát jelez.
- Erősen ajánlott alaposan tesztelni a szkriptet, mielőtt felhasználja egy sablonban. A szkript hibakeresése a saját virtuális gépén egyszerűbb lesz.
- Ne helyezzen bizalmas adatokat a szkriptekbe. 
- A szkripthelyeknek nyilvánosan elérhetőknek kell lenniük, kivéve, ha [MSI-t használ.](./image-builder-user-assigned-identity.md)

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

 
A testreszabási szakasz egy tömb. Az Azure Image Builder sorrendben futtatja a testreszabókon. A testreszabó bármilyen hibája sikertelen lesz a buildfolyamatban. 

> [!NOTE]
> A beágyazott parancsokat megtekintheti a rendszerképsablon definíciójában, és Microsoft ügyfélszolgálata segítségért a támogatási esethez. Ha bizalmas adatokkal rendelkezik, át kell őket áthelyezni az Azure Storage-szkriptekbe, ahol a hozzáférés hitelesítést igényel.
 
### <a name="shell-customizer"></a>Rendszerhéj-testreszabó

A rendszerhéj-testreszabó támogatja a rendszerhéjszk szkriptek futtatását, és ezeknek nyilvánosan elérhetőnek kell lenniük az IB számára azok eléréséhez.

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

- **type** – Shell 
- **name** – a testreszabás nyomon követésének neve 
- **scriptUri** – A fájl helyének URI-ja 
- **beágyazott –** héjparancsok tömbje, vesszővel elválasztva.
- **sha256Checksum** – A fájl sha256 ellenőrzőumának értéke, ezt helyben hozza létre, majd Image Builder ellenőrző és érvényesíti.
    * Az sha256Checksum létrehozásához futtassa a következőt egy Mac/Linux rendszerű terminál használatával: `sha256sum <fileName>`

> [!NOTE]
> A beágyazott parancsokat a rendszer a rendszerképsablon definíciójának részeként tárolja. Ezeket a rendszerkép-definíció kiírásakor láthatja, és hibaelhárítási támogatási eset esetén az Microsoft ügyfélszolgálata számára is láthatók. Ha bizalmas parancsokat vagy értékeket használ, erősen ajánlott ezeket áthelyezni szkriptekbe, és felhasználói identitással hitelesíteni az Azure Storage-ban.

#### <a name="super-user-privileges"></a>Felügyelői jogosultságok
Ahhoz, hogy a parancsok felügyelői jogosultságokkal fusson, előtagjuknak a előtaggal kell lennie, ezeket hozzáadhatja szkriptekhez, vagy használhat beágyazott `sudo` parancsokat, például:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
Példa sudo-t használó szkriptre, amelyre a scriptUri használatával hivatkozhat:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Windows újraindítás-testreszabazó 
Az Újraindítás testreszabó lehetővé teszi egy Windows rendszerű virtuális gép újraindítását, majd annak online állapotba való visszavárását. Ez lehetővé teszi újraindítást igénylő szoftverek telepítését.  

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
- **Típus:** WindowsRestart
- **restartCommand** – Parancs az újraindítás végrehajtásához (nem kötelező). A mező alapértelmezett értéke: `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – Az újraindítás sikeres volt-e (nem kötelező) ellenőrzéséhez szükséges parancs. 
- **restartTimeout** – Az újraindítás időkorlátja nagyságrendű és egységnyi sztringként van megadva. Például: `5m` (5 perc) vagy `2h` (2 óra). Az alapértelmezett érték: "5m"

### <a name="linux-restart"></a>Linux újraindítása  
A Linux újraindítási testreszabója azonban nincs, ha azonban olyan illesztőprogramokat vagy összetevőket telepít, amelyek újraindítást igényelnek, telepítheti őket, és újraindíthatja őket a rendszerhéj-testreszabó használatával. A build vm 20 perc SSH-időtúllépést hoz létre.

### <a name="powershell-customizer"></a>PowerShell-testreszabó 
A rendszerhéj-testreszabó támogatja a PowerShell-szkriptek és a beágyazott parancsok futtatását, a szkriptnek nyilvánosan elérhetőnek kell lennie az IB számára azok eléréséhez.

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

- **type** – PowerShell.
- **scriptUri** – URI a PowerShell-parancsfájl helyére. 
- **inline** – Futtatandó beágyazott parancsok, vesszővel elválasztva.
- **validExitCodes** – Nem kötelező, érvényes kódok, amelyek visszaadhatóak a script/inline parancsból, így elkerülhetők a script/inline parancs jelentett meghibásodásai.
- **runElevated** – Választható, logikai, emelt szintű engedélyekkel rendelkező parancsok és szkriptek futtatásának támogatása.
- **sha256Checksum** – A fájl sha256 ellenőrzőumának értéke, ezt helyben hozza létre, majd Image Builder ellenőrző és érvényesíti.
    * Az sha256Checksum létrehozása PowerShell használatával Windows [Get-Hash rendszeren](/powershell/module/microsoft.powershell.utility/get-filehash)


### <a name="file-customizer"></a>Fájlszakorátor

A Fájlszakoráló lehetővé teszi, hogy a képkészítő letöltsen egy fájlt egy GitHub- vagy Azure Storage-tárolóból. Ha olyan rendszerkép-összeállítási folyamata van, amely build-összetevőkre támaszkodik, beállíthatja a fájl testreszakorálóját úgy, hogy a buildmegosztásból töltse le a fájlt, és helyezze át az összetevőket a rendszerképbe.  

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

Fájl testreszakoráló tulajdonságai:

- **sourceUri** – egy elérhető tárolási végpont, amely a GitHub vagy az Azure Storage lehet. Csak egy fájlt tölthet le, egy teljes könyvtárat nem. Ha le kell töltenie egy könyvtárat, használjon tömörített fájlt, majd a Rendszerhéj vagy a PowerShell testreszabók használatával tömörítse le. 

> [!NOTE]
> Ha a sourceUri egy Azure Storage-fiók, függetlenül attól, hogy a blob nyilvánosként van-e megjelölve, engedélyt kell ad a felügyelt felhasználói identitásnak a blob olvasási hozzáférésére. A tárolási engedélyek [beállítását](./image-builder-user-assigned-identity.md#create-a-resource-group) ebben a példában láthatja.

- **destination** (cél) – ez a teljes célútvonal és fájlnév. Minden hivatkozott elérési útnak és alkönyvtárnak léteznie kell. Ezeket a rendszerhéj- vagy PowerShell-testreszabók használatával állíthatja be előre. Az elérési út létrehozásához használhatja a szkript-testreszabadolókat. 

Ezt a Windows-könyvtárak és a Linux-elérési utak támogatják, de van néhány különbség: 
- Linux operációs rendszer – a Képkészítő csak a /tmp elérési utat tudja írni.
- Windows – Nincs elérésiút-korlátozás, de az elérési útnak léteznie kell.
 
 
Ha hiba történik a fájl letöltésekor vagy egy megadott könyvtárban való közzétételekor, a testreszabási lépés sikertelen lesz, és ez a customization.log fájlban található.

> [!NOTE]
> A fájl testreszabó csak kis méretű, 20 MB< méretű fájlok letöltésére alkalmas. Nagyobb fájlletöltések esetén használjon szkriptet vagy beágyazott parancsot, és a kód használatával töltsön le fájlokat( például Linux `wget` vagy `curl` , Windows, `Invoke-WebRequest` ).

### <a name="windows-update-customizer"></a>Windows Update Customizer
Ez a testreszabó a [Packer Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) közösségére épül, amely egy, a Packer-közösség által fenntartott nyílt forráskódú projekt. A Microsoft teszteli és ellenőrzi a kiépítést az Image Builder szolgáltatással, támogatja a vele kapcsolatos problémák kivizsgálását, és a problémák megoldásán dolgozik, a Microsoft azonban hivatalosan nem támogatja a nyílt forráskódú projektet. Az Windows Update Provisionerrel kapcsolatos részletes dokumentációért és segítségért tekintse meg a projekt adattárát.

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
- **type**  – WindowsUpdate.
- **searchCriteria** – Nem kötelező, meghatározza a telepített frissítések típusát (Ajánlott, Fontos stb.), a BrowseOnly=0 és az IsInstalled=0 (Ajánlott) az alapértelmezett érték.
- **filters** (szűrők) – Nem kötelező, lehetővé teszi egy szűrő megadását a frissítésekbe való be- vagy kizáráshoz.
- **updateLimit** – Nem kötelező, azt határozza meg, hogy hány frissítés telepíthető, alapértelmezés szerint 1000.
 
> [!NOTE]
> A Windows Update a testreszabási eszköz meghiúsulhat, ha vannak függőben lévő Windows-újraindítások vagy még futó alkalmazástelepítések. Ezt a hibát általában a customization.log naplófájlban `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` láthatja. Határozottan javasoljuk, hogy fontolja meg a Windows újraindításának hozzáadását, és/vagy várjon elegendő időt az alkalmazások telepítésére alvó vagy várakozási parancsokkal a beágyazott parancsokban vagy szkriptekben a Windows Update. [](/powershell/module/microsoft.powershell.utility/start-sleep)

### <a name="generalize"></a>Általánossá tétel 
Alapértelmezés szerint az Azure Image Builder a rendszerkép testreszabási fázisának végén futtatja a "megszüntetés" kódot a rendszerkép "általánosításához". Az általánosító folyamat során a rendszerképet úgy kell beállítani, hogy újra felhasználható legyen több virtuális gép létrehozásához. Windows rendszerű virtuális gépek esetén az Azure Image Builder Sysprepet használja. Linux esetén az Azure Image Builder a "waagent -deprovision" rendszert futtatja. 

Előfordulhat, Image Builder felhasználók által általánosítandó parancsok nem minden helyzetben megfelelők, ezért az Azure Image Builder lehetővé teszi a parancs szükség esetén való testreszabását. 

Ha meglévő testreszabásokat minkál, és különböző Sysprep/waagent parancsokat használ, használhatja az Image Builder általános parancsait, és ha a virtuális gép létrehozása sikertelen, használjon saját Sysprep- vagy waagent-parancsokat.

Ha az Azure Image Builder sikeresen létrehoz egy egyéni Windows-rendszerképet, és létrehoz róla egy virtuális gépet, akkor a virtuális gép létrehozása meghiúsul vagy nem fejeződik be sikeresen, át kell vizsgálnia a Windows Server Sysprep dokumentációját, vagy támogatási kérést kell létrehoznia a Windows Server Sysprep ügyfélszolgálati csapatával, akik hibaelhárítást és tanácsadást tudnak adni a megfelelő Sysprep-használattal kapcsolatban.


#### <a name="default-sysprep-command"></a>Alapértelmezett Sysprep parancs
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
#### <a name="default-linux-deprovision-command"></a>Alapértelmezett Linux-megszüntetés parancs

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>A parancsok felülbírálása
A parancsok felülbírálása érdekében a PowerShell- vagy a héjparancsfájl-kiépítési segítségével hozza létre a parancsfájlokat pontosan a fájlnévvel, és helyezze őket a megfelelő könyvtárakba:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder beolvassa ezeket a parancsokat, ezek a "customization.log" AIB-naplókba lesznek kiírva. Tekintse [meg a naplók](image-builder-troubleshoot.md#customization-log) gyűjtésével kapcsolatos hibaelhárítási útmutatót.
 
## <a name="properties-distribute"></a>Tulajdonságok: distribute

Az Azure Image Builder három terjesztési célt támogat: 

- **managedImage** – felügyelt rendszerkép.
- **sharedImage** – Shared Image Gallery.
- **VHD** – VHD egy tárfiókban.

Egy rendszerképet ugyanabban a konfigurációban terjeszthet mindkét céltípushoz.

> [!NOTE]
> Az alapértelmezett AIB sysprep parancs nem tartalmazza a "/mode:vm" parancsot, de ez szükséges lehet olyan rendszerképek létrehozásakor, amelyeken telepítve lesz a HyperV szerepkör. Ha hozzá kell adni ezt a parancs argumentumot, felül kell bírálni a sysprep parancsot.

Mivel egynél több cél között terjeszthető, a Image Builder minden terjesztési célhoz fenntart egy állapotot, amely a lekérdezéssel érhető `runOutputName` el.  A egy olyan objektum, amely az `runOutputName` eloszlás után lekérdezhető az eloszlásról. Lekérdezheti például a VHD helyét, vagy a régiókat, amelyekbe a rendszerképverzió replikálva lett, vagy létrehozhatja a SIG-rendszerkép verzióját. Ez minden terjesztési cél tulajdonsága. A `runOutputName` egyedinek kell lennie minden terjesztési célon. Lássunk egy példát, amely egy Shared Image Gallery lekérdezése:

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

A rendszerkép kimenete egy felügyelt rendszerkép-erőforrás lesz.

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
 
Tulajdonságok elosztása:
- **type** – managedImage 
- **imageId** – A célként azonosított rendszerkép erőforrás-azonosítója, várt formátuma: /subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **location** – a felügyelt rendszerkép helye.  
- **runOutputName** – egyedi név az elosztás azonosításához.  
- **artifactTags** – Felhasználó által megadott opcionális kulcs-érték párcímkék.
 
 
> [!NOTE]
> A cél erőforráscsoportnak léteznie kell.
> Ha a rendszerképet egy másik régióba szeretné terjeszteni, az megnöveli az üzembe helyezési időt. 

### <a name="distribute-sharedimage"></a>Terjesztés: sharedImage 
Az Azure Shared Image Gallery egy új rendszerképkezelési szolgáltatás, amely lehetővé teszi a rendszerkép-régiók replikációjának, verziószámozásának és egyéni rendszerképek megosztásának kezelését. Az Azure Image Builder támogatja a terjesztést ezzel a szolgáltatással, így a rendszerképeket a megosztott rendszerkép-katalógusok által támogatott régiókba terjesztheti. 
 
A Shared Image Gallery a következőből áll: 
 
- Katalógus – Tároló több megosztott rendszerképhez. A katalógusok egy régióban üzembe helyezhetők.
- Képdefiníciók – képek fogalmi csoportosítása. 
- Rendszerképverziók – Ez egy virtuális gép vagy méretezési készlet üzembe helyezéséhez használt rendszerképtípus. A rendszerképverziók replikálhatók olyan régiókba, ahol virtuális gépeket kell üzembe helyezni.
 
Ahhoz, hogy terjeszthető legyen a rendszerkép-katalógusba, létre kell hoznia egy katalógust és egy képdefiníciót. Lásd: [Megosztott képek.](../shared-images-cli.md) 

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

Megosztott rendszerkép-katalógusok tulajdonságainak terjesztése:

- **type** – sharedImage  
- **galleryImageId** – A megosztott rendszerkép-katalógus azonosítója, amely két formátumban megadva lehet:
    * Automatikus verziószámozás – Image Builder létrehoz egy monoton verziószámot. Ez akkor hasznos, ha továbbra is ugyanazon sablonból szeretné újraépíteni a lemezképeket: A formátum a következő: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Explicit verziószámozás – Meg lehet adni a verziószámot, amit a képkészítővel használni szeretne. A formátum a következő: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** – egyedi név az elosztás azonosításához.  
- **artifactTags** – Felhasználó által megadott opcionális kulcs-érték párcímkék.
- **replicationRegions** – A replikációhoz szükséges régiók tömbje. Az egyik régiónak az a régiónak kell lennie, ahol a katalógus üzembe van stb. A régiók hozzáadása a buildidő növekedését jelenti, mivel a build nem fejeződik be a replikáció befejezéséig.
- **excludeFromLatest** (nem kötelező) Ezzel a beállítással megjelölhető, hogy a létrehozott rendszerképverzió nem a SIG-definíció legújabb verziójaként lesz használva, az alapértelmezett érték "false".
- **A storageAccountType** (nem kötelező) Az AIB támogatja az ilyen típusú tárolók megadását a létrehozni kívánt rendszerképverzióhoz:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Ha a hivatkozott és a rendszerképsablon nem ugyanazon a helyen található, a rendszerképek létrehozása további `image definition` időt fog látni. Image Builder jelenleg nem rendelkezik paraméterrel a rendszerkép `location` verzióerőforrása számára, a szülőtől vegyük `image definition` át. Ha például egy rendszerkép-definíció a westus nyugati régiója, és a rendszerképverziót az eastus régióba szeretné replikálni, a rendszer átmásol egy blobot a westus régióba. Ebből létrejön a westus rendszerképverzió-erőforrása, majd az eastusba replikálódik. A további replikációs idő elkerülése érdekében győződjön meg arról, hogy a és a rendszerképsablon `image definition` ugyanazon a helyen van.


### <a name="distribute-vhd"></a>Terjesztés: VHD  
A kimenetet vhD-fájlként is ki lehet kimenetként látni. Ezután átmásolhatja a VHD-t, és közzéteheti az Azure MarketPlace-en, vagy használhatja a következővel: Azure Stack.  

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

VHD-paraméterek elosztása:

- **type** – VHD.
- **runOutputName** – egyedi név az elosztás azonosításához.  
- **tags** – Felhasználó által megadott kulcs-érték párok opcionális címkéi.
 
Az Azure Image Builder nem engedélyezi a felhasználónak a tárfiók helyének megadását, de a hely lekérdezhető az `runOutputs` állapotából.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> A virtuális merevlemez létrehozása után a lehető leghamarabb másolja át egy másik helyre. A virtuális merevlemezt a rendszer egy tárfiókban tárolja, amely a rendszerképsablon azure-beli Image Builder létrehozásakor jött létre. Ha törli a rendszerképsablont, akkor elveszíti a virtuális merevlemezt. 

## <a name="image-template-operations"></a>Rendszerképsablon-műveletek

### <a name="starting-an-image-build"></a>Rendszerkép-összeállítás indítása
A buildek futtatásához a "Run" metódust kell meghívni a rendszerképsablon erőforrásán, példák `run` a parancsokra:

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

### <a name="cancelling-an-image-build"></a>Rendszerkép-összeállítás lemondása
Ha helytelennek vélt rendszerkép-összeállítást futtat, felhasználói adatbevitelre vár, vagy úgy érzi, hogy soha nem fog sikeresen befejeződni, akkor megszakíthatja a buildet.

A build bármikor megszakítható. Ha az elosztási fázis elindult, akkor is megszakíthatja a műveletet, de törölnie kell minden olyan lemezképet, amely esetleg nem fejeződött be. A megszakítási parancs nem várja meg, amíg a megszakítás befejeződik. Figyelje meg a folyamat megszakításának állapotát `lastrunstatus.runstate` a következő [állapotparancsokkal:](image-builder-troubleshoot.md#customization-log).


Példák `cancel` parancsokra:

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

.json-mintafájlok érhetők el különböző forgatókönyvekhez az [Azure Image Builder GitHubon.](https://github.com/azure/azvmimagebuilder)