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
ms.openlocfilehash: 8100d9e12f107e0c4598876c46453b46c6ee4d0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122000"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Ismert problémák – az Azure arc-kompatibilis adatszolgáltatások (előzetes verzió)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>2021. február

- A csatlakoztatott fürt üzemmódja le van tiltva

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

### <a name="postgresql"></a>PostgreSQL

- Az Azure arc-kompatibilis PostgreSQL-nagy kapacitású pontatlan hibaüzenetet ad vissza, ha nem tudja visszaállítani a megadott relatív időpontot. Ha például olyan időpontot adott meg a visszaállításhoz, amely régebbi, mint a biztonsági másolatok tartalma, a visszaállítás a következőhöz hasonló hibaüzenettel fog meghiúsulni: `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
Ha ez bekövetkezik, indítsa újra a parancsot egy olyan időpontot jelző időtartományon belül, amelynek a biztonsági másolatait tartalmazza. Ezt a tartományt a biztonsági mentések listázásával és a készítésük dátumát megtekintve fogja meghatározni.
- Az időpontra történő visszaállítás csak a kiszolgálócsoportok között támogatott. Egy időpontra vonatkozó visszaállítási műveletben szereplő célkiszolgáló nem lehet az a kiszolgáló, amelyről a biztonsági mentést elvégezte. A kiszolgálónak más csoportnak kell lennie. A teljes visszaállítás azonban ugyanazon kiszolgálócsoport esetében támogatott.
- A teljes visszaállításhoz szükség van egy biztonsági mentési azonosítóra. Alapértelmezés szerint, ha nem jelez biztonsági mentési azonosítót, a rendszer a legújabb biztonsági mentést fogja használni. Ez a kiadás nem működik.

## <a name="next-steps"></a>Következő lépések

> **Csak szeretné kipróbálni a dolgokat?**  
> Gyorsan elsajátíthatja az [Azure arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) az AK-ra, az AWS rugalmas Kubernetes szolgáltatásra (EKS), a Google Cloud Kubernetes Engine-re (GKE) vagy egy Azure-beli virtuális gépre.

- [Az ügyféleszközök telepítése](install-client-tools.md)
- [Hozza létre az Azure arc-adatkezelőt](create-data-controller.md) (először telepítenie kell az ügyféleszközök telepítését)
- [Azure SQL felügyelt példány létrehozása az Azure arc](create-sql-managed-instance.md) szolgáltatásban (először létre kell hoznia egy Azure arc-adatkezelőt)
- [Hozzon létre egy Azure Database for PostgreSQL nagy kapacitású-kiszolgáló csoportot az Azure-ív](create-postgresql-hyperscale-server-group.md) (először egy Azure arc-adatkezelőt kell létrehozni)
- [Erőforrás-szolgáltatók az Azure-szolgáltatásokhoz](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Kibocsátási megjegyzések – az Azure arc-kompatibilis adatszolgáltatások (előzetes verzió)](release-notes.md)
