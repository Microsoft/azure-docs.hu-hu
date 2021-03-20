---
title: Az Azure Automation állapot konfigurációjának konfigurálása a skálán
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az adatmennyiséget Azure Automation állapot konfigurációjában.
keywords: DSC, PowerShell, konfigurálás, beállítás
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc55a4c5ab20cac041a00a0f924b207eb256ae8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86186520"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Az Azure Automation állapot konfigurációjának konfigurálása a skálán

> A következőkre vonatkozik: Windows PowerShell 5,1

Több száz vagy több ezer kiszolgáló kezelése kihívást jelenthet.
Az ügyfelek visszajelzéseket adtak arról, hogy a legnehezebb szempont a [konfigurációs információk](/powershell/scripting/dsc/configurations/configdata)tényleges kezelése.
Információk rendszerezése logikai szerkezetek között, például hely, típus és környezet.

> [!NOTE]
> Ez a cikk egy olyan megoldásra utal, amelyet a nyílt forráskódú közösség tart fenn.
> A támogatás csak GitHub-együttműködés formájában érhető el, nem a Microsofttól.

## <a name="community-project-datum"></a>Közösségi projekt: Datum

A probléma megoldásához egy [Datum](https://github.com/gaelcolas/Datum) nevű közösségi karbantartó megoldás lett létrehozva.
Az Datum nagyszerű ötleteket kínál más konfigurációs felügyeleti platformoktól, és ugyanazt a megoldást alkalmazza a PowerShell DSC-hez.
Az adatokat logikai ötletek alapján [rendezi a rendszer szöveges fájlokba](https://github.com/gaelcolas/Datum#3-intended-usage) .
Ilyenek például a következők:

- Globálisan alkalmazandó beállítások
- A hely összes kiszolgálójára érvényes beállítások
- Az összes adatbázis-kiszolgálóra alkalmazandó beállítások
- Egyedi kiszolgálóbeállítások

Ezeket az információkat a kívánt fájlformátumban (JSON, YAML vagy PSD1) rendezi a rendszer.
Ezután a rendszer parancsmagokat biztosít a konfigurációs adatfájlok létrehozásához azáltal, hogy az egyes fájlokból származó [információkat összevonja](https://github.com/gaelcolas/Datum#datum-tree) egy kiszolgáló vagy kiszolgálói szerepkör egyetlen nézetében.

Az adatfájlok létrejötte után a [DSC-konfigurációs parancsfájlok](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) használatával MOF-fájlokat hozhat létre, és [feltöltheti a MOF-fájlokat a Azure Automationba](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Ezután regisztrálja a kiszolgálókat akár [a helyszínen](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) , akár [Az Azure-ban](./automation-dsc-onboarding.md#enable-azure-vms) a konfigurációk lekéréséhez.

Az Datum kipróbálásához látogasson el a [PowerShell-galériara](https://www.powershellgallery.com/packages/datum/) , és töltse le a megoldást, vagy kattintson a "Project site" (projekt helye) elemre a [dokumentáció](https://github.com/gaelcolas/Datum#2-getting-started--concepts)megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

- A PowerShell DSC megismeréséhez tekintse meg a [Windows PowerShell a kívánt állapot konfigurációjának áttekintése](/powershell/scripting/dsc/overview/overview)című témakört.
- Ismerje meg a PowerShell DSC-erőforrásait a [DSC-erőforrásokban](/powershell/scripting/dsc/resources/resources).
- A helyi Configuration Manager konfiguráció részleteit lásd: [a helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaconfig).
