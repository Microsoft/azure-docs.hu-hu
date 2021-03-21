---
title: BLOB Storage-adatAzure Storage Explorer-csoport adatelemzési folyamatának áthelyezése
description: Ismerje meg, hogyan tölthetők fel és tölthetők le az Azure-Blob Storageokból az Azure Storage Explorer használatával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 53cb8cdd1c5f9824b07b16b8b6c70648603b9f38
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788909"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Adatok áthelyezése Azure-Blob Storageba és onnan az Azure Storage Explorer használatával
A Azure Storage Explorer a Microsoft ingyenes eszköze, amely lehetővé teszi az Azure Storage-adatok használatát Windows, macOS és Linux rendszeren. Ez a témakör leírja, hogyan töltheti fel és töltheti le az Azure Blob Storageról származó adatok feltöltését és letöltését. Az eszköz letölthető [Microsoft Azure Storage Explorerról](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Ha olyan virtuális gépet használ, amely az Azure-beli [adatelemzési virtuális gépek](../data-science-virtual-machine/overview.md)által biztosított parancsfájlokkal lett beállítva, akkor Azure Storage Explorer már telepítve van a virtuális gépen.
> 
> [!NOTE]
> Az Azure Blob Storage teljes bevezetéséhez tekintse meg az [Azure Blob alapjai](../../storage/blobs/storage-quickstart-blobs-dotnet.md) és az [Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts)témakört.   
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez a dokumentum feltételezi, hogy rendelkezik Azure-előfizetéssel, egy Storage-fiókkal és az adott fiókhoz tartozó tárolási kulccsal. Az adatfeltöltés/-letöltés előtt ismernie kell az Azure Storage-fiók nevét és a fiók kulcsát. 

* Azure-előfizetés beállításához tekintse meg az [ingyenes egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).
* A Storage-fiók létrehozásával, valamint a fiók-és a kulcsfontosságú információk beszerzésével kapcsolatos útmutatásért lásd: [Tudnivalók az Azure Storage-fiókokról](../../storage/common/storage-account-create.md). Jegyezze fel a Storage-fiók elérési kulcsát, mivel szüksége van erre a kulcsra ahhoz, hogy a Azure Storage Explorer eszközzel csatlakozhasson a fiókhoz.
* A Azure Storage Explorer eszköz letölthető [Microsoft Azure Storage Explorerról](https://storageexplorer.com/). A telepítés során fogadja el az alapértelmezett értékeket.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Az Azure Storage Explorer használata
A következő lépések bemutatják, hogyan tölthetők fel/tölthetők le az adatok a Azure Storage Explorer használatával. 

1. Microsoft Azure Storage Explorer elindítása.
2. Ha be szeretné állítani a **bejelentkezést a fiókjába...** varázsló, válassza az **Azure-Fiókbeállítások** ikont, majd **vegyen fel egy fiókot** , és adja meg a hitelesítő adatokat. 
![Azure Storage-fiók hozzáadása](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. A **Kapcsolódás az Azure Storage** -hoz varázsló létrehozásához válassza a **Kapcsolódás az Azure Storage-hoz** ikont. ![Kattintson a "kapcsolódás az Azure Storage-hoz" elemre.](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Adja meg az Azure Storage-fiók elérési kulcsát a **Kapcsolódás az Azure Storage-hoz** varázsló, majd a **tovább** gombra. ![Adja meg az Azure Storage-fiók elérési kulcsát](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Adja meg a Storage-fiók nevét a **fióknév** mezőben, majd kattintson a **tovább** gombra. ![Külső tárterület csatolása](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Ekkor megjelenik a hozzáadott Storage-fiók. Ha BLOB-tárolót szeretne létrehozni egy Storage-fiókban, kattintson a jobb gombbal a **blob** -tárolók csomópontra a fiókban, válassza a **blob-tároló létrehozása** elemet, és adjon meg egy nevet.
7. Az adatok tárolóba való feltöltéséhez válassza ki a tárolót, és kattintson a **feltöltés** gombra.
![Tárfiókok](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. A **fájlok** mező jobb oldalán kattintson a **...** elemre, válasszon ki egy vagy több fájlt a fájlrendszerből való feltöltéshez, majd kattintson a **feltöltés** gombra a fájlok feltöltésének megkezdéséhez. ![ Fájlok feltöltése](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Az adatletöltéshez válassza ki a blobot a megfelelő tárolóban a letöltéshez, majd kattintson a **Letöltés** gombra. ![Fájlok letöltése](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)
