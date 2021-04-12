---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: c003e765574d764b6653823e8554d0718ea85e0e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081154"
---
Az Azure-fájlmegosztás tárolása a felhőben történik egy Azure Storage-fiókban.
A teljesítménnyel kapcsolatos további szempontokat itt találja.

Ha magas aktív megosztásokkal rendelkezik (több felhasználó és/vagy alkalmazás által használt megosztások), akkor két Azure-fájlmegosztás elérheti a Storage-fiók teljesítményének korlátját.

Ajánlott eljárás a Storage-fiókok üzembe helyezése egy fájlmegosztás használatával.
Több Azure-fájlmegosztás is egyesíthető ugyanabba a Storage-fiókba, ha archiválási megosztást használ, vagy ha a napi tevékenység várható.

Ezek a szempontok többek között a felhőbe való közvetlen hozzáférésre (egy Azure-beli virtuális gépen keresztül) is érvényesek, mint Azure File Sync. Ha csak ezen megosztásokon szeretné használni a Azure File Sync, akkor a többit egyetlen Azure Storage-fiókba csoportosíthatja.

Ha létrehozta a megosztások listáját, az egyes megosztásokat az abban található Storage-fiókhoz kell rendelnie.

Az előző fázisban a megfelelő számú megosztást állapította meg. Ebben a lépésben létrehozta a Storage-fiókok fájlmegosztás számára való hozzárendelését. Most telepítse a megfelelő számú Azure Storage-fiókot a megfelelő számú Azure-fájlmegosztás megtartásával.

Győződjön meg arról, hogy az egyes Storage-fiókok régiója azonos, és megfelel a már üzembe helyezett Storage Sync Service-erőforrás régiójának.

> [!CAUTION]
> Ha olyan Azure-fájlmegosztást hoz létre, amelynek 100-TiB-korlátja van, akkor ez a megosztás csak a helyileg redundáns tárolási vagy zóna-redundáns tárolási redundancia-beállításokat használhatja. Az 100-TiB-fájlmegosztás használata előtt gondolja át, hogy a tárolási redundancia szükséges-e.

Alapértelmezés szerint az Azure-fájlmegosztás továbbra is 5 TiB korláttal hozható létre. Mivel új Storage-fiókokat hoz létre, kövesse az [útmutatást, és hozzon létre olyan Storage-fiókokat, amelyek engedélyezik az Azure-fájlmegosztás 100-TiB-korlátait](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

A Storage-fiókok telepítésekor egy másik szempont az Azure Storage redundancia. Lásd: az [Azure Storage-redundancia beállításai](../articles/storage/common/storage-redundancy.md).

Az erőforrások nevei szintén fontosak. Ha például egy Azure Storage-fiókba csoportosítja a HR-részleg több megosztását, akkor a megfelelő nevet adja a Storage-fióknak. Hasonlóképpen, az Azure-fájlmegosztás elnevezése esetén a helyszíni partnereknek használt nevekhez hasonló neveket kell használnia.