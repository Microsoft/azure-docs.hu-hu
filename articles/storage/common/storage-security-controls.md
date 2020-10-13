---
title: Biztonsági vezérlők
titleSuffix: Azure Storage
description: 'Tekintse meg a Security Control ellenőrzőlistákat az Azure Storage kiértékeléséhez. Érintett területek: adatvédelem, hálózat, figyelés és naplózás, identitás és konfiguráció.'
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 3b4d74e7ba869e0438a936817d824e841823d472
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715722"
---
# <a name="security-controls-for-azure-storage"></a>Az Azure Storage biztonsági vezérlői

Ez a cikk az Azure Storage-ba beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen |  |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Lásd: [Storage Service encryption az ügyfél által felügyelt kulcsokkal Azure Key Vaultban](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Oszlop szintű titkosítás (Azure Data Services)| N.A. |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | A szabványos HTTPS/TLS-mechanizmusok támogatása.  A felhasználók a szolgáltatásba való továbbítás előtt is titkosítani tudják az adatokat. |
| Titkosított API-hívások| Igen |  |

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| Szolgáltatás-címkék támogatása| Igen | Az Azure Storage által támogatott szolgáltatási címkékkel kapcsolatos további információkért lásd: az [Azure-szolgáltatások címkéi – áttekintés](../../virtual-network/service-tags-overview.md) . |
| VNet-befecskendezés támogatása| N.A. |  |
| Hálózati elkülönítés és tűzfal-támogatás| Igen | |
| Kényszerített bújtatás támogatása| N.A. |  |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Azure Monitor metrikák|
| Vezérlési és felügyeleti síkok naplózása és naplózása | Igen | Azure-tevékenységnapló |
| Adatsíkok naplózása és naplózása| Igen | Erőforrás-naplók Azure Monitor |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Hitelesítés| Igen | Azure Active Directory, megosztott kulcs, közös hozzáférési jogkivonat. |
| Engedélyezés| Igen | Támogatás engedélyezése Azure RBAC, POSIX ACL-eken és SAS-tokeneken keresztül |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Azure Resource Manager API-k támogatása az erőforrás-szolgáltatónál |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../../security/fundamentals/security-controls.md).