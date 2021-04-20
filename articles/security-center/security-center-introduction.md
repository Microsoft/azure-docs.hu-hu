---
title: Mi az az Azure Security Center?| Microsoft Docs
description: Ez az oldal Security Center legfontosabb előnyeit ismerteti – a biztonsági állapot felderítését, és a felhőbeli és a helyszíni erőforrások lefedettségének javításával.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 741cd68145b262c1f200ced9a7f28b25673b6925
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738917"
---
# <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?

Azure Security Center az infrastruktúrabiztonság egységes felügyeleti rendszere, amely megerősíti az adatközpontok biztonsági rendszerét, és fejlett fenyegetésvédelmet biztosít a hibrid felhőbeli számítási feladatok számára – akár az Azure-ban, akár nem – a helyszínen.

Az erőforrások biztonságának megtartása közös erőfeszítés a felhőszolgáltató, az Azure és Ön, illetve az ügyfél között. A felhőbe való áthelyezés során meg kell győződni a számítási feladatok biztonságáról, és ugyanakkor az IaaS-re (szolgáltatott infrastruktúrára) való áthelyezéskor nagyobb az ügyfelek felelőssége, mint a PaaS (szolgáltatásként biztosított platform) és az SaaS (szolgáltatott szoftver) esetében. Azure Security Center biztosítja a hálózat biztonságának, a szolgáltatások biztonságának és a biztonsági rendszer mindenképpen megfelelő biztonságának biztosítása érdekében szükséges eszközöket.

Az Azure Security Center a három legsürgetőbb biztonsági kihívásra reagál:

-   **Gyorsan változó számítási feladatok** – Ez a felhő erőssége és kihívása is. A felhasználók egyfelől több lehetőséggel rendelkeznek. Másfelől azonban hogyan tud meggyőződni arról, hogy a felhasználók által létrehozott és használt, folyamatosan változó szolgáltatósok megfelelnek az Ön biztonsági elvárásainak, és követik az ajánlott biztonsági eljárásokat?

-   **Egyre kifinomultabb támadások** – Bárhol is futtatja a számítási feladatokat, a támadások egyre kifinomultabbá válnak. Biztonságossá kell tennie a nyilvános felhőben található számítási feladatait, amelyek tulajdonképpen az interneten található feladatok, és az ajánlott biztonsági eljárások követése nélkül sebezhetővé teszik az Ön környezetét.

-   **Kevés a biztonsági szakértő** – A biztonsági riasztások és riasztási rendszerek száma jelentősen meghaladja a környezetek védelemmel való ellátásához szükséges háttértudással és tapasztalattal rendelkező rendszergazdák számát. Kihívást jelenthet naprakésznek maradni a legújabb támadásfajtákról, a folyamatosan változó biztonsági világban így szinte lehetetlen egy helyben maradni.

A Security Center eszközeivel megvédheti magát a fenti kihívásokkal szemben, és a következőket teheti:

-   **A biztonsági helyzet megerősítése:** Security Center felméri a környezetet, és lehetővé teszi az erőforrások állapotának és biztonságának a felmérését.

-   **Fenyegetésekkel szembeni** védelem: Security Center felméri a számítási feladatokat, és fenyegetés-megelőzési javaslatokat és biztonsági riasztásokat küld.

-   **Gyorsabb biztonság**: A Security Centerben minden a felhőkörnyezetek sebességével történik. A natív integrációnak köszönhetően a Security Center üzembe helyezése könnyű, ezután pedig kihasználhatja az automatikus kiépítés és az Azure-szolgáltatások nyújtotta védelem előnyeit.

> [!NOTE]
> Ez a szolgáltatás támogatja [a Azure Lighthouse,](../lighthouse/overview.md)amellyel a szolgáltatók bejelentkeznek a saját bérlőjükbe az ügyfelek által delegált előfizetések és erőforráscsoportok kezeléséhez. Az Azure Security Center esetén az előfizetést delegálni kell az egyes erőforráscsoportok helyett.

## <a name="architecture"></a>Architektúra

Mivel Security Center az Azure natív részét képezi, az Azure-beli PaaS-szolgáltatások – beleértve az Service Fabric-t, az SQL Database-t, a SQL Managed Instance-t és a tárfiókokat is – az Security Center figyelik és védik anélkül, hogy üzembe helyezésre lenne szükség.

Emellett a Security Center a nem Azure-beli kiszolgálókat és virtuális gépeket is védi a felhőben vagy a helyszínen, Windows- és Linux-kiszolgálók esetén is, ha telepíti rájuk a Log Analytics-ügynököt. Az Azure-beli virtuális gépek automatikusan ki vannak építve a Security Center.

Az ügynököktől és az Azure-ból gyűjtött események korrelálnak a biztonsági elemzési motorban, hogy személyre szabott javaslatokat (a tevékenységeket biztosítanak) biztosítsanak, és ezeket kövesse a számítási feladatok biztonságának és a biztonsági riasztások végrehajtásához. A lehető leghamarabb vizsgálja meg az ilyen riasztásokat, hogy biztosan ne legyenek rosszindulatú támadások a számítási feladatokon.

Ha engedélyezi Security Center szabályzatot, a Security Center beépített biztonsági szabályzat a Azure Policy beépített kezdeményezésként jelenik meg a Security Center kategóriában. A beépített kezdeményezés automatikusan hozzá lesz rendelve az összes Security Center előfizetéshez (függetlenül attól, hogy engedélyezve vannak-e Azure Defender vagy sem). A beépített kezdeményezés csak a naplózási szabályzatokat tartalmazza. A házirendek Security Center kapcsolatos Azure Policy lásd: Working [with security policies ..](tutorial-security-policy.md)

## <a name="strengthen-security-posture"></a>Biztonsági helyzet megerősítése

Azure Security Center lehetővé teszi a biztonsági rendszer megerősítését. Ez azt jelenti, hogy segít azonosítani és végrehajtani a biztonsági ajánlott eljárásként ajánlott, a biztonsági megoldásként javasolt biztonsági feladatokat, és ezeket implementálja a gépeken, az adatszolgáltatásokban és az alkalmazásokban. Ez magában foglalja a biztonsági szabályzatok felügyeletét és érvényesítését, valamint az Azure-beli virtuális gépek, a nem Azure-beli kiszolgálók és az Azure PaaS-szolgáltatások megfelelőnek való megfelelését. Security Center biztosítja az eszközöket, amelyek a munkaterhelések madártávra való megtekintéséhez, valamint a hálózati biztonsági tulajdonra összpontosító rálátást kínálnak. 

### <a name="manage-organization-security-policy-and-compliance"></a>Szervezeti biztonsági szabályzat és megfelelőség kezelése

A biztonsági alapszintű ismeretek és a számítási feladatok biztonságának biztosítása, amely a testre szabott biztonsági szabályzatok alkalmazásával kezdődik. Mivel a Security Center szabályzatok a Azure Policy-vezérlőkre épülnek, egy világosztályú szabályzatmegoldás teljes körű és rugalmas skáláját **és rugalmasságát kínálja.** A Security Center beállíthatja, hogy a szabályzatok felügyeleti csoportokon, előfizetések között vagy akár egy teljes bérlőn fusson.

:::image type="content" source="./media/security-center-intro/sc-dashboard.png" alt-text="Házirendkezelés oldal":::

Security Center segítségével azonosíthatja **az árnyék-it-előfizetéseket.** Ha az irányítópulton  nem szereplő előfizetéseket nézi meg, azonnal tudhatja, hogy mikor vannak újonnan létrehozott előfizetések, és győződjön meg arról, hogy a szabályzatok vonatkoznak rá, és az irányítópult Azure Security Center.

:::image type="content" source="./media/security-center-intro/sc-policy-dashboard.png" alt-text="Security Center szabályzat irányítópultja":::

### <a name="continuous-assessments"></a>Folyamatos értékelések

Security Center a Security Center folyamatosan felderíti a számítási feladatokban üzembe helyezett új erőforrásokat, és felméri, hogy a biztonsági ajánlott eljárásoknak megfelelően vannak-e konfigurálva, ha nem, akkor meg vannak-e jelölve, és megjelenik a javításhoz szükséges javaslatok rangsorolt listája a gépek védelme érdekében. A javaslatok ezen listáját az [Azure Biztonsági](https://docs.microsoft.com/security/benchmark/azure/introduction)teljesítményteszt (Microsoft által szerzői, Azure-specifikus) irányelvek engedélyezi és támogatja a közös megfelelőségi keretrendszeren alapuló biztonsági és megfelelőségi ajánlott eljárásokhoz. Ez a széles körben elterjedt teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) vezérlőire épül, és a felhőközpontú biztonságra összpontosít.

Annak érdekében, hogy jobban át tudja látni, mennyire fontosak az egyes javaslatok az általános  biztonsági Security Center a javaslatokat biztonsági vezérlőkbe csoportosan, és minden vezérlőhöz hozzáad egy biztonsági pontszámértéket. Ez elengedhetetlen a biztonsági munka **rangsorolásának lehetővé tételében.**

:::image type="content" source="./media/security-center-intro/sc-secure-score.png" alt-text="Security Center pontszám":::

### <a name="network-map"></a>Hálózati térkép

A hálózat biztonsági állapotának folyamatos figyelése érdekében Security Center hálózat egyik leghatékonyabb eszköze a **Hálózattérkép.** A térképen láthatja a számítási feladatok topológiáját, így láthatja, hogy az egyes csomópontok megfelelően vannak-e konfigurálva. Láthatja, hogyan csatlakoznak a csomópontok, ami segít blokkolni a nemkívánatos kapcsolatokat, ami megkönnyítheti a támadók számára a hálózatban való elsiklódást.

:::image type="content" source="./media/security-center-intro/sc-net-map.png" alt-text="Security Center hálózati térkép":::


### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>A biztonság optimalizálása és javítása az ajánlott vezérlők konfigurálásával

A Azure Security Center a javaslatokban rejlik. A javaslatok a számítási feladatokban talált konkrét biztonsági problémákra vannak szabva, és a Security Center a biztonsági rendszergazda nem csupán a biztonsági rések keresését, hanem konkrét utasításokat is biztosít a biztonsági rések megszabadítása érdekében.

Így a Security Center nem csupán biztonsági szabályzatokat állíthat be, hanem biztonságos konfigurációs szabványokat alkalmazhat az erőforrásokon.

A javaslatok segítenek csökkenteni az egyes erőforrások támadási felületét. Ez magában foglalja az Azure-beli virtuális gépeket, a nem Azure-kiszolgálókat, valamint az Olyan Azure PaaS-szolgáltatásokat, mint az SQL- és Storage-fiókok, és így tovább – ahol az egyes erőforrástípusokat másképp értékelik, és saját szabványokkal rendelkezik.

:::image type="content" source="./media/security-center-intro/sc-recommendation-example.png" alt-text="Security Center javaslat példája":::

## <a name="protect-against-threats"></a>Védelem a fenyegetésekkel szemben

Security Center által védett infrastruktúra (IaaS) rétegben, a nem Azure-beli kiszolgálókon, valamint az Azure platformszolgáltatásai (PaaS) esetében is észlelheti és megelőzheti a fenyegetéseket.

Security Center veszélyforrások elleni védelem magában foglalja az olvadásos támadási lánc elemzését, amely automatikusan korrelál a környezetben a kibertámadási lánc elemzése alapján, így jobban átlátja egy támadási kampány teljes történetét, amelyben elindult, és milyen hatással volt az erőforrásaira.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center biztonsági riasztások listája":::

### <a name="integration-with-microsoft-defender-for-endpoint"></a>Integráció a Microsoft Defender for Endpoint alkalmazással

Azure Defender kiszolgálókhoz való használata magában foglalja a Microsoft Defender for Endpoint automatikus, natív integrációját. További információ: Végpontok védelme a Security Center [integrált EDR-megoldásával: Microsoft Defender végponthoz](security-center-wdatp.md)


### <a name="protect-paas"></a>A PaaS védelme

Security Center segítségével észlelheti a fenyegetéseket az Azure PaaS-szolgáltatásokban. Észlelheti az Azure-szolgáltatásokat célzó fenyegetéseket, például Azure App Service, Azure SQL, Azure Storage-fiókokat és további adatszolgáltatásokat. Kihasználhatja az Microsoft Cloud App Security User and Entity Behavioral Analytics (UEBA) natív integrációját is az Azure-tevékenységnaplók anomáliadetektálásának végrehajtásához.

### <a name="block-brute-force-attacks"></a>Találgatásos támadások blokkolása

Security Center segít korlátozni a találgatásos támadásoknak való kitettséget. A virtuális gépek portjaihoz való hozzáférés csökkentésével, a virtuális gépekhez való megfelelő hozzáférés használatával a szükségtelen hozzáférés megakadályozásával is megsokosíthatja a hálózatot. A biztonságos hozzáférési szabályzatokat beállíthatja a kiválasztott portokon, csak a jogosult felhasználók, az engedélyezett forrás IP-címtartományok vagy IP-címek számára, valamint korlátozott ideig.

### <a name="protect-data-services"></a>Adatszolgáltatások védelme

Security Center olyan képességeket tartalmaz, amelyek segítenek az adatok automatikus besorolásában a Azure SQL. Emellett felméréseket is kaphat a biztonsági rések Azure SQL storage-szolgáltatásokban, valamint javaslatokat kaphat azok enyhítésére.

## <a name="get-secure-faster"></a>Gyorsabb biztonság

A natív Azure-integráció (beleértve a Azure Policy- és Azure Monitor-naplókat) és más Microsoft biztonsági megoldásokkal ( például az Microsoft Cloud App Security-hez és a Végponthoz a Microsoft Defenderhez) való zökkenőmentes integrációval segít abban, hogy a biztonsági megoldás átfogó legyen, valamint egyszerű legyen a bevezetés és bevezetés.

Emellett kiterjesztheti a teljes megoldást az Azure-on kívül a más felhőkben és helyszíni adatközpontokban futó számítási feladatokra is.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Azure-erőforrások automatikus felderítése és a használatbavés

Security Center zökkenőmentes, natív integrációt biztosít az Azure-erőforrásokkal és az Azure-erőforrásokkal. Ez azt jelenti, hogy egy teljes biztonsági történetet hozhat létre, amely Azure Policy és az összes Azure-erőforrás beépített Security Center-szabályzatait is magában foglalja, és meg kell győződni arról, hogy a rendszer automatikusan alkalmazza a teljes dolgot az újonnan felfedezett erőforrásokra, amikor létrehozza őket az Azure-ban.

Részletes naplógyűjtés – A Windows és a Linux rendszer naplóit mind a biztonsági elemzési motor használja, és javaslatok és riasztások létrehozásához használható.

## <a name="next-steps"></a>Következő lépések

- A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).

- Security Center ingyenes tarifacsomagja engedélyezve van az összes aktuális Azure-előfizetésén, miután először felkeresi az Azure Security Center irányítópultját az Azure Portal-ban, vagy ha programozott módon, API-n keresztül engedélyezi. A fejlett biztonsági felügyeleti és fenyegetésészlelési képességek előnyeinek kihasználás érdekében engedélyeznie kell a Azure Defender. Azure Defender 30 napig ingyenesen próbálkozhat. További információkért tekintse meg a [Security Center díjszabási lapját](https://azure.microsoft.com/pricing/details/security-center/).

- Ha készen áll a Azure Defender engedélyezésére, rövid [útmutató:](security-center-get-started.md) Azure Security Center lépések végigvezetik.