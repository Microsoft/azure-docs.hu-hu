---
title: Rövid útmutató – Azure Confidential Computing virtuális gép létrehozása a Marketplace-en
description: Az üzembe helyezés első lépésekhez megtanulhatja, hogyan hozhat létre gyorsan egy bizalmas számítástechnikai virtuális gépet a Marketplace-en.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: be935dbd7e4559bcad8c5cf78622a5c63810f54c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812389"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>Rövid útmutató: Azure Confidential Computing virtuális gép üzembe helyezése a Marketplace-en

Az Azure Confidential Computing használatának első Azure Marketplace intel SGX-alapú virtuális gép (VM) létrehozása a Azure Marketplace használatával. Ezután telepíteni fogja az Open Enclave Software Development Kit (SDK) csomagot a fejlesztési környezet beállításához. 

Ez az oktatóanyag akkor ajánlott, ha gyorsan szeretne üzembe helyezni egy bizalmas számítástechnikai virtuális gépet. A virtuális gépek speciális hardveren futnak, és meghatározott konfigurációs bemeneteket igényelnek a kívánt futtatáshoz. Az ebben a rövid útmutatóban ismertetett Marketplace-ajánlat megkönnyíti az üzembe helyezést a felhasználói bevitel korlátozásának köszönhetően.

Ha további egyéni konfigurációval szeretne üzembe helyezni egy bizalmas számítási virtuális gépet, kövesse az Azure Portal Confidential Compute virtuális gép üzembe [helyezésének lépéseit.](quick-create-portal.md)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt [hozzon](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) létre egy fiókot.

> [!NOTE]
> Az ingyenes próbafiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Frissítsen egy fizetős előfizetésre.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Felül írja be az **Azure Confidential Computing keresősávba** a következőt: .

1. A **Marketplace szakaszban válassza az Azure Confidential Computing (Virtual Machine)** **lehetőséget.** 

    ![Válassza a Marketplace lehetőséget](media/quick-create-marketplace/portal-search-marketplace.png)    

1. Az Azure Confidential Compute üzembe helyezésének kezdőlapján válassza a **Létrehozás lehetőséget.**
 

## <a name="configure-your-virtual-machine"></a>A virtuális gép konfigurálása

1. Az Alapvető **beállítások lapon** válassza ki az **Előfizetést és** az **Erőforráscsoportot.** Az erőforráscsoportnak üresnek kell lennie ahhoz, hogy ebből a sablonból üzembe helyez egy virtuális gépet.

1. Írja be vagy válassza ki az alábbi értékeket:

   * **Régió:** Válassza ki az Önnek megfelelő Azure-régiót.

        > [!NOTE]
        > A bizalmas számítási virtuális gépek csak meghatározott régiókban elérhető speciális hardveren futnak. A virtuális gépekhez elérhető DCsv2-Series az elérhető [régiókat.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)
    
    * **Válassza a Kép:** Válasszon ki egy képet. Ha szeretné végrehajtani ezt az oktatóanyagot, válassza az Ubuntu 18.04 (Gen 2) lehetőséget. Ellenkező esetben a rendszer az alábbi megfelelő lépésekben átirányítja. 

    * **Virtuális gép neve:**, adja meg az új virtuális gép nevét.

    * **Hitelesítés típusa:** Linux rendszerű virtuális gép létrehozásakor válassza a nyilvános **SSH-kulcsot.** 

         > [!NOTE]
         > Választhat, hogy a hitelesítéshez egy SSH-s nyilvános kulcsot vagy egy jelszót használ. Az SSH használata biztonságosabb. Az SSH-kulcs létrehozásával kapcsolatban lásd az [SSH-kulcsok az Azure-ban történő létrehozásának lépéseit Linux és Mac rendszeren Linux rendszerű virtuális gépek számára](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Felhasználónév:** Adja meg a virtuális gép rendszergazdai nevét.

    * **Nyilvános SSH-kulcs:** Ha van, adja meg az RSA nyilvános kulcsát.
    
    * **Jelszó:** Ha van, adja meg a jelszót a hitelesítéshez.
 
1. Válassza **a Tovább: Virtuális gép beállításai** gombot a képernyő alján.

    > [!IMPORTANT]
    > Várjon, amíg az oldal frissül. Nem *jelenik meg* a "Confidential Computing DCsv2 sorozatú virtuális gépek korlátozott számú régióban elérhetők" üzenet. Ha ez az üzenet továbbra is fennáll, térjen vissza az előző oldalra, és válasszon ki egy DCsv2-Series régiót.

1. A **méret módosítása beállításhoz** válasszon egy olyan virtuális gépet, amely bizalmas számítási képességekkel rendelkezik a méretválasztóban. 

    > [!TIP]
    > A ( , DC1s_v2 **,** **DC2s_v2**, **DC4s_V2**) és a (DC4s_V2) és a (DC8_v2) **méreteknek kell DC8_v2.** Jelenleg csak ezek a virtuálisgép-méretek támogatják a bizalmas számításokat. [További információ](virtual-machine-solutions.md).

1. Az **Operációsrendszer-lemez típusa mezőben** válasszon egy lemeztípust.

1. A **Virtual Network** hozzon létre egy újat, vagy válasszon egy meglévő erőforrásból.

1. Az **Alhálózathoz** hozzon létre egy újat, vagy válasszon egy meglévő erőforrásból.

1. A **Nyilvános bejövő portok kiválasztása beállításhoz válassza** az **SSH(Linux)/RDP(Windows) lehetőséget.** Ebben a rövid útmutatóban ez a lépés szükséges a virtuális géphez való csatlakozáshoz és az Open Enclave SDK konfigurációjának befejezéséhez. 

1. A **Rendszerindítási diagnosztika funkciónál** hagyja letiltva ezt a rövid útmutatót. 

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

1. Az Áttekintés **+ létrehozás panelen** válassza a Létrehozás **lehetőséget.**

> [!NOTE]
> Folytassa a következő szakaszsal, és ha Linux rendszerű virtuális gépet helyezett üzembe, folytassa ezzel az oktatóanyagkal. Ha Windows rendszerű virtuális gépet helyezett üzembe, kövesse az alábbi lépéseket a [Windows](../virtual-machines/windows/connect-logon.md) rendszerű virtuális géphez való csatlakozáshoz, majd telepítse az [OE SDK-t Windows rendszeren.](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)


## <a name="connect-to-the-linux-vm"></a> Kapcsolódás a Linux rendszerű virtuális géphez

Ha már megnyitott egy BASH-parancssort, csatlakozzon az Azure-beli virtuális géphez az **ssh** paranccsal. A következő parancsban helyettesítse be a Linux rendszerű virtuális gép felhasználónevét és IP-címét a csatlakozáshoz.

```bash
ssh azureadmin@40.55.55.555
```

A virtuális gép nyilvános IP-címét a Azure Portal a virtuális gép Áttekintés szakaszában találja.

![IP-cím az Azure Portalon](media/quick-create-portal/public-ip-virtual-machine.png)

Ha Windows rendszeren fut, és nincs BASH-rendszerhéja, telepítsen egy SSH-ügyfelet, például a PuTTY-t.

1. [Töltse le és telepítse a PuTTY-t](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Futtassa a PuTTY-t.

1. A PuTTY konfigurációs képernyőjén adja meg a virtuális gép nyilvános IP-címét.

1. Válassza **a Megnyitás lehetőséget,** és írja be a felhasználónevét és jelszavát.

A Linux rendszerű virtuális gépekhez való csatlakozásról további információt a [Linux rendszerű virtuális gép az Azure-ban a Portal használatával történő létrehozását](../virtual-machines/linux/quick-create-portal.md) ismertető cikkben talál.

> [!NOTE]
> Ha PuTTY biztonsági riasztást lát arról, hogy a kiszolgáló gazdakulcsa nincs gyorsítótárazva a beállításjegyzékben, válasszon az alábbi lehetőségek közül. Ha megbízik a gazdagépben, válassza az **Igen** lehetőséget a kulcs PuTTy gyorsítótárhoz való hozzáadásához és a csatlakozás folytatásához. Ha csak egyszer szeretne csatlakozni anélkül, hogy hozzáadja a kulcsot a gyorsítótárhoz, válassza a **Nem lehetőséget.** Ha nem bízik meg ebben a gazdagépben, válassza a **Mégse lehetőséget** a kapcsolat megszakításhoz.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Az Open Enclave SDK (OE SDK) telepítése <a id="Install"></a>

Kövesse az [OE SDK](https://github.com/openenclave/openenclave) ubuntu 18.04 LTS Gen 2 rendszerképet futtató DCsv2-Series virtuális gépen való telepítéséhez szükséges részletes utasításokat. 

Ha a virtuális gép Ubuntu 18.04 LTS Gen 2-en fut, kövesse az [Ubuntu 18.04 telepítési utasításait.](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md) 

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. Az Intel és a Microsoft APT-adattárak konfigurálása

```bash
echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main' | sudo tee /etc/apt/sources.list.d/intel-sgx.list
wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add -

echo "deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-7 main" | sudo tee /etc/apt/sources.list.d/llvm-toolchain-bionic-7.list
wget -qO - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -

echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | sudo tee /etc/apt/sources.list.d/msprod.list
wget -qO - https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

#### <a name="2-install-the-intel-sgx-dcap-driver"></a>2. Az Intel SGX DCAP-illesztő telepítése
Előfordulhat, hogy az Ubuntu egyes verzióiban már telepítve van az Intel SGX-illesztőprogram. Ellenőrizze a következő paranccsal: 

```bash
dmesg | grep -i sgx
[  106.775199] sgx: intel_sgx: Intel SGX DCAP Driver {version}
``` 
Ha a kimenet üres, telepítse az illesztőt: 

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.7/linux/distro/ubuntu18.04-server/sgx_linux_x64_driver_1.35.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Használja az Intel SGX webhelyének legújabb Intel [SGX DCAP-illesztőprogramját.](https://01.org/intel-software-guard-extensions/downloads)

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Az Intel és az Open Enclave csomagok és függőségek telepítése

```bash
sudo apt -y install clang-8 libssl-dev gdb libsgx-enclave-common libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Ez a lépés az [az-dcap-client](https://github.com/microsoft/azure-dcap-client) csomagot is telepíti, amely a távoli igazolás végrehajtásához szükséges az Azure-ban.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **Az Open Enclave SDK telepítésének ellenőrzése**

A telepített SDK ellenőrzéséhez és használatához lásd: [Using the Open Enclave SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) on GitHub (Az Open Enclave SDK használata a GitHubon).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. 

Válassza ki a virtuális gép erőforráscsoportját, majd válassza a **Törlés lehetőséget.** Az erőforrások törlésének befejezéséhez erősítse meg az erőforráscsoport nevét.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy bizalmas számítási virtuális gépet, és telepítette az Open Enclave SDK-t. További információ az Azure-beli bizalmas számítási virtuális gépekről: [Megoldások a Virtual Machines.](virtual-machine-solutions.md) 

A GitHub Open Enclave SDK-mintáiból megismerheti, hogyan építhet fel bizalmas számítási alkalmazásokat. 

> [!div class="nextstepaction"]
> [Open Enclave SDK-minták kiépítése](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
