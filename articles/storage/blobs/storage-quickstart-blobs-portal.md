---
title: Rövid útmutató – blob létrehozása a Azure Portal
titleSuffix: Azure Storage
description: Ebben a rövid útmutatóban az Azure Portal használatával kezelheti az objektumtárat (blobtárat). Majd az Azure Portal segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: tamram
ms.openlocfilehash: edadcc4025913052e048ea94d47cac253e4bcd1a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95523331"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása az Azure Portal használatával

Ez a rövid útmutató azt ismerteti, hogyan használható az [Azure Portal](https://portal.azure.com/) egy Azure Storage-tároló létrehozására, valamint blokkblobok fel- és letöltésére a tárolóban.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Tároló létrehozása

Az Azure Portalon a következő lépések végrehajtásával hozhat létre egy tárolót:

1. Az Azure Portalon lépjen az új tárfiókjára.
2. A Storage-fiók bal oldali menüjében görgessen a **blob Service** szakaszra, majd válassza a **tárolók** lehetőséget.
3. Válassza a **+ Tároló** gombot.
4. Adja meg az új tároló nevét. A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat. A tárolók és a Blobok neveivel kapcsolatos további információkért lásd: [tárolók, blobok és metaadatok elnevezése és hivatkozása](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
5. Adja meg a tároló nyilvános hozzáférési szintjét. Az alapértelmezett szint: **Privát (nincs névtelen hozzáférés)**.
6. A tároló létrehozásához válassza az **OK** gombot.

    :::image type="content" source="media/storage-quickstart-blobs-portal/create-container.png" alt-text="A tárolók Azure Portalon történő létrehozását bemutató képernyőkép":::

## <a name="upload-a-block-blob"></a>Blokkblob feltöltése

A blokkblobok blobokká összefűzött adatblokkokból állnak. A Blob Storage legtöbb használati forgatókönyvében blokkblobokat kell használni. A blokkblobok ideális megoldást kínálnak szöveges és bináris adatok (például fájlok, képek és videók) felhőben történő tárolására. Ez a rövid útmutató a blokkblobok használatát mutatja be.

Az Azure Portalon a következő lépések végrehajtásával tölthet fel egy blokkblobot az Azure Portalon lévő új tárolóba:

1. Az Azure Portalon lépjen az előző szakaszban létrehozott tárolóhoz.
1. Válassza ki a tárolót a benne lévő blobok listájának megtekintéséhez. Ez a tároló új, így még nem tartalmaz blobokat.
1. Kattintson a **feltöltés** gombra a feltöltés panel megnyitásához, és tallózással keresse meg a helyi fájlrendszert, és keresse meg a blokkoló blobként feltölteni kívánt fájlt. A **speciális** szakaszt kibontva a feltöltési művelet egyéb beállításait is konfigurálhatja.

    :::image type="content" source="media/storage-quickstart-blobs-portal/upload-blob.png" alt-text="A Blobok helyi meghajtóról a Azure Portal használatával történő feltöltését bemutató képernyőkép":::

1. A blob feltöltéséhez kattintson a **feltöltés** gombra.
1. Ezzel a módszerrel tetszőleges számú blobot tölthet fel. Látni fogja, hogy az új blobok már fel vannak sorolva a tárolóban.

## <a name="download-a-block-blob"></a>Blokkblob letöltése

A blokkblobok letölthetők a böngészőben történő megjelenítéshez vagy a helyi fájlrendszerben történő mentéshez. A blokkblobok letöltéséhez kövesse az alábbi lépéseket:

1. Lépjen az előző szakaszban feltöltött blobok listájához.
1. Kattintson a jobb gombbal a letölteni kívánt blobra, majd válassza a **Letöltés** elemet.

    :::image type="content" source="media/storage-quickstart-blobs-portal/download-blob.png" alt-text="A blobok Azure Portalon való letöltését bemutató képernyőkép":::

## <a name="delete-a-block-blob"></a>Blokkos blob törlése

A Azure Portal egy vagy több blobjának törléséhez kövesse az alábbi lépéseket:

1. A Azure Portal navigáljon a tárolóhoz.
1. Megjeleníti a tárolóban lévő Blobok listáját.
1. A jelölőnégyzet segítségével Válasszon ki egy vagy több blobot a listából.
1. Kattintson a **Törlés** gombra a kiválasztott Blobok törléséhez.
1. A párbeszédpanelen erősítse meg a törlést, és jelezze, hogy törölni kívánja-e a blob-pillanatképeket is.

:::image type="content" source="media/storage-quickstart-blobs-portal/delete-blobs.png" alt-text="A Blobok törlését bemutató képernyőkép a Azure Portal":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a rövid útmutatóban létrehozott összes erőforrás eltávolításához egyszerűen törölheti a tárolót. Ekkor a rendszer törli az adott tárolóban lévő összes blobot is.

A tároló törlése:

1. Az Azure Portalon lépjen a tárfiókban tárolóinak listájához.
1. Válassza ki a törölni kívánt tárolót.
1. Válassza a **Továbbiak** gombot (**...**), majd a **Törlés** elemet.
1. Erősítse meg, hogy törölni kívánja a tárolót.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy tárolót, és hogyan tölthet fel blobokat Azure Portal használatával. Ha szeretne többet megtudni a blob Storage webalkalmazásból való használatáról, folytassa az oktatóanyagot, amely bemutatja, hogyan tölthet fel képeket egy Storage-fiókba.

> [!div class="nextstepaction"]
> [Oktatóanyag: képadatok feltöltése a felhőbe az Azure Storage szolgáltatással](storage-upload-process-images.md)