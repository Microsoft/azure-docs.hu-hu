---
title: Azure Firewall távoli munkahelyi támogatás
description: Ez a cikk bemutatja, hogyan támogathatja a Azure Firewall a távoli munkahelyi kényszerített követelményeket.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 3c0e2033ee559af38a6816bdfa611eea86b14dea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94658315"
---
# <a name="azure-firewall-remote-work-support"></a>Azure Firewall távoli munkahelyi támogatás

A Azure Firewall felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely az Azure-beli virtuális hálózati erőforrásokat védi. Egy teljes mértékben állapotalapú tűzfalszolgáltatás, beépített magas rendelkezésre állással és korlátlan felhőméretezhetőséggel.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>A virtuális asztali infrastruktúra (VDI) üzembe helyezésének támogatása

A otthoni házirendekkel kapcsolatos munkához számos IT-szervezet szükséges a kapacitás, a hálózat, a biztonság és az irányítás alapvető változásainak kezeléséhez. Az alkalmazottakat nem védi a helyszíni szolgáltatásokhoz társított rétegzett biztonsági házirendek, miközben otthon működik. Az Azure-ban a virtuális asztali infrastruktúra (VDI) üzembe helyezésével gyorsan reagálhat a szervezetek erre a változó környezetre. Azonban szükség van arra, hogy megvédje a bejövő/kimenő internet-hozzáférést ezekhez a VDI-telepítésekhez. A VDI-környezetek elleni védelem érdekében Azure Firewall [DNAT-szabályokat](rule-processing.md) és a [fenyegetésekkel kapcsolatos intelligencián](threat-intel.md) alapuló szűrési képességeket is használhatja.

## <a name="azure-windows-virtual-desktop-support"></a>Azure Windows rendszerű virtuális asztali ügyfélszolgálat

A Windows Virtual Desktop egy átfogó asztali és app Virtualization szolgáltatás, amely az Azure-ban fut. Ez az egyetlen olyan virtuális asztali infrastruktúra (VDI), amely egyszerűsített felügyeletet, többmunkamenetes Windows 10 rendszert, Microsoft 365 alkalmazások nagyvállalati verzióinak optimalizálását és a Távoli asztali szolgáltatások (RDS) környezetek támogatását biztosítja. Percek alatt üzembe helyezheti és méretezheti a Windows rendszerű asztali számítógépeit és alkalmazásait, és beépített biztonsági és megfelelőségi funkciókat érhet el. A Windows virtuális asztal nem igényli a virtuális hálózathoz való bejövő hozzáférés megnyitását. Azonban engedélyeznie kell a kimenő hálózati kapcsolatok készletét a virtuális hálózatban futó Windows virtuális asztali virtuális gépekhez. További információ: [a Azure Firewall használata a Windows rendszerű virtuális asztali környezetek elleni védelemhez](protect-windows-virtual-desktop.md).

## <a name="next-steps"></a>Következő lépések

További információ a [Windows rendszerű virtuális asztali környezetről](../virtual-desktop/index.yml).