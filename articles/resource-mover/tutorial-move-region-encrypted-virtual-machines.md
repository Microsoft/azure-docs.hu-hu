---
title: Titkosított Azure-beli virtuális gépek áthelyezése régiók között az Azure-erőforrás-mozgatóval
description: Megtudhatja, hogyan helyezhet át titkosított Azure-beli virtuális gépeket egy másik régióba az Azure Resource mozgató használatával
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 014b4d09a991ae4d0bb31ec0b9adee0c9e3b3553
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361009"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Oktatóanyag: titkosított Azure-beli virtuális gépek áthelyezése régiók között

Ebből a cikkből megtudhatja, hogyan helyezhet át titkosított Azure-beli virtuális gépeket egy másik Azure-régióba az [Azure-erőforrás-mozgató](overview.md)használatával. A titkosítás a következőképpen értendő:

- Azok a virtuális gépek, amelyeken engedélyezve van az Azure Disk Encryption szolgáltatás. [További információ](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- Vagy az ügyfél által felügyelt kulcsokat (CMKs) használó virtuális gépek a titkosításhoz (kiszolgálóoldali titkosítás). [További információ](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek ellenőrzése. 
> * Az Azure Disk Encryption szolgáltatást használó virtuális gépek esetében a forrás régió kulcstárolóból másolja a kulcsokat és a titkos kulcsokat a célként megadott régió kulcstartóba.
> * Készítse elő a virtuális gépeket a mozgatásához, majd válassza ki az áthelyezni kívánt forrás régió erőforrásait.
> * Erőforrás-függőségek feloldása.
> * Az Azure Disk Encryption szolgáltatást használó virtuális gépek esetében manuálisan rendelje hozzá a célként megadott Key vaultot. Az ügyfél által felügyelt kulcsokkal rendelkező, kiszolgálóoldali titkosítást használó virtuális gépek esetében manuálisan rendeljen hozzá egy lemezes titkosítási készletet a célhelyhez.
> * Helyezze át a Key vaultot és/vagy a lemez titkosítási készletét.
> * A forrás erőforráscsoport előkészítése és áthelyezése. 
> * A többi erőforrás előkészítése és áthelyezése.
> * Döntse el, hogy el kívánja-e dobni vagy véglegesíteni az áthelyezést. 
> * Az áthelyezést követően szükség esetén eltávolíthatja az erőforrásokat a forrás régióban.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb elérési útját mutatják be, és az alapértelmezett beállításokat használják. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/). Ezután jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="prerequisites"></a>Előfeltételek

**Követelmény** |**Részletek**
--- | ---
**Előfizetés engedélyei** | Győződjön meg arról, hogy *tulajdonosi* hozzáféréssel rendelkezik az áthelyezni kívánt erőforrásokat tartalmazó előfizetéshez.<br/><br/> **Miért van szükség tulajdonosi hozzáférésre?** Amikor először ad hozzá egy erőforrást egy adott forráshoz és célhoz egy Azure-előfizetésben, az erőforrás-mozgató létrehoz egy [rendszerhez rendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (korábbi nevén felügyelt szolgáltatás azonosítása (MSI)), amelyet az előfizetés megbízhatónak tekint. Az identitás létrehozásához, valamint a szükséges szerepkör (közreműködő és felhasználói hozzáférés rendszergazdája a forrás-előfizetésben) való hozzárendeléséhez az erőforrások hozzáadásához használt fióknak *tulajdonosi* engedélyekkel kell rendelkeznie az előfizetésben. [További](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) információ az Azure-szerepkörökről.
**VM-támogatás** | Győződjön meg arról, hogy az áthelyezni kívánt virtuális gépek támogatottak.<br/><br/> - [Ellenőrizze](support-matrix-move-region-azure-vm.md#windows-vm-support) a támogatott Windows-alapú virtuális gépeket.<br/><br/> - [Ellenőrizze](support-matrix-move-region-azure-vm.md#linux-vm-support) a támogatott Linux-alapú virtuális gépek és kernel-verziók ellenőrzését.<br/><br/> – A támogatott [számítási](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [tárolási](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)és [hálózati](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) beállítások keresése.
**Key Vault-követelmények (Azure Disk Encryption)** | Ha az Azure Disk Encryption engedélyezve van a virtuális gépek számára, a forrástartomány kulcstartóján kívül egy kulcstartó is szükséges a céltartományban. [Hozzon létre egy kulcstartót](../key-vault/general/quick-create-portal.md).<br/><br/> A forrás-és a célként megadott régióban található kulcstartók esetében a következő engedélyekre van szükség:<br/><br/> -Kulcs engedélyei: kulcskezelő műveletek (Beolvasás, Listázás); Titkosítási műveletek (visszafejtés és titkosítás).<br/><br/> -Titkos engedélyek: titkos felügyeleti műveletek (Beolvasás, Listázás és beállítás)<br/><br/> -Tanúsítvány (Listázás és lekérés).
**Lemezes titkosítási csoport (kiszolgálóoldali titkosítás CMK)** | Ha kiszolgálóoldali titkosítással rendelkező virtuális gépeket használ egy CMK, a forrásoldali régióban lévő lemezes titkosítás mellett a céltartományban is szükség van egy lemezes titkosítási csoportra. [Hozzon létre egy lemezes titkosítási készletet](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> A régiók közötti áthelyezés nem támogatott, ha HSM-kulcsokat használ az ügyfél által felügyelt kulcsokhoz.
**Cél régió kvótája** | Az előfizetéshez elegendő kvóta szükséges ahhoz, hogy létrehozza a célhelyen áthelyezett erőforrásokat. Ha nem rendelkezik kvótával, [kérjen további korlátozásokat](../azure-resource-manager/management/azure-subscription-service-limits.md).
**A célként megadott régió díjai** | Ellenőrizze, hogy a virtuális gépeket áthelyező cél régióhoz tartozó díjszabást és díjakat kell-e használni. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával segítséget nyújthat.


## <a name="verify-user-permissions-on-key-vault-for-vms-using-azure-disk-encryption-ade"></a>A Key vaulton lévő virtuális gépekre vonatkozó felhasználói engedélyek ellenőrzése Azure Disk Encryption (ADE) használatával

Ha olyan virtuális gépeket helyez át, amelyeken engedélyezve van az Azure Disk Encryption, le kell futtatnia egy parancsfájlt az [alább](#copy-the-keys-to-the-destination-key-vault) leírtak szerint, amelyhez a parancsfájlt végrehajtó felhasználónak megfelelő engedélyekkel kell rendelkeznie. A szükséges engedélyekről az alábbi táblázatból tájékozódhat. Az engedélyek módosításához a Azure Portal a Key vaultra navigálva, a **Beállítások** területen válassza a **hozzáférési szabályzatok** lehetőséget.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Gomb a Key Vault hozzáférési házirendjeinek megnyitásához." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Ha nincsenek felhasználói engedélyek, válassza a **hozzáférési házirend hozzáadása** lehetőséget, és adja meg az engedélyeket. Ha a felhasználói fiók már rendelkezik szabályzattal, a **felhasználó** területen állítsa be az engedélyeket az alábbi táblázat szerint.

Az ADE-t használó Azure-beli virtuális gépek a következő variációkkal rendelkezhetnek, és ennek megfelelően kell beállítani az engedélyeket a megfelelő összetevőkhöz.
- Alapértelmezett beállítás, amelyben a lemez csak titok használatával titkosított
- Biztonság hozzáadva a [kulcs titkosítási kulcsával](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-keyvault"></a>Forrás régió kulcstartója

A parancsfájlt végrehajtó felhasználónak be kell állítania az alábbi engedélyeket 

**Összetevő** | **Engedély szükséges**
--- | ---
Titkos kulcsok|  Engedély kérése <br> </br> A **Secret permissions** >   **Secret Management műveletekben** válassza a **beolvasás** lehetőséget. 
Kulcsok <br> </br> Ha a kulcs titkosítási kulcsát (KEK) használja, akkor a titkok mellett erre az engedélyre is szüksége van| Lekérési és visszafejtési engedély <br> </br> A **Key permissions** kulcskezelő  >  **műveletei** területen válassza a **beolvasás** lehetőséget. A **titkosítási műveletek** területen válassza a **Visszafejtés** lehetőséget.

### <a name="destination-region-keyvault"></a>Rendeltetési régió kulcstartója

A **hozzáférési házirendek** területen győződjön meg arról, hogy a **kötet titkosításának Azure Disk Encryption** engedélyezve van. 

A parancsfájlt végrehajtó felhasználónak be kell állítania az alábbi engedélyeket 

**Összetevő** | **Engedély szükséges**
--- | ---
Titkos kulcsok|  Engedély beállítása <br> </br> A **Secret permissions** >   **Secret Management műveletekben** válassza a **beállítás** lehetőséget. 
Kulcsok <br> </br> Ha a kulcs titkosítási kulcsát (KEK) használja, akkor a titkok mellett erre az engedélyre is szüksége van| Engedély beszerzése, létrehozása és titkosítása <br> </br> A **Key permissions** kulcskezelő  >  **műveletei** területen válassza a **beolvasás** és **Létrehozás** elemet. A **titkosítási műveletek** területen válassza a **titkosítás** lehetőséget.

A fenti engedélyek mellett a célként megadott Key vaultban engedélyeket kell adni ahhoz a [felügyelt Rendszeridentitáshoz](./common-questions.md#how-is-managed-identity-used-in-resource-mover) , amelyet az erőforrás-mozgató használ az Azure-erőforrások eléréséhez az Ön nevében. 

1. A **Beállítások** területen válassza a **hozzáférési házirendek hozzáadása** elemet. 
2. A **rendszerbiztonsági tag kiválasztása** területen keresse meg az MSI-t. Az MSI neve: ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
3. Adja hozzá a következő engedélyeket az MSI-hez

**Összetevő** | **Engedély szükséges**
--- | ---
Titkos kulcsok|  Engedélyek beolvasása és listázása <br> </br> A **Secret permissions** >   **Secret Management műveletekben** válassza a **beolvasás** és **Listázás** lehetőséget. 
Kulcsok <br> </br> Ha a kulcs titkosítási kulcsát (KEK) használja, akkor a titkok mellett erre az engedélyre is szüksége van| Letöltés, engedélyek listázása <br> </br> A **Key permissions** kulcskezelő  >  **műveletei** területen válassza a **beolvasás** és **lista** lehetőséget.



### <a name="copy-the-keys-to-the-destination-key-vault"></a>A kulcsok másolása a cél Key vaultba

A forrás Key vaultban lévő titkosítási titkokat és kulcsokat át kell másolnia a cél Key vaultba egy általunk biztosított parancsfájl használatával.

- Futtatja a szkriptet a PowerShellben. Javasoljuk, hogy futtassa a legújabb PowerShell-verziót.
- Pontosabban, a parancsfájlhoz a következő modulok szükségesek:
    - Az.Compute
    - Az. kulcstartó (3.0.0-es verzió)
    - Az. accounts (2.2.3-es verzió)

A parancsokat a következő sorrendben futtassa le:

1. Navigáljon a [szkripthez](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) a githubon.
2. Másolja a parancsfájl tartalmát egy helyi fájlba, és nevezze el *Copy-keys.ps1*.
3. Futtassa a szkriptet.
4. Jelentkezzen be az Azure-ba.
5. A **felhasználói bemeneti** előugró ablakban válassza ki a forrás-előfizetést, az erőforráscsoportot és a forrás virtuális gépet. Ezután válassza ki a célhelyet és a tárolókat a lemez és a kulcs titkosításához.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="A bemeneti parancsfájl értékeinek beugró ablaka." :::


6. A szkript befejezésekor a képernyő kimenete azt jelzi, hogy a CopyKeys sikeres volt.

## <a name="prepare-vms"></a>Virtuális gépek előkészítése

1. Miután [ellenőrizte, hogy a virtuális gépek megfelelnek](#prerequisites)-e a követelményeknek, győződjön meg arról, hogy az áthelyezni kívánt virtuális gépek be vannak kapcsolva. A célként megadott régióban elérhetővé kívánt összes virtuális gép lemezét csatolni és inicializálni kell a virtuális gépen.
3. Győződjön meg arról, hogy a virtuális gépek rendelkeznek a legújabb megbízható főtanúsítványokkal, valamint a visszavont tanúsítványok listájának (CRL) frissítésével. Ehhez tegye a következőket:
    - Windows rendszerű virtuális gépeken telepítse a legújabb Windows-frissítéseket.
    - Linux rendszerű virtuális gépeken kövesse a terjesztői útmutatót, hogy a számítógépek rendelkezzenek a legújabb tanúsítványokkal és CRL-vel. 
4. A virtuális gépek kimenő kapcsolatának engedélyezése a következőképpen:
    - Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, engedélyezze az alábbi [URL-címek](support-matrix-move-region-azure-vm.md#url-access) elérését
    - Ha hálózati biztonsági csoport (NSG) szabályokat használ a kimenő kapcsolatok vezérléséhez, hozza létre ezeket a [szolgáltatási kódelemek szabályait](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Válassza ki az áthelyezni kívánt erőforrásokat


- Bármelyik támogatott erőforrástípust kiválaszthatja a kiválasztott forrás régió bármelyik erőforráscsoporthoz.  
- Az erőforrásokat egy olyan célcsoportba helyezi át, amely ugyanabban az előfizetésben található, mint a forrás-régió. Ha módosítani szeretné az előfizetést, ezt az erőforrások áthelyezése után teheti meg.

Válassza az erőforrások lehetőséget a következők szerint:

1. A Azure Portal keresse meg az *erőforrás-mozgató* kifejezést. Ezután a **szolgáltatások** területen válassza az **Azure-erőforrás mozgató** lehetőséget.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Keresési eredmények az erőforrás-mozgató Azure Portalban." :::

2. Az **Áttekintés** területen kattintson az **Áthelyezés régiók között** elemre.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Gomb egy másik régióba való áthelyezéshez szükséges erőforrások hozzáadásához." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. Az **erőforrások áthelyezése**  >  **forrás + cél** területen válassza ki a forrás-előfizetést és a régiót.
4. A **cél** mezőben válassza ki azt a régiót, amelyre át szeretné helyezni a virtuális gépeket. Ezután kattintson a **Tovább** gombra.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Lap a forrás és a cél régió kiválasztásához.." :::

5. Az **áthelyezni kívánt erőforrások** területen kattintson az **erőforrások kiválasztása** elemre.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Gombra kattintva válassza ki az áthelyezni kívánt erőforrást.]." :::

6. Az **erőforrások kiválasztása** területen válassza ki a virtuális gépeket. Csak [az áthelyezéshez támogatott](#prepare-vms)erőforrásokat adhat hozzá. Ezután kattintson a **kész** gombra.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Lapon válassza ki az áthelyezni kívánt virtuális gépeket." :::

    > [!NOTE]
    >  Ebben az oktatóanyagban egy olyan virtuális gépet választunk ki, amely kiszolgálóoldali titkosítást (a (()-VM-t) használ egy ügyfél által felügyelt kulccsal, valamint egy virtuális gépet, amelyen engedélyezve van a lemezes titkosítás (a-beli-VM-ade).

7.  Az **áthelyezni kívánt erőforrásokhoz** kattintson a **tovább** gombra.
8. A **felülvizsgálat** területen ellenőrizze a forrás-és a célhely beállításait. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Lapon ellenőrizheti a beállításokat, és folytathatja az áthelyezést." :::

9. Az erőforrások hozzáadásának megkezdéséhez kattintson a **Folytatás** gombra.
10. A folyamat nyomon követéséhez válassza az értesítések ikont. A hozzáadási folyamat sikeres befejeződése után válassza a **hozzáadott erőforrások** lehetőséget az értesítések áthelyezéséhez.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="A rendszer sikeresen hozzáadta az erőforrások megerősítésének értesítését." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. Az értesítésre való kattintás után tekintse át az erőforrásokat az **egyes régiók között** oldalon.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="A függőben lévő előkészítéssel hozzáadott erőforrásokat bemutató lapok." :::

> [!NOTE]
> - A hozzáadott erőforrások *előkészítése függő* állapotba kerülnek.
> - A virtuális gépek erőforráscsoport hozzáadása automatikusan megtörténik.
> - Ha módosítja a **célként megadott konfigurációs** bejegyzéseket olyan erőforrás használatára, amely már létezik a célhelyen, az erőforrás állapota *függőben* állapotra van állítva, mert nincs szükség áthelyezésre.
> - Ha el kívánja távolítani egy hozzáadott erőforrást, akkor az a metódus, amely az áthelyezési folyamat helyétől függ. [További információ](remove-move-resources.md).


## <a name="resolve-dependencies"></a>Függőségek feloldása

1. Ha bármelyik erőforrás egy *érvényesítési függőségek* üzenetet jelenít meg a **problémák** oszlopban, válassza a **függőségek ellenőrzése** gombot.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="NButton a függőségek vizsgálatához." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Az érvényesítési folyamat megkezdődik.
2. Ha függőségek találhatók, kattintson a **függőségek hozzáadása** elemre.  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Gombra a függőségek hozzáadásához." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. A **függőségek hozzáadása** területen hagyja meg az alapértelmezett **összes függőség megjelenítése** beállítást.

    - Az **összes függőség megjelenítése** az erőforrás közvetlen és közvetett függőségein keresztül. Egy virtuális gép esetében például a NIC, a Virtual Network, a hálózati biztonsági csoportok (NSG) stb.
    - Az **első szint függőségeinek megjelenítése csak** a közvetlen függőségeket jeleníti meg. Egy virtuális gép esetében például a hálózati ADAPTERt jeleníti meg, a virtuális hálózatot azonban nem.
 
4. Válassza ki azokat a függő erőforrásokat, amelyeket hozzá szeretne adni > **függőségek hozzáadása** elemet.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="A függőségek listából válassza a függőségek lehetőséget." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. Újból érvényesítse a függőségeket. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Oldalt az újbóli ellenőrzéshez." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Cél erőforrások kiosztása

A titkosításhoz társított cél erőforrásokhoz manuális hozzárendelés szükséges.

- Ha olyan virtuális gépet helyez át, amely rendelkezik az Azure Disk Encryption (ADE) szolgáltatással, a célként megadott régióban található kulcstartó függőségként fog megjelenni.
- Ha olyan virtuális gépet helyez át, amely az egyéni által felügyelt kulcsokat (CMKs) használó kiszolgálóoldali titkosítást használ, akkor a célként kijelölt lemez titkosítása függőségként jelenik meg. 
- Mivel ez az oktatóanyag egy olyan virtuális gépet helyez át, amelyen az ADE engedélyezve van, a CMK használó virtuális gépek pedig függőségként jelennek meg a célszámítógép és a lemez titkosítási készlete között.

Rendeljen hozzá manuálisan a következő módon:

1. A lemez titkosítási készletének bejegyzésében válassza ki az **erőforrás nincs hozzárendelve** elemet a **cél konfiguráció** oszlopban.
2. A **konfigurációs beállítások** területen válassza ki a céllemez titkosítási készletét. Ezután válassza a **módosítások mentése** elemet.
3. Kiválaszthatja, hogy menti és érvényesítse a módosítani kívánt erőforrás függőségeit, vagy csak mentse a módosításokat, és ellenőrizze, hogy az egyik menetben hogyan módosítható.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Lapon válassza ki a lemezterület-titkosítási készletet a célhely területen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    A célként megadott erőforrás hozzáadása után a lemez titkosítási készlet állapota a *függőben lévő áthelyezésre* vált.
3. A Key Vault-bejegyzésben válassza ki az **erőforrás nincs hozzárendelve** elemet a **cél konfiguráció** oszlopban. **Konfigurációs beállítások**, válassza ki a cél kulcstartót. Mentse a módosításokat. 

Ebben a szakaszban a lemez titkosítási készlete és a Key Vault állapota is a *függőben lévő áthelyezésre* vált.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Lapon válassza ki az egyéb erőforrások előkészítését." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

A titkosítási erőforrások áthelyezési folyamatának véglegesítéséhez és befejezéséhez.

1. Az **egyes régiókban** válassza ki az erőforrást (a lemez titkosítási készletét vagy a kulcstartót) > **véglegesíti az áthelyezést**.
2. ln **erőforrások áthelyezése**, kattintson a **véglegesítés** elemre.

> [!NOTE]
> Az áthelyezés véglegesítése után az erőforrás a *forrás törlés függőben* állapotú.


## <a name="move-the-source-resource-group"></a>A forrás erőforráscsoport áthelyezése 

A virtuális gépek előkészítése és áthelyezése előtt a virtuálisgép-erőforráscsoporthoz a célként megadott régióban kell lennie. 

### <a name="prepare-to-move-the-source-resource-group"></a>Felkészülés a forrásként szolgáló erőforráscsoport áthelyezésére

Az előkészítési folyamat során az erőforrás-mozgató Azure Resource Manager (ARM) sablonokat hoz létre az erőforráscsoport-beállítások használatával. Az erőforráscsoport erőforrásai nem érintettek.

Előkészítés a következőképpen történik:

1. Az **egyes régiókban** válassza ki a forrásként szolgáló erőforráscsoportot > **előkészítése**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Erőforráscsoport előkészítése." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. Az **erőforrások előkészítése** területen kattintson az **előkészítés** gombra.

> [!NOTE]
> Az erőforráscsoport előkészítése után a folyamatban van a *mozgatás függőben* állapotba állítása. 

 
### <a name="move-the-source-resource-group"></a>A forrás erőforráscsoport áthelyezése

Az áthelyezést a következőképpen indíthatja el:

1. Az **egyes régiókban** válassza ki az erőforráscsoportot > **Áthelyezés kezdeményezése**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Gombra az áthelyezés indításához." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. ln **erőforrások áthelyezése**, kattintson az **Áthelyezés kezdeményezése** lehetőségre. Az erőforráscsoport *folyamatban* állapotba lép.   
3. Az áthelyezés kezdeményezése után létrejön a célként megadott erőforráscsoport, amely a generált ARM-sablon alapján történik. A forrás erőforráscsoport egy véglegesített *Áthelyezés függő* állapotba kerül.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Tekintse át a véglegesített áthelyezés függőben állapotot." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Az áthelyezési folyamat véglegesítése és befejezése:

1. Az **egyes régiókban** válassza ki az erőforráscsoportot > **véglegesíti az áthelyezést**.
2. ln **erőforrások áthelyezése**, kattintson a **véglegesítés** elemre.

> [!NOTE]
> Miután véglegesíti az áthelyezést, a forrás erőforráscsoport *függő* állapotban van.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Tekintse át a törlés függőben állapotot." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Az áthelyezni kívánt erőforrások előkészítése

Most, hogy áthelyezi a titkosítási erőforrásokat és a forrás-erőforráscsoportot, előkészítheti az *előkészítés függő* állapotú egyéb erőforrások áthelyezését.


1. Az **egyes régiókban** ismételje meg a műveletet, és hárítsa el a problémákat.
2. Ha a cél beállításait a mozgatás megkezdése előtt szeretné szerkeszteni, válassza ki a hivatkozást az erőforráshoz tartozó **cél konfiguráció** oszlopban, és szerkessze a beállításokat. Ha szerkeszti a cél virtuális gép beállításait, a cél virtuális gép mérete nem lehet kisebb, mint a forrás virtuális gép mérete.
3. Válassza az erőforrások **előkészítése** lehetőséget az áthelyezni kívánt *előkészítési* állapotban.
3. Az **erőforrások előkészítése** területen válassza az **előkészítés** lehetőséget.

    - Az előkészítési folyamat során a rendszer a Azure Site Recovery mobilitási ügynököt telepíti a virtuális gépekre a replikáláshoz.
    - A virtuálisgép-adatreplikációt a rendszer rendszeres időközönként replikálja a célhelyre. Ez nem befolyásolja a forrás virtuális gépet.
    - Az erőforrás áthelyezése ARM-sablonokat hoz létre a többi forrás erőforráshoz.

Az erőforrások előkészítése után folyamatban van egy *függőben lévő áthelyezési* állapot.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Az a lap, amely megjeleníti az erőforrásokat a függőben lévő áthelyezés állapotának kezdeményezéséhez." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése

Az előkészített erőforrások segítségével most már elindíthatja az áthelyezést. 

1. Az **egyes régiókban** válassza az állapot *elindítása függőben* állapotú erőforrások lehetőséget. Ezután kattintson az **Áthelyezés kezdeményezése** lehetőségre.
2. Az **erőforrások áthelyezése** területen kattintson az **Áthelyezés kezdeményezése** lehetőségre.
3. Nyomon követheti a folyamat lépéseit az értesítések sávján.

    - Virtuális gépek esetén a replika virtuális gépek a célként megadott régióban jönnek létre. A forrás virtuális gép le van állítva, és néhány állásidő (általában perc).
    - Az erőforrás-mozgató újra létrehozza a többi erőforrást az előkészített ARM-sablonok használatával. Általában nincs leállás.
    - Az erőforrások áthelyezése után a rendszer végrehajtja a *függőben lévő* véglegesített állapotot.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Az a lap, amely egy véglegesített állapotba helyezi az erőforrásokat." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Elveti vagy véglegesíti?

A kezdeti áthelyezés után eldöntheti, hogy véglegesíteni kívánja-e az áthelyezést, vagy elveti azt. 

- **Elvetés**: Ha teszteli a tesztet, elkerülheti az áthelyezést, és nem szeretné ténylegesen áthelyezni a forrás erőforrást. Az áthelyezés elvetése visszaadja az erőforrást a *függőben lévő áthelyezés indításának* állapotára.
- **Véglegesítés**: a véglegesítés befejezi az áthelyezést a célként megadott régióba. A véglegesítést követően a forrás erőforrás a *delete Source függőben* állapotba kerül, és eldöntheti, hogy szeretné-e törölni.


## <a name="discard-the-move"></a>Áthelyezés elvetése 

Az áthelyezés a következőképpen törölhető:

1. Az **egyes régiókban** válassza az állapot- *végrehajtási áthelyezés függőben* lévő erőforrások lehetőséget, majd kattintson az **Áthelyezés elvetése** lehetőségre.
2. Az **Áthelyezés elvetése** területen kattintson az **Elvetés** gombra.
3. Nyomon követheti a folyamat lépéseit az értesítések sávján.


> [!NOTE]
> Az erőforrások elvetése után a virtuális gépek egy *függőben lévő áthelyezési* állapotba kerülnek.

## <a name="commit-the-move"></a>Az áthelyezés elkövetése

Ha szeretné befejezni az áthelyezési folyamatot, véglegesítse az áthelyezést. 

1. Az **egyes régiókban** válassza az állapot- *végrehajtási áthelyezés függőben* lévő erőforrások lehetőséget, majd kattintson az **Áthelyezés elkövetése** elemre.
2. Az **erőforrások elkövetése** területen kattintson a **véglegesítés** elemre.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="A lap, amelybe erőforrásokat szeretne véglegesíteni." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. Nyomon követheti a végrehajtás előrehaladását az értesítések sávon.

> [!NOTE]
> - Az áthelyezés véglegesítése után a virtuális gépek replikációja leáll. A forrás virtuális gépet nem érinti a véglegesítés.
> - A commit művelet nem befolyásolja a forrás hálózatkezelési erőforrásokat.
> - Az áthelyezés véglegesítése után az erőforrások a *forrás törlés függőben* állapotban vannak.



## <a name="configure-settings-after-the-move"></a>Beállítások konfigurálása az áthelyezés után

- A mobilitási szolgáltatás nem lesz automatikusan eltávolítva a virtuális gépekről. Távolítsa el manuálisan, vagy hagyja meg, ha azt tervezi, hogy újra áthelyezi a kiszolgálót.
- Módosítsa az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szabályait az áthelyezés után.

## <a name="delete-source-resources-after-commit"></a>Forrás erőforrásainak törlése a véglegesítés után

Az áthelyezést követően lehetőség van a forrás régió erőforrásainak törlésére is. 

1. A **régiók között** válassza ki a törölni kívánt forrás-erőforrásokat. Ezután válassza a **forrás törlése** lehetőséget.
2. A **forrás törlése** lapon tekintse át, hogy mit szeretne törölni, és a **Törlés megerősítése** mezőbe írja be az **Igen értéket**. A művelet visszafordíthatatlan, ezért alaposan passzoljon.
3. Az **Igen** érték beírása után válassza a **forrás törlése** lehetőséget.

> [!NOTE]
>  Az erőforrás-áthelyezési portálon nem törölhet erőforráscsoportok, kulcstartók vagy SQL Server-kiszolgálók. Ezeket egyenként kell törölnie az egyes erőforrások tulajdonságlapján.


## <a name="delete-additional-resources-created-for-move"></a>Az áthelyezéshez létrehozott további erőforrások törlése

Az áthelyezést követően manuálisan törölheti az áthelyezési gyűjteményt, és Site Recovery a létrehozott erőforrásokat.

- Az áthelyezési gyűjtemény alapértelmezés szerint el van rejtve. Ha szeretné látni, be kell kapcsolnia a rejtett erőforrásokat.
- A gyorsítótár-tárolónak olyan zárolása van, amelyet törölni kell, mielőtt törölni lehet.

A következőképpen törölheti: 
1. Keresse meg az erőforrásokat az erőforráscsoporthoz ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Győződjön meg arról, hogy a forrás régióban lévő összes virtuális gép és más forrás erőforrás át lett helyezve vagy törölve lett. Ezzel biztosíthatja, hogy ne legyenek használatban függő erőforrások.
2. Erőforrások törlése:

    - Az áthelyezési gyűjtemény neve: ```movecollection-<sourceregion>-<target-region>``` .
    - A cache Storage-fiók neve ```resmovecache<guid>```
    - A tár neve: ```ResourceMove-<sourceregion>-<target-region>-GUID``` .
## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Titkosított Azure-beli virtuális gépeket és azok függő erőforrásait áthelyezte egy másik Azure-régióba.


Az Azure SQL Database-adatbázisok és a rugalmas készletek másik régióba való áthelyezésének kipróbálása folyamatban van.

> [!div class="nextstepaction"]
> [Azure SQL-erőforrások áthelyezése](./tutorial-move-region-sql.md)
