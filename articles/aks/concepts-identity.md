---
title: Fogalmak – hozzáférés és identitás az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes szolgáltatás (ak) hozzáférését és identitását, beleértve a Azure Active Directory integrációt, a szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC), valamint a szerepköröket és kötéseket.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: dc1e54106e2f31c7390d784cba6f92cf775e963c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572688"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Hozzáférési és identitás-beállítások az Azure Kubernetes Service (AKS) szolgáltatáshoz

A hitelesítés, a hozzáférés/engedélyezés és a biztonságos Kubernetes-fürtök többféleképpen is megadhatók. A Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) használatával a felhasználók, csoportok és szolgáltatásfiókok hozzáférése csak a szükséges erőforrásokhoz biztosítható. Az Azure Kubernetes Service (ak) segítségével tovább növelheti a biztonsági és az engedélyezési struktúrát Azure Active Directory és az Azure RBAC használatával. Ezek a módszerek segítenek a fürt hozzáférésének biztonságossá tételében, és csak a minimálisan szükséges engedélyeket biztosítják a fejlesztők és a kezelők számára.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek segítséget nyújtanak az AK-ban lévő engedélyek hitelesítéséhez és hozzárendeléséhez.

## <a name="aks-service-permissions"></a>AK szolgáltatás engedélyei

Fürt létrehozásakor az AK létrehozza vagy módosítja azokat az erőforrásokat, amelyeket létre kell hoznia és futtatnia kell a fürtöt, például virtuális gépeket és hálózati adaptereket a fürtöt létrehozó felhasználó nevében. Ez az identitás különbözik a fürt személyazonossági engedélyével, amely a fürt létrehozásakor jön létre.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>A fürt engedélyeinek létrehozásához és üzemeltetéséhez szükséges identitás

A fürt létrehozásához és üzemeltetéséhez a következő engedélyekre van szükség.

| Engedély | Ok |
|---|---|
| Microsoft. számítás/diskEncryptionSets/olvasás | A lemez titkosítási készletének AZONOSÍTÓjának olvasásához szükséges. |
| Microsoft. számítás/proximityPlacementGroups/írás | A közelségi elhelyezési csoportok frissítéséhez szükséges. |
| Microsoft. Network/applicationGateways/READ <br/> Microsoft. Network/applicationGateways/Write <br/> Microsoft. Network/virtualNetworks/alhálózatok/csatlakozás/művelet | Az Application Gateway konfigurálásához és az alhálózat csatlakoztatásához szükséges. |
| Microsoft. Network/virtualNetworks/alhálózatok/csatlakozás/művelet | Egyéni VNET használata esetén az alhálózat hálózati biztonsági csoportjának konfigurálásához szükséges.|
| Microsoft. Network/nyilvános IP/csatlakozás/művelet <br/> Microsoft. Network/publicIPPrefixes/csatlakozás/művelet | A kimenő nyilvános IP-címek a standard Load Balancer való konfigurálásához szükséges. |
| Microsoft. OperationalInsights/munkaterületek/sharedkeys/olvasás <br/> Microsoft. OperationalInsights/munkaterületek/olvasás <br/> Microsoft. OperationsManagement/megoldások/írás <br/> Microsoft. OperationsManagement/megoldások/olvasás <br/> Microsoft. ManagedIdentity/userAssignedIdentities/hozzárendelés/művelet | Log Analytics munkaterületek létrehozásához és frissítéséhez, valamint a tárolók Azure-figyeléséhez szükséges. |

### <a name="aks-cluster-identity-permissions"></a>AK-fürt identitásának engedélyei

A következő engedélyeket használja az AK-fürt identitása, amelyet a rendszer létrehoz és társít az AK-fürthöz a fürt létrehozásakor. Az alábbi okokból minden engedély használatos:

| Engedély | Ok |
|---|---|
| Microsoft. Network/loadBalancers/delete <br/> Microsoft. Network/loadBalancers/READ <br/> Microsoft. Network/loadBalancers/Write | A terheléselosztó terheléselosztó-szolgáltatáshoz való konfigurálásához szükséges. |
| Microsoft. Network/nyilvános IP/delete <br/> Microsoft. Network/nyilvános IP/READ <br/> Microsoft. Network/nyilvános IP/Write | A terheléselosztó szolgáltatás nyilvános IP-címeinek megkereséséhez és konfigurálásához szükséges. |
| Microsoft. Network/nyilvános IP/csatlakozás/művelet | A terheléselosztó szolgáltatás nyilvános IP-címeinek konfigurálásához szükséges. |
| Microsoft. Network/networkSecurityGroups/READ <br/> Microsoft. Network/networkSecurityGroups/Write | A terheléselosztó szolgáltatás biztonsági szabályainak létrehozásához vagy törléséhez szükséges. |
| Microsoft. számítás/lemezek/törlés <br/> Microsoft. számítás/lemezek/olvasás <br/> Microsoft. számítás/lemezek/írás <br/> Microsoft. számítás/helyszínek/kiírások/olvasás | A AzureDisks konfigurálásához szükséges. |
| Microsoft. Storage/storageAccounts/delete <br/> Microsoft. Storage/storageAccounts/Listkeys műveletének beolvasása/művelet <br/> Microsoft. Storage/storageAccounts/olvasás <br/> Microsoft. Storage/storageAccounts/írás <br/> Microsoft. Storage/Operations/READ | A AzureFile vagy AzureDisk tartozó Storage-fiókok konfigurálásához szükséges. |
| Microsoft. Network/routeTables/READ <br/> Microsoft. Network/routeTables/Routes/delete <br/> Microsoft. Network/routeTables/Routes/READ <br/> Microsoft. Network/routeTables/Routes/Write <br/> Microsoft. Network/routeTables/Write | A csomópontok útválasztási tábláinak és útvonalának konfigurálásához szükséges. |
| Microsoft. számítás/virtualMachines/olvasás | Egy VMAS található virtuális gépek információinak megkereséséhez szükséges, például a zónák, a tartalék tartomány, a méret és az adatlemezek. |
| Microsoft. számítás/virtualMachines/írás | Egy VMAS lévő virtuális géphez való AzureDisks csatolásához szükséges. |
| Microsoft. számítás/virtualMachineScaleSets/olvasás <br/> Microsoft. számítás/virtualMachineScaleSets/virtualMachines/olvasás <br/> Microsoft. számítás/virtualMachineScaleSets/virtualmachines/instanceView/READ | A virtuálisgép-méretezési csoportokban lévő virtuális gépek információinak megkereséséhez szükséges, például a zónák, a tartalék tartomány, a méret és az adatlemezek. |
| Microsoft. Network/networkInterfaces/Write | Ahhoz szükséges, hogy egy VMAS egy virtuális gépet egy terheléselosztó háttérbeli címkészlet számára adjon hozzá. |
| Microsoft. számítás/virtualMachineScaleSets/írás | Egy virtuálisgép-méretezési csoport terheléselosztásához szükséges a virtuálisgép-méretezési csoportokban, és bővíteni kell a csomópontokat. |
| Microsoft. számítás/virtualMachineScaleSets/virtualmachines/írás | A AzureDisks csatolásához és egy virtuális gép virtuálisgép-méretezési csoportból a terheléselosztó számára való hozzáadásához szükséges. |
| Microsoft. Network/networkInterfaces/READ | A VMAS virtuális gépei számára a belső IP-címek és a terheléselosztó háttér-címkészlet esetében szükséges. |
| Microsoft. számítás/virtualMachineScaleSets/virtualMachines/networkInterfaces/READ | A virtuálisgép-méretezési csoportokban lévő virtuális gépekhez tartozó belső IP-címek és terheléselosztó háttér-címkészlet számára szükséges. |
| Microsoft. számítás/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/nyilvános IP/READ | A virtuálisgép-méretezési csoportokban lévő virtuális gépek nyilvános IP-címeinek megkereséséhez szükséges. |
| Microsoft. Network/virtualNetworks/READ <br/> Microsoft. Network/virtualNetworks/alhálózatok/olvasás | Szükséges annak ellenőrzéséhez, hogy létezik-e alhálózat a belső terheléselosztó számára egy másik erőforráscsoporthoz. |
| Microsoft. számítás/Pillanatképek/törlés <br/> Microsoft. számítás/Pillanatképek/olvasás <br/> Microsoft. számítás/Pillanatképek/írás | A AzureDisk Pillanatképek konfigurálásához szükséges. |
| Microsoft. számítás/helyszínek/méreteinek listáján/olvasás <br/> Microsoft. számítás/helyszínek/műveletek/olvasás | A virtuálisgép-méretek megkereséséhez szükséges a AzureDisk. |

### <a name="additional-cluster-identity-permissions"></a>További fürt-identitási engedélyek

Az alábbi további engedélyek szükségesek a fürt identitása számára, ha adott attribútumokkal rendelkező fürtöt hoz létre. Ezek az engedélyek nem lesznek automatikusan hozzárendelve, ezért ezeket az engedélyeket a létrehozás után hozzá kell adni a fürt identitásához.

| Engedély | Ok |
|---|---|
| Microsoft. Network/networkSecurityGroups/Write <br/> Microsoft. Network/networkSecurityGroups/READ | Akkor szükséges, ha egy másik erőforráscsoport hálózati biztonsági csoportját használja. A terheléselosztó szolgáltatás biztonsági szabályainak konfigurálásához szükséges. |
| Microsoft. Network/virtualNetworks/alhálózatok/olvasás <br/> Microsoft. Network/virtualNetworks/alhálózatok/csatlakozás/művelet | Kötelező, ha egy alhálózatot használ egy másik erőforráscsoporthoz, például egy egyéni VNET. |
| Microsoft. Network/routeTables/Routes/READ <br/> Microsoft. Network/routeTables/Routes/Write | Kötelező, ha egy másik erőforráscsoport útválasztási táblájához társított alhálózatot használ, például egy egyéni VNET egyéni útválasztási táblázattal. Szükséges annak ellenőrzéséhez, hogy egy alhálózat már létezik-e a másik erőforráscsoport alhálózatához. |
| Microsoft. Network/virtualNetworks/alhálózatok/olvasás | A belső terheléselosztó egy másik erőforráscsoporthoz való használata esetén kötelező. Szükséges annak ellenőrzéséhez, hogy már létezik-e alhálózat a belső terheléselosztó számára az erőforráscsoporthoz. |

## <a name="kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC)

A felhasználók által elvégezhető műveletek részletes szűrésének biztosításához a Kubernetes a Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC) használja. Ez a vezérlési mechanizmus lehetővé teszi a felhasználók vagy felhasználói csoportok hozzárendelését, például az erőforrások létrehozását és módosítását, illetve a naplók megtekintését az alkalmazás-munkaterhelések futtatásához. Ezek az engedélyek egyetlen névtérre is érvényesek, vagy a teljes AK-fürtön keresztül is megadhatók. A Kubernetes RBAC segítségével létrehozhat *szerepköröket* az engedélyek definiálásához, majd hozzárendelheti ezeket a szerepköröket a felhasználókhoz *szerepkör-kötésekkel*.

További információ: [a KUBERNETES RBAC-hitelesítés használata][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Szerepkörök és ClusterRoles

Mielőtt engedélyeket rendel a felhasználókhoz a Kubernetes RBAC, először az engedélyeket *szerepkörként* kell megadnia. A Kubernetes szerepkörei engedélyeket *biztosítanak* . Nincs a *megtagadási* engedély fogalma.

A szerepkörök a névtéren belüli engedélyek megadására szolgálnak. Ha engedélyeket kell megadnia a teljes fürtön, vagy az erőforrásokat egy adott névtéren kívül kell megadnia, akkor Ehelyett használhatja a *ClusterRoles*.

A ClusterRole ugyanúgy működik, hogy engedélyeket biztosítson az erőforrásokhoz, de a teljes fürtön lévő erőforrásokra, nem pedig egy adott névtérre is alkalmazható.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings és ClusterRoleBindings

Ha a szerepkörök úgy vannak meghatározva, hogy engedélyeket adjanak az erőforrásoknak, akkor ezeket a Kubernetes RBAC engedélyeket egy *RoleBinding* kell rendelnie. Ha az AK-fürt [integrálva van Azure Active Directory (Azure ad) szolgáltatással](#azure-active-directory-integration), a kötések azt jelentik, hogy az Azure ad-felhasználók hogyan kapnak engedélyeket a fürtön belüli műveletek végrehajtásához: Hogyan [vezérelheti a hozzáférést a Kubernetes a szerepköralapú hozzáférés-vezérlés és a Azure Active Directory identitások használatával](azure-ad-rbac.md).

A szerepkör-kötések egy adott névtér szerepköreinek hozzárendelésére szolgálnak. Ez a megközelítés lehetővé teszi, hogy logikailag elkülönítse egyetlen AK-beli fürtöt, és a felhasználók csak a hozzárendelt névtérben lévő alkalmazás-erőforrásokat tudják elérni. Ha a szerepköröket a teljes fürtön kell megkötnie, vagy egy adott névtéren kívüli fürt erőforrásaira van szüksége, használhatja a *ClusterRoleBindings*.

A ClusterRoleBinding ugyanúgy működik, mint a szerepkörök felhasználókhoz való kötése, de a teljes fürtön lévő erőforrásokra, nem pedig egy adott névtérre is alkalmazhatók. Ez a megközelítés lehetővé teszi, hogy a rendszergazdák vagy a támogatási mérnökök hozzáférjenek az AK-fürt összes erőforrásához.


> [!NOTE]
> A Microsoft/AK által végrehajtott összes művelet felhasználói beleegyezett a beépített Kubernetes `aks-service` -szerepkör és a beépített szerepkör-kötés alá `aks-service-rolebinding` . Ez a szerepkör lehetővé teszi az AK számára a fürtökkel kapcsolatos problémák hibaelhárítását és diagnosztizálását, de nem módosíthatja az engedélyeket, és nem hozhat létre szerepköröket vagy szerepkör-kötéseket, illetve egyéb magas jogosultsági A szerepkör-hozzáférés csak az aktív támogatási jegyek esetében engedélyezett az igény szerinti (JIT) hozzáféréssel. Tudjon meg többet az [AK-támogatási szabályzatokról](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Kubernetes-szolgáltatásfiókok

A Kubernetes egyik elsődleges felhasználói típusa egy *szolgáltatásfiók*. Egy szolgáltatásfiók létezik a-ben, amelyet a a Kubernetes API felügyel. A szolgáltatásfiókok hitelesítő adatait a rendszer Kubernetes titokként tárolja, ami lehetővé teszi számukra, hogy az engedélyezett hüvelyek használják az API-kiszolgálóval való kommunikációt. A legtöbb API-kérelem hitelesítési jogkivonatot biztosít egy szolgáltatásfiók vagy egy normál felhasználói fiók számára.

A normál felhasználói fiókok több hagyományos hozzáférést biztosítanak az emberi rendszergazdáknak és a fejlesztőknek, nem csupán szolgáltatásoknak és folyamatoknak. A Kubernetes önmagában nem biztosít Identitáskezelés-kezelési megoldást, ahol a rendszer normál felhasználói fiókokat és jelszavakat tárol. Ehelyett a külső identitási megoldások integrálhatók a Kubernetes-be. AK-fürtök esetében ez az integrált Identity megoldás Azure Active Directory.

További információ a Kubernetes identitási lehetőségeiről: Kubernetes- [hitelesítés][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integráció

Az AK-fürtök biztonsága javítható Azure Active Directory (AD) integrálásával. Az Azure AD egy több-bérlős, felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatás, amely az alapszintű címtárszolgáltatások, az alkalmazások és az identitások védelmét ötvözi. Az Azure AD lehetővé teszi, hogy a helyszíni identitásokat AK-fürtökbe integrálva egyetlen forrást biztosítson a fiókok kezeléséhez és biztonságához.

![Azure Active Directory-integráció AK-fürtökkel](media/concepts-identity/aad-integration.png)

Az Azure AD-vel integrált AK-fürtök segítségével a felhasználók vagy csoportok hozzáférést biztosíthatnak a Kubernetes-erőforrásokhoz a névtérben vagy a fürtön belül. A konfigurációs környezet beszerzéséhez a `kubectl` felhasználó az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot futtathatja. Ha a felhasználó ezt követően együttműködik az AK-fürttel `kubectl` , a rendszer felszólítja, hogy jelentkezzen be az Azure ad-beli hitelesítő adataival. Ez a megközelítés egyetlen forrást biztosít a felhasználói fiókok felügyeletéhez és a jelszó hitelesítő adataihoz. A felhasználó csak a fürt rendszergazdája által meghatározott erőforrásokat érheti el.

Az Azure AD-hitelesítés az OpenID-kapcsolattal rendelkező AK-fürtökhöz van megadva. Az OpenID Connect egy OAuth 2,0 protokollra épülő identitási réteg. Az OpenID Connecttel kapcsolatos további információkért tekintse meg az [ID Connect dokumentációját][openid-connect]. A Kubernetes-fürtön belül a rendszer [webhook jogkivonat-hitelesítést][webhook-token-docs] használ a hitelesítési tokenek ellenőrzéséhez. A webhook-jogkivonat hitelesítése az AK-fürt részeként van konfigurálva és felügyelve.

### <a name="webhook-and-api-server"></a>Webhook és API-kiszolgáló

![Webhook és API-kiszolgáló hitelesítési folyamata](media/concepts-identity/auth-flow.png)

Ahogy az a fenti ábrán is látható, az API-kiszolgáló meghívja az AK webhook-kiszolgálót, és a következő lépéseket hajtja végre:

1. Az Azure AD ügyfélalkalmazás a kubectl használatával jelentkezik be a OAuth 2,0-es [eszköz-engedélyezési folyamattal](../active-directory/develop/v2-oauth2-device-code.md)rendelkező felhasználókba.
2. Az Azure AD egy access_token, id_token és egy refresh_token biztosít.
3. A felhasználó kérést küld a kubectl access_token a kubeconfig.
4. A Kubectl az API-kiszolgálónak küldi a access_token.
5. Az API-kiszolgáló a hitelesítési webhook-kiszolgálóval van konfigurálva az érvényesítés végrehajtásához.
6. A hitelesítési webhook-kiszolgáló megerősíti, hogy a JSON Web Token aláírása érvényes az Azure AD nyilvános aláíró kulcsának ellenőrzésével.
7. A kiszolgálóalkalmazás felhasználó által megadott hitelesítő adatok használatával kérdezi le a bejelentkezett felhasználó csoporttagságait az MS Graph APIból.
8. A rendszer választ kap az API-kiszolgálónak a felhasználói adatokkal, például a hozzáférési jogkivonat egyszerű felhasználóneve (UPN) jogcímével, valamint a felhasználó csoporttagság alapján.
9. Az API a Kubernetes szerepkör/RoleBinding alapján hajt végre engedélyezési döntést.
10. Az engedélyezést követően az API-kiszolgáló egy választ küld a kubectl.
11. A Kubectl visszajelzést nyújt a felhasználónak.
 
**Ismerje meg, hogyan integrálhatja az AK-t [a HRE-](managed-aad.md)mel.**

## <a name="azure-role-based-access-control-azure-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)

Az Azure RBAC egy [Azure Resource Managerra](../azure-resource-manager/management/overview.md) épülő engedélyezési rendszer, amely részletes hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz.

 Az Azure RBAC úgy van kialakítva, hogy az Azure-előfizetésen belüli erőforrásokkal működjön, miközben a Kubernetes RBAC úgy van kialakítva, hogy az AK-fürtön belüli Kubernetes 

Az Azure RBAC létrehoz egy szerepkör- *definíciót* , amely az alkalmazandó engedélyeket ismerteti. A felhasználó vagy csoport ezt követően egy adott *hatókörhöz* tartozó *szerepkör-hozzárendeléssel* rendeli hozzá ezt a szerepkör-definíciót, amely lehet önálló erőforrás, erőforráscsoport vagy az előfizetés egésze.

További információ: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?][azure-rbac]

Az AK-fürtök teljes körű működtetéséhez két hozzáférési szint szükséges: 
1. [Hozzáférés az AK-erőforráshoz az Azure-előfizetésében](#azure-rbac-to-authorize-access-to-the-aks-resource). Ezzel a folyamattal vezérelheti a fürtök méretezését vagy frissítését az AK API-k használatával, valamint a kubeconfig lekérésével.
2. Hozzáférés a Kubernetes API-hoz. Ezt a hozzáférést a [KUBERNETES RBAC](#kubernetes-role-based-access-control-kubernetes-rbac) (hagyományos) vagy az [Azure RBAC és a Kubernetes-hitelesítés integrálásával](#azure-rbac-for-kubernetes-authorization-preview) vezérelheti

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure-RBAC az AK-erőforráshoz való hozzáférés engedélyezéséhez

Az Azure RBAC segítségével megadhatja a felhasználók (vagy identitások) számára az AK-erőforrások részletes elérését egy vagy több előfizetésben. Előfordulhat például, hogy az [Azure Kubernetes szolgáltatás közreműködői szerepköre](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) lehetővé teszi, hogy olyan műveleteket hajtson végre, mint a fürt skálázása és frissítése. Míg egy másik felhasználó rendelkezhet az [Azure Kubernetes Service cluster rendszergazdai szerepkörrel](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) , amely csak engedélyt ad a rendszergazdai kubeconfig lekérésére.

Másik lehetőségként megadhatja a felhasználónak az általános [közreműködő](../role-based-access-control/built-in-roles.md#contributor) szerepkört is, amely magában foglalja a fenti engedélyeket és minden lehetséges műveletet az AK-erőforráson, kivéve az engedélyek felügyeletét.

További információ az Azure RBAC használatával a kubeconfig-fájlhoz való hozzáférés biztonságossá tételéhez, amely hozzáférést biztosít a Kubernetes [API-](control-kubeconfig-access.md)hoz.

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC for Kubernetes-engedélyezés (előzetes verzió)

Az Azure RBAC-integrációjában az AK egy Kubernetes-hitelesítési webhook-kiszolgálót használ, amely lehetővé teszi az Azure AD-hez integrált K8s-fürterőforrások engedélyeinek és hozzárendeléseinek az Azure szerepkör-definíció és a szerepkör-hozzárendelések használatával történő kezelését.

![Azure-RBAC az Kubernetes engedélyezési folyamatához](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Ahogy az a fenti ábrán is látható, az Azure RBAC-integráció használatakor a Kubernetes API-nak küldött összes kérelem ugyanezt a hitelesítési folyamatot fogja követni, ahogy az a [Azure Active Directory Integration (integráció) szakaszban](#azure-active-directory-integration)is látható. 

De ezt követően, ahelyett, hogy kizárólag az Kubernetes RBAC-ra támaszkodik, a kérést az Azure engedélyezi, feltéve, hogy a kérést használó identitás létezik a HRE-ben. Ha az identitás nem létezik a HRE, például egy Kubernetes-szolgáltatásfiók, akkor az Azure-RBAC nem indul el, és ez lesz a normál Kubernetes RBAC.

Ebben a forgatókönyvben az egyik négy beépített szerepkört is megadhatja a felhasználóknak, vagy létrehozhat egyéni szerepköröket, mint a Kubernetes szerepköreivel, de ebben az esetben az Azure RBAC-mechanizmusok és API-k használatával. 

Ez a funkció lehetővé teszi, hogy például ne csak a felhasználók számára engedélyezze az AK-erőforrást az előfizetések között, de be kell állítania és hozzá kell adni azokat a szerepköröket és engedélyeket, amelyek a Kubernetes API-hoz való hozzáférést vezérlik a fürtben. Például megadhatja a `Azure Kubernetes Service RBAC Viewer` szerepkört az előfizetés hatókörében, és a címzettje minden fürtből listázhatja és lekérheti az összes Kubernetes objektumot, de nem módosíthatja azokat.

> [!IMPORTANT]
> Vegye figyelembe, hogy a funkció használata előtt engedélyeznie kell az Azure RBAC Kubernetes-engedélyezést. További részletekért és részletes útmutatásért [lásd itt](manage-azure-rbac.md).

#### <a name="built-in-roles"></a>Beépített szerepkörök

Az AK a következő négy beépített szerepkört biztosítja. Hasonlóak a [Kubernetes beépített szerepköreihez](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) , de néhány különbséggel, például a CRDs támogatásával. Az egyes beépített szerepkörök által engedélyezett műveletek teljes listáját [itt](../role-based-access-control/built-in-roles.md)találja.

| Szerepkör                                | Leírás  |
|-------------------------------------|--------------|
| Az Azure Kubernetes Service RBAC megjelenítője  | Lehetővé teszi a csak olvasási hozzáférést a névtérben lévő legtöbb objektum megtekintéséhez. Nem teszi lehetővé a szerepkörök és a szerepkör-kötések megtekintését. Ez a szerepkör nem teszi lehetővé a megtekintést `Secrets` , mivel a titkok tartalmának olvasása lehetővé teszi `ServiceAccount` a névtérben lévő hitelesítő adatok elérését, ami lehetővé TENNÉ az API `ServiceAccount` -k elérését a névtérben (a jogosultságok eszkalációjának formája).  |
| Az Azure Kubernetes Service RBAC-írója | Olvasási/írási hozzáférést tesz lehetővé a névtér legtöbb objektumához. Ez a szerepkör nem teszi lehetővé a szerepkörök vagy szerepkör-kötések megtekintését és módosítását. Ez a szerepkör azonban lehetővé teszi a `Secrets` hüvelyek elérését és futtatását a névtér bármely ServiceAccount, így felhasználható a névtérben található bármely SERVICEACCOUNT API-hozzáférési szintjeinek megszerzésére. |
| Az Azure Kubernetes Service RBAC rendszergazdája  | A rendszergazdai hozzáférés engedélyezése a névtéren belül. Írási/olvasási hozzáférés engedélyezése a névtér (vagy a fürt hatóköre) legtöbb erőforrásához, beleértve a szerepkörök és a szerepkör-kötések létrehozását a névtéren belül. Ez a szerepkör nem engedélyez írási hozzáférést az erőforrás-kvótához vagy magához a névtérhez. |
| Azure Kubernetes Service RBAC-fürt rendszergazdája  | Lehetővé teszi a felügyelők számára, hogy bármilyen műveletet végezzenek bármilyen erőforráson. Teljes hozzáférést biztosít a fürt összes erőforrásához és az összes névtérhez. |


## <a name="summary"></a>Összefoglalás

Ez a táblázat összefoglalja, hogy a felhasználók hogyan hitelesíthetők a Kubernetes, ha engedélyezve van az Azure AD-integráció.  A felhasználói parancsok minden esetben a következőket tartalmazzák:
1. `az login`Az Azure-ban való hitelesítéshez futtassa a parancsot.
1. Futtassa `az aks get-credentials` a parancsot a fürthöz tartozó hitelesítő adatok letöltéséhez a alkalmazásban `.kube/config` .
1. Futtassa a `kubectl` parancsokat (amelyek közül az első az alábbi táblázatban leírtak szerint a fürtön való hitelesítéshez a böngésző alapú hitelesítést indítja el).

A második oszlopban szereplő szerepkör-támogatás a Azure Portal **Access Control** lapján látható Azure RBAC szerepkör-támogatás. A fürt rendszergazdája Azure AD-csoport a portál **konfiguráció** lapján (vagy az Azure CLI-ben található paraméter nevével) jelenik meg `--aad-admin-group-object-ids` .

| Description        | Szerepkör megadása kötelező| Fürt rendszergazdai Azure AD-csoport (ok) | A következő esetekben használja |
| -------------------|------------|----------------------------|-------------|
| Örökölt rendszergazdai bejelentkezés ügyféltanúsítvány használatával| Az **Azure Kubernetes rendszergazdai szerepköre**. Ez a szerepkör lehetővé teszi `az aks get-credentials` a jelzővel való használatot `--admin` , amely egy [örökölt (nem Azure ad-) fürt rendszergazdai tanúsítványát](control-kubeconfig-access.md) tölti le a felhasználó számára `.kube/config` . Ez az egyetlen célja az "Azure Kubernetes-rendszergazdai szerepkör".|n.a.|Ha véglegesen letiltja azt, hogy nem fér hozzá a fürthöz hozzáféréssel rendelkező érvényes Azure AD-csoporthoz.| 
| Azure AD manuális (fürt) RoleBindings| Az **Azure Kubernetes felhasználói szerepköre**. A "user" szerepkör lehetővé teszi a `az aks get-credentials` jelző nélküli használatot `--admin` . (Ez az egyetlen célja az "Azure Kubernetes felhasználói szerepkör".) Ennek eredményeképpen egy Azure AD-kompatibilis fürtön [egy üres bejegyzés](control-kubeconfig-access.md) tölthető le `.kube/config` , amely a böngészőalapú hitelesítést indítja el, amikor először használja `kubectl` .| A felhasználó nem szerepel ezen csoportok egyikében sem. Mivel a felhasználó nem tagja a fürt rendszergazdai csoportjainak, a jogosultságokat teljes mértékben a RoleBindings vagy ClusterRoleBindings vezérli. A (fürt) RoleBindings az [Azure ad-felhasználókat vagy az Azure ad-csoportokat jelölik](azure-ad-rbac.md) `subjects` . Ha nincsenek beállítva ilyen kötések, a felhasználó nem fog tudni Excute egyetlen `kubectl` parancsot sem.|Ha részletes hozzáférés-vezérlést szeretne, és nem használja az Azure RBAC-t a Kubernetes engedélyezéséhez. Vegye figyelembe, hogy a kötéseket beállító felhasználónak a táblázatban felsorolt más módszerek egyikével kell bejelentkeznie.|
| Azure AD a rendszergazda csoport tagjaként| Lásd fent|A felhasználó az itt felsorolt csoportok egyikének tagja. Az AK automatikusan létrehoz egy ClusterRoleBinding, amely az összes felsorolt csoportot a `cluster-admin` Kubernetes szerepkörhöz köti. Az ezekben a csoportokban lévő felhasználók az összes parancsot is futtathatják `kubectl` `cluster-admin` .|Ha a felhasználók számára teljes körű rendszergazdai jogosultságot szeretne biztosítani, és _nem_ használja az Azure RBAC-t a Kubernetes engedélyezéséhez.|
| Azure AD az Azure RBAC Kubernetes-engedélyezéshez|Két szerepkör: először az **Azure Kubernetes felhasználói szerepköre** (a fentiek szerint). Másodszor, az egyik "Azure Kubernetes Service **RBAC**..." a fent felsorolt szerepkörök vagy a saját egyéni alternatívája.|A konfiguráció lap rendszergazdai szerepkörök mezője nem releváns, ha engedélyezve van az Azure-RBAC az Kubernetes-hitelesítéshez.|Azure RBAC-t használ a Kubernetes engedélyezéséhez. Ez a megközelítés részletes szabályozást biztosít anélkül, hogy be kellene állítania a RoleBindings vagy a ClusterRoleBindings.|

## <a name="next-steps"></a>Következő lépések

- Az Azure AD és a Kubernetes RBAC megismeréséhez tekintse meg a következőt: [Azure Active Directory integrálása az AK-val][aks-aad].
- A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][operator-best-practices-identity].
- Az Azure RBAC Kubernetes-hitelesítéssel való használatának megkezdéséhez lásd: az Azure [RBAC használata az Azure Kubernetes Service (ak) fürtön belüli hozzáférés engedélyezéséhez](manage-azure-rbac.md).
- A kubeconfig-fájl védelmének megkezdéséhez lásd: [a fürt konfigurációs fájljához való hozzáférés korlátozása](control-kubeconfig-access.md)

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
