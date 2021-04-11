---
title: Tartalom feltöltése egy eszköz CLI-be
description: A jelen témakörben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre Media Services-objektumot, melybe tartalmat tud feltölteni.
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
ms.date: 02/16/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0b722b302434a547e56c12e92eaa134fa76ae07e
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105964144"
---
# <a name="create-an-asset"></a>Adategység létrehozása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk bemutatja, hogyan hozhat létre Media Services adategységet.  Egy adategységet fog használni a médiatartalom kódolásához és adatfolyamként való tárolásához.  Ha többet szeretne megtudni Media Services eszközökről, olvassa el az adategységeket [Azure Media Services v3](assets-concept.md)

## <a name="prerequisites"></a>Előfeltételek

Egy eszköz létrehozásához kövesse az [Media Services fiók létrehozása](./account-create-how-to.md) a szükséges Media Services fiók és erőforráscsoport létrehozásához című témakör lépéseit.

## <a name="methods"></a>Metódusok

## <a name="cli"></a>[Parancssori felület](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>A REST használata

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>A cURL használata

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Következő lépések

[Media Services áttekintése](media-services-overview.md)
