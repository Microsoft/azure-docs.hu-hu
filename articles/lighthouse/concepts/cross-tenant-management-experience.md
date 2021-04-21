---
title: Bérlők közötti felügyeleti megoldások
description: Az Azure-beli delegált erőforrás-kezelés lehetővé teszi a bérlők közötti felügyeleti élményt.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 027d1d5e81d5a652a7e2d5441c40440c661f730f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778624"
---
# <a name="cross-tenant-management-experiences"></a>Bérlők közötti felügyeleti megoldások

Szolgáltatóként a Azure Lighthouse kezelheti [](../overview.md) több ügyfél erőforrásait a saját Azure Active Directory (Azure AD)-bérlőn belül. Az Azure-beli delegált erőforrás-kezelés használatával számos feladat és szolgáltatás elvégezhető a felügyelt [bérlők között.](../concepts/azure-delegated-resource-management.md)

> [!TIP]
> Az Azure-beli delegált erőforrás-kezelés olyan vállalaton belül is használható, amely több [Azure AD-bérlővel](enterprise.md) rendelkezik a több-bérlős adminisztráció egyszerűsítése érdekében.

## <a name="understanding-tenants-and-delegation"></a>A bérlők és a delegálás ismertetése

Az Azure AD-bérlő egy szervezetet képvisel. Ez az Azure AD egy dedikált példánya, amelyet a szervezetek megkapnak, amikor kapcsolatot hoznak létre a Microsofttal az Azure-ba, Microsoft 365 vagy más szolgáltatásokra való regisztrációval. Minden Azure AD-bérlő különálló, elkülönül a többi Azure AD-bérlőtől, és saját bérlőazonosítóval (GUID) rendelkezik. További információ: [Mi a Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Az ügyfelek Azure-erőforrásainak kezeléséhez a szolgáltatóknak általában az adott ügyfél bérlője által hozzárendelt fiókkal kell bejelentkezniük az Azure Portal-be, így az ügyfél bérlői rendszergazdájának kell létrehoznia és kezelnie a szolgáltató felhasználói fiókjait.

A Azure Lighthouse az előjegyzési folyamat határozza meg a szolgáltató bérlőjébe azokat a felhasználókat, akik dolgozhatnak az ügyfél bérlőjéhez delegált előfizetésekkel és erőforráscsoportokkal. Ezek a felhasználók ezután bejelentkeznek a Azure Portal a saját hitelesítő adataik használatával. A Azure Portal kezelhetik az összes olyan ügyfélhez tartozó erőforrásokat, akikhez hozzáféréssel rendelkezik. Ehhez látogasson el a [](../how-to/view-manage-customers.md) Azure Portal oldalára, vagy közvetlenül az ügyfél előfizetésének kontextusában, a Azure Portal api-kon keresztül.

Azure Lighthouse nagyobb rugalmasságot biztosít több ügyfél erőforrásainak kezeléséhez anélkül, hogy különböző bérlők különböző fiókjaiba jelentkezik be. Egy szolgáltatónak például két különböző feladatkörű és hozzáférési szintű ügyfele lehet. A Azure Lighthouse jogosult felhasználók bejelentkeznek a szolgáltató bérlőbe ezen erőforrások eléréséhez.

![Az egyetlen szolgáltató bérlőn keresztül felügyelt ügyfélerőforrásokat bemutató ábra.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API-k és felügyeleti eszközök támogatása

A delegált erőforrásokon közvetlenül a portálon, vagy API-k és felügyeleti eszközök (például az Azure CLI és a Azure PowerShell) használatával végezhet felügyeleti feladatokat. A delegált erőforrásokkal való munka során minden meglévő API használható, ha a funkció támogatott a bérlők közötti felügyelethez, és a felhasználó rendelkezik a megfelelő engedélyekkel.

A Azure PowerShell [Get-AzSubscription parancsmag](/powershell/module/Az.Accounts/Get-AzSubscription) alapértelmezés szerint a kezelő bérlőhöz a értéket `TenantId` fogja mutatni. Az egyes előfizetések és attribútumai segítségével megállapíthatja, hogy a visszaadott előfizetés egy felügyelt bérlőhöz vagy a felügyelt `HomeTenantId` `ManagedByTenantIds` bérlőhöz tartozik-e.

Hasonlóképpen az Azure CLI olyan parancsai, mint az [az account list,](/cli/azure/account#az_account_list) a és az `homeTenantId` `managedByTenants` attribútumot mutatják. Ha az Azure CLI használata közben nem látja ezeket az értékeket, próbálja meg törölni a gyorsítótárat a `az account clear` futtatásával, majd a `az login --identity` következővel: .

Az Azure REST API előfizetések [– Get](/rest/api/resources/subscriptions/get) és [Subscriptions – List](/rest/api/resources/subscriptions/list) parancsok közé tartoznak a `ManagedByTenant` következők: .

> [!NOTE]
> Az API-k által Azure Lighthouse bérlőkre vonatkozó információk mellett a bérlők a partnerbérlőket is tükrözhetnek a Azure Databricks Azure által felügyelt alkalmazások esetében.

Emellett olyan API-kat is biztosítunk, amelyek csak az adott Azure Lighthouse elvégzésére vonatkoznak. További információért tekintse meg a **Referencia szakaszt.**

## <a name="enhanced-services-and-scenarios"></a>Továbbfejlesztett szolgáltatások és forgatókönyvek

A legtöbb feladat és szolgáltatás elvégezhető a delegált erőforrásokon, különböző felügyelt bérlőkön. Az alábbiakban néhány olyan kulcsfontosságú forgatókönyvet olvashat, amelyekben a bérlők közötti felügyelet különösen hatékony lehet.

[Azure Arc:](../../azure-arc/index.yml)

- Hibrid kiszolgálók nagy léptékű kezelése – [Azure Arc kiszolgálók:](../../azure-arc/servers/overview.md)
  - [Az Azure-on kívüli,](../../azure-arc/servers/onboard-portal.md) delegált előfizetéshez és/vagy erőforráscsoportokhoz kapcsolódó Windows Server- vagy Linux-gépek kezelése az Azure-ban
  - Csatlakoztatott gépek kezelése Azure-szerkezetekkel, például Azure Policy címkézéssel
  - Győződjön meg arról, hogy az ügyfelek hibrid környezetei ugyanazt a szabályzatkészletet alkalmazzák
  - A Azure Security Center a megfelelőség figyelése az ügyfelek hibrid környezetében
- Hibrid Kubernetes-fürtök nagy léptékű kezelése – Azure Arc [Kubernetes (előzetes verzió) :](../../azure-arc/kubernetes/overview.md)
  - [Delegált](../../azure-arc/kubernetes/quickstart-connect-cluster.md) előfizetéshez és/vagy erőforráscsoportokhoz csatlakoztatott Kubernetes-fürtök kezelése az Azure-ban
  - [A GitOps használata](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) csatlakoztatott fürtökhöz
  - Szabályzatok érvényesítése csatlakoztatott fürtökön

[Azure Automation:](../../automation/index.yml)

- Automation-fiókok használata a delegált erőforrások eléréséhez és a velük való munkához

[Azure Backup:](../../backup/index.yml)

- Ügyféladatok biztonsági mentése és visszaállítása helyszíni számítási [feladatokból, Azure-beli virtuális gépekről, Azure-fájlmegosztásokból és egyéb szolgáltatásokból](../..//backup/backup-overview.md#what-can-i-back-up)
- Az összes delegált ügyfélerőforrás adatainak megtekintése a [Backup Centerben](../../backup/backup-center-overview.md)
- A [Backup-kezelő](../../backup/monitor-azure-backup-with-backup-explorer.md) segítségével megtekintheti a biztonsági mentési elemek működési adatait (beleértve a még nem konfigurált Azure-erőforrásokat) és a delegált előfizetések figyelési információit (feladatokat és riasztásokat). A Backup-kezelő jelenleg csak az Azure-beli virtuális gépek adataihoz érhető el.
- A [Backup-jelentések](../../backup/configure-reports.md) előfizetések között az előzménytrendek nyomon követéséhez, a biztonsági másolatok tárhelyhasználatának elemzéséhez, valamint a biztonsági mentések és visszaállítások naplózásához.

[Azure Blueprints:](../../governance/blueprints/index.yml)

- A Azure Blueprints erőforrássablonok és egyéb összetevők üzembe helyezésének [](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) vezénylése (további hozzáférés szükséges az ügyfél-előfizetés előkészítéséhez)

[Azure Cost Management + Billing:](../../cost-management-billing/index.yml)

- A kezelő bérlőben a CSP-partnerek megtekinthetik, kezelhetik és elemezhetik (a vásárlásokat nem beleértve) az Azure-csomaghoz kapcsolódó, adók előtti költségeket. A költségek a kiskereskedelmi árakon és az Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) hozzáférésén alapulnak, amely a partner számára az ügyfél előfizetéséhez szükséges. Jelenleg az Egyes ügyfél-előfizetések fogyasztási költségeit megtekintheti kiskereskedelmi díjszabás szerint az Azure RBAC-hozzáférés alapján.

[Azure Key Vault:](../../key-vault/general/index.yml)

- Kulcstartók létrehozása az ügyfélbérlőkben
- Kulcstartók létrehozása felügyelt identitással az ügyfélbérlőkben

[Azure Kubernetes Service (AKS) :](../../aks/index.yml)

- Üzemeltetett Kubernetes-környezetek kezelése, valamint tárolóba helyezett alkalmazások üzembe helyezése és kezelése az ügyfélbérlőkben
- Fürtök üzembe helyezése és kezelése az ügyfélbérlőkben
-   A Azure Monitor használata a különböző ügyfélbérlők teljesítményének figyelése tárolókhoz

[Azure Migrate:](../../migrate/index.yml)

- Migrálási projektek létrehozása az ügyfélbérlőben és virtuális gépek migrálása

[Azure Monitor:](../../azure-monitor/index.yml)

- A delegált előfizetések riasztásának megtekintése, valamint a riasztások megtekintése és frissítése az összes előfizetésben
- A delegált előfizetések tevékenységnapló-részleteinek megtekintése
- [Log Analytics:](../../azure-monitor/logs/service-providers.md)Több bérlőben lévő távoli munkaterületek adatainak lekérdezése (vegye figyelembe, hogy az ügyfélbérlők munkaterületeiről való adateléréshez használt Automation-fiókokat ugyanabban a bérlőben kell létrehozni)
- [Tevékenységnapló-riasztások létrehozása,](../../azure-monitor/alerts/alerts-activity-log.md) megtekintése és kezelése az ügyfélbérlőkben
- Hozzon létre olyan riasztásokat az ügyfélbérlőkben, amelyek automatizálást aktiválnak( például Azure Automation runbookokat vagy Azure Functions webhookokon keresztül a kezelő bérlőben
- Diagnosztikai [beállítások létrehozása az](../..//azure-monitor/essentials/diagnostic-settings.md) ügyfélbérlőkben az erőforrásnaplóknak a kezelő bérlő munkaterületei számára való elküldéhez
- SAP-számítási feladatok esetén az [SAP-megoldások](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293) metrikák figyelése az ügyfélbérlők összesített nézetében

[Azure-hálózatkezelés:](../../networking/networking-overview.md)

- Azure-beli [hálózati Virtual Network](../../virtual-network/index.yml) és virtuális hálózati adapterek (vNIC-k) üzembe helyezése és kezelése felügyelt bérlőkben
- Az ügyfelek [Azure Firewall](../../firewall/overview.md) erőforrásainak védelme érdekében üzembe helyezheti és Virtual Network konfigurálhatja
- Kapcsolati szolgáltatások, például a [Azure Virtual WAN,](../../virtual-wan/virtual-wan-about.md) [az ExpressRoute](../../expressroute/expressroute-introduction.md)és [a VPN-átjárók kezelése](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- A Azure Lighthouse [MSP-program](../../networking/networking-partners-msp.md) kulcsfontosságú forgatókönyvei Azure-hálózatkezelés támogatásához

[Azure Policy:](../../governance/policy/index.yml)

- Szabályzatdefiníciók létrehozása és szerkesztése a delegált előfizetések között
- Szabályzatdefiníciók és szabályzat-hozzárendelések üzembe helyezése több bérlőn
- Ügyfél által definiált szabályzatdefiníciók hozzárendelése a delegált előfizetések között
- Az ügyfelek a szolgáltató által a saját maguk által meghozott szabályzatokkal együtt látják a szabályzatokat
- Helyre [tudja helyezni a deployIfNotExists függvényt, vagy](../how-to/deploy-policy-remediation.md) módosíthatja a hozzárendeléseket a felügyelt bérlőn belül
- Vegye figyelembe, hogy a nem megfelelő erőforrások megfelelőségi részleteinek megtekintése az ügyfélbérlőkben jelenleg nem támogatott

[Azure Resource Graph:](../../governance/resource-graph/index.yml)

- Most már tartalmazza a bérlőazonosítót a visszaadott lekérdezési eredményekben, így azonosíthatja, hogy egy előfizetés felügyelt bérlőhöz tartozik-e

[Azure Security Center:](../../security-center/index.yml)

- Bérlők közötti láthatóság
  - A biztonsági szabályzatok megfelelőségét figyelheti, és biztosíthatja a bérlők összes erőforrásának biztonsági lefedettségét
  - Folyamatos jogszabályi megfelelés monitorozása több bérlőn egyetlen nézetben
  - A beavatkozást élvező biztonsági javaslatok monitorozása, osztályozása és rangsorolása biztonságos pontszámszámítással
- Bérlők közötti biztonsági biztonság kezelése
  - Biztonsági szabályzatok kezelése
  - Beavatkozás olyan erőforrásokon, amelyek nem követelményeknek megfelelnek a beavatkozásra használható biztonsági javaslatoknak
  - Biztonsággal kapcsolatos adatok gyűjtése és tárolása
- Bérlők közötti fenyegetésészlelés és -védelem
  - Fenyegetések észlelése bérlői erőforrások között
  - Fejlett fenyegetésvédelmi vezérlők, például igény szerinti (JIT) virtuálisgép-hozzáférés alkalmazása
  - A hálózati biztonsági csoportok konfigurációjának megsokosodása adaptív hálózat-megsokolással
  - Győződjön meg arról, hogy a kiszolgálók csak az adaptív alkalmazásvezérlőkkel futtatott alkalmazásokat és folyamatokat futtatják
  - Fontos fájlok és beállításjegyzék-bejegyzések változásainak figyelése a fájlintegritás-figyelés (FIM) segítségével
- Vegye figyelembe, hogy a teljes előfizetést delegálni kell a kezelő bérlőnek; Azure Security Center forgatókönyveket a delegált erőforráscsoportok nem támogatják

[Azure Sentinel:](../../sentinel/multiple-tenants-service-providers.md)

- Ügyfélbérlők Azure Sentinel [erőforrások kezelése](../../sentinel/multiple-tenants-service-providers.md)
- [Támadások követése és biztonsági riasztások megtekintése több bérlőn](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Több munkaterületen](../../sentinel/multiple-workspace-view.md) Azure Sentinel bérlők közötti incidensek megtekintése

[Azure Service Health:](../../service-health/index.yml)

- Ügyfélerőforrások állapotának figyelése a Azure Resource Health
- Az ügyfelek által használt Azure-szolgáltatások állapotának nyomon követése

[Azure Site Recovery:](../../site-recovery/index.yml)

- Azure-beli virtuális gépek vészhelyreállítási beállításainak kezelése az ügyfélbérlőkben (vegye figyelembe, hogy a virtuálisgép-bővítmények másolására nem használhat `RunAs` fiókokat)

[Azure Virtual Machines:](../../virtual-machines/index.yml)

- Virtuálisgép-bővítmények használata üzembe helyezés utáni konfigurációs és automatizálási feladatok végrehajtásához Azure-beli virtuális gépeken
- Rendszerindítási diagnosztika használata Azure-beli virtuális gépek hibaelhárításához
- Virtuális gépek elérése soros konzollal
- Virtuális gépek integrálása Azure Key Vault jelszavakhoz, titkos kulcsokhoz vagy titkosítási kulcsokhoz a lemeztitkosításhoz felügyelt identitás használatával szabályzat [használatával,](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)amely biztosítja, hogy a titkos kulcsok tárolása a felügyelt bérlőkben Key Vault tárolódva
- Vegye figyelembe, hogy a virtuális gépekre való távoli Azure Active Directory nem használható távoli bejelentkezéshez

Támogatási kérések:

- [A delegált  ](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) erőforrásokra vonatkozó támogatási kéréseket a Azure Portal súgóból és támogatásból nyithatja meg (a delegált hatókör számára elérhető támogatási csomag kiválasztása)
- Az [Azure Quota API használata](/rest/api/reserved-vm-instances/quotaapi) a delegált ügyfélerőforrások Azure-szolgáltatási kvótáinak megtekintéséhez és kezeléséhez

## <a name="current-limitations"></a>Aktuális korlátozások

Az összes forgatókönyv esetében vegye figyelembe az alábbi aktuális korlátozásokat:

- A Azure Resource Manager által kezelt kérések az Azure Lighthouse. A kérések műveleti URI-i a következővel `https://management.azure.com` kezdődnek: . Az erőforrástípus egy példánya Key Vault (például a titkos kulcsokhoz való hozzáférés vagy a tárolási adatok elérése) által kezelt kérések azonban nem támogatottak a Azure Lighthouse. A kérések műveleti URI-i általában a példány egyedi címével kezdődnek, például vagy `https://myaccount.blob.core.windows.net` `https://mykeyvault.vault.azure.net/` . Az utóbbi általában adatművelet, és nem felügyeleti művelet.
- A szerepkör-hozzárendelések az [Azure beépített szerepköreit kell használniuk.](../../role-based-access-control/built-in-roles.md) Az Azure-beli delegált erőforrás-kezelés jelenleg minden beépített szerepkört támogat, kivéve a Tulajdonost vagy bármely olyan beépített szerepkört, amely [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) rendelkezik engedéllyel. A Felhasználói hozzáférés rendszergazdája szerepkör csak korlátozott használatra támogatott a szerepkörök felügyelt identitásokhoz [való hozzárendelése esetén.](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)  Az egyéni szerepkörök [és a klasszikus előfizetés-rendszergazdai szerepkörök](../../role-based-access-control/classic-administrators.md) nem támogatottak.
- Bár az előfizetéseket használó előfizetéseket Azure Databricks, a kezelő bérlő felhasználói jelenleg nem Azure Databricks a munkaterületeket egy delegált előfizetésen.
- Bár az előfizetések és az erőforrás-zárolással felhasználhatja az erőforrás-csoportokat, ezek a zárolások nem akadályozzák meg, hogy a kezelő bérlő felhasználói műveleteket végeznek el. A rendszer [által](../../role-based-access-control/deny-assignments.md) felügyelt erőforrásokat ,például az Azure által felügyelt alkalmazások vagy az Azure Blueprints által létrehozott erőforrásokat (rendszer által hozzárendelt megtagadási hozzárendelések) védő hozzárendelések megakadályozzák, hogy a kezelő bérlő felhasználói az erőforrásokat használják; az ügyfélbérlő felhasználói azonban jelenleg nem hozhatnak létre saját megtagadási hozzárendeléseket (felhasználó által hozzárendelt megtagadási hozzárendeléseket).
- Az előfizetések delegálása az országos [felhőben](../../active-directory/develop/authentication-national-cloud.md) és a nyilvános Azure-felhőben, illetve két különálló országos felhőben nem támogatott.

## <a name="next-steps"></a>Következő lépések

- Az ügyfeleket Azure Lighthouse, akár Azure Resource Manager, [](../how-to/onboard-customer.md) akár egy privát vagy nyilvános felügyelt szolgáltatási ajánlat közzétételével a [Azure Marketplace.](../how-to/publish-managed-services-offers.md)
- [Az ügyfelek megtekintéséhez és](../how-to/view-manage-customers.md) kezeléséhez a saját **ügyfeleket a** Azure Portal.