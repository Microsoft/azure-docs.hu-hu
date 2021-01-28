---
title: Felügyelt identitások és megbízható tárolók
description: A Media Services felügyelt identitásokkal is használhatók a megbízható tárolás engedélyezéséhez.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: 291508a6beaa687b3a10f55df4591ce601ab51a0
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956174"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>Felügyelt identitások és megbízható tárolás a Media Services szolgáltatással

A Media Services [felügyelt identitásokkal](../../active-directory/managed-identities-azure-resources/overview.md) is használhatók a megbízható tárolás engedélyezéséhez. Media Services fiók létrehozásakor hozzá kell rendelnie egy Storage-fiókhoz. A Media Services rendszerhitelesítéssel érheti el a Storage-fiókot. Media Services ellenőrzi, hogy a Media Services fiók és a Storage-fiók ugyanahhoz az előfizetéshez tartozik-e, és ellenőrzi, hogy a társítást hozzáadó felhasználó hozzáfér-e a Storage-fiókhoz Azure Resource Manager RBAC.

## <a name="trusted-storage"></a>Megbízható tárterület

Ha azonban tűzfalat szeretne használni a Storage-fiók biztonságossá tételéhez, felügyelt identitás-hitelesítést kell használnia. Lehetővé teszi Media Services számára, hogy hozzáférjen a megbízható tároló-hozzáférésen keresztül tűzfallal vagy VNet-korlátozással konfigurált Storage-fiókhoz.  A megbízható Microsoft-szolgáltatásokkal kapcsolatos további információkért lásd: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="media-services-managed-identity-scenarios"></a>Media Services által felügyelt identitási forgatókönyvek

Jelenleg két forgatókönyv áll rendelkezésre, ahol a felügyelt identitás használható a Media Services használatával:

- A Storage-fiókok eléréséhez használja a Media Services fiók felügyelt identitását.

- A Media Services fiók felügyelt identitásával férhet hozzá az ügyfelek kulcsaihoz Key Vaulthoz.

A következő két szakasz a két forgatókönyv különbségeit írja le.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>A Media Services fiók felügyelt identitásának használata a Storage-fiókok eléréséhez

1. Hozzon létre egy Media Services fiókot felügyelt identitással.
1. Adja meg a felügyelt identitás elsődleges hozzáférését egy Ön által birtokolt Storage-fiókhoz.
1. Media Services ezután a felügyelt identitás használatával férhet hozzá a Storage-fiókhoz az Ön nevében.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>A Media Services fiók felügyelt identitásával férhet hozzá az ügyfelek kulcsaihoz Key Vaulthoz

1. Hozzon létre egy Media Services fiókot felügyelt identitással.
1. Adja meg a felügyelt identitás elsődleges hozzáférését egy Ön által birtokolt Key Vaulthoz.
1. Konfigurálja a Media Services fiókot az ügyfél-kulcson alapuló fiók titkosításának használatára.
1. A Media Services a felügyelt identitás használatával fér hozzá a Key Vaulthoz az Ön nevében.

További információ az ügyfél által felügyelt kulcsokról és Key Vaultekről: [saját kulcs használata (ügyfél által felügyelt kulcsok) a Media Services](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Oktatóanyagok

Ezek az oktatóanyagok a fent említett forgatókönyveket is tartalmazzák.

- [Az ügyfél által felügyelt kulcsok vagy BYOK használata a Azure Portal használatával Media Services](tutorial-byok-portal.md)
- [Az ügyfél által felügyelt kulcsokat vagy BYOK Media Services REST API használhatja](tutorial-byok-postman.md).

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogy az Ön és az Azure-alkalmazások milyen felügyelt identitásokkal rendelkeznek, tekintse meg az [Azure ad által felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md)című témakört.