---
title: Privát hely elérésének engedélyezése Azure Functions
description: Megtudhatja, hogyan állíthatja be az Azure-beli virtuális hálózat saját webhelyhez való hozzáférését Azure Functionshoz.
author: craigshoemaker
ms.author: cshoe
ms.service: azure-functions
ms.topic: tutorial
ms.date: 06/17/2020
ms.openlocfilehash: 766ad12daeb6d2763f7ed5fe026cd4a0021eaf33
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937042"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Oktatóanyag: Azure Functions Private site-hozzáférés létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a [Private site-hozzáférést](./functions-networking-options.md#private-endpoint-connections) a Azure functions. A Private site Access használatával megkövetelheti, hogy a függvény kódja csak egy adott virtuális hálózatról induljon el.

A Private site Access olyan helyzetekben hasznos, amikor a Function alkalmazáshoz való hozzáférést egy adott virtuális hálózatra kell korlátozni. A Function alkalmazás például csak egy adott szervezet alkalmazottaira, illetve a megadott virtuális hálózaton belüli szolgáltatásokra (például egy másik Azure-függvényre, Azure-beli virtuális gépre vagy egy AK-fürtre) alkalmazható.

Ha egy functions alkalmazásnak hozzá kell férnie az Azure-erőforrásokhoz a virtuális hálózaton belül, vagy [szolgáltatási végpontokon](../virtual-network/virtual-network-service-endpoints-overview.md)keresztül kell csatlakoznia, akkor a [virtuális hálózat integrációja](./functions-create-vnet.md) szükséges.

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja a saját webhelyhez való hozzáférést a Function alkalmazáshoz:

> [!div class="checklist"]
> * Virtuális gép létrehozása
> * Azure-beli megerősített szolgáltatás létrehozása
> * Azure Functions-alkalmazás létrehozása
> * Virtuális hálózati szolgáltatás végpontjának konfigurálása
> * Azure-függvény létrehozása és üzembe helyezése
> * A függvény meghívása a virtuális hálózaton kívülről és azokon belül

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="topology"></a>Topológia

Az alábbi ábrán a létrehozandó megoldás architektúrája látható:

![Magas szintű architektúra diagram a Private site Access megoldáshoz](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban fontos megérteni az IP-címzést és alhálózatokat. Ebből a cikkből megtudhatja, hogyan kezelheti [a címzési és alhálók alapjait](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Számos további cikk és videó elérhető online állapotban.

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az oktatóanyag első lépése egy új virtuális gép létrehozása egy virtuális hálózaton belül.  A rendszer a virtuális gép használatával fér hozzá a függvényhez, miután korlátozta, hogy hozzáférése csak a virtuális hálózaton belülről legyen elérhető.

1. Válassza az **erőforrás létrehozása** gombot.

1. A Keresés mezőbe írja be a **Windows Server** kifejezést, majd a keresési eredmények között válassza a **Windows Server** elemet.

1. Válassza a **Windows server 2019 Datacenter** lehetőséget a Windows Server beállításai közül, majd kattintson a **Létrehozás** gombra.

1. Az _alapvető_ beállítások lapon használja a virtuális gép beállításait az alábbi táblázatban megadott módon:

    >[!div class="mx-imgBorder"]
    >![Az új Windowsos virtuális gép alapjai lap](./media/functions-create-private-site-access/create-vm-3.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | _Előfizetés_ | Az Ön előfizetése | Az az előfizetés, amelyben az erőforrások létrejöttek. |
    | [_Erőforráscsoport_](../azure-resource-manager/management/overview.md) | myResourceGroup | Válassza ki az oktatóanyaghoz tartozó összes erőforrást tartalmazó erőforráscsoportot.  Ugyanezen erőforráscsoport használatával egyszerűbbé válik az erőforrások törlése, amikor ezzel az Oktatóanyaggal végzett. |
    | _Virtuális gép neve_ | myVM | A virtuális gép nevének egyedinek kell lennie az erőforráscsoporthoz |
    | [_Region_](https://azure.microsoft.com/regions/) | USA USA északi középső régiója | Válasszon egy Önhöz közeli régiót vagy az elérni kívánt funkciók közelében. |
    | _Nyilvános bejövő portok_ | Nincs | Válassza a **nincs** lehetőséget, hogy a virtuális géphez ne legyen bejövő kapcsolat az internetről. A virtuális géphez való távoli hozzáférés az Azure Bastion szolgáltatáson keresztül lesz konfigurálva. |

1. Válassza a _hálózatkezelés_ fület, és válassza az **új létrehozása** lehetőséget az új virtuális hálózat konfigurálásához.

    >[!div class="mx-imgBorder"]
    >![A "hálózatkezelés" fület a "virtuális hálózat" szakaszban Kiemelt "új létrehozás" művelettel ábrázoló képernyőkép.](./media/functions-create-private-site-access/create-vm-networking.png)

1. A _virtuális hálózat létrehozása_ területen használja az alábbi táblázatban található beállításokat a rendszerkép alatt:

    >[!div class="mx-imgBorder"]
    >![Új virtuális hálózat létrehozása az új virtuális GÉPHEZ](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | _Név_ | myResourceGroup – vnet | A virtuális hálózathoz generált alapértelmezett nevet használhatja. |
    | _Címtartomány_ | 10.10.0.0/16 | Használjon egyetlen címtartományt a virtuális hálózathoz. |
    | _Alhálózat neve_ | Oktatóanyag | Az alhálózat neve. |
    | _Címtartomány_ (alhálózat) | 10.10.1.0/24 | Az alhálózat mérete határozza meg, hogy hány csatolót lehet hozzáadni az alhálózathoz. Ezt az alhálózatot a virtuális gép használja. A/24 alhálózat 254 gazdagép-címeket biztosít. |

1. A virtuális hálózat létrehozásához kattintson **az OK gombra** .
1. A _hálózatkezelés_ lapon ellenőrizze, hogy **nincs** -e kiválasztva a _nyilvános IP-cím_ beállítás.
1. Válassza a _felügyelet_ fület, majd a _diagnosztikai Storage-fiók_ területen válassza az **új létrehozása** lehetőséget egy új Storage-fiók létrehozásához.
1. Hagyja meg az _identitás_, az _automatikus leállítás_ és a _biztonsági mentési_ csoportok alapértelmezett értékeit.
1. Válassza az _Áttekintés + létrehozás_ lehetőséget. Az érvényesítés befejezése után válassza a **Létrehozás** lehetőséget. A virtuális gép létrehozási folyamata néhány percet vesz igénybe.

## <a name="configure-azure-bastion"></a>Az Azure Bastion konfigurálása

Az [Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) egy teljes körűen felügyelt Azure-szolgáltatás, amely biztonságos RDP-és SSH-hozzáférést biztosít a virtuális gépekhez közvetlenül a Azure Portal. Az Azure Bastion szolgáltatás használatával nem kell konfigurálnia az RDP-hozzáféréshez kapcsolódó hálózati beállításokat.

1. A portálon válassza a **Hozzáadás** lehetőséget az erőforráscsoport nézet tetején.
1. A keresőmezőbe írja be a következőt: **Bastion**.
1. A keresési eredmények között válassza a **Bastion** lehetőséget.
1. A **Létrehozás** gombra kattintva megkezdheti egy új Azure Bastion-erőforrás létrehozásának folyamatát. A _Virtual Network (virtuális hálózat_ ) szakaszban egy hibaüzenet jelenik meg, mivel még nincs AzureBastionSubnet alhálózat. Az alhálózat a következő lépésekben jön létre. Használja az alábbi táblázatban található beállításokat a rendszerkép alatt:

    >[!div class="mx-imgBorder"]
    >![Az Azure Bastion létrehozásának megkezdése](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | _Név_ | myBastion | Az új megerősített erőforrás neve |
    | _Régió_ | USA északi középső régiója | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) vagy a funkciókhoz tartozó egyéb szolgáltatások közelében. |
    | _Virtuális hálózat_ | myResourceGroup – vnet | Az a virtuális hálózat, amelyben a megerősített erőforrás létre lesz hozva |
    | _Alhálózat_ | AzureBastionSubnet | Annak a virtuális hálózatnak az alhálózata, amelyhez az új megerősített gazdagép erőforrása telepítve lesz. Létre kell hoznia egy alhálózatot a **AzureBastionSubnet** név érték használatával. Ez az érték lehetővé teszi, hogy az Azure tudja, melyik alhálózaton telepítse a megerősített erőforrásokat a következőre:. Legalább **/27** vagy nagyobb (/27,/26 stb.) alhálózatot kell használnia. |

    > [!NOTE]
    > Az Azure megerősített erőforrás létrehozásával kapcsolatos részletes útmutatóért tekintse meg az [Azure Bastion-gazdagép létrehozása](../bastion/tutorial-create-host-portal.md) oktatóanyagot.

1. Hozzon létre egy alhálózatot, amelyben az Azure kiépítheti az Azure-beli megerősített gazdagépet. Az **alhálózati konfiguráció kezelése** lehetőség megnyit egy új ablaktáblát, ahol megadhat egy új alhálózatot.  Válassza a **+ alhálózat** lehetőséget egy új alhálózat létrehozásához.
1. Az alhálózatnak a **AzureBastionSubnet** névvel kell rendelkeznie, és az alhálózati előtagnak legalább **/27-** nek kell lennie.  Az alhálózat létrehozásához kattintson **az OK gombra** .

    >[!div class="mx-imgBorder"]
    >![Alhálózat létrehozása az Azure Bastion-gazdagéphez](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

1. A _bástya létrehozása_ lapon válassza ki az újonnan létrehozott **AzureBastionSubnet** az elérhető alhálózatok listájából.

    >[!div class="mx-imgBorder"]
    >![Egy adott alhálózattal rendelkező Azure Bastion-gazdagép létrehozása](./media/functions-create-private-site-access/create-bastion-basics-2.png)

1. Válassza a **felülvizsgálat & létrehozás** lehetőséget. Az érvényesítés befejeződése után válassza a **Létrehozás** lehetőséget. Az Azure Bastion-erőforrás létrehozása eltarthat néhány percig.

## <a name="create-an-azure-functions-app"></a>Azure Functions-alkalmazás létrehozása

A következő lépés egy Function-alkalmazás létrehozása az Azure-ban a [felhasználási terv](consumption-plan.md)használatával. Az oktatóanyag későbbi részében üzembe helyezi a függvény kódját erre az erőforrásra.

1. A portálon válassza a **Hozzáadás** lehetőséget az erőforráscsoport nézet tetején.
1. Válassza ki a **számítási > függvényalkalmazás**
1. Az _alapismeretek_ szakaszban használja az alábbi táblázatban megadott Function App-beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | _Erőforráscsoport_ | myResourceGroup | Válassza ki az oktatóanyaghoz tartozó összes erőforrást tartalmazó erőforráscsoportot.  Ha a Function alkalmazáshoz ugyanazt az erőforráscsoportot használja, és a virtuális gép megkönnyíti az erőforrások törlését, amikor ezzel az Oktatóanyaggal végzett. |
    | _Függvényalkalmazás neve_ | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: a – z (kis-és nagybetűk megkülönböztetése), 0-9 és –. |
    | _Közzététel_ | Kód | Kódfájlok közzétételét teszi lehetővé egy Docker-tárolóban. |
    | _Futtatókörnyezet verme_ | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. |
    | _Régió_ | USA északi középső régiója | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) vagy a funkciókhoz tartozó egyéb szolgáltatások közelében. |

    Válassza a **következő: >üzemeltetése** gombot.
1. A _hoszting_ szakaszban válassza ki a megfelelő _Storage-fiókot_, _operációs rendszert_ és _tervet_ a következő táblázatban leírtak szerint.

    | Beállítás      | Ajánlott érték  | Leírás      |
    | ------------ | ---------------- | ---------------- |
    | _Storage-fiók_ | Globálisan egyedi név | Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [Storage-fiókra vonatkozó követelményeknek](storage-considerations.md#storage-account-requirements). |
    | _Operációs rendszer_ | Előnyben részesített operációs rendszer | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. |
    | _Terv_ | Használat | A [üzemeltetési terv](./functions-scale.md) azt diktálja, hogy a függvény alkalmazás hogyan méretezhető, és az erőforrások elérhetők legyenek az egyes példányok számára. |
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget az alkalmazás-konfiguráció kiválasztásának áttekintéséhez.
1. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

## <a name="configure-access-restrictions"></a>Hozzáférési korlátozások konfigurálása

A következő lépés a [hozzáférési korlátozások](../app-service/app-service-ip-restrictions.md) konfigurálása annak biztosítására, hogy csak a virtuális hálózaton található erőforrások hívhatják meg a függvényt.

A [privát hely](functions-networking-options.md#private-endpoint-connections) elérését az Azure Virtual Network [szolgáltatás végpontjának](../virtual-network/virtual-network-service-endpoints-overview.md) létrehozásával engedélyezheti a Function alkalmazás és a megadott virtuális hálózat között. A hozzáférési korlátozások a szolgáltatási végpontokon keresztül valósíthatók meg. A szolgáltatási végpontok biztosítják, hogy csak a megadott virtuális hálózaton belülről származó forgalom férhessenek hozzá a kijelölt erőforráshoz. Ebben az esetben a kijelölt erőforrás az Azure-függvény.

1. A Function alkalmazásban válassza a **hálózatkezelés** hivatkozást a _Beállítások_ szakasz fejlécében.
1. A _hálózatkezelés_ lap a kiindulási pont, amely az Azure bejárati ajtót, a Azure CDNt és a hozzáférés korlátozásait is beállítja.
1. Válassza a **hozzáférési korlátozások konfigurálása** lehetőséget a privát hely elérésének konfigurálásához.
1. A _hozzáférési korlátozások_ lapon csak az alapértelmezett korlátozás jelenik meg. Az alapértelmezett érték nem korlátozza a Function alkalmazáshoz való hozzáférést.  Válassza a **szabály hozzáadása** lehetőséget a privát hely hozzáférés-korlátozási konfigurációjának létrehozásához.
1. A _hozzáférés-korlátozás hozzáadása_ panelen adja meg az új szabály _nevét_, _prioritását_ és _leírását_ .
1. Válassza ki a **Virtual Network** elemet a _típus_ legördülő listából, majd válassza ki a korábban létrehozott virtuális hálózatot, majd válassza ki az **oktatóanyag** -alhálózatot. 
    > [!NOTE]
    > A szolgáltatás végpontjának engedélyezése több percet is igénybe vehet.
1. A _hozzáférési korlátozások_ lap most már azt mutatja, hogy van új korlátozás. Eltarthat néhány másodpercig, amíg a _végpont állapota_ le lesz tiltva a kiépítés útján, hogy engedélyezve legyen.

    >[!IMPORTANT]
    > Minden Function alkalmazáshoz egy [speciális eszköz-(kudu-) hely](../app-service/app-service-ip-restrictions.md#restrict-access-to-an-scm-site) tartozik, amely a Function app üzemelő példányának kezelésére szolgál. A webhely a következőhöz hasonló URL-címről érhető el: `<FUNCTION_APP_NAME>.scm.azurewebsites.net` . A hozzáférési korlátozások engedélyezése a kudu-helyen megakadályozza a projekt kódjának helyi fejlesztői munkaállomásról történő telepítését, majd az üzembe helyezés elvégzéséhez a virtuális hálózaton belül ügynökre van szükség.

## <a name="access-the-functions-app"></a>A functions alkalmazás elérése

1. Térjen vissza az előzőleg létrehozott Function alkalmazáshoz.  Az _Áttekintés_ szakaszban másolja az URL-címet.

    >[!div class="mx-imgBorder"]
    >![A függvény alkalmazás URL-címének beolvasása](./media/functions-create-private-site-access/access-function-overview.png)

    Ha a Function app alkalmazást a virtuális hálózatán kívüli számítógépről próbálja elérni, egy HTTP 403-lapot fog kapni, amely azt jelzi, hogy a hozzáférés tiltott.
1. Térjen vissza az erőforráscsoporthoz, és válassza ki a korábban létrehozott virtuális gépet. Ahhoz, hogy a virtuális gépről hozzáférhessen a webhelyhez, az Azure megerősített szolgáltatáson keresztül kell csatlakoznia a virtuális géphez.
1. Válassza a **kapcsolat** lehetőséget, majd válassza a **Bastion** lehetőséget.
1. Adja meg a virtuális géphez való bejelentkezéshez szükséges felhasználónevet és jelszót.
1. Válassza a **Kapcsolódás** lehetőséget. Egy új böngészőablak jelenik meg, amely lehetővé teszi a virtuális gép interakcióját.
A webhely a virtuális GÉPEN található webböngészőből érhető el, mert a virtuális gép a virtuális hálózaton keresztül éri el a helyet.  Amíg a hely csak a kijelölt virtuális hálózatról érhető el, a nyilvános DNS-bejegyzés marad.

## <a name="create-a-function"></a>Függvény létrehozása

Az oktatóanyag következő lépése egy HTTP-triggerű Azure-függvény létrehozása. Ha a függvényt HTTP GET vagy POST metódussal hívja meg, a "Hello, {Name}" választ kell eredményeznie.  

1. A Azure Functions-alkalmazás létrehozásához és üzembe helyezéséhez kövesse az alábbi rövid útmutatók egyikét.

    * [Visual Studio Code](./create-first-function-vs-code-csharp.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Parancssor](./create-first-function-cli-csharp.md)
    * [Maven (Java)](./create-first-function-cli-java.md?tabs=bash,browser)

1. Azure Functions projekt közzétételekor válassza ki az oktatóanyag során korábban létrehozott Function app-erőforrást.
1. Ellenőrizze, hogy a függvény telepítve van-e.

    >[!div class="mx-imgBorder"]
    >![Üzembe helyezett függvény a függvények listájában](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>A függvény közvetlen meghívása

1. A függvényhez való hozzáférés teszteléséhez át kell másolnia a függvény URL-címét. Válassza ki az üzembe helyezett függvényt, majd kattintson a **függvény URL-címének beolvasása** elemre. Ezután a **Másolás** gombra kattintva másolja az URL-címet a vágólapra.

    >[!div class="mx-imgBorder"]
    >![A függvény URL-címének másolása](./media/functions-create-private-site-access/get-function-url.png)

1. Illessze be az URL-címet egy webböngészőbe. Ha most megpróbál hozzáférni a Function alkalmazáshoz egy, a virtuális hálózaton kívüli számítógépről, akkor az alkalmazáshoz való hozzáférést jelző HTTP 403-válasz tiltott.

## <a name="invoke-the-function-from-the-virtual-network"></a>A függvény meghívása a virtuális hálózatról

A függvény elérése egy webböngészőn keresztül (az Azure Bastion szolgáltatás használatával) a konfigurált virtuális GÉPEN, a virtuális hálózatban sikerrel jár.

>[!div class="mx-imgBorder"]
>![Az Azure-függvény elérése az Azure Bastion használatával](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések


> [!div class="nextstepaction"]
> [További tudnivalók a függvények hálózati beállításairól](./functions-networking-options.md)