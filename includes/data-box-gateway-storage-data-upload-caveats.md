---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582108"
---
A következő kikötések az Azure-ba való váltáskor érvényesek az adatvédelemre.

- Javasoljuk, hogy egynél több eszköz ne írjon ugyanahhoz a tárolóhoz.
- Ha van egy meglévő Azure-objektuma (például egy blob vagy egy fájl) a felhőben az éppen másolt objektum nevével, akkor az eszköz felülírja a fájlt a felhőben.
- A megosztási mappák alatt létrehozott üres címtár-hierarchiát (fájlok nélkül) a blob-tárolók nem töltötték fel.
- Az Adatmásolás a Fájlkezelőben vagy a parancssoron keresztül is elvégezhető. Ha a másolt fájlok összesített mérete meghaladja a 10 GB-ot, javasoljuk, hogy használjon tömeges másolási programot, például `Robocopy` vagy `rsync` . A tömeges másolási eszközök megismétlik a másolási műveletet időszakos hibák esetén, és további rugalmasságot biztosítanak.
- Ha az Azure Storage-tárolóhoz társított megosztás olyan blobokat tölt fel, amelyek nem egyeznek a megosztáshoz a létrehozáskor meghatározott Blobok típusával, akkor az ilyen Blobok nem frissülnek. Ha például létrehoz egy blokk blob-megosztást az eszközön, társítsa a megosztást egy olyan meglévő felhő-tárolóval, amely az oldal Blobokkal rendelkezik, frissítse a megosztást a fájlok letöltéséhez, majd módosítsa azokat a frissített fájlokat, amelyek már a felhőben blobként vannak tárolva, a feltöltési hibákat fogja látni.
- Miután létrehozott egy fájlt a megosztásokban, a fájl átnevezése nem támogatott.
- A fájlok megosztásból való törlése nem törli a bejegyzéseket a tárfiókból.
- Ha az `rsync` Adatmásolást használja, akkor `rsync -a` a beállítás nem támogatott.