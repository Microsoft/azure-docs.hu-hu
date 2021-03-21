---
title: Igényelhető virtuális gépek létrehozása és kezelése a Azure DevTest Labsban | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá a Azure Portal egy igény szerinti virtuális gépet a Azure DevTest Labshoz, és hogyan lehet a következő folyamatokat felvenni a virtuális gépek igénylésére/lekérésére.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1fe949d7104fe6437087e77c403cc0d921ebd025
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270979"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Igényelhető virtuális gépek létrehozása és kezelése Azure DevTest Labs
Az igény szerinti virtuális gépeket hasonló módon veheti fel a laborba úgy, hogy hogyan [vegyen fel egy szabványos virtuális gépet](devtest-lab-add-vm.md) – egy olyan *alapból* , amely [Egyéni rendszerkép](devtest-lab-create-template.md), [képlet](devtest-lab-manage-formulas.md)vagy [piactér-rendszerkép](devtest-lab-configure-marketplace-images.md). Ez az oktatóanyag végigvezeti a Azure Portal használatával, hogy a DevTest Labs szolgáltatásban egy, az igénylést használó virtuális gépet felvegyen egy laborba, és megjeleníti a felhasználó által a virtuális gép igénylésére és visszaigénylésére vonatkozó folyamatokat.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>A lekéréses virtuális gép tesztkörnyezetben való hozzáadásának lépései Azure DevTest Labs
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
3. Válassza a **Létrehozás** lehetőséget a megadott virtuális gép laborhoz való hozzáadásához.

   A labor lapon megjelenik a virtuális gép létrehozásának állapota – elsőként a **Létrehozás** után, majd a virtuális gép elindítása után **futtatva** .

> [!NOTE]
> Ha [Azure Resource Manager-sablonokon](devtest-lab-create-environment-from-arm.md)keresztül helyez üzembe tesztkörnyezet-alapú virtuális gépeket, a tulajdonságok szakaszban a **allowClaim** tulajdonság True értékre állításával hozhat létre igénylésre képes virtuális gépeket.


## <a name="using-a-claimable-vm"></a>Igény szerinti virtuális gép használata

A felhasználók az alábbi lépések egyikével igényelhetnek bármely virtuális gépet a "igényelhető virtuális gépek" listájáról:

* A labor "áttekintés" paneljének alján található "igényelhető virtuális gépek" listájában kattintson a jobb gombbal a listában szereplő egyik virtuális gépre, és válassza a **jogcím-gép** lehetőséget.

  ![Igényeljen egy konkrét igényelhető virtuális gépet.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Az "áttekintés" panel tetején válassza a **jogcím bármelyik** lehetőséget. A rendszer egy véletlenszerűen kiválasztott virtuális gépet rendel a jogcímek listájához.

  ![Igényeljen bármely igényelhető virtuális gépet.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Miután egy felhasználó megkövetelte a virtuális gépet, a DevTest Labs elindítja a gépet, és áthelyezi a labor felhasználó "saját virtuális gépek" listájára. Ez azt jelenti, hogy a labor felhasználó ekkor már rendelkezik tulajdonosi jogosultságokkal ezen a gépen. A lépéshez szükséges idő a kezdési időponttól és a jogcím-esemény során végrehajtott egyéb egyéni művelettől függően változhat. Az igénylést követően a gép már nem érhető el a kérelmezhető készletben.  

## <a name="unclaim-a-vm"></a>Virtuális gép lekérése

Ha egy felhasználó egy igényelt virtuális gép használatával fejeződött be, és elérhetővé szeretné tenni valaki másnak, a következő lépések egyikével visszaadhatják a kért virtuális gépet a jogcímek listájához:

- A "saját virtuális gépek" listájában kattintson a jobb gombbal a listában szereplő egyik virtuális gépre, vagy válassza ki a három pontot (...), és válassza a **jogcímek** megszüntetése lehetőséget.

  ![A virtuális gép listáján szereplő virtuális gép lekérése.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- A "saját virtuális gépek" listájában válasszon ki egy virtuális gépet a felügyeleti ablaktáblájának megnyitásához, majd a felső menüsorból válassza a kikapcsolás **elemet.**

  ![Virtuális gép lekérése a virtuális gép felügyeleti paneljén.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Ha a felhasználó nem igényel virtuális gépet, akkor már nem rendelkezik tulajdonosi engedélyekkel az adott labor virtuális géphez, és az elérhetővé válik bármely más labor-felhasználó számára abban az állapotban, hogy a készletbe tért vissza. 

### <a name="transferring-the-data-disk"></a>Az adatlemez átvitele
Ha egy igényelt virtuális gépnek van egy adatlemeze, és a felhasználó nem állítja azt, akkor az adatlemez marad a virtuális géppel. Ha egy másik felhasználó ezt követően azt állítja be, hogy a virtuális gép, az új felhasználó az adatlemezt és a virtuális gépet is állítja be.

Ez az úgynevezett "adatlemez átvitele". Az adatlemez ezután elérhetővé válik az **adatlemezek** az új felhasználó listájában, amelyekkel kezelhetők.

![Adatlemezek visszaigénylése](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Következő lépések
* A létrehozást követően csatlakozhat a virtuális géphez a felügyeleti paneljén a **Kapcsolódás** lehetőség kiválasztásával.
* Ismerkedjen meg a [DevTest Labs Azure Resource Manager a rövid útmutató sablonjának galériájában](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
