---
title: Konfigurációk létrehozása meglévő kiszolgálókból Azure Automation állapot-konfigurációhoz
description: Ez a cikk azt ismerteti, hogyan hozhatók létre konfigurációk meglévő kiszolgálókról Azure Automation állapot-konfigurációhoz.
keywords: DSC, PowerShell, konfigurálás, beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f376fb383e50a39f0f12d45cf9b5ae47ad6fcbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86186503"
---
# <a name="create-configurations-from-existing-servers"></a>Konfigurációk létrehozása meglévő kiszolgálókból

> A következőkre vonatkozik: Windows PowerShell 5,1

A meglévő kiszolgálók konfigurációinak létrehozása kihívást jelentő feladat lehet.
Előfordulhat, hogy nem szeretné, hogy *minden* beállítás, csak azokat, amelyeket érdekel.
Még akkor is tudnia kell, hogy milyen sorrendben kell alkalmazni a beállításokat ahhoz, hogy a konfiguráció sikeres legyen.

> [!NOTE]
> Ez a cikk egy olyan megoldásra utal, amelyet a nyílt forráskódú közösség tart fenn.
> A támogatás csak GitHub-együttműködés formájában érhető el, nem a Microsofttól.

## <a name="community-project-reversedsc"></a>Közösségi projekt: ReverseDSC

A [ReverseDSC](https://github.com/microsoft/reversedsc) nevű közösségi karbantartó megoldás azért jött létre, hogy ezen a helyen működjön a SharePoint megkezdése előtt.

A megoldás a [SharePointDSC erőforrásra](https://github.com/powershell/sharepointdsc) épül, és kibővíti a meglévő SharePoint-kiszolgálók [összegyűjtési adatainak](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) összekapcsolására.
A legújabb verzió több [kibontási móddal](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) rendelkezik, amelyekkel meghatározható, hogy milyen szintű információt tartalmazzon.

A megoldás használatával a SharePointDSC konfigurációs parancsfájljaival használandó [konfigurációs adatai](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) hozhatók létre.

Az adatfájlok létrejötte után a [DSC-konfigurációs parancsfájlok](/powershell/scripting/dsc/overview/overview) használatával MOF-fájlokat hozhat létre, és [feltöltheti a MOF-fájlokat a Azure Automationba](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Ezután regisztrálja a kiszolgálókat akár [a helyszínen](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) , akár [Az Azure-ban](./automation-dsc-onboarding.md#enable-azure-vms) a konfigurációk lekéréséhez.

A ReverseDSC kipróbálásához látogasson el a [PowerShell-galériara](https://www.powershellgallery.com/packages/ReverseDSC/) , és töltse le a megoldást, vagy kattintson a "Project site" (projekt helye) elemre a [dokumentáció](https://github.com/Microsoft/sharepointDSC.reverse)megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

- A PowerShell DSC megismeréséhez tekintse meg a [Windows PowerShell a kívánt állapot konfigurációjának áttekintése](/powershell/scripting/dsc/overview/overview)című témakört.
- Ismerje meg a PowerShell DSC-erőforrásait a [DSC-erőforrásokban](/powershell/scripting/dsc/resources/resources).
- A helyi Configuration Manager konfiguráció részleteit lásd: [a helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig).
