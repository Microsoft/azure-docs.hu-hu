---
title: Beágyazott virtualizálás Linux Azure IoT Edge windowsos virtuális gépeken | Microsoft Docs
description: Ismerje meg, hogyan navigálhat a beágyazott virtualizálás között a Linux Azure IoT Edge windowsos környezetben.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4f01362fd9342c1f508f165b34e121a11e8d07e2
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496588"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Beágyazott virtualizálás Linux Azure IoT Edge Windows rendszeren
A beágyazott virtualizálásnak két formája kompatibilis a Linux Azure IoT Edge Windows rendszeren való telepítéssel. A felhasználók dönthetnek úgy, hogy helyi virtuális gépen vagy Azure-beli virtuális gépen keresztül helyeznek üzembe. Ez a cikk egyértelművé teszi a felhasználók számára, hogy melyik lehetőség a legmegfelelőbb a forgatókönyvükhöz, és betekintést nyújt a konfigurációs követelményekbe.

> [!NOTE]
>
> Győződjön meg arról, hogy a [beágyazott virtualizáláshoz egyetlen](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) hálózati beállítást engedélyez. Ha ezt elmulasztja, az EFLOW-telepítési hibákat eredményez. 

## <a name="deployment-on-local-vm"></a>Üzembe helyezés helyi virtuális gépen
Ez az alapkonfiguráció minden olyan Windows rendszerű virtuális gép esetén, amely Linuxon Azure IoT Edge windowsos virtuális gépeket. Ebben az esetben a beágyazott virtualizálást engedélyezni kell az üzembe helyezés megkezdése előtt. A forgatókönyv konfigurálásával kapcsolatos további információkért olvassa [el a Run Hyper-V in a Virtual Machine with Nested Virtualization (Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) futtatása virtuális gépen beágyazott virtualizálással) fejezetet.

Ha Windows Servert használ, győződjön meg arról, hogy a [Hyper-V szerepkört telepíti.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)

## <a name="deployment-on-azure-vms"></a>Üzembe helyezés Azure-beli virtuális gépeken
Ha Az Azure-beli virtuális gépeken való üzembe helyezés mellett dönt, vegye figyelembe, hogy külső kapcsolót nem tervez. Azure IoT Edge Windows rendszeren futó linuxos virtuális gépek szintén nem kompatibilisek a Kiszolgáló termékváltozatát futtató Azure-beli virtuális gépeken, kivéve, ha egy adott szkript végrehajtása egy alapértelmezett kapcsolót hoz létre. Az alapértelmezett kapcsolók beállításának további információiért tekintse meg az alábbi [Windows Server szakaszt.](#windows-server) 

> [!NOTE]
>
> Az EFLOW-t kiszolgáló Azure-beli virtuális gépeknek beágyazott virtualizálást támogató virtuális [gépeknek kell lennie](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>A külső kapcsoló korlátozott használata
Ha a virtuális gép nem tud KÜLSŐ kapcsolón keresztül IP-címet beszerezni, az üzembe helyezési funkció automatikusan a belső alapértelmezett kapcsolót használja az üzembe helyezéshez. Ez azt jelenti, hogy a linuxos virtuális géphez Azure IoT Edge kezelése csak a céleszközön hajtható végre (vagyis a Linux rendszerű virtuális gép Azure IoT Edge-hez való csatlakozás a WAC PowerShell SSH-bővítményen keresztül csak localhoston lehetséges).

## <a name="windows-server"></a>Windows Server
Vegye figyelembe, hogy Windows Server-felhasználók Azure IoT Edge windowsos linuxos alkalmazások nem támogatják automatikusan az alapértelmezett kapcsolót.

* A helyi virtuális gép következményei: Győződjön meg arról, hogy az EFLOW virtuális gép a külső kapcsolón keresztül tud IP-címet beszerezni.

* Az Azure-beli virtuális gépekkel kapcsolatos következmények: Mivel az Azure-beli virtuális gépeken nincs külső kapcsoló, az EFLOW nem helyezhető üzembe, mielőtt beállít egy belső kapcsolót a kiszolgálón.

A kiszolgáló-termékváltozatok alapértelmezés szerint nincsenek alapértelmezett kapcsolók (függetlenül attól, hogy a kiszolgáló termékváltozata Azure-beli virtuális gépen fut-e). Ha olyan Azure-beli virtuális gépen van üzembe állítva, ahol a külső kapcsoló nem használható, az alapértelmezett kapcsolót manuálisan kell beállítani és konfigurálni, mielőtt Azure IoT Edge linuxos linuxos üzembe helyezéshez. Az üzembe helyezési funkció ezt lefedi, mivel a belső kapcsoló IP-konfigurációját, NAT-konfigurációt, valamint DHCP-kiszolgáló telepítését és konfigurálását igényli. A nyilvános előzetes verzióban elérhető üzembe helyezési funkció azt állítja, hogy ezek a beállítások nem rontják a hálózati konfigurációt a produktív üzemelő példányok esetén.

* Az alapértelmezett kapcsoló manuális beállításával kapcsolatos releváns információkat itt talál: Beágyazott [virtualizálás](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) engedélyezése az Azure Virtual Machines
* A DHCP-kiszolgáló e forgatókönyvhöz való beállításával kapcsolatos dokumentációt itt találhatja: DHCP üzembe helyezése [az Windows PowerShell](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
