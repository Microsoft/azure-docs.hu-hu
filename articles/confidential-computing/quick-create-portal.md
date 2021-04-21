---
title: Rövid útmutató – Azure Confidential Computing virtuális gép létrehozása a Azure Portal
description: Az üzembe helyezés első lépésekhez megtanulhatja, hogyan hozhat létre gyorsan egy bizalmas számítási virtuális gépet a Azure Portal.
author: JBCook
ms.author: JenCook
ms.date: 04/23/2020
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.custom:
- mode-portal
ms.openlocfilehash: 1ae6631c3f6ee71d7a09832956c7e687ceca22b6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819051"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-azure-portal"></a>Rövid útmutató: Azure Confidential Computing virtuális gép üzembe helyezése a Azure Portal

Az Azure Confidential Computing használatának első Azure Portal intel SGX-alapú virtuális gép (VM) létrehozása a Azure Portal használatával. Ezután telepíteni fogja az Open Enclave Software Development Kit (SDK) csomagot a fejlesztési környezet beállításához. 

Ez az oktatóanyag akkor ajánlott, ha egyéni konfigurációval szeretne üzembe helyezni egy bizalmas számítási virtuális gépet. Ellenkező esetben javasoljuk, hogy kövesse a Microsoft kereskedelmi piactéren a confidential Computing virtuális gépek üzembe [helyezésének lépéseit.](quick-create-marketplace.md)


## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt [hozzon](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) létre egy fiókot.

> [!NOTE]
> Az ingyenes próbafiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Frissítsen egy fizetős előfizetésre.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A lap tetején válassza az **Erőforrás létrehozása lehetőséget.**

1. A **Marketplace panelen** válassza a **számítás lehetőséget** a bal oldalon.

1. Keresse meg és válassza **a Virtuális gép lehetőséget.**

    ![Virtuális gép üzembe helyezése](media/quick-create-portal/compute-virtual-machine.png)

1. A Virtuális gép kezdőlapján válassza a Létrehozás **lehetőséget.**


## <a name="configure-a-confidential-computing-virtual-machine"></a>Bizalmas számítási virtuális gép konfigurálása

1. Az Alapvető **beállítások lapon** válassza ki az **Előfizetést és** az **Erőforráscsoportot.**

1. A **Virtuális gép neve alatt** adja meg az új virtuális gép nevét.

1. Írja be vagy válassza ki az alábbi értékeket:

   * **Régió:** Válassza ki az Önnek megfelelő Azure-régiót.

        > [!NOTE]
        > A bizalmas számítási virtuális gépek csak bizonyos régiókban elérhető speciális hardveren futnak. A virtuális gépekhez elérhető DCsv2-Series az elérhető [régiókat.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)

1. Konfigurálja a virtuális géphez használni szeretne operációsrendszer-lemezképet.

    * **Válassza a Kép:** Ebben az oktatóanyagban válassza az Ubuntu 18.04 LTS lehetőséget. Választhatja a Windows Server 2019, a Windows Server 2016 vagy az Ubuntu 20.04 LTS lehetőséget is. Ha ezt választja, a rendszer ennek megfelelően átirányítja az oktatóanyagba.
    
    * **A Gen 2:** Confidential compute virtual machines only run on 2 generation images (A bizalmas számítási virtuális gépek csak 2. generációs rendszerképeken [futnak) rendszerképet.](../virtual-machines/generation-2.md) Győződjön meg arról, hogy a kiválasztott rendszerkép 2. generációs rendszerkép. Kattintson a **fenti Speciális** lapra, ahol a virtuális gépet konfigurálja. Görgessen le, amíg meg nem találja a "Vm Generation" (Virtuális gép generációja) szakaszt. Válassza a Gen 2 lehetőséget, majd vissza az **Alapvető beállítások** lapra.
    

        ![Speciális lap](media/quick-create-portal/advanced-tab-virtual-machine.png)


        ![Virtuálisgép-generáció](media/quick-create-portal/gen2-virtual-machine.png)

    * **Térjen vissza az alapszintű konfigurációhoz:** Vissza az Alapok **lapra** a felső navigációs sáv használatával.

1. Válasszon ki egy virtuális gépet, amely bizalmas számítási képességekkel rendelkezik a méretválasztóban a **méret módosítása kiválasztásával.** A virtuális gép méretválasztóban kattintson az Összes **szűrő törlése elemre.** Válassza **a Szűrő hozzáadása** lehetőséget, válassza a Család szűrőtípust, majd csak a Bizalmas számítás **lehetőséget.** 

    ![DCsv2-Series virtuális gépek](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > A ( , DC1s_v2 **,** **DC2s_v2**, **DC4s_V2**) és a (DC4s_V2) és a (DC8_v2) **méreteknek kell DC8_v2.** Jelenleg csak ezek a virtuálisgép-méretek támogatják az Intel SGX bizalmas számítástechnikát. [További információ](virtual-machine-solutions.md).

1. Adja meg a következő adatokat:

   * **Hitelesítés típusa:** Linux rendszerű virtuális gép létrehozásakor válassza a nyilvános **SSH-kulcsot.** 

        > [!NOTE]
        > Választhat, hogy a hitelesítéshez egy SSH-s nyilvános kulcsot vagy egy jelszót használ. Az SSH használata biztonságosabb. Az SSH-kulcs létrehozásával kapcsolatban lásd az [SSH-kulcsok az Azure-ban történő létrehozásának lépéseit Linux és Mac rendszeren Linux rendszerű virtuális gépek számára](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Felhasználónév:** Adja meg a virtuális gép rendszergazdai nevét.

    * **Nyilvános SSH-kulcs:** Ha van, adja meg az RSA nyilvános kulcsát.
    
    * **Jelszó:** Ha van, adja meg a jelszót a hitelesítéshez.

    * **Nyilvános bejövő portok:** Válassza a **Kijelölt** portok engedélyezése lehetőséget, majd a Nyilvános bejövő portok kiválasztása **listában** válassza az **SSH (22)** és a **HTTP (80)** lehetőséget. Ha Windows rendszerű virtuális gépet helyez üzembe, válassza a **HTTP (80)** és **az RDP (3389) lehetőséget.** Ebben a rövid útmutatóban ez a lépés szükséges a virtuális géphez való csatlakozáshoz és az Open Enclave SDK konfigurációjának befejezéséhez. 

     ![Bejövő portok](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Módosításokat a **Lemezek lapon.**

   * Ha a virtuális géphez DC1s_v2 **,** DC2s_v2 **virtuális** DC4s_V2, válasszon olyan lemeztípust, amely standard SSD **vagy** **prémium SSD.**  
   * Ha virtuális gépet **DC8_v2,** válassza a **standard SSD** lehetőséget lemeztípusként.

1. A következő lapokon vagy az alapértelmezett beállításokat meg is őrizve bármilyen módosítást el kell eszközlni a beállításokon.

    * **Hálózat**
    * **Felügyelet**
    * **Vendégkonfiguráció**
    * **Címkék**

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

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="IP-cím az Azure Portalon":::

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

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. Az Intel- és a Microsoft APT-adattárak konfigurálása

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
