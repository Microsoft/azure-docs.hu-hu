---
title: Azure Security Center GYIK – a Virtual Machines szolgáltatással kapcsolatos kérdések
description: 'Gyakori kérdések a Azure Security Center-beli virtuális gépekkel kapcsolatban: olyan termék, amely segít a fenyegetések megelőzésében, észlelésében és megválaszolásában.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 32023fca15de9d3f9258bc05166c3cff4184ea15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96499849"
---
# <a name="faq---questions-about-virtual-machines"></a>GYAKORI kérdések – a Virtual Machines szolgáltatással kapcsolatos kérdések


## <a name="what-types-of-virtual-machines-are-supported"></a>Milyen típusú virtuális gépek támogatottak?

A [klasszikus és a Resource Manager-alapú üzemi modellekkel](../azure-resource-manager/management/deployment-models.md)létrehozott virtuális gépek (VM-EK) figyelése és javaslatai elérhetők.

A támogatott platformok listáját a [Azure Security Center támogatott platformok](security-center-os-coverage.md) részben tekintheti meg.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Miért nem Azure Security Center ismeri fel az Azure-beli virtuális gépen futó antimalware-megoldást?

A Azure Security Center az Azure-bővítményeken keresztül telepített antimalware-t is betekintést biztosít. A Security Center például nem képes észlelni a megadott rendszerképre előre telepített kártevő szoftvereket, vagy ha a saját folyamatai (például a konfigurációs felügyeleti rendszerek) használatával telepítette az antimalware-t a virtuális gépekre.


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Miért kapok "hiányzó vizsgálati adataim" üzenetet a virtuális géphez?

Ez az üzenet jelenik meg, ha egy virtuális gép vizsgálati adatai hiányoznak. Miután az Azure Security Centerben engedélyezte az adatgyűjtést, némi időt igénybe vehet (de kevesebb mint egy órát), amíg az adatok érkezni kezdenek. A vizsgálati adatok kezdeti feltöltését követően akkor jelenhet meg ez az üzenetet, ha egyáltalán nincsenek vizsgálati adatok, vagy ha az utóbbi időben nem érkeztek. A leállított állapotú virtuális gépekről nem érkeznek vizsgálati adatok. Ez az üzenet akkor is megjelenhet, ha a vizsgálati adatok nem lettek a közelmúltban kitöltve (a Windows-ügynök adatmegőrzési szabályzatának megfelelően, amelynek alapértelmezett értéke 30 nap).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Milyen gyakran Security Center az operációs rendszer biztonsági réseit, a rendszerfrissítéseket és az Endpoint Protection szolgáltatással kapcsolatos problémákat?

Az alábbiakban láthatók a biztonsági rések, frissítések és problémák Security Center vizsgálatainak késési ideje:

- Operációs rendszer biztonsági beállításai – az Adatfrissítés 48 órán belül megtörténik
- Rendszerfrissítések – a rendszer 24 órán belül frissíti az adatfrissítést
- Endpoint Protection problémák – az Adatfrissítés 8 órán belül frissül

Security Center általában óránként vizsgálja az új adatforrásokat, és ennek megfelelően frissíti az ajánlásokat. 

> [!NOTE]
> Security Center a Log Analytics ügynök használatával gyűjti és tárolja az adatokat. További információ: [Azure Security Center platform migrálása](./security-center-enable-data-collection.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Miért jelenik meg a "VM-ügynök hiányzik?" üzenet?

A virtuálisgép-ügynöknek telepítve kell lennie a virtuális gépeken az adatgyűjtés engedélyezéséhez. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök más virtuális gépekre való telepítésével kapcsolatos információkért tekintse meg a [VM-ügynök és-bővítmények](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)című blogbejegyzést.