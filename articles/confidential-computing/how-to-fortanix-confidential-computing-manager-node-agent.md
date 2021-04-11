---
title: Alkalmazás futtatása a Fortanix bizalmas számítástechnikai kezelőjével
description: Megtudhatja, hogyan alakíthatja át a Fortanix bizalmas számítástechnikai kezelőjét a tárolóban lévő képek átalakításához
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 03/24/2021
ms.author: JenCook
ms.openlocfilehash: 91a3f0a38d1182e445eba39bf31092be17f9a1fd
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111614"
---
# <a name="how-to-run-an-application-with-fortanix-confidential-computing-manager"></a>Útmutató: alkalmazás futtatása a Fortanix bizalmas számítástechnikai kezelőjével

Indítsa el az alkalmazást az Azure bizalmas számítástechnikai szolgáltatásban a [Fortanix bizalmas számítástechnikai kezelő](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) és a [Fortanix Node Agent ügynök](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) használatával a [Fortanix](https://www.fortanix.com/)-ból.


A Fortanix egy külső gyártótól származó szoftvergyártó, amely az Azure-infrastruktúrára épülő termékekkel és szolgáltatásokkal rendelkezik. Más külső szolgáltatók is hasonló bizalmas számítástechnikai szolgáltatásokat nyújtanak az Azure-ban.

> [!Note]
> A dokumentumban hivatkozott termékek nem a Microsoft felügyelete alá tartoznak. A Microsoft ezt az információt kizárólag kényelmi célokra nyújtja, és a nem Microsoft-termékekre való hivatkozás nem a Microsoft jóváhagyását jelenti.

Ebből az oktatóanyagból megtudhatja, hogyan alakíthatja át az alkalmazás rendszerképét egy bizalmas, számítási védelemmel ellátott képre. Ez a környezet [Fortanix](https://www.fortanix.com/) szoftvert használ, amelyet az Azure DCsv2-Series Intel SGX enklávéhoz-kompatibilis virtuális gépek működtetnek. Ez a megoldás a kritikus biztonsági házirendeket, például az identitás-ellenőrzést és az adathozzáférés-vezérlést hangolja össze.

A Fortanix-specifikus támogatáshoz csatlakozzon a [Fortanix Slack-közösséghez](https://fortanix.com/community/) , és használja a csatornát `#enclavemanager` .

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik a Fortanix bizalmas számítástechnikai Manager-fiókkal, akkor a Kezdés előtt [regisztráljon](https://ccm.fortanix.com/auth/sign-up) .
1. Privát [Docker](https://docs.docker.com/) -beállításjegyzék a konvertált alkalmazások lemezképének leküldéséhez.
1. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Az ingyenes próbaverziós fiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Váltson utólagos elszámolású előfizetésre.

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Alkalmazás hozzáadása a Fortanix bizalmas számítástechnikai kezelőjéhez

1. Jelentkezzen be a [Fortanix bizalmas számítástechnikai kezelőjébe (FORTANIX CCM)](https://ccm.fortanix.com).
1. Lépjen a **fiókok** lapra, és válassza a **fiók hozzáadása** lehetőséget egy új fiók létrehozásához.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-account-new.png" alt-text="Képernyőkép, amely bemutatja, hogyan lehet létrehozni egy fiókot.":::

1. A fiók létrehozása után nyomja meg a **kijelölés lehetőséget** az újonnan létrehozott fiók kiválasztásához. Most már elkezdheti a számítási csomópontok regisztrálását és az alkalmazások létrehozását.
1. Alkalmazás hozzáadásához kattintson a **+ alkalmazás** gombra. Ebben a példában a lombik-kiszolgáló enklávé OS-alkalmazást vesszük fel.

1. Válassza az enklávé operációs rendszer alkalmazás **Hozzáadás** gombját.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-enclave-application.png" alt-text="Képernyőkép, amely bemutatja, hogyan adhat hozzá egy alkalmazást.":::

   > [!NOTE]
   > Ez az oktatóanyag csak az enklávé OS-alkalmazások hozzáadását ismerteti. [További](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager) információ az EDP rozsda-alkalmazások Fortanix bizalmas számítástechnikai kezelőhöz való bevezetéséről.

1. Ebben az oktatóanyagban a Fortanix Docker-beállításjegyzékét fogjuk használni a minta alkalmazáshoz. Adja meg a részleteket az alábbi információk alapján. A kimeneti rendszerkép megtartásához használja a privát Docker-beállításjegyzéket.

    - **Alkalmazás neve**: Python Application Server
    - **Leírás**: Python-lombik kiszolgálója
    - **Bemeneti rendszerkép neve**: fortanix/Python-lombik
    - **Kimeneti rendszerkép neve**: fortanx-Private/Python-lombik-SGX enklávéhoz
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Memória mérete**: 1 GB
    - **Szálak száma**: 128

    Nem *kötelező*: futtassa az alkalmazást.
    - **Docker hub**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **Alkalmazás**: fortanix/Python-lombik

      Futtassa az alábbi parancsot:

      ```bash
         sudo docker run fortanix/python-flask
      ```

1. Tanúsítvány hozzáadása. Adja meg az adatokat az alábbi adatok alapján, majd válassza a **Next (tovább**) gombot:
    - **Tartomány**: SajátPr. domain. Dom
    - **Típus**: a bizalmas számítástechnikai kezelő által kiállított tanúsítvány
    - **Kulcs elérési útja**:/appkey.PEM
    - **Kulcs típusa**: RSA
    - **Tanúsítvány elérési útja**:/appcert.PEM
    - **RSA-kulcs mérete**: 2048 bit

## <a name="create-an-image"></a>Rendszerkép létrehozása

A Fortanix CCM-rendszerkép egy alkalmazás szoftveres kiadása vagy verziója. Mindegyik rendszerkép egy enklávé-kivonattal (MRENCLAVE) van társítva.

1. A **rendszerkép hozzáadása** lapon adja meg a **kimeneti rendszerkép nevének** **beállításjegyzékbeli hitelesítő adatait** . Ezek a hitelesítő adatok a privát Docker-beállításjegyzék elérésére szolgálnak, ahol a rendszer leküldi a rendszerképet.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-image.png" alt-text="Képernyőkép, amely bemutatja, hogyan hozhat létre egy rendszerképet.":::

1. Adja meg a rendszerkép címkéjét, és válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-tag.png" alt-text="Képernyőkép, amely bemutatja, hogyan adhat hozzá egy címkét.":::

## <a name="domain-and-image-allowlist"></a>Tartomány és rendszerkép engedélyezési

Egy alkalmazás, amelynek tartománya hozzá van adva a engedélyezési, egy TLS-tanúsítványt kap a Fortanix bizalmas számítástechnikai kezelőjétől. Hasonlóképpen, ha egy alkalmazás a konvertált rendszerképből fut, akkor megpróbál kapcsolatba lépni a Fortanix bizalmas számítástechnikai kezelőjével. Az alkalmazás ezután kérni fogja a TLS-tanúsítványt.

Váltson a **feladatok** lapra a bal oldalon, és hagyja jóvá a függőben lévő kéréseket a tartomány és a rendszerkép engedélyezéséhez.

## <a name="enroll-compute-node-agent-in-azure"></a>Számítási csomópont-ügynök regisztrálása az Azure-ban

### <a name="generate-and-copy-join-token"></a>Csatlakozási jogkivonat előállítása és másolása

A Fortanix bizalmas számítástechnikai kezelőjében létre kell hoznia egy jogkivonatot. Ez a token lehetővé teszi, hogy az Azure-beli számítási csomópont hitelesítse magát. Ezt a tokent meg kell adnia az Azure-beli virtuális géphez.

1. A felügyeleti konzolon válassza a **+ csomópont regisztrálása** gombot.
1. Az illesztési jogkivonat létrehozásához válassza a **jogkivonat** előállítása lehetőséget. Másolja a tokent.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Csomópontok regisztrálása a Fortanix-csomóponti Ügynökbe az Azure Marketplace-en

A Fortanix-csomópontok létrehozásakor a rendszer egy virtuális gépet, egy hálózati adaptert, egy virtuális hálózatot, egy hálózati biztonsági csoportot és egy nyilvános IP-címet helyez üzembe az Azure-erőforráscsoporthoz. Azure-előfizetését óránként számlázjuk a virtuális géphez. A Fortanix csomópont-ügynök létrehozása előtt tekintse át az Azure [virtuális gépek díjszabási lapját](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a DCsv2 sorozathoz. Ha nincs használatban, törölje az Azure-erőforrásokat.

1. Nyissa meg az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) -t, és jelentkezzen be az Azure-beli hitelesítő adataival.
1. A keresősáv mezőbe írja be a **Fortanix bizalmas számítástechnikai csomópont-ügynök** kifejezést. Válassza ki azt az alkalmazást, amely a **Fortanix bizalmas számítástechnikai csomópont-ügynök** nevű keresőmezőbe mutat, hogy az ajánlat kezdőlapján lépjen.

   ![Keresés a piactéren](media/how-to-fortanix-confidential-computing-manager-node-agent/search-fortanix-marketplace.png)
1. Válassza a **Letöltés most** lehetőséget, adja meg az adatokat, ha szükséges, majd kattintson a **Folytatás** gombra. A rendszer átirányítja a Azure Portalra.
1. Válassza a **Létrehozás** lehetőséget a Fortanix bizalmas számítástechnikai csomópont-ügynök üzembe helyezési lapjának megadásához.
1. Ezen a lapon megadhatja a virtuális gép üzembe helyezéséhez szükséges adatokat. Pontosabban, ez a virtuális gép egy DCsv2-Series Intel SGX ENKLÁVÉHOZ-kompatibilis virtuális gép az Azure-ban, amelyen telepítve van a Fortanix Node Agent szoftver. A csomópont-ügynök lehetővé teszi, hogy az átalakított rendszerkép biztonságosan fusson az Azure-beli Intel SGX ENKLÁVÉHOZ-csomópontokon.  Válassza ki azt az **előfizetést** és **erőforráscsoportot** , ahol telepíteni szeretné a virtuális gépet és a kapcsolódó erőforrásokat.

   > [!NOTE]
   > DCsv2-Series virtuális gépek Azure-ban való telepítésekor megkötések vannak. Előfordulhat, hogy további magokra vonatkozó kvótát kell igényelnie. További információt az Azure-beli [virtuális gépeken található bizalmas számítástechnikai megoldásokról szóló](./virtual-machine-solutions.md) témakörben olvashat.

1. Válasszon a rendelkezésre álló régiók közül.
1. A **csomópont neve** mezőbe írja be a virtuális gép nevét.
1. Adjon meg egy felhasználónevet és egy jelszót (vagy SSH-kulcsot) a virtuális géphez való hitelesítéshez.
1. Hagyja meg az operációsrendszer-lemez alapértelmezett méretét 200-ként, és válassza ki a virtuálisgép-méretet (Standard_DC4s_v2 elegendő ehhez az oktatóanyaghoz).
1. Illessze be a korábban létrehozott tokent a **JOIN tokenben**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/deploy-fortanix-node-agent-protocol.png" alt-text="Képernyőkép, amely bemutatja, hogyan helyezhet üzembe egy erőforrást.":::

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. Győződjön meg arról, hogy az ellenőrzés megfelel, majd válassza a **Létrehozás** lehetőséget. Az összes erőforrás telepítésekor a számítási csomópont regisztrálva van a Fortanix bizalmas számítástechnikai kezelőjében.

## <a name="run-the-application-image-on-the-compute-node"></a>Az alkalmazás rendszerképének futtatása a számítási csomóponton

Futtassa az alkalmazást az alábbi parancs végrehajtásával. Győződjön meg arról, hogy a csomópont IP-címét, a portot és a konvertált lemezképfájlt bemenetként módosítja az adott alkalmazáshoz.

Ebben az oktatóanyagban a végrehajtandó parancs a következő:

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

Ahol:

- a *52.152.206.164* a csomóponti ügynök gazdagépének IP-címe
- *9092* a csomópont-ügynök által figyelt port
- *fortanix-Private/Python-lombik-SGX enklávéhoz* a konvertált alkalmazás, amely a Fortanix bizalmas számítástechnikai vezető webportál **lemezképek táblájának** **lemezkép neve** oszlopában található lemezképek lapon, a kép neve oszlopban található.

## <a name="verify-and-monitor-the-running-application"></a>A futó alkalmazás ellenőrzése és figyelése

1. Térjen vissza a [Fortanix bizalmas számítástechnikai kezelőjéhez](https://ccm.fortanix.com/console).
1. Győződjön meg arról, hogy azon a **fiókon** belül dolgozik, amelyben regisztrálta a csomópontot.
1. Nyissa meg a **felügyeleti konzolt** a bal oldali navigációs ablaktábla felső ikonjának kiválasztásával.
1. Válassza az **alkalmazás** fület.
1. Ellenőrizze, hogy van-e futó alkalmazás társítva egy társított számítási csomóponttal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és a kapcsolódó erőforrásokat. Az erőforráscsoport törlésével törli a konvertált rendszerképhez társított csomópontok regisztrációját.

Válassza ki a virtuális géphez tartozó erőforráscsoportot, majd válassza a **Törlés** lehetőséget. Erősítse meg az erőforráscsoport nevét az erőforrások törlésének befejezéséhez.

A létrehozott Fortanix bizalmas számítástechnikai kezelő fiók törléséhez nyissa meg a Fortanix bizalmas számítástechnikai kezelő [fiókok lapját](https://ccm.fortanix.com/accounts) . Vigye a kurzort a törölni kívánt fiók fölé. Válassza a jobb felső sarokban található függőleges fekete pontokat, és válassza a **fiók törlése** lehetőséget.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/delete-account.png" alt-text="A fiók törlését bemutató képernyőkép.":::

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a Fortanix-eszközök használatával alakíthatja át az alkalmazás rendszerképét, hogy az a bizalmas számítástechnikai virtuális gépen fusson. Az Azure-beli bizalmas számítástechnikai virtuális gépekkel kapcsolatos további információkért lásd: [Virtual Machines-megoldások](virtual-machine-solutions.md).

Az Azure bizalmas számítástechnikai kínálatával kapcsolatos további információkért lásd: az [Azure bizalmas számítástechnika áttekintése](overview.md).

Megtudhatja, hogyan hajthat végre hasonló feladatokat más, harmadik féltől származó ajánlatokkal az Azure-ban, például a [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) és a [Scone](https://sconedocs.github.io).