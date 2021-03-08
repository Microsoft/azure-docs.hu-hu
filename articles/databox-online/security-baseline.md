---
title: Az Azure biztonsági alapterve Azure Stack Edge-hez
description: Az Azure Stack Edge biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 460fd66ed4651248639334caa55eb8facbce866d
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452373"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Az Azure biztonsági alapterve Azure Stack Edge-hez

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) Microsoft Azure stack Edge-re vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** és az Azure stack Edge-re vonatkozó kapcsolódó útmutatás szerint van csoportosítva. Az Azure Stack Edge-re nem alkalmazható **vezérlők** ki lettek zárva.

Ha szeretné megtekinteni, hogy Azure Stack Edge teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure stack Edge biztonsági alapkonfiguráció-hozzárendelési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: a belső forgalom biztonságának megvalósítása

**Útmutató**: a felhasználók a Microsoft által biztosított, fizikai, Azure stack peremhálózati eszközt üzembe helyezhetik a belső hozzáférésre szolgáló saját magánhálózaton, és további lehetőségeket biztosítanak. Az Azure Stack Edge-eszköz például elérhető az ügyfél belső hálózatán, és egy ügyfél által konfigurált IP-címet igényel. Emellett az ügyfél hozzáférési jelszót is választ az eszköz felhasználói felületének eléréséhez. 

A belső forgalom védelmét az alábbiak biztosítják:

- Az Azure Stack Edge-eszköz Azure Portal és SDK-kezeléséhez Transport Layer Security (TLS) 1,2-es verzió szükséges.

- Az eszközhöz való bármely ügyfél-hozzáférés a helyi webes felületen keresztül történik a szabványos TLS 1,2 alapértelmezett biztonságos protokoll használatával.

- Csak az engedélyezett Azure Stack Edge Pro-eszköz csatlakozhat az Azure-előfizetésében az ügyfél által létrehozott Azure Stack Edge szolgáltatáshoz.

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.
 
- [A TLS 1,2 konfigurálása Azure Stack Edge Pro GPU-eszközt elérő Windows-ügyfeleken](azure-stack-edge-j-series-configure-tls-settings.md)

- [Gyors útmutató – Ismerkedés a Azure Stack Edge Pro és a GPU szolgáltatással](azure-stack-edge-gpu-quickstart.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="ns-2-connect-private-networks-together"></a>NS-2: magánhálózati hálózatok összekapcsolása

**Útmutató**: az ügyfelek választhatnak pont – hely virtuális MAGÁNHÁLÓZATI (VPN) kapcsolatot egy Azure stack Edge-eszköznek a helyi magánhálózaton keresztül az Azure-hálózatra való csatlakoztatásához. A VPN egy második titkosítási réteget biztosít az ügyfél eszközéről az Azure-ba irányuló adatátviteli rétegbeli biztonsághoz. 

Az ügyfelek a Azure Stack peremhálózati eszközön a Azure Portal vagy a Azure PowerShellon keresztül konfigurálhatják a virtuális magánhálózat szolgáltatásait.

- [Azure VPN konfigurálása Azure PowerShell szkripttel Azure Stack Edge Pro R és Azure Stack Edge mini R-hez](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [A TLS 1,2 konfigurálása Azure Stack Edge Pro GPU-eszközt elérő Windows-ügyfeleken](azure-stack-edge-j-series-configure-tls-settings.md)

- [Oktatóanyag: tanúsítványok konfigurálása a Azure Stack Edge Pro R-hez](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Privát hálózati hozzáférés létesítése Azure-szolgáltatásokhoz

**Útmutató**: az ügyfelek választhatnak pont – hely virtuális MAGÁNHÁLÓZATI (VPN) kapcsolatot egy Azure stack Edge-eszköznek a helyi magánhálózaton keresztül az Azure-hálózatra való csatlakoztatásához. A VPN egy második titkosítási réteget biztosít az ügyfél eszközéről az Azure-ba irányuló adatátviteli rétegbeli biztonsághoz. 

- [Azure VPN konfigurálása Azure PowerShell szkripttel Azure Stack Edge Pro R és Azure Stack Edge mini R-hez](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [A TLS 1,2 konfigurálása Azure Stack Edge Pro GPU-eszközt elérő Windows-ügyfeleken](azure-stack-edge-j-series-configure-tls-settings.md)

- [Oktatóanyag: tanúsítványok konfigurálása a Azure Stack Edge Pro R-hez](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: a külső hálózati támadásoktól származó alkalmazások és szolgáltatások elleni védelem

**Útmutató**: az Azure stack Edge-eszköz tartalmazza a szabványos Windows Server hálózati védelmi funkciókat, amelyek nem konfigurálhatók az ügyfelek által.

Az ügyfelek dönthetnek úgy, hogy a hálózati virtuális berendezéssel, például a fejlett, elosztott szolgáltatásmegtagadási (DDoS) védelemmel ellátott tűzfallal csatlakoznak a Azure Stack peremhálózati eszközön a külső támadásoktól.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure stack Edge-eszköz által használt végpontokat a Microsoft felügyeli. Az ügyfelek felelősek a helyszíni rendszerekre telepíteni kívánt további vezérlőkért.

Az Azure Stack Edge-eszköz a saját behatolás-észlelési funkcióit használja a szolgáltatás megóvására. 

- [Azure Stack Edge-biztonság ismertetése](azure-stack-edge-security.md)

- [Azure Stack Edge portjának adatai](azure-stack-edge-gpu-system-requirements.md)

- [Hardveres és szoftveres behatolás-észlelési naplók beolvasása](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="identity-management"></a>Identitáskezelés

*További információ: [Azure Security Benchmark: Identitáskezelés](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Alkalmazásidentitások biztonságos és automatikus kezelése

**Útmutató**: az összes Azure stack peremhálózati eszköznek automatikusan van egy rendszerhez rendelt felügyelt identitása a Azure Active Directoryban (Azure ad). A felügyelt identitás jelenleg a Azure Stack Edge-ben üzemeltetett virtuális gépek Felhőbeli kezelésére szolgál.

Azure Stack peremhálózati eszközök a helyi hozzáféréshez zárolt állapotba kerülnek. A helyi eszköz rendszergazdai fiókjához a helyi webes felhasználói felületen vagy a PowerShell-felületen keresztül kell csatlakoznia az eszközhöz, és erős jelszót kell beállítania. Biztonságos helyen tárolhatja és kezelheti az eszköz rendszergazdai hitelesítő adatait, például egy Azure Key Vault, és a szervezeti szabványok alapján elforgathatja a rendszergazdai jelszót.

- [Azure Stack peremhálózati eszköz jelszavas védelemmel való ellátása](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Az Azure AD-beli egyszeri bejelentkezés használata alkalmazások eléréséhez

**Útmutató**: az egyszeri bejelentkezés Azure stack peremhálózati végponti eszközökön nem támogatott. A standard Azure Active Directory (Azure AD) alapú egyszeri bejelentkezés engedélyezésével azonban biztonságossá teheti az Azure felhőalapú erőforrásaihoz való hozzáférést.

- [Alkalmazás egyszeri bejelentkezésének megismerése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Erős hitelesítési vezérlők használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a többtényezős hitelesítés egy erős hitelesítési vezérlő, de az Azure stack Edge szolgáltatás felhasználói számára elérhető funkció. A szolgáltatás használható olyan támogatott forgatókönyvek esetén, mint például az Azure Stack Edge-eszközök kezelése a Azure Portal. Vegye figyelembe, hogy az Azure Stack Edge-eszközök helyi hozzáférése nem támogatja a többtényezős hitelesítést.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Fiókok rendellenességeinek monitorozása, és riasztás azok alapján

**Útmutató**: engedélyezze a Azure monitor számára az eszköz-vagy tároló-naplók összegyűjtését az Azure stack Edge szolgáltatáshoz. Megfigyelheti a tárolókat, például több számítási alkalmazást futtató Kubernetes-fürtön az eszközön.

Emellett egy támogatási csomag, amely tartalmazza a naplókat is, az Azure Stack Edge-eszköz helyi webes felhasználói felületén gyűjthetők. Vegye figyelembe, hogy a támogatási csomag nem érhető el a Azure Portal.
 
- [Azure Monitor engedélyezése a Azure Stack Edge Pro-eszközön](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [Támogatási csomag összegyűjtése](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure-erőforrásokhoz való hozzáférés korlátozása feltételek alapján

**Útmutató**: Azure Active Directory (Azure ad) feltételes hozzáférés az Azure stack Edge-szolgáltatáshoz való hitelesítéshez használható funkció. Az Azure Stack Edge Service egy olyan erőforrás a Azure Portalban, amely lehetővé teszi egy Azure Stack Edge Pro-eszköz felügyeletét különböző földrajzi helyeken keresztül. 

- [Mi a feltételes hozzáférés?](../active-directory/conditional-access/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Hitelesítő adatok nem szándékos elérhetővé tételének kizárása

**Útmutató**: kövesse az ajánlott eljárásokat a hitelesítő adatok védelméhez, például:

- Az eszköz az Azure-ban Azure Stack Edge-erőforrással való aktiválásához használt aktiválási kulcs.
- Jelentkezzen be a hitelesítő adatokkal az Azure Stack Edge-eszköz eléréséhez.
- A Azure Stack Edge-eszköz helyreállítását megkönnyítő kulcstárolók.
- Csatorna titkosítási kulcsa

Forgassa el és szinkronizálja rendszeresen a Storage-fiók kulcsait, hogy megvédje a Storage-fiókját a jogosulatlan felhasználóktól.

- [Azure Stack Edge Pro biztonság és adatvédelem](azure-stack-edge-security.md)

- [Tárkulcsok szinkronizálása](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információ: [Azure Security Benchmark: Emelt jogosultságú hozzáférés](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Az üzletileg kritikus rendszerek rendszergazdai elérésének korlátozása

**Útmutató**: Azure stack Edge-megoldás számos, erős hozzáférés-vezérlésű összetevővel rendelkezik, amelyekkel korlátozható az üzleti szempontból kritikus eszközök hozzáférése. A szervezetnek szüksége lesz egy Nagyvállalati Szerződés (EA) vagy Cloud Solution Provider (CSP) vagy egy Microsoft Azure szponzorálás előfizetésre az eszköz konfigurálásához és kezeléséhez: 

A Azure Stack Edge szolgáltatást az Azure biztonsági szolgáltatásai védik az Azure-ban üzemeltetett felügyeleti szolgáltatásként. Az erőforráshoz tartozó titkosítási kulcsot bármely szoftverfejlesztői készlet felügyeleti művelethez használhatja az eszköz tulajdonságaiban, de csak akkor tekintheti meg a titkosítási kulcsot, ha rendelkezik a megfelelő engedélyekkel a (z) Graph API erőforráshoz.

Az Azure Stack Edge Pro-eszköz egy helyszíni eszköz, amely segít az adatok helyi feldolgozásában, majd az Azure-ba küldésében. Az eszköz:

- aktiválási kulcsra van szüksége az Azure Stack Edge szolgáltatás eléréséhez.
- az eszköz jelszavai mindig védelmet biztosítanak.
- a biztonságos rendszerindítás engedélyezve van.

- egy zárolt eszköz. Az eszköz alaplapi-felügyeleti vezérlője (BMC) és a BIOS jelszóval védett. A BIOS-t korlátozott felhasználói hozzáférés védi.
- a Windows Defender Device Guard eszközt futtatja. Az Eszközkezelő lehetővé teszi, hogy csak a kód-integritási házirendekben meghatározott megbízható alkalmazásokat futtasson.

Az Azure Stack Edge eléréséhez használt összes hitelesítő adat és titok védelméhez kövesse az ajánlott eljárásokat. 

- [Ajánlott eljárások a jelszavakhoz](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Felhasználói hozzáférés rendszerességének áttekintése és egyeztetése

**Útmutató**: Azure stack Edge rendelkezik egy "EdgeUser" nevű felhasználóval, amely képes konfigurálni az eszközt. Emellett Azure Resource Manager "EdgeArmUser" nevű felhasználóval is rendelkezik az eszköz helyi Azure Resource Manager funkcióihoz. 

Az ajánlott eljárásokat a következő védelemmel kell ellátni:

- A helyszíni eszköz eléréséhez használt hitelesítő adatok

- SMB-megosztás hitelesítő adatai.

- Hozzáférés az NFS-megosztások használatára konfigurált ügyféloldali rendszerekhez.

- A helyi Storage-fiókok a blob REST API használatakor való eléréséhez használt helyi Storage-fiókok kulcsai.

További információ a hivatkozott hivatkozáson érhető el.

- [Az Azure Stack Edge-eszközök biztonságával kapcsolatos információk](azure-stack-edge-security.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: a biztonságos és elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus szolgáltatási operátorok biztonsága szempontjából. Biztonságos felhasználói munkaállomások használata az Azure-ban vagy anélkül, rendszergazdai feladatokhoz. Az Azure Active Directory (Azure AD), a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) és a Microsoft Intune használatával biztonságos és felügyelt felhasználói munkaállomás helyezhető üzembe felügyeleti feladatokhoz. 

A védett munkaállomások központi kezelésével kikényszeríthető a biztonságos konfiguráció, beleértve az erős hitelesítést, a szoftveres és hardveres alapkonfigurációikat, valamint a korlátozott logikai és hálózati hozzáférést.

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Emelt szintű hozzáférésű munkaállomás üzembe helyezése](/security/compass/privileged-access-deployment)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: A bizalmas adatok védelme

**Útmutató**: Azure stack Edge az összes kezelt adat bizalmasként való kezelését csak a jogosult felhasználók férhetnek hozzá az adatokhoz. Az Azure Stack Edge szolgáltatás eléréséhez használt hitelesítő adatok védelméhez ajánlott eljárásokat kell követnie.

- [Azure Stack Edge Pro biztonság és adatvédelem](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Bizalmas információk átvitel közbeni titkosítása

**Útmutató**: a Azure stack Edge biztonságos csatornákat használ a repülésben lévő adatátvitelhez. Ezek a következők:

- A standard TLS 1,2 az eszköz és az Azure-felhő közötti adattovábbításra szolgál. Nincs tartalék a TLS 1,1-es és korábbi verzióihoz. Az ügynök kommunikációja le lesz tiltva, ha a TLS 1,2 nem támogatott. A TLS 1,2 a Azure Portal és a szoftverfejlesztői készlet (SDK) felügyeletéhez is szükséges.

- Amikor az ügyfelek egy böngésző helyi webes felhasználói felületén keresztül érik el az eszközt, a standard TLS 1,2 alapértelmezett biztonságos protokollként van használatban.

Az ajánlott eljárás az, hogy a böngészőt a TLS 1,2 használatára konfigurálja. Az SMB 3,0 titkosítással az adatok védelme érdekében az adatkiszolgálókról másolhatja őket.

- [Ajánlott eljárások a repülésben tárolt adatvédelemhez](azure-stack-edge-security.md#protect-data-in-flight)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információ: [Azure Security Benchmark: Összetevők kezelése](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Gondoskodás arról, hogy a biztonsági csapat átlássa az adategységek kockázatait

**Útmutató**: Győződjön meg arról, hogy a biztonsági csapatok biztonsági olvasó engedélyekkel rendelkeznek az Azure-bérlőben és -előfizetésekben, hogy monitorozni tudják a biztonsági kockázatokat az Azure Security Centerrel. 

A biztonsági kockázatok monitorozása a biztonsági csapat felelősségi köreinek struktúrájától függően egy központi biztonsági csapat vagy egy helyi csapat felelőssége lehet. A biztonsági megállapításokat és kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyek széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoport), vagy a hatókör alkalmazható adott felügyeleti csoportokra vagy előfizetésekre. 

Vegye figyelembe, hogy további engedélyekre lehet szükség a számítási feladatok és szolgáltatások láthatóságának megkezdéséhez. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../governance/management-groups/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: csak a jogosult felhasználók, például a "EdgeArmUser" érhetik el a Azure stack Edge-eszköz API-jait a helyi Azure Resource Manager keresztül. A felhasználói fiókok jelszavait csak a Azure Portal lehet felügyelni. 

- [Azure Resource Manager-jelszó beállítása](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: csak jóváhagyott alkalmazások használata számítási erőforrásokban

**Útmutató**: a saját alkalmazásaiban a helyileg létrehozott virtuális gépeken is futtathatók. A PowerShell-parancsfájlok használatával helyi számítási virtuális gépeket hozhat létre a verem peremhálózati eszközén. Azt javasoljuk, hogy csak megbízható alkalmazásokat használjon a helyi virtuális gépeken való futtatáshoz. 

- [A PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetben](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információ: [Azure Security Benchmark: Naplózás és fenyegetésészlelés](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: az Azure-erőforrások fenyegetés-észlelésének engedélyezése

**Útmutató**: az Azure stack Edge nem nyújt fenyegetés-észlelési képességeket. Azonban az ügyfelek az offline veszélyforrások észleléséhez és elemzéséhez támogatási csomagban gyűjthetik be a naplókat. 

- [Támogatási csomag összegyűjtése Azure Stack Edge-eszközhöz](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Naplózás engedélyezése Azure-beli hálózati tevékenységekhez

**Útmutató**: Azure stack Edge a letölthető támogatási csomag részeként engedélyezve van a hálózati naplók naplózása. Ezeket a naplókat elemezheti az Azure Stack Edge-eszközökre vonatkozó, félig valós idejű figyelés megvalósításához.

- [Azure Stack peremhálózati támogatási csomag összegyűjtése](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Naplózás engedélyezése Azure-erőforrásokhoz

**Útmutató**: a naplókhoz való valós idejű figyelés jelenleg nem támogatott Azure stack Edge-ben. Létezik olyan támogatási csomag, amely lehetővé teszi, hogy elemezze a benne foglalt különböző naplókat, például a tűzfalat, a szoftvereket, a hardveres behatolást és a rendszernaplókat az Azure Stack Edge Pro-eszközhöz. Vegye figyelembe, hogy a rendszer a szoftver behatolását vagy az alapértelmezett tűzfal naplóit gyűjti a bejövő és kimenő forgalomhoz.

- [Támogatási csomag összegyűjtése Azure Stack Edge-hez](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Biztonsági naplók kezelésének és elemzésének központosítása

**Útmutató**: a naplókhoz való valós idejű figyelés jelenleg nem támogatott Azure stack Edge-ben. Azonban olyan támogatási csomagot is gyűjthet, amely lehetővé teszi a benne foglalt különböző naplók elemzését.  A támogatási csomag tömörítve van, és a rendszer letölti az Ön által választott elérési útra. Bontsa ki a csomagot, és tekintse meg a benne található rendszernapló-fájlokat. Ezeket a naplókat elküldheti a biztonsági információkkal kapcsolatos esemény-felügyeleti eszközre vagy egy másik központi tárolási helyre az elemzéshez.

- [Támogatási csomag összegyűjtése Azure Stack Edge-hez](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Tárolt naplók megőrzésének konfigurálása

**Útmutató**: a naplózási tárolási időszak nem módosítható az Azure stack Edge-eszközön. A régebbi naplók szükség szerint törlődnek. A támogatási csomagokat az eszközről rendszeres időközönként összegyűjtheti, hogy minden követelmény hosszabb ideig őrizze meg a naplókat. 

- [Támogatási csomag összegyűjtése Azure Stack Edge-hez](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: a jóváhagyott időszinkronizálási források használata

**Útmutató**: az Azure stack Edge a Microsoft Time.Windows.com, egy hálózati időszolgáltató kiszolgálót használ.  Azure Stack Edge azt is lehetővé teszi az ügyfél számára, hogy a választott Network Time Protocol-kiszolgálót konfigurálja.

- [Azure Stack Edge-eszköz időbeállításainak konfigurálása](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Előkészítés – incidensválasz-folyamat frissítése az Azure-hoz

**Útmutató**: Ellenőrizze, hogy a szervezeti incidensek válaszának terve tartalmazza-e a folyamatokat: 

- a biztonsági incidensekre való reagáláshoz

- amelyek frissítve lettek az Azure Cloud-hoz
 
- a készültség biztosítása érdekében rendszeresen gyakorolják

Azt javasoljuk, hogy a nagyvállalati környezetben szabványosított incidens-reagálási folyamatokkal implementálja a biztonságot.

- [Biztonság implementálása a teljes nagyvállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incidensválasz referencia-útmutató](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Előkészítés – incidensről szóló értesítés beállítása

**Útmutató**: Állítson be a biztonsági incidensekre vonatkozó kapcsolattartási adatokat az Azure Security Centerben. A Microsoft ezen kapcsolattartási adatok használatával keresi meg Önt, ha a Microsoft Security Response Center (MSCR) felfedezi, hogy az adataihoz törvénytelen vagy jogosulatlan módon fértek hozzá. Lehetősége van az incidensekkel kapcsolatos riasztások és értesítések testreszabására a különböző Azure-szolgáltatásokban az incidensválasz-igények alapján. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: észlelés és elemzés – incidensek létrehozása kiváló minőségű riasztások alapján 

**Útmutató**: Ellenőrizze, hogy van-e olyan folyamat, amellyel kiváló minőségű riasztások hozhatók létre, és mérhetők a minőségük. Ez lehetővé teszi, hogy megismerje a múltbeli incidensek tanulságait, és rangsorolja a riasztásokat az elemzők számára, hogy elkerülje a hamis pozitív riasztások feldolgozásának idejét. Kiváló minőségű riasztásokat készíthet a múltbeli incidensek tapasztalatai alapján, érvényesített közösségi forrásokból és eszközökről, amelyek a különböző riasztási forrásokra vonatkozó naplózási és korrelációs riasztások létrehozásához és törléséhez készültek. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba. Hozzon létre speciális riasztási szabályokat az Azure Sentinel használatával automatikus incidensek létrehozásához a vizsgálathoz. 

A Security Center riasztások és javaslatok exportálása az Azure Sentinelbe az Exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. Javasoljuk, hogy hozzon létre egy folyamatot a riasztások és javaslatok automatizált módon történő exportálásához a folyamatos biztonság érdekében.

- [Az exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Észlelés és elemzés – incidensek kivizsgálása

**Útmutató**: az elemzők különböző adatforrások lekérdezésére és használatára képesek a lehetséges incidensek kivizsgálásakor bekövetkezett tevékenység teljes nézetének összeállításához. A különböző naplózási típusokat össze kell gyűjteni, hogy nyomon kövessék egy lehetséges támadó tevékenységeit a kill láncban a vak foltok elkerülése érdekében.  Emellett gondoskodjon arról is, hogy a rendszer a korábbi referenciák szerint rögzítse az információkat.  

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következők is lehetnek:

- Hálózati adatok – az Azure Network Watcher, az Azure Monitor és a hálózati biztonsági csoportok forgalmi naplóinak használata a hálózati forgalom naplóinak és más elemzési adatok rögzítéséhez. 

- A futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-készítési funkciójával pillanatképet készíthet a futó rendszer lemezéről. 

    - Válassza ki az operációs rendszer natív memóriaképének képességét a futó rendszer memóriájának pillanatképének létrehozásához.

    - Használja az Azure-szolgáltatások pillanatkép-szolgáltatását vagy egy harmadik féltől származó szoftver-képességet a futó rendszerek pillanatképének létrehozásához.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé szinte bármely naplózási forráson és egy esetfelügyeleti portálon az incidensek teljes életciklusának kezeléséhez. A vizsgálatok során kapott információk társíthatók egy incidenssel nyomkövetés és jelentéskészítés céljából. 

- [Windows rendszerű gép lemezének pillanatképe](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux rendszerű gép lemezének pillanatképe](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-támogatás – diagnosztikai információk és memóriaképek gyűjtése](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentinellel](../sentinel/tutorial-investigate-cases.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Észlelés és elemzés – incidensek rangsorolása

**Útmutató**: Biztosítson kontextust az elemzők számára hogy a riasztás súlyossága és az összetevő bizalmassága alapján tudják, mely incidensekre összpontosítsanak először. A Azure Security Center az összes riasztáshoz hozzárendelt súlyosságot a következővel lehet rangsorolni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy a Security Center mennyire biztos a találatban vagy a riasztás kibocsátásához használt elemzésben, valamint abban, hogy a riasztáshoz vezető tevékenység rosszindulatú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások, különösen a bizalmas adatok feldolgozását végző erőforrások azonosításához és kategorizálásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Elszigetelés, kiiktatás és helyreállítás – az incidenskezelés automatizálása

**Útmutató**: Az ismétlődő manuális tevékenységek automatizálásával jobb reakcióidő érhető el, és csökkenthető az elemzők terhelése. A manuális tevékenységek végrehajtása több időt vesz igénybe, emiatt az elemzők kevesebb incidenst képesek kezelni. A manuális feladatok ki is merítik az elemzőket, ami növeli az emberi hiba lehetőségét, és az elemzők kevésbé tudnak hatékonyan összpontosítani az összetett feladatokra. Az Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióival automatikusan indíthat el műveleteket és futtathat forgatókönyveket, hogy reagáljon a bejövő biztonsági riasztásokra. A forgatókönyv műveleteket hajt végre, például értesítéseket küld, fiókokat tilt le, és elkülöníti a problémás hálózatokat. 

- [Munkafolyamat-automatizálás konfigurálása a Security Centerben](../security-center/workflow-automation.md)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Security Centerben](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Sentinelben](../sentinel/tutorial-respond-threats-playbook.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="posture-and-vulnerability-management"></a>A biztonsági állapot és a biztonsági rések kezelése

*További információ: [Azure Security Benchmark: A biztonsági állapot és a biztonsági rések kezelése](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: biztonságos konfigurációk létrehozása számítási erőforrásokhoz

**Útmutató**: Azure stack Edge támogatja az ügyfelek által létrehozott helyi virtuális gépek biztonságos konfigurációjának létrehozását. Javasoljuk, hogy a Microsoft által biztosított irányelvek segítségével hozzon létre biztonsági alapkonfigurációkat a helyi virtuális gépek létrehozásakor,

- [A biztonsági megfelelőségi eszközkészletben található biztonsági alaptervek letöltése](/windows/security/threat-protection/windows-security-baselines)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: a számítási erőforrások biztonságos konfigurációjának fenntartása

**Útmutató**: Azure stack Edge nem nyújt támogatást az ügyfelek által létrehozott helyi virtuális gépek biztonságos konfigurációinak fenntartásához. Erősen ajánlott, hogy az ügyfelek a biztonsági megfelelőségi eszközkészleteket (SCT) használják a számítási erőforrások biztonságos konfigurációinak fenntartásához.

A Azure Stack Edge által felügyelt gazdagép operációs rendszere és virtuális gépei megőrzik biztonsági konfigurációit. 

- [Windows biztonsági alaptervek](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: az egyéni operációs rendszer és a tároló lemezképének biztonságos tárolása

**Útmutató**: az Azure stack Edge által felügyelt gazdagép operációs rendszerek és virtuális gépek biztonságos tárolása történik. 

Az ügyfelek saját virtuális gépeket és tárolókat hozhatnak fel, és a biztonságos felügyeletért felelősek.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Szoftveres biztonsági rések gyors és automatikus javítása

**Útmutató**: a Azure stack Edge rendszeres javításokat és riasztásokat biztosít a felhasználók számára, ha ezek a frissítések elérhetők a biztonsági rések elhárítása érdekében. Az ügyfél felelőssége, hogy az eszközeiket és a virtuális gépeket (ezek alapján létrehozva) naprakészen tartsa a legújabb javításokkal.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Rendszeres támadásszimulációk végrehajtása

**Útmutató**: Szükség esetén végezzen behatolási teszteket vagy riasztási gyakorlatokat az Azure-erőforrásokon, hogy biztosítva legyen az összes kritikus biztonsági találat megoldása.
A Microsoft-felhő behatolástesztelési beavatkozási szabályai szerint eljárva biztosíthatja, hogy a behatolási tesztek nem sértik a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolási tesztek az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="endpoint-security"></a>Endpoint Security

*További információt az [Azure biztonsági teljesítményteszt: Endpoint Security](../security/benchmarks/security-controls-v2-endpoint-security.md)című témakörben talál.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: végpont-észlelés és-válasz használata (EDR)

**Útmutató**: Azure stack Edge nem támogatja közvetlenül a végpontok észlelését és válaszát (EDR). Azonban gyűjthet egy támogatási csomagot, és lekérheti a naplókat. Ezeket a naplókat azután elemezni lehet a rendellenes tevékenységek kereséséhez. 

- [Támogatási csomag összegyűjtése Azure Stack Edge-eszközhöz](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: központilag felügyelt, modern kártevő szoftverek használata

**Útmutató**: Azure stack Edge a Windows Defender Application Control (WDAC) használatával szigorú kód-integritási (CI) házirenddel rendelkezik, amely csak az előre meghatározott alkalmazások és parancsfájlok futtatását engedélyezi. Engedélyezve van a Windows Defender valós idejű védelme (RTP) elleni kártevő is. Az ügyfél dönthet úgy, hogy kártevő szoftvereket futtat a Azure Stack peremhálózati eszközön helyileg futtatandó számítási virtuális gépeken.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

*További információ: [Azure Security Benchmark: Biztonsági mentés és helyreállítás](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: rendszeres automatikus biztonsági mentések biztosítása

**Útmutató**: az Azure stack Edge-eszköz rendszeres biztonsági mentése javasolt, és Azure Backup és más, harmadik féltől származó adatvédelmi megoldásokkal is elvégezhető az eszközön üzembe helyezett virtuális gépek adatainak védelme érdekében. 

A harmadik féltől származó adatvédelmi megoldások, például az összetartó, a CommVault és a Veritas biztonsági mentési megoldást is biztosíthatnak a helyi SMB-vagy NFS-megosztásokban lévő adatvédelemhez. 

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [Eszköz meghibásodásának előkészítése](azure-stack-edge-gpu-prepare-device-failure.md)

- [A virtuális gépek fájljainak és mappáinak a védelemmel való ellátása](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="br-2-encrypt-backup-data"></a>BR-2: biztonsági mentési adataik titkosítása

**Útmutató**: gondoskodjon arról, hogy a biztonsági másolatok védve legyenek a támadásokkal szemben. Ennek tartalmaznia kell a biztonsági másolatok titkosítását a titkosság elvesztése elleni védelem érdekében.  További információkért tekintse meg a választható biztonsági mentési megoldást.

- [Az adatvédelmet a peremhálózat helyi megosztásai között](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [Fájlok és mappák védetté való ellátása a virtuális gépeken](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Az összes biztonsági másolat és az ügyfelek által kezelt kulcsok ellenőrzése

**Útmutató**: a biztonsági másolatok adat-visszaállításának rendszeres időközönkénti végrehajtása. 

- [Azure Stack Edge helyreállítási eljárásai](azure-stack-edge-gpu-recover-device-failure.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: A kulcsok elvesztésével járó kockázat csökkentése

**Útmutató**: gondoskodjon arról, hogy az összes Azure stack Edge biztonsági mentés, beleértve az ügyfél által felügyelt kulcsokat, az ajánlott szervezeti eljárások szerint védve legyen. Az Azure Stack Edge-eszköz társítva van egy Azure Storage-fiókkal, amelyet az Azure-ban tárolt adataihoz célként használt adattárként használ a rendszer. 

Az Azure Storage-fiókhoz való hozzáférést az Azure-előfizetések, valamint az ehhez a Storage-fiókhoz társított 2 512 bites tárterület-hozzáférési kulcsok vezérlik. Az egyik hozzáférési kulcs a hitelesítési célokra használatos, amikor az Azure Stack Edge-eszköz hozzáfér a Storage-fiókhoz. A másik kulcs az időszakos kulcs elforgatására van fenntartva. Győződjön meg arról, hogy az ajánlott biztonsági eljárások a kulcsok elforgatására szolgálnak.

- [Azure Stack peremhálózati eszköz adatainak védelme az Azure Storage-fiókokban](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információ: [Azure Security Benchmark: Irányítás és stratégia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Eszközkezelési és adatvédelmi stratégia 

**Útmutató**: Mindig dokumentáljon és tegyen közzé a rendszerek és adatok folyamatos monitorozására és védelmére vonatkozó egyértelmű stratégiát. Állítsa fel az üzletileg kritikus adatok és rendszerek felmérésének, védelmének és monitorozásának fontossági sorrendjét. 

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   Az üzleti kockázattal összhangban lévő adatbesorolási szabványok

-   A kockázatok és az eszközleltár biztonsági szervezet általi láthatósága 

-   A használandó Azure-szolgáltatások biztonsági szervezet általi jóváhagyása 

-   Az biztonsága azok teljes életciklusában

-   A vállalati adabesorolással összhangban szükséges hozzáférés-vezérlési stratégia

-   Natív Azure-beli és külső adatvédelmi képességek használata

-   Átvitel közbeni és inaktív adattitkosításra vonatkozó követelmények

-   A megfelelő titkosítási szabványok

További információkat az alábbi hivatkozásokon találhat:
- [Az Azure Security architektúrára vonatkozó ajánlásai – Tárolás, adatok és titkosítás](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Az Azure Security alapjai – Azure-beli adatbiztonság, titkosítás és tárolás](../security/fundamentals/encryption-overview.md)

- [Felhőadaptálási keretrendszer – Az Azure-beli adatbiztonsághoz és titkosításhoz ajánlott eljárások](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – Eszközkezelés](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – Adatvédelem](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Nagyvállalati szegmentálási stratégia definiálása 

**Útmutató**: Alakítson ki az egész vállalatra kiterjedő stratégiát, amely identitás, alkalmazás, előfizetés, felügyeleti csoport és más vezérlők kombinációjával szegmentálja az összetevőkhöz való hozzáférést.

Gondosan egyensúlyozza ki a biztonsági elkülönítés igényét azoknak a rendszereknek a mindennapos működésével, amelyeknek kommunikálniuk kell egymással, és hozzá kell férniük az adatokhoz.

Gondoskodjon róla, hogy a szegmentálási stratégia következetesen meg legyen valósítva az olyan vezérlőtípusokon, mint a hálózati biztonság, az identitás- és hozzáférés-modellek, az alkalmazások jogosultsági és hozzáférési modelljei és az emberi folyamatokra vonatkozó vezérlők.

- [Útmutató Azure-beli szegmentálási stratégiához (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató Azure-beli szegmentálási stratégiához (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [Hálózati szegmentálás vállalati szegmentálási stratégiához igazítása](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Biztonsági állapotot felügyelő stratégiája definiálása

**Útmutató**: Folyamatosan mérje és mérsékelje a kockázatokat, amelyeknek az egyes összetevők, valamint az a környezet van kitéve, amelyben üzemeltetve vannak. Kezelje kiemelten az olyan nagy értékű összetevőket és leginkább elérhetővé tett támadási felületeket, mint a közzétett alkalmazások, a hálózat be- és kilépési pontjai, a felhasználói és rendszergazdai végpontok stb.

- [Azure Security Benchmark - Biztonsági állapot és biztonsági rések felmérése](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: A vállalati szerepkörök, a felelősség és az elszámoltathatóság összehangolása

**Útmutató**: Mindig dokumentálja és tegye közzé a biztonsági szervezet szerepköreire és felelősségi köreire vonatkozó egyértelmű stratégiát. Kezelje kiemelten a biztonsági döntések egyértelmű elszámoltathatóságát, a megosztott felelősségi modell oktatását mindenki számára, és a technikai csapatok oktatását a felhőbeli biztonsági technikákra.

- [Ajánlott Azure-biztonsági eljárások 1 – Személyek: Csapatok oktatása a felhőbeli biztonság kialakítására](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Ajánlott Azure-biztonsági eljárások 2 – Személyek: Csapatok oktatása a felhőbeli biztonsági technológiákra](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Ajánlott Azure-biztonsági eljárások 3 – Folyamat: Elszámoltathatóság hozzárendelése felhőbeli biztonsági döntésekhez](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Hálózati biztonsági stratégia definiálása

**Útmutató**: Azure-hálózati biztonsági módszert a vállalat átfogó biztonsági hozzáférés-vezérlési stratégiájának részeként alakíthat ki.  

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   Központosított hálózatkezelési és biztonsági felelősség

-   A vállalati szegmentálási stratégiának megfelelő virtuális hálózati szegmentálási modell

-   Szervizelési stratégia különböző fenyegetési és támadási helyzetekre

-   Internetes peremhálózati, bejövő és kimenő forgalomra vonatkozó stratégia

-   Hibrid felhős és helyszíni kapcsolódási stratégia

-   Naprakész hálózati biztonsági összetevők (pl. hálózati diagramok, hálózati referencia-architektúra)

További információkat az alábbi hivatkozásokon találhat:
- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Hálózati biztonság](../security/benchmarks/index.yml)

- [A nagyvállalati hálózati biztonság áttekintése](../security/fundamentals/network-overview.md)

- [Nagyvállalati hálózati architektúrára vonatkozó stratégia](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Identitáskezelési és emelt jogosultságú hozzáférési stratégia definiálása

**Útmutató**: Az identitások és az emelt jogosultságú hozzáférések kezelésének Azure-beli módszerét a vállalat átfogó biztonsági hozzáférés-vezérlési stratégiájának részeként alakíthat ki.  

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   Központosított identitáskezelési és hitelesítési rendszer és annak kapcsolatai más belső és külső identitásrendszerekkel

-   Erős hitelesítési módszerek különböző használati helyzetek és feltételek esetén

-   Emelt jogosultságú felhasználók védelme

-   Rendellenes felhasználói tevékenységek monitorozása és kezelése  

-   Felhasználói identitások és hozzáférések felülvizsgálati és egyeztetési folyamata

További információkat az alábbi hivatkozásokon találhat:

- [Azure Security Benchmark – Identitáskezelés](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – Emelt jogosultságú hozzáférés](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure identitáskezelésének biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Naplózási és veszélyforrás-kezelési stratégia definiálása

**Útmutató**: Naplózási és veszélyforrás-kezelési stratégia kidolgozásával gyorsan észlelheti és elháríthatja a fenyegetéseket, ugyanakkor betartva a megfelelőségi követelményeket. Kezelje kiemelten a jó minőségű riasztások biztosítását az elemzők számára, és a könnyen használható felületeket, hogy az integráció és a manuális lépések helyett a veszélyforrásokra összpontosíthassanak. 

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   A biztonsági üzemeltetési (SecOps) szervezet szerepköre és feladatai 

-   Jól definiált incidenskezelési folyamat az NIST-vel vagy más iparági keretrendszerrel összhangban 

-   Naplórögzítés és -megőrzés a veszélyforrások észlelése, az incidensek kezelése és a megfelelőségi igények támogatására

-   A fenyegetésekkel kapcsolatos információk központosított láthatósága és összevetése SIEM, natív Azure-képességek és más források használatával 

-   Kommunikációs és értesítési terv az ügyfelek, szállítók és külső érdekelt felek számára

-   Natív Azure-beli és külső platformok használata incidensek kezelésére, például naplózásra és veszélyforrások észlelésére, kivizsgálásra és a támadások megfékezésére és megszüntetésére

-   Folyamatok az olyan incidenskezelési és incidens utáni tevékenységekhez, mint a tanulságok levonása és a nyomok megőrzése

További információkat az alábbi hivatkozásokon találhat:

- [Azure Security Benchmark – Naplózás és fenyegetésészlelés](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – Incidenskezelés](../security/benchmarks/security-controls-v2-incident-response.md)

- [Ajánlott Azure-biztonsági eljárások 4 – Folyamat. Incidensválasz-folyamat frissítése a felhőhöz](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure-adaptálási keretrendszer, útmutató naplózási és jelentéskészítési döntésekhez](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
