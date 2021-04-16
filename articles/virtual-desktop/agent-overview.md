---
title: A Windows Virtual Desktop-ügynök – első lépések
description: Az ügynök Windows Virtual Desktop és frissítési folyamatok áttekintése.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 529a86712994aae91a554589d383cc748f79d07f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520105"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>A Windows Virtual Desktop-ügynök – első lépések

A Windows Virtual Desktop Service framework három fő összetevőből áll: a Távoli asztal, a szolgáltatásból és a virtuális gépekből. Ezek a virtuális gépek abban az ügyfél-előfizetésben vannak, ahol a Windows Virtual Desktop ügynök és az ügynök rendszertöltője telepítve van. Az ügynök köztes kommunikálóként működik a szolgáltatás és a virtuális gépek között, ami lehetővé teszi a csatlakozást. Ezért ha problémákat tapasztal az ügynök telepítésével, frissítésével vagy konfigurálásával kapcsolatban, a virtuális gépek nem fognak tudni csatlakozni a szolgáltatáshoz. Az ügynök rendszertöltője az a végrehajtható fájl, amely betölti az ügynököt. 

Ez a cikk rövid áttekintést nyújt az ügynöktelepítési és -frissítési folyamatokról.

>[!NOTE]
>Ez a dokumentáció nem az FSLogix-ügynökhöz vagy a Távoli asztal ügynökhöz való.


## <a name="initial-installation-process"></a>Kezdeti telepítési folyamat

A Windows Virtual Desktop ügynök először kétféleképpen telepíthető. Ha virtuális gépeket (VM-eket) telepít a Azure Portal és Azure Marketplace, az ügynök és az ügynök rendszertöltője automatikusan telepítve lesz. Ha a PowerShell használatával hoz létre virtuális gépeket, manuálisan kell letöltenie az ügynököt és az ügynök rendszertöltő .msi fájljait, amikor a [PowerShell-Windows Virtual Desktop](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)gazdagépkészletet hoz létre. Az ügynök telepítése után telepíti a Windows Virtual Desktop és a Geno Monitoring-ügynököt. Az egymás mellett található verem összetevőre azért van szükség, hogy a felhasználók biztonságosan hozzanak létre fordított kiszolgálók és ügyfelek közötti kapcsolatokat. A Geneva Monitoring-ügynök figyeli az ügynök állapotát. Mindhárom összetevő elengedhetetlen a végpontok között működő felhasználói kapcsolatok megfelelő működéséhez.

>[!IMPORTANT]
>Az Windows Virtual Desktop ügynök, a egymás mellett található verem és a Geno Monitoring-ügynök sikeres telepítéséhez fel kell oldani a Szükséges URL-címek listájában felsorolt összes [URL-cím blokkolását.](safe-url-list.md#virtual-machines) Ezeknek az URL-címeknek a feloldására a Windows Virtual Desktop szüksége.

## <a name="agent-update-process"></a>Ügynök frissítési folyamata

A Windows Virtual Desktop szolgáltatás frissíti az ügynököt, amikor egy frissítés elérhetővé válik. Az ügynökfrissítések tartalmazhatnak új funkciókat vagy a korábbi problémák megoldását. Az ügynök legújabb stabil verziójának mindig telepítve kell lennie, hogy a virtuális gépek ne veszítsenek el kapcsolatot vagy biztonságot. A Windows Virtual Desktop-ügynök kezdeti verziójának telepítése után az ügynök rendszeresen lekérdezi az Windows Virtual Desktop szolgáltatást annak megállapításához, hogy elérhető-e az ügynök, a verem vagy a figyelési összetevő újabb verziója. Ha az összetevők újabb verziója már telepítve van, a frissített összetevőt automatikusan telepíti a repülési rendszer.

Az ügynök új verziói rendszeres időközönként, hetente minden Azure-előfizetésben telepítve vannak. Ezeket a frissítési időszakokat "járatoknak" nevezzük. Repülőjáratkor előfordulhat, hogy a gazdagépkészletben található virtuális gépek különböző időpontokban kapják meg az ügynök frissítését. Az összes előfizetésben található összes virtuálisgép-ügynök frissül az üzembe helyezési időszak végéig. A Windows Virtual Desktop rendszer az ügynökfrissítés stabilitásának és minőségének biztosításával növeli a szolgáltatás megbízhatóságát.


Egyéb fontos dolgok, amit érdemes szem előtt tartani:

- Mivel a gazdagépkészlet virtuális gépei különböző időpontokban kaphatják meg az ügynök frissítéseit, meg kell tudni mondania a különbséget a repülési problémák és a sikertelen ügynökfrissítések között. Ha a windowsos naplók alkalmazásában a **virtuális** gép eseménynaplóiba  >    >   Eseménynapló"3277-es azonosítójú" eseményt lát, az azt jelenti, hogy az ügynök frissítése nem működött. Ha nem látja ezt az eseményt, akkor a virtuális gép egy másik járaton van, és később frissülni fog.
- Amikor a Geneva Monitoring-ügynök a legújabb verzióra frissül, a régi GenevaTask-feladat megtalálható és le van tiltva, mielőtt új feladatot hoz létre az új monitorozási ügynökhöz. A figyelőügynök korábbi verziója nem törlődik abban az esetben, ha a figyelőügynök legújabb verziója olyan problémát okoz, amely miatt vissza kell állni a korábbi verzióra a javításhoz. Ha a legújabb verzióval probléma van, a régi monitorozási ügynök újra engedélyezve lesz a figyelési adatok további kézbesítéséhez. A figyelő összes olyan verziója törlődik a virtuális gépről, amely a frissítés előtti utolsó telepítésnél korábbi.
- A virtuális gép egyszerre három verzióban tartja meg a egymás mellett található veremeket. Ez gyors helyreállítást tesz lehetővé, ha a frissítéssel valami baj van. A verem legkorábbi verziója el lesz távolítva a virtuális gépről, amikor a verem frissül.

Az ügynök frissítése általában 2–3 percig tart egy új virtuális gépen, és nem okozhatja, hogy a virtuális gép kapcsolata megszakadjon vagy leálljon. Ez a frissítési folyamat a Windows Virtual Desktop (klasszikus) és a Windows Virtual Desktop legújabb verziójára is Azure Resource Manager.

## <a name="next-steps"></a>Következő lépések

Most, hogy már jobban érti a Windows Virtual Desktop ügynökét, íme néhány forrásanyag, amelyek segíthetnek Önnek:

- Ha ügynökkel vagy kapcsolattal kapcsolatos problémákat tapasztal, tekintse meg a Windows Virtual Desktop problémák hibaelhárítási [útmutatóját.](troubleshoot-agent.md)
