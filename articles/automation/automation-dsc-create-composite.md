---
title: Konfigurációk konvertálása összetett erőforrásokra Azure Automation állapot-konfigurációhoz
description: Ez a cikk azt ismerteti, hogyan alakíthatja át a konfigurációkat összetett erőforrásokra Azure Automation állapot-konfigurációra.
keywords: DSC, PowerShell, konfigurálás, beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5833308f03d328d988ac818d83ff5ee618a4c79c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563572"
---
# <a name="convert-configurations-to-composite-resources"></a>Konfigurációk átalakítása összetett erőforrásokká

> A következőkre vonatkozik: Windows PowerShell 5,1

A konfigurációk létrehozási lépéseinek megkezdése után gyorsan létrehozhat olyan "forgatókönyveket", amelyek a beállítások csoportjait kezelik.
Ilyenek például a következők:

- webkiszolgáló létrehozása
- DNS-kiszolgáló létrehozása
- SharePoint-kiszolgáló létrehozása
- SQL-fürt konfigurálása
- tűzfalbeállítások kezelése
- jelszavas beállítások kezelése

Ha szeretné megosztani másokkal a munkát, a legjobb megoldás a konfiguráció [összetett erőforrásként](/powershell/scripting/dsc/resources/authoringresourcecomposite)való csomagolása.
Az összetett erőforrások első alkalommal történő létrehozása rendkívül nagy lehet.

> [!NOTE]
> Ez a cikk egy olyan megoldásra utal, amelyet a nyílt forráskódú közösség tart fenn.
> A támogatás csak GitHub-együttműködés formájában érhető el, nem a Microsofttól.

## <a name="community-project-compositeresource"></a>Közösségi projekt: CompositeResource

A probléma megoldásához létrehozott egy [CompositeResource](https://github.com/microsoft/compositeresource) nevű közösségi karbantartó megoldást.

A CompositeResource automatizálja a konfigurációban egy új modul létrehozásának folyamatát.
Első lépésként a (z) (vagy Build kiszolgáló) konfigurációs parancsfájl [beszerzése](https://devblogs.microsoft.com/scripting/how-to-reuse-windows-powershell-functions-in-scripts/) a munkaállomáson, hogy be legyen töltve a memóriában.
Ezután ahelyett, hogy a konfigurációt futtatja egy MOF-fájl létrehozásához, használja a CompositeResource modul által biztosított funkciót az átalakítás automatizálásához.
A parancsmag betölti a konfiguráció tartalmát, lekéri a paraméterek listáját, és létrehoz egy új modult, amire szüksége van.

Miután létrehozta a modult, megnövelheti a verziót, és hozzáadhat kibocsátási megjegyzéseket minden alkalommal, amikor módosításokat végez, és közzéteszi azt a saját [PowerShellGet-adattárában](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Miután létrehozott egy összetett erőforrás-modult, amely tartalmazza a konfigurációt (vagy több konfigurációt), ezeket használhatja az Azure-ban a készíthető [authoring Experience](./compose-configurationwithcompositeresources.md) -ben, vagy hozzáadhatja őket a [DSC konfigurációs parancsfájljaihoz](/powershell/scripting/dsc/configurations/configurations) a MOF-fájlok LÉTREHOZÁSához és [a MOF-fájlok Azure Automationba való feltöltéséhez](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Ezután regisztrálja a kiszolgálókat akár [a helyszínen](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) , akár [Az Azure-ban](./automation-dsc-onboarding.md#enable-azure-vms) a konfigurációk lekéréséhez.
A projekt legújabb frissítése [runbookok](https://www.powershellgallery.com/packages?q=DscGallerySamples) is közzétett a Azure Automation számára, hogy automatizálja a konfigurációk importálásának folyamatát a PowerShell-galériaból.

A DSC-hez készült összetett erőforrások létrehozásának automatizálásához látogasson el a [PowerShell-galériare](https://www.powershellgallery.com/packages/compositeresource/) , és töltse le a megoldást, vagy kattintson a "Project site" (projekt helye) elemre a [dokumentáció](https://github.com/microsoft/compositeresource)megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

- A PowerShell DSC megismeréséhez tekintse meg a [Windows PowerShell a kívánt állapot konfigurációjának áttekintése](/powershell/scripting/dsc/overview/overview)című témakört.
- Ismerje meg a PowerShell DSC-erőforrásait a [DSC-erőforrásokban](/powershell/scripting/dsc/resources/resources).
- A helyi Configuration Manager konfiguráció részleteit lásd: [a helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig).
