---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c0ca96d1829a73f856de021d1286e53007b03219
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87368973"
---
Az Azure az alábbi Azure beépített szerepköröket biztosítja a blob-és üzenetsor-információhoz való hozzáférés engedélyezéséhez az Azure AD és a OAuth használatával:

- [Storage blob-adattulajdonos](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): a használatával a Azure Data Lake Storage Gen2 számára biztosíthatja a tulajdonosi és a POSIX hozzáférés-vezérlést. További információ: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage blob-adatközreműködői](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): a használatával olvasási/írási/törlési engedélyeket adhat a blob Storage-erőforrásokhoz.
- [Storage blob-Adatolvasó](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): a használatával írásvédett engedélyeket adhat a blob Storage-erőforrásokhoz.
- [Storage blob-delegáló](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): felhasználói delegálási kulcs beszerzése, amely egy tároló vagy blob Azure ad-beli hitelesítő adataival aláírt közös hozzáférési aláírás létrehozásához használható.
- [Storage-üzenetsor adatközreműködői](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): a használatával olvasási/írási/törlési engedélyeket adhat az Azure Queues szolgáltatásnak.
- [Storage-üzenetsor Adatolvasója](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): a használatával csak olvasási jogosultságot adhat az Azure Queues szolgáltatásnak.
- [Storage-üzenetsor adatüzenet-processzora](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): a használatával megtekintheti az Azure Storage-várólistákban lévő üzenetekhez való betekintés, lekérés és törlés engedélyeit.
- [Tárolási várólista Adatüzenetének küldője](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): a használatával adhat hozzá engedélyeket az üzenetekhez az Azure Storage-várólistákban.

Csak az adathozzáféréshez explicit módon definiált szerepköröknek kell megadniuk a rendszerbiztonsági tag számára a blob-vagy üzenetsor-adat elérését. A beépített szerepkörök, például a **tulajdonos**, a **közreműködő**és a **Storage-fiók közreműködői** lehetővé teszik a rendszerbiztonsági tag számára a Storage-fiókok kezelését, de nem biztosítanak hozzáférést az adott fiókon belüli blob-vagy ÜZENETSOR-információhoz az Azure ad-n keresztül. Ha azonban egy szerepkör tartalmazza a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/műveletet**, akkor a szerepkörhöz hozzárendelt felhasználó a fiók hozzáférési kulcsainak megosztott kulcson keresztüli engedélyezésével férhet hozzá a Storage-fiókban tárolt adathoz. További információ: [a Azure Portal használata a blob-vagy üzenetsor-adatok eléréséhez](../articles/storage/common/storage-access-blobs-queues-portal.md).

Az Azure Storage-ban az adatszolgáltatások és a felügyeleti szolgáltatás Azure-beli beépített szerepköreivel kapcsolatos részletes információkért tekintse meg a **Storage** szakaszt az Azure [RBAC beépített Azure-beli szerepkörökben](../articles/role-based-access-control/built-in-roles.md#storage). Emellett az Azure-ban engedélyeket biztosító szerepkörök különböző típusaival kapcsolatos információkért lásd: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure RBAC-szerepkörök és Azure ad-szerepkörök](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> A RBAC szerepkör-hozzárendelések akár öt percet is igénybe vehetnek.
