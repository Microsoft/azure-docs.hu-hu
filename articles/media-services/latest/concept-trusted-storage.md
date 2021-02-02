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
ms.openlocfilehash: 59c1eb7936bc113f8935d6fa2ad378c6994c3ca9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408488"
---
# <a name="trusted-storage-for-media-services"></a>Megbízható tárterület Media Services

Media Services fiók létrehozásakor hozzá kell rendelnie egy Storage-fiókhoz. A Media Services rendszerhitelesítéssel érheti el a Storage-fiókot. Media Services ellenőrzi, hogy a Media Services fiók és a Storage-fiók ugyanahhoz az előfizetéshez tartozik-e, és ellenőrzi, hogy a társítást hozzáadó felhasználó hozzáfér-e a Storage-fiókhoz Azure Resource Manager RBAC.

Ha azonban tűzfalat szeretne használni a Storage-fiók biztonságossá tételéhez és a megbízható tárterület engedélyezéséhez, akkor [felügyelt identitások](concept-managed-identities.md) hitelesítését kell használnia. Lehetővé teszi Media Services számára, hogy hozzáférjen a megbízható tároló-hozzáférésen keresztül tűzfallal vagy VNet-korlátozással konfigurált Storage-fiókhoz.

A felügyelt identitásokkal rendelkező megbízható tárolók létrehozási módszereinek megismeréséhez olvassa el a [felügyelt identitások és a Media Services](concept-managed-identities.md).

További információ az ügyfél által felügyelt kulcsokról és Key Vaultekről: [saját kulcs használata (ügyfél által felügyelt kulcsok) a Media Services](concept-use-customer-managed-keys-byok.md)

A megbízható Microsoft-szolgáltatásokkal kapcsolatos további információkért lásd: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="tutorials"></a>Oktatóanyagok

Ezek az oktatóanyagok a fent említett forgatókönyveket is tartalmazzák.

- [Az ügyfél által felügyelt kulcsok vagy BYOK használata a Azure Portal használatával Media Services](tutorial-byok-portal.md)
- [Az ügyfél által felügyelt kulcsokat vagy BYOK Media Services REST API használhatja](tutorial-byok-postman.md).

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogy az Ön és az Azure-alkalmazások milyen felügyelt identitásokkal rendelkeznek, tekintse meg az [Azure ad által felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md)című témakört.