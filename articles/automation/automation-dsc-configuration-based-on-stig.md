---
title: Az adatkészletek konfigurálása a STIG Azure Automation állapotának konfigurálásához
description: Ez a cikk azt ismerteti, hogyan konfigurálható a DoD STIG Azure Automation állapot-konfiguráció alapján.
keywords: DSC, PowerShell, konfigurálás, beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dab0cd7f7d660808b4ed7a91318baad55f80928c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87015136"
---
# <a name="configure-data-based-on-security-technical-information-guide-stig"></a>Adatok konfigurálása a biztonsági technikai információk útmutatója (STIG) alapján

> A következőkre vonatkozik: Windows PowerShell 5,1

A konfigurációs tartalom első alkalommal történő létrehozása kihívást jelenthet.
Sok esetben a cél az, hogy automatizálja a kiszolgálók konfigurációját az "alapterv" után, amely remélhetőleg egy iparági javaslathoz igazodik.

> [!NOTE]
> Ez a cikk egy olyan megoldásra utal, amelyet a nyílt forráskódú közösség tart fenn.
> A támogatás csak GitHub-együttműködés formájában érhető el, nem a Microsofttól.

## <a name="community-project-powerstig"></a>Közösségi projekt: PowerSTIG

A [PowerSTIG](https://github.com/microsoft/powerstig) nevű közösségi projekt célja, hogy a probléma megoldásához a Stig (biztonsági technikai megvalósítási útmutató) által biztosított [nyilvános információk](https://public.cyber.mil/stigs/) alapján DSC-tartalmat generáljon.

Az alapkonfigurációk kezelése bonyolultabb, mint amilyennek hangzik.
Számos szervezetnek [dokumentálnia kell a kivételeket](https://github.com/microsoft/powerstig#powerstigdata) a szabályokhoz, és az adatmennyiséget nagy mértékben kell kezelnie.
A PowerSTIG úgy kezeli a problémát, hogy [összetett erőforrásokat](https://github.com/microsoft/powerstig#powerstigdsc) biztosít a konfiguráció egyes területeinek kezeléséhez ahelyett, hogy egyetlen nagyméretű fájl összes beállítását próbálja meg kezelni.

A konfigurációk létrehozása után a [DSC-konfigurációs parancsfájlok](/powershell/scripting/dsc/configurations/configurations) segítségével létrehozhat MOF-fájlokat, és [feltöltheti a MOF-fájlokat a Azure Automationba](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Ezután regisztrálja a kiszolgálókat akár [a helyszínen](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) , akár [Az Azure-ban](./automation-dsc-onboarding.md#enable-azure-vms) a konfigurációk lekéréséhez.

A PowerSTIG kipróbálásához látogasson el a [PowerShell-galériara](https://www.powershellgallery.com) , és töltse le a megoldást, vagy kattintson a "Project site" (projekt helye) elemre a [dokumentáció](https://github.com/microsoft/powerstig)megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

- A PowerShell DSC megismeréséhez tekintse meg a [Windows PowerShell a kívánt állapot konfigurációjának áttekintése](/powershell/scripting/dsc/overview/overview)című témakört.
- Ismerje meg a PowerShell DSC-erőforrásait a [DSC-erőforrásokban](/powershell/scripting/dsc/resources/resources).
- A helyi Configuration Manager konfiguráció részleteit lásd: [a helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig).
