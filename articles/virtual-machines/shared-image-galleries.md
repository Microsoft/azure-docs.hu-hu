---
title: Virtuálisgép-lemezképek megosztása megosztott galériákkal
description: Megtudhatja, hogyan használhatja a megosztott képtárakat a linuxos virtuális gépek rendszerképeinek megosztására a szervezeten belül.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 10/14/2020
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 9a5bc0620468980da68e1b92f758d5f3862ac7d3
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487253"
---
# <a name="shared-image-galleries-overview"></a>Megosztott képgyűjtemények – áttekintés

A Shared Image Gallery szolgáltatás segít struktúrát és rendszert kialakítani a képek köré. A megosztott képtárak a következőket biztosítják:

- Lemezképek globális replikálása.
- A könnyebb felügyelet érdekében a rendszerképek verziószámozása és csoportosítása.
- A Availability Zones-t támogató régiókban található, a zóna redundáns tárolási (ZRS) fiókjaival rendelkező, nagyon elérhető rendszerképek. A ZRS nagyobb rugalmasságot biztosít a zónabeli hibákkal szemben.
- Premium Storage-támogatás (Premium_LRS).
- Az Azure RBAC használatával megoszthatja az előfizetéseket, és akár Active Directory (AD) bérlők között is.
- Az üzembe helyezések skálázása minden egyes régióban képreplikákkal.

Megosztott képkatalógus használatával a képeket megoszthatja a szervezeten belüli különböző felhasználókkal, egyszerű szolgáltatásokkal vagy AD-csoportokkal. A megosztott lemezképek több régióba is replikálhatók, így az üzemelő példányok gyorsabban méretezhetők.

A rendszerkép a teljes virtuális gép (beleértve a csatlakoztatott adatlemezeket is) vagy csak az operációsrendszer-lemez másolata, attól függően, hogy hogyan lett létrehozva. Amikor létrehoz egy virtuális gépet a lemezképből, a lemezképben található virtuális merevlemezek egy másolatát használják az új virtuális gép lemezeinek létrehozásához. A rendszerkép a tárolóban marad, és újra felhasználható új virtuális gépek létrehozásához.

Ha nagy számú lemezképet szeretne fenntartani, és a vállalaton belül elérhetővé szeretné tenni őket, a megosztott képkatalógust adattárként is használhatja. 

A megosztott képkatalógus funkció több erőforrástípust tartalmaz:

| Erőforrás | Leírás|
|----------|------------|
| **Rendszerkép forrása** | Ez egy olyan erőforrás, amellyel rendszerkép- **verziót** hozhat létre egy Képtárban. A rendszerkép forrása lehet egy meglévő Azure-beli virtuális gép, amely [általánosított vagy specializált](#generalized-and-specialized-images), felügyelt rendszerkép, pillanatkép, virtuális merevlemez vagy rendszerkép verziója egy másik Képtárban. |
| **Képtár** | Az Azure Marketplace-hez hasonlóan a képkatalógus **is a lemezképek** kezeléséhez és megosztásához használható tárház, de Ön szabályozhatja, hogy ki férhet hozzá. |
| **Rendszerkép definíciója** | A képdefiníciók egy gyűjteményen belül jönnek létre, és a rendszerképekkel és a belső használat követelményeivel kapcsolatos információkat hordozzák. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. |
| **Rendszerképverzió** | A **rendszerkép verziója** az, amit a virtuális gép létrehozásához használ gyűjtemény létrehozásakor. A környezethez szükség lehet a rendszerkép több verziójára. A felügyelt rendszerképekhez hasonlóan, amikor **rendszerkép-verziót** használ egy virtuális gép létrehozásához, a rendszerkép verziója a virtuális gép új lemezének létrehozására szolgál. A rendszerkép verziója többször is használható. |

<br>

![Ábra, amely bemutatja, hogyan lehet egy rendszerkép több verziója is a galériában](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Rendszerkép-definíciók

A rendszerkép-definíciók egy adott rendszerkép verzióihoz tartozó logikai Csoportosítások. A rendszerkép definíciója információt tartalmaz arról, hogy a rendszerkép hogyan lett létrehozva, melyik operációs rendszer, illetve a rendszerkép használatával kapcsolatos egyéb információk. A rendszerkép definíciója olyan, mint egy csomag, amely az adott rendszerkép létrehozásával kapcsolatos összes részletre vonatkozik. Nem telepít virtuális gépet lemezkép-definícióból, hanem a definícióból létrehozott lemezkép-verziókból.

Az egyes képdefiníciók három paramétert használnak a **Publisherben**, az **ajánlatban** és az **SKU**-ban. Ezek egy adott rendszerkép-definíció megtalálására szolgálnak. Rendelkezhet egy vagy két, de nem mindhárom értékkel rendelkező képverzióval is.  Íme például három képdefiníció és értékeik:

|Rendszerkép-definíció|Publisher|Ajánlat|SKU|
|---|---|---|---|
|myImage1|Contoso|Finance|Háttérrendszer|
|myImage2|Contoso|Finance|Előtér|
|myImage3|Tesztelés|Finance|Előtér|

Mindhárom ilyen egyedi értéket tartalmaz. A formátum hasonló ahhoz, ahogyan jelenleg az [Azure Marketplace-lemezképekhez](./windows/cli-ps-findimage.md) tartozó közzétevőt, ajánlatot és SKU-t megadhatja Azure PowerShell a Piactéri lemezkép legújabb verziójának beszerzéséhez. Minden rendszerkép-definíciónak egyedi készlettel kell rendelkeznie ezeknek az értékeknek.

A következő paraméterek határozzák meg, hogy milyen típusú képverziókat tartalmazhatnak:

- Operációs rendszer állapota – [általánosított vagy specializált](#generalized-and-specialized-images)operációsrendszer-állapotot állíthat be. A mező kitöltése kötelező.
- Operációs rendszer – lehet Windows vagy Linux. A mező kitöltése kötelező.
-   Hyper-V generáció – megadhatja, hogy a rendszerkép az 1. generációból vagy [2. generációs](generation-2.md) Hyper-v virtuális merevlemezből lett-e létrehozva. Az alapértelmezett érték az 1. generáció.


A következő más paraméterek is megadhatók a képdefinícióban, így könnyebben nyomon követheti az erőforrásokat:

- Leírás – a Leírás használatával részletesebb információkat adhat meg arról, hogy miért létezik a rendszerkép definíciója. Előfordulhat például, hogy rendelkezik egy rendszerkép-definícióval az előtér-kiszolgálóhoz, amelyen az alkalmazás előre telepítve van.
- Végfelhasználói licencszerződés – a rendszerkép-definícióra vonatkozó végfelhasználói licencszerződésre mutathat.
- Adatvédelmi nyilatkozat és kibocsátási megjegyzések – a kibocsátási megjegyzések és az adatvédelmi nyilatkozatok tárolása az Azure Storage-ban, valamint egy URI megadása a rendszerkép definíciójának részeként való hozzáféréshez.
- Élettartam dátuma – a rendszerkép definíciójának élettartama után az Automation használatával törölheti a régi képdefiníciókat.
- Címke – címkéket adhat hozzá a rendszerkép definíciójának létrehozásakor. További információ a címkékkel kapcsolatban: [címkék használata az erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)
- Minimális és maximális vCPU és memória-javaslatok – ha a rendszerképnek van vCPU-és memória-javaslata, csatolhatja ezeket az információkat a rendszerkép-definícióhoz.
- Nem engedélyezett lemezek típusai – a virtuális gép tárolási igényeivel kapcsolatos információkat adhat meg. Ha például a rendszerkép nem szabványos HDD-lemezekhez van kiválasztva, akkor azokat a letiltási listához adja.
- A Piactéri rendszerképekre vonatkozó vásárlási terv adatai –, `-PurchasePlanPublisher` `-PurchasePlanName` és `-PurchasePlanProduct` . További információ a vásárlási tervekről: [rendszerképek keresése az Azure Marketplace](./windows/cli-ps-findimage.md) -en, valamint a [lemezképek létrehozásakor az Azure Marketplace vásárlási terv információi](marketplace-images.md).


## <a name="image-versions"></a>Rendszerkép-verziók

A **rendszerkép verziója** a virtuális gép létrehozásához használatos. A környezethez szükség lehet a rendszerkép több verziójára. Ha **rendszerkép-verziót** használ a virtuális gép létrehozásához, a rendszer a rendszerkép verzióját használja a virtuális gép új lemezének létrehozásához. A rendszerkép verziója többször is használható.

A rendszerkép verziójának tulajdonságai a következők:

- Verziószám. Ezt használja a rendszerkép verziójának neve. A formátuma mindig a következő: MajorVersion. MinorVersion. Patch. Ha a virtuális gép létrehozásakor a **legújabbat** adja meg, a rendszer a legújabb képet választja ki a legmagasabb MajorVersion, majd a MinorVersion, majd a javítást. 
- Forrás. A forrás lehet egy virtuális gép, felügyelt lemez, pillanatkép, felügyelt lemezkép vagy más lemezkép verziója. 
- A legutóbbi kizárása. A verziót megtarthatja a legújabb lemezkép-verzióként való használathoz. 
- Az élettartam dátuma. Az a dátum, amely után a virtuális gépek nem hozhatók létre ebből a rendszerképből.


## <a name="generalized-and-specialized-images"></a>Általánosított és speciális rendszerképek

A megosztott rendszerkép-katalógusban két operációsrendszer-állapot támogatott. Általában a lemezképek megkövetelik, hogy a rendszerkép létrehozásához használt virtuális gép általánosítva legyen a lemezkép készítése előtt. Az általánosítás egy olyan folyamat, amely eltávolítja a gép és a felhasználó által megadott adatokat a virtuális gépről. Windows esetén a rendszer a Sysprep eszközt használja. A Linux rendszerben [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` vagy paramétereket is használhat `-deprovision+user` .

A speciális virtuális gépek nem a gép specifikus információinak és fiókjainak eltávolítására szolgáló folyamattal rendelkeznek. Emellett a speciális lemezképből létrehozott virtuális gépekhez nincs `osProfile` társítva. Ez azt jelenti, hogy a speciális képek bizonyos előnyök mellett bizonyos korlátozásokkal is rendelkeznek.

- A speciális rendszerképekből létrehozott virtuális gépek és méretezési csoportok gyorsabban üzembe helyezhetők. Mivel azok olyan forrásokból jönnek létre, amelyek már az első rendszerindításon keresztül lettek létrehozva, a rendszerképekből létrehozott virtuális gépek gyorsabban indulnak.
- A virtuális gépre való bejelentkezéshez használható fiókok az adott virtuális gépről létrehozott speciális rendszerkép használatával létrehozott virtuális gépeken is használhatók.
- A virtuális gépek neve annak a virtuális gépnek a **számítógépnevét** fogja tartalmazni, amelyet a rendszerkép készített. Az ütközések elkerülése érdekében módosítsa a számítógép nevét.
- A `osProfile` használatával a () segítségével a virtuális gép bizalmas adatokat továbbít a szolgáltatásnak `secrets` . Ez problémákat okozhat a kulcstartó, a WinRM és a által használt egyéb funkciók használatával `secrets` `osProfile` . Bizonyos esetekben a felügyelt szolgáltatásbeli identitások (MSI) használatával is megkerülheti ezeket a korlátozásokat.

## <a name="regional-support"></a>Regionális támogatás

Az összes nyilvános régió lehet célcsoport, de az Ausztrália középső és Ausztrália középső régiójában való replikáláshoz a 2. előfizetést hozzá kell adni az engedélyezési listához. Ha egy előfizetést szeretne kérni az engedélyezési listához, lépjen a következőre: https://docs.microsoft.com/azure/virtual-machines/shared-image-galleries#regional-support

## <a name="limits"></a>Korlátok 

Az erőforrások megosztott képtárakkal való üzembe helyezéséhez korlátok, előfizetések vonatkoznak:
- 100 megosztott képtárak, előfizetések száma régiónként
- 1 000 képdefiníciók, előfizetések régiónként
- 10 000 képverzió, előfizetések száma régiónként
- 10 rendszerkép-verzió replikái, előfizetések régiónként
- A lemezképhez csatolt minden lemeznek 1 TB-nál kisebbnek vagy azzal egyenlőnek kell lennie

További információ: az [erőforrás-használat korlátainak korlátozása](../networking/check-usage-against-limits.md) példákkal a jelenlegi használat ellenõrzéséhez.
 
## <a name="scaling"></a>Méretezés
A megosztott képkatalógus segítségével megadhatja, hogy az Azure hány replikát őrizzen meg a képeken. Ez segítséget nyújt a több virtuális gépre kiterjedő üzembe helyezési forgatókönyvekben, mivel a virtuális gépek központi telepítése különböző replikák számára lehetséges, ami csökkenti annak a valószínűségét, hogy egy replika túlterhelése miatt szabályozható a példány-létrehozási feldolgozás.

A megosztott képtárat mostantól egy virtuálisgép-méretezési csoportba helyezheti üzembe egy 1 000-es virtuálisgép-példányon (a 600-ból felügyelt lemezképekkel). A képreplikák jobb teljesítményt, megbízhatóságot és konzisztenciát biztosítanak a központi telepítéshez.  Az egyes célcsoportok között különböző replikákat állíthat be, a régió skálázási igényei alapján. Mivel minden replika a rendszerkép egy részletes másolata, ez segít az üzembe helyezések lineárisan történő méretezésében minden további replikával. Habár a két rendszerkép vagy régió nem egyezik, a következő általános útmutatást láthatjuk, hogyan használhat replikákat egy régióban:

- Nem virtuálisgép-méretezési csoport esetén – minden olyan 20 virtuális gép esetében, amelyet egyszerre hoz létre, javasoljuk, hogy tartsa meg az egyik replikát. Ha például 120 virtuális gépet hoz létre egyidejűleg ugyanazon rendszerkép használatával egy régióban, javasoljuk, hogy legalább 6 replikát őrizzen meg a rendszerképből. 
- A virtuálisgép-méretezési csoport üzembe helyezése esetén – a legfeljebb 600 példánnyal rendelkező méretezési csoportokra vonatkozóan javasoljuk, hogy tartsa meg legalább egy replikát. Ha például egyszerre 5 méretezési csoportot hoz létre, és mindegyik 600 virtuálisgép-példánnyal ugyanazt a rendszerképet használja egyetlen régióban, javasoljuk, hogy legalább 5 replikát őrizzen meg a rendszerképből. 

Mindig azt javasoljuk, hogy a replikák számát a képméret, a tartalom és az operációs rendszer típusa miatt ne felépítse.

![A képek méretezését bemutató ábra](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Képek nagyfokú elérhetővé tétele

Az [Azure Zone redundáns tároló (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) rugalmasságot biztosít a rendelkezésre állási zóna meghibásodása esetén a régióban. A megosztott képkatalógus általános elérhetősége mellett dönthet úgy, hogy a rendszerképeket a ZRS-fiókokban tárolja a Availability Zones régiókban. 

Kiválaszthatja az egyes célcsoportok fiókjának típusát is. Az alapértelmezett Storage-fióktípus Standard_LRS, de az Availability Zones-vel rendelkező régiók esetében Standard_ZRS is választhat. A ZRS regionális elérhetőségét [itt](../storage/common/storage-redundancy.md)tekintheti meg.

![ZRS mutató ábra](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replikáció
A megosztott képkatalógus lehetővé teszi a képek más Azure-régiókba történő automatikus replikálását is. Az egyes megosztott rendszerkép-verziók különböző régiókba replikálhatók, attól függően, hogy mi értelme van a szervezetnek. Az egyik példa az, hogy mindig replikálja a legújabb rendszerképet több régióban, míg az összes régebbi verzió csak 1 régióban érhető el. Ez a megosztott rendszerkép-verziók tárolási költségeinek megtakarítását is lehetővé teheti. 

Az a régió, amelyet a megosztott rendszerkép replikál, a rendszer a létrehozási idő után frissítheti. A különböző régiókba való replikáláshoz szükséges idő a másolt adatok mennyiségétől és a verzió által replikált régiók számától függ. Bizonyos esetekben ez néhány órát is igénybe vehet. A replikáció során régiónként is megtekintheti a replikálás állapotát. Ha a lemezkép replikálása egy régióban fejeződött be, akkor az adott lemezkép verziójának használatával üzembe helyezhet egy virtuális gépet vagy méretezési készletet a régióban.

![A képek replikálásának módját bemutató ábra](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Access

Mivel a megosztott képtára, a képdefiníció és a rendszerkép verziója minden erőforrás, a beépített natív Azure RBAC-vezérlőkkel is megoszthatók. Az Azure RBAC használatával ezeket az erőforrásokat megoszthatja más felhasználókkal, egyszerű szolgáltatásokkal és csoportokkal is. Akár a bérlőn kívüli személyekhez is megoszthatja a hozzáférést. Miután egy felhasználó hozzáfér a megosztott lemezkép verziójához, üzembe helyezhet egy virtuális gépet vagy egy virtuálisgép-méretezési készletet.  Itt látható a megosztási mátrix, amely segít megérteni, hogy a felhasználó milyen módon férhet hozzá:

| Felhasználóval megosztva     | Megosztott rendszerkép-katalógus | Rendszerkép-definíció | Rendszerképverzió |
|----------------------|----------------------|--------------|----------------------|
| Megosztott rendszerkép-katalógus | Igen                  | Igen          | Yes                  |
| Rendszerkép-definíció     | Nem                   | Igen          | Yes                  |

A legjobb megoldás érdekében javasoljuk, hogy ossza meg a gyűjteményt a katalógus szintjén. Az egyes rendszerkép-verziók megosztását nem ajánlott. További információ az Azure RBAC: Azure- [szerepkörök kiosztása](../role-based-access-control/role-assignments-portal.md).

A képek a több-bérlős alkalmazások regisztrálásával is megoszthatók, akár a bérlők között is. A képek bérlők közötti megosztásával kapcsolatos további információkért tekintse meg az [Azure CLI](./linux/share-images-across-tenants.md) vagy a [PowerShell](./windows/share-images-across-tenants.md)használatával "a katalógus virtuálisgép-rendszerképeinek megosztása az Azure-bérlők között" című témakört.

## <a name="billing"></a>Számlázás
A megosztott rendszerkép-katalógus szolgáltatás használata nem díjköteles. A következő erőforrásokért kell fizetnie:
-   Az egyes replikák tárolásának tárolási költségei. A tárolási költséget pillanatképként számítjuk fel, és a rendszerkép verziószáma, a rendszerkép verziójának replikái, valamint azoknak a régióknak a száma alapján történik, amelyekre a verzió replikálódik. 
-   A hálózati kimenő forgalom díja a forrás régióból a replikált régiókba való első lemezkép-verzió replikálásához. A későbbi replikákat a régión belül kezeljük, így nincs további díj. 

Tegyük fel például, hogy rendelkezik egy 127 GB-OS operációsrendszer-lemezzel, amely csak 10GB tárterületet foglal le, és egy üres 32 GB adatlemezt. Az egyes képek foglalt mérete csak 10 GB lehet. A rendszerkép három régióba replikálódik, és minden régió két replikával rendelkezik. Hat teljes pillanatkép fog megjelenni, amelyek mindegyike 10 GB-ot használ. Az egyes Pillanatképek tárolási költségeit a 10 GB foglalt méret alapján számítjuk fel. A további két régióba való másoláshoz az első replika hálózati kimenő forgalmi díjait kell megfizetnie. A pillanatképek díjszabásáról az egyes régiókban a [felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)című témakörben olvashat bővebben. A hálózati kimenő forgalomról további információt a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/)című témakörben talál.


## <a name="updating-resources"></a>Erőforrások frissítése

A létrehozás után módosításokat végezhet a Képtár erőforrásaiban. Ezek a következőkre korlátozódnak:
 
Megosztott képgyűjtemény:
- Leírás

Rendszerkép definíciója:
- Ajánlott vCPU
- Ajánlott memória
- Leírás
- Élettartam vége

Rendszerkép verziója:
- Regionális replika száma
- Célcsoportok
- Legutóbbi kizárás
- Élettartam vége

## <a name="sdk-support"></a>SDK-támogatás

A következő SDK-k támogatják a megosztott képtárak létrehozását:

- [.NET](/dotnet/api/overview/azure/virtualmachines/management)
- [Java](/java/azure/)
- [Node.js](/javascript/api/@azure/arm-compute)
- [Python](/python/api/overview/azure/virtualmachines)
- [Ugrás](/azure/go/)

## <a name="templates"></a>Sablonok

Létrehozhat megosztott képkatalógus-erőforrást sablonok használatával. Több Azure Gyorsindítás-sablon is elérhető: 

- [Shared Image Gallery létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerképverzió létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása rendszerképverzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Gyakori kérdések 

* [Hogyan lehet listázni az összes megosztott képgyűjtemény erőforrását az előfizetések között?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Áthelyezhetem a meglévő rendszerképet a megosztott képgalériába?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Létrehozhatok lemezkép-verziót egy speciális lemezről?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Áthelyezhetem a megosztott rendszerkép-katalógus erőforrását egy másik előfizetésbe a létrehozása után?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Replikálható a képverziók a felhők között, például az Azure China 21Vianet vagy az Azure Germany vagy a Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Replikálható a rendszerképem verziója az előfizetések között?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Megoszthatom a képverziókat az Azure AD-bérlők között?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Mennyi ideig tart a képverziók replikálása a célcsoportok között?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Mi a különbség a forrásoldali régió és a célcsoport között?](#what-is-the-difference-between-source-region-and-target-region)
* [Hogyan megadnia a forrás régiót a rendszerkép verziójának létrehozásakor?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Hogyan az egyes régiókban létrehozandó rendszerkép-verziók replikáinak számát?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Létrehozhatom a megosztott képtárat egy másik helyen, mint a rendszerkép-definíció és a rendszerkép verziója?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Milyen díjak vonatkoznak a megosztott képtárat használó szolgáltatásokra?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Milyen API-verziót érdemes használni a megosztott rendszerkép-katalógus és a rendszerkép-definíció és a rendszerkép-verzió létrehozásához?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Milyen API-verziót használhatok a rendszerkép verziójának használatával létrehozott megosztott virtuális gépek vagy virtuálisgép-méretezések létrehozásához?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [Frissíthetem a felügyelt lemezkép használatával létrehozott virtuálisgép-méretezési csoportját a megosztott képgyűjteményi rendszerképek használatához?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Hogyan lehet listázni az összes megosztott képgyűjtemény erőforrását az előfizetések között?

Az alábbi lépéseket követve listázhatja az összes megosztott képkatalógus-erőforrást az előfizetések között, amelyekhez hozzáféréssel rendelkezik a Azure Portalban:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Görgessen le az oldalra, és válassza az **összes erőforrás** lehetőséget.
1. Válassza ki az összes olyan előfizetést, amelyben az összes erőforrást listázni szeretné.
1. Keresse meg a **megosztott képgyűjtemény** típusú erőforrásokat.
  
Ha az összes olyan előfizetésben szeretné listázni az összes megosztott képkatalógus-erőforrást, amelyre jogosult, használja a következő parancsot az Azure CLI-ben:

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

További információ: a katalógus- **erőforrások kezelése** az [Azure CLI](update-image-resources-cli.md) vagy a [PowerShell](update-image-resources-powershell.md)használatával.

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Áthelyezhetem a meglévő rendszerképet a megosztott képgalériába?
 
Igen. A lemezképek típusain alapuló 3 forgatókönyv van.

 1. eset: ha felügyelt lemezképpel rendelkezik, létrehozhat egy rendszerkép-definíciót és egy rendszerkép-verziót is. További információ: **Migrálás felügyelt rendszerképből rendszerkép verziójára** az [Azure CLI](image-version-managed-image-cli.md) vagy a [PowerShell](image-version-managed-image-powershell.md)használatával.

 2. forgatókönyv: Ha nem felügyelt rendszerképet használ, létrehozhat egy felügyelt rendszerképet, majd létrehozhatja a rendszerkép definícióját és a rendszerkép verzióját. 

 3. forgatókönyv: Ha a helyi fájlrendszerben virtuális merevlemezt használ, fel kell töltenie a virtuális merevlemezt egy felügyelt rendszerképbe, majd a rendszerkép-definíciót és a rendszerkép verzióját is létrehozhatja belőle.

- Ha a VHD egy Windows rendszerű virtuális gép, tekintse meg [a virtuális merevlemez feltöltése](./windows/upload-generalized-managed.md)című témakört.
- Ha a virtuális merevlemez Linux rendszerű virtuális gépen van, lásd: [virtuális merevlemez feltöltése](./linux/upload-vhd.md#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Létrehozhatok lemezkép-verziót egy speciális lemezről?

Igen, létrehozhat egy virtuális gépet egy speciális rendszerképből a [parancssori](vm-specialized-image-version-cli.md)felület, a [PowerShell](vm-specialized-image-version-powershell.md)vagy az API használatával. 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Áthelyezhetem a megosztott rendszerkép-katalógus erőforrását egy másik előfizetésbe a létrehozása után?

Nem, nem helyezheti át a megosztott rendszerkép-katalógus erőforrását egy másik előfizetésbe. A katalógusban lévő rendszerképeket más régiókba replikálhatja, vagy átmásolhat egy képet egy másik gyűjteményből az [Azure CLI](image-version-another-gallery-cli.md) vagy a [PowerShell](image-version-another-gallery-powershell.md)használatával.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Replikálható a képverziók a felhők között, például az Azure China 21Vianet vagy az Azure Germany vagy a Azure Government Cloud?

Nem, a képverziókat nem lehet a felhők között replikálni.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Replikálható a rendszerképem verziója az előfizetések között?

Nem, a képverziókat replikálhatja egy előfizetésben lévő régiók között, és más előfizetésekben is használhatja a RBAC-on keresztül.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Megoszthatom a képverziókat az Azure AD-bérlők között? 

Igen, a RBAC használatával a bérlők között oszthat meg személyeket. Ha azonban a méretezést szeretné megosztani, tekintse meg a "katalógus-rendszerképek megosztása Azure-bérlők között" részt a [PowerShell](./windows/share-images-across-tenants.md) vagy a [CLI](./linux/share-images-across-tenants.md)használatával.

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Mennyi ideig tart a képverziók replikálása a célcsoportok között?

A lemezkép verziójának replikálási ideje teljes mértékben a rendszerkép méretétől és a replikált régiók számától függ. Javasoljuk azonban, hogy a képet kis méretűre kell állítani, a forrás-és a célcsoportok pedig a legjobb eredmények elérése érdekében. A replikáció állapotát a-ReplicationStatus jelzővel is megtekintheti.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Mi a különbség a forrásoldali régió és a célcsoport között?

A forrástartomány az a régió, amelyben a rendszer létrehozza a rendszerkép verzióját, és a célcsoportok azok a régiók, amelyekben a rendszerkép verziójának másolatát tárolja a rendszer. Minden egyes rendszerkép-verzióhoz csak egy forrásoldali régió tartozhat. Továbbá győződjön meg arról, hogy a forrás régió helyét az egyik célként megadott régióként adja meg, amikor létrehozza a rendszerkép verzióját.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Hogyan megadnia a forrás régiót a rendszerkép verziójának létrehozásakor?

A rendszerkép verziójának létrehozásakor a PowerShellben a **--Location** címkét használhatja a parancssori felületen, és megadhatja **a forrás** régiót. Győződjön meg arról, hogy az alaprendszerképként használt felügyelt rendszerkép ugyanazon a helyen található, mint a rendszerkép verziójának létrehozásához használni kívánt hely. Továbbá győződjön meg arról, hogy a forrás régió helyét az egyik célként megadott régióként adja meg, amikor létrehozza a rendszerkép verzióját.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Hogyan az egyes régiókban létrehozandó rendszerkép-verziók replikáinak számát?

Kétféle módon adhatja meg az egyes régiókban létrehozandó rendszerkép-verziók replikáinak számát:
 
1. A regionális replikák száma, amely meghatározza, hogy régiónként hány replikát kíván létrehozni. 
2. A közös replikák száma, amely az alapértelmezett régiónként szám, ha nincs megadva a területi replika száma. 

A területi replika számának megadásához adja meg a helyet az adott régióban létrehozni kívánt replikák számával együtt: "dél-közép-Egyesült Államok = 2". 

Ha a területi replikák száma nincs megadva az egyes helyekhez, akkor a replikák alapértelmezett száma lesz a megadott közös replika. 

A gyakori replikák számának a CLI-ben való megadásához használja a parancsban a **--replika-Count** argumentumot `az sig image-version create` .

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Létrehozhatom a megosztott képtárat egy másik helyen, mint a rendszerkép-definíció és a rendszerkép verziója?

Igen, ez lehetséges. Javasoljuk azonban, hogy az erőforráscsoportot, a közös képtárat, a képdefiníciót és a rendszerkép verziószámát ugyanazon a helyen tartsa.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Milyen díjak vonatkoznak a megosztott képtárat használó szolgáltatásokra?

A megosztott képkatalógus szolgáltatás használatához nem számítunk fel díjat, kivéve a rendszerkép-verziók és a hálózati kimenő forgalom tárolási díját a forrás régióról a célként megadott régiókba történő replikáláshoz.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Milyen API-verziót érdemes használni a megosztott rendszerkép-katalógus és a rendszerkép-definíció és a rendszerkép-verzió létrehozásához?

A megosztott képtárakkal, képdefiníciókkal és képverziókkal való együttműködéshez javasoljuk, hogy használja a 2018-06-01-es API-verziót. A zóna redundáns tárterületének (ZRS) 2019-03-01-es vagy újabb verziójúnak kell lennie.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Milyen API-verziót használhatok a rendszerkép verziójának használatával létrehozott megosztott virtuális gépek vagy virtuálisgép-méretezések létrehozásához?

A virtuális gépek és a virtuálisgép-méretezési csoport rendszerképeket használó üzembe helyezése esetén javasoljuk, hogy a 2018-04-01-es vagy újabb API-verziót használja.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>Frissíthetem a felügyelt lemezkép használatával létrehozott virtuálisgép-méretezési csoportját a megosztott képgyűjteményi rendszerképek használatához?

Igen, frissítheti a méretezési csoport képhivatkozását egy felügyelt lemezképből egy megosztott lemezkép-katalógusba, ha az operációs rendszer típusa, a Hyper-V generációja és az adatlemez elrendezése megegyezik a lemezképek között.

## <a name="troubleshoot-shared-image-gallery-issues"></a>Megosztott rendszerkép-katalógussal kapcsolatos problémák elhárítása
Ha a megosztott képkatalógus erőforrásaiban bármilyen művelet végrehajtásával kapcsolatos problémái vannak, tekintse meg a gyakori hibák listáját a [hibaelhárítási útmutatóban](troubleshooting-shared-images.md).

Emellett a kérdését a következő címen teheti közzé és címkézheti `azure-virtual-machines-images` : [Q&a](/answers/topics/azure-virtual-machines-images.html).

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan helyezhet üzembe megosztott rendszerképeket az [Azure CLI](shared-images-cli.md) vagy a [PowerShell](shared-images-powershell.md)használatával.
