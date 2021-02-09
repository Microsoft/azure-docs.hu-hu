---
title: BLOB verziószámozása
titleSuffix: Azure Storage
description: A blob Storage verziószámozása automatikusan fenntartja az objektumok korábbi verzióit, és az időbélyegekkel azonosítja azokat. A Blobok korábbi verzióit visszaállíthatja az adatok helyreállításához, ha az hibásan módosul vagy törölve lett.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 267fd57b2fd359a73d5c1e01568aba14594e9290
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980277"
---
# <a name="blob-versioning"></a>BLOB verziószámozása

A blob Storage verziószámozásának engedélyezésével automatikusan megtarthatja az objektumok korábbi verzióit.  Ha a blob verziószámozása engedélyezve van, visszaállíthatja a blob egy korábbi verzióját az adatok helyreállításához, ha az hibásan van módosítva vagy törölve.

A blob verziószámozása engedélyezve van a Storage-fiókban, és a Storage-fiókban lévő összes blobra érvényes. Miután engedélyezte a blob verziószámozását egy Storage-fiókhoz, az Azure Storage automatikusan megőrzi a Storage-fiókban lévő összes blob verzióját.

A Microsoft azt javasolja, hogy a Blobok verziószámozásával fenntartsa a blob korábbi verzióit a kiváló adatvédelem érdekében. Ha lehetséges, a blob-Pillanatképek helyett használjon blob-verziószámozást a korábbi verziók karbantartásához. A blob-Pillanatképek hasonló funkciókat biztosítanak a Blobok korábbi verzióinak fenntartásához, de a pillanatképeket az alkalmazásnak manuálisan kell megtartania.

A blob verziószámozásának engedélyezéséről a [blob verziószámozásának engedélyezése és kezelése](versioning-enable.md)című témakörben olvashat bővebben.

> [!IMPORTANT]
> A blob verziószámozása nem segít helyreállítani egy Storage-fiók vagy-tároló véletlen törlését. A Storage-fiók véletlen törlésének megelőzése érdekében állítson be egy **CannotDelete** -zárolást a Storage-fiók erőforrásán. Az Azure-erőforrások zárolásával kapcsolatos további információkért lásd: [erőforrások zárolása a váratlan változások megelőzése érdekében](../../azure-resource-manager/management/lock-resources.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-versioning-works"></a>A blob-verziószámozás működése

A verzió rögzíti egy blob állapotát egy adott időpontban. Ha a blob verziószámozása engedélyezve van egy Storage-fiókhoz, az Azure Storage automatikusan létrehoz egy blob új verzióját a blob módosításának vagy törlésének minden egyes időpontjában.

Ha olyan blobot hoz létre, amelyen engedélyezve van a verziószámozás, az új blob a blob aktuális verziója (vagy az alap blob). Ha ezt követően módosítja a blobot, az Azure Storage egy olyan verziót hoz létre, amely rögzíti a blob állapotát a módosítás előtt. A módosított blob lesz az új aktuális verzió. Minden alkalommal létrejön egy új verzió, amikor módosítja a blobot.

Az alábbi ábra bemutatja, hogyan jönnek létre a verziók az írási és törlési műveletekben, és hogyan lehet a korábbi verziót az aktuális verzióra előléptetni:

:::image type="content" source="media/versioning-overview/blob-versioning-diagram.png" alt-text="A blob verziószámozásának működését bemutató ábra":::

Ha a Blobok egy része nagy számú verziót használ, növelheti a Blobok listázási műveleteinek késését. A Microsoft a Blobok kevesebb mint 1000 verzióját javasolja. Az életciklus-kezelési szolgáltatással automatikusan törölheti a régi verziókat. Az életciklus-kezeléssel kapcsolatos további információkért lásd: [a költségek optimalizálása az Azure Blob Storage hozzáférési szintjeinek automatizálásával](storage-lifecycle-management-concepts.md).

Ha olyan blobot töröl, amelyen engedélyezve van a verziószámozás, az Azure Storage egy olyan verziót hoz létre, amely rögzíti a blob állapotát a törlés előtt. Ezután törli a blob aktuális verzióját, de a blob verziója továbbra is fennmarad, így szükség esetén újra létrehozható. 

A blob-verziók nem változtathatók meg. Egy meglévő blob-verzió tartalmát vagy metaadatait nem módosíthatja.

A blob verziószámozása az általános célú v2, a blob és a blob Storage-fiókok esetében érhető el. A Azure Data Lake Storage Gen2-mel való használatra engedélyezett hierarchikus névtérrel rendelkező Storage-fiókok jelenleg nem támogatottak.

Az Azure Storage REST API 2019-10-10-es és újabb verziója támogatja a blob verziószámozását.

### <a name="version-id"></a>Verzióazonosító

Minden blob-verziót egy verzióazonosító azonosít. A verzióazonosító értéke a blob írásának vagy frissítésének időbélyegzője. A verzió AZONOSÍTÓját a rendszer a verzió létrehozásakor rendeli hozzá.

A blob egy adott verziójához olvasási vagy törlési műveleteket is végrehajthat, ha megadja annak verziószámát. Ha kihagyja a verzióazonosító AZONOSÍTÓját, a művelet az aktuális verzióra (az alap blobra) kerül.

Amikor írási műveletet hív meg egy blob létrehozásához vagy módosításához, az Azure Storage az *x-MS-Version-ID* fejlécet adja vissza a válaszban. Ez a fejléc tartalmazza az írási művelet által létrehozott blob aktuális verziójának verziószámát.

A verzió-azonosító a verzió élettartama esetén azonos marad.

### <a name="versioning-on-write-operations"></a>Verziószámozás írási műveleteken

Ha be van kapcsolva a blob verziószámozása, a Blobok minden írási művelete új verziót hoz létre. Az írási műveletek közé tartozik a [blob elhelyezése](/rest/api/storageservices/put-blob), a [letiltási lista](/rest/api/storageservices/put-block-list), a [blob másolása](/rest/api/storageservices/copy-blob)és a [blob metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata).

Ha az írási művelet egy új blobot hoz létre, akkor az eredményül kapott blob a blob aktuális verziója. Ha az írási művelet módosítja egy meglévő blobot, akkor az új adatrögzítés a frissített blobban történik, amely a jelenlegi verzió, az Azure Storage pedig egy olyan verziót hoz létre, amely menti a blob korábbi állapotát.

Az egyszerűség kedvéért a cikkben látható ábrák egyszerű egész értékként jelenítik meg a verzió AZONOSÍTÓját. A valóságban a verzió-azonosító egy időbélyeg. Az aktuális verzió kék színnel jelenik meg, a korábbi verziók pedig szürkén jelennek meg.

Az alábbi ábrán látható, hogy az írási műveletek milyen hatással vannak a blob-verziókra. BLOB létrehozásakor a blob az aktuális verzió. Ha ugyanezt a blobot módosítják, a rendszer létrehoz egy új verziót a blob korábbi állapotának mentéséhez, és a frissített blob lesz az aktuális verzió.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="Azt bemutató ábra, hogy az írási műveletek milyen hatással vannak a verziószámozásra.":::

> [!NOTE]
> Egy olyan blob, amely a verziószámozás előtt lett létrehozva, a Storage-fiókhoz nem tartozik verzióazonosító. A blob módosításakor a módosított blob az aktuális verzió lesz, és a rendszer létrehoz egy verziót, amely a blob állapotát a frissítés előtt menti. A verzióhoz hozzá van rendelve egy verzióazonosító, amely a létrehozási ideje.

### <a name="versioning-on-delete-operations"></a>Verziószámozás a törlési műveleteken

Amikor töröl egy blobot, a blob aktuális verziója egy korábbi verzió lesz, és a rendszer törli az alap blobot. A blob törlését követően a blob összes korábbi verziója megőrződik.

A [blob törlési](/rest/api/storageservices/delete-blob) műveletének meghívása a verzió-azonosító nélkül törli az alap blobot. Egy adott verzió törléséhez adja meg a verzió AZONOSÍTÓját a törlési művelethez.

A következő ábrán látható, hogy a törlési művelet milyen hatással van egy verzióval ellátott blobra:

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="A verziószámmal rendelkező blob törlését bemutató ábra.":::

Ha új adatmennyiséget ír a blobba, a a blob új verzióját hozza létre. A meglévő verziók nem érintettek, ahogy az a következő ábrán is látható.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="A verzió törlését követően a verziószámozás újbóli létrehozását bemutató ábra.":::

### <a name="blob-types"></a>Blobtípusok

Ha a blob verziószámozása engedélyezve van egy Storage-fiókhoz, a blokk Blobok összes írási és törlési művelete egy új verzió létrehozását indítja el, kivéve a [put blokk](/rest/api/storageservices/put-block) műveletet.

A blobok és a hozzáfűző Blobok esetében csak az írási és törlési műveletek egy részhalmaza indítja el a verzió létrehozását. Ezek a műveletek a következők:

- [Put Blob](/rest/api/storageservices/put-blob)
- [Tiltási lista](/rest/api/storageservices/put-block-list)
- [Delete Blob](/rest/api/storageservices/delete-blob)
- [BLOB metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata)
- [Copy Blob](/rest/api/storageservices/copy-blob)

A következő műveletek nem indítják el új verzió létrehozását. A műveletek változásainak rögzítéséhez készítsen manuális pillanatképet:

- [Put oldal](/rest/api/storageservices/put-page) (oldal blobja)
- [Blokk hozzáfűzése](/rest/api/storageservices/append-block) (blob hozzáfűzése)

A Blobok összes verziójának azonos típusú blobnak kell lennie. Ha egy blob korábbi verziókkal rendelkezik, nem írhatja felül az egyik típusú blobot egy másik típussal, kivéve, ha először törli a blobot és annak összes verzióját.

### <a name="access-tiers"></a>Hozzáférési szintek

Egy blokk blob bármely verzióját, beleértve a jelenlegi verziót is, áthelyezheti egy másik blob-hozzáférési szintjére, ha meghívja a [blob-rétegek beállítása](/rest/api/storageservices/set-blob-tier) műveletet. Az alacsonyabb kapacitás díjszabásának kihasználásával kihasználhatja a Blobok régebbi verzióit a lassú vagy az archív szintre. További információkért lásd [: Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md).

A blokkos Blobok megfelelő szintjére való áthelyezésének automatizálásához használja a blob életciklus-kezelését. A életciklus-kezeléssel kapcsolatos további információkért lásd: [Az Azure Blob Storage életciklusának kezelése](storage-lifecycle-management-concepts.md).

## <a name="enable-or-disable-blob-versioning"></a>BLOB verziószámozásának engedélyezése vagy letiltása

A blob verziószámozásának engedélyezéséről és letiltásáról a [blob verziószámozásának engedélyezése és kezelése](versioning-enable.md)című témakörben olvashat bővebben.

A blob verziószámozásának letiltása nem törli a meglévő blobokat, verziókat vagy pillanatképeket. Ha kikapcsolja a blob verziószámozását, a meglévő verziók továbbra is elérhetők maradnak a Storage-fiókban. A későbbiekben nem jönnek létre új verziók.

Ha a Storage-fiókban a verziószámozás letiltása vagy módosítása után lett létrehozva vagy módosítva, a blob felülírása új verziót hoz létre. A frissített blob már nem az aktuális verzió, és nem rendelkezik verziószámmal. A blob összes további frissítése felülírja az adatforrást az előző állapot mentése nélkül.

A verziószám letiltását követően a verzió AZONOSÍTÓjának használatával olvashatja vagy törölheti a verziókat. A Blobok verzióinak listázása a verziószámozás után is letiltható.

A következő ábra azt mutatja be, hogyan lehet a blobokat a verziószámozást követően letiltani, mert egy nem verziószámú blobot hoz létre. A blobhoz társított meglévő verziók megmaradnak.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="Az alapszintű blob a verziószámozást követően módosított ábráját mutatja.":::

## <a name="blob-versioning-and-soft-delete"></a>BLOB verziószámozása és a Soft delete

A blob verziószámozása és a blob Soft delete együttműködve biztosítja az optimális adatvédelmet. Ha engedélyezi a helyreállítható törlést, megadhatja, hogy az Azure Storage mennyi ideig őrizze meg a törölt blobokat. A rendszer minden olyan helyreállított blob-verziót megtart a rendszeren, amely nem törölhető a törlés utáni megőrzési időszakon belül. A blob Soft delete szolgáltatással kapcsolatos további információkért lásd az [Azure Storage-Blobok helyreállítható törlését](./soft-delete-blob-overview.md)ismertető témakört.

### <a name="deleting-a-blob-or-version"></a>BLOB vagy verzió törlése

A Soft delete további védelmet biztosít a blob-verziók törléséhez. Ha a Storage-fiókban mind a verziószámozás, mind a Soft delete engedélyezve van, akkor a blob törlésekor az Azure Storage egy új verziót hoz létre, amely azonnal menti a blob állapotát a törlés előtt, és törli az aktuális verziót. Az új verzió nem törlődik, és a rendszer nem távolítja el, ha a helyreállítható törlés megőrzési ideje lejár.

Ha törli a blob egy korábbi verzióját, a verzió nem törlődik. A rendszer megőrzi a helyreállított verziót a Storage-fiókhoz tartozó helyreállítható törlési beállításokban megadott megőrzési időszakon belül, és véglegesen törölve lesz, ha a helyreállítható törlés megőrzési ideje lejár.

A blob egy korábbi verziójának eltávolításához explicit módon törölje azt a verzió AZONOSÍTÓjának megadásával.

Az alábbi ábrán látható, hogy mi történik a Blobok vagy a Blobok verziójának törlésekor.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="A verzió törlését bemutató ábra, amely lehetővé teszi, hogy a helyreállított törlés engedélyezve legyen.":::

Ha mind a verziószámozás, mind a Soft delete engedélyezve van egy Storage-fiókon, akkor a blob vagy blob verziójának módosításakor és törlésekor nem jön létre helyreállított pillanatkép.

### <a name="restoring-a-soft-deleted-version"></a>A nem törölt verziók visszaállítása

A helyreállítható blob-verziót visszaállíthatja úgy, hogy a (z) verzióban meghívja a [blob törlésének](/rest/api/storageservices/undelete-blob) visszavonása műveletet, miközben a helyreállítható törlés megőrzési ideje érvényben van. A **blob törlésének** visszavonása művelet visszaállítja a blob összes nem törölt verzióját.

A nem **törölhető blob** művelettel rendelkező, helyreállított verziók visszaállítása nem nyújt semmilyen verziót az aktuális verzióra. Az aktuális verzió visszaállításához először állítsa vissza az összes helyreállítatlan verziót, majd a blob [másolása](/rest/api/storageservices/copy-blob) művelettel másolja át az előző verziót a blob visszaállításához.

Az alábbi ábra bemutatja, hogyan állíthatja vissza a nem törölt blob-verziókat a **blob törlésével** , és hogyan állíthatja vissza a blob aktuális verzióját a blob **másolása** művelettel.

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="A Soft-Deleted verziók visszaállítását bemutató ábra.":::

A helyreállítható törlés megőrzési időtartamának lejárta után a rendszer véglegesen törli az összes helyreállított blob-verziót.

## <a name="blob-versioning-and-blob-snapshots"></a>BLOB-verziószámozás és blob-Pillanatképek

A blob-pillanatkép egy adott időpontban végrehajtott blob írásvédett példánya. A blob-Pillanatképek és a blob-verziók hasonlóak, de az Ön vagy az alkalmazása manuálisan hozza létre a pillanatképeket, míg a blob-verziók automatikusan létrejönnek egy írási vagy törlési művelet során, amikor a blob Verziószámozás engedélyezve van a Storage-fiókhoz.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a Blobok verziószámozásának engedélyezése után is frissítse az alkalmazást, hogy leállítsa a pillanatképek letiltását. Ha a Verziószámozás engedélyezve van a Storage-fiókhoz, a rendszer a Blobok frissítését és törlését minden blokkban rögzíti és megőrzi. A pillanatképek készítése nem nyújt további védelmet a blokk blob-adataihoz, ha engedélyezve van a blob verziószámozása, és növelheti a költségeket és az alkalmazások összetettségét.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>BLOB pillanatképének engedélyezése, ha a Verziószámozás engedélyezve van

Habár nem ajánlott, pillanatképet készíthet egy olyan blobról, amely szintén verziója is. Ha nem tudja frissíteni az alkalmazást a Blobok pillanatképének leállításához a verziószámozás engedélyezésekor, az alkalmazás képes támogatni a pillanatképeket és a verziókat is.

Amikor pillanatképet készít egy verzióval ellátott blobról, a pillanatkép létrehozásakor egy új verzió jön létre. Pillanatkép készítésekor a rendszer új aktuális verziót is létrehoz.

Az alábbi ábrán látható, hogy mi történik, amikor pillanatképet készít egy verzióval ellátott blobról. A diagramon a blob-verziók és-Pillanatképek a 2-es és 3-as AZONOSÍTÓJÚ változattal azonos adathalmazt tartalmaznak.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="Egy verzióval ellátott blob pillanatképeit bemutató ábra.":::

## <a name="authorize-operations-on-blob-versions"></a>Műveletek engedélyezése blob-verziókon

Az alábbi módszerek egyikével engedélyezheti a blob-verziókhoz való hozzáférést:

- Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával engedélyeket adhat egy Azure Active Directory (Azure AD) rendszerbiztonsági tag számára. A Microsoft az Azure AD használatát javasolja a kiváló biztonság és a könnyű használat érdekében. További információ az Azure AD és a blob-műveletek használatáról: a Blobok [és várólisták hozzáférésének engedélyezése Azure Active Directory használatával](../common/storage-auth-aad.md).
- Közös hozzáférésű aláírás (SAS) használatával a blob-verziókhoz való hozzáférés delegálására. Adja meg az aláírt erőforrástípus verziószámát `bv` , amely a blob verziószámát jelöli, hogy egy adott verzió műveleteihez hozzon létre egy sas-jogkivonatot. A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md).
- A fiók-hozzáférési kulcsok használatával engedélyezheti a megosztott kulccsal rendelkező blob-verziók műveleteit. További információ: [Engedélyezés megosztott kulccsal](/rest/api/storageservices/authorize-with-shared-key).

A blob verziószámozása úgy lett kialakítva, hogy az adatok véletlen vagy rosszindulatú törléssel védve legyenek. A védelem fokozása érdekében a Blobok verziójának törléséhez speciális engedélyek szükségesek. A következő szakaszok ismertetik a blob-verziók törléséhez szükséges engedélyeket.

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>Azure RBAC művelet blob-verzió törléséhez

A következő táblázat bemutatja, hogy mely Azure RBAC-műveletek támogatják a Blobok vagy a Blobok verzióinak törlését.

| Description | Blob service művelet | Az Azure RBAC-adatművelet szükséges | Azure beépített szerepkör-támogatás |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| A blob aktuális verziójának törlése | Delete Blob | **Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/delete** | Storage-blobadatok közreműködője |
| Verzió törlése | Delete Blob | **Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/deleteBlobVersion/Action** | Storage-blobadatok tulajdonosa |

### <a name="shared-access-signature-sas-parameters"></a>Közös hozzáférésű aláírás (SAS) paraméterei

A blob verziójának aláírt erőforrása: `bv` . További információ: [Service sas létrehozása](/rest/api/storageservices/create-service-sas) vagy [felhasználói delegálási sas létrehozása](/rest/api/storageservices/create-user-delegation-sas).

A következő táblázat a blob-verziók törléséhez szükséges engedélyeket mutatja be az SAS-ben.

| **Engedély** | **URI-szimbólum** | **Engedélyezett műveletek** |
|----------------|----------------|------------------------|
| Törlés         | x              | BLOB verziójának törlése. |

## <a name="pricing-and-billing"></a>Árak és számlázás

A blob verziószámozásának engedélyezése további adattárolási díjat eredményezhet a fiókhoz. Az alkalmazás tervezésekor fontos tisztában lennie azzal, hogy ezek a díjak hogyan merülhetnek fel, így csökkentheti a költségeket.

A blob-verziók (például a blob-Pillanatképek) számlázása az aktív adatforgalommal megegyező sebességgel történik. A verziók számlázásának módja attól függ, hogy explicit módon beállította-e a szintet az alap blobhoz vagy valamelyik verzióhoz (vagy pillanatképekhez). A blob-rétegekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyors, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md).

Ha nem módosította a blobot vagy a verzióhoz tartozó szintet, akkor a blob, annak verziói és az esetlegesen elérhető Pillanatképek egyedi adattömbökért kell fizetnie. További információ: számlázás, [Ha a blob-szintet nem adta meg explicit módon](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Ha módosította a blob vagy a verzió szintjét, akkor a teljes objektumért díjat számítunk fel, függetlenül attól, hogy a blob és a verziószáma is ugyanabban a szinten van-e. További információ: számlázás, [Ha a blob szintjét explicit módon állították be](#billing-when-the-blob-tier-has-been-explicitly-set).

> [!NOTE]
> A gyakran felülírt adatok verziószámozásának engedélyezése nagyobb tárolási kapacitást eredményezhet, és nagyobb késéssel jár a listázási műveletek során. A probléma enyhítése érdekében a gyakran felülírt, különálló Storage-fiókban tárolja a letiltott verziókat.

A blob-Pillanatképek számlázási adataival kapcsolatos további információkért lásd: [blob-Pillanatképek](snapshots-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Számlázás, ha a blob szintje nincs explicit módon beállítva

Ha nem állította be explicit módon a blob rétegét egy alapblobhoz vagy annak bármelyik verziójához, akkor a blobban, annak verzióiban, valamint az esetlegesen megjelenő pillanatképekben szereplő egyedi blokkokért vagy lapokért kell fizetnie. A blobokban megosztott és a hozzájuk tartozó verziókra csak egyszer kerül sor. Ha egy blob frissül, akkor az alap blobban lévő adatok eltérnek a verziójában tárolt adatoktól, és az egyedi adatok egy blokkon vagy oldalon vannak felszámítva.

Ha egy blokkon belüli blokkot cserél le, a rendszer ezt a blokkot egy egyedi blokkként számítja fel. Ez akkor is igaz, ha a blokk ugyanazzal az AZONOSÍTÓval és ugyanazzal az adattal rendelkezik, mint az előző verzióban. Miután a blokk újra véglegesítve lett, az a korábbi verzióban lévőtől eltér, és az adatokért kell fizetnie. Ugyanez a helyzet igaz egy olyan oldal blobján, amely azonos adattal frissült.

A blob Storage nem rendelkezik annak megállapításához, hogy két blokk tartalmaz-e azonos adathalmazt. Minden feltöltött és véglegesített blokk egyediként lesz kezelve, még akkor is, ha ugyanazokat az adatblokkokat és AZONOSÍTÓkat is tartalmazta. Mivel a díjak az egyedi blokkok esetében merülnek fel, fontos szem előtt tartani, hogy ha a Verziószámozás engedélyezve van, további egyedi blokkok és további költségek is megmaradnak.

Ha engedélyezve van a blob verziószámozása, hívja meg a frissítési műveleteket a blokk blobokon, hogy a lehető legkevesebb blokkot frissítse. Az írási műveletek, amelyek lehetővé teszik a részletes szabályozást a blokkoknál, a [blokk](/rest/api/storageservices/put-block) és a [put blokkot](/rest/api/storageservices/put-block-list)is fel kell venni. A [put blob](/rest/api/storageservices/put-blob) művelet ugyanakkor lecseréli egy blob teljes tartalmát, ezért további díjakat eredményezhet.

A következő forgatókönyvek azt mutatják be, hogyan merülhetnek fel a letiltási blobok és azok verziói, amikor a blob szintje nincs explicit módon beállítva.

#### <a name="scenario-1"></a>1\. példa

Az 1. forgatókönyvben a blobnak van egy korábbi verziója. A blob frissítése a verzió létrehozása óta nem történt meg, ezért csak az 1., 2. és 3. egyedi blokkokra számítunk fel díjakat.

![1. ábra, amely az alap blob és a korábbi verzió egyedi blokkokra vonatkozó számlázását mutatja be.](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>2\. példa

A 2. forgatókönyvben a blob egy blokkját (a diagram 3. blokkját) frissítették. Annak ellenére, hogy a frissített blokk ugyanazokat az adatazonosítókat és ugyanazokat az azonosítót tartalmazza, nem ugyanaz, mint a 3. blokk az előző verzióban. Ennek eredményeképpen a fiók négy blokk után lesz felszámítva.

![2. ábra: az alap blob és a korábbi verzió egyedi blokkokra vonatkozó számlázását mutatja.](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>3\. példa

A 3. forgatókönyvben a blob frissült, de a verzió nem. A 3. blokk lecserélve a 4-es blokkra az alap blobban, de a korábbi verzió továbbra is a 3. blokkot tükrözi. Ennek eredményeképpen a fiók négy blokk után lesz felszámítva.

![3. ábra: az alap blob és a korábbi verzió egyedi blokkokra vonatkozó számlázását mutatja.](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>4\. példa

A 4. forgatókönyvben az alap blob teljesen frissítve lett, és az eredeti blokk egyikét sem tartalmazza. Ennek eredményeképpen a fiók az alap blobban szereplő összes nyolc egyedi blokk után &mdash; , az előző verzióban pedig négyre lesz felszámítva. Ez a forgatókönyv akkor fordulhat elő, ha egy blobba [helyezi a Put blob](/rest/api/storageservices/put-blob) műveletet, mert az az alap blob teljes tartalmát lecseréli.

![4. ábra: az alap blob és a korábbi verzió egyedi blokkokra vonatkozó számlázását mutatja.](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Számlázás, ha a blob szintje explicit módon be van állítva

Ha explicit módon beállította a blob-szintet egy blobhoz vagy verzióhoz (vagy pillanatképhez), akkor a rendszer az új szinten lévő objektum teljes tartalomra vonatkozó díját számítja fel, függetlenül attól, hogy az eredeti szinten található objektummal osztozik-e. Az eredeti szinten a legrégebbi verzió tartalmának teljes hosszát is felszámítjuk. Az eredeti szinten maradó korábbi verziók vagy Pillanatképek a megosztható egyedi blokkokért lesznek felszámítva, ahogy azt a [számlázás nem adta meg explicit](#billing-when-the-blob-tier-has-not-been-explicitly-set)módon.

#### <a name="moving-a-blob-to-a-new-tier"></a>BLOB áthelyezése egy új szintjére

A következő táblázat ismerteti a Blobok vagy verziók számlázási viselkedését, ha az új szintjére kerül.

| Ha a blob-szintet explicit módon beállította... | Ezután számlázunk... |
|-|-|
| Egy korábbi verziójú alapszintű blob | Az alapszintű blob az új szinten és az eredeti szinten lévő legrégebbi verzió, valamint a többi verzióban található egyedi blokkok. <sup>1</sup> |
| Egy korábbi verziót és egy pillanatképet tartalmazó alap blob | Az alap blob az új szinten, az eredeti szint legrégebbi verziója, valamint az eredeti szinten lévő legrégebbi pillanatkép, valamint a többi verzióban<sup>vagy pillanatképekben</sup>szereplő egyedi blokkok. |
| Egy korábbi verzió | A verzió az új szinten és az alap blob az eredeti szinten, valamint a többi verzió egyedi blokkja. <sup>1</sup> |

<sup>1</sup> Ha vannak olyan korábbi verziók vagy Pillanatképek, amelyek nem lettek áthelyezve az eredeti szintjéről, akkor ezek a verziók vagy Pillanatképek az általuk tartalmazott egyedi blokkok száma alapján lesznek felszámítva, a [számlázás, ha a blob szintjét nem adta meg explicit módon](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Az alábbi ábra azt szemlélteti, hogyan történik az objektumok számlázása, amikor egy verzióval ellátott blob átkerül egy másik szintjére.

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="Ábra, amely bemutatja, hogyan történik az objektumok számlázása, amikor egy verzióval ellátott blobot explicit módon határoznak meg.":::

A blob, a verzió vagy a pillanatkép szintje explicit módon történő beállítása nem vonható vissza. Ha egy blobot egy új szintre helyez át, majd visszahelyezi az eredeti szintjére, akkor akkor is a teljes tartalomért kell fizetnie, ha az objektum más objektumokkal is megosztja az eredeti szintet.

A Blobok, verziók vagy Pillanatképek szintjét explicit módon beállító műveletek a következők:

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- A [blobot](/rest/api/storageservices/put-blob) a megadott szintűvé tegye
- [Letiltási lista](/rest/api/storageservices/put-block-list) megadása a megadott szintű szinten
- [Blob másolása](/rest/api/storageservices/copy-blob) megadott szintű példánnyal

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>BLOB törlése, ha a Soft delete engedélyezve van

Ha a blob-törlés engedélyezve van, ha olyan alapszintű blobot töröl vagy felülír, amelynek a szintje explicit módon be lett állítva, akkor a Soft-Deleted blob összes korábbi verzióját teljes tartalommal számlázjuk. További információ a Blobok verziószámozásáról és a helyreállítható törlésről: a [blob verziószámozása és a Soft delete](#blob-versioning-and-soft-delete)használata.

A következő táblázat ismerteti a nem megfelelően törölt Blobok számlázási viselkedését attól függően, hogy a verziószámozás engedélyezett vagy le van tiltva. Ha a Verziószámozás engedélyezve van, akkor a rendszer egy verziót hoz létre, amikor egy blobot törölnek. Ha a verziószámozás le van tiltva, a Blobok törlésével egy törlési pillanatkép jön létre.

| Ha egy alapszintű blobot felülír a réteg explicit módon beállított szintjével... | Ezután számlázunk... |
|-|-|
| Ha a blob-alapú törlés és verziószámozás egyaránt engedélyezve van | A teljes tartalom hosszúságú összes meglévő verzió a szintjétől függetlenül. |
| Ha a blob-törlés engedélyezve van, de a verziószámozás le van tiltva | Az összes létező, teljes tartalommal rendelkező törlési pillanatkép a rétegtől függetlenül. |

## <a name="see-also"></a>Lásd még

- [BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)
- [BLOB pillanatképének létrehozása](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Az Azure Storage-Blobok Soft delete](./soft-delete-blob-overview.md)