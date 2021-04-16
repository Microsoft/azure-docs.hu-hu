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
ms.openlocfilehash: 18f64defbc4222b46d858cb9cfd3e9b56866a4d0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511922"
---
Az Azure a következő beépített Azure-szerepköröket biztosítja a blob- és üzenetsoradatokhoz való hozzáférés Azure AD és OAuth használatával való hitelesítéséhez:

- [Storage-blobadatok tulajdonosa](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Beállíthatja a tulajdonost, illetve kezelheti az Azure Data Lake Storage Gen2 POSIX hozzáférés-vezérlését. További információért lásd: [Hozzáférés-vezérlés az Azure Data Lake Storage Gen2-ben](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage-blobadatok közreműködője](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Olvasási/írási/törlési engedélyeket adhat a Blob Storage-erőforrásoknak.
- [Storage-blobadatok olvasója](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Csak olvasási engedélyeket adhat a Blob Storage-erőforrásoknak.
- [Storage-blobadatok delegátora](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Lekérhet egy felhasználódelegálási kulcsot, amellyel létrehozhat egy Azure AD hitelesítő adatokkal aláírt, közös hozzáférésű jogosultságkódot egy tároló vagy blob számára.
- [Storage-üzenetsoradatok közreműködője](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Olvasási/írási/törlési engedélyeket adhat az Azure-üzenetsoroknak.
- [Storage-üzenetsoradatok olvasója](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Csak olvasási engedélyeket adhat az Azure-üzenetsoroknak.
- [Storage-üzenetsoradatok üzenetfeldolgozója](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Betekintési, lekérési és törlési engedélyeket adhat az Azure Storage-üzenetsorok üzeneteinek.
- [Storage-üzenetsoradatok üzenetküldője](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Hozzáadási engedélyeket adhat az Azure Storage-üzenetsorok üzeneteinek.

Csak az adateléréshez kifejezetten definiált szerepkörök teszik lehetővé a rendszerbiztonsági tag számára a blob- vagy üzenetsoradatokhoz való hozzáférést. Az olyan beépített szerepkörök, mint  a **Tulajdonos,** Közreműködő és Tárfiók-közreműködő, lehetővé teszik a rendszerbiztonsági tag számára a tárfiókok kezelését, de nem biztosítanak hozzáférést a fiókon belüli blob- vagy üzenetsoradatokhoz az Azure AD-n keresztül. Ha azonban egy szerepkör tartalmazza a **Microsoft.Storage/storageAccounts/listKeys/action** adatokat, akkor az a felhasználó, akihez ez a szerepkör hozzá van rendelve, megosztott kulcsos hitelesítéssel férhet hozzá a tárfiók adataihoz a fiók hozzáférési kulcsával. További információ: [Blob Azure Portal- vagy üzenetsoradatok elérése a következővel:](../articles/storage/blobs/authorize-data-operations-portal.md).

Az Azure Storage az adatszolgáltatásokhoz és a felügyeleti szolgáltatáshoz készült beépített  Azure-szerepkörökkel kapcsolatos részletes információkért lásd a Storage szakaszt az Azure beépített azure [RBAC-szerepkörei című részben.](../articles/role-based-access-control/built-in-roles.md#storage) Emellett az Azure-ban engedélyeket biztosítanak különböző szerepkörtípusokkal kapcsolatos információkért lásd: Klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és [Azure AD-szerepkörök.](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)

> [!IMPORTANT]
> Az Azure-beli szerepkör-hozzárendelések propagálása akár 30 percet is igénybe vehet.
