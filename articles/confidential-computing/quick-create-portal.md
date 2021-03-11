---
title: Rövid útmutató – Azure bizalmas számítástechnikai virtuális gép létrehozása a Azure Portal
description: Ismerkedjen meg az üzemelő példányokkal, hogy megtanulja, hogyan hozhat létre gyorsan egy bizalmas számítástechnikai virtuális gépet a Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/23/2020
ms.author: JenCook
ms.openlocfilehash: 3f0984acd66bd5d6c148be8451938d3152fb9ca7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566668"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-azure-portal"></a>Gyors útmutató: Azure-beli bizalmas számítástechnikai virtuális gép üzembe helyezése a Azure Portal

Ismerkedjen meg az Azure bizalmas számítástechnikai szolgáltatásával, amely az Intel SGX ENKLÁVÉHOZ által támogatott virtuális gép (VM) létrehozásához Azure Portal használatával. Ezután telepíti a Open enklávé szoftverfejlesztői készletét (SDK) a fejlesztési környezet beállításához. 

Ez az oktatóanyag akkor ajánlott, ha egy bizalmas számítási virtuális gép egyéni konfigurációval való üzembe helyezését érdekli. Ellenkező esetben javasoljuk, hogy a [Microsoft kereskedelmi piactéren a bizalmas számítástechnikai virtuális gépek üzembe helyezésének lépéseit](quick-create-marketplace.md)kövesse.


## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Az ingyenes próbaverziós fiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Váltson utólagos elszámolású előfizetésre.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A felső részen válassza az **erőforrás létrehozása** lehetőséget.

1. A **piactér** ablaktáblán válassza a bal oldali **számítás** lehetőséget.

1. Keresse meg és válassza ki a **virtuális gépet**.

    ![Virtuális gép üzembe helyezése](media/quick-create-portal/compute-virtual-machine.png)

1. A virtuális gép kezdőlapján válassza a **Létrehozás** lehetőséget.


## <a name="configure-a-confidential-computing-virtual-machine"></a>Bizalmas számítástechnikai virtuális gép konfigurálása

1. Az **alapvető beállítások** lapon válassza ki az **előfizetést** és az **erőforráscsoportot**.

1. A **virtuális gép neve** mezőbe írja be az új virtuális gép nevét.

1. Írja be vagy válassza ki az alábbi értékeket:

   * **Régió**: válassza ki az Ön számára legmegfelelőbb Azure-régiót.

        > [!NOTE]
        > A bizalmas számítási virtuális gépek csak bizonyos régiókban elérhető speciális hardvereken futnak. A DCsv2-Series virtuális gépek legújabb elérhető régióiért lásd: [elérhető régiók](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

1. Konfigurálja az operációs rendszer azon rendszerképét, amelyet használni szeretne a virtuális géphez.

    * **Válasszon képet**: ebben az oktatóanyagban válassza az Ubuntu 18,04 LTS lehetőséget. Kiválaszthatja a Windows Server 2019, a Windows Server 2016 vagy a és az Ubuntu 16,04 LTS elemet is. Ha ezt választja, ennek az oktatóanyagnak megfelelően lesz átirányítva.
    
    * **A 2. gen lemezképének váltása: a** bizalmas számítási virtuális gépek csak [2. generációs](../virtual-machines/generation-2.md) lemezképeken futnak. Győződjön meg arról, hogy a kiválasztott rendszerkép egy 2. generációs rendszerkép. Kattintson a **speciális** fülre a virtuális gép konfigurálásakor. Görgessen lefelé, amíg meg nem találja a "VM-generáció" feliratú szakaszt. Válassza a Gen 2 elemet, majd térjen vissza az **alapok** lapra.
    

        ![Speciális lap](media/quick-create-portal/advanced-tab-virtual-machine.png)


        ![Virtuálisgép-generáció](media/quick-create-portal/gen2-virtual-machine.png)

    * **Térjen vissza az alapszintű konfigurációhoz**: lépjen vissza az **alapok** lapra a felső navigációs sávon.

1. A méret kiválasztása lehetőség kiválasztásával válasszon ki egy olyan virtuális gépet, amely bizalmas számítási képességekkel **rendelkezik.** A virtuális gép mérete választóban kattintson az **összes szűrő törlése** elemre. Válassza a **szűrő hozzáadása** lehetőséget, válassza a **család** lehetőséget a szűrő típusa mezőben, majd válassza a csak **bizalmas számítás** lehetőséget.

    ![DCsv2-Series virtuális gépek](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > Meg kell jelennie a méretek **DC1s_v2**, **DC2s_v2**, **DC4s_V2** és **DC8_v2**. Ez az egyetlen olyan virtuálisgép-méret, amely jelenleg támogatja a bizalmas számítástechnikai feladatokat. [További információk](virtual-machine-solutions.md).

1. Adja meg a következő információkat:

   * **Hitelesítés típusa**: válassza a **nyilvános SSH-kulcs** lehetőséget, ha Linux rendszerű virtuális gépet hoz létre. 

        > [!NOTE]
        > Választhat, hogy a hitelesítéshez egy SSH-s nyilvános kulcsot vagy egy jelszót használ. Az SSH használata biztonságosabb. Az SSH-kulcs létrehozásával kapcsolatban lásd az [SSH-kulcsok az Azure-ban történő létrehozásának lépéseit Linux és Mac rendszeren Linux rendszerű virtuális gépek számára](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Username (Felhasználónév**): adja meg a virtuális gép rendszergazdájának nevét.

    * **Nyilvános SSH-kulcs**: ha szükséges, adja meg az RSA nyilvános kulcsát.
    
    * **Password (jelszó**): ha szükséges, adja meg a jelszót a hitelesítéshez.

    * **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése** lehetőséget, és válassza az **SSH (22)** és a **http (80)** lehetőséget a **nyilvános bejövő portok kiválasztása** listában. Ha Windows rendszerű virtuális gépet telepít, válassza a **http (80)** és az **RDP (3389)** lehetőséget. Ebben a rövid útmutatóban ez a lépés szükséges a virtuális géphez való kapcsolódáshoz és az Open enklávé SDK konfigurációjának befejezéséhez. 

     ![Bejövő portok](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Módosítsa a **lemezeket a lemezek** lapon.

   * Ha **DC1s_v2**, **DC2s_v2**, **DC4s_V2** virtuális gépet választ, válasszon egy **standard SSD** vagy **prémium SSD** típusú lemezt. 
   * Ha **DC8_v2** virtuális gépet választott, válassza a **standard SSD** lehetőséget a lemez típusaként.

1. Végezze el a kívánt módosításokat a következő lapokon, vagy tartsa meg az alapértelmezett beállításokat.

    * **Hálózat**
    * **Felügyelet**
    * **Vendégkonfiguráció**
    * **Címkék**

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

1. A **felülvizsgálat + létrehozás** ablaktáblán válassza a **Létrehozás** lehetőséget.

> [!NOTE]
> Folytassa a következő szakasszal, és folytassa ezt az oktatóanyagot, ha Linux rendszerű virtuális gépet helyezett üzembe. Ha telepített egy Windows rendszerű virtuális gépet, [kövesse az alábbi lépéseket a Windows rendszerű virtuális géphez való kapcsolódáshoz](../virtual-machines/windows/connect-logon.md) , majd [telepítse az OE SDK-t a Windows rendszerre](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a> Kapcsolódás a Linux rendszerű virtuális géphez

Ha már megnyitott egy BASH-parancssort, csatlakozzon az Azure-beli virtuális géphez az **ssh** paranccsal. A következő parancsban helyettesítse be a Linux rendszerű virtuális gép felhasználónevét és IP-címét a csatlakozáshoz.

```bash
ssh azureadmin@40.55.55.555
```

A virtuális gép nyilvános IP-címét a Azure Portal, a virtuális gép Áttekintés szakaszában találja.

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="IP-cím az Azure Portalon":::

Ha Windows rendszert használ, és nem rendelkezik BASH-rendszerhéjral, telepítsen egy SSH-ügyfelet, például a PuTTY-t.

1. [Töltse le és telepítse a PuTTY-t](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Futtassa a PuTTY-t.

1. A PuTTY konfigurációs képernyőjén adja meg a virtuális gép nyilvános IP-címét.

1. Válassza a **Megnyitás** lehetőséget, majd adja meg a felhasználónevet és a jelszót az üzenetekben.

A Linux rendszerű virtuális gépekhez való csatlakozásról további információt a [Linux rendszerű virtuális gép az Azure-ban a Portal használatával történő létrehozását](../virtual-machines/linux/quick-create-portal.md) ismertető cikkben talál.

> [!NOTE]
> Ha a kiszolgáló gazdagép-kulcsát nem gyorsítótárazza a beállításjegyzékben, a következő lehetőségek közül választhat: Putty biztonsági riasztás. Ha megbízik a gazdagépen, válassza az **Igen** lehetőséget a kulcs a PuTTY gyorsítótárba való felvételéhez és a csatlakozás folytatásához. Ha csak egyszer szeretné csatlakoztatni a csatlakozást, anélkül, hogy a kulcsot a gyorsítótárba venné, válassza a **nem** lehetőséget. Ha nem bízik meg a gazdagépen, kattintson a **Mégse** gombra a kapcsolat megszakításához.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Az Open enklávé SDK (OE SDK) telepítése <a id="Install"></a>

A következő lépésekkel telepítheti az [OE SDK](https://github.com/openenclave/openenclave) -t az DCsv2-Series virtuális gépre, amely Ubuntu 18,04 LTS Gen 2 rendszerképet futtat. 

Ha a virtuális gépe Ubuntu 16,04 LTS Gen 2 rendszeren fut, akkor az [ubuntu 16,04 telepítési utasításait](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)kell követnie.

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. az Intel és a Microsoft APT-Tárházak konfigurálása

```bash
echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main' | sudo tee /etc/apt/sources.list.d/intel-sgx.list
wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add -

echo "deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-7 main" | sudo tee /etc/apt/sources.list.d/llvm-toolchain-bionic-7.list
wget -qO - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -

echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | sudo tee /etc/apt/sources.list.d/msprod.list
wget -qO - https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

#### <a name="2-install-the-intel-sgx-dcap-driver"></a>2. az Intel SGX ENKLÁVÉHOZ DCAP illesztőprogramjának telepítése

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.9/linux/distro/ubuntu18.04-server/sgx_linux_x64_driver_1.36.2.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Használja a legújabb Intel SGX ENKLÁVÉHOZ DCAP illesztőprogramot az [Intel SGX enklávéhoz webhelyéről](https://01.org/intel-software-guard-extensions/downloads).

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Telepítse az Intel-és Open enklávé-csomagokat és-függőségeket

```bash
sudo apt -y install clang-7 libssl-dev gdb libsgx-enclave-common libsgx-enclave-common-dev libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Ez a lépés az az [-Dcap-Client](https://github.com/microsoft/azure-dcap-client) csomagot is telepíti, amely szükséges az Azure-beli távoli igazolás végrehajtásához.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **ellenőrizze az Open ENKLÁVÉ SDK telepítését**

Lásd: [az Open ENKLÁVÉ SDK használata](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) a githubon a telepített SDK ellenőrzéséhez és használatához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. 

Válassza ki a virtuális géphez tartozó erőforráscsoportot, majd válassza a **Törlés** lehetőséget. Erősítse meg az erőforráscsoport nevét az erőforrások törlésének befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy bizalmas számítástechnikai virtuális gépet helyezett üzembe, és telepítette az Open enklávé SDK-t. Az Azure-beli bizalmas számítástechnikai virtuális gépekkel kapcsolatos további információkért lásd: [Virtual Machines-megoldások](virtual-machine-solutions.md). 

Ismerje meg, hogyan hozhat létre bizalmas számítástechnikai alkalmazásokat, ha folytatja az Open enklávé SDK-mintákat a GitHubon. 

> [!div class="nextstepaction"]
> [Open enklávé SDK-minták építése](https://github.com/openenclave/openenclave/blob/master/samples/README.md)