---
title: PowerShell-példaszkriptek
description: Példák, amelyek bemutatják, hogyan használható az előtér a PowerShell-parancsfájlok segítségével
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 53f09dd042543ebc433e9d9cbbd65db332e8fb6d
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518211"
---
# <a name="example-powershell-scripts"></a>PowerShell-példaszkriptek

Az Azure távoli renderelés a következő két REST API-t biztosítja:

- [Átalakítási REST API](../how-tos/conversion/conversion-rest-api.md)
- [Munkamenet-REST API](../how-tos/session-rest-api.md)

Az [ARR Samples adattár](https://github.com/Azure/azure-remote-rendering) a *parancsfájlokat* tartalmazó mappában található minta parancsfájlokat tartalmaz a szolgáltatás REST API-kkal való interakcióhoz. Ez a cikk a használatuk leírását ismerteti.

> [!TIP]
> Létezik egy [ARRT nevű UI-alapú eszköz](azure-remote-rendering-asset-tool.md) is a szolgáltatással való kommunikációhoz, amely kényelmes alternatíva a parancsfájlok használatával. ![ARRT](./media/azure-remote-rendering-asset-tool.png "ARRT képernyőkép")

> [!CAUTION]
> Ha a REST API függvények hívása túl gyakran fordul elő, a kiszolgáló leszabályozását és a hibák visszaadását eredményezi. Ebben az esetben a HTTP-hibakód azonosítója 429 ("túl sok kérés"). Szabályként a **következő hívások között 5-10 másodperces** késleltetésnek kell lennie.

## <a name="prerequisites"></a>Előfeltételek

A minta szkriptek végrehajtásához a [Azure PowerShell](/powershell/azure/)funkcionális beállítása szükséges.

1. Az Azure PowerShell telepítése:
    1. Nyisson meg egy PowerShell-ablakot rendszergazdai jogosultságokkal.
    1. Futtassa `Install-Module -Name Az -AllowClobber`

1. Ha hibát tapasztal a parancsfájlok futtatásával kapcsolatban, győződjön meg arról, hogy a [végrehajtási házirend](/powershell/module/microsoft.powershell.core/about/about_execution_policies) megfelelően van beállítva:
    1. Nyisson meg egy PowerShell-ablakot rendszergazdai jogosultságokkal.
    1. Futtassa `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Azure Storage-fiók előkészítése](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Jelentkezzen be az Azure Remote rendering-fiókját tartalmazó előfizetésbe:
    1. Indítson el egy PowerShell-ablakot.
    1. Futtatás: `Connect-AzAccount` , és kövesse a képernyőn megjelenő utasításokat.

    > [!NOTE]
    > Ha a szervezet egynél több előfizetéssel rendelkezik, lehet, hogy meg kell adnia a SubscriptionId és a bérlői argumentumokat. A részletek a [AzAccount dokumentációjában](/powershell/module/az.accounts/connect-azaccount)találhatók.

1. Töltse le a *Scripts* mappát az [Azure távoli renderelés GitHub-adattárból](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>Konfigurációs fájl

A fájlok mellett ki `.ps1` `arrconfig.json` kell töltenie a következőt:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Ügyeljen arra, hogy a LocalAssetDirectoryPath útvonalon a két fordított perjelet \\ \\ ("") használja, és minden más elérési úton, például a inputFolderPath és a inputAssetPath használjon perjelet.

> [!CAUTION]
> A választható értékeket ki kell tölteni, vagy el kell távolítani a kulcsot és az értéket. Ha például nem használja a  `"outputAssetFileName"` paramétert, törölnie kell a teljes sort a következőn belül: `arrconfig.json` .

### <a name="accountsettings"></a>accountSettings

`arrAccountId`És esetében `arrAccountKey` lásd: [Azure távoli renderelési fiók létrehozása](../how-tos/create-an-account.md).
Az `region` [elérhető régiók listájának](../reference/regions.md)megtekintéséhez.

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Ha **RenderingSession.ps1** szeretne futtatni, ezt a struktúrát ki kell tölteni:

- **vmSize:** Kiválasztja a virtuális gép méretét. Válassza a [*standard*](../reference/vm-sizes.md) vagy a [*prémium*](../reference/vm-sizes.md)lehetőséget. Ha már nincs szüksége rájuk, állítsa le a renderelési munkameneteket.
- **maxLeaseTime:** Az az időtartam, ameddig a virtuális gépet bérbe szeretné venni. Ha a bérlet lejár, a rendszer leállítja. A bérleti idő később bővíthető (lásd alább).

### <a name="assetconversionsettings"></a>assetConversionSettings

Ha **Conversion.ps1** szeretne futtatni, ezt a struktúrát ki kell tölteni.

Részletekért lásd: [Azure Storage-fiók előkészítése](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Parancsfájl: RenderingSession.ps1

Ez a szkript a renderelési munkamenetek létrehozására, lekérdezésére és leállítására szolgál.

> [!IMPORTANT]
> Győződjön meg róla, hogy kitöltötte a *accountSettings* és a *renderingSessionSettings* szakaszt a arrconfig.json.

### <a name="create-a-rendering-session"></a>Renderelési munkamenet létrehozása

Normál használat teljes kitöltött arrconfig.jsa következőn:

```PowerShell
.\RenderingSession.ps1
```

A parancsfájl meghívja a [munkamenet-felügyeleti REST API](../how-tos/session-rest-api.md) , hogy a megadott beállításokkal indítson el egy RENDERELÉSI virtuális gépet. A művelet során a rendszer lekéri a *munkamenet*-azonosítókat. Ezután lekérdezi a munkamenet tulajdonságait, amíg a munkamenet nem áll készen, vagy hiba történt.

**Alternatív konfigurációs** fájl használata:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Az **egyes beállításokat felülbírálhatja** a konfigurációs fájlból:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Ha csak **lekérdezés nélkül szeretné elindítani a munkamenetet**, használhatja a következőt:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

A parancsfájl által lekért *munkamenet* -azonosítónak át kell adni a legtöbb más munkamenet-parancsnak.

### <a name="retrieve-session-properties"></a>Munkamenet-tulajdonságok beolvasása

A munkamenet tulajdonságainak beszerzéséhez futtassa a következő parancsot:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

`-Poll`A használatával megvárhatja, amíg a munkamenet *kész* vagy hiba történt.

### <a name="list-active-sessions"></a>Aktív munkamenetek listázása

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Munkamenet leállítása

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Munkamenet tulajdonságainak módosítása

Jelenleg csak a munkamenet maxLeaseTime módosítását támogatjuk.

> [!NOTE]
> A címbérleti idő mindig a munkamenet virtuális gépe első létrehozásakor számít. Így a munkamenet bérletének meghosszabbítása egy órával a *maxLeaseTime* növelésével.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Parancsfájl: Conversion.ps1

Ez a szkript a bemeneti modellek az Azure Remote rendering-specifikus futtatókörnyezet-formátumba való átalakítására szolgál.

> [!IMPORTANT]
> Győződjön meg róla, hogy kitöltötte a *accountSettings* és a *assetConversionSettings* szakaszt a arrconfig.json.

A parancsfájl a következő két lehetőséget mutatja be a Storage-fiókok használatára a szolgáltatásban:

- Azure távoli renderelési fiókkal összekapcsolt Storage-fiók
- Hozzáférés biztosítása a tárterülethez közös hozzáférésű aláírások (SAS) használatával

### <a name="linked-storage-account"></a>Társított Storage-fiók

Miután teljes mértékben kitöltötte arrconfig.jsés csatolt egy Storage-fiókot, a következő parancsot használhatja. A Storage-fiók összekapcsolása a [fiók létrehozása](../how-tos/create-an-account.md#link-storage-accounts)című témakörben található.

A társított Storage-fiók használata az előnyben részesített módszer a konverziós szolgáltatás használatára, mivel nem kell közös hozzáférési aláírásokat létrehoznia.

```PowerShell
.\Conversion.ps1
```

1. Töltse fel a fájlban található összes fájlt a `assetConversionSettings.modelLocation` bemeneti blob-tárolóba a megadott érték alatt `inputFolderPath` .
1. A modell átalakításának meghívása [REST API](../how-tos/conversion/conversion-rest-api.md) a [modell átalakításának](../how-tos/conversion/model-conversion.md) elindításához
1. A konverzió állapotának lekérdezése, amíg az átalakítás sikeres vagy sikertelen volt.
1. A konvertált fájl helyének (Storage-fiók, kimeneti tároló, fájl elérési útja a tárolóban) kimeneti adatai.

### <a name="access-to-storage-via-shared-access-signatures"></a>Hozzáférés a tárolóhoz megosztott hozzáférési aláírások használatával

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Ez a következő lesz:

1. Töltse fel a helyi fájlt a fájlból a `assetConversionSettings.localAssetDirectoryPath` bemeneti blob-tárolóba.
1. SAS URI létrehozása a bemeneti tárolóhoz.
1. SAS URI létrehozása a kimeneti tárolóhoz.
1. Hívja meg a [modell átalakítását REST API](../how-tos/conversion/conversion-rest-api.md) a [modell átalakításának](../how-tos/conversion/model-conversion.md)elindításához.
1. A konverzió állapotának lekérdezése, amíg az átalakítás sikeres vagy sikertelen volt.
1. A konvertált fájl helyének (Storage-fiók, kimeneti tároló, fájl elérési útja a tárolóban) kimeneti adatai.
1. A kimeneti blob-tárolóban egy SAS URI-t ad a konvertált modellnek.

### <a name="additional-command-line-options"></a>További parancssori kapcsolók

**Alternatív konfigurációs** fájl használata:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Ha csak **lekérdezés nélkül szeretné elindítani a modell-konverziót**, használhatja a következőt:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Az **egyes beállításokat felülbírálhatja** a konfigurációs fájlból az alábbi parancssori kapcsolók használatával:

* **Azonosító:** A GetConversionStatus használt ConversionId
* **ArrAccountId:** ArrAccountId accountSettings
* **ArrAccountKey:** felülbírálás a accountSettings ArrAccountKey
* **Régió:** felülbírálás a accountSettings régiójában
* **ResourceGroup:** felülbírálás a assetConversionSettings ResourceGroup
* **StorageAccountName:** felülbírálás a assetConversionSettings StorageAccountName
* **BlobInputContainerName:** felülbírálás a assetConversionSettings blobInputContainer
* **LocalAssetDirectoryPath:** felülbírálás a assetConversionSettings LocalAssetDirectoryPath
* **InputAssetPath:** felülbírálás a assetConversionSettings InputAssetPath
* **BlobOutputContainerName:** felülbírálás a assetConversionSettings BlobOutputContainerName
* **OutputFolderPath:** felülbírálás a assetConversionSettings OutputFolderPath
* **OutputAssetFileName:** felülbírálás a assetConversionSettings OutputAssetFileName

Például a következőhöz hasonló lehetőségek közül választhat:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Az egyes átalakítási szakaszok futtatása

Ha a folyamat egyes lépéseit szeretné futtatni, a következőt használhatja:

Csak az adott LocalAssetDirectoryPath származó adatok feltöltése.

```PowerShell
.\Conversion.ps1 -Upload
```

Csak a blob Storage-ba feltöltött modell átalakítási folyamatának elindítása (ne futtassa a feltöltést, ne kérdezze le a konverziós állapotot) a szkript egy *conversionId* ad vissza.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

A konverzió állapotát a következő paranccsal kérheti le:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

`-Poll`A paranccsal megvárhatja, amíg a konverzió megtörténik, vagy hiba történt.

## <a name="next-steps"></a>Következő lépések

- [Gyors útmutató: modell megjelenítése egységgel](../quickstarts/render-model.md)
- [Gyorsútmutató: Modell konvertálása a renderelés előtt](../quickstarts/convert-model.md)
- [Modell átalakítása](../how-tos/conversion/model-conversion.md)