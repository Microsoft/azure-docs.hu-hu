---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 75acbb30c2bf811b7ae72d6939b9f164554fdd32
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98860179"
---
- Csak a 2 048 bites, a 3 072 bites és a 4 096 bites [szoftverek és HSM RSA-kulcsok](../articles/key-vault/keys/about-keys.md) támogatottak, más kulcsok és méretek nem.
    - A [HSM](../articles/key-vault/keys/hsm-protected-keys.md) -kulcsokhoz **prémium** szintű Azure Key Vault szükséges.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított egyéni rendszerképekből létrehozott lemezeket ugyanazzal az ügyfél által felügyelt kulcsokkal kell titkosítani, és ugyanahhoz az előfizetéshez kell tartoznia.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított lemezekről létrehozott pillanatképeket ugyanazzal az ügyfél által felügyelt kulcsokkal kell titkosítani.
- Az ügyfél által felügyelt kulcsokhoz (Azure Key Vaultok, lemez titkosítási készletek, virtuális gépek, lemezek és Pillanatképek) kapcsolódó összes erőforrásnak ugyanabban az előfizetésben és régióban kell lennie.
- Az ügyfél által felügyelt kulcsokkal titkosított lemezek, Pillanatképek és lemezképek nem helyezhetők át másik erőforráscsoporthoz és előfizetésbe.
- Az Azure Disk Encryption használatával jelenleg vagy korábban titkosított felügyelt lemezek nem titkosíthatók az ügyfél által felügyelt kulcsokkal.
- A legfeljebb 1000 lemezes titkosítási csoportot tud létrehozni régiónként/előfizetéssel.
- Az ügyfél által felügyelt kulcsok megosztott képtárakkal történő használatáról további információt az [előzetes verzió: az ügyfél által felügyelt kulcsok használata a lemezképek titkosításához](../articles/virtual-machines/image-version-encryption.md)című témakörben talál.
