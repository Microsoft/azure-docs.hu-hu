---
title: Felügyelt HSM helyi RBAC beépített szerepkörei – Azure Key Vault | Microsoft Docs
description: A felügyelt HSM beépített szerepköreinek áttekintése, amelyek a felhasználókhoz, az egyszerű szolgáltatásokhoz, a csoportokhoz és a felügyelt identitásokhoz rendelhetők.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 01e96922d9c0c47eaf4d430e92eafcd9d0964e13
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557224"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>A felügyelt HSM helyi beépített RBAC-szerepkörei

A felügyelt HSM helyi RBAC több beépített szerepkörrel rendelkezik. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, az egyszerű szolgáltatásokhoz, a csoportokhoz és a felügyelt identitásokhoz. Ahhoz, hogy egy rendszerbiztonsági tag műveletet hajtson végre, hozzá kell rendelnie egy olyan szerepkört, amely engedélyt ad nekik a művelet végrehajtásához. Ezek a szerepkörök és műveletek csak az adatsík-műveletekre vonatkozó engedélyek kezelését teszik lehetővé. A felügyelt HSM-erőforráshoz tartozó vezérlési sík kezeléséhez [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/overview.md)kell használnia. Néhány példa a vezérlési sík műveleteire: új felügyelt HSM vagy frissítés, áthelyezés, törlés.

## <a name="built-in-roles"></a>Beépített szerepkörök

|Szerepkör neve|Leírás|ID (Azonosító)|
|---|---|---|
|Felügyelt HSM-rendszergazda| Engedélyeket biztosít a biztonsági tartományhoz, a teljes biztonsági mentéshez/visszaállításhoz és a szerepkör-kezeléshez kapcsolódó összes művelet elvégzéséhez. Nem engedélyezett a kulcskezelő műveletek végrehajtása.|a290e904-7015-4bba-90c8-60543313cdb4|
|Felügyelt HSM titkosítási igazgató|Engedélyeket biztosít az összes szerepkör-kezelés végrehajtásához, a törölt kulcsok törléséhez vagy helyreállításához, illetve a kulcsok exportálásához. Nem engedélyezett más kulcskezelő művelet végrehajtása.|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Felügyelt HSM kriptográfiai felhasználó|Engedélyeket biztosít az összes kulcskezelő művelet végrehajtásához, kivéve a törlést vagy a törölt kulcsok helyreállítását, illetve a kulcsok exportálását.|21dbd100-6940-42c2-9190-5d6cb909625b|
|Felügyelt HSM házirend rendszergazdája| Engedélyt ad a szerepkör-hozzárendelések létrehozására és törlésére|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|Felügyelt HSM titkosítási auditor|Olvasási engedélyt ad a kulcs attribútumainak olvasására (de nem használható).|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Felügyelt HSM titkosítási szolgáltatás titkosítása| Engedélyt ad a kulcsok használatára a szolgáltatás titkosításához. |33413926-3206-4cdd-b39a-83574fe37a17|
|Felügyelt HSM biztonsági mentés| Engedélyt ad egyetlen kulcs vagy teljes HSM biztonsági mentés végrehajtására.|7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>Engedélyezett műveletek
> [!NOTE]  
> - Az "X" érték azt jelzi, hogy egy szerepkör jogosult az adatok művelet végrehajtására. Üres cella azt jelzi, hogy a szerepkör nem rendelkezik az adatművelet végrehajtásához szükséges pemission.
> - Az összes adatművelet neve "Microsoft. kulcstartó/managedHsm" előtaggal rendelkezik, amely az alábbi táblázatokban van kihagyva a rövidség kedvéért.
> - Az összes szerepkör neve "felügyelt HSM" előtaggal rendelkezik, amelyet a rendszer az alábbi táblázatban hagy el a rövidség kedvéért.

|Adatművelet | Rendszergazda | Kriptográfiai igazgató | Kriptográfiai felhasználó | Házirend rendszergazdája | Titkosítási szolgáltatás titkosítása | Backup | Kriptográfiai auditor|
|---|---|---|---|---|---|---|---|
|**Biztonsági tartományok kezelése**|
/securitydomain/download/action|<center>X</center>||||||
/securitydomain/upload/action|<center>X</center>||||||
/securitydomain/upload/read|<center>X</center>||||||
/securitydomain/transferkey/read|<center>X</center>||||||
|**Kulcskezelés**|
|/keys/read/action|||<center>X</center>||<center>X</center>||<center>X</center>|
|/keys/write/action|||<center>X</center>||||
|/keys/create|||<center>X</center>||||
|/keys/delete|||<center>X</center>||||
|/keys/deletedKeys/read/action||<center>X</center>|||||
|/keys/deletedKeys/recover/action||<center>X</center>|||||
|/keys/deletedKeys/delete||<center>X</center>|||||<center>X</center>|
|/keys/backup/action|||<center>X</center>|||<center>X</center>|
|/keys/restore/action|||<center>X</center>||||
|/keys/export/action||<center>X</center>|||||
|/keys/release/action|||<center>X</center>||||
|/keys/import/action|||<center>X</center>||||
|**Kulcs titkosítási műveletei**|
|/keys/encrypt/action|||<center>X</center>||||
|/keys/decrypt/action|||<center>X</center>||||
|/keys/wrap/action|||<center>X</center>||<center>X</center>||
|/keys/unwrap/action|||<center>X</center>||<center>X</center>||
|/keys/sign/action|||<center>X</center>||||
|/keys/verify/action|||<center>X</center>||||
|**Szerepkör-kezelés**|
|/roleAssignments/read/action|<center>X</center>|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>
|/roleAssignments/write/action|<center>X</center>|<center>X</center>||<center>X</center>|||
|/roleAssignments/delete/action|<center>X</center>|<center>X</center>||<center>X</center>|||
|/roleDefinitions/read/action|<center>X</center>|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>
|/roleDefinitions/write/action|<center>X</center>|<center>X</center>||<center>X</center>|||
|/roleDefinitions/delete/action|<center>X</center>|<center>X</center>||<center>X</center>|||
|**Biztonsági mentési/visszaállítási felügyelet**|
|/backup/start/action|<center>X</center>|||||<center>X</center>|
|/backup/status/action|<center>X</center>|||||<center>X</center>|
|/restore/start/action|<center>X</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>Következő lépések

- Tekintse át az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)áttekintését.
- A [felügyelt HSM szerepkör-kezelésről](role-management.md) szóló oktatóanyag megtekintése
