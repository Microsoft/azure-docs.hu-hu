---
title: A címkézetlen jegyzékfájlok megőrzésére szolgáló szabályzat
description: Megtudhatja, hogyan engedélyezheti az adatmegőrzési szabályzatot az Azure Container registryben a címkézetlen jegyzékfájlok meghatározott időszak után történő automatikus törléséhez.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 5dda85934bb10cf16fd90381539b892df4f5445c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83683457"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Adatmegőrzési szabályzat beállítása a címkézetlen jegyzékekhez

Azure Container Registry lehetővé teszi az *adatmegőrzési szabályzat* beállítását olyan tárolt képjegyzékek esetében, amelyek nem rendelkeznek társított címkékkel (*címkézett jegyzékfájlokkal*). Ha egy adatmegőrzési szabály engedélyezve van, a beállításjegyzékben lévő címkézetlen jegyzékfájlok automatikusan törlődnek a megadott számú nap elteltével. Ez a szolgáltatás megakadályozza, hogy a beállításjegyzék nem szükséges összetevőkkel töltse fel a szolgáltatást, és segít a tárolási költségek megtakarításában. Ha a `delete-enabled` címkézetlen jegyzékfájl attribútuma a értékre van állítva `false` , a jegyzékfájl nem törölhető, és a megőrzési szabály nem érvényes.

Az Azure CLI Azure Cloud Shell vagy helyi telepítése segítségével futtathatja a jelen cikkben szereplő példákat. Ha helyileg szeretné használni, a 2.0.74 vagy újabb verziót kötelező megadni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

Az adatmegőrzési szabályzat a **prémium** szintű tároló-nyilvántartások egyik funkciója. További információ a beállításjegyzék szolgáltatási szintjeiről: [Azure Container Registry szolgáltatási szintek](container-registry-skus.md).

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

> [!WARNING]
> Adatmegőrzési házirend beállítása gondossággal – törölt képadatokkal nem állítható helyre. Ha olyan rendszerekkel rendelkezik, amelyekben a manifest Digest (a rendszerkép neve helyett) lekéri a képeket, ne állítson be adatmegőrzési szabályt a címkézetlen jegyzékekhez. A címkézetlen lemezképek törlésével megakadályozhatja, hogy ezek a rendszerek kihúzzanak a lemezképeket a beállításjegyzékből. A jegyzékfájlok helyett érdemes lehet egy *egyedi címkézési* sémát alkalmazni, amely [ajánlott eljárás](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Csak a címkézetlen jegyzékek adatmegőrzési szabályzata állítható be.
* Az adatmegőrzési szabály jelenleg csak azokra a jegyzékfájlokra vonatkozik, amelyeket a szabályzat engedélyezése *után* címkéztek. A beállításjegyzékben meglévő címkézetlen jegyzékek nem vonatkoznak a szabályzatra. Meglévő címkézetlen jegyzékfájlok törléséhez tekintse meg a példák a [tároló lemezképének törlése a Azure Container Registry](container-registry-delete.md)-ben című témakört.

## <a name="about-the-retention-policy"></a>Tudnivalók az adatmegőrzési szabályzatról

A Azure Container Registry a beállításjegyzékben lévő jegyzékfájlok leltározását végzi. Ha egy jegyzékfájl címkézetlen, ellenőrzi az adatmegőrzési szabályt. Ha egy adatmegőrzési szabály engedélyezve van, egy jegyzékfájl törlési művelete várólistára kerül, egy adott dátummal, a szabályzatban beállított napok száma szerint.

Egy külön üzenetsor-kezelési feladatokkal folyamatosan dolgozza fel az üzeneteket, igény szerint méretezhető. Tegyük fel például, hogy egy 30 napos adatmegőrzési szabályzattal rendelkező beállításjegyzékben a két jegyzékfájlt (1 óra) megcímkézte. Két üzenet várólistára kerül. Ezután 30 nappal később, körülbelül 1 órával az üzenetek lekérése a sorból és a feldolgozás után történik, feltételezve, hogy a házirend még érvényben volt.

## <a name="set-a-retention-policy---cli"></a>Adatmegőrzési szabály beállítása – parancssori felület

Az alábbi példa bemutatja, hogyan állíthatja be a beállításjegyzékben az Azure CLI használatával a címkézetlen jegyzékfájlok megőrzési szabályát.

### <a name="enable-a-retention-policy"></a>Adatmegőrzési szabályzat engedélyezése

Alapértelmezés szerint a tároló-beállításjegyzékben nincs beállítva adatmegőrzési szabály. Adatmegőrzési szabály beállításához vagy frissítéséhez futtassa az az [ACR config adatmegőrzési frissítés][az-acr-config-retention-update] parancsot az Azure CLI-ben. 0 és 365 közötti napokat is megadhat a címkézetlen jegyzékfájlok megőrzéséhez. Ha nem adja meg a napok számát, a parancs alapértelmezés szerint 7 napot állít be. A megőrzési időtartam után a beállításjegyzékben szereplő összes címkézetlen jegyzékfájl automatikusan törlődik.

A következő példa egy 30 napos adatmegőrzési szabályzatot állít be címkézetlen jegyzékekhez a beállításjegyzék *myregistry*:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

A következő példa egy szabályzatot állít be a beállításjegyzékben lévő összes jegyzékfájl törlésére, amint a címke fel van jelölve. Hozza létre ezt a házirendet 0 napos megőrzési időtartam beállításával. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Adatmegőrzési szabály érvényesítése

Ha az előző szabályzatot 0 napos megőrzési időtartammal engedélyezi, gyorsan ellenőrizheti, hogy a címkézetlen jegyzékfájlok törlődnek-e:

1. Küldjön le egy `hello-world:latest` képképet a beállításjegyzékbe, vagy cserélje le egy másik, tetszés szerinti tesztelési képet.
1. Jelölését a `hello-world:latest` képet, például az az [ACR adattár jelölését][az-acr-repository-untag] parancs használatával. A címkézetlen jegyzékfájl a beállításjegyzékben marad.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Néhány másodpercen belül töröljük a címkézetlen jegyzékfájlt. A törlést a tárház jegyzékfájlok listázásával ellenőrizheti, például az az [ACR repository show-Manifests][az-acr-repository-show-manifests] parancs használatával. Ha a rendszerkép csak egyetlen a tárházban, a tárház törlődik.

### <a name="disable-a-retention-policy"></a>Adatmegőrzési szabály letiltása

Ha meg szeretné tekinteni a beállításjegyzékben beállított adatmegőrzési szabályzatot, futtassa az az [ACR config adatmegőrzési show][az-acr-config-retention-show] parancsot:

```azurecli
az acr config retention show --registry myregistry
```

Ha le szeretne tiltani egy adatmegőrzési szabályt egy beállításjegyzékben, futtassa az az [ACR config megőrzési frissítés][az-acr-config-retention-update] parancsot, és állítsa be a következőt `--status disabled` :

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Adatmegőrzési szabályzat beállítása – portál

A beállításjegyzék adatmegőrzési házirendjét is beállíthatja a [Azure Portalban](https://portal.azure.com). A következő példa azt mutatja be, hogyan használható a portál a címkézetlen jegyzékfájlok megőrzési szabályának beállítására a beállításjegyzékben.

### <a name="enable-a-retention-policy"></a>Adatmegőrzési szabályzat engedélyezése

1. Navigáljon az Azure Container Registry szolgáltatáshoz. A **házirendek** területen válassza a **megőrzés** (előzetes verzió) lehetőséget.
1. Az **állapot** területen válassza az **engedélyezve** lehetőséget.
1. A címkézetlen jegyzékfájlok megőrzéséhez válassza ki a 0 és 365 közötti napok számát. Kattintson a **Mentés** gombra.

![Adatmegőrzési szabályzat engedélyezése Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Adatmegőrzési szabály letiltása

1. Navigáljon az Azure Container Registry szolgáltatáshoz. A **házirendek** területen válassza a **megőrzés** (előzetes verzió) lehetőséget.
1. Az **állapot** területen válassza a **Letiltva** lehetőséget. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

* További információ a [lemezképek és adattárak Azure Container Registry való törlésének](container-registry-delete.md) lehetőségeiről

* Megtudhatja, hogyan lehet [automatikusan törölni](container-registry-auto-purge.md) a kijelölt lemezképeket és jegyzékeket egy beállításjegyzékből

* További információ a [lemezképek és](container-registry-image-lock.md) a jegyzékek beállításjegyzékben való zárolásának lehetőségeiről

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
