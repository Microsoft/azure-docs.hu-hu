---
title: Megbízható tárterület Media Services
description: A felügyelt identitások hitelesítése lehetővé teszi, hogy a Media Services hozzáférhessen a megbízható tárolók hozzáférésével tűzfallal vagy VNet-korlátozással konfigurált Storage-fiókhoz.
keywords: megbízható tárolás, felügyelt identitások
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: fd92eed127ec50a3d3a86f667d9aa764b79c190a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585407"
---
# <a name="trusted-storage-for-media-services"></a>Megbízható tárterület Media Services

Media Services fiók létrehozásakor hozzá kell rendelnie egy Storage-fiókhoz. A Media Services a rendszerhitelesítés vagy a felügyelt identitás-hitelesítés használatával érheti el a Storage-fiókot. Media Services ellenőrzi, hogy a Media Services fiók és a Storage-fiók ugyanahhoz az előfizetéshez tartozik-e, és ellenőrzi, hogy a társítást hozzáadó felhasználó hozzáfér-e a Storage-fiókhoz Azure Resource Manager RBAC.

>[!NOTE]
>A megbízható tárterület csak az API-ban érhető el, és jelenleg nincs engedélyezve a Azure Portal.

## <a name="trusted-storage-with-a-firewall"></a>Megbízható tárterület tűzfallal

Ha azonban tűzfalat szeretne használni a Storage-fiók biztonságossá tételéhez és a megbízható tárolás engedélyezéséhez, a [felügyelt identitások](concept-managed-identities.md) hitelesítése az előnyben részesített lehetőség. Lehetővé teszi Media Services számára, hogy hozzáférjen a megbízható tároló-hozzáférésen keresztül tűzfallal vagy VNet-korlátozással konfigurált Storage-fiókhoz.

## <a name="tutorial"></a>Oktatóanyag

További információt a megbízható tárolás engedélyezéséről a [Media Services megbízható tárolóval](tutorial-trusted-storage-rest.md) foglalkozó oktatóanyagban talál.

> [!NOTE]
> Ahhoz, hogy a Media Services képes legyen olvasni és írni a Storage-fiókot, meg kell adnia az AMS által felügyelt identitás tárolójának blob-adatközreműködői hozzáférését.  Az általános közreműködő szerepkör megadása nem fog működni, mert nem engedélyezi a megfelelő engedélyeket az adatsíkon.

## <a name="further-reading"></a>További információ

A felügyelt identitásokkal rendelkező megbízható tárolók létrehozási módszereinek megismeréséhez olvassa el a [felügyelt identitások és a Media Services](concept-managed-identities.md).

A megbízható Microsoft-szolgáltatásokkal kapcsolatos további információkért lásd: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogy az Ön és az Azure-alkalmazások milyen felügyelt identitásokkal rendelkeznek, tekintse meg az [Azure ad által felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md)című témakört.
