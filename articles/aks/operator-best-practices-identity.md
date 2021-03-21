---
title: Ajánlott eljárások az identitás kezeléséhez
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az Azure Kubernetes Service-ben (ak) lévő fürtök hitelesítésének és engedélyezésének kezelésével kapcsolatos ajánlott eljárásokat.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 8e0c7324f5b73b3a2ac5e5fd6fa256202035077a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790969"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a hitelesítéshez és az engedélyezéshez az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes szolgáltatásban (ak) lévő fürtök üzembe helyezése és karbantartása során meg kell valósítania az erőforrásokhoz és szolgáltatásokhoz való hozzáférés felügyeletének módjait. A vezérlőelemek nélkül a fiókok hozzáférhetnek a nem szükséges erőforrásokhoz és szolgáltatásokhoz. Emellett nehéz lehet nyomon követni, hogy mely hitelesítő adatok lettek alkalmazva a módosítások elvégzésére.

Ez az ajánlott eljárás azt ismerteti, hogyan kezelhető a fürt operátora az AK-fürtök hozzáférésének és identitásának kezeléséhez. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * AK-fürt felhasználóinak hitelesítése Azure Active Directory
> * Erőforrásokhoz való hozzáférés szabályozása Kubernetes szerepköralapú hozzáférés-vezérléssel (Kubernetes RBAC)
> * Az Azure RBAC használatával részletesen szabályozhatja az AK-erőforráshoz és a Kubernetes API-hoz való hozzáférést a skálán, valamint a kubeconfig.
> * Felügyelt identitás használata a hüvelyek hitelesítéséhez más szolgáltatásokkal

## <a name="use-azure-active-directory"></a>Az Azure Active Directory használata

**Ajánlott eljárási útmutató** – AK-fürtök üzembe helyezése az Azure ad-integrációval. Az Azure AD központosítja az Identitáskezelés összetevőt. A felhasználói fiók vagy a csoport állapotának bármilyen módosítása automatikusan frissül az AK-fürthöz való hozzáférés során. Szerepkörök vagy ClusterRoles és kötések használata a következő szakaszban leírtak szerint a hatókörhöz tartozó felhasználók vagy csoportok számára a lehető legkevesebb engedélyre van szükség.

A Kubernetes-fürt fejlesztőinek és alkalmazás-tulajdonosainak különböző erőforrásokhoz kell hozzáférnie. A Kubernetes nem biztosít Identitáskezelés-kezelési megoldást annak szabályozására, hogy mely felhasználók kezelhetik az erőforrásokat. Ehelyett általában meglévő identitási megoldással integrálja a fürtöt. A Azure Active Directory (AD) egy nagyvállalati használatra kész Identitáskezelés megoldás, amely integrálható az AK-fürtökkel.

Az Azure AD-vel integrált, az AK-ban lévő fürtökkel olyan *szerepköröket* vagy *ClusterRoles* hozhat létre, amelyek hozzáférési engedélyeket határoznak meg az erőforrásokhoz. Ezután a szerepköröket a felhasználókhoz vagy csoportokhoz *kötheti* az Azure ad-ben. Ezeket a Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC) a következő szakaszban tárgyaljuk. Az Azure AD integrációja és az erőforrásokhoz való hozzáférés szabályozása az alábbi ábrán látható:

![Fürt szintű hitelesítés Azure Active Directory AK-val való integrációhoz](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. A fejlesztői hitelesítés az Azure AD-vel.
1. Az Azure AD-jogkivonat kiállítási végpontja kiadja a hozzáférési jogkivonatot.
1. A fejlesztő az Azure AD-tokent használó műveletet hajt végre, például `kubectl create pod`
1. A Kubernetes érvényesíti a jogkivonatot Azure Active Directory és beolvassa a fejlesztő csoporttagságait.
1. A Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC) és a fürt házirendjeit alkalmazza a rendszer.
1. A fejlesztő kérelme sikeres, vagy nem az Azure AD-csoporttagság és-Kubernetes RBAC és-szabályzatok korábbi ellenőrzése alapján.

Az Azure AD-t használó AK-fürtök létrehozásával kapcsolatban lásd: [Azure Active Directory integrálása az AK-val][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes szerepköralapú hozzáférés-vezérlés használata (Kubernetes RBAC)

**Ajánlott eljárások – útmutató** – a Kubernetes RBAC segítségével meghatározhatja, hogy a felhasználók vagy csoportok milyen engedélyekkel rendelkezzenek a fürt erőforrásaihoz. Hozzon létre olyan szerepköröket és kötéseket, amelyek a minimálisan szükséges engedélyeket rendelik hozzá. Integrálhatja az Azure AD-val, így a felhasználói állapot vagy a csoporttagság bármilyen változása automatikusan frissül, és a fürt erőforrásaihoz való hozzáférés aktuális.

A Kubernetes-ben a fürt erőforrásaihoz való hozzáférés részletes szabályozását is lehetővé teheti. Az engedélyek a fürt szintjén vagy adott névterekben vannak meghatározva. Megadhatja, hogy mely erőforrások kezelhetők, és milyen engedélyekkel rendelkezik. Ezeket a szerepköröket ezután a rendszer egy kötést használó felhasználókra vagy csoportokra alkalmazza. További információ a *szerepkörökről*, a *ClusterRoles* és a *kötésekről*: [Az Azure Kubernetes Service (ak) hozzáférési és identitási beállításai][aks-concepts-identity].

Például létrehozhat egy olyan szerepkört, amely teljes hozzáférést biztosít a *Finance-app* nevű névtérben található erőforrásokhoz, ahogyan az a következő PÉLDÁBAN látható YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Ekkor létrejön egy RoleBinding, amely az alábbi YAML-jegyzékben látható módon köti össze az Azure AD felhasználói *developer1 \@ contoso.com* a RoleBinding.

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Ha a *developer1- \@ contoso.com* hitelesítve van az AK-fürtön, teljes körű engedélyekkel rendelkeznek a *Pénzügy-alkalmazás* névtér erőforrásaihoz. Így logikailag elkülönítheti és szabályozhatja az erőforrásokhoz való hozzáférést. Az Kubernetes-RBAC az előző szakaszban tárgyalt Azure AD-integrációval együtt kell használni.

Ha szeretné megtudni, hogy az Azure AD-csoportok hogyan használhatják a Kubernetes-erőforrásokhoz való hozzáférést a Kubernetes RBAC használatával, tekintse meg a következőt: a [fürterőforrások hozzáférésének szabályozása szerepköralapú hozzáférés-vezérléssel és Azure Active Directory az AK-beli identitások használatával][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Az Azure RBAC használata 
**Ajánlott eljárási útmutató** – az Azure RBAC segítségével meghatározhatja azokat a minimálisan szükséges engedélyeket, amelyeknek a felhasználóknak vagy csoportoknak AK-beli erőforrásokra van szüksége egy vagy több előfizetésben.

Az AK-fürtök teljes körű működtetéséhez két hozzáférési szint szükséges: 
1. Hozzáférés az AK-erőforráshoz az Azure-előfizetésében. Ez a hozzáférési szint lehetővé teszi, hogy szabályozza a fürtök méretezését vagy frissítését az AK API-k használatával, valamint a kubeconfig lekérésével.
Ha szeretné megtudni, hogyan szabályozhatja az AK-erőforráshoz és a kubeconfig való hozzáférést, tekintse meg a [fürt konfigurációs fájljához való hozzáférés korlátozása](control-kubeconfig-access.md)című témakört.

2. Hozzáférés a Kubernetes API-hoz. Ezt a hozzáférési szintet a [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (hagyományos) vagy az Azure RBAC és a Kubernetes-hitelesítés integrálásával vezérli.
A Kubernetes API-nak az Azure RBAC használatával történő részletes ismertetését lásd: [Az Azure RBAC használata Kubernetes-hitelesítéshez](manage-azure-rbac.md).

## <a name="use-pod-managed-identities"></a>A pod által felügyelt identitások használata

**Ajánlott eljárási útmutató** – ne használjon rögzített hitelesítő adatokat a hüvelyeken vagy a tárolók rendszerképein belül, mivel azok a sugárterhelés vagy a visszaélések kockázatának vannak kitéve. Ehelyett a pod identitys használatával automatikusan kérhet hozzáférést egy központi Azure AD-identitási megoldás használatával. A pod-identitások csak a Linux-hüvelyek és a tároló-lemezképek használatára készültek.

> [!NOTE]
> A Windows-tárolók esetében a pod által felügyelt identitások támogatása hamarosan elérhető lesz.

Ha a hüvelynek más Azure-szolgáltatásokhoz (például Cosmos DBhoz, Key Vaulthoz vagy Blob Storagehoz is hozzá kell férnie, a pod-nak hozzáférési hitelesítő adatokra van szüksége. Ezeket a hozzáférési hitelesítő adatokat a tároló lemezképével vagy Kubernetes-titokként kell megadni, de manuálisan kell létrehozni és hozzárendelni. A hitelesítő adatok gyakran újra felhasználhatók a hüvelyek között, és a rendszer nem fordítja rendszeresen.

Az Azure-erőforrások Pod által felügyelt identitásai lehetővé teszi, hogy automatikusan kérjen hozzáférést a szolgáltatásokhoz az Azure AD-n keresztül. A pod által felügyelt identitások jelenleg előzetes verzióban érhetők el az Azure Kubernetes Service-ben. Az első lépésekhez tekintse meg a [Azure Active Directory Pod által felügyelt identitások használata az Azure Kubernetes Service-ben (előzetes verzió)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) dokumentációját. A pod által felügyelt identitások nem határozzák meg manuálisan a hüvelyek hitelesítő adatait, ehelyett hozzáférési tokent kérnek valós időben, és csak a hozzájuk rendelt szolgáltatások elérésére használhatók. Az AK-ban két összetevő van, amelyek kezelik a műveleteket, hogy a hüvelyek felügyelt identitásokat használjanak:

* **A Node Management Identity (NMI) kiszolgáló** egy olyan Pod, amely daemonset elemet fut az AK-fürt minden csomópontján. A NMI-kiszolgáló az Azure-szolgáltatásokhoz tartozó Pod-kérelmeket figyeli.
* **Az Azure erőforrás-szolgáltató** lekérdezi a Kubernetes API-kiszolgálót, és ellenőrzi a pod-nak megfelelő Azure Identity-hozzárendelést.

Ha a hüvely egy Azure-szolgáltatáshoz fér hozzá, a hálózati szabályok átirányítják a forgalmat a Node Management Identity (NMI) kiszolgálóra. A NMI-kiszolgáló azonosítja az Azure-szolgáltatásokhoz hozzáférést kérő hüvelyeket a távoli címük alapján, és lekérdezi az Azure erőforrás-szolgáltatót. Az Azure erőforrások-szolgáltató ellenőrzi az Azure Identity-leképezéseket az AK-fürtben, és a NMI-kiszolgáló ezt követően hozzáférési jogkivonatot kér a pod identitás-leképezése alapján Azure Active Directorytól (AD). Az Azure AD hozzáférést biztosít a NMI-kiszolgálóhoz, amelyet a rendszer visszaadott a pod-nek. Ezt a hozzáférési jogkivonatot a pod használhatja a szolgáltatásokhoz való hozzáféréshez az Azure-ban.

A következő példában egy fejlesztő létrehoz egy Pod-t, amely egy felügyelt identitást használ a Azure SQL Databasehoz való hozzáférés kéréséhez:

![A pod-identitások lehetővé teszik, hogy a pod automatikusan kérjen hozzáférést más szolgáltatásokhoz](media/operator-best-practices-identity/pod-identities.png)

1. A fürt operátora először létrehoz egy szolgáltatásfiókot, amely az identitások hozzárendelésére szolgál, amikor a hüvelyek a szolgáltatásokhoz való hozzáférést igényelnek.
1. A rendszer üzembe helyezi a NMI-kiszolgálót a pod-kérelmek továbbításához az Azure erőforrás-szolgáltatóval együtt, az Azure AD-hez való hozzáférési jogkivonatok esetén.
1. A fejlesztő olyan felügyelt identitással helyez üzembe egy Pod-t, amely hozzáférési jogkivonatot kér a NMI-kiszolgálón keresztül.
1. A rendszer visszaküldi a jogkivonatot a pod-nak, és hozzáfér Azure SQL Database

> [!NOTE]
> A pod által felügyelt identitások jelenleg előzetes verzió állapotban vannak.

A pod által felügyelt identitások használatához lásd: [Azure Active Directory Pod által felügyelt identitások használata az Azure Kubernetes Service-ben (előzetes verzió)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity).

## <a name="next-steps"></a>Következő lépések

Ez az ajánlott eljárásokat ismertető cikk a fürt és az erőforrások hitelesítésére és engedélyezésére koncentrál. Az ajánlott eljárások némelyikének megvalósításához tekintse meg a következő cikkeket:

* [Azure Active Directory integrálása AK-val][aks-aad]
* [Azure Active Directory Pod által felügyelt identitások használata az Azure Kubernetes Service-ben (előzetes verzió)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

Az AK-beli fürtműveleteket kapcsolatos további információkért tekintse meg az alábbi ajánlott eljárásokat:

* [Több-bérlős működés és fürtelkülönítés][aks-best-practices-cluster-isolation]
* [Alapszintű Kubernetes Scheduler-funkciók][aks-best-practices-scheduler]
* [Speciális Kubernetes Scheduler-funkciók][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
