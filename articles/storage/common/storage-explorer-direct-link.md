---
title: Azure Storage Explorer közvetlen kapcsolat | Microsoft Docs
description: Azure Storage Explorer közvetlen hivatkozás dokumentációja
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 121df1e1c3ab6d0741d3e4da1144a86938da70ed
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582254"
---
# <a name="azure-storage-explorer-direct-link"></a>Azure Storage Explorer közvetlen hivatkozás

Windows és macOS rendszeren a Storage Explorer támogatja az URI-ket a `storageexplorer://` protokollal. Ezeket az URI-ket közvetlen hivatkozásoknak nevezzük. A közvetlen hivatkozás egy Azure Storage-erőforrásra mutat Storage Explorerban. A közvetlen hivatkozás után megnyílik Storage Explorer és navigáljon ahhoz az erőforráshoz, amelyre rámutat. Ez a cikk azt ismerteti, hogyan működnek a közvetlen kapcsolatok és hogyan használhatók.

## <a name="how-direct-links-work"></a>A közvetlen kapcsolatok működése

Storage Explorer a fanézet használatával jeleníti meg az erőforrásokat az Azure-ban. A közvetlen hivatkozás a fa csatolt erőforrás-csomópontjának hierarchikus információit tartalmazza. Közvetlen hivatkozás követése esetén Storage Explorer megnyílik, és fogadja a paramétereket a közvetlen hivatkozásban. Storage Explorer ezután ezekkel a paraméterekkel navigáljon a csatolt erőforráshoz a fanézetben.

> [!IMPORTANT]
> Be kell jelentkeznie, és rendelkeznie kell a megfelelő engedélyekkel ahhoz, hogy egy közvetlen hivatkozáshoz hozzáférjen a kapcsolódó erőforráshoz.

## <a name="parameters"></a>Paraméterek

Egy Storage Explorer közvetlen hivatkozás mindig a protokollal kezdődik `storageexplorer://` . A következő táblázat ismerteti a közvetlen hivatkozás lehetséges paramétereit.

Paraméter | Leírás
:---------| :---------
`v`         | A közvetlen hivatkozás protokolljának verziója.
`accountid` | A társított erőforráshoz tartozó Storage-fiók Azure Resource Manager erőforrás-azonosítója. Ha a társított erőforrás egy Storage-fiók, akkor ez az azonosító lesz az adott Storage-fiók Azure Resource Manager erőforrás-azonosítója. Ellenkező esetben ez az azonosító lesz annak a Storage-fióknak a Azure Resource Manager erőforrás-azonosítója, amelyhez a társított erőforrás tartozik.
`resourcetype` | Választható. Csak akkor használható, ha a csatolt erőforrás blob-tároló, fájlmegosztás, üzenetsor vagy tábla. A következők egyike lehet: "Azure. BlobContainer", "Azure. fájlmegosztás", "Azure. üzenetsor", "Azure. fájlmegosztás".
`resourcename` | Választható. Csak akkor használható, ha a csatolt erőforrás blob-tároló, fájlmegosztás, üzenetsor vagy tábla. A csatolt erőforrás neve.

Az alábbi példa egy blob-tárolóra mutató közvetlen hivatkozást mutat be. 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Közvetlen hivatkozás beszerzése Storage Explorer

A Storage Explorer használatával közvetlen hivatkozást kaphat egy erőforráshoz. Nyissa meg az erőforrás csomópontjának helyi menüjét a fanézetben. Ezután használja a "közvetlen hivatkozás másolása" műveletet a vágólapra mutató közvetlen hivatkozás másolásához.

## <a name="direct-link-limitations"></a>Közvetlen kapcsolat korlátozásai

A közvetlen hivatkozások csak az előfizetési csomópontok alatti erőforrások esetében támogatottak. Emellett csak a következő erőforrástípusok támogatottak:

- Storage-fiókok
- Blobtárolók
- Üzenetsorok
- Fájlmegosztások
- Táblázatok
