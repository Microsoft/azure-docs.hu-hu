---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011738"
---
A Azure Files tárolt összes adatok titkosítva maradnak az Azure Storage Service encryption (SSE) használatával. A Storage szolgáltatás titkosítása a Windows BitLockerhez hasonlóan működik: az adattitkosítás a fájlrendszer szintje alatt történik. Mivel az adattitkosítás az Azure-fájlmegosztás fájlrendszere alatt történik, mivel a lemezre van kódolva, nem szükséges hozzáférni az ügyfélen alapuló kulcshoz az Azure-fájlmegosztás olvasásához vagy írásához. A REST titkosítás az SMB-és NFS-protokollokra is érvényes.

Alapértelmezés szerint a Azure Filesban tárolt adatforgalom Microsoft által felügyelt kulcsokkal van titkosítva. A Microsoft által felügyelt kulcsokkal a Microsoft a kulcsokat az adat titkosítására és visszafejtésére, valamint az azok rendszeres elforgatására felelős. Azt is megteheti, hogy felügyeli a saját kulcsait, így szabályozhatja a rotációs folyamatot. Ha úgy dönt, hogy az ügyfél által felügyelt kulcsokkal titkosítja a fájlmegosztást, Azure Files jogosult a kulcsok elérésére az ügyfelektől érkező olvasási és írási kérések teljesítéséhez. Az ügyfél által felügyelt kulcsokkal bármikor visszavonhatja ezt az engedélyt, de ez azt jelenti, hogy az Azure-fájlmegosztás többé nem lesz elérhető az SMB-n vagy a legtöbbet a kiosztott API-n keresztül.

Azure Files ugyanazt a titkosítási sémát használja, mint a többi Azure Storage-szolgáltatás, például az Azure Blob Storage. Az Azure Storage Service encryption (SSE) szolgáltatással kapcsolatos további információkért lásd: [Az Azure Storage titkosítása az inaktív adatokhoz](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).