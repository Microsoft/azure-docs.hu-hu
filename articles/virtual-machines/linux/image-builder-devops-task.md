---
title: Az Azure rendszerkép-készítő szolgáltatás DevOps feladata
description: Az Azure DevOps feladata a Build-összetevők behelyezése egy virtuálisgép-rendszerképbe az alkalmazás és az operációs rendszer telepítéséhez és konfigurálásához.
author: danielsollondon
ms.author: danis
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: d02a5c6bc194009d459647721dab16be0dcade84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670472"
---
# <a name="azure-image-builder-service-devops-task"></a>Az Azure rendszerkép-készítő szolgáltatás DevOps feladata

Ebből a cikkből megtudhatja, hogyan szúrhat be összetevőket egy virtuálisgép-rendszerképbe az Azure DevOps-feladatokkal, így telepítheti és konfigurálhatja az alkalmazást és az operációs rendszert.

## <a name="devops-task-versions"></a>DevOps-feladatok verziói
Két Azure VM rendszerkép-készítő (AIB) DevOps-feladat létezik:

* ["STABLE" AIB feladat](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder), ez a legújabb stabil Build, amelyet teszteltek, és a telemetria nem jelenít meg problémákat. 


* ["Instabil" AIB feladat](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary), amely lehetővé teszi a legújabb frissítések és funkciók bevonását, lehetővé téve az ügyfeleknek a tesztelését, mielőtt előléptetik a "STABLE" feladatba. Ha nincsenek jelentett problémák, és a telemetria nem jelenít meg problémát, körülbelül 1 héttel később, a feladatsort "STABLE"-re fogjuk előléptetni. 

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [stabil DevOps feladatot a Visual Studio piactérről](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder).
* Rendelkeznie kell egy VSTS DevOps-fiókkal és egy létrehozott létrehozási folyamattal.
* Regisztrálja és engedélyezze a rendszerkép-készítő szolgáltatás követelményeit a folyamatok által használt előfizetésben:
    * [Az PowerShell](../windows/image-builder-powershell.md#register-features)
    * [Az parancssori felület](../windows/image-builder.md#register-the-features)
    
* Hozzon létre egy szabványos Azure Storage-fiókot a forrás rendszerkép-erőforráscsoport területén, más erőforráscsoport-vagy Storage-fiókokat is használhat. A Storage-fiók a DevOps feladatból a rendszerképbe helyezi át a Build-összetevőket.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>Feladat hozzáadása a kiadási folyamathoz

**Kiadási folyamat**  >  **szerkesztésének** kiválasztása

A felhasználói ügynöknél válassza a *+* Hozzáadás lehetőséget, majd keressen rá a **rendszerkép-szerkesztőre**. Válassza a **Hozzáadás** lehetőséget.

Adja meg a következő feladat tulajdonságait:

### <a name="azure-subscription"></a>Azure-előfizetés

Válassza ki a legördülő menüből azt az előfizetést, amelyen futtatni szeretné a rendszerkép-szerkesztőt. Használja ugyanazt az előfizetést, ahol a forrás-lemezképek találhatók, valamint a lemezképek terjesztésének helyét. Engedélyeznie kell a rendszerkép-szerkesztő közreműködői hozzáférését az előfizetéshez vagy az erőforráscsoporthoz.

### <a name="resource-group"></a>Erőforráscsoport

Használja azt az erőforráscsoportot, amelyben az ideiglenes Képsablon-összetevőt tárolni fogja. A sablon-összetevők létrehozásakor létrejön egy további ideiglenes rendszerkép-készítő erőforráscsoport `IT_<DestinationResourceGroup>_<TemplateName>_guid` . Az ideiglenes erőforráscsoport tárolja a rendszerkép metaadatait, például a parancsfájlokat. A feladat végén a rendszer törli a Képsablon összetevőjét és az ideiglenes rendszerkép-készítő erőforráscsoportot.
 
### <a name="location"></a>Hely

A hely az a régió, ahol a rendszerkép-szerkesztő futni fog. Csak egy meghatározott számú [régió](../image-builder-overview.md#regions) támogatott. Ezen a helyen a forrás lemezképeknek jelen kell lenniük. Ha például megosztott képtárat használ, az adott régióban léteznie kell egy replikának.

### <a name="managed-identity-required"></a>Felügyelt identitás (kötelező)
A rendszerkép-készítőnek felügyelt identitásra van szüksége, amelyet a forrás egyéni lemezképek olvasásához, az Azure Storage-hoz való kapcsolódáshoz és Egyéni rendszerképek létrehozásához használ. További információt [itt](../image-builder-overview.md#permissions) talál.

### <a name="vnet-support"></a>VNET-támogatás

Jelenleg a DevOps feladat nem támogatja egy meglévő alhálózat megadását, ez az ütemterv, de ha egy meglévő VNET kíván használni, használhat egy ARM-sablont, amely egy, a-ben beágyazott rendszerkép-készítő sablonnal rendelkezik, tekintse meg a Windows rendszerkép-készítő sablon példáit, vagy használja az [az AIB PowerShellt](../windows/image-builder-powershell.md).

### <a name="source"></a>Forrás

A forrás rendszerképnek a támogatott rendszerkép-készítő OSs-nek kell lennie. Kiválaszthatja a meglévő egyéni lemezképeket ugyanabban a régióban, mint az Image Builder:
* Felügyelt rendszerkép – a resourceId kell átadnia, például:
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Azure megosztott rendszerkép-katalógus – a rendszerkép resourceId kell átadnia, például:
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    Ha be kell szereznie a megosztott képkatalógus legújabb verzióját, akkor az az PowerShell vagy AZ AZ CLI Task előtt, amely a legújabb verziót fogja beszerezni, és beállítja a DevOps változót. Használja a változót az az VM rendszerkép-készítő DevOps feladatban. További információ: [példák](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery).

* Piacon Alaprendszerkép: a népszerű rendszerképek legördülő lista, amely mindig a támogatott operációs rendszer legújabb verzióját fogja használni. 

    Ha az alaprendszerkép nem szerepel a listán, megadhatja a pontos képet a használatával `Publisher:Offer:Sku` .

    Alaprendszerkép verziója (nem kötelező) – megadhatja a használni kívánt rendszerkép verzióját, alapértelmezett érték: `latest` .

### <a name="customize"></a>Testreszabás

#### <a name="provisioner"></a>Kiépítés

Kezdetben két testreszabó támogatott – **rendszerhéj** és **PowerShell**. Csak a inline használata támogatott. Ha parancsfájlokat szeretne letölteni, a beágyazott parancsokat is átadhatja.

Az operációs rendszer esetében válassza a PowerShell vagy a rendszerhéj lehetőséget.

#### <a name="windows-update-task"></a>Windows Update feladat

Csak Windows esetén a feladat a testreszabások végén Windows Update fut. Kezeli a szükséges újraindításokat.

A rendszer a következő Windows Update konfigurációt hajtja végre:
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
Telepíti azokat a fontos és ajánlott Windows-frissítéseket, amelyek nem előzetes verziójúak.

#### <a name="handling-reboots"></a>Újraindítások kezelője
A DevOps feladat jelenleg nem támogatja a Windows-buildek újraindítását, ha a PowerShell-kóddal próbálkozik, a Build sikertelen lesz. A Linux-buildek újraindításához azonban programkódot is használhat.

#### <a name="build-path"></a>Létrehozási útvonal

A feladat úgy van kialakítva, hogy képes legyen DevOps-Build kiadási összetevőket beszúrni a képbe. A munka elvégzéséhez létre kell hoznia egy Build folyamatot. A kiadási folyamat telepítésekor fel kell vennie a Build-összetevők tárházát.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="Válassza az összetevő hozzáadása lehetőséget a kiadási folyamatban.":::

A **létrehozási útvonal** gombra kattintva válassza ki a képre helyezni kívánt Build mappát. A rendszerkép-szerkesztő feladat az összes fájlt és könyvtárat átmásolja benne. A rendszerkép létrehozásakor a képszerkesztő különböző elérési utakon helyezi üzembe a fájlokat és könyvtárakat az operációs rendszertől függően.

> [!IMPORTANT]
> Tárház-összetevő hozzáadásakor előfordulhat, hogy a könyvtárat aláhúzás *_* előtaggal látja el. Az aláhúzás a beágyazott parancsokkal kapcsolatos problémákat okozhat. Használja a parancsok megfelelő idézőjeleit.
> 

A következő példa a működésének módját mutatja be:

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="Hierarchia megjelenítésére szolgáló címtár-struktúra.":::


* Windows-fájlok találhatók a-ben `C:\` . A rendszer létrehoz egy nevű könyvtárat `buildArtifacts` , amely tartalmazza a `webapp` könyvtárat.

* Linux-fájlok léteznek a alkalmazásban  `/tmp` . A `webapp` rendszer létrehozza a könyvtárat, amely tartalmazza az összes fájlt és könyvtárat. A fájlokat a címtárból kell áthelyeznie. Ellenkező esetben a rendszer törli, mert az ideiglenes könyvtárban van.

#### <a name="inline-customization-script"></a>Beágyazott testreszabási parancsfájl

* Windows – megadhatja a PowerShell beágyazott parancsait vesszővel elválasztva. Ha parancsfájlt szeretne futtatni a Build könyvtárában, a következőt használhatja:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

   Hivatkozhat több parancsfájlra, vagy további parancsokat is hozzáadhat, például:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    & 'c:\buildArtifacts\webapp\installAgent.ps1'
    ```
* Linux – Linux rendszereken a Build összetevők a `/tmp` könyvtárba kerülnek. Sok Linux OSs esetében azonban a rendszer újraindításkor törli a/tmp könyvtár tartalmát. Ha azt szeretné, hogy az összetevők a képen is elérhetők legyenek, létre kell hoznia egy másik könyvtárat, és át kell másolnia őket.  Például:

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    Ha a "/tmp" könyvtárat használja, az alábbi kód használatával hajthatja végre a parancsfájlt.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>Mi történik a Build-összetevőkkel a rendszerkép létrehozása után?

> [!NOTE]
> A rendszerkép-szerkesztő nem távolítja el automatikusan a Build-összetevőket, erősen azt javasoljuk, hogy mindig legyen kód a Build-összetevők eltávolításához.
> 

* A Windows-rendszerkép-szerkesztő telepíti a fájlokat a `c:\buildArtifacts` könyvtárba. A könyvtár megmarad, el kell távolítania a könyvtárat. Eltávolíthatja azt a futtatott szkriptből. Például:

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux – a Build összetevők bekerülnek a `/tmp` könyvtárba. Számos Linux OSs esetében azonban az újraindításkor `/tmp` törlődik a könyvtár tartalma. Azt javasoljuk, hogy a tartalom eltávolításához legyen kód, és ne használja az operációs rendszert a tartalom eltávolításához. Például:

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>A képépítés teljes hossza

A teljes hossz még nem módosítható a DevOps-folyamat feladatban. Az alapértelmezett 240 percet használja. Ha szeretné bővíteni a [buildTimeoutInMinutes](./image-builder-json.md#properties-buildtimeoutinminutes), akkor a kiadási folyamat az az CLI feladat használatával végezhető el. Konfigurálja a feladatot egy sablon másolásához és elküldéséhez. Példaként tekintse meg ezt a [megoldást](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder), vagy használja az az PowerShellt.


#### <a name="storage-account"></a>Tárfiók

Válassza ki az előfeltételekben létrehozott Storage-fiókot. Ha nem jelenik meg a listában, a rendszerkép-szerkesztő nem rendelkezik engedéllyel.

A Build indításakor a rendszerkép-szerkesztő létrehoz egy nevű tárolót `imagebuilder-vststask` . A tároló tárolja a tárházból származó Build-összetevőket.

> [!NOTE]
> Az egyes buildek után manuálisan kell törölnie a Storage-fiókot vagy-tárolót.
>

### <a name="distribute"></a>Elosztás

3 terjesztési típus támogatott.

#### <a name="managed-image"></a>Felügyelt rendszerkép

* ResourceId
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* Helyek

#### <a name="azure-shared-image-gallery"></a>Az Azure megosztott képtára

A megosztott rendszerkép-gyűjteménynek már léteznie **kell** .

* ResourceId 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* Régiók: régiók listája, vesszővel elválasztva. Például: westus, eastus, CentralUS

#### <a name="vhd"></a>VHD

Ehhez a rendszerkép-szerkesztőhöz nem adhat át semmilyen értéket, ha a virtuális merevlemezt az ideiglenes rendszerkép-készítő erőforráscsoporthoz, `IT_<DestinationResourceGroup>_<TemplateName>` a  VHD-tárolóban fogja kibocsátani. A kiadási Build indításakor a rendszerkép-szerkesztő naplófájlokat bocsát ki. Ha elkészült, a rendszer kibocsátja a VHD URL-címét.

### <a name="optional-settings"></a>Választható beállítások

* Virtuálisgép- [méret](image-builder-json.md#vmprofile) – a virtuális gép méretét felülbírálhatja az alapértelmezett *Standard_D1_v2*. Előfordulhat, hogy felülbírálja a teljes testreszabási idő csökkentését, vagy azért, mert olyan rendszerképeket szeretne létrehozni, amelyek bizonyos virtuálisgép-mérettől függenek, például GPU/HPC stb.

## <a name="how-it-works"></a>Működés

A kiadás létrehozásakor a feladat létrehoz egy tárolót a *imagebuilder-vststask* nevű Storage-fiókban. A zip-fájl feltöltésével feltölti a Build-összetevőket, és létrehoz egy SAS-jogkivonatot.

A feladat a rendszerkép-készítő sablon összetevő létrehozásához a feladat által átadott tulajdonságokat használja. A feladat a következő műveleteket végzi el:
* Letölti a Build-összetevő zip-fájlját és az egyéb társított parancsfájlokat. A fájlok mentése egy Storage-fiókba történik az ideiglenes rendszerkép-készítő erőforráscsoporthoz `IT_<DestinationResourceGroup>_<TemplateName>` .
* Létrehoz egy előrögzített *t_* és egy 10 számjegyű monoton egész számot. A sablon a kiválasztott erőforráscsoporthoz lett mentve. A sablon létezik az erőforráscsoport buildének időtartamára. 

Példa a kimenetre:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

A rendszerkép létrehozásakor a futtatási állapotot a kiadási naplók jelentik:

```text
starting run template...
```

A rendszerkép létrehozása után a következő szöveghez hasonló kimenet jelenik meg:

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

A rendszerkép sablonja `IT_<DestinationResourceGroup>_<TemplateName>` törölve lett.

Megteheti a "$ (imageUri)" VSTS változót, és használhatja azt a következő feladatban, vagy egyszerűen használhatja az értéket, és felépítheti a virtuális gépet.

## <a name="output-devops-variables"></a>Kimeneti DevOps változók

A forrás piactér rendszerképének pub/ajánlat/SKU/verziója:
* $ (pirPublisher)
* $ (pirOffer)
* $ (pirSku)
* $ (pirVersion)

Rendszerkép URI-ja – az elosztott rendszerkép ResourceID:
* $ (imageUri)

## <a name="faq"></a>GYIK

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>Használhatok már létrehozott meglévő képsablont a DevOps-en kívül?

Jelenleg nem.

### <a name="can-i-specify-the-image-template-name"></a>Megadhatom a rendszerkép sablonjának nevét?

Nem. A rendszer a sablon egyedi nevét használja, majd törli.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>A rendszerkép-szerkesztő nem sikerült. Hogyan lehet elhárítani a hibát?

Ha felépítési hiba történik, a DevOps feladat nem törli az előkészítési erőforráscsoportot. Elérheti a Build testreszabási naplóját tartalmazó átmeneti erőforrás-csoportot.

Hibaüzenet jelenik meg a virtuálisgép-rendszerkép-szerkesztő feladathoz tartozó DevOps-naplóban, és megtekintheti a testreszabási napló helyét. Például:

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="Példa DevOps-feladatra, amely hibát jelez.":::

A hibaelhárítással kapcsolatos további információkért lásd: az [Azure rendszerkép-készítő szolgáltatás hibaelhárítása](image-builder-troubleshoot.md). 

A hiba kivizsgálása után törölheti az előkészítési erőforráscsoportot. Először törölje a rendszerkép-sablon erőforrás-összetevőjét. Az összetevő *t_tel* előtaggal van ellátva, és a DevOps feladat-létrehozási naplójában található:

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

A rendszerkép sablonjának erőforrás-összetevője a tevékenységben kezdetben megadott erőforráscsoport. Ha elkészült a hibaelhárítással, törölje az összetevőt. Ha a Azure Portal használatával törli az erőforráscsoportot, válassza a **rejtett típusok megjelenítése** lehetőséget az összetevő megtekintéséhez.


## <a name="next-steps"></a>Következő lépések

További információ: az [Azure rendszerkép-készítő áttekintése](../image-builder-overview.md).
