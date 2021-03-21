---
title: Ausztrál kormány, az ISM által védett terv minta-vezérlőinek
description: Az ausztrál kormány és a védett tervekhez tartozó ISM-minta hozzárendelésének vezérlése. Minden vezérlő egy vagy több olyan Azure Policy-definícióra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 01/21/2021
ms.topic: sample
ms.openlocfilehash: df143a871db3d871fe6354eab23b7ca6dc012b78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98679997"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Az Australian Government által védett tervezetek mintájának vezérlése

A következő cikk azt ismerteti, hogyan mutatja be az Azure-tervezetek az ausztrál kormánynak az ISM-ben védett tervezeteket az ISM által védett vezérlőkre. A vezérlőkkel kapcsolatos további információkért lásd: [védett ISM](https://www.cyber.gov.au/ism).

A következő leképezések az ISM- **védelemmel ellátott** vezérlőkre vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki az **\[ előnézetet \] : az Australian Government ISM által védett vezérlők naplózása és speciális virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények** beépített házirend-kezdeményezésének támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem egy-az-egyhez vagy egy teljes egyezés egy vezérlőelem és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md).


## <a name="location-constraints"></a>Hely megkötései

Ez a terv segítséget nyújt az összes erőforrás és erőforráscsoport központi telepítésének helyének korlátozására az "ausztráliai központi", "Australia Central2", "Kelet-Ausztrália" és "Délkelet-Ausztrália" számára a következő Azure Policy definíciók kiosztásával:

- Engedélyezett helyszínek (az "Ausztrália középső régiója", "Ausztráliában Central2", "Kelet-Ausztrália" és "Délkelet-Ausztrália")
- Az erőforráscsoportok engedélyezett helyei (az "Ausztrália középső régiója", "Australia Central2", "Kelet-Ausztrália" és "Délkelet-Ausztrália")

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>Irányelvek a személyzet biztonságához – a rendszerekhez és azok erőforrásaihoz való hozzáférés

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 a személyzet egy rendszerhez és erőforrásaihoz való hozzáférése egyedi módon azonosítható

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 a rendszerekhez, alkalmazásokhoz és adattárakhoz való szabványos hozzáférés arra korlátozódik, hogy a személyzet köteles felvenni feladataikat

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Előfeltételek telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 a rendszerjogosultságú hozzáférés a rendszerekhez, az alkalmazásokhoz és az adattárakhoz, amikor az első alkalommal kérelmezik és újra érvényesítik azokat egy éves vagy gyakoribb módon

- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Előfeltételek telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 rendszerjogosultságú hozzáférés a rendszerekhez, alkalmazásokhoz és adattárakhoz arra korlátozódik, hogy a személyzet köteles felvenni feladataikat

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Előfeltételek telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 a megosztott felhasználói fiókok használata szigorúan szabályozott, és az ilyen fiókokat használó személyzet egyedileg azonosítható

- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Előfeltételek telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 a Kiemelt jogosultságú felhasználók dedikált emelt szintű fiókot rendelnek hozzá kizárólag az emelt szintű hozzáférést igénylő feladatokhoz

- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza
- Előfeltételek telepítése a Windows rendszerű virtuális gépek naplózására, amelyekben a rendszergazdák csoport a megadott tagok bármelyikét tartalmazza

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 a rendszerekhez, alkalmazásokhoz és adattárakhoz való hozzáférés az ugyanazon a napon már nem jogosult a hozzáférésre.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 ha a személyzet ideiglenes hozzáférést kap egy rendszerhez, az érvényben lévő biztonsági ellenőrzésekkel korlátozhatja, hogy a hozzáférésük csak a számukra szükséges adatokhoz legyen felkészítve

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>Irányelvek a rendszer megerősítéséhez – operációs rendszer megerősítése

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 az operációs rendszer legújabb verziója (N) vagy N-1 verziója a szabványos operációs környezetekhez (SOEs) használható.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 nem szükséges operációsrendszer-fiókok, szoftverek, összetevők, szolgáltatások és funkciók eltávolítása vagy letiltása

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 az alkalmazás-engedélyezési megoldás minden kiszolgálón implementálva van a végrehajtható fájlok, a szoftverek kódtárai, a parancsfájlok és a telepítők jóváhagyott készletre való végrehajtásának korlátozására.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 víruskereső szoftver van implementálva a munkaállomásokon és kiszolgálókon, és konfigurálva van a következő: aláírás-alapú észlelés engedélyezve és magas szintű, heurisztikus észlelésen alapuló észlelésre van beállítva, és magas szintű, az észlelési aláírásokra van beállítva, és legalább napi rendszerességgel, automatikus és rendszeres vizsgálattal van konfigurálva az összes rögzített lemezre

- A Microsoft IaaSAntimalware bővítményt Windows-kiszolgálókon kell telepíteni
- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>Irányelvek a rendszer megerősítéséhez – hitelesítés megerősítése

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 a felhasználók hitelesítése megtörtént, mielőtt hozzáférést kapnak egy rendszerhez és annak erőforrásaihoz

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez
- A Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek lehetővé teszik a távoli kapcsolatokat jelszavak nélküli fiókokból
- A jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózásának előfeltételei üzembe helyezése
- Olyan Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekhez jelszó nélküli fiók tartozik
- Előfeltételek telepítése a jelszavak nélküli fiókkal rendelkező linuxos virtuális gépek naplózásához

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 a többtényezős hitelesítés az általános jogú felhasználók hitelesítésére szolgál

- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 a többtényezős hitelesítés az összes Kiemelt felhasználó és bármely más megbízhatósági kapcsolat hitelesítésére szolgál

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 az egytényezős hitelesítéshez használt hozzáférési kódok legalább 14 karakterből állnak, és ideális esetben 4 véletlenszerű kifejezéssel rendelkeznek

- A Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a "biztonsági beállítások – fiókok házirendjeiben"
- Előfeltételek telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a "biztonsági beállítások – fiók szabályzatokban"

## <a name="guidelines-for-system-management---system-administration"></a>Irányelvek a rendszerkezeléshez – rendszerfelügyelet

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 a többtényezős hitelesítés a felhasználók hitelesítésére szolgál minden alkalommal, amikor Kiemelt műveleteket végeznek

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 a felügyeleti forgalom csak a rendszerek és alkalmazások felügyeletéhez használt hálózati zónákból lehetséges.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- A távoli hibakeresést ki kell kapcsolni API Apps
- A távoli hibakeresést ki kell kapcsolni a Function apps szolgáltatásban
- A távoli hibakeresést ki kell kapcsolni a webalkalmazásokhoz

## <a name="guidelines-for-system-management---system-patching"></a>Irányelvek a rendszerkezeléshez – rendszer-javítások

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 biztonsági rések a szélsőséges kockázatnak kitett alkalmazások és illesztőprogramok esetében a gyártók, a független harmadik felek, a rendszerkezelők vagy a felhasználók által azonosított biztonsági rések 48 órán belül javítottak, frissülnek vagy csökkenthetők.

- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- A sebezhetőségi felmérést engedélyezni kell a gépeken
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- Az SQL Server sebezhetőségi felmérési beállításainak tartalmaznia kell egy e-mail-címet a vizsgálati jelentések fogadásához

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 biztonsági rések a nagy kockázatnak kitett alkalmazások és illesztőprogramok esetében a gyártók, a független harmadik felek, a rendszerkezelők vagy a felhasználók által azonosított biztonsági sebezhetőségtől számított két héten belül megtörténik a javítás, a frissítés vagy a megoldás enyhítése.

- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- A sebezhetőségi felmérést engedélyezni kell Virtual Machines
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- Az SQL Server sebezhetőségi felmérési beállításainak tartalmaznia kell egy e-mail-címet a vizsgálati jelentések fogadásához

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 a közepes vagy alacsony kockázatnak kitett alkalmazások és illesztőprogramok biztonsági sebezhetőségei a szállítók, a független harmadik felek, a rendszerkezelők vagy a felhasználók által azonosított biztonsági rés egy hónapon belül kijavítottak, frissülnek vagy csökkenthetők.

- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- A sebezhetőségi felmérést engedélyezni kell Virtual Machines
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- Az SQL Server sebezhetőségi felmérési beállításainak tartalmaznia kell egy e-mail-címet a vizsgálati jelentések fogadásához

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 biztonsági rések a szélsőséges kockázatnak kitett operációs rendszerek és belső vezérlőprogram esetében a gyártók, a független harmadik felek, a rendszerkezelők vagy a felhasználók által azonosított biztonsági rések 48 órán belül javítottak, frissülnek vagy csökkenthetők.

- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- A sebezhetőségi felmérést engedélyezni kell Virtual Machines
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- Az SQL Server sebezhetőségi felmérési beállításainak tartalmaznia kell egy e-mail-címet a vizsgálati jelentések fogadásához

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 a magas kockázatnak kitett operációs rendszerek és belső vezérlőprogram biztonsági rései a gyártók, a független harmadik felek, a rendszerkezelők vagy a felhasználók által azonosított biztonsági sebezhetőség két héten belül vannak kitéve, frissítve vagy enyhítve

- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- A sebezhetőségi felmérést engedélyezni kell Virtual Machines
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- Az SQL Server sebezhetőségi felmérési beállításainak tartalmaznia kell egy e-mail-címet a vizsgálati jelentések fogadásához

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 a közepes vagy alacsony kockázatnak kitett operációs rendszerek és belső vezérlőprogram biztonsági rései a gyártók, a független harmadik felek, a rendszerkezelők vagy a felhasználók által azonosított biztonsági sebezhetőségtől számított egy hónapon belül kijavítottak, frissülnek vagy csökkenthetők.

- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- A sebezhetőségi felmérést engedélyezni kell Virtual Machines
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell
- Az SQL Server sebezhetőségi felmérési beállításainak tartalmaznia kell egy e-mail-címet a vizsgálati jelentések fogadásához

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>Irányelvek a rendszerkezeléshez – az adatbiztonsági mentés és a visszaállítás

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 fontos információk, szoftverek és konfigurációs beállítások biztonsági mentése legalább naponta történik

- Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>Irányelvek a rendszerfigyeléshez – eseménynaplózás és naplózás

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 A központosított naplózási létesítmény implementálva van, és a rendszerek úgy vannak konfigurálva, hogy az eseménynaplókat a lehető leghamarabb mentse a központosított naplózási létesítménybe az egyes események bekövetkezése után.

- Az Azure-előfizetéseknek log-profillal kell rendelkezniük a műveletnapló számára

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 a következő események vannak naplózva operációs rendszerekre: hozzáférés fontos információkhoz és folyamatokhoz, alkalmazás-összeomlások és hibaüzenetek, speciális jogosultságok használata, a fiókok módosításai, a biztonsági házirend módosításai, a rendszerkonfigurációk, a tartománynévrendszer (DNS) és a Hypertext Transfer Protocol (HTTP) kérelmek módosításai, sikertelen hozzáférés az adatforgalomhoz és a rendszererőforrásokhoz, a szolgáltatási hibákhoz és újraindítás , az adatok átvitele külső adathordozóra, felhasználóra vagy csoportra, speciális jogosultságok használata

- \[Előzetes verzió \] : log Analytics ügynök üzembe helyezésének naplózása – nincs listázva a virtuálisgép-rendszerkép (operációs rendszer)
- Log Analytics ügynök üzembe helyezésének naplózása a VMSS-ben – nincs listázva a VM-rendszerkép (operációs rendszer)
- Log Analytics munkaterületének naplózása a virtuális gép számára – a jelentés eltérése
- Diagnosztikai beállítás naplózása

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 a következő események vannak naplózva az adatbázisokhoz: különösen fontos információkhoz férhet hozzá, az új felhasználók, különösen a Kiemelt felhasználók, a megjegyzéseket tartalmazó lekérdezések, a több beágyazott lekérdezést tartalmazó lekérdezések, a lekérdezési vagy adatbázis-riasztások vagy meghibásodások, a jogosultságok emelésének megkísérlése, a sikeres vagy sikertelen hozzáférés, az adatbázis-struktúra módosításai, a felhasználói szerepkörökre vagy az adatbázis engedélyeire vonatkozó módosítások, adatbázis-rendszergazdai műveletek , adatbázis-bejelentkezések és kijelentkezések, az adatmódosítások, végrehajtható parancsok használata

- A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon
- Diagnosztikai beállítás naplózása
- A speciális adatbiztonságot engedélyezni kell az SQL felügyelt példányain

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>Irányelvek a rendszerfigyeléshez – biztonsági rések kezelése

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 a sebezhetőségi felmérések és a penetrációs tesztek elvégzése megfelelő szakképzett személyzettel történik a rendszer üzembe helyezése előtt, a rendszer jelentős módosítása után, és legalább évente vagy a rendszertulajdonos által megadott módon.

- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- A sebezhetőségi felmérést engedélyezni kell Virtual Machines
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell
- A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell

## <a name="guidelines-for-database-systems-management---database-servers"></a>Az adatbázis-rendszerek kezelésével kapcsolatos irányelvek – adatbázis-kiszolgálók

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 az adatbázis-kiszolgálók merevlemez-meghajtói titkosítottak teljes lemezes titkosítás használatával

- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 az adatbázis-kiszolgálók és a webalkalmazások között kommunikált adatok titkosítottak

- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A nem biztonságos kommunikációs protokollokat használó Windows-webkiszolgálók naplózási eredményeinek megjelenítése
- A biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózásához szükséges előfeltételek központi telepítése

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>Irányelvek az adatbázis-rendszerkezeléshez – adatbázis-kezelő rendszer szoftver

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 az alapértelmezett adatbázis-rendszergazdai fiókok le vannak tiltva, át lettek nevezve vagy megváltoztak a hozzáférési kódok

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 az adatbázis-rendszergazdák egyedi és azonosítható fiókkal rendelkeznek

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 adatbázis-rendszergazdai fiókok nincsenek megosztva különböző adatbázisok között

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 adatbázis-rendszergazdai fiókok kizárólag felügyeleti feladatokhoz használhatók, és az általános célú, adatbázissal való interakcióhoz használt adatbázis-fiókok

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 az adatbázis-rendszergazdai hozzáférés az alapértelmezett rendszergazdai engedélyekkel és az összes engedélyekkel rendelkező fiókok helyett meghatározott szerepkörökre korlátozódik.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>Útmutató a titkosítás használatához – titkosítási alapok

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 a REST-adatokhoz használt titkosítási szoftverek teljes lemezes titkosítást, vagy részleges titkosítást alkalmaznak, ahol a hozzáférés-vezérlés csak a titkosított partíción való írást engedélyezi.

- A lemezes titkosítást a virtuális gépeken kell alkalmazni

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>Útmutató a titkosítás használatához – Transport Layer Security

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 csak a TLS legújabb verziója használatos

- A legújabb TLS-verziót kell használni az API-alkalmazásban
- A legújabb TLS-verziót kell használni a webalkalmazásban
- A legújabb TLS-verziót kell használni a függvényalkalmazás
- A biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózásához szükséges előfeltételek központi telepítése
- A nem biztonságos kommunikációs protokollokat használó Windows-webkiszolgálók naplózási eredményeinek megjelenítése

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>Irányelvek az adatátvitelhez és a tartalom szűréséhez – tartalom szűrése

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 víruskereső-vizsgálat több különböző keresőmotor használatával történik minden tartalomon.

- A Microsoft IaaSAntimalware bővítményt Windows-kiszolgálókon kell telepíteni
- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>Irányelvek az adatátvitelhez és a tartalom szűréséhez – webalkalmazás-fejlesztés

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 az összes webalkalmazási tartalom kizárólag HTTPS használatával érhető el

- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- A webalkalmazás csak HTTPS protokollon keresztül érhető el
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 webalkalmazások esetén a webalkalmazások és a felhasználók védelme érdekében a webalkalmazások

- A CORS nem teszi lehetővé minden erőforrás számára a webalkalmazások elérését

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>Útmutató a hálózatkezeléshez – hálózati tervezés és konfigurálás

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 hálózati hozzáférés-vezérlés valósítható meg a hálózatokon a nem engedélyezett hálózati eszközök kapcsolatának megakadályozása érdekében

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 hálózati hozzáférés-vezérlés van megvalósítva, hogy korlátozza a hálózati szegmensek és azok közötti adatforgalom korlátozását, hogy csak az üzleti célra szükségesek legyenek

- Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- Az adaptív hálózatokra vonatkozó kötési javaslatokat az internetre irányuló virtuális gépeken kell alkalmazni

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>Útmutató a hálózatkezeléshez – a online szolgáltatások szolgáltatás folytonossága

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 a szolgáltatásmegtagadási támadások megelőzését és enyhítését célzó stratégiákat a szolgáltatók tárgyalják, pontosabban: a szolgáltatásra vonatkozó szolgáltatásmegtagadási támadásokkal szembeni ellenálló képességgel rendelkeznek. a szolgáltatásmegtagadási támadások miatt várhatóan felmerülő költségek, az ügyfelek értesítési küszöbértékei, illetve a online szolgáltatások kikapcsolása a szolgáltatásmegtagadási támadások során, a szolgáltatásmegtagadási támadásokkal szemben elvégezhető, előzetesen jóváhagyott műveletek, amelyekkel a letiltási és a szolgáltatási támadásokkal kapcsolatos támadások megakadályozása érdekében a felsőbb rétegbeli szolgáltatók blokkolják a rosszindulatú forgalmat a lehető legnagyobb mértékben

- DDoS Protection a standardot engedélyezni kell


> [!NOTE]
> Az adott Azure Policy-definíciók rendelkezésre állása Azure Government és más nemzeti felhőkben is változhat. 

## <a name="next-steps"></a>Következő lépések

További cikkek a tervekről és a használatukról:

> [!div class="nextstepaction"]
> [ISM-védelemmel ellátott terv – áttekintés](./index.md) 
>  [ISM-védelemmel ellátott terv – lépések üzembe helyezése](./deploy.md)

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
