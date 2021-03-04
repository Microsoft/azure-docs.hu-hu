---
title: Ügynökök automatikus üzembe helyezése Azure Security Centerhoz | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állítható be a Log Analytics-ügynök és a Azure Security Center által használt egyéb ügynökök és bővítmények automatikus üzembe helyezése
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: d9d0739704a9f5f16bdbde80661192b2f1ca9bb1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099420"
---
# <a name="configure-auto-provisioning-for-agents-and-extensions-from-azure-security-center"></a>Az ügynökök és bővítmények automatikus kiépítés beállítása Azure Security Center

Security Center adatokat gyűjt az erőforrásokról az adott erőforráshoz tartozó megfelelő ügynökkel vagy bővítményekkel, valamint az Ön által engedélyezett adatgyűjtési típussal. Az alábbi precedures használatával gondoskodhat arról, hogy az erőforrása rendelkezzen a Log Analytics ügynök és a Azure Security Center által használt egyéb ügynökök és bővítmények automatikus kiépítés beállításával.

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

## <a name="availability"></a>Rendelkezésre állás

| Szempont                  | Részletek                                                                                                                                                                                                                      |
|-------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kiadás állapota:          | **Szolgáltatás**: az automatikus kiépítés általánosan elérhető (GA)<br>**Ügynök és bővítmények**: log Analytics Azure-beli virtuális gépek ügynöke, a Microsoft függőségi ügynök előzetes verzióban érhető el, a Kubernetes házirend-bővítménye a ga                |
| Árképzési                | Ingyenes                                                                                                                                                                                                                         |
| Támogatott célhelyek: | ![Igen](./media/icons/yes-icon.png) Azure-gépek<br>![Nem](./media/icons/no-icon.png) Azure arc-gépek<br>![Nem](./media/icons/no-icon.png) Kubernetes-csomópontok<br>![Nem](./media/icons/no-icon.png) Virtual Machine Scale Sets |
| Felhők                 | ![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Igen](./media/icons/yes-icon.png) US Gov, Kína gov, egyéb gov                                                                                                      |
|                         |                                                                                                                                                                                                                              |

## <a name="how-does-security-center-collect-data"></a>Hogyan gyűjt Security Center adatokat?

A Security Center adatokat gyűjt az Azure-beli virtuális gépekről (VM), a virtuálisgép-méretezési csoportokról, a IaaS-tárolókra és a nem Azure-ból (beleértve a helyszíni gépeket is) a biztonsági rések és fenyegetések figyelésére. 

Az adatgyűjtés szükséges a hiányzó frissítések láthatóságának biztosításához, a hibásan konfigurált operációs rendszer biztonsági beállításai, az Endpoint Protection állapota, valamint az állapot és a veszélyforrások elleni védelem. Az adatgyűjtés csak számítási erőforrások (virtuális gépek, virtuálisgép-méretezési csoportok, IaaS-tárolók és nem Azure-beli számítógépek) esetében szükséges. Kihasználhatja a Azure Security Center akkor is, ha nem rendelkezik ügynökökkel; azonban korlátozott a biztonság, és a fent felsorolt képességek nem támogatottak.  

Az adatok gyűjtése a használatával történik:

- A **log Analytics ügynök**, amely különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat olvas be a gépről, és átmásolja az adatokat a munkaterületre elemzés céljából. Ilyenek például a következők: az operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek és a bejelentkezett felhasználó.
- A **biztonsági bővítmények**, például a [Kubernetes Azure Policy-bővítménye](../governance/policy/concepts/policy-for-kubernetes.md), amely a speciális erőforrástípusok Security Center vonatkozó információkat is biztosít.

> [!TIP]
> Ahogy a Security Center nőtt, a megfigyelhető erőforrások típusai is megnőttek. A bővítmények száma is megnőtt. Az automatikus kiépítés kibővült a további erőforrástípusok támogatásához a Azure Policy képességeinek kihasználásával.

## <a name="why-use-auto-provisioning"></a>Miért érdemes az automatikus kiépítés használatára?
Az ezen *a lapon leírt* ügynökök és bővítmények bármelyike telepíthető manuálisan (lásd: [a log Analytics ügynök manuális telepítése](#manual-agent)). Az **automatikus kiépítés** azonban csökkenti a felügyelet terhelését azáltal, hogy az összes szükséges ügynököt és bővítményt telepíti a meglévő és a új gépekre, így biztosítva az összes támogatott erőforrás gyorsabb biztonsági lefedettségét. 

Javasoljuk, hogy engedélyezze az automatikus kiépítés használatát, de alapértelmezés szerint le van tiltva.

## <a name="how-does-auto-provisioning-work"></a>Hogyan működik az automatikus kiépítés?
Security Center automatikus kiépítési beállításai rendelkeznek a támogatott bővítmények egyes típusaihoz tartozó kapcsolókkal. Ha engedélyezi egy bővítmény automatikus kiosztását, akkor a megfelelő telepítést akkor kell kiosztania, **Ha nem létezik** a házirend, hogy a bővítmény az adott típusú meglévő és jövőbeli erőforrásokon legyen kiépítve.

> [!TIP]
> További információ a Azure Policy-effektusokról, beleértve az üzembe helyezést, ha nem létezik [Azure Policy-effektusok értelmezése](../governance/policy/concepts/effects.md).


## <a name="enable-auto-provisioning-of-the-log-analytics-agent-and-extensions"></a>A log Analytics-ügynök és-bővítmények automatikus kiépítés engedélyezése <a name="auto-provision-mma"></a>

Ha az automatikus kiépítés be van kapcsolva a Log Analytics ügynöknél, Security Center üzembe helyezi az ügynököt az összes támogatott Azure-beli virtuális gépen és minden újonnan létrehozott rendszeren. A támogatott platformok listáját lásd: [Azure Security Center támogatott platformok](security-center-os-coverage.md).

Az log Analytics-ügynök automatikus kiépítés engedélyezése:

1. A Security Center menüjében válassza a **díjszabás & beállítások** lehetőséget.
1. Válassza ki az adott előfizetést.
1. Az **automatikus kiépítés** lapon állítsa be a log Analytics ügynök állapotát a következőre **:.**

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Az log Analytics-ügynök automatikus kiépítés engedélyezése":::

1. A konfigurációs beállítások panelen adja meg a használni kívánt munkaterületet.

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="A Log Analytics-ügynökök virtuális gépekre való automatikus kiépítési beállításainak konfigurációs beállításai" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **Csatlakoztassa az Azure-beli virtuális gépeket a Security Center-Security Center által létrehozott alapértelmezett munkaterülethez** , és hozzon létre egy új erőforráscsoportot és egy alapértelmezett munkaterületet ugyanabban a földrajzi helyben, és kapcsolja össze az ügynököt az adott munkaterülettel. Ha egy előfizetés több földrajzi helyről származó virtuális gépeket tartalmaz, Security Center több munkaterületet hoz létre az adatok adatvédelmi követelményeinek való megfelelés biztosítása érdekében.

        A munkaterület és az erőforráscsoport elnevezési konvenciója a következő: 
        - Munkaterület: DefaultWorkspace-[subscription-ID]-[geo] 
        - Erőforráscsoport: DefaultResourceGroup-[geo] 

        A Security Center automatikusan engedélyezi a Security Center megoldást a munkaterületen az előfizetéshez beállított díjszabási szinten. 

        > [!TIP]
        > Az alapértelmezett munkaterületekkel kapcsolatos kérdésekért lásd:
        >
        > - [Fizetnem kell Azure Monitor naplókat a Security Center által létrehozott munkaterületeken?](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [Hol jött létre az alapértelmezett Log Analytics munkaterület?](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [Törölhetem a Security Center által létrehozott alapértelmezett munkaterületeket?](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **Azure-beli virtuális gépek összekötése egy másik munkaterülettel** – a legördülő listából válassza ki a munkaterületet az összegyűjtött adatok tárolásához. A legördülő lista az összes előfizetése összes munkaterületét tartalmazza. Ezzel a lehetőséggel adatokat gyűjthet a különböző előfizetésekben futó virtuális gépekről, és az összeset tárolhatja a kiválasztott munkaterületen.  

        Ha már rendelkezik meglévő Log Analytics munkaterülettel, érdemes lehet ugyanazt a munkaterületet használni (olvasási és írási engedélyeket igényel a munkaterületen). Ez a beállítás akkor hasznos, ha a szervezet központi munkaterületét használja, és a biztonsági adatgyűjtés céljából szeretné használni. További információ a [naplózási és munkaterületekhez való hozzáférés kezelése Azure monitorban](../azure-monitor/logs/manage-access.md).

        Ha a kiválasztott munkaterülethez már engedélyezve van a "biztonság" vagy a "SecurityCenterFree" megoldás, a díjszabás automatikusan be lesz állítva. Ha nem, telepítsen egy Security Center megoldást a munkaterületre:

        1. A Security Center menüjében nyissa meg a **díjszabási & beállításait**.
        1. Válassza ki azt a munkaterületet, amelyhez csatlakoztatni kívánja az ügynököket.
        1. Válassza ki **Az Azure Defender vagy az** **Azure Defender kikapcsolását**.

1. A **Windows biztonsági események** konfigurációjában válassza ki a tárolni kívánt nyers esemény-adatok mennyiségét:
    - **Nincs** – a biztonsági események tárolójának letiltása. Ez az alapértelmezett beállítás.
    - **Minimal (minimális** ) – kis mennyiségű esemény, amelynél minimalizálni szeretné az esemény kötetét.
    - **Common (gyakori** ) – olyan események összessége, amelyek megfelelnek a legtöbb ügyfélnek, és teljes körű naplózási nyomvonalat biztosítanak.
    - **Minden esemény** – azon ügyfelek számára, akik az összes eseményt tárolni szeretnék.

    > [!TIP]
    > Ha ezeket a beállításokat a munkaterület szintjén szeretné megadni, tekintse meg a [biztonsági esemény beállítása a munkaterület szintjén](#setting-the-security-event-option-at-the-workspace-level)című témakört.
    > 
    > További információ ezekről a lehetőségekről: [a log Analytics ügynök Windows biztonsági eseményeinek beállításai](#data-collection-tier).

1. Válassza az **alkalmaz** lehetőséget a konfiguráció ablaktáblán.

1. A log Analytics ügynökön kívüli bővítmény automatikus kiépítés engedélyezése: 

    1. Ha engedélyezi az automatikus kiépítés a Microsoft függőségi ügynök számára, győződjön meg arról, hogy az log Analytics ügynök automatikus központi telepítésre van beállítva.
    1. A megfelelő bővítmény állapotának **bekapcsolása a következőre** :.

        :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="Váltás az automatikus kiépítés engedélyezéséhez a K8s házirend-bővítményéhez":::

    1. Kattintson a **Mentés** gombra. Az Azure-szabályzat hozzá van rendelve, és létrejön egy szervizelési feladat.

        |Mellék  |Szabályzat  |
        |---------|---------|
        |Kubernetes vonatkozó házirend-bővítmény|[Azure Policy bővítmény üzembe helyezése az Azure Kubernetes Service-fürtökön](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
        |Microsoft függőségi ügynök (előzetes verzió) (Windows rendszerű virtuális gépek)|[Windows rendszerű virtuális gépek függőségi ügynökének telepítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
        |Microsoft függőségi ügynök (előzetes verzió) (Linux rendszerű virtuális gépek)|[Függőségi ügynök telepítése Linux rendszerű virtuális gépekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|
        |||

1. Kattintson a **Mentés** gombra. Ha egy munkaterületet ki kell építeni, az ügynök telepítése akár 25 percet is igénybe vehet.

1. A rendszer megkérdezi, hogy szeretné-e újrakonfigurálni azokat a figyelt virtuális gépeket, amelyek korábban egy alapértelmezett munkaterülethez voltak csatlakoztatva:

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="A figyelt virtuális gépek újrakonfigurálására vonatkozó beállítások áttekintése":::

    - **Nem** – az új munkaterület-beállítások csak olyan újonnan felderített virtuális gépekre lesznek alkalmazva, amelyeken nincs telepítve a log Analytics ügynök.
    - **Igen** – az új munkaterület-beállítások minden virtuális gépre érvényesek lesznek, és a Security Center létrehozott munkaterülethez jelenleg kapcsolódó minden virtuális gép újra csatlakozik az új cél munkaterülethez.

   > [!NOTE]
   > Ha az **Igen** lehetőséget választja, ne törölje a Security Center által létrehozott munkaterületeket, amíg az összes virtuális gép újra nem csatlakozik az új cél munkaterülethez. A művelet meghiúsul, ha a munkaterület túl korán van törölve.


## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>A Windows biztonsági eseményeinek beállításai a Log Analytics ügynökhöz <a name="data-collection-tier"></a> 

Azure Security Center adatgyűjtési szintjeinek kiválasztása csak a Log Analytics munkaterületen lévő biztonsági események *tárolására* vonatkozik. A Log Analytics ügynök továbbra is összegyűjti és elemzi a Security Center veszélyforrások elleni védelemhez szükséges biztonsági eseményeket, függetlenül attól, hogy milyen biztonsági események vannak tárolva a munkaterületen. A biztonsági események tárolásának kiválasztása lehetővé teszi ezeknek az eseményeknek a vizsgálatát, keresését és naplózását a munkaterületen.

### <a name="requirements"></a>Követelmények 
A Windows biztonsági eseményeinek tárolásához Azure Defender szükséges. [További információ az Azure defenderről](azure-defender.md).

Az adatok Log Analyticsban való tárolása további díjakat eredményezhet az adattároláshoz. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.

### <a name="information-for-azure-sentinel-users"></a>Információk az Azure Sentinel-felhasználók számára 
Az Azure Sentinel felhasználói: vegye figyelembe, hogy egy adott munkaterület kontextusában a biztonsági események gyűjtése a Azure Security Center vagy az Azure Sentinel használatával konfigurálható, de nem mindkettő. Ha azt tervezi, hogy az Azure Sentinelt olyan munkaterülethez adja hozzá, amely már kap riasztásokat a Azure Security Centerból, és a biztonsági események gyűjtésére van beállítva, két lehetőség közül választhat:
- Hagyja meg Azure Security Center a biztonsági események gyűjteményét. Ezeket az eseményeket az Azure Sentinelben és az Azure Defenderben is lekérdezheti és elemezheti. Azonban nem fogja tudni figyelni az összekötő kapcsolati állapotát, vagy megváltoztatni a konfigurációját az Azure Sentinelben. Ha ez fontos az Ön számára, vegye figyelembe a második lehetőséget.
- Tiltsa le a biztonsági események gyűjtését Azure Security Centerban (a **Windows biztonsági eseményeinek** a log Analytics-ügynök konfigurációjában **nincs** értékre állításával). Ezután adja hozzá a Security Events-összekötőt az Azure Sentinel-ben. Ahogy az első lehetőség, az Azure Sentinel és az Azure Defender/ASC eseményeinek lekérdezésére és elemzésére is képes lesz, de mostantól nyomon követheti az összekötő kapcsolati állapotát, vagy megváltoztathatja a konfigurációját a-ben, és csak az Azure Sentinel-ben.

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>Milyen típusú események tárolódnak a "Common" és a "Minimal"?
Ezek a készletek a tipikus forgatókönyvek kezelésére lettek tervezve. Győződjön meg arról, hogy a megvalósítása előtt ki kell értékelnie az igényeinek megfelelőt.

A **gyakori** és **minimális** lehetőségek eseményeinek meghatározásához ügyfeleinkkel és iparági szabványokkal dolgozunk az egyes események szűrés nélküli gyakoriságának megismerése és a használatuk során. A folyamat során a következő irányelveket használtuk:

- **Minimális** – győződjön meg arról, hogy ez a készlet csak olyan eseményekre vonatkozik, amelyek egy nagyon alacsony kötettel rendelkező sikeres szabálysértést és fontos eseményeket jelezhetnek. Ez a készlet például a felhasználó sikeres és sikertelen bejelentkezését (4624, 4625) tartalmazza, de nem tartalmazza a kijelentkezést, amely fontos a naplózáshoz, de az észleléshez nem releváns, és viszonylag nagy mennyiségű. A készlet adatmennyiségének nagy része a bejelentkezési események és a folyamat-létrehozási esemény (4688-es azonosítójú esemény).
- **Common** – teljes felhasználói naplózási nyomvonalat adhat meg ebben a készletben. Ez a készlet például felhasználói bejelentkezéseket és felhasználói bejelentkezéseket is tartalmaz (4634-es azonosítójú esemény). Olyan naplózási műveleteket is tartalmaz, mint például a biztonsági csoport módosításai, a fő tartományvezérlő Kerberos-műveletei, valamint az iparági szervezetek által ajánlott egyéb események.

A nagyon alacsony kötettel rendelkező események a közös készletbe kerültek, mint az összes esemény kiválasztásának fő motivációja, hogy csökkentse a kötetet, és ne az adott események kiszűrésére.

Itt látható az egyes készletekhez tartozó biztonsági és alkalmazás-zárolási események azonosítóinak teljes részletezése:

| Adatréteg | Összegyűjtött események indikátorai |
| --- | --- |
| Minimális | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, |
| | 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| Közös | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, |
| | 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, |
| | 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, |
| | 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, |
| | 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, |
| | 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> - Ha Csoportházirend objektumot (GPO-t) használ, javasoljuk, hogy engedélyezze a naplózási házirendek folyamatát a 4688-as és a 4688-es eseményen belüli *parancssori* mezőben. További információ a 4688-es folyamat-létrehozási eseményről: Security Center [Gyakori kérdések](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled). További információ ezekről a naplózási házirendekről: a [naplórend ajánlásai](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Ha engedélyezni szeretné az adatgyűjtést az [adaptív alkalmazások vezérlőinek](security-center-adaptive-application.md)számára, Security Center egy helyi AppLocker-házirendet konfigurál a naplózási módban az összes alkalmazás engedélyezéséhez. Ez azt eredményezi, hogy az AppLocker az Security Center által összegyűjtött és kihasználható eseményeket hozza elő. Fontos megjegyezni, hogy ez a szabályzat nem lesz konfigurálva olyan gépeken, amelyeken már van konfigurált AppLocker-házirend. 
> - A Windows Filtering platform [5156-es azonosítójú eseményének](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)összegyűjtéséhez engedélyeznie kell a [naplózási szűrési platform kapcsolatát](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol készlet/Subcategory: "a platform kapcsolatának szűrése"/Success: Engedélyezés)
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>A biztonsági esemény beállításának beállítása a munkaterület szintjén

Meghatározhatja a munkaterület szintjén tárolt biztonsági események adatmennyiségét.

1. A Azure Portal Security Center menüjében válassza a **díjszabás & beállítások** lehetőséget.
1. Válassza ki a megfelelő munkaterületet. A munkaterületek egyetlen adatgyűjtési eseménye az ezen a lapon leírt Windows biztonsági események.

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="A biztonsági eseménynek a munkaterületen tárolni kívánt adatértékének beállítása":::

1. Válassza ki a tárolni kívánt nyers események mennyiségét, majd válassza a **Mentés** lehetőséget.

## <a name="manual-agent-provisioning"></a>Manuális ügynök kiépítés <a name="manual-agent"></a>
 
A Log Analytics-ügynök manuális telepítése:

1. Az automatikus kiépítés letiltása.

1. Szükség esetén létrehozhat egy munkaterületet.

1. Engedélyezze az Azure Defender szolgáltatást azon a munkaterületen, amelyre a Log Analytics-ügynököt telepíti:

    1. A Security Center menüjében válassza a **díjszabás & beállítások** lehetőséget.

    1. Állítsa be azt a munkaterületet, amelyre telepíteni kívánja az ügynököt. Győződjön meg arról, hogy a munkaterület ugyanahhoz az előfizetéshez tartozik, amelyet Security Center használ, és hogy rendelkezik írási/olvasási engedéllyel a munkaterülethez.

    1. Válassza **Az Azure Defender** lehetőséget, és **mentse** a következőt:.

       >[!NOTE]
       >Ha a munkaterülethez már engedélyezve van egy **biztonsági** vagy **SecurityCenterFree** megoldás, a díjszabás automatikusan be lesz állítva. 

1. Ha az ügynököket Resource Manager-sablonnal szeretné telepíteni az új virtuális gépeken, telepítse a Log Analytics-ügynököt:

   - [A Windows Log Analytics ügynökének telepítése](../virtual-machines/extensions/oms-windows.md)
   - [A Linux rendszerhez készült Log Analytics-ügynök telepítése](../virtual-machines/extensions/oms-linux.md)

1. Ha az ügynököket a meglévő virtuális gépekre szeretné telepíteni, kövesse az [adatok gyűjtése az Azure Virtual Machinesról](../azure-monitor/vm/quick-collect-azurevm.md) című szakaszt (az **esemény-és teljesítményadatok gyűjtése** nem kötelező).

1. Ha az ügynököket a PowerShell használatával szeretné telepíteni, használja a Virtual Machines dokumentációjának utasításait:

    - [Windowsos gépek esetén](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [Linuxos gépek esetén](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> A Security Center PowerShell használatával történő előkészítésével kapcsolatos útmutatásért lásd: [a Azure Security Center bevezetésének automatizálása a PowerShell használatával](security-center-powershell-onboarding.md).


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatikus kiépítés egy korábban létező ügynök telepítése esetén <a name="preexisting"></a> 

A következő használati esetek határozzák meg, hogy az automatikus kiépítési funkció milyen esetekben működik, ha már van telepítve ügynök vagy bővítmény. 

- **Log Analytics ügynök telepítve van a gépen, de nem bővítményként (közvetlen ügynökként)** – ha a log Analytics ügynök közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), Security Center telepíti a log Analytics-ügynök bővítményt, és a log Analytics-ügynököt a legújabb verzióra is frissítheti.
A telepített ügynök továbbra is jelentést készít a már konfigurált munkaterület (ek) ről, és a Security Center-ben konfigurált munkaterületnek is jelentést küld (a Windows rendszerű gépeken támogatott a többszörös vezérlési funkció).
Ha a konfigurált munkaterület egy felhasználói munkaterület (nem Security Center alapértelmezett munkaterülete), akkor telepítenie kell a "biztonság" vagy a "SecurityCenterFree" megoldást arra, hogy Security Center a munkaterületre jelentett virtuális gépekről és számítógépekről származó események feldolgozásának megkezdéséhez.

    Linux rendszerű gépek esetén az ügynök többsoros vezérlése még nem támogatott – ezért ha egy meglévő ügynök telepítését észleli, az automatikus kiépítés nem történik meg, és a gép konfigurációja nem módosul.

    Az előfizetésekben lévő, a 2019. március 17. előtt Security Center, a meglévő ügynök észlelése után a Log Analytics ügynök bővítmény nem lesz telepítve, és a gép nem lesz hatással. Ezen gépek esetében tekintse meg a "figyelési ügynök állapotával kapcsolatos problémák megoldása a gépeken" című javaslatot az ügynök telepítési problémáinak megoldásához ezeken a gépeken.
  
- **System Center Operations Manager ügynök telepítve van a gépen** – a Security Center a log Analytics Agent bővítményt a meglévő Operations Managerra telepíti. A meglévő Operations Manager ügynöke a szokásos módon folytatja a Operations Manager-kiszolgáló jelentését. A Operations Manager ügynök és Log Analytics ügynök közös futásidejű kódtárakat oszt meg, amelyek a folyamat során a legújabb verzióra lesznek frissítve. Ha a Operations Manager ügynök 2012-es verziója telepítve van **, ne engedélyezze** az automatikus kiépítés engedélyezését.

- Már létezik **egy meglévő** virtuálisgép-bővítmény:
    - Ha a figyelési ügynök bővítményként van telepítve, a bővítmény konfigurációja lehetővé teszi, hogy a jelentéskészítés csak egyetlen munkaterületre legyen elérhető. A Security Center nem bírálja felül a felhasználói munkaterületek meglévő kapcsolatait. Security Center a virtuális gépről a már csatlakoztatott munkaterületen tárolja a biztonsági adatait, ha a "biztonság" vagy a "SecurityCenterFree" megoldás telepítve van rajta. Security Center a folyamat legújabb verziójára frissítheti a bővítmény verzióját.
    - Ha szeretné látni, hogy a meglévő bővítmény mely munkaterületre küld adatokat, futtassa a tesztet a [Azure Security Center kapcsolatának ellenőrzéséhez](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center). Másik lehetőségként megnyithatja Log Analytics munkaterületeket, kiválaszthat egy munkaterületet, kiválaszthatja a virtuális gépet, és megtekintheti a Log Analytics-ügynök közötti kapcsolatokat.
    - Ha olyan környezettel rendelkezik, amelyben a Log Analytics ügynök telepítve van az ügyfél-munkaállomásokon, és jelentéskészítés egy meglévő Log Analytics munkaterületre, tekintse át az [Azure Security Center által támogatott operációs rendszerek](security-center-os-coverage.md) listáját, és győződjön meg arról, hogy az operációs rendszer támogatott. További információ: [meglévő log Analytics-ügyfelek](./faq-azure-monitor-logs.md).
 

## <a name="disable-auto-provisioning"></a>Automatikus kiépítés letiltása <a name="offprovisioning"></a>

Az automatikus kiépítés letiltásakor az ügynökök nem lesznek kiépítve az új virtuális gépeken.

Az ügynök automatikus kiépítési funkciójának kikapcsolásához:

1. A portál Security Center menüjében válassza a **díjszabás & beállítások** lehetőséget.
1. Válassza ki az adott előfizetést.
1. Válassza az **automatikus kiépítés** lehetőséget.
1. **Kapcsolja ki** az állapotot Off értékre a megfelelő ügynöknél.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Bekapcsolja az automatikus kiépítés letiltását az ügynök típusa szerint":::

1. Kattintson a **Mentés** gombra. Ha az automatikus kiépítés le van tiltva, az alapértelmezett munkaterület-konfiguráció szakasz nem jelenik meg:

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="Ha az automatikus kiépítés le van tiltva, a konfigurációs cella üres":::


> [!NOTE]
>  Az automatikus kiépítés letiltása nem távolítja el a Log Analytics ügynököt azon Azure-beli virtuális gépekről, amelyeken az ügynök ki lett építve. További információ a OMS-bővítmény eltávolításáról: [Hogyan Security Center által telepített OMS-bővítmények eltávolítása](faq-data-collection-agents.md#remove-oms).
>


## <a name="troubleshooting"></a>Hibaelhárítás

-   A telepítési problémák automatikus kiépítésének azonosításához lásd: az [ügynök állapotával kapcsolatos problémák elhárítása](security-center-troubleshooting-guide.md#mon-agent).
-  A figyelési ügynök hálózati követelményeinek azonosításához lásd: a [figyelési ügynök hálózati követelményeinek hibaelhárítása](security-center-troubleshooting-guide.md#mon-network-req).
-   A manuális előkészítési problémák azonosításához lásd: [az Operations Management Suite bevezetési problémáinak elhárítása](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).



## <a name="next-steps"></a>Következő lépések
Ez az oldal azt ismerteti, hogyan engedélyezhető az automatikus kiépítés a Log Analytics-ügynök és más Security Center-bővítmények számára. Azt is ismerteti, hogyan lehet definiálni egy Log Analytics munkaterületet, amelyben tárolni kívánja az összegyűjtött adatokat. Az adatgyűjtés engedélyezéséhez mindkét művelet szükséges. Az adatok tárolása Log Analyticsban, akár új, akár meglévő munkaterületet használ, az adattárolásra további díjak merülhetnek fel. A díjszabással kapcsolatos részletekért a választott pénznemben és a régiója szerint tekintse meg a [Security Center díjszabását](https://azure.microsoft.com/pricing/details/security-center/).