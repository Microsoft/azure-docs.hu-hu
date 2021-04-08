---
title: Rövid útmutató – Azure bizalmas számítástechnikai virtuális gép létrehozása a piactéren
description: Ismerkedjen meg az üzemelő példányokkal, hogy megtanulja, hogyan hozhat létre gyorsan egy bizalmas számítástechnikai virtuális gépet a piactéren.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: aba23b67574fb74b7cd571dc5d4642bb8b991b93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566634"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>Rövid útmutató: Azure bizalmas számítástechnikai virtuális gép üzembe helyezése a piactéren

Ismerkedjen meg az Azure bizalmas számítástechnikai szolgáltatásával az Azure Marketplace használatával, amely az Intel SGX ENKLÁVÉHOZ által támogatott virtuális gépet (VM) hoz létre. Ezután telepíti a Open enklávé szoftverfejlesztői készletét (SDK) a fejlesztési környezet beállításához. 

Ez az oktatóanyag akkor ajánlott, ha gyorsan el szeretné kezdeni a bizalmas számítástechnikai virtuális gépek üzembe helyezését. A virtuális gépek speciális hardveren futnak, és a kívánt konfigurációs bemenetek megkövetelésével kell futniuk. Az ebben a rövid útmutatóban ismertetett Marketplace-ajánlat megkönnyíti az üzembe helyezést a felhasználói bevitel korlátozásával.

Ha további egyéni konfigurációval szeretne egy bizalmas számítási virtuális gépet üzembe helyezni, kövesse a [Azure Portal bizalmas számítási virtuális gép üzembe helyezésének lépéseit](quick-create-portal.md).

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Az ingyenes próbaverziós fiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Váltson utólagos elszámolású előfizetésre.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A felső részen írja be az **Azure bizalmas számítástechnika** kifejezést a keresősávba.

1. A **piactér** szakaszban válassza az **Azure bizalmas számítástechnika (virtuális gép)** lehetőséget. 

    ![Piactér kiválasztása](media/quick-create-marketplace/portal-search-marketplace.png)    

1. Az Azure bizalmas számítási üzembe helyezési lapján válassza a **Létrehozás** lehetőséget.
 

## <a name="configure-your-virtual-machine"></a>A virtuális gép konfigurálása

1. Az **alapvető beállítások** lapon válassza ki az **előfizetést** és az **erőforráscsoportot**. Az erőforráscsoport üresen kell lennie ahhoz, hogy virtuális gépet helyezzen üzembe a sablonból.

1. Írja be vagy válassza ki az alábbi értékeket:

   * **Régió**: válassza ki az Ön számára legmegfelelőbb Azure-régiót.

        > [!NOTE]
        > A bizalmas számítási virtuális gépek csak bizonyos régiókban elérhető speciális hardvereken futnak. A DCsv2-Series virtuális gépek legújabb elérhető régióiért lásd: [elérhető régiók](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).
    
    * **Válassza a rendszerkép** elemet: válasszon ki egy képet. Ha el szeretné végezni ezt az oktatóanyagot, válassza az Ubuntu 18,04 (2. gen) lehetőséget. Ellenkező esetben az alábbi megfelelő lépésekben lesz átirányítva. 

    * **Virtuális gép neve** mezőbe írja be az új virtuális gép nevét.

    * **Hitelesítés típusa**: válassza a **nyilvános SSH-kulcs** lehetőséget, ha Linux rendszerű virtuális gépet hoz létre. 

         > [!NOTE]
         > Választhat, hogy a hitelesítéshez egy SSH-s nyilvános kulcsot vagy egy jelszót használ. Az SSH használata biztonságosabb. Az SSH-kulcs létrehozásával kapcsolatban lásd az [SSH-kulcsok az Azure-ban történő létrehozásának lépéseit Linux és Mac rendszeren Linux rendszerű virtuális gépek számára](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Username (Felhasználónév**): adja meg a virtuális gép rendszergazdájának nevét.

    * **Nyilvános SSH-kulcs**: ha szükséges, adja meg az RSA nyilvános kulcsát.
    
    * **Password (jelszó**): ha szükséges, adja meg a jelszót a hitelesítéshez.
 
1. A képernyő alján kattintson a **következő: virtuális gép beállításai** gombra.

    > [!IMPORTANT]
    > Várjon, amíg a lap frissül. A "bizalmas számítástechnikai DCsv2 sorozatú virtuális gépek csak korlátozott számú régióban érhetők el." üzenet *jelenik meg.* Ha az üzenet továbbra is fennáll, térjen vissza az előző oldalra, és válasszon ki egy elérhető DCsv2-Series régiót.

1. A **módosítási méret** beállításnál válasszon egy olyan virtuális gépet, amely a méret választóban bizalmas számítási képességekkel rendelkezik. 

    > [!TIP]
    > Meg kell jelennie a méretek **DC1s_v2**, **DC2s_v2**, **DC4s_V2** és **DC8_v2**. Ez az egyetlen olyan virtuálisgép-méret, amely jelenleg támogatja a bizalmas számítástechnikai feladatokat. [További információk](virtual-machine-solutions.md).

1. **Operációsrendszer-lemez típusa** esetén válassza ki a lemez típusát.

1. **Virtual Network** esetén hozzon létre egy újat, vagy válasszon egy meglévő erőforrásból.

1. **Alhálózat** esetén hozzon létre egy újat, vagy válasszon egy meglévő erőforrásból.

1. A **nyilvános bejövő portok kiválasztása** lehetőségnél válassza az **SSH (Linux)/RDP (Windows)** lehetőséget. Ebben a rövid útmutatóban ez a lépés szükséges a virtuális géphez való kapcsolódáshoz és az Open enklávé SDK konfigurációjának befejezéséhez. 

1. A **rendszerindítási diagnosztika** esetében hagyja letiltva ezt a rövid útmutatót. 

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

![IP-cím az Azure Portalon](media/quick-create-portal/public-ip-virtual-machine.png)

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
wget https://download.01.org/intel-sgx/sgx-dcap/1.4/linux/distro/ubuntuServer18.04/sgx_linux_x64_driver_1.21.bin -O sgx_linux_x64_driver.bin
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
