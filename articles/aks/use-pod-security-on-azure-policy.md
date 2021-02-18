---
title: Biztonságos hüvelyek Azure Policy az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan védheti meg a hüvelyeket az Azure Kubernetes Service (ak) Azure Policyával
services: container-service
ms.topic: article
ms.date: 09/22/2020
ms.openlocfilehash: 34f2bfe346d7163a254e2ccecd1d7ef63ddb4194
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092631"
---
# <a name="secure-pods-with-azure-policy"></a>Podok biztonságossá tétele az Azure Policyval

Az AK-fürt biztonságának növelése érdekében megadhatja, hogy a rendszer milyen függvényeket biztosítson, és ha a vállalat szabályzata minden esetben működik. Ez a hozzáférés az AK-hoz készült [Azure Policy-bővítmény][kubernetes-policy-reference]által biztosított beépített házirendekkel van meghatározva. Azáltal, hogy további szabályozást biztosít a pod specifikációjának (például a legfelső szintű jogosultságok) biztonsági szempontjai felett, lehetővé teszi a szigorúbb biztonsági betartást és láthatóságot a fürtben üzembe helyezett környezetekben. Ha egy pod nem felel meg a szabályzatban meghatározott feltételeknek, Azure Policy letilthatja a hüvelyt a szabálysértés elindításához vagy megjelöléséhez. Ez a cikk bemutatja, hogyan használhatók a Azure Policy a hüvelyek az AK-ban való üzembe helyezésének korlátozására.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

### <a name="install-the-azure-policy-add-on-for-aks"></a>A Azure Policy-bővítmény telepítése az AK-hoz

Az AK-hüvelyek Azure Policyon keresztüli biztonságossá tételéhez telepítenie kell az AK-ra vonatkozó Azure Policy-bővítményt egy AK-fürtön. [A Azure Policy bővítmény telepítéséhez](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks)kövesse az alábbi lépéseket.

Ez a dokumentum azt feltételezi, hogy rendelkezik a következőkkel, amelyek üzembe helyezése a fentiekben látható.

* A `Microsoft.ContainerService` és az erőforrás-szolgáltató regisztrálása a `Microsoft.PolicyInsights``az provider register`
* Azure CLI 2,12 vagy újabb
* Egy 1,15-es vagy újabb verziójú, az Azure Policy-Bővítménysel telepített AK-fürt

## <a name="overview-of-securing-pods-with-azure-policy-for-aks"></a>A hüvelyek biztonságossá tétele a Azure Policy for AK-val – áttekintés

>[!NOTE]
> Ez a dokumentum részletesen ismerteti, hogyan használhatók a Azure Policy a hüvelyek védelmére, amely a [Kubernetes Pod biztonsági szabályzatának](use-pod-security-policies.md)utódja az előzetes verzióban.
> **A pod biztonsági házirend (előzetes verzió) és a Azure Policy-bővítmény nem engedélyezhető egyszerre.**
> 
> Ha a Azure Policy bővítményt egy olyan fürtbe telepíti, amelyen engedélyezve van a pod biztonsági házirend, [kövesse az alábbi lépéseket a pod biztonsági szabályzat letiltásához](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster).

Egy AK-fürtben a belépésvezérlés az API-kiszolgálónak küldött kérések elfogására szolgálnak, amikor egy erőforrást létre kell hozni és frissíteni kell. A beléptetési vezérlő Ezután *érvényesítheti* az erőforrás-kérelmet a szabályok egy meghatározott készlete alapján, függetlenül attól, hogy létre kell-e hozni.

Korábban a Kubernetes-projekten keresztül engedélyezte a Feature [Pod biztonsági házirendet (előzetes verzió)](use-pod-security-policies.md) , hogy korlátozza a felhasznált hüvelyek üzembe helyezését.

A Azure Policy bővítmény használatával egy AK-fürt használhat beépített Azure-szabályzatokat, amelyek biztosítják a hüvelyek és egyéb Kubernetes hasonló, a pod biztonsági házirendhez hasonlóan. Az AK Azure Policy-bővítménye a [forgalomirányító](https://github.com/open-policy-agent/gatekeeper)felügyelt példányát telepíti, amely egy érvényesítő beléptetési vezérlő. A Kubernetes Azure Policy a nyílt forráskódú nyílt házirend-ügynökre épül, amely a [Rego házirend nyelvére](../governance/policy/concepts/policy-for-kubernetes.md#policy-language)támaszkodik.

Ez a dokumentum részletesen ismerteti, hogyan használhatók a Azure Policy a hüvelyek biztonságossá tételéhez egy AK-fürtben, és hogyan lehet áttelepíteni a pod biztonsági házirendeket (előzetes verzió).

## <a name="limitations"></a>Korlátozások

A következő általános korlátozások érvényesek a Kubernetes-fürtök Azure Policy-bővítményére:

- A Kubernetes Azure Policy bővítménye a **1,14** -es vagy újabb verziójú Kubernetes támogatott.
- A Kubernetes Azure Policy bővítménye csak Linux-csomópontos készleteken telepíthető
- Csak a beépített szabályzat-definíciók támogatottak
- A szabályzattal nem kompatibilis rekordok maximális száma a fürtön: **500**
- Nem megfelelő rekordok maximális száma (előfizetés: **1 000 000** )
- Az Azure Policy-bővítményen kívüli forgalomirányító telepítése nem támogatott. A Azure Policy bővítmény engedélyezése előtt távolítsa el az előző forgalomirányító-telepítés által telepített összetevőket.
- A [meg nem felelés okai](../governance/policy/how-to/determine-non-compliance.md#compliance-reasons) nem érhetők el ehhez az [erőforrás-szolgáltatói módhoz](../governance/policy/concepts/definition-structure.md#resource-provider-modes)

A következő korlátozások érvényesek az AK-ra vonatkozó Azure Policy-bővítményre:

- Az [AK Pod biztonsági szabályzat (előzetes verzió)](use-pod-security-policies.md) és az AK Azure Policy bővítménye nem engedélyezhető egyszerre. 
- A Azure Policy bővítmény által automatikusan kizárt névterek a következő kiértékeléshez: _Kube-System_, _forgalomirányító-System_ és _AK-periszkóp_. Ha a 1,20-es vagy újabb Kubernetes-es verzióval rendelkező tarka hálózati házirendet használ, a rendszer 2 további névteret automatikusan kizárja, amelyek a _tarka-System_ és a _tigera-operátorok_.

### <a name="recommendations"></a>Javaslatok

Az alábbi általános javaslatok a Azure Policy bővítmény használatára:

- A Azure Policy bővítmény 3 forgalomirányító-összetevő futtatását igényli: 1 naplózási Pod és 2 webhook Pod-replika. Ezek az összetevők több erőforrást használnak, mint a Kubernetes erőforrások száma és a házirend-hozzárendelések a fürtben, amely naplózási és kényszerítési műveleteket igényelnek.

  - Ha kevesebb mint 500 hüvely van egyetlen fürtben, legfeljebb 20 megkötést biztosítunk: 2 vCPU és 350 MB memória/összetevő.
  - Több mint 500 hüvelyre egyetlen fürtben, legfeljebb 40 korlátozással: 3 vCPU és 600 MB memória/összetevő.

A következő javaslat csak az AK-ra és a Azure Policy bővítményre vonatkozik:

- A rendszercsomópont-készletet és a `CriticalAddonsOnly` Taint használva ütemezze a forgalomirányító hüvelyeket. További információ: [a rendszercsomópont-készletek használata](use-system-pools.md#system-and-user-node-pools).
- Biztonságos kimenő forgalom az AK-fürtökből. További információ: a [csomópontok kimenő forgalmának szabályozása a fürtcsomópontok esetében](limit-egress-traffic.md).
- Ha a fürt `aad-pod-identity` engedélyezve van, a csomópont által felügyelt identitás (NMI)-hüvelyek módosítja a csomópontok iptables-et az Azure-példány metaadatainak végpontjának lehallgatására. Ez a konfiguráció azt jelenti, hogy a metaadat-végpontra irányuló kéréseket a NMI akkor is elfogja, ha a pod nem használja `aad-pod-identity` . A AzurePodIdentityException CRD konfigurálható úgy, hogy tájékoztassa `aad-pod-identity` , hogy a tőkekövetelmény-ből származó, a CRD-ban definiált címkével rendelkező, a NMI-ben való feldolgozás nélkül proxyra irányuló kérelmeket. A `kubernetes.azure.com/managedby: aks` _Kube-_ rendszernévtérben címkével ellátott rendszerhüvelyeket ki kell ZÁRNI a `aad-pod-identity` AzurePodIdentityException CRD konfigurálásával. További információ: [a HRE-Pod-Identity letiltása egy adott Pod vagy alkalmazáshoz](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Kivétel konfigurálásához telepítse a [MIC-Exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

A Azure Policy-bővítmény működéséhez CPU-és memória-erőforrások szükségesek. Ezek a követelmények a fürt méretének növekedésével növekednek. Tekintse meg a Azure Policy bővítmény használatának általános útmutatóját [Azure Policy javaslatokat][policy-recommendations] .

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Azure-szabályzatok a Kubernetes-hüvelyek védelméhez

A Azure Policy bővítmény telepítése után a rendszer alapértelmezés szerint nem alkalmaz szabályzatokat.

11 beépített egyéni Azure-szabályzat és két beépített kezdeményezés van, amelyek kifejezetten biztonságos hüvelyt használnak egy AK-fürtben.
Minden szabályzat testreszabható. [Itt találja az AK-szabályzatok teljes listáját és azok támogatott hatásait][policy-samples]. További információ a [Azure Policy effektusokról](../governance/policy/concepts/effects.md).

Az Azure-szabályzatokat a felügyeleti csoport, az előfizetés vagy az erőforráscsoport szintjén lehet alkalmazni. Ha az erőforráscsoport szintjén rendel hozzá egy szabályzatot, győződjön meg arról, hogy a cél AK-fürt erőforráscsoport a házirend hatókörén belül van kiválasztva. A hozzárendelt hatókörben lévő összes fürt és a telepített Azure Policy-bővítmény a házirend hatókörében van.

Ha a [Pod biztonsági szabályzatot (előzetes verzió) ](use-pod-security-policies.md)használja, Ismerje meg, hogyan [telepíthet át Azure Policyre és más viselkedési eltérésekre](#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

### <a name="built-in-policy-initiatives"></a>Beépített házirend-kezdeményezések

Az Azure Policy-ben kezdeményezett kezdeményezések olyan házirend-definíciók gyűjteményei, amelyek egy egyedi átfogó cél megvalósítására vannak kialakítva. A kezdeményezések használata leegyszerűsítheti a házirendek felügyeletét és hozzárendelését az AK-fürtök között. Egy kezdeményezés egyetlen objektumként létezik, további információ a [Azure Policy kezdeményezésekről](../governance/policy/overview.md#initiative-definition).

A Kubernetes Azure Policy két beépített kezdeményezést kínál, amelyek biztosítják a hüvelyek [, az alapkonfiguráció és a](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) [korlátozott hozzáférését](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00).

A beépített kezdeményezések mind a [Kubernetes származó Pod biztonsági szabályzatban](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml)használt definíciók alapján készültek.

|[Pod biztonsági házirend-vezérlés](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Azure Policy definíció hivatkozása| [Alapterv kezdeményezés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) | [Korlátozott kezdeményezés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) |
|---|---|---|---|
|Privilegizált tárolók futtatásának letiltása|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| Igen | Yes
|A gazdagép-névterek megosztott használatának tiltása|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| Igen | Yes
|A gazdagép hálózatkezelésének és portjainak használatának korlátozása|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| Igen | Yes
|A gazda fájlrendszer használatának korlátozása|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| Igen | Yes
|A Linux-funkciók korlátozása az [alapértelmezett készletre](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | Igen | Yes
|Meghatározott mennyiségi típusok használatának korlátozása|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | Igen – engedélyezett kötetek típusai:,,, `configMap` `emptyDir` `projected` `downwardAPI` , `persistentVolumeClaim`|
|Jogosultság-eszkaláció a gyökérhez|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | Yes |
|A tároló felhasználói és csoportjai azonosítóinak korlátozása|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Yes|
|A pod köteteit birtokló FSGroup foglalásának korlátozása|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Igen – az engedélyezett szabályok a következők:,, `runAsUser: mustRunAsNonRoot` `supplementalGroup: mustRunAs 1:65536` `fsGroup: mustRunAs 1:65535` `runAsGroup: mustRunAs 1:65535` .  |
|Seccompot-profilt igényel|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | Igen, a allowedProfiles * `docker/default` vagy `runtime/default` |

\* a Docker/default értéke elavult a Kubernetes-ben a v 1.11 óta

### <a name="additional-optional-policies"></a>További választható házirendek

További Azure-szabályzatok is léteznek, amelyek a kezdeményezés alkalmazásán kívül is alkalmazhatók. Ha a beépített kezdeményezések nem teljesítik a követelményeket, érdemes lehet ezeket a szabályzatokat is hozzáfűzni.

|[Pod biztonsági házirend-vezérlés](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Azure Policy definíció hivatkozása| A kiindulási kezdeményezés mellett alkalmazza | A korlátozott kezdeményezés mellett alkalmazza |
|---|---|---|---|
|A tárolók által használt AppArmor-profil megadása|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | Választható | Választható |
|Csak olvasható csatlakoztatások engedélyezése|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | Választható | Választható |
|Korlátozás adott FlexVolume-illesztőprogramokra|[Nyilvános felhő](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | Opcionális – csak akkor használja, ha csak a FlexVolume-illesztőprogramokat szeretné korlátozni, a "megadott mennyiségi típusok használatának korlátozása" beállítással nem. | Nem alkalmazható – a korlátozott kezdeményezés a "meghatározott mennyiségi típusok használatának korlátozása", amely nem engedélyezi az összes FlexVolume-illesztőprogramot |

### <a name="unsupported-built-in-policies-for-managed-aks-clusters"></a>Nem támogatott beépített szabályzatok a felügyelt AK-fürtökhöz

> [!NOTE]
> A következő 3 szabályzatok **nem támogatottak az AK-ban** , mert az AK felügyelt szolgáltatásként felügyelt és biztosította a szempontokat. Ezek a szabályzatok kifejezetten az Azure arc csatlakoztatott fürtökhöz készültek, nem felügyelt vezérlési síkokkal.

|[Pod biztonsági házirend-vezérlés](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)|
|---|
|Egy tároló egyéni SELinux-környezetének megadása|
|A tárolók által használt sysctl-profil korlátozása|
|A proc-csatlakoztatások alapértelmezett típusai a támadási felület csökkentése érdekében vannak meghatározva|

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>Névtér kizárása

> [!WARNING]
> Az olyan rendszergazdai névterekben található hüvelyek, mint például a Kube-System kell futniuk ahhoz, hogy a fürt kifogástalan maradjon, a szükséges névtér eltávolítása az alapértelmezett kizárt névterek listájáról a szükséges rendszer-Pod miatt megsértheti a házirend megsértését.

Az ak-nak szüksége van a rendszerhüvelyek futtatására a fürtön a kritikus szolgáltatások, például a DNS-feloldás biztosításához. A pod funkcióit korlátozó szabályzatok hatással lehetnek a System Pod stabilitására. Ennek eredményeképpen a következő névterek **ki vannak zárva a házirend kiértékelése során a létrehozási, frissítési és házirend-naplózás során beléptetési kérelmek során**. Ez kényszeríti az ilyen névterek új központi telepítéseit az Azure-szabályzatokból való kizárásra.

1. kube-system
1. forgalomirányító – System
1. azure-arc
1. AK – periszkóp

A létrehozás, a frissítés és a naplózás során további egyéni névterek is kizárható a kiértékelésből. Ezeket a kivételeket akkor kell használni, ha olyan speciális hüvelyekkel rendelkezik, amelyek engedélyezett névtérben futnak, és el szeretné kerülni a naplózási szabálysértések aktiválását.

## <a name="apply-the-baseline-initiative"></a>Az alapterv alkalmazása

> [!TIP]
> Az összes házirend alapértelmezett értéke egy naplózási hatás. A hatások a Azure Policyon keresztül bármikor módosíthatók.

A kiindulási kezdeményezés alkalmazásához a Azure Portalon keresztül rendelhet hozzá.
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. Kövesse [ezt a hivatkozást a Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) a pod Security alapkonfiguráció kezdeményezésének áttekintéséhez
1. Állítsa be a **hatókört** az előfizetési szintre, vagy csak az AK-alapú fürtöket tároló erőforráscsoportot a Azure Policy-bővítmény engedélyezve
1. Válassza a **Parameters (paraméterek** ) lapot, és frissítse a **effektust** a alkalmazásból az alapkonfigurációt `audit` `deny` sértő új központi telepítések letiltásához
1. További névterek hozzáadása a kiértékelésből a létrehozás, a frissítés és a naplózás során kizárható, hogy [egyes névterek kényszerítve vannak kizárva a szabályzat kiértékelése alól.](#namespace-exclusion) 
 ![ frissítés hatása](media/use-pod-security-on-azure-policy/update-effect.png)
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a szabályzatok elküldéséhez

A szabályzatok a fürtön való futtatásával ellenőrizhetők `kubectl get constrainttemplates` .

> [!NOTE]
> [A házirendek akár 20 percet](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition) is igénybe vehetnek az egyes fürtökbe való szinkronizáláshoz.

A kimenetnek a következőhöz hasonlónak kell lennie:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>Emelt szintű Pod elutasításának ellenőrzése

Első lépésként tesztelje, mi történik, ha egy Pod-t a biztonsági környezetével ütemezhet `privileged: true` . Ez a biztonsági környezet a pod jogosultságait bővíti. Az alapszintű kezdeményezés nem engedélyezi a privilegizált hüvelyeket, ezért a rendszer megtagadja a kérést, mert a telepítés elutasítása megtörténik.

Hozzon létre egy nevű fájlt `nginx-privileged.yaml` , és illessze be a következő YAML-jegyzékbe:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f nginx-privileged.yaml
```

A várt módon a pod nem ütemezhető, ahogy az a következő példában látható:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

A pod nem éri el az ütemezési szakaszt, ezért nincs szükség a törlendő erőforrásokra, mielőtt továbblép.

## <a name="test-creation-of-an-unprivileged-pod"></a>Nem privilegizált Pod-létrehozási teszt létrehozása

Az előző példában a tároló képe automatikusan megpróbálta a root használatával kötni a NGINX-et a 80-es portra. Ezt a kérést az alapházirend-kezdeményezés megtagadta, így a pod nem indul el. Próbálja ki most ugyanezt az NGINX Pod-t, emelt szintű hozzáférés nélkül.

Hozzon létre egy nevű fájlt `nginx-unprivileged.yaml` , és illessze be a következő YAML-jegyzékbe:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f nginx-unprivileged.yaml
```

A pod sikeresen ütemezve. Ha a [kubectl Get hüvely][kubectl-get] parancs használatával tekinti meg a pod állapotát, a pod a következőket *futtatja*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Ez a példa azt az alapkonfigurációt mutatja be, amely csak a gyűjtemény házirendjeit sértő központi telepítéseket érinti. Az engedélyezett központi telepítések továbbra is működőképesek.

A [kubectl delete][kubectl-delete] paranccsal törölje az NGINX unprivilegizált Pod-t, és adja meg a YAML-jegyzék nevét:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-policies-and-the-azure-policy-add-on"></a>Házirendek és a Azure Policy bővítmény letiltása

Az alapterv-kezdeményezés eltávolítása:

1. Navigáljon a Azure Portal házirend panelére
1. **Hozzárendelések** kiválasztása a bal oldali panelen
1. Kattintson a "..." a kiindulási profil melletti gomb
1. Válassza a "hozzárendelés törlése" lehetőséget.

![Hozzárendelés törlése](media/use-pod-security-on-azure-policy/delete-assignment.png)

A Azure Policy-bővítmény letiltásához használja az az [AK disable-addons][az-aks-disable-addons] parancsot.

```azurecli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

Ismerje meg, hogyan távolíthatja el a [Azure Policy bővítményt a Azure Portalról](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks).

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Áttelepítés Kubernetes Pod biztonsági szabályzatból Azure Policy

A pod biztonsági házirendből való Migrálás esetén a következő műveleteket kell végrehajtania egy fürtön.

1. A [Pod biztonsági szabályzat letiltása](use-pod-security-policies.md#clean-up-resources) a fürtön
1. A [Azure Policy bővítmény][kubernetes-policy-reference] engedélyezése
1. A kívánt Azure-szabályzatok [elérhetővé tétele a beépített szabályzatok][policy-samples] közül

Az alábbiakban összefoglaljuk a pod biztonsági házirend és a Azure Policy viselkedésének változásait.

|Eset| Pod biztonsági szabályzat | Azure Policy |
|---|---|---|
|Telepítés|A pod biztonsági házirend funkció engedélyezése |Azure Policy bővítmény engedélyezése
|Szabályzatok telepítése| Pod biztonsági házirend-erőforrás üzembe helyezése| Rendeljen Azure-szabályzatokat az előfizetés vagy az erőforráscsoport hatóköréhez. A Kubernetes erőforrás-alkalmazásaihoz a Azure Policy bővítmény szükséges.
| Alapértelmezett házirendek | Ha a pod biztonsági házirend engedélyezve van az AK-ban, a rendszer az alapértelmezett privilegizált és korlátlan szabályzatokat alkalmazza. | A Azure Policy bővítmény engedélyezésével nincsenek alkalmazva alapértelmezett házirendek. Explicit módon engedélyeznie kell a házirendeket Azure Policy.
| Kik hozhatnak létre és rendelhetnek szabályzatokat | A fürt rendszergazdája létrehoz egy Pod biztonsági házirend-erőforrást | A felhasználóknak a "tulajdonos" vagy az "erőforrás-házirend közreműködői" engedélyekkel kell rendelkezniük az AK fürterőforrás-csoportban. – Az API-n keresztül a felhasználók szabályzatokat rendelhetnek az AK-fürt erőforrás-hatókörében. A felhasználónak legalább a "tulajdonos" vagy az "erőforrás-házirend közreműködői" engedélyekkel kell rendelkeznie az AK-fürt erőforrásán. – A Azure Portal a szabályzatokat a felügyeleti csoport/előfizetés/erőforráscsoport szintjén lehet hozzárendelni.
| Szabályzatok engedélyezése| A felhasználók és a szolgáltatásfiókok kifejezetten engedélyeket igényelnek a pod biztonsági szabályzatok használatához. | A házirendek engedélyezéséhez nincs szükség további hozzárendelésre. Miután a házirendek hozzá vannak rendelve az Azure-ban, a fürt összes felhasználója használhatja ezeket a házirendeket.
| Házirend alkalmazhatósága | A rendszergazda felhasználó megkerüli a pod biztonsági szabályzatok kényszerítését. | Minden felhasználó (rendszergazda & nem rendszergazda) ugyanazokat a szabályzatokat látja. A felhasználókon alapuló különleges ház nem található. A házirend-alkalmazás nem zárható ki a névtér szintjén.
| Házirend hatóköre | A pod biztonsági szabályzatok nem névterek | Az Azure Policy által használt korlátozási sablonok nem névterek.
| Megtagadás/naplózás/mutáció művelet | A pod biztonsági szabályzatok csak a megtagadási műveleteket támogatják. A mutáció a létrehozási kérelmek alapértelmezett értékeivel végezhető el. Az érvényesítés a frissítési kérelmek során végezhető el.| A Azure Policy a naplózási & megtagadási műveleteket is támogatja. A mutáció még nem támogatott, de tervezett.
| A pod biztonsági szabályzatoknak való megfelelőség | A pod biztonsági házirend engedélyezése előtt a hüvelyek megfelelősége nem látható. A pod biztonsági házirendek engedélyezése után létrehozott nem megfelelő hüvelyek megtagadva. | Az Azure-szabályzatok alkalmazása előtt létezett, nem megfelelő hüvelyek megjelenhetnek a szabályzat megsértése során. Az Azure-házirendek engedélyezése után létrehozott nem megfelelő hüvelyek megtagadva, ha a szabályzatok megtagadási hatással vannak beállítva.
| Szabályzatok megtekintése a fürtön | `kubectl get psp` | `kubectl get constrainttemplate` – Az összes szabályzatot visszaadja a rendszer.
| Pod biztonsági házirend standard – jogosultságú | Alapértelmezés szerint a Kiemelt Pod biztonsági házirend erőforrás jön létre a funkció engedélyezésekor. | Az emelt szintű üzemmód nem korlátozza a korlátozást, ezért nem rendelkezik Azure Policy-hozzárendeléssel.
| [Pod biztonsági házirend standard – alapkonfiguráció/alapértelmezett](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | A felhasználó telepíti a pod biztonsági házirend alaperőforrását. | A Azure Policy egy beépített alapkonfigurációs [kezdeményezést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) biztosít, amely az eredeti Pod biztonsági házirendre mutat.
| [Pod biztonsági házirend standard – korlátozott](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | A felhasználó egy Pod biztonsági házirendre korlátozott erőforrást telepít. | A Azure Policy egy [beépített korlátozott kezdeményezést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) biztosít, amely leképezi a korlátozott Pod biztonsági házirendet.

## <a name="next-steps"></a>Következő lépések

Ez a cikk bemutatja, hogyan alkalmazhat egy olyan Azure-szabályzatot, amely korlátozza a privilegizált hüvelyek üzembe helyezését a privilegizált hozzáférés használatának megakadályozása érdekében. Számos szabályzat alkalmazható, például a kötetek használatát korlátozó szabályzatok. Az elérhető beállításokkal kapcsolatos további információkért tekintse meg a [Kubernetes dokumentációs][kubernetes-policy-reference]dokumentációjának Azure Policy.

A pod hálózati forgalom korlátozásával kapcsolatos további információkért lásd: [biztonságos forgalom a hüvelyek között a hálózati házirendek használatával az AK-ban][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity
[aad-pod-identity-exception]: https://azure.github.io/aad-pod-identity/docs/configure/application_exception

<!-- LINKS - internal -->
[policy-recommendations]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-limitations]: ../governance/policy/concepts/policy-for-kubernetes.md?#limitations
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete