---
title: Ajánlott eljárások Azure Key Vault felügyelt HSM használatával
description: Ez a dokumentum a Key Vault használatának ajánlott eljárásait ismerteti.
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90995360"
---
# <a name="best-practices-when-using-managed-hsm"></a>Ajánlott eljárások a felügyelt HSM használatakor

## <a name="control-access-to-your-managed-hsm"></a>A felügyelt HSM hozzáférésének szabályozása

A felügyelt HSM egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsoknak. Mivel ezek a kulcsok bizalmasak és üzleti szempontból kritikus fontosságúak, ügyeljen arra, hogy biztonságos hozzáférést biztosítson a felügyelt HSM úgy, hogy csak a jogosult alkalmazásokat és felhasználókat engedélyezi. Ez a [cikk](access-control.md) áttekintést nyújt a hozzáférési modellről. A hitelesítéssel és engedélyezéssel, valamint a szerepköralapú hozzáférés-vezérléssel foglalkozik.
- Hozzon létre egy [Azure Active Directory biztonsági csoportot](../../active-directory/fundamentals/active-directory-manage-groups.md) a HSM-rendszergazdák számára (a rendszergazdai szerepkör egyéni felhasználókhoz rendelése helyett). Ez megakadályozza az "adminisztrációs zárolást", ha az egyes fiókok törlése történik.
- A felügyeleti csoportok, előfizetések, erőforráscsoportok és felügyelt HSM hozzáférésének zárolása az Azure RBAC segítségével szabályozhatja a felügyeleti csoportok, előfizetések és erőforráscsoportok hozzáférését.
- Kulcsos szerepkör-hozzárendelések létrehozása [felügyelt HSM helyi RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac) használatával
- A minimális jogosultság-hozzáférési tag használata szerepkörök hozzárendeléséhez

## <a name="choose-regions-that-support-availability-zones"></a>Válassza ki a rendelkezésre állási zónákat támogató régiókat

- A legjobb magas rendelkezésre állás és a zóna rugalmasságának biztosítása érdekében válassza ki az Azure-régiókat, ahol a [Availability Zones](../../availability-zones/az-overview.md) támogatottak. Ezek a régiók a Azure Portal javasolt régióiként jelennek meg.

## <a name="backup"></a>Backup

- Győződjön meg arról, hogy rendszeresen készít biztonsági mentést a HSM-ről. A biztonsági mentések a HSM szintjén és a megadott kulcsok esetében is megoldhatók. 

## <a name="turn-on-logging"></a>Naplózás bekapcsolása

- [Kapcsolja be a naplózást](logging.md) a HSM-hez. Riasztásokat is beállíthat.

## <a name="turn-on-recovery-options"></a>Helyreállítási beállítások bekapcsolása

- Alapértelmezés szerint a [Soft delete](../general/soft-delete-overview.md) be van kapcsolva.
- Ha azt szeretné, hogy a rendszer csak a Soft delete bekapcsolását követően engedélyezze a védelem kitisztítását,

## <a name="next-steps"></a>Következő lépések

- A teljes biztonsági mentéssel [és](backup-restore.md) visszaállítással kapcsolatos információkért tekintse meg a HSM biztonsági mentéséről és visszaállításáról szóló információkat.
- Lásd: [felügyelt HSM naplózása](logging.md) , amelyből megtudhatja, hogyan konfigurálhatja a naplózást a Azure monitor használatával
- Lásd: [felügyelt HSM-kulcsok kezelése](key-management.md) a kulcskezelő szolgáltatáshoz.
- A szerepkör-hozzárendelések kezeléséhez lásd: [felügyelt HSM szerepkör-kezelés](role-management.md) .
