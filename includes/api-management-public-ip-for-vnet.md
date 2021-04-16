---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531625"
---
* **Egy standard [termékváltozatú nyilvános IPv4-cím,](../articles/virtual-network/public-ip-addresses.md#standard)** ha az ügyfél a 2021-01-01-preview vagy újabb API-verziót használja. A nyilvános IP-cím erőforrásra a virtuális hálózat külső vagy belső hozzáférésre való beállításakor van szükség. Belső virtuális hálózat esetén a nyilvános IP-cím csak felügyeleti műveletekhez használatos. További információ a következő [IP-API Management:](../articles/api-management/api-management-howto-ip-addresses.md).

  * Az IP-címnek ugyanabban a régióban és előfizetésben kell lennie, mint a API Management példánynak és a virtuális hálózatnak.

  * Az IP-cím értékét a rendszer az adott régióban található virtuális nyilvános IPv4-API Management rendeli hozzá. 

  * Ha külsőről belső virtuális hálózatra vált (vagy fordítva), módosítja a hálózat alhálózatát, vagy frissíti a rendelkezésre állási zónákat a API Management-példányhoz, másik nyilvános IP-címet kell konfigurálnia. 

  > [!IMPORTANT]
  > A Azure Portal jelenleg a 2021. 01. 01. 01. verziójú API-verziót használja egy új példány API Management frissítésekhez. Ezt az API-verziót megadhatja egy Azure Resource Manager sablonnal vagy a API Management REST API. Az Azure CLI és Azure PowerShell az API 2020-12-01-es verzióját.
