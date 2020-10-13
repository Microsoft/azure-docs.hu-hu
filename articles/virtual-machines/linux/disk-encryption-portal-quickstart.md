---
title: Linux rendszerű virtuális gép létrehozása és titkosítása az Azure Portallal
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és titkosíthat Linux rendszerű virtuális gépeket a Azure Portal használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 2a5588940d4efacc08d89b72bde4433d725a4994
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978407"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Rövid útmutató: virtuális gép létrehozása és titkosítása a Azure Portal

Az Azure-beli virtuális gépek (VM-ek) létrehozhatók az Azure Portal segítségével. Az Azure Portal egy böngészőalapú felhasználói felület a virtuális gépek és a társított erőforrások létrehozására. Ebben a rövid útmutatóban az Ubuntu 18,04 LTS rendszert futtató linuxos virtuális gép (VM) üzembe helyezéséhez a Azure Portal fogja használni a titkosítási kulcsok tárolásához és a virtuális gép titkosításához.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában az **Erőforrás létrehozása** gombra.
1. Az új lap népszerű területén válassza az **Ubuntu Server 18,04 LTS**elemet.
1. Az alapvető beállítások lap projekt részletei területén ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.
1. Az "erőforráscsoport" területen válassza az **új létrehozása**lehetőséget. A név mezőbe írja be a *myResourceGroup* nevet, majd kattintson az **OK gombra**.
1. A **virtuális gép neve**mezőbe írja be a következőt: *MyVM*.
1. A régió területen válassza az USA *keleti* **régióját**.
1. Győződjön meg arról, hogy a **méret** *szabványos D2s v3*.
1. A **rendszergazdai fiók**területen válassza a *jelszó* lehetőséget **hitelesítési típusként**. Adja meg a felhasználónevet és a jelszót.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-linux-vm-creation.png" alt-text="Linuxos virtuális gép létrehozása képernyő&quot;:::

    > [!WARNING]
    > A &quot;lemezek&quot; lapon a **lemez beállításai**területen található &quot;titkosítási típus&quot; mező szerepel. Ez a mező a [Managed Disks](../managed-disks-overview.md) + CMK titkosítási beállításainak megadására szolgál, **nem** a Azure Disk Encryption.
    >
    > A félreértések elkerülése érdekében javasoljuk, hogy az oktatóanyag elvégzése során teljes mértékben kihagyja a *lemezek* fület.

1. Válassza a &quot;felügyelet&quot; fület, és ellenőrizze, hogy rendelkezik-e diagnosztikai Storage-fiókkal. Ha nincs Storage-fiókja, válassza az *új létrehozása*elemet, nevezze el a Storage-fiók *myStorageAccount*, és válassza az &quot;OK" lehetőséget.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="Linuxos virtuális gép létrehozása képernyő&quot;:::

    > [!WARNING]
    > A &quot;lemezek&quot; lapon a **lemez beállításai**területen található &quot;titkosítási típus&quot; mező szerepel. Ez a mező a [Managed Disks](../managed-disks-overview.md) + CMK titkosítási beállításainak megadására szolgál, **nem** a Azure Disk Encryption.
    >
    > A félreértések elkerülése érdekében javasoljuk, hogy az oktatóanyag elvégzése során teljes mértékben kihagyja a *lemezek* fület.

1. Válassza a &quot;felügyelet&quot; fület, és ellenőrizze, hogy rendelkezik-e diagnosztikai Storage-fiókkal. Ha nincs Storage-fiókja, válassza az *új létrehozása*elemet, nevezze el a Storage-fiók *myStorageAccount*, és válassza az &quot;OK" gombra.
1. A **Virtuális gép létrehozása** lapon láthatja a létrehozandó virtuális gép részleteit. Ha készen áll, kattintson a **Létrehozás** gombra.

A virtuális gép üzembe helyezése eltarthat néhány percig. Az üzembe helyezés végeztével lépjen tovább a következő szakaszra.

## <a name="encrypt-the-virtual-machine"></a>A virtuális gép titkosítása

1. Ha a virtuális gép üzembe helyezése befejeződött, válassza az **Ugrás az erőforráshoz**lehetőséget.
1. A bal oldali oldalsávon válassza a **lemezek**lehetőséget.
1. A felső sávon válassza a **További beállítások** lehetőséget.
1. A **titkosítási beállítások**  >  **lemezeken a titkosításhoz**válassza az **operációs rendszer és az adatlemezek**lehetőséget.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="Linuxos virtuális gép létrehozása képernyő&quot;:::

    > [!WARNING]
    > A &quot;lemezek&quot; lapon a **lemez beállításai**területen található &quot;titkosítási típus&quot; mező szerepel. Ez a mező a [Managed Disks](../managed-disks-overview.md) + CMK titkosítási beállításainak megadására szolgál, **nem** a Azure Disk Encryption.
    >
    > A félreértések elkerülése érdekében javasoljuk, hogy az oktatóanyag elvégzése során teljes mértékben kihagyja a *lemezek* fület.

1. Válassza a &quot;felügyelet&quot; fület, és ellenőrizze, hogy rendelkezik-e diagnosztikai Storage-fiókkal. Ha nincs Storage-fiókja, válassza az *új létrehozása*elemet, nevezze el a Storage-fiók *myStorageAccount*, és válassza az &quot;OK":::

1. A **titkosítási beállítások**területen válassza **a Key Vault és a kulcs kiválasztása a titkosításhoz**lehetőséget.
1. A **válasszon kulcsot Azure Key Vault** képernyőn válassza az **új létrehozása**lehetőséget.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="Linuxos virtuális gép létrehozása képernyő&quot;:::

    > [!WARNING]
    > A &quot;lemezek&quot; lapon a **lemez beállításai**területen található &quot;titkosítási típus&quot; mező szerepel. Ez a mező a [Managed Disks](../managed-disks-overview.md) + CMK titkosítási beállításainak megadására szolgál, **nem** a Azure Disk Encryption.
    >
    > A félreértések elkerülése érdekében javasoljuk, hogy az oktatóanyag elvégzése során teljes mértékben kihagyja a *lemezek* fület.

1. Válassza a &quot;felügyelet&quot; fület, és ellenőrizze, hogy rendelkezik-e diagnosztikai Storage-fiókkal. Ha nincs Storage-fiókja, válassza az *új létrehozása*elemet, nevezze el a Storage-fiók *myStorageAccount*, és válassza az &quot;OK":::

1. A **Key Vault és a Key**billentyű bal oldalán válassza **a kattintás elemet a kulcs kiválasztásához**.
1. A **válasszon kulcsot a Azure Key Vault közül**, a **Key Vault** mezőben válassza az **új létrehozása**lehetőséget.
1. A **Key Vault létrehozása** képernyőn győződjön meg arról, hogy az erőforráscsoport *myResourceGroup*, és adja meg a kulcstartó nevét.  Minden Azure-beli Key vaultnak egyedi névvel kell rendelkeznie.
1. A **hozzáférési házirendek** lapon jelölje be a **Azure Disk Encryption a kötet titkosítása** jelölőnégyzetet.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="Linuxos virtuális gép létrehozása képernyő&quot;:::

    > [!WARNING]
    > A &quot;lemezek&quot; lapon a **lemez beállításai**területen található &quot;titkosítási típus&quot; mező szerepel. Ez a mező a [Managed Disks](../managed-disks-overview.md) + CMK titkosítási beállításainak megadására szolgál, **nem** a Azure Disk Encryption.
    >
    > A félreértések elkerülése érdekében javasoljuk, hogy az oktatóanyag elvégzése során teljes mértékben kihagyja a *lemezek* fület.

1. Válassza a &quot;felügyelet&quot; fület, és ellenőrizze, hogy rendelkezik-e diagnosztikai Storage-fiókkal. Ha nincs Storage-fiókja, válassza az *új létrehozása*elemet, nevezze el a Storage-fiók *myStorageAccount*, és válassza az &quot;OK":::

1. Válassza a **Felülvizsgálat + létrehozás** lehetőséget.  
1. Miután a kulcstartó érvényesítése sikeres volt, válassza a **Létrehozás**lehetőséget. Ezzel a beállítással visszatérhet a **Select kulcsra Azure Key Vault** képernyőről.
1. Hagyja üresen a **kulcsmező** mezőt, és válassza a **kiválasztás**lehetőséget.
1. A titkosítás képernyő felső részén kattintson a **Mentés**gombra. A felugró ablak figyelmezteti, hogy a virtuális gép újraindul. Kattintson a **Yes** (Igen) gombra.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, kattintson a Törlés elemre, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, amely engedélyezve lett a titkosítási kulcsokhoz, létrehozott egy virtuális gépet, és engedélyezte a virtuális gép titkosítását.  

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintése](disk-encryption-overview.md)