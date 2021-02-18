---
title: Egy Azure-beli virtuális gép rendszer-újraindításának ismertetése | Microsoft Docs
description: A virtuális gép újraindítását okozó események listája
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c3dcb4a7b92f5aab1336e49a7d299614855b48fd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591161"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Az Azure-beli virtuális gép rendszer-újraindításának ismertetése

Előfordulhat, hogy az Azure-beli virtuális gépek (VM-EK) időnként újraindulnak, anélkül, hogy az újraindítási műveletet kezdeményezték volna. Ez a cikk felsorolja azokat a műveleteket és eseményeket, amelyek a virtuális gépek újraindítását okozhatják, és betekintést nyújtanak a váratlan újraindítási problémák elkerülésére vagy az ilyen problémák hatásának csökkentésére.

## <a name="configure-the-vms-for-high-availability"></a>A virtuális gépek konfigurálása magas rendelkezésre álláshoz

Az Azure-on futó alkalmazások védelmének legjobb módja a virtuális gépek újraindításának és leállásának a biztosítása a virtuális gépek magas rendelkezésre álláshoz való konfigurálásához.

Ha ezt a redundancia-szintet szeretné biztosítani az alkalmazás számára, javasoljuk, hogy legalább két virtuális gépet egy rendelkezésre állási csoportban csoportosítson. Ez a konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető legyen, és megfelel a 99,95%-os [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/)-nak.

A rendelkezésre állási csoportokkal kapcsolatos további információkért lásd: [virtuális gépek rendelkezésre állásának kezelése](../manage-availability.md)

## <a name="resource-health-information"></a>Resource Health információk

Azure Resource Health egy olyan szolgáltatás, amely lehetővé teszi az egyes Azure-erőforrások állapotát, és gyakorlati útmutatást nyújt a problémák elhárításához. Olyan felhőalapú környezetben, ahol nem lehet közvetlenül hozzáférni a kiszolgálókhoz vagy infrastruktúra-elemekhez, a Resource Health célja, hogy csökkentse a hibaelhárítás során eltöltött időt. A cél az, hogy csökkentse azt az időpontot, ameddig a probléma kihasználása az alkalmazásban vagy az Azure platformon belüli eseményben rejlik. További információ: [Resource Health megismerése és használata](../../service-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Műveletek és események, amelyek a virtuális gép újraindítását okozhatják

### <a name="planned-maintenance"></a>Tervezett karbantartás

Microsoft Azure rendszeresen végez frissítéseket a világ minden táján, hogy javítsa a virtuális gépeken alapuló gazdagép-infrastruktúra megbízhatóságát, teljesítményét és biztonságát. A frissítések, köztük a memória-megőrzési frissítések jelentős része a virtuális gépekre vagy a Cloud Servicesre gyakorolt hatás nélkül történik.

Néhány frissítéshez azonban újraindítás szükséges. Ilyen esetekben a virtuális gépek le vannak állítva az infrastruktúra javítása közben, majd a virtuális gépek újraindulnak.

Az Azure tervezett karbantartásának megismeréséhez és a linuxos virtuális gépek rendelkezésre állásának befolyásolásához tekintse meg az itt felsorolt cikkeket. A cikkek háttértudást biztosítanak az Azure tervezett karbantartási folyamatáról, illetve arról, hogy miként ütemezheti úgy a tervezett karbantartást, hogy az a lehető legkisebb hatást gyakorolja.

- [A virtuális gépek tervezett karbantartása az Azure-ban](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)
- [Azure-beli virtuális gépek tervezett karbantartásának ütemezése](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)

### <a name="memory-preserving-updates"></a>Memóriamegőrző frissítések

A Microsoft Azure frissítéseinek ezen osztályában a felhasználók nem gyakorolnak hatást a futó virtuális gépekre. A frissítések sok esetben olyan összetevőkhöz vagy szolgáltatásokhoz tartoznak, amelyek frissíthetők a futó példány zavarása nélkül. Némelyik a gazdagép operációs rendszerének platform-infrastruktúrájának frissítései, amelyeket a virtuális gépek újraindítása nélkül lehet alkalmazni.

Ezeket a memóriamegőrző frissítéseket olyan technológia biztosítja, amely lehetővé teszi a helyszíni élő áttelepítést. A frissítés során a virtuális gép *szüneteltetett* állapotba kerül. Ez az állapot megőrzi a memóriát a RAM-ban, amíg az alapjául szolgáló gazda operációs rendszer megkapja a szükséges frissítéseket és javításokat. A virtuális gép a szüneteltetés után 30 másodpercen belül folytatja a működését. A szüneteltetés után a virtuális gép órája automatikusan szinkronizálódik.

A rövid szüneteltetési időszak miatt a frissítések ezen a mechanizmuson keresztül történő üzembe helyezése jelentősen csökkenti a virtuális gépekre gyakorolt hatást. Így azonban nem minden frissítés helyezhető üzembe. 

A többpéldányos frissítések végrehajtása (a rendelkezésre állási csoportokban lévő virtuális gépeken) frissítési tartományonként történik.

> [!NOTE]
> A régi kernel-verziókkal rendelkező Linux-gépeket a kernel pánikja érinti a frissítési módszer során. A probléma elkerüléséhez frissítsen a kernel 3.10.0-327.10.1 vagy újabb verziójára. További információ: [Azure Linux rendszerű virtuális gép a 3,10-alapú kernelen a gazdagép csomópontjának frissítése után](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>A felhasználó által kezdeményezett újraindítási vagy leállítási műveletek

Ha újraindítást végez a Azure Portal, Azure PowerShell, parancssori felületen vagy REST API, megkeresheti az eseményt az Azure-beli [tevékenység naplójában](../../azure-monitor/essentials/platform-logs-overview.md).

Ha a virtuális gép operációs rendszerének a műveletét hajtja végre, a rendszernaplókban megkeresheti az eseményt.

Más forgatókönyvek, amelyek általában a virtuális gép újraindítását okozzák, több konfigurációs módosítási műveletet is tartalmazhatnak. Általában egy figyelmeztető üzenet jelenik meg, amely jelzi, hogy egy adott művelet végrehajtása a virtuális gép újraindítását eredményezi. Ilyenek például a virtuális gépek átméretezési műveletei, a rendszergazdai fiók jelszavának módosítása és statikus IP-cím beállítása.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center és Windows Update

Azure Security Center figyeli a napi Windows és Linux rendszerű virtuális gépeket az operációs rendszer frissítéseinek hiányában. Security Center lekéri az elérhető biztonsági és kritikus frissítések listáját Windows Update vagy Windows Server Update Services (WSUS) szolgáltatásból attól függően, hogy melyik szolgáltatást konfigurálja a Windows rendszerű virtuális gépeken. A Security Center a linuxos rendszerek legújabb frissítéseit is ellenőrzi. Ha a virtuális gép hiányzik a rendszerfrissítésből, Security Center javasolja a rendszerfrissítések alkalmazását. A rendszerfrissítések alkalmazásának vezérlése a Azure Portal Security Centeron keresztül történik. Néhány frissítés alkalmazása után szükség lehet a virtuális gépek újraindítására. További információ: [rendszerfrissítések alkalmazása Azure Security Centerban](../../security-center/asset-inventory.md).

A helyszíni kiszolgálókhoz hasonlóan az Azure nem küldi le a frissítéseket a Windows Updateról a Windows rendszerű virtuális gépekre, mert ezeket a gépeket a felhasználók számára kívánja felügyelni. Javasoljuk azonban, hogy engedélyezze az automatikus Windows Update beállítást. A frissítések Windows Updateról történő automatikus telepítése a frissítések alkalmazása után is okozhat újraindítást. További információ: [Windows Update GYIK](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>A virtuális gép rendelkezésre állását befolyásoló egyéb helyzetek

Más esetekben előfordulhat, hogy az Azure aktívan felfüggeszti a virtuális gépek használatát. A művelet elvégzése előtt e-mail-értesítéseket fog kapni, így lehetősége lesz a mögöttes problémák megoldására. A virtuális gépek rendelkezésre állását befolyásoló problémák például a biztonsági szabálysértések és a fizetési módok lejárta.

### <a name="host-server-faults"></a>A gazdakiszolgáló hibái

A virtuális gépet egy Azure-adatközponton belül futó fizikai kiszolgálón üzemelteti a rendszer. A fizikai kiszolgáló egy másik Azure-összetevő mellett futtatja a gazdagép ügynökének nevezett ügynököt. Ha ezek az Azure-beli szoftver-összetevők a fizikai kiszolgálón nem válaszolnak, a figyelő rendszer elindítja a gazdagép újraindítását a helyreállítás megkísérlése érdekében. A virtuális gép általában öt percen belül újra elérhető, és továbbra is ugyanazon a gazdagépen él, mint korábban.

A kiszolgálói hibákat általában hardverhiba okozta, például a merevlemez vagy a SSD-meghajtó meghibásodása. Az Azure folyamatosan figyeli ezeket az eseményeket, azonosítja a mögöttes hibákat, és kivezeti a frissítéseket a megoldás implementálása és tesztelése után.

Mivel egyes gazdagép-kiszolgálói hibák az adott kiszolgálóra jellemzőek lehetnek, a virtuális gép újraindítási helyzetét a virtuális gép másik gazdagép-kiszolgálóra történő manuális ismételt üzembe helyezésével lehet javítani. Ez a művelet a virtuális gép részletek lapjának **újratelepítése** lehetőségével indítható el, vagy a virtuális gép leállításával és újraindításával a Azure Portal.

### <a name="auto-recovery"></a>Automatikus helyreállítás

Ha a gazda-kiszolgáló valamilyen okból nem indítható újra, az Azure platform automatikus helyreállítási műveletet indít el, hogy a hibás gazdagép-kiszolgáló ne legyen elforgatva további vizsgálat céljára. 

A rendszer a gazdagépen lévő összes virtuális gépet egy másik, kifogástalan állapotú gazdagépre helyezi át. Ez a folyamat általában 15 percen belül befejeződik. További információ az automatikus helyreállítási folyamatról: [virtuális gépek automatikus helyreállítása](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Nem tervezett karbantartás

Ritkán előfordulhat, hogy az Azure-műveleti csapatnak karbantartási tevékenységeket kell végeznie az Azure platform általános állapotának biztosítása érdekében. Ez a viselkedés hatással lehet a virtuális gépek rendelkezésre állására, és általában ugyanazokat az automatikus helyreállítási műveleteket eredményezi, mint a korábban leírtak szerint.  

A nem tervezett karbantartás a következőket foglalja magában:

- Sürgős csomópont-töredezettségmentesítés
- Sürgős hálózati kapcsolók frissítései

### <a name="vm-crashes"></a>VIRTUÁLIS gépek összeomlása

A virtuális gépeken belüli problémák miatt előfordulhat, hogy a virtuális gépek újraindulnak. A virtuális gépen futó munkaterhelés vagy szerepkör a vendég operációs rendszeren is aktiválhatja a hibákat. Ha segítségre van az összeomlás okának meghatározásához, tekintse meg a Windows rendszerű virtuális gépek rendszer-és alkalmazás-naplóit, valamint a linuxos virtuális gépek soros naplóit.

### <a name="storage-related-forced-shutdowns"></a>A Storage szolgáltatással kapcsolatos kényszerített leállások

Az Azure-beli virtuális gépek az Azure Storage-infrastruktúrában üzemeltetett operációs rendszerek és adattárolási virtuális lemezeket használják. Ha a virtuális gép és a hozzá tartozó virtuális lemezek közötti rendelkezésre állást vagy kapcsolatot több mint 120 másodpercre érinti, az Azure platform a virtuális gépek kényszerített leállítását hajtja végre, hogy elkerülje az adatok sérülését. A rendszer automatikusan visszaállítja a virtuális gépeket a tárolási kapcsolat visszaállítása után. 

A Leállítás időtartama akár öt percet is igénybe vehet, de jóval hosszabb lehet. A következő a tárolással kapcsolatos kényszerített leállításokhoz kapcsolódó konkrét esetek egyike: 

**IO-korlátok túllépése**

Előfordulhat, hogy a virtuális gépek átmenetileg le vannak állítva az I/o-kérelmek következetes szabályozása miatt, mert a másodpercenkénti I/o-műveletek mennyisége (IOPS) meghaladja a lemez I/O-korlátait. (A standard szintű lemezes tárolás 500 IOPS van korlátozva.) A probléma megoldásához használjon lemezes csíkozást, vagy konfigurálja a tárterületet a vendég virtuális gépen a munkaterheléstől függően. 

### <a name="other-incidents"></a>Egyéb incidensek

Ritka esetekben egy elterjedt probléma az Azure-adatközpont több kiszolgálójára is hatással lehet. Ha ez a probléma merül fel, az Azure csapata e-mail-értesítéseket küld az érintett előfizetésekhez. A folyamatban lévő kimaradások és a múltbeli incidensek állapotának ellenőrzéséhez tekintse meg a [Azure Service Health irányítópultot](https://azure.microsoft.com/status/) és a Azure Portal.