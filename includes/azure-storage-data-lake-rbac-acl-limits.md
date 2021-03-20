---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017676"
---
| Mechanizmus | Hatókör |Korlátok | Támogatott szintű engedély |
|---|---|---|---|
| Azure RBAC-vel | Storage-fiókok, tárolók. <br>Erőforrások Azure-beli szerepkör-hozzárendeléseinek összevetése az előfizetés vagy az erőforráscsoport szintjén. | 2000 Azure szerepkör-hozzárendelések egy előfizetésben | Azure-szerepkörök (beépített vagy egyéni) |
| ACL| Könyvtár, fájl |32 ACL-bejegyzés (gyakorlatilag 28 ACL-bejegyzés) fájlonként és könyvtárként. Hozzáférés és alapértelmezett ACL-ek mindegyike rendelkezik saját 32 ACL-bejegyzési korláttal. |ACL-engedély|
