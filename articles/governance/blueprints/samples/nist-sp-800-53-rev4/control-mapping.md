---
title: Minta – NIST SP 800-53 R4 terv – vezérlés leképezése
description: A NIST SP 800-53 R4 tervrajz-minta leképezésének vezérlése Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 6d0b9202cdece44378964ee98d63a126162849fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003041"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>A NIST SP 800-53 R4 Blueprint minta vezérlésének leképezése

A következő cikk azt ismerteti, hogyan jelennek meg az Azure-tervezetek NIST SP 800-53 R4 tervezet-minta a NIST SP 800-53 R4-vezérlők számára. További információ a vezérlőkről: [NIST SP 800-53](https://nvd.nist.gov/800-53).

A következő leképezések a **NIST SP 800-53 (Rev. 4)** vezérlőkre vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a szabályzatot a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki **az\] \[előnézetet: A NIST SP 800-53 R4-es verziójának naplózása és üzembe helyezése** adott virtuálisgép-bővítményekkel a beépített házirend-kezdeményezés támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem 1:1 vagy teljes egyezés van egy vezérlő és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/nist-sp-800-53-rev4/control-mapping.md).

## <a name="ac-2-account-management"></a>AC-2 fiókok kezelése

Ez a terv segít áttekinteni azokat a fiókokat, amelyek esetleg nem felelnek meg a szervezete fiókjának felügyeleti követelményeinek. Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek az előfizetésre és az elavult fiókokra vonatkozó olvasási, írási és tulajdonosi engedélyekkel auditálják a külső fiókokat. A szabályzatok által auditált fiókok áttekintésével megteheti a megfelelő lépéseket a fiókok kezelésével kapcsolatos követelmények teljesítése érdekében.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Fiókkezelés | Szerepköralapú sémák

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat is HOZZÁRENDEL az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését. A terv emellett egy Azure Policy definíciót rendel hozzá az egyéni RBAC-szabályok használatának naplózásához. Az egyéni RBAC-szabályok megvalósításának megismerése segíthet a szükséges és a megfelelő implementáció ellenőrzésében, mivel az egyéni RBAC-szabályok hibásak.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Egyéni RBAC-szabályok használatának naplózása
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) fiókok kezelése | Fiókok figyelése/atipikus használat

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A virtuális gépek eléréséhez szükséges JIT-kérelmeket a rendszer naplózza a tevékenység naplójában, amely lehetővé teszi az atipikus használat figyelését. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincsenek konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="ac-4-information-flow-enforcement"></a>AC-4 információáramlás kényszerítése

A több eredetű erőforrás-megosztás (CORS) lehetővé teszi App Services-erőforrások kérését egy külső tartományból. A Microsoft azt javasolja, hogy csak a szükséges tartományokat engedélyezze az API-val, a funkcióval és a webalkalmazásokkal való interakcióhoz. Ez a terv egy [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá, amely segít a CORS-erőforrások hozzáférési korlátozásának figyelésében Azure Security Centerban.
A CORS-implementációk ismertetése segít ellenőrizni, hogy az információáramlási vezérlők implementálva vannak-e.

- A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a webalkalmazáshoz

## <a name="ac-5-separation-of-duties"></a>A feladatok AC-5 elkülönítése

Csak egy Azure-előfizetéshez tartozó tulajdonos nem engedélyez felügyeleti redundanciát. Ezzel szemben az Azure-előfizetések tulajdonosai is növelhetik a biztonsági réseket egy sérült tulajdonosi fiókon keresztül. Ez a tervezet segítséget nyújt a megfelelő számú Azure-előfizetési tulajdonos fenntartásához olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek az Azure-előfizetések tulajdonosainak számát naplózzák. A terv emellett Azure Policy definíciókat is hozzárendel, amelyek segítenek a rendszergazdák csoport tagságának vezérlésében a Windows rendszerű virtuális gépeken. Az előfizetés tulajdonosa és a virtuális gép rendszergazdai engedélyeinek kezelése segíthet a feladatok megfelelő elkülönítésének megvalósításában.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Windows rendszerű virtuális gépek naplózása, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Windows rendszerű virtuális gépek naplózása, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) legalacsonyabb jogosultság | Felhasználói jogosultságok áttekintése

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá a naplózási fiókokhoz, amelyeket érdemes áttekinteni. A fiókokra vonatkozó mutatók áttekintésével meggyőződhet arról, hogy a minimális jogosultsági szintű vezérlők implementálva vannak.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Windows rendszerű virtuális gépek naplózása, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Windows rendszerű virtuális gépek naplózása, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

## <a name="ac-16-security-attributes"></a>AC-16 biztonsági attribútumok

A Azure SQL Database speciális adatbiztonságának adatfelderítési és besorolási funkciója lehetővé teszi az adatbázisok bizalmas adatainak feltárását, osztályozását, címkézését és védelmét. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül. A speciális adatok biztonsága segítséget nyújt a szervezete megfelelő biztonsági attribútumaival kapcsolatos információk biztosításához. Ez a terv [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá a speciális adatbiztonság az SQL Serveren való használatának figyeléséhez és érvényesítéséhez. 

- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) távoli hozzáférés | Automatikus figyelés/vezérlés

Ez a terv segít a távelérés figyelésében és vezérlésében azáltal, hogy [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével figyeli, hogy a Azure app Service alkalmazás távoli hibakeresése ki van-e kapcsolva, valamint olyan házirend-definíciókat, amelyek a távoli hozzáférést lehetővé tevő linuxos virtuális gépek jelszavak nélküli fiókok kapcsolatai. Ez a terv egy Azure Policy-definíciót is hozzárendel, amely segít megfigyelni a nem korlátozott hozzáférést a Storage-fiókokhoz. Ezen mutatók monitorozásával biztosítható, hogy a távelérési módszerek megfeleljenek a biztonsági szabályzatnak.

- \[Előzetes\]verzió: A jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózása
- \[Előzetes\]verzió: Követelmények telepítése a jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózásához
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz
- A távoli hibakeresést ki kell kapcsolni függvényalkalmazás
- Távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Az AU-3 (2) naplózási rekordok tartalma | Tervezett auditálási rekordok központosított kezelése

A Azure Monitor által gyűjtött adatok naplózása egy Log Analytics munkaterületen történik, amely lehetővé teszi a központi konfigurációt és a felügyeletet. Ez a terv segít biztosítani az események naplózását olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek az log Analytics Agent Azure-beli virtuális gépeken való üzembe helyezését naplózzák és érvényesítik.

- \[Előzetes\]verzió: Log Analytics ügynök üzembe helyezésének naplózása – a virtuálisgép-rendszerkép (operációs rendszer) fel van listázva
- \[Előzetes\]verzió: Log Analytics ügynök üzembe helyezésének naplózása a VMSS-ben – nincs listázva a VM-rendszerkép (operációs rendszer)
- \[Előzetes\]verzió: Log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- \[Előzetes\]verzió: A Linux VM Scale Sets (VMSS) Log Analytics-ügynökének üzembe helyezése
- \[Előzetes\]verzió: A Linux rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése
- \[Előzetes\]verzió: A Windows VM Scale Sets (VMSS) Log Analytics ügynökének üzembe helyezése
- \[Előzetes\]verzió: Windows rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Válasz a feldolgozási hibák naplózására

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek figyelik a naplózási és eseménynaplózási konfigurációkat. Ezeknek a konfigurációknak a figyelése a rendszerhiba vagy a helytelen konfiguráció jelzését, valamint a javítási műveletek elvégzését is lehetővé teszi.

- Diagnosztikai beállítás naplózása
- Az SQL Server szintű naplózási beállítások naplózása
- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) audit felülvizsgálat, elemzés és jelentéskészítés | Központi áttekintés és elemzés

A Azure Monitor által összegyűjtött adatok naplózása egy Log Analytics munkaterületen történik, amely lehetővé teszi a központi jelentéskészítést és elemzést. Ez a terv segít biztosítani az események naplózását olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek az log Analytics Agent Azure-beli virtuális gépeken való üzembe helyezését naplózzák és érvényesítik.

- \[Előzetes\]verzió: Log Analytics ügynök üzembe helyezésének naplózása – a virtuálisgép-rendszerkép (operációs rendszer) fel van listázva
- \[Előzetes\]verzió: Log Analytics ügynök üzembe helyezésének naplózása a VMSS-ben – nincs listázva a VM-rendszerkép (operációs rendszer)
- \[Előzetes\]verzió: Log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- \[Előzetes\]verzió: A Linux VM Scale Sets (VMSS) Log Analytics-ügynökének üzembe helyezése
- \[Előzetes\]verzió: A Linux rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése
- \[Előzetes\]verzió: A Windows VM Scale Sets (VMSS) Log Analytics ügynökének üzembe helyezése
- \[Előzetes\]verzió: Windows rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése

## <a name="au-12-audit-generation"></a>AU-12 naplózási generáció

Ez a terv segítséget nyújt a rendszeresemények naplózásához az Azure-erőforrások naplózási beállításait naplózó [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével. Ezek a szabályzat-definíciók a Log Analytics ügynök Azure-beli virtuális gépeken való üzembe helyezését és az egyéb Azure-erőforrástípusok naplózási beállításainak konfigurálását is kikényszerítik. Ezek a szabályzat-definíciók a diagnosztikai naplók konfigurációját is naplózzák, hogy betekintést nyújtsanak az Azure-erőforrásokon belül végrehajtott műveletekre. Emellett a naplózás és a speciális adatbiztonság is konfigurálva van az SQL-kiszolgálókon.

- \[Előzetes\]verzió: Log Analytics ügynök üzembe helyezésének naplózása – a virtuálisgép-rendszerkép (operációs rendszer) fel van listázva
- \[Előzetes\]verzió: Log Analytics ügynök üzembe helyezésének naplózása a VMSS-ben – nincs listázva a VM-rendszerkép (operációs rendszer)
- \[Előzetes\]verzió: Log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- \[Előzetes\]verzió: A Linux VM Scale Sets (VMSS) Log Analytics-ügynökének üzembe helyezése
- \[Előzetes\]verzió: A Linux rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése
- \[Előzetes\]verzió: A Windows VM Scale Sets (VMSS) Log Analytics ügynökének üzembe helyezése
- \[Előzetes\]verzió: Windows rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése
- Diagnosztikai beállítás naplózása
- Az SQL Server szintű naplózási beállítások naplózása
- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Naplózás üzembe helyezése SQL-kiszolgálókon
- Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) a legkevésbé szükséges funkciók | Program végrehajtásának megakadályozása

A Azure Security Center adaptív alkalmazás-vezérlése intelligens, automatizált, teljes körű alkalmazás-engedélyezési megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem kényszerítési módban futtatható, amely tiltja a nem jóváhagyott alkalmazások futtatását. Ez a terv egy Azure Policy-definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, amelyekben az alkalmazás-engedélyezési lista ajánlott, de még nincs konfigurálva.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) a legkevésbé szükséges funkciók | Engedélyezett szoftver/engedélyezési lista

A Azure Security Center adaptív alkalmazás-vezérlése intelligens, automatizált, teljes körű alkalmazás-engedélyezési megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem lehetővé teszi a jóváhagyott alkalmazások listáját a virtuális gépekhez. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, amelyekben az alkalmazás-engedélyezési lista ajánlott, de még nincs konfigurálva.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="cm-11-user-installed-software"></a>CM-11 felhasználó által telepített szoftver

A Azure Security Center adaptív alkalmazás-vezérlése intelligens, automatizált, teljes körű alkalmazás-engedélyezési megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem segítségével érvényesítheti és figyelheti a szoftverkorlátozó házirendek megfelelőségét. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, amelyekben az alkalmazás-engedélyezési lista ajánlott, de még nincs konfigurálva.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="cp-7-alternate-processing-site"></a>CP-7 alternatív feldolgozási hely

Azure Site Recovery replikálja a virtuális gépeken futó munkaterheléseket egy elsődleges helyről a másodlagos helyre. Ha áramkimaradás fordul elő az elsődleges helyen, a munkaterhelés feladatátvétele a másodlagos helyen történik. Ez a tervrajz olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely a virtuális gépeket a vész-helyreállítási beállítások nélkül naplózza. A kijelző figyelése segít biztosítani a szükséges készenléti ellenőrzéseket.

- Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés a Kiemelt jogosultságú fiókokhoz

Ez a terv segít a privilegizált hozzáférés korlátozásában és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével olyan fiókokat naplózni, amelyek tulajdonosi és/vagy írási engedélyekkel nem rendelkeznek a többtényezős hitelesítés engedélyezésével. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés írási engedéllyel rendelkező fiókjain

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) azonosítás és hitelesítés (szervezeti felhasználók) | Nem Kiemelt fiókok hálózati hozzáférése

Ez a terv segítséget nyújt a hozzáférés korlátozásához és vezérléséhez, ha egy [Azure Policy](../../../policy/overview.md) definíció hozzárendelésével naplózza a fiókokat olyan olvasási engedélyekkel, amelyeken nincs engedélyezve a többtényezős hitelesítés. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon

## <a name="ia-5-authenticator-management"></a>IA-5 hitelesítő kezelése

Ez a terv azokat a [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek a Linux rendszerű virtuális gépeket a jelszavak nélküli fiókok távoli kapcsolatainak engedélyezésére és/vagy helytelen engedélyekkel rendelkeznek a passwd fájlban. Ez a terv a Windows rendszerű virtuális gépekhez tartozó jelszó-titkosítási típus konfigurációját naplózó szabályzat-definíciókat is hozzárendeli. Ezen mutatók monitorozásával biztosíthatja, hogy a rendszerhitelesítő megfeleljenek a szervezete azonosítási és hitelesítési házirendjének.

- \[Előzetes\]verzió: A passwd fájl engedélyeivel nem rendelkező linuxos virtuális gépek naplózása 0644 értékre
- \[Előzetes\]verzió: Linux rendszerű virtuális gépek naplózása jelszó nélküli fiókkal
- \[Előzetes\]verzió: A jelszavakat nem tároló Windows rendszerű virtuális gépek naplózása visszafejthető titkosítással
- \[Előzetes\]verzió: Követelmények telepítése olyan linuxos virtuális gépek naplózására, amelyek nem rendelkeznek a passwd fájl engedélyeivel 0644 értékre állítva
- \[Előzetes\]verzió: Követelmények telepítése a jelszavak nélküli fiókkal rendelkező linuxos virtuális gépek naplózására
- \[Előzetes\]verzió: Követelmények telepítése a jelszavakat nem tároló Windows-alapú virtuális gépek naplózásához visszafejthető titkosítással

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) hitelesítő kezelése | Jelszó-alapú hitelesítés

Ez a terv segítséget nyújt az erős jelszavak betartatásához olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek a minimális szilárdságot és egyéb jelszavakat nem igénylő Windows rendszerű virtuális gépeket naplózzák. A virtuális gépeknek a jelszó erőssége megsértésével kapcsolatos ismerete segít megtenni a szükséges javítási műveleteket, hogy az összes virtuálisgép-felhasználói fiók jelszava megfeleljen a szervezete jelszavas házirendjének.

- \[Előzetes\]verzió: Az előző 24 jelszó újbóli használatát lehetővé tevő Windows rendszerű virtuális gépek naplózása
- \[Előzetes\]verzió: A maximális jelszóval nem rendelkező Windows rendszerű virtuális gépek naplózása 70 nap
- \[Előzetes\]verzió: Olyan Windows rendszerű virtuális gépek naplózása, amelyek nem rendelkeznek minimális jelszóval (1 nap)
- \[Előzetes\]verzió: A jelszó-bonyolultsági beállítással nem rendelkező Windows-alapú virtuális gépek naplózása
- \[Előzetes\]verzió: A jelszó minimális hosszát 14 karakterre nem korlátozó Windows-alapú virtuális gépek naplózása
- \[Előzetes\]verzió: A jelszavakat nem tároló Windows rendszerű virtuális gépek naplózása visszafejthető titkosítással
- \[Előzetes\]verzió: Az előző 24 jelszó újbóli használatát lehetővé tevő Windows rendszerű virtuális gépek naplózására vonatkozó követelmények telepítése
- \[Előzetes\]verzió: Követelmények telepítése a maximális jelszóval nem rendelkező Windows rendszerű virtuális gépek naplózásához 70 nap
- \[Előzetes\]verzió: Követelmények telepítése olyan Windows rendszerű virtuális gépek naplózására, amelyek nem rendelkeznek minimális jelszóval (1 nap)
- \[Előzetes\]verzió: A jelszó-bonyolultsági beállítással nem rendelkező Windows rendszerű virtuális gépek naplózási követelményeinek központi telepítése
- \[Előzetes\]verzió: Követelmények telepítése a Windows rendszerű virtuális gépek naplózására, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakternél
- \[Előzetes\]verzió: Követelmények telepítése a jelszavakat nem tároló Windows-alapú virtuális gépek naplózásához visszafejthető titkosítással

## <a name="ra-5-vulnerability-scanning"></a>RA-5 sebezhetőségi vizsgálat

Ez a terv segítséget nyújt az információs rendszer biztonsági réseinak kezeléséhez olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek figyelik az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuális gépek Azure Security Center- A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára. A terv emellett olyan szabályzat-definíciókat is hozzárendel, amelyek az SQL-kiszolgálókon a speciális adatbiztonságot auditálják és érvényesítik. A speciális adatbiztonság biztonsági rések felmérése és a komplex veszélyforrások elleni védelem lehetővé teszi, hogy jobban megértse a telepített erőforrások sebezhetőségeit.

- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A virtuális gépek biztonsági konfigurációjában lévő biztonsági réseket szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="sc-5-denial-of-service-protection"></a>SC-5 szolgáltatásmegtagadás-védelem

Az Azure Distributed szolgáltatásmegtagadás (DDoS) standard csomagja további funkciókat és kockázatcsökkentő képességeket biztosít az alapszintű szolgáltatási szinten. Ezek a további funkciók Azure Monitor integrációt és a támadás utáni kockázatcsökkentő jelentések áttekintését is lehetővé teszi. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely azt naplózza, hogy a DDoS standard szint engedélyezve van-e. A szolgáltatási rétegek közötti képességbeli különbség megismerése segíthet kiválasztani a legjobb megoldást az Azure-környezethez tartozó szolgáltatások megtagadásának kezeléséhez.

- DDoS Protection a standardot engedélyezni kell

## <a name="sc-7-boundary-protection"></a>SC-7 határ védelme

Ez a terv segít a Rendszerhatár kezelésében és szabályozásában azáltal, hogy hozzárendel egy [Azure Policy](../../../policy/overview.md) definíciót, amely figyeli a hálózati biztonsági csoportra vonatkozó ajánlásokat a Azure Security Centerban. Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a potenciális támadási felület csökkentése érdekében.
Emellett a terv a nem védett végpontokat, alkalmazásokat és tárolási fiókokat figyelő szabályzat-definíciókat is hozzárendeli. A tűzfal által nem védett végpontok és alkalmazások, valamint a korlátlan hozzáféréssel rendelkező Storage-fiókok nem kívánt hozzáférést biztosíthatnak az információs rendszeren belül található információkhoz.

- Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani
- Korlátozni kell az internet felé irányuló végponton keresztüli hozzáférést
- A IaaS lévő webalkalmazások NSG-szabályait meg kell erősíteni
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) határ védelme | Hozzáférési pontok

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A JIT virtuális gép hozzáférése segít korlátozni az Azure-ban lévő erőforrások külső kapcsolatainak számát. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincsenek konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) határ védelme | Külső távközlési szolgáltatások

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A JIT virtuális gépekhez való hozzáférés a hozzáférési kérelmek és a jóváhagyási folyamatok megkönnyítésével segíti a forgalomra vonatkozó kivételek kezelését. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincsenek konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) a továbbítás titkossága és integritása | Titkosítási vagy alternatív fizikai védelem

Ez a terv segít a továbbított adatok bizalmas és integritásának védelmében [Azure Policy](../../../policy/overview.md) definíciók kiosztásával, amelyek segítségével figyelheti a kommunikációs protokollok által megvalósított titkosítási mechanizmusokat. A kommunikáció megfelelő titkosítása segíthet a szervezet követelményeinek teljesítésében, illetve az információk jogosulatlan közzétételtől és módosítástól való védelmében.

- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- A biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózása
- Követelmények telepítése a biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózásához
- Alkalmazás függvény csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell
- Webes alkalmazás csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) a REST-adatok védelme | Titkosítási védelem

Ebből a tervből megtudhatja, hogyan kényszerítheti ki a szabályzatot a titkosítási vezérlők használatára, hogy olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendeljen hozzá, amelyek kikényszerítik az adott titkosítási vezérlőket, és naplózzák a gyenge titkosítási beállítások használatát. Annak megismerése, hogy az Azure-erőforrások nem optimális titkosítási konfigurációval rendelkezzenek-e, segítheti a javítási műveleteket, hogy az erőforrások konfigurálása az adatvédelmi szabályzatnak megfelelően történjen. Pontosabban, a tervhez hozzárendelt szabályzat-definíciók titkosítást igényelnek a Storage-fiókokhoz; transzparens adattitkosítás megkövetelése SQL-adatbázisokban; és naplózza a hiányzó titkosítást az SQL-adatbázisokon, a virtuális gépek lemezein és az Automation-fiók változóinak.

- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Az SQL DB transzparens adattitkosításának üzembe helyezése
- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- Titkosítás megkövetelése Data Lake Store fiókokon
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás

## <a name="si-2-flaw-remediation"></a>SI-2 hibák szervizelése

Ez a terv a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, az SQL-biztonsági réseket és a virtuális gépek Azure Security-beli biztonsági réseit figyelő [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével segíti az információs rendszerek hibáinak kezelését. Center. A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára. Ez a terv egy szabályzat-definíciót is hozzárendel, amely biztosítja a virtuálisgép-méretezési csoportok operációs rendszerének javítását.

- Az operációsrendszer-rendszerkép automatikus javításának megkövetelése Virtual Machine Scale Sets
- A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell
- A rendszerfrissítéseket telepíteni kell a virtuális gépekre
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A virtuális gépek biztonsági konfigurációjában lévő biztonsági réseket szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="si-3-malicious-code-protection"></a>SI-3 kártékony kódok védelme

Ez a terv segít az Endpoint Protection kezelésében, beleértve a kártékony programkódok védelmét is, ha olyan [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek figyelik a Azure Security Center virtuális gépei hiányzó Endpoint Protection szolgáltatását, és kikényszerítik a Microsoftot antimalware megoldás a Windows rendszerű virtuális gépeken.

- A Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware-bővítmény telepítése
- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) kártékony kódok védelme | Központi felügyelet

Ez a terv segít az Endpoint Protection kezelésében, beleértve a kártékony programkódok védelmét is, ha olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek a Azure Security Center virtuális gépei hiányzó Endpoint Protection szolgáltatását figyelik. A Azure Security Center központosított felügyeleti és jelentéskészítési képességeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést kapjon az üzembe helyezett Azure-erőforrások biztonsági állapotával.

- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="si-4-information-system-monitoring"></a>SI-4 információs rendszer figyelése

Ez a terv a naplózás és az adatbiztonság Azure-erőforrásokon keresztüli naplózásával és érvényesítésével segíti a rendszerek figyelését. A szabályzatok a Log Analytics ügynök üzembe helyezését, valamint az SQL-adatbázisok, a Storage-fiókok és a hálózati erőforrások fokozott biztonsági beállításait naplózzák és érvényesítik. Ezek a képességek segítenek a rendellenes viselkedés és a támadási mutatók észlelésében, így elvégezheti a megfelelő lépéseket.

- \[Előzetes\]verzió: Log Analytics ügynök üzembe helyezésének naplózása – a virtuálisgép-rendszerkép (operációs rendszer) fel van listázva
- \[Előzetes\]verzió: Log Analytics ügynök üzembe helyezésének naplózása a VMSS-ben – nincs listázva a VM-rendszerkép (operációs rendszer)
- \[Előzetes\]verzió: Log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- \[Előzetes\]verzió: A Linux VM Scale Sets (VMSS) Log Analytics-ügynökének üzembe helyezése
- \[Előzetes\]verzió: A Linux rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése
- \[Előzetes\]verzió: A Windows VM Scale Sets (VMSS) Log Analytics ügynökének üzembe helyezése
- \[Előzetes\]verzió: Windows rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése
- A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon
- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Komplex veszélyforrások elleni védelem üzembe helyezése a Storage-fiókokon
- Naplózás üzembe helyezése SQL-kiszolgálókon
- A Network Watcher üzembe helyezése virtuális hálózatok létrehozásakor
- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) információs rendszer figyelése | Traffic/Covert kiszűrése elemzése

Az Azure Storage komplex veszélyforrások elleni védelme szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. A védelmi riasztások közé tartoznak a rendellenes hozzáférési minták, a rendellenes kibontások/feltöltések és a gyanús tárolási tevékenységek. Ezek a mutatók segítséget nyújtanak a további kiszűrése észlelésében.

- Komplex veszélyforrások elleni védelem üzembe helyezése a Storage-fiókokon

> [!NOTE]
> Az adott Azure Policy-definíciók rendelkezésre állása Azure Government és más nemzeti felhőkben is változhat.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a NIST SP 800-53 R4 terv vezérlési leképezését, az alábbi cikkekben megismerheti a tervet, és bemutatja, hogyan helyezheti üzembe a mintát:

> [!div class="nextstepaction"]
> [NIST SP 800-53 R4 terv – áttekintés](./index.md)
> [NIST SP 800-53 R4 Blueprint – lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.