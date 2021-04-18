---
title: Titkosított Azure-beli virtuális gépek áthelyezése régiók között az Azure Resource Mover
description: Megtudhatja, hogyan helyezze át a titkosított Azure-beli virtuális gépeket egy másik Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 457c4c4752b4d78434b1fb90710472b1998f1c4e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600692"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Oktatóanyag: Titkosított Azure-beli virtuális gépek áthelyezése régiók között

Ez a cikk azt ismerteti, hogyan lehet titkosított Azure-beli virtuális gépeket (VIRTUÁLIS gépeket) áthelyezni egy [másik Azure-régióba](overview.md)a Azure Resource Mover. 

A titkosított virtuális gépek leírása a következő lehet:

- Olyan virtuális gépek, amelyeken engedélyezve vannak Azure Disk Encryption lemezekkel. További információ: Windows rendszerű virtuális gép létrehozása és titkosítása a [Azure Portal.](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- Az ügyfelek által felügyelt kulcsokat (CMK-okat) az adatok titkosítására használt virtuális gépek, illetve kiszolgálóoldali titkosítás. További információ: A felügyelt lemezek kiszolgálóoldali Azure Portal ügyfél által kezelt kulcsokkal való titkosításának engedélyezése a [következőben:](../virtual-machines/disks-enable-customer-managed-keys-portal.md).


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze az előfeltételeket. 
> * Az engedélyezett Azure Disk Encryption esetén másolja át a kulcsokat és titkos kulcsokat a forrás-régió kulcstartóból a cél-régió kulcstartóba.
> * Készítse elő a virtuális gépek áthelyezését és az erőforrások kiválasztását abban a forrás-régióban, amelyből át szeretné őket áthelyezni.
> * Erőforrás-függőségek feloldása.
> * Az engedélyezett Azure Disk Encryption virtuális gépekhez manuálisan rendelje hozzá a célkulcstartót. Az ügyfél által felügyelt kulcsokkal kiszolgálóoldali titkosítást felhasználó által felügyelt virtuális gépek esetén manuálisan rendeljen hozzá egy lemeztitkosítási készletet a célhelyen.
> * Helyezze át a kulcstartót vagy a lemeztitkosítási készletet.
> * Készítse elő és helyezze át a forrás erőforráscsoportot. 
> * Készítse elő és helyezze át a többi erőforrást.
> * Döntse el, hogy elveti vagy véglegesi az áthelyezést. 
> * Ha szeretné, távolítsa el az erőforrásokat a forrás régióban az áthelyezés után.

> [!NOTE]
> Ez az oktatóanyag a forgatókönyv kipróbálásának leggyorsabb útvonalát mutatja be. Csak az alapértelmezett beállításokat használja. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/). Ezután jelentkezzen be a [Azure Portal.](https://portal.azure.com)

## <a name="prerequisites"></a>Előfeltételek

Követelmény |Részletek
--- | ---
**Előfizetési engedélyek** | Ellenőrizze, hogy rendelkezik-e *Tulajdonosi* hozzáféréssel az áthelyezni kívánt erőforrásokat tartalmazó előfizetésben.<br/><br/> *Miért van szükség tulajdonosi hozzáférésre?* Amikor első alkalommal ad hozzá erőforrást egy [Azure-előfizetésben](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)egy adott forrás- és célpárhoz, a Resource Mover létrehoz egy rendszer által hozzárendelt felügyelt identitást , korábbi nevén a felügyeltszolgáltatás-identitást (MSI). Ezt az identitást az előfizetés megbízhatónak bízik meg. Mielőtt létrehozhatja az identitást, és hozzárendelheti  a szükséges szerepkörökhöz *(Közreműködő* és Felhasználói hozzáférés  rendszergazdája a forrás-előfizetésben), az erőforrások hozzáadásához használt fióknak tulajdonosi engedélyekkel kell rendelkeznie az előfizetésben. További információ: [Klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure AD-szerepkörök.](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)
**Virtuális gépek támogatása** | A következő lépésekkel ellenőrizze, hogy az áthelyezni kívánt virtuális gépek támogatottak-e:<li>[Ellenőrizze a](support-matrix-move-region-azure-vm.md#windows-vm-support) támogatott Windows rendszerű virtuális gépeket.<li>[Ellenőrizze a](support-matrix-move-region-azure-vm.md#linux-vm-support) támogatott Linux rendszerű virtuális gépeket és kernelverziókat.<li>Ellenőrizze a [támogatott számítási,](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings) [tárolási](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)és [hálózati beállításokat.](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)
**Key Vault-követelmények (Azure Disk Encryption)** | Ha a virtuális Azure Disk Encryption engedélyezve van, a forrás- és a célrégióban is szüksége lesz egy kulcstartóra. További információ: [Kulcstartó létrehozása.](../key-vault/general/quick-create-portal.md)<br/><br/> A forrás- és célrégió kulcstartóihoz a következő engedélyekre van szüksége:<li>Kulcsengedélyek: Kulcskezelési műveletek (Get, List) és Titkosítási műveletek (Visszafejtés és titkosítás)<li>Titkos listára vonatkozó engedélyek: Secret Management Operations (Get, List és Set)<li>Tanúsítvány (List and Get)
**Lemeztitkosítási készlet (kiszolgálóoldali titkosítás CMK-val)** | Ha cmK-t használó kiszolgálóoldali titkosítást használó virtuális gépeket használ, a forrás- és a célrégióban egyaránt szüksége lesz egy lemeztitkosítási készletre. További információ: [Lemeztitkosítási készlet létrehozása.](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)<br/><br/> A régiók közötti áthelyezés nem támogatott, ha ügyfél által kezelt kulcsokhoz hardveres biztonsági modult (HSM-kulcsokat) használ.
**Cél-régió kvótája** | Az előfizetésnek elegendő kvótára van szüksége a célterületen átköltöztetni szükséges erőforrások létrehozásához. Ha nem áll benne kvóta, kérjen [további korlátokat.](../azure-resource-manager/management/azure-subscription-service-limits.md)
**Célterület díjai** | Ellenőrizze a célterülethez társított díjszabást és díjakat, amelyekbe a virtuális gépeket átköltözteti. Használja a [díjkalkulátort.](https://azure.microsoft.com/pricing/calculator/)


## <a name="verify-permissions-in-the-key-vault"></a>Engedélyek ellenőrzése a kulcstartóban

Ha olyan virtuális gépeket mozgat, amelyeken engedélyezve Azure Disk Encryption, futtatnia kell egy [](#copy-the-keys-to-the-destination-key-vault) szkriptet a Kulcsok másolása a célkulcs-tárolóba című szakaszban említettek szerint. A szkriptet végrehajtó felhasználóknak megfelelő engedélyekkel kell rendelkezniük ehhez. A szükséges engedélyeket az alábbi táblázatban lehet megérteni. Az engedélyek módosításának lehetőségeit úgy találhatja meg, hogy a kulcstartóra vált a Azure Portal. A **Beállítások alatt** válassza a Hozzáférési **szabályzatok lehetőséget.**

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="A Key Vault Beállítások panelének &quot;Hozzáférési szabályzatok&quot; hivatkozásának képernyőképe." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Ha nincsenek meg a felhasználói engedélyek, válassza a **Hozzáférési** szabályzat hozzáadása lehetőséget, majd adja meg az engedélyeket. Ha a felhasználói fiók már rendelkezik szabályzatokkal, a **Felhasználó** alatt állítsa be az engedélyeket az alábbi táblázatban található utasításoknak megfelelően.

Az alkalmazásokat Azure Disk Encryption Azure-beli virtuális gépek a következő változatokkal is rendelkezni tudnak, és az engedélyeket a megfelelő összetevőknek megfelelően kell beállítania. A virtuális gépek a következővel lehetnek:
- Egy alapértelmezett beállítás, amelyben a lemez csak titkos kódokkal van titkosítva.
- A kulcstitkosítási kulcsot [(KEK) használó biztonság hozzáadva.](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-key-vault"></a>Forrás-régió kulcstartója

A szkriptet használó felhasználók számára állítsa be az engedélyeket a következő összetevőkhöz: 

Összetevő | Szükséges engedélyek
--- | ---
Titkos kulcsok |  *Lekérés* <br></br> Válassza **a Secret permissions** Secret Management Operations (Titkos engedélyek Titkos kezelés  >  **műveletei)** lehetőséget, majd a Get (Lekért) **lehetőséget.** 
Kulcsok <br></br> KEK használata esetén ezekre az engedélyekre a titkos kulcsokra vonatkozó engedélyek mellett szüksége lesz rá. | *Be-* és *visszafejtése* <br></br> Válassza **a Kulcsengedélyek**  >  **kulcskezelési műveletek** lehetőséget, majd a **Lekért lehetőséget.** A **Titkosítási műveletek alatt válassza** a **Visszafejtés lehetőséget.**

### <a name="destination-region-key-vault"></a>Cél-régió kulcstartója

Győződjön **meg arról,** hogy a Hozzáférési szabályzatok **Azure Disk Encryption kötettitkosításhoz engedélyezve van.** 

A szkriptet használó felhasználók számára állítsa be az engedélyeket a következő összetevőkhöz: 

Összetevő | Szükséges engedélyek
--- | ---
Titkos kulcsok |  *Set* <br></br> Válassza **a Titkos engedélyek** Secret Management  >  **Operations (Titkos engedélyek) lehetőséget,** majd a Set (Beállítás) **lehetőséget.** 
Kulcsok <br></br> KEK használata esetén ezekre az engedélyekre a titkos kulcsokra vonatkozó engedélyek mellett szüksége lesz rá. | *Get*, *Create* és *Encrypt* <br></br> Válassza **a Key Permissions** Key Management Operations (Kulcskezelési  >  **műveletek)** lehetőséget, majd a Get and Create **(Be-** és **létrehozás) lehetőséget.** A **Titkosítási műveletek alatt válassza** a Titkosítás **lehetőséget.**

<br>

A fenti engedélyek mellett a célkulcstartóban engedélyeket kell hozzáadnia [](./common-questions.md#how-is-managed-identity-used-in-resource-mover) ahhoz a felügyeltrendszer-identitáshoz, amely alapján a Mover erőforrás az Ön nevében hozzáfér az Azure-erőforrásokhoz. 

1. A **Beállítások alatt** válassza a Hozzáférési **szabályzatok hozzáadása lehetőséget.** 
1. A **Rendszerbiztonsági tag kiválasztása mezőben** keresse meg az MSI-t. Az MSI neve ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
1. Az MSI-hez adja hozzá a következő engedélyeket:

    Összetevő | Szükséges engedélyek
    --- | ---
    Titkos kulcsok|  *Le- és* *lista* <br></br> Válassza **a Titkos hozzáférések** Secret Management Operations (Titkos hozzáférések)  >  **lehetőséget,** majd a Get and List **(Lekért és** lista) **lehetőséget.** 
    Kulcsok <br></br> KEK használata esetén ezekre az engedélyekre a titkos kulcsokra vonatkozó engedélyek mellett szüksége lesz rá. | *Le- és* *lista* <br></br> Válassza **a Kulcsengedélyek**  >  **kulcskezelési műveletek,** majd a **Lekért és lista** **lehetőséget.**

<br>

### <a name="copy-the-keys-to-the-destination-key-vault"></a>A kulcsok másolása a célkulcstartóba

Másolja a titkosítási titkos kódokat és kulcsokat [a](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) forráskulcs-tárolóból a célkulcs-tárolóba egy általunk megírt szkript használatával.

- Futtassa a szkriptet a PowerShellben. Javasoljuk, hogy a PowerShell legújabb verzióját használja.
- A szkripthez a következő modulokra van szükség:
    - Az.Compute
    - Az.KeyVault (3.0.0-s verzió)
    - Az.Accounts (2.2.3-as verzió)

A szkript futtatásához tegye a következőket:

1. Nyissa meg [a szkriptet](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) a GitHubon.
1. Másolja a szkript tartalmát egy helyi fájlba, és nevezze *el* Copy-keys.ps1.
1. Futtassa a szkriptet.
1. Jelentkezzen be az Azure Portalra.
1. A Felhasználói bemenetek ablak  legördülő listáiban válassza ki a forrás-előfizetést, az erőforráscsoportot és a forrás virtuális gépet, majd válassza ki a célhelyet, valamint a lemez- és kulcstitkosítás céltartóit.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Képernyőkép a &quot;Felhasználói bemenetek&quot; ablakról a szkriptértékek beviteléhez." :::

1. Kattintson a **Kiválasztás** gombra. 
   
   Ha a szkript futása befejeződött, egy üzenet értesíti, hogy a CopyKeys sikeresen lefutott.

## <a name="prepare-vms"></a>Virtuális gépek előkészítése

1. Miután ellenőrizte, hogy a virtuális gépek megfelelnek-e az előfeltételeknek, [](#prerequisites)győződjön meg arról, hogy az áthelyezni kívánt virtuális gépek be vannak kapcsolva. A cél régióban elérhetővé tenni kívánt összes virtuálisgép-lemezt csatolni és inicializálni kell a virtuális gépen.
1. Annak biztosításához, hogy a virtuális gépek a legújabb megbízható főtanúsítvánnyal és frissített tanúsítvány-visszavonási listával (CRL) rendelkezik, tegye a következőket:
    - Windows rendszerű virtuális gépeken telepítse a legújabb Windows-frissítéseket.
    - Linux rendszerű virtuális gépeken kövesse a terjesztői útmutatót, hogy a gépek a legújabb tanúsítványokkal és CRL-ekkel is rendelkezésre edz. 
1. A virtuális gépek kimenő kapcsolatának engedélyezése érdekében tegye a következők egyikét:
    - Ha URL-alapú tűzfalproxyt használ a kimenő kapcsolatok vezérléséhez, engedélyezze a hozzáférést a [URL-címekhez.](support-matrix-move-region-azure-vm.md#url-access)
    - Ha hálózati biztonsági csoportra (NSG) vonatkozó szabályokat használ a kimenő kapcsolat vezérléséhez, hozza létre ezeket a [szolgáltatáscímke-szabályokat.](support-matrix-move-region-azure-vm.md#nsg-rules)

## <a name="select-the-resources-to-move"></a>Az áthelyezni kívánt erőforrások kiválasztása

- Bármelyik támogatott erőforrástípust kiválaszthatja a kiválasztott forrás régióban található bármelyik erőforráscsoportban.  
- Az erőforrásokat áthelyezheti egy olyan célterületre, amely ugyanabban az előfizetésben található, mint a forrás-régió. Ha módosítani szeretné az előfizetést, azt az erőforrások áthelyezte után is megteheti.

Az erőforrások kiválasztásához tegye a következőket:

1. A Azure Portal keressen rá az **erőforrás-átvétel kifejezésre.** Ezután a **Szolgáltatások alatt válassza** a Azure Resource Mover. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Képernyőkép a keresési eredményekről a Azure Resource Mover keresési Azure Portal." :::

1. A Azure Resource Mover **panelen** válassza az Áthelyezés **régiók között lehetőséget.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Képernyőkép az &quot;Áthelyezés régiók között&quot; gombról, ahol erőforrásokat adhat hozzá egy másik régióba való áthelyezéshez." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

1. Az Erőforrások **áthelyezése panelen** válassza a **Forrás + cél lapot.** Ezután a legördülő listában válassza ki a forrás-előfizetést és -régiót.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="A forrás- és cél régiót kiválasztó oldal." :::

1. A **Cél területen** válassza ki a régiót, ahová át szeretné áthelyezni a virtuális gépeket, majd válassza a Tovább **lehetőséget.**

1. Válassza az **Áthelyezni kívánt erőforrások** lapot, majd az **Erőforrások kiválasztása lehetőséget.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Képernyőkép az &quot;Erőforrások áthelyezése&quot; panelről és az &quot;Erőforrások kiválasztása&quot; gombról.]." :::

1. Az Erőforrások **kiválasztása panelen** válassza ki az áthelyezni kívánt virtuális gépeket. Ahogy azt a [Select the resources to move](#select-the-resources-to-move) (Áthelyezni kívánt erőforrások kiválasztása) szakaszban említettük, csak az áthelyezéshez támogatott erőforrásokat adhat hozzá.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Képernyőkép az &quot;Erőforrások kiválasztása&quot; panelről az áthelyezni kívánt virtuális gépek kiválasztásához." :::

    > [!NOTE]
    >  Ebben az oktatóanyagban egy olyan virtuális gépet választ, amely kiszolgálóoldali titkosítást (rayne-vm) használ egy ügyfél által felügyelt kulccsal, és egy virtuális gépet, amelynél engedélyezve van a lemeztitkosítás (rayne-vm-ade).

1. Válassza a **Kész** lehetőséget.
1. Válassza az **Áthelyezni kívánt erőforrások lapot,** majd kattintson a Tovább **gombra.**
1. Válassza az **Áttekintés lapot,** majd ellenőrizze a forrás- és célbeállításokat. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="A forrás- és célbeállítások áttekintésére vonatkozó panel képernyőképe." :::

1. Válassza **a Folytatás** lehetőséget az erőforrások hozzáadásának megkezdéséhez.
1. A folyamat nyomon követéséhez válassza az értesítések ikont. A folyamat sikeres befejezése után az Értesítések **panelen** válassza az **Áthelyezni kívánt erőforrások hozzáadva lehetőséget.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Képernyőkép az &quot;Értesítések&quot; panelről annak megerősítéséhez, hogy az erőforrások hozzáadása sikeres volt." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
1. Az értesítés kiválasztása után tekintse át az erőforrásokat a **Régiók között** lapon.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="A hozzáadott erőforrások &quot;Előkészítés függőben&quot; állapottal való képernyőképe." :::

> [!NOTE]
> - A hozzáadt erőforrások Előkészítés függő állapotban *vannak.*
> - A rendszer automatikusan hozzáadja a virtuális gépek erőforráscsoportját.
> - Ha úgy  módosítja a Célkonfigurációs bejegyzéseket, hogy a cél régióban már létező erőforrást használjanak, az erőforrás állapota Véglegesítés *függőben* lesz, mert nem kell áthelyezést kezdeményezni hozzá.
> - Ha egy hozzáadott erőforrást szeretne eltávolítani, a használt metódus attól függ, hogy hol van az áthelyezési folyamatban. További információ: [Áthelyezési gyűjtemények és erőforráscsoportok kezelése.](remove-move-resources.md)


## <a name="resolve-dependencies"></a>Függőségek feloldása

1. Ha valamelyik erőforrás a *Függőségek* ellenőrzése üzenetet mutatja a **Problémák** oszlopban, válassza a **Függőségek ellenőrzése** gombot.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="A &quot;Függőségek ellenőrzése&quot; gombot bemutató képernyőkép." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Megkezdődik az ellenőrzési folyamat.
1. Ha találhatók függőségek, válassza a **Függőségek hozzáadása lehetőséget.**  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="A &quot;Függőségek hozzáadása&quot; gomb képernyőképe." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


1. A **Függőségek hozzáadása panelen** hagyja meg az alapértelmezett **Minden függőség megjelenítése beállítást.**

    - **Az összes függőség megjelenítése** egy erőforrás összes közvetlen és közvetett függősége között iterál. Virtuális gépek esetén például a hálózati adaptert, a virtuális hálózatot, a hálózati biztonsági csoportokat (NSG-ket) és így tovább.
    - **Az első szintű függőségek megjelenítése csak a** közvetlen függőségeket jeleníti meg. Egy virtuális gép esetén például a hálózati adapter látható, a virtuális hálózat nem.
 
1. Válassza ki a hozzáadni kívánt függő erőforrásokat, majd válassza a **Függőségek hozzáadása lehetőséget.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Képernyőkép a függőségek listájáról és a &quot;Függőségek hozzáadása&quot; gombról." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

1. Ellenőrizze újra a függőségeket. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="A függőségek újraértékezésére vonatkozó panel képernyőképe." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Célerőforrások hozzárendelése

A titkosításhoz társított célerőforrásokat manuálisan kell hozzárendelni.

- Ha olyan virtuális gépet mozgat, amely Azure Disk Encryption engedélyezve van, a cél régióban található kulcstartó függőségként jelenik meg.
- Ha cmK-ket használó kiszolgálóoldali titkosítást használó virtuális gépet mozgat, a cél régióban lévő lemeztitkosítási készlet függőségként jelenik meg. 
- Mivel ez az oktatóanyag egy olyan virtuális gép áthelyezését mutatja be, amely Azure Disk Encryption engedélyezett, és cmK-t használ, a célkulcs-tároló és a lemeztitkosítási készlet is függőségként fog mutatni.

A célerőforrások manuális hozzárendeléshez tegye a következőket:

1. A lemeztitkosítási készlet bejegyzésben válassza a Cél konfigurációja **oszlopban** a Nincs hozzárendelve **erőforrás** lehetőséget.
1. A **Konfigurációs beállítások lapon** válassza ki a céllemez titkosítási készletét, majd válassza a Módosítások mentése **lehetőséget.**
1. Mentheti és ellenőrizheti a módosított erőforrás függőségeit, vagy mentheti csak a módosításokat, majd egyszerre ellenőrizheti a módosításokat.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Képernyőkép a &quot;Célkonfiguráció&quot; panelről a módosítások cél régióban való mentésére." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    A célerőforrás hozzáadása után a lemeztitkosítási készlet állapota *Véglegesítés függőben állapotúra változik.*

1. A Key Vault-bejegyzésben válassza a **Célkonfiguráció oszlopban** a Nincs hozzárendelve **erőforrás** lehetőséget. A **Konfigurációs beállítások alatt** válassza ki a célkulcstartót, majd mentse a módosításokat. 

Ebben a szakaszban a lemeztitkosítási készlet és a kulcstartó állapota *Véglegesítés függőben állapotúra változik.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Képernyőkép az egyéb erőforrások előkészítésére vonatkozó panelről." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

A titkosítási erőforrások áthelyezési folyamatának véglegesítéshez és befejezéséhez tegye a következőket:

1. A **Régiók között beállításban** válassza ki az erőforrást (lemeztitkosítási készlet vagy kulcstartó), majd válassza **az Áthelyezés véglegesítése lehetőséget.**
1. Az **Erőforrások áthelyezése mezőben** válassza a **Véglegesítés lehetőséget.**

> [!NOTE]
> Miután véglegeste az áthelyezést, az erőforrás állapota Delete source pending (Forrás *törlése függőben) állapotra változik.*


## <a name="move-the-source-resource-group"></a>A forrásként szükséges erőforráscsoport áthelyezése 

A virtuális gépek előkészítése és áthelyezése előtt a virtuálisgép-erőforráscsoportnak jelen kell lennie a cél régióban. 

### <a name="prepare-to-move-the-source-resource-group"></a>Felkészülés a forrásként szolgáló erőforráscsoport áthelyezésére

Az előkészítési folyamat során a Resource Mover Azure Resource Manager (ARM) sablonokat hoz létre az erőforráscsoport beállításaiból. Az erőforráscsoporton belüli erőforrásokra ez nem lesz hatással.

A forrás erőforráscsoport áthelyezésének előkészítéséhez tegye a következőket:

1. A **Régiók között mezőben** válassza ki a forrásként kiválasztott erőforráscsoportot, majd válassza a Prepare (Előkészítés) **lehetőséget.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Az &quot;Erőforrások előkészítése&quot; panel &quot;Előkészítés&quot; gombjának képernyőképe." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

1. Az **Erőforrások előkészítése mezőben** válassza a **Prepare (Előkészítés) lehetőséget.**

> [!NOTE]
> Miután előkészítette az áthelyezést, az erőforráscsoport állapota *Függőben az Áthelyezés kezdeményezése állapotra változik.* 

 
### <a name="move-the-source-resource-group"></a>A forrásként szükséges erőforráscsoport áthelyezése

Kezdje el a forráskénti erőforráscsoport áthelyezését az alábbiak szerint:

1. A Régiók **közötti panelen** válassza ki az erőforráscsoportot, majd válassza az **Áthelyezés kezdeményezése lehetőséget.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Képernyőkép az &quot;Áthelyezés kezdeményezése&quot; gombról a Régiók között panelen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

1. Az Erőforrások **áthelyezése panelen** válassza az **Áthelyezés kezdeményezése lehetőséget.** Az erőforráscsoport állapota *Folyamatban lévő áthelyezés kezdeményezése állapotra változik.*   
1. Az áthelyezés kezdeményezése után létrejön a célként szolgáló erőforráscsoport a létrehozott ARM-sablon alapján. A forrás erőforráscsoport állapota *Véglegesítés áthelyezés függőben állapotra változik.*

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Az &quot;Erőforrások áthelyezése&quot; panel képernyőképe, amely azt mutatja, hogy az erőforráscsoport állapota &quot;Áthelyezés véglegesítés függőben&quot; állapotra változott." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Az áthelyezés véglegesítéshez és a folyamat befejezéséhez tegye a következőket:

1. A Régiók **közötti panelen** válassza ki az erőforráscsoportot, majd válassza az **Áthelyezés véglegesítése lehetőséget.**
1. Az Erőforrások **áthelyezése panelen** válassza a **Véglegesítés lehetőséget.**

> [!NOTE]
> Miután véglegeste az áthelyezést, a forrásként kijelölt erőforráscsoport állapota *Delete source pending (Forrás törlése függőben) állapotra változik.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Képernyőkép a forrásként kijelölt erőforráscsoportról, amely a &quot;Forrás törlése függőben&quot; állapotra változott." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Erőforrások előkészítése az áthelyezéshez

Most, hogy áthelyezte a titkosítási erőforrásokat és a forrásként használt erőforráscsoportot, előkészítheti a többi olyan erőforrás áthelyezését, amelynek aktuális állapota *Prepare pending*.


1. A Régiók **közötti panelen ellenőrizze** újra az áthelyezést, és oldja meg a problémákat.
1. Ha az áthelyezés megkezdése előtt szerkeszteni szeretné a célbeállításokat, válassza az erőforrás Célkonfiguráció oszlopában található hivatkozást, majd szerkessze a beállításokat.  Ha szerkeszti a cél virtuális gép beállításait, a cél virtuális gép mérete nem lehet kisebb, mint a forrás virtuális gép mérete.
1. Az áthelyezni kívánt *Előkészítés függő* állapotú erőforrásokhoz válassza az Előkészítés **lehetőséget.**
1. Az Erőforrások **előkészítése panelen** válassza az Előkészítés **lehetőséget.**

    - Az előkészítés során a Azure Site Recovery mobilitási ügynök telepítve lesz a virtuális gépekre a replikálásukhoz.
    - A rendszer rendszeres időközönként replikálja a virtuális gép adatait a célterületre. Ez nincs hatással a forrás virtuális gépre.
    - Az erőforrás áthelyezése ARM-sablonokat hoz létre a többi forráserőforráshoz.

> [!NOTE]
> Miután előkészítette az erőforrásokat, az állapotuk *Áthelyezés kezdeményezése függőben állapotra változik.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Az &quot;Erőforrások előkészítése&quot; panel képernyőképe, amely az &quot;Áthelyezés kezdeményezése függőben&quot; állapotú erőforrásokat mutatja." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése

Most, hogy előkészítette az előkészített erőforrásokat, megkezdheti az áthelyezést. 

1. A Régiók **közötti panelen** válassza ki azokat az erőforrásokat, amelyeknek az *Állapota Áthelyezés kezdeményezése függőben,* majd válassza az Áthelyezés **kezdeményezése lehetőséget.**
1. Az Erőforrások **áthelyezése panelen** válassza az **Áthelyezés kezdeményezése lehetőséget.**
1. Az áthelyezés előrehaladását az értesítési sávon követheti nyomon.

    - Virtuális gépekhez a replika virtuális gépek a célterületen vannak létrehozva. A forrás virtuális gép leáll, és némi állásidő (általában percek) következik be.
    - A Resource Mover az előkészített ARM-sablonok használatával hoz létre újra más erőforrásokat. Általában nincs állásidő.
    - Miután áthelyezte az erőforrásokat, az állapotuk *Véglegesítés áthelyezés függőben állapotra változik.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Képernyőkép az erőforrások &quot;Áthelyezés véglegesítésének függőben&quot; állapotával való listájáról." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Elveti vagy véglegesítést ad vissza?

A kezdeti áthelyezés után eldöntheti, hogy véglegesítést vagy elveti az áthelyezést. 

- **Elvetés:** Ha teszteli az áthelyezést, és nem szeretné ténylegesen áthelyezni a forráserőforrást, elvetheti az áthelyezést. Az áthelyezés elvetve az erőforrást *Függőben állapotú áthelyezés kezdeményezése állapotra adja* vissza.
- **Véglegesítés:** A véglegesítés befejezi a célterületre való áthelyezést. Miután véglegeste a forráserőforrást, az állapota Delete *source pending*(Forrás törlése függőben) állapotra változik, és eldöntheti, hogy törölni szeretné-e.


## <a name="discard-the-move"></a>Az áthelyezés elvetve 

Az áthelyezés elvethez tegye a következőket:

1. A Régiók **közötti panelen** jelölje ki azokat az erőforrásokat, amelyeknek az állapota *Commit move pending*(Áthelyezés véglegesítés függőben) állapotú, majd válassza **az Áthelyezés elvetása lehetőséget.**
1. Az Áthelyezés **elvetése panelen** válassza az Elvetés **lehetőséget.**
1. Az áthelyezés előrehaladását az értesítési sávon követheti nyomon.


> [!NOTE]
> Miután elvette az erőforrásokat, a virtuális gép állapota *Függőben állapotra változik Áthelyezés kezdeményezése függőben állapotra.*

## <a name="commit-the-move"></a>Az áthelyezés véglegesítése

Az áthelyezési folyamat befejezéséhez véglegesítést kell végrehajtania az alábbi műveletekkel: 

1. A Régiók **közötti panelen** válassza ki azokat az erőforrásokat, amelyeknek az állapota *Commit move pending*(Áthelyezés véglegesítésre függőben) , majd válassza **a Commit move (Áthelyezés véglegesítése) lehetőséget.**
1. Az Erőforrások **véglegesítése panelen** válassza a **Véglegesítés lehetőséget.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Képernyőkép azon erőforrások listájáról, amelyek véglegesítenie kell az erőforrásokat az áthelyezés véglegesítéshez." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

1. Kövesse nyomon a véglegesítési folyamat előrehaladását az értesítési sávon.

> [!NOTE]
> - Az áthelyezést követően a virtuális gépek replikálása leáll. A véglegesítés nem befolyásolja a forrás virtuális gépet.
> - A véglegesítési folyamat nincs hatással a forráshálózati erőforrásokra.
> - Miután véglegeste az áthelyezést, az erőforrás állapota a Forrás törlése *függőben állapotra változik.*



## <a name="configure-settings-after-the-move"></a>Beállítások konfigurálása az áthelyezés után

- A mobilitási szolgáltatás nem törlődik automatikusan a virtuális gépekről. Távolítsa el manuálisan, vagy hagyja meg, ha azt tervezi, hogy újra áthelyezi a kiszolgálót.
- Módosítsa az Azure szerepköralapú hozzáférés-vezérlési (RBAC-) szabályait az áthelyezés után.

## <a name="delete-source-resources-after-commit"></a>Forráserőforrások törlése véglegesítés után

Az áthelyezés után igény szerint törölheti a forrás régióban található erőforrásokat. 

1. A Régiók **közötti panelen** jelölje ki a törölni kívánt forráserőforrásokat, majd válassza a **Forrás törlése lehetőséget.**
1. A **Delete source (Forrás törlése)** mezőbe írja be a yes (igen) parancsot, majd a **Confirm delete**(Törlés megerősítése) mezőbe írja be a **következőt:**. A művelet nem visszafordítható, ezért gondosan ellenőrizze!
1. Miután beírta a **yes (igen)** választ, válassza **a Delete source (Forrás törlése) lehetőséget.**

> [!NOTE]
>  Az Erőforrás áthelyezése portálon nem törölhet erőforráscsoportokat, kulcstartókat vagy SQL Server példányokat. Mindegyiket külön-külön kell törölnie az egyes erőforrások tulajdonságok lapján.


## <a name="delete-resources-that-you-created-for-the-move"></a>Az áthelyezéshez létrehozott erőforrások törlése

Az áthelyezés után manuálisan törölheti az áthelyezési gyűjteményt, és Site Recovery a folyamat során létrehozott erőforrásokat.

- Az áthelyezési gyűjtemény alapértelmezés szerint rejtett. Hogy lássa, be kell kapcsolnia a rejtett erőforrásokat.
- A gyorsítótár tárolója rendelkezik egy zárolással, amely a törlés előtt törölhető.

Az erőforrások törléséhez tegye a következőket: 
1. Keresse meg az erőforrásokat az ```RegionMoveRG-<sourceregion>-<target-region>``` erőforráscsoportban.
1. Ellenőrizze, hogy a forrás régióban található összes virtuális gép és egyéb forráserőforrás át lett-e helyezték vagy törölték-e. Ez a lépés biztosítja, hogy ne használja őket függőben lévő erőforrások.
1. Törölje az erőforrásokat:

    - Gyűjtemény nevének áthelyezése: ```movecollection-<sourceregion>-<target-region>```
    - Gyorsítótár-tárfiók neve: ```resmovecache<guid>```
    - Tároló neve: ```ResourceMove-<sourceregion>-<target-region>-GUID```
## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Titkosított Azure-beli virtuális gépeket és azok függő erőforrásait áthelyezte egy másik Azure-régióba.


Következő lépésként próbáljon meg adatbázisokat Azure SQL és rugalmas készleteket egy másik régióba.

> [!div class="nextstepaction"]
> [Erőforrások Azure SQL áthelyezése](./tutorial-move-region-sql.md)
