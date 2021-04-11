---
author: v-amallick
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: v-amallick
ms.openlocfilehash: d20ed4d39921f8000f77f947c4372bd8b10ca642
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294115"
---
Mostantól [privát végpontokat](../articles/private-link/private-endpoint-overview.md) is használhat az adatok biztonságos biztonsági mentésére a virtuális hálózaton belüli kiszolgálókról a Recovery Services-tárolóba. A privát végpont egy IP-címet használ a tár VNET. A virtuális hálózaton belüli erőforrásai és a tároló közötti hálózati forgalom a virtuális hálózatra és a Microsoft gerinc hálózatán található privát kapcsolatra is áthalad. Ezzel kiküszöbölhető a nyilvános internetről való kitettség. Az Azure-beli virtuális gépeken futó SQL-és SAP HANA-adatbázisok biztonsági mentésére és visszaállítására privát végpontok használhatók. A MARS-ügynököt használó helyszíni kiszolgálókhoz is használható.

Az Azure virtuális gép biztonsági mentése nem igényel internetkapcsolatot, ezért nincs szükség privát végpontokra a hálózat elkülönítésének engedélyezéséhez.

További információ a Azure Backup privát végpontokról [itt](../articles/backup/private-endpoints.md)olvasható.