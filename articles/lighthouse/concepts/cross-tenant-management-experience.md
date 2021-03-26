---
title: Bérlők közötti felügyeleti megoldások
description: Az Azure-beli delegált erőforrás-kezelés lehetővé teszi a több-bérlős felügyeleti élményt.
ms.date: 03/23/2021
ms.topic: conceptual
ms.openlocfilehash: a774eb08989e8fc45a117d83d81a5b510e8202b4
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559910"
---
# <a name="cross-tenant-management-experiences"></a>Bérlők közötti felügyeleti megoldások

Szolgáltatóként az [Azure Lighthouse](../overview.md) segítségével több ügyfél számára is kezelhet erőforrásokat a saját Azure Active Directory (Azure ad) bérlőn belül. A felügyelt bérlők számos feladatot és szolgáltatást végezhetnek el az [Azure-beli delegált erőforrás-kezelés](../concepts/azure-delegated-resource-management.md)használatával.

> [!TIP]
> Az Azure-beli delegált erőforrás-kezelés [olyan vállalaton belül is felhasználható, amely több Azure ad-Bérlővel rendelkezik a több](enterprise.md) -bérlős felügyelet egyszerűsítése érdekében.

## <a name="understanding-tenants-and-delegation"></a>A bérlők és a delegálás ismertetése

Az Azure AD-bérlő egy szervezet képviselete. Ez az Azure AD dedikált példánya, amelyet a szervezet akkor kap, amikor kapcsolatot hoz létre a Microsofttal az Azure-ra, Microsoft 365ra vagy más szolgáltatásokra való feliratkozással. Minden Azure AD-bérlő különálló, és elkülönül a többi Azure AD-bérlőtől, és saját bérlői AZONOSÍTÓval (GUID) rendelkezik. További információ: [Mi az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Az ügyfelekhez tartozó Azure-erőforrások kezeléséhez általában a szolgáltatóknak az ügyfél bérlője által hozzárendelt fiókkal kell bejelentkezniük a Azure Portalba, és az ügyfél bérlője számára meg kell adni a rendszergazdának, hogy a szolgáltatóhoz tartozó felhasználói fiókokat hozzanak létre és kezeljenek.

Az Azure Lighthouse használatával a bevezetési folyamat meghatározza a szolgáltató bérlője azon felhasználóit, akik jogosultak lesznek a delegált előfizetéseken és erőforráscsoportoknál az ügyfél bérlője számára. Ezek a felhasználók ezután a saját hitelesítő adataikkal jelentkezhetnek be a Azure Portalba. A Azure Portalon belül az összes olyan ügyfél erőforrásait kezelhetik, amelyhez hozzáféréssel rendelkeznek. Ezt úgy teheti meg, hogy felkeresi a [saját ügyfelek](../how-to/view-manage-customers.md) lapját a Azure Portalon, vagy közvetlenül az ügyfél előfizetésének kontextusában, akár a Azure Portal, akár API-n keresztül.

Az Azure Lighthouse nagyobb rugalmasságot biztosít a több ügyfél erőforrásainak kezeléséhez anélkül, hogy a különböző bérlők különböző fiókjaiba kellene bejelentkezniük. Előfordulhat például, hogy a szolgáltatónak két ügyfele van, akik eltérő felelősséggel és hozzáférési szinttel rendelkeznek. Az Azure Lighthouse használatával a jogosult felhasználók bejelentkezhetnek a szolgáltató bérlőbe az erőforrások eléréséhez.

![Az egyik szolgáltatói bérlőn keresztül felügyelt ügyfelek erőforrásait bemutató ábra.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API-k és felügyeleti eszközök támogatása

A delegált erőforrásokon közvetlenül a portálon, vagy API-k és felügyeleti eszközök (például az Azure CLI és a Azure PowerShell) használatával is elvégezheti a felügyeleti feladatokat. A rendszer minden meglévő API-t felhasználhat a delegált erőforrások használata esetén, ha a funkció támogatott a több-bérlős felügyelet esetében, és a felhasználó rendelkezik a megfelelő engedélyekkel.

A Azure PowerShell [Get-AzSubscription parancsmag](/powershell/module/Az.Accounts/Get-AzSubscription) alapértelmezés szerint a következőt fogja megjeleníteni a `TenantId` bérlő kezeléséhez. `HomeTenantId` `ManagedByTenantIds` Minden előfizetéshez használhatja a és attribútumokat, így meghatározhatja, hogy egy visszaadott előfizetés felügyelt bérlőhöz vagy a felügyeleti bérlőhöz tartozik-e.

Ehhez hasonlóan az Azure CLI-parancsok, például az [az Account List](/cli/azure/account#az-account-list) a és az attribútumok láthatók `homeTenantId` `managedByTenants` . Ha nem látja ezeket az értékeket az Azure CLI használatakor, a következő lépésekkel próbálja meg törölni a gyorsítótárat `az account clear` `az login --identity` .

Az Azure REST API az [előfizetések – Get](/rest/api/resources/subscriptions/get) és [Subscriptions-List](/rest/api/resources/subscriptions/list) parancs tartalmazza `ManagedByTenant` .

> [!NOTE]
> Az Azure Lighthouse-hoz kapcsolódó bérlői információk mellett az API-k által megjelenített bérlők a Azure Databricks és az Azure által felügyelt alkalmazások partner bérlőit is tükrözheti.

Olyan API-kat is biztosítunk, amelyek az Azure Lighthouse-feladatok végrehajtására vonatkoznak. További információért lásd a **hivatkozási** szakaszt.

## <a name="enhanced-services-and-scenarios"></a>Továbbfejlesztett szolgáltatások és forgatókönyvek

A legtöbb feladat és szolgáltatás elvégezhető a delegált erőforrásokon, különböző felügyelt bérlőkön. Az alábbiakban néhány olyan főbb forgatókönyvet talál, ahol a több-bérlős felügyelet különösen hatékony lehet.

[Azure-ív](../../azure-arc/index.yml):

- Hibrid kiszolgálók kezelése a skálán – [Azure arc-kompatibilis kiszolgálók](../../azure-arc/servers/overview.md):
  - Az Azure-on kívüli delegált előfizetésekhez és/vagy erőforráscsoportokhoz [csatlakoztatott Windows Server-vagy Linux-alapú gépek felügyelete](../../azure-arc/servers/onboard-portal.md)
  - Csatlakoztatott gépek kezelése Azure-konstrukciók használatával, például Azure Policy és címkézés
  - Győződjön meg arról, hogy ugyanazokat a szabályzatokat alkalmazza az ügyfelek hibrid környezetei között
  - Azure Security Center használata az ügyfelek hibrid környezetei közötti megfelelés figyeléséhez
- Hibrid Kubernetes-fürtök kezelése a skálán – [Azure arc-kompatibilis Kubernetes (előzetes verzió)](../../azure-arc/kubernetes/overview.md):
  - Az Azure-ban delegált előfizetésekhez és/vagy erőforráscsoportokhöz [kapcsolódó Kubernetes-fürtök kezelése](../../azure-arc/kubernetes/quickstart-connect-cluster.md)
  - [GitOps használata](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) csatlakoztatott fürtökhöz
  - Házirendek betartatása a csatlakoztatott fürtök között

[Azure Automation](../../automation/index.yml):

- Az Automation-fiókok használata a delegált erőforrások eléréséhez és használatához

[Azure Backup](../../backup/index.yml):

- A helyszíni számítási feladatokból [, Azure-beli virtuális gépekből, Azure-fájlmegosztásból és egyebekből származó](../..//backup/backup-overview.md#what-can-i-back-up) ügyféladatok biztonsági mentése és visszaállítása
- Az összes delegált ügyfél-erőforrásra vonatkozó információk megtekintése a [Backup Centerben](../../backup/backup-center-overview.md)
- A [Backup Explorer](../../backup/monitor-azure-backup-with-backup-explorer.md) segítségével megtekintheti a biztonsági másolati elemek működési információit (beleértve a biztonsági mentéshez még nem konfigurált Azure-erőforrásokat), valamint a delegált előfizetések figyelési információit (feladatok és riasztások). A Backup Explorer jelenleg csak az Azure-beli virtuálisgép-szolgáltatásokhoz érhető el.
- [Készítsen biztonsági mentési jelentéseket](../../backup/configure-reports.md) a delegált előfizetések között a korábbi trendek nyomon követéséhez, a biztonsági mentési tárolók felhasználásának elemzéséhez, valamint a biztonsági másolatok és visszaállítások ellenőrzéséhez

[Azure-tervezetek](../../governance/blueprints/index.yml):

- Az Azure tervrajzai segítségével összehangolhatja az erőforrás-sablonok és egyéb összetevők üzembe helyezését (az ügyfél-előfizetés előkészítéséhez [További hozzáférés](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) szükséges)

[Azure Cost Management + számlázás](../../cost-management-billing/index.yml):

- A bérlők felügyelete alatt a CSP-partnerek megtekinthetik, kezelhetik és elemezhetik az előfizetéssel kapcsolatos használati költségeket (nem tartalmazza a vásárlásokat) az Azure-csomag alatti ügyfelek számára. A költségek a kiskereskedelmi díjak és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) alapján érhetők el, amelyhez a partner az ügyfél előfizetéséhez tartozik.

[Azure Key Vault](../../key-vault/general/index.yml):

- Kulcstartók létrehozása az ügyfelek bérlői számára
- Felügyelt identitás használata kulcstartók létrehozásához az ügyfelek bérlői számára

[Azure Kubernetes szolgáltatás (ak)](../../aks/index.yml):

- Üzemeltetett Kubernetes-környezetek kezelése, tárolón belüli alkalmazások üzembe helyezése és kezelése az ügyfelek bérlői között
- Fürtök üzembe helyezése és kezelése az ügyfelek bérlői számára
-   Azure Monitor használata tárolók számára a teljesítmény figyeléséhez az ügyfél-bérlők között

[Azure Migrate](../../migrate/index.yml):

- Áttelepítési projektek létrehozása az ügyfél-bérlőben és a virtuális gépek áttelepítése

[Azure monitor](../../azure-monitor/index.yml):

- A delegált előfizetésekre vonatkozó riasztások megtekintése, valamint a riasztások megtekintésének és frissítésének lehetősége az összes előfizetésben
- A delegált előfizetések tevékenységi naplójának részleteinek megtekintése
- [Log Analytics](../../azure-monitor/logs/service-providers.md): a távoli munkaterületekről származó adatok lekérdezése több bérlőn (vegye figyelembe, hogy az ügyfél bérlői munkaterületeiről származó adatokhoz való hozzáféréshez használt Automation-fiókokat ugyanabban a bérlőben kell létrehozni)
- [Műveletnapló-riasztások létrehozása, megtekintése és kezelése](../../azure-monitor/alerts/alerts-activity-log.md) az ügyfelek bérlői számára
- Hozzon létre riasztásokat az olyan ügyfél-bérlők számára, amelyek automatizálják az automatizálást, például Azure Automation runbookok vagy Azure Functionst a bérlő kezelése webhookokon keresztül
- [Diagnosztikai beállítások](../..//azure-monitor/essentials/diagnostic-settings.md) létrehozása az ügyfelek bérlői számára az erőforrás-naplók elküldéséhez a munkaterületek kezeléséhez a bérlőben
- SAP-alapú számítási [feladatokhoz az SAP-megoldások metrikáinak figyelése összesített nézettel az ügyfél-bérlők között](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Azure hálózatkezelés](../../networking/networking-overview.md):

- [Azure Virtual Network](../../virtual-network/index.yml) -és virtuális hálózati adapterek (vnic-EK) üzembe helyezése és kezelése felügyelt bérlők keretében
- [Azure Firewall](../../firewall/overview.md) üzembe helyezése és konfigurálása az ügyfelek Virtual Network erőforrásainak védeleméhez
- A kapcsolódási szolgáltatások, például az [Azure Virtual WAN](../../virtual-wan/virtual-wan-about.md), a [ExpressRoute](../../expressroute/expressroute-introduction.md)és a [VPN-átjárók](../../vpn-gateway/vpn-gateway-about-vpngateways.md) kezelése
- Az Azure- [beli hálózati MSP-program](../../networking/networking-partners-msp.md) főbb forgatókönyvei az Azure Lighthouse használatával támogatottak

[Azure Policy](../../governance/policy/index.yml):

- Szabályzat-definíciók létrehozása és szerkesztése a delegált előfizetéseken belül
- Házirend-definíciók és szabályzat-hozzárendelések telepítése több bérlő között
- Ügyfél által definiált szabályzat-definíciók kiosztása delegált előfizetéseken belül
- Az ügyfelek a szolgáltató által készített szabályzatokat a saját maguk által létrehozott szabályzatok mellett látják
- Javíthatja [a felügyelt bérlőn belüli deployIfNotExists, vagy módosíthatja a hozzárendeléseket](../how-to/deploy-policy-remediation.md)
- Vegye figyelembe, hogy az ügyfél bérlői nem megfelelő erőforrásainak megfelelőségi adatainak megtekintése jelenleg nem támogatott

[Azure-erőforrás gráf](../../governance/resource-graph/index.yml):

- A most már tartalmazza a bérlő AZONOSÍTÓját a visszaadott lekérdezés eredményei között, így meghatározhatja, hogy egy előfizetés felügyelt bérlőhöz tartozik-e

[Azure Security Center](../../security-center/index.yml):

- Több-bérlős láthatóság
  - A biztonsági szabályzatoknak való megfelelőség monitorozása és a biztonsági lefedettség biztosítása az összes bérlő erőforrásai között
  - Folyamatos szabályozási megfelelőség monitorozása több bérlőn egyetlen nézetben
  - Gyakorlati biztonsági javaslatok monitorozása, osztályozása és rangsorolása biztonságos pontszámok számításával
- Több-bérlős biztonsági testhelyzet kezelése
  - Biztonsági szabályzatok kezelése
  - Tegyen lépéseket olyan erőforrásokra, amelyek nem felelnek meg a végrehajtható biztonsági javaslatoknak
  - Biztonsággal kapcsolatos adatok gyűjtése és tárolása
- Több-bérlős fenyegetések észlelése és védelme
  - Fenyegetések észlelése a bérlők erőforrásai között
  - Komplex veszélyforrások elleni védelem szabályozása, például igény szerinti (JIT) virtuálisgép-hozzáférés alkalmazása
  - Hálózati biztonsági csoport konfigurációjának megerősítése adaptív hálózati megerősítéssel
  - Győződjön meg arról, hogy a kiszolgálók csak azokat az alkalmazásokat és folyamatokat futtatják, amelyeknek adaptív alkalmazás-vezérlőkkel kell rendelkezniük
  - Fontos fájlok és beállításjegyzék-bejegyzések változásainak figyelése a fájlok integritásának figyelésével (FIM)
- Vegye figyelembe, hogy a teljes előfizetést delegálni kell a bérlői felügyelethez; A Azure Security Center-forgatókönyvek nem támogatottak a delegált erőforráscsoportok esetében

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Azure Sentinel-erőforrások kezelése [az ügyfelek bérlői számára](../../sentinel/multiple-tenants-service-providers.md)
- [Támadások követése és a biztonsági riasztások megtekintése több bérlőn](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- Több Azure Sentinel-munkaterületre kiterjedő [incidensek megtekintése](../../sentinel/multiple-workspace-view.md) a bérlők között

[Azure Service Health](../../service-health/index.yml):

- Az ügyfelek erőforrásainak állapotának figyelése Azure Resource Health
- Az ügyfelek által használt Azure-szolgáltatások állapotának nyomon követése

[Azure site Recovery](../../site-recovery/index.yml):

- Az Azure-beli virtuális gépek vész-helyreállítási lehetőségeinek kezelése az ügyfelek bérlői számára (vegye figyelembe, hogy a virtuálisgép- `RunAs` bővítmények másolására nem használhatók fiókok)

[Azure Virtual Machines](../../virtual-machines/index.yml):

- Virtuálisgép-bővítmények használata az üzembe helyezés utáni konfigurációs és automatizálási feladatok biztosításához az Azure-beli virtuális gépeken
- Rendszerindítási diagnosztika használata Azure-beli virtuális gépek hibakereséséhez
- Virtuális gépek elérése soros konzollal
- A virtuális gépeket a szabályzattal [felügyelt identitás](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)használatával integrálhatja a jelszavakhoz, a titkokhoz vagy a titkosítási kulcsokhoz Azure Key Vaultokkal, így biztosíthatja, hogy a titkokat a felügyelt bérlők egyik Key Vault tárolja.
- Vegye figyelembe, hogy a virtuális gépekhez nem használhat Azure Active Directory távoli bejelentkezéshez

Támogatási kérelmek:

- A [ **Súgó és támogatás** támogatási kéréseinek megnyitása](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) a Azure Portal a delegált erőforrások esetében (a delegált hatókörhöz elérhető támogatási csomag kiválasztása)
- Az Azure [kvóta API](/rest/api/reserved-vm-instances/quotaapi) használatával megtekintheti és kezelheti az Azure-szolgáltatási kvótákat a delegált ügyfelek erőforrásaihoz

## <a name="current-limitations"></a>Aktuális korlátozások

Az összes forgatókönyv esetén vegye figyelembe a következő korlátozásokat:

- Az Azure Resource Manager által kezelt kérelmeket az Azure Lighthouse használatával lehet elvégezni. A kérelmekhez tartozó műveleti URI-k a következővel kezdődnek: `https://management.azure.com` . Azonban az erőforrástípus egy példánya által kezelt kérelmek (Key Vault például a Secrets Access vagy a Storage adatelérése) nem támogatottak az Azure Lighthouse-ban. Ezeknek a kérelmeknek a műveleti URI-k jellemzően a példány egyedi címeivel kezdődnek, például: `https://myaccount.blob.core.windows.net` vagy `https://mykeyvault.vault.azure.net/` . Az utóbbi általában az adatműveletek, nem pedig a felügyeleti műveletek.
- A szerepkör-hozzárendeléseknek az [Azure beépített szerepköreit](../../role-based-access-control/built-in-roles.md)kell használniuk. Az Azure-beli delegált erőforrás-kezelés jelenleg minden beépített szerepkört támogat, kivéve a tulajdonost vagy az engedéllyel rendelkező beépített szerepköröket [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) . A felhasználói hozzáférés rendszergazdai szerepköre csak korlátozott használat esetén támogatott a [szerepkörök hozzárendeléséhez a felügyelt identitásokhoz](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Az egyéni szerepkörök és a [klasszikus előfizetés-rendszergazdai szerepkörök](../../role-based-access-control/classic-administrators.md) nem támogatottak.
- A Azure Databrickst használó előfizetések előállítása közben a bérlők kezelése szolgáltatásban lévő felhasználók jelenleg nem indíthatnak Azure Databricks-munkaterületeket a delegált előfizetéseken.
- Az erőforrás-zárolással rendelkező előfizetések és erőforráscsoportok bevezetése közben ezek a zárolások nem akadályozzák meg a felhasználók által végzett műveletek végrehajtását a bérlők felügyeletében. A rendszer által felügyelt erőforrások, például az Azure által felügyelt alkalmazások vagy az Azure-tervrajzok (rendszer által hozzárendelt megtagadási hozzárendelések) által létrehozott [hozzárendelések megtagadása](../../role-based-access-control/deny-assignments.md) , hogy a bérlők ne tudják eljárni az adott erőforráson. Ugyanakkor az ügyfél bérlője jelenleg nem hozhat létre saját megtagadási hozzárendeléseket (felhasználó által hozzárendelt megtagadási hozzárendeléseket).
- Az előfizetések delegálása egy [nemzeti felhőben](../../active-directory/develop/authentication-national-cloud.md) és az Azure nyilvános felhőben, vagy két külön nemzeti felhők között nem támogatott.

## <a name="next-steps"></a>Következő lépések

- Az ügyfeleket az Azure világítótoronyba [Azure Resource Manager sablonok használatával](../how-to/onboard-customer.md) vagy [egy magán-vagy nyilvános felügyelt szolgáltatás Azure Marketplace-re való közzétételével](../how-to/publish-managed-services-offers.md)teheti közzé.
- [Megtekintheti és kezelheti az ügyfeleket](../how-to/view-manage-customers.md) a Azure Portalban lévő **ügyfelekkel** .