---
title: Azure Media Services-fiók létrehozása
description: Ez az oktatóanyag végigvezeti egy Azure Media Services fiók létrehozásának lépésein.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/4/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9234b27e2f08e65f569393bde342cba3f37adee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95971348"
---
# <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Az Azure-ban tárolt médiatartalmak titkosításának, kódolásának, elemzésének, kezelésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot. A Media Services fiókot egy vagy több Storage-fiókhoz kell társítani. Ez a cikk új Azure Media Services-fiók létrehozásának lépéseit ismerteti.

[!INCLUDE [note 2020-05-01 API](./includes/note-2020-05-01-account-creation.md)]

 Media Services-fiók létrehozásához használhatja a Azure Portal vagy a CLI-t. Válassza ki a használni kívánt metódus lapját.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

<!-- NOTE: The following are in the includes folder and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

## <a name="portal"></a>[Portál](#tab/portal/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

## <a name="cli"></a>[Parancssori felület](#tab/cli/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

## <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

---

## <a name="next-steps"></a>Következő lépések

[Fájl streamelése](stream-files-dotnet-quickstart.md)