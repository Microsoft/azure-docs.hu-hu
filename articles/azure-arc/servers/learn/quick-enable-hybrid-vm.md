---
title: Hibrid gép összekapcsolása az Azure arc-kompatibilis kiszolgálókkal
description: Ismerje meg, hogyan csatlakozhat és regisztrálhat hibrid gépet az Azure arc-kompatibilis kiszolgálókon.
ms.topic: quickstart
ms.date: 12/15/2020
ms.openlocfilehash: c52b8d1f7098a7a2a88a9770a3b768b7fea31775
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646826"
---
# <a name="quickstart-connect-hybrid-machines-with-azure-arc-enabled-servers"></a>Gyors útmutató: hibrid gépek összekapcsolása az Azure arc-kompatibilis kiszolgálókkal

Az [Azure arc-kompatibilis kiszolgálók](../overview.md) lehetővé teszik a helyszíni, Edge és többfelhős környezetekben üzemeltetett Windows és Linux rendszerű gépek felügyeletét és szabályozását. Ebben a rövid útmutatóban üzembe helyezi és konfigurálja a csatlakoztatott gépi ügynököt az Azure-on kívül üzemeltetett Windows-vagy Linux-gépen az arc-kompatibilis kiszolgálók általi felügyelethez.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Az arc-kompatibilis kiszolgálók hibrid csatlakoztatott gépi ügynökének üzembe helyezéséhez rendszergazdai engedélyekkel kell rendelkeznie a gépen az ügynök telepítéséhez és konfigurálásához. Linux rendszeren a legfelső szintű fiókkal és a Windows rendszeren egy olyan fiókkal, amely a helyi Rendszergazdák csoport tagja.

* Az első lépések előtt tekintse át az ügynök [előfeltételeit](../agent-overview.md#prerequisites) , és ellenőrizze a következőket:

    * A célszámítógép támogatott [operációs rendszert](../agent-overview.md#supported-operating-systems)futtat.

    * A fiókja megkapja a [szükséges Azure-szerepkörök](../agent-overview.md#required-permissions)hozzárendelését.

    * Ha a gép tűzfalon vagy proxykiszolgálón keresztül csatlakozik az interneten keresztüli kommunikációhoz, győződjön meg arról, hogy a [felsorolt](../agent-overview.md#networking-configuration) URL-címek nincsenek letiltva.

    * Az Azure arc-kompatibilis kiszolgálók csak az [itt](../overview.md#supported-regions)megadott régiókat támogatják.

> [!WARNING]
> A Linux-állomásnév vagy a Windows-számítógép neve nem használhatja a név egyik foglalt kifejezését vagy védjegyét, ellenkező esetben a csatlakoztatott gép Azure-ba való regisztrálására tett kísérlet sikertelen lesz. A fenntartott szavak listáját a [fenntartott erőforrás-hibák feloldása](../../../azure-resource-manager/templates/error-reserved-resource-name.md) című témakörben tekintheti meg.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Azure-erőforrás-szolgáltatók regisztrálása

Az Azure arc használatára képes kiszolgálók a szolgáltatás használatához a következő Azure-erőforrás-szolgáltatótól függenek:

* Microsoft. HybridCompute
* Microsoft. GuestConfiguration

Regisztrálja őket a következő parancsok használatával:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Telepítési parancsfájl létrehozása

Az Azure arc letöltésének, telepítésének és létrehozásának automatizálására szolgáló parancsfájl a Azure Portal érhető el. A folyamat befejezéséhez tegye a következőket:

1. Indítsa el az Azure arc szolgáltatást a Azure Portal a **minden szolgáltatás** elemre kattintva, majd a kiszolgálók keresése és kiválasztása **– Azure arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Az ív használatára képes kiszolgálók keresése az összes szolgáltatásban" border="false":::

1. A **kiszolgálók – Azure arc** lapon válassza a **Hozzáadás** lehetőséget a bal felső sarokban.

1. A **módszer kiválasztása** lapon jelölje be a **kiszolgálók hozzáadása interaktív parancsfájl** csempével lehetőséget, majd válassza a **parancsfájl létrehozása** lehetőséget.

1. A **parancsfájl létrehozása** lapon válassza ki azt az előfizetést és erőforráscsoportot, ahol a gépet az Azure-ban szeretné felügyelni. Válasszon ki egy Azure-helyet, ahol a rendszer a számítógép metaadatait tárolja. Ez a hely lehet azonos vagy eltérő, mint az erőforráscsoport helye.

1. Az **Előfeltételek** lapon tekintse át az információkat, majd kattintson a **Tovább gombra: erőforrás részletei**.

1. Az **erőforrás részletei** lapon adja meg a következőket:

    1. Az **erőforráscsoport** legördülő listában válassza ki azt az erőforráscsoportot, amelyből a gépet felügyelni kívánja.
    1. A **régió** legördülő listában válassza ki az Azure-régiót a kiszolgálók metaadatainak tárolásához.
    1. Az **operációs rendszer** legördülő listában válassza ki azt az operációs rendszert, amelyre a parancsfájlt konfigurálni kívánja.
    1. Ha a gép egy proxykiszolgálón keresztül kommunikál az internethez, adja meg a proxykiszolgáló IP-címét, vagy azt a nevet és portszámot, amelyet a gép használni fog a proxykiszolgálóhoz való kommunikációhoz. Adja meg az értéket a formátumban `http://<proxyURL>:<proxyport>` .
    1. Kattintson a **Tovább gombra: címkék**.

1. A **címkék** lapon tekintse át a javasolt alapértelmezett **fizikai hely címkéit** , és adjon meg egy értéket, vagy adjon meg egy vagy több **Egyéni címkét** a szabványok támogatásához.

1. Válassza **a tovább lehetőséget: parancsfájl letöltése és futtatása**.

1. A **letöltési és futtatási parancsfájl** lapon tekintse át az összegzési információkat, majd kattintson a **Letöltés** gombra. Ha továbbra is módosításokat kell végeznie, válassza az **előző** lehetőséget.

## <a name="install-the-agent-using-the-script"></a>Az ügynök telepítése a parancsfájl használatával

### <a name="windows-agent"></a>Windows-ügynök

1. Jelentkezzen be a kiszolgálóra.

1. Nyisson meg egy emelt szintű 64-bites PowerShell-parancssort.

1. Váltson arra a mappára vagy megosztásra, amelyre a parancsfájlt másolta, majd futtassa azt a kiszolgálón a parancsfájl futtatásával `./OnboardingScript.ps1` .

### <a name="linux-agent"></a>Linux-ügynök

1. Az alábbi parancs futtatásával telepítheti a Linux-ügynököt azon a célszámítógépen, amely közvetlenül tud kommunikálni az Azure-ban:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Ha a célszámítógép egy proxykiszolgálón keresztül kommunikál, futtassa a következő parancsot:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Az Azure Arc csatlakozásának ellenőrzése

Miután telepítette az ügynököt, és konfigurálta az Azure arc-kompatibilis kiszolgálókhoz való csatlakozáshoz, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakozott-e. Megtekintheti a gépet a [Azure Portalban](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="A számítógép sikeres csatlakoztatása" border="false":::

## <a name="next-steps"></a>Következő lépések

Most, hogy engedélyezte a Linux vagy Windows rendszerű hibrid gépet, és sikeresen csatlakozott a szolgáltatáshoz, készen áll a Azure Policy az Azure-ban való megfelelőség megismerésére.

Ha meg szeretné tudni, hogyan azonosíthatók azok az Azure arc-kompatibilis kiszolgálók, amelyeken nincs telepítve a Log Analytics ügynök, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához](tutorial-assign-policy-portal.md)
