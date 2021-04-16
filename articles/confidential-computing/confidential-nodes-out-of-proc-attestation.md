---
title: Igazolás támogatása az Intel SGX ajánlati segítő DaemonSettel az Azure-ban (előzetes verzió)
description: Egy DaemonSet, amely az ajánlatot az Intel SGX-alkalmazásfolyamaton kívül generálja. Ez a cikk azt ismerteti, hogyan biztosítja a folyamaton kívülre vonatkozó igazolási létesítményt a tárolóban futó bizalmas számítási feladatokhoz.
ms.service: container-service
ms.subservice: confidential-computing
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 849fd7afa3f9365f31ee8e03d9f9cc2174d64304
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484404"
---
# <a name="platform-software-management-with-intel-sgx-quote-helper-daemonset-preview"></a>Platformszoftverek kezelése az Intel SGX ajánlatkezelő daemonSet (előzetes verzió) használatával

[A távoli igazolást](confidential-computing-enclaves.md) végző enklávéalkalmazások generált ajánlatot igényelnek. Ez az ajánlat kriptográfiai igazolást biztosít az identitásról és az alkalmazás állapotáról, valamint az enklávét futtató környezetről. Az ajánlat generálása olyan megbízható szoftverösszetevőt igényel, amely az Intel Platform Software Components (PSW) részét képezi.

## <a name="overview"></a>Áttekintés
 
Az Intel két igazolási módot támogat az ajánlat generálása futtatásához:

- *A folyamaton belül az* enklávé alkalmazásfolyamatában található megbízható szoftverösszetevők vannak telepítve.

- *A folyamaton kívüli gazdagépen* az enklávéalkalmazáson kívül található megbízható szoftverösszetevők is találhatóak.
 
Az Open Enclave SDK-val készült Intel Software Guard Extension- (Intel SGX-) alkalmazások alapértelmezés szerint a folyamaton keresztüli igazolási módot használják. Az Intel SGX-alapú alkalmazások lehetővé teszik a folyamaton túli igazolási módot. Ha ezt a módot szeretné használni, további üzemeltetésre van szüksége, és az alkalmazáson kívül kell elérhetővé teszi a szükséges összetevőket, például az Architectural Enclave Service Manager (AESM) összetevőt.

Ez a funkció növeli az enklávé-alkalmazások üzemidejét az Intel platform frissítései vagy a DCAP-illesztőprogramok frissítései során. Ezért javasoljuk a használatát.

Ha engedélyezni szeretné ezt a funkciót egy Azure Kubernetes Services- (AKS-) fürtön, adja hozzá a parancsot az Azure CLI-hez a bizalmas számítási bővítmény `--enable-sgxquotehelper` engedélyezésekor. 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

További információ: [Rövid útmutató: AKS-fürt](confidential-nodes-aks-get-started.md)üzembe helyezése bizalmas számítási csomópontokkal az Azure CLI használatával.

## <a name="benefits-of-the-out-of-process-mode"></a>A folyamaton nem használható mód előnyei

Az alábbi lista az igazolási mód néhány fő előnyét ismerteti:

-   A PSW ajánlat-generáló összetevőihez nem szükséges frissítés az egyes tárolóba helyezni kívánt alkalmazásokhoz. A tárolótulajdonosoknak nem kell kezelniük a frissítéseket a tárolójukban. A tárolótulajdonosok ehelyett arra a szolgáltatói felületre támaszkodnak, amely meghívja a központosított szolgáltatást a tárolón kívül. A szolgáltató frissíti és kezeli a tárolót.

-   Nem kell aggódnia az igazolási hibák miatt az elavult PSW-összetevők miatt. Az összetevők frissítéseit a szolgáltató kezeli.

-   A folyamaton kívül mód jobban kihasználja az EPC-memóriát, mint a folyamat közbeni mód. Folyamat közben minden enklávéalkalmazásnak példányosoznia kell a QE és a PCE másolatát a távoli igazoláshoz. Folyamaton túli módban nincs szükség arra, hogy a tároló tárolja ezeket az enklávékat, ezért nem használ fel enklávémot a tárolók kvótájaból.

-   Amikor az Intel SGX-illesztőt egy Linux-kernelen keresztül vezeti be, kényszerítve van arra, hogy egy enklávé magasabb szintű jogosultsággal rendelkezik. Ez a jogosultság lehetővé teszi, hogy az enklávé meghívja a PCE-t, ami megszakítja a folyamat közben futó enklávéalkalmazást. Alapértelmezés szerint az enklávaok nem kapják meg ezt az engedélyt. Ha ezt a jogosultságot enklávéalkalmazásnak adja, az alkalmazás telepítési folyamatának módosításaira van szükség. Ezzel szemben a folyamaton túli módban a folyamaton túli kérelmeket kezelő szolgáltatás szolgáltatója biztosítja, hogy a szolgáltatás ezzel a jogosultsággal telepíthető.

-   A PSW-hez és a DCAP-hez nem kell visszamenőleges kompatibilitást ellenőriznie. A PSW ajánlat-generáló összetevőinek frissítéseit a szolgáltató a frissítés előtt ellenőrzi a visszamenőleges kompatibilitás érdekében. Ez segít kezelni a kompatibilitási problémákat a bizalmas számítási feladatok frissítésének üzembe helyezése előtt.

## <a name="confidential-workloads"></a>Bizalmas számítási feladatok

Az ajánlatkérés és az ajánlat létrehozása külön fut, de ugyanazon a fizikai gépen. Az ajánlat generálása központosított, és az összes entitás ajánlatkérését szolgálja ki. Ahhoz, hogy bármely entitás lekérte az idézőjeleket, a felületet megfelelően definiálni kell, és felderíthetőnek kell lennie.

![Az árajánlat-kérelmező, az ajánlat létrehozása és a felület közötti kapcsolatokat bemutató diagram.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Ez az absztrakt modell a bizalmas számítási feladatok forgatókönyvére vonatkozik a már elérhető AESM-szolgáltatás előnyeinek kihasználása által. Az AESM tárolóba van tárolódva, és DaemonSetként van üzembe helyezni a Kubernetes-fürtön. A Kubernetes garantálja az AESM-szolgáltatástároló podba csomagolt egyetlen példányát az egyes ügynökcsomóponton való üzembe helyezéshez. Az új Intel SGX idézet DaemonSet függőséget fog tartalmazni az sgx-device-plugin DaemonSettől, mert az AESM-szolgáltatás tárolója EPC memóriát kér az sgx-device-beépülő modulból a QE és a PCE-enklávek indításához.

Minden tárolónak le kell választania a folyamaton kívüli ajánlatkészítést a környezeti változó beállításával a `SGX_AESM_ADDR=1` létrehozás során. A tárolónak tartalmaznia kell a libsgx-quote-ex csomagot is, amely felelős azért, hogy a kérést az alapértelmezett Unix-tartomány szoftvercsatornájára irányja.

Egy alkalmazás továbbra is használhatja a folyamaton belüli igazolást, mint korábban, de az alkalmazáson belül nem használható egyszerre feldolgozás alatt és használaton belül. A folyamaton túli infrastruktúra alapértelmezés szerint elérhető, és erőforrásokat használ fel.

## <a name="sample-implementation"></a>Példa implementációra

Az alábbi Docker-fájl az Open Enclave-en alapuló alkalmazások mintája. Állítsa be a környezeti változót a Docker-fájlban, vagy állítsa be az üzembe `SGX_AESM_ADDR=1` helyezési fájlban. Az alábbi minta a Docker-fájl és az üzembe helyezés részleteit tartalmazza. 

  > [!Note] 
  > Ahhoz, hogy a folyamaton belülről származó igazolás megfelelően működjön, az Intel libsgx-quote-ex könyvtárát az alkalmazástárolóba kell csomagolni.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for details
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
# This sets the flag for out-of-process attestation mode. Alternatively you can set this flag on the deployment files.
ENV SGX_AESM_ADDR=1 

CMD make run
```
Másik lehetőségként beállíthatja a folyamaton belüli igazolási módot az üzembe helyezési .yaml-fájlban. Ezt a következőképpen teheti meg:

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

[Rövid útmutató: AKS-fürt üzembe helyezése bizalmas számítási csomópontokkal az Azure CLI használatával](./confidential-nodes-aks-get-started.md)

[Gyors kezdőminták – bizalmas tárolók](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKUs](../virtual-machines/dcv2-series.md)
