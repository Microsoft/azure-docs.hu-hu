---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491011"
---
#### <a name="requirements-for-key-vault-firewall"></a>A Key Vault tűzfallal kapcsolatos követelmények

Ha [Key Vault tűzfal](../articles/key-vault/general/network-security.md) engedélyezve van a kulcstartón, a következő további követelmények szükségesek:

* A Key Vault eléréséhez a API Management példány **rendszer által hozzárendelt** felügyelt identitását kell használnia.
* Key Vault tűzfalon engedélyezze a **megbízható Microsoft-szolgáltatások számára a tűzfal megkerülésének engedélyezése** beállítást.

#### <a name="virtual-network-requirements"></a>Virtuális hálózati követelmények

Ha a API Management példány egy virtuális hálózaton van telepítve, konfigurálja a következő hálózati beállításokat is:

* A API Management alhálózaton Azure Key Vault engedélyezése a [szolgáltatási végpontoknak](../articles/key-vault/general/overview-vnet-service-endpoints.md) .
* Konfiguráljon egy hálózati biztonsági csoport (NSG) szabályt, hogy engedélyezze a kimenő forgalmat a AzureKeyVault és a AzureActiveDirectory [szolgáltatás címkéi](../articles/virtual-network/service-tags-overview.md)számára. 

Részletekért lásd: hálózati konfiguráció részletei a [Kapcsolódás virtuális hálózathoz](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues).