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
ms.openlocfilehash: f014ce55dc40723faf1b60f908814f9fa0428b8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99214018"
---
Az Azure az alábbi Azure beépített szerepköröket biztosítja a blob-és üzenetsor-információhoz való hozzáférés engedélyezéséhez az Azure AD és a OAuth használatával:

- [Storage-blobadatok tulajdonosa](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Beállíthatja a tulajdonost, illetve kezelheti az Azure Data Lake Storage Gen2 POSIX hozzáférés-vezérlését. További információért lásd: [Hozzáférés-vezérlés az Azure Data Lake Storage Gen2-ben](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage-blobadatok közreműködője](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Olvasási/írási/törlési engedélyeket adhat a Blob Storage-erőforrásoknak.
- [Storage-blobadatok olvasója](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Csak olvasási engedélyeket adhat a Blob Storage-erőforrásoknak.
- [Storage-blobadatok delegátora](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Lekérhet egy felhasználódelegálási kulcsot, amellyel létrehozhat egy Azure AD hitelesítő adatokkal aláírt, közös hozzáférésű jogosultságkódot egy tároló vagy blob számára.
- [Storage-üzenetsoradatok közreműködője](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Olvasási/írási/törlési engedélyeket adhat az Azure-üzenetsoroknak.
- [Storage-üzenetsoradatok olvasója](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Csak olvasási engedélyeket adhat az Azure-üzenetsoroknak.
- [Storage-üzenetsoradatok üzenetfeldolgozója](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Betekintési, lekérési és törlési engedélyeket adhat az Azure Storage-üzenetsorok üzeneteinek.
- [Storage-üzenetsoradatok üzenetküldője](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Hozzáadási engedélyeket adhat az Azure Storage-üzenetsorok üzeneteinek.

Csak az adathozzáféréshez explicit módon definiált szerepköröknek kell megadniuk a rendszerbiztonsági tag számára a blob-vagy üzenetsor-adat elérését. A beépített szerepkörök, például a **tulajdonos**, a **közreműködő** és a **Storage-fiók közreműködői** lehetővé teszik a rendszerbiztonsági tag számára a Storage-fiókok kezelését, de nem biztosítanak hozzáférést az adott fiókon belüli blob-vagy ÜZENETSOR-információhoz az Azure ad-n keresztül. Ha azonban egy szerepkör tartalmazza a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/műveletet**, akkor a szerepkörhöz hozzárendelt felhasználó a fiók hozzáférési kulcsainak megosztott kulcson keresztüli engedélyezésével férhet hozzá a Storage-fiókban tárolt adathoz. További információ: [a Azure Portal használata a blob-vagy üzenetsor-adatok eléréséhez](../articles/storage/blobs/authorize-data-operations-portal.md).

Az Azure Storage-ban az adatszolgáltatások és a felügyeleti szolgáltatás Azure-beli beépített szerepköreivel kapcsolatos részletes információkért tekintse meg a **Storage** szakaszt az Azure [RBAC beépített Azure-beli szerepkörökben](../articles/role-based-access-control/built-in-roles.md#storage). Emellett az Azure-ban engedélyeket biztosító szerepkörök különböző típusaival kapcsolatos információkért lásd: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure ad-szerepkörök](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Az Azure szerepkör-hozzárendelések akár 30 percet is igénybe vehetnek.
