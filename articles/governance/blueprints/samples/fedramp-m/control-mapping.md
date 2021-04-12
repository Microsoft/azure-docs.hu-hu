---
title: FedRAMP mérsékelt terv minta-vezérlőelemek
description: A FedRAMP mérsékelt tervrajz-minta leképezésének vezérlése. Minden vezérlő egy vagy több olyan Azure Policy-definícióra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 7c8b2524958be1a4b5e49ff870c7c01a8f89f305
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386669"
---
# <a name="control-mapping-of-the-fedramp-moderate-blueprint-sample"></a>A FedRAMP mérsékelt tervrajz-minta leképezésének vezérlése

A következő cikk azt ismerteti, hogyan FedRAMP az Azure-tervezetek mérsékelt tervrajzok a FedRAMP mérsékelt vezérlőelemekre. A vezérlőkkel kapcsolatos további információkért lásd: [FedRAMP biztonsági vezérlők alapterve](https://www.fedramp.gov/).

A következő leképezések a **FedRAMP mérsékelt** vezérlői. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki az **\[ előnézetet \] : a naplózás FedRAMP mérsékelten vezérli, és telepítsen speciális virtuálisgép-bővítményeket a naplózási követelmények** beépített házirend-kezdeményezésének támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem egy-az-egyhez vagy egy teljes egyezés egy vezérlőelem és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/fedramp-m/control-mapping.md).

## <a name="ac-2-account-management"></a>AC-2 fiókok kezelése

Ez a terv segít áttekinteni azokat a fiókokat, amelyek esetleg nem felelnek meg a szervezete fiókjának felügyeleti követelményeinek. Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek a külső fiókokat az előfizetések és az elavult fiókok olvasási, írási és tulajdonosi engedélyeivel naplózzák. A szabályzatok által auditált fiókok áttekintésével megteheti a megfelelő lépéseket a fiókok kezelésével kapcsolatos követelmények teljesítése érdekében.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Fiókkezelés | Role-Based sémák

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../../../role-based-access-control/overview.md) segítségével felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat is HOZZÁRENDEL az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését. A terv emellett egy Azure Policy definíciót rendel hozzá az egyéni Azure RBAC-szabályok használatának naplózásához. Az egyéni Azure RBAC-szabályok megvalósításának megismerése segíthet a szükséges és a megfelelő implementáció ellenőrzésében, mivel az egyéni Azure RBAC-szabályok hibásak.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Egyéni RBAC-szabályok használatának naplózása
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) fiókok kezelése | Fiókok figyelése/atipikus használat

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A virtuális gépek eléréséhez szükséges JIT-kérelmeket a rendszer naplózza a tevékenység naplójában, amely lehetővé teszi az atipikus használat figyelését. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincs konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="ac-4-information-flow-enforcement"></a>AC-4 információáramlás kényszerítése

A több eredetű erőforrás-megosztás (CORS) lehetővé teszi App Services-erőforrások kérését egy külső tartományból. A Microsoft azt javasolja, hogy csak a szükséges tartományokat engedélyezze az API-val, a funkcióval és a webalkalmazásokkal való interakcióhoz. Ez a terv egy [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá, amely segít a CORS-erőforrások hozzáférési korlátozásának figyelésében Azure Security Centerban. A CORS-implementációk ismertetése segít ellenőrizni, hogy az információáramlási vezérlők implementálva vannak-e.

- A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a webalkalmazáshoz

## <a name="ac-5-separation-of-duties"></a>A feladatok AC-5 elkülönítése

Csak egy Azure-előfizetéshez tartozó tulajdonos nem engedélyez felügyeleti redundanciát. Ezzel szemben az Azure-előfizetések tulajdonosai is növelhetik a biztonsági réseket egy sérült tulajdonosi fiókon keresztül. Ez a tervezet segítséget nyújt a megfelelő számú Azure-előfizetési tulajdonos fenntartásához olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek az Azure-előfizetések tulajdonosainak számát naplózzák. A terv emellett Azure Policy definíciókat is hozzárendel, amelyek segítenek a rendszergazdák csoport tagságának vezérlésében a Windows rendszerű virtuális gépeken. Az előfizetés tulajdonosa és a virtuális gép rendszergazdai engedélyeinek kezelése segíthet a feladatok megfelelő elkülönítésének megvalósításában.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Windows rendszerű virtuális gépek naplózása, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Windows rendszerű virtuális gépek naplózása, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Előfeltételek telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Telepítse a Windows rendszerű virtuális gépek naplózásának előfeltételeit, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) távoli hozzáférés | Automatikus figyelés/vezérlés

Ez a terv segít a távelérés figyelésében és vezérlésében azáltal, hogy [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével figyeli, hogy a Azure app Service alkalmazás távoli hibakeresése ki van-e kapcsolva, valamint olyan házirend-definíciókat, amelyek olyan Linux virtuális gépeket naplóznak, amelyek lehetővé teszik a távoli kapcsolódást Ez a terv egy Azure Policy-definíciót is hozzárendel, amely segít megfigyelni a nem korlátozott hozzáférést a Storage-fiókokhoz. Ezen mutatók monitorozásával biztosítható, hogy a távelérési módszerek megfeleljenek a biztonsági szabályzatnak.

- \[Előzetes verzió \] : jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózása
- \[Előzetes verzió \] : követelmények központi telepítése a jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózásához
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz
- A távoli hibakeresést ki kell kapcsolni függvényalkalmazás
- A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Válasz a feldolgozási hibák naplózására

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek figyelik a naplózási és eseménynaplózási konfigurációkat. Ezeknek a konfigurációknak a figyelése a rendszerhiba vagy a helytelen konfiguráció jelzését, valamint a javítási műveletek elvégzését is lehetővé teszi.

- Diagnosztikai beállítás naplózása
- Az SQL Server naplózását engedélyezni kell
- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon

## <a name="au-12-audit-generation"></a>AU-12 naplózási generáció

Ez a terv segítséget nyújt a rendszeresemények naplózásához az Azure-erőforrások naplózási beállításait naplózó [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével.
Ezek a szabályzat-definíciók a Log Analytics ügynök Azure-beli virtuális gépeken való üzembe helyezését és az egyéb Azure-erőforrástípusok naplózási beállításainak konfigurálását is kikényszerítik. Ezek a szabályzat-definíciók a diagnosztikai naplók konfigurációját is naplózzák, hogy betekintést nyújtsanak az Azure-erőforrásokon belül végrehajtott műveletekre. Emellett a naplózás és a speciális adatbiztonság is konfigurálva van az SQL-kiszolgálókon.

- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása a VMSS-ben – nincs listázva a VM-rendszerkép (operációs rendszer)
- \[Előzetes verzió \] : log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux-VM Scale sets (VMSS)
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[Előzetes verzió \] : log Analytics ügynök központi telepítése Windows VM Scale sets (VMSS)
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken
- Diagnosztikai beállítás naplózása
- Az SQL Server naplózását engedélyezni kell
- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Naplózás üzembe helyezése SQL-kiszolgálókon
- Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) a legkevésbé szükséges funkciók | Program végrehajtásának megakadályozása

A Azure Security Center adaptív alkalmazás-vezérlése egy intelligens, automatizált, teljes körű alkalmazás-szűrési megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem kényszerítési módban futtatható, amely tiltja a nem jóváhagyott alkalmazások futtatását. Ez a tervrajz olyan Azure Policy-definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, amelyekben az alkalmazás engedélyezett listája javasolt, de még nincs konfigurálva.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) a legkevésbé szükséges funkciók | Engedélyezett szoftver/engedélyezési lista

A Azure Security Center adaptív alkalmazás-vezérlése egy intelligens, automatizált, teljes körű alkalmazás-szűrési megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem lehetővé teszi a jóváhagyott alkalmazások listáját a virtuális gépekhez. Ez a tervrajz olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, amelyekben az alkalmazás engedélyezett listája javasolt, de még nincs konfigurálva.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="cm-11-user-installed-software"></a>CM – 11 User-Installed szoftver

A Azure Security Center adaptív alkalmazás-vezérlése egy intelligens, automatizált, teljes körű alkalmazás-szűrési megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem segítségével érvényesítheti és figyelheti a szoftverkorlátozó házirendek megfelelőségét. Ez a tervrajz olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, amelyekben az alkalmazás engedélyezett listája javasolt, de még nincs konfigurálva.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="cp-7-alternate-processing-site"></a>CP-7 alternatív feldolgozási hely

Azure Site Recovery replikálja a virtuális gépeken futó munkaterheléseket egy elsődleges helyről a másodlagos helyre. Ha áramkimaradás fordul elő az elsődleges helyen, a munkaterhelés feladatátvétele a másodlagos helyen történik. Ez a tervrajz olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely a virtuális gépeket a vész-helyreállítási beállítások nélkül naplózza. A kijelző figyelése segít biztosítani a szükséges készenléti ellenőrzéseket.

- Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés a Kiemelt jogosultságú fiókokhoz

Ez a terv segít a privilegizált hozzáférés korlátozásában és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével olyan fiókokat naplózni, amelyek tulajdonosi és/vagy írási engedélyekkel nem rendelkeznek a többtényezős hitelesítés engedélyezésével. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés írási engedélyekkel rendelkező fiókjaiban

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) azonosítás és hitelesítés (szervezeti felhasználók) | Nem Kiemelt fiókok hálózati hozzáférése

Ez a terv segítséget nyújt a hozzáférés korlátozásához és vezérléséhez, ha egy [Azure Policy](../../../policy/overview.md) definíció hozzárendelésével naplózza a fiókokat olyan olvasási engedélyekkel, amelyeken nincs engedélyezve a többtényezős hitelesítés. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon

## <a name="ia-5-authenticator-management"></a>IA-5 hitelesítő kezelése

Ez a terv azokat a [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek a Linux rendszerű virtuális gépeket a jelszavak nélküli fiókok távoli kapcsolatainak engedélyezésére és/vagy helytelen engedélyekkel rendelkeznek a passwd fájlban. Ez a terv a Windows rendszerű virtuális gépekhez tartozó jelszó-titkosítási típus konfigurációját naplózó szabályzat-definíciókat is hozzárendeli. Ezen mutatók monitorozásával biztosíthatja, hogy a rendszerhitelesítő megfeleljenek a szervezete azonosítási és hitelesítési házirendjének.

- \[Előzetes verzió \] : olyan linuxos virtuális gépek naplózása, amelyek nem rendelkeznek a passwd fájl engedélyeivel 0644 értékre állítva
- \[Előzetes verzió \] : jelszavak nélküli fiókkal rendelkező linuxos virtuális gépek naplózása
- \[Előzetes \] verzió: a jelszavakat nem tároló Windows rendszerű virtuális gépek naplózása visszafejthető titkosítással
- \[Előzetes verzió \] : követelmények telepítése olyan linuxos virtuális gépek naplózására, amelyek nem rendelkeznek a passwd fájl engedélyeivel 0644 értékre állítva
- \[Előzetes verzió \] : követelmények telepítése a jelszavak nélküli fiókkal rendelkező linuxos virtuális gépek naplózására
- \[Előzetes \] verzió: követelmények telepítése a jelszavakat nem tároló Windows-alapú virtuális gépek naplózásához visszafejthető titkosítással

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) hitelesítő kezelése | Password-Based hitelesítés

Ez a terv segítséget nyújt az erős jelszavak betartatásához olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek a minimális szilárdságot és egyéb jelszavakat nem igénylő Windows rendszerű virtuális gépeket naplózzák. A virtuális gépeknek a jelszó erőssége megsértésével kapcsolatos ismerete segít megtenni a szükséges javítási műveleteket, hogy az összes virtuálisgép-felhasználói fiók jelszava megfeleljen a szervezete jelszavas házirendjének.

- \[Előzetes \] verzió: az előző 24 jelszó újbóli használatát lehetővé tevő Windows rendszerű virtuális gépek naplózása
- \[Előzetes \] verzió: a maximális jelszóval nem rendelkező Windows rendszerű virtuális gépek naplózása 70 nap
- \[Előzetes \] verzió: olyan Windows rendszerű virtuális gépek naplózása, amelyek nem rendelkeznek minimális jelszóval (1 nap)
- \[Előzetes verzió \] : engedélyezve van a jelszó-bonyolultsági beállítással nem rendelkező Windows rendszerű virtuális gépek naplózása
- \[Előzetes verzió \] : a jelszó minimális hosszát 14 karakternél nem korlátozó Windows-alapú virtuális gépek naplózása
- \[Előzetes \] verzió: a jelszavakat nem tároló Windows rendszerű virtuális gépek naplózása visszafejthető titkosítással
- \[Előzetes \] verzió: követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyek lehetővé teszik az előző 24 jelszó újbóli használatát
- \[Előzetes \] verzió: követelmények központi telepítése Windows rendszerű virtuális gépek naplózásához, amelyek nem rendelkeznek maximális jelszóval (70 nap)
- \[Előzetes \] verzió: követelmények telepítése a Windows rendszerű virtuális gépek naplózásához, amelyek nem rendelkeznek legalább 1 napos jelszóval
- \[Előzetes verzió \] : követelmények központi telepítése a jelszó-bonyolultsági beállítással nem rendelkező Windows rendszerű virtuális gépek naplózásához
- \[Előzetes verzió \] : követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre
- \[Előzetes \] verzió: követelmények telepítése a jelszavakat nem tároló Windows-alapú virtuális gépek naplózásához visszafejthető titkosítással

## <a name="ra-5-vulnerability-scanning"></a>RA-5 sebezhetőségi vizsgálat

Ez a terv segítséget nyújt az információs rendszer biztonsági réseinak kezeléséhez olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek figyelik az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuális gépek Azure Security Center-
A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára. A terv emellett olyan szabályzat-definíciókat is hozzárendel, amelyek az SQL-kiszolgálókon a speciális adatbiztonságot auditálják és érvényesítik. A speciális adatbiztonság biztonsági rések felmérése és a komplex veszélyforrások elleni védelem lehetővé teszi, hogy jobban megértse a telepített erőforrások sebezhetőségeit.

- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A virtuális gépek biztonsági konfigurációjában lévő biztonsági réseket szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="sc-5-denial-of-service-protection"></a>SC-5 szolgáltatásmegtagadás-védelem

Az Azure Distributed szolgáltatásmegtagadás (DDoS) standard csomagja további funkciókat és kockázatcsökkentő képességeket biztosít az alapszintű szolgáltatási szinten. Ezek a további funkciók Azure Monitor integrációt és a támadás utáni kockázatcsökkentő jelentések áttekintését is lehetővé teszi. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely azt naplózza, hogy a DDoS standard szint engedélyezve van-e. A szolgáltatási rétegek közötti képességbeli különbség megismerése segíthet kiválasztani a legjobb megoldást az Azure-környezethez tartozó szolgáltatások megtagadásának kezeléséhez.

- DDoS Protection a standardot engedélyezni kell

## <a name="sc-7-boundary-protection"></a>SC-7 határ védelme

Ez a terv segít a Rendszerhatár kezelésében és szabályozásában azáltal, hogy hozzárendel egy [Azure Policy](../../../policy/overview.md) definíciót, amely figyeli a hálózati biztonsági csoportra vonatkozó ajánlásokat a Azure Security Centerban. Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a potenciális támadási felület csökkentése érdekében. Emellett a terv a nem védett végpontokat, alkalmazásokat és tárolási fiókokat figyelő szabályzat-definíciókat is hozzárendeli. A tűzfal által nem védett végpontok és alkalmazások, valamint a korlátlan hozzáféréssel rendelkező Storage-fiókok nem kívánt hozzáférést biztosíthatnak az információs rendszeren belül található információkhoz.

- Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani
- Korlátozni kell az internet felé irányuló végponton keresztüli hozzáférést
- A webportokat korlátozni kell a virtuális géphez társított hálózati biztonsági csoportokhoz
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) határ védelme | Hozzáférési pontok

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A JIT virtuális gép hozzáférése segít korlátozni az Azure-ban lévő erőforrások külső kapcsolatainak számát. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincs konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) határ védelme | Külső távközlési szolgáltatások

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A JIT virtuális gépekhez való hozzáférés a hozzáférési kérelmek és a jóváhagyási folyamatok megkönnyítésével segíti a forgalomra vonatkozó kivételek kezelését. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincs konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) a továbbítás titkossága és integritása | Titkosítási vagy alternatív fizikai védelem

Ez a terv segít a továbbított adatok bizalmas és integritásának védelmében [Azure Policy](../../../policy/overview.md) definíciók kiosztásával, amelyek segítségével figyelheti a kommunikációs protokollok által megvalósított titkosítási mechanizmusokat. A kommunikáció megfelelő titkosítása segíthet a szervezet követelményeinek teljesítésében, illetve az információk jogosulatlan közzétételtől és módosítástól való védelmében.

- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- A biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózása
- Követelmények telepítése a biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózásához
- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A webalkalmazás csak HTTPS protokollon keresztül érhető el

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) a REST-adatok védelme | Titkosítási védelem

Ebből a tervből megtudhatja, hogyan kényszerítheti ki a szabályzatot a titkosítási vezérlők használatára, hogy olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendeljen hozzá, amelyek kikényszerítik az adott titkosítási vezérlőket, és naplózzák a gyenge titkosítási beállítások használatát. Annak megismerése, hogy az Azure-erőforrások nem optimális titkosítási konfigurációval rendelkezzenek-e, segítheti a javítási műveleteket, hogy az erőforrások konfigurálása az adatvédelmi szabályzatnak megfelelően történjen. Pontosabban, a tervhez hozzárendelt szabályzat-definíciók titkosítást igényelnek a Storage-fiókokhoz; transzparens adattitkosítás megkövetelése SQL-adatbázisokban; és naplózza a hiányzó titkosítást az SQL-adatbázisokon, a virtuális gépek lemezein és az Automation-fiók változóinak.

- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Az SQL DB transzparens adattitkosításának üzembe helyezése
- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- Titkosítás megkövetelése Data Lake Store fiókokon
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás

## <a name="si-2-flaw-remediation"></a>SI-2 hibák szervizelése

Ebből a tervből megtudhatja, hogyan kezelheti a rendszerhibákat a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, az SQL-biztonsági réseket és a virtuális gépek Azure Security Center-beli biztonsági réseit figyelő [Azure Policy](../../../policy/overview.md) definíciók A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára. Ez a terv egy szabályzat-definíciót is hozzárendel, amely biztosítja a virtuálisgép-méretezési csoportok operációs rendszerének javítását.

- Az operációsrendszer-rendszerkép automatikus javításának megkövetelése Virtual Machine Scale Sets
- A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell
- A rendszerfrissítéseket telepíteni kell a virtuális gépekre
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A virtuális gépek biztonsági konfigurációjában lévő biztonsági réseket szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="si-3-malicious-code-protection"></a>SI-3 kártékony kódok védelme

Ez a terv segít az Endpoint Protection kezelésében, beleértve a kártékony programkódok védelmét is, ha olyan [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek figyelik a Azure Security Center virtuális gépei hiányzó Endpoint Protection szolgáltatását, és kikényszerítik a Microsoft antimalware megoldást a Windows rendszerű virtuális gépeken.

- A Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware-bővítmény telepítése
- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) kártékony kódok védelme | Központi felügyelet

Ez a terv segít az Endpoint Protection kezelésében, beleértve a kártékony programkódok védelmét is, ha olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek a Azure Security Center virtuális gépei hiányzó Endpoint Protection szolgáltatását figyelik. A Azure Security Center központosított felügyeleti és jelentéskészítési képességeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést kapjon az üzembe helyezett Azure-erőforrások biztonsági állapotával.

- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="si-4-information-system-monitoring"></a>SI-4 információs rendszer figyelése

Ez a terv a naplózás és az adatbiztonság Azure-erőforrásokon keresztüli naplózásával és érvényesítésével segíti a rendszerek figyelését. A szabályzatok a Log Analytics ügynök üzembe helyezését, valamint az SQL-adatbázisok, a Storage-fiókok és a hálózati erőforrások fokozott biztonsági beállításait naplózzák és érvényesítik. Ezek a képességek segítenek a rendellenes viselkedés és a támadási mutatók észlelésében, így elvégezheti a megfelelő lépéseket.

- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása a VMSS-ben – nincs listázva a VM-rendszerkép (operációs rendszer)
- \[Előzetes verzió \] : log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux-VM Scale sets (VMSS)
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[Előzetes verzió \] : log Analytics ügynök központi telepítése Windows VM Scale sets (VMSS)
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken
- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Komplex veszélyforrások elleni védelem üzembe helyezése a Storage-fiókokon
- Naplózás üzembe helyezése SQL-kiszolgálókon
- A Network Watcher üzembe helyezése virtuális hálózatok létrehozásakor
- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon

> [!NOTE]
> Az adott Azure Policy-definíciók rendelkezésre állása Azure Government és más nemzeti felhőkben is változhat. 

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a FedRAMP mérsékelt tervének vezérlési leképezését, a következő cikkekben megismerheti a terv ismertetését és a minta üzembe helyezésének módját:

> [!div class="nextstepaction"]
> [FedRAMP mérsékelt terv – áttekintés](./index.md) 
>  [FedRAMP mérsékelt terv – lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
