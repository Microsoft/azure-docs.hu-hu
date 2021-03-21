---
title: Erőforrás-áthelyezési és-topológiai változások Azure File Sync
description: Megtudhatja, hogyan helyezheti át a szinkronizálási erőforrásokat erőforráscsoportok, előfizetések és Azure Active Directory (HRE) bérlők között.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 3/10/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: acd5f9263a74d3273dc65522e77d83c90a719311
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555354"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Azure File Sync erőforrások áthelyezése egy másik erőforráscsoporthoz, előfizetésbe vagy HRE-bérlőbe

Ez a cikk azt ismerteti, hogyan lehet módosítani az erőforrás-csoport, az előfizetés vagy a Azure Active Directory (HRE) bérlőjét a Azure File Sync felhőalapú erőforrásaihoz és az Azure Storage-fiókokhoz.

Ha módosítani szeretné a Azure File Sync Felhőbeli erőforrásokat, fontos, hogy a tárolási erőforrásokat egyszerre vegye figyelembe. A következő erőforrások léteznek:

**Erőforrások Azure File Sync (hierarchikus sorrendben)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Storage Sync szolgáltatás
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Regisztrált kiszolgáló
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Szinkronizálási csoport
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Felhőbeli végpont
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Kiszolgálói végpont

A Azure File Sync csak a Storage Sync Service-erőforrást helyezi át. Az alerőforrások a szülőhöz vannak kötve, és nem helyezhetők át másik Storage Sync szolgáltatásba.

**Azure Storage-erőforrások (hierarchikus sorrendben)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Storage-fiók
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Fájlmegosztás

Az Azure Storage-ban csak a Storage-fiók használható az áthelyezésre. Az Azure-fájlmegosztás alerőforrásként nem helyezhető át másik Storage-fiókba.

## <a name="supported-combinations"></a>Támogatott kombinációk

Az erőforrás-áthelyezés, a Storage-fiók és a legfelső szintű Azure File Sync erőforrás, a *Storage Sync szolgáltatás* megtervezése során együtt kell figyelembe venni.

Ajánlott eljárásként a Storage Sync szolgáltatást és a fájlmegosztást szinkronizáló Storage-fiókokat mindig ugyanabban az előfizetésben kell tárolni. Ezek a kombinációk támogatottak:

* A Storage Sync szolgáltatás és a Storage-fiókok **különböző erőforráscsoportok** (ugyanabban az Azure-bérlő) találhatók.
* A Storage Sync szolgáltatás és a Storage-fiókok **különböző előfizetésekben** találhatók (azonos Azure-bérlő)

> [!IMPORTANT]
> A különböző mozgatási kombinációk révén a Storage Sync szolgáltatás és a Storage-fiókok különböző előfizetésekben, különböző HRE-bérlők által szabályozva lehetnek. A szinkronizálás úgy tűnik, hogy működik, de ez nem támogatott konfiguráció. A szinkronizálás a jövőben leállhat, és nem tud visszakerülni egy működő feltételre.

Az erőforrás-áthelyezés megtervezése során különböző szempontokat kell figyelembe venni [az azonos HRE-bérlőn belüli áthelyezéshez](#move-within-the-same-azure-active-directory-tenant) és [egy másik HRE-bérlőhöz való](#move-to-a-new-azure-active-directory-tenant)áttéréshez. HRE-bérlők áthelyezésekor a szinkronizálási és tárolási erőforrások mindig együtt mozognak.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Áthelyezés ugyanazon a Azure Active Directory bérlőn belül

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Egy rendszerkép, amely a Storage Sync szolgáltatás erőforrásának Azure Portal jeleníti meg, az áthelyezési paranccsal kibontva. Megjeleníti az erőforráscsoport áthelyezési és előfizetés-áthelyezési beállításait." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        A Storage Sync Service-erőforrások áthelyezésének kényelmes módja a Azure Portal használata. Navigáljon az áthelyezni kívánt Storage Sync szolgáltatáshoz, és válassza az **Áthelyezés** a parancssorból lehetőséget. Ugyanezek a lépések érvényesek a Storage-fiókok áthelyezésére. Ily módon egy erőforráscsoport összes erőforrását is áthelyezheti. A teljes erőforráscsoport áthelyezése akkor ajánlott, ha rendelkezik a Storage Sync szolgáltatással, és az összes használatban lévő Storage-fiókja ebben az erőforráscsoporthoz.
    :::column-end:::
:::row-end:::

> [!WARNING]
> A Storage-fiók erőforrásának áthelyezése után a szinkronizálás azonnal leáll. Az új előfizetéshez tartozó megfelelő Storage-fiókok eléréséhez manuálisan engedélyeznie kell a szinkronizálást. A [Azure file Sync Storage-hozzáférés engedélyezési](#azure-file-sync-storage-access-authorization) szakasza megadja a szükséges lépéseket.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Áthelyezés új Azure Active Directory-bérlőre

Az egyes erőforrások, például a Storage Sync Service vagy a Storage-fiókok, nem helyezhetők át egy másik HRE-bérlőre. Csak az Azure-előfizetések helyezhetik át az HRE-bérlőket. Gondoljon az előfizetési struktúrára az új HRE-bérlőben. A Azure File Synchoz külön előfizetést használhat. 

1. Hozzon létre egy Azure-előfizetést (vagy határozzon meg egy meglévőt az áthelyezni kívánt régi bérlőn).
1. [Hajtson végre egy előfizetést a](#move-within-the-same-azure-active-directory-tenant) Storage Sync szolgáltatás és az összes társított Storage-fiók ugyanazon HRE-bérlőn belül.
1. A szinkronizálás le fog állni. Fejezze be a bérlőt azonnal, vagy [állítsa vissza a szinkronizálási képességet az áthelyezett Storage-fiókok eléréséhez](#azure-file-sync-storage-access-authorization). Ezután később is átléphet az új HRE-bérlőre.

Ha az összes kapcsolódó Azure File Sync erőforrást lefoglalta a saját előfizetésében, készen áll arra, hogy áthelyezze a teljes előfizetést a cél HRE-bérlőre. Az [átvitel előfizetési útmutatója](../../role-based-access-control/transfer-subscription.md) lehetővé teszi az ilyen átvitelek megtervezését és végrehajtását.

> [!WARNING]
> Ha az egyik bérlőről a másikra helyez át egy előfizetést, a szinkronizálás azonnal leáll. Az új előfizetéshez tartozó megfelelő Storage-fiókok eléréséhez manuálisan engedélyeznie kell a szinkronizálást. A [Azure file Sync Storage-hozzáférés engedélyezési](#azure-file-sync-storage-access-authorization) szakasza megadja a szükséges lépéseket.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Egy kép, amely a Azure Portal, az előfizetés áttekintése panelt jeleníti meg, kiemelve a könyvtár módosítása eszköztár parancsát a középpontban, az oldal tetején." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Készen áll az áttelepítés elindítására, ha rendelkezik egy csomaggal és a szükséges engedélyekkel:
        1. A Azure Portal navigáljon az előfizetéshez, az **Áttekintés** panelre.
        1. Válassza a **Címtár módosítása** lehetőséget
        1. Az új HRE-bérlő hozzárendeléséhez kövesse a varázsló lépéseit.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Tároló-hozzáférési engedély Azure File Sync

Ha a Storage-fiókokat új előfizetésre helyezi át, vagy egy előfizetésben egy új Azure Active Directory (HRE) bérlőre helyezi át, a szinkronizálás leáll. Szerepköralapú hozzáférés (RBAC) használatával engedélyezhető Azure File Sync a Storage-fiókhoz való hozzáféréshez, és ezek a szerepkör-hozzárendelések nem települnek át az erőforrásokkal.

### <a name="azure-file-sync-service-principal"></a>Azure File Sync egyszerű szolgáltatásnév

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Egy kép, amely a Azure Portal, az előfizetés kezelése, a regisztrált erőforrás-szolgáltatók." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        Az Azure File Sync egyszerű szolgáltatásnak léteznie kell a HRE-bérlőben, mielőtt engedélyezi a szinkronizálási hozzáférést egy Storage-fiókhoz. </br></br> Ha még ma hoz létre új Azure-előfizetést, a *Microsoft. StorageSync* Azure file Sync erőforrás-szolgáltató automatikusan regisztrálva van az előfizetésében. Az erőforrás-szolgáltató regisztrálása az előfizetést szabályozó Azure Active Directory bérlőben elérhetővé teszi a szinkronizáláshoz szükséges *egyszerű szolgáltatást* . Egy egyszerű szolgáltatásnév hasonló a HRE lévő felhasználói fiókhoz. A Azure File Sync egyszerű szolgáltatásnév használatával engedélyezheti az erőforrásokhoz való hozzáférést szerepköralapú hozzáférés-vezérléssel (RBAC) keresztül. Az egyetlen erőforrás-szinkronizáláshoz hozzá kell férnie a által szinkronizálni kívánt fájlmegosztást tartalmazó Storage-fiókokhoz. A *Microsoft. StorageSync* hozzá kell rendelni a beépített szerepkör **-olvasóhoz és az adateléréshez** a Storage-fiókban. </br></br> Ez a hozzárendelés automatikusan történik a bejelentkezett felhasználó felhasználói környezetében, amikor fájlmegosztást ad hozzá egy szinkronizálási csoporthoz, vagy más szóval létrehoz egy Felhőbeli végpontot. Ha egy Storage-fiók új előfizetésre vagy HRE-bérlőre kerül át, akkor ez a szerepkör-hozzárendelés elvész, és [manuálisan újra kell létrehozni](#establish-sync-access-to-a-storage-account).
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Ha a cél Azure-előfizetés nem lett nemrégiben létrehozva, ellenőrizze, hogy a *Microsoft. StorageSync* erőforrás-szolgáltató regisztrálva van-e az előfizetésben. Ha nem, manuálisan adja hozzá ugyanazon a portálon.

### <a name="establish-sync-access-to-a-storage-account"></a>A Storage-fiókhoz való szinkronizálási hozzáférés létrehozása

A [Azure file Sync egyszerű szolgáltatásnak](#azure-file-sync-service-principal) a szerepköralapú hozzáférés-vezérléssel (RBAC) keresztül kell engedélyeznie a Storage-fiókokhoz való hozzáférést. A *Microsoft. StorageSync* hozzá kell rendelni a beépített szerepkör **-olvasóhoz és az adateléréshez** a Storage-fiókban. 

Ez a hozzárendelés általában automatikusan történik a bejelentkezett felhasználó felhasználói környezetében, amikor fájlmegosztást ad hozzá egy szinkronizálási csoporthoz, vagy más szóval létrehoz egy Felhőbeli végpontot. Ha azonban egy Storage-fiók új előfizetésre vagy HRE-bérlőre vált, a szerepkör-hozzárendelés elvész, és manuálisan újra kell létrehozni.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Egy rendszerképet, amely a Storage-fiók olvasó-és adatelérési szerepköréhez rendelt Microsoft. StorageSync egyszerű szolgáltatást jeleníti meg":::
    :::column-end:::
    :::column:::
        A Azure Portal navigáljon ahhoz a Storage-fiókhoz, amely számára engedélyezni kívánja a szinkronizálási hozzáférést. <ol><li>A bal oldali tartalomjegyzékben válassza a **hozzáférés-vezérlés (iam)** lehetőséget.</li><li>Válassza ki a szerepkör-hozzárendelések lapot a Storage-fiókhoz hozzáférő felhasználók és alkalmazások (egyszerű szolgáltatásnév) listájához.</li><li>Válassza a **Hozzáadás** lehetőséget</li><li>A **szerepkör mezőben** válassza ki az **olvasó és az adathozzáférés** lehetőséget.</li><li>A **Select (kiválasztás) mezőbe** írja be a *Microsoft. StorageSync*, válassza ki a szerepkört, majd kattintson a **Save (Mentés**) gombra.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Áthelyezés másik Azure-régióba

A Azure File Sync erőforrás- *tárolási szinkronizálási szolgáltatás* és a szinkronizálandó fájlmegosztást tartalmazó Storage-fiókok rendelkeznek egy Azure-régióval, amelyet a-ben helyeztek üzembe. A régiót az erőforrások létrehozásakor határozza meg. A Storage Sync szolgáltatás és a Storage-fiók erőforrásainak meg kell egyezniük. Ezek a régiók nem módosíthatók a létrehozásuk után bármelyik erőforrástípus esetében.

Ha egy másik régiót rendel hozzá egy erőforráshoz, az nem azonos a [régió feladatátvételével](#region-fail-over), ami támogatott lehet a Storage-fiók redundancia-beállításaitól függően.

## <a name="region-fail-over"></a>Régió feladatátvétele

Az Azure Storage egy Storage-fiókhoz [kínál geo-redundancia-beállításokat](../common/storage-redundancy.md#geo-redundant-storage) . Ezek a redundancia-beállítások problémákat okozhatnak a Azure File Sync használt Storage-fiókok esetében. Ennek fő oka, hogy a földrajzilag távoli régiók közötti replikálást a Azure File Sync nem hajtja végre, hanem egy, az Azure-beli tárolási alrendszerhez beépített tárolási replikációs technológiával. Nem ismeri az alkalmazás állapotát, és a Azure File Sync egy olyan alkalmazás, amely egy adott pillanatban az Azure-fájlmegosztás és az onnan érkező fájlok között szinkronizál. Ha bármelyik földrajzilag kifizetett tárterület-redundancia lehetőséget választja, akkor az összes adata nem vész el nagy mennyiségű vészhelyzetben. Az [adatvesztést azonban előre](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss)fel kell mérni.

> [!CAUTION]
> A feladatátvétel soha nem megfelelő helyettesítője ahhoz, hogy az erőforrásokat a megfelelő Azure-régióban lehessen kiépíteni. Ha az erőforrások "rossz" régióban vannak, érdemes megfontolnia a szinkronizálás leállítását és a szinkronizálás újbóli beállítását a kívánt régióban üzembe helyezett új Azure-fájlmegosztás számára.

Egy regionális feladatátvételt a Microsoft olyan katasztrofális eseményen is elindíthat, amely egy hosszabb ideig elindított adatközpontokat fog megjeleníteni egy Azure-régióban. Előfordulhat, hogy az üzleti állásidő definíciója kevesebb, mint a Microsoft számára a regionális feladatátvétel megkezdése előtt felkészült idő. Ilyen esetben az [ügyfelek a feladatátvételt is elindítják](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> Feladatátvétel esetén be kell állítania egy támogatási jegyet az érintett Storage Sync Services számára, hogy újra működjön.

## <a name="see-also"></a>Lásd még

- [Az Azure file share és a Sync áttelepítési útmutatók áttekintése](storage-files-migration-overview.md)
- [Azure File Sync – hibaelhárítás](storage-sync-files-troubleshoot.md)
- [Azure File Sync központi telepítésének megtervezése](storage-sync-files-planning.md)
