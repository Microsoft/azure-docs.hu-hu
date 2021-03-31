---
title: Ajánlott biztonsági eljárások az Azure-beli IaaS számítási feladatokhoz | Microsoft Docs
description: " A számítási feladatok Azure IaaS-ba való áttelepítése lehetőséget biztosít a tervek újraértékelésére "
services: security
documentationcenter: na
author: terrylanfear
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 6f073777930b4d026d826d2c3586e0886f906206
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102503079"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Ajánlott biztonsági eljárások IaaS számítási feladatokhoz az Azure-ban
Ez a cikk a virtuális gépek és operációs rendszerek ajánlott biztonsági eljárásait ismerteti.

Az ajánlott eljárások a vélemények konszenzusán alapulnak, és a jelenlegi Azure platform-képességekkel és-szolgáltatásokkal működnek. Mivel a vélemények és technológiák idővel változhatnak, ez a cikk frissülni fog, hogy tükrözze ezeket a módosításokat.

A legtöbb infrastruktúra-szolgáltatási (IaaS) forgatókönyv esetén az [Azure Virtual Machines (VM)](../../virtual-machines/index.yml) a felhő-számítástechnikai szolgáltatásokat használó szervezetek fő munkaterhelése. Ez a tény olyan [hibrid forgatókönyvekben](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) nyilvánvaló, ahol a szervezetek lassan szeretnék áttelepíteni a számítási feladatokat a felhőbe. Ilyen esetekben kövesse a [IaaS általános biztonsági szempontjait](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx), és alkalmazza az ajánlott biztonsági eljárásokat az összes virtuális gépre.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Virtuális gépek elleni védelem hitelesítés és hozzáférés-vezérlés használatával
A virtuális gépek védelmének első lépése annak biztosítása, hogy csak a jogosult felhasználók állíthatnak be új virtuális gépeket és érhetik el a virtuális gépeket.

> [!NOTE]
> Az Azure-beli linuxos virtuális gépek biztonságának javítása érdekében integrálható az Azure AD-hitelesítéssel. Ha az [Azure ad-hitelesítést Linux rendszerű virtuális gépekhez](../../virtual-machines/linux/login-using-aad.md)használja, központilag szabályozza és érvényesíti a virtuális gépekhez való hozzáférést engedélyező vagy megtagadó házirendeket.
>
>

**Ajánlott eljárás**: a virtuális gépek hozzáférésének szabályozása.   
**Részletek**: az [Azure-szabályzatok](../../governance/policy/overview.md) segítségével hozhat létre konvenciókat a szervezet erőforrásaihoz, és létrehozhat testreszabott házirendeket. Alkalmazza ezeket a házirendeket erőforrásokra, például [erőforráscsoportok](../../azure-resource-manager/management/overview.md). Az erőforráscsoporthoz tartozó virtuális gépek öröklik a szabályzatokat.

Ha a cég több előfizetéssel rendelkezik, szüksége lehet egy hatékony módszerre az előfizetések hozzáférésének, szabályzatainak és megfelelőségének kezelésére. Az [Azure felügyeleti csoportjai](../../governance/management-groups/overview.md) az előfizetések feletti szintű hatókört biztosítanak. Az előfizetéseket felügyeleti csoportokba (tárolókban) rendszerezheti, és alkalmazhatja az Ön irányítási feltételeit ezekre a csoportokra. Egy felügyeleti csoportban lévő összes előfizetés automatikusan örökli a csoportra alkalmazott feltételeket. A felügyeleti csoportok nagy léptékű, nagyvállalati szintű felügyeletet tesznek lehetővé, függetlenül az előfizetése típusától.

**Ajánlott eljárás**: csökkentse a változékonyságot a beállításában és a virtuális gépek üzembe helyezésében.   
**Részletek**: [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) sablonok használatával erősítse meg az üzembe helyezési lehetőségeket, és könnyebben értelmezheti és leltárba hozhatja a virtuális gépeket a környezetben.

**Ajánlott eljárás**: biztonságos privilegizált hozzáférés.   
**Részletek**: a [legalacsonyabb jogosultsági szintű megközelítés](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) és a beépített Azure-szerepkörök használata lehetővé teszi a felhasználók számára a virtuális gépek elérését és beállítását:

- [Virtuális gépek közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): felügyelheti a virtuális gépeket, de nem a virtuális hálózati vagy a Storage-fiókot, amelyhez csatlakoznak.
- [Klasszikus virtuálisgép-közreműködő](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): felügyelheti a klasszikus üzemi modellel létrehozott virtuális gépeket, de nem a virtuális hálózati vagy a Storage-fiókot, amelyhez a virtuális gépek csatlakoznak.
- [Biztonsági rendszergazda](../../role-based-access-control/built-in-roles.md#security-admin): csak Security Centerban: megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági szabályzatokat, biztonsági házirendeket szerkeszthet, megtekintheti a riasztásokat és a javaslatokat, elhagyhatja a riasztás
- [DevTest Labs-felhasználó](../../role-based-access-control/built-in-roles.md#devtest-labs-user): megtekintheti a virtuális gépeket, és csatlakozhat, indíthat, indíthat újra és állíthat le.

Az előfizetési rendszergazdák és az adminisztrátorok módosíthatják ezt a beállítást, így a rendszergazdák az előfizetésben lévő összes virtuális gép rendszergazdái. Győződjön meg arról, hogy az összes előfizetés-rendszergazda és-rendszergazda megbízhatónak tartja a számítógépekre való bejelentkezést.

> [!NOTE]
> Javasoljuk, hogy az azonos életciklusú virtuális gépeket ugyanabban az erőforráscsoporthoz egyesítse. Az erőforráscsoportok használatával üzembe helyezheti, figyelheti és elvégezheti az erőforrások számlázási költségeit.
>
>

A virtuális gépek hozzáférését és beállítását vezérlő szervezetek javítják a virtuális gépek teljes biztonságát.

## <a name="use-multiple-vms-for-better-availability"></a>Több virtuális gép használata a jobb rendelkezésre állás érdekében
Ha a virtuális gép olyan kritikus fontosságú alkalmazásokat futtat, amelyeknek magas rendelkezésre állásra van szükségük, javasoljuk, hogy több virtuális gépet használjon. A jobb rendelkezésre állás érdekében használjon [rendelkezésre állási készletet](../../virtual-machines/availability-set-overview.md) vagy rendelkezésre állási [zónákat](../../availability-zones/az-overview.md).

A rendelkezésre állási csoport olyan logikai csoportosítás, amelyet az Azure-ban használhat, így biztosíthatja, hogy a benne található virtuálisgép-erőforrások elkülönítve legyenek egymástól, amikor egy Azure-adatközpontban üzembe vannak helyezve. Az Azure biztosítja, hogy a rendelkezésre állási csoportba helyezett virtuális gépek több fizikai kiszolgálón, számítási állványokon, tárolási egységeken és hálózati kapcsolókon fussanak. Ha hardveres vagy Azure-beli szoftveres hiba lép fel, a rendszer csak a virtuális gépek egy részhalmazát érinti, és a teljes alkalmazás továbbra is elérhető lesz az ügyfelek számára. A rendelkezésre állási csoportok nélkülözhetetlen képességgel rendelkeznek, ha megbízható felhőalapú megoldásokat szeretne létrehozni.

## <a name="protect-against-malware"></a>Védekezés a kártevők ellen
A kártevők elleni védelmet a vírusok, kémprogramok és más kártevő szoftverek azonosításához és eltávolításához kell telepíteni. Telepítheti a [Microsoft antimalware](antimalware.md) -t vagy egy Microsoft-partner Endpoint Protection-megoldását ([Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)és [System Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

A Microsoft antimalware olyan funkciókat tartalmaz, mint a valós idejű védelem, az ütemezett vizsgálat, a kártevők szervizelése, az aláírások frissítése, a motor frissítései, a minták jelentéskészítés és a kizárási események gyűjteménye. Az éles környezettől függetlenül üzemeltetett környezetekben a virtuális gépek és a Cloud Services elleni védelem érdekében antimalware-bővítményt használhat.

Az üzembe helyezéshez és a beépített észlelésekhez (riasztások és incidensek) egyszerűen integrálható a Microsoft antimalware és a partneri megoldások [Azure Security Center](../../security-center/index.yml) használatával.

**Ajánlott eljárás**: kártevők elleni védelemre szolgáló antimalware-megoldás telepítése.   
**Részletek**: [Microsoft partneri megoldás vagy Microsoft antimalware telepítése](../../security-center/security-center-services.md#supported-endpoint-protection-solutions-)

**Ajánlott eljárás**: az antimalware-megoldás integrálása a Security Center a védelem állapotának figyelésére.   
**Részletek**: [az Endpoint Protection hibáinak kezelése a Security Center](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>A virtuális gépek frissítéseinek kezelése
Az Azure-beli virtuális gépeket, például az összes helyszíni virtuális gépet, a felhasználók felügyelik. Ezekre az Azure nem küldi le a Windows-frissítéseket. A virtuális gép frissítéseit kell kezelnie.

**Ajánlott eljárás**: a virtuális gépek naprakészen tartása.   
**Részletek**: a Azure Automation [Update Management](../../automation/update-management/overview.md) megoldásával kezelheti az Azure-ban, a helyszíni környezetekben vagy más felhőalapú szolgáltatókban üzembe helyezett Windows-és Linux-számítógépek operációs rendszerének frissítéseit. Az elérhető frissítések állapota minden ügynökszámítógépen egyszerűen felmérhető, és felügyelhető a kiszolgálók szükséges frissítéseinek telepítése is.

A Frissítéskezelés által kezelt számítógépek a következő konfigurációk használatával hajtják végre a felméréseket és frissítik az üzemelő példányokat:

- Microsoft Monitoring Agent (MMA) Windows vagy Linux rendszerhez
- PowerShell-célállapotkonfiguráció (DSC) Linux rendszerre
- Automation hibrid runbook-feldolgozó
- Microsoft Update vagy Windows Server Update Services (WSUS) Windows-számítógépekhez

Ha Windows Update használ, hagyja engedélyezve az automatikus Windows Update beállítást.

**Ajánlott eljárás**: Ellenőrizze, hogy a létrehozott rendszerképek tartalmazzák-e a legújabb Windows-frissítések körét.   
**Részletek**: minden központi telepítés első lépéseként keresse meg és telepítse az összes Windows-frissítést. Ez a mérték különösen fontos az Ön vagy a saját könyvtárából származó rendszerképek telepítésekor. Habár az Azure Marketplace-ről származó rendszerképeket alapértelmezés szerint automatikusan frissíti a rendszer, a nyilvános kiadás után a késési idő (akár néhány hét) is lehet.

**Ajánlott eljárás**: rendszeresen telepítse újra a virtuális gépeket az operációs rendszer új verziójának kényszerítéséhez.   
**Részletek**: megadhatja a virtuális gépet egy [Azure Resource Manager sablonnal](../../azure-resource-manager/templates/template-syntax.md) , hogy könnyen újra üzembe lehessen helyezni. A sablonok használatával a rendszer egy javított és biztonságos virtuális gépet biztosít, amikor szüksége van rá.

**Ajánlott eljárás**: a virtuális gépek biztonsági frissítéseinek gyors alkalmazása.   
**Részletek**: engedélyezze a Azure Security Center (ingyenes szint vagy standard szint) a [hiányzó biztonsági frissítések azonosításához és azok alkalmazásához](../../security-center/asset-inventory.md).

**Ajánlott eljárás**: telepítse a legújabb biztonsági frissítéseket.   
**Részletek**: az ügyfelek által az Azure-ba áthelyezett első munkaterhelések némelyike Labs-és külső rendszerű rendszerek. Ha az Azure-beli virtuális gépek olyan alkalmazásokat vagy szolgáltatásokat futtatnak, amelyek számára elérhetőnek kell lenniük az interneten, ügyelni kell a javítások megadására. Az operációs rendszeren túli javítás. A partneri alkalmazásokban a nem javított biztonsági rések olyan problémákat okozhatnak, amelyek elkerülhetők, ha a megfelelő javítási felügyelet van érvényben.

**Ajánlott eljárás**: biztonsági mentési megoldás üzembe helyezése és tesztelése.   
**Részletek**: a biztonsági mentést ugyanúgy kell kezelni, mint bármely más műveletet. Ez az éles környezet részét képező, a felhőre kiterjedő rendszerek esetében igaz.

A teszt-és fejlesztői rendszereknek olyan biztonsági mentési stratégiákat kell követniük, amelyek a felhasználók által a megszokott módon létrehozott visszaállítási képességeket biztosítják a helyszíni környezetek tapasztalatai alapján. Az Azure-ba áthelyezett éles számítási feladatok csak akkor integrálhatók a meglévő biztonsági mentési megoldásokkal, ha lehetséges. Vagy használhatja a [Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) a biztonsági mentési követelmények megoldásához.

Azok a szervezetek, amelyek nem kényszerítik a szoftverfrissítési házirendeket, jobban ki vannak téve az ismert, korábban rögzített biztonsági réseket használó fenyegetéseknek. Az iparági szabályozásoknak való megfelelés érdekében a vállalatoknak bizonyítaniuk kell, hogy szorgalmasak, és a megfelelő biztonsági ellenőrzésekkel biztosítják a felhőben található számítási feladatok biztonságát.

Szoftver – a hagyományos adatközpontok és az Azure-IaaS ajánlott eljárásainak frissítése számos hasonlóságot mutat. Javasoljuk, hogy értékelje ki az aktuális szoftverfrissítési szabályzatokat az Azure-ban található virtuális gépek belefoglalásához.

## <a name="manage-your-vm-security-posture"></a>A virtuális gép biztonsági helyzetének kezelése
A előforduló kiberfenyegetésekkel kapcsolatban fejlődik. A virtuális gépek védelméhez olyan figyelési képességre van szükség, amely gyorsan képes észlelni a fenyegetéseket, megakadályozhatja az erőforrásokhoz való jogosulatlan hozzáférést, riasztásokat indíthat, és csökkentheti a téves pozitív állapotot

A Windows és a Linux [rendszerű](../../security-center/security-center-introduction.md) [virtuális gépek](../../security-center/security-center-introduction.md)biztonsági helyzetének figyeléséhez használja a [Azure Security Center](../../security-center/security-center-introduction.md). Security Center a virtuális gépek védelmét a következő lehetőségek kihasználása révén biztosíthatja:

- Operációs rendszer biztonsági beállításainak alkalmazása ajánlott konfigurációs szabályokkal.
- Azonosíthatja és letöltheti a rendszerbiztonsági és a kritikus fontosságú, esetlegesen hiányzó frissítéseket.
- Javaslatok üzembe helyezése a végponti kártevők elleni védelemhez.
- Ellenőrizze a lemez titkosítását.
- A biztonsági rések felmérése és szervizelése.
- Fenyegetések észlelése.

A Security Center aktívan nyomon követheti a fenyegetéseket, és a potenciális fenyegetések a biztonsági riasztásokban vannak kitéve. A korrelált fenyegetéseket egyetlen, biztonsági incidensnek nevezett nézetben összesítjük.

A Security Center [Azure monitor naplókban](../../azure-monitor/logs/log-query-overview.md)tárolja az adattárakat. Azure Monitor a naplók egy lekérdezési nyelvet és elemzési motort biztosítanak, amely betekintést nyújt az alkalmazások és erőforrások működésére. A rendszer az adatokat a felhőben vagy a helyszínen lévő virtuális gépekre telepített [Azure monitorokból](../../batch/monitoring-overview.md), felügyeleti megoldásokból és ügynökökből is gyűjti. A közös funkcióknak köszönhetően átfogó képet alkothat a környezetről.

Azok a szervezetek, amelyek nem kényszerítik ki az erős biztonságot a virtuális gépek számára, továbbra is tudatában vannak a jogosulatlan felhasználók lehetséges kísérleteinek a biztonsági ellenőrzések megkerülése érdekében

## <a name="monitor-vm-performance"></a>VIRTUÁLIS gépek teljesítményének figyelése
Az erőforrás-visszaélés akkor lehet probléma, ha a virtuális gép folyamatai több erőforrást használnak, mint amennyit kellene. A virtuális gépekkel kapcsolatos teljesítményproblémák a szolgáltatás megszakadásához vezethetnek, ami sérti a rendelkezésre állás biztonsági elvét. Ez különösen fontos az IIS-t vagy más webkiszolgálókat üzemeltető virtuális gépek esetében, mivel a magas CPU-vagy memóriahasználat a szolgáltatásmegtagadási (DoS) támadásokat jelezhetik. Fontos, hogy a virtuális gépek hozzáférését a probléma előfordulásakor ne csak a reaktív módon figyelje, hanem proaktív módon, a normál működés során mért alapkonfigurációt is.

Javasoljuk, hogy a [Azure monitor](../../azure-monitor/data-platform.md) használatával betekintést nyerjen az erőforrás állapotára. Azure Monitor funkciók:

- [Erőforrás-diagnosztikai naplófájlok](../../azure-monitor/essentials/platform-logs-overview.md): FIGYELI a virtuális gépek erőforrásait, és azonosítja a teljesítményt és a rendelkezésre állást befolyásoló lehetséges problémákat.
- [Azure Diagnostics bővítmény](../../azure-monitor/agents/diagnostics-extension-overview.md): a Windows rendszerű virtuális gépek monitorozási és diagnosztikai képességeit biztosítja. Ezeket a képességeket engedélyezheti, ha a bővítményt a [Azure Resource Manager sablon](../../virtual-machines/extensions/diagnostics-template.md)részeként is engedélyezi.

Azok a szervezetek, amelyek nem figyelik a virtuális gépek teljesítményét, nem tudják megállapítani, hogy a teljesítménybeli minták bizonyos változásai normál vagy rendellenesek. Egy virtuális gép, amely a megszokottnál több erőforrást használ, a külső erőforrás vagy a virtuális gépen futó sérült folyamat támadását jelezhetik.

## <a name="encrypt-your-virtual-hard-disk-files"></a>A virtuális merevlemezek fájljainak titkosítása
Javasoljuk, hogy Titkosítsa a virtuális merevlemezeket (VHD-k), hogy megvédje a rendszerindító kötetet és az adatmennyiségeket a tárhelyen, valamint a titkosítási kulcsokat és a titkokat.

A [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) segítségével titkosíthatja a Windows-és Linux-IaaS virtuális gépek lemezeit. Azure Disk Encryption a Windows iparági szabványnak megfelelő [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) -szolgáltatását és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) FUNKCIÓJÁT használja az operációs rendszer és az adatlemezek mennyiségi titkosításának biztosításához. A megoldás integrálva van [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) a Key Vault-előfizetésében lévő lemezes titkosítási kulcsok és titkos kódok vezérléséhez és kezeléséhez. A megoldás azt is biztosítja, hogy a virtuális gépek lemezein tárolt összes adatok titkosítva legyenek az Azure Storage-ban.

A Azure Disk Encryption használatának ajánlott eljárásai a következők:

**Ajánlott eljárás**: a virtuális gépek titkosításának engedélyezése.   
**Részletek**: a Azure Disk Encryption létrehozza és írja a kulcstárolóba a titkosítási kulcsokat. A Key vaultban lévő titkosítási kulcsok kezeléséhez Azure AD-hitelesítés szükséges. Hozzon létre egy Azure AD-alkalmazást erre a célra. Hitelesítési célból az ügyfél titkos hitelesítése vagy az [ügyféltanúsítvány-alapú Azure ad-hitelesítés](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)is használható.

**Ajánlott eljárás**: kulcs titkosítási kulcs (KEK) használata a titkosítási kulcsok további biztonsági rétegéhez. Adjon hozzá egy KEK-t a kulcstartóhoz.   
**Részletek**: az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmag használatával hozzon létre egy kulcs-titkosítási kulcsot a kulcstartóban. Egy KEK-t is importálhat a helyszíni hardveres biztonsági modulról (HSM) a kulcskezelő szolgáltatáshoz. További információ: [Key Vault dokumentáció](../../key-vault/keys/hsm-protected-keys.md). Ha a kulcs titkosítási kulcsa meg van adva, Azure Disk Encryption ezt a kulcsot használja a titkosítási titok becsomagolásához a Key Vaultba való írás előtt. A kulcs letéti másolatának a helyszíni kulcskezelő HSM-ben való megőrzése további védelmet nyújt a kulcsok véletlen törlésével szemben.

**Ajánlott eljárás**: készítsen [pillanatképet](../../virtual-machines/windows/snapshot-copy-managed-disk.md) és/vagy biztonsági mentést a lemezek titkosítása előtt. A biztonsági másolatok helyreállítási lehetőséget biztosítanak, ha nem várt hiba történik a titkosítás során.   
**Részletek**: a felügyelt lemezekkel rendelkező virtuális gépek biztonsági mentést igényelnek a titkosítás megkezdése előtt. A biztonsági mentést követően a **set-AzVMDiskEncryptionExtension** parancsmag használatával titkosíthatja a felügyelt lemezeket a *-skipVmBackup* paraméter megadásával. További információ a titkosított virtuális gépek biztonsági mentéséről és visszaállításáról: [Azure Backup](../../backup/backup-azure-vms-encryption.md) .

**Ajánlott eljárás**: annak biztosítása érdekében, hogy a titkosítási titkok ne legyenek régióközi határokon átívelőek, Azure Disk Encryption szükség van a kulcstartóra és a virtuális gépekre, amelyek ugyanabban a régióban találhatók.   
**Részletek**: hozzon létre és használjon olyan kulcstartót, amely ugyanabban a régióban található, mint a titkosítani kívánt virtuális gép.

Azure Disk Encryption alkalmazása esetén a következő üzleti igényeknek is eleget kell tennie:

- A szolgáltatott infrastruktúra inaktív virtuális gépeit védő, az iparági szabványnak megfelelő titkosítási technológia kielégíti a vállalati biztonsági és megfelelőségi követelményeket.
- A IaaS-alapú virtuális gépek az ügyfél által vezérelt kulcsok és szabályzatok alapján kezdődnek, és a Key vaultban is naplózhatja a használatot.

## <a name="restrict-direct-internet-connectivity"></a>Közvetlen internetkapcsolat korlátozása
A virtuális gép közvetlen internetkapcsolatának figyelése és korlátozása. A támadók folyamatosan megkeresik a nyilvános Felhőbeli IP-tartományokat a nyílt felügyeleti portok számára, és megkísérlik a közös jelszavakat, valamint az ismert, nem javított biztonsági réseket. Az alábbi táblázat a támadások elleni védelemhez nyújt ajánlott eljárásokat:

**Ajánlott eljárás**: a hálózati Útválasztás és biztonság véletlen expozíciójának megakadályozása.   
**Részletek**: az Azure RBAC használatával győződjön meg arról, hogy csak a központi hálózati csoport rendelkezik engedéllyel a hálózati erőforrásokhoz.

**Ajánlott eljárás**: azonosítsa és javítsa ki a kitett virtuális gépeket, amelyek engedélyezik a hozzáférést az "any" forrás IP-címről.   
**Részletek**: Azure Security Center használata. Security Center azt javasolja, hogy az internetre irányuló végpontokon keresztül korlátozza a hozzáférést, ha valamelyik hálózati biztonsági csoport egy vagy több bejövő szabályt tartalmaz, amelyek engedélyezik a hozzáférést az "any" forrás IP-címről. Security Center azt javasolja, hogy szerkessze ezeket a bejövő szabályokat, hogy [korlátozza](../../security-center/security-center-network-recommendations.md) a hozzáférést a forrás IP-címekhez, amelyeknek ténylegesen hozzáférésre van szükségük.

**Ajánlott eljárás**: a felügyeleti portok korlátozása (RDP, SSH).   
**Részletek**: az igény szerinti [(JIT)](../../security-center/security-center-just-in-time.md) virtuálisgép-hozzáférés lehetővé teszi az Azure-beli virtuális gépek bejövő forgalmának zárolását, így csökkentve a támadásoknak való kitettséget, miközben könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz, ha szükséges. Ha a JIT engedélyezve van, Security Center a hálózati biztonsági csoport szabályának létrehozásával zárolja az Azure-beli virtuális gépek felé irányuló bejövő forgalmat. Válassza ki a virtuális gépen azokat a portokat, amelyeken a bejövő forgalom le lesz zárva. Ezeket a portokat a JIT-megoldás vezérli.

## <a name="next-steps"></a>Következő lépések
Az Azure-beli felhőalapú megoldások tervezésekor, üzembe helyezése és kezelése során ajánlott biztonsági eljárásokat az [Azure biztonsági eljárásaival és modelljeivel](best-practices-and-patterns.md) foglalkozó témakörben talál.

Az Azure-biztonsággal és a kapcsolódó Microsoft-szolgáltatásokkal kapcsolatos általános információk az alábbi forrásokból érhetők el:
* Az [Azure Security csapatának blogja](/archive/blogs/azuresecurity/) – naprakész információk az Azure Security legújabb frissítéseiről
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – a Microsoft biztonsági rései, például az Azure-nal kapcsolatos problémák, jelentések vagy e-mailen keresztül secure@microsoft.com