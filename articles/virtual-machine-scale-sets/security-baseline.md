---
title: Az Azure biztonsági alapkonfigurációja Virtual Machine Scale Sets
description: A Virtual Machine Scale Sets alapkonfigurációja eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 60b1de12f031a55388960a6e3c4e7df00c43e3c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867821"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Az Azure biztonsági alapkonfigurációja Virtual Machine Scale Sets

Ez a biztonsági alapkonfiguráció az Azure biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Virtual Machine Scale Sets. Az Azure biztonsági teljesítményteszt javaslatokat tesz arra vonatkozóan, hogyan biztosíthatja  a felhőalapú megoldások biztonságát az Azure-ban.A tartalom az Azure biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, a felhőalapú megoldásokra vonatkozó Virtual Machine Scale Sets.

> [!NOTE]
> **Az** olyan Virtual Machine Scale Sets, amelyekért a felelősség a Microsoft felelős, ki vannak zárva. Ha meg Virtual Machine Scale Sets az Azure-biztonsági teljesítményteszt teljes leképezését, tekintse meg a teljes Virtual Machine Scale Sets alapkonfiguráció **[leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/virtual-machine-scale-sets-security-baseline-v1.1.xlsx)**

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Azure-beli virtuális gép létrehozásakor létre kell hoznia egy virtuális hálózatot, vagy egy meglévő virtuális hálózatot kell használnia, és konfigurálnia kell a virtuális gépet egy alhálózattal. Győződjön meg arról, hogy az összes telepített alhálózatra alkalmazva van egy hálózati biztonsági csoport, amely az alkalmazások megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel rendelkezik.

Másik lehetőségként, ha egy központosított tűzfalhoz egy adott eset áll rendelkezésre, Azure Firewall is használható a követelmények követelményeknek való megfeleléshez.

- [Azure-beli virtuálisgép-méretezési csoportok hálózatkezelése](virtual-machine-scale-sets-networking.md)

- [Új Virtual Network](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [A virtuális gép üzembe helyezése és Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** A Azure Security Center segítségével azonosíthatja és követhatja a hálózatvédelmi javaslatokat az Azure-beli virtuálisgép- (VM-) erőforrások biztonságának biztosítása érdekében. Engedélyezze az NSG forgalmi naplóit, és küldje el a naplókat egy tárfiókba a virtuális gépek szokatlan tevékenységekre vonatkozó forgalomnaplóztatása érdekében.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** Ha a virtuálisgép-méretezési csoportot webalkalmazások gazdagépeként használja, használjon hálózati biztonsági csoportot (NSG)a Virtuálisgép-méretezési csoport alhálózatán annak korlátozásához, hogy milyen hálózati forgalom, portok és protokollok kommunikálhatnak. Az NSG-k konfigurálásakor kövesse a legkevésbé emelt szintű hálózati megközelítést, hogy csak az alkalmazáshoz szükséges forgalmat engedélyezze.

A kritikus webalkalmazások Azure Web Application Firewall (WAF) is üzembe helyezheti a bejövő forgalom további vizsgálatának érdekében. Engedélyezze a WAF diagnosztikai beállítását, és a naplókat egy tárfiókba, eseményközpontba vagy Log Analytics-munkaterületre.

- [Azure-beli virtuálisgép-méretezési csoportok hálózatkezelése](virtual-machine-scale-sets-networking.md)

- [Alkalmazásátjáró létrehozása Web Application Firewall a Azure Portal](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** A DDoS-támadások elleni védelem érdekében engedélyezze az elosztott szolgáltatásmegtagadás (DDoS) standard szintű védelmét a virtuális hálózatokon. Az Azure Security Center intelligencia használatával figyelheti az ismert kártékony IP-címekkel való kommunikációt.  Konfigurálja Azure Firewall az összes Virtual Network szegmensben úgy, hogy a Fenyegetésintelligencia engedélyezve legyen, és "Riasztás és megtagadás" beállítással legyen konfigurálva a kártékony hálózati forgalomhoz.

A Azure Security Center hálózati hozzáférésével korlátozott időre korlátozhatja a Windows Virtual Machines IP-címeknek való kitettségét.  Emellett az adaptív Azure Security Center használatával olyan NSG-konfigurációkat javasolhat, amelyek a tényleges forgalom és a fenyegetésintelligencia alapján korlátozzák a portokat és a forrás IP-eket.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [A Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Az Azure Security Center fenyegetésintelligencia](../security-center/azure-defender.md)

- [Az Azure Security Center az adaptív hálózat-megtartóképességek](../security-center/security-center-adaptive-network-hardening.md)

- [Az Azure Security Center hálózati kapcsolati kapcsolatok Access Control](../security-center/security-center-just-in-time.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Az NSG-forgalom naplóit rögzítheti egy tárfiókban, így folyamatrekordokat hozhat létre az Azure Virtual Machines. Rendellenes tevékenységek vizsgálatakor engedélyezheti a Network Watcher, hogy a hálózati forgalom vizsgálható legyen szokatlan és váratlan tevékenység miatt.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** A Network Watcher által biztosított csomagrögzítések és egy nyílt forráskódú IDS eszköz kombinálásával hálózati behatolásészlelést végezhet a fenyegetések széles köréhez. Emellett szükség szerint üzembe helyezhet Azure Firewall a Virtual Network szegmensekre úgy, hogy a Fenyegetésintelligencia engedélyezve van, és "Riasztás és megtagadás" beállítással van konfigurálva a kártékony hálózati forgalomhoz.

- [Hálózati behatolásészlelés Network Watcher nyílt forráskódú eszközökkel](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [A virtuális gép Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása a Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Webalkalmazások forgalmának kezelése

**Útmutató:** Ha virtuálisgép-méretezési csoporttal webalkalmazásokat Azure Application Gateway webalkalmazások számára telepíthet, amelyeken engedélyezve van a HTTPS/SSL a megbízható tanúsítványokhoz. A Azure Application Gateway az alkalmazás webes forgalmát adott erőforrásokhoz irányíthatja azáltal, hogy figyelőket rendel a portokhoz, szabályokat hoz létre, és erőforrásokat ad hozzá egy háttérkészlethez, például a virtuálisgép-méretezési csoportokhoz stb.

- [A virtuális gép Application Gateway](../application-gateway/quick-create-portal.md)

- [A HTTPS használatára Application Gateway konfigurálása](../application-gateway/create-ssl-portal.md)

- [Application Gateway-re hivatkozó méretezési csoport létrehozása](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

- [A 7. rétegbeli terheléselosztás és az Azure webalkalmazás-átjárók használata](../application-gateway/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** A Virtual Network címkékkel meghatározhatja a hálózati biztonsági csoportok hálózati hozzáférés-vezérlését, vagy Azure Firewall Azure-beli virtuális gépekhez konfigurált hálózati hozzáférés-vezérlést. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha megadja a szolgáltatáscímke nevét (például ApiManagement) egy szabály megfelelő forrás- vagy célmezőben, engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásával.

- [A szolgáltatáscímkék használata és használata](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Standard biztonsági konfigurációk meghatározása és implementációja az Azure Virtual Machine Scale Sets a Azure Policy. Az Azure Blueprints használatával leegyszerűsítheti a nagy méretű Azure-beli virtuális gépek üzembe helyezését, mivel egyetlen tervdefinícióba csomagolásával egyetlen tervdefinícióba kell Azure Resource Manager környezeti összetevők, például Azure Resource Manager-sablonok, szerepkör-hozzárendelések és Azure Policy-hozzárendelések. Alkalmazhatja a tervet az előfizetések számára, és engedélyezheti az erőforrás-kezeléshez a terv verziószámozását.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [A virtuálisgép-méretezési csoport sablonjainak megismerése](virtual-machine-scale-sets-mvss-start.md) 

- [Azure Policy mintákat a hálózatépítéshez](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprints-terv létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Használhat címkéket a hálózati biztonsági csoportokhoz (NSG-khez) és a Windows rendszerű virtuális gépekhez konfigurált hálózati biztonsággal és forgalomfolyammal kapcsolatos egyéb erőforrásokhoz. Az egyes NSG-szabályoknál a "Leírás" mezőben adhatja meg az üzleti igényeket és/vagy időtartamot minden olyan szabályhoz, amely engedélyezi a hálózat be- és kiforgalmát.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Új Virtual Network](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnapló segítségével monitor követheti az Azure-beli virtuálisgép-méretezési csoporthoz kapcsolódó hálózati erőforrás-konfigurációk változásait. Hozzon létre riasztásokat a Azure Monitor, amelyek a kritikus hálózati beállítások vagy erőforrások módosításakor aktiválódnak.

A Azure Policy virtuálisgép-méretezési csoporttal kapcsolatos hálózati erőforrások konfigurációjának ellenőrzésével (és/vagy szervizlével) kapcsolatos ellenőrzéshez használhatja a virtuálisgép-méretezési csoportokat.

- [Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy mintákat a hálózatépítéshez](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 1.11](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-11.md)]

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizálási források használata

**Útmutató:** A Microsoft fenntartja az Azure-erőforrások időforrását, ön azonban kezelheti az erőforrások időszinkronizálási Virtual Machines.

- [Időszinkronizálás konfigurálása Azure Windows számítási erőforrásokhoz](../virtual-machines/windows/time-sync.md)

- [Időszinkronizálás konfigurálása Azure-beli Linux számítási erőforrásokhoz](../virtual-machines/linux/time-sync.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** A tevékenységnaplók segítségével naplózhatja a virtuálisgép-méretezési készlet erőforrásain végrehajtott műveleteket és műveleteket. A tevékenységnapló az erőforrások összes írási műveletét (PUT, POST, DELETE) tartalmazza, kivéve az olvasási műveleteket (GET). A tevékenységnaplók hibakeresésre használhatók hibaelhárításkor, vagy annak figyelése érdekében, hogy a szervezet felhasználói hogyan módosították az erőforrásokat.

Engedélyezheti és beveheti az Azure-tevékenységnaplókból vagy virtuálisgép-erőforrásokból előállított naplóadatokat a Azure Sentinel vagy egy külső SIEM számára a központi biztonsági naplókezeléshez.

A Azure Security Center az Azure-erőforrások biztonsági eseménynapló-monitorozásának Virtual Machines. A biztonsági eseménynapló által generált adatmennyiség miatt a rendszer alapértelmezés szerint nem tárolja az adatokat. 

Ha a szervezet meg szeretné őrizni a virtuális gép biztonsági eseménynapló-adatait, akkor az adatok egy Log Analytics-munkaterületen tárolhatók a virtuális gépen konfigurált kívánt adatgyűjtési Azure Security Center.

- [Platformnaplók és -metrikák gyűjtése Azure Monitor ](../azure-monitor/essentials/diagnostic-settings.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Első lépések a Azure Monitor külső SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

- [Adatgyűjtés az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Virtuális gépek figyelése az Azure-ban](../azure-monitor/vm/monitor-vm-azure.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** A tevékenységnaplók segítségével naplózhatja a virtuálisgép-méretezési készlet erőforrásain végrehajtott műveleteket és műveleteket. A tevékenységnapló az erőforrások összes írási műveletét (PUT, POST, DELETE) tartalmazza, kivéve az olvasási műveleteket (GET). A tevékenységnaplók hibakeresésre használhatók hibaelhárításkor, vagy annak figyelése érdekében, hogy a szervezet egy felhasználója hogyan módosította az erőforrásokat.

Engedélyezze a vendég operációs rendszer diagnosztikai adatainak gyűjtését a diagnosztikai bővítmény üzembe helyezésével a Virtual Machines (VM). A diagnosztikai bővítménnyel diagnosztikai adatokat, például alkalmazásnaplókat vagy teljesítményszámlálókat gyűjthet egy Azure-beli virtuális gépről.

Az Azure-beli virtuálisgép-méretezési készlet által támogatott alkalmazások és szolgáltatások fejlett láthatósága érdekében engedélyezheti a Azure Monitor for VMs és az Application Insights szolgáltatást is. A Application Insights figyelheti az alkalmazást, és telemetriai adatokat rögzíthet, például HTTP-kéréseket, kivételeket stb., így korrelálhatja a virtuális gépek és az alkalmazás közötti problémákat.

- [Platformnaplók és -metrikák gyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Virtuális gépek figyelése az Azure-ban](../azure-monitor/vm/monitor-vm-azure.md)

- [Az Application Insights áttekintése](../azure-monitor/app/app-insights-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése operációs rendszerekről

**Útmutató:** A Azure Security Center biztosít biztonsági eseménynapló-monitorozást az Azure Virtual Machines. A biztonsági eseménynapló által generált adatmennyiség miatt a rendszer alapértelmezés szerint nem tárolja az adatokat. 

Ha a szervezet meg szeretné őrizni a virtuális gép biztonsági eseménynapló-adatait, akkor az adatok egy Log Analytics-munkaterületen tárolhatók a virtuális gépen konfigurált kívánt adatgyűjtési Azure Security Center.

- [Adatgyűjtés az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Virtuális gépek figyelése az Azure-ban](../azure-monitor/vm/monitor-vm-azure.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-4.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági naplók tárolásának megőrzésének konfigurálása

**Útmutató:** Győződjön meg arról, hogy a virtuális gépek naplóinak tárolásához használt tárfiókok vagy Log Analytics-munkaterületek naplómegőrzési időszaka a szervezet megfelelőségi szabályozásának megfelelően van beállítva.

- [Virtuális gépek figyelése az Azure-ban](../azure-monitor/vm/monitor-vm-azure.md)

- [Log Analytics-munkaterület megőrzési időtartamának konfigurálása](../azure-monitor/logs/manage-cost-storage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Naplók elemzése és figyelése rendellenes viselkedések kereséséhez és az eredmények rendszeres áttekintéséhez. A Azure Monitor naplók áttekintéséhez és a naplóadatok lekérdezéséhez.

Másik lehetőségként engedélyezheti és felveheti az adatokat egy Azure Sentinel külső SIEM számára a naplók figyelése és áttekintése érdekében.

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [A Log Analytics-munkaterület](/azure/azure-monitor/logs/get-started-queries)

- [Egyéni lekérdezések végrehajtása a Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Azure Security Center Log Analytics-munkaterülettel konfigurált adatokat az Azure-beli virtuális hálózat biztonsági naplóiban és eseményeiben található rendellenes tevékenységek figyelése és Virtual Machines.  

Másik lehetőségként engedélyezheti és felveheti az adatokat a Azure Sentinel vagy egy külső SIEM számára a rendellenes tevékenységekre vonatkozó riasztások beállításához.

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése a Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a Log Analytics-naplóadatokról](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosította

**Útmutató:** A Microsoft Kártevőirtót használhatja a Azure Cloud Services és Virtual Machines és konfigurálhatja a Windows rendszerű virtuális gépeket úgy, hogy az eseményeket egy Azure Storage-fiókba naplózják. Konfigurálnia kell egy Log Analytics-munkaterületet, hogy az eseményeket a tárfiókból tudja behozni, és ahol szükséges, riasztásokat hozzon létre. Kövesse a következő Azure Security Center: "Compute &amp; Apps".  Linux rendszerű virtuális gépekhez egy külső eszközre lesz szüksége a kártevők elleni biztonsági rések észleléséhez. 

- [A Microsoft kártevőirtó szoftver Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

- [Vendégszintű monitorozás engedélyezése a Virtual Machines](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

- [Útmutató Linux-kiszolgálók az Azure Security Centerbe való felvételéhez](../security-center/quickstart-onboard-machines.md) 

- [Az alábbi hivatkozás a Microsoft által ajánlott biztonsági irányelveket tartalmazza, amelyek a kiválasztott biztonsági rést okozó szoftver kritériumainak listájaként szolgálhatnak](../virtual-machines/security-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-8.md)]

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-lekérdezésnaplózás engedélyezése

**Útmutató:** Harmadik féltől származó megoldás implementálja a Azure Marketplace DNS-naplózási megoldáshoz, a szervezet által szükségesnek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutató:** A Azure Security Center azure-beli virtuális gépek biztonsági eseménynapló-Virtual Machines biztosít. Security Center az összes támogatott Azure-beli virtuális gépre kiépíti a Microsoft Monitoring Agentet, illetve minden olyan újat, amely az automatikus kiépítés engedélyezése esetén jön létre, VAGY manuálisan is telepítheti az ügynököt.  Az ügynök engedélyezi a 4688-as folyamat-létrehozási eseményt és a CommandLine mezőt a 4688-as eseményen belül. A virtuális gépen létrehozott új folyamatokat az EventLog rögzíti, és Security Center észlelési szolgáltatásai figyelik.

Linux rendszerű virtuális gépek esetén manuálisan konfigurálhatja a konzolnaplózást csomópontonként, és syslogok használatával tárolhatja az adatokat.  Emellett a Azure Monitor Log Analytics-munkaterületén is áttekinthet naplókat, és lekérdezéseket hajthatja végre az Azure-beli virtuális gépek rendszernapló-adatain.

- [Adatgyűjtés az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [Egyéni lekérdezések végrehajtása a Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Rendszernapló-adatforrások az Azure Monitorban](../azure-monitor/agents/data-sources-syslog.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Bár Azure Active Directory (Azure AD) a felhasználói hozzáférés felügyeletének ajánlott módszere, előfordulhat, hogy az Azure Virtual Machines rendelkezik helyi fiókokkal. A helyi és tartományi fiókokat is át kell vizsgálni és kezelni kell, általában minimális memóriaigény mellett. Emellett használja ki az Azure Privileged Identity Management a virtuális gépek erőforrásainak eléréséhez használt rendszergazdai fiókokhoz.

- [Információk a helyi fiókokról](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

- [Információk a Privileged Identity Managerről](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** Az Azure-beli virtuálisgép-méretezési Azure Active Directory (Azure AD) nem használja az alapértelmezett jelszavakat. Az alapértelmezett jelszavakat felhasználó külső alkalmazásokért és Marketplace-szolgáltatásokért felelős ügyfél.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárások létrehozása a virtuális gépekhez hozzáféréssel rendelkező dedikált rendszergazdai fiókok használatával kapcsolatban. A Azure Security Center és hozzáférés-kezelés használatával figyelheti a rendszergazdai fiókok számát. Az Azure-beli virtuális gépek erőforrásainak eléréséhez használt rendszergazdai fiókokat az Azure Privileged Identity Management (PIM) is képes kezelni. Az Azure Privileged Identity Management számos lehetőséget kínál, például az igény szerint való jogosultságszint-emelést, a többtényezős hitelesítést a szerepkör feltételezése előtt, valamint a delegálást, hogy az engedélyek csak bizonyos időkeretek esetén érhetők el, és jóváhagyóra van szükség.

- [Az Azure Security Center és a hozzáférés](../security-center/security-center-identity-access.md)

- [Információk a Privileged Identity Managerről](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 3.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-3-3.md)]

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** Amikor csak lehetséges, használjon SSO-t az Azure Active Directory (Azure AD) szolgáltatással az önálló hitelesítő adatok szolgáltatásonkénti konfigurálása helyett. Használja Azure Security Center Identity and Access Management recommendations (Identitás- és hozzáférés-kezelés) javaslatokat.

- [Egyszeri bejelentkezés alkalmazásokba az Azure AD-ban](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center identitás- és hozzáférés-kezelésre vonatkozó javaslatokat.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató:** Emelt hozzáférési szintű munkaállomások (emelt szintű hozzáférésű munkaállomások) használata az Azure-erőforrásokba való bejelentkezéshez és konfiguráláshoz konfigurált többtényezős hitelesítéssel.

- [Tudnivalók a Privileged Access munkaállomásról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával naplókat és riasztásokat generál, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. Az Azure AD Kockázatészlelések használatával riasztásokat és jelentéseket lehet megtekinteni a kockázatos felhasználói viselkedésről. Az ügyfél igény szerint a Azure Security Center riasztásokat a Azure Monitor, és egyéni riasztásokat/értesítéseket konfigurálhat műveletcsoportok használatával.

- [A Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [A Azure Security Center észlelésének ismertetése (gyanús tevékenység)](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Műveletcsoportok konfigurálása egyéni riasztáshoz és értesítéshez](../azure-monitor/alerts/action-groups.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** Az Azure Active Directory (Azure AD) feltételes hozzáférési szabályzatai és nevestűs helyei segítségével csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításai számára engedélyezi a hozzáférést.

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt. Az Azure AD emellett a felhasználói hitelesítő adatokat is megsóz, hashekkel és biztonságosan tárolja.  A felügyelt identitások használatával bármilyen, az Azure AD-hitelesítést támogató szolgáltatásban hitelesíthet, beleértve a Key Vault is, anélkül, hogy hitelesítő adatokat ad meg a kódban. A virtuális gépen futó kód a felügyelt identitásával hozzáférési jogkivonatokat kérhet az Azure AD-hitelesítést támogató szolgáltatásokhoz.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

- [Az Azure-erőforrások felügyelt identitásának áttekintése](../active-directory/managed-identities-azure-resources/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Emellett az Azure AD identitás-hozzáférési felülvizsgálatok használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférését rendszeresen áttekintheti, hogy csak a megfelelő felhasználók férnek hozzá. Azure-beli virtuális gépek használata esetén át kell vizsgálnia a helyi biztonsági csoportokat és felhasználókat, hogy ne legyen olyan váratlan fiók, amely veszélyeztetné a rendszert.

- [Az Azure Identity Hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Diagnosztikai beállítások konfigurálása a Azure Active Directory (Azure AD) számára az auditnaplók és bejelentkezési naplók Log Analytics-munkaterületre való küldése céljából. Emellett a Azure Monitor naplók áttekintéséhez és az Azure-beli virtuális gépekről származó naplóadatok lekérdezéséhez.

- [A Log Analytics-munkaterület](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Virtuális gépek figyelése az Azure-ban](/azure/azure-monitor/vm/monitor-vm-azure)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Azure Active Directory (Azure AD) Kockázat- és Identity Protection-funkcióival konfigurálhatja az automatikus válaszokat a tárfiók erőforrásaival kapcsolatos gyanús műveletekre. A szervezet biztonsági válaszait Azure Sentinel automatikus válaszokat kell engedélyeznie.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Hozzáférés a Microsoft számára a releváns ügyféladatokhoz a támogatási forgatókönyvek során

**Útmutató:** Olyan támogatási forgatókönyvekben, ahol a Microsoftnak hozzá kell férni az ügyféladatokhoz (például egy támogatási kérés során), használja az Ügyfélszéf for Azure-beli virtuális gépekhez az ügyféladat-hozzáférési kérelmek áttekintéséhez és jóváhagyásához vagy elutasításához.

- [A Ügyfélszéf](../security/fundamentals/customer-lockbox-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon lehet követni a bizalmas adatokat tároló vagy feldolgozó Azure-beli virtuális gépeket.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Különálló előfizetéseket és/vagy felügyeleti csoportokat implementálja a fejlesztéshez, teszteléshez és éles környezethez. Az erőforrásokat virtuális hálózattal/alhálózattal kell elválasztani, megfelelően megcímkézni, és egy hálózati biztonsági csoporton (NSG-n) belül vagy egy Azure Firewall. A Virtual Machines adatok tárolásához vagy feldolgozásához szabályzatokat és eljárásokat alkalmazva kapcsolja ki őket, amikor nincs használatban.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [A Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Új Virtual Network](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [A Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztás vagy riasztás és megtagadás konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** Harmadik féltől származó megoldás megvalósítása a hálózati szegélyhálózatokon, amely figyeli a bizalmas információk jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztja az információbiztonsági szakembereket.

A Microsoft által felügyelt mögöttes platform esetén a Microsoft minden ügyféltartalmat bizalmasként kezel az ügyfelek adatvesztéssel és -kitettséggel szembeni védelmére. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** A Windowst futtató Virtual Machines -t futtató virtuális gépekre, illetve azok között áttért adatok többféleképpen titkosítva vannak a kapcsolat jellegétől függően, például amikor RDP- vagy SSH-munkamenetben csatlakoznak egy virtuális géphez.

A Microsoft a Transport Layer Security (TLS) protokollt használja az adatok védelmére a felhőszolgáltatások és az ügyfelek közötti utazás során.

- [Átvitel közbeni titkosítás virtuális gépeken](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosításához

**Útmutató:** Harmadik féltől származó aktív felderítési eszköz használatával azonosíthatja a szervezet technológiai rendszerei által tárolt, feldolgozott vagy továbbított összes bizalmas információt, beleértve a helyszínen vagy egy távoli szolgáltatónál található adatokat, és frissítheti a szervezet bizalmas információinak leltárát.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Erőforrásokhoz való hozzáférés vezérlése az Azure RBAC használatával 

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával elkülönítheti a csapaton belüli feladatokat, és csak olyan mennyiségű hozzáférést adhat a virtuális gép (VM) felhasználóinak, amelyekre a feladataik elvégzéséhez szükségük van. Ahelyett, hogy mindenki számára korlátlan engedélyeket ad a virtuális gépen, csak bizonyos műveleteket engedélyezhet. A virtuális gép hozzáférés-vezérlését konfigurálhatja a Azure Portal az Azure CLI vagy a Azure PowerShell.

- [Azure RBAC-vel](../role-based-access-control/overview.md)

- [Beépített Azure-szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató:** Harmadik féltől származó eszköz, például automatizált gazdagépalapú adatveszteség-megelőzési megoldás megvalósítása a hozzáférés-vezérlés kényszerítése az adatsértés kockázatának csökkentése érdekében.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Az Virtual Machines (VM) virtuális lemezei kiszolgálóoldali vagy Azure Disk Encryption (ADE) titkosítással vannak titkosítva. Azure Disk Encryption Linux DM-Crypt a felügyelt lemezek ügyfél által felügyelt kulcsokkal való titkosításához a vendég virtuális gépen. Az ügyfél által kezelt kulcsokkal való kiszolgálóoldali titkosítás azáltal javítja az ADE-t, hogy lehetővé teszi, hogy bármilyen operációsrendszer-típust és rendszerképet használjon a virtuális gépekhez a Storage szolgáltatásban tárolt adatok titkosításával.

- [Azure Disk Encryption a Virtual Machine Scale Sets](disk-encryption-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Azure Monitor Azure-tevékenységnaplóval használva riasztásokat hozhat létre a virtuálisgép-méretezési csoportokkal és a kapcsolódó erőforrásokkal kapcsolatos változásokról.  

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Storage Analytics-naplózás](../storage/common/storage-analytics-logging.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált biztonsági rések ellenőrzési eszközeinek futtatása

**Útmutató:** Kövesse a biztonsági rések felmérésének Azure Security Center azure-beli virtuális Virtual Machines.  Használja az Azure Security által ajánlott vagy külső megoldást a virtuális gépek sebezhetőségi felmérésének végrehajtásához.

- [Biztonsági rések felmérésére Azure Security Center javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-1.md)]

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának üzembe helyezése

**Útmutató:** Az operációs rendszer automatikus frissítésének engedélyezése a támogatott operációsrendszer-verziókhoz vagy az egyes operációs rendszerekben tárolt egyéni lemezképek Shared Image Gallery.

- [Virtuálisgép-méretezési készletek automatikus operációsrendszer-frissítései az Azure-ban](virtual-machine-scale-sets-automatic-upgrade.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 5.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-2.md)]

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Automatikus javításkezelési megoldás üzembe helyezése külső szoftvercímek számára

**Útmutató:** Az Azure Virtual Machine Scale Sets az operációs rendszer rendszerképének automatikus frissítését is használhatja. Használhatja az Azure Desired State Configuration (DSC) bővítményt a mögöttes virtuális gépekhez a Virtuálisgép-méretezési készletben. A DSC-vel úgy konfigurálhatja a virtuális gépeket, ahogy online állapotba állnak, hogy a kívánt szoftvert futtatják.

- [Az Virtual Machine Scale Sets használata a Azure DSC bővítvekkel](virtual-machine-scale-sets-dsc.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: A biztonsági rések biztonsági vizsgálatának összehasonlítása

**Útmutató:** Vizsgálati eredmények konzisztens időközönként történő exportálása és az eredmények összehasonlítása a biztonsági rések szervizlének ellenőrzéséhez. A biztonsági rések kezelésére vonatkozó, Azure Security Center javaslat használata esetén az ügyfél a kiválasztott megoldás portálján elfordulhat, hogy megtekintse az előzményvizsgálati adatokat.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** Használja a rendszer által biztosított alapértelmezett kockázatminősítéseket (Azure Security Center.

- [A Azure Security Center pontszámok](../security-center/secure-score-security-controls.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-5.md)]

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetések összes erőforrását (beleértve a virtuális gépeket is). Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes enumerálni az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása Olyan Azure-erőforrásokra, amelyek metaadatokat adva logikailag rendszerezik őket egy taxonómia szerint.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** A méretezési csoportok Virtual Machines és a kapcsolódó erőforrások rendszerezéséhez és nyomon követéséhez szükség szerint használjon címkézést, felügyeleti csoportokat és különálló előfizetéseket. Rendszeresen összeegyezteti a leltárat, és gondoskodik arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Új Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és leltározása

**Útmutató:** Leltár létrehozása a jóváhagyott Azure-erőforrásokról és a számítási erőforrásokhoz jóváhagyott szoftverekről a szervezeti igényeknek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Az Azure Policy használatával korlátozásokat korlátozhat az ügyfél-előfizetés(ök)ben az alábbi beépített szabályzatdefiníciók használatával létrehozott erőforrások típusára:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett a Azure Resource Graph erőforrások lekérdezéséhez/felderítéséhez is használhatja az előfizetés(ek)et. Ez segíthet a magas biztonságon alapuló környezetekben, például a Storage-fiókokkal rendelkezőkben.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató:** Azure Automation teljes körű vezérlést biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során.  Az Azure Virtual Machine Inventory használatával automatizálhatja az adatok gyűjtését a virtuális gépeken Virtual Machines. Megjegyzés: A szoftvernév, a verzió, a közzétevő és a frissítés ideje a Azure Portal. A telepítési dátumhoz és egyéb információkhoz való hozzáféréshez az ügyfélnek engedélyeznie kell a vendégszintű diagnosztikát, és be kell vinnie a Windows-eseménynaplókat egy Log Analytics-munkaterületre.

Az adaptív alkalmazásvezérlők jelenleg nem érhetők el a Virtual Machine Scale Sets.

- [Bevezetés az Azure Automationbe](../automation/automation-intro.md)

- [Az Azure-beli virtuális gépek leltárának engedélyezése](../automation/automation-tutorial-installed-software.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Nem jóváhagyott Azure-erőforrások és -szoftveralkalmazások eltávolítása

**Útmutató:** Azure Automation teljes körű vezérlést biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során.  A Change Tracking az összes telepített szoftver azonosítására Virtual Machines. Implementálja saját folyamatát, vagy használja a Azure Automation State Configuration a jogosulatlan szoftverek eltávolításához.

- [Bevezetés az Azure Automationbe](../automation/automation-intro.md)

- [A környezet változásainak nyomon követése a Change Tracking megoldással](../automation/change-tracking/overview.md)

- [Azure Automation State Configuration áttekintés](../automation/automation-dsc-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazások használata

**Útmutató:** Az adaptív alkalmazásvezérlők jelenleg nem érhetők el a Virtual Machine Scale Sets. Külső szoftver használata a használat szabályozása kizárólag jóváhagyott alkalmazásokhoz.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 6.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-8.md)]

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Az Azure Policy használatával korlátozásokat korlátozhat az ügyfél-előfizetés(ök)ben az alábbi beépített szabályzatdefiníciók használatával létrehozott erőforrások típusára:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 6.9](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-9.md)]

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: A jóváhagyott szoftvercímek leltárának fenntartása

**Útmutató:** Az adaptív alkalmazásvezérlők jelenleg nem érhetők el Virtual Machine Scale Sets. Harmadik féltől származó megoldás implementálja, ha ez nem felel meg a szervezet követelményeinek.

- [Az adaptív Azure Security Center használata](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 6.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-10.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure-beli feltételes hozzáféréssel korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "Hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: A felhasználók szkriptek számítási erőforrásokon belüli végrehajtásának korlátozása

**Útmutató:** A szkriptek típusától függően az operációs rendszerre vonatkozó konfigurációk vagy külső erőforrások használatával korlátozhatja a felhasználók szkriptek végrehajtására való képességét az Azure számítási erőforrásain belül.

- [PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: A magas kockázatú alkalmazások fizikai vagy logikai elkülönítése

**Útmutató:** Az Azure-környezetben üzembe helyezett magas kockázatú alkalmazások virtuális hálózattal, alhálózattal, előfizetésekkel, felügyeleti csoportokkal stb. elkülöníthetőek, és megfelelően védettek lehetnek egy Azure Firewall, Web Application Firewall (WAF) vagy hálózati biztonsági csoport (NSG) segítségével. 

- [Virtuális hálózatok és virtuális gépek az Azure-ban](/azure/virtual-machines/windows/network-overview)

- [Azure Firewall áttekintés](../firewall/overview.md)

- [Web Application Firewall áttekintés](../web-application-firewall/overview.md)

- [Hálózati biztonság – áttekintés](../virtual-network/network-security-groups-overview.md)

- [Az Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)

- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)

- [Útmutató az előfizetéssel kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** A Azure Policy vagy Azure Security Center az összes Azure-erőforrás biztonsági konfigurációjának fenntartásához. Emellett a Azure Resource Manager exportálhatja a sablont az JavaScript Object Notation-ban (JSON), amelyet át kell vizsgálni, hogy a konfigurációk megfelelnek-e a vállalat biztonsági követelményeinek, illetve meghaladják-e a követelményeket.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Információk a virtuálisgép-sablon letöltésével kapcsolatban](/previous-versions/azure/virtual-machines/windows/download-template)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató:** A Azure Security Center biztonsági konfigurációk biztonsági réseit javító javaslat Virtual Machines a biztonsági konfigurációk fenntartásához az összes számítási erőforráson.

- [A javaslatok Azure Security Center figyelése](../security-center/security-center-recommendations.md)

- [A javaslatok Azure Security Center szervize](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk fenntartása

**Útmutató:** Azure Resource Manager és Azure-szabályzatok használatával biztonságosan konfigurálhatja a méretezési csoportokkal társított Azure Virtual Machines erőforrásokat.  Azure Resource Manager sablonok JSON-alapú fájlok, amelyek a virtuális gép üzembe helyezéséhez használhatók Azure-erőforrásokkal együtt, és az egyéni sablonokat meg kell őrizni.  A karbantartást a Microsoft végzi el az alapsablonok alapján.  Az Azure Policy [deny] és a [deploy if not exist] ([Üzembe helyezés, ha nem létezik]) használatával kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon.

- [Információk a sablonsablonok Azure Resource Manager létrehozásáról](../virtual-machines/windows/ps-template.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [A Azure Policy hatásainak ismertetése](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató:** Az Azure Virtual Machines (VM) biztonságos konfigurációjának fenntartására számos lehetőség áll rendelkezésre:

1. Azure Resource Manager sablonok: Ezek olyan JSON-alapú fájlok, amelyek a virtuális gép üzembe helyezéséhez használatosak a Azure Portal-ból, és az egyéni sablonokat meg kell őrizni. A karbantartást a Microsoft végzi el az alapsablonok alapján.

2. Egyéni virtuális merevlemez (VHD): Bizonyos körülmények között szükség lehet egyéni VHD-fájlokra, például olyan összetett környezetek esetén, amelyek más módon nem kezelhetők.

3. Azure Automation State Configuration: Az alap operációs rendszer üzembe helyezése után ez a beállítás részletesebb szabályozására használható, és az Automation-keretrendszeren keresztül kényszeríthető ki.

A legtöbb esetben a Microsoft alap virtuálisgép-sablonjai és a Azure Automation Desired State Configuration segíthetnek a biztonsági követelmények követelményeknek való megfelelésben és fenntartásában.

- [Információk a virtuálisgép-sablon letöltésével kapcsolatban](/previous-versions/azure/virtual-machines/windows/download-template)

- [Információk ARM-sablonok létrehozásáról](../virtual-machines/windows/ps-template.md)

- [Egyéni virtuális gép virtuális merevlemezének feltöltése az Azure-ba](../virtual-machines/windows/upload-generalized-managed.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 7.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-4.md)]

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Az Azure DevOps használatával biztonságosan tárolhatja és kezelheti a kódot, például egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat, Desired State Configuration szkripteket stb.  Ahhoz, hogy hozzáférjen az Azure DevOpsban kezelt erőforrásokhoz, például a kódhoz, a buildhez és a munkakövetéshez, rendelkeznie kell az adott erőforrásokhoz szükséges engedélyekkel. A legtöbb engedély beépített biztonsági csoportokon keresztül adható meg az Engedélyek és hozzáférés dokumentumban leírtak szerint. Engedélyeket adhat vagy tagadhat meg adott felhasználóknak, beépített biztonsági csoportoknak vagy az Azure Active Directory-ben (Azure AD)-ben meghatározott csoportoknak, ha azok integrálva vannak az Azure DevOps-ral, vagy Active Directory, ha integrálva vannak a TFS-sel.

- [Kód tárolása az Azure DevOpsban](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Az Azure DevOps engedélyeinek és csoportjainak](/azure/devops/organizations/security/about-permissions)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató:** Ha egyéni rendszerképeket (például virtuális merevlemezt) használ, az Azure szerepköralapú hozzáférés-vezérléssel biztosíthatja, hogy csak a jogosult felhasználók férnek hozzá a lemezképhez.  

- [Az Azure-beli RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Az RBAC konfigurálása az Azure-ban](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** A Azure Policy a virtuális gépek rendszerkonfigurációinak riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és folyamatot a szabályzati kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Konfigurációkezelő eszközök központi telepítése operációs rendszerekhez

**Útmutató:** Azure Automation State Configuration egy konfigurációkezelő szolgáltatás bármely felhőbeli vagy helyszíni adatközpont Desired State Configuration (DSC) csomópontjaihoz. Gyors és egyszerű skálázhatóságot tesz lehetővé több ezer gép között egy központi, biztonságos helyről. Egyszerűen felvehet gépeket, deklaratív konfigurációkat rendelhet hozzájuk, és megtekintheti a jelentéseket, amelyek bemutatják, hogy az egyes gépek megfelelnek-e a megadott célállapotnak. 

- [Gépek Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása az Azure-erőforrásokhoz

**Útmutató:** A Azure Security Center azure-beli virtuális gépek alapkonfiguráció-vizsgálatának végrehajtásához.  Az automatikus konfigurálás további módszerei közé tartozik a Azure Automation State Configuration.

- [Javaslatok szervizlének Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Ismerkedés a Azure Automation State Configuration](../automation/automation-dsc-getting-started.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Automatikus konfigurációfigyelés megvalósítása operációs rendszerekhez

**Útmutató:** Azure Automation State Configuration egy konfigurációkezelő szolgáltatás bármely felhőbeli vagy helyszíni adatközpont Desired State Configuration (DSC) csomópontjaihoz. Gyors és egyszerű skálázhatóságot tesz lehetővé több ezer gép között egy központi, biztonságos helyről. Egyszerűen felvehet gépeket, deklaratív konfigurációkat rendelhet hozzájuk, és megtekintheti a jelentéseket, amelyek bemutatják, hogy az egyes gépek megfelelnek-e a megadott célállapotnak.

- [Gépek Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 7.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-10.md)]

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** A Managed Service Identity és a Azure Key Vault együtt használva leegyszerűsítheti és biztonságossá teheti a titkos adatok kezelését a felhőalkalmazások számára.

- [Az Azure Managed Identities integrálása](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Új Key Vault](../key-vault/general/quick-create-portal.md)

- [Hitelesítés a Key Vault](../key-vault/general/authentication.md)

- [Új hozzáférési szabályzat Key Vault hozzárendelése](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Felügyelt identitások használata az Azure-szolgáltatások automatikus felügyelt identitással való Azure Active Directory (Azure AD) használatával. A felügyelt identitások lehetővé teszik az Azure AD-hitelesítést támogató bármely szolgáltatásban történő hitelesítést, beleértve a Key Vault is, anélkül, hogy hitelesítő adatokat ad meg a kódban.

- [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódon belül. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevők elleni védelem.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Központilag felügyelt kártevőirtó szoftverek használata

**Útmutató:** Az Microsoft Antimalware Azure-beli Windows rendszerű virtuális gépekhez használható erőforrások folyamatos figyelése és megvédése.  Az Azure-beli Linux rendszerű virtuális gépeken egy külső eszközre lesz szüksége a kártevők elleni védelemhez. 

- [A Microsoft Antimalware és Cloud Services konfigurálása Virtual Machines](../security/fundamentals/antimalware.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 8.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-1.md)]

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: A kártevőirtó szoftverek és aláírások frissítésének biztosítása

**Útmutató:** Ha Windows rendszerű virtuális gépekhez van üzembe állítva, Microsoft Antimalware Azure-hoz való alkalmazás alapértelmezés szerint automatikusan telepíti a legújabb aláírási, platform- és motorfrissítéseket. Kövesse a Azure Security Center a "Compute Apps" (Számítási alkalmazások) javaslatokat, hogy minden végpont naprakész legyen a &amp; legújabb aláírásokkal. A Windows operációs rendszer további biztonsági megoldásokkal is védhető, így korlátozható a vírus- és kártevőalapú támadások kockázata a Microsoft Defender Advanced Threat Protection szolgáltatással, amely integrálható a Azure Security Center.

Az Azure-beli Linux rendszerű virtuális gépeken egy külső eszközre lesz szüksége a kártevők elleni védelemhez. 

- [A Microsoft Antimalware és Azure Cloud Services üzembe Virtual Machines](../security/fundamentals/antimalware.md)

- [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure) 

- [A Microsoft Antimalware és Cloud Services konfigurálása Virtual Machines](../virtual-machines/security-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 8.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-3.md)]

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** Pillanatkép készítése a példányhoz csatolt Azure-beli virtuálisgép-méretezési csoport példányról vagy felügyelt lemezről PowerShell vagy REST API-k használatával.  A biztonsági mentési Azure Automation rendszeres időközönként futtathatja a biztonsági mentési szkripteket.

- [Pillanatkép készítése virtuálisgép-méretezésikészlet-példányról és felügyelt lemezről](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance) 

- [Bevezetés a Azure Automation](../automation/automation-intro.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és ügyfél által kezelt kulcsok biztonsági mentése

**Útmutató:** Pillanatképek készítése az Azure-beli virtuális gépekről vagy az ezekhez a példányokhoz csatolt felügyelt lemezekről PowerShell vagy REST API-k használatával. Az ügyfelek által felügyelt kulcsok biztonsági Azure Key Vault.

Engedélyezze Azure Backup és célként az Azure Virtual Machines (VM), valamint a kívánt gyakoriságot és megőrzési időszakokat. Ebbe beletartozik a rendszerállapot teljes biztonsági mentése is. Ha Azure Disk Encryptiont használ, az Azure-beli virtuális gép biztonsági mentése automatikusan kezeli az ügyfél által kezelt kulcsok biztonsági mentését.

- [Titkosítást használó Azure-beli virtuális gépek biztonsági mentése](../backup/backup-azure-vms-encryption.md)

- [Az Azure-beli virtuális gépek biztonsági mentésének áttekintése](../backup/backup-azure-vms-introduction.md)

- [Pillanatkép készítése virtuálisgép-méretezésikészlet-példányról és felügyelt lemezről](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

- [Kulcstartókulcsok biztonsági mentése az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** A felügyelt lemezek adat-visszaállítási képességének rendszeres végrehajtása a Azure Backup. Szükség esetén tesztelje a tartalom elkülönített virtuális hálózatra vagy előfizetésbe való visszaállítását. Az ügyfél teszteli az ügyfél által kezelt biztonságimentés-kulcsok visszaállítását.

Azure Disk Encryption használata esetén a lemeztitkosítási kulcsokkal visszaállíthatja a virtuálisgép-méretezési készleteket. Lemeztitkosítás használata esetén a lemeztitkosítási kulcsokkal visszaállíthatja az Azure-beli virtuális gépet.

- [Biztonsági mentés titkosítást használó Azure-beli virtuális gépeken](../backup/backup-azure-vms-encryption.md)

- [Lemez visszaállítása és helyreállított virtuális gép létrehozása az Azure-ban](../backup/tutorial-restore-disk.md)

- [Kulcstartókulcsok visszaállítása az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Lemeztitkosítás engedélyezése az Azure Virtual Machine Scale Sets](disk-encryption-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** A felügyelt lemezek törlési védelmének engedélyezése zárolások használatával. Engedélyezze Soft-Delete és végleges törlés elleni védelmet a Key Vault a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében.  

- [Erőforrások zárolása a váratlan módosítások megelőzése érdekében](../azure-resource-manager/management/lock-resources.md)

- [Azure Key Vault törlés és végleges törlés elleni védelem áttekintése](../key-vault/general/soft-delete-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.  

- [Útmutató saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Az NIST számítógépes biztonsági incidenskezelési útmutatójának segítségével segítséget nyújt saját incidenskezelési tervének létrehozásában](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így priorizálhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy Security Center mennyire magabiztos a riasztást kiadó eredményben vagy a metrikában, valamint a megbízhatósági szinten, hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e. 

Emellett egyértelműen jelöljük meg az előfizetéseket (például: éles környezetben, nem éles környezetben) címkéket használ, és létrehoz egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozása esetén.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez az Azure-erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [NIST-kiadvány – Útmutató az IT-tervekhez és -képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidensek kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy illetéktelen vagy jogosulatlan fél fért hozzá adataihoz. Tekintse át az incidenseket a tény után, és győződjön meg arról, hogy a problémák megoldódnak.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A Azure Security Center riasztások és javaslatok exportálása a Folyamatos exportálás funkcióval az Azure-erőforrásokat érintő kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A Azure Security Center munkafolyamat-automatizálási szolgáltatásával automatikusan aktiválhat válaszokat a biztonsági riasztásokkal és javaslatokkal kapcsolatos "Logic Apps" üzenetben az Azure-erőforrások védelme érdekében. 

- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolási tesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft Engagement-szabályokat annak biztosításához, hogy a behatolási tesztek ne sértsék meg a Microsoft szabályzatát. A Microsoft red teaming és élő webhely-behatolási tesztek a Microsoft által felügyelt felhőinfra infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett stratégiájának és végrehajtásának használata.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
