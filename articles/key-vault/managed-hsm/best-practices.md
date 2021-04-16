---
title: A Managed HSM Azure Key Vault eljárások
description: Ez a dokumentum a Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 9ef3b19e5064c8a88bf80eebf57539be72747fe4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482517"
---
# <a name="best-practices-when-using-managed-hsm"></a>Ajánlott eljárások a Managed HSM használatával kapcsolatban

## <a name="control-access-to-your-managed-hsm"></a>A felügyelt HSM-hez való hozzáférés szabályozása

A Managed HSM egy olyan felhőszolgáltatás, amely védi a titkosítási kulcsokat. Mivel ezek a kulcsok bizalmasak és üzleti szempontból kritikus fontosságúak, ügyeljen arra, hogy biztonságossá tegye a felügyelt HSM-hez való hozzáférést úgy, hogy csak a jogosult alkalmazások és felhasználók számára engedélyezi a hozzáférést. Ez [a cikk](access-control.md) áttekintést nyújt a hozzáférési modellről. Ismerteti a hitelesítést és engedélyezést, valamint a szerepköralapú hozzáférés-vezérlést.
- Hozzon [létre Azure Active Directory biztonsági](../../active-directory/fundamentals/active-directory-manage-groups.md) csoportot a HSM-rendszergazdák számára (ahelyett, hogy rendszergazdai szerepkört rendelné egyénekhez). Ez megakadályozza a "felügyelet zárolását" az egyes fiókok törlése esetén.
- A felügyeleti csoportokhoz, előfizetésekhez, erőforráscsoportokhoz és felügyelt HSM-hez való hozzáférés zárolása – Az Azure RBAC használatával szabályozhatja a felügyeleti csoportokhoz, előfizetésekhez és erőforráscsoportokhoz való hozzáférést
- Hozzon létre kulcsonkénti szerepkör-hozzárendeléseket [a Managed HSM helyi RBAC használatával.](access-control.md#data-plane-and-managed-hsm-local-rbac)
- A feladatok elkülönítésének fenntartása érdekében ne rendeljen több szerepkört ugyanazokhoz a rendszerbiztonsági csoportokhoz. 
- A szerepkörök hozzárendelése a legkisebb jogosultsági szintű jogosultsággal rendelkező rendszerbiztonsági tag használatával.
- Hozzon létre egyéni szerepkör-definíciót pontos engedélyekkel.

## <a name="choose-regions-that-support-availability-zones"></a>Rendelkezésre állási zónákat támogató régiók kiválasztása

- A legjobb magas rendelkezésre állás és zónatűrés biztosítása [](../../availability-zones/az-overview.md) érdekében válassza ki az Azure-régiókat, Availability Zones támogatottak. Ezek a régiók "Ajánlott régiók"ként jelennek meg a Azure Portal.

## <a name="backup"></a>Backup

- Győződjön meg arról, hogy rendszeres biztonsági másolatokat készít a HSM-ről. A biztonsági mentések a HSM szintjén és adott kulcsoknál is készítve vannak. 

## <a name="turn-on-logging"></a>Naplózás bekapcsolása

- [Kapcsolja be a](logging.md) naplózást a HSM-hez. Riasztásokat is beállít.

## <a name="turn-on-recovery-options"></a>Helyreállítási beállítások bekapcsolás

- [A Soft Delete](../general/soft-delete-overview.md) alapértelmezés szerint be van kapcsolva.
- Kapcsolja be a végleges törlés elleni védelmet, ha a HSM kényszerített törlését még akkor is meg szeretné őrizni, ha a soft delete be van kapcsolva.

## <a name="next-steps"></a>Következő lépések

- A teljes HSM biztonsági [mentéssel/visszaállítással](backup-restore.md) kapcsolatos információkért lásd: Teljes biztonsági mentés/visszaállítás.
- A [managed HSM-naplózással kapcsolatos](logging.md) további Azure Monitor a naplózás konfiguráláskor
- A kulcskezeléshez lásd: [Felügyelt HSM-kulcsok](key-management.md) kezelése.
- Lásd: Managed HSM role management for managing role assignments (Felügyelt [HSM szerepkörkezelés](role-management.md) a szerepkör-hozzárendelések kezeléséhez).
