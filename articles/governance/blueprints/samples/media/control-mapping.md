---
title: Média tervminta vezérlői
description: A Média tervminták leképezésének szabályozása. Minden vezérlő egy vagy több olyan definícióra van Azure Policy, amelyek az értékelést segítik.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: 4c04c661d7dd8b2b07a05c20524258d6bb887e62
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376698"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>A Média tervminta vezérlőelem-leképezése

A következő cikk részletesen bemutatja, Azure Blueprints Media-tervminta hogyan lesz leképezve a Média vezérlőkre. További információ a vezérlőkről: [Média.](https://www.motionpictures.org/best-practices)

Az alábbi leképezések a Média **vezérlőkre vannak leképezve.** A jobb oldalon a navigációval közvetlenül egy adott vezérlőelem-leképezéshez ugorhat. Számos leképezett vezérlő egy Azure Policy [van](../../../policy/overview.md) megvalósítva. A teljes kezdeményezés áttekintéshez nyissa meg a **szabályzatot** a Azure Portal, és válassza a **Definíciók** lapot. Ezután keresse meg és válassza ki az **\[ Előzetes \] verzió: Média naplózása** beépített szabályzati kezdeményezést.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [definícióhoz Azure Policy](../../../policy/overview.md) társítva. Ezek a szabályzatok segíthetnek [felmérni a](../../../policy/how-to/get-compliance-data.md) vezérlőnek való megfelelőséget; A vezérlők és egy vagy több szabályzat között azonban gyakran nincs egy-az-egyhez vagy egy teljes egyezés. Ezért a **szabályzatok Azure Policy** csak magukra a szabályzatra vonatkoznak; Ez nem biztosítja, hogy teljes mértékben megfelel egy vezérlő összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyekre jelenleg egyetlen Azure Policy sem vonatkoznak. Ezért a megfelelőségi Azure Policy csak részlegesen látható a teljes megfelelőségi állapotról. A megfelelőségi tervminta vezérlői és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub véglegesítési előzményeit.](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md)

## <a name="access-control"></a>Hozzáférés-vezérlés

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1 – Győződjön meg arról, hogy nem létezik gyökérelérési kulcs

- \[Előzetes verzió: Előfeltételek központi telepítése az olyan Windows rendszerű virtuális gépek naplózására, amelyek nem tartalmazzák a megadott tanúsítványokat \] a Megbízható gyökérkönyvtárban

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 – A jelszavakat, PIN-eket és jogkivonatokat védeni kell

- \[Előzetes verzió: Előfeltételek telepítése olyan Windows rendszerű virtuális gépek naplózásához, amelyek nem korlátozzák a jelszó minimális hosszát \] 14 karakterre

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 – A megosztott fiókhoz való hozzáférés tiltott

- A RootManageSharedAccessKey kivételével minden engedélyezési szabályt el kell távolítani Service Bus névtérből

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 – A rendszernek a jogosult felhasználókra kell korlátoznia a hozzáférést.

- Tárfiókok korlátlan hálózati hozzáférésének naplózása

### <a name="ac--114--system-must-enforce-access-rights"></a>AC– 1.14 – A rendszernek hozzáférési jogosultságokat kell kikényszerítenie.

- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációjának naplózásához szükséges előfeltételek üzembe helyezése \] a Felhasználói jogok kiosztása funkcióban

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC- 1.15 – A biztonsággal kapcsolatos információkhoz vagy funkciókhoz való jogosulatlan hozzáférés megakadályozása.

- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációiból származó naplózási eredmények \] megjelenítése a Biztonsági beállítások – Rendszerbeállítások között

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 – A feladatok elkülönítését a szerepkör megfelelő hozzárendelése révén kell kikényszeríteni.

- [Előzetes \] verzió: Role-Based Access Control (RBAC) a Kubernetes Servicesben kell használni

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40 – Győződjön meg arról, hogy a rendszerek nem csatlakoznak egyszerre megbízható és nem megbízható hálózatokhoz.

- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációjának naplózásához szükséges előfeltételek üzembe helyezése \] a "Biztonsági beállítások – Hálózati hozzáférés" alatt

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 & AC- 1.43 – A nem alkalmazottak távelérését úgy kell korlátozni, hogy csak kifejezetten jóváhagyott információs rendszerekhez engedélyezze a hozzáférést

- \[Előzetes verzió: Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek lehetővé teszik a jelszavak nélküli \] fiókok távoli kapcsolatait

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50 – Biztonsági események naplózása minden információsrendszer-összetevőhöz.

- A diagnosztikai naplókat Logic Apps engedélyezni kell

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54 – Győződjön meg arról, hogy a többtényezős hitelesítés (MFA) engedélyezve van a felhőkonzol összes felhasználója számára.

- Az MFA-t engedélyezni kell az előfizetés minden írási engedéllyel rendelkező fiókjában
- A többtényezős hitelesítést (MFA) engedélyezni kell az összes írási jogosultsággal rendelkező előfizetési fiókhoz, hogy megelőzhető legyen a fiókok vagy erőforrások megsértése.

## <a name="auditing--logging"></a>Naplózási & naplózása

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1 – A sikeres és sikertelen eseményeket naplózza.

- Engedélyezni kell a diagnosztikai naplókat a Keresési szolgáltatásokban

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL -2.16 – A hálózati eszközöknek/példányoknak naplózva kell lennie az adott hálózati eszköz/példány által kritikus fontosságúként besorolt eseményeket (PLB-k, webalkalmazási tűzfalak stb.)

- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációiból származó naplózási eredmények \] megjelenítése a "Biztonsági beállítások – Fiókok" menüben

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17 – A kiszolgálóknak/példányoknak naplózva kell lennie az adott kiszolgáló/példány által kritikus fontosságúként besorolt eseményeket

- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációiból származó naplózási eredmények \] megjelenítése a "Biztonsági beállítások – Fiókok" menüben

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 – A tartományi eseményeknek naplózva kell lennie a tartományi felügyeleti szoftver által kritikus vagy magas biztonsági eseményként besorolt eseményeknek

- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációiból származó naplózási eredmények \] megjelenítése a "Biztonsági beállítások – Fiókok" alatt
- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációjának naplózásához szükséges előfeltételek üzembe helyezése \] a "Biztonsági beállítások – Microsoft hálózati ügyfél" alatt

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20 – A tartományi eseményeknek a tartományi biztonsági vezérlők által kritikus fontosságúként besorolt eseményeket naplózva kell lennie

- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációiból származó naplózási eredmények \] megjelenítése a "Biztonsági beállítások – Fiókok" menüben

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21 – A tartományi eseményeknek naplózva kell lennie a tartományi napló hozzáférésének vagy módosításainak

- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációiból származó naplózási eredmények \] megjelenítése a Biztonsági beállítások – Helyreállítási konzolban

## <a name="cryptographic-controls"></a>Titkosítási funkciók

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2 – Az alkalmazásoknak és rendszereknek a jelenlegi titkosítási megoldásokat kell használniuk az adatok védelméhez.

- transzparens adattitkosítás SQL-adatbázisokon engedélyezni kell az adatbázis-adatokat
- Engedélyezni kell a transzparens adattitkosítást az aktív adatok védelme és a megfelelőségi követelmények teljesítéséhez

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5 – A digitális tanúsítványokat jóváhagyott hitelesítésszolgáltatónak kell aláírni.

- \[Előzetes verzió: A megadott számú napon belül lejáró tanúsítványokat tartalmazó Windows rendszerű virtuális gépek \] naplózási eredményeinek megjelenítése

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6 – A digitális tanúsítványokat egyedileg kell hozzárendelni egy felhasználóhoz vagy eszközhöz.

- \[Előzetes verzió: A megadott számú napon belül lejáró tanúsítványokat tartalmazó Windows rendszerű virtuális gépek naplózására vonatkozó \] előfeltételek telepítése

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7 – A titkosítási anyagokat tárolni kell a rekordok visszafejtéséhez a rekordok megőrzésének időtartamához.

- Lemeztitkosítást kell alkalmazni a virtuális gépeken
- Az engedélyezett lemeztitkosítással nem rendelkező virtuális gépeket a Azure Security Center figyelik javaslatként

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8 – A titkos és titkos kulcsokat biztonságosan kell tárolni.

- transzparens adattitkosítás SQL-adatbázisokon engedélyezni kell az adatbázis-adatokat
- A transzparens adattitkosítást engedélyezni kell az aktív adatok védelme és a megfelelőségi követelmények betartása érdekében

## <a name="change--config-management"></a>Konfigurációkezelés & módosítása

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2 – Csak a jogosult felhasználók hajthatnak végre jóváhagyott módosításokat a rendszeren.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálók hiányzó biztonságirendszer-frissítéseit a Azure Security Center fogja figyelni

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12 – Naprakész, teljes, pontos és könnyen elérhető alapkonfiguráció fenntartása az információs rendszer számára.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálók hiányzó biztonságirendszer-frissítéseit a Azure Security Center fogja figyelni

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13 – Automatizált eszközök használata az információs rendszer alapkonfigurációjának fenntartásához.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálók hiányzó biztonságirendszer-frissítéseit a Azure Security Center fogja figyelni

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14 – A szükségtelen és/vagy nem biztonságos függvények, portok, protokollok és szolgáltatások azonosítása és letiltása.

- A hálózati adapterek letiltják az IP-továbbítást
- \[Előzetes verzió: A virtuális gépen az \] IP-továbbítást le kell tiltani

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19 – A biztonsági konfigurációs beállítások módosításainak figyelése.

- Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22 – Győződjön meg arról, hogy csak a jogosult szoftverek és frissítések vannak telepítve a vállalati rendszereken.

- A rendszerfrissítéseket telepíteni kell a gépeken
- A kiszolgálókon hiányzó biztonságirendszer-frissítéseket a Azure Security Center figyelheti javaslatként

## <a name="identity--authentication"></a>Identitás & hitelesítés

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1 – A felhasználói fiókokat egyedileg kell hozzárendelni egyénekhez, hogy hozzáférjenek a nem nyilvánosként besorolt információkhoz. A fiókokat szabványosított logikai formátumban kell összeépíteni.

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből, hogy megelőzhető legyen a nemmonitorált hozzáférés.

## <a name="network-security"></a>Hálózati biztonság

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2 – A hálózati eszközkezelési funkciókhoz való hozzáférés a jogosult felhasználókra korlátozódik.

- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációjának naplózásához szükséges előfeltételek üzembe helyezése \] a "Biztonsági beállítások – Hálózati hozzáférés" alatt

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3 – Minden hálózati eszközt a legbiztonságosabb konfigurációval kell konfigurálni.

- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációjának naplózásához szükséges előfeltételek üzembe helyezése \] a "Biztonsági beállítások – Hálózati hozzáférés" alatt

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5 – A tűzfalon keresztül a rendszerhez való összes hálózati kapcsolatot rendszeresen jóvá kell hagyni és naplóba kell hagyni.

- \[Előzetes verzió: A Windows tűzfal tulajdonságai között windowsos virtuális gépek konfigurációiból \] származó naplózási eredmények megjelenítése

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7 – Megfelelő vezérlőknek kell jelen lennie a megbízható hálózatok és a nem megbízható vagy nyilvános hálózatok közötti határvonalakon.

- \[Előzetes verzió: A Windows rendszerű virtuális gépek konfigurációjának naplózásával kapcsolatos előfeltételek üzembe helyezése \] a Windows tűzfal tulajdonságaiban

## <a name="security-planning"></a>A biztonság tervezése

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3 – Olyan fenyegetéseket kell azonosítani, amelyek negatív hatással lehetnek a céges információk és tartalmak bizalmasságára, integritására vagy rendelkezésre állására, valamint előfordulásuk valószínűségére.

- Az Advanced Threat Protection-típusokat "Mind" állapotúra kell állítani a felügyelt SQL-példányok Advanced Data Security beállításaiban

### <a name="security-continuity"></a>Biztonsági folytonosság

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5 – A hosszú távú tárolás során az adatoknak a megőrzési időtartam alatt elérhetőnek kell lenniük, és védeni kell őket a média romlása és a technológia változásai ellen.

- Az SQL-kiszolgálókat 90 napnál több napnál több naplózási megőrzési idővel kell konfigurálni.
- Naplózási 90 napnál rövidebb megőrzési időtartammal konfigurált SQL-kiszolgálók naplózása.

## <a name="system-integrity"></a>Rendszerintegritás

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3 – Csak a jogosult személyek figyelhetnek hálózati és felhasználói tevékenységeket.

- Az SQL-adatbázisok biztonsági réseit helyre kell ásni
- Figyelje a sebezhetőségi felmérés vizsgálatának eredményeit és az adatbázis biztonsági réseit javító javaslatokat.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4 – Az internetre néző rendszereknek behatolásészlelést kell észlelnie.

- Fenyegetésészlelés üzembe helyezése SQL-kiszolgálókon

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13 – A szabványosított központilag felügyelt kártevőirtó szoftvert a teljes vállalatnál meg kell valósítanunk.

- Alapértelmezett Microsoft IaaSAntimalware bővítmény telepítése Windows Serverhez

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14 – A kártevőirtó szoftvernek hetente legalább a számítógépeken és az adathordozón kell átvizsgálni.

- Alapértelmezett Microsoft IaaSAntimalware bővítmény telepítése Windows Serverhez

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4 – Győződjön meg arról, hogy az alkalmazásokban havonta ellenőrzik a biztonsági réseket.

- A virtuálisgép-méretezési készletek biztonsági konfigurációjának biztonsági réseit helyre kell ásni
- A virtuálisgép-méretezési készletek operációsrendszer-biztonsági réseit naplózva megvédheti őket a támadásokkal szemben.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5 – Győződjön meg arról, hogy a biztonsági rések azonosítva vannak, veszélyforrásokhoz vannak párosítva, és ki vannak értékelve a kockázatok.

- A virtuálisgép-méretezési készletek biztonsági konfigurációjának biztonsági réseit helyre kell ásni
- A virtuálisgép-méretezési készletek operációsrendszer-biztonsági réseit naplózva megvédheti őket a támadásokkal szemben.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6 – Győződjön meg arról, hogy az azonosított biztonsági réseket egy kölcsönösen elfogadott idővonalon belül orvosolták.

- A virtuálisgép-méretezési készletek biztonsági konfigurációjának biztonsági réseit helyre kell ásni
- A virtuálisgép-méretezési készletek operációsrendszer-biztonsági réseit naplózva megvédheti őket a támadásokkal szemben.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7 – A biztonsági réseket kezelő rendszerekhez való hozzáférést és annak használatát a jogosult személyzetre kell korlátozni.

- A virtuálisgép-méretezési készletek biztonsági konfigurációjának biztonsági réseit helyre kell ásni
- A virtuálisgép-méretezési készletek operációsrendszer-biztonsági réseit naplózva megvédheti őket a támadásokkal szemben.

> [!NOTE]
> Az egyes Azure Policy rendelkezésre állása eltérő lehet a Azure Government és más országos felhőkben. 

## <a name="next-steps"></a>Következő lépések

Áttekintte a Média tervminta vezérlőelem-leképezését. Ezután a következő cikkekben megismerheti a minta áttekintését és üzembe helyezését:

> [!div class="next step action"]
> [Média terv – Áttekintés](./control-mapping.md) 
>  [Média terv – Üzembe helyezési lépések](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
