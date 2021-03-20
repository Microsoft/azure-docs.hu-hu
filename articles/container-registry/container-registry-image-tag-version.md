---
title: Ajánlott eljárások a képcímkéhez
description: Ajánlott eljárások a Docker-tároló rendszerképeinek címkézéséhez és verziószámozásához, amikor képeket küld és húz le egy Azure Container registryből
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75445736"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>A tároló lemezképének címkézésére és verziószámozására vonatkozó javaslatok

Amikor egy tároló-beállításjegyzékbe helyezi üzembe a tároló-lemezképek telepítését, majd telepíti őket, szüksége lesz egy, a képcímkézésre és a verziószámozásra vonatkozó stratégiára. Ebből a cikkből megtudhatja, hogyan illeszkedik a tároló életciklusa során a következő két módszerre:

* **Stabil címkék** – felhasználható címkék, például egy fő vagy alverzió (például *mycontainerimage: 1.0*) jelzése.
* **Egyedi címkék** – egy másik címkét minden olyan képhez, amelyet egy beállításjegyzékbe küld, például *mycontainerimage: abc123*.

## <a name="stable-tags"></a>Stabil Címkék

**Javaslat**: használjon stabil címkéket a tároló-buildek **alapképeinek** fenntartásához. Kerülje a stabil címkékkel való üzembe helyezést, mivel ezek a címkék továbbra is megkapják a frissítéseket, és következetlenségeket vezethetnek be az éles környezetekben.

A *stabil címkék* azt jelentik, hogy egy fejlesztő vagy egy összeállítási rendszer továbbra is lehívhat egy adott címkét, amely továbbra is frissítéseket kap. A stabil nem jelenti azt, hogy a tartalom zárolva van. Ehelyett a stabil a rendszerképnek stabilnak kell lennie az adott verzió céljához. A "stabil" maradni a biztonsági javítások vagy a keretrendszer frissítéseinek alkalmazásával lehet kiszolgálni.

### <a name="example"></a>Példa

A Framework Team-hajók 1,0-es verzióval rendelkeznek. Tudják, hogy frissítéseket küldenek, beleértve a kisebb frissítéseket is. Egy adott fő-és alverzióhoz tartozó stabil címkék támogatásához két stabil címkével rendelkeznek.

* `:1` – a főverzió stabil címkéje. `1` a "legújabb" vagy "legújabb" 1. * verziót jelöli.
* `:1.0`– az 1,0-es verzió stabil címkéje, amely lehetővé teszi a fejlesztők számára, hogy a 1,0-es frissítésekhez kapcsolódjanak, és ne legyenek továbbítva a 1,1-ra a kiadáskor.

A csapat a `:latest` címkét is használja, amely a legújabb stabil címkére mutat, függetlenül attól, hogy milyen a jelenlegi főverzió.

Ha az alapszintű lemezkép frissítései elérhetők, vagy a keretrendszer bármely típusú karbantartási kiadása, a stabil címkékkel rendelkező rendszerképek frissülnek a legújabb kivonatoló értékre, amely az adott verzió legújabb stabil kiadását jelöli.

Ebben az esetben a fő és a másodlagos címkéket is folyamatosan szervizeli a rendszer. Alaprendszerkép-forgatókönyv esetén ez lehetővé teszi, hogy a rendszerkép tulajdonosa kiszolgált lemezképeket biztosítson.

### <a name="delete-untagged-manifests"></a>Címkézetlen jegyzékfájlok törlése

Ha egy stabil címkével rendelkező képet frissít, a korábban címkézett Rendszerkép nem címkézett, és egy árva rendszerképet eredményez. Az előző rendszerkép jegyzékfájlja és az egyedi rétegbeli adatok a beállításjegyzékben maradnak. A beállításjegyzék méretének megőrzése érdekében rendszeresen törölheti a nem címkézett jegyzékfájlokat a stabil képfrissítések eredményeként. Például a megadott időtartamnál régebbi címkézetlen jegyzékfájlok [automatikus kitisztítása](container-registry-auto-purge.md) vagy a címkézetlen jegyzékfájlok [adatmegőrzési szabályzatának](container-registry-retention-policy.md) beállítása.

## <a name="unique-tags"></a>Egyedi címkék

**Javaslat**: használjon egyedi címkéket az üzemelő **példányokhoz**, különösen olyan környezetben, amely több csomópontra is méretezhető. Valószínűleg az összetevők konzisztens verziójának szándékos telepítését szeretné használni. Ha a tároló újraindul, vagy egy Orchestrator több példányra van kibővítve, a gazdagépek nem fognak véletlenül újabb verziót lekérni, és nem konzisztensek a többi csomóponttal.

Az egyedi címkézés egyszerűen azt jelenti, hogy a beállításjegyzékbe leküldött összes rendszerkép egyedi címkével rendelkezik. A címkék nem lesznek újra felhasználva. Az egyedi címkék létrehozásához több minta is készíthető, többek között a következők:

* **Dátum-idő bélyegző** – ez a megközelítés meglehetősen gyakori, mivel a rendszerkép létrehozásakor egyértelműen megtudhatja, hogy mikor készült. De hogyan lehet összekapcsolni a Build-rendszerrel? Meg kell keresnie az adott időpontban befejezett buildet? Milyen időzóna van? Az összes Build rendszerét UTC-re kalibrálták?
* **Git-véglegesítés**  – ez a megközelítés csak az alapszintű rendszerkép frissítéseinek támogatásának megkezdése után működik. Ha egy alaprendszerkép frissítése történik, a Build rendszer a korábbi buildtel megegyező git-véglegesítés után indul el. Az alaprendszerkép azonban új tartalommal rendelkezik. Általánosságban elmondható, hogy a git-véglegesítés egy *félig* stabil címkét biztosít.
* **Manifest Digest** – a tároló-beállításjegyzékbe leküldött összes tároló-rendszerkép egy jegyzékfájlhoz van társítva, amely egyedi SHA-256 kivonattal vagy kivonattal van azonosítva. Míg egyedi, a kivonat hosszú, nehezen olvasható és nem korrelál a Build-környezettel.
* **Build azonosítója** – ez a lehetőség lehet a legjobb megoldás, mivel ez valószínűleg növekményes, és lehetővé teszi, hogy az összes összetevő és napló megtalálásához visszakapcsolja az adott buildet. A manifest-kivonatokhoz hasonlóan előfordulhat, hogy az embernek nem kell elolvasnia a problémát.

  Ha a szervezet több Build-rendszerrel rendelkezik, a címke előállítása a létrehozási rendszer nevével a következő beállítás egyik változata: `<build-system>-<build-id>` . Megkülönböztetni például az API csapat Jenkins Build rendszerét és a webes csapat Azure-folyamatait.

### <a name="lock-deployed-image-tags"></a>Telepített képcímkék zárolása

Ajánlott eljárásként Azt javasoljuk, hogy az attribútumának beállításával [zárolja](container-registry-image-lock.md) az összes üzembe helyezett képcímkét `write-enabled` `false` . Ez a gyakorlat megakadályozza, hogy véletlenül eltávolítsa a rendszerképet a beállításjegyzékből, és esetleg megszakítsa a telepítést. A zárolási lépést felveheti a kiadási folyamatba.

Az üzembe helyezett lemezkép zárolása továbbra is lehetővé teszi, hogy a beállításjegyzékben a többi, nem telepített lemezképet eltávolítsa a beállításjegyzékből Azure Container Registry szolgáltatások használatával. Például a címkézetlen jegyzékfájlok [automatikus kiürítése](container-registry-auto-purge.md) vagy a megadott időtartamnál régebbi lemezképek kijavítása, illetve a címkézetlen jegyzékfájlok [megőrzési szabályának](container-registry-retention-policy.md) beállítása.

## <a name="next-steps"></a>Következő lépések

Az ebben a cikkben szereplő fogalmak részletesebb ismertetését lásd [: Docker-címkék címkézése és a Docker-rendszerképek verziószámozására vonatkozó ajánlott eljárások](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Az Azure Container Registry teljesítményének és költséghatékony használatának maximalizálása érdekében tekintse meg az [ajánlott eljárásokat a Azure Container Registryhoz](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

