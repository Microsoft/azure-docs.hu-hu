---
title: Azure Backup Azure biztonsági alapterve
description: A Azure Backup biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 33408c65fdc09972807aaa8afdf123f1c39cfff5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700260"
---
# <a name="azure-security-baseline-for-azure-backup"></a>Azure Backup Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Azure Backupre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure Backup vonatkozó kapcsolódó útmutatás. A Azure Backup nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Azure Backup teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Backup biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure Backup által használt végpontokat (beleértve az Microsoft Azure Recovery Services ügynököt is) a Microsoft felügyeli. Ön felelős minden olyan további ellenőrzésért, amelyet telepíteni kíván a helyszíni rendszerekre.

- [A MARS-ügynök hálózatkezelési és hozzáférés-támogatásának ismertetése](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent#networking-and-access-support)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: Ha a Mars-ügynököt egy Azure-beli virtuális gépen használja, használja a AzureBackup szolgáltatás címkéjét a hálózati biztonsági csoportokon vagy Azure Firewall a kimenő hozzáférés engedélyezéséhez Azure Backuphoz.

- [Azure-beli virtuális gépeken futó SQL Server-adatbázisok biztonsági mentése](backup-sql-server-database-azure-vms.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: Ha a Mars-ügynököt egy Azure-beli virtuális gépen használja, társítsa a virtuális gépet egy hálózati biztonsági csoporttal, és a Leírás használatával határozza meg a szabály üzleti szükségletét

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: Ha a Mars-ügynököt egy hálózati biztonsági csoport vagy Azure Firewall által védett Azure-beli virtuális gépen használja, az Azure-Tevékenységnaplók segítségével figyelheti a NSG vagy a tűzfal konfigurációját. Előfordulhat, hogy olyan Azure Monitoron belül riasztásokat hoz létre, amelyek elindítják az erőforrások módosításait.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat egy log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. Az Azure-tevékenység naplójának adatai alapján meghatározhatja az Azure-erőforrások vezérlési síkja szintjén végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" lehetőséget.

Emellett betöltheti a naplókat a Azure Monitor segítségével a Azure Backup által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhat tárolási fiókokat a hosszú távú/archiválási tároláshoz. Azt is megteheti, hogy az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidensek és események kezelése (SIEM) számára is felhasználhatja a táblát.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](/azure/azure-monitor/platform/activity-log)

- [Diagnosztikai beállítások használata Recovery Services-tárolók számára](backup-azure-diagnostic-events.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat egy log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. Az Azure-tevékenység naplójának adatai alapján meghatározhatja az Azure-erőforrások vezérlési síkja szintjén végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" lehetőséget.

Emellett a Azure Backup diagnosztikai eseményeket küld, amelyeket az elemzés, a riasztás és a jelentéskészítés céljából gyűjthet és használhat fel. A Recovery Services-tároló diagnosztikai beállításait a Azure Portal használatával konfigurálhatja. Egy vagy több diagnosztikai eseményt is elküldhet egy Storage-fiókba, az Event hub-ba vagy egy Log Analytics-munkaterületre.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](/azure/azure-monitor/platform/activity-log)

- [Diagnosztikai beállítások használata Recovery Services-tárolók számára](backup-azure-diagnostic-events.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitor az Azure Recovery Services-tárolóhoz társított log Analytics-munkaterületek naplózási megőrzési időszakának beállítása a szervezet megfelelőségi előírásai szerint.

- [Napló-megőrzési paraméterek beállítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a Azure Backup beépített figyelési és riasztási képességeket biztosít egy Recovery Services-tárolóban. Ezek a képességek további felügyeleti infrastruktúra nélkül érhetők el. A figyelés és a jelentéskészítés méretezését a Azure Monitor használatával is növelheti.

Engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez, valamint számos más elemzéshez a Recovery Services-tárolók által összegyűjtött tevékenység-naplózási adatok alapján.

- [Azure Backup munkaterhelések figyelése](backup-azure-monitoring-built-in-monitor.md)

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](/azure/azure-monitor/platform/activity-log)

- [Azure-Tevékenységnaplók összegyűjtése és elemzése Log Analytics munkaterületen Azure Monitor](/azure/azure-monitor/platform/activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a Azure Backup beépített figyelési és riasztási képességeket biztosít egy Recovery Services-tárolóban. Ezek a képességek további felügyeleti infrastruktúra nélkül érhetők el. A figyelés és a jelentéskészítés méretezését a Azure Monitor használatával is növelheti.

A riasztások elsődlegesen olyan helyzetek, amikor a felhasználók értesítést kapnak, hogy el tudják végezni a megfelelő lépéseket. A biztonsági mentési riasztások szakasz a Azure Backup szolgáltatás által generált riasztásokat jeleníti meg. Ezeket a riasztásokat a szolgáltatás definiálja, és nem hozhat létre egyéni riasztásokat.

Egy Log Analytics munkaterületet is felvehet az Azure Sentinel szolgáltatásba, mivel ez egy biztonsági előkészítési automatikus válasz-(felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását. Emellett Azure Monitor használatával is létrehozhat egyéni napló-riasztásokat a Log Analytics munkaterületen.

- [Azure Backup munkaterhelések figyelése](backup-azure-monitoring-built-in-monitor.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](/azure/azure-monitor/platform/alerts-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezheti, hogy mely fiókok tagjai ennek a szerepkörnek. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure Active Directory (Azure ad) nem az alapértelmezett jelszavak fogalma. Más Azure-erőforrások, amelyek jelszó megadását igénylik a bonyolultsági követelményekkel és a jelszó minimális hosszával hozhatók létre, és a szolgáltatástól függően eltérőek. Ön felelős harmadik féltől származó alkalmazásokért és piactér-szolgáltatásért, amelyek az alapértelmezett jelszavakat használhatják.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: az Azure-alkalmazás regisztrálása (egyszerű szolgáltatásnév) használatával lekérheti a Recovery Services-TÁROLÓk API-hívásokkal való interakcióhoz használható jogkivonatot.

- [Az Azure REST API-k meghívása](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Az ügyfélalkalmazás (egyszerű szolgáltatásnév) regisztrálása Azure Active Directory (Azure AD-vel)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Recovery Services API-információk](/rest/api/recoveryservices/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: ha a Azure Backupban kritikus műveleteket hajt végre, meg kell adnia egy biztonsági PIN-kódot, amely elérhető a Azure Portal. A Azure Active Directory (Azure AD) többtényezős hitelesítésének engedélyezése biztonsági réteget ad. Csak az érvényes Azure-beli hitelesítő adatokkal rendelkező és egy második eszközről hitelesített jogosult felhasználók férhetnek hozzá a Azure Portalhoz.

- [Azure-Multi-Factor Authentication a Azure Backup](backup-azure-security-feature.md)

- [Felhőalapú Azure AD Multi-Factor Authentication-telepítés megtervezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure által felügyelt munkaállomások használata az azure ad multi-Factor Authentication (MFA) használatára, amely a Azure Backup-kompatibilis erőforrások bejelentkezésére és konfigurálására van konfigurálva.

- [Emelt hozzáférési szintű munkaállomások](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Felhőalapú Azure AD Multi-Factor Authentication-telepítés megtervezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

Emellett az Azure AD kockázati észleléseit is használhatja a kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintéséhez.

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést a Azure Portalhoz az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként a Azure Backup példányokhoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [Azure Backup konfigurálása az Azure AD-bejelentkezés használatára](../app-service/configure-authentication-provider-aad.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure AD hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférését rendszeresen felül kell vizsgálni, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure AD hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként a Azure Backup példányokhoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Hozzáférése van az Azure AD bejelentkezési tevékenységeihez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik az Azure Sentinel vagy egy harmadik féltől származó SIEM integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics belül is konfigurálhatja a kívánt naplózási riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory (Azure ad) használata a Recovery Services-tárolók központi hitelesítési és engedélyezési rendszereként. A vezérlési síkon (a Azure Portal) való bejelentkezéshez használja a Azure AD Identity Protection és a kockázati észlelési funkciókat a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszok konfigurálásához. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezésének megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: jelenleg nem érhető el; Azure Backup esetében Ügyfélszéf még nem támogatott.

- [Ügyfélszéf által támogatott szolgáltatások listája](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az Azure infrastruktúra-szolgáltatás (IaaS) Virtual Machinesának biztonsági mentésekor a Azure Backup független és elkülönített biztonsági mentéseket biztosít az eredeti adatvesztés elleni védelemhez. A biztonsági másolatok egy helyreállítási tárban vannak tárolva, a helyreállítási pontok beépített kezelésével.

Külön előfizetéseket és választható felügyeleti csoportokat implementálhat fejlesztési, tesztelési és éles Recovery Services-tárolók számára. Az erőforrásokat virtuális hálózatoknak vagy alhálózatoknak kell elkülöníteni, megfelelően címkézve, és egy hálózati biztonsági csoport vagy Azure Firewall által védettnek kell lennie. A bizalmas adatok tárolására vagy feldolgozására szolgáló erőforrásoknak elég elszigeteltnek kell lenniük. A bizalmas adatok tárolására és feldolgozására Virtual Machines a házirend és eljárás (ok) bekapcsolásával kikapcsolhatja azokat, ha nincsenek használatban.

- [Azure Backup áttekintése](backup-overview.md)

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: jelenleg nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Backup számára.

A Microsoft kezeli a Azure Backup alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: jelenleg nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Backup számára.

A Microsoft kezeli a Azure Backup alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az azure szerepköralapú Access Control (Azure RBAC) lehetővé teszi a részletes hozzáférés-vezérlést az Azure-hoz. Az Azure RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szükségük van.

A Azure Backup három beépített szerepkört biztosít a biztonságimásolat-kezelési műveletek vezérléséhez: a biztonsági másolatok közreműködői, a biztonságimásolat-felelős és a biztonsági mentési olvasó. A biztonsági másolatok beépített szerepkörei a különböző biztonsági mentési felügyeleti műveletekhez képezhetők le.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [Az Azure szerepköralapú Access Control használata Azure Backup helyreállítási pontok kezeléséhez](backup-rbac-rs-vault.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Azure Backup támogatja a REST-adatok titkosítását. Helyszíni biztonsági mentéshez az inaktív állapotú adatokat védő titkosítás használható az Azure-ra irányuló biztonsági mentés során megadott jelszóval. A Felhőbeli munkaterhelések esetében az adatok a Storage Service Encryption (SSE) használatával titkosítva vannak. A Microsoft soha nem fejt vissza adatokat.

Ha a MARS-ügynökkel készít biztonsági mentést, vagy egy ügyfél által felügyelt kulccsal titkosított Recovery Services-tárolót használ, csak a titkosítási kulcshoz férhet hozzá. A Microsoft soha nem tart fenn másolatot, és nem fér hozzá a kulcshoz. Ha a kulcs rossz helyre került, a Microsoft nem tudja helyreállítani a biztonsági mentési adatokat.

- [A inaktív adatok titkosításának megismerése Azure Backup](backup-encryption.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel az Azure Recovery Services-tárolók, valamint más kritikus vagy kapcsolódó erőforrások változásai zajlanak.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: még nem érhető el; a sebezhetőségi felmérés a Azure Security Centerban még nem érhető el Azure Backuphoz.

A Microsoft által beolvasott és javított mögöttes platform. Tekintse át az Azure Backup számára elérhető biztonsági vezérlőket a szolgáltatás konfigurálásával kapcsolatos biztonsági rések csökkentése érdekében.

- [A Azure Backup számára elérhető biztonsági vezérlők ismertetése](/azure/backup/backup-security-controls)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: jelenleg nem érhető el; a Azure Backup biztonsági beállításai még nem támogatottak a Azure Security Centerban.

- [A Azure Security Center támogatott Péter-szolgáltatások listája](../security-center/features-paas.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok) az előfizetéseken belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: az Azure-erőforrások rendszerezéséhez és nyomon követéséhez használja a címkézést, a felügyeleti csoportokat és a különálló előfizetéseket, ahol szükséges. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: jóváhagyott Azure-erőforrások és jóváhagyott szoftverek definiálása számítási erőforrásokhoz.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrásokra vonatkozóan.

Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra 

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Recovery Services-tár szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Recoveryservices szolgáltatónál" névtérben egyéni szabályzatok létrehozásához a Recovery Services-tárolók konfigurációjának naplózásához vagy érvényesítéséhez.

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Policy [megtagadás] és a [telepítés ha nem létezik] hatással van az Azure-erőforrások biztonságos beállításainak betartatására.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a "Microsoft. recoveryservices szolgáltatónál" névtérben található beépített Azure Policy definíciók és Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a **Microsoft. recoveryservices szolgáltatónál** névtérben található beépített Azure Policy-definíciók és Azure Policy-Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Az Azure-erőforrások konfigurációinak automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés, ha nem léteznek] effektusokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Mars-ügynök beállításakor tárolja a titkosítási jelszót Azure Key Vaulton belül.

- [Hitelesítés Key Vault](../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: az Azure-hoz készült Microsoft antimalware engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Backup), de nem fut a tartalomon.

A nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, például App Service, Data Lake Storage és Blob Storage.

Az adatszolgáltatások Azure Security Center veszélyforrások elleni védelmének használatával észlelheti a Storage-fiókokba feltöltött kártevőket.

- [Megismerheti a Microsoft antimalware-t az Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

- [Az adatszolgáltatások Azure Security Center veszélyforrások elleni védelmének megismerése](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a helyileg redundáns tárolás (LRS) háromszor replikálja az adatait (az adatai három másolatát hozza létre) egy adatközpontban lévő tárolási méretezési egységben. Az adatok összes másolata ugyanabban a régióban található. Az LRS egy alacsony költségű megoldás az adatok védelmére a helyi hardveres hibák esetén. Az alapértelmezett és ajánlott replikációs lehetőség a georedundáns tárolás (GRS). A GRS egy másodlagos régióba replikálja az adatait, és több száz kilométer távolságra van a forrásadatok elsődleges helyétől. A GRS több mint LRS, de a GRS magasabb szintű tartósságot biztosít az adataihoz, még akkor is, ha van regionális leállás.

Az ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül.

- [Azure Backup áttekintése](backup-overview.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [A titkosítás megismerése Azure Backup](backup-encryption.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. recoveryservices szolgáltatónál**:

[!INCLUDE [Resource Policy for Microsoft.RecoveryServices 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.recoveryservices-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az ügyfél által felügyelt kulcsok biztonsági másolatának visszaállítása.

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a helyszíni biztonsági mentéshez a titkosítást az Azure-ba történő biztonsági mentéshez megadott hozzáférési kód használatával biztosítjuk. Azure-beli virtuális gépek esetén a rendszer Storage Service Encryption (SSE) használatával titkosítja az inaktív adatokat. A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához engedélyezheti a Key Vault törlését.

- [A Soft delete engedélyezése Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [A NIST számítógép-biztonsági incidensek kezelési útmutatója](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket, és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhő-infrastruktúrával,-szolgáltatásokkal és-alkalmazásokkal kapcsolatos további információkat a Microsoft által kezelt felhőalapú infrastruktúra, szolgáltatások és alkalmazások című témakörben találhatja meg.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
