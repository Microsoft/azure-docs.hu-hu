---
title: Szabályzat a nem megjelölt jegyzékek megőrzéséhez
description: Megtudhatja, hogyan engedélyezheti a megőrzési szabályzatot az Azure Container Registryben a nem megjelölt jegyzékek adott időszak utáni automatikus törléséhez.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 81ce92a2533cc8a688be43da9406cd5b0e726509
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781342"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Adatmegőrzési szabályzat beállítása nem megjelölt jegyzékek számára

Azure Container Registry megtartási szabályzatot állíthat be *olyan* tárolt rendszerképjegyzékek számára, amelyek nem társított címkékkel (címkézetlen jegyzékekkel) vannak *társítva.* Ha engedélyezve van egy adatmegőrzési szabályzat, a beállításjegyzékben nem megjelölt jegyzékek automatikusan törlődnek a beállított számú nap után. Ez a funkció megakadályozza, hogy a regisztrációs adatbázis feltöltsen olyan összetevőkkel, amelyekre nincs szükség, és segít a tárolási költségek megtakarításában. Ha egy nem megjelölt jegyzékfájl attribútuma a következőre van beállítva: , a jegyzékfájl nem törölhető, és a megőrzési `delete-enabled` `false` szabályzat nem érvényes.

Az ebben a cikkben Azure Cloud Shell példaparancsok futtatásához használhatja az Azure CLI parancssori felületét vagy helyi telepítését. Ha helyileg szeretné használni, a 2.0.74-es vagy újabb verzió szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A megőrzési szabályzat a **Prémium** tárolóregisztrálók funkciója. A beállításjegyzék szolgáltatásszintekkel kapcsolatos információkért lásd: [Azure Container Registry szolgáltatási szintek.](container-registry-skus.md)

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek.](#preview-limitations) Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

> [!WARNING]
> Állítson be egy adatmegőrzési szabályzatot gond nélkül törölt képadatokkal, amely NEM ÁLLÍTHATÓ VISSZA. Ha vannak olyan rendszerei, amelyek jegyzékkivonat alapján (és nem a rendszerképnévvel) leküldik a rendszerképeket, ne állítson be megőrzési szabályzatot a nem megjelölt jegyzékek számára. A nem látható lemezképek törlésével megakadályozhatja, hogy ezek a rendszerek leküldhetzék a lemezképeket a regisztrációs adatbázisból. A jegyzékfájlos lehúzás helyett  érdemes lehet egy egyedi címkézési sémát használni, amely az [ajánlott eljárás.](container-registry-image-tag-version.md)

## <a name="preview-limitations"></a>Az előzetes verzió korlátozásai

* Megőrzési szabályzatot csak a nem megjelölt jegyzékek számára állíthat be.
* Az adatmegőrzési szabályzat jelenleg csak azokra a jegyzékfájlra vonatkozik, amelyek a szabályzat engedélyezése után *nincsenek* megjelölve. A regisztrációs adatbázisban meglévő, nem megjelölt jegyzékek nem vonatkoznak a szabályzatra. A meglévő, nem megjelölt jegyzékek törléséhez tekintse meg a tároló rendszerképének törlését [Azure Container Registry.](container-registry-delete.md)

## <a name="about-the-retention-policy"></a>Az adatmegőrzési szabályzatról

Azure Container Registry hivatkozik a regisztrációs adatbázisban a jegyzékfájlokat számláló számlálókra. Ha egy jegyzékfájl nincs megjelölve, ellenőrzi a megőrzési szabályzatot. Ha engedélyezve van egy megőrzési szabályzat, a jegyzékfájl-törlési művelet várólistára kerül, és a szabályzatban megadott napok száma alapján egy adott dátummal.

Egy különálló üzenetsor-kezelési feladat folyamatosan feldolgozza az üzeneteket, és szükség szerint skáláz. Tegyük fel például, hogy egy 30 napos adatmegőrzési szabályzatú regisztrációs adatbázisban 1 órás távolságra van két jegyzék kilistázva. Két üzenet lenne a várólistán. Ezután 30 nappal később, körülbelül 1 órával az üzenetek lekérése az üzenetsorból történt, és feltéve, hogy a szabályzat még érvényben van.

## <a name="set-a-retention-policy---cli"></a>Adatmegőrzési szabályzat beállítása – CLI

Az alábbi példa bemutatja, hogyan állíthat be adatmegőrzési szabályzatot a regisztrációs adatbázisban a fel nem használt jegyzékek számára az Azure CLI használatával.

### <a name="enable-a-retention-policy"></a>Adatmegőrzési szabályzat engedélyezése

Alapértelmezés szerint nincs megőrzési szabályzat beállítva a tároló-beállításjegyzékben. Megőrzési szabályzat beállításhoz vagy frissítéséhez futtassa [az az acr config retention update][az-acr-config-retention-update] parancsot az Azure CLI-ban. A 0 és 365 közötti napok számát is megadhatja a nem megjelölt jegyzékek megőrzéséhez. Ha nem adja meg a napok számát, a parancs alapértelmezés szerint 7 napot állít be. A megőrzési időszak után a rendszer automatikusan törli a beállításjegyzékben nem megjelölt jegyzékeket.

Az alábbi példa 30 napos adatmegőrzési szabályzatot állít be a *myregistry* beállításjegyzékben nem megjelölt jegyzékek számára:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

Az alábbi példa beállítja a szabályzatot, amely törli a regisztrációs adatbázis jegyzékfájlját, amint az nincs megjelölve. Ezt a szabályzatot 0 napos megőrzési időtartam beállításával hozhatja létre. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Adatmegőrzési szabályzat ellenőrzése

Ha a fenti szabályzatot 0 napos megőrzési időtartammal engedélyezi, gyorsan ellenőrizheti, hogy a rendszer törölte-e a nem megjelölt jegyzékeket:

1. Egy tesztképkép leküldése a regisztrációs adatbázisba, vagy egy másik `hello-world:latest` választott tesztkép behelyettesítője.
1. A rendszerképet például az `hello-world:latest` [az acr repository untag paranccsal lehet lecímkézni.][az-acr-repository-untag] A nem megjelölt jegyzékfájl a beállításjegyzékben marad.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. A rendszer néhány másodpercen belül törli a nem megjelölt jegyzékfájlt. A törlés ellenőrzéséhez listázást kell használnia az adattárban, például az [az acr repository show-manifests paranccsal.][az-acr-repository-show-manifests] Ha a tesztkép volt az egyetlen az adattárban, maga az adattár törlődik.

### <a name="disable-a-retention-policy"></a>Adatmegőrzési szabályzat letiltása

A beállításjegyzékben beállított adatmegőrzési szabályzatot az [az acr config retention show paranccsal láthatja:][az-acr-config-retention-show]

```azurecli
az acr config retention show --registry myregistry
```

Egy beállításjegyzék adatmegőrzési szabályzatának letiltásához futtassa az [az acr config retention update][az-acr-config-retention-update] parancsot, és állítsa be a következőt: `--status disabled`

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Adatmegőrzési szabályzat beállítása – portál

A beállításjegyzék adatmegőrzési szabályzatát a következő [Azure Portal.](https://portal.azure.com) Az alábbi példa bemutatja, hogyan állíthat be megőrzési szabályzatot a portál használatával a regisztrációs adatbázisban nem használt jegyzékek számára.

### <a name="enable-a-retention-policy"></a>Adatmegőrzési szabályzat engedélyezése

1. Lépjen az Azure Container Registrybe. A **Szabályzatok alatt** válassza a **Megőrzés** (előzetes verzió) lehetőséget.
1. Az **Állapot mezőben** válassza az **Engedélyezve lehetőséget.**
1. Jelöljön ki néhány napot 0 és 365 között a nem megjelölt jegyzékek megőrzéséhez. Kattintson a **Mentés** gombra.

![Adatmegőrzési szabályzat engedélyezése a Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Adatmegőrzési szabályzat letiltása

1. Lépjen az Azure Container Registryhez. A **Szabályzatok alatt** válassza a **Megőrzés** (előzetes verzió) lehetőséget.
1. Az **Állapot mezőben** válassza a **Letiltva lehetőséget.** Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

* További információ a képek és adattárak törlésének [lehetőségeiről](container-registry-delete.md) a Azure Container Registry

* Tudnivalók a kiválasztott [rendszerképek](container-registry-auto-purge.md) és jegyzékek beállításjegyzékből való automatikus kiürítésről

* További információ a rendszerképek és [jegyzékek regisztrációs adatbázisban való](container-registry-image-lock.md) zárolásának beállításairól

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az_acr_config_retention_update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az_acr_config_retention_show
[az-acr-repository-untag]: /cli/azure/acr/repository#az_acr_repository_untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
