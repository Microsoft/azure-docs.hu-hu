---
title: Virtuális gép hozzáadása laborhoz Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá egy virtuális gépet a Azure DevTest Labshoz a Azure Portal használatával. Kiválaszthat egy olyan alapot, amely vagy egyéni rendszerkép vagy képlet.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07efcf93b963009494021b90e6c730c4615301ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91297623"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Virtuális gép hozzáadása laborhoz Azure DevTest Labs
Ha már [létrehozta az első virtuális gépet](tutorial-create-custom-lab.md#add-a-vm-to-the-lab), akkor valószínű, hogy egy előre betöltött [Piactéri rendszerképből](devtest-lab-configure-marketplace-images.md)származik. Most, ha további virtuális gépeket szeretne hozzáadni a laborhoz, kiválaszthat egy [Egyéni rendszerképet](devtest-lab-create-template.md) vagy egy [képletet](devtest-lab-manage-formulas.md)tartalmazó *alapot* is. Ez az oktatóanyag végigvezeti a virtuális gép DevTest Labs-beli laborban való hozzáadásának Azure Portal használatával.

A cikk azt is bemutatja, hogyan kezelheti a virtuális gép összetevőit a laborban.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>A virtuális gép tesztkörnyezetben való hozzáadásának lépései Azure DevTest Labs
1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza a **minden szolgáltatás** lehetőséget, majd válassza a **DevTest Labs** elemet a **DEVOPS** szakaszban. Ha a * (csillag) lehetőséget választja a **DevTest Labs** mellett a **DEVOPS** szakaszban. Ez a művelet hozzáadja a **DevTest Labs** szolgáltatást a bal oldali navigációs menühöz, hogy a következő alkalommal könnyen elérhető legyen. Ezután kiválaszthatja a bal oldali navigációs menü **DevTest Labs** elemét.

    ![Minden szolgáltatás – válassza a DevTest Labs lehetőséget](./media/devtest-lab-create-lab/all-services-select.png)
1. A Labs listából válassza ki azt a labort, amelyben létre szeretné hozni a virtuális gépet.
2. A labor **Áttekintés** lapján válassza a **+ Hozzáadás** lehetőséget.

    ![Virtuális gép hozzáadása gomb](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. A **kiinduló** kiválasztása lapon válasszon ki egy Piactéri rendszerképet a virtuális géphez.
1. A **virtuális gép** lap **alapbeállítások** lapján hajtsa végre a következő műveleteket:
    1. A virtuális gép neve szövegmezőben adjon meg egy nevet a **virtuális gépnek** . A szövegmező előre ki van töltve egy egyedi, automatikusan létrehozott névvel. A név az e-mail-címen belüli felhasználónévnek felel meg, amelyet egy egyedi 3 számjegyű szám követ. Ezzel a funkcióval megtakaríthatja a gép nevét, és minden egyes számítógép létrehozásakor begépelheti azt. Ha kívánja, felülbírálhatja ezt az automatikusan kitöltött mezőt az Ön által választott névvel. A virtuális gép automatikusan kitöltött nevének felülbírálásához írjon be egy nevet a virtuálisgép- **név** szövegmezőbe.
    2. Adjon meg egy **felhasználónevet** , amely rendszergazdai jogosultságokat kap a virtuális gépen. A gép **felhasználóneve** előre ki van töltve egy egyedi, automatikusan létrehozott névvel. A név az e-mail-címen belüli felhasználónévnek felel meg. Ezzel a funkcióval időt takaríthat meg, hogy minden új gép létrehozásakor eldöntse a felhasználónevet. Ezt követően felülbírálhatja ezt az automatikusan kitöltött mezőt az Ön által választott felhasználónévvel, ha szeretné. A felhasználónévhez tartozó automatikusan kitöltött érték felülbírálásához írjon be egy értéket a **Felhasználónév** szövegmezőbe. Ez a felhasználó **rendszergazdai** jogosultságokat kap a virtuális gépen.
    3. Ha az első virtuális gépet a laborban hozza létre, adjon meg egy **jelszót** a felhasználó számára. Ha ezt a jelszót alapértelmezett jelszóként szeretné menteni a laborhoz társított Azure Key vaultban, válassza a **Mentés alapértelmezett jelszóként** lehetőséget. Az alapértelmezett jelszót a Key Vault menti a Name: **VmPassword**. Amikor további virtuális gépeket próbál létrehozni a tesztkörnyezetben, a rendszer automatikusan kiválasztja a **jelszót** a **VmPassword** . Az érték felülbírálásához törölje a jelet a **mentett titkos kód használata** jelölőnégyzetből, és adjon meg egy jelszót.

        ![Képernyőkép, amely a "Alapbeállítások" elemet és a "felhasználói beállítások" megadott értékeket jeleníti meg.](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        Először a Key vaultban is mentheti a titkos kulcsokat, majd használhatja azt a laborban található virtuális gép létrehozásakor. További információ: a [titkos kulcsok tárolása a kulcstartóban](devtest-lab-store-secrets-in-key-vault.md). A Key vaultban tárolt jelszó használatához válassza a **mentett titkos kód használata** lehetőséget, és adjon meg egy olyan kulcsot, amely megfelel a titkos kulcsnak (jelszó).
    4. A **További beállítások** szakaszban válassza a **méret módosítása** lehetőséget. Válassza ki az előre definiált elemek egyikét, amely meghatározza a processzor magjait, a RAM méretét és a létrehozandó virtuális gép merevlemezének méretét.
    5. Válassza az összetevők **hozzáadása vagy eltávolítása** lehetőséget. Válassza ki és konfigurálja azokat az összetevőket, amelyeket hozzá szeretne adni az alaprendszerképhez.
    **Megjegyzés:** Ha most ismerkedik a DevTest Labs szolgáltatással vagy az összetevők konfigurálásával, tekintse meg a [meglévő összetevő hozzáadása virtuális géphez](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) című szakaszt, és ha elkészült, térjen vissza ide.
2. Váltson a felül található **Speciális beállítások** lapra, és végezze el a következő műveleteket:
    1. Ha módosítani szeretné azt a virtuális hálózatot, amelyen a virtuális gép található, válassza a **VNet módosítása** lehetőséget.
    2. Az alhálózat módosításához válassza az **alhálózat módosítása** lehetőséget.
    3. Annak megadása, hogy a virtuális gép IP-címe **nyilvános, magánjellegű vagy megosztott**.
    4. A virtuális gép automatikus törléséhez határozza meg a **lejárat dátumát és időpontját**.
    5. Ahhoz, hogy a virtuális gép számára elérhető legyen egy labor-felhasználó, válassza az **Igen** lehetőséget a **gép igény** szerinti beállításának megköveteléséhez.
    6. Adja meg a tesztkörnyezet felhasználói számára elérhetővé tenni kívánt **virtuális gép példányainak** számát.

        ![Alap kiválasztása](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Válassza a **Létrehozás** lehetőséget a megadott virtuális gép laborhoz való hozzáadásához.

   A labor lapon megjelenik a virtuális gép létrehozásának állapota – elsőként a **Létrehozás** után, majd a virtuális gép elindítása után **futtatva** .

    ![Virtuális gép létrehozásának állapota](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Meglévő összetevő hozzáadása egy virtuális géphez
A virtuális gépek létrehozása során meglévő összetevőket is hozzáadhat. Az egyes laborok a nyilvános DevTest Labs összetevő-tárházból, valamint a létrehozott és a saját összetevő-tárházba felvett összetevőkből álló összetevőket tartalmaznak.

* Azure DevTest Labs *az* összetevők lehetővé teszik a virtuális gép üzembe helyezésekor végrehajtandó *műveletek* megadását, például a Windows PowerShell-parancsfájlok futtatását, a bash-parancsok futtatását és a szoftverek telepítését.
* Az összetevő- *Paraméterek* lehetővé teszik az adott forgatókönyvhöz tartozó összetevők testreszabását

Az összetevők létrehozásával kapcsolatos információkért tekintse meg a cikket, [amelyből megtudhatja, hogyan hozhat létre saját összetevőket a DevTest Labs használatával](devtest-lab-artifact-author.md).

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
1. A laborok listájából válassza ki azt a labort, amely a használni kívánt virtuális gépet tartalmazza.
1. Válassza **a saját virtuális gépek** lehetőséget.
1. Válassza ki a kívánt virtuális gépet.
1. Válassza az összetevők **kezelése** lehetőséget.
1. Válassza az összetevők **alkalmazása** lehetőséget.
1. Az összetevők **alkalmazása** ablaktáblán válassza ki azt az összetevőt, amelyet hozzá szeretne adni a virtuális géphez.
1. Az összetevő **hozzáadása** panelen adja meg a szükséges paramétereket, valamint az esetlegesen szükséges paramétereket.
1. A **Hozzáadás** gombra kattintva vegye fel az összetevőt, és térjen vissza az összetevők **alkalmazása** ablaktáblára.
1. Folytassa az összetevők hozzáadását a virtuális géphez szükséges módon.
1. Az összetevők hozzáadása után [megváltoztathatja az összetevők futtatásának sorrendjét](#change-the-order-in-which-artifacts-are-run). Visszatérhet az összetevők [megtekintéséhez vagy módosításához](#view-or-modify-an-artifact)is.
1. Ha elkészült az összetevők hozzáadásával, kattintson az **alkalmaz** gombra.

## <a name="change-the-order-in-which-artifacts-are-run"></a>Az összetevők futtatási sorrendjének módosítása
Alapértelmezés szerint az összetevők műveletei a virtuális géphez való hozzáadásuk sorrendjében hajthatók végre.
Az alábbi lépések bemutatják, hogyan módosíthatja az összetevők futtatásának sorrendjét.

1. Az összetevők **alkalmazása** ablaktábla felső részén válassza ki a virtuális géphez hozzáadott összetevők számát jelző hivatkozást.

    ![A virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. A **kiválasztott** összetevők panelen húzza az összetevőket a kívánt sorrendbe. **Megjegyzés:** Ha nem sikerül áthúzni az összetevőt, győződjön meg róla, hogy az összetevő bal oldaláról húzza az elemet.
1. Kattintson az **OK** gombra, amikor végzett.

## <a name="view-or-modify-an-artifact"></a>Összetevő megtekintése és módosítása
Az alábbi lépések bemutatják, hogyan lehet megtekinteni vagy módosítani egy összetevő paramétereit:

1. Az összetevők **alkalmazása** ablaktábla felső részén válassza ki a virtuális géphez hozzáadott összetevők számát jelző hivatkozást.

    ![A virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. A **kiválasztott** összetevők ablaktáblán válassza ki a megtekinteni vagy szerkeszteni kívánt összetevőt.
1. Az összetevő **hozzáadása** panelen végezze el a szükséges módosításokat, majd kattintson az **OK** gombra az összetevők **hozzáadása** ablaktábla bezárásához.
1. A **kijelölt** összetevők ablaktábla bezárásához kattintson **az OK gombra** .

## <a name="save-azure-resource-manager-template"></a>Azure Resource Manager sablon mentése
Az Azure Resource Manager-sablonok deklaratív módszert biztosítanak egy ismételhető telepítés megadására.
A következő lépések azt ismertetik, hogyan menthetők a létrehozandó virtuális géphez Azure Resource Manager sablon.
A mentés után a Azure Resource Manager sablonnal [telepítheti az új virtuális gépeket Azure PowerShell](../azure-resource-manager/templates/overview.md)használatával.

1. A **virtuális gép** ablaktáblán válassza a **Azure Resource Manager sablon megtekintése** lehetőséget.
2. A **Azure Resource Manager sablon megtekintése** panelen válassza ki a sablon szövegét.
3. A kijelölt szöveg másolása a vágólapra.
4. Kattintson az **OK** gombra a **nézet Azure Resource Manager sablon paneljének** bezárásához.
5. Nyisson meg egy szövegszerkesztőt.
6. Illessze be a sablon szövegét a vágólapra.
7. Mentse a fájlt későbbi használatra.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Következő lépések
* A virtuális gép létrehozása után a virtuális gép paneljén a **Kapcsolódás** lehetőségre kattintva csatlakozhat a virtuális géphez.
* Ismerje meg, hogyan [hozhat létre egyéni összetevőket a DevTest Labs virtuális géphez](devtest-lab-artifact-author.md).
* Ismerkedjen meg a [DevTest Labs Azure Resource Manager a rövid útmutató sablonjának galériájában](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
