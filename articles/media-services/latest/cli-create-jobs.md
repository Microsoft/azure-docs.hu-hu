---
title: Azure CLI parancsfájl – példa – feladatok létrehozása és elküldése
description: A jelen témakörben lévő Azure CLI-szkript bemutatja, hogyan tud feladatot küldeni egy egyszerű kódoló Átalakításnak HTTPS URL-cím használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9291d53822f0fbb80f759908933db58f2224c3d7
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091908"
---
# <a name="cli-example-create-and-submit-a-job"></a>CLI-példa: Feladat létrehozása és elküldése

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Services v3-ban a videófeldolgozási feladatok indításakor meg kell adnia a Media Services számára a bemeneti videó helyét. Az egyik lehetőség, ha egy HTTPS URL-címet ad meg a feladathoz tartozó bemenetként (a jelen cikkben látható módon). 

## <a name="prerequisites"></a>Előfeltételek 

[Hozzon létre egy Media Services fiókot](./create-account-howto.md).

## <a name="example-script"></a>Példaszkript

A futtatásakor beállíthat `az ams job start` egy címkét a feladatok kimenetén. A címke később is használható annak azonosítására, hogy mi ez a kimeneti eszköz. 

- Ha a címkéhez értéket rendel, állítsa a "--output-assets" kifejezést "assetname = label" értékre.
- Ha nem rendel értéket a címkéhez, állítsa a "--output-assets" értéket a "assetname =" értékre.
  Figyelje meg, hogy hozzáadja a "=" kifejezést a következőhöz: `output-assets` . 

```azurecli
az ams job start \
  --name testJob001 \
  --transform-name testEncodingTransform \
  --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' \
  --files 'Ignite-short.mp4' \
  --output-assets testOutputAssetName= \
  -a amsaccount \
  -g amsResourceGroup 
```

Ehhez hasonló választ kap:

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

## <a name="next-steps"></a>Következő lépések

[az AMS Job (CLI)](/cli/azure/ams/job?view=azure-cli-latest)
