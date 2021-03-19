---
title: Beágyazott virtualizálási Azure IoT Edge Linux rendszerhez Windows rendszeren | Microsoft Docs
description: Ismerje meg, hogyan navigálhat a beágyazott virtualizálás Azure IoT Edge Linux rendszeren Windowson.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0e0021ec3564ca079f9ab02fe5ed3f0cfa5a1560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609270"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Beágyazott virtualizálási Azure IoT Edge Linux rendszerhez Windows rendszeren
A beágyazott virtualizálás két formája kompatibilis a Windows Linux rendszerhez készült Azure IoT Edgeával. A felhasználók egy helyi virtuális gépen vagy egy Azure-beli virtuális gépen telepíthetik az üzembe helyezést. Ez a cikk a felhasználóknak egyértelművé teszi, hogy melyik lehetőség a legmegfelelőbb a forgatókönyvekhez, és betekintést nyújt a konfigurációs követelményekbe.

> [!NOTE]
>
> Győződjön meg arról, hogy egy [netowrking-beállítást](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) engedélyez a beágyazott virtualizálás számára. Ha ezt elmulasztja, a rendszer EFLOW telepítési hibákat eredményez. 

## <a name="deployment-on-local-vm"></a>Üzembe helyezés helyi virtuális gépen
Ez az alapvető megközelítés minden olyan Windows rendszerű virtuális gép esetében, amely Azure IoT Edge Linux rendszerű Windows rendszeren fut. Ebben az esetben a beágyazott virtualizálás engedélyezése szükséges a telepítés megkezdése előtt. A forgatókönyv konfigurálásával kapcsolatos további információkért olvassa el a [Hyper-V futtatása a beágyazott virtualizálás virtuális gépen](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) című témakört.

Ha Windows Servert használ, győződjön meg róla, hogy [telepíti a Hyper-V szerepkört](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Üzembe helyezés Azure-beli virtuális gépeken
Ha az Azure-beli virtuális gépeken való üzembe helyezést választja, vegye figyelembe, hogy a kialakítás nem rendelkezik külső kapcsolóval. A Windows rendszerhez készült Linux Azure IoT Edge nem kompatibilis a Server SKU-t futtató Azure-beli virtuális gépekkel, kivéve, ha egy adott parancsfájlt hajt végre, amely egy alapértelmezett kapcsolót hoz létre. Az alapértelmezett kapcsolók létrehozásával kapcsolatos további tudnivalókért tekintse meg az alábbi [Windows Server szakaszt](#windows-server) . 

> [!NOTE]
>
> Minden olyan Azure-beli virtuális gépnek, amely EFLOW gazdagépnek kell lennie, a [beágyazott virtualizálás használatát támogató](../virtual-machines/acu.md) virtuális gépeknek kell lennie


## <a name="limited-use-of-external-switch"></a>A külső kapcsoló korlátozott használata
Olyan helyzetekben, amikor a virtuális gép nem tud IP-címet beolvasni egy külső kapcsolón keresztül, a központi telepítés funkció automatikusan a belső alapértelmezett kapcsolót használja a központi telepítéshez. Ez azt jelenti, hogy a Linux rendszerű virtuális gépekhez tartozó Azure IoT Edge felügyeletét csak a célként szolgáló eszközön lehet végrehajtani (például a WAC PowerShell SSH-bővítményen keresztül a Linux rendszerű virtuális gépekhez való Azure IoT Edge csatlakozás csak a localhost rendszeren lehetséges).

## <a name="windows-server"></a>Windows Server
Windows Server-felhasználók esetén vegye figyelembe, hogy a Windows rendszerhez készült Linux Azure IoT Edge nem támogatja automatikusan az alapértelmezett kapcsolót.

* A helyi virtuális gép következményei: gondoskodjon arról, hogy a EFLOW virtuális gép a külső kapcsolón keresztül szerezzen be egy IP-címet.

* Az Azure-beli virtuális gép következményei: mivel nincs külső kapcsoló az Azure virtuális gépeken, nem lehet telepíteni a EFLOW-t, mielőtt belső kapcsolót hozna létre a kiszolgálón.

Alapértelmezés szerint nincs alapértelmezett kapcsoló a kiszolgáló SKU-ban (függetlenül attól, hogy a kiszolgáló SKU egy Azure-beli virtuális gépen fut-e). Ha olyan Azure-beli virtuális gépre telepíti a rendszert, amelyen a külső kapcsoló nem használható, akkor az alapértelmezett kapcsolót manuálisan kell beállítani és konfigurálni, mielőtt a Linux rendszerhez Azure IoT Edge a Windows-telepítéshez. Az üzembe helyezési funkció magában foglalja ezt, mivel az IP-konfigurációt igényel a belső kapcsolóhoz, a NAT-konfigurációhoz, valamint a DHCP-kiszolgáló telepítéséhez és konfigurálásához. A nyilvános előzetes verzióban elérhető üzembe helyezési funkció azt jelzi, hogy ezek a beállítások nem befolyásolják a hatékony üzemelő példányok hálózati konfigurációit.

* Az alapértelmezett kapcsoló manuális beállításával kapcsolatos információkat itt találja: [beágyazott virtualizálás engedélyezése az Azure-ban Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* A DHCP-kiszolgáló e forgatókönyvhöz való beállításának dokumentációja itt található: [DHCP telepítése a Windows PowerShell használatával](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
