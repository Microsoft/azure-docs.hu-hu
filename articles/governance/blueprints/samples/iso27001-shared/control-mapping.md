---
title: ISO 27001 megosztott szolgáltatások tervrajz mintájának vezérlői
description: Az ISO 27001 megosztott szolgáltatások tervrajzi mintájának vezérlése. Minden vezérlő egy vagy több olyan Azure Policy-definícióra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 64266e72bf2aa24ac7fbf70f87f27739709fa0be
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626342"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001 megosztott szolgáltatások tervrajzi mintájának vezérlése

A következő cikk azt ismerteti, hogyan jelennek meg az Azure-tervezetek ISO 27001 megosztott szolgáltatások tervrajza az ISO 27001-vezérlőkhöz. További információ a vezérlőelemekről: [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

A következő leképezések az **ISO 27001:2013** -vezérlőkre vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután megkeresheti és kiválaszthatja az **\[ \] ISO 27001:2013-es auditot, és telepítheti a speciális virtuálisgép-bővítményeket a naplózási követelmények** beépített házirend-kezdeményezésének támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem egy-az-egyhez vagy egy teljes egyezés egy vezérlőelem és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-shared/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A. 6.1.2 feladatai elkülönítése

Csak egy Azure-előfizetéshez tartozó tulajdonos nem engedélyez felügyeleti redundanciát. Ezzel szemben az Azure-előfizetések tulajdonosai is növelhetik a biztonsági réseket egy sérült tulajdonosi fiókon keresztül. Ez a terv segít a megfelelő számú Azure-előfizetési tulajdonos fenntartásában azáltal, hogy két [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá, amelyek az Azure-előfizetések tulajdonosainak számát naplózzák. Az előfizetés tulajdonosi engedélyeinek kezelése segítséget nyújt a feladatok megfelelő elkülönítésének megvalósításában.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

## <a name="a821-classification-of-information"></a>A. 8.2.1 besorolása

Az Azure [SQL sebezhetőség-értékelő szolgáltatása](../../../../azure-sql/database/sql-vulnerability-assessment.md) segít felderíteni az adatbázisaiban tárolt bizalmas adatokat, és javaslatokat is tartalmaz az adatok besorolására. Ez a terv egy [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá az SQL sebezhetőség-értékelési vizsgálat során azonosított sebezhetőségek ellenőrzéséhez.

- Az SQL-adatbázisok biztonsági réseit szervizelni kell

## <a name="a912-access-to-networks-and-network-services"></a>A. 9.1.2-hozzáférés a hálózatokhoz és a hálózati szolgáltatásokhoz

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../../../role-based-access-control/overview.md) segítségével felügyelheti, hogy ki férhet hozzá az Azure-erőforrásokhoz. Ez a terv az Azure-erőforrásokhoz való hozzáférés szabályozását teszi lehetővé a hét [Azure Policy](../../../policy/overview.md) definíció kiosztásával. Ezek a házirendek olyan erőforrástípusok és konfigurációk használatát naplózzák, amelyek lehetővé tehetik az erőforrásokhoz való hozzáférést.
A szabályzatok megsértése miatti erőforrások megismerése segíthet az Azure-erőforrások elérését engedélyező, a jogosult felhasználókra korlátozódó kijavítási műveletek elvégzésében.

- Olyan Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekhez jelszó nélküli fiók tartozik
- A Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek lehetővé teszik a távoli kapcsolatokat jelszavak nélküli fiókokból
- A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra
- A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra
- Felügyelt lemezeket nem használó virtuális gépek naplózása

## <a name="a923-management-of-privileged-access-rights"></a>A. 9.2.3-kezelés az emelt szintű hozzáférési jogosultságokkal

Ez a terv segít a privilegizált hozzáférési jogosultságok korlátozásában és szabályozásában azáltal, hogy négy [Azure Policy](../../../policy/overview.md) definíció hozzárendelésével naplózza a tulajdonossal és/vagy írási engedélyekkel rendelkező külső fiókokat, valamint olyan fiókokat, amelyek tulajdonosi és/vagy írási engedélyekkel rendelkeznek, amelyek nem rendelkeznek a többtényezős Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével felügyelheti, hogy ki férhet hozzá az Azure-erőforrásokhoz. A terv emellett három Azure Policy definíciót is hozzárendel az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését. A terv egy Azure Policy-definíciót is hozzárendel az egyéni Azure RBAC-szabályok használatának naplózásához. Az egyéni Azure RBAC-szabályok megvalósításának megismerése segíthet a szükséges és a megfelelő implementáció ellenőrzésében, mivel az egyéni Azure RBAC-szabályok hibásak.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez
- Egyéni RBAC-szabályok használatának naplózása

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A. a felhasználók titkos hitelesítési adatainak 9.2.4 kezelése

Ez a terv három [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá a többtényezős hitelesítéssel nem rendelkező fiókok naplózásához. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik. A terv két Azure Policy-definíciót is hozzárendel, amelyek a Linux rendszerű virtuális gép jelszavas fájljának engedélyeit naplózzák, ha helytelenül vannak beállítva. Ez a beállítás lehetővé teszi, hogy a hitelesítő adatok biztonságának megelőzése érdekében javítsa a megfelelő lépéseket.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában
- Olyan Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek a passwd fájl engedélyeivel 0644 értékre állítva

## <a name="a925-review-of-user-access-rights"></a>A. 9.2.5-áttekintés a felhasználói hozzáférési jogosultságokról

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../../../role-based-access-control/overview.md) segítségével felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv négy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá a naplózási fiókokhoz, amelyeket érdemes áttekinteni, beleértve az értékcsökkenéssel rendelkező fiókokat és a emelt szintű engedélyekkel rendelkező külső fiókokat.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A. 9.2.6 eltávolítása vagy a hozzáférési jogosultságok módosítása

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../../../role-based-access-control/overview.md) segítségével felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) és az Azure RBAC segítségével frissítheti a felhasználói szerepköröket a szervezeti változások tükrözése érdekében. Ha szükséges, a fiókokat le lehet tiltani a bejelentkezés (vagy Eltávolítás) alól, amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat. Ez a terv két [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá az olyan leértékelt fiókokhoz, amelyeket el kell tekinteni az eltávolításhoz.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

## <a name="a942-secure-log-on-procedures"></a>A. 9.4.2 biztonságos bejelentkezés eljárásai

Ez a terv három Azure Policy definíciót rendel hozzá a többtényezős hitelesítéssel nem rendelkező fiókok naplózásához. Az Azure AD Multi-Factor Authentication további biztonságot nyújt a hitelesítés második formáját igényli, és erős hitelesítést biztosít. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában

## <a name="a943-password-management-system"></a>A. 9.4.3 jelszavas felügyeleti rendszer

Ez a terv segít kikényszeríteni az erős jelszavakat olyan 10 [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek olyan Windows rendszerű virtuális gépeket naplóznak, amelyek nem érvényesítik a minimális szilárdság A jelszó erősségét sértő virtuális gépek ismerete segít az összes virtuálisgép-felhasználói fiók jelszavának megfelelő javítási műveletek elvégzésében.

- Olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyeken nincs engedélyezve a jelszó bonyolultsága beállítás
- Olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek maximális jelszóval (70 nap)
- Olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek legalább 1 napos jelszóval
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre
- A korábbi 24 jelszó újbóli használatát lehetővé tevő Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A. 10.1.1 szabályzat a titkosítási vezérlők használatára

Ez a terv segít kikényszeríteni a szabályzatot a kriptográfiai vezérlők használatára úgy, hogy 13 [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá, amelyek bizonyos titkosítási vezérlőket kényszerítenek, és a gyenge titkosítási beállítások naplózását használják. Annak megismerése, hogy az Azure-erőforrások nem optimális titkosítási konfigurációval rendelkezzenek-e, segítheti a javítási műveleteket, hogy az erőforrások konfigurálása az adatvédelmi szabályzatnak megfelelően történjen. Pontosabban, a tervhez hozzárendelt szabályzatok titkosítást igényelnek a blob Storage-fiókokhoz és a adatlake Storage-fiókokhoz; transzparens adattitkosítás megkövetelése SQL-adatbázisokban; a Storage-fiókokon, az SQL-adatbázisokon, a virtuálisgép-lemezeken és az Automation-fiók változójában a hiányzó titkosítás naplózása; nem biztonságos kapcsolatok naplózása a Storage-fiókok, a Function apps, a webalkalmazás, a API Apps és a Redis Cache használatával; gyenge virtuális gép jelszavas titkosításának naplózása; és a titkosítatlan Service Fabric kommunikáció naplózása.

- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- A webalkalmazás csak HTTPS protokollon keresztül érhető el
- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- A jelszavakat visszafejthető titkosítással nem tároló Windows rendszerű virtuális gépekről származó naplózási eredmények megjelenítése
- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- Az Automation-fiók változóit titkosítani kell
- Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás

## <a name="a1241-event-logging"></a>A. 12.4.1 eseménynaplózása

Ez a terv segítséget nyújt a rendszeresemények naplózásához, ha hét [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá, amelyek naplózzák az Azure-erőforrások naplózási beállításait.
A diagnosztikai naplók betekintést nyújtanak az Azure-erőforrásokon belül végrehajtott műveletekre.

- A függőségi ügynök üzembe helyezésének naplózása – a virtuálisgép-rendszerkép (operációs rendszer) fel van listázva
- A függőségi ügynök üzembe helyezése a virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- [Előzetes verzió]: naplózás Log Analytics ügynök üzembe helyezése – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- Diagnosztikai beállítás naplózása
- Az SQL Server naplózását engedélyezni kell

## <a name="a1243-administrator-and-operator-logs"></a>A. 12.4.3-rendszergazdai és-kezelői naplók

Ez a terv segítséget nyújt a rendszeresemények naplózásához, ha hét Azure Policy definíciót rendel hozzá, amelyek naplózzák az Azure-erőforrások naplózási beállításait. A diagnosztikai naplók betekintést nyújtanak az Azure-erőforrásokon belül végrehajtott műveletekre.

- A függőségi ügynök üzembe helyezésének naplózása – a virtuálisgép-rendszerkép (operációs rendszer) fel van listázva
- A függőségi ügynök üzembe helyezése a virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- [Előzetes verzió]: naplózás Log Analytics ügynök üzembe helyezése – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- Diagnosztikai beállítás naplózása
- Az SQL Server naplózását engedélyezni kell

## <a name="a1244-clock-synchronization"></a>A. 12.4.4 óra szinkronizálása

Ez a terv segítséget nyújt a rendszeresemények naplózásához, ha hét Azure Policy definíciót rendel hozzá, amelyek naplózzák az Azure-erőforrások naplózási beállításait. Az Azure-naplók a szinkronizált belső órákat használják az események időbeli korrelációs rekordjának létrehozásához az erőforrások között.

- A függőségi ügynök üzembe helyezésének naplózása – a virtuálisgép-rendszerkép (operációs rendszer) fel van listázva
- A függőségi ügynök üzembe helyezése a virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- [Előzetes verzió]: naplózás Log Analytics ügynök üzembe helyezése – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – nincs listázva a virtuális gép rendszerképe (operációs rendszer)
- Diagnosztikai beállítás naplózása
- Az SQL Server naplózását engedélyezni kell

## <a name="a1251-installation-of-software-on-operational-systems"></a>A. 12.5.1 szoftver telepítése az operatív rendszereken

Az adaptív alkalmazások vezérlése Azure Security Center megoldás, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban található virtuális gépeken. Ez a tervrajz olyan Azure Policy-definíciót rendel hozzá, amely figyeli az engedélyezett alkalmazások készletének változásait. Ez a funkció segítséget nyújt a szoftverek és alkalmazások Azure-beli virtuális gépeken való telepítésének szabályozásában.

- Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken

## <a name="a1261-management-of-technical-vulnerabilities"></a>A. a technikai sebezhetőségek 12.6.1 kezelése

Ebből a tervből megtudhatja, hogyan kezelheti az információs rendszer biztonsági réseit úgy, hogy öt [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, Azure Security Center az SQL biztonsági réseket és a virtuális gép A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára.

- Hiányzó Endpoint Protection figyelése Azure Security Center
- A rendszerfrissítéseket telepíteni kell a gépeken
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni

## <a name="a1262-restrictions-on-software-installation"></a>A szoftver telepítésének. 12.6.2 korlátozása

Az adaptív alkalmazások vezérlése Azure Security Center megoldás, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban található virtuális gépeken. Ez a tervrajz olyan Azure Policy-definíciót rendel hozzá, amely figyeli az engedélyezett alkalmazások készletének változásait. A Szoftvertelepítés korlátozásai segíthetnek csökkenteni a szoftveres biztonsági rések bevezetésének valószínűségét.

- Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken

## <a name="a1311-network-controls"></a>A. 13.1.1 hálózati vezérlők

Ez a terv segít a hálózatok kezelésében és szabályozásában egy olyan [Azure Policy](../../../policy/overview.md) -definíció hozzárendelésével, amely a hálózati biztonsági csoportokat a megengedő szabályokkal figyeli. A túl megengedhető szabályok lehetővé tehetik a nem kívánt hálózati hozzáférést, és azt felül kell vizsgálni. Ez a terv a nem védett végpontok, alkalmazások és Storage-fiókok figyelésére szolgáló három Azure Policy definíciót is hozzárendeli. A tűzfal által nem védett végpontok és alkalmazások, valamint a korlátlan hozzáféréssel rendelkező Storage-fiókok nem kívánt hozzáférést biztosíthatnak az információs rendszeren belül található információkhoz.

- Korlátozni kell az internet felé irányuló végponton keresztüli hozzáférést
- A Storage-fiókoknak korlátoznia kell a hálózati hozzáférést

## <a name="a1321-information-transfer-policies-and-procedures"></a>A. 13.2.1-adatátviteli szabályzatok és eljárások

A terv segítségével gondoskodhat arról, hogy az Azure-szolgáltatásokkal való adatátvitel biztonságos legyen, ha két [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá a nem biztonságos kapcsolatok naplózásához a Storage-fiókokhoz és a Redis cache.

- Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az ISO 27001 megosztott szolgáltatások tervének vezérlési leképezését, az alábbi cikkekben megismerheti az architektúrát és a minta üzembe helyezésének módját:

> [!div class="nextstepaction"]
> [ISO 27001 megosztott szolgáltatások tervrajza – áttekintés](./index.md) 
>  [ISO 27001 megosztott szolgáltatások tervrajza – lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
