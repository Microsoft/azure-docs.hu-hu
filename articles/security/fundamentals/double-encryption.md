---
title: Dupla titkosítás Microsoft Azure
description: Ez a cikk azt ismerteti, hogy a Microsoft Azure hogyan nyújt kettős titkosítást a REST-adatok és az adatok átvitele közben.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88227152"
---
# <a name="double-encryption"></a>Dupla titkosítás
A kettős titkosítás olyankor van, ahol két vagy több független titkosítási réteg van engedélyezve, hogy az egyik titkosítási réteg sérülése elleni védelmet nyújtson. Két titkosítási réteg használata csökkenti az adattitkosítással kapcsolatos fenyegetéseket. Például:

- Konfigurációs hibák az adattitkosításban
- Megvalósítási hibák a titkosítási algoritmusban
- Egyetlen titkosítási kulcs biztonsága

Az Azure kettős titkosítást biztosít a REST-és adatátviteli adatokhoz.

## <a name="data-at-rest"></a>Inaktív adatok
A Microsoft megközelítése, hogy a következő két titkosítási réteget engedélyezzük a REST-adatokhoz:

- **A lemez titkosítása az ügyfél által felügyelt kulcsokkal**. A lemezes titkosításhoz saját kulcsot kell megadnia. Saját kulcsokat hozhat a Key Vault (BYOK – Bring Your Own Key), vagy létrehozhat új kulcsokat a Azure Key Vault a kívánt erőforrások titkosításához.
- **Infrastruktúra-titkosítás a platform által felügyelt kulcsokkal**.  Alapértelmezés szerint a rendszer a platform által felügyelt titkosítási kulcsok használatával automatikusan titkosítja a lemezeket.

## <a name="data-in-transit"></a>Átvitt adatok
A Microsoft megközelítése, hogy a következő két titkosítási réteget engedélyezze a továbbítási adatforgalomban:

- **Adatátviteli titkosítás Transport Layer Security (TLS) 1,2 használatával az adatvédelemhez, amikor a Cloud Services és Ön között utazik**. Az adatközpontot elhagyó összes forgalom átvitel alatt áll, még akkor is, ha a forgalmi cél egy másik tartományvezérlő ugyanabban a régióban. A TLS 1,2 a használt alapértelmezett biztonsági protokoll. A TLS erős hitelesítést, az üzenetek védelmét és integritását (az üzenetek illetéktelen módosításának, elfogásának és hamisításának észlelését), az együttműködési képességet, az algoritmus rugalmasságát, valamint a könnyű üzembe helyezést és használatot biztosít.
- **További titkosítási réteg van megadva az infrastruktúra rétegben**. Az IEEE 802.1 AE MAC biztonsági szabványokat (más néven MACsec) használó adatkapcsolati rétegbeli titkosítási módszer a mögöttes hálózati hardveren pont – pont között van alkalmazva. Amikor az Azure-ügyfelek forgalmát átveszi az adatközpontok között – a Microsoft által nem szabályozott fizikai határokon kívül (vagy a Microsoft nevében) – a csomagokat a rendszer a küldés előtt titkosítja és visszafejti az eszközökön, megelőzve a fizikai "személy-középre" vagy a figyelési/wiretapping támadásokat. Mivel ez a technológia önmagában is a hálózati hardverhez van integrálva, a hálózati hardverek esetében a sebesség nélküli titkosítás biztosítja a kapcsolati késleltetés növelését. Ez a MACsec-titkosítás alapértelmezés szerint a régión vagy régión belüli összes Azure-forgalomra vonatkozik, és az ügyfelek részéről nincs szükség beavatkozásra.

## <a name="next-steps"></a>Következő lépések
Ismerje meg [, hogyan használják a titkosítást az Azure-ban](encryption-overview.md).
