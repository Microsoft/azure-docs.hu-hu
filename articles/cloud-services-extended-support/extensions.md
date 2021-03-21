---
title: Bővítmények Cloud Serviceshoz (bővített támogatás)
description: Bővítmények Cloud Serviceshoz (bővített támogatás)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: f9029a36dc3b778e139b4553524e8e2ca6b4bbad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98757169"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Bővítmények Cloud Serviceshoz (bővített támogatás)

A bővítmények olyan kisméretű alkalmazások, amelyek telepítés utáni konfigurációt és automatizálási feladatokat biztosítanak a szerepkörökön. Például engedélyezheti a szerepkör Távoli asztal-csatlakozását a Cloud Service-ben (kiterjesztett támogatás) való üzembe helyezés során Távoli asztal bővítmény használatával.  

## <a name="remote-desktop-extension"></a>Távoli asztal bővítmény

Távoli asztal lehetővé teszi az Azure-ban futó szerepkör asztalának elérését. A távoli asztali kapcsolat segítségével az alkalmazással kapcsolatos problémák elhárításához és diagnosztizálásához használhatja a rendszert.

A fejlesztés során egy távoli asztali kapcsolat is engedélyezhető a szerepkörben a távoli asztali modulok a szolgáltatás definíciójában vagy a távoli asztal bővítménnyel. 

További információ: [a távoli asztal konfigurálása a Azure Portalból](enable-rdp.md)

## <a name="windows-azure-diagnostics-extension"></a>Windows Azure Diagnostics bővítmény

Bármely felhőalapú szolgáltatás fő teljesítménymutatóit nyomon követheti. Minden Cloud Service-szerepkör minimális adatmennyiséget gyűjt: a CPU-használatot, a hálózati használatot és a lemez kihasználtságát. Ha a Cloud Service-ben a Microsoft. Azure. Diagnostics bővítmény van alkalmazva egy szerepkörre, akkor a szerepkör további adatpontokat gyűjthet. 

Alapszintű figyeléssel a szerepkör példányaiból származó teljesítményszámláló-adatok mintavétele és összegyűjtése 3 percenként történik. Ezt az alapszintű figyelési adat nem tárolja a Storage-fiókban, és nem rendelkezik további díjszabással. 

A speciális monitorozással a további mérőszámok mintavételezése és gyűjtése 5 perc, 1 óra és 12 óra időközönként történik. Az összesített adatokat egy Storage-fiókban tárolja a rendszer, és 10 nap után törlődik. A használt Storage-fiókot a szerepkör konfigurálja; a különböző szerepkörökhöz különböző tárolási fiókokat használhat. 

További információ: [a Windows Azure Diagnostics bővítmény alkalmazása Cloud Servicesban (kiterjesztett támogatás)](enable-wad.md)


## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Services [üzembe helyezésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás).
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).
