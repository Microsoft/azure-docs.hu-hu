---
title: A fürt automéretező használata az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan használható a fürt automatikus méretezése, hogy a fürt automatikusan méretezhető legyen az alkalmazások igényeinek kielégítéséhez egy Azure Kubernetes-szolgáltatásbeli (ak-beli) fürtben.
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: c0564dc3b394b4a65e70a487b6f6989cb306bdda
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373249"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Fürt automatikus skálázása az alkalmazás igényeinek kielégítéséhez az Azure Kubernetes Service-ben (AKS)

Az alkalmazások az Azure Kubernetes szolgáltatásban (ak) való megtartásához szükség lehet a számítási feladatokat futtató csomópontok számának módosítására. A fürt autoscaleer összetevője megtekintheti a fürtben lévő hüvelyeket, amelyek erőforrás-korlátozások miatt nem ütemezhetők. Problémák észlelése esetén a Node-készlet csomópontjainak száma megnő az alkalmazás iránti igény kielégítése érdekében. A csomópontokat a futó hüvelyek hiánya is rendszeresen ellenőrzi, a csomópontok számával azonban szükség szerint csökken. Ez a lehetőség lehetővé teszi a csomópontok számának automatikus méretezését az AK-fürtben, így hatékony és költséghatékony fürtöt futtathat.

Ez a cikk bemutatja, hogyan engedélyezheti és kezelheti a fürt automéretezőjét egy AK-fürtben.

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI 2.0.76 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>Tudnivalók a fürt autoskálázásáról

Ha módosítani szeretné az alkalmazási igényeket, például a munkanapokat és az estét, vagy egy hétvégét, a fürtöknek gyakran szükségük van az automatikus skálázásra. Az AK-fürtök kétféleképpen méretezhetők a következőképpen:

* A **fürt autoskálázása** figyeli a hüvelyeket, amelyek erőforrás-megkötések miatt nem ütemezhetők a csomópontokon. A fürt ezután automatikusan növeli a csomópontok számát.
* A **horizontális Pod automéretező** a Kubernetes-fürt metrikák kiszolgálóját használja a hüvelyek erőforrás-igényének figyeléséhez. Ha egy alkalmazásnak több erőforrásra van szüksége, a hüvelyek száma automatikusan növekszik az igény kielégítése érdekében.

![A fürt autoskálázása és a horizontális Pod autoskálázás gyakran együtt működik a szükséges alkalmazási igények támogatásához](media/autoscaler/cluster-autoscaler.png)

A horizontális Pod autoskálázás és a fürt autoskálázása is csökkentheti a hüvelyek és a csomópontok számát igény szerint. A fürt autoskálázása csökkenti a csomópontok számát, ha egy adott időszakban nem használták fel a rendelkezésre álló kapacitást. A fürt automéretezője által eltávolított csomóponton lévő hüvelyek biztonságosan vannak ütemezve a fürtben máshol. Előfordulhat, hogy a fürt autoskálázása nem tud leméretezni, ha a hüvely nem helyezhető át, például a következő helyzetekben:

* Egy Pod közvetlenül jön létre, és nem támogatja a vezérlő objektum, például egy központi telepítés vagy egy replikakészlet.
* A pod-megszakadási költségvetés (PDB) túl korlátozó, és nem teszi lehetővé, hogy a hüvelyek száma egy bizonyos küszöbérték alá kerüljön.
* A pod olyan csomópont-választókat vagy affinitást használ, amelyeket nem lehet tiszteletben venni, ha egy másik csomóponton van ütemezve.

Ha többet szeretne megtudni arról, hogy a fürt automéretezője miért nem tudja méretezni a méretezést, tekintse meg, hogy [milyen típusú hüvelyek akadályozhatják a fürt autoskálázását a csomópontok eltávolításával?][autoscaler-scaledown]

A fürt automatikus méretezése indítási paramétereket használ olyan dolgokhoz, mint a méretezési események és az erőforrás-küszöbértékek közötti időintervallumok. További információ arról, hogy milyen paramétereket használ a fürt autoskálázása: [az autoskálázási profil használata](#using-the-autoscaler-profile).

A fürt és a horizontális Pod automéretezők együtt is működhetnek, és a fürtben gyakran is üzembe helyezhetők. Kombinálva a horizontális Pod automéretező az alkalmazás igényének kielégítéséhez szükséges hüvelyek számának futtatására koncentrál. A fürt autoskálázása az ütemezett hüvelyek támogatásához szükséges csomópontok számának futtatására fókuszál.

> [!NOTE]
> A manuális skálázás le van tiltva a fürt automatikus méretezésének használatakor. Hagyja, hogy a fürt autoskálázása határozza meg a csomópontok szükséges számát. Ha manuálisan szeretné méretezni a fürtöt, [Tiltsa le a fürt automéretezőjét](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AK-fürt létrehozása és a fürt autoskálázásának engedélyezése

Ha AK-fürtöt kell létrehoznia, használja az az [AK Create][az-aks-create] parancsot. A fürthöz tartozó, a fürthöz tartozó autoskálázás engedélyezéséhez és konfigurálásához használja a `--enable-cluster-autoscaler` paramétert, és adja meg a csomópontot és a-t `--min-count` `--max-count` .

> [!IMPORTANT]
> A fürt automéretezője egy Kubernetes-összetevő. Bár az AK-fürt egy virtuálisgép-méretezési készletet használ a csomópontokhoz, ne manuálisan engedélyezzen vagy szerkessze a méretezési csoport autoskálázásának beállításait a Azure Portal vagy az Azure CLI használatával. Lehetővé teszi, hogy a Kubernetes-fürt autoskálázása felügyelje a szükséges méretezési beállításokat. További információ: [módosíthatom a csomópont-ERŐFORRÁSCSOPORT AK-erőforrásait?][aks-faq-node-resource-group]

Az alábbi példa egy AK-fürtöt hoz létre egyetlen, virtuálisgép-méretezési csoport által támogatott csomópont-készlettel. Emellett lehetővé teszi a fürthöz tartozó automéretezőt a fürt csomópont-készletén, és legalább *1* és legfeljebb *3* csomópontot állít be:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

A fürt létrehozása és a fürt autoskálázási beállításainak konfigurálása néhány percet vesz igénybe.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Meglévő AK-fürt frissítése a fürt autoskálázásának engedélyezéséhez

Az az [AK Update][az-aks-update] paranccsal engedélyezheti és konfigurálhatja a fürt automéretezőjét a meglévő fürthöz tartozó csomópont-készleten. Használja a `--enable-cluster-autoscaler` paramétert, és határozzon meg egy csomópontot `--min-count` és `--max-count` .

> [!IMPORTANT]
> A fürt automéretezője egy Kubernetes-összetevő. Bár az AK-fürt egy virtuálisgép-méretezési készletet használ a csomópontokhoz, ne manuálisan engedélyezzen vagy szerkessze a méretezési csoport autoskálázásának beállításait a Azure Portal vagy az Azure CLI használatával. Lehetővé teszi, hogy a Kubernetes-fürt autoskálázása felügyelje a szükséges méretezési beállításokat. További információ: [módosíthatom a csomópont-ERŐFORRÁSCSOPORT AK-erőforrásait?][aks-faq-node-resource-group]

Az alábbi példa frissíti egy meglévő AK-fürtöt, hogy lehetővé tegye a fürt automéretezőjét a fürt csomópont-készletén, és legalább *1* és legfeljebb *3* csomópontot állít be:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

A fürt frissítése néhány percet vesz igénybe, és konfigurálja a fürt autoskálázási beállításait.

## <a name="change-the-cluster-autoscaler-settings"></a>A fürt autoskálázási beállításainak módosítása

> [!IMPORTANT]
> Ha több Node-készlettel rendelkezik az AK-fürtben, ugorjon az [autoskálázás több ügynök-készlettel szakaszra](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). A több ügynököt tartalmazó fürtökhöz a `az aks nodepool` parancs használatával kell beállítani a csomópont-készletre vonatkozó tulajdonságok módosítása helyett `az aks` .

Az előző lépésben egy AK-fürt létrehozásához vagy egy meglévő csomópont-készlet frissítéséhez a fürthöz tartozó minimális csomópontok számának értéke *1*, a csomópontok maximális száma pedig *3*. Az alkalmazás követelményeinek változása esetén előfordulhat, hogy módosítania kell a fürt automatikusan méretezhető csomópontjának darabszámát.

A csomópontok számának módosításához használja az az [AK Update][az-aks-update] parancsot.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

A fenti példa frissíti a fürt automéretezőjét az egyetlen csomópontos készleten a *myAKSCluster* -ben legalább *1* és legfeljebb *5* csomópontra.

> [!NOTE]
> A fürt automatikus méretezése az egyes csomópont-készleteken beállított minimális és maximális darabszámon alapuló méretezési döntéseket tesz, de a minimális vagy a maximális szám frissítése után nem kényszeríti ki őket. Ha például az aktuális csomópontok száma értéke 3, akkor a rendszer nem fogja azonnal méretezni a készletet 5-re. Ha a csomópontok minimális száma meghaladja az aktuálisan használt csomópontok számát, akkor a rendszer az új min vagy Max beállítást veszi figyelembe, ha elegendő unschedulable hüvely áll rendelkezésre, amely 2 új csomópontot igényel, és egy autoskálázási eseményt vált ki. A skálázási esemény után az új Count-korlátokat figyelembe vesszük.

Figyelje az alkalmazások és szolgáltatások teljesítményét, és állítsa be úgy a fürt automatikusan méretezhető csomópontjának számát, hogy az megfeleljen a szükséges teljesítménynek.

## <a name="using-the-autoscaler-profile"></a>Az autoskálázási profil használata

A fürt autoskálázásának részletesebb adatait úgy is konfigurálhatja, hogy módosítja a teljes fürtre kiterjedő autoskálázási profil alapértelmezett értékeit. Egy leskálázási esemény például akkor fordul elő, ha a csomópontok 10 perc elteltével vannak kihasználva. Ha 15 percenként futtatott munkaterhelésekkel rendelkezett, érdemes lehet módosítani az autoskálázási profilt úgy, hogy a használatban lévő csomópontok között 15 vagy 20 percet is igénybe vehet. Ha engedélyezi a fürt automéretezőjét, a rendszer az alapértelmezett profilt használja, kivéve, ha eltérő beállításokat ad meg. A fürt autoskálázási profilja a következő beállításokat tudja frissíteni:

| Beállítás                          | Leírás                                                                              | Alapértelmezett érték |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| vizsgálat – intervallum                    | A fürt fel-vagy leskálázásának újraértékelésének gyakorisága                                    | 10 másodperc    |
| vertikális leskálázás – késleltetés utáni Hozzáadás       | A vertikális felskálázást követő kiértékelés utáni időtartam                               | 10 perc    |
| leskálázás – késleltetés utáni törlés    | Ennyi ideig a csomópont törlése után a kiértékelés folytatódik                          | vizsgálat – intervallum |
| leskálázás – késés utáni késleltetés   | A kiértékelési hibák leskálázása után eltelt távolság                     | 3 perc     |
| vertikális leskálázás – szükségtelen idő         | Mennyi ideig kell szükségtelen egy csomópontot lekicsinyíteni                  | 10 perc    |
| leskálázás – nem olvasható idő          | Mennyi ideig kell szükségtelen egy olvashatatlan csomópontot megállapítani ahhoz, hogy a leskálázásra jogosult legyen         | 20 perc    |
| méretezés lefelé-kihasználtsága – küszöbérték | A csomópont kihasználtsági szintje, amely a kért erőforrások összegeként van meghatározva a kapacitás alapján elosztva | 0,5 |
| maximális – kecses megszakítás – mp     | Maximális időtartam másodpercben, ameddig a fürt autoskálázása a csomópontok skálázására tett kísérlet során megvárja a pod-megszakítást. | 600 másodperc   |
| egyenleg – hasonló csomópont-csoportok      | Észleli a hasonló csomópont-készleteket, és kiegyenlíti a közöttük lévő csomópontok számát                 | hamis         |
| bővítő                         | A vertikális felskálázáshoz használandó Node Pool [Expander](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) típusa Lehetséges értékek: `most-pods` , `random` , `least-waste` , `priority` | véletlenszerű | 
| Kihagyás – csomópontok – helyi tároló    | Ha az igaz fürt automéretezője soha nem törli a helyi tárolóval rendelkező, a hüvelyekkel rendelkező csomópontokat, például EmptyDir vagy HostPath | true |
| Kihagyás – csomópontok – a-System-hüvelyek      | Ha az igaz fürt automéretezője soha nem törli a Kube-rendszerből származó (kivéve a Daemonset elemet vagy a Mirror hüvelyeket) csomópontokat. | true | 
| maximális – üres – tömeges törlés            | Egy időben törölhető üres csomópontok maximális száma                       | 10 csomópont      |
| új – Pod – vertikális felskálázás           | Olyan forgatókönyvek esetében, mint például a burst/batch skálázás, ahol nem szeretné, hogy a CA a kubernetes ütemező az összes hüvelyt ütemezheti, a CA-nak a nem ütemezett hüvelyek figyelmen kívül hagyásához kell lennie, mielőtt azok már bizonyos korban vannak.                                                                                                                | 0 másodperc    |
| Max-Total – nem olvasott – százalék     | A fürtben lévő nem olvasott csomópontok maximális százalékos aránya. A százalékos arány túllépése után a CA leállítja a műveleteket | 45% |
| Max-Node-kiépítés ideje          | Az a maximális idő, ameddig az autoskálázás megvárja a csomópont kiosztását                           | 15 perc    |   
| ok – összesen – nem olvasott – darabszám           | Az engedélyezett nem olvasott csomópontok száma, a maximális összegtől függetlenül – nem olvasott – százalék            | 3 csomópont       |

> [!IMPORTANT]
> A fürt autoskálázási profilja hatással van az összes olyan csomópontra, amely a fürt automéretezőjét használja. Node-készletben nem állítható be egy autoskálázási profil.
>
> A fürt autoskálázási profiljának az Azure CLI *2.11.1* vagy újabb verzióját kell megadnia. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>A fürt autoskálázási profiljának beállítása meglévő AK-fürtön

A fürthöz tartozó autoskálázási profil beállításához használja az az [AK Update][az-aks-update-preview] parancsot a *fürt-autoskálázás-profil* paraméterrel. A következő példa a vizsgálat időköze beállítást a 30-as értékként konfigurálja a profilban.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Ha a fürtben lévő csomópont-készleteken engedélyezi a fürt autoskálázását, akkor ezek a fürtök a fürt automatikusan méretezhető profilját is használják. Például:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> A fürt automatikus méretezési profiljának beállításakor a fürt automatikus méretezését engedélyező meglévő csomópont-készletek azonnal elkezdik használni a profilt.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>A fürt autoskálázási profiljának beállítása AK-fürt létrehozásakor

A fürt létrehozásakor a *fürt-autoskálázási profil* paramétert is használhatja. Például:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

A fenti parancs létrehoz egy AK-fürtöt, és a vizsgálati időközt 30 másodpercen belül meghatározza a teljes fürtre kiterjedő autoskálázási profilhoz. A parancs a fürtben lévő automéretezőt is engedélyezi a kezdeti csomópont-készleten, beállítja a csomópontok minimális száma 1 értékre, a csomópontok maximális száma pedig 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Fürt autoskálázási profiljának alaphelyzetbe állítása alapértelmezett értékekre

A fürthöz tartozó autoskálázási profil alaphelyzetbe állításához használja az az [AK Update][az-aks-update-preview] parancsot.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>A fürt autoskálázásának letiltása

Ha már nem kívánja használni a fürt automéretezőjét, letilthatja az az [AK Update][az-aks-update-preview] paranccsal, és megadhatja a `--disable-cluster-autoscaler` paramétert. A csomópontok nem törlődnek, ha a fürt autoskálázása le van tiltva.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

A fürt automéretezőjét az az [AK Scale][az-aks-scale] paranccsal manuálisan is méretezheti. Ha a horizontális Pod automéretezőt használja, a funkció továbbra is fut a fürt automéretezője szolgáltatással, de a hüvelyek nem lesznek ütemezve, ha az összes csomópont-erőforrás használatban van.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Letiltott fürt autoskálázásának újbóli engedélyezése

Ha újra engedélyezni szeretné a fürt automéretezőjét egy meglévő fürtön, újra engedélyezheti azt az az [AK Update][az-aks-update-preview] paranccsal, amely megadja a `--enable-cluster-autoscaler` , a és a `--min-count` `--max-count` paramétereket.

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Fürthöz tartozó autoskálázási naplók és állapot beolvasása

Az autoskálázási események diagnosztizálásához és hibakereséséhez a naplók és az állapot lekérhető az automatikusan méretezhető bővítményből.

Az AK kezeli a fürt automéretezőjét az Ön nevében, és futtatja a felügyelt vezérlési síkon. Engedélyezheti a vezérlési sík csomópontot, hogy megtekintse a HITELESÍTÉSSZOLGÁLTATÓI naplókat és műveleteket.

Az alábbi lépésekkel konfigurálhatja a naplóknak a fürt autoskálázási szolgáltatásból Log Analyticsba való leküldését.

1. Szabály beállítása az erőforrás-naplókhoz a fürt leküldéséhez – az autoskálázási naplók Log Analytics. Az [utasításokat itt][aks-view-master-logs]találja, és ellenőrizze, hogy be van-e jelölve a `cluster-autoscaler` "naplók" beállításainak kiválasztásakor.
1. Válassza a "naplók" szakaszt a fürtön a Azure Portal használatával.
1. Adja meg a következő példában szereplő lekérdezést Log Analyticsba:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Az alábbi példához hasonló naplóknak kell megjelennie, amíg vannak naplók a lekéréshez.

![Naplók Log Analytics](media/autoscaler/autoscaler-logs.png)

A fürt autoskálázása egy elnevezett állapotba is kiírja az állapotot `configmap` `cluster-autoscaler-status` . A naplók beolvasásához hajtsa végre a következő `kubectl` parancsot. A rendszer minden, a fürt autoskálázásával konfigurált csomópont-készletre vonatkozó állapotot fog jelenteni.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Ha többet szeretne megtudni arról, hogy mi történik az autoskálázással, olvassa el a gyakori kérdések a [Kubernetes/autoscaleer GitHub-projektben][kubernetes-faq]című témakört.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>A fürt autoskálázásának használata több Node-készlettel engedélyezve

A fürt autoskálázása egyszerre [több Node-készlettel][aks-multiple-node-pools] is használható. Ezt a dokumentumot követve megtudhatja, hogyan engedélyezheti több csomópontos készletet, és hogyan adhat hozzá további csomópont-készleteket egy meglévő fürthöz. Mindkét funkció együttes használatakor engedélyezheti a fürt automatikus méretezését a fürt minden egyes csomópontján, és egyedi automatikus skálázási szabályokat adhat át mindegyiknek.

Az alábbi parancs feltételezi, hogy követte a jelen dokumentum korábbi részében ismertetett [kezdeti útmutatást](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) , és frissíteni szeretné egy meglévő csomópont-készlet maximális darabszámát *3* és *5* között. A meglévő Node-készlet beállításainak frissítéséhez használja az az [AK nodepool Update][az-aks-nodepool-update] parancsot.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

A fürt automéretezője le lehet tiltani az [az AK nodepool Update paranccsal][az-aks-nodepool-update] , és a paramétert kell átadni `--disable-cluster-autoscaler` .

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Ha újra engedélyezni szeretné a fürt automéretezőjét egy meglévő fürtön, újra engedélyezheti azt az az [AK nodepool Update][az-aks-nodepool-update] paranccsal, amely megadja a `--enable-cluster-autoscaler` , a és a `--min-count` `--max-count` paramétereket.

> [!NOTE]
> Ha azt tervezi, hogy a fürt automéretezőjét olyan nodepools használja, amely több zónára terjed ki, és kihasználja az olyan zónákhoz kapcsolódó ütemezési funkciókat, mint például a kötet topológia-ütemezése, az ajánlásnak egy nodepool kell rendelkeznie, és engedélyeznie kell az `--balance-similar-node-groups` autoskálázási profilt. Így biztosítható, hogy az autoskálázás sikeresen felskálázásra kerüljön, és megpróbálkozik a nodepools méretével.

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt mutatja be, hogyan lehet automatikusan méretezni az AK-csomópontok számát. A vízszintes Pod automatikus méretezés használatával automatikusan módosíthatja az alkalmazást futtató hüvelyek számát. A horizontális Pod automéretező használatának lépéseiért lásd: [alkalmazások méretezése az AK-ban][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
