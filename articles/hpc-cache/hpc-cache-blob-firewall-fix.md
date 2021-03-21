---
title: A tárolási tűzfal beállításainak használata
description: A Storage-fiók hálózati tűzfalának beállítása hibát okozhat, ha Azure Blob Storage-tárolót hoz létre az Azure HPC cache szolgáltatásban. Ebben a cikkben megkerülő megoldással megkerülheti a korlátozást, amíg meg nem történik a szoftver javítása.
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: v-erkel
ms.openlocfilehash: 10d68ce679fe42f5deeaae364bc46adb23436a27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587151"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>A Blob Storage-fiók tűzfalbeállításainak kikerülése

A Storage-fiók tűzfalaban használt bizonyos beállítások miatt a blob Storage-cél létrehozása sikertelen lesz. Az Azure HPC cache csapata a probléma egy szoftveres javításán dolgozik, de a cikk utasításait követve megkerülheti.

A tűzfal azon beállítása, amely csak a "kiválasztott hálózatokból" engedélyezi a hozzáférést, megakadályozhatja, hogy a gyorsítótár hozzon létre vagy módosítson egy blob Storage-célt. Ez a konfiguráció a Storage-fiók **tűzfalak és a virtuális hálózatok** beállítások lapján található.

A probléma az, hogy a gyorsítótár-szolgáltatás egy olyan rejtett szolgáltatás virtuális hálózatot használ, amely külön az ügyfél-környezettől. Nem lehet explicit módon engedélyezni ezt a hálózatot a Storage-fiók eléréséhez.

BLOB Storage-tároló létrehozásakor a gyorsítótár-szolgáltatás ezt a hálózatot használja annak ellenőrzése érdekében, hogy a tároló üres-e. Ha a tűzfal nem engedélyezi a hozzáférést a rejtett hálózatról, az ellenőrzés sikertelen lesz, és a tárolási cél létrehozása sikertelen lesz.

A tűzfal blokkolja a blob Storage-névtér elérési útjának módosításait is.

A probléma megkerüléséhez átmenetileg módosítsa a tűzfal beállításait a tárolási cél létrehozásakor:

1. Nyissa meg a Storage-fiók **tűzfalak és virtuális hálózatok** lapját, és módosítsa a "hozzáférés engedélyezése" beállítást **minden hálózatra**.
1. Hozza létre a blob Storage-tárolót az Azure HPC-gyorsítótárban.
1. Hozza létre a tárolási cél névterének elérési útját.
1. A tárolási cél és az elérési út sikeres létrehozása után módosítsa a fiók tűzfalának beállításait a **kiválasztott hálózatokra**.

Az Azure HPC-gyorsítótár nem használja a szolgáltatás virtuális hálózatát a befejezett tárolási cél eléréséhez.

A megkerülő megoldással kapcsolatos segítségért [forduljon a Microsoft szolgáltatáshoz és a támogatási](hpc-cache-support-ticket.md)szolgálathoz.
