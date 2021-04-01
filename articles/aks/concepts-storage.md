---
title: Fogalmak – tárolás az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes szolgáltatás (ak) tárolóját, beleértve a köteteket, az állandó köteteket, a tárolási osztályokat és a jogcímeket.
services: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: bf910c66694a62505f259c0a95a88f7dfed05d19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127957"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes szolgáltatásban (ak) lévő alkalmazások tárolási lehetőségei

Előfordulhat, hogy az Azure Kubernetes szolgáltatásban (ak) futó alkalmazásoknak tárolniuk és le kell kérniük az adatgyűjtést. Egyes alkalmazás-munkaterhelések esetén ez az adattároló helyi, gyors tárterületet használhat a csomóponton, amely már nem szükséges a hüvelyek törlésekor. Az alkalmazások más számítási feladataihoz olyan tárterület szükséges, amely az Azure-platformon belül még több normál adatköteten is fennmarad. Előfordulhat, hogy több hüvelynek ugyanazokat az adatköteteket kell megosztania, vagy újra kell csatlakoztatnia az adatköteteket, ha a pod egy másik csomóponton van átütemezhetve. Végezetül előfordulhat, hogy bizalmas adatokat vagy alkalmazás-konfigurációs adatokat kell beszúrnia a hüvelybe.

![Azure Kubernetes Services-(ak-) fürtön lévő alkalmazások tárolási lehetőségei](media/concepts-storage/aks-storage-options.png)

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek a tárolást biztosítják alkalmazásai számára az AK-ban:

- [Kötetek](#volumes)
- [Állandó kötetek](#persistent-volumes)
- [Tárolási osztályok](#storage-classes)
- [Tartóskötet-jogcímek](#persistent-volume-claims)

## <a name="volumes"></a>Kötetek

Az alkalmazásoknak gyakran kell tudniuk tárolni és beolvasni az adatgyűjtést. Mivel a Kubernetes általában az egyes hüvelyeket ideiglenes, eldobható erőforrásként kezeli, a különböző megközelítések az alkalmazások igény szerinti felhasználására és megőrzésére szolgálnak. A *kötetek* a hüvelyek és az alkalmazások életciklusa közötti adattárolási,-lekérési és-megőrzési módot jelölik.

A hagyományos kötetek tárolására és lekérésére az Azure Storage által támogatott Kubernetes-erőforrások jönnek létre. Manuálisan is létrehozhatja ezeket az adatköteteket, amelyeket közvetlenül a hüvelyekhez rendelhet, vagy Kubernetes automatikusan létrehozhatja őket. Ezek az adatkötetek az Azure-lemezeket és a Azure Files is használhatják:

- Az *Azure-lemezek* használhatók Kubernetes *adatlemez* -erőforrások létrehozásához. A lemezek az Azure Premium Storage-t, a nagy teljesítményű SSD-ket, illetve az Azure standard Storage-t is használhatják, a normál HDD-k által támogatottak. A legtöbb éles és fejlesztési számítási feladathoz használja a Premium Storage-t. Az Azure-lemezek *ReadWriteOnce*-ként vannak csatlakoztatva, így csak egyetlen Pod számára érhető el. A több hüvelyrel egyidejűleg elérhető tárolási kötetek esetében használja a Azure Files.
- *Azure Files* használható egy Azure Storage-fiók által támogatott SMB 3,0-megosztás csatlakoztatására a hüvelyek számára. A fájlok lehetővé teszik az adatmegosztást több csomóponton és hüvelyen keresztül. A fájlok a normál HDD-k, illetve az Azure Premium Storage által támogatott Azure standard Storage-t is használhatják, nagy teljesítményű SSD-k által támogatottak.

A Kubernetes-ben a kötetek több, mint egy hagyományos lemezt képviselnek, ahol az információ tárolható és lekérhető. A Kubernetes kötetek a tárolók általi használatra is használhatók a pod-ba történő adatbevitelhez. A Kubernetes-ben a gyakori további mennyiségi típusok a következők:

- *emptyDir* – ezt a kötetet általában a pod ideiglenes tárolóhelyként használják. A hüvelyen belüli összes tároló hozzáfér a köteten lévő adatforgalomhoz. Az ebbe a kötetbe írt adattípusok csak a pod élettartama esetén maradnak meg, a kötetet törli a rendszer. Ez a kötet jellemzően a mögöttes helyi csomópont lemezes tárterületét használja, bár csak a csomópont memóriájában létezhet.
- *Secret (titkos* ) – Ez a kötet a bizalmas adatok hüvelybe, például jelszavakba való beadására szolgál. Először hozzon létre egy titkos kulcsot a Kubernetes API használatával. A pod vagy az üzembe helyezés megadásakor a rendszer egy adott titkot kérhet. A titkos kulcsokat csak olyan csomópontok számára biztosítjuk, amelyekhez szükség van egy ütemezett Pod-re, és a titkot a *tmpfs* tárolja, nem lemezre írva. Ha a titkos kulcsot tartalmazó csomópont utolsó podét törli, a titkos kulcsot a rendszer törli a csomópont tmpfs. A titkos kulcsok tárolása egy adott névtéren belül történik, és csak ugyanazon a névtéren belüli hüvelyek érhetik el.
- *configMap* – ezt a kötetet a kulcs-érték párok tulajdonságainak hüvelybe való beírására, például az alkalmazás konfigurációs adataiba kell beszúrni. Ahelyett, hogy az alkalmazás konfigurációs információit definiálja egy tároló képén belül, megadhatja azt Kubernetes-erőforrásként, amely könnyen frissíthető és alkalmazható a hüvelyek új példányaira, amelyeket üzembe helyeztek. A titkos kulcshoz hasonlóan először is létre kell hoznia egy ConfigMap a Kubernetes API használatával. Ezt a ConfigMap akkor lehet kérni, ha egy Pod vagy üzemelő példányt határoz meg. A ConfigMaps egy adott névtéren belül tárolódnak, és csak ugyanazon a névtéren belüli hüvelyek érhetik el.

## <a name="persistent-volumes"></a>Állandó kötetek

A pod életciklusának részeként definiált és létrehozott kötetek csak a pod törlése után állnak fenn. A hüvelyek gyakran arra várnak, hogy a tárolójuk továbbra is megmaradjon, ha a karbantartási esemény során egy másik gazdagépen átütemezett Pod, különösen a StatefulSets. Az *állandó kötet* (PV) olyan tárolási erőforrás, amelyet a Kubernetes API hozott létre és felügyel, amely az egyes Pod-k élettartamán túl is létezhet.

Az Azure-lemezek vagy-fájlok a PersistentVolume biztosítására szolgálnak. Ahogy azt a kötetek előző szakasza is jelzi, a lemezek és a fájlok választását gyakran az adatmennyiség vagy a teljesítmény szintjéhez való egyidejű hozzáférés szükségessége határozza meg.

![Állandó kötetek egy Azure Kubernetes Services (ak) fürtben](media/concepts-storage/persistent-volumes.png)

A PersistentVolume lehet *statikusan* létrehozni, vagy a Kubernetes API-kiszolgáló által *dinamikusan* létrehozott. Ha egy Pod ütemezve van, és olyan tárolót kér, amely jelenleg nem érhető el, a Kubernetes létrehozhatja az alapul szolgáló Azure Disk vagy Files tárolót, és csatolhatja azt a pod-hoz. A dinamikus kiépítés egy *StorageClass* segítségével azonosítja, hogy milyen típusú Azure-tárolót kell létrehozni.

## <a name="storage-classes"></a>Tárolási osztályok

A különböző tárolási rétegek (például a prémium és a standard) definiálásához létrehozhat egy *StorageClass*. A StorageClass a *reclaimPolicy* is meghatározza. Ez a reclaimPolicy az alapul szolgáló Azure Storage-erőforrás viselkedését szabályozza a pod törlésekor, és előfordulhat, hogy az állandó kötetre már nincs szükség. A mögöttes tárolási erőforrás törölhető vagy megtartható egy későbbi Pod-nal való használathoz.

Az AK-ban négy kezdeti `StorageClasses` kapcsolat jön létre a fürtön a fán tárolt beépülő modulok használatával:

- `default` – Felügyelt lemez létrehozásához az Azure StandardSSD Storage szolgáltatást használja. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-lemez törlődik, ha az azt használó állandó kötet törlődik.
- `managed-premium` – Felügyelt lemez létrehozásához az Azure Premium Storage szolgáltatást használja. A visszaigénylési házirend újból biztosítja, hogy az alapul szolgáló Azure-lemez törlődik, ha az azt használó állandó kötet törlődik.
- `azurefile` -Az Azure standard Storage használatával létrehoz egy Azure-fájlmegosztást. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-fájlmegosztás törölve legyen, ha az azt használó állandó kötet törlődik.
- `azurefile-premium` – Az Azure Premium Storage használatával létrehoz egy Azure-fájlmegosztást. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-fájlmegosztás törölve legyen, ha az azt használó állandó kötet törlődik.

Az új Container Storage Interface (CSI) külső beépülő modulokat (előzetes verzió) használó fürtök esetén a következő továbbiak jönnek `StorageClasses` létre:
- `managed-csi` -Az Azure StandardSSD helyileg redundáns tárolást (LRS) használ egy felügyelt lemez létrehozásához. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-lemez törlődik, ha az azt használó állandó kötet törlődik. A tárolási osztály azt is konfigurálja, hogy az állandó kötetek bővíthetők legyenek, csak az állandó mennyiségi jogcímet kell módosítania az új mérettel.
- `managed-csi-premium` -Az Azure Premium helyileg redundáns tárolást (LRS) használja egy felügyelt lemez létrehozásához. A visszaigénylési házirend újból biztosítja, hogy az alapul szolgáló Azure-lemez törlődik, ha az azt használó állandó kötet törlődik. Hasonlóképpen, ez a tárolási osztály lehetővé teszi az állandó kötetek kibontását.
- `azurefile-csi` -Az Azure standard Storage használatával létrehoz egy Azure-fájlmegosztást. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-fájlmegosztás törölve legyen, ha az azt használó állandó kötet törlődik.
- `azurefile-csi-premium` – Az Azure Premium Storage használatával létrehoz egy Azure-fájlmegosztást. A visszaigénylési házirend biztosítja, hogy az alapul szolgáló Azure-fájlmegosztás törölve legyen, ha az azt használó állandó kötet törlődik.

Ha nem ad meg StorageClass egy állandó kötethez, a rendszer az alapértelmezett StorageClass használja. Ügyeljen arra, hogy az állandó kötetek kérésekor a szükséges tárterületet használják. A használatával további igényekhez is létrehozhat StorageClass `kubectl` . Az alábbi példa prémium Managed Disks használ, és megadja, hogy a mögöttes Azure-lemezt meg kell *őrizni* a pod törlésekor:

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

A PersistentVolumeClaim egy adott StorageClass, elérési módra és méretre vonatkozó lemez-vagy file Storage-t kér. A Kubernetes API-kiszolgáló dinamikusan kiépítheti a mögöttes tárolási erőforrást az Azure-ban, ha nincs meglévő erőforrás a jogcím teljesítéséhez a definiált StorageClass alapján. A pod definíciója tartalmazza a kötet csatlakoztatását, ha a kötet csatlakoztatva van a pod-hoz.

![Állandó mennyiségi jogcímek egy Azure Kubernetes Services (ak) fürtben](media/concepts-storage/persistent-volume-claims.png)

Egy PersistentVolume egy PersistentVolumeClaim van *kötve* , amint egy rendelkezésre álló tárolási erőforrás hozzá lett rendelve az azt kérő Pod-hez. Az állandó kötetek 1:1-es leképezése a jogcímekre történik.

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

Ha létrehoz egy Pod-definíciót, a rendszer az állandó kötet jogcímet adja meg a kívánt tár igényléséhez. Ezután megadhatja az alkalmazások *volumeMount* az olvasási és írási adatbevitelhez. Az alábbi YAML-jegyzék azt mutatja be, hogy a korábbi állandó kötet jogcíme hogyan csatlakoztatható a kötethez a */mnt/Azure*-ben:

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
