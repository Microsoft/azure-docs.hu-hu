---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dc18efe03cbc8a3f657ae4afc781941e8e76611c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466758"
---
A következő kikötések az Azure-ba való váltáskor érvényesek az adatvédelemre.

- Javasoljuk, hogy egynél több eszköz ne írjon ugyanahhoz a tárolóhoz.
- Ha van egy meglévő Azure-objektuma (például egy blob vagy egy fájl) a felhőben az éppen másolt objektum nevével, az eszköz felülírja a fájlt a felhőben.
- A megosztási mappák alatt létrehozott üres címtár-hierarchiát (fájlok nélkül) a blob-tárolók nem töltötték fel.
- Az adatok másolásához használhat húzást a Fájlkezelőben, vagy a parancssor használatával is másolhatja őket. Ha a másolt fájlok összesített mérete meghaladja a 10 GB-ot, azt javasoljuk, hogy használjon tömeges másolási programot, például Robocopyt vagy rsyncet. A tömeges másolási eszközök folytatják a másolási műveletet az időszakos hibák esetében, és további rugalmasságot biztosítanak. Ha a blob Storage-t REST-on keresztül használja, a AzCopy vagy a Azure Storage Explorer használható.
- Ha az Azure Storage-tárolóhoz társított megosztás olyan blobokat tölt fel, amelyek nem egyeznek a megosztáshoz a létrehozáskor meghatározott Blobok típusával, akkor az ilyen Blobok nem frissülnek. Például egy blokkblob-megosztást hoz létre az eszközön. Társítsa a megosztást egy lapblobokkal rendelkező, meglévő felhőbeli tárolóhoz. Frissítse a megosztást a fájlok letöltéséhez. Módosítson néhány frissített fájlt, amelyet a rendszer már lapblobként tárol a felhőben. Feltöltési hibákat fog tapasztalni.
- Miután létrehozott egy fájlt a megosztásokban, a fájl átnevezése nem támogatott.
- A fájlok megosztásból való törlése nem törli a bejegyzéseket a tárfiókból.
- Ha az rsync használatával másol Adatmásolást, akkor `rsync -a` a beállítás nem támogatott.
