---
title: Alapfogalmak – Hozzáférés és identitás az Azure Kubernetes Servicesben (AKS)
description: Megismeri az Azure Kubernetes Service (AKS) hozzáférését és identitását, beleértve a Azure Active Directory-integrációt, a Kubernetes szerepköralapú hozzáférés-vezérlését (Kubernetes RBAC), valamint a szerepköröket és kötéseket.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: b10d31cf069bc4f28a1597ec12160fa6ed98b8ce
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789554"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Hozzáférési és identitás-beállítások az Azure Kubernetes Service (AKS) szolgáltatáshoz

A Kubernetes-fürtökhöz való hozzáférést többféleképpen hitelesítheti, engedélyezheti, biztosíthatja és szabályozhatja. 
* A Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) használatával csak a szükséges erőforrásokhoz adhat hozzáférést a felhasználóknak, csoportoknak és szolgáltatásfiókoknak. 
* A Azure Kubernetes Service (AKS) segítségével tovább javíthatja a biztonság és az engedélyek struktúráját a Azure Active Directory és az Azure RBAC használatával. 

A Kubernetes RBAC és az AKS segítségével biztonságossá teszi a fürthöz való hozzáférést, és csak a minimálisan szükséges engedélyeket biztosítja a fejlesztők és az operátorok számára.

Ez a cikk az AKS-engedélyek hitelesítését és hozzárendelését segítő alapvető fogalmakat mutat be.

## <a name="aks-service-permissions"></a>Az AKS-szolgáltatás engedélyei

Fürt létrehozásakor az AKS létrehozza vagy módosítja a szükséges erőforrásokat (például virtuális gépeket és NIC-ket) a fürt létrehozásához és futtatásához a felhasználó nevében. Ez az identitás nem azonos a fürt identitásengedélyével, amely a fürt létrehozása során jön létre.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Identitás létrehozása és a fürtengedélyek üzemeltetése

A fürtöt létrehozására és üzemeltetési identitásának a következő engedélyekre van szüksége.

| Engedély | Ok |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | A lemeztitkosítási készlet azonosítójának olvasásához szükséges. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Közelségi elhelyezési csoportok frissítéséhez szükséges. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Az alkalmazásátjárók konfigurálásához és az alhálózathoz való csatlakozáshoz szükséges. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Az alhálózat hálózati biztonsági csoportjának egyéni virtuális hálózat használata esetén való konfigurálához szükséges.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | A kimenő nyilvános IP-k konfigurálához szükséges a standard Load Balancer. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Log Analytics-munkaterületek létrehozásához és frissítéséhez, valamint tárolók Azure-monitorozásához szükséges. |

### <a name="aks-cluster-identity-permissions"></a>AKS-fürtidentitás engedélyei

Az AKS-fürt identitása a következő engedélyeket használja, amely az AKS-fürthöz van társítva. Az engedélyeket az alábbi okokból használjuk:

| Engedély | Ok |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | A felhasználók létrehozásához és a fürt üzemeltetéséhez szükséges
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | A LoadBalancer-szolgáltatás terheléselosztásának konfigurálához szükséges. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | A LoadBalancer szolgáltatás nyilvános IP-ip-ének megkereséséhez és konfigurálásához szükséges. |
| `Microsoft.Network/publicIPAddresses/join/action` | Nyilvános IP-k loadBalancer szolgáltatáshoz való konfigurálásához szükséges. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Egy LoadBalancer-szolgáltatás biztonsági szabályainak létrehozásához vagy törléséhez szükséges. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Az AzureDisks konfigurálásához szükséges. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Az AzureFile vagy az AzureDisk tárfiókok konfigurálához szükséges. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | A csomópontok útválasztási táblázatai és útvonalai konfigurálására van szükség. |
| `Microsoft.Compute/virtualMachines/read` | A VMAS virtuális gépekkel kapcsolatos információk, például zónák, tartalék tartomány, méret és adatlemezek kereséséhez szükséges. |
| `Microsoft.Compute/virtualMachines/write` | Az AzureDisks virtuális géphez VMAS-ban való csatolásához szükséges. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | A virtuálisgép-méretezési csoportban lévő virtuális gépek (például zónák, tartalék tartomány, méret és adatlemezek) információinak kereséséhez szükséges. |
| `Microsoft.Network/networkInterfaces/write` | Virtuális gép VMAS-ban való hozzáadásához szükséges egy terheléselosztási háttércímkészlethez. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Virtuálisgép-méretezési csoport terheléselosztási háttércímkészlethez való hozzáadásához és egy virtuálisgép-méretezési csoport csomópontjainak horizontális felskálához szükséges. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Az AzureDisks csatolásához és virtuális gép virtuálisgép-méretezési készletből a terheléselosztáshoz való hozzáadásához szükséges. |
| `Microsoft.Network/networkInterfaces/read` | A belső IP-címek és a terheléselosztási háttércímkészletek VMAS-ban lévő virtuális gépek kereséséhez szükséges. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Belső IP-címek és terheléselosztási háttércímkészletek kereséséhez szükséges egy virtuálisgép-méretezési készletben lévő virtuális géphez. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | A virtuálisgép-méretezési készletben lévő virtuális gépek nyilvános IP-ip-ének megkereséhez szükséges. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Annak ellenőrzéséhez szükséges, hogy létezik-e alhálózat a belső terheléselosztáshoz egy másik erőforráscsoportban. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Az AzureDisk pillanatkép-beállításainak konfigurálához szükséges. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Az AzureDisk-kötetkorlátok megkereséhez szükséges a virtuális gépek méretének megállapítása. |

### <a name="additional-cluster-identity-permissions"></a>További fürtidentitási engedélyek

Adott attribútumokkal rendelkező fürt létrehozásakor a következő további engedélyekre lesz szüksége a fürtidentitáshoz. Mivel ezek az engedélyek nincsenek automatikusan hozzárendelve, a létrehozásuk után hozzá kell adni őket a fürtidentitáshoz.

| Engedély | Ok |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Akkor szükséges, ha egy másik erőforráscsoportban hálózati biztonsági csoportot használ. A LoadBalancer szolgáltatás biztonsági szabályainak konfigurálához szükséges. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Kötelező, ha egy másik erőforráscsoportban, például egyéni virtuális hálózatban található alhálózatot használ. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Kötelező, ha egy másik erőforráscsoportban található útvonaltáblához társított alhálózatot használ, például egy egyéni útvonaltáblát használó egyéni VNET-et. Annak ellenőrzéséhez szükséges, hogy létezik-e már alhálózat a másik erőforráscsoport alhálózatához. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Kötelező, ha egy másik erőforráscsoportban belső terheléselosztást használ. Annak ellenőrzéséhez szükséges, hogy létezik-e már alhálózat az erőforráscsoport belső terheléselosztásához. |
| `Microsoft.Network/privatednszones/*` | Kötelező, ha privát DNS-zónát használ egy másik erőforráscsoportban, például egy egyéni privateDNSZone erőforráscsoportban. |

## <a name="kubernetes-rbac"></a>A Kubernetes szerepköralapú hozzáférés-vezérlése (RBAC)

A Kubernetes RBAC a felhasználói műveletek részletes szűrését biztosítja. Ezzel a vezérlési mechanizmussal:
* Felhasználókat vagy felhasználói csoportokat rendelhet hozzá az erőforrások létrehozásához és módosításához, illetve a futó alkalmazások számítási feladataiból származó naplók megtekintéséhez. 
* Az engedélyek hatóköre egyetlen névtérre vagy a teljes AKS-fürtre terjedhet ki. 
* Létrehozhat *szerepköröket az* engedélyek meghatározásához, majd hozzárendelheti ezeket a szerepköröket a *szerepkörkötésekkel rendelkező felhasználókhoz.*

További információ: [A Kubernetes RBAC-hitelesítés használata.][kubernetes-rbac]

### <a name="roles-and-clusterroles"></a>Szerepkörök és fürtszerepkepkök

#### <a name="roles"></a>Szerepkörök
Mielőtt engedélyeket rendelne a Kubernetes RBAC-t használó felhasználókhoz, a felhasználói engedélyeket szerepkörként kell *meghatároznia.* Engedélyek megadása egy névtéren belül szerepkörök használatával. 

> [!NOTE]
> A Kubernetes-szerepkörök *engedélyeket* engedélyeznek; nem tagadják *meg az* engedélyeket.

Ha a teljes fürtre vagy egy adott névtéren kívüli fürterőforrásokra vonatkozó engedélyeket is meg kell adni, használhatja a *ClusterRoles használhatja a következőt:*.

#### <a name="clusterroles"></a>ClusterRoles (Fürtiroles)

A ClusterRole a teljes fürt erőforrásaira vonatkozó engedélyeket biztosít és alkalmaz, nem pedig egy adott névtérre.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings és ClusterRoleBindings

Miután definiálta az erőforrásokra vonatkozó engedélyek megadására vonatkozó szerepköröket, a Kubernetes RBAC-engedélyeket a *RoleBinding szerepkörhöz kell rendelnie.* Ha az AKS-fürt integrálható az [Azure Active Directory (Azure AD)](#azure-ad-integration)használatával, a RoleBindings engedélyeket ad az Azure AD-felhasználóknak a fürtön belüli műveletek végrehajtásához. Tekintse meg a Fürterőforrásokhoz való hozzáférés szabályozása [a Kubernetes](azure-ad-rbac.md)szerepköralapú hozzáférés-vezérlése és identitások használatával Azure Active Directory című bemutatja.

#### <a name="rolebindings"></a>RoleBindings (Szerepkörkötések)

Szerepkörök hozzárendelése felhasználókhoz egy adott névtérhez a RoleBindings használatával. A RoleBindings segítségével logikailag elkülöníthet egyetlen AKS-fürtöt, így a felhasználók csak a hozzárendelt névtér alkalmazás-erőforrásaihoz férhetnek hozzá. 

A szerepkörök a teljes fürthöz vagy egy adott névtéren kívüli fürterőforrásokhoz való kötéséhez ehelyett a *ClusterRoleBindings nevet használja.*

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

A ClusterRoleBinding használata esetén a szerepköröket felhasználókhoz köti, és a teljes fürt erőforrásaira alkalmazza, nem pedig egy adott névtérre. Ezzel a megközelítéssel hozzáférést adhat a rendszergazdáknak vagy a támogatási mérnököknek az AKS-fürt összes erőforrásához.


> [!NOTE]
> A Microsoft/AKS felhasználói jóváhagyással hajt végre fürtműveleteket egy beépített Kubernetes-szerepkör és beépített `aks-service` szerepkörkötés `aks-service-rolebinding` keretében. 
> 
> Ez a szerepkör lehetővé teszi az AKS számára a fürtproblémák elhárítását és diagnosztizálát, de nem módosíthatja az engedélyeket, és nem hozhat létre szerepköröket vagy szerepkörkötéseket, illetve egyéb magas jogosultságú műveleteket. A szerepkör-hozzáférés csak az aktív támogatási jegyeken van engedélyezve igény szerinti (JIT) hozzáféréssel. További információ az [AKS támogatási szabályzatairól.](support-policies.md)


### <a name="kubernetes-service-accounts"></a>Kubernetes-szolgáltatásfiókok

*A szolgáltatásfiókok* a Kubernetes egyik elsődleges felhasználótípusa. A Kubernetes API szolgáltatásfiókokat tart és kezel. A szolgáltatásfiók hitelesítő adatait a rendszer Kubernetes titkos kulcsként tárolja, így az arra jogosult podok az API-kiszolgálóval kommunikálhatnak. A legtöbb API-kérés hitelesítési jogkivonatot biztosít egy szolgáltatásfiókhoz vagy egy normál felhasználói fiókhoz.

A normál felhasználói fiókok hagyományosabb hozzáférést szolgáltatásokat és folyamatokat, de emberi rendszergazdáknak és fejlesztőknek is lehetővé teszik. Bár a Kubernetes nem biztosít identitáskezelési megoldást a normál felhasználói fiókok és jelszavak tárolására, külső identitáskezelési megoldásokat integrálhat a Kubernetesbe. AKS-fürtök esetén ez az integrált identitásmegoldás az Azure AD.

További információ a Kubernetes identitási lehetőségeiről: [Kubernetes-hitelesítés.][kubernetes-authentication]

## <a name="azure-ad-integration"></a>Azure AD-integráció

Az AKS-fürt biztonságának növelése az Azure AD-integrációval. A vállalati identitáskezelés évtizedeiből álló Azure AD egy több-bérlős, felhőalapú címtár- és identitáskezelési szolgáltatás, amely egyesíti az alapvető címtárszolgáltatásokat, az alkalmazás-hozzáférés-kezelési és az identitásvédelmet. Az Azure AD-val helyszíni identitásokat integrálhat AKS-fürtökbe, így egyetlen forrást biztosít a fiókkezeléshez és a biztonsághoz.

![Azure Active Directory AKS-fürtök integrációja](media/concepts-identity/aad-integration.png)

Az Azure AD-be integrált AKS-fürtök segítségével hozzáférést adhat a felhasználóknak vagy csoportoknak a Kubernetes-erőforrásokhoz egy névtérben vagy a fürtön belül. 

1. A konfigurációs `kubectl` környezet beszerzéséhez a felhasználó futtatja [az az aks get-credentials][az-aks-get-credentials] parancsot. 
1. Amikor egy felhasználó kapcsolatba lép az AKS-fürtön az -ral, a rendszer arra kéri, hogy jelentkezzen be `kubectl` Az Azure AD-beli hitelesítő adataival. 

Ez a megközelítés egyetlen forrást biztosít a felhasználói fiókok kezeléséhez és a jelszó hitelesítő adataihoz. A felhasználó csak a fürt rendszergazdája által meghatározott erőforrásokhoz férhet hozzá.

Az Azure AD-hitelesítést az AKS-fürtök biztosítják OpenID Connect. OpenID Connect az OAuth 2.0 protokollra épülő identitásréteg. További információt a OpenID Connect Open [ID Connect dokumentációjában talál.][openid-connect] A Kubernetes-fürtön belül a [webhook-jogkivonatok][webhook-token-docs] hitelesítése a hitelesítési jogkivonatok ellenőrzésére használható. A webhook-jogkivonatok hitelesítése az AKS-fürt részeként van konfigurálva és kezelhető.

### <a name="webhook-and-api-server"></a>Webhook és API-kiszolgáló

![Webhook- és API-kiszolgálóhitelesítési folyamat](media/concepts-identity/auth-flow.png)

Ahogy a fenti ábrán látható, az API-kiszolgáló az AKS-webhook-kiszolgálót hívja meg, és a következő lépéseket végzi el:

1. `kubectl` az Azure AD-ügyfélalkalmazással jelentkeztet be felhasználókat [az OAuth 2.0](../active-directory/develop/v2-oauth2-device-code.md)eszközengedélyezési folyamatával.
2. Az Azure AD egy access_token, id_token és egy refresh_token.
3. A felhasználó kérést tesz a `kubectl` következőre: , access_token a következőből: `kubeconfig` .
4. `kubectl` elküldi a access_token az API Servernek.
5. Az API-kiszolgáló az Auth WebHook-kiszolgálóval van konfigurálva az ellenőrzés végrehajtásához.
6. A hitelesítési webhook-kiszolgáló az Azure AD JSON-webtoken aláírókulcs ellenőrzéséhez ellenőrzi, hogy az aláírás érvényes-e.
7. A kiszolgálóalkalmazás a felhasználó által megadott hitelesítő adatokkal lekérdezi a bejelentkezett felhasználó csoporttagságait az MS Graph API.
8. A rendszer egy választ küld az API-kiszolgálónak olyan felhasználói adatokkal, mint a hozzáférési jogkivonat egyszerű felhasználóneve (UPN) jogcíme, valamint a felhasználó csoporttagságának az objektumazonosító alapján.
9. Az API a Kubernetes-szerepkör/RoleBinding alapján hoz engedélyezési döntést.
10. Az engedély után az API-kiszolgáló választ ad vissza a következőnek: `kubectl` .
11. `kubectl` visszajelzést küld a felhasználónak.
 
Ismerje meg, hogyan integrálhatja az AKS-t az Azure AD-be az [AKS által felügyelt Azure AD-integrációs útmutatóval.](managed-aad.md)

## <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) [](../azure-resource-manager/management/overview.md) egy olyan engedélyezési rendszer, amely Azure Resource Manager az Azure-erőforrások részletes hozzáférés-kezelését biztosítja.

| RBAC-rendszer | Description |
|---|---|
| A Kubernetes szerepköralapú hozzáférés-vezérlése (RBAC) | Az AKS-fürtön belüli Kubernetes-erőforrásokon való használatra tervezték. |
| Azure RBAC-vel | Az Azure-előfizetésen belüli erőforrásokon való használatra tervezték. |

Az Azure RBAC használatával  létrehoz egy szerepkör-definíciót, amely felvázolja az alkalmazandó engedélyeket. Ezután hozzárendelheti ezt a szerepkör-definíciót egy felhasználóhoz vagy csoporthoz egy adott hatókör szerepkör-hozzárendelése *révén.*  A hatókör lehet egyéni erőforrás, erőforráscsoport vagy az előfizetésen belül.

További információ: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?][azure-rbac]

Az AKS-fürt teljes működtetéséhez két hozzáférési szint szükséges: 
* [Hozzáférés az AKS-erőforráshoz az Azure-előfizetésben.](#azure-rbac-to-authorize-access-to-the-aks-resource) 
  * Szabályozhatja a fürt skálázását vagy frissítését az AKS API-k használatával.
  * Húzza le a `kubeconfig` et.
* Hozzáférés a Kubernetes API-hoz. Ezt a hozzáférést a következő feltételek valamelyike vezérli:
  * [Kubernetes RBAC](#kubernetes-rbac) (hagyományosan).
  * [Az Azure RBAC integrálása az AKS-sel a Kubernetes-hitelesítéshez.](#azure-rbac-for-kubernetes-authorization-preview)

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC az AKS-erőforráshoz való hozzáféréshez

Az Azure RBAC segítségével részletes hozzáférést nyújthat a felhasználóknak (vagy identitások) az AKS-erőforrásokhoz egy vagy több előfizetésben. A fürt méretezéséhez és [frissítéséhez például](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) használhatja Azure Kubernetes Service Közreműködő szerepkört. Eközben egy másik, [Azure Kubernetes Service fürt-rendszergazdai](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) szerepkörsel rendelkező felhasználó csak a rendszergazda lekért engedélyével `kubeconfig` rendelkezik.

Másik lehetőségként általános közreműködői szerepkört is adhat [a](../role-based-access-control/built-in-roles.md#contributor) felhasználónak. Az általános közreműködői szerepkörben a felhasználók a fenti engedélyeket és minden lehetséges műveletet elvégeznek az AKS-erőforráson az engedélyek kezelése kivételével.

[Az Azure RBAC használatával meghatározhatja a Hozzáférést a Kubernetes konfigurációs fájlhoz az AKS-ban.](control-kubeconfig-access.md)

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC Kubernetes-hitelesítéshez (előzetes verzió)

Az Azure RBAC-integrációval az AKS egy Kubernetes-engedélyezési webhook-kiszolgálót fog használni, így az Azure AD-hez integrált Kubernetes-fürterőforrás-engedélyeket és -hozzárendeléseket azure-beli szerepkör-definíciók és szerepkör-hozzárendelések használatával kezelheti.

![Azure RBAC a Kuberneteshez engedélyezési folyamat](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Ahogy a fenti ábrán is látható, az Azure RBAC-integráció használata esetén a Kubernetes API felé minden kérés ugyanazt a hitelesítési folyamatot fogja követni, mint a Azure Active Directory [integráció szakasza.](#azure-ad-integration) 

Ha a kérést érvénybe hozó identitás létezik az Azure AD-ban, az Azure a Kubernetes RBAC csapatával együtt engedélyezi a kérést. Ha az identitás az Azure AD-n kívül található (pl. Egy Kubernetes-szolgáltatásfiók), az engedélyezés a normál Kubernetes RBAC-re fog visszaesni.

Ebben a forgatókönyvben Azure RBAC-mechanizmusokat és API-kat használ a felhasználók beépített szerepkörök hozzárendeléséhez vagy egyéni szerepkörök létrehozásához, ahogyan a Kubernetes-szerepkörök esetében tenné. 

Ezzel a funkcióval nem csupán engedélyeket ad a felhasználóknak az AKS-erőforráshoz az előfizetések között, hanem konfigurálja a Kubernetes API-hozzáférést vezérlő fürtök szerepkörét és engedélyét is. Megadhatja például a `Azure Kubernetes Service RBAC Viewer` szerepkört az előfizetés hatókörében. A szerepkör-címzett az összes fürt összes Kubernetes-objektumát le tudja majd listába sorolni és le tudja szerezni módosítás nélkül.

> [!IMPORTANT]
> A funkció használata előtt engedélyeznie kell az Azure RBAC-t a Kubernetes-hitelesítéshez. További részletekért és részletes útmutatásért kövesse az [Azure RBAC használata Kubernetes-hitelesítéshez](manage-azure-rbac.md) című útmutatót.

#### <a name="built-in-roles"></a>Beépített szerepkörök

Az AKS az alábbi négy beépített szerepkört biztosítja. Hasonlóak a [Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) beépített szerepköreihez, néhány különbséggel, például a CRD-k támogatásával. Tekintse meg az egyes [Beépített Azure-szerepkör](../role-based-access-control/built-in-roles.md)által engedélyezett műveletek teljes listáját.

| Szerepkör                                | Leírás  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC-megjelenítő  | Csak olvasási hozzáférést biztosít a névtér legtöbb objektumának eléréséhez. <br> Nem engedélyezi a szerepkörök vagy szerepkörkötések megtekintését.<br> Nem engedélyezi a `Secrets` megtekintését. A tartalom olvasása hozzáférést biztosít a névtérben a hitelesítő adatokhoz, ami lehetővé teszi az API-hozzáférést, mint bármely más a névtérben (a jogosultságok `Secrets` `ServiceAccount` `ServiceAccount` eszkalálásának egyik formája).  |
| Azure Kubernetes Service RBAC-író | Olvasási/írási hozzáférést biztosít a névtér legtöbb objektumához. <br> Nem engedélyezi a szerepkörök vagy szerepkörkötések megtekintését vagy módosítását. <br> Lehetővé teszi a podok bármely ServiceAccount-ként való hozzáférését és futtatását a névtérben, így bármely ServiceAccount API-hozzáférési szintje elérhető a `Secrets` névtérben. |
| Azure Kubernetes Service RBAC-rendszergazda  | Engedélyezi a rendszergazdai hozzáférést, amely egy névtérben adható meg. <br> Olvasási/írási hozzáférést biztosít a névtér (vagy fürthatókör) legtöbb erőforrása számára, beleértve a szerepkörök és szerepkörkötések névtérben való létrehozására vonatkozó képességet is. <br> Nem engedélyezi az írási hozzáférést az erőforráskvótához vagy a névtérhez. |
| Azure Kubernetes Service RBAC-fürt rendszergazdája  | Lehetővé teszi, hogy a felügyelői hozzáférés bármilyen műveletet hajtson végre bármely erőforráson. <br> Teljes körű vezérlést biztosít a fürt összes erőforrása és az összes névtér felett. |


## <a name="summary"></a>Összefoglalás

Tekintse meg a táblázatot, hogy röviden összefoglalja, hogyan hitelesíthetők a felhasználók a Kubernetesben, ha az Azure AD-integráció engedélyezve van. A felhasználó parancssorozata minden esetben a következő:
1. Az `az login` Azure-beli hitelesítéshez futtassa a (futtatás) adatokat.
1. A fürt hitelesítő adatainak letöltéséhez futtassa a `az aks get-credentials` következőt: `.kube/config` .
1. `kubectl`Futtatassa a parancsokat. 
   * Az első parancs böngészőalapú hitelesítést aktiválhat a fürtön való hitelesítéshez az alábbi táblázatban leírtak szerint.

A Azure Portal a következőt találja:
* A *második oszlopban* hivatkozott szerepkör-engedély (Azure RBAC szerepkör-Access Control **jelenik** meg. 
* A Fürt rendszergazdája Azure AD-csoport a Konfiguráció **lapon jelenik** meg.
  * Paraméternévvel is megtalálható `--aad-admin-group-object-ids` az Azure CLI-ban.


| Description        | Szerepkör megadása szükséges| Fürt-rendszergazdai Azure AD-csoport(ak) | A következő esetekben használja |
| -------------------|------------|----------------------------|-------------|
| Régi rendszergazdai bejelentkezés ügyfél-tanúsítvánnyal| **Azure Kubernetes rendszergazdai szerepkör.** Ez a szerepkör lehetővé teszi a jelölővel való használatot, amely letölt egy örökölt `az aks get-credentials` `--admin` [(nem Azure AD-beli)](control-kubeconfig-access.md) fürt-rendszergazdai tanúsítványt a felhasználó `.kube/config` azonosítójába. Ez az "Azure Kubernetes rendszergazdai szerepkör" egyetlen célja.|n.a.|Ha véglegesen le van tiltva, mert nem rendelkezik hozzáféréssel a fürthöz hozzáféréssel rendelkező érvényes Azure AD-csoporthoz.| 
| Azure AD manuális (fürt)RoleBindings használatával| **Azure Kubernetes felhasználói szerepkör.** A "User" (Felhasználó) szerepkör használata `az aks get-credentials` a jelző nélkül is lehetővé `--admin` teszi. (Ez az "Azure Kubernetes felhasználói szerepkör" egyetlen célja.) Az eredmény egy Azure AD-kompatibilis fürtön egy üres bejegyzés letöltése a-be, amely elindítja [a](control-kubeconfig-access.md) böngészőalapú hitelesítést, amikor a először `.kube/config` `kubectl` használja.| A felhasználó egyik csoportban sem van. Mivel a felhasználó egyetlen fürtgazdai csoportban sem található meg, a jogosultságai teljes mértékben a fürt rendszergazdái által beállított RoleBindings vagy ClusterRoleBindings alapján lesznek szabályozva. A (fürt)RoleBindings Azure AD-felhasználókat vagy [Azure AD-csoportokat](azure-ad-rbac.md) nevez meg sajátjukként. `subjects` Ha nincs ilyen kötés beállítva, a felhasználó nem fog tudni parancsokat `kubectl` használni.|Ha finomhangolt hozzáférés-vezérlést szeretne, és nem használja az Azure RBAC-t a Kubernetes-hitelesítéshez. Vegye figyelembe, hogy a kötéseket beállítja felhasználónak a táblázatban felsorolt módszerek egyikével kell bejelentkeznie.|
| Azure AD a rendszergazdai csoport tagja szerint| Lásd fent|A felhasználó tagja az itt felsorolt csoportok egyikének. Az AKS automatikusan létrehoz egy ClusterRoleBinding szerepkört, amely az összes felsorolt csoportot a `cluster-admin` Kubernetes-szerepkörhöz köti. Így az ezekben a csoportokban a felhasználók az összes `kubectl` parancsot a következőként futtatják: `cluster-admin` .|Ha kényelmes módon szeretne teljes rendszergazdai jogosultságokat ad a felhasználóknak, és nem használja az Azure RBAC-t a Kubernetes-hitelesítéshez. |
| Azure AD És Azure RBAC a Kubernetes-hitelesítéshez|Két szerepkör: <br> Először az **Azure Kubernetes felhasználói szerepkörét** (a fentiek szerint). <br> Másodszor, az egyik "Azure Kubernetes Service **RBAC..."** a fent felsorolt szerepköröket, vagy a saját egyéni alternatívát.|A Konfiguráció lap Rendszergazdai szerepkörök mezője irreleváns, ha az Azure RBAC for Kubernetes Authorization engedélyezve van.|Azure RBAC-t használ a Kubernetes-hitelesítéshez. Ezzel a módszersel a RoleBindings vagy a ClusterRoleBindings beállítása nélkül is finomhangolhatja az irányítást.|

## <a name="next-steps"></a>Következő lépések

- Az Azure AD és a Kubernetes RBAC használatának első lépésekért lásd: Integráció Azure Active Directory [AKS-sel.][aks-aad]
- A kapcsolódó ajánlott eljárásokért lásd: Ajánlott eljárások az AKS-sel való hitelesítéshez [és engedélyezéshez.][operator-best-practices-identity]
- Az Azure RBAC Kubernetes-hitelesítéshez való használatának első lépésekért lásd: Hozzáférés engedélyezése az Azure RBAC használatával az [Azure Kubernetes Service-fürtön belül.](manage-azure-rbac.md)
- A fájl biztonságossá tétele: A fürtkonfigurációs `kubeconfig` [fájlhoz való hozzáférés korlátozása](control-kubeconfig-access.md)

A Kubernetes és az AKS alapvető fogalmairól a következő cikkekben talál további információt:

- [Kubernetes/ AKS-fürtök és számítási feladatok][aks-concepts-clusters-workloads]
- [Kubernetes/ AKS-biztonság][aks-concepts-security]
- [Kubernetes/ AKS virtuális hálózatok][aks-concepts-network]
- [Kubernetes/ AKS-tároló][aks-concepts-storage]
- [Kubernetes/ AKS-méretezés][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
