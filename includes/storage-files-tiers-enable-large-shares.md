---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99569467"
---
Alapértelmezés szerint a standard fájlmegosztás legfeljebb 5 TiB-ra terjedhet, de a megosztási korlátot 100 TiB-ra növelheti. A megosztási korlát növeléséhez engedélyezze a **nagyméretű fájlmegosztást** a Storage-fiókban. A Premium Storage-fiókok (*FileStorage* Storage-fiókok) nem rendelkeznek a nagyméretű fájlmegosztás-szolgáltatás jelzőjével, mivel az összes prémium fájlmegosztás már engedélyezve van a teljes 100-TiB kapacitás kiépítése érdekében.

A nagyméretű fájlmegosztást csak a helyileg redundáns vagy a zónában redundáns szabványos Storage-fiókok esetében lehet engedélyezni. Ha engedélyezte a nagyméretű fájlmegosztás funkció jelzőjét, a redundancia nem módosítható a Geo-redundáns vagy a Geo-Zone-redundáns tárolóra.

Ha nagyméretű fájlmegosztást szeretne engedélyezni egy meglévő Storage-fiókon, navigáljon a **fájlmegosztás** elemhez a Storage-fiók tartalomjegyzékében.
Ezen a panelen válassza a **kapacitás megosztása** lehetőséget, módosítsa a megosztási kapacitást **100 TiB** értékre, majd válassza a **Mentés** lehetőséget.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="A nagyméretű fájlmegosztás engedélyezése beállítás képernyőképe a Azure Portalban." lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

Az 100-TiB-fájlmegosztás a [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) PowerShell-parancsmagon és az [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) Azure CLI-parancson keresztül is engedélyezhető. A nagyméretű fájlok megosztásának engedélyezésével kapcsolatos részletes utasításokért lásd: [nagyméretű fájlmegosztás engedélyezése és létrehozása](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Ha többet szeretne megtudni arról, hogyan hozhatók létre fájlmegosztás az új Storage-fiókokban, tekintse meg [Az Azure-fájlmegosztás létrehozását](../articles/storage/files/storage-how-to-create-file-share.md)ismertető részt.