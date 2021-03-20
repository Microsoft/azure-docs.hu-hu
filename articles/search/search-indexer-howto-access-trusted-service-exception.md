---
title: Indexelő hozzáférés az Azure Storage-hoz a megbízható szolgáltatásokkal kapcsolatos kivétel használatával
titleSuffix: Azure Cognitive Search
description: Az Azure-Cognitive Search indexelő általi hozzáférésének engedélyezése az Azure Storage-ban biztonságosan tárolt adateléréshez.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92101375"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>Indexelő hozzáférés az Azure Storage-hoz a megbízható szolgáltatási kivétel (Azure Cognitive Search) használatával

Az Azure-Cognitive Search szolgáltatásban az Azure Storage-fiókokban lévő adathozzáférést használó indexelő a [megbízható szolgáltatásokra vonatkozó kivételi](../storage/common/storage-network-security.md#exceptions) képességet használhatják az adathozzáférés biztonságos eléréséhez. Ez a mechanizmus olyan ügyfelek számára biztosít egyszerű, biztonságos és ingyenes alternatívát, akik a Storage-fiókokban lévő adatok eléréséhez nem tudnak [Indexelő hozzáférést biztosítani az IP-tűzfalszabályok használatával](search-indexer-howto-access-ip-restricted.md) .

> [!NOTE]
> A Storage-fiókokban lévő adatok megbízható szolgáltatási kivételen keresztüli elérésének támogatása az Azure Blob Storage-ra és Azure Data Lake Gen2-tárolóra korlátozódik. Az Azure Table Storage nem támogatott.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>1. lépés: a kapcsolatok konfigurálása felügyelt identitás használatával

Kövesse az [Azure Storage-fiókkal felügyelt identitás használatával létesített kapcsolatok beállítása](search-howto-managed-identities-storage.md)című témakör utasításait. Ha elkészült, a keresési szolgáltatást Azure Active Directory megbízható szolgáltatásként regisztrálja, és az Azure Storage-ban engedélyeket kap az adatokhoz és információkhoz való hozzáféréshez szükséges keresési identitáshoz.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>2. lépés: a megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a Storage-fiókhoz

A Azure Portal navigáljon a Storage-fiók **tűzfalak és virtuális hálózatok** lapjára. Győződjön meg arról, hogy a **megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése ehhez a Storage-fiókhoz** jelölőnégyzet be van jelölve. Ez a beállítás csak az adott keresőszolgáltatás-példány megfelelő szerepköralapú hozzáférését teszi lehetővé a Storage-fiókhoz (erős hitelesítés) a Storage-fiókban lévő adateléréshez, még akkor is, ha az IP-tűzfalszabályok védelme alatt áll.

![Megbízható szolgáltatás kivétele](media\search-indexer-howto-secure-access\exception.png "Megbízható szolgáltatás kivétele")

Az indexelő mostantól hozzáférhetnek a Storage-fiókban lévő adataihoz, még akkor is, ha a fiók IP-tűzfalszabályok használatával védett.

## <a name="next-steps"></a>Következő lépések

További információ az Azure Storage indexelő szolgáltatásáról:

- [Azure Blob-indexelő](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 indexelő](search-howto-index-azure-data-lake-storage.md)
- [Azure Table indexelő](search-howto-indexing-azure-tables.md)