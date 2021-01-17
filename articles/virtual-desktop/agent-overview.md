---
title: Ismerkedés a Windows rendszerű virtuális asztali ügynökkel
description: A Windows rendszerű virtuális asztali ügynök és a frissítési folyamatok áttekintése.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 6450b44e5c7281f0a24fd5000c9feec6e61fa29c
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540629"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Ismerkedés a Windows rendszerű virtuális asztali ügynökkel

A Windows rendszerű virtuális asztali szolgáltatások keretrendszere három fő összetevőből áll: a Távoli asztal-ügyfélre, a szolgáltatásra és a virtuális gépekre. Ezek a virtuális gépek az ügyfél-előfizetésben jelennek meg, ahol telepítve van a Windows rendszerű virtuális asztali ügynök és az ügynök rendszerbetöltő. Az ügynök közbenső kommunikátorként működik a szolgáltatás és a virtuális gépek között, ami lehetővé teszi a kapcsolódást. Ezért ha az ügynök telepítésével, frissítésével vagy konfigurálásával kapcsolatos problémákat tapasztal, a virtuális gépek nem fognak tudni csatlakozni a szolgáltatáshoz. Az ügynök rendszerbetöltője az ügynököt betöltő végrehajtható fájl. 

Ez a cikk az ügynök telepítési és frissítési folyamatainak rövid áttekintését nyújtja.

>[!NOTE]
>Ez a dokumentáció nem a FSLogix-ügynökhöz vagy a Távoli asztal ügyfél-ügynökhöz készült.


## <a name="initial-installation-process"></a>Kezdeti telepítési folyamat

A Windows rendszerű virtuális asztali ügynök kezdetben kétféleképpen telepíthető. Ha virtuális gépeket (VM-ket) hoz létre a Azure Portal és az Azure Marketplace-en, a rendszer automatikusan telepíti az ügynököt és az ügynök rendszerbetöltőjét. Ha a virtuális gépeket a PowerShell használatával hozza létre, akkor manuálisan kell letöltenie az ügynököt és az ügynököt betöltő. msi fájlokat, amikor [a PowerShell-lel létrehoz egy Windows rendszerű virtuális asztali címkészletet](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool). Ha az ügynök telepítve van, a Windows rendszerű virtuális asztali párhuzamos verem és a Genfi figyelő ügynök is telepítve van. Az egymás melletti stack összetevő szükséges ahhoz, hogy a felhasználók biztonságosan hozzanak létre fordított kiszolgáló – ügyfél kapcsolatokat. A Genfi figyelő ügynök figyeli az ügynök állapotát. Mindhárom összetevő elengedhetetlen a végpontok közötti felhasználói kapcsolatok megfelelő működéséhez.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali ügynök, egymás melletti verem és a Genfi figyelő ügynök sikeres telepítéséhez fel kell oldania a [szükséges URL-címek listájában](safe-url-list.md#virtual-machines)felsorolt összes URL-cím blokkolását. Az URL-címek blokkolásának feloldása szükséges a Windows virtuális asztali szolgáltatás használatához.

## <a name="agent-update-process"></a>Ügynök frissítési folyamata

A Windows rendszerű virtuális asztali szolgáltatás automatikusan frissíti az ügynököt, amikor egy frissítés elérhetővé válik. Az ügynökök frissítései tartalmazhatnak új funkciókat, vagy javíthatják a korábbi problémákat. Miután telepítette a Windows rendszerű virtuális asztali ügynök kezdeti verzióját, az ügynök rendszeresen lekérdezi a Windows Virtual Desktop szolgáltatást annak megállapítására, hogy van-e az ügynök újabb verziója, és hogy elérhetők-e az összetevők. Ha van új verziója, az ügynök betöltője automatikusan letölti az ügynök legújabb verzióját, a párhuzamos stacket és a Genfi figyelési ügynököt.

>[!NOTE]
>- Ha a Genfi figyelő ügynök a legújabb verzióra frissül, a régi GenevaTask feladat az új figyelési ügynökhöz tartozó új feladat létrehozása előtt található és le van tiltva. A figyelési ügynök korábbi verziója nem törlődik abban az esetben, ha a figyelési ügynök legújabb verziója olyan problémával rendelkezik, amely megköveteli a korábbi verzióra való visszaállást a javításhoz. Ha a legújabb verzióban probléma van, a régi figyelési ügynök ismét engedélyezve lesz a figyelési adattovábbítás folytatásához. A figyelő minden olyan verziója, amely korábban a frissítés előtt lett telepítve, a rendszer törli a virtuális gépről.
>- A virtuális gép egyszerre három verziót tart fenn egymás melletti veremben. Ez lehetővé teszi a gyors helyreállítást, ha valami probléma merül fel a frissítéssel. A verem legkorábbi verzióját a rendszer eltávolítja a virtuális gépről, valahányszor a verem frissül.

A frissítés telepítése általában 2-3 percet vesz igénybe egy új virtuális gépen, és nem okozhatja, hogy a virtuális gép kapcsolata megszakadjon vagy leáll. Ez a frissítési folyamat a Windows rendszerű virtuális asztali (klasszikus) és a Windows rendszerű virtuális asztal legújabb verziójára vonatkozik Azure Resource Manager.

## <a name="next-steps"></a>További lépések

Most, hogy jobban megértette a Windows rendszerű virtuális asztali ügynököt, íme néhány olyan erőforrás, amely segíthet:

- Tekintse meg a [Windows rendszerű virtuális asztali ügynök frissítései](whats-new.md) szakaszt, és tekintse meg az új ügynök frissítésével kapcsolatos információkat.
- Ha ügynökkel vagy kapcsolattal kapcsolatos problémákat tapasztal, tekintse meg a [Windows rendszerű virtuális asztali ügynökkel kapcsolatos hibaelhárítási útmutatót](troubleshoot-agent.md).