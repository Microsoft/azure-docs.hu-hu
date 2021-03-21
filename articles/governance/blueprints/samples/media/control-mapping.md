---
title: A Media Blueprint Sample-vezérlők
description: A Media Blueprint-minták leképezésének vezérlése. Minden vezérlő egy vagy több olyan Azure Policy-definícióra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: e1356acfd7b6b9693322000077b221781b58681f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98035036"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>A Media Blueprint minta vezérlőelem-hozzárendelésének vezérlése

A következő cikk részletesen ismerteti, hogyan történik az Azure-tervrajzok adathordozó-tervezetének leképezése az adathordozó-vezérlőkre. További információ a vezérlőkről: [adathordozó](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

A következő leképezések az **adathordozó** -vezérlőkre vonatkoznak. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki az **\[ előnézetet \] : a Media Controls** beépített házirend-kezdeményezésének naplózása.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem egy-az-egyhez vagy egy teljes egyezés egy vezérlőelem és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Hozzáférés-vezérlés

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1,1 – Győződjön meg arról, hogy nem létezik gyökérszintű hozzáférési kulcs

- \[Előzetes verzió \] : előfeltételek központi telepítése a megadott tanúsítványokat nem tartalmazó Windows-alapú virtuális gépek naplózásához a megbízható gyökérben

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1,2 – a jelszavakat, a PIN-ket és a tokeneket védeni kell

- \[Előzetes verzió \] : előfeltételek központi telepítése a Windows rendszerű virtuális gépek naplózására, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakternél

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1,8 – a megosztott fiókhoz való hozzáférés tiltott

- A RootManageSharedAccessKey kivételével minden engedélyezési szabályt el kell távolítani Service Bus névtérből

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1,9 – a rendszeren korlátozni kell a jogosult felhasználók hozzáférését.

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1,14 – a rendszeren ki kell kényszeríteni a hozzáférési jogokat.

- \[Előzetes verzió \] : előfeltételek központi telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a "felhasználói jogok kiosztása" alkalmazásban

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1,15 – a biztonsággal kapcsolatos információkhoz vagy funkciókhoz való jogosulatlan hozzáférés megakadályozása.

- \[Előzetes verzió \] : a Windows rendszerű virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a biztonsági beállítások – rendszerbeállítások menüpontban

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 – a feladatok elkülönítését a szerepkör megfelelő hozzárendelésével kell kikényszeríteni.

- [Előzetes verzió \] : Role-Based Access Control (RBAC) használata a Kubernetes-szolgáltatásokban

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1,40 – győződjön meg arról, hogy a rendszerek nem kapcsolódnak egyszerre a megbízható hálózatokhoz és a nem megbízható hálózatokhoz.

- \[Előzetes verzió \] : előfeltételek központi telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a "biztonsági beállítások – hálózati hozzáférés" szakaszban

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1,42 & AC-1,43 – a nem alkalmazottak számára elérhető távelérést csak a kifejezetten jóváhagyott információs rendszerekhez való hozzáférés engedélyezésére kell korlátozni.

- \[Előzetes verzió \] : a Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek lehetővé teszik a távoli kapcsolatokat a jelszavak nélküli fiókoktól

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1,50 – a biztonsággal kapcsolatos események naplózása az összes információs rendszer összetevőire vonatkozóan.

- A Logic Apps lévő diagnosztikai naplókat engedélyezni kell

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1,54 – győződjön meg arról, hogy a többtényezős hitelesítés (MFA) engedélyezve van a felhőalapú konzol összes felhasználója számára.

- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában
- A fiókok vagy erőforrások megszegésének megelőzése érdekében minden írási jogosultsággal rendelkező előfizetési fiók esetében engedélyezni kell a Multi-Factor Authentication (MFA) használatát.

## <a name="auditing--logging"></a>Naplózási & naplózása

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2,1 – a sikeres és sikertelen eseményeket naplózni kell.

- A keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat.

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2,16 – a hálózati eszközöknek/példányoknak a hálózati eszköz/példány (ELBs, webalkalmazási tűzfalak stb.) által kritikus biztonsági eseményként besorolt összes eseményt be kell jelentkezniük.

- \[Előzetes verzió \] : naplózási eredmények megjelenítése a Windows rendszerű virtuális gépek konfigurációjában a "biztonsági beállítások – fiókok"

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2,17 – a kiszolgálóknak/példányoknak minden olyan eseményt be kell jelentkezniük, amely kritikus biztonsági eseménynek minősül az adott kiszolgálón/példányon

- \[Előzetes verzió \] : naplózási eredmények megjelenítése a Windows rendszerű virtuális gépek konfigurációjában a "biztonsági beállítások – fiókok"

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2,19 – a tartományi eseményeknek be kell jelentkezniük a tartományi felügyeleti szoftver által kritikus vagy magas biztonsági eseményként besorolt eseményekbe

- \[Előzetes verzió \] : naplózási eredmények megjelenítése a Windows rendszerű virtuális gépek konfigurációjában a "biztonsági beállítások – fiókok"
- \[Előzetes verzió \] : előfeltételek központi telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a biztonsági beállítások – Microsoft hálózati ügyfél

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2,20 – a tartomány eseményeinek minden olyan eseményt be kell jelentkezniük, amely kritikus biztonsági eseménynek minősül a tartományi biztonsági ellenőrzésekben

- \[Előzetes verzió \] : naplózási eredmények megjelenítése a Windows rendszerű virtuális gépek konfigurációjában a "biztonsági beállítások – fiókok"

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2,21 – a tartomány eseményeinek be kell jelentkezniük a tartományi napló összes hozzáférésére vagy módosítására.

- \[Előzetes verzió \] : naplózási eredmények megjelenítése a Windows rendszerű virtuális gépek konfigurációjában a "biztonsági beállítások – helyreállítási konzol"

## <a name="cryptographic-controls"></a>Titkosítási vezérlők

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4,2 – az alkalmazásoknak és a rendszereknek a jelenlegi titkosítási megoldásokat kell használniuk az adatok védelméhez.

- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás
- Az átlátható adattitkosítást engedélyezni kell a REST-alapú adatok védelme és a megfelelőségi követelmények teljesítése érdekében

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4,5 – a digitális tanúsítványokat jóváhagyott hitelesítésszolgáltatótól kell aláírni.

- \[Előzetes \] verzió: a megadott számú napon belül lejáró tanúsítványokat tartalmazó Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4,6 – a digitális tanúsítványokat egyedileg kell hozzárendelni egy felhasználóhoz vagy eszközhöz.

- \[Előzetes \] verzió: a megadott számú napon belül lejáró tanúsítványokat tartalmazó Windows-alapú virtuális gépek naplózásához szükséges előfeltételek központi telepítése

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4,7 – a titkosítási anyagokat úgy kell tárolni, hogy lehetővé tegyék a rekordok visszafejtését a rekordok megtartásának időtartamára.

- A lemezes titkosítást a virtuális gépeken kell alkalmazni
- Az engedélyezett lemezes titkosítás nélküli virtuális gépeket a Azure Security Center a javaslatok szerint figyeli

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4,8 – a titkos kulcsot és a titkos kulcsokat biztonságosan kell tárolni.

- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás
- Az átlátható adattitkosítást engedélyezni kell a REST-alapú adatok védelme és a megfelelőségi követelmények teljesítése érdekében

## <a name="change--config-management"></a>& konfiguráció kezelésének módosítása

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5,2 – csak a jogosult felhasználók alkalmazhatnak jóváhagyott módosításokat a rendszeren.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálókon hiányzó biztonsági rendszerfrissítéseket a Azure Security Center a javaslatok szerint figyeli

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5,12 – naprakész, teljes, pontos és könnyen elérhető alapkonfigurációt tart fenn az információs rendszer számára.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálókon hiányzó biztonsági rendszerfrissítéseket a Azure Security Center a javaslatok szerint figyeli

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5,13 – automatizált eszközöket alkalmaz az információs rendszer alapkonfigurációjának fenntartásához.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálókon hiányzó biztonsági rendszerfrissítéseket a Azure Security Center a javaslatok szerint figyeli

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5,14 – a szükségtelen és/vagy nem biztonságos függvények, portok, protokollok és szolgáltatások azonosítása és letiltása.

- A hálózati adaptereknek le kell tiltaniuk az IP-továbbítást
- \[Előzetes verzió \] : a virtuális gépen lévő IP-továbbítást le kell tiltani

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5,19 – a biztonsági konfigurációs beállítások változásainak figyelése.

- Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5,22 – gondoskodjon arról, hogy csak a jogosult szoftverek és frissítések legyenek telepítve a vállalati rendszerekre.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálókon hiányzó biztonsági rendszerfrissítéseket a Azure Security Center a javaslatok szerint figyeli

## <a name="identity--authentication"></a>Identitás-& hitelesítés

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7,1 – a felhasználói fiókokat egyedileg kell hozzárendelni az egyéni felhasználókhoz a nem nyilvánosként besorolt információhoz való hozzáféréshez. A fiók-azonosítókat szabványosított logikai formátum használatával kell létrehozni.

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- A nem figyelt hozzáférés megakadályozása érdekében el kell távolítani a tulajdonosi engedélyekkel rendelkező külső fiókokat az előfizetésből.

## <a name="network-security"></a>Hálózati biztonság

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9,2 – a hálózati eszközök felügyeletéhez való hozzáférés csak a jogosult felhasználókra korlátozódik.

- \[Előzetes verzió \] : előfeltételek központi telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a "biztonsági beállítások – hálózati hozzáférés" szakaszban

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9,3 – az összes hálózati eszközt a legbiztonságosabb konfigurációk alapján kell konfigurálni.

- \[Előzetes verzió \] : előfeltételek központi telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a "biztonsági beállítások – hálózati hozzáférés" szakaszban

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9,5 – a rendszernek a tűzfalon keresztül történő összes hálózati kapcsolatát rendszeresen jóvá kell hagynia és naplózni kell.

- \[Előzetes verzió: a Windows rendszerű \] virtuális gépek konfigurációjának naplózási eredményeinek megjelenítése a Windows tűzfal tulajdonságainál

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9,7 – a megfelelő vezérlőknek a megbízható hálózat és a nem megbízható vagy nyilvános hálózat közötti határokat kell tartalmazniuk.

- \[Előzetes verzió \] : előfeltételek központi telepítése a Windows rendszerű virtuális gépek konfigurációinak naplózásához a Windows tűzfal tulajdonságainál

## <a name="security-planning"></a>A biztonság tervezése

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11,3 – a fenyegetéseket azonosítani kell, amely negatív hatással lehet a vállalati információk és tartalmak titkosságára, integritására vagy rendelkezésre állására, valamint az előfordulásuk valószínűségére.

- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell beállítani az SQL felügyelt példány speciális adatbiztonsági beállításainál

### <a name="security-continuity"></a>Biztonság folytonossága

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12,5 – a hosszú távú tárolásban lévő adatoknak elérhetőnek kell lenniük a megőrzési időszak alatt, és védeni kell a média romlását és a technológiai változásokat.

- Az SQL-kiszolgálókat az 90 napnál hosszabb naplózási megőrzési napokon kell konfigurálni.
- A naplózási megőrzési időtartammal konfigurált SQL serverek naplózása 90 napnál rövidebb.

## <a name="system-integrity"></a>Rendszerintegritás

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14,3 – csak a felhatalmazott munkatársak figyelhetők meg a hálózati és a felhasználói tevékenységek.

- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A sebezhetőségi felmérés eredményeinek figyelése, valamint az adatbázis-biztonsági rések szervizelésével kapcsolatos javaslatok.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14,4 – az internet felé irányuló rendszereknek behatolás-észleléssel kell rendelkezniük.

- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14,13 – a szabványos központilag felügyelt kártevő szoftvereket a vállalaton belül kell megvalósítani.

- A Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware-bővítmény telepítése

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>Az SI-14,14-anti-malware szoftvereknek legalább a számítógépeket és a médiafájlokat kell beolvasniuk.

- A Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware-bővítmény telepítése

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15,4 – gondoskodjon arról, hogy az alkalmazások havonta legyenek megvizsgálva.

- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A támadások elleni védelem érdekében ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokban.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15,5 – győződjön meg arról, hogy a biztonsági rések azonosíthatók, a fenyegetések párosítása és a kockázat kiértékelése.

- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A támadások elleni védelem érdekében ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokban.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15,6 – győződjön meg arról, hogy az azonosított biztonsági rések szervizelése az idősoron kölcsönösen egyeztetett időkereten belül megtörtént.

- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A támadások elleni védelem érdekében ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokban.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15,7 – a sebezhetőségi felügyeleti rendszerek elérését és használatát csak a jogosult személyek használhatják.

- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A támadások elleni védelem érdekében ellenőrizze az operációs rendszer biztonsági réseit a virtuálisgép-méretezési csoportokban.

> [!NOTE]
> Az adott Azure Policy-definíciók rendelkezésre állása Azure Government és más nemzeti felhőkben is változhat. 

## <a name="next-steps"></a>Következő lépések

Áttekintette a Media Blueprint-minta vezérlési leképezését. A következő cikkekben megismerheti az áttekintést és a minta üzembe helyezésének módját:

> [!div class="next step action"]
> [Media Blueprint – áttekintés](./control-mapping.md) 
>  [Media Blueprint – lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
