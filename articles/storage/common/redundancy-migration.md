---
title: Tárfiók replikálási módjának megváltoztatása
titleSuffix: Azure Storage
description: Megtudhatja, hogyan módosíthatja a meglévő tárfiókban lévő adatok replikálása.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eb8bbf852803df53c43cef90bd2229bfcddd60d4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766186"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Tárfiók replikálási módjának megváltoztatása

Az Azure Storage mindig több másolatot tárol az adatokról, így védve vannak a tervezett és nem tervezett eseményektől, beleértve az átmeneti hardverhibákat, a hálózati vagy áramkimaradásokat és a súlyos természeti katasztrófákat. A redundancia biztosítja, hogy a tárfiók még meghibásodások esetén is megfeleljen az Azure Storage szolgáltatói szerződésének [(SLA).](https://azure.microsoft.com/support/legal/sla/storage/)

Az Azure Storage a következő replikációtípusokat kínálja:

- Helyileg redundáns tárolás (LRS)
- Zónaredundáns tárolás (ZRS)
- Georedundáns tárolás (GRS) vagy írási hozzáférésű georedundáns tárolás (RA-GRS)
- Geo-zónaredundáns tárolás (GZRS) vagy íráselérésű geo-zónaredundáns tárolás (RA-GZRS)

Az egyes lehetőségek áttekintését lásd: [Azure Storage redundancia.](storage-redundancy.md)

## <a name="switch-between-types-of-replication"></a>Váltás a replikáció típusai között

Tárfiókot az egyik replikációtípusról bármely más típusra válthat, de néhány forgatókönyv egyszerűbb, mint a többi. Ha georeplikációt vagy olvasási hozzáférést szeretne hozzáadni vagy eltávolítani a másodlagos régióhoz, a Azure Portal, a PowerShell vagy az Azure CLI használatával frissítheti a replikációs beállítást. Ha azonban módosítani szeretné az adatok replikáltságát az elsődleges régióban az LRS-ről ZRS-re vagy fordítva, manuális migrálást kell végrehajtania.

Az alábbi táblázat áttekintést nyújt arról, hogyan válthat az egyes replikációs típusról egy másikra:

| Kapcsol | ... az LRS-be | ... a GRS/RA-GRS-re | ... ZRS-hez | ... GZRS-re/RA-GZRS-re |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... az LRS-ről</b> | N/A | A Azure Portal<sup>1,2</sup> replikációs beállításának módosítása a Azure Portal, a PowerShell vagy a parancssori felület használatával | Manuális migrálás végrehajtása <br /><br /> OR <br /><br /> Élő migrálás kérése | Manuális migrálás végrehajtása <br /><br /> OR <br /><br /> Először váltson GRS/RA-GRS-re, majd igényelj élő migrálást<sup>1</sup> |
| <b>... a GRS/RA-GRS-ről</b> | A Azure Portal, a PowerShell vagy a parancssori felület használatával módosíthatja a replikációs beállítást | N/A | Manuális migrálás végrehajtása <br /><br /> OR <br /><br /> Először váltson LRS-re, majd igényelj élő migrálást | Manuális migrálás végrehajtása <br /><br /> OR <br /><br /> Élő migrálás kérése |
| <b>... ZRS-ről</b> | Manuális migrálás végrehajtása | Manuális migrálás végrehajtása | N/A | Élő migrálás kérése |
| <b>... a GZRS-ről/RA-GZRS-ről</b> | Manuális migrálás végrehajtása | Manuális migrálás végrehajtása | A Azure Portal, a PowerShell vagy a parancssori felület használatával módosíthatja a replikációs beállítást | N/A |

<sup>1</sup> A ki- és beáramlott forgalomért egyszer kell díjat fizetni.<br />
<sup>2</sup> Az LRS-ről GRS-re való migrálás nem támogatott, ha a tárfiók archív szinten tartalmaz blobokat.<br />
<sup>3</sup> A ZRS-ről GZRS-re/RA-GZRS-re való átalakítás vagy fordítva nem támogatott a következő régiókban: USA 2. keleti régiója, USA keleti régiója, Nyugat-Európa.

> [!CAUTION]
> Ha fiók-feladatátvételt végzett az (RA-)GRS vagy (RA-)GZRS fiókhoz, a fiók helyileg redundáns (LRS) lesz az új elsődleges régióban a feladatátvétel után. [](storage-disaster-recovery-guidance.md) A feladatátvételből eredő LRS-fiók ZRS-be vagy GZRS-be való élő áttelepítése nem támogatott. Ez még az úgynevezett feladat-visszavételi műveletek esetében is igaz. Ha például végrehajt egy fiók feladatátvételét az RA-GZRS-ről a másodlagos régióban található LRS-re, majd újra ra-GRS-re konfigurálja, és újabb fiók-feladatátvételt hajt végre az eredeti elsődleges régióba, nem tud kapcsolatba lépni az eredeti élő áttelepítés támogatással az elsődleges régióban található RA-GZRS-re. Ehelyett manuális migrálást kell végrehajtania a ZRS-be vagy a GZRS-be.

## <a name="change-the-replication-setting"></a>A replikációs beállítás módosítása

A tárfiókok replikációs beállításának módosításához használhatja a Azure Portal, a PowerShellt vagy az Azure CLI-t, ha nem módosítja az adatok replikálási mikéntét az elsődleges régióban. Ha az elsődleges régióban található LRS-ről ZRS-re migrál az elsődleges régióban, vagy fordítva, manuális vagy élő áttelepítést kell végrehajtania.

A tárfiók replikálásának módosítása nem eredményez időt az alkalmazások számára.

# <a name="portal"></a>[Portál](#tab/portal)

Ha módosítania kell a tárfiók redundancia-beállítását a Azure Portal kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. Válassza a **Konfiguráció** beállítást.
1. Frissítse a **Replikáció** beállítást.

![A replikációs beállítás portálon történő beállítását bemutató képernyőkép](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha módosítania kell a tárfiók redundancia-beállítását a PowerShell használatával, hívja meg a [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot, és adja meg a `-SkuName` paramétert:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha módosítania kell a tárfiók redundancia-beállítását az Azure CLI-val, hívja meg [az az storage account update](/cli/azure/storage/account#az_storage_account_update) parancsot, és adja meg a `--sku` paramétert:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Manuális migrálás végrehajtása ZRS-re, GZRS-re vagy RA-GZRS-re

Ha módosítani szeretné a tárfiókban található adatok replikálási mikéntét az elsődleges régióban az LRS-ről ZRS-re vagy fordítva, manuális migrálást is végrehajthat. A manuális migrálás az élő migrálásnál nagyobb rugalmasságot biztosít. Ön szabályozhatja a manuális migrálás időzítését, ezért ezt a beállítást akkor használja, ha a migrálást egy adott dátumig kell végrehajtania.

Ha az elsődleges régióban manuális migrálást végez az LRS-ről a ZRS-re, vagy fordítva, a cél tárfiók georedundáns lehet, és konfigurálható a másodlagos régióhoz való olvasási hozzáférésre is. Egy lépéssel például egy LRS-fiókot egy GZRS- vagy RA-GZRS-fiókba mihet át.

A manuális migrálás az alkalmazás leállását eredményezheti. Ha az alkalmazás magas rendelkezésre állást igényel, a Microsoft élő migrálási lehetőséget is biztosít. Az élő migrálás egy állásidő nélküli, helyszíni migrálás.

Manuális migrálás esetén átmásolja az adatokat a meglévő tárfiókból egy új tárfiókba, amely ZRS-t használ az elsődleges régióban. Manuális áttelepítés végrehajtásához az alábbi lehetőségek egyikét használhatja:

- Az adatok másolása egy meglévő eszközzel, például az AzCopyval, az Azure Storage egyik ügyfélkódtárával vagy egy megbízható külső eszközzel.
- Ha ismeri a Hadoopot vagy a HDInsightot, a forrás tárfiókot és a cél tárfiókot is csatolhatja a fürthöz. Ezután párhuzamosíthatja az adatmásoló folyamatot egy olyan eszközzel, mint a DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Élő migrálás kérése ZRS-re, GZRS-re vagy RA-GZRS-re

Ha tárfiókját LRS-ről ZRS-be kell migrálnia az elsődleges régióban, alkalmazás-állásidő nélkül, élő migrálást kérhet a Microsofttól. Az LRS-ről GZRS-re vagy RA-GZRS-re való migráláshoz először váltson GRS-re vagy RA-GRS-re, majd kérjen élő migrálást. Hasonlóképpen kérhet élő migrálást GRS-ről vagy RA-GRS-ről GZRS-re vagy RA-GZRS-re. A GRS-ről vagy az RA-GRS-ről ZRS-re való migráláshoz először váltson LRS-re, majd kérjen élő migrálást.

Az élő áttelepítés során a tárfiókban a tartósság és a rendelkezésre állás elvesztése nélkül férhet hozzá az adatokhoz. Az Azure Storage SLA-t a migrálási folyamat során fenntartjuk. Az élő migrálás nem jár adatvesztéssel. A szolgáltatásvégpontokat, a hozzáférési kulcsokat, a közös hozzáférésű jogosultságokat és az egyéb fiókbeállításokat a migrálás után is változatlanul hagyja.

A ZRS csak az általános célú v2-fiókokat támogatja, ezért mindenképpen frissítse a tárfiókot, mielőtt kérelmet küld a ZRS-be való élő migrálásra. További információ: [Frissítés általános célú v2 tárfiókra.](storage-account-upgrade.md) A tárfióknak élő áttelepítéssel migrálható adatokat kell tartalmaznia.

Az élő áttelepítés csak az LRS- vagy GRS-replikációt támogató tárfiókok esetében támogatott. Ha a fiókja RA-GRS-t használ, a folytatás előtt módosítania kell a fiók replikációs típusát LRS-re vagy GRS-re. Ez a köztes lépés eltávolítja az RA-GRS által a migrálás előtt biztosított, csak olvasható másodlagos végpontot.

Bár a Microsoft azonnal kezeli az élő migrálási kérelmet, a migrálás befejezésének időpontjára nem vállal garanciát. Ha egy adott időpontig szükséges az adatok ZRS-re való migrálása, a Microsoft a manuális migrálás végrehajtását javasolja. Minél több adat található a fiókban, általában annál tovább tart az adatok migrálása.

Manuális áttelepítést kell végrehajtania, ha:

- Az adatokat egy, a forrásfióktól eltérő régióban található ZRS-tárfiókba szeretné átemelni.
- A tárfiók prémium szintű lapblob- vagy blokkblobfiók.
- Az adatokat ZRS-ről LRS-re, GRS-re vagy RA-GRS-re szeretné migrálni.
- A tárfiók az archív szinten található adatokat is tartalmaz.

Élő áttelepítést kérhet a Azure ügyfélszolgálata [portálon.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 

> [!IMPORTANT]
> Ha egynél több tárfiókot kell milaniához, hozzon létre egyetlen támogatási jegyet, és adja meg az átalakítani kívánt fiókok nevét a **Részletek lapon.**

Élő migrálás kérése érdekében kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg az átemelni kívánt tárfiókot.
1. A **Támogatás + hibaelhárítás alatt válassza** az Új támogatási kérelem **lehetőséget.**
1. Töltse ki **az Alapvető beállítások** lapot a fiók adatai alapján:
    - **Probléma típusa:** Válassza a **Technikai lehetőséget.**
    - **Szolgáltatás:** Válassza a **Saját szolgáltatások,** majd a **Tárfiók kezelése lehetőséget.**
    - **Erőforrás:** Válassza ki az átemelni kívánt tárfiókot. Ha több tárfiókot kell megadnia, ezt a Részletek **szakaszban használhatja.**
    - **Probléma típusa:** Válassza az **Adatáttelepítés lehetőséget.**
    - **Probléma altípusa:** Válassza az **Áttelepítés ZRS-re, GZRS-re vagy RA-GZRS-re lehetőséget.**

    :::image type="content" source="media/redundancy-migration/request-live-migration-basics-portal.png" alt-text="Képernyőkép az élő migrálás kérésének – Alapvető beállítások lapról":::

1. Kattintson a **Tovább** gombra. A **Megoldások lapon** ellenőrizheti a tárfiókok migrálásra való jogosultságát.
1. Kattintson a **Tovább** gombra. Ha egynél több tárfiókot kell átemelni, akkor a Részletek lapon adja meg az egyes fiókok nevét pontosvesszővel elválasztva. 

    :::image type="content" source="media/redundancy-migration/request-live-migration-details-portal.png" alt-text="Képernyőkép az élő migrálás kérelmezésről – Részletek lap":::

1. Töltse ki a további szükséges adatokat a **Részletek** lapon, majd válassza az Áttekintés **+ létrehozás** lehetőséget a támogatási jegy áttekintéséhez és elküldéhez. Egy támogatási személy kapcsolatba lép Önnel, hogy segítséget nyújtson, amire szüksége lehet.

> [!NOTE]
> A prémium szintű fájlmegosztások (FileStorage-fiókok) csak LRS-hez és ZRS-hez érhetők el.
>
> A GZRS-tárfiókok jelenleg nem támogatják az archív szintet. További [részletek: Azure Blob Storage: hot, cool, and archive access tiers (Azure Blob Storage: a gyors,](../blobs/storage-blob-storage-tiers.md) a elérésű és az archív hozzáférési szintek).
>
> A felügyelt lemezek csak LRS-hez érhetők el, és nem migrálhatóak ZRS-be. A standard felügyelt SSD-lemezek pillanatképei és rendszerképei standard HDD-tárolón tárolhatók, és az LRS és a [ZRS lehetőségek](https://azure.microsoft.com/pricing/details/managed-disks/)közül választhat. További információ a rendelkezésre állási készletekkel való integrációról: Bevezetés az [Azure felügyelt lemezeibe.](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets)

## <a name="switch-from-zrs-classic"></a>Váltás a klasszikus ZRS-ről

> [!IMPORTANT]
> A Microsoft 2021. március 31-én kiveszi és átemeli a klasszikus ZRS-fiókokat. Az elarazás előtt további részleteket biztosítanak a ZRS klasszikus ügyfeleinek.
>
> Miután a ZRS általánosan elérhetővé válik egy adott régióban, az ügyfelek nem hozhatnak létre klasszikus ZRS-fiókokat a Azure Portal régióban. Klasszikus ZRS-fiókok létrehozása a Microsoft PowerShell és az Azure CLI használatával lehetőség mindaddig, amíg a klasszikus ZRS el nem elavult. A ZRS rendelkezésre álló helyének információiért lásd: [Azure Storage redundancia.](storage-redundancy.md)

A klasszikus ZRS aszinkron módon replikálja az adatokat egy vagy két régió adatközpontja között. Előfordulhat, hogy a replikált adatok csak akkor érhetők el, ha a Microsoft feladatátvételt kezdeményez a másodlagos adatbázisba. A klasszikus ZRS-fiók nem konvertálható LRS, GRS vagy RA-GRS formátumra vagy onnan másra. A klasszikus ZRS-fiókok nem támogatják a metrikákat és a naplózást sem.

A klasszikus ZRS csak az általános célú V1(GPv1) tárfiókok **blokkblobjai** esetében érhető el. A tárfiókokkal kapcsolatos további információkért lásd: [Az Azure Storage-fiók áttekintése.](storage-account-overview.md)

A ZRS-fiókadatok LRS-, GRS-, RA-GRS- vagy klasszikus ZRS-fiókba vagy onnan való manuális áttelepítéséhez használja az alábbi eszközök egyikét: AzCopy, Azure Storage Explorer, PowerShell vagy Azure CLI. Saját migrálási megoldást is létrehozhat az Azure Storage egyik ügyfélkódtárával.

Klasszikus ZRS-tárfiókját is frissítheti ZRS-fiókra a Azure Portal, a PowerShell vagy az Azure CLI használatával olyan régiókban, ahol a ZRS elérhető.

# <a name="portal"></a>[Portál](#tab/portal)

A ZRS-Azure Portal nyissa meg a fiók  konfigurációs beállításait, és válassza a Frissítés **lehetőséget:**

![Frissítés klasszikus ZRS-ről ZRS-re a portálon](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A ZRS-re a PowerShell használatával való frissítéshez hívja meg a következő parancsot:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A ZRS-re az Azure CLI használatával való frissítéshez hívja a következő parancsot:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Az adatok replikálásának módosításával kapcsolatos költségek

Az adatok replikálási folyamatának módosításával járó költségek a konverziós útvonaltól függenek. Az Azure Storage redundanciaajánlatai közé tartozik az LRS, a ZRS, a GRS, az RA-GRS, a GZRS és az RA-GZRS.

Ha például  az LRS-ről bármely más típusú replikációra lép, az további költségeket fog fizetni, mivel egy kifinomultabb redundanciaszintre lép át. A  GRS-re vagy RA-GRS-re való migrálás a bejövő sávszélességgel kapcsolatos díjat fog fizetni, mert az adatok (az elsődleges régióban) replikálva vannak a távoli másodlagos régióba. Ez egy egyszeres költség a kezdeti beállításkor. Az adatok másolása után nem kell további migrálási díjakat fizetni. A sávszélességgel kapcsolatos díjakról további információt az [Azure Storage díjszabását tartalmazó oldalon talál.](https://azure.microsoft.com/pricing/details/storage/blobs/)

Ha a tárfiókot GRS-ről LRS-re migrálja, nincs további költség, de a replikált adatok törlődnek a másodlagos helyről.

> [!IMPORTANT]
> Ha tárfiókját RA-GRS-ről GRS-re vagy LRS-re migrálta, a fiók ra-GRS formátumban lesz kiszámlázva az átalakítás dátumán túl még 30 napig.

## <a name="see-also"></a>Lásd még

- [Redundancia az Azure Storage szolgáltatásban](storage-redundancy.md)
- [Tárfiók Utolsó szinkronizálás ideje tulajdonságának ellenőrzése](last-sync-time-get.md)
- [Georedundania használata magas rendelkezésre állású alkalmazások tervezéséhez](geo-redundant-design.md)
