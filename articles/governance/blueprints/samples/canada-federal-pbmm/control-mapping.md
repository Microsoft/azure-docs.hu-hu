---
title: Kanadai szövetségi PBMM terv minta vezérlők
description: A kanadai szövetségi PBMM tervrajzi mintáinak leképezése. Minden vezérlő egy vagy több olyan Azure Policy-definícióra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 440b07db514d8af0cf7f4177a6bac497eead2b37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627214"
---
# <a name="control-mapping-of-the-canada-federal-pbmm-blueprint-sample"></a>A Kanada szövetségi PBMM tervezetének vezérlési mintája

A következő cikk azt ismerteti, hogyan történik az Azure-tervezetek kanadai szövetségi védett B, közepes integritású, közepes rendelkezésre állású (PBMM) tervrajza a Kanada szövetségi PBMM-vezérlőinek. További információ a vezérlőelemekről: [Kanada szövetségi PBMM](https://www.canada.ca/en/government/system/digital-government/digital-government-innovations/cloud-services/government-canada-security-control-profile-cloud-based-it-services.html).

A következő leképezések a **Kanada szövetségi PBMM** -vezérlői. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezt követően keresse meg és válassza ki az **\[ előnézetet \] : az audit Canada Federal PBMM Controls** beépített Policy Initiative.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem egy-az-egyhez vagy egy teljes egyezés egy vezérlőelem és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/canada-federal-pbmm/control-mapping.md).

## <a name="location-constraints"></a>Hely megkötései

Ez a terv segítséget nyújt az összes erőforrás és erőforráscsoport központi telepítésének helyének korlátozásához a "Kanada középső régiója" és "Kelet-Kanada" számára a következő Azure Policy-definíciók kiosztásával:

- Engedélyezett helyszínek (a "Kanada középső régiója" és "Kelet-Kanada" között rögzített)
- Engedélyezett telephelyek az erőforráscsoportok számára (a "Kanada középső régiója" és "Kelet-Kanada" között rögzített kód)

## <a name="ac-2-account-management"></a>AC-2 fiókok kezelése

Ez a terv segít áttekinteni azokat a fiókokat, amelyek esetleg nem felelnek meg a szervezete fiókjának felügyeleti követelményeinek. Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek az előfizetésre és az elavult fiókokra vonatkozó olvasási, írási és tulajdonosi engedélyekkel auditálják a külső fiókokat. A szabályzatok által auditált fiókok áttekintésével megteheti a megfelelő lépéseket a fiókok kezelésével kapcsolatos követelmények teljesítése érdekében.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből


## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Fiókkezelés | Role-Based sémák

Az Azure [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../../../role-based-access-control/overview.md) valósít meg, amellyel kezelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat is HOZZÁRENDEL az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését. A terv emellett egy Azure Policy definíciót rendel hozzá az egyéni Azure RBAC-szabályok használatának naplózásához. Az egyéni Azure RBAC-szabályok megvalósításának megismerése segíthet a szükséges és a megfelelő implementáció ellenőrzésében, mivel az egyéni Azure RBAC-szabályok hibásak.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez

## <a name="ac-4-information-flow-enforcement"></a>AC-4 információáramlás kényszerítése

A több eredetű erőforrás-megosztás (CORS) lehetővé teszi App Services-erőforrások kérését egy külső tartományból. A Microsoft azt javasolja, hogy csak a szükséges tartományokat engedélyezze az API-val, a funkcióval és a webalkalmazásokkal való interakcióhoz. Ez a terv egy [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá, amely segít a CORS-erőforrások hozzáférési korlátozásának figyelésében Azure Security Centerban. A CORS-implementációk ismertetése segít ellenőrizni, hogy az információáramlási vezérlők implementálva vannak-e.

- A CORS nem teszi lehetővé minden erőforrás számára a webalkalmazások elérését

## <a name="ac-5-separation-of-duties"></a>A feladatok AC-5 elkülönítése

Csak egy Azure-előfizetéshez tartozó tulajdonos nem engedélyez felügyeleti redundanciát. Ezzel szemben az Azure-előfizetések tulajdonosai is növelhetik a biztonsági réseket egy sérült tulajdonosi fiókon keresztül. Ez a tervezet segítséget nyújt a megfelelő számú Azure-előfizetési tulajdonos fenntartásához olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek az Azure-előfizetések tulajdonosainak számát naplózzák. A terv emellett Azure Policy definíciókat is hozzárendel, amelyek segítenek a rendszergazdák csoport tagságának vezérlésében a Windows rendszerű virtuális gépeken. Az előfizetés tulajdonosa és a virtuális gép rendszergazdai engedélyeinek kezelése segíthet a feladatok megfelelő elkülönítésének megvalósításában.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- Windows rendszerű gépek naplózása, amelyeknek a megadott tagjai vannak a rendszergazdák csoportban
- A rendszergazdák csoport megadott tagjaiból hiányzó Windows-gépek naplózása

## <a name="ac-6-least-privilege"></a>Legalább AC-6 jogosultság

Az Azure [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../../../role-based-access-control/overview.md) valósít meg, amellyel kezelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá a naplózási fiókokhoz, amelyeket érdemes áttekinteni. A fiókokra vonatkozó mutatók áttekintésével meggyőződhet arról, hogy a minimális jogosultsági szintű vezérlők implementálva vannak.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- Windows rendszerű gépek naplózása, amelyeknek a megadott tagjai vannak a rendszergazdák csoportban
- A rendszergazdák csoport megadott tagjaiból hiányzó Windows-gépek naplózása

## <a name="ac-7-security-attributes"></a>AC-7 biztonsági attribútumok

A Azure SQL Database speciális adatbiztonságának adatfelderítési és besorolási funkciója lehetővé teszi az adatbázisok bizalmas adatainak feltárását, osztályozását, címkézését és védelmét. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül. A speciális adatok biztonsága segítséget nyújt a szervezete megfelelő biztonsági attribútumaival kapcsolatos információk biztosításához. Ez a terv [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá a speciális adatbiztonság az SQL Serveren való használatának figyeléséhez és érvényesítéséhez.

- A speciális adatbiztonságot engedélyezni kell a felügyelt SQL-példányon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) távoli hozzáférés | Automatikus figyelés/vezérlés

Ez a terv segít a távelérés figyelésében és vezérlésében azáltal, hogy [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével figyeli, hogy a Azure app Service alkalmazás távoli hibakeresése ki van-e kapcsolva. A terv emellett olyan szabályzat-definíciókat is hozzárendel, amelyek a jelszavak nélküli fiókok távoli kapcsolatait engedélyező linuxos virtuális gépeket naplózzák. Emellett a terv egy Azure Policy-definíciót rendel hozzá, amely segítséget nyújt a Storage-fiókok korlátozás nélküli elérésének figyelésében. Ezen mutatók monitorozásával biztosítható, hogy a távelérési módszerek megfeleljenek a biztonsági szabályzatnak.

- A Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek lehetővé teszik a távoli kapcsolatokat jelszavak nélküli fiókokból
- A Storage-fiókoknak korlátoznia kell a hálózati hozzáférést
- A távoli hibakeresést ki kell kapcsolni API Apps
- A távoli hibakeresést ki kell kapcsolni a Function apps szolgáltatásban
- A távoli hibakeresést ki kell kapcsolni a webalkalmazásokhoz

## <a name="au-3-2-content-of-audit-records"></a>Az AU-3 (2) naplózási rekordok tartalma

A Azure Monitor által gyűjtött adatok naplózása egy Log Analytics munkaterületen történik, amely lehetővé teszi a központi konfigurációt és a felügyeletet. Ez a terv segít biztosítani az események naplózását olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek az log Analytics Agent Azure-beli virtuális gépeken való üzembe helyezését naplózzák és érvényesítik.

- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- Log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Válasz a feldolgozási hibák naplózására

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek figyelik a naplózási és eseménynaplózási konfigurációkat. Ezeknek a konfigurációknak a figyelése a rendszerhiba vagy a helytelen konfiguráció jelzését, valamint a javítási műveletek elvégzését is lehetővé teszi.

- Diagnosztikai beállítás naplózása
- Az SQL Server naplózását engedélyezni kell
- A speciális adatbiztonságot engedélyezni kell a felügyelt SQL-példányon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) audit felülvizsgálat, elemzés és jelentéskészítés | Központi áttekintés és elemzés

A Azure Monitor által összegyűjtött adatok naplózása egy Log Analytics munkaterületen történik, amely lehetővé teszi a központi jelentéskészítést és elemzést. Ez a terv segít biztosítani az események naplózását olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek az log Analytics Agent Azure-beli virtuális gépeken való üzembe helyezését naplózzák és érvényesítik.

- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- Log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken

## <a name="au-12-audit-generation"></a>AU-12 naplózási generáció

Ez a terv segítséget nyújt a rendszeresemények naplózásához az Azure-erőforrások naplózási beállításait naplózó [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével.
Ezek a szabályzat-definíciók a Log Analytics ügynök Azure-beli virtuális gépeken való üzembe helyezését és az egyéb Azure-erőforrástípusok naplózási beállításainak konfigurálását is kikényszerítik. Ezek a szabályzat-definíciók a diagnosztikai naplók konfigurációját is naplózzák, hogy betekintést nyújtsanak az Azure-erőforrásokon belül végrehajtott műveletekre. Emellett a naplózás és a speciális adatbiztonság is konfigurálva van az SQL-kiszolgálókon.

- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- Log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése

- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken
- Diagnosztikai beállítás naplózása
- Az SQL Server naplózását engedélyezni kell
- A speciális adatbiztonságot engedélyezni kell a felügyelt SQL-példányon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Naplózás üzembe helyezése SQL-kiszolgálókon
- Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) a legkevésbé szükséges funkciók | Engedélyezett szoftver/engedélyezési lista

A Azure Security Center adaptív alkalmazás-vezérlése intelligens, automatizált, teljes körű alkalmazás-engedélyezési lista megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem lehetővé teszi a jóváhagyott alkalmazások listáját a virtuális gépekhez. Ez a tervrajz olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, ahol az alkalmazás engedélyezési listája ajánlott, de még nincs konfigurálva.

- Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken

## <a name="cm-11-user-installed-software"></a>CM – 11 User-Installed szoftver

A Azure Security Center adaptív alkalmazás-vezérlése intelligens, automatizált, teljes körű alkalmazás-engedélyezési lista megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem segítségével érvényesítheti és figyelheti a szoftverkorlátozó házirendek megfelelőségét. Ez a tervrajz olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, ahol az alkalmazás engedélyezési listája ajánlott, de még nincs konfigurálva.

- Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken

## <a name="cp-7-alternate-processing-site"></a>CP-7 alternatív feldolgozási hely

Azure Site Recovery replikálja a virtuális gépeken futó munkaterheléseket egy elsődleges helyről a másodlagos helyre. Ha áramkimaradás fordul elő az elsődleges helyen, a munkaterhelés feladatátvétele a másodlagos helyen történik. Ez a tervrajz olyan [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely a virtuális gépeket a vész-helyreállítási beállítások nélkül naplózza. A kijelző figyelése segít biztosítani a szükséges készenléti ellenőrzéseket.

- Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés a Kiemelt jogosultságú fiókokhoz

Ez a terv segít a privilegizált hozzáférés korlátozásában és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével olyan fiókokat naplózni, amelyek tulajdonosi és/vagy írási engedélyekkel nem rendelkeznek a többtényezős hitelesítés engedélyezésével. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában

## <a name="ia-5-authenticator-management"></a>IA-5 hitelesítő kezelése

Ez a terv azokat a [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek a Linux rendszerű virtuális gépeket a jelszavak nélküli fiókok távoli kapcsolatainak engedélyezésére és/vagy helytelen engedélyekkel rendelkeznek a passwd fájlban. Ez a terv a Windows rendszerű virtuális gépekhez tartozó jelszó-titkosítási típus konfigurációját naplózó szabályzat-definíciókat is hozzárendeli. Ezen mutatók monitorozásával biztosíthatja, hogy a rendszerhitelesítő megfeleljenek a szervezete azonosítási és hitelesítési házirendjének.

- Olyan Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek a passwd fájl engedélyeivel 0644 értékre állítva
- Olyan Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekhez jelszó nélküli fiók tartozik

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) hitelesítő kezelése | Password-Based hitelesítés

Ez a terv segítséget nyújt az erős jelszavak betartatásához olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek a minimális szilárdságot és egyéb jelszavakat nem igénylő Windows rendszerű virtuális gépeket naplózzák. A virtuális gépeknek a jelszó erőssége megsértésével kapcsolatos ismerete segít megtenni a szükséges javítási műveleteket, hogy az összes virtuálisgép-felhasználói fiók jelszava megfeleljen a szervezete jelszavas házirendjének.

- A korábbi 24 jelszó újbóli használatát lehetővé tevő Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése
- Olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek maximális jelszóval (70 nap)
- Olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek legalább 1 napos jelszóval
- Olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyeken nincs engedélyezve a jelszó bonyolultsága beállítás
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre

## <a name="ia-8-100-identification-and-authentication-non-organizational-users--identity-and-credential-assurance-levels"></a>IA-8 (100) azonosítás és hitelesítés (nem szervezeti felhasználók) | Identitás-és hitelesítő adatok megbízhatósági szintjei

Ez a terv segít a privilegizált hozzáférés korlátozásában és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével olyan fiókokat naplózni, amelyek tulajdonosi és/vagy írási engedélyekkel nem rendelkeznek a többtényezős hitelesítés engedélyezésével. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában

## <a name="ra-5-vulnerability-scanning"></a>RA-5 sebezhetőségi vizsgálat

Ez a terv segítséget nyújt az információs rendszer biztonsági réseinak kezeléséhez olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek figyelik az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuális gépek Azure Security Center-
A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára. A terv emellett olyan szabályzat-definíciókat is hozzárendel, amelyek az SQL-kiszolgálókon a speciális adatbiztonságot auditálják és érvényesítik. A speciális adatbiztonság biztonsági rések felmérése és a komplex veszélyforrások elleni védelem lehetővé teszi, hogy jobban megértse a telepített erőforrások sebezhetőségeit.

- A speciális adatbiztonságot engedélyezni kell a felügyelt SQL-példányon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="sc-5-denial-of-service-protection"></a>SC-5 szolgáltatásmegtagadás-védelem

Az Azure Distributed szolgáltatásmegtagadás (DDoS) standard csomagja további funkciókat és kockázatcsökkentő képességeket biztosít az alapszintű szolgáltatási szinten. Ezek a további funkciók Azure Monitor integrációt és a támadás utáni kockázatcsökkentő jelentések áttekintését is lehetővé teszi. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely azt naplózza, hogy a DDoS standard szint engedélyezve van-e. A szolgáltatási rétegek közötti képességbeli különbség megismerése segíthet kiválasztani a legjobb megoldást az Azure-környezethez tartozó szolgáltatások megtagadásának kezeléséhez.

- DDoS Protection a standardot engedélyezni kell

## <a name="sc-7-boundary-protection"></a>SC-7 határ védelme

Ez a terv segít a Rendszerhatár kezelésében és szabályozásában azáltal, hogy hozzárendel egy [Azure Policy](../../../policy/overview.md) definíciót, amely figyeli a hálózati biztonsági csoportra vonatkozó ajánlásokat a Azure Security Centerban. Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a potenciális támadási felület csökkentése érdekében. Emellett a terv a nem védett végpontokat, alkalmazásokat és tárolási fiókokat figyelő szabályzat-definíciókat is hozzárendeli. A tűzfal által nem védett végpontok és alkalmazások, valamint a korlátlan hozzáféréssel rendelkező Storage-fiókok nem kívánt hozzáférést biztosíthatnak az információs rendszeren belül található információkhoz.

- Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni
- Korlátozni kell az internet felé irányuló végponton keresztüli hozzáférést
- A Storage-fiókoknak korlátoznia kell a hálózati hozzáférést

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) határ védelme | Hozzáférési pontok

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A JIT virtuális gép hozzáférése segít korlátozni az Azure-ban lévő erőforrások külső kapcsolatainak számát. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincs konfigurálva.

- Azure DDoS Protection a standardot engedélyezni kell

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) határ védelme | Külső távközlési szolgáltatások

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A JIT virtuális gépekhez való hozzáférés a hozzáférési kérelmek és a jóváhagyási folyamatok megkönnyítésével segíti a forgalomra vonatkozó kivételek kezelését. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincs konfigurálva.

- Azure DDoS Protection a standardot engedélyezni kell

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) a továbbítás titkossága és integritása | Titkosítási vagy alternatív fizikai védelem

Ez a terv segít a továbbított adatok bizalmas és integritásának védelmében [Azure Policy](../../../policy/overview.md) definíciók kiosztásával, amelyek segítségével figyelheti a kommunikációs protokollok által megvalósított titkosítási mechanizmusokat. A kommunikáció megfelelő titkosítása segíthet a szervezet követelményeinek teljesítésében, illetve az információk jogosulatlan közzétételtől és módosítástól való védelmében.

- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- A nem biztonságos kommunikációs protokollokat használó Windows-webkiszolgálók naplózási eredményeinek megjelenítése
- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges
- A webalkalmazás csak HTTPS protokollon keresztül érhető el
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt

## <a name="sc-28-1-protection-of-information-at-rest"></a>SC-28 (1) információk védelme a nyugalmi állapotban

Ebből a tervből megtudhatja, hogyan kényszerítheti ki a szabályzatot a titkosítási vezérlők használatára, hogy olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendeljen hozzá, amelyek kikényszerítik az adott titkosítási vezérlőket, és naplózzák a gyenge titkosítási beállítások használatát. Annak megismerése, hogy az Azure-erőforrások nem optimális titkosítási konfigurációval rendelkezzenek-e, segítheti a javítási műveleteket, hogy az erőforrások konfigurálása az adatvédelmi szabályzatnak megfelelően történjen. Pontosabban, a tervhez hozzárendelt szabályzat-definíciók titkosítást igényelnek a Storage-fiókokhoz; transzparens adattitkosítás megkövetelése SQL-adatbázisokban; és naplózza a hiányzó titkosítást az SQL-adatbázisokon, a virtuális gépek lemezein és az Automation-fiók változóinak.

- A speciális adatbiztonságot engedélyezni kell a felügyelt SQL-példányon
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
- A rendszerfrissítéseket telepíteni kell a gépeken
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
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
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- Log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken
- A speciális adatbiztonságot engedélyezni kell a felügyelt SQL-példányon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Komplex veszélyforrások elleni védelem üzembe helyezése a Storage-fiókokon
- Naplózás üzembe helyezése SQL-kiszolgálókon
- A Network Watcher üzembe helyezése virtuális hálózatok létrehozásakor
- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon

> [!NOTE]
> Az adott Azure Policy-definíciók rendelkezésre állása Azure Government és más nemzeti felhőkben is változhat. 

## <a name="next-steps"></a>Következő lépések

Áttekintette a kanadai szövetségi PBMM terv mintájának vezérlési leképezését. A következő cikkekben megismerheti az áttekintést és a minta üzembe helyezésének módját:

> [!div class="nextstepaction"]
> [Kanadai szövetségi PBMM terv – áttekintés](./control-mapping.md) 
>  [Kanadai szövetségi PBMM terv – lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
