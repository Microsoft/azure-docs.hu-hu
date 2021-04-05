---
title: Videofájlok továbbítása Azure Media Services CLI-vel
description: Kövesse az oktatóanyag lépéseit, hogy az Azure CLI-vel hozzon létre egy új Azure Media Services fiókot, kódoljon egy fájlt, és továbbítsa a Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c78205d7e2b41628de9e8b92c9fa5506e82158cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954496"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---azure-cli"></a>Oktatóanyag: távoli fájl kódolása URL-cím alapján és a videó továbbítása az Azure CLI-vel

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ebből az oktatóanyagból megtudhatja, hogyan kódolhat és továbbíthat videókat különböző böngészőkön és eszközökön Azure Media Services és az Azure CLI használatával. A bemeneti tartalmat HTTPS vagy SAS URL-címekkel, illetve az Azure Blob Storage-ban lévő fájlok elérési útjaival is megadhatja.

A cikkben szereplő példa egy HTTPS URL-címen keresztül elérhetővé teszi a tartalmat. Media Services v3 jelenleg nem támogatja a darabolásos átvitel kódolását HTTPS URL-címeken keresztül.

Az oktatóanyag végére egy videót is továbbíthat.  

![Videó lejátszása](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Az Azure-beli médiatartalmak titkosítása, kódolása, elemzése, kezelése és továbbítása előtt létre kell hoznia egy Media Services fiókot. Ezt a fiókot egy vagy több Storage-fiókhoz kell társítani.

A Media Services-fióknak és az összes társított Storage-fióknak ugyanabban az Azure-előfizetésben kell lennie. Javasoljuk, hogy a késés és az adatforgalom költségeinek korlátozásához olyan tárolási fiókokat használjon, amelyek a Media Services-fiókkal azonos helyen vannak.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

```azurecli-interactive
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

Ebben a példában egy General-Purpose v2 standard szintű LRS-fiókot hozunk létre.

Ha a Storage-fiókokkal szeretne kísérletezni, használja a következőt: `--sku Standard_LRS` . Ha éles üzemben lévő SKU-t vesz fel, érdemes lehet használni `--sku Standard_RAGRS` , amely földrajzi replikációt biztosít az üzletmenet folytonossága érdekében. További információ: Storage- [fiókok](/cli/azure/storage/account).

```azurecli-interactive
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Azure Media Services-fiók létrehozása

```azurecli-interactive
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

A következőhöz hasonló választ kaphat:

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-the-streaming-endpoint"></a>A streamvégpont elindítása

A következő Azure CLI-parancs elindítja az alapértelmezett **folyamatos átviteli végpontot**.

```azurecli-interactive
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

A következőhöz hasonló választ kaphat:

```
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

Ha az adatfolyam-végpont már fut, a következő üzenet jelenik meg:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Átalakítás létrehozása az adaptív sávszélességű kódoláshoz

Hozzon létre egy **átalakítót** a videók kódolásához vagy elemzéséhez használható gyakori feladatok konfigurálásához. Ebben a példában az adaptív sávszélességű kódolást használjuk. Ezután beküldünk egy feladatot a létrehozott átalakítás alá. A feladatnak az a kérése, hogy a rendszer átadja az átalakítást a megadott videó-vagy hangtartalom-bevitelre Media Services.

```azurecli-interactive
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

A következőhöz hasonló választ kaphat:

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása

Hozzon létre egy kimeneti **eszközt** a kódolási feladatok kimenetéhez.

```azurecli-interactive
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

A következőhöz hasonló választ kaphat:

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-a-job-by-using-https-input"></a>Feladatok indítása HTTPS-bemenet használatával

Amikor feladatokat küld a videók feldolgozásához, meg kell tudnia Media Services hol található a bemeneti videó. Az egyik lehetőség egy HTTPS URL-cím megadása a feladathoz tartozó bemenetként, ahogy az ebben a példában is látható.

A futtatásakor beállíthat `az ams job start` egy címkét a feladatok kimenetén. Ezután a címkével azonosíthatja, hogy a kimeneti objektum milyen.

- Ha a címkéhez értéket rendel, állítsa a "--output-assets" kifejezést "assetname = label" értékre.
- Ha nem rendel értéket a címkéhez, állítsa a "--output-assets" értéket a "assetname =" értékre.

  Figyelje meg, hogy hozzáadjuk a "=" kifejezést a következőhöz: `output-assets` .

```azurecli-interactive
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup
```

A következőhöz hasonló választ kaphat:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>Állapot ellenőrzése

Öt perc alatt ellenőriznie kell a feladatok állapotát. Ennek a következőnek kell lennie: "finished". Nem fejeződött be, néhány perc múlva próbálkozzon újra. Ha elkészült, ugorjon a következő lépésre, és hozzon létre egy **folyamatos átviteli lokátort**.

```azurecli-interactive
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Adatfolyam-kereső létrehozása és elérési út beolvasása

A kódolás befejezése után a következő lépés az, hogy a kimeneti eszközön elérhetővé tegye a videót az ügyfelek számára a lejátszáshoz. Ehhez először hozzon létre egy streaming-lokátort. Ezután hozza létre az ügyfelek által használható Streaming URL-címeket.

### <a name="create-a-streaming-locator"></a>Streamelési lokátor létrehozása

```azurecli-interactive
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

A következőhöz hasonló választ kaphat:

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>Adatfolyam-kereső elérési útjainak beolvasása

```azurecli-interactive
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

A következőhöz hasonló választ kaphat:

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

Másolja a HTTP Live Streaming (HLS) elérési útját. Ebben az esetben ez a következő: `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)` .

## <a name="build-the-url"></a>Az URL-cím összeállítása

### <a name="get-the-streaming-endpoint-host-name"></a>A streaming Endpoint Host nevének beolvasása

```azurecli-interactive
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Másolja az `hostName` értéket. Ebben az esetben ez a következő: `amsaccount-usw22.streaming.media.azure.net` .

### <a name="assemble-the-url"></a>Az URL-cím összeállítása

"https://" + &lt; állomásnév értéke &gt;  +  &lt; HLS útvonal értéke&gt;

Bemutatunk egy példát:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Lejátszás tesztelése Azure Media Player használatával

> [!NOTE]
> Ha egy kiszolgáló HTTPS-helyen található, mindenképpen indítsa el az URL-címet a "https" előtaggal.

1. Nyisson meg egy webböngészőt, és lépjen a következőre: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Az **URL-cím** mezőbe illessze be az előző szakaszban létrehozott URL-címet. Az URL-címet HLS, Dash vagy Smooth formátumban is beillesztheti. A Azure Media Player automatikusan egy megfelelő adatfolyam-protokollt használ a lejátszáshoz az eszközön.
3. Válassza a **lejátszó frissítése** lehetőséget.

>[!NOTE]
>Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoport egyik erőforrására sem, beleértve az oktatóanyaghoz létrehozott Media Services-és Storage-fiókokat, törölje az erőforráscsoportot.

Futtassa ezt az Azure CLI-parancsot:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Következő lépések

[Media Services áttekintése](media-services-overview.md)
