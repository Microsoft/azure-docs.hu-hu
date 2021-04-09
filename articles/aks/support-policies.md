---
title: Támogatási szabályzatok az Azure Kubernetes Service (ak) szolgáltatáshoz
description: Ismerje meg az Azure Kubernetes Service (ak) támogatási szabályzatait, a közös felelősséget és az előzetes verzióban (vagy az Alpha-ban vagy a Beta-ban) elérhető szolgáltatásokat.
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 86b1c0bba30b41a2ee17cfbdf05286c4d2b3fb8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91892710"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Támogatási szabályzatok az Azure Kubernetes Service-hez

Ez a cikk részletesen ismerteti az Azure Kubernetes Service (ak) technikai támogatási szabályzatait és korlátozásait. A cikk az ügynök-csomópontok felügyeletét, a felügyelt vezérlési sík összetevőit, a harmadik féltől származó nyílt forráskódú összetevőket, valamint a biztonsági vagy javítási felügyeletet is ismerteti.

## <a name="service-updates-and-releases"></a>Szolgáltatások frissítései és kiadásai

* A kiadással kapcsolatos információkért lásd az [AK kibocsátási megjegyzéseit](https://github.com/Azure/AKS/releases).
* Az előzetes verzió szolgáltatásaival kapcsolatos információkért lásd: az [AK előzetes verziójának funkciói és kapcsolódó projektjei](https://awesomeopensource.com/projects/aks?categoryPage=11).

## <a name="managed-features-in-aks"></a>Felügyelt funkciók az AK-ban

Az alap infrastruktúra szolgáltatásként (IaaS) Felhőbeli összetevői, például a számítási vagy hálózatkezelési összetevők lehetővé teszik az alacsony szintű vezérlők és a testreszabási lehetőségek elérését. Ezzel szemben az AK egy kulcsrakész Kubernetes üzembe helyezést biztosít, amely a fürthöz szükséges konfigurációk és képességek közös készletét biztosítja. Egy AK-felhasználóként korlátozott testreszabási és üzembe helyezési lehetőségek állnak rendelkezésre. Az Exchange-ben nem kell aggódnia, vagy közvetlenül a Kubernetes-fürtöket kell kezelnie.

Az AK-val teljes körűen felügyelt *vezérlési síkot* kap. A vezérlő síkja tartalmazza az összes szükséges összetevőt és szolgáltatást, és Kubernetes-fürtöket biztosít a végfelhasználók számára. Az összes Kubernetes-összetevőt a Microsoft tartja karban és üzemelteti.

A Microsoft a következő összetevőket felügyeli és figyeli a vezérlés ablaktáblán:

* Kubelet vagy Kubernetes API-kiszolgálók
* Etcd vagy egy kompatibilis kulcs-érték tároló, amely a szolgáltatásminőség (QoS), a méretezhetőség és a futtatókörnyezet biztosítására szolgál.
* DNS-szolgáltatások (például Kube-DNS vagy CoreDNS)
* Kubernetes proxy vagy hálózatkezelés
* Minden további addon vagy rendszerösszetevő, amely a Kube-System névtérben fut

Az AK nem a szolgáltatásként nyújtott platform ("Pásti") megoldás. Egyes összetevők, például az ügynökök csomópontjai *megosztott felelősséggel* rendelkeznek, ahol a felhasználóknak segíteniük kell az AK-fürt fenntartásában. Felhasználói bevitelre van szükség, például az ügynök csomópont operációs rendszer (OS) biztonsági javításának alkalmazásához.

A szolgáltatások *kezelése* abban az értelemben történik, hogy a Microsoft és az AK-csoport üzembe helyezi, működteti és felelős a szolgáltatás rendelkezésre állásának és működésének biztosításáért. Az ügyfelek nem változtathatják meg ezeket a felügyelt összetevőket. A Microsoft korlátozza a testreszabást, hogy konzisztens és skálázható felhasználói élményt biztosítson. A teljes mértékben testreszabható megoldásért lásd: [AK-motor](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Megosztott feladatkörök

Fürt létrehozásakor meg kell adnia a Kubernetes-ügynök által létrehozott csomópontokat. A számítási feladatok ezen a csomópontokon futnak.

Mivel az ügynök csomópontjai privát kódokat hajtanak végre, és bizalmas adatokat tárolnak, Microsoft ügyfélszolgálata csak nagyon korlátozott módon férhetnek hozzájuk. Microsoft ügyfélszolgálata nem tud bejelentkezni a alkalmazásba, végrehajthatja a parancsait, vagy megtekintheti a csomópontok naplóit kifejezett engedélye vagy segítsége nélkül.

Minden olyan módosítás, amely közvetlenül az ügynök csomópontjain történik, a IaaS API-k bármelyikét használva a fürt nem támogatott. Az ügynökök csomópontjain végrehajtott módosításokat a kubernetes-natív mechanizmusok, például a használatával kell elvégezni `Daemon Sets` .

Hasonlóképpen, miközben a fürthöz és a csomópontokhoz (például címkékhez és címkékhez) metaadatokat adhat hozzá, a rendszer által létrehozott metaadatok bármelyikének módosítása a fürtöt nem fogja támogatni.

## <a name="aks-support-coverage"></a>AK-támogatás lefedettsége

A Microsoft a következő példákhoz nyújt technikai támogatást:

* A Kubernetes szolgáltatás által biztosított és támogatott összes Kubernetes-összetevő, például az API-kiszolgáló kapcsolata.
* A Kubernetes Control Plant-szolgáltatások (Kubernetes-vezérlési sík, API-kiszolgáló, etcd és coreDNS) felügyelete, üzemidő, QoS és műveletei.
* Etcd-adattár. A támogatás 30 percenként, a etcd és a fürt állapotának visszaállítására vonatkozó, automatizált, transzparens biztonsági mentéseket tartalmaz. Ezek a biztonsági másolatok nem érhetők el közvetlenül Ön vagy bármely felhasználó számára. Biztosítják az adatmegbízhatóságot és a konzisztenciát. Etcd. az igény szerinti visszaállítás vagy visszaállítás szolgáltatásként nem támogatott.
* Bármely integrációs pont az Azure Cloud Provider-illesztőprogramban a Kubernetes-hez. Ezek közé tartoznak más Azure-szolgáltatások, például a terheléselosztó, az állandó kötetek vagy a hálózatkezelés (Kubernetes és Azure CNI) integrációja.
* A vezérlési sík összetevőinek, például a Kubernetes API-kiszolgáló, a etcd és a coreDNS testreszabásával kapcsolatos kérdések és problémák.
* A hálózatkezeléssel kapcsolatos problémák, például az Azure CNI, a kubenet vagy más hálózati hozzáférési és működési problémák. A problémák magukban foglalhatják a DNS-feloldást, a csomagok elvesztését, az útválasztást és így tovább. A Microsoft különböző hálózati forgatókönyveket támogat:
  * A Kubenet és az Azure CNI felügyelt virtuális hálózatok vagy egyéni (saját) alhálózatokat használ.
  * Kapcsolódás más Azure-szolgáltatásokhoz és alkalmazásokhoz
  * Bejövő vezérlők és bejövő vagy terheléselosztó konfigurációk
  * Hálózati teljesítmény és késés


> [!NOTE]
> A Microsoft/AK által végrehajtott összes művelet felhasználói beleegyezett a beépített Kubernetes `aks-service` -szerepkör és a beépített szerepkör-kötés alá `aks-service-rolebinding` . Ez a szerepkör lehetővé teszi az AK számára a fürtökkel kapcsolatos problémák hibaelhárítását és diagnosztizálását, de nem módosíthatja az engedélyeket, és nem hozhat létre szerepköröket vagy szerepkör-kötéseket, illetve egyéb magas jogosultsági A szerepkör-hozzáférés csak az aktív támogatási jegyek esetében engedélyezett az igény szerinti (JIT) hozzáféréssel.

A Microsoft nem nyújt technikai támogatást az alábbi példákhoz:

* Kérdések a Kubernetes használatáról. A Microsoft ügyfélszolgálata például nem ad tanácsot az egyéni bejövő vezérlők létrehozásához, az alkalmazások számítási feladatainak használatához, vagy harmadik féltől származó vagy nyílt forráskódú szoftvercsomagok vagy eszközök alkalmazásához.
  > [!NOTE]
  > Microsoft ügyfélszolgálata tud tanácsot adni az AK-fürtök működéséről, testreszabásáról és finomhangolásáról (például Kubernetes működési problémák és eljárások).
* Harmadik féltől származó nyílt forráskódú projektek, amelyek nem a Kubernetes-vezérlési sík részeként vannak megadva, vagy AK-fürtökkel vannak telepítve. Ezek a projektek lehetnek Istio, Helm, megbízottat vagy mások.
  > [!NOTE]
  > A Microsoft a harmadik féltől származó nyílt forráskódú projektek, például a Helm számára nyújt legjobb támogatást. Ha a harmadik féltől származó nyílt forráskódú eszköz integrálva van a Kubernetes Azure Cloud providerrel vagy más AK-specifikus hibákkal, a Microsoft a Microsoft dokumentációjában szereplő példákat és alkalmazásokat támogatja.
* Harmadik féltől származó zárt forrású szoftver. Ez a szoftver biztonsági ellenőrzési eszközöket és hálózati eszközöket, illetve szoftvereket tartalmazhat.
* A hálózati testreszabások nem az [AK-dokumentációban](./index.yml)felsoroltak.


## <a name="aks-support-coverage-for-agent-nodes"></a>AK-támogatás lefedettsége az ügynök csomópontjain

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>Microsoft-feladatok az AK-ügynökök csomópontjaihoz

A Microsoft és a felhasználók megosztják a Kubernetes-ügynök csomópontjainak felelősségét, ahol:

* Az alap operációs rendszer rendszerképének a szükséges kiegészítései vannak (például figyelési és hálózati ügynökök).
* Az ügynök csomópontjai automatikusan kapják meg az operációs rendszer javításait.
* Az ügynökök csomópontjain futó Kubernetes-vezérlési sík-összetevőkkel kapcsolatos problémákat a rendszer automatikusan szervizeli. Ezek az összetevők a következők:
  * `Kube-proxy`
  * Hálózati alagutak, amelyek kommunikációs útvonalakat biztosítanak a Kubernetes fő összetevőinek
  * `Kubelet`
  * `Moby` vagy `ContainerD`

> [!NOTE]
> Ha az ügynök csomópontja nem működik, az AK újraindíthatja az egyes összetevőket vagy a teljes ügynök csomópontot. Ezek az újraindítási műveletek automatizáltak, és automatikus szervizelést biztosítanak a gyakori problémákhoz. Ha többet szeretne tudni az automatikus szervizelési mechanizmusokról, tekintse meg a [csomópontok automatikus javítását](node-auto-repair.md) ismertető témakört.

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>Az AK-ügynökök csomópontjaihoz tartozó ügyfél-felelősségi körök

A Microsoft a lemezkép-csomópontok számára hetente javításokat és új lemezképeket biztosít, de alapértelmezés szerint nem javítja automatikusan őket. Ha szeretné megtartani az ügynök csomópont operációs rendszerének és a futásidejű összetevőinek javítását, érdemes megtartania a [csomópontok rendszeres frissítésének](node-image-upgrade.md) ütemtervét, vagy automatizálni azt.

Hasonlóképpen, az AK rendszeresen mentesíti az új kubernetes-javításokat és a másodlagos verziókat. Ezek a frissítések a Kubernetes biztonsági vagy funkcionalitásbeli újdonságait is tartalmazhatják. Ön felelős a fürtök kubernetes-verziójának frissítésével és a [kubernetes-támogatási verzió szabályzatának](supported-kubernetes-versions.md)megfelelően.

#### <a name="user-customization-of-agent-nodes"></a>Az ügynökök csomópontjainak felhasználói testreszabása
> [!NOTE]
> Az AK-ügynökök csomópontjai a Azure Portalban normál Azure IaaS-erőforrásokként jelennek meg. Ezek a virtuális gépek azonban egy egyéni Azure-erőforráscsoporthoz vannak telepítve (általában MC_ \* ). Az alap operációsrendszer-rendszerkép nem módosítható, vagy a IaaS API-k vagy erőforrások használatával nem végezheti el a csomópontok közvetlen testreszabását. Az AK API-n keresztül nem végrehajtott egyéni módosítások nem maradnak meg frissítés, méretezés, frissítés vagy újraindítás útján. Ne hajtsa végre a módosításokat az ügynök csomópontjain, kivéve, ha a Microsoft ügyfélszolgálata a módosítások elvégzésére utasítja.

Az AK kezeli az ügynök-csomópontok életciklusát és műveleteit az Ön nevében – az ügynök-csomópontokhoz társított IaaS-erőforrások módosítása **nem támogatott**. A nem támogatott műveletek egyik példája a Node Pool virtuálisgép-méretezési csoport testreszabása a konfigurációk manuális módosításával a virtuálisgép-méretezési csoport portálon vagy az API-n keresztül.
 
A munkaterhelés-specifikus konfigurációk vagy csomagok esetében az AK a [Kubernetes `daemon sets` ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)használatát javasolja.

A Kubernetes privilegizált `daemon sets` és az init-tárolók használata lehetővé teszi, hogy a külső gyártótól származó szoftvereket a fürt ügynök csomópontjain hangolja/módosítsa vagy telepítse. Ilyen testreszabási példák például az egyéni biztonsági ellenőrzési szoftverek hozzáadása vagy a sysctl-beállítások frissítése.

Ez az elérési út akkor ajánlott, ha a fenti követelmények érvényesek, az AK-tervezés és-támogatás nem tud segítséget nyújtani a csomópontot egy egyéni telepítés miatt nem elérhetővé tehető módosítások hibaelhárításában vagy diagnosztizálásában `daemon set` .

### <a name="security-issues-and-patching"></a>Biztonsági problémák és javítás

Ha egy vagy több AK-beli felügyelt összetevőben biztonsági hiba található, az AK csapata az összes érintett fürtöt kijavítja a probléma enyhítése érdekében. Azt is megteheti, hogy a csapat a felhasználók számára is frissíti az útmutatót.

A biztonsági hiányosságok által érintett ügynökök csomópontjai esetén a Microsoft értesíti Önt a hatásról, valamint a biztonsági probléma elhárításához vagy megoldásához szükséges lépésekről (általában a csomópont-rendszerkép frissítése vagy a fürt javításának frissítése).

### <a name="node-maintenance-and-access"></a>Csomópont-karbantartás és-hozzáférés

Habár bejelentkezhet és módosíthatja az ügynökök csomópontjait, ezt a műveletet nem lehet megtenni, mert a módosítások nem támogatják a fürtöt.

## <a name="network-ports-access-and-nsgs"></a>Hálózati portok, hozzáférés és NSG

A NSG csak egyéni alhálózatokon lehet testreszabni. A NSG nem szabhatók testre a felügyelt alhálózatokon vagy az ügynök-csomópontok hálózati adapter szintjén. Az AK kilépési követelményekkel rendelkezik bizonyos végpontokra, a kimenő forgalom szabályozására és a szükséges kapcsolatok biztosítására: a [kimenő forgalom korlátozása](limit-egress-traffic.md).

## <a name="stopped-or-de-allocated-clusters"></a>Leállított vagy kiosztott fürtök

Ahogy korábban már említettük, a IaaS API-k/CLI/portál használatával manuálisan, az összes fürtcsomópont kiosztásával a fürt kikerül a támogatásból. Az egyetlen támogatott mód az összes csomópont leállítására/lefoglalására az [AK-fürt leállítása](start-stop-cluster.md#stop-an-aks-cluster), amely akár 12 hónapig is megőrzi a fürt állapotát.

A több mint 12 hónapig leállított fürtök többé nem őrzik meg az állapotot. 

Az AK API-kkal nem rendelkező fürtök nem rendelkeznek állapot-megőrzési garanciákkal. Az ebben az állapotban lévő fürtök vezérlési gépei 30 nap után törlődnek, és 12 hónap elteltével törlődnek.

Az AK fenntartja a jogot arra, hogy archiválja azokat a vezérlési síkokat, amelyek a további 30 napnál nem hosszabb időszakra vonatkozó támogatási irányelvek alapján vannak konfigurálva. Az AK biztonsági mentéseket tart fenn a fürt etcd metaadataiban, és a fürt könnyen újra kiosztható. Ezt az újraosztást bármely PUT művelet kezdeményezheti, amely a fürtöt visszahelyezi a támogatásba, például frissítésre vagy méretezésre az Active Agent-csomópontokra.

Ha az előfizetés fel van függesztve vagy törölve van, akkor a fürt vezérlési síkja és állapota 90 nap után törlődik.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Az Alpha és a Beta Kubernetes funkciói nem támogatottak

Az AK-ban a felsőbb rétegbeli Kubernetes projektben csak a stabil és a bétaverziós funkciók támogatottak. Ha másként nincs dokumentálva, az AK nem támogatja a felsőbb rétegbeli Kubernetes projektben elérhető alfa-funkciók használatát.

## <a name="preview-features-or-feature-flags"></a>Előzetes verziójú funkciók vagy szolgáltatások jelzői

A kiterjesztett tesztelést és felhasználói visszajelzéseket igénylő funkciók és funkciók esetében a Microsoft új előzetes verziójú funkciókat vagy szolgáltatásokat bocsát ki a szolgáltatás jelölője mögött. Ezeket a funkciókat előzetes vagy bétaverziós funkciókként érdemes figyelembe venni.

Az előzetes verziójú funkciók vagy a szolgáltatás-jelölő funkciók nem éles környezetben használhatók. Az API-k és a viselkedés, a hibajavítások és más változások folyamatos változásai instabil fürtöket és állásidőt okozhatnak.

A nyilvános előzetes verzióban elérhető funkciók a "legjobb erőfeszítés" támogatásban részesülnek, mivel ezek a funkciók előzetes verzióban érhetők el, és nem éles környezetben, és az AK technikai támogatási csapatai csak munkaidőben támogatottak. További információkért lásd:

* [Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>Felsőbb rétegbeli hibák és problémák

A felsőbb rétegbeli Kubernetes projekt fejlesztésének gyorsasága miatt a hibák mindig felmerülhetnek. Ezen hibák némelyike nem oldható fel és nem dolgozható fel az AK-rendszeren belül. A hibajavítások Ehelyett nagyobb javítások szükségesek a felsőbb rétegbeli projektekhez (például Kubernetes, node vagy Agent operációs rendszerekhez és kernelekhez). A Microsoft tulajdonában lévő összetevők (például az Azure Cloud Provider), az AK és az Azure személyzete elkötelezett a Közösségen belüli felsőbb szintű problémák megoldásában.

Ha egy vagy több felsőbb rétegbeli hiba okozza a technikai támogatási problémát, az AK-támogatás és a mérnöki csapatok a következőket teszik:

* Azonosítsa és kapcsolja össze a felsőbb rétegbeli hibákat az összes kiegészítő részlettel, hogy elmagyarázza, miért befolyásolja a probléma a fürtjét vagy a munkaterhelést. Az ügyfelek a szükséges adattárakra mutató hivatkozásokat kapnak, így megtekinthetik a problémákat, és megtekinthetik, hogy egy új kiadás mikor fog javításokat nyújtani.
* Adja meg a lehetséges megkerülő megoldásokat vagy a megoldást. Ha a problémát enyhíteni lehet, a rendszer egy [ismert problémát](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) fog benyújtani az AK-tárházban. Az ismert problémákkal kapcsolatos bejelentés a következőket ismerteti:
  * A probléma, beleértve a felsőbb rétegbeli hibákra mutató hivatkozásokat is.
  * Megkerülő megoldás, valamint a frissítés vagy a megoldás más megőrzésének részletei.
  * A probléma felvételének durva ütemezése a felsőbb rétegbeli kiadási lépésszám alapján.
