---
title: Az Azure Device kiépítési szolgáltatás adattitkosítása az ügyfelek által felügyelt kulcsok használatával | Microsoft Docs
description: Az ügyfél által felügyelt kulcsok használata a Device kiépítési szolgáltatáshoz – az inaktív adatok titkosítása
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: d22a01bab81fc330484e7715a65c89a1cfd7802c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967176"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Az ügyfél által felügyelt kulcsok használata a Device kiépítési szolgáltatáshoz – az inaktív adatok titkosítása

## <a name="overview"></a>Áttekintés

A Device kiépítési szolgáltatás (DPS) támogatja az inaktív adatok titkosítását az ügyfél által felügyelt kulcsokkal (CMK), más néven a saját kulcs használata (BYOK). A DPS inaktív és átvitel alatt álló adatok titkosítását teszi lehetővé az adatközpontokban, és visszafejti azt az Ön számára, ahogy Ön is hozzáfér. Alapértelmezés szerint a DPS a Microsoft által felügyelt kulcsokat használja az adatok titkosításához a nyugalmi állapotban. A CMK segítségével további titkosítási réteget kaphat az alapértelmezett platform titkosításhoz, ha úgy dönt, hogy az inaktív adatok titkosítása a [Azure Key Vaulton](https://azure.microsoft.com/services/key-vault/)keresztül felügyelt kulcs-titkosítási kulccsal történik. Ez rugalmasságot biztosít a kulcsok létrehozására, elforgatására, letiltására és visszavonására. Ha a CMK konfigurálva van a DPS-hez, az azt jelenti, hogy a kettős titkosítás engedélyezve van, és két védelmi réteggel rendelkezik, amelyek aktívan védik az adataikat. 

Ehhez a funkcióhoz új DPS létrehozására van szükség. Ha szeretné kipróbálni ezt a funkciót, lépjen kapcsolatba velünk a [Microsoft támogatási szolgálatán](https://azure.microsoft.com/support/create-ticket/)keresztül. Ossza meg vállalata nevét és előfizetési AZONOSÍTÓját, amikor kapcsolatba lép a Microsoft ügyfélszolgálatával.


## <a name="next-steps"></a>Következő lépések

* [További információ az eszköz kiépítési szolgáltatásáról](./index.yml)

* [További információ a Azure Key Vault](../key-vault/general/overview.md)