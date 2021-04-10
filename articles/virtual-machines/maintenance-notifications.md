---
title: Karbantartási értesítések
description: Az Azure-ban futó virtuális gépek karbantartási értesítéseinek áttekintése.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 8/12/2020
ms.author: shants
ms.openlocfilehash: 92cb780a80f1010fd1c2f5d19fe616e0285de73b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564526"
---
# <a name="handling-planned-maintenance-notifications"></a>Tervezett karbantartási értesítések feldolgozása

Az Azure rendszeresen végez frissítéseket a virtuális gépeket futtató infrastruktúra megbízhatóságának, teljesítményének és biztonságának javítása érdekében. A frissítések olyan változások, mint az üzemeltetési környezet javítása vagy a hardver frissítése és leszerelése. A frissítések többsége az üzemeltetett virtuális gépekre gyakorolt hatás nélkül fejeződik be. Vannak azonban olyan esetek, amikor a frissítések hatással vannak a következőkre:

- Ha a karbantartás nem igényel újraindítást, az Azure néhány másodpercig szünetelteti a virtuális gépet, amíg a rendszer frissíti a gazdagépet. Az ilyen típusú karbantartási műveletek tartalék tartományra vonatkoznak a tartalék tartomány alapján. A rendszer leállítja az előrehaladást, ha a rendszer figyelmeztetési állapottal kapcsolatos riasztásokat fogad.

- Ha a karbantartás újraindítást igényel, a karbantartás megtervezése után értesítést kap. Körülbelül 35 napos időablakot kap, ahol megkezdheti a karbantartást, ha Ön is működik.


Az újraindítást igénylő tervezett karbantartás hullámokban van ütemezve. Minden egyes hullám különböző hatókörrel (régiókkal) rendelkezik.

- A Wave az ügyfeleknek küldött értesítésekkel kezdődik. Alapértelmezés szerint az értesítés az előfizetés-rendszergazda és a társ-rendszergazdák számára lesz elküldve. A [műveletnapló értesítéseivel](../service-health/alerts-activity-log-service-notifications-portal.md)további címzetteket és üzenetküldési lehetőségeket, például e-maileket, SMS-t és webhookokat adhat hozzá.  
- Ha egy értesítés bekerül, az *önkiszolgáló ablak* elérhetővé válik. Ebben az ablakban lekérdezheti, hogy mely virtuális gépek érintettek, és hogyan kezdheti el a karbantartást a saját ütemezési igényei alapján. Az önkiszolgáló ablak általában körülbelül 35 nap.
- Az önkiszolgáló ablak után megkezdődik az *ütemezett karbantartási* időszak. Ezen az időszakon belül az Azure ütemezni fogja a szükséges karbantartást a virtuális gépen. 

Ahhoz, hogy az Azure automatikusan megkezdje a karbantartást, a két Windows rendszernek kell megadnia a karbantartási és a virtuális gépek újraindítását.


A Azure Portal, a PowerShell, a REST API és a CLI segítségével lekérdezheti a virtuális gépek karbantartási ablakait, és elindíthatja az önkiszolgáló karbantartást.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>A karbantartást az önkiszolgáló ablakon belül kell elindítani?  

Az alábbi irányelvek segítségével eldöntheti, hogy használja-e ezt a képességet, és a karbantartást a saját időpontjában kell megkezdenie. 

> [!NOTE] 
> Előfordulhat, hogy az önkiszolgáló karbantartás nem érhető el az összes virtuális géphez. Annak megállapításához, hogy elérhető-e az előjelzéses újratelepítés a virtuális géphez, keresse meg a **Start Now** (Karbantartás) állapotot. Az önkiszolgáló karbantartás jelenleg nem érhető el Cloud Services (webes/feldolgozói szerepkör) és Service Fabrichoz.


Az önkiszolgáló karbantartás nem ajánlott a **rendelkezésre állási csoportokkal** történő üzembe helyezéshez. A rendelkezésre állási csoportok egyszerre csak egy frissítési tartományt frissítenek. 

- Hagyja, hogy az Azure aktiválja a karbantartást. Az újraindítást igénylő karbantartás esetén a karbantartási tartomány frissíti a tartományt. A frissítési tartományok nem feltétlenül kapják meg a karbantartást egymás után, és a frissítési tartományok között 30 perces szünet van. 
- Ha bizonyos kapacitás ideiglenes elvesztése (1 frissítési tartomány) aggodalomra ad okot, a karbantartási időszak alatt hozzáadhat példányokat. 
- Az újraindítást nem igénylő karbantartás esetén a frissítések a tartalék tartomány szintjén lesznek alkalmazva. 

**Ne** használja az önkiszolgáló karbantartást az alábbi helyzetekben: 
- Ha a virtuális gépeket gyakran, manuálisan, a DevTest Labs használatával, az automatikus leállítás vagy az ütemterv alapján állítja le, akkor visszaállíthatja a karbantartási állapotot, így további állásidőt eredményezhet.
- A rövid élettartamú virtuális gépeken, amelyeket tudni fog, a karbantartási hullám vége előtt törölni fogjuk. 
- Olyan számítási feladatokhoz, amelyekben a frissítéskor szükséges helyi (ideiglenes) lemezen található nagy állapotú munkaterhelések vannak tárolva. 
- Olyan esetekben, amikor gyakran lakik a virtuális gép, mert visszaállíthatja a karbantartási állapotot. 
- Ha olyan ütemezett eseményeket fogadott el, amelyek lehetővé teszik az előjelzéses feladatátvételt vagy a számítási feladatok zökkenőmentes leállítását, a karbantartási leállítás megkezdése előtt 15 perccel

**Használja** az önkiszolgáló karbantartást, ha azt tervezi, hogy a virtuális gépet az ütemezett karbantartási fázisban megszakítás nélkül futtatja, és a fent említett számlálók egyike sem alkalmazható. 

A következő esetekben ajánlott önkiszolgáló karbantartást használni:
- Pontos karbantartási időszakot kell kommunikálnia a felügyelettel vagy a végfelhasználóval. 
- A karbantartást egy adott dátummal kell végrehajtania. 
- Meg kell határoznia a karbantartási folyamatot, például a többrétegű alkalmazást a biztonságos helyreállítás biztosításához.
- Két frissítési tartomány (frissítési) között több mint 30 percnyi virtuális gép helyreállítási ideje szükséges. A frissítési tartományok közötti idő szabályozásához a virtuális gépek karbantartását egyszerre egy frissítési tartományon (UD) kell elindítania.


## <a name="faq"></a>GYIK


**K: Miért van szükség a virtuális gépek újraindítására?**

**A:** Habár az Azure platform frissítéseinek és frissítéseinek többsége nem befolyásolja a virtuális gép rendelkezésre állását, vannak olyan esetek, amikor nem tudjuk elkerülni az Azure-ban üzemeltetett virtuális gépek újraindítását. Több olyan módosítást is összegyűjtöttünk, amelyek a virtuális gépek újraindítását eredményező kiszolgálók újraindítását igénylik.

**K: Ha a magas rendelkezésre állásra vonatkozó javaslatokat a rendelkezésre állási csoport használatával követem, akkor biztonságban vagyok?**

**A:** A rendelkezésre állási csoportokban vagy virtuálisgép-méretezési csoportokban üzembe helyezett virtuális gépek a frissítési tartományok (UD) fogalmát jelentik. A karbantartás végrehajtásakor az Azure tiszteletben tartja az UD korlátozást, és nem indít újra a virtuális gépeket a különböző UD-ból (ugyanazon rendelkezésre állási csoporton belül).  Az Azure a virtuális gépek következő csoportjára való áttérés előtt is legalább 30 percet vár. 

További információ a magas rendelkezésre állásról: [virtuális gépek rendelkezésre állása az Azure-ban](availability.md).

**K: Hogyan értesítést kap a tervezett karbantartásról?**

**A:** Egy tervezett karbantartási hullám egy vagy több Azure-régióra vonatkozó ütemterv beállításával kezdődik. Az előfizetés-adminisztrátorok, a közös adminisztrátorok, a tulajdonosok és a közreműködők e-mailben értesítést kapnak (egy e-mail-cím/előfizetés). Az értesítéshez tartozó további csatornákat és címzetteket a műveletnapló riasztásai használatával lehet konfigurálni. Ha olyan régióba helyez üzembe egy virtuális gépet, ahol a tervezett karbantartás már ütemezve van, nem fogja tudni megkapni az értesítést, hanem a virtuális gép karbantartási állapotát.

**K: nem látom a tervezett karbantartást a portálon, a PowerShellben vagy a CLI-ben. mi a baj?**

**A:** A tervezett karbantartással kapcsolatos információk a tervezett karbantartási hullámokban csak azokra a virtuális gépekre vonatkozóan érhetők el, amelyeket a rendszer érint. Más szóval, ha nem jelenik meg az adatai, lehet, hogy a karbantartási hullám már befejeződött (vagy nem indult el), vagy hogy a virtuális gép már egy frissített kiszolgálón található.

**K: van mód arra, hogy pontosan megtudjam, mikor érinti a virtuális gépet?**

**A:** Az ütemterv beállításakor több napos időablakot határozunk meg. Az ebben az ablakban található kiszolgálók (és virtuális gépek) pontos sorrendje azonban ismeretlen. Azok az ügyfelek, akik szeretnék megismerni a virtuális gépek pontos idejét, használhatnak [ütemezett eseményeket](./linux/scheduled-events.md) és lekérdezéseket a virtuális gépen, és 15 perces értesítést kapnak a virtuális gép újraindítása előtt.

**K: mennyi időt vesz igénybe a virtuális gép újraindítása?**

**A:**  A virtuális gép méretétől függően az újraindítás akár több percet is igénybe vehet az önkiszolgáló karbantartási időszak alatt. Az ütemezett karbantartási időszakban az Azure által kezdeményezett újraindítások során az újraindítás általában körülbelül 25 percet vesz igénybe. Vegye figyelembe, hogy ha Cloud Services (webes/feldolgozói szerepkör), Virtual Machine Scale Sets vagy rendelkezésre állási csoportot használ, a rendszer az ütemezett karbantartási időszak során 30 percet kap az egyes VM-csoportok (UD) között.

**K: mi a tapasztalata Virtual Machine Scale Sets esetén?**

**A:** A tervezett karbantartás már elérhető a Virtual Machine Scale Sets számára. Az önkiszolgáló karbantartás elindításával kapcsolatos utasításokért tekintse meg a [virtuálisgép-méretezési csoportok tervezett karbantartását](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) ismertető dokumentumot.

**K: mi a tapasztalata Cloud Services (webes/feldolgozói szerepkör) és Service Fabric esetén?**

**A:** Habár a tervezett karbantartás érinti ezeket a platformokat, az ezeket a platformokat használó ügyfelek biztonságosak, mivel csak egyetlen frissítési tartományban (UD) lévő virtuális gépek lesznek hatással a megadott időpontra. Az önkiszolgáló karbantartás jelenleg nem érhető el Cloud Services (webes/feldolgozói szerepkör) és Service Fabrichoz.

**K: nem látok semmilyen karbantartási információt a virtuális gépeken. Mi volt a baj?**

**A:** Több oka is van annak, hogy miért nem jelenik meg karbantartási információ a virtuális gépeken:
1.  Microsoft belsőként jelölt előfizetést használ.
2.  A virtuális gépek nincsenek karbantartásra ütemezve. Lehetséges, hogy a karbantartási hullám véget ért, megszakították vagy módosították, így a virtuális gépeket már nem érinti.
3. Kiosztotta a virtuális gépet, majd elindította. Ez azt eredményezheti, hogy a virtuális gép olyan helyre vált, amely nem rendelkezik tervezett karbantartási hullámtal. Így a virtuális gép többé nem jeleníti meg a karbantartási információkat. 
4.  Nem rendelkezik a virtuális gépek listájának nézetéhez hozzáadott **karbantartási** oszloppal. Noha ezt az oszlopot hozzáadta az alapértelmezett nézethez, a nem alapértelmezett oszlopok megjelenítésére konfigurált ügyfeleknek manuálisan kell felvenniük a **karbantartási** oszlopot a virtuálisgép-lista nézetbe.

**K: a virtuális gép másodszor is karbantartásra van ütemezve. miért?**

**A:** Több felhasználási eset van, ahol a virtuális gépet karbantartásra ütemezi, miután már elvégezte a karbantartást – az újbóli üzembe helyezést:
1.  Megszakítottuk a karbantartási hullámot, és egy másik hasznos adattartalommal újraindítottuk. Lehetséges, hogy hibás adattartalmat észlelt, ezért egyszerűen üzembe kell helyezni egy további hasznos adatot.
2.  A virtuális gép egy hardverhiba miatt *meggyógyult* egy másik csomópontra.
3.  Azt választotta, hogy leállítja (felszabadítja), majd újraindítja a virtuális gépet.
4.  A virtuális gép **automatikus leállítása** be van kapcsolva.



## <a name="next-steps"></a>Következő lépések

Az [Azure CLI](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) vagy [Portal](maintenance-notifications-portal.md)használatával kezelheti a tervezett karbantartást.
