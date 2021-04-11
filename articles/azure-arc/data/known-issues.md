---
title: Az Azure arc-kompatibilis adatszolgáltatások – ismert problémák
description: Legújabb ismert problémák
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: ee652047a33d73ece2d7648905fa590d90b1fb2f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029505"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Ismert problémák – az Azure arc-kompatibilis adatszolgáltatások (előzetes verzió)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>Március 2021

### <a name="data-controller"></a>Adatkezelő

- Az adatvezérlőt közvetlen csatlakozási módban is létrehozhatja a Azure Portal. A más Azure arc-kompatibilis adatszolgáltatási eszközökkel való üzembe helyezés nem támogatott. Pontosabban nem telepíthet adatvezérlőt közvetlen csatlakozási módban a jelen kiadás során az alábbi eszközök bármelyikével.
   - Azure Data Studio
   - Azure-beli adatcli ( `azdata` )
   - Kubernetes natív eszközök

   [Az Azure arc-adatkezelő üzembe helyezése | A közvetlen csatlakozási mód](deploy-data-controller-direct-mode.md) azt ismerteti, hogyan hozhatja létre az adatkezelőt a portálon. 

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure arc engedélyezve PostgreSQL nagy kapacitású

- Egy Azure arc-kompatibilis postgres nagy kapacitású-kiszolgálócsoport üzembe helyezése nem támogatott a közvetlen csatlakozási mód számára engedélyezett ív-adatkezelőben.
- Érvénytelen értéket kell átadni a `--extensions` paraméternek egy kiszolgálócsoport konfigurációjának szerkesztésekor, hogy a További bővítmények helytelenül visszaállítsák az engedélyezett bővítmények listáját, hogy mi volt a kiszolgálócsoport létrehozásakor, és megakadályozza, hogy a felhasználó további bővítményeket hozzon létre. Az egyetlen megkerülő megoldás, ha ez történik, törölje a kiszolgálócsoport törlését, majd telepítse újra.

## <a name="february-2021"></a>2021. február

### <a name="data-controller"></a>Adatkezelő

- A közvetlen összekapcsolási fürt üzemmódja le van tiltva

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure arc engedélyezve PostgreSQL nagy kapacitású

- Az időponthoz való visszaállítás jelenleg nem támogatott az NFS-tárolón.
- Egyszerre nem lehet engedélyezni és konfigurálni a pg_cron-bővítményt. Ehhez két parancsot kell használnia. Egy parancs, amely lehetővé teszi, és egy parancs konfigurálását. 

   Például:
   ```console
   § azdata arc postgres server edit -n myservergroup --extensions pg_cron 
   § azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
   ```

   Az első parancshoz a kiszolgálócsoport újraindítása szükséges. Ezért a második parancs végrehajtása előtt győződjön meg arról, hogy a kiszolgálócsoport állapota a frissítéstől a kész értékre vált. Ha a második parancsot az újraindítás befejeződése előtt hajtja végre, a művelet sikertelen lesz. Ha ez a helyzet, egyszerűen várjon néhány percet, majd futtassa újra a második parancsot.

## <a name="introduced-prior-to-february-2021"></a>Február 2021. előtt bemutatott

### <a name="data-controller"></a>Adatkezelő

- Az Azure Kubernetes szolgáltatásban (ak) a Kubernetes 1.19. x verziója nem támogatott.
- A Kubernetes 1,19 `containerd` nem támogatott.
- Az Azure-beli adatkezelő erőforrás jelenleg egy Azure-erőforrás. Az összes frissítés, például a törlés nem kerül vissza a kubernetes-fürtbe.
- A példányok neve nem lehet hosszabb 13 karakternél.
- Nincs helyben történő frissítés az Azure arc-adatkezelő vagy adatbázis-példányok számára.
- Az Arc-kompatibilis adatszolgáltatások tárolórendszerképei nem rendelkeznek aláírással.  Lehet, hogy konfigurálnia kell a Kubernetes-csomópontokat az aláírás nélküli tárolórendszerképek lekérésére.  Ha például a Docker-t tároló futtatókörnyezetként használja, beállíthatja a DOCKER_CONTENT_TRUST = 0 környezeti változót, és újraindulhat.  Más tároló-futtatókörnyezetek hasonló beállításokkal rendelkeznek, ilyen például az [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Nem hozható létre az Azure arc használatára képes SQL felügyelt példányok vagy PostgreSQL nagy kapacitású-kiszolgálócsoportok a Azure Portal.
- Most, ha az NFS-t használja, az `allowRunAsRoot` `true` Azure arc-adatkezelő létrehozása előtt be kell állítania a telepítési profil fájljában.
- Csak az SQL és a PostgreSQL bejelentkezési hitelesítés.  Azure Active Directory vagy Active Directory nem támogatott.
- A OpenShift adatvezérlőjének létrehozásához nyugodt biztonsági korlátozások szükségesek.  További információt a dokumentációban találhat.
- Ha Azure Kubernetes Service (ak) motort használ az Azure arc-adatkezelővel és az adatbázis-példányokkal rendelkező Azure Stack hubhoz, az újabb verzióra való frissítés nem támogatott. A Kubernetes-fürt frissítése előtt távolítsa el az Azure arc-adatkezelőt és az összes adatbázis-példányt.
- A [több rendelkezésre állási zónára](../../aks/availability-zones.md) kiterjedő AK-fürtök jelenleg nem támogatottak az Azure arc-kompatibilis adatszolgáltatások esetében. Ha el szeretné kerülni ezt a problémát, akkor amikor az AK-fürtöt Azure Portalban hozza létre, ha olyan régiót választ ki, ahol elérhetők zónák, törölje az összes zónát a kijelölés vezérlőelemből. Lásd a következő képet:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Törölje az egyes zónák jelölőnégyzeteit a none érték megadásához.":::


## <a name="next-steps"></a>Következő lépések

> **Csak szeretné kipróbálni a dolgokat?**  
> Gyorsan elsajátíthatja az [Azure arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) az AK-ra, az AWS rugalmas Kubernetes szolgáltatásra (EKS), a Google Cloud Kubernetes Engine-re (GKE) vagy egy Azure-beli virtuális gépre.

- [Az ügyféleszközök telepítése](install-client-tools.md)
- [Hozza létre az Azure arc-adatkezelőt](create-data-controller.md) (először telepítenie kell az ügyféleszközök telepítését)
- [Azure SQL felügyelt példány létrehozása az Azure arc](create-sql-managed-instance.md) szolgáltatásban (először létre kell hoznia egy Azure arc-adatkezelőt)
- [Hozzon létre egy Azure Database for PostgreSQL nagy kapacitású-kiszolgáló csoportot az Azure-ív](create-postgresql-hyperscale-server-group.md) (először egy Azure arc-adatkezelőt kell létrehozni)
- [Erőforrás-szolgáltatók az Azure-szolgáltatásokhoz](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Kibocsátási megjegyzések – az Azure arc-kompatibilis adatszolgáltatások (előzetes verzió)](release-notes.md)
