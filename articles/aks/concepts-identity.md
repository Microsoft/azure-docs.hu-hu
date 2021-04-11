---
title: Fogalmak – hozzáférés és identitás az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes szolgáltatás (ak) hozzáférését és identitását, beleértve a Azure Active Directory integrációt, a szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC), valamint a szerepköröket és kötéseket.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 76871565e0bb4ca1811d46531d07b89181d07e19
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105918"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Hozzáférési és identitás-beállítások az Azure Kubernetes Service (AKS) szolgáltatáshoz

A Kubernetes-fürtökhöz való hozzáférés több módon is hitelesíthető, engedélyezhető, biztosítható és szabályozható. 
* A Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) használatával a felhasználók, csoportok és szolgáltatásfiókok hozzáférése csak a szükséges erőforrásokhoz biztosítható. 
* Az Azure Kubernetes Service (ak) használatával tovább javíthatja a biztonsági és az engedélyezési struktúrát Azure Active Directory és az Azure RBAC használatával. 

A Kubernetes RBAC és az AK segítségével biztonságossá teheti a fürt hozzáférését, és csak a minimálisan szükséges engedélyeket biztosítja a fejlesztők és a kezelők számára.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek segítséget nyújtanak az AK-ban lévő engedélyek hitelesítéséhez és hozzárendeléséhez.

## <a name="aks-service-permissions"></a>AK szolgáltatás engedélyei

Fürt létrehozásakor az AK létrehozza vagy módosítja a szükséges erőforrásokat (például virtuális gépeket és hálózati adaptereket) a fürt létrehozásához és futtatásához a felhasználó nevében. Ez az identitás különbözik a fürt személyazonossági engedélyével, amely a fürt létrehozásakor jön létre.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>A fürt engedélyeinek létrehozásához és üzemeltetéséhez szükséges identitás

A fürt létrehozásához és üzemeltetéséhez a következő engedélyekre van szükség.

| Engedély | Ok |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | A lemez titkosítási készletének AZONOSÍTÓjának olvasásához szükséges. |
| `Microsoft.Compute/proximityPlacementGroups/write` | A közelségi elhelyezési csoportok frissítéséhez szükséges. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Az Application Gateway konfigurálásához és az alhálózat csatlakoztatásához szükséges. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Egyéni VNET használata esetén az alhálózat hálózati biztonsági csoportjának konfigurálásához szükséges.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | A kimenő nyilvános IP-címek a standard Load Balancer való konfigurálásához szükséges. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Log Analytics munkaterületek létrehozásához és frissítéséhez, valamint a tárolók Azure-figyeléséhez szükséges. |

### <a name="aks-cluster-identity-permissions"></a>AK-fürt identitásának engedélyei

A következő engedélyeket használja az AK-fürt identitása, amely az AK-fürthöz lett létrehozva és társítva. Az alábbi okokból minden engedély használatos:

| Engedély | Ok |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | A felhasználók létrehozásához és a fürt üzemeltetéséhez szükséges
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | A terheléselosztó terheléselosztó-szolgáltatáshoz való konfigurálásához szükséges. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | A terheléselosztó szolgáltatás nyilvános IP-címeinek megkereséséhez és konfigurálásához szükséges. |
| `Microsoft.Network/publicIPAddresses/join/action` | A terheléselosztó szolgáltatás nyilvános IP-címeinek konfigurálásához szükséges. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | A terheléselosztó szolgáltatás biztonsági szabályainak létrehozásához vagy törléséhez szükséges. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | A AzureDisks konfigurálásához szükséges. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | A AzureFile vagy AzureDisk tartozó Storage-fiókok konfigurálásához szükséges. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | A csomópontok útválasztási tábláinak és útvonalának konfigurálásához szükséges. |
| `Microsoft.Compute/virtualMachines/read` | Egy VMAS található virtuális gépek információinak megkereséséhez szükséges, például a zónák, a tartalék tartomány, a méret és az adatlemezek. |
| `Microsoft.Compute/virtualMachines/write` | Egy VMAS lévő virtuális géphez való AzureDisks csatolásához szükséges. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | A virtuálisgép-méretezési csoportokban lévő virtuális gépek információinak megkereséséhez szükséges, például a zónák, a tartalék tartomány, a méret és az adatlemezek. |
| `Microsoft.Network/networkInterfaces/write` | Ahhoz szükséges, hogy egy VMAS egy virtuális gépet egy terheléselosztó háttérbeli címkészlet számára adjon hozzá. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Egy virtuálisgép-méretezési csoport terheléselosztásához szükséges a virtuálisgép-méretezési csoportokban, és bővíteni kell a csomópontokat. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | A AzureDisks csatolásához és egy virtuális gép virtuálisgép-méretezési csoportból a terheléselosztó számára való hozzáadásához szükséges. |
| `Microsoft.Network/networkInterfaces/read` | A VMAS virtuális gépei számára a belső IP-címek és a terheléselosztó háttér-címkészlet esetében szükséges. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | A virtuálisgép-méretezési csoportokban lévő virtuális gépekhez tartozó belső IP-címek és terheléselosztó háttér-címkészlet számára szükséges. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | A virtuálisgép-méretezési csoportokban lévő virtuális gépek nyilvános IP-címeinek megkereséséhez szükséges. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Szükséges annak ellenőrzéséhez, hogy létezik-e alhálózat a belső terheléselosztó számára egy másik erőforráscsoporthoz. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | A AzureDisk Pillanatképek konfigurálásához szükséges. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | A virtuálisgép-méretek megkereséséhez szükséges a AzureDisk. |

### <a name="additional-cluster-identity-permissions"></a>További fürt-identitási engedélyek

Ha adott attribútumokkal rendelkező fürtöt hoz létre, akkor a fürt identitásához a következő további engedélyek szükségesek. Mivel ezek az engedélyek nem lesznek automatikusan hozzárendelve, a létrehozás után hozzá kell adni őket a fürt identitásához.

| Engedély | Ok |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Akkor szükséges, ha egy másik erőforráscsoport hálózati biztonsági csoportját használja. A terheléselosztó szolgáltatás biztonsági szabályainak konfigurálásához szükséges. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Kötelező, ha egy alhálózatot használ egy másik erőforráscsoporthoz, például egy egyéni VNET. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Kötelező, ha egy másik erőforráscsoport útválasztási táblájához társított alhálózatot használ, például egy egyéni VNET egyéni útválasztási táblázattal. Szükséges annak ellenőrzéséhez, hogy egy alhálózat már létezik-e a másik erőforráscsoport alhálózatához. |
| `Microsoft.Network/virtualNetworks/subnets/read` | A belső terheléselosztó egy másik erőforráscsoporthoz való használata esetén kötelező. Szükséges annak ellenőrzéséhez, hogy már létezik-e alhálózat a belső terheléselosztó számára az erőforráscsoporthoz. |
| `Microsoft.Network/privatednszones/*` | Kötelező, ha egy privát DNS-zónát használ egy másik erőforráscsoporthoz, például egy egyéni privateDNSZone. |

## <a name="kubernetes-rbac"></a>A Kubernetes szerepköralapú hozzáférés-vezérlése (RBAC)

A Kubernetes RBAC a felhasználói műveletek részletes szűrését teszi lehetővé. Ezzel a vezérlési mechanizmussal:
* Az erőforrások létrehozásához és módosításához, illetve az alkalmazás-munkaterhelések naplóinak megtekintéséhez engedélyeket kell rendelnie a felhasználókhoz vagy a felhasználói csoportokhoz. 
* A hatókörre vonatkozó engedélyeket egyetlen névtérhez vagy a teljes AK-fürthöz használhatja. 
* Hozzon létre *szerepköröket* az engedélyek definiálásához, majd rendelje hozzá ezeket a szerepköröket a felhasználókhoz *szerepkör-kötésekkel*.

További információ: [a KUBERNETES RBAC-hitelesítés használata][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Szerepkörök és ClusterRoles

#### <a name="roles"></a>Szerepkörök
Ahhoz, hogy engedélyeket rendeljen a felhasználókhoz a Kubernetes RBAC, a felhasználói engedélyeket *szerepkörként* kell meghatároznia. Engedélyeket adhat meg a névtéren belül a szerepkörök használatával. 

> [!NOTE]
> A Kubernetes szerepkörei engedélyeket *biztosítanak* ; nem *tagadják* meg az engedélyeket.

Ha engedélyeket szeretne adni a teljes fürtön vagy egy adott névtéren kívüli fürtözött erőforrásokon, használhatja a *ClusterRoles*-t.

#### <a name="clusterroles"></a>ClusterRoles

A ClusterRole a teljes fürtön lévő erőforrásokra vonatkozó engedélyeket biztosít és alkalmaz, nem pedig egy adott névteret.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings és ClusterRoleBindings

Miután definiálta a szerepköröket az erőforrásokra vonatkozó engedélyek megadásához, hozzá kell rendelnie a Kubernetes RBAC engedélyeit egy *RoleBinding*. Ha az AK-fürt [integrálva van Azure Active Directory (Azure ad) szolgáltatással](#azure-ad-integration), a RoleBindings engedélyeket biztosít az Azure ad-felhasználóknak a fürtön belüli műveletek végrehajtásához. Megtudhatja, hogyan [vezérelheti a Kubernetes a szerepköralapú hozzáférés-vezérlés és a Azure Active Directory identitások használatával a fürt erőforrásaihoz való hozzáférést](azure-ad-rbac.md).

#### <a name="rolebindings"></a>RoleBindings

Szerepkörök kiosztása egy adott névtér felhasználói számára a RoleBindings használatával. A RoleBindings használatával logikailag elkülönítheti egyoszlopos fürtöket, így csak a felhasználók férhetnek hozzá a hozzárendelt névtérben lévő alkalmazás-erőforrásokhoz. 

Ha a szerepköröket a teljes fürtön kívánja kötni, vagy egy adott névtéren kívüli fürt erőforrásaira, használja inkább a *ClusterRoleBindings*-t.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

A ClusterRoleBinding a szerepköröket a felhasználókhoz köti, és a teljes fürtön lévő erőforrásokra, nem pedig egy adott névtérre vonatkozik. Ez a megközelítés lehetővé teszi, hogy a rendszergazdák vagy a támogatási mérnökök hozzáférjenek az AK-fürt összes erőforrásához.


> [!NOTE]
> A Microsoft/AK egy beépített Kubernetes-szerepkörhöz `aks-service` és beépített szerepkör-kötéshez tartozó felhasználói beleegyező műveleteket hajt végre `aks-service-rolebinding` . 
> 
> Ez a szerepkör lehetővé teszi az AK számára a fürtökkel kapcsolatos problémák hibaelhárítását és diagnosztizálását, de nem módosíthatja az engedélyeket, és nem hozhat létre szerepköröket vagy szerepkör-kötéseket, illetve egyéb magas jogosultsági A szerepkör-hozzáférés csak az aktív támogatási jegyek esetében engedélyezett az igény szerinti (JIT) hozzáféréssel. Tudjon meg többet az [AK-támogatási szabályzatokról](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Kubernetes-szolgáltatásfiókok

A *szolgáltatásfiókok* a Kubernetes egyik elsődleges felhasználói típusa. A Kubernetes API a szolgáltatásfiókok tárolására és kezelésére szolgál. A szolgáltatásfiók hitelesítő adatait Kubernetes titokként tárolja a rendszer, így az engedélyezett hüvelyek is használhatják az API-kiszolgálóval folytatott kommunikációt. A legtöbb API-kérelem hitelesítési jogkivonatot biztosít egy szolgáltatásfiók vagy egy normál felhasználói fiók számára.

A normál felhasználói fiókok több hagyományos hozzáférést tesznek lehetővé az emberi rendszergazdák vagy a fejlesztők számára, nem csupán a szolgáltatásokhoz és folyamatokhoz. Míg a Kubernetes nem biztosít a normál felhasználói fiókok és jelszavak tárolására szolgáló Identitáskezelés megoldását, integrálhatja a külső identitási megoldásokat a Kubernetes-be. AK-fürtök esetében ez az integrált Identity megoldás az Azure AD.

További információ a Kubernetes identitási lehetőségeiről: Kubernetes- [hitelesítés][kubernetes-authentication].

## <a name="azure-ad-integration"></a>Azure AD-integráció

Az AK-fürt biztonságának növelése az Azure AD-integrációval. Az Azure AD egy több-bérlős, felhőalapú címtár-és Identitáskezelő szolgáltatás, amely az alapszintű címtárszolgáltatások, az alkalmazás-hozzáférés-kezelés és az identitások védelmét ötvözi. Az Azure AD lehetővé teszi, hogy a helyszíni identitásokat AK-fürtökbe integrálva egyetlen forrást biztosítson a fiókok kezeléséhez és biztonságához.

![Azure Active Directory-integráció AK-fürtökkel](media/concepts-identity/aad-integration.png)

Az Azure AD-vel integrált AK-fürtök segítségével a felhasználók vagy csoportok hozzáférést biztosíthatnak a Kubernetes-erőforrásokhoz a névtérben vagy a fürtön belül. 

1. A konfigurációs környezet beszerzéséhez `kubectl` a felhasználó az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot futtatja. 
1. Ha a felhasználó együttműködik az AK-beli fürttel `kubectl` , a rendszer felszólítja, hogy jelentkezzen be az Azure ad-beli hitelesítő adataival. 

Ez a megközelítés egyetlen forrást biztosít a felhasználói fiókok felügyeletéhez és a jelszó hitelesítő adataihoz. A felhasználó csak a fürt rendszergazdája által meghatározott erőforrásokat érheti el.

Az Azure AD-hitelesítés az OpenID-kapcsolattal rendelkező AK-fürtökhöz van megadva. Az OpenID Connect egy OAuth 2,0 protokollra épülő identitási réteg. Az OpenID Connecttel kapcsolatos további információkért tekintse meg az [ID Connect dokumentációját][openid-connect]. A Kubernetes-fürtön belül a rendszer [webhook jogkivonat-hitelesítést][webhook-token-docs] használ a hitelesítési tokenek ellenőrzéséhez. A webhook-jogkivonat hitelesítése az AK-fürt részeként van konfigurálva és felügyelve.

### <a name="webhook-and-api-server"></a>Webhook és API-kiszolgáló

![Webhook és API-kiszolgáló hitelesítési folyamata](media/concepts-identity/auth-flow.png)

Ahogy az a fenti ábrán is látható, az API-kiszolgáló meghívja az AK webhook-kiszolgálót, és a következő lépéseket hajtja végre:

1. `kubectl` Az Azure AD ügyfélalkalmazás használatával jelentkezik be a felhasználók számára az [OAuth 2,0-es eszköz engedélyezési folyamatával](../active-directory/develop/v2-oauth2-device-code.md).
2. Az Azure AD egy access_token, id_token és egy refresh_token biztosít.
3. A felhasználó elküld egy kérést a `kubectl` access_token `kubeconfig` .
4. `kubectl` a access_token küldése az API-kiszolgálónak.
5. Az API-kiszolgáló a hitelesítési webhook-kiszolgálóval van konfigurálva az érvényesítés végrehajtásához.
6. A hitelesítési webhook-kiszolgáló megerősíti, hogy a JSON Web Token aláírása érvényes az Azure AD nyilvános aláíró kulcsának ellenőrzésével.
7. A kiszolgálóalkalmazás felhasználó által megadott hitelesítő adatok használatával kérdezi le a bejelentkezett felhasználó csoporttagságait az MS Graph APIból.
8. A rendszer választ kap az API-kiszolgálónak a felhasználói adatokkal, például a hozzáférési jogkivonat egyszerű felhasználóneve (UPN) jogcímével, valamint a felhasználó csoporttagság alapján.
9. Az API a Kubernetes szerepkör/RoleBinding alapján hajt végre engedélyezési döntést.
10. Az engedélyezést követően az API-kiszolgáló egy választ küld vissza `kubectl` .
11. `kubectl` visszajelzést nyújt a felhasználónak.
 
Ismerje meg, hogyan integrálhatja az AK-t az Azure AD-val az [AK-nal felügyelt Azure ad-integráció útmutatójának](managed-aad.md)segítségével.

## <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) egy [Azure Resource Managerra](../azure-resource-manager/management/overview.md) épülő engedélyezési rendszer, amely részletes hozzáférést biztosít az Azure-erőforrások kezeléséhez.

| RBAC-rendszeren | Description |
|---|---|
| A Kubernetes szerepköralapú hozzáférés-vezérlése (RBAC) | Az Kubernetes-erőforrások az AK-fürtön belüli működésére lett tervezve. |
| Azure RBAC-vel | Az Azure-előfizetésen belüli erőforrásokkal való munkavégzésre lett tervezve. |

Az Azure RBAC létrehoz egy szerepkör- *definíciót* , amely az alkalmazandó engedélyeket ismerteti. Ezután hozzárendelheti a szerepkör-definícióhoz tartozó felhasználót vagy csoportot egy adott *hatókörhöz* tartozó *szerepkör-hozzárendelés* használatával. A hatókör lehet önálló erőforrás, erőforráscsoport vagy az előfizetés egésze.

További információ: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?][azure-rbac]

Az AK-fürtök teljes körű működtetéséhez két hozzáférési szint szükséges: 
* [Hozzáférés az AK-erőforráshoz az Azure-előfizetésében](#azure-rbac-to-authorize-access-to-the-aks-resource). 
  * Szabályozhatja a fürt méretezését vagy frissítését az AK API-k használatával.
  * Húzza ki a t `kubeconfig` .
* Hozzáférés a Kubernetes API-hoz. Ezt a hozzáférést a következők vezérlik:
  * [KUBERNETES RBAC](#kubernetes-rbac) (hagyományosan).
  * [Az Azure RBAC és az AK Kubernetes-engedélyezésének integrálása](#azure-rbac-for-kubernetes-authorization-preview).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure-RBAC az AK-erőforráshoz való hozzáférés engedélyezéséhez

Az Azure RBAC segítségével megadhatja a felhasználók (vagy identitások) számára az AK-erőforrások részletes elérését egy vagy több előfizetésben. Használhatja például az [Azure Kubernetes szolgáltatás közreműködői szerepkörét](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) a fürt skálázásához és frissítéséhez. Eközben egy másik felhasználónak, aki az [Azure Kubernetes Service-fürt rendszergazdai szerepkörrel](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) rendelkezik, csak engedélyt kap a rendszergazda lekérésére `kubeconfig` .

Másik lehetőségként megadhatja a felhasználónak az általános [közreműködő](../role-based-access-control/built-in-roles.md#contributor) szerepkört. Az általános közreműködő szerepkörrel a felhasználók a fenti engedélyeket és minden lehetséges műveletet elvégezhetik az AK-erőforráson, kivéve az engedélyek kezelését.

Az [Azure RBAC segítségével definiálhatja a Kubernetes konfigurációs fájlhoz való hozzáférést az AK-ban](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC for Kubernetes-engedélyezés (előzetes verzió)

Az Azure RBAC-integrációja révén az AK egy Kubernetes-hitelesítési webhook-kiszolgálót használ, így az Azure AD-hez integrált Kubernetes-fürterőforrás-engedélyeket és-hozzárendeléseket az Azure szerepkör-definíció és a szerepkör-hozzárendelések segítségével kezelheti.

![Azure-RBAC az Kubernetes engedélyezési folyamatához](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Ahogy a fenti ábrán is látható, az Azure RBAC-integráció használatakor a Kubernetes API-nak küldött összes kérelem ugyanazt a hitelesítési folyamatot fogja követni, ahogy az a [Azure Active Directory Integration (integráció) szakaszban](#azure-ad-integration)is szerepel. 

Ha a kérést az Azure AD-ben megjelenő identitás már létezik, az Azure a Kubernetes RBAC-vel együttműködve engedélyezi a kérést. Ha az identitás az Azure AD-n kívül van (azaz egy Kubernetes-szolgáltatásfiók), az engedélyezés megakadályozza a normál Kubernetes-RBAC.

Ebben az esetben az Azure RBAC-mechanizmusok és API-k segítségével a felhasználók beépített szerepköröket rendelhet hozzá, vagy egyéni szerepköröket hozhat létre, ugyanúgy, mint a Kubernetes-szerepkörökkel. 

Ezzel a szolgáltatással nem csak a felhasználók jogosultak engedélyeket az AK-erőforrásra az előfizetések között, de a szerepköröket és engedélyeket is konfigurálja a Kubernetes API-hozzáférését vezérlő ezen fürtökön belül. Például megadhatja a `Azure Kubernetes Service RBAC Viewer` szerepkört az előfizetés hatókörében. A szerepkör címzettjei az összes fürt összes Kubernetes-objektumát megtekinthetik és lekérhetik anélkül, hogy módosítani kellene őket.

> [!IMPORTANT]
> A funkció használata előtt engedélyeznie kell az Azure RBAC Kubernetes-engedélyezést. További részletekért és részletes útmutatásért kövesse az [Azure RBAC használata Kubernetes-hitelesítéshez](manage-azure-rbac.md) című útmutatót.

#### <a name="built-in-roles"></a>Beépített szerepkörök

Az AK a következő négy beépített szerepkört biztosítja. Hasonlóak a [Kubernetes beépített szerepköreihez](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) , néhány különbséggel, például a CRDs támogatásával. Tekintse meg az egyes [Azure-beli beépített szerepkörök](../role-based-access-control/built-in-roles.md)által engedélyezett műveletek teljes listáját.

| Szerepkör                                | Leírás  |
|-------------------------------------|--------------|
| Az Azure Kubernetes Service RBAC megjelenítője  | Lehetővé teszi a csak olvasási hozzáférést a névtérben lévő legtöbb objektum megtekintéséhez. <br> Nem engedélyezi a szerepkörök és a szerepkör-kötések megtekintését.<br> Nem engedélyezi a megtekintést `Secrets` . A tartalom olvasása `Secrets` lehetővé teszi `ServiceAccount` a névtérben lévő hitelesítő adatok elérését, ami lehetővé TENNÉ az API-hozzáférés használatát `ServiceAccount` a névtérben (a jogosultságok kiterjesztésének formája).  |
| Az Azure Kubernetes Service RBAC-írója | Olvasási/írási hozzáférést tesz lehetővé a névtér legtöbb objektumához. <br> Nem engedélyezi a szerepkörök és a szerepkör-kötések megtekintését és módosítását. <br> Lehetővé teszi a `Secrets` hüvelyek elérését és futtatását a névtér bármely ServiceAccount, így felhasználható a névtérben található bármely SERVICEACCOUNT API-hozzáférési szintjeinek megszerzésére. |
| Az Azure Kubernetes Service RBAC rendszergazdája  | A rendszergazdai hozzáférés engedélyezése a névtéren belül. <br> Írási/olvasási hozzáférés engedélyezése a névtér (vagy a fürt hatóköre) legtöbb erőforrásához, beleértve a szerepkörök és a szerepkör-kötések létrehozását a névtéren belül. <br> Az erőforrás-kvótához vagy a névtérhez való írási hozzáférés nem engedélyezett. |
| Azure Kubernetes Service RBAC-fürt rendszergazdája  | Lehetővé teszi a felügyelők számára, hogy bármilyen műveletet végezzenek bármilyen erőforráson. <br> Teljes körű vezérlést biztosít a fürt összes erőforrásához és az összes névtérhez. |


## <a name="summary"></a>Összefoglalás

Tekintse meg a táblázatot, amelyben gyorsan összefoglalhatja, hogy a felhasználók hogyan tudják hitelesíteni magukat a Kubernetes, ha engedélyezve van az Azure AD-integráció. A felhasználói parancsok minden esetben a következőket tartalmazzák:
1. `az login`Az Azure-ban való hitelesítéshez futtassa a parancsot.
1. Futtassa `az aks get-credentials` a parancsot a fürthöz tartozó hitelesítő adatok letöltéséhez a alkalmazásban `.kube/config` .
1. `kubectl`Parancsok futtatása. 
   * Az első parancs a következő táblázatban leírtak szerint engedélyezheti a böngészőalapú hitelesítést a fürtön végzett hitelesítéshez.

A Azure Portal a következőket találja:
* A második oszlopban hivatkozott *szerepkör-engedélyezési* (Azure RBAC-szerepkör engedélyezése) megjelenik a **Access Control** lapon. 
* A fürt rendszergazdája Azure AD-csoport a **konfiguráció** lapon jelenik meg.
  * A paraméter neve is megtalálható `--aad-admin-group-object-ids` Az Azure CLI-ben.


| Description        | Szerepkör megadása kötelező| Fürt rendszergazdai Azure AD-csoport (ok) | A következő esetekben használja |
| -------------------|------------|----------------------------|-------------|
| Örökölt rendszergazdai bejelentkezés ügyféltanúsítvány használatával| Az **Azure Kubernetes rendszergazdai szerepköre**. Ez a szerepkör lehetővé teszi `az aks get-credentials` a jelzővel való használatot `--admin` , amely egy [örökölt (nem Azure ad-) fürt rendszergazdai tanúsítványát](control-kubeconfig-access.md) tölti le a felhasználó számára `.kube/config` . Ez az egyetlen célja az "Azure Kubernetes-rendszergazdai szerepkör".|n.a.|Ha véglegesen letiltja azt, hogy nem fér hozzá a fürthöz hozzáféréssel rendelkező érvényes Azure AD-csoporthoz.| 
| Azure AD manuális (fürt) RoleBindings| Az **Azure Kubernetes felhasználói szerepköre**. A "user" szerepkör lehetővé teszi a `az aks get-credentials` jelző nélküli használatot `--admin` . (Ez az egyetlen célja az "Azure Kubernetes felhasználói szerepkör".) Ennek eredményeképpen egy Azure AD-kompatibilis fürtön [egy üres bejegyzés](control-kubeconfig-access.md) tölthető le `.kube/config` , amely a böngészőalapú hitelesítést indítja el, amikor először használja `kubectl` .| A felhasználó nem szerepel ezen csoportok egyikében sem. Mivel a felhasználó nem tagja a fürt rendszergazdai csoportjainak, a jogosultságokat teljes mértékben a RoleBindings vagy ClusterRoleBindings vezérli. A (fürt) RoleBindings az [Azure ad-felhasználókat vagy az Azure ad-csoportokat jelölik](azure-ad-rbac.md) `subjects` . Ha nincsenek beállítva ilyen kötések, a felhasználó nem fog tudni Excute egyetlen `kubectl` parancsot sem.|Ha részletes hozzáférés-vezérlést szeretne, és nem használja az Azure RBAC-t a Kubernetes engedélyezéséhez. Vegye figyelembe, hogy a kötéseket beállító felhasználónak a táblázatban felsorolt más módszerek egyikével kell bejelentkeznie.|
| Azure AD a rendszergazda csoport tagjaként| Lásd fent|A felhasználó az itt felsorolt csoportok egyikének tagja. Az AK automatikusan létrehoz egy ClusterRoleBinding, amely az összes felsorolt csoportot a `cluster-admin` Kubernetes szerepkörhöz köti. Az ezekben a csoportokban lévő felhasználók az összes parancsot is futtathatják `kubectl` `cluster-admin` .|Ha a felhasználók számára teljes körű rendszergazdai jogosultságot szeretne biztosítani, és _nem_ használja az Azure RBAC-t a Kubernetes engedélyezéséhez.|
| Azure AD az Azure RBAC Kubernetes-engedélyezéshez|Két szerepkör: <br> Első lépésként az **Azure Kubernetes felhasználói szerepköre** (a fentiek szerint). <br> Másodszor, az egyik "Azure Kubernetes Service **RBAC**..." a fent felsorolt szerepkörök vagy a saját egyéni alternatívája.|A konfiguráció lap rendszergazdai szerepkörök mezője nem releváns, ha engedélyezve van az Azure-RBAC az Kubernetes-hitelesítéshez.|Azure RBAC-t használ a Kubernetes engedélyezéséhez. Ez a megközelítés részletes szabályozást biztosít anélkül, hogy be kellene állítania a RoleBindings vagy a ClusterRoleBindings.|

## <a name="next-steps"></a>Következő lépések

- Az Azure AD és a Kubernetes RBAC megismeréséhez tekintse meg a következőt: [Azure Active Directory integrálása az AK-val][aks-aad].
- A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][operator-best-practices-identity].
- Az Azure RBAC Kubernetes-hitelesítéssel való használatának megkezdéséhez lásd: az Azure [RBAC használata az Azure Kubernetes Service (ak) fürtön belüli hozzáférés engedélyezéséhez](manage-azure-rbac.md).
- A fájl védelmének megkezdéséhez `kubeconfig` lásd: a [fürt konfigurációs fájljához való hozzáférés korlátozása](control-kubeconfig-access.md)

Az alapvető Kubernetes és az AK-fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Kubernetes/AK-fürtök és-munkaterhelések][aks-concepts-clusters-workloads]
- [Kubernetes/AK biztonság][aks-concepts-security]
- [Kubernetes/AK virtuális hálózatok][aks-concepts-network]
- [Kubernetes/AK-tároló][aks-concepts-storage]
- [Kubernetes/AK-skála][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
