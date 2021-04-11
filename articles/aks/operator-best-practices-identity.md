---
title: Ajánlott eljárások az identitás kezeléséhez
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az Azure Kubernetes Service-ben (ak) lévő fürtök hitelesítésének és engedélyezésének kezelésével kapcsolatos ajánlott eljárásokat.
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: de84e3e2a8da3e1b5195978a8a2204fdfa2108d7
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105102"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a hitelesítéshez és az engedélyezéshez az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes szolgáltatásban (ak) lévő fürtök üzembe helyezése és karbantartása során az erőforrásokhoz és szolgáltatásokhoz való hozzáférés kezelésének módjait kell megvalósítani. A vezérlőelemek nélkül:
* A fiókok nem tudnak hozzáférni a szükségtelen erőforrásokhoz és szolgáltatásokhoz. 
* A változtatások elvégzéséhez használt hitelesítő adatok nyomon követése nehéz lehet.

Ez az ajánlott eljárás azt ismerteti, hogyan kezelhető a fürt operátora az AK-fürtök hozzáférésének és identitásának kezeléséhez. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Az AK-fürtök felhasználóinak hitelesítése Azure Active Directoryokkal.
> * Az erőforrásokhoz való hozzáférés szabályozása Kubernetes szerepköralapú hozzáférés-vezérléssel (Kubernetes RBAC).
> * Az Azure RBAC segítségével részletesen szabályozhatja az AK-erőforráshoz, a Kubernetes API-hoz és a skálához való hozzáférést `kubeconfig` .
> * Felügyelt identitás használata a hüvelyek más szolgáltatásokkal való hitelesítéséhez.

## <a name="use-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) használata

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> AK-fürtök üzembe helyezése az Azure AD-integrációval. Az Azure AD központosítja az Identitáskezelés összetevőt. A felhasználói fiók vagy a csoport állapotának bármilyen módosítása automatikusan frissül az AK-fürthöz való hozzáférés során. A hatókörrel rendelkező felhasználók vagy csoportok a minimális engedélyek összegéhez [szerepkörök, ClusterRoles vagy kötések](#use-kubernetes-role-based-access-control-kubernetes-rbac)használatával.

A Kubernetes-fürt fejlesztőinek és az alkalmazás tulajdonosainak különböző erőforrásokhoz kell hozzáférnie. A Kubernetes nem rendelkezik Identitáskezelés-kezelési megoldással, amellyel szabályozhatja azokat az erőforrásokat, amelyekkel a felhasználók kezelhetik. Ehelyett általában meglévő identitási megoldással integrálja a fürtöt. Adja meg az Azure AD: egy nagyvállalati használatra kész Identitáskezelés, amely az AK-fürtökkel integrálható.

Az Azure AD-vel integrált, az AK-ban lévő fürtökkel olyan *szerepköröket* vagy *ClusterRoles* hozhat létre, amelyek hozzáférési engedélyeket határoznak meg az erőforrásokhoz Ezután a szerepköröket a felhasználókhoz vagy csoportokhoz *kötheti* az Azure ad-ben. További információk ezekről a Kubernetes-RBAC [a következő szakaszban olvashatók](#use-kubernetes-role-based-access-control-kubernetes-rbac). Az Azure AD-integráció és az erőforrásokhoz való hozzáférés szabályozása az alábbi ábrán látható:

![Fürt szintű hitelesítés Azure Active Directory AK-val való integrációhoz](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. A fejlesztői hitelesítés az Azure AD-vel.
1. Az Azure AD-jogkivonat kiállítási végpontja kiadja a hozzáférési jogkivonatot.
1. A fejlesztő egy műveletet hajt végre az Azure AD-jogkivonat használatával, például: `kubectl create pod` .
1. A Kubernetes érvényesíti a jogkivonatot az Azure AD-vel, és beolvassa a fejlesztő csoporttagságait.
1. A Kubernetes RBAC és a fürt házirendjeit alkalmazza a rendszer.
1. A fejlesztő kérelme sikeres volt az Azure AD-csoporttagság és-Kubernetes RBAC és-szabályzatok korábbi ellenőrzése alapján.

Az Azure AD-t használó AK-fürtök létrehozásával kapcsolatban lásd: [Azure Active Directory integrálása az AK-val][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes szerepköralapú hozzáférés-vezérlés használata (Kubernetes RBAC)

> **Útmutatás az ajánlott eljárásokhoz**
> 
> Felhasználói vagy csoportos engedélyek definiálása a Kubernetes RBAC. Hozzon létre olyan szerepköröket és kötéseket, amelyek a minimálisan szükséges engedélyeket rendelik hozzá. Az Azure AD-vel való integrációval automatikusan frissítheti a felhasználói vagy csoporttagság-módosításokat, és megtarthatja a hozzáférést a fürterőforrások aktuális állapotához.

A Kubernetes részletes hozzáférés-vezérlést biztosít a fürt erőforrásaihoz. Engedélyeket adhat meg a fürt szintjén vagy adott névterekhez. Meghatározhatja, hogy mely erőforrások kezelhetők és milyen engedélyekkel rendelkeznek. Ezután alkalmazza ezeket a szerepköröket a felhasználókra vagy a csoportokra kötéssel. További információ a *szerepkörökről*, a *ClusterRoles* és a *kötésekről*: [Az Azure Kubernetes Service (ak) hozzáférési és identitási beállításai][aks-concepts-identity].

Például létrehozhat egy olyan szerepkört, amely teljes körű hozzáféréssel rendelkezik a *Finance-app* nevű névtérben található erőforrásokhoz, ahogy az a következő PÉLDÁBAN látható YAML-jegyzékben látható:

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

Ezután hozzon létre egy RoleBinding, és kösse az Azure AD felhasználói *developer1 \@ contoso.com* a RoleBinding, ahogy az a következő YAML-jegyzékben látható:

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

Ha a *developer1- \@ contoso.com* hitelesítve van az AK-fürtön, teljes körű engedélyekkel rendelkeznek a *Pénzügy-alkalmazás* névtér erőforrásaihoz. Így logikailag elkülönítheti és szabályozhatja az erőforrásokhoz való hozzáférést. Az Kubernetes RBAC az Azure AD-integrációval együtt használhatja.

Ha szeretné megtudni, hogy az Azure AD-csoportok hogyan használhatják a Kubernetes-erőforrásokhoz való hozzáférést a Kubernetes RBAC használatával, tekintse meg a következőt: a [fürterőforrások hozzáférésének szabályozása szerepköralapú hozzáférés-vezérléssel és Azure Active Directory az AK-beli identitások használatával][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Az Azure RBAC használata 
> **Útmutatás az ajánlott eljárásokhoz** 
> 
> Az Azure RBAC segítségével megadhatja a minimálisan szükséges felhasználói és csoportos engedélyeket az AK-erőforrásokhoz egy vagy több előfizetésben.

Az AK-fürtök teljes körű működtetéséhez két hozzáférési szint szükséges: 
1. Hozzáférés az AK-erőforráshoz az Azure-előfizetésében. 

    Ez a hozzáférési szint a következőket teszi lehetővé:
      * A fürt méretezésének vagy frissítésének szabályozása az AK API-k használatával
      * Húzza ki a t `kubeconfig` .

    Ha szeretné megtudni, hogyan szabályozhatja a hozzáférést az AK-erőforráshoz és a `kubeconfig` -hez, tekintse meg a [fürt konfigurációs fájljának korlátozása](control-kubeconfig-access.md)című témakört.

2. Hozzáférés a Kubernetes API-hoz. 
    
    Ezt a hozzáférési szintet a következők vezérlik:
    * [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (hagyományosan) vagy 
    * Az Azure RBAC és az AK-val való integrálásával a kubernetes-hitelesítéshez.

    Ha szeretné megtudni, hogyan adhat meg részletes engedélyeket a Kubernetes API-nak az Azure RBAC használatával, tekintse meg az [Azure RBAC használata Kubernetes-hitelesítéshez](manage-azure-rbac.md)című témakört.

## <a name="use-pod-managed-identities"></a>A pod által felügyelt identitások használata

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> Ne használjon rögzített hitelesítő adatokat a hüvelyeken vagy a tárolók rendszerképein belül, mivel azok az expozíció vagy a visszaélés kockázatának vannak kitéve. Ehelyett a *Pod* identitys használatával automatikusan kérhet hozzáférést egy központi Azure ad-identitási megoldás használatával. 

> [!NOTE]
> A pod-identitások csak a Linux-hüvelyek és a tároló-lemezképek használatára készültek. A Windows-tárolók esetében a pod által felügyelt identitások támogatása hamarosan elérhető lesz.

Más Azure-szolgáltatások (például Cosmos DB, Key Vault vagy Blob Storage) eléréséhez a pod-nak hozzáférési hitelesítő adatokra van szüksége. Megadhatja a hozzáférési hitelesítő adatokat a tároló képével, vagy befecskendezheti őket Kubernetes-titokként. Mindkét esetben manuálisan kell létrehoznia és hozzárendelni azokat. Ezek a hitelesítő adatok általában újra felhasználhatók a hüvelyek között, és a rendszer nem fordítja rendszeresen.

Az Azure-erőforrások Pod által felügyelt identitásával automatikusan hozzáférést kér a szolgáltatásokhoz az Azure AD-n keresztül. A pod által felügyelt identitások jelenleg előzetes verzióban elérhetők az AK-ban. Tekintse meg a [Azure Active Directory Pod által felügyelt identitások használata az Azure Kubernetes szolgáltatásban (előzetes verzió) [( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) dokumentáció az első lépésekhez) [. 

A hüvelyekhez tartozó hitelesítő adatok manuális meghatározása helyett a hüvely által felügyelt identitások valós időben igényelnek hozzáférési jogkivonatot, és csak a hozzájuk rendelt szolgáltatások elérésére használják. Az AK-ban két összetevő van, amelyek kezelik a műveleteket, hogy a hüvelyek felügyelt identitásokat használjanak:

* **A Node Management Identity (NMI) kiszolgáló** egy olyan Pod, amely daemonset elemet fut az AK-fürt minden csomópontján. A NMI-kiszolgáló az Azure-szolgáltatásokhoz tartozó Pod-kérelmeket figyeli.
* **Az Azure erőforrás-szolgáltató** lekérdezi a Kubernetes API-kiszolgálót, és ellenőrzi a pod-nak megfelelő Azure Identity-hozzárendelést.

Ha a hüvely egy Azure-szolgáltatáshoz való hozzáférést kér, a hálózati szabályok átirányítják a forgalmat a NMI-kiszolgálóra. 
1. A NMI-kiszolgáló:
    * Azonosítja az Azure-szolgáltatásokhoz való hozzáférést kérő hüvelyeket a távoli címük alapján.
    * Lekérdezi az Azure erőforrás-szolgáltatót. 
1. Az Azure erőforrás-szolgáltató ellenőrzi az Azure Identity-hozzárendeléseket az AK-fürtben.
1. A NMI-kiszolgáló hozzáférési jogkivonatot kér az Azure AD-től a pod identitásának leképezése alapján. 
1. Az Azure AD hozzáférést biztosít a NMI-kiszolgálóhoz, amelyet a rendszer visszaadott a pod-nek. 
    * Ezt a hozzáférési jogkivonatot a pod használhatja a szolgáltatásokhoz való hozzáféréshez az Azure-ban.

A következő példában egy fejlesztő létrehoz egy Pod-t, amely egy felügyelt identitást használ a Azure SQL Databasehoz való hozzáférés kéréséhez:

![A pod-identitások lehetővé teszik, hogy a pod automatikusan kérjen hozzáférést más szolgáltatásokhoz](media/operator-best-practices-identity/pod-identities.png)

1. A fürt operátora létrehoz egy szolgáltatásfiókot az identitások hozzárendeléséhez, amikor a hüvely a szolgáltatásokhoz való hozzáférést kéri.
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
