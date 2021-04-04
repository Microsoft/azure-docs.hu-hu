---
title: Adatkészletek hozzáadása egy meglévő Azure-adatmegosztáshoz
description: Megtudhatja, hogyan adhat hozzá adatkészleteket egy meglévő adatmegosztáshoz az Azure-adatmegosztásban, és hogyan oszthat meg ugyanazzal a címzettekkel.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 3bfa911921e9bacde2649ee8c4f0d4bc31b56f54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910542"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Adatkészletek hozzáadása meglévő megosztáshoz az Azure-beli adatmegosztásban

Ez a cikk azt ismerteti, hogyan adhat hozzá adatkészleteket egy már meglévő adatmegosztáshoz az Azure-adatmegosztás használatával. Így több adatmegosztást is megoszthat ugyanazzal a címzettekkel anélkül, hogy új megosztást kellene létrehoznia.

Az adatkészletek megosztás létrehozásakor történő hozzáadásával kapcsolatos információkért tekintse meg az [adatok megosztása](share-your-data.md) oktatóanyagot.

## <a name="navigate-to-a-sent-data-share"></a>Navigáljon az elküldett adatmegosztáshoz

Az Azure-adatmegosztás területen navigáljon az elküldett megosztáshoz, és válassza az **adatkészletek** lapot. További adathalmazok hozzáadásához kattintson az **+ adatkészletek hozzáadása** gombra.

![Képernyőkép: a kiválasztott adatkészletek hozzáadása.](./media/how-to/how-to-add-datasets/add-datasets.png)

A jobb oldali panelen válassza ki a hozzáadni kívánt adatkészlet típusát, majd kattintson a **tovább** gombra. Válassza ki azt az előfizetést és erőforráscsoportot, amelyet hozzá szeretne adni. A legördülő nyilak használatával keresse meg, majd jelölje be a hozzáadandó adatmező melletti jelölőnégyzetet.

![Képernyőfelvétel: a Blob Storage hozzáadása panel, ahol kiválaszthatja az adatelemet.](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Ha az **adatkészletek hozzáadása** gombra kattint, a rendszer hozzáadja az adatkészleteket a megosztáshoz. Megjegyzés: a felhasználóknak el kell indítaniuk a pillanatképet ahhoz, hogy megjelenjenek az új adatkészletek. Ha vannak beállított pillanatkép-beállítások, a felhasználók a következő ütemezett pillanatkép befejeződése után láthatják az új adatkészleteket. A pillanatkép-beállítások konfigurálása nélkül a felhasználónak manuálisan kell elindítania az adatok teljes vagy növekményes másolatát a frissítések fogadásához. A pillanatképekkel kapcsolatos további információkért lásd: [Pillanatképek](terminology.md).

## <a name="next-steps"></a>Következő lépések
További információ a [címzettek meglévő adatmegosztáshoz való hozzáadásáról](how-to-add-recipients.md).