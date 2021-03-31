---
title: A VNet-adatforrások Azure Analysis Services konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy Azure Analysis Services kiszolgálót az Azure Virtual Network (VNet) adatforrásaihoz tartozó átjáró használatára.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 912dab16cd854ae940b7bbdfe88a8da7adf1c5e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84197191"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Átjáró használata az adatforrásokhoz egy Azure-beli virtuális hálózaton (VNeten)

Ez a cikk a Azure Analysis Services **AlwaysUseGateway** Server-tulajdonságot ismerteti, ha az adatforrások [Azure-Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)vannak használatban.

## <a name="server-access-to-vnet-data-sources"></a>Kiszolgáló-hozzáférés VNet-adatforrásokhoz

Ha az adatforrások egy VNet keresztül érhetők el, a Azure Analysis Services-kiszolgálónak csatlakoznia kell ezekhez az adatforrásokhoz, mintha a helyszínen, a saját környezetében legyenek. A **AlwaysUseGateway** -kiszolgáló tulajdonság konfigurálásával megadhatja, hogy a kiszolgáló az összes adatforrást egy helyszíni [átjárón](analysis-services-gateway.md)keresztül férhessen hozzá. 

Az Azure SQL felügyelt példányának adatforrásai privát IP-címmel futnak az Azure-VNet belül. Ha a nyilvános végpont engedélyezve van a példányon, nincs szükség átjáróra. Ha a nyilvános végpont nincs engedélyezve, egy helyszíni adatátjáróra van szükség, és a AlwaysUseGateway tulajdonságot igaz értékre kell állítani.

> [!NOTE]
> Ez a tulajdonság csak akkor érvényes, ha egy helyszíni [adatátjáró](analysis-services-gateway.md) van telepítve és konfigurálva. Az átjáró lehet a VNet.

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway tulajdonság konfigurálása

1. A SSMS > Server > **Tulajdonságok**  >  **általános** beállításnál válassza a **speciális (mind) tulajdonságok megjelenítése** lehetőséget.
2. A **ASPaaS\AlwaysUseGateway** válassza az **igaz** lehetőséget.

    ![Mindig az átjáró tulajdonság használata](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Lásd még
[Csatlakozás helyszíni adatforrásokhoz](analysis-services-gateway.md)   
[Helyszíni adatátjáró telepítése és konfigurálása](analysis-services-gateway-install.md)   
[Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

