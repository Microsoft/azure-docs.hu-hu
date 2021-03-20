---
title: Belső vezérlőprogram biztonsága – Azure Security
description: Ismerje meg, hogyan védi a Microsoft az Azure-hardvereket és a belső vezérlőprogramot.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557962"
---
# <a name="firmware-security"></a>A belső vezérlőprogram biztonsága
Ez a cikk azt ismerteti, hogyan védi a Microsoft a felhő hardveres ökoszisztémáját és ellátási láncait.

## <a name="securing-the-cloud-hardware-ecosystem"></a>A felhőalapú hardverek ökoszisztémájának védelme
A Microsoft aktívan partnerei a felhő hardveres ökoszisztémáján belül a folyamatos biztonsági tökéletesítések elvégzéséhez:

- Együttműködik az Azure hardveres és belső vezérlőprogram-partnereivel (például a Component Manufacturers és a rendszerintegrátorokkal) az Azure hardveres és belső vezérlőprogram biztonsági követelményeinek kielégítése érdekében.

- A partnerek a termékeik biztonsági helyzetének folyamatos értékelését és javítását a Microsoft által meghatározott követelmények alapján, például a következő területeken:

  - Belső vezérlőprogram biztonságos indítása
  - Biztonságos belső vezérlőprogram helyreállítása
  - Belső vezérlőprogram biztonságos frissítése
  - Belső vezérlőprogram titkosítása
  - Zárolt hardver
  - Részletes hibakeresési telemetria
  - Rendszer-támogatás a TPM 2,0 hardverhez a mért rendszerindítás engedélyezéséhez

- A [nyílt számítási projekt (OCP)](https://www.opencompute.org/wiki/Security) biztonsági projekt bevonása és a hozzájuk való közreműködés a specifikációk fejlesztésével. A specifikációk az ökoszisztéma biztonságos kialakításának és architektúrájának egységességét és érthetőségét támogatják.

   > [!NOTE]
   > A OCP biztonsági projekthez való hozzájárulás példája a [hardveres biztonságos rendszerindítás](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0) specifikációja.

## <a name="securing-hardware-and-firmware-supply-chains"></a>Hardver-és belső vezérlőprogram-ellátási láncok biztonságossá tétele
Az Azure-hoz készült felhőalapú hardveres szállítók és szállítók a Microsoft által fejlesztett ellátási lánc biztonsági folyamatainak és követelményeinek is eleget kell tenniük. A hardver-és belső vezérlőprogram fejlesztési és üzembe helyezési folyamatainak a Microsoft [biztonsági fejlesztési életciklus](https://www.microsoft.com/securityengineering/sdl) (SDL) folyamatainak követéséhez szükségesek, például:

- Fenyegetésmodellezés
- Biztonságos tervezési felülvizsgálatok
- A belső vezérlőprogram felülvizsgálata és a behatolás tesztelése
- Biztonságos létrehozási és tesztelési környezetek
- Biztonsági sebezhetőségek kezelése és incidensekre adott válasz

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a platform integritásának és biztonságának elvégzéséről, olvassa el a következőt:

- [Biztonságos rendszerindítás](secure-boot.md)
- [Mért rendszerindítás és gazdagép-igazolás](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Titkosítás inaktív állapotban](encryption-atrest.md)
- [A hipervizor biztonsága](hypervisor.md)