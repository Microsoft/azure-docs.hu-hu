---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6e819e1078ac90ef16070702e7961122b06c1d6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107880950"
---
Előzetes verzióban az NFS a következő korlátozásokkal rendelkezik:

- Az NFS 4.1 jelenleg csak a protokollspecifikáció legtöbb [funkcióját támogatja.](https://tools.ietf.org/html/rfc5661) Egyes funkciók, például a delegálások és a visszahívás, a zárolásfrissítések és a visszalépések, a Kerberos-hitelesítés és a titkosítás nem támogatottak.
- Ha a kérések többsége metaadat-központú, akkor a késés rosszabb lesz az olvasási/írási/frissítési műveletekhez képest.
- Az NFS-megosztások csak az új tárfiók/fiókok esetében engedélyezhetők/hoznak létre, a meglévő tárfiókok esetében nem
- Csak a felügyeleti sík REST API-k támogatottak. Az adatsík REST API-i nem érhetők el, ami azt jelenti, hogy az Storage Explorer és hasonló eszközök nem fognak működni az NFS-megosztásokkal, és nem is tallózhat az NFS-megosztási adatok között a Azure Portal.
- Az AzCopy jelenleg nem támogatott.
- Csak a prémium szintű csomaghoz érhető el.
- Az NFS-megosztások csak numerikus UID/GID-t fogadnak el. Annak érdekében, hogy az ügyfelek ne küldsenek alfanumerikus UID-t/GID-t, tiltsa le az azonosítóleképezést.
- Privát kapcsolatok használata esetén a megosztások csak egy adott virtuális gép tárfiókja számára csatlakoztathatóak. A más tárfiókok megosztási kísérlete sikertelen lesz.
- A legjobb, ha az elsődleges csoporthoz rendelt engedélyekre támaszkodik. Előfordulhat, hogy a felhasználó nem elsődleges csoportjához rendelt engedélyek egy ismert hiba miatt megtagadják a hozzáférést.

### <a name="azure-storage-features-not-yet-supported"></a>Az Azure Storage még nem támogatott funkciói

Emellett az alábbi Azure Files nfs-megosztások nem érhetők el:

- Identitásalapú hitelesítés
- Azure Backup támogatás
- Pillanatképek
- Helyreállítható törlés
- Teljes körű titkosítás az átvitel során (részletekért lásd: [NFS-biztonság](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (csak Windows-ügyfelek esetén érhető el, amelyeket az NFS 4.1 nem támogat)
