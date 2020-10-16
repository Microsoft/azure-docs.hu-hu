---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131632"
---
| Mechanizmus | Hatókör |Korlátok | Támogatott szintű engedély |
|---|---|---|---|
| RBAC | Storage-fiókok, tárolók. <br>Erőforrás-hozzárendelések RBAC az előfizetés vagy az erőforráscsoport szintjén. | 2000 RBAC egy előfizetésben | RBAC-szerepkörök (beépített vagy egyéni) |
| ACL| Könyvtár, fájl |32 ACL-bejegyzés (gyakorlatilag 28 ACL-bejegyzés) fájlonként és könyvtárként. Hozzáférés és alapértelmezett ACL-ek mindegyike rendelkezik saját 32 ACL-bejegyzési korláttal. |ACL-engedély|
