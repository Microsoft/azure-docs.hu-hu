---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: fd7124ad83a446d7dde39a836c337a97b12a0a0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95560575"
---
Mostantól [privát végpontokat](../articles/private-link/private-endpoint-overview.md) is használhat az adatok biztonságos biztonsági mentésére a virtuális hálózaton belüli kiszolgálókról a Recovery Services-tárolóba. A privát végpont egy IP-címet használ a tár VNET. A virtuális hálózaton belüli erőforrásai és a tároló közötti hálózati forgalom a virtuális hálózatra és a Microsoft gerinc hálózatán található privát kapcsolatra is áthalad. Ezzel kiküszöbölhető a nyilvános internetről való kitettség. Az Azure-beli virtuális gépeken futó SQL-és SAP HANA-adatbázisok biztonsági mentésére és visszaállítására privát végpontok használhatók. A MARS-ügynököt használó helyszíni kiszolgálókhoz is használható.

Az Azure virtuális gép biztonsági mentése nem igényel internetkapcsolatot, ezért nincs szükség privát végpontokra a hálózat elkülönítésének engedélyezéséhez.

További információ a Azure Backup privát végpontokról [itt](../articles/backup/private-endpoints.md)olvasható.