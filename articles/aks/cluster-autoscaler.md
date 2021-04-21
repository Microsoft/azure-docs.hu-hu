---
title: Az automatikus fürtméretozó használata Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan skálázhatja automatikusan a fürtöt az automatikus fürtskála-skálázóval, hogy megfeleljen az alkalmazásigénynek egy Azure Kubernetes Service (AKS) fürtben.
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: f02b91f73320786716e356639d8134280325dc19
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775996"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Fürt automatikus skálázása az alkalmazás igényeinek kielégítéséhez az Azure Kubernetes Service-ben (AKS)

Ahhoz, hogy lépést tartson a számítási Azure Kubernetes Service (AKS) alkalmazásigényeivel, előfordulhat, hogy módosítania kell a számítási feladatokat futtató csomópontok számát. Az automatikus fürtméretozó összetevő figyelni tudja a fürtben az erőforrás-korlátozások miatt nem ütemezni nem tudó podokat. Problémák észlelésekor a csomópontkészletben lévő csomópontok száma az alkalmazásigénynek megfelelő mértékben nő. A csomópontok rendszeresen ellenőrzik, hogy nincsenek-e futó podok, és a csomópontok száma ezután szükség szerint csökken. Az AKS-fürtben található csomópontok számának automatikus fel- vagy leskálá- és felskálái lehetővé teszi egy hatékony, költséghatékony fürt futtatását.

Ez a cikk bemutatja, hogyan engedélyezheti és kezelheti az automatikus fürtméretozót egy AKS-fürtben.

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI 2.0.76-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>Az automatikus fürtméretozó

Ahhoz, hogy alkalmazkodni lehet az alkalmazás változó igényeihez, például a munkanapok és az esti órák között, vagy egy hétvégen, a fürtöknek gyakran szükségük van egy automatikus skálázható módszerre. Az AKS-fürtök kétféleképpen méretezhetők:

* Az **automatikus fürtméretozó** olyan podokat figyel, amelyek erőforrás-korlátozások miatt nem ütemezhatóak a csomópontokon. A fürt ezután automatikusan megnöveli a csomópontok számát.
* Az **automatikus horizontális podméretozó** a Kubernetes-fürt Metrics Server kiszolgálóját használja a podok erőforrásigényének figyelése érdekében. Ha egy alkalmazásnak több erőforrásra van szüksége, a podok száma automatikusan nő az igényeknek megfelelően.

![Az automatikus fürtméretozó és a podok horizontális felskálálója gyakran működik együtt a szükséges alkalmazásigények kielégítésében](media/autoscaler/cluster-autoscaler.png)

A podok automatikus horizontális skálázási és fürtméretozója is csökkentheti a podok és csomópontok számát, ha szükséges. Az automatikus fürtméretozó csökkenti a csomópontok számát, ha egy ideig nem használt kapacitást. Az automatikus fürtméretozó által eltávolítható csomóponton található podok biztonságosan vannak ütemezve a fürt más részén. Előfordulhat, hogy az automatikus fürtméretozó nem tud leskálát végezni, ha a podok nem tudnak áthelyezni, például a következő helyzetekben:

* A pod közvetlenül jön létre, és nem egy vezérlőobjektum, például egy üzemelő példány vagy replikakészlet által van létrehozva.
* A podkimaradási költségvetés (PDB) túl korlátozó, és nem teszi lehetővé, hogy a podok száma egy adott küszöbérték alá csökken.
* A podok olyan csomópontválasztókat vagy antiaffiniát használnak, amelyek nem amelyek egy másik csomóponton ütemezve nem lesznek becsatlva.

További információ arról, hogy az automatikus fürtskáláló hogyan nem skálázható le: Milyen típusú podok megakadályozhatják, hogy az automatikus fürtskáláló eltávolítsa [a csomópontokat?][autoscaler-scaledown]

Az automatikus fürtméretozó indítási paramétereket használ például a skálázási események és az erőforrás-küszöbértékek közötti időintervallumok esetében. További információ az automatikus fürtméretozó által használt paraméterekről: [Az automatikus skálázási profil használata.](#using-the-autoscaler-profile)

A fürtök és az automatikus horizontális podméretezők együtt is működnek, és gyakran egy fürtben vannak üzembe helyezni. Az automatikus horizontális podméretozó együttesen az alkalmazásigénynek megfelelő számú pod futtatására összpontosít. Az automatikus fürtméretozó az ütemezett podok támogatásához szükséges csomópontok számának futtatására összpontosít.

> [!NOTE]
> Az automatikus fürtméretozó használata esetén a manuális skálázás le van tiltva. Hagyja, hogy az automatikus fürtméretozó határozza meg a csomópontok szükséges számát. Ha manuálisan szeretné méretezni a fürtöt, [tiltsa le az automatikus fürtméretozót.](#disable-the-cluster-autoscaler)

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS-fürt létrehozása és az automatikus fürtméretozó engedélyezése

Ha létre kell hoznia egy AKS-fürtöt, használja [az az aks create][az-aks-create] parancsot. Ha engedélyezni és konfigurálni szeretné az automatikus fürtméretozót a fürt csomópontkészletében, használja a paramétert, és adjon meg `--enable-cluster-autoscaler` egy és egy `--min-count` `--max-count` csomópontot.

> [!IMPORTANT]
> Az automatikus fürtméretozó egy Kubernetes-összetevő. Bár az AKS-fürt virtuálisgép-méretezési csoportokat használ a csomópontokhoz, ne engedélyezze vagy szerkessze manuálisan a méretezési csoport automatikus skálázási beállításait a Azure Portal az Azure CLI használatával. Hagyja, hogy a Kubernetes-fürt automatikus skálázási eszköze kezelje a szükséges skálázási beállításokat. További információ: [Módosítom az AKS-erőforrásokat a csomópont erőforráscsoportban?][aks-faq-node-resource-group]

Az alábbi példa egy AKS-fürtöt hoz létre egyetlen csomópontkészletben, amelyet egy virtuálisgép-méretezési csoport egészül ki. Emellett engedélyezi az automatikus fürtméretozót a fürt csomópontkészletében, és legalább *1,* de legfeljebb *3* csomópontot állít be:

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

A fürt létrehozása és az automatikus fürtskála-beállítások konfigurálása néhány percet vesz igénybe.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Meglévő AKS-fürt frissítése az automatikus fürtméretozó engedélyezéséhez

Az [az aks update paranccsal][az-aks-update] engedélyezheti és konfigurálhatja az automatikus fürtméretozót a meglévő fürt csomópontkészletén. Használja a `--enable-cluster-autoscaler` paramétert, és adjon meg egy csomópontot és `--min-count` `--max-count` .

> [!IMPORTANT]
> Az automatikus fürtméretozó egy Kubernetes-összetevő. Bár az AKS-fürt virtuálisgép-méretezési csoportokat használ a csomópontokhoz, ne engedélyezze vagy szerkessze manuálisan a méretezési csoport automatikus skálázási beállításait a Azure Portal az Azure CLI használatával. Hagyja, hogy a Kubernetes-fürt automatikus skálázási eszköze kezelje a szükséges skálázási beállításokat. További információ: [Módosítom az AKS-erőforrásokat a csomópont erőforráscsoportban?][aks-faq-node-resource-group]

Az alábbi példa frissíti a meglévő AKS-fürtöt, hogy engedélyezze az automatikus fürtméretozót a fürt csomópontkészletében, és legalább *1* vagy legfeljebb *3* csomópontot állít be:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

A fürt frissítése és az automatikus fürtméretozó beállításainak konfigurálása néhány percet vesz igénybe.

## <a name="change-the-cluster-autoscaler-settings"></a>Az automatikus fürtméretozó beállításainak módosítása

> [!IMPORTANT]
> Ha az AKS-fürtben több csomópontkészlet található, ugorjon a több ügynökkészletet is tartalmazó automatikus [skálázás szakaszra.](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled) A több ügynökkészletet is rendelkező fürtöknek a parancskészlet használatával kell módosítaniuk a csomópontkészlet specifikus tulajdonságait `az aks nodepool` a `az aks` helyett.

Az előző lépésben egy AKS-fürt létrehozásához vagy egy meglévő csomópontkészlet frissítéséhez az automatikus fürtméretozó minimális csomópontszáma *1,* a csomópontok maximális száma pedig *3* volt. Mivel az alkalmazásnak változnia kell, előfordulhat, hogy módosítania kell az automatikus fürtméretozó csomópontszámát.

A csomópontok számát az [az aks update paranccsal módosíthatja.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

A fenti példa a *myAKSCluster* egycsomópontos készletében lévő automatikus fürtméretozót legalább *1,* de legfeljebb *5 csomópontra* frissíti.

> [!NOTE]
> Az automatikus fürtméretozó az egyes csomópontkészletek minimális és maximális száma alapján hoz skálázási döntéseket, de a minimális vagy maximális darabszám frissítése után nem kényszeríti ki őket. Ha például az aktuális csomópontszám 3,5 lesz, akkor a rendszer nem skálázja fel azonnal a készletet 5-re. Ha a csomópontkészlet minimális száma az aktuális csomópontok számnál magasabb, akkor az új minimális vagy maximális beállítások akkor lesznek érvényesek, ha elegendő nem ütemezhető pod van jelen, ami 2 új csomópontot igényel, és automatikus skálázási eseményt aktivál. A méretezési esemény után az új darabszámkorlátok is teljesülnek.

Figyelje az alkalmazások és szolgáltatások teljesítményét, és állítsa be az automatikus fürtméretező csomópontszámait a szükséges teljesítménynek megfelelően.

## <a name="using-the-autoscaler-profile"></a>Az automatikus skálázási profil használata

A fürt automatikus skálázási profiljában az alapértelmezett értékek módosításával konfigurálhatja a fürt automatikus skálázási profiljának részletesebb adatait is. Egy leskálásos esemény például akkor következik be, ha a csomópontok kihasználtása 10 perc után nincs kihasználva. Ha 15 percenként futtatott számítási feladatokat, akkor 15 vagy 20 perc után módosíthatja az automatikus skálázási profilt, hogy a kihasznált csomópontok alatt skálázható legyen. Ha engedélyezi az automatikus fürtméretozót, a rendszer egy alapértelmezett profilt használ, kivéve, ha eltérő beállításokat ad meg. Az automatikus fürtméretozó profilja a következő frissítheti a beállításokat:

| Beállítás                          | Leírás                                                                              | Alapértelmezett érték |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| beolvasási időköz                    | A fürtök fel- vagy leskálanásra vonatkozó újra kiértékelésének milyen gyakran történik                                    | 10 másodperc    |
| leskálskálás késleltetés hozzáadása után       | Mennyi idő után folytatódik a horizontális felskálásos leskálás kiértékelése?                               | 10 perc    |
| scale-down-delay-after-delete    | Mennyi idő után folytatódik a leskálásos leskálásos kiértékelés a csomópont törlése után?                          | beolvasási időköz |
| scale-down-delay-after-failure   | Mennyi ideig folytatódik a leskálásos leskálás sikertelen leskálás utáni értékelés?                     | 3 perc     |
| nem szükségtelen leskálásos leskálás         | Mennyi ideig kell szükségtelenné lennie egy csomópontnak a leskálás előtt?                  | 10 perc    |
| leskálás nélküli leskálás ideje          | Mennyi ideig kell szükségtelenné lennie egy olvasatlan csomópontnak, mielőtt jogosulttá lenne a leskálálásra         | 20 perc    |
| leskálásos kihasználtság küszöbértéke | A csomópont kihasználtságának szintje, amely a kért erőforrások kapacitások szerint elosztva van meghatározva, amely alatt egy csomópontot felskálásnak lehet tekinteni | 0,5 |
| max-graceful-termination-sec     | Másodpercek maximális száma, amíg az automatikus fürtskála-felskálazó a podok befejezésére vár a csomópontok leskálizásakor | 600 másodperc   |
| balance-similar-node-groups      | Észleli a hasonló csomópontkészleteket, és elegyenli közöttük a csomópontok számát                 | hamis         |
| Expander                         | A [felskálásban](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) használt csomópontkészlet-bővítő típusa. Lehetséges értékek: `most-pods` , `random` , `least-waste` , `priority` | Véletlen | 
| skip-nodes-with-local-storage    | Ha a valódi automatikus fürtméretozó soha nem törli a helyi tárterületű podokkal (például EmptyDir vagy HostPath) lévő csomópontokat | true |
| skip-nodes-with-system-pods      | Ha a valódi automatikus fürtméretozó soha nem töröl podokkal csomópontokat a Kube-rendszerből (kivéve a DaemonSet vagy a tükrözött podokat) | true | 
| max-empty-bulk-delete            | Az egyidejűleg törölhető üres csomópontok maximális száma                       | 10 csomópont      |
| new-pod-scale-up-delay           | Olyan forgatókönyvek esetén, mint például a burst/batch scale (burst/batch scale) olyan forgatókönyvek esetén, ahol nem szeretné, hogy a CA azelőtt járjon el, hogy a Kubernetes-ütemező ütemezné az összes podot, a CA-t arra is meg lehet szólni, hogy hagyja figyelmen kívül a nem ütemezett podokat, mielőtt azok egy bizonyos korra lenne szükség.                                                                                                                | 0 másodperc    |
| max-total-unready-percentage     | A fürtben lévő olvasatlan csomópontok maximális százalékos aránya. A százalékos érték túllépése után a CA leállítja a műveleteket | 45% |
| max-node-provision-time          | Az az időtartam, amely alatt az automatikus skálázó megvárja a csomópontok üzembe építését                           | 15 perc    |   
| ok-total-unready-count           | Engedélyezett olvasatlan csomópontok száma, a max-total-unready-percentage értéktől függetlenül            | 3 csomópont       |

> [!IMPORTANT]
> Az automatikus fürtskálás profil az automatikus fürtméretozót felhasználó összes csomópontkészletre hatással van. Csomópontkészletenként nem állíthat be automatikus skálázási profilt.
>
> Az automatikus fürtméretozó profilhoz az Azure CLI *2.11.1-es* vagy újabb verziójára van szükség. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Az automatikus fürtskála-profil beállítása meglévő AKS-fürtön

Az [az aks update paranccsal][az-aks-update-preview] és a *cluster-autoscaler-profile* paraméterrel beállíthatja az automatikus fürtméretozói profilt a fürtön. Az alábbi példa 30-as értékként konfigurálja a vizsgálati időköz beállítását a profilban.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Ha engedélyezi az automatikus fürtméretozót a fürtben található csomópontkészleten, ezek a fürtök az automatikus fürtskála-profilt is használni fogják. Például:

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
> Amikor beállít egy automatikus fürtméretozói profilt, az automatikus fürtméretozóval rendelkező meglévő csomópontkészletek azonnal használatba fognak indulni.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Automatikus fürtméretozó profil beállítása AKS-fürt létrehozásakor

A fürt létrehozásakor használhatja az *automatikus fürtméretozó-profil* paramétert is. Például:

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

A fenti parancs létrehoz egy AKS-fürtöt, és 30 másodpercként határozza meg a vizsgálati időközt a fürtre kiterjedő automatikus skálázási profilhoz. A parancs a fürt automatikus skálázási eszközét is engedélyezi a kezdeti csomópontkészleten, a csomópontok minimális számát 1-re, a csomópontok maximális számát pedig 3-ra állítja.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Fürt automatikus skálázási profiljának visszaállítása alapértelmezett értékekre

Az [az aks update paranccsal][az-aks-update-preview] állítsa alaphelyzetbe a fürtön az automatikus skálázási profilt.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Az automatikus fürtméretozó letiltása

Ha már nem szeretné használni az automatikus fürtméretozót, letilthatja az [az aks update][az-aks-update-preview] paranccsal, és megadhatja a `--disable-cluster-autoscaler` paramétert. A csomópontok nem törlődnek, ha az automatikus fürtméretozó le van tiltva.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Az automatikus fürtméretozó letiltása után manuálisan skálázhatja a fürtöt az [az aks scale paranccsal.][az-aks-scale] Ha az automatikus horizontális podméretozót használja, ez a szolgáltatás továbbra is fut, ha az automatikus fürtméretozó le van tiltva, de előfordulhat, hogy a podok nem ütemezhatók, ha az összes csomópont-erőforrás használatban van.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Letiltott automatikus fürtméretozó újra engedélyezése

Ha egy meglévő fürtön szeretné újra engedélyezni az automatikus fürtméretozót, újra engedélyezheti az [az aks update][az-aks-update-preview] paranccsal, és megadhatja a , és `--enable-cluster-autoscaler` `--min-count` `--max-count` paramétereket.

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Automatikus fürtméretozó naplóinak és állapotának lekérése

Az automatikus skálázási események diagnosztizálásához és hibakereséshez a naplók és állapotok az automatikus skálázási bővítményből olvashatók be.

Az AKS felügyeli az automatikus fürtméretozót az Ön nevében, és futtatja azt a felügyelt vezérlősíkon. Engedélyezheti a vezérlősík csomópontját a ca naplói és műveletei megtekintéséhez.

Az automatikus fürtméretozóból a Log Analyticsbe lekért naplók konfigurálhoz kövesse az alábbi lépéseket.

1. Állítson be egy szabályt az erőforrásnaplókhoz a fürt automatikus skálázási naplóinak a Log Analyticsbe való leküldéséhez. [Az utasítások itt vannak részletezve,][aks-view-master-logs]győződjön meg arról, hogy be van-e va a `cluster-autoscaler` jelölőnégyzete a "Naplók" beállításainak kiválasztásakor.
1. Válassza a fürt "Naplók" szakaszát a Azure Portal.
1. Adja meg a következő példalekérdezéseket a Log Analyticsben:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

A naplóknak az alábbi példához hasonlónak kell lennie, ha vannak lekérhető naplók.

![Log Analytics-naplók](media/autoscaler/autoscaler-logs.png)

Az automatikus fürtméretozó egy nevű névre is kiírja az `configmap` `cluster-autoscaler-status` állapotokat. A naplók lekérése a következő `kubectl` paranccsal hajtható végre. Az automatikus fürtméretozóval konfigurált csomópontkészletek állapotjelentést fognak tartalmazni.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Ha többet szeretne megtudni arról, hogy mi van naplózva az automatikus skálázóból, olvassa el a [Kubernetes/autoscaler GitHub-projekttel][kubernetes-faq]kapcsolatos gyakori kérdéseket.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Az automatikus fürtméretozó használata engedélyezett több csomópontkészlet esetén

Az automatikus fürtméretozó több csomópontkészlet engedélyezése mellett [is][aks-multiple-node-pools] használható. A dokumentumból megtudhatja, hogyan engedélyezhet több csomópontkészletet, és hogyan adhat hozzá további csomópontkészleteket egy meglévő fürthöz. Ha mindkét funkciót együtt használja, engedélyezi az automatikus fürtméretozót a fürt minden egyes csomópontkészletében, és egyedi automatikus skálázási szabályokat ad át mindegyiknek.

Az alábbi parancs feltételezi, [](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) hogy követte a dokumentum korábbi szakaszában található kezdeti utasításokat, és egy meglévő csomópontkészlet maximális számát *3-tól* *5-ig* szeretné frissíteni. Egy meglévő csomópontkészlet beállításainak frissítéséhez használja az [az aks nodepool update][az-aks-nodepool-update] parancsot.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Az automatikus fürtméretozó az [az aks nodepool frissítéssel][az-aks-nodepool-update] és a paraméter átadásával `--disable-cluster-autoscaler` tiltható le.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Ha egy meglévő fürtön szeretné újra engedélyezni az automatikus fürtméretozót, az [az aks nodepool update][az-aks-nodepool-update] paranccsal újra engedélyezheti, és megadhatja a , és `--enable-cluster-autoscaler` `--min-count` `--max-count` paramétereket.

> [!NOTE]
> Ha azt tervezi, hogy az automatikus fürtskálálót olyan csomópontkészletekkel használja, amelyek több zónára is kiterjednek, és a zónákhoz kapcsolódó ütemezési funkciókat( például a kötet topológiai ütemezését) használják, azt javasoljuk, hogy zónánként egy csomópontkészletet tartalmaz, és engedélyezze a szolgáltatást az automatikus skálázási `--balance-similar-node-groups` profilon keresztül. Ezzel biztosíthatja, hogy az automatikus skálázási eszköz sikeresen felskálálódhat, és megpróbálhatja kiegyensúlyozottan tartani a csomópontkészletek méretét.

## <a name="next-steps"></a>Következő lépések

Ez a cikk az AKS-csomópontok számának automatikus méretezését mutatta be. Az automatikus horizontális podméretozóval automatikusan beállíthatja az alkalmazást futtató podok számát. Az automatikus horizontális podméretozó használatának lépéseiért lásd: [Alkalmazások skálázása az AKS-ban.][aks-scale-apps]

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why