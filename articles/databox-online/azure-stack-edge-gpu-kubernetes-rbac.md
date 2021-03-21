---
title: A Kubernetes szerepköralapú hozzáférés-vezérlésének megismerése Azure Stack Edge Pro-eszközön | Microsoft Docs
description: Ismerteti, hogyan történik a Kubernetes szerepköralapú hozzáférés-vezérlés egy Azure Stack Edge Pro-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 1b38a9807e05385a378fa6103710fb6b393c7b1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443148"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes szerepköralapú hozzáférés-vezérlés a Azure Stack Edge Pro GPU-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A Azure Stack Edge Pro-eszközön a számítási szerepkör konfigurálásakor létrejön egy Kubernetes-fürt. A Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) segítségével korlátozhatja az eszközön található fürterőforrások elérését.

Ez a cikk áttekintést nyújt a Kubernetes által biztosított Kubernetes RBAC rendszerről, valamint arról, hogy a Kubernetes-RBAC Hogyan valósul meg az Azure Stack Edge Pro-eszközön. 

## <a name="kubernetes-rbac"></a>A Kubernetes szerepköralapú hozzáférés-vezérlése (RBAC)

A Kubernetes RBAC lehetővé teszi felhasználók vagy felhasználói csoportok hozzárendelését, valamint az erőforrások létrehozását és módosítását, illetve a naplók megtekintését az alkalmazás számítási feladatainak futtatásához. Ezek az engedélyek egyetlen névtérre vagy a teljes fürtre is érvényesek. 

A Kubernetes-fürt beállításakor a rendszer egyetlen felhasználót hoz létre a fürtnek megfelelően, és neve a fürt rendszergazdai felhasználója.  Egy `kubeconfig` fájl társítva van a fürt rendszergazdai felhasználója számára. A `kubeconfig` fájl egy szövegfájl, amely tartalmazza a fürthöz való kapcsolódáshoz szükséges összes konfigurációs információt a felhasználó hitelesítéséhez.

## <a name="namespaces-types"></a>Névterek típusai

A Kubernetes-erőforrások, például a hüvelyek és a központi telepítések logikailag vannak csoportosítva egy névtérbe. Ezek a csoportok lehetővé teszik a Kubernetes-fürtök logikai felosztását, valamint az erőforrások létrehozásához, megtekintéséhez vagy kezeléséhez való hozzáférés korlátozását. A felhasználók csak a hozzájuk rendelt névterekben lévő erőforrásokkal tudnak kommunikálni.

A névterek olyan környezetekben használhatók, amelyekben számos felhasználó több csapat vagy projekt között oszlik meg. További információ: Kubernetes- [névterek](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

Az Azure Stack Edge Pro-eszköz a következő névtereket tartalmazhatja:

- **Rendszernévtér** – ez a névtér az alapvető erőforrások, például a DNS és a proxy, illetve a Kubernetes irányítópult. Általában nem telepítheti saját alkalmazásait ebbe a névtérbe. Használja ezt a névteret a Kubernetes-fürtökkel kapcsolatos problémák hibakereséséhez. 

    Az eszközön több rendszernévtér található, és az ezekhez a rendszernévtérekhez tartozó nevek vannak fenntartva. Itt látható a fenntartott rendszernévtérek listája: 
    - kube-system
    - metallb – rendszerek
    - dbe – névtér
    - alapértelmezett
    - kubernetes – irányítópult
    - Kube – csomópont-bérlet
    - kube-public


    Ügyeljen arra, hogy ne használjon fenntartott neveket a létrehozott felhasználói névterekhez. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Felhasználói névtér** – ezek azok a névterek, amelyeket a **kubectl** vagy az eszköz PowerShell-felületén keresztül hozhat létre az alkalmazások helyi telepítéséhez.
 
- **IoT Edge névtér** – ehhez a `iotedge` névtérhez kapcsolódhat IoT Edge használatával telepített alkalmazások kezeléséhez.

- **Azure arc-névtér** – ehhez a névtérhez kapcsolódhat az `azure-arc` Azure arc használatával üzembe helyezett alkalmazások kezeléséhez. Az Azure arc használatával más felhasználói névterekben is telepíthet alkalmazásokat. 

## <a name="namespaces-and-users"></a>Névterek és felhasználók

A valós világban fontos a fürtöt több névtérre osztani. 

- **Több felhasználó**: Ha több felhasználóval is rendelkezik, akkor több névtér is lehetővé teszi, hogy ezek a felhasználók az alkalmazásait és szolgáltatásait az adott névterekben, egymástól elkülönítve telepíthessék. 
- **Egyetlen felhasználó**: akkor is, ha egyetlen felhasználó van, több névtér is lehetővé teszi, hogy a felhasználó ugyanazon a Kubernetes-fürtön futtassa az alkalmazások több verzióját.

### <a name="roles-and-rolebindings"></a>Szerepkörök és RoleBindings

A Kubernetes a szerepkör és a szerepkör-kötés fogalmával rendelkezik, amely lehetővé teszi, hogy a felhasználók vagy erőforrások számára a névtér szintjén és a fürt szintjén biztosítson engedélyeket. 

- **Szerepkörök**: megadhatja a felhasználóknak az engedélyeket **szerepkörként** , majd **szerepkörök** használatával engedélyeket adhat meg a névtéren belül. 
- **RoleBindings**: Miután definiálta a szerepköröket, a **RoleBindings** használatával rendelhet hozzá szerepköröket egy adott névtérhez. 

Ez a megközelítés lehetővé teszi, hogy logikailag elkülönítse egyetlen Kubernetes-fürtöt, és a felhasználók csak a hozzájuk rendelt névtérben tudják elérni az alkalmazás erőforrásait. 

## <a name="kubernetes-rbac-on-azure-stack-edge-pro"></a>Kubernetes RBAC a Azure Stack Edge Pro-ban

A Kubernetes RBAC jelenlegi implementációjában a Azure Stack Edge Pro lehetővé teszi, hogy a következő műveleteket egy korlátozott PowerShell-RunSpace végezze:

- Hozzon létre névtereket.  
- Hozzon létre további felhasználókat.
- Rendszergazdai hozzáférést biztosít a létrehozott névterekhez. Ne feledje, hogy nem lesz hozzáférése a fürt rendszergazdai szerepköréhez vagy a teljes fürtre kiterjedő erőforrások nézetéhez.
- `kubeconfig`A Kubernetes-fürt eléréséhez szükséges adatokat tartalmazó fájl beolvasása.


Az Azure Stack Edge Pro-eszköz több rendszernévtérrel rendelkezik, és a `kubeconfig` névtér eléréséhez fájlokkal rendelkező felhasználói névtereket is létrehozhat. A felhasználók teljes körűen vezérelhetik ezeket a névtereket, és létrehozhatnak vagy módosíthatnak felhasználókat, vagy hozzáférést biztosíthatnak a felhasználóknak. Csak a fürt rendszergazdája fér hozzá a rendszernévtérhez és a fürtre kiterjedő erőforrásokhoz. Az egy `aseuser` csak olvasási hozzáféréssel rendelkezik a rendszernévterekhez.

Itt látható egy diagram, amely a Kubernetes RBAC megvalósítását ábrázolja Azure Stack Edge Pro-eszközön.

![Kubernetes RBAC Azure Stack Edge Pro-eszközön](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

Ebben az ábrában Alice, Bob és Chuck csak a hozzárendelt felhasználói névterekhez fér hozzá, ami ebben az esetben a, a `ns1` `ns2` és a `ns3` . Ezeken a névtereken belül rendszergazdai hozzáférésük van. A fürt rendszergazdájának viszont rendszergazdai hozzáférése van a rendszer névteréhez és a fürtre kiterjedő erőforrásokhoz.

Felhasználóként névtereket és felhasználókat hozhat létre, felhasználókat rendelhet hozzá a névterekhez vagy letöltheti a `kubeconfig` fájlokat. Részletes útmutatásért lásd: [hozzáférés a Kubernetes-fürthöz a Azure stack Edge Pro-on keresztül a kuebctl-on keresztül](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Ha névtereket és felhasználókat használ a Azure Stack Edge Pro-eszközökön, a következő kikötések érvényesek:

- Nem végezhet olyan műveleteket, mint például a felhasználók létrehozása, a névtér elérésének engedélyezése vagy visszavonása a felhasználóhoz a rendszernévtérek esetében. Példák a rendszernévtérre:,,, `kube-system` `metallb-system` `kubernetes-dashboard` `default` `kube-node-lease` , `kube-public` . A rendszernévtérek közé tartoznak a központi telepítési típusok számára fenntartott névterek (például a `iotedge` (IoT Edge névtér) és az `azure-arc` Azure arc-névtér is.
- Létrehozhat felhasználói névtereket, és ezeken a névterekben létrehozhat további felhasználókat, és engedélyezheti vagy visszavonhatja a névtér elérését a felhasználók számára.
- Nem hozhat létre olyan névtereket, amelyek neve megegyezik a rendszernévtér bármely rendszernévterével. A rendszernévtér neve fenntartva.  
- Nem hozhat létre olyan felhasználói névtereket, amelyek más felhasználói névterek által már használt névvel rendelkeznek. Ha például Ön létrehozta a t `test-ns` , nem hozhat létre újabb `test-ns` névteret.
- A már fenntartott névvel rendelkező felhasználókat nem lehet létrehozni. Például `aseuser` egy fenntartott felhasználó, és nem használható.


## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan hozhat létre egy felhasználót, hozzon létre egy névteret, és engedélyezze a hozzáférést a névtérhez, tekintse meg a [Kubernetes-fürt elérése a kubectl keresztül](azure-stack-edge-gpu-create-kubernetes-cluster.md)című témakört.

