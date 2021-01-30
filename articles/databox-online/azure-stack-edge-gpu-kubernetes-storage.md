---
title: A Kubernetes Azure Stack Edge Pro-eszközön való felügyeletének ismertetése | Microsoft Docs
description: Ismerteti, hogyan történik a Kubernetes-tárolók kezelése Azure Stack Edge Pro-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 01/28/2021
ms.author: alkohli
ms.openlocfilehash: 2d079f2fa3e67f1ec915a02de3e195ccac538209
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063312"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes a Azure Stack Edge Pro GPU-eszközön

A Azure Stack Edge Pro-eszközön a számítási szerepkör konfigurálásakor létrejön egy Kubernetes-fürt. A Kubernetes-fürt létrehozása után a tároló alkalmazások a Kubernetes-fürtön helyezhetők üzembe a hüvelyben. A tárolók a Kubernetes-fürtben többféleképpen is megadhatók. 

Ez a cikk azokat a módszereket ismerteti, amelyekkel kiépíthető a Kubernetes-fürtön lévő tárterület, különösen az Azure Stack Edge Pro-eszköz kontextusában. 

## <a name="storage-requirements-for-kubernetes-pods"></a>A Kubernetes hüvelyek tárolási követelményei

A Kubernetes-hüvelyek állapot nélküliek, de az általuk futtatott alkalmazások általában állapottal rendelkeznek. Mivel a hüvelyek rövid életűek lehetnek, és a Kubernetes-csomópontok közötti újraindítást, feladatátvételt vagy áthelyezést hajtanak végre, a következő követelményeknek kell teljesülniük a pod-hoz tartozó tárolóhoz. 

A tárterületnek a következőket kell tennie:

- Élő a pod-on kívül.
- Függetlennek kell lennie a pod életciklustól.
- A Kubernetes összes csomópontján elérhetőnek kell lennie.

Annak megismeréséhez, hogy a tárterület hogyan legyen kezelve a Kubernetes, az egyiknek két API-erőforrást kell megismernie: 

- **PersistentVolume (PV)**: ez egy tároló a Kubernetes-fürtben. A Kubernetes-tároló statikusan kiépíthető `PersistentVolume` . Az is lehetséges, hogy dinamikusan kiépíthető  `StorageClass` .

- **PersistentVolumeClaim (PVC)**: ez egy felhasználó általi tárolásra vonatkozó kérelem. Az adatpvcek NAPELEMes erőforrásokat használnak. A PVC-ket adott méretű és hozzáférési üzemmódokra lehet kérni. 

    Mivel a felhasználóknak `PersistentVolumes` eltérő tulajdonságokkal kell rendelkezniük a különböző problémákhoz, ezért a fürt rendszergazdáinak a `PersistentVolumes` méret és a hozzáférés módjainál több szempontból is különbözőnek kell lenniük. Ezeknek az igényeknek megfelelően szüksége lesz az `StorageClass` erőforrásra.

A tárolók kiépítés lehet statikus vagy dinamikus. A kiépítési típusok mindegyikét a következő szakaszok tárgyalják.

## <a name="static-provisioning"></a>Statikus kiépítés

A Kubernetes-fürt rendszergazdái statikusan is kiépíthetik a tárolót. Ehhez használhatja az SMB/NFS fájlrendszereken alapuló tárolási hátteret, vagy olyan iSCSI-lemezeket használhat, amelyek helyileg, helyszíni környezetben, vagy akár a felhőben lévő Azure Files vagy Azure-lemezek használatával csatlakoznak a hálózathoz. Az ilyen típusú tárolók alapértelmezés szerint nincsenek kiépítve, és a fürt rendszergazdáinak meg kell tervezniük és kezelnie kell ezt a kiépítési lehetőséget. 
 
Itt látható egy diagram, amely azt ábrázolja, hogyan használják a statikusan kiosztott tárolókat a Kubernetes: 

![Statikus kiépítés a PersistentVolumes-on keresztül](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

A következő lépések történnek: 

1. **Tárterület kiépítése**: a fürt rendszergazdája kiépíti a tárolót. Ebben a példában a fürt rendszergazdája egy vagy több olyan SMB-megosztást hoz létre, amely automatikusan létrehoz állandó kötet objektumokat a Kubernetes-fürtben, amely ezeknek a megosztásoknak felel meg. 

1. **Jogcím tárolója**: elküld egy olyan PVC-telepítést, amely a tárolót kéri. Ez a tárolási jogcím a PersistentVolumeClaim (PVC). Ha a PV mérete és hozzáférési módja megegyezik a PVC-vel, akkor a PVC a PV-hez van kötve. A PVC és a PV Térkép egy-az-egyhez.

1. **PVC csatlakoztatása a tárolóhoz**: Ha a PVC a PV-hez van kötve, csatlakoztathatja ezt a PVC-t a tároló egyik elérési útjához. Amikor a tárolóban lévő alkalmazás logikája beolvassa vagy beírja az elérési utat, az adatok bekerülnek az SMB-tárolóba.
 

## <a name="dynamic-provisioning"></a>Dinamikus kiépítés

Itt látható egy diagram, amely azt ábrázolja, hogyan használják a statikusan kiosztott tárolókat a Kubernetes: 

![Dinamikus kiépítés a StorageClasses-on keresztül](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

A következő lépések történnek: 

1. **Tárolási osztály definiálása**: a fürt rendszergazdája definiál egy tárolási osztályt a Kubernetes-fürthöz tartozó működési környezettől függően. A fürt rendszergazdája is üzembe helyez egy kiépítő szolgáltatást, amely még egy, a Kubernetes-fürtön üzembe helyezett Pod vagy alkalmazás. A kiépítő részletesen ismerteti a megosztások dinamikus kiépítésének részleteit.  

1. **Jogcím tárolója**: elküld egy alkalmazást, amely a tárterületet igényli. Miután létrehozta a PVC-t ezzel a tárolási osztállyal, a rendszer meghívja a létesítést. 

1. **Tárterület dinamikus kiépítése**: a kiépítés dinamikusan létrehozza a helyi lemezes tárolóhoz társított megosztást. A megosztás létrehozása után egy, a megosztáshoz automatikusan kapcsolódó PV-objektumot is létrehoz.

1. A **PVC csatlakoztatása a tárolóhoz**: Ha a PVC a PV-hez van kötve, a PVC-t a tárolóra csatlakoztathatja úgy, hogy az a statikus kiépítés és a megosztásba való beolvasás vagy írás.


## <a name="storage-provisioning-on-azure-stack-edge-pro"></a>Tárolási kiépítés Azure Stack Edge Pro-ban

Az Azure Stack Edge Pro-eszközön a statikusan kiépített `PersistentVolumes` eszközök a tárolási képességek használatával jönnek létre. Ha kiépít egy megosztást, és **használja a megosztás az Edge használatával számítási** lehetőséget, ez a művelet automatikusan létrehoz egy PV-erőforrást a Kubernetes-fürtben.

![Helyi megosztás létrehozása a Azure Portalban statikus kiépítés esetén](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

A Felhőbeli rétegek használatához létrehozhat egy Edge-Felhőbeli megosztást a megosztás az Edge számítási funkcióval lehetőséggel való használatával. A rendszer automatikusan létrehoz egy PV-t ehhez a megosztáshoz. A peremhálózati megosztásba írt alkalmazásadatok a felhőbe vannak bontva. 

![Felhőalapú megosztás létrehozása Azure Portal statikus kiépítés esetén](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

SMB-és NFS-megosztásokat is létrehozhat a Azure Stack Edge Pro-eszközön lévő PVs statikus kiépítéséhez. A PV kiépítés után egy PVC-t fog küldeni a tároló igényléséhez. Íme egy példa egy olyan PVC-telepítésre, `yaml` amely a tárterületet állítja be, és az Ön által kiépített megosztásokat használja.


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

További információ: állapot- [nyilvántartó alkalmazás üzembe helyezése statikus kiépítés használatával a Azure stack Edge Pro-n keresztül a kubectl-on keresztül](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Ha ugyanazt a statikusan kiosztott tárolót szeretné elérni, a IoT tartozó tárolási kötések megfelelő mennyiségi csatlakoztatási beállításai a következők. Az az az `/home/input` elérési út, ahol a kötet elérhető a tárolón belül.

```
{
"HostConfig": {
"Mounts": [
{
"Target": "/home/input",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
},
{
"Target": "/home/output",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
}]
}
}
```

Azure Stack Edge Pro is rendelkezik egy olyan beépített `StorageClass` névvel `ase-node-local` , amely egy, a Kubernetes-csomóponthoz csatolt adatlemez-tárolót használ. Ez `StorageClass` támogatja a dinamikus kiépítés használatát. Létrehozhat egy `StorageClass` hivatkozást a pod-alkalmazásokban, és a rendszer automatikusan létrehoz egy PV-t az Ön számára. További információkért tekintse meg a [Kubernetes-irányítópultot](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) a lekérdezéshez `ase-node-local StorageClass` .

![Beépített tárolási osztály a Kubernetes-irányítópulton](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

További információ: állapot- [nyilvántartó alkalmazás üzembe helyezése a Azure stack Edge Pro-n keresztül a kuebctl-on keresztül történő dinamikus kiépítés használatával](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).

## <a name="choose-storage-type"></a>Tárolási típus kiválasztása

Előfordulhat, hogy az üzembe helyezett munkaterheléstől függően ki kell választania a tárolási típust. 

- Ha azt szeretné `ReadWriteMany` `PersistentVolumes` , hogy a kötetek milyen módon legyenek csatlakoztatva az írási-olvasási és a különböző csomópontok, akkor használjon statikus kiosztást az SMB/NFS-megosztásokhoz.

- Ha az üzembe helyezett alkalmazások POSIX-megfelelőségi követelménye (például MongoDB, PostgreSQL, MySQL vagy Prometheus), használja a beépített StorageClass. A hozzáférési módok, `ReadWriteOnce` vagy a kötet egyetlen csomópont által írható-olvashatóként van csatolva. 


A hozzáférési módokkal kapcsolatos további információkért lásd: [Kubernetes-kötetek hozzáférési módja](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes).


## <a name="next-steps"></a>Következő lépések

A statikus kiépítésének megismeréséhez `PersistentVolume` lásd:

- [Üzembe helyezhet egy állapot-nyilvántartó alkalmazást a Azure stack Edge Pro-n keresztül a kubectl-on keresztül történő statikus kiépítés használatával](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Ha szeretné megtudni, hogyan lehet dinamikusan kiépíteni a t `StorageClass` , tekintse meg a következőt:

- [Üzembe helyezhet egy állapot-nyilvántartó alkalmazást a Azure stack Edge Pro-n keresztül a kuebctl-on keresztül történő dinamikus kiépítés használatával](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).
