---
title: OPERÁCIÓSRENDSZER-lemezek titkosítása az ügyfél által felügyelt kulcsok használatával Azure DevTest Labs
description: Megtudhatja, hogyan titkosíthatja az operációs rendszer (OS) lemezeit az ügyfél által felügyelt kulcsokkal Azure DevTest Labsban.
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 26ef4ff1529483da9956c6dcc43807af0ffd6463
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96341207"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Operációs rendszer (OS) lemezek titkosítása az ügyfél által felügyelt kulcsok használatával Azure DevTest Labs
A kiszolgálóoldali titkosítás (SSE) védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az SSE alapértelmezés szerint automatikusan titkosítja a felügyelt lemezeken tárolt adatait az Azure-ban (az operációs rendszer és az adatlemezek), amikor a felhőben megmarad. További információ az Azure-beli [lemezek titkosításáról](../virtual-machines/disk-encryption.md) . 

A DevTest Labs szolgáltatásban a labor részeként létrehozott operációsrendszer-lemezek és adatlemezek a platform által felügyelt kulcsok használatával lesznek titkosítva. A labor tulajdonosaként azonban úgy is dönthet, hogy a saját kulcsait használva titkosítja a tesztkörnyezet virtuális gépe operációsrendszer-lemezeit. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, megadhatja az **ügyfél által felügyelt kulcsot** , amelyet a rendszer a Lab operációsrendszer-lemezeken tárolt adattitkosításhoz használ. Az ügyfél által felügyelt kulcsokkal és az egyéb felügyelt lemezes titkosítási típusokkal kapcsolatos további információkért lásd: [ügyfél által felügyelt kulcsok](../virtual-machines/disk-encryption.md#customer-managed-keys). Lásd még: [korlátozások az ügyfél által felügyelt kulcsok használatával](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions).

> [!NOTE]
> - Az ügyfél által felügyelt kulccsal jelenleg a lemezes titkosítás csak a DevTest Labs operációsrendszer-lemezei esetében támogatott. 
> 
> - A beállítás az újonnan létrehozott operációsrendszer-lemezekre vonatkozik a laborban. Ha úgy dönt, hogy egy bizonyos ponton módosítja a lemez titkosítását, a tesztkörnyezetben lévő régebbi lemezek továbbra is titkosítva maradnak az előző lemezes titkosítási készlet használatával. 

A következő szakasz bemutatja, hogyan állíthatja be a labor tulajdonosa a titkosítást az ügyfél által felügyelt kulcs használatával.

## <a name="pre-requisites"></a>Előfeltételek

1. Ha nem rendelkezik lemezes titkosítási készlettel, akkor az alábbi cikk alapján [állítson be egy Key Vault és egy lemezes titkosítási készletet](../virtual-machines/disks-enable-customer-managed-keys-portal.md). Jegyezze fel a következő követelményeket a lemez titkosítási készletéhez: 

    - A lemez titkosítási készletének ugyanabban a **régióban és előfizetésben kell lennie, mint a labornak**. 
    - Győződjön meg arról, hogy (Lab tulajdonos) rendelkezik **-e legalább olvasó szintű hozzáféréssel** ahhoz a lemezes titkosítási készlethez, amelyet a labor operációsrendszer-lemezek titkosításához fog használni. 
1. A 8/1/2020-es előtti laborok esetében a labor tulajdonosának biztosítania kell, hogy a labor rendszerhez rendelt identitás engedélyezve legyen. Ehhez **a** labor tulajdonosa megtekintheti a labort, kattintson a **konfiguráció és házirendek** elemre, kattintson az **identitás (előzetes verzió)** panelre, majd a rendszerhez rendelt identitás **állapotának** módosítása elemre, és kattintson a **Mentés** gombra. Az 8/1/2020 labor rendszerhez rendelt identitása után létrehozott új laborok alapértelmezés szerint engedélyezve lesznek. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="Felügyelt kulcsok":::
1. Ahhoz, hogy a labor kezelni tudja az összes Lab operációsrendszer-lemez titkosítását, a labor tulajdonosának explicit módon meg kell adnia a tesztkörnyezet **rendszerhez rendelt identitás** -olvasó szerepkörét a lemez titkosítási készletén, valamint a virtuális gépi közreműködő szerepkört a mögöttes Azure-előfizetésben. A labor tulajdonosa a következő lépések végrehajtásával teheti meg:

   
    1. Győződjön meg arról, hogy tagja a [felhasználói hozzáférés rendszergazdai szerepkörének](../role-based-access-control/built-in-roles.md#user-access-administrator) az Azure-előfizetési szinten, így kezelheti az Azure-erőforrásokhoz való felhasználói hozzáférést. 
    1. A **lemez titkosítási készlete** lapon a bal oldali menüben válassza a **hozzáférés-vezérlés (iam)** lehetőséget. 
    1. Válassza a **+ Hozzáadás** lehetőséget az eszköztáron, majd válassza **a szerepkör-hozzárendelés hozzáadása** lehetőséget.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="Szerepkör-kezelés hozzáadása – menü":::
    1. A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki az **olvasó** szerepkört vagy egy olyan szerepkört, amely nagyobb hozzáférést tesz lehetővé. 
    1. Írja be annak a labornak a nevét, amelyhez a rendszer a lemez titkosítási készletét használni fogja, majd válassza ki a labor nevét (a laborhoz a rendszerszintű identitást) a legördülő listából. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="A tesztkörnyezet rendszerfelügyelt identitásának kiválasztása":::        
    1. Válassza az eszköztár **Save** (Mentés) elemét. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="Szerepkör-hozzárendelés mentése":::
3. Adja hozzá a labor **rendszerhez rendelt identitását** a **virtuális gép közreműködői** szerepkörhöz az **előfizetés**  ->  **-hozzáférés-vezérlés (iam)** lapon. A lépések hasonlóak az előző lépések lépéseihez. 

    
    1. Navigáljon a Azure Portal **előfizetés** lapjára. 
    1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget. 
    1. Válassza a **+ Hozzáadás** lehetőséget az eszköztáron, majd kattintson **a szerepkör-hozzárendelés hozzáadása** lehetőségre. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="Előfizetés – > hozzáférés-vezérlés (IAM) lap":::
    1. A **szerepkör-hozzárendelés hozzáadása** lapon válassza a **virtuális gép közreműködője** lehetőséget a szerepkörhöz.
    1. Írja be a labor nevét, majd a legördülő listából válassza ki a labor **nevét** (a tesztkörnyezet rendszerhez rendelt identitása). 
    1. Válassza az eszköztár **Save** (Mentés) elemét. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>A Lab operációsrendszer-lemezek titkosítása ügyfél által felügyelt kulccsal 

1. A Azure Portalban a labor kezdőlapján válassza a **konfiguráció és házirendek** elemet a bal oldali menüben. 
1. A **konfiguráció és házirendek** lapon válassza a **lemezek (előzetes verzió)** lehetőséget a **titkosítás** szakaszban. Alapértelmezés szerint a **titkosítási típus** a platform által **felügyelt kulccsal történő titkosításra** van beállítva.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="A konfiguráció és a házirendek lap lemezek lapja":::
1. A **titkosítás típusa** beállításnál válassza a legördülő lista **ügyfél által felügyelt kulcsával történő titkosítás** lehetőséget. 
1. A **lemezes titkosítási készlet** esetében válassza ki a korábban létrehozott lemezes titkosítási készletet. Ez ugyanaz a lemezes titkosítási készlet, amelyet a labor rendszerhez rendelt identitása tud elérni.
1. Válassza az eszköztár **Save** (Mentés) elemét. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="Titkosítás engedélyezése az ügyfél által felügyelt kulccsal":::
1. A következő szöveget tartalmazó üzenetablakban: *Ez a beállítás a tesztkörnyezetben újonnan létrehozott gépekre lesz érvényes. A régi operációsrendszer-lemez titkosítva marad a régi lemezes titkosítási készlettel*, majd kattintson **az OK gombra**. 

    A konfigurálást követően a labor operációsrendszer-lemezeket a rendszer a lemez titkosítási készletével megadott ügyfél által felügyelt kulccsal fogja titkosítani. 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>Annak ellenőrzése, hogy a lemezek titkosítása folyamatban van-e

1. Nyissa meg a laborban egy, az ügyfél által felügyelt kulccsal rendelkező lemez titkosításának engedélyezése után létrehozott labor virtuális gépet.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="Virtuális gép, amelyen engedélyezve van a lemez titkosítása":::
1. Kattintson a virtuális gép erőforráscsoporthoz, és kattintson az operációsrendszer-lemezre.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="Virtuálisgép-erőforráscsoport":::
1. Lépjen a titkosításhoz, és ellenőrizze, hogy a titkosítás beállítása ügyfél által felügyelt kulcsra van-e beállítva a kiválasztott lemezes titkosítási készlettel.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="Titkosítás ellenőrzése":::
  
## <a name="next-steps"></a>Következő lépések

Lásd az alábbi cikkeket: 

- [Azure Disk Encryption](../virtual-machines/disk-encryption.md). 
- [Felhasználó által kezelt kulcsok](../virtual-machines/disk-encryption.md#customer-managed-keys)