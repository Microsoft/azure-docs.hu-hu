---
title: Azure Key Vault tároló másik erőforráscsoportba való | Microsoft Docs
description: Útmutatás a kulcstartó másik erőforráscsoportba való áthelyezéshez.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 14ecbcaa35153438601a3dabb70f5b38006ded1b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749563"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Egy Azure-kulcstartó áthelyezése erőforráscsoportok között

## <a name="overview"></a>Áttekintés

A kulcstartók erőforráscsoportok közötti mozgatása a key vault támogatott funkciója. A kulcstartók erőforráscsoportok közötti mozgatása nincs hatással a Key Vault tűzfal- vagy hozzáférési szabályzatkonfigurációira. A csatlakoztatott alkalmazásoknak és szolgáltatásnévnek továbbra is a kívántnak megfelelően kell működnie.

> [!IMPORTANT]
> **A lemeztitkosításhoz használt kulcstartók nem áthelyezhetőek.**
> Ha a kulcstartót lemeztitkosítással használja egy virtuális géphez, a kulcstartó nem áthelyezhető másik erőforráscsoportba vagy előfizetésbe, amíg a lemeztitkosítás engedélyezve van. A kulcstartó új erőforráscsoportba vagy előfizetésbe való áthelyezéséhez le kell tiltania a lemeztitkosítást. 

## <a name="design-considerations"></a>Kialakítási szempontok

Előfordulhat, hogy a szervezet kényszerítési Azure Policy vagy kizárásokkal valósított meg az erőforráscsoport szintjén. Előfordulhat, hogy más szabályzat-hozzárendelések vannak abban az erőforráscsoportban, ahol a kulcstartó jelenleg létezik, valamint abban az erőforráscsoportban, amelyben a kulcstartót át kell költöztetni. A szabályzatkövetelmények ütközése megszakítja az alkalmazásokat.

### <a name="example"></a>Példa

Van egy key vaulthoz csatlakoztatott alkalmazása, amely két évig érvényes tanúsítványokat hoz létre. Az erőforráscsoport, amelyben a kulcstartót próbálja áthelyezni, olyan szabályzat-hozzárendelést tartalmaz, amely blokkolja az egy évnél hosszabb ideig érvényes tanúsítványok létrehozását. Miután a kulcstartót az új erőforráscsoportba költözteti, egy Azure Policy-hozzárendelés blokkolni fogja a két évre érvényes tanúsítvány létrehozására vonatkozó műveletet.

### <a name="solution"></a>Megoldás

Mindenképpen a Azure Portal Azure Policy oldalára lépve nézze meg az aktuális erőforráscsoport szabályzat-hozzárendeléseit, valamint azt az erőforráscsoportot, amelybe át fog költözni, és győződjön meg arról, hogy nincsenek eltérései.

## <a name="procedure"></a>Eljárás

1. Bejelentkezés az Azure Portalra
2. Lépjen a kulcstartóhoz
3. Kattintson az "Áttekintés" lapra
4. Az "Áthelyezés" gomb kiválasztása
5. A legördülő menüben válassza az "Áthelyezés másik erőforráscsoportba" lehetőséget
6. Válassza ki azt az erőforráscsoportot, ahová a kulcstartót át szeretné áthelyezni
7. Az erőforrások áthelyezésével kapcsolatos figyelmeztetés nyugtázása
8. Válassza az OK gobot.

Key Vault kiértékeli az erőforrás-áthelyezés érvényességét, és riasztást küld az esetleges hibákról. Ha nem található hiba, az erőforrás áthelyezése befejeződik. 
