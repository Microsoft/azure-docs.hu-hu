---
title: Azure CLI-példaszkript – Fájl feltöltése egy tárolóba | Microsoft Docs
description: Ez a cikk bemutatja, hogyan használhatja az Azure CLI-parancsfájlt egy helyi fájl feltöltéséhez egy tárolóba.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: d0a28bafef887bf0c40aa88eba1873f82cf36fd7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346085"
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>CLI-példa: Helyi fájl feltöltése egy tárolóba 

A cikkben bemutatott Azure CLI-szkripttel feltölthet egy helyi fájlt egy tárfiókbeli tárolóba.

## <a name="prerequisites"></a>Előfeltételek 

* [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md).
* Tekintse át az eszközök kezelése című [véleményt.](manage-asset-concept.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Példaszkript

```Azure CLI 
#!/bin/bash
# Update the following variables for your own settings:
storageAccountName=build2018storage
assetContainer="asset-4c834446-7e55-4760-9a25-f2d4fb1f4657"
localFile="..\Media\ignite-short.mp4"
blobName="ignite-short.mp4"
sasToken="?sv=2015-07-08&sr=c&sig=u1uy9OIeXnZUEN62hE0bDgg%2FPXYgRDNGnQxE%2BSi51dM%3D&se=2018-04-29T18:42:02Z&sp=rwl"
# Use the az storage modules to upload a local file to the container using the SAS URL from previous step.
# If you are logged in already to the subscription with access to the storage account, you do not need to use the --sas-token at all. Just eliminate it below.
# The container name is in the SAS URL path, and should be set with the -c option.
# Use the -f option to point to a local file on your machine.
# Use the -n option to name the blob in storage.
# Use the --account-name option to point to the storage account name to use 
# Use the --sas-token option to place the SAS token after the query string from previous step. 
# NOTE that the SAS Token is only good for up to 24 hours max. 
#
az storage blob upload \
    -c $assetContainer \
    -f $localFile \
    -n $blobName \
    --account-name $storageAccountName \
    --sas-token $sasToken \
echo "press  [ENTER]  to continue."
read continue
```

## <a name="next-steps"></a>További lépések

[A Media Services áttekintése](media-services-overview.md)
