---
title: Egyesült Királyság hivatalos & Egyesült Királysági NHS-tervezetének mintája
description: Az Egyesült Királyság hivatalos és az Egyesült Királysági NHS-tervezetek mintáinak szabályozása. Minden vezérlő egy vagy több olyan Azure Policy-definícióra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: bf10bcf7064306094664c1ff560187ef77645d4d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627566"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Az Egyesült Királyság hivatalos és az Egyesült Királysági NHS-tervezetek mintáinak szabályozása

A következő cikk azt ismerteti, hogyan jelennek meg az Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetei az Egyesült Királyság hivatalos és egyesült királysági NHS-vezérlőinek. További információ a vezérlőelemekről: [Egyesült Királyság hivatalos](https://www.gov.uk/government/publications/government-security-classifications).

Az alábbi hozzárendelések az **Egyesült Királyság hivatalos** és **Egyesült Királysági NHS** -vezérlőinek. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki az **\[ előzetes \] naplózási Egyesült Királyság hivatalos és egyesült királysági NHS-vezérlőit, és telepítsen speciális virtuálisgép-bővítményeket a naplózási követelmények** beépített házirend-kezdeményezésének támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem egy-az-egyhez vagy egy teljes egyezés egy vezérlőelem és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 adatátviteli védelem

A terv segítségével biztosítható, hogy az Azure-szolgáltatásokkal való adatátvitel biztonságos legyen, ha olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek naplózzák a nem biztonságos kapcsolatokat a Storage-fiókokhoz és Redis cache.

- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A nem biztonságos kommunikációs protokollokat használó Windows-webkiszolgálók naplózási eredményeinek megjelenítése
- A webalkalmazás csak HTTPS protokollon keresztül érhető el
- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el

## <a name="23-data-at-rest-protection"></a>2,3 inaktív adatok védelme

Ez a terv segítséget nyújt a házirendnek a titkosítási vezérlők használatára való érvényesítéséhez, ha olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek kikényszerítik az adott titkosítási vezérlőket, és naplózzák a gyenge titkosítási beállítások használatát. Annak megismerése, hogy az Azure-erőforrások nem optimális titkosítási konfigurációval rendelkezzenek-e, segítheti a javítási műveleteket, hogy az erőforrások konfigurálása az adatvédelmi szabályzatnak megfelelően történjen. Pontosabban, a tervhez hozzárendelt szabályzatok titkosítást igényelnek a Lake Storage-fiókokhoz; transzparens adattitkosítás megkövetelése SQL-adatbázisokban; a Storage-fiókokon, az SQL-adatbázisokon, a virtuálisgép-lemezeken és az Automation-fiók változójában a hiányzó titkosítás naplózása; nem biztonságos kapcsolatok naplózása a Storage-fiókokhoz és a Redis Cache; gyenge virtuális gép jelszavas titkosításának naplózása; és a titkosítatlan Service Fabric kommunikáció naplózása.

- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- Az Automation-fiók változóit titkosítani kell
- Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás
- Az SQL DB transzparens adattitkosításának üzembe helyezése
- Titkosítás megkövetelése Data Lake Store fiókokon
- Engedélyezett helyszínek (az "Egyesült Királyság déli régiója" és az "Egyesült Királyság nyugati régiója" között rögzített)
- Engedélyezett telephelyek az erőforráscsoportok számára (az "Egyesült Királyság déli régiója" és az "Egyesült Királyság nyugati régiója" között rögzített kód)

## <a name="52-vulnerability-management"></a>5,2 biztonsági rések kezelése

Ez a terv segítséget nyújt az információs rendszer biztonsági réseinak kezeléséhez olyan [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével, amelyek figyelik a hiányzó Endpoint Protectiont, a hiányzó rendszerfrissítéseket, az operációs rendszer sebezhetőségeit, az SQL biztonsági réseket és a virtuális Ezek az információk valós idejű információkat biztosítanak az üzembe helyezett erőforrások biztonsági állapotáról, és segíthetnek a Szervizelési műveletek rangsorolásában.

- Hiányzó Endpoint Protection figyelése Azure Security Center
- A rendszerfrissítéseket telepíteni kell a gépeken
- A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- A biztonsági rések felmérését engedélyezni kell a felügyelt SQL-példányon
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A speciális adatbiztonságot engedélyezni kell a felügyelt SQL-példányon
- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon

## <a name="53-protective-monitoring"></a>5,3 védelmi monitorozás

Ez a tervezet segítséget nyújt az információs rendszer eszközeinek védelméhez olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek védelmet nyújtanak a korlátlan hozzáférés, a listázási tevékenységek és a fenyegetések ellen.

- A Storage-fiókoknak korlátoznia kell a hálózati hozzáférést
- Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken
- Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva
- Azure DDoS Protection a standardot engedélyezni kell
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell beállítani az SQL felügyelt példány speciális adatbiztonsági beállításainál
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell állítani az SQL Server speciális adatbiztonsági beállításaiban
- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon
- A Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware-bővítmény telepítése

## <a name="9-secure-user-management"></a>9 biztonságos felhasználói felügyelet 

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz.
A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv segít a hozzáférési jogosultságok korlátozásában és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók kiosztásával, hogy a külső fiókokat a tulajdonossal és/vagy az olvasási/írási engedélyekkel és a tulajdonosi, olvasási és/vagy írási engedélyekkel rendelkező fiókokkal naplózza, amelyeken nincs engedélyezve a többtényezős hitelesítés

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="10-identity-and-authentication"></a>10 identitás és hitelesítés

Ez a terv segít a hozzáférési jogosultságok korlátozásában és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók kiosztásával, hogy a külső fiókokat a tulajdonossal és/vagy az olvasási/írási engedélyekkel és a tulajdonosi, olvasási és/vagy írási engedélyekkel rendelkező fiókokkal naplózza, amelyeken nincs engedélyezve a többtényezős hitelesítés

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a terv Azure Policy definíciókat rendel hozzá az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez

Ez a terv Azure Policy definíciókat is hozzárendeli a naplózási fiókokhoz, amelyeket érdemes áttekinteni, beleértve az értékcsökkenéssel rendelkező fiókokat és a külső fiókokat is. Ha szükséges, a fiókokat le lehet tiltani a bejelentkezés (vagy Eltávolítás) alól, amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat.
Ez a terv két Azure Policy-definíciót rendel hozzá az olyan leértékelt fiókokhoz, amelyeket el kell tekinteni az eltávolításhoz.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a terv egy Azure Policy-definíciót is hozzárendel, amely a Linux rendszerű virtuális gép jelszavas engedélyeinek naplózására vonatkozó engedélyeket naplózza, ha helytelenül vannak beállítva. Ez a kialakítás lehetővé teszi a korrekciós műveletek elvégzését, hogy a hitelesítő adatok ne legyenek biztonságban.

- Olyan Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek a passwd fájl engedélyeivel 0644 értékre állítva

Ez a terv segít az erős jelszavak betartatásában olyan Azure Policy-definíciók hozzárendelésével, amelyek a minimális szilárdságot és egyéb jelszavakat nem érvényesítő Windows-virtuális gépeket naplózzák. A jelszó erősségét sértő virtuális gépek ismerete segít az összes virtuálisgép-felhasználói fiók jelszavának megfelelő javítási műveletek elvégzésében.

- Olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyeken nincs engedélyezve a jelszó bonyolultsága beállítás
- Olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek maximális jelszóval (70 nap)
- Olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek legalább 1 napos jelszóval
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre
- A korábbi 24 jelszó újbóli használatát lehetővé tevő Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése

A terv az Azure-erőforrásokhoz való hozzáférés szabályozását is lehetővé teszi Azure Policy definíciók hozzárendelésével. Ezek a házirendek olyan erőforrástípusok és konfigurációk használatát naplózzák, amelyek lehetővé tehetik az erőforrásokhoz való hozzáférést. A szabályzatok megsértése miatti erőforrások megismerése segíthet az Azure-erőforrások elérését engedélyező, a jogosult felhasználókra korlátozódó kijavítási műveletek elvégzésében.

- Olyan Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekhez jelszó nélküli fiók tartozik
- A Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek lehetővé teszik a távoli kapcsolatokat jelszavak nélküli fiókokból
- A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra
- A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra
- Felügyelt lemezeket nem használó virtuális gépek naplózása

## <a name="11-external-interface-protection"></a>11 külső felület védelme

Ha több mint 25 házirendet használ a megfelelő biztonságos felhasználói felügyelethez, ez a terv a nem korlátozott tárolási fiókokat figyelő [Azure Policy](../../../policy/overview.md) -definíció hozzárendelésével segíti a szolgáltatási felületek jogosulatlan hozzáférés elleni védelmét.
A korlátlan hozzáféréssel rendelkező Storage-fiókok nem kívánt hozzáférést biztosíthatnak az információs rendszeren belül található információkhoz. Ez a terv egy olyan szabályzatot is hozzárendel, amely lehetővé teszi az adaptív alkalmazások vezérlését a virtuális gépeken.

- A Storage-fiókoknak korlátoznia kell a hálózati hozzáférést
- Biztonságos alkalmazások definiálására szolgáló adaptív alkalmazás-vezérlőelemeket engedélyezni kell a gépeken
- Korlátozni kell az internet felé irányuló végponton keresztüli hozzáférést
- Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni
- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- A virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni
- A távoli hibakeresést ki kell kapcsolni a Function apps szolgáltatásban
- A távoli hibakeresést ki kell kapcsolni a webalkalmazásokhoz
- A távoli hibakeresést ki kell kapcsolni API Apps

## <a name="13-audit-information-for-users"></a>13 naplózási információ a felhasználók számára

Ez a terv segítséget nyújt a rendszeresemények naplózásához az Azure-erőforrások naplózási beállításait naplózó [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével.
A hozzárendelt szabályzat azt is naplózza, hogy a virtuális gépek nem küldenek naplókat egy adott log Analytics-munkaterületre.

- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Diagnosztikai beállítás naplózása
- \[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken
- A Network Watcher üzembe helyezése virtuális hálózatok létrehozásakor


## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetének vezérlési leképezését, az alábbi cikkekben megismerheti az áttekintést és a minta üzembe helyezésének módját:

> [!div class="nextstepaction"]
> [Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetei – áttekintés](./index.md) 
>  [Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetei – lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
