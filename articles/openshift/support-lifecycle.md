---
title: Azure Red Hat OpenShift-támogatás életciklusa
description: Ismerje meg az Azure Red Hat OpenShift támogatási életciklusát és támogatott verzióit
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: fca01c77a1ff47cbeee167eb408ed9f29a1307bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634328"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Az Azure Red Hat OpenShift 4 támogatási életciklusa

A Red Hat nagyjából három havonta kibocsátja a Red Hat OpenShift Container platform (OCP) alverzióit. Ezek a kiadások új funkciókat és fejlesztési lehetőségeket tartalmaznak. A javítási kiadások gyakoribbak (általában hetente), és csak a kritikus hibajavítások egy alverzión belül. Ezek a javítások a biztonsági rések vagy a jelentős hibák javítását is tartalmazhatják.

Az Azure Red Hat OpenShift a OCP meghatározott kiadásaiból épül fel. Ez a cikk az Azure Red Hat OpenShift által támogatott OCP-verziókat, valamint a frissítések, az elavult fájlok és a támogatási szabályzatok részleteit ismerteti.

## <a name="red-hat-openshift-versions"></a>Red Hat OpenShift-verziók

A Red Hat OpenShift-tároló platform szemantikai verziószámozást használ. A szemantikai verziószámozás különböző szintű verziószámokat használ a verziószámozás különböző szintjeinek megadásához. Az alábbi táblázat a szemantikai verziószám különböző részeit mutatja be, ebben az esetben a példa verziószámának 4.4.11.

|Főverzió (x)|Másodlagos verzió (y)|Javítás (z)|
|-|-|-|
|4|4|11|

A verzióban lévő egyes számok általános kompatibilitást jeleznek az előző verzióval:

* **Főverzió**: a rendszer jelenleg nem tervezi a főverziók kiadásait. A főverziók akkor változnak, ha a nem kompatibilis API-változások vagy a visszamenőleges kompatibilitás megszakad.
* **Alverzió**: körülbelül 3 havonta megjelent. Az alverziók frissítései tartalmazhatják a funkciók hozzáadását, a fejlesztéseket, az elavult, az eltávolítást, a hibajavításokat, a biztonsági fejlesztéseket és egyéb fejlesztéseket.
* **Javítások**: általában hetente vagy igény szerint szabadítanak fel. A javítás verziófrissítése hibajavításokat, biztonsági fejlesztéseket és egyéb fejlesztéseket tartalmazhat.

Az ügyfeleknek törekedniük kell a futtatott főverzió legújabb másodlagos kiadásának futtatására. Ha például az üzemi fürt 4,4-on van, és a 4,5 a 4 sorozat legújabb általánosan elérhető alverziója, akkor a lehető leghamarabb frissítsen a 4,5-ra.

### <a name="upgrade-channels"></a>Frissítési csatornák

A frissítési csatornák a Red Hat OpenShift Container platform (OCP) egy kisebb verziójához vannak kötve. Például a OCP 4,4 frissítési csatornái soha nem tartalmazzák a 4,5-es kiadásra való frissítést. A frissítési csatornák vezérlő csak a kiadás kijelölését szabályozza, és nem befolyásolja a fürt verzióját.

Az Azure Red Hat OpenShift 4 csak a stabil csatornákat támogatja. Például: STABLE-4,4.

A STABLE-4,5 csatornán keresztül frissítheti az Azure Red Hat OpenShift egy korábbi verzióját. A gyors, előzetes kiadású és a tagjelölt csatornákon keresztül frissített fürtöket nem támogatja a rendszer.

Ha olyan csatornára vált át, amely nem tartalmazza a jelenlegi kiadást, a rendszer riasztást jelenít meg, és nem javasol frissítéseket, de az eredeti csatornára bármikor visszatérhet.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>A Red Hat OpenShift-tároló platformjának támogatási szabályzata

Az Azure Red Hat OpenShift a Red Hat OpenShift Container platform két általánosan elérhető (GA) másodlagos verzióját támogatja:
* Az Azure Red Hat OpenShift kiadásban megjelent legújabb GA-alverzió (amelyet N-ként fogunk hivatkozni)
* Egy korábbi másodlagos verzió (N-1)

Ha elérhető egy stabil verziófrissítési csatornán, akkor a felsőbb rétegbeli OCP elérhető újabb másodlagos kiadások is támogatottak.

A kritikus javítások frissítéseit az Azure Red Hat OpenShift site megbízhatósági mérnökök (SRE-EK) automatikusan alkalmazza a fürtökre. Azok az ügyfelek, akik előre szeretnék telepíteni a javítási frissítéseket, szabadon dönthetnek.

Ha például az Azure Red Hat OpenShift jelenleg 4.5. z-es verzióját mutatja be, a támogatás a következő verziókhoz érhető el:

|Új alverzió|Támogatott verziók listája|
|-|-|
|4.5. z|4.5. z, 4.4. z|

a ". z" a javítási verziókra jellemző. Ha egy stabil frissítési csatornában érhető el, az ügyfelek frissíthetik a 4.6. z verzióra is.

Új alverzió bevezetésekor a legrégebbi alverzió elavult, és el lesz távolítva. Tegyük fel például, hogy a jelenleg támogatott verziók listája 4.5. z és 4.4. z. Ha az Azure Red Hat OpenShift 4.6. z kiadást bocsát ki, a 4.4. z kiadás megszűnik, és 30 napon belül nem lesz támogatott.

> [!NOTE]
> Vegye figyelembe, hogy ha az ügyfelek nem támogatott Red Hat OpenShift-verziót futtatnak, akkor a rendszer a fürt támogatásának kérelmezése után is megkérheti a frissítést. A nem támogatott Red Hat OpenShift-kiadásokat futtató fürtöket az Azure Red Hat OpenShift SLA-ja nem fedi le.

## <a name="release-and-deprecation-process"></a>Kiadási és elavult folyamat

Az Azure Red Hat OpenShift kiadási naptárában a közelgő verziókra és az elavult változatokra is hivatkozhat.

A Red Hat OpenShift Container platform új, másodlagos verzióihoz:
* Az Azure Red Hat OpenShift SRE csapata közzé tesz egy előzetes bejelentést az új verzió kiadásának tervezett dátumával, és a régi verziók elavult változatát az [Azure Red Hat OpenShift kibocsátási megjegyzésekben](https://github.com/Azure/OpenShift/releases) legalább 30 nappal az Eltávolítás előtt.
* Az Azure Red Hat OpenShift SRE csapata minden ügyfél számára elérhetővé tesz egy szolgáltatás-állapotra vonatkozó értesítést, amely az Azure Red Hat OpenShift és a portálon keresztül érhető el, és e-mailt küld az előfizetési rendszergazdáknak a tervezett verzió-eltávolítási dátumokkal.
* Az ügyfelek a verziók eltávolítása után 30 napig frissíthetnek a támogatott alverzióra, hogy továbbra is megkapják a támogatást.

A Red Hat OpenShift Container platform új javítási verzióihoz:
* A javítási verziók sürgős jellegéből adódóan ezek a szolgáltatások az Azure Red Hat OpenShift SRE csapata által elérhetővé válnak.
* Általánosságban elmondható, hogy az Azure Red Hat OpenShift SRE csapata nem végez széles körű kommunikációt az új javítási verziók telepítéséhez. A csapat azonban folyamatosan figyeli és ellenőrzi a rendelkezésre álló CVE-javításokat, hogy azok időben is támogassák őket. Ha az ügyfél beavatkozására van szükség, a csapat értesíti az ügyfeleket a frissítésről.

## <a name="supported-versions-policy-exceptions"></a>Támogatott verziók házirend-kivételei

Az Azure Red Hat OpenShift SRE csapata fenntartja a jogot az új/meglévő verziók hozzáadására vagy eltávolítására, vagy a közelgő másodlagos kiadási verziók késleltetésére, amelyek azonosítva lettek, hogy egy vagy több kritikus termelést befolyásoló hibát vagy biztonsági problémát észlelt az előzetes értesítés nélkül.

Előfordulhat, hogy a rendszer kihagyja a javítási kiadásokat, vagy a bevezetést a hiba vagy a biztonsági probléma súlyossága alapján gyorsítja fel.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal és CLI-verziók

Ha Azure Red Hat OpenShift-fürtöt telepít a portálon vagy az Azure CLI-vel, a fürt alapértelmezett értéke a legújabb (N) alverzió és a legújabb kritikus javítás. Ha például az Azure Red Hat OpenShift támogatja a 4.5. z és 4.4. z, az új telepítések alapértelmezett verziója 4.5. z. Azok az ügyfelek, akik a legújabb felsőbb rétegbeli OCP alverziót szeretnék használni (N + 1, N + 2), bármikor frissíthetik a fürtöt a stabil frissítési csatornákon elérhető kiadásokra.

## <a name="azure-red-hat-openshift-release-calendar"></a>Azure Red Hat OpenShift kiadási naptár

Tekintse meg az alábbi útmutatót a [korábbi Red Hat OpenShift Container platform (upstream) kiadási előzményeiről](https://access.redhat.com/support/policy/updates/openshift/#dates).

|OCP verziója|Felsőbb rétegbeli kiadás|Az Azure Red Hat OpenShift általánosan elérhető|Elhasználódott|
|-|-|-|-|
|4.3|2020. január|2020. április| 2020. augusztus|
|4.4|2020. május|2020. július|4,6 GA|
|4,5|2020. július| 2020. november|4,7 GA
|4,6|2020. október| 2021. február|4,8 GA|

## <a name="faq"></a>GYIK

**Mi történik, ha egy felhasználó egy alverzióval nem támogatott OpenShift-fürtöt frissít?**

Ha az N-2 vagy régebbi verziót használ, az azt jelenti, hogy a támogatáson kívül esik, és a rendszer a frissítésre kéri. Ha az N-2 verzióról N-1-re való frissítés sikeres, akkor a támogatási szabályzatunk keretében visszakerül. Például:
* Ha a legrégebbi támogatott Azure Red Hat OpenShift 4.4. z, és a 4.3. z vagy régebbi verzióban van, akkor a támogatáson kívül van.
* Ha a 4.3. z-ről 4.4. z vagy újabb verzióra történő frissítés sikeres, a támogatási szabályzatunk keretében visszakerül.

A fürt korábbi verzióra vagy visszaállításra való visszaállítása nem támogatott. Csak újabb verzióra való frissítés támogatott.

**Mit jelent a "támogatáson kívül" kifejezés?**

"A támogatáson kívül" azt jelenti, hogy a futtatott verzió a támogatott verziók listáján kívül esik, és előfordulhat, hogy a rendszer a támogatás kérelmezése után egy támogatott verzióra frissíti a fürtöt, kivéve, ha a verzió elavulttá tételét követő 30 napos türelmi időszakon belül van. Emellett az Azure Red Hat OpenShift a 30 napos türelmi időszak végén nem biztosít futtatókörnyezeti vagy SLA-garanciát a támogatott verziók listáján kívüli fürtökhöz.
