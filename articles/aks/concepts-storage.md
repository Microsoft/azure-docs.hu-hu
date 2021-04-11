---
title: Fogalmak – tárolás az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes szolgáltatás (ak) tárolóját, beleértve a köteteket, az állandó köteteket, a tárolási osztályokat és a jogcímeket.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a1f68b06c31597a1d2d044686274e86a79e6e9a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105782"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes szolgáltatásban (ak) lévő alkalmazások tárolási lehetőségei

Előfordulhat, hogy az Azure Kubernetes szolgáltatásban (ak) futó alkalmazásoknak tárolniuk és le kell kérniük az adatgyűjtést. Míg egyes alkalmazás-munkaterhelések helyi, gyors tárterületet használhatnak a szükségtelen, kiürített csomópontokon, másoknak olyan tárterületre van szükségük, amely az Azure-platformon belül még több normál adatköteten is fennmarad. 

Több hüvelyre lehet szükség:
* Ugyanazokat az adatköteteket használja. 
* Az adatkötetek újracsatolása, ha a pod egy másik csomóponton van átütemezett. 

Végezetül előfordulhat, hogy bizalmas adatokat vagy alkalmazás-konfigurációs adatokat kell beszúrnia a hüvelybe.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek a tárolást biztosítják alkalmazásai számára az AK-ban:

- [Kötetek](#volumes)
- [Tartós kötetek](#persistent-volumes)
- [Tárolási osztályok](#storage-classes)
- [Tartóskötet-jogcímek](#persistent-volume-claims)

![Azure Kubernetes Services-(ak-) fürtön lévő alkalmazások tárolási lehetőségei](media/concepts-storage/aks-storage-options.png)

## <a name="volumes"></a>Kötetek

A Kubernetes általában az egyes hüvelyeket ideiglenes, eldobható erőforrásként kezeli. Az alkalmazások az adatfelhasználás és-megőrzés különböző megközelítési módokon érhetők el. A *kötetek* a hüvelyek és az alkalmazások életciklusa közötti adattárolási,-lekérési és-megőrzési módot jelölik.

A hagyományos kötetek az Azure Storage által támogatott Kubernetes-erőforrásokként jönnek létre. Manuálisan is létrehozhat olyan adatköteteket, amelyek közvetlenül a hüvelyekhez rendelhetők, vagy Kubernetes automatikusan létrehozzák őket. Az adatkötetek az Azure-lemezeket és a Azure Files is használhatják.

### <a name="azure-disks"></a>Azure Disks

Az *Azure Disks* használatával hozzon létre egy Kubernetes *adatlemez* -erőforrást. A lemezek a következőket használhatják:
* Prémium szintű, nagy teljesítményű SSD-ket támogató Azure Premium Storage vagy 
* Szabványos HDD-k által támogatott Azure standard Storage. 

> [!TIP]
>A legtöbb éles és fejlesztési számítási feladathoz használja a Premium Storage-t. 

Mivel az Azure-lemezek *ReadWriteOnce* vannak csatlakoztatva, csak egyetlen Pod számára érhetők el. A több hüvelyrel egyidejűleg elérhető tárolási kötetek esetében használja a Azure Files.

### <a name="azure-files"></a>Azure Files
A *Azure Files* használatával csatlakoztathat egy Azure Storage-fiók által támogatott SMB 3,0-megosztást a hüvelyekhez. A fájlok lehetővé teszik az adatmegosztást több csomópont és hüvely között, és használhatják a következőket:
* Prémium szintű, nagy teljesítményű SSD-ket támogató Azure Premium Storage vagy 
* Normál HDD-k által támogatott Azure standard Storage.

### <a name="volume-types"></a>Kötetek típusai
A Kubernetes-kötetek több, mint egy hagyományos lemezt képviselnek az információk tárolásához és lekéréséhez. A Kubernetes kötetek a tárolók általi használatra is használhatók a pod-ba történő adatbevitelhez. 

A Kubernetes gyakori mennyiségi típusai a következők:

#### <a name="emptydir"></a>emptyDir

Általában a pod ideiglenes tárolóhelyként használatos. A hüvelyen belüli összes tároló hozzáfér a köteten lévő adatforgalomhoz. Az ebbe a kötetbe írt adatmennyiség csak a pod élettartama alatt marad. A hüvely törlése után a kötet törlődik. Ez a kötet jellemzően a mögöttes helyi csomópont lemezes tárterületét használja, bár csak a csomópont memóriájában létezhet.

#### <a name="secret"></a>titkos kód

A *titkos* kötetek használatával a bizalmas adatokat hüvelybe, például jelszavakba lehet beszúrni. 
1. Hozzon létre egy titkos kulcsot a Kubernetes API használatával. 
1. Adja meg a pod vagy az üzembe helyezést, és kérjen egy adott titkot. 
    * A titkos kulcsokat csak egy ütemezett Pod-vel rendelkező csomópontok számára biztosítjuk, amelyekhez szükség van rájuk.
    * A titkos kulcsot a rendszer a *tmpfs* tárolja, nem lemezre írva. 
1. Ha törli az utolsó Pod-t egy titkos kulcsot igénylő csomóponton, a titkos kulcsot a rendszer törli a csomópont tmpfs. 
   * A titkos kulcsok tárolása egy adott névtéren belül történik, és csak ugyanazon a névtéren belüli hüvelyek érhetik el.

#### <a name="configmap"></a>configMap

A *configMap* segítségével szúrhatja be a kulcs-érték párok tulajdonságait hüvelybe, például az alkalmazás konfigurációs adataiba. Az alkalmazás konfigurációs információit Kubernetes-erőforrásként definiálhatja, egyszerűen frissítheti és alkalmazhatja a hüvelyek új példányaira, ahogy azokat üzembe helyezik. 

Például titkos kulcs használata esetén:
1. Hozzon létre egy ConfigMap a Kubernetes API használatával. 
1. Adja meg a ConfigMap egy Pod vagy üzemelő példány megadásakor. 
   * A ConfigMaps egy adott névtéren belül tárolódnak, és csak ugyanazon a névtéren belüli hüvelyek érhetik el.

## <a name="persistent-volumes"></a>Tartós kötetek

A pod-életciklus részeként definiált és létrehozott kötetek csak a pod törlése után állnak fenn. A hüvelyek gyakran arra várnak, hogy a tárolójuk továbbra is megmaradjon, ha a karbantartási esemény során egy másik gazdagépen átütemezett Pod, különösen a StatefulSets. Az *állandó kötet* (PV) olyan tárolási erőforrás, amelyet a Kubernetes API hozott létre és felügyel, amely az egyes Pod-k élettartamán túl is létezhet.

Az Azure-lemezek és-fájlok használatával biztosíthatja a PersistentVolume. Ahogy az a [kötetek](#volumes) szakaszban is látható, a lemezek vagy fájlok megválasztását gyakran az adatmennyiség vagy a teljesítmény szintjéhez való egyidejű hozzáférés szükségessége határozza meg.

![Állandó kötetek egy Azure Kubernetes Services (ak) fürtben](media/concepts-storage/persistent-volumes.png)

A PersistentVolume lehet *statikusan* létrehozni, vagy a Kubernetes API-kiszolgáló által *dinamikusan* létrehozott. Ha egy Pod ütemezve van, és jelenleg nem elérhető tárterületet igényel, a Kubernetes létrehozhatja az alapul szolgáló Azure Disk vagy Files tárolót, és csatolhatja azt a pod-hoz. A dinamikus kiépítés egy *StorageClass* segítségével azonosítja, hogy milyen típusú Azure-tárolót kell létrehozni.

## <a name="storage-classes"></a>Tárolási osztályok

A különböző tárolási rétegek (például a prémium és a standard) definiálásához létrehozhat egy *StorageClass*. 

A StorageClass a *reclaimPolicy* is meghatározza. Ha törli a hüvelyt, és az állandó kötet már nem szükséges, a reclaimPolicy az alapul szolgáló Azure Storage-erőforrás viselkedését vezérli. A mögöttes tárolási erőforrás törölhető vagy megtartható egy későbbi Pod-nal való használathoz.

Az AK-ban négy kezdeti `StorageClasses` kapcsolat jön létre a fürtön a fán tárolt beépülő modulok használatával:

| Engedély | Ok |
|---|---|
| `default` | Az Azure StandardSSD Storage használatával felügyelt lemezt hoz létre. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-lemez törlődik, ha az azt használó állandó kötet törlődik. |
| `managed-premium` | Felügyelt lemez létrehozásához az Azure Premium Storage szolgáltatást használja. A visszaigénylési házirend újból biztosítja, hogy az alapul szolgáló Azure-lemez törlődik, ha az azt használó állandó kötet törlődik. |
| `azurefile` | Az Azure standard Storage használatával hozza létre az Azure-fájlmegosztást. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-fájlmegosztás törölve legyen, ha az azt használó állandó kötet törlődik. |
| `azurefile-premium` | Az Azure Premium Storage-t használja Azure-fájlmegosztás létrehozásához. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-fájlmegosztás törölve legyen, ha az azt használó állandó kötet törlődik.|

Az új Container Storage Interface (CSI) külső beépülő modulokat (előzetes verzió) használó fürtök esetén a következő extrák `StorageClasses` jönnek létre:

| Engedély | Ok |
|---|---|
| `managed-csi` | Az Azure StandardSSD helyileg redundáns tárolást (LRS) használ egy felügyelt lemez létrehozásához. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-lemez törlődik, ha az azt használó állandó kötet törlődik. A tárolási osztály azt is konfigurálja, hogy az állandó kötetek bővíthetők legyenek, csak az állandó mennyiségi jogcímet kell módosítania az új mérettel. |
| `managed-csi-premium` | Az Azure Premium helyileg redundáns tárolást (LRS) használja egy felügyelt lemez létrehozásához. A visszaigénylési házirend újból biztosítja, hogy az alapul szolgáló Azure-lemez törlődik, ha az azt használó állandó kötet törlődik. Hasonlóképpen, ez a tárolási osztály lehetővé teszi az állandó kötetek kibontását. |
| `azurefile-csi` | Az Azure standard Storage használatával hozza létre az Azure-fájlmegosztást. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-fájlmegosztás törölve legyen, ha az azt használó állandó kötet törlődik. |
| `azurefile-csi-premium` | Az Azure Premium Storage-t használja Azure-fájlmegosztás létrehozásához. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-fájlmegosztás törölve legyen, ha az azt használó állandó kötet törlődik.|

Ha nem ad meg StorageClass egy állandó kötethez, a rendszer az alapértelmezett StorageClass fogja használni. Győződjön meg arról, hogy a kötetek a szükséges tárterületet használják állandó kötetek kérésekor. 

A használatával további igényekhez is létrehozhat StorageClass `kubectl` . Az alábbi példa prémium Managed Disks használ, és megadja, hogy a mögöttes Azure-lemezt meg kell *őrizni* a pod törlésekor:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> Az AK összehangolja az alapértelmezett tárolási osztályokat, és felülírja a tárolási osztályokon végrehajtott módosításokat.

## <a name="persistent-volume-claims"></a>Tartóskötet-jogcímek

A PersistentVolumeClaim egy adott StorageClass, elérési módra és méretre vonatkozó lemez-vagy file Storage-t kér. A Kubernetes API-kiszolgáló dinamikusan kiépítheti a mögöttes Azure Storage-erőforrást, ha egy meglévő erőforrás nem tudja teljesíteni a jogcímet a megadott StorageClass alapján. 

A pod definíciója tartalmazza a kötet csatlakoztatását, ha a kötet csatlakoztatva van a pod-hoz.

![Állandó mennyiségi jogcímek egy Azure Kubernetes Services (ak) fürtben](media/concepts-storage/persistent-volume-claims.png)

Miután hozzárendelte a rendelkezésre álló tárolási erőforrást a tárolóhoz, a PersistentVolume egy PersistentVolumeClaim van *kötve* . Az állandó kötetek a jogcímek 1:1 vannak leképezve.

Az alábbi példa YAML-jegyzék egy állandó mennyiségi jogcímet mutat be, amely a *felügyelt prémium* StorageClass használja, és a lemez *5Gi* kéri a következő méretben:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

A pod-definíció létrehozásakor a következőket is megadhatja:
* Az állandó kötet jogcíme, amely a kívánt tárterületet kéri. 
* Az alkalmazások *volumeMount* az adatolvasáshoz és az adatíráshoz. 

Az alábbi YAML-jegyzék azt mutatja be, hogy a korábbi állandó kötet jogcíme hogyan csatlakoztatható a kötethez a */mnt/Azure*-ben:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Ha Windows-tárolóban lévő kötetet kíván csatlakoztatni, a meghajtóbetűjelet és elérési utat kell megadnia. Például:

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>Következő lépések

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a tároláshoz és a biztonsági mentéshez az AK-ban][operator-best-practices-storage].

Az Azure-lemezeket vagy Azure Filesokat használó dinamikus és statikus kötetek létrehozásával kapcsolatban tekintse meg a következő útmutató cikkeket:

- [Statikus kötet létrehozása az Azure-lemezek használatával][aks-static-disks]
- [Statikus kötet létrehozása Azure Files használatával][aks-static-files]
- [Dinamikus kötet létrehozása az Azure-lemezek használatával][aks-dynamic-disks]
- [Dinamikus kötet létrehozása Azure Files használatával][aks-dynamic-files]

Az alapvető Kubernetes és az AK-fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Kubernetes/AK-fürtök és-munkaterhelések][aks-concepts-clusters-workloads]
- [Kubernetes/AK-identitás][aks-concepts-identity]
- [Kubernetes/AK biztonság][aks-concepts-security]
- [Kubernetes/AK virtuális hálózatok][aks-concepts-network]
- [Kubernetes/AK-skála][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
