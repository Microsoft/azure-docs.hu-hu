---
title: Azure AD-alkalmazás létrehozása és konfigurálása a Azure Media Services API eléréséhez az Azure CLI használatával | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használható az Azure CLI egy Azure AD-alkalmazás létrehozásához, és hogyan konfigurálható Azure Media Services API eléréséhez.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6517cc8224a1a4e2e3319fde52fb621e4f7ab564
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016933"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Azure AD-alkalmazás létrehozása és konfigurálása a Media Services API eléréséhez az Azure CLI használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Ebből a témakörből megtudhatja, hogyan használhatja az Azure CLI-t egy Azure Active Directory (Azure AD) alkalmazás és egyszerű szolgáltatásnév létrehozásához Azure Media Services erőforrások eléréséhez. 

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Részletekért lásd: az [Azure ingyenes próbaverziója](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információ: [Azure Media Services fiók létrehozása a Azure Portal használatával](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>A Azure Cloud Shell használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Indítsa el a Cloud Shell a portál felső navigációs paneljén.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

További információ: [Azure Cloud Shell áttekintése](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Azure AD-alkalmazás létrehozása és a Media-fiókhoz való hozzáférés konfigurálása az Azure CLI-vel
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Például:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Ebben a példában a **hatókör** a Media Services-fiók teljes erőforrás-elérési útja. A **hatókör** azonban bármilyen szinten lehet.

Például a következő szintek egyike lehet:
 
* Az **előfizetés** szintje.
* Az **erőforráscsoport** szintje.
* Az **erőforrás** szintje (például egy adathordozó-fiók).

További információ: Azure- [szolgáltatásnév létrehozása az Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli)

Lásd még: [Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure CLI-](../../role-based-access-control/role-assignments-cli.md)vel. 

## <a name="next-steps"></a>Következő lépések

Ismerkedés a [fájlok feltöltésével a fiókjába](media-services-portal-upload-files.md).
