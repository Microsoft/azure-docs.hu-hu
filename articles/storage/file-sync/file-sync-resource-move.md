---
title: Azure File Sync erőforrások mozgatása és a topológia változásai
description: Megtudhatja, hogyan helyezze át a szinkronizálási erőforrásokat erőforráscsoportok, előfizetések és Azure Active Directory (AAD)-bérlők között.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb59b8d48d8411ade620304fbd143d86ac5ae919
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796980"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Erőforrások Azure File Sync áthelyezése egy másik erőforráscsoportba, előfizetésbe vagy AAD-bérlőbe

Ez a cikk azt ismerteti, hogyan módosítja az erőforráscsoport, előfizetés vagy Azure Active Directory -bérlő (AAD) Azure File Sync felhőerőforrásai és Azure Storage-fiókjai esetében.

A felhőalapú erőforrások Azure File Sync tervezésekor fontos, hogy a tárolási erőforrásokat is egyszerre vegye figyelembe. A következő erőforrások léteznek:

**Azure File Sync erőforrások (hierarchikus sorrendben)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Társzinkronizálási szolgáltatás
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Regisztrált kiszolgáló
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Szinkronizálási csoport
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Felhőbeli végpont
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Kiszolgálóvégpont

A Azure File Sync csak a Storage Sync Service-erőforrást tudja áthozni. Minden alforrás a szülőjére van kötve, és nem lehet másik szinkronizálási szolgáltatásba áthelyezni.

**Azure Storage-erőforrások (hierarchikus sorrendben)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Tárfiók
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Fájlmegosztás

Az Azure Storage-ban az egyetlen olyan erőforrás, amely áthelyezésre képes, a tárfiók. Az Azure-fájlmegosztások nem áthelyezhetőek másik tárfiókba alforrásként.

## <a name="supported-combinations"></a>Támogatott kombinációk

Az erőforrások áthelyezésének megtervezésekor a tárfiókot és a legfelső szintű Azure File Sync storage *sync service* nevű erőforrást együtt kell figyelembe venni.

Ajánlott eljárásként a Storage Sync Service-nek és a fájlmegosztásokat szinkronizáló tárfióknak mindig ugyanabban az előfizetésben kell lennie. Ezek a kombinációk támogatottak:

* A Társzinkronizálási szolgáltatás és a tárfiókok különböző **erőforráscsoportokban** (ugyanaz az Azure-bérlő) találhatók
* A Társzinkronizálási szolgáltatás és a tárfiókok különböző **előfizetésben** (ugyanaz az Azure-bérlő) találhatók

> [!IMPORTANT]
> A különböző áthely-kombinációk révén a Társzinkronizálási szolgáltatás és a tárfiókok különböző előfizetésekbe is beülhet, amelyekre különböző AAD-bérlők vannak irányítva. Úgy tűnik, hogy a szinkronizálás működik is, de ez a konfiguráció nem támogatott. A szinkronizálás a jövőben leállhat, és nem tud újra működő állapothoz állni.

Az erőforrások áthelyezésének tervezésekor különböző szempontokat kell figyelembe venni az ugyanazon [AAD-bérlőn](#move-within-the-same-azure-active-directory-tenant) belüli és egy másik [AAD-bérlőre való áthelyezéskor.](#move-to-a-new-azure-active-directory-tenant) AAD-bérlők áthelyezésekor mindig helyezze át a szinkronizálási és tárolási erőforrásokat.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Áthelyezés ugyanazon a bérlőn Azure Active Directory belül

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="A Storage Sync Azure Portal erőforráshoz való hozzárendelését bemutató kép, kibontva az Áthelyezés parancsot. Megjeleníti az erőforráscsoport áthelyezési és előfizetés-áthelyezési beállításait." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        A Storage Sync Service-erőforrások áthelyezésének kényelmes módja az Azure Portal. Lépjen az áthelyezni kívánt szinkronizálási szolgáltatáshoz, és válassza az **Áthelyezés** lehetőséget a parancssávon. Ugyanezek a lépések vonatkoznak a tárfiókok áthelyezésre is. Így az összes erőforrást áthelyezheti egy erőforráscsoportba. Egy teljes erőforráscsoport áthelyezését akkor javasoljuk, ha a Szinkronizálási szolgáltatás és az ebben az erőforráscsoportban használt összes tárfiókot használja.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Tárfiók-erőforrás áthelyezésekor a szinkronizálás azonnal leáll. Az új előfizetésben a megfelelő tárfiókok eléréséhez manuálisan kell engedélyeznie a szinkronizálást. A [Azure File Sync storage access authorization (Tárelérés engedélyezése)](#azure-file-sync-storage-access-authorization) című szakasz adja meg a szükséges lépéseket.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Áthelyezés új Azure Active Directory bérlőre

Az egyes erőforrások, például a Társzinkronizálási szolgáltatás vagy a tárfiókok, önmagukban nem áthelyezhetőek egy másik AAD-bérlőbe. Csak az Azure-előfizetések tudnak AAD-bérlőket áthelyezni. Gondolja át az új AAD-bérlő előfizetési struktúráját. Dedikált előfizetést is használhat a Azure File Sync. 

1. Hozzon létre egy Azure-előfizetést (vagy határozz meg egy meglévőt a régi bérlőben, amit át kell költözni.
1. [Végezze el az előfizetés áthelyezését](#move-within-the-same-azure-active-directory-tenant) a Storage Sync Service ugyanazon AAD-bérlőjébe és az összes társított tárfiókba.
1. A szinkronizálás leáll. Azonnal befejezi a bérlő áthelyezését, vagy visszaállíthatja a szinkronizálás azon képességét, hogy hozzáférjen az áthelyezet [tárfiókhoz.](#azure-file-sync-storage-access-authorization) Ezt követően később át tud lépni az új AAD-bérlőre.

Miután az összes Azure File Sync erőforrást a saját előfizetésébe lefoglalta, készen áll a teljes előfizetés áthelyezésére a cél AAD-bérlőbe. Az [átadási előfizetési útmutató](../../role-based-access-control/transfer-subscription.md) lehetővé teszi az ilyen átvitelek tervezését és végrehajtását.

> [!WARNING]
> Amikor előfizetést továbbít egy bérlőről egy másikra, a szinkronizálás azonnal leáll. Az új előfizetésben a megfelelő tárfiókok eléréséhez manuálisan kell engedélyeznie a szinkronizálást. A [Azure File Sync storage access authorization (Tárelérés engedélyezése)](#azure-file-sync-storage-access-authorization) című szakasz adja meg a szükséges lépéseket.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Kép a Azure Portal, Előfizetés áttekintése panelről, kiemelve a Címtár módosítása eszköztárparancsot a lap tetején, középen." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Ha már rendelkezik tervvel és a szükséges engedélyekkel, készen áll a migrálás elkezdésére:
        1. A Azure Portal lépjen az előfizetés Áttekintés **paneljéhez.**
        1. Válassza a **Címtár módosítása** lehetőséget
        1. Kövesse a varázsló lépéseit az új AAD-bérlő hozzárendeléséhez.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Azure File Sync hozzáférés engedélyezése

Ha a tárfiókokat áthelyezi egy új előfizetésbe, vagy egy előfizetésen belül áthelyezi egy új Azure Active Directory-bérlőbe, a szinkronizálás leáll. A szerepköralapú hozzáféréssel (RBAC) a Azure File Sync engedélyezheti a tárfiókok hozzáférését, és ezeket a szerepkör-hozzárendeléseket a rendszer nem migrálja az erőforrásokkal.

### <a name="azure-file-sync-service-principal"></a>Azure File Sync szolgáltatásnév

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Kép a Azure Portal, az előfizetés-kezelésről és a regisztrált erőforrás-szolgáltatókról." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        A Azure File Sync szolgáltatásnévnek léteznie kell az AAD-bérlőben, mielőtt engedélyezné a tárfiók szinkronizálási hozzáférését. </br></br> Amikor még ma létrehoz egy új Azure-előfizetést, a Azure File Sync *Microsoft.StorageSync* erőforrás-szolgáltató automatikusan regisztrálva lesz az előfizetésében. Az erőforrás-szolgáltató regisztrációja elérhetővé *teszi* a szolgáltatásnév szinkronizálását az előfizetést Azure Active Directory bérlőben. A szolgáltatásnév hasonló az AAD-hez egy felhasználói fiókhoz. A szolgáltatásnév használatával Azure File Sync szerepköralapú hozzáférés-vezérléssel (RBAC) engedélyezheti az erőforrásokhoz való hozzáférést. Az egyetlen erőforrás-szinkronizáláshoz hozzáférésre van szükség, a szinkronizálni kívánt fájlmegosztásokat tartalmazó tárfiókok. *A Microsoft.StorageSyncet* hozzá kell rendelni a beépített **Olvasó** és Adatelérési szerepkörhöz a tárfiókon. </br></br> Ez a hozzárendelés automatikusan történik a bejelentkezett felhasználó felhasználói környezetében, amikor fájlmegosztást ad hozzá egy szinkronizálási csoporthoz, vagy más szóval létrehoz egy felhőbeli végpontot. Amikor egy tárfiók új előfizetésbe vagy AAD-bérlőbe kerül, ez a szerepkör-hozzárendelés elveszik, és manuálisan újra kell [állítani.](#establish-sync-access-to-a-storage-account)
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Ha a cél Azure-előfizetést nem a közelmúltban hozták létre, ellenőrizze, hogy a *Microsoft.StorageSync* erőforrás-szolgáltató regisztrálva van-e az előfizetésben. Ha nem, adja hozzá manuálisan ugyanazon a portál panelen.

### <a name="establish-sync-access-to-a-storage-account"></a>Szinkronizálási hozzáférés létrehozása tárfiókhoz

A [Azure File Sync szolgáltatásnévvel](#azure-file-sync-service-principal) kell engedélyezni a tárfiókhoz való hozzáférést szerepköralapú hozzáférés-vezérléssel (RBAC). *A Microsoft.StorageSync-et* hozzá kell rendelni a beépített **Olvasó** és Adatelérési szerepkörhöz a tárfiókon. 

Ez a hozzárendelés általában automatikusan történik a bejelentkezett felhasználó felhasználói környezetében, amikor fájlmegosztást ad hozzá egy szinkronizálási csoporthoz, vagy más szóval létrehoz egy felhőbeli végpontot. Ha azonban egy tárfiók új előfizetésbe vagy AAD-bérlőbe kerül, ez a szerepkör-hozzárendelés elveszik, és manuálisan újra kell állítani.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Kép egy tárfiók olvasói és adatelérési szerepköréhez rendelt Microsoft.StorageSync szolgáltatásnévről":::
    :::column-end:::
    :::column:::
        A Azure Portal lépjen ahhoz a tárfiókhoz, amelyhez újra meg kell bizonyosodni a szinkronizálási hozzáféréshez. <ol><li>A bal oldali tartalomjegyzékben válassza a Hozzáférés-vezérlés **(IAM)** lehetőséget.</li><li>Válassza a Szerepkör-hozzárendelések lapot azon felhasználók és alkalmazások (szolgáltatásnév) listájához, akik hozzáféréssel rendelkezik a tárfiókhoz.</li><li>Válassza a **Hozzáadás** lehetőséget</li><li>A Szerepkör **mezőben válassza** az **Olvasó és adatelérés lehetőséget.**</li><li>A Select **(Kijelölés) mezőbe** írja be a *Microsoft.StorageSync parancsot,* válassza ki a szerepkört, majd kattintson a Save (Mentés) **gombra.**</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Áthelyezés másik Azure-régióba

A Azure File Sync Storage *Sync Service* erőforrás és a szinkronizált fájlmegosztásokat tartalmazó tárfiókok azure-régióban vannak üzembe helyezni. Ezt a régiót az erőforrás létrehozásakor határozzák meg. A Társzinkronizálási szolgáltatás és a tárfiók erőforrásainak egyezniük kell. Ezek a régiók a létrehozásuk után egyik erőforrástípuson sem módosíthatók.

Egy másik régió erőforráshoz való hozzárendelése eltér [a](#region-fail-over)régió feladatátvételétől, amely a tárfiók redundanciabeállításától függően támogatott.

## <a name="region-fail-over"></a>Régió feladatátvétele

[Az Azure Storage georedundanikai lehetőségeket kínál](../common/storage-redundancy.md#geo-redundant-storage) a tárfiókok számára. Ezek a redundanciabeállítások problémákat okozhatnak a Azure File Sync. Ennek fő oka az, hogy a földrajzilag távoli régiók közötti replikációt nem a Azure File Sync végzi, hanem az Azure tárolási alrendszerének beépített tárreplikációs technológiája. Nem képes megérteni az alkalmazás állapotát, és Azure File Sync olyan alkalmazás, amely azure-fájlmegosztásokkal szinkronizál egy adott pillanatban. Ha ezek közül a földrajzilag túlterhelt tárhely-redundancia-lehetőségek bármelyikét választotta, nem veszíti el az összes adatát egy nagy méretű katasztrófa esetén. Azonban előre kell látnia [az adatvesztést.](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss)

> [!CAUTION]
> A feladatátvétel soha nem helyettesíti megfelelően az erőforrások megfelelő Azure-régióban való üzembe hagyása esetén. Ha az erőforrások a "rossz" régióban vannak, fontolja meg a szinkronizálás leállítását, és a szinkronizálás a kívánt régióban üzembe helyezett új Azure-fájlmegosztásokkal való újrabeállítását.

A regionális feladatátvételt a Microsoft egy olyan katasztrofális eseményben is elindíthatja, amely hosszabb ideig nem képes adatközpontokat renderelésre egy Azure-régióban. A vállalkozás által fenntartható állásidő definíciója kevesebb lehet, mint az az idő, amely alatt a Microsoft készen áll arra, hogy átveszi a feladatátvételt a regionális feladatátvétel megkezdése előtt. Ilyen esetben a feladatátvételt az ügyfelek is [kezdeményezhetik.](../common/storage-initiate-account-failover.md)

> [!IMPORTANT]
> Feladatátvétel esetén be kell nyújtott egy támogatási jegyet az érintett Storage Sync Services-szolgáltatásokhoz, hogy a szinkronizálás ismét működjön.

## <a name="see-also"></a>Lásd még

- [Az Azure-fájlmegosztások és -szinkronizálás migrálási útmutatóinak áttekintése](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- [Azure File Sync – hibaelhárítás](file-sync-troubleshoot.md)
- [Az Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)