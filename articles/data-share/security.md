---
title: Az Azure Data Share biztonsági áttekintése
description: Az Azure Data Share biztonsági áttekintése
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 4e62645dd5a7a8336df4fccf12daebc730a91168
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678430"
---
# <a name="security-overview-for-azure-data-share"></a>Az Azure Data Share biztonsági áttekintése

Ez a cikk az Azure-beli adatmegosztási szolgáltatás biztonsági áttekintését tartalmazza.

## <a name="security-overview"></a>Biztonsági áttekintés

Az Azure-adatmegosztás kihasználja az Azure által az inaktív és az átvitel alatt álló adatok védelméhez használt mögöttes biztonsági adatokat. Az adatok titkosítva vannak, ahol a mögöttes adattár támogatja. Az adatátvitel a TLS 1,2 használatával is titkosítva van az átvitel során. Az adatmegosztások metaadatait a rendszer a REST és az átvitel során is titkosítja. Az Azure-beli adatmegosztás nem tárolja a megosztott ügyféladatok tartalmát.

Az Azure-adatmegosztás felügyelt identitást (korábbi nevén MSI) használ az adatok megosztásához használt adattárakhoz való hozzáféréshez. Az adatszolgáltató és az adatfogyasztó között nincs hitelesítő adatok cseréje. A felügyelt identitással kapcsolatos további információkért tekintse meg az [Azure-erőforrások felügyelt identitásait](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)ismertető témakört. Az adatok megosztásához szükséges szerepkörökről és engedélyekről a [szerepkörök és követelmények](concepts-roles-permissions.md)című témakörben olvashat bővebben.

## <a name="access-control"></a>Hozzáférés-vezérlés

Az Azure-adatmegosztás hozzáférés-vezérlése beállítható az adatmegosztási erőforrás szintjén, így biztosítva, hogy azok elérhetők legyenek a jogosultak számára. Az adatmegosztási erőforrások tulajdonosa és közreműködői megoszthatják az adatmegosztási megosztásokat, és módosíthatják a meglévő megosztásokat. Az adatmegosztási erőforrások olvasója megtekintheti a megosztásokat, de nem végezhet módosításokat. 

A megosztások létrehozása vagy fogadása után a megfelelő engedélyekkel rendelkező felhasználók módosíthatják az adatmegosztási erőforrást. Ha egy megosztást létrehozó vagy fogadó felhasználó elhagyja a szervezetet, nem szakítja meg az adatforgalom megosztását vagy leállítását. Az adatmegosztási erőforrásra vonatkozó megfelelő engedélyekkel rendelkező felhasználók továbbra is kezelhetik a megosztást.

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Adatok megosztása a vagy a rendszerből az adattárakba az engedélyezett tűzfallal
Ha be van kapcsolva az adatok és a között a tűzfalon keresztüli fiókok megosztása, engedélyeznie kell a **megbízható Microsoft-szolgáltatások használatát** a Storage-fiókban. További részletek: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) .


## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.
