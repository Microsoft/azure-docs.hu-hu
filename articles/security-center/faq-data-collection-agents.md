---
title: Azure Security Center gyakori kérdések – adatgyűjtés és ügynökök
description: Az adatgyűjtéssel,-ügynökökkel és-munkaterületekkel kapcsolatos gyakori kérdések Azure Security Center, egy olyan termék, amely segít a fenyegetések megelőzésében, észlelésében és megválaszolásában.
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
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 754e9516c5fd47668cbff5bdc64b8dfdeed050d8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705134"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Gyakori kérdések – az adatgyűjtés, az ügynökök és a munkaterületek kérdései

A Security Center adatokat gyűjt az Azure-beli virtuális gépekről (VM), a virtuálisgép-méretezési csoportokról, a IaaS-tárolók és a nem Azure-beli számítógépekről (beleértve a helyszíni gépeket is) a biztonsági rések és fenyegetések figyelésére. Az adatok gyűjtése a Log Analytics ügynök használatával történik, amely beolvassa a különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat a gépről, és az adatokat a munkaterületre másolja az elemzéshez.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Fizetnem kell Azure Monitor naplókat a Security Center által létrehozott munkaterületeken?

Nem. A Security Center által létrehozott munkaterületek, miközben a csomópontok számlázása Azure Monitor naplókhoz van konfigurálva, nem számítunk fel Azure Monitor naplók díját. Security Center számlázás mindig a Security Center biztonsági házirendje és a munkaterületre telepített megoldások alapján történik:

- **Azure Defender off** – Security Center engedélyezi a "SecurityCenterFree" megoldást az alapértelmezett munkaterületen. Nem számítunk fel díjat, ha az Azure Defender ki van kapcsolva.

- Az **Azure Defender on** – Security Center engedélyezi a "biztonsági" megoldást az alapértelmezett munkaterületen.

További információ a díjszabásról: [Security Center díjszabása](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> A Security Center által létrehozott munkaterületek log Analytics árképzési szintje nem befolyásolja Security Center számlázást.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>Mi a Log Analytics ügynök?

A biztonsági rések és fenyegetések figyeléséhez Azure Security Center a [log Analytics ügynöktől](../azure-monitor/agents/log-analytics-agent.md) függ – ez ugyanaz az ügynök, amelyet a Azure monitor szolgáltatás használ. 

Az ügynököt más néven Microsoft monitoring agentnek (vagy "MMA") nevezzük. 

Az ügynök különböző biztonsággal kapcsolatos konfigurációs adatokat és eseménynaplókat gyűjt a csatlakoztatott gépekről, majd átmásolja az adatokat a Log Analytics munkaterületre további elemzés céljából. Ilyenek például a következők: az operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek és a bejelentkezett felhasználó.

Győződjön meg arról, hogy a gépek az ügynök által támogatott operációs rendszerek egyikét futtatják az alábbi lapokon leírtak szerint:

* [Log Analytics ügynök a Windows által támogatott operációs rendszerekhez](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

* [A Linux által támogatott operációs rendszerek Log Analytics ügynöke](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

További információ az [log Analytics-ügynök által gyűjtött adatokról](security-center-enable-data-collection.md).




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Mi minősíti a virtuális gépet az Log Analytics ügynök telepítésének automatikus kiépítéséhez?

A Windows-vagy Linux-IaaS virtuális gépek az alábbiak szerint minősülnek:

- A Log Analytics ügynök bővítmény jelenleg nincs telepítve a virtuális gépen.
- A virtuális gép fut állapotban van.
- A Windows vagy Linux rendszerű Azure-beli [virtuális gép ügynöke](../virtual-machines/extensions/agent-windows.md) telepítve van.
- A virtuális gép nem használatos készülékként, például webalkalmazási tűzfalként vagy a következő generációs tűzfalon.


## <a name="where-is-the-default-log-analytics-workspace-created"></a>Hol jött létre az alapértelmezett Log Analytics munkaterület?

Az alapértelmezett munkaterület helye az Azure-régiótól függ:

- A Egyesült Államok és Brazíliában lévő virtuális gépek esetében a munkaterület helye a Egyesült Államok
- Kanadában működő virtuális gépek esetén a munkaterület helye Kanada
- Az Európában lévő virtuális gépek esetében a munkaterület helye Európa
- Az Egyesült Királyságban lévő virtuális gépek esetében a munkaterület helye az Egyesült Királyság
- A Kelet-Ázsia és Délkelet-Ázsiában lévő virtuális gépek esetében a munkaterület helye Ázsia
- A Koreában lévő virtuális gépek esetében a munkaterület helye Korea
- Az indiai virtuális gépek esetében a munkaterület helye India
- A Japánban működő virtuális gépek esetében a munkaterület helye Japán
- Kínában működő virtuális gépek esetén a munkaterület helye Kína
- Ausztráliában lévő virtuális gépek esetében a munkaterület helye Ausztrália


## <a name="what-security-events-are-collected-by-the-log-analytics-agent"></a>Milyen biztonsági eseményeket gyűjtenek a Log Analytics ügynök?

Az ügynök által gyűjtött biztonsági események teljes listáját a ["Common" és a "Minimal" biztonsági események beállításainál](security-center-enable-data-collection.md#what-event-types-are-stored-for-common-and-minimal)tekintheti meg.

> [!IMPORTANT]
> Vegye figyelembe, hogy egyes szolgáltatásokhoz, például a Azure Firewallhoz, ha engedélyezte a naplózást, és kiválasztott egy csevegő erőforrást a naplóhoz (például a naplót *részletesen* állítja be), jelentős hatással lehet az log Analytics munkaterület tárolási igényeire. 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Törölhetem a Security Center által létrehozott alapértelmezett munkaterületeket?

**Az alapértelmezett munkaterület törlése nem ajánlott.** Security Center az alapértelmezett munkaterületeket használja a virtuális gépek biztonsági adatainak tárolására. Ha töröl egy munkaterületet, Security Center nem tudja összegyűjteni ezeket az adatokat, és bizonyos biztonsági javaslatok és riasztások nem érhetők el.

A helyreállításhoz távolítsa el a Log Analytics ügynököt a törölt munkaterülethez csatlakoztatott virtuális gépeken. Security Center újratelepíti az ügynököt, és új alapértelmezett munkaterületeket hoz létre.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hogyan használhatom a meglévő Log Analytics munkaterületet?

Kiválaszthat egy meglévő Log Analytics munkaterületet a Security Center által gyűjtött adatok tárolásához. Meglévő Log Analytics munkaterület használata:

- A munkaterületet társítani kell a kiválasztott Azure-előfizetéshez.
- Legalább olvasási jogosultsággal kell rendelkeznie a munkaterület eléréséhez.

Meglévő Log Analytics munkaterület kiválasztása:

1. A Security Center menüjében válassza a **díjszabás & beállítások** lehetőséget.
1. Válassza ki az adott előfizetést.
1. Nyissa meg az **automatikus kiépítés** lapot, s
1. A Log Analytics ügynöknél válassza a **konfiguráció szerkesztése** lehetőséget. 

    :::image type="content" source="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png" alt-text="Az automatikus központi telepítés használatakor használandó Log Analytics-ügynök konfigurációja" lightbox="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png":::

1. Válassza az Azure-beli **virtuális gépek összekapcsolását egy másik munkaterületre** , és válassza ki a meglévő munkaterületet

    :::image type="content" source="./media/security-center-enable-data-collection/choose-workspace.png" alt-text="A Log Analytics ügynök nem alapértelmezett munkaterületének kiválasztása a jelentéshez" lightbox="./media/security-center-enable-data-collection/choose-workspace.png":::

    > [!TIP]
    > A lista csak azokat a munkaterületeket tartalmazza, amelyekhez hozzáférése van, és amelyek az Azure-előfizetésében találhatók.

1. Kattintson a **Mentés** gombra. A rendszer megkérdezi, hogy szeretné-e újrakonfigurálni a figyelt virtuális gépeket.

    - Válassza a **nem** lehetőséget, ha azt szeretné, hogy az új munkaterület-beállítások **csak az új virtuális gépeken legyenek érvényesek**. Az új munkaterület-beállítások csak az új ügynök telepítésére érvényesek; újonnan felderített virtuális gépek, amelyeken nincs telepítve a Log Analytics ügynök.
    - Válassza az **Igen** lehetőséget, ha azt szeretné, hogy az új munkaterület-beállítások **minden virtuális gépen érvényesek** legyenek. Továbbá a Security Center létrehozott munkaterülethez csatlakozó összes virtuális gép újra csatlakozik az új cél munkaterülethez.

    > [!NOTE]
    > Ha az **Igen** lehetőséget választja, ne törölje a Security Center által létrehozott munkaterületeket, amíg az összes virtuális gép újra nem csatlakozik az új cél munkaterülethez. A művelet meghiúsul, ha a munkaterület túl korán van törölve.


## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Mi a teendő, ha a Log Analytics-ügynök már telepítve van a virtuális gépen lévő bővítményként?<a name="mmaextensioninstalled"></a>

Ha a figyelési ügynök bővítményként van telepítve, a bővítmény konfigurációja lehetővé teszi, hogy a jelentéskészítés csak egyetlen munkaterületre legyen elérhető. A Security Center nem bírálja felül a felhasználói munkaterületek meglévő kapcsolatait. Security Center a biztonsági adatok egy már csatlakoztatott munkaterületen lévő virtuális gépről lesznek tárolva, ha a "biztonság" vagy a "SecurityCenterFree" megoldás telepítve van rajta. Security Center a folyamat legújabb verziójára frissítheti a bővítmény verzióját.

További információ: [az automatikus kiépítés egy korábban létező ügynök telepítése esetén](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Mi a teendő, ha a Log Analytics ügynök közvetlenül telepítve van a gépen, de bővítményként nem (közvetlen ügynök)?<a name="directagentinstalled"></a>

Ha a Log Analytics ügynök közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), Security Center telepíti a Log Analytics-ügynök bővítményt, és a Log Analytics-ügynököt a legújabb verzióra frissíti.

A telepített ügynök továbbra is a már konfigurált munkaterülete (ke) t jelenti, és a jelentés a Security Center konfigurált munkaterületre fog jelentést adni (a többsoros vezérlés támogatott a Windows rendszerű gépeken).

Ha a konfigurált munkaterület egy felhasználói munkaterület (nem Security Center alapértelmezett munkaterülete), akkor telepítenie kell a "biztonság" vagy a "SecurityCenterFree" megoldást arra, hogy Security Center a munkaterületre jelentett virtuális gépekről és számítógépekről származó események feldolgozásának megkezdéséhez.

Linux rendszerű gépek esetén az ügynök többsoros vezérlése még nem támogatott – ezért ha egy meglévő ügynök telepítését észleli, az automatikus kiépítés nem történik meg, és a gép konfigurációja nem módosul.

A Security Center március 17 2019 előtt előfizetett előfizetések meglévő gépei esetén a rendszer nem telepíti a Log Analytics-ügynök bővítményt, és a gép nem lesz hatással. Ezen gépek esetében tekintse meg a "figyelési ügynök állapotával kapcsolatos problémák megoldása a gépeken" című javaslatot az ügynök telepítési problémáinak megoldásához ezeken a gépeken

További információ: [Mi történik, ha egy System Center Operations Manager vagy OMS közvetlen ügynök már telepítve van a virtuális gépre?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Mi a teendő, ha egy System Center Operations Manager ügynök már telepítve van a virtuális gépre?<a name="scomomsinstalled"></a>

A Security Center a Log Analytics-ügynök bővítményét a meglévő System Center Operations Manager-ügynökhöz fogja telepíteni. A meglévő ügynök a szokásos módon továbbra is jelentést küld a System Center Operations Manager-kiszolgálónak. Vegye figyelembe, hogy a Operations Manager ügynök és Log Analytics ügynök közös futásidejű kódtárakat használ, amelyeket a rendszer a folyamat során a legújabb verzióra frissít. Megjegyzés: Ha a Operations Manager ügynök 2012-es verziója van telepítve, ne kapcsolja be az automatikus kiépítés funkciót (a kezelhetőségi képességek elvesznek, ha a Operations Manager-kiszolgáló a 2012-es verzióban is megtalálható).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Mik a bővítmények eltávolításának következményei?

Ha eltávolítja a Microsoft figyelési bővítményt, Security Center nem tud biztonsági adatokat gyűjteni a virtuális gépről, és bizonyos biztonsági javaslatok és riasztások nem érhetők el. A Security Center 24 órán belül megállapítja, hogy a virtuális gép hiányzik a bővítményből, és újratelepíti a bővítményt.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hogyan leállítani az ügynök automatikus telepítését és a munkaterület létrehozását?

Az előfizetésekhez tartozó automatikus kiépítés kikapcsolható a biztonsági házirendben, de ez nem ajánlott. Az automatikus kiépítési korlátok kikapcsolása Security Center javaslatok és riasztások. Az automatikus kiépítés letiltása:

1. A Security Center menüjében válassza a **díjszabás & beállítások** lehetőséget.
1. Válassza ki az adott előfizetést.
1. Ha az előfizetéshez engedélyezve van az Azure Defender, nyissa meg az **Azure Defender** -csomagokat, és válassza ki az **Azure Defender** szolgáltatást.

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Az Azure Defender engedélyezése vagy letiltása":::

1. Az **automatikus kiépítés** lapon válassza a toll lehetőséget, majd az automatikus kiosztás kikapcsolása elemet a  **biztonsági házirend – adatgyűjtési** lapon.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Az Log Analytics-ügynök automatikus központi telepítésének engedélyezése":::

1. Kattintson a **Mentés** gombra.


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Letiltom az ügynök automatikus telepítését és a munkaterület létrehozását?

> [!NOTE]
> Ügyeljen arra, hogy tekintse át a [kilépések](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) [következményeit?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) és az ajánlott lépések szakaszt, ha az automatikus kiépítés lehetőséget választja.

Előfordulhat, hogy ki kell kapcsolni az automatikus kiépítés lehetőségeit, ha az alábbiak vonatkoznak Önre:

- A Security Center automatikus ügynök telepítése a teljes előfizetésre vonatkozik. A virtuális gépek egy részhalmazára nem alkalmazhat automatikus telepítést. Ha vannak olyan kritikus virtuális gépek, amelyek nem telepíthetők a Log Analytics ügynökkel, akkor ki kell kapcsolni az automatikus kiépítés lehetőségeit.
- A Log Analytics Agent bővítmény telepítése frissíti az ügynök verzióját. Ez egy közvetlen ügynökre és egy System Center Operations Manager ügynökre vonatkozik (utóbbi esetben a Operations Manager és a Log Analytics ügynök közös futtatókörnyezeti kódtárakat használ – ez a folyamat során frissülni fog). Ha a telepített Operations Manager ügynök az 2012-es verzió, és frissül, akkor a kezelhetőségi képességek elvesznek, ha a Operations Manager-kiszolgáló a 2012-es verzióval is rendelkezik. Ha a telepített Operations Manager ügynök 2012-es verzióval rendelkezik, érdemes megfontolni az automatikus kiépítés kiválasztását.
- Ha az előfizetéshez (egy központi munkaterülethez) kívüli egyéni munkaterülettel rendelkezik, akkor az automatikus kiépítés után le kell választania. Manuálisan is telepítheti a Log Analytics-ügynök bővítményt, és anélkül csatlakoztathatja a munkaterületet, hogy Security Center felülbírálja a kapcsolatot.
- Ha el szeretné kerülni az előfizetések több munkaterületének létrehozását, és saját egyéni munkaterülettel rendelkezik az előfizetésben, akkor két lehetőség közül választhat:

   1. Kiválaszthatja az automatikus kiépítés lehetőségeit. Az áttelepítés után állítsa be a munkaterület alapértelmezett beállításait a [Hogyan használhatom a meglévő log Analytics munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace) című témakörben leírtak szerint?

   1. Vagy engedélyezheti az áttelepítés befejeződését, a virtuális gépekre telepítendő Log Analytics ügynököt, valamint a létrehozott munkaterülethez csatlakozó virtuális gépeket. Ezután válassza ki a saját egyéni munkaterületét úgy, hogy a már telepített ügynökök újrakonfigurálásához megnyomja az alapértelmezett munkaterület-beállítást. További információ: [Hogyan használhatom a meglévő log Analytics munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Milyen hatással van az automatikus kiépítés kivonására?

Az áttelepítés befejezésekor Security Center nem tud biztonsági adatokat gyűjteni a virtuális gépről, és bizonyos biztonsági javaslatok és riasztások nem érhetők el. Ha letiltja, telepítse manuálisan a Log Analytics-ügynököt. A [kilépéskor tekintse meg a javasolt lépéseket](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Mi a javasolt lépés az automatikus kiépítés kiválasztásakor?

Telepítse manuálisan a Log Analytics-ügynök bővítményét, hogy Security Center biztonsági adatokat gyűjtsön a virtuális gépekről, és javaslatokat és riasztásokat nyújtson. A telepítéssel kapcsolatos útmutatásért tekintse meg a [Windows rendszerű virtuális gép](../virtual-machines/extensions/oms-windows.md) vagy az ügynök telepítése a [linuxos virtuális géphez](../virtual-machines/extensions/oms-linux.md) című témakört.

Az ügynököt bármely meglévő egyéni munkaterülethez vagy Security Center létrehozott munkaterülethez is összekapcsolhatjuk. Ha egy egyéni munkaterületen nincs engedélyezve a "biztonság" vagy a "SecurityCenterFree" megoldás, akkor egy megoldást kell alkalmaznia. Az alkalmazáshoz válassza ki az egyéni munkaterületet vagy előfizetést, és alkalmazza a díjszabási szintet a **biztonsági házirend – díjszabási** csomag lapon.

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Az Azure Defender engedélyezése vagy letiltása":::

Security Center a kiválasztott díjszabási csomag alapján engedélyezi a megfelelő megoldást a munkaterületen.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Hogyan eltávolítja a Security Center által telepített OMS-bővítményeket?<a name="remove-oms"></a>

Manuálisan is eltávolíthatja a Log Analytics ügynököt. Ez nem ajánlott, mert korlátozza Security Center javaslatokat és riasztásokat.

> [!NOTE]
> Ha engedélyezve van az adatgyűjtés, Security Center újratelepíti az ügynököt az eltávolítása után.  Az ügynök manuális eltávolítása előtt le kell tiltania az adatgyűjtés letiltását. Lásd: Hogyan az ügynök automatikus telepítésének leállítása és a munkaterület létrehozása? az adatgyűjtés letiltására vonatkozó utasítások.

Az ügynök manuális eltávolítása:

1.    A portálon nyissa meg **log Analytics**.

1.    A Log Analytics lapon válasszon ki egy munkaterületet:

1.    Válassza ki azokat a virtuális gépeket, amelyeket nem szeretne figyelni, és válassza a **Leválasztás** lehetőséget.

   ![Az ügynök eltávolítása][3]

> [!NOTE]
> Ha egy Linux rendszerű virtuális gép már rendelkezik egy nem kiterjesztésű OMS-ügynökkel, a bővítmény eltávolítása eltávolítja az ügynököt is, és újra kell telepítenie.


## <a name="how-do-i-disable-data-collection"></a>Hogyan az adatgyűjtés letiltása?

Az automatikus kiépítés kifejezetten ajánlott ahhoz, hogy biztonsági riasztásokat és javaslatokat kapjon a rendszerfrissítésekkel, az operációs rendszer sebezhetőségével és az Endpoint Protection szolgáltatással kapcsolatban. Alapértelmezés szerint az automatikus kiépítés le van tiltva.

Ha engedélyezte, de most le szeretné tiltani:

1. [A Azure Portal](https://portal.azure.com)nyissa meg **Security Center** , és válassza a **díjszabás és beállítások** lehetőséget.

1. Válassza ki azt az előfizetést, amelyen le szeretné tiltani az automatikus kiépítés szolgáltatást.

1. Az **automatikus kiépítés** alatt kapcsolja ki a log Analytics-ügynök kapcsolóját.


## <a name="how-do-i-enable-data-collection"></a>Hogyan engedélyezi az adatgyűjtés?

Az Azure-előfizetéshez tartozó adatgyűjtést a biztonsági szabályzatban engedélyezheti. Az adatgyűjtés engedélyezéséhez. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), válassza a **Tallózás** lehetőséget, válassza a **Security Center** lehetőséget, és válassza a **biztonsági szabályzat** lehetőséget. Válassza ki azt az előfizetést, amelyhez engedélyezni kívánja az automatikus kiépítés engedélyezését. Amikor kiválaszt egy előfizetési **biztonsági házirendet** , megnyílik az adatgyűjtés. Az **automatikus kiépítés** területen válassza **a** be lehetőséget.


## <a name="what-happens-when-data-collection-is-enabled"></a>Mi történik, ha az adatgyűjtés engedélyezve van?

Ha engedélyezve van az automatikus kiépítés, Security Center a Log Analytics ügynököt az összes támogatott Azure-beli virtuális gépen és a létrehozott újakon. Az automatikus kiépítés ajánlott, de a manuális ügynök telepítése is elérhető. [Ismerje meg, hogyan telepítheti a log Analytics Agent bővítményt](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Az ügynök engedélyezi a 4688-es folyamat-létrehozási eseményt és a 4688-es eseményen belüli *parancssori* mezőt. A virtuális gépen létrehozott új folyamatokat az eseménynapló rögzíti, és a Security Center észlelési szolgáltatásai figyelik. Az egyes új folyamatokra vonatkozóan rögzített részletekkel kapcsolatos további információkért lásd: [a Leírás mezői a 4688-ben](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Az ügynök a virtuális gépen létrehozott 4688 eseményt is gyűjti, és azokat a keresés során tárolja.

Az ügynök az [adaptív alkalmazások vezérlőinek](security-center-adaptive-application.md)adatgyűjtését is lehetővé teszi, Security Center a helyi AppLocker-házirendet naplózási módban konfigurálja az összes alkalmazás engedélyezéséhez. Ez a szabályzat az AppLockert az események előállítására fogja használni, amelyeket a Security Center gyűjt és használ. Fontos megjegyezni, hogy ez a szabályzat nem lesz konfigurálva olyan gépeken, amelyeken már van konfigurált AppLocker-házirend. 

Ha a Security Center gyanús tevékenységet észlel a virtuális gépen, a rendszer e-mailben értesíti az ügyfelet, ha a [biztonsági kapcsolattartási adatokat](security-center-provide-security-contact-details.md) megadták. A Security Center biztonsági riasztások irányítópultján is látható egy riasztás.


## <a name="will-security-center-work-using-an-oms-gateway"></a>A Security Center OMS-átjáró használatával fog működni?

Igen. Azure Security Center kihasználja Azure Monitor az Azure-beli virtuális gépekről és kiszolgálókról származó adatok gyűjtésére az Log Analytics-ügynök használatával.
Az adatok gyűjtéséhez az egyes virtuális gépeknek és kiszolgálóknak HTTPS használatával kell csatlakozniuk az internethez. A csatlakozás lehet közvetlen, proxy használatával vagy a [OMS-átjárón](../azure-monitor/agents/gateway.md)keresztül.


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Befolyásolja a figyelési ügynök a saját kiszolgálók teljesítményét?

Az ügynök a rendszererőforrások névleges mennyiségét használja fel, és kevés hatással van a teljesítményre. A teljesítményre gyakorolt hatásra, valamint az ügynökre és a bővítményre vonatkozó további információkért tekintse meg a [tervezési és üzemeltetési útmutatót](security-center-planning-and-operations-guide.md#data-collection-and-storage).




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png