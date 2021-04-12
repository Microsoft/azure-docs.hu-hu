---
title: Ügyfél által felügyelt kulcsok vagy BYOK portál használata
description: Ebben az oktatóanyagban a Azure Portal használatával engedélyezheti az ügyfél által felügyelt kulcsokat, vagy saját kulcsot (BYOK) hozhat egy Azure Media Services Storage-fiókkal.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: e34649162c7a30d4ab43aa068d2c864b5c2d90e0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282346"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>Oktatóanyag: az ügyfél által felügyelt kulcsok vagy BYOK használata a Azure Portal használatával Media Services

Az 2020-05-01 API-val az ügyfél által felügyelt RSA-kulcs egy rendszerfelügyelt identitással rendelkező Azure Media Services-fiókkal használható. Ez az oktatóanyag a Azure Portal lépéseit ismerteti.

A használt szolgáltatások a következők:

- Azure Storage
- Azure Key Vault
- Azure Media Services

Ebből az oktatóanyagból megismerheti, hogyan használhatja a Azure Portal a következőkre:

> [!div class="checklist"]
> - Hozzon létre egy erőforráscsoportot.
> - Hozzon létre egy rendszer által felügyelt identitású Storage-fiókot.
> - Rendszer által felügyelt identitással rendelkező Media Services-fiók létrehozása.
> - Hozzon létre egy Key vaultot az ügyfél által felügyelt RSA-kulcs tárolásához.

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free/).

## <a name="system-managed-keys"></a>Rendszerfelügyelt kulcsok

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> A következő Storage-fiók létrehozási lépéseihez válassza ki a rendszer által felügyelt kulcs választását a speciális beállítások területen.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> A következő tárolási titkosítási lépéseknél válassza ki az **ügyfél által felügyelt kulcs választását**.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>A kulcs módosítása

Media Services automatikusan észleli a kulcs módosításakor. Nem kötelező: a folyamat teszteléséhez hozzon létre egy másik, ugyanarra a kulcsra vonatkozó verziót. Media Servicesnak meg kell állapítania, hogy a kulcs megváltozott.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a létrehozott erőforrások használatát, és *nem kívánja tovább használni a számlázást*, törölje őket.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan teheti meg a következőket:
> [!div class="nextstepaction"]
> [Távoli fájl kódolása URL-cím alapján és a videó továbbítása a REST-tel](stream-files-tutorial-with-rest.md)
