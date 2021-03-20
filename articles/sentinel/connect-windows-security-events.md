---
title: A Windows biztonsági eseményeinek összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogy a biztonsági események összekötővel hogyan továbbíthatja a Windows rendszerű rendszerek összes biztonsági eseményét az Azure Sentinel-munkaterületre.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: bc75ee64174957ad6486146b4da6f8a66a2120e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570556"
---
# <a name="connect-windows-security-events"></a>A Windows biztonsági eseményeinek csatlakoztatása 

A Security Events Connector lehetővé teszi, hogy a Windows rendszerű (kiszolgálók és munkaállomások, fizikai és virtuális) összes biztonsági eseményét továbbítsa az Azure Sentinel-munkaterületre. Ez lehetővé teszi, hogy megtekintse a Windows biztonsági eseményeket az irányítópultokon, hogy azok az egyéni riasztások létrehozásakor használhatók legyenek, és a vizsgálatok javítására támaszkodjon, így jobban betekintést nyerhet a szervezet hálózatára, és bővítheti biztonsági működési képességeit. Kiválaszthatja, hogy mely eseményeket kívánja továbbítani a következő készletek közül: <a name="event-sets"></a>

- **Minden esemény** – minden Windows-biztonsági és AppLocker-esemény.
- **Common (általános** ) – a naplózási célokra szolgáló szabványos események. Ebben a készletben a teljes felhasználói naplózási nyomvonal szerepel. Például a felhasználói bejelentkezési és a felhasználói kijelentkezési eseményeket is tartalmazza (eseményazonosító: 4624, 4634). Vannak olyan naplózási műveletek is, mint például a biztonsági csoport módosításai, a kulcs tartományvezérlő Kerberos-műveletei és más típusú események az elfogadott ajánlott eljárásokkal összhangban.

    A **Common** Event set olyan típusú eseményeket tartalmazhat, amelyek nem annyira gyakoriak.  Ennek az az oka, hogy a **közös** készlet lényege, hogy az események mennyiségét jobban kezelhető szintre csökkentse, miközben a teljes naplózási nyomvonalat továbbra is fenntartja.

- **Minimális** – a lehetséges fenyegetéseket jelző események kis halmaza. Ez a készlet nem tartalmaz teljes naplózási nyomvonalat. Csak azokra az eseményekre vonatkozik, amelyek a sikeres jogsértést jelezhetik, és olyan fontos eseményeket is tartalmaz, amelyek nagyon alacsony arányban fordulnak elő. Például sikeres és sikertelen felhasználói bejelentkezéseket tartalmaz (4624, 4625), de nem tartalmaz olyan kijelentkezési adatokat (4634), amelyek a naplózás szempontjából fontosak, de nem értelmezik az észlelést, és viszonylag nagy mennyiségű adatot tartalmaznak. A készlet adatmennyiségének nagy része bejelentkezési eseményből és folyamat-létrehozási eseményből áll (4688-es AZONOSÍTÓJÚ esemény).

- **Nincs** – nincsenek biztonsági vagy AppLocker-események. (Ezzel a beállítással lehet letiltani az összekötőt.)

    Az alábbi lista az egyes készletekhez tartozó biztonsági és alkalmazás-zárolási események azonosítóinak teljes részletezését tartalmazza:

    | Esemény-készlet | Összegyűjtött események azonosítói |
    | --- | --- |
    | **Minimális** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033 |
    | **Közös** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720 , 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946 , 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> A biztonsági események gyűjteménye egy adott munkaterület kontextusában konfigurálható Azure Security Center vagy Azure Sentinel rendszerből, de nem mindkettőn keresztül. Ha az Azure Sentinelt olyan munkaterületen helyezi üzembe, amely már beolvassa az Azure Defender-riasztásokat a Azure Security Centerból, és biztonsági események gyűjtésére van beállítva, két lehetőség közül választhat:
> - Hagyja meg Azure Security Center a biztonsági események gyűjteményét. Ezeket az eseményeket az Azure Sentinelben és az Azure Defenderben is lekérdezheti és elemezheti. Azonban nem fogja tudni figyelni az összekötő kapcsolati állapotát, vagy megváltoztatni a konfigurációját az Azure Sentinelben. Ha ez fontos az Ön számára, vegye figyelembe a második lehetőséget.
>
> - [Tiltsa le a biztonsági események gyűjtését](../security-center/security-center-enable-data-collection.md) Azure Security Centerban, és csak ezután adja hozzá a Security Events-összekötőt az Azure Sentinel-ben. Ahogy az első lehetőség, az Azure Sentinel és az Azure Defender/ASC eseményeinek lekérdezésére és elemzésére is képes lesz, de mostantól nyomon követheti az összekötő kapcsolati állapotát, vagy megváltoztathatja a konfigurációját a-ben, és csak az Azure Sentinel-ben.

## <a name="set-up-the-windows-security-events-connector"></a>A Windows biztonsági események összekötő beállítása

Windowsos biztonsági események összegyűjtése az Azure Sentinelben:

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget. Az összekötők listájában kattintson a **biztonsági események** elemre, majd a jobb alsó sarokban található **összekötő megnyitása lap** gombra. Ezután kövesse az **utasítások** lapon látható képernyőn megjelenő utasításokat a szakasz további részében leírtak szerint.

1. Ellenőrizze, hogy rendelkezik-e a megfelelő engedélyekkel az összekötő lapon az **Előfeltételek** részben leírtak szerint.

1. Töltse le és telepítse a [log Analytics-ügynököt](../azure-monitor/agents/log-analytics-agent.md) (más néven Microsoft monitoring agentet vagy MMA-t) azon gépekre, amelyeken biztonsági eseményeket szeretne továbbítani az Azure Sentinel szolgáltatásba.

    Azure-Virtual Machines esetén:
    
    1. Kattintson az **ügynök telepítése az Azure Windows rendszerű virtuális gépen** lehetőségre, majd az alább megjelenő hivatkozásra.
    1. Minden csatlakozni kívánt virtuális géphez kattintson a nevére a jobb oldalon megjelenő listában, majd kattintson a **Kapcsolódás** elemre.

    Nem Azure-beli Windows rendszerű gépek esetén (fizikai, virtuális helyszíni vagy virtuális egy másik felhőben):

    1. Kattintson az **ügynök telepítése nem Azure-beli Windows-gépen** elemre, majd az alább megjelenő hivatkozásra.
    1. Kattintson a jobb oldalon, a **Windows rendszerű számítógépek** alatt megjelenő megfelelő letöltési hivatkozásokra.
    1. A letöltött végrehajtható fájl használatával telepítse az ügynököt az Ön által választott Windows-rendszerekre, és konfigurálja azt a **munkaterület-azonosító és** a fent említett letöltési hivatkozások alatt megjelenő kulcsok használatával.

    > [!NOTE]
    >
    > Annak engedélyezéséhez, hogy a Windows rendszerű rendszerek ne legyenek a szükséges internetkapcsolattal az Azure Sentinelhez, töltse le és telepítse a **OMS-átjárót** egy külön gépre, a jobb alsó sarokban lévő hivatkozásra kattintva, hogy proxyként működjön.  Továbbra is telepítenie kell a Log Analytics-ügynököt minden olyan Windows rendszerre, amelynek eseményeit össze szeretné gyűjteni.
    >
    > Erről a forgatókönyvről a [ **log Analytics átjáró** dokumentációjában](../azure-monitor/agents/gateway.md)talál további információt.

    További telepítési lehetőségekért és további részletekért tekintse meg az [ **log Analytics ügynök** dokumentációját](../azure-monitor/agents/agent-windows.md).

1. Válassza ki, hogy melyik eseményazonosító ([az összes, a Common vagy a minimum](#event-sets)) legyen továbbítva.

1. Kattintson a **Frissítés** parancsra.

1. Ha a Windows biztonsági eseményeihez a Log Analytics vonatkozó sémát szeretné használni, írja be a következőt `SecurityEvent` a lekérdezési ablakba:.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Előfordulhat, hogy körülbelül 20 percet vesz igénybe, amíg a naplók meg nem jelennek a Log Analyticsban. 

### <a name="configure-the-security-events-connector-for-anomalous-rdp-login-detection"></a>A biztonsági események összekötő konfigurálása rendellenes RDP-bejelentkezés észleléséhez

> [!IMPORTANT]
> A rendellenes RDP-bejelentkezési észlelés jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Sentinel gépi tanulást (ML) alkalmazhat a biztonsági események adataira a rendellenes RDP protokoll (RDP) bejelentkezési tevékenységek azonosításához. A forgatókönyvek a következők:

- **Szokatlan IP** – az IP-címet az elmúlt 30 napban ritkán vagy soha nem észlelték

- **Szokatlan földrajzi hely** – az elmúlt 30 napban az IP-cím, a város, az ország és az ASN nem észlelhető

- **Új felhasználó** – az új felhasználó egy IP-címről és földrajzi helyről jelentkezik be, és mindkettőt nem várták a 30 nappal korábbi adatok alapján.

**Konfigurációs utasítások**

1. Az RDP bejelentkezési adatok (eseményazonosító: 4624) gyűjtését a **biztonsági események** adatösszekötőn keresztül kell gyűjteni. Győződjön meg arról, hogy a "None" érték mellett kiválasztott egy [eseményt](#event-sets) az Azure sentinelbe való továbbításhoz.

1. Az Azure Sentinel-portálon kattintson az **elemzés** elemre, majd kattintson a **szabály sablonok** fülre. Válassza ki az **(előzetes verzió) rendellenes RDP-bejelentkezés észlelési** szabályát, és **engedélyezze** az **állapot** csúszkát.

    > [!NOTE]
    > Mivel a gépi tanulási algoritmus 30 napos adatmennyiséget igényel a felhasználói viselkedés alapkonfigurációjának összeállításához, az incidensek észlelése előtt 30 napig kell engedélyeznie a biztonsági események gyűjtését.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztathatók a Windows biztonsági eseményei az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerkedjen meg a fenyegetések észlelésével az Azure Sentinel használatával, [beépített](tutorial-detect-threats-built-in.md) vagy [Egyéni](tutorial-detect-threats-custom.md) szabályokkal.

