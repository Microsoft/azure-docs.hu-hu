---
title: Biztonságos alkalmazások tervezése Microsoft Azure
description: Ez a cikk a webalkalmazás-projekt követelmény-és kialakítási fázisaiban megfontolandó ajánlott eljárásokat ismerteti.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 743412b7602e5781911cdf190e41a5ee15bfddd4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487677"
---
# <a name="design-secure-applications-on-azure"></a>Biztonságos alkalmazások tervezése az Azure-ban
Ebben a cikkben a Felhőbeli alkalmazások tervezésekor megfontolandó biztonsági tevékenységeket és vezérlőket mutatjuk be. A Microsoft [biztonsági fejlesztési életciklus (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) követelményei és tervezési szakaszaiban figyelembe venni kívánt biztonsági kérdések és fogalmak betanítása, valamint az erőforrások képzése. A cél az, hogy segítséget nyújtson olyan tevékenységek és Azure-szolgáltatások definiálásához, amelyeket a biztonságosabb alkalmazások kialakításához használhat.

Ebben a cikkben a következő SDL-fázisokat tárgyaljuk:

- Képzés
- Követelmények
- Tervezés

## <a name="training"></a>Képzés
Mielőtt megkezdené a felhőalapú alkalmazás fejlesztését, szánjon időt az Azure biztonságának és adatvédelmének megismerésére. Ennek a lépésnek a segítségével csökkentheti a kihasználható biztonsági rések számát és súlyosságát az alkalmazásban. Az állandóan változó fenyegetést jelentő tájképre való megfelelő reagálásra készen áll.

A betanítási fázisban a következő erőforrásokkal megismerkedhet a fejlesztők számára elérhető Azure-szolgáltatásokkal és az Azure-ban ajánlott biztonsági eljárásokkal:

  - Az [Azure fejlesztői útmutatója](https://azure.microsoft.com/campaigns/developer-guide/) bemutatja, hogyan kezdheti meg az Azure-t. Az útmutató bemutatja, hogy mely szolgáltatásokat használhatja az alkalmazások futtatásához, az adatok tárolásához, az intelligenciához, a IoT-alkalmazások készítéséhez és a megoldások hatékonyabb és biztonságos megoldásához.

  - Az [első lépések útmutatója az Azure-fejlesztőknek](../../guides/developer/azure-developer-guide.md) alapvető információkat nyújt azokról a fejlesztőknek, akik az Azure platform fejlesztési igényeiknek való használatának megkezdéséhez keresnek.

  - Az [SDK-k és eszközök](../../index.yml?pivot=sdkstools) az Azure-ban elérhető eszközöket írják le.

  - Az [Azure DevOps Services](/azure/devops/) fejlesztési együttműködési eszközöket biztosít. Az eszközök közé tartozik a nagy teljesítményű folyamatok, az ingyenes git-adattárak, a konfigurálható Kanban-táblák, valamint a kiterjedt automatizált és felhőalapú terheléses tesztelés.
    A [DevOps Resource Center](/azure/devops/learn/) a tanulási DevOps gyakorlatok, a git-verziókövetés, az agilis módszerek, a Microsoft DevOps és a saját DevOps-előrehaladás értékelésének módjával ötvözi a forrásokat.

  - Az [éles környezetbe való továbbítás előtt megfontolandó 5 legfontosabb biztonsági elem](/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) azt mutatja be, hogy miként védheti meg webalkalmazásait az Azure-ban, és megvédheti alkalmazásait a leggyakoribb és a veszélyes webalkalmazás-támadásokkal szemben.

  - Az Azure-hoz készült [Secure DevOps Kit](https://azsk.azurewebsites.net/index.html) olyan parancsfájlok, eszközök, bővítmények és automatizálások gyűjteménye, amelyek a kiterjedt automatizálást használó DevOps-csapatok átfogó Azure-előfizetési és erőforrás-biztonsági igényeit is kielégítik. Az Azure-hoz készült Secure DevOps Kit megmutathatja, hogyan integrálhatja zökkenőmentesen a biztonságot a natív DevOps-munkafolyamataiba. A csomag olyan eszközöket használ, mint például a biztonsági ellenőrző tesztek (SVTs), amelyek segítségével a fejlesztők biztonságos kódot írhatnak, és a kódolási és korai fejlesztési fázisokban ellenőrizhetik a felhőalapú alkalmazásaik biztonságos konfigurációját.

  - Az [Azure-megoldásokkal kapcsolatos ajánlott biztonsági eljárások](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) az Azure-ban a felhőalapú megoldások tervezésekor, üzembe helyezéséhez és kezeléséhez használt biztonsági eljárások gyűjteményét biztosítják.

## <a name="requirements"></a>Követelmények
A követelmények definíciós fázisa kulcsfontosságú lépés annak meghatározásához, hogy az alkalmazás mit tesz, és mit fog tenni a kiadásakor. A követelmények szakasza arra is vonatkozik, hogy milyen biztonsági ellenőrzéseket kell létrehoznia az alkalmazásba. Ebben a fázisban azt is megkezdi, hogy az összes SDL-ben elvégezze a biztonságos alkalmazások kiadását és üzembe helyezését.

### <a name="consider-security-and-privacy-issues"></a>Biztonsági és adatvédelmi problémák megfontolása
Ez a fázis a legjobb alkalom az alapvető biztonsági és adatvédelmi problémák megfontolására. A projekt elején a biztonság és adatvédelem elfogadható szintjeinek meghatározása segíti a csapatot:

- A biztonsági problémákkal kapcsolatos kockázatok megismerése.
- Azonosítsa és javítsa ki a biztonsági hibákat a fejlesztés során.
- A teljes projekten belül a biztonsági és adatvédelmi szinteket is alkalmazhatja.

Az alkalmazás követelményeinek megírásakor ügyeljen arra, hogy olyan biztonsági vezérlőket vegyen figyelembe, amelyek segíthetnek az alkalmazás és az adatbiztonság megőrzésében.

### <a name="ask-security-questions"></a>Biztonsági kérdések megkérdezése
Tegye fel a következő biztonsági kérdéseket:

  - Az alkalmazásom bizalmas adatokat tartalmaz?

  - Az alkalmazás olyan adatokat gyűjt vagy tárol, amelyeknek meg kell felelniük az iparági szabványoknak és a megfelelőségi programoknak, mint például a [szövetségi pénzügyi intézmény-típusvizsgálati Tanács (FFIEC)](/previous-versions/azure/security/blueprints/ffiec-analytics-overview) vagy a [Payment Card Industry adatbiztonsági szabványai (PCI DSS)](/previous-versions/azure/security/blueprints/pcidss-analytics-overview)?

  - Az alkalmazásom bizalmas személyes vagy vásárlói adatokat gyűjt vagy tartalmaz, amelyek akár saját, akár egyéb információkkal is használhatók egy személy azonosítására, kapcsolatfelvételére vagy megkeresésére?

  - Az alkalmazásom olyan adatokat gyűjt vagy tartalmaz, amelyek az egyén orvosi, oktatási, pénzügyi vagy foglalkoztatási adataihoz való hozzáféréshez használhatók? A követelmények szakasza alapján azonosíthatja az adataik érzékenységét, és azonosíthatja az alkalmazásához használni kívánt adatvédelmi módszert.

  - Hol és hogyan történik az adataim tárolása? Gondolja át, hogyan fogja figyelni az alkalmazás által a váratlan változásokhoz (például a lassabb válaszidő) használt tárolási szolgáltatásokat. Képes lesz befolyásolni a naplózást, hogy részletesebb adatokat gyűjtsön, és részletesen elemezze a problémát?

  - Elérhető lesz-e az alkalmazás a nyilvános (interneten) vagy csak belsőleg? Ha az alkalmazása elérhető a nyilvánosság számára, hogyan védhető meg a nem megfelelő módon gyűjtött adatok? Ha az alkalmazás csak belsőleg érhető el, vegye figyelembe, hogy a szervezeten belül kik férhetnek hozzá az alkalmazáshoz, és hogy mennyi ideig legyenek elérhetők.

  - Megérti az identitás modelljét, mielőtt megkezdené az alkalmazás megtervezését? Hogyan állapítja meg, hogy kik a felhasználók, és mit jogosult a felhasználó?

  - Az alkalmazásom érzékeny vagy fontos feladatokat végez (például a pénz átruházása, az ajtók felszabadítása vagy a gyógyszer kézbesítése)?
    Gondolja át, hogyan fogja érvényesíteni, hogy a bizalmas feladatot ellátó felhasználó jogosult-e a feladat végrehajtására, és hogy Ön hogyan fogja hitelesíteni azt a személyt, akit ők mondanak. Az engedélyezés (AuthZ) az a művelet, amely egy hitelesített rendszerbiztonsági tag engedélyének megadására szolgál. A hitelesítés (AuthN) az a jogi személy, aki a jogszerű hitelesítő adatokkal kapcsolatos kihívást jelent.

  - Az alkalmazásom bármilyen kockázatos szoftverrel kapcsolatos tevékenységet végez, például lehetővé teszi a felhasználók számára fájlok vagy más adatok feltöltését vagy letöltését? Ha az alkalmazása kockázatos tevékenységeket végez, gondolja át, hogy az alkalmazás hogyan fogja védelemmel ellátni a felhasználókat a kártékony fájlok vagy adatok kezelésében.

### <a name="review-owasp-top-10"></a>A OWASP áttekintése – első 10
Érdemes áttekinteni a [<span class="underline">OWASP első 10 alkalmazás biztonsági kockázatait</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
A OWASP Top 10 a webalkalmazások kritikus fontosságú biztonsági kockázatait kezeli.
Ezeknek a biztonsági kockázatoknak a megismerése segíthet olyan követelmény-és kialakítási döntések meghozatalában, amelyek az alkalmazásban a kockázatok minimalizálására használhatók.

Fontos, hogy a biztonsági ellenőrzésekkel ne kelljen megelőzni a behatolást.
Azonban azt is [feltételezi, hogy a rendszer megszegi a](/azure/devops/learn/devops-at-microsoft/security-in-devops) felmerülő problémákat. A szabálysértést feltételezve, hogy a biztonsággal kapcsolatos fontos kérdéseket előre megválaszolja, ezért nem kell sürgősen megválaszolnia:

  - Hogyan történik a támadás észlelése?

  - Mi a teendő, ha támadás vagy szerződésszegés történt?

  - Hogyan lehet helyreállítani a támadásokat, például az adatszivárgást vagy a módosítást?

## <a name="design"></a>Tervezés

A tervezési fázis kritikus fontosságú a tervezési és funkcionális specifikációkkal kapcsolatos ajánlott eljárások kialakításához. Emellett kritikus fontosságú a kockázatkezelési feladatok elvégzésében is, amelyek segítenek enyhíteni a teljes projekt biztonsági és adatvédelmi problémáit.

Ha biztonsági követelmények vannak érvényben, és biztonságos tervezési fogalmakat használ, elkerülheti vagy lecsökkentheti a biztonsági hibák lehetőségeit. A biztonsági hiba az alkalmazás kialakításának olyan felügyelete, amely lehetővé teheti, hogy a felhasználó rosszindulatú vagy váratlan műveleteket hajtson végre az alkalmazás megjelenése után.

A tervezési fázisban azt is érdemes meggondolni, hogyan alkalmazhat biztonságot a rétegekben; az egyik védelmi szint nem feltétlenül elég. Mi történik, ha egy támadó átveszi a webalkalmazási tűzfalat (WAF)? Azt szeretné, hogy a támadás ellen védelmet biztosítson egy másik biztonsági ellenőrzés.

Ezt szem előtt tartva a következő biztonságos tervezési fogalmakat és a biztonságos alkalmazások tervezésekor szükséges biztonsági ellenőrzéseket tárgyaljuk:

- Használjon biztonságos kódolási függvénytárat és szoftver-keretrendszert.
- A sebezhető összetevők vizsgálata.
- A veszélyforrások modellezése az alkalmazás tervezésekor.
- Csökkentse a támadási felületet.
- Az identitás szabályzatának elfogadása elsődleges biztonsági területként.
- A fontos tranzakciók ismételt hitelesítésének megkövetelése.
- A kulcsok, a hitelesítő adatok és más titkok védelméhez használjon kulcskezelő megoldást.
- Bizalmas adatok védelme.
- Nem biztonságos mértékek implementálása.
- Használja ki a hibák és a kivételek kezelését.
- Naplózás és riasztás használata.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Biztonságos kódolási függvénytár és szoftveres keretrendszer használata

A fejlesztéshez használjon egy biztonságos kódolási könyvtárat és egy beágyazott biztonsági szoftvert tartalmazó szoftver-keretrendszert. A fejlesztők használhatnak meglévő, bevált funkciókat (titkosítás, bemeneti higiénia, kimeneti kódolás, kulcsok vagy a kapcsolatok karakterlánca, valamint minden más, ami biztonsági ellenőrzésnek lenne tekinthető) ahelyett, hogy a biztonsági ellenőrzéseket a semmiből kellene fejleszteni. Ez segítséget nyújt a biztonsággal kapcsolatos tervezési és megvalósítási hibák elleni védelemben.

Ügyeljen arra, hogy a keretrendszer legújabb verzióját és a keretrendszerben elérhető összes biztonsági funkciót használja. A Microsoft széles körű [fejlesztői eszközöket](https://azure.microsoft.com/product-categories/developer-tools/) kínál minden fejlesztőnek, bármilyen platformon vagy nyelven dolgozhat a felhőalapú alkalmazások biztosításához. Különböző [SDK](https://azure.microsoft.com/downloads/)-k közül választhatja ki a kívánt nyelvet.
Kihasználhatja a fejlett hibakeresési képességekkel és beépített Azure-támogatással rendelkező teljes funkcionalitású integrált fejlesztői környezeteket és szerkesztőket.

A Microsoft számos különböző [nyelvet, keretrendszert és eszközt](../../index.yml?panel=sdkstools-all&pivot=sdkstools) kínál az Azure-beli alkalmazások fejlesztéséhez. Ilyen például az [Azure .net-és .net Core-fejlesztőknek](/dotnet/azure/). Az általunk kínált nyelvekhez és keretrendszerekhez rövid útmutatók, oktatóanyagok és API-referenciák találhatók, amelyek segítenek a gyors kezdésben.

Az Azure számos szolgáltatást kínál, amelyekkel webhelyeket és webalkalmazásokat futtathat. Ezek a szolgáltatások lehetővé teszik a fejlesztést kedvenc nyelvén, legyen az .NET, .NET Core, Java, Ruby, Node.js, PHP vagy Python.
[Azure App Service Web Apps](../../app-service/overview.md) (Web Apps) egyike ezeknek a szolgáltatásoknak.

Web Apps a Microsoft Azure erejét hozzáadja az alkalmazáshoz. Ez magában foglalja a biztonságot, a terheléselosztást, az automatikus skálázást és az automatizált felügyeletet. Emellett kihasználhatja Web Apps DevOps funkcióit, például a csomagkezelő, az átmeneti környezetek, az egyéni tartományok, az SSL/TLS-tanúsítványok és az Azure DevOps, a GitHub, a Docker hub és más források folyamatos üzembe helyezését.

Az Azure más szolgáltatásokat is kínál, amelyek segítségével webhelyeket és webalkalmazásokat futtathat. A legtöbb forgatókönyvhöz a Webalkalmazások a legjobb választás. A Micro Service-architektúra esetében érdemes megfontolni az [Azure-Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Ha a kódot futtató virtuális gépek nagyobb mértékű felügyeletére van szüksége, akkor érdemes megfontolnia az [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) használatát.
További információ az Azure-szolgáltatások közötti választásról: [Azure app Service, Virtual Machines, Service Fabric és Cloud Services összehasonlítása](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Frissítések alkalmazása összetevőkön

A biztonsági rések megelőzése érdekében folyamatosan leltárba kell vennie az ügyféloldali és a kiszolgálóoldali összetevőket (például keretrendszerek és könyvtárak), valamint a frissítések függőségeit. Az új biztonsági rések és a frissített szoftverek folyamatosan jelennek meg. Ellenőrizze, hogy van-e folyamatos terve a használt könyvtárak és összetevők figyelésére, osztályozására és alkalmazására.

Tekintse meg a [Web Application Security Project (OWASP) megnyitása](https://www.owasp.org/index.php/Main_Page) című lapot az [ismert biztonsági réseket használó összetevők használatával](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) kapcsolatos javaslatokért. Előfizethet az e-mailes riasztásokra is a használt összetevőkkel kapcsolatos biztonsági rések esetében.

### <a name="use-threat-modeling-during-application-design"></a>Veszélyforrások modellezése az alkalmazás tervezésekor

A veszélyforrások modellezése a potenciális biztonsági fenyegetések azonosításának folyamata a vállalat és az alkalmazás számára, majd a megfelelő enyhítések biztosítása. Az SDL azt adja meg, hogy a csapatok a tervezési fázisban a veszélyforrások modellezését veszik igénybe, amikor a lehetséges problémák megoldása viszonylag egyszerű és költséghatékony. A veszélyforrások modellezése a tervezési fázisban nagy mértékben csökkentheti a teljes fejlesztési költségeket.

A veszélyforrások modellezési folyamatának elősegítése érdekében az [SDL-Threat Modeling Toolt](threat-modeling-tool.md) a nem biztonsági szakértők szem előtt tartásával alakítottuk ki. Ez az eszköz megkönnyíti a veszélyforrások modellezését minden fejlesztő számára azáltal, hogy egyértelmű útmutatást biztosít a veszélyforrások létrehozásához és elemzéséhez.

Az alkalmazások tervezésének modellezése és a [Stride](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) -fenyegetések számbavétele – a hamisítás, a hamisítás, a Megtagadás, az információk közzététele, a szolgáltatásmegtagadás és a jogosultsági szint megemelése – az összes megbízhatósági határ tekintetében hatékony módszert jelent a tervezési hibák kifogására a korai fázisban. A következő táblázat felsorolja a Stride-fenyegetéseket, és példákat tartalmaz az Azure által biztosított szolgáltatások használatára. Ezek a enyhítések nem fognak működni minden helyzetben.

| Fenyegetés | Biztonsági tulajdonság | Az Azure platform lehetséges enyhítése |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Identitáshamisítás               | Hitelesítés        | [HTTPS-kapcsolatok megkövetelése](/aspnet/core/security/enforcing-ssl?tabs=visual-studio&view=aspnetcore-2.1). |
| Illetéktelen adatmódosítás              | Integritás             | Ellenőrizze az SSL/TLS-tanúsítványokat. Az SSL/TLS protokollt használó alkalmazásoknak teljes mértékben ellenőriznie kell azon entitások X. 509 tanúsítványait, amelyekhez csatlakoznak. [A x509-tanúsítványok kezeléséhez](../../key-vault/general/about-keys-secrets-certificates.md)használjon Azure Key Vault tanúsítványokat. |
| Letagadhatóság            | Letagadhatatlanság       | Az Azure [monitorozásának és diagnosztizálásának](/azure/architecture/best-practices/monitoring)engedélyezése.|
| Információfelfedés | Titkosság       | Bizalmas adatok titkosítása [a nyugalmi](../fundamentals/encryption-atrest.md) [állapotban és az átvitel során](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Szolgáltatásmegtagadás      | Rendelkezésre állás          | Teljesítmény-mérőszámok figyelése a szolgáltatási feltételek esetleges megtagadásához. A kapcsolatok szűrőinek implementálása. Az [Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md#next-steps)az alkalmazás-tervezési ajánlott eljárásokkal együtt védelmet nyújt a DDoS-támadásokkal szemben.|
| Jogosultsági szint emelése | Engedélyezés         | Azure Active Directory <span class="underline"></span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)használata.|

### <a name="reduce-your-attack-surface"></a>Csökkentse a támadási felületet

A támadási felület az a teljes összeg, ahol lehetséges sebezhetőségek merülhetnek fel. Ebben a dokumentumban az alkalmazás támadási felületére koncentrálunk.
A hangsúly az alkalmazások támadás elleni védelmén van. Egy egyszerű és gyors módszer a támadási felület minimalizálására, ha eltávolítja a nem használt erőforrásokat és kódokat az alkalmazásból. Minél kisebb az alkalmazás, annál kisebb a támadási felülete. Például távolítsa el a következőt:

- A még nem kiadott funkciók kódja.
- A támogatási kód hibakeresése.
- A nem használt vagy elavult hálózati adapterek és protokollok.
- Virtuális gépek és egyéb erőforrások, amelyeket nem használ.

Az erőforrások rendszeres tisztítása és a használaton kívüli kódok eltávolítása nagyszerű módot biztosít arra, hogy a rosszindulatú szereplők kevesebb lehetőséggel rendelkezzenek a támadásokra.

A támadási felület csökkentésének részletesebb és részletes módja a támadási felületek elemzésének elvégzése. A támadási felületek elemzése segítségével leképezheti a rendszer azon részeit, amelyeket felül kell vizsgálni és meg kell vizsgálni a biztonsági rések ellen.

A támadási felületek elemzésének célja, hogy megértse az alkalmazás kockázati területeit, hogy a fejlesztők és a biztonsági szakemberek tisztában legyenek azzal, hogy az alkalmazás mely részeit támadják meg. Ezután megtalálhatja a lehetséges kisméretű lehetőségeket, nyomon követheti a támadási felület változásait, és azt, hogy mit jelent ez a kockázati perspektívában.

A támadási felületek elemzése segítséget nyújt a következők azonosításában:

- A biztonsági rések ellenőrzéséhez és teszteléséhez szükséges függvények és részek.
- Magas kockázatú, védelmi jellegű védelmet igénylő kódok (a rendszer azon részei, amelyeknek védenie kell).
- Ha megváltoztatja a támadási felületet, és frissítenie kell a fenyegetések értékelését.

A támadók számára a potenciális gyenge hely vagy biztonsági rés kihasználásához szükséges lehetőségek csökkentése megköveteli az alkalmazás teljes támadási felületének alapos elemzését. Emellett magában foglalja a rendszerszolgáltatásokhoz való hozzáférés letiltását vagy korlátozását, a legalacsonyabb jogosultsági elv alkalmazását és a réteges védelem használatát, ahol csak lehetséges.

Megbeszéljük a [támadási felület felülvizsgálatának elvégzését](secure-develop.md#conduct-attack-surface-review) az SDL ellenőrzési fázisában.

> [!NOTE]
> **Mi a különbség a veszélyforrások modellezése és a támadási felület elemzése között?**
A veszélyforrások modellezése a potenciális biztonsági fenyegetések azonosításának folyamata, és a fenyegetések elleni megfelelő enyhítés biztosítása. A támadási felület elemzése a kód olyan magas kockázatú területeit azonosítja, amelyek nyitottak a támadásra. Az alkalmazás központi telepítésének megkezdése előtt meg kell keresnie az alkalmazás magas kockázatú területeinek védelmét, valamint a kódok ezen területeinek vizsgálatát és tesztelését.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Identitásra vonatkozó szabályzat elfogadása elsődleges biztonsági peremhálózatként

A felhőalapú alkalmazások tervezésekor fontos, hogy az identitás-központú megközelítés hálózati központú megközelítése alapján kiterjessze a biztonsági peremhálózat középpontját. Az elsődleges helyszíni biztonsági peremhálózat a szervezet hálózata volt. A legtöbb helyszíni biztonsági terv a hálózatot használja elsődleges biztonsági kimutatásként. A Felhőbeli alkalmazások esetében jobban kiszolgálja az identitást, mint az elsődleges biztonsági területet.

A webalkalmazások fejlesztéséhez szükséges identitás-központú megközelítés fejlesztése:

- A többtényezős hitelesítés érvényesítése a felhasználók számára.
- Használjon erős hitelesítési és engedélyezési platformokat.
- Alkalmazza a legalacsonyabb jogosultsági szint elvét.
- Igény szerinti hozzáférés megvalósítása.

#### <a name="enforce-multi-factor-authentication-for-users"></a>A többtényezős hitelesítés betartatása a felhasználók számára

Használjon kétfaktoros hitelesítést. A kétfaktoros hitelesítés a jelenlegi szabvány a hitelesítéshez és az engedélyezéshez, mivel elkerüli a felhasználónévvel és a hitelesítéshez használt jelszó-típusokkal járó biztonsági gyengeségeket. Az Azure felügyeleti felületek (Azure Portal/távoli PowerShell) és az ügyfelek felé irányuló szolgáltatások elérését az [azure multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)használatára kell tervezni és konfigurálni.

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Erős hitelesítési és engedélyezési platformok használata

Egyéni kód helyett platform által biztosított hitelesítési és engedélyezési mechanizmusokat használhat. Ennek az az oka, hogy az egyéni hitelesítési kód fejlesztése a hibákra is hajlamos lehet. A kereskedelmi kódokat (például a Microsofttól) gyakran részletesen áttekintjük a biztonság érdekében. A [Azure Active Directory (Azure ad)](../../active-directory/fundamentals/active-directory-whatis.md) az identitás-és hozzáférés-kezeléshez használható Azure-megoldás. Ezek az Azure AD-eszközök és-szolgáltatások segítenek a biztonságos fejlesztésben:

- A [Microsoft Identity platform](../../active-directory/develop/index.yml) azon összetevők összessége, amelyekkel a fejlesztők biztonságosan jelentkezhetnek be a felhasználókba. A platform segítséget nyújt azoknak a fejlesztőknek, akik több-bérlős alkalmazásokat fejlesztenek egybérlős, üzletági (LOB) alkalmazásokat és fejlesztőket. Az alapszintű bejelentkezésen felül a Microsoft Identity platform használatával létrehozott alkalmazások meghívhatják a Microsoft API-kat és az egyéni API-kat. A Microsoft Identity platform támogatja az iparági szabványnak megfelelő protokollokat, például a OAuth 2,0 és az OpenID connectet.

- A [Azure Active Directory B2C (Azure ad B2C)](../../active-directory-b2c/index.yml) egy Identitáskezelő szolgáltatás, amellyel testreszabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik a profiljaikat, amikor az alkalmazásaikat használják. Ide tartoznak a többek között az iOS, az Android és a .NET rendszerhez fejlesztett alkalmazások is. A Azure AD B2C engedélyezi ezeket a műveleteket az ügyfelek identitásának védelme mellett.

#### <a name="apply-the-principle-of-least-privilege"></a>A legalacsonyabb jogosultsági szint elvét alkalmazza

A [legalacsonyabb jogosultsági](https://en.wikipedia.org/wiki/Principle_of_least_privilege) szint fogalma azt jelenti, hogy a felhasználóknak a munkájuk elvégzéséhez és a feladatok végrehajtásához szükséges pontos hozzáférési szintű hozzáférésre és felügyeletre van szükségük.

Szeretné, hogy a szoftverfejlesztők tartományi rendszergazdai jogokkal rendelkeznek? Szüksége lenne egy felügyeleti asszisztensnek a személyes számítógépén lévő rendszergazdai ellenőrzésekhez? A szoftverhez való hozzáférés kiértékelése nem különbözik. Ha az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) használatával különböző képességeket és szolgáltatói jogosultságokat biztosít a felhasználóknak az alkalmazásban, akkor mindenki számára egyáltalán nem fog hozzáférni. Az egyes szerepkörökhöz szükséges hozzáférés korlátozásával korlátozható a biztonsági problémák kockázata.

Győződjön meg arról, hogy az alkalmazás a [legkevesebb jogosultságot](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) alkalmazza a hozzáférési szokásaik során.

> [!NOTE]
> A minimális jogosultságú szabályoknak a szoftverre és a szoftvert létrehozó személyekre kell vonatkozniuk. A szoftverfejlesztők nagy kockázatot jelenthetnek az informatikai biztonságra, ha túl sok hozzáférést kapnak. A következmények súlyosak lehetnek, ha egy fejlesztő rosszindulatú szándékkal rendelkezik, vagy túl sok hozzáférést kap. Javasoljuk, hogy a fejlesztési életciklus során a lehető legkevesebb jogosultságot alkalmazza a fejlesztőknek.

#### <a name="implement-just-in-time-access"></a>Igény szerinti hozzáférés megvalósítása

Igény *szerinti (JIT* ) hozzáférés megvalósítása a jogosultságok expozíciós idejének további csökkentése érdekében. [Azure ad Privileged Identity Management](../../active-directory/roles/security-planning.md#stage-3-take-control-of-admin-activity) használata a következőhöz:

- Adja meg a felhasználóknak azokat az engedélyeket, amelyekre csak JIT szükséges.
- A lerövidített időtartamhoz rendeljen hozzá szerepköröket úgy, hogy a jogosultságok automatikusan visszavonásra kerülnek.

### <a name="require-re-authentication-for-important-transactions"></a>Ismételt hitelesítés megkövetelése fontos tranzakciókhoz

A [helyek közötti kérések hamisítása](/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (más néven *XSRF* vagy *CSRF*) olyan webalkalmazások elleni támadás, amelyekben egy rosszindulatú webalkalmazás befolyásolja az ügyféloldali böngésző és a böngészőt meghatalmazó webalkalmazás közötti interakciót. A helyek közötti kérelmek hamisításával kapcsolatos támadások lehetségesek, mert a böngészők bizonyos típusú hitelesítési jogkivonatokat automatikusan küldenek egy webhelyre irányuló minden kéréssel.
Ezt a kiaknázási formát is nevezzük *egykattintásos támadásnak* vagy *munkamenet-lovaglásnak* , mivel a támadás kihasználja a felhasználó korábban hitelesített munkamenetét.

Az ilyen típusú támadásokkal szembeni védekezés legjobb módja, ha megkéri a felhasználót, hogy csak a felhasználó tudja biztosítani az összes fontos tranzakciót, például a vásárlást, a fiók inaktiválását vagy a jelszó módosítását. Megkérheti a felhasználót, hogy írja be újra a jelszavát, hajtsa végre a CAPTCHA-t, vagy küldjön be egy titkos jogkivonatot, amely csak a felhasználó számára lenne lehetséges. A leggyakoribb megközelítés a titkos jogkivonat.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Kulcskezelő megoldás használata a kulcsok, a hitelesítő adatok és más titkok biztonságossá tételéhez

A kulcsok és a hitelesítő adatok elvesztése gyakori probléma. A kulcsok és a hitelesítő adatok elvesztésekor az egyetlen dolog rosszabb, mint a jogosulatlan felek hozzáférése. A támadók kihasználhatják az automatizált és manuális technikákat, hogy megtalálják a kód-adattárakban, például a GitHubban tárolt kulcsokat és titkokat. A kulcsokat és a titkokat ne helyezze el ezekben a nyilvános programkódokban vagy más kiszolgálókon.

A kulcsokat, tanúsítványokat, titkokat és a kapcsolatok karakterláncait mindig a kulcskezelő megoldásba helyezi. Használhat olyan központosított megoldást, amelyben a kulcsok és a titkos kulcsokat hardveres biztonsági modulok (HSM-EK) tárolják. Az Azure a [Azure Key Vault](../../key-vault/general/overview.md)segítségével a felhőben található HSM-t biztosít.

A Key Vault *titkos tároló*: ez egy központi felhőalapú szolgáltatás az alkalmazási titkok tárolásához. Key Vault a bizalmas adatok biztonsága érdekében az alkalmazás titkos adatait egyetlen, központi helyen tárolhatja, és biztonságos hozzáférést, engedélyek vezérlését és hozzáférés-naplózást biztosít.

A titkokat *külön* tárolókban tárolja a rendszer. Mindegyik tároló rendelkezik saját konfigurációs és biztonsági házirendekkel a hozzáférés szabályozásához. Az adatokat egy REST APIon vagy egy, a legtöbb programozási nyelvhez elérhető ügyféloldali SDK-n keresztül érheti el.

> [!IMPORTANT]
> Azure Key Vault úgy van kialakítva, hogy a kiszolgálói alkalmazások konfigurációs titkait tárolja. A szolgáltatás nem az alkalmazás felhasználóihoz tartozó adattárolásra szolgál. Ez tükröződik a teljesítményjellemzőiben, az API-jában és a költségmodelljében.
>
> A felhasználói adattárolást máshol kell tárolni, például egy olyan Azure SQL Database-példányban, amely transzparens adattitkosítás (TDE) vagy egy Azure Storage Service Encryption-t használó Storage-fiókban található. Az alkalmazás által az adattárakhoz való hozzáféréshez használt titkos kulcsok Azure Key Vaultban tárolhatók.

### <a name="protect-sensitive-data"></a>Bizalmas adatok védelme

Az adatok védelme fontos részét képezi a biztonsági stratégiának.
Az adatbesorolás és az adatvédelem iránti igények meghatározása segít az alkalmazás adatbiztonsággal való megtervezésében. A tárolt adatmennyiség és az üzleti hatás osztályozása (kategorizálása) segítségével a fejlesztők meghatározhatják az adatkezeléssel kapcsolatos kockázatokat.

Az adatformátumok tervezésekor az összes vonatkozó adat megkülönböztethető az adatoktól. Győződjön meg arról, hogy az alkalmazás bizalmasként kezeli a megfelelő adatokat. Ezek a gyakorlatok segíthetnek a bizalmas adatok védelmében:

- Használjon titkosítást.
- Kerülje a nehezen megfejthető titkokat, például a kulcsokat és a jelszavakat.
- Győződjön meg arról, hogy a hozzáférés-vezérlés és a naplózás van érvényben.

#### <a name="use-encryption"></a>Titkosítás használata

Az adatok védelmének fontos részét kell képeznie a biztonsági stratégiának.
Ha az adatok egy adatbázisban vannak tárolva, vagy ha a hely között oda-vissza mozog, használja az inaktív [adatok](../fundamentals/encryption-atrest.md) titkosítását (az adatbázisban) és az adatátviteli adattitkosítást [(a](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) felhasználó, az adatbázis, az API vagy a szolgáltatási végpont felé). Javasoljuk, hogy mindig használjon SSL/TLS protokollokat az adatcseréhez. Győződjön meg arról, hogy a TLS legújabb verzióját használja a titkosításhoz (jelenleg ez a 1,2-es verzió).

#### <a name="avoid-hard-coding"></a>A nehezen megfejthető kódolás elkerülése

Néhány dolgot soha nem szabad a szoftverben rögzítetten kódolni. Ilyenek például az állomásnevek vagy az IP-címek, az URL-címek, az e-mail-címek, a felhasználónevek, a jelszavak, a Storage-fiókok kulcsai és más titkosítási kulcsok. Érdemes lehet olyan követelményeket megvalósítani, amelyek körébe tartozik a kódban szereplő, vagy nem módosítható kód, beleértve a kód megjegyzési részeit is.

Amikor megjegyzéseket helyez el a kódban, ügyeljen arra, hogy ne mentsen bizalmas adatokat. Ez magában foglalja az e-mail-címét, a jelszavát, a kapcsolatok karakterláncait, az alkalmazással kapcsolatos információkat, amelyeket csak a szervezeten belüli valaki ismer, és bármi más, ami előnyt jelenthet az alkalmazás vagy szervezet támadójának.

Alapvetően feltételezhető, hogy a fejlesztési projektben minden a központilag köztudottan fog megjelenni. Kerülje a projekt bármilyen típusú bizalmas adatát.

Korábban megbeszéltük [Azure Key Vault](../../key-vault/general/overview.md). A Key Vault használhatók a titkos kódok, például a kulcsok és a jelszavak tárolására, a titkosítás helyett. Ha a Key Vault az Azure-erőforrások felügyelt identitásával együtt használja, az Azure-webalkalmazás könnyen és biztonságosan férhet hozzá a titkos konfigurációs értékekhez anélkül, hogy bármilyen titkot nem tárol a verziókövetés vagy a konfigurációban. További információ: [a titkok kezelése a kiszolgálói alkalmazásokban a Azure Key Vault](/learn/modules/manage-secrets-with-azure-key-vault/)használatával.

### <a name="implement-fail-safe-measures"></a>Nem biztonságos mértékek implementálása

Az alkalmazásnak képesnek kell lennie a végrehajtás során következetesen előforduló [hibák](/dotnet/standard/exceptions/) kezelésére. Az alkalmazásnak az összes hibát el kell látnia, és nem lehet biztonságos vagy lezárva.

Győződjön meg arról is, hogy a hibák naplózása megfelelő felhasználói környezettel történik a gyanús vagy kártékony tevékenységek azonosításához. A naplókat elegendő időre kell megőrizni a késleltetett törvényszéki elemzések engedélyezéséhez. A naplókat olyan formátumban kell megadni, amelyet egy naplózási felügyeleti megoldás könnyen használhat. Ellenőrizze, hogy a biztonsággal kapcsolatos hibákra vonatkozó riasztások aktiválva vannak-e. A nem megfelelő naplózás és figyelés lehetővé teszi a támadók számára a rendszerek további támadását és az adatmegőrzés fenntartását.

### <a name="take-advantage-of-error-and-exception-handling"></a>Kihasználhatja a hibák és a kivételek kezelését

A megfelelő hiba és a [kivétel-kezelési](/dotnet/standard/exceptions/best-practices-for-exceptions) szolgáltatás bevezetése fontos része a védekező kódolásnak. A hibák és a kivételek kezelését kritikus fontosságú a rendszer megbízhatóságának és biztonságossá tételének biztosítása érdekében. A hibakezelés hibái a biztonsági rések különböző típusaihoz vezethetnek, például az információk kiszivárgásához a támadók számára, és a támadók könnyebben megismerhetik a platformot és a kialakítást.

Győződjön meg a következőket:

- A kivételeket központosított módon kezelheti a kód duplikált [próbálkozási és észlelési blokkjának](/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) elkerüléséhez.

- Az alkalmazásban az összes váratlan viselkedést kezeli a rendszer.

- A felhasználók számára megjelenő üzenetek nem szivárognak ki kritikus adatokat, de elegendő információt nyújtanak a probléma magyarázatához.

- A rendszer naplózza a kivételeket, és elegendő információt biztosít a kriminalisztikai vagy az incidensi csapatoknak a vizsgálathoz.

A [Azure Logic apps](../../logic-apps/logic-apps-overview.md) egy első osztályú élményt biztosít a függő rendszerek által okozott [hibák és kivételek kezeléséhez](../../logic-apps/logic-apps-exception-handling.md) . A Logic Apps használatával munkafolyamatokat hozhat létre, amelyekkel automatizálhatja a vállalatok és a szervezetek alkalmazásait, adatait, rendszereit és szolgáltatásait integráló feladatokat és folyamatokat.

### <a name="use-logging-and-alerting"></a>Naplózás és riasztás használata

A biztonsági vizsgálatokkal kapcsolatos biztonsági problémák [naplózása](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) , valamint riasztások kiváltása a problémákról, hogy az emberek időben tudják a problémákat. Az összes összetevő naplózásának és naplózásának engedélyezése. A naplóknak rögzíteniük kell a felhasználói környezetet, és azonosítaniuk kell az összes fontos eseményt.

Győződjön meg arról, hogy nem naplózza a felhasználó által a webhelyre küldött bizalmas adatokat. A bizalmas adatokra például a következők tartoznak:

- Felhasználói hitelesítő adatok
- Társadalombiztosítási számok vagy egyéb azonosítási információk
- Hitelkártya-számok vagy egyéb pénzügyi információk
- Állapotadatok
- Titkos kulcsok vagy egyéb adatok, amelyek a titkosított adatok visszafejtésére használhatók
- Rendszer-vagy alkalmazás-információk, amelyek az alkalmazás hatékonyabb támadásához használhatók

Győződjön meg arról, hogy az alkalmazás figyeli a felhasználói felügyeleti eseményeket, például a sikeres és sikertelen felhasználói bejelentkezéseket, a jelszó alaphelyzetbe állítását, a jelszó módosításait, a fiók zárolását és a felhasználói regisztrációt. Az események naplózása segít észlelni és reagálni az esetlegesen gyanús viselkedésre. Lehetővé teszi az operatív adatok összegyűjtését is, például az alkalmazáshoz való hozzáférést.

## <a name="next-steps"></a>Következő lépések
A következő cikkekben olyan biztonsági vezérlőket és tevékenységeket ajánlunk, amelyek segíthetnek a biztonságos alkalmazások fejlesztésében és üzembe helyezésében.

- [Biztonságos alkalmazások fejlesztése](secure-develop.md)
- [Biztonságos alkalmazások telepítése](secure-deploy.md)