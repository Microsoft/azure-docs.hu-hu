---
title: Karbantartás és frissítések
description: Az Azure-ban futó virtuális gépek karbantartásának és frissítéseinek áttekintése.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: shants
ms.openlocfilehash: 51df72e31acaadc83f4c094b99fa938377e5f023
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500002"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Az Azure-ban futó virtuális gépek karbantartása

Az Azure rendszeresen frissíti a platformját a virtuális gépek gazdagép-infrastruktúrájának megbízhatóságának, teljesítményének és biztonságának javítása érdekében. E frissítések célja a szoftverösszetevők üzemeltetési környezetben történő javításától a hálózati összetevők frissítéséig, illetve a hardverleszerelésig terjed. 

A frissítések ritkán érintik az üzemeltetett virtuális gépeket. Ha a frissítések hatással vannak, az Azure kiválasztja a legkevésbé hatásos módszert a frissítésekhez:

- Ha a frissítés nem igényel újraindítást, a rendszer szünetelteti a virtuális gépet a gazdagép frissítésekor, vagy a virtuális gép egy már frissített gazdagépre van áttelepítve. 
- Ha a karbantartás újraindítást igényel, értesítést kap a tervezett karbantartásról. Az Azure egy időablakot is biztosít, amelyben saját maga is elindíthatja a karbantartást. Az önkarbantartási időszak általában 35 nap, kivéve, ha a karbantartás nem sürgős. Az Azure olyan technológiákat fektet be, amelyek csökkentik azon esetek számát, amelyekben a tervezett platform karbantartásához szükség van a virtuális gépek újraindítására. A tervezett karbantartás kezelésével kapcsolatos útmutatásért lásd: tervezett karbantartási értesítések kezelése az Azure [CLI](maintenance-notifications-cli.md), a [PowerShell](maintenance-notifications-powershell.md) vagy a [Portal](maintenance-notifications-portal.md)használatával.

Ez a lap leírja, hogy az Azure hogyan hajtja végre mindkét típusú karbantartást. További információ a nem tervezett eseményekről (kimaradások): [a Windows rendszerű virtuális gépek rendelkezésre állásának kezelése](./availability.md) , illetve a megfelelő [Linux](./availability.md)-cikk.

A virtuális gépeken belül a Windows vagy [Linux](./linux/scheduled-events.md)rendszerhez [készült Scheduled Events használatával](./windows/scheduled-events.md) értesítéseket kaphat a közelgő karbantartásról.



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Újraindítást nem igénylő karbantartás

A legtöbb platform frissítései nem érintik az ügyfél virtuális gépeket. Ha egy nem befolyásoló frissítés nem lehetséges, az Azure kiválasztja a legkevésbé érintett frissítési mechanizmust az ügyfél virtuális gépei számára. 

A legtöbb nem nulla hatású karbantartás kevesebb, mint 10 másodpercig szünetelteti a virtuális gépet. Bizonyos esetekben az Azure memóriát használ, és megőrzi a karbantartási mechanizmusokat. Ezek a mechanizmusok akár 30 másodpercig szüneteltetik a virtuális gépet, és megőrzik a memóriát a memóriában. A virtuális gép ezután folytatódik, és az órája automatikusan szinkronizálva lesz. 

Memória – a karbantartási munkálatok az Azure-beli virtuális gépek több mint 90 százalékára is használhatók. A G, M, N és H sorozat esetében nem működik. Az Azure egyre inkább élő áttelepítési technológiákat használ, és javítja a memória-megőrzést biztosító karbantartási mechanizmusokat a szüneteltetési időtartam csökkentése érdekében.  

Az újraindítást nem igénylő karbantartási műveletek egyszerre csak egy tartalék tartományt alkalmaznak. Leállnak, ha figyelmeztetési állapotot kapnak a platform monitorozási eszközeitől. 

Az ilyen típusú frissítések hatással lehetnek egyes alkalmazásokra. Ha a virtuális gép élő áttelepítése egy másik gazdagépre történik, egyes érzékeny munkaterhelések némi teljesítménybeli romlást jelezhetnek néhány perc alatt, ami a virtuális gép szüneteltetését eredményezi. A virtuális gépek karbantartásának előkészítéséhez és az Azure-karbantartás során felmerülő hatás csökkentéséhez próbálkozzon az [Scheduled Events Windows](./windows/scheduled-events.md) vagy [Linux](./linux/scheduled-events.md) rendszerű alkalmazásokkal való használatával. 

Az összes karbantartási tevékenység, beleértve a nulla hatású és újraindítás nélküli frissítéseket, a karbantartási vezérlés funkciót is használhatja. Az [Azure dedikált gazdagépeit](./dedicated-hosts.md) vagy egy [elkülönített virtuális gépet](../security/fundamentals/isolation-choices.md)kell használnia. A karbantartási ellenőrzés lehetőséget biztosít az összes platform frissítésének kihagyása és a frissítések adott időpontban történő alkalmazásához a 35 napos időszakon belül. További információ: [a frissítések kezelése a karbantartási és az Azure CLI](maintenance-control.md)használatával.


### <a name="live-migration"></a>Élő áttelepítés

Az élő áttelepítés olyan művelet, amely nem igényel újraindítást, és megőrzi a virtuális gép memóriáját. Szüneteltető vagy befagyasztást okoz, ami általában legfeljebb 5 másodperc. A G, M, N és H sorozat kivételével az összes infrastruktúra-szolgáltatási (IaaS) virtuális gép jogosult az élő áttelepítésre. A jogosult virtuális gépek az Azure-flottába telepített IaaS-alapú virtuális gépek több mint 90 százalékát képviselik. 

Az Azure platform a következő helyzetekben elindítja az élő áttelepítést:
- Tervezett karbantartás
- Hardverhiba
- Foglalási optimalizálások

Egyes tervezett karbantartási forgatókönyvek élő áttelepítést használnak, és az élő áttelepítési műveletek elindulásakor a Scheduled Events segítségével előre is tájékozódhat.

Az élő áttelepítés a virtuális gépek áthelyezésére is használható, ha Azure Machine Learning algoritmusok egy közelgő hardverhiba előrejelzését vagy a virtuálisgép-foglalások optimalizálását szeretnék. A csökkentett teljesítményű hardvereket észlelő prediktív modellezéssel kapcsolatos további információkért lásd: [Az Azure-beli virtuális gépek rugalmasságának növelése a prediktív gépi tanulással és az élő áttelepítéssel](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Az élő áttelepítési értesítések megjelennek a figyelő Azure Portalban, valamint a Service Health naplók és a Scheduled Events esetén, ha ezeket a szolgáltatásokat használja.

## <a name="maintenance-that-requires-a-reboot"></a>Újraindítást igénylő karbantartás

Azon ritka esetekben, amikor a virtuális gépeket újra kell indítani a tervezett karbantartáshoz, a rendszer előzetes értesítést küld. A tervezett karbantartásnak két fázisa van: az önkiszolgáló fázis és egy ütemezett karbantartási fázis.

Az *önkiszolgáló fázisban*, amely általában négy hétig tart, elindítja a karbantartást a virtuális gépeken. Az önkiszolgáló szolgáltatás részeként lekérdezheti az egyes virtuális gépek állapotát, és megtekintheti az utolsó karbantartási kérelem eredményét.

Az önkiszolgáló karbantartás indításakor a virtuális gép egy már frissített csomópontra lesz telepítve. Mivel a virtuális gép újraindul, az ideiglenes lemez elvész, és a virtuális hálózati adapterhez társított dinamikus IP-címek frissülnek.

Ha hiba merül fel az önkiszolgáló karbantartás során, a művelet leáll, a virtuális gép nem frissül, és a lehetőséggel megpróbálkozhat az önkiszolgáló karbantartással. 

Az önkiszolgáló fázis végén az *ütemezett karbantartási fázis* kezdődik. Ebben a fázisban továbbra is lekérdezhető a karbantartási fázis, de a karbantartás nem indítható el.

Az újraindítást igénylő karbantartás kezelésével kapcsolatos további információkért lásd: **tervezett karbantartási értesítések kezelése** az Azure [CLI](maintenance-notifications-cli.md), a [PowerShell](maintenance-notifications-powershell.md) vagy a [Portal](maintenance-notifications-portal.md)használatával. 

### <a name="availability-considerations-during-scheduled-maintenance"></a>A rendelkezésre állással kapcsolatos megfontolások az ütemezett karbantartás során 

Ha úgy dönt, hogy megvárja az ütemezett karbantartási szakaszt, néhány dolgot figyelembe kell vennie a virtuális gépek legmagasabb rendelkezésre állásának fenntartása érdekében. 

#### <a name="paired-regions"></a>Párosított régiók

Minden egyes Azure-régió párosítva van egy másik régióval, amely ugyanazon a földrajzi helyen található. Együttesen egy régió párokat alkotnak. Az ütemezett karbantartási fázisban az Azure csak a régió pár egyetlen régiójában frissíti a virtuális gépeket. Ha például az USA északi középső régiójában frissíti a virtuális gépet, akkor az Azure nem frissíti az USA déli középső régiójában lévő virtuális gépeket. Azonban más régiók (például Észak-Európa) karbantarthatók ugyanabban az időben, mint az USA keleti régiója. Annak megértése, hogy a régió-párok hogyan segíthetnek a virtuális gépek régiók közötti jobb elosztásában. További információ: [Azure region Pairs](../best-practices-availability-paired-regions.md).

#### <a name="availability-sets-and-scale-sets"></a>Rendelkezésre állási készletek és méretezési csoportok

Az Azure-beli virtuális gépeken futó számítási feladatok üzembe helyezésekor létrehozhatók a *rendelkezésre állási csoporton* belüli virtuális gépek, amelyek magas rendelkezésre állást biztosítanak az alkalmazás számára. A rendelkezésre állási csoportok használata esetén gondoskodhat arról, hogy az újraindítást igénylő leállás vagy karbantartási események során legalább egy virtuális gép elérhető legyen.

Egy rendelkezésre állási csoporton belül az egyes virtuális gépek akár 20 frissítési tartományon keresztül is elterjednek. Az ütemezett karbantartás során egy adott időpontban csak egy frissítési tartomány frissül. A frissítési tartományok nem feltétlenül frissülnek egymás után. 

A virtuálisgép- *méretezési* csoportok egy olyan Azure-beli számítási erőforrás, amellyel azonos virtuális gépek egyetlen erőforrásként való üzembe helyezésére és kezelésére használhatók. A méretezési csoport automatikusan települ a frissítési között, például a virtuális gépeket egy rendelkezésre állási csoportba. A rendelkezésre állási csoportokhoz hasonlóan a méretezési csoportok használatakor a rendszer az ütemezett karbantartás során egy adott időpontban csak egy UD frissítést végez.

A virtuális gépek magas rendelkezésre állásra való beállításával kapcsolatos további információkért lásd: a [Windows rendszerű virtuális gépek rendelkezésre állásának kezelése](./availability.md) , vagy a megfelelő [Linux](./availability.md)-cikk.

#### <a name="availability-zones"></a>Rendelkezésreállási zónák

A rendelkezésre állási zónák egy Azure-régióban található egyedi fizikai helyek. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosításához legalább három különálló zónának kell lennie az összes engedélyezett régióban. 

A rendelkezésre állási zónák egy tartalék tartomány és egy frissítési tartomány kombinációja. Ha három vagy több virtuális gépet hoz létre három zónában egy Azure-régióban, a virtuális gépeket a rendszer gyakorlatilag három tartalék tartományba és három frissítési tartományba terjeszti. Az Azure platform felismeri ezt az eloszlást a frissítési tartományok között, hogy megbizonyosodjon róla, hogy a különböző zónákban lévő virtuális gépek nem frissülnek egyidőben.

Az infrastruktúra minden egyes frissítése zónán kívüli zónában, egyetlen régióban található. Az üzembe helyezés azonban 1. zónaban is elvégezhető, és a különböző üzemelő példányok 2. zónan is elérhetők lesznek. A központi telepítések nincsenek szerializálva. Egyetlen központi telepítés azonban egyszerre csak egy zónát mutat be a kockázat csökkentése érdekében.

## <a name="next-steps"></a>Következő lépések 

Az [Azure CLI](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) vagy a [portál](maintenance-notifications-portal.md) használatával felügyelheti a tervezett karbantartást.