---
title: Az Azure platform integritása és biztonsága – Azure Security
description: Az Azure platform integritásának és biztonságának technikai áttekintése.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557986"
---
# <a name="platform-integrity-and-security-overview"></a>A platform integritásának és biztonságának áttekintése
Az Azure-flották több millió kiszolgálóból (gazdagépből) állnak, és naponta több ezren bővülnek. Az újraindítások, az operációs rendszer frissítése vagy a javítások napi rendszerességgel több ezer gazdagépen is részt vesznek. Mielőtt egy gazdagép csatlakozni tud a flottához, és megkezdheti az ügyfelek munkaterhelésének elfogadását, a Microsoft ellenőrzi, hogy a gazdagép biztonságos és megbízható állapotban van-e. Ez az ellenőrzés biztosítja, hogy az ellátási lánc vagy a karbantartási munkafolyamatok során nem történtek rosszindulatú vagy véletlen módosítások a rendszerindítási szekvenciális összetevőkön.

## <a name="securing-azure-hardware-and-firmware"></a>Az Azure-hardver és a belső vezérlőprogram védelme
Ez a cikksorozat azt ismerteti, hogy a Microsoft miként biztosítja a gazdagépek integritását és biztonságát az életciklusuk különböző szakaszaiban, a gyártástól a naplementeig. A cikkek címe:
 
- [A belső vezérlőprogram biztonsága](firmware.md)
- [UEFI biztonságos rendszerindítás](secure-boot.md)
- [Mért rendszerindítás és gazdagép-igazolás](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Titkosítás inaktív állapotban](encryption-atrest.md)
- [A hipervizor biztonsága](hypervisor.md)
 
## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogy a Microsoft aktívan partnere a felhőalapú hardver-ökoszisztémán belül a folyamatos [belső vezérlőprogram biztonsági tökéletesítésének](firmware.md)megtervezéséhez.

- Ismerje meg a [megosztott felelősségét a felhőben](shared-responsibility.md).