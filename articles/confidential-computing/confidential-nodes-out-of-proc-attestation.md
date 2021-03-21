---
title: Folyamaton kívüli igazolás támogatása az Intel SGX ENKLÁVÉHOZ quote Helper Daemonset elemet az Azure-ban (előzetes verzió)
description: Daemonset elemet a SGX ENKLÁVÉHOZ alkalmazás folyamatán kívüli ajánlat létrehozásához. Ez a cikk azt mutatja be, hogy a rendszer a rovided kívüli, a tárolón belül futó bizalmas számítási feladatokhoz a folyamaton kívüli igazolási létesítményt ismerteti.
ms.service: container-service
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 5d872032ea5b4e08c3f436dd3bfc202786d8514d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553255"
---
# <a name="platform-software-management-with-sgx-quote-helper-daemon-set-preview"></a>Platform Software Management a SGX ENKLÁVÉHOZ quote Helper Daemon set (előzetes verzió)

A távoli igazolást végző [enklávé-alkalmazásokhoz](confidential-computing-enclaves.md) generált idézőjel szükséges. Ez az ajánlat kriptográfiai igazolást nyújt az alkalmazás identitásáról és állapotáról, valamint az enklávét futtató környezetről. Az árajánlat létrehozásához az Intel platform Software Components (PSW) részét képező megbízható szoftver-összetevők szükségesek.

## <a name="overview"></a>Áttekintés
 
Az Intel két igazolási módot támogat az idézőjelek létrehozásához:
- **in-proc**: az enklávé alkalmazás folyamatán belül tárolja a megbízható szoftver összetevőit.

- **folyamaton** kívüli: az enklávé alkalmazáson kívül tárolja a megbízható szoftverek összetevőit.
 
Az Open enklávé SDK-val létrehozott SGX ENKLÁVÉHOZ-alkalmazások alapértelmezés szerint a-proc igazolási módot használják. A SGX ENKLÁVÉHOZ-alapú alkalmazások lehetővé teszik a folyamaton kívüli működést, és a szükséges összetevők (például az építészeti enklávé Service Manager (AESM), az alkalmazáson kívüli) további üzemeltetését igénylik.

A funkció használata **erősen ajánlott**, mivel az az időpontot fokozza az enklávé alkalmazásai számára az Intel platform frissítései vagy a DCAP-illesztőprogram frissítései során.

A szolgáltatás az AK-fürtön való engedélyezéséhez módosítsa az Add--Enable-sgxquotehelper parancsot a CLI-be a bizalmas számítástechnikai bővítmény engedélyezésekor. Részletes CLI-utasítások [itt](confidential-nodes-aks-get-started.md)találhatók: 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

## <a name="why-and-what-are-the-benefits-of-out-of-proc"></a>Miért és milyen előnyökkel jár az out-of-proc?

-   Nem szükséges frissítés a PSW-hoz készült idézőjelek létrehozásához az egyes tároló alkalmazások esetében: a folyamaton kívüli tárolók tulajdonosainak nem kell a tárolóban lévő frissítéseket kezelnie. A tároló tulajdonosai Ehelyett a szolgáltató által megadott felületet használják, amely a központi szolgáltatást a tárolón kívül hívja meg, amelyet a szolgáltató fog frissíteni és felügyelni.

-   Az elavult PSW-összetevők miatt nem kell aggódnia az igazolási hibákkal kapcsolatban: az idézőjelek létrehozása magában foglalja a megbízható számítástechnikai alap (TCB) részét képező, megbízható SW Components-Quoting enklávé (QE) & kiépítési tanúsítvány enklávéját (PCE). Ezeknek az SW-összetevőknek naprakésznek kell lenniük az igazolási követelmények fenntartása érdekében. Mivel a szolgáltató kezeli ezeknek az összetevőknek a frissítéseit, az ügyfeleknek soha nem kell az igazolási hibákkal foglalkoznia a tárolón belüli elavult megbízható SW-összetevők miatt.

-   Az EPC-memória jobb kihasználtsága a folyamaton belüli igazolási módban, minden egyes enklávé-alkalmazásnak a QE és a PCE másolatát kell létrehoznia a távoli igazoláshoz. A folyamaton kívül nem szükséges, hogy a tároló tárolja ezeket az enklávékat, így nem használja az enklávé memóriáját a tároló kvótából.

-   Védelmet biztosít a kernel-kényszerítés ellen, ha a SGX ENKLÁVÉHOZ-illesztőprogram a Linux kernelbe áramlik, és az enklávé magasabb szintű jogosultságokkal fog rendelkezni. Ez a jogosultság lehetővé teszi az enklávé számára a PCE meghívását, amely a folyamaton kívüli módban futó enklávé-alkalmazást fogja megszüntetni. Alapértelmezés szerint az enklávék nem kapják meg ezt az engedélyt. Ha ezt a jogosultságot egy enklávé-alkalmazáshoz kívánja megadni, az alkalmazás telepítési folyamatának módosítására van szükség. Ezt egyszerűen kezelheti az olyan folyamaton kívüli modellekhez, mint a folyamaton kívüli kérelmeket kezelő szolgáltatás szolgáltatója, gondoskodni fog arról, hogy a szolgáltatás telepítve legyen ezzel a jogosultsággal.

-   A PSW & DCAP való visszamenőleges kompatibilitás ellenőrzése nem szükséges. A rendszer a frissítés előtt ellenőrzi, hogy a PSW az idézőjel generációs összetevőinek frissítéseit a szolgáltató visszafelé kompatibilis-e. Ez segítséget nyújt a kompatibilitási problémák előzetes kezelésében és a bizalmas számítási feladatokhoz tartozó frissítések telepítése előtt.

## <a name="how-does-the-out-of-proc-attestation-mode-work-for-confidential-workloads-scenario"></a>Hogyan működik a folyamaton kívüli igazolási mód a bizalmas számítási feladatok esetén?

A magas szintű kialakítás azt a modellt követi, amelyben az árajánlat-kérelmező és az idézőjelek létrehozása külön történik, de ugyanazon a fizikai gépen. Az idézőjelek létrehozása központosított módon történik, és az összes entitásra vonatkozó kérelmeket szolgál ki. Az illesztőfelületet megfelelően meg kell határozni, és minden entitás számára meg kell állapítani az idézőjelek kérését.

![SGX enklávéhoz-ajánlat segítő AESM](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

A fenti absztrakt modell a bizalmas munkaterhelési forgatókönyvre vonatkozik, a már elérhető AESM szolgáltatás kihasználása révén. A AESM tárolóban van, és Daemonset elemet a Kubernetes-fürtön. A Kubernetes az egyes ügynökök csomópontjain üzembe helyezhető AESM-tárolók egyetlen példányát garantálja. Az új SGX ENKLÁVÉHOZ-daemonset elemet az SGX enklávéhoz-Device-plugin daemonset elemet függ, mivel a AESM-szolgáltatás tárolója az SGX enklávéhoz-Device-beépülő modulból kér EPC memóriát a QE és a PCE enklávék elindításához.

Minden tárolónak a folyamaton kívüli idézőjelek létrehozásához kell bejelentkeznie a **SGX_AESM_ADDR = 1** környezeti változó beállításával a létrehozás során. A tárolónak tartalmaznia kell a libsgx-quota-ex csomagot is, amely felelős az alapértelmezett Unix-tartományhoz tartozó kérelem irányításához

Egy alkalmazás továbbra is használhatja a folyamaton belüli igazolást, de az alkalmazásban nem használható egyszerre a-proc és az out-of-proc. A folyamaton kívüli infrastruktúra alapértelmezés szerint elérhető, és erőforrásokat használ.

## <a name="sample-implementation"></a>Példa implementáció

Az alábbi Docker-fájl egy Open enklávé-alapú alkalmazás mintája. Állítsa be a SGX_AESM_ADDR = 1 környezeti változót a Docker-fájlban, vagy állítsa azt a telepítési fájlra. Kövesse az alábbi mintát a Docker-fájl és az üzembe helyezési YAML részleteihez. 

  > [!Note] 
  > Az Intel által az **libsgx** -ből származó, a folyamaton kívüli igazoláshoz az alkalmazás-tárolóban kell becsomagolni, hogy megfelelően működjön.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
Azt is megteheti, hogy az üzembe helyezési YAML fájlban az alább látható módon beállíthatja a folyamaton kívüli igazolási módot

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>Következő lépések
[Bizalmas csomópontok (DCsv2-sorozat) kiépítése az AK-on](./confidential-nodes-aks-get-started.md)

[Gyors kezdő minták – bizalmas tárolók](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU-lista](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
