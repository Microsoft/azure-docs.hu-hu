---
title: Vész-helyreállítási útmutató az Azure-hoz készült avere-vFXT
description: Adatok védelme a avere-vFXT az Azure-ban véletlen törlés vagy kimaradás esetén
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 5cc4678b082aa5a4a3f90518ff8fac448f414f1a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342246"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Vész-helyreállítási útmutató az Azure-hoz készült avere-vFXT

Ez a cikk azokat a stratégiákat ismerteti, amelyek védik az Azure-munkafolyamatok avere-vFXT, és útmutatást nyújt az adatok biztonsági mentéséhez, hogy a rendszer baleseteket vagy kimaradásokat lehessen helyreállítani.

A avere vFXT for Azure átmenetileg a gyorsítótárában tárolja az adattárolást. Az adattárolási rendszerek hosszú távú tárolása – helyszíni hardverrendszer, Azure Blob Storage-tárolók vagy mindkettő.

Az kimaradások és az esetleges adatvesztés elleni védelem érdekében vegye figyelembe a következő négy területet:

* A leállás elleni védelem, ha az Azure-rendszer avere vFXT elérhetetlenné válik
* Adatok védelme a fürt gyorsítótárában
* Adatok védelme a háttérbeli NAS-hardveres tárolóban
* Adatok védelme a háttérbeli Azure Blob Cloud Storage-ban

Az Azure Customer minden avere-vFXT létre kell hoznia a saját átfogó vész-helyreállítási tervét, amely tartalmazza az ezen elemek csomagjait. A vFXT-fürttel használt alkalmazásokba rugalmasságot is építhet. További segítségért olvassa el a [következő lépések](#next-steps) hivatkozásait.

## <a name="protect-against-downtime"></a>Az állásidő elleni védelem

A redundancia a avere vFXT for Azure termékhez készült:

* A fürt nagyon elérhető, és az egyes fürtcsomópontok feladatátvétele minimális megszakítással végezhető el.
* A gyorsítótárban megváltoztatott adatváltozások a hosszú távú tárolás érdekében rendszeresen a háttérrendszer-tárolók (hardveres NAS vagy Azure Blob) számára íródnak.

Az Azure-fürt minden avere-vFXT egyetlen rendelkezésre állási zónában kell lennie, de a különböző zónákban vagy különböző régiókban található redundáns fürtöket a regionális leállás esetén is gyorsan elérhetővé teheti.

A tárolókat több régióban is elhelyezheti, ha aggódik az adathozzáférés elvesztésével kapcsolatban. Ne feledje azonban, hogy a régiók közötti tranzakciók nagyobb késéssel járnak, mint a régión belüli tranzakcióknál magasabbak.

## <a name="protect-data-in-the-cluster-cache"></a>A fürt gyorsítótárában tárolt adatvédelem

A gyorsítótárazott adatfájlok mindig az alapszintű tárolóba kerülnek, a normál leállítás előtt, de nem ellenőrzött leállítás esetén a gyorsítótárban lévő módosult adatvesztés is elvész.

Ha a fürtöt használja a fájlok olvasásának optimalizálására, az elveszített módosítások nem változnak. Ha a fürt használatával is gyorsítótárazza a fájl módosításait (írásokat), vegye figyelembe, hogy az alapszintű filers [gyorsítótár-házirendjeit](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) szeretné-e módosítani.<!-- link to legacy doc --> annak testre szabásához, hogy a rendszer milyen gyakran írja be az adattárolást a hosszú távú tárolóba.

Általánosságban elmondható, hogy a helyreállítási tervnek a háttérrendszer biztonsági mentésére kell összpontosítania, amelyek több adattal rendelkeznek, és általában még fontosabbak a munkafolyamatok a meghibásodás utáni újbóli létrehozásához.

## <a name="protect-data-in-nas-core-filers"></a>Az adatvédelmet a NAS Core filers szolgáltatásban

Fogadja el az elfogadott metódusokat a helyszíni NAS-beli hardveres hálózati tárolóban tárolt adatvédelemhez, beleértve a pillanatképeket és a teljes biztonsági mentést a NAS-szolgáltató által javasolt módon. Ezeknek az alapszintű Filer-fájloknak a vész-helyreállítási lehetősége meghaladja a jelen cikk hatókörét.

## <a name="protect-data-in-azure-blob-storage"></a>Az Azure Blob Storage adatainak védelme

Az Azure-hoz készült avere-vFXT helyileg redundáns tárolást (LRS) használ az Azure Blob Core filers szolgáltatásokhoz. Ez azt jelenti, hogy a blob-tárolók adatait a rendszer automatikusan átmásolja az átmeneti hardveres hibákra az adatközponton belül.

Ez a szakasz tippekkel szolgál arról, hogy miként lehet a blob Storage-ban lévő adatok további védelme az egész régióra kiterjedő kimaradások vagy véletlen törlések révén.

Az Azure Blob Storage-ban tárolt adatok védelmére vonatkozó ajánlott eljárások a következők:

* Másolja a kritikus adatait egy másik régióban lévő másik Storage-fiókba gyakran (amilyen gyakran a vész-helyreállítási terv határozza meg).
* Az összes megcélzott rendszeren lévő adathozzáférés szabályozása a véletlen törlés vagy a sérülés elkerülése érdekében. Érdemes lehet [erőforrás-zárolásokat](../azure-resource-manager/management/lock-resources.md) használni az adattárolón.
* Engedélyezze a avere vFXT for Azure [Cloud Snapshot](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) funkciót a blob Core-alapú filers szolgáltatáshoz.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Avere-vFXT alapszintű Filer-adatbázis másolása biztonsági mentési fiókba

Az alábbi lépéseket követve hozzon létre egy adatbiztonsági mentést egy másik fiókban.

1. Ha szükséges, állítson elő egy új titkosítási kulcsot, és biztonságosan tárolja az érintett rendszereken kívül.

   Ha az adatok titkosítása az Azure-fürthöz készült avere-vFXT történik, akkor az adatok másik Storage-fiókba való másolása előtt új titkosítási kulcsot kell kiállítania. Biztonságosan tárolhatja a kulcsot és a jelszót olyan létesítményben, amely biztonságban van, és nem lesz hatással a regionális meghibásodásokra.

   Ezt a kulcsot akkor kell megadnia, amikor hozzáadja a tárolót egy fürthöz – még akkor is, ha az eredeti fürthöz újra hozzáadja.

   [Felhőbeli titkosítási beállítások](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>) olvasása<!-- link to legacy doc site --> részletes információk.

   Ha a tároló csak az Azure beépített titkosítását használja, akkor kihagyhatja ezt a lépést.

1. Távolítsa el a Core filert a rendszerből. Ez arra kényszeríti a fürtöt, hogy az összes módosított adatlemezt a háttérbeli tárolóba írja.

   Bár a biztonsági mentés után újra hozzá kell adnia a Core Filer-t, a rendszer eltávolítja a legjobb módszert annak biztosítására, hogy az összes adatfájl teljes mértékben a háttérbe legyen írva. (A "felfüggesztés" beállítás esetenként a gyorsítótárban is hagyhatja a módosult adatértékeket.) <!-- xxx true? or just metadata? -->

   Jegyezze fel az alapszintű Filer nevét és a csatlakozási információkat (a Vezérlőpult **névtér** lapján), hogy replikálja a tárolót a biztonsági mentést követően.

   Távolítsa el a Core filert a fürt Vezérlőpultjának használatával. [Nyissa meg a fürt Vezérlőpultját](avere-vfxt-cluster-gui.md) , és válassza a **Core** Filer  >  **Manage Core filers** elemet. Keresse meg azt a tárolási rendszert, amelyről biztonsági másolatot szeretne készíteni, majd az **Eltávolítás** gombbal törölje azt a fürtből.

1. Hozzon létre egy új, üres blob Storage-tárolót egy másik tárolási fiókban egy másik régióban.

1. Bármilyen kényelmes másolási eszközzel másolhatja az alapvető Filer-t az új tárolóra. A másolatnak módosítás nélkül replikálnia kell az adatfájlokat, és nem szabad megszakítania az Azure-hoz készült avere vFXT által használt szabadalmaztatott Cloud fájlrendszer-formátumot. Az Azure-alapú eszközök közé tartozik a [AzCopy](../storage/common/storage-use-azcopy-v10.md), a [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)és az [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Az adatok biztonsági mentési tárolóba való másolása után adja hozzá az eredeti tárolót a fürthöz a [tároló konfigurálása](avere-vfxt-add-storage.md)című témakörben leírtak szerint.

   * Használja ugyanazt az alapszintű Filer-nevet és-elágazási információt, hogy az ügyfél-munkafolyamatok ne kelljen módosítani.
   * Állítsa a **gyűjtő tartalmának** értékét a meglévő adatbeállításra.
   * Ha a tárolót a fürt titkosította, akkor az aktuális titkosítási kulcsot kell megadnia a tartalomhoz. (Ez az első lépésben frissített kulcs.)

Az első után készített biztonsági mentésekhez nem szükséges új tárolót létrehoznia. Azonban érdemes lehet új titkosítási kulcsot generálni, amikor biztonsági mentést végez, hogy meggyőződjön arról, hogy az aktuális kulcs egy olyan helyen van tárolva, amelyet megjegyez.

### <a name="access-a-backup-data-source-during-an-outage"></a>Biztonsági mentési adatforrás elérése leállás közben

Ha a biztonsági mentési tárolót az Azure-fürthöz tartozó avere-vFXT szeretné elérni, kövesse az alábbi eljárást:

1. Ha szükséges, hozzon létre egy új avere-vFXT az Azure-fürthöz egy nem érintett régióban.

   > [!TIP]
   > Amikor létrehoz egy avere-vFXT az Azure-fürthöz, mentheti a létrehozási sablonját és paramétereit. Ha ezt az információt az elsődleges fürt létrehozásakor menti, akkor a segítségével egy olyan helyettesítő fürtöt hozhat létre, amely azonos tulajdonságokkal rendelkezik. Az [Összefoglalás](avere-vfxt-deploy.md#validation-and-purchase) lapon kattintson a **sablon és paraméterek letöltése** hivatkozásra. Mentse az adatokat egy fájlba, mielőtt létrehozza a fürtöt.

1. Vegyen fel egy új, az ismétlődő blob-tárolóra mutató Cloud Core filert.

   Győződjön meg arról, hogy a célként megadott tároló már tartalmaz adatmennyiséget az alapszintű Filer-létrehozási varázsló **gyűjtői tartalom** beállítása területén. (A rendszernek riasztást kell kapnia, ha véletlenül **üresen** hagyja ezt a tulajdonságot.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Ha szükséges, frissítse az ügyfeleket úgy, hogy az új fürtöt vagy az új Core Filer-t az eredeti helyett csatlakoztassa. (Ha a Replacement Core filert az eredeti tárolóval megegyező névvel és csatlakozási útvonallal adja hozzá, akkor nem kell frissítenie az ügyfelek folyamatait, kivéve, ha új IP-címen kell csatlakoztatnia az új fürtöt.)

## <a name="next-steps"></a>Következő lépések

* Az Azure-hoz készült avere-vFXT beállításainak testreszabásával kapcsolatos további információkért olvassa el a [fürt finomhangolása](avere-vfxt-tuning.md)című témakört.
* További információ a vész-helyreállításról és az Azure-beli rugalmas alkalmazások létrehozásáról:

  * [Műszaki útmutató az Azure rugalmasságáról](/azure/architecture/framework/resiliency/overview)
  * [Helyreállítás egész régióra kiterjedő szolgáltatáskimaradás esetén](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Vészhelyreállítás és magas szintű rendelkezésre állás az Azure-alkalmazásokhoz](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->