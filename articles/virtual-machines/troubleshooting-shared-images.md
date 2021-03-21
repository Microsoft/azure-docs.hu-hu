---
title: A megosztott rendszerképekkel kapcsolatos problémák elhárítása az Azure-ban
description: Ismerje meg, hogyan lehet elhárítani a megosztott képtárakkal kapcsolatos problémákat.
author: olayemio
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: d80caf767d923ce2539ca254a8312371155a3104
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553731"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>Megosztott rendszerkép-galériák az Azure-ban – problémamegoldás

Ha problémákat tapasztal a megosztott képtárakon, a képdefiníciókban és a képverziókon végzett műveletekben, futtassa újra a sikertelen parancsot hibakeresési módban. A hibakeresési mód aktiválásához a `--debug` kapcsolót az Azure CLI-vel és a PowerShell-lel való `-Debug` kapcsolóval aktiválhatja. A hiba megtalálása után kövesse ezt a cikket a hibák megoldásához.


## <a name="creating-or-modifying-a-gallery"></a>Gyűjtemény létrehozása vagy módosítása ##

**Üzenet**: *a katalógus neve érvénytelen. Az engedélyezett karakterek angol alfanumerikus karakterek, aláhúzások és a közepén engedélyezett időszakok, legfeljebb 80 karakter. Az összes többi speciális karakter (beleértve a kötőjeleket is) nem engedélyezett.*  
**OK**: a katalógus neve nem felel meg az elnevezési követelményeknek.  
**Áthidaló megoldás**: válasszon egy olyan nevet, amely megfelel az alábbi feltételeknek: 
- 80 karakteres korláttal rendelkezik
- Csak angol betűket, számokat, aláhúzást és pontot tartalmaz
- Angol betűvel vagy számmal kezdődik és végződik

**Üzenet**: *az "galleryName" entitás neve érvénytelen az érvényességi szabálynak megfelelően: ^ [^ \_ \W] [\w-. \_ ] {0,79} (? <! [-.]) $.*  
**OK**: a katalógus neve nem felel meg az elnevezési követelményeknek.  
**Megkerülő megoldás**: válasszon egy olyan nevet a katalógusnak, amely megfelel a következő feltételeknek: 
- 80 karakteres korláttal rendelkezik
- Csak angol betűket, számokat, aláhúzást és pontot tartalmaz
- Angol betűvel vagy számmal kezdődik és végződik

**Üzenet**: *a megadott erőforrás neve <galleryName \> érvénytelen záró karaktereket tartalmaz: <karaktert \> . A név nem végződhet karakterrel: <karakter \>*  
**OK**: a katalógus neve pont vagy aláhúzás karakterrel végződik.  
**Megkerülő megoldás**: válasszon egy olyan nevet a katalógusnak, amely megfelel a következő feltételeknek: 
- 80 karakteres korláttal rendelkezik
- Csak angol betűket, számokat, aláhúzást és pontot tartalmaz
- Angol betűvel vagy számmal kezdődik és végződik

**Üzenet**: *a megadott hely <régiója \> nem érhető el a "Microsoft. számítás/galériák" erőforrástípus számára. Az erőforrástípus számára elérhető régiók listája..* .  
**OK**: a katalógushoz megadott régió helytelen, vagy hozzáférési kérelemre van szükség.  
**Áthidaló megoldás**: Győződjön meg arról, hogy a régió neve helyes. Ha a régió neve helyes, küldje el a régió [hozzáférési kérelmét](/troubleshoot/azure/general/region-access-request-process) .

**Üzenet**: *nem lehet törölni az erőforrást a beágyazott erőforrások törlése előtt.*  
**OK**: olyan katalógust próbált törölni, amely legalább egy meglévő rendszerkép-definíciót tartalmaz. A katalógusnak üresnek kell lennie ahhoz, hogy törölni lehessen.  
**Áthidaló megoldás**: törölje az összes rendszerkép-definíciót a katalógusban, majd folytassa a katalógus törlésével. Ha a rendszerkép definíciója rendszerkép-verziókat tartalmaz, törölnie kell a rendszerkép verzióit, mielőtt törölné a rendszerkép-definíciókat.

**Üzenet**: *a (z) "<galleryName" katalógus neve \> nem egyedi a (z) "" előfizetésen belül <subscriptionId> . Válasszon egy másik katalógus nevét.*  
**OK**: van egy már meglévő katalógusa ugyanazzal a névvel, és egy azonos nevű gyűjteményt próbált meg létrehozni.  
**Megkerülő megoldás**: válasszon másik nevet a gyűjteménynek.

**Üzenet**: *az erőforrás-<galleryName \> már létezik a (z \_ ) \> <resourceGroup <1 \> . régiójában. Nem hozható létre azonos nevű erőforrás a (z \_ ) <2. régióban \> . Válasszon ki egy új erőforrás-nevet.*  
**OK**: van egy már meglévő katalógusa ugyanazzal a névvel, és egy azonos nevű gyűjteményt próbált meg létrehozni.  
**Megkerülő megoldás**: válasszon másik nevet a gyűjteménynek.

## <a name="creating-or-modifying-image-definitions"></a>Rendszerkép-definíciók létrehozása vagy módosítása ##

**Üzenet**: *a (z) galleryImage. properties. <tulajdonság módosítása \> nem engedélyezett.*  
**OK**: megpróbálta módosítani az operációs rendszer típusát, az operációs rendszer állapotát, a Hyper-V generációját, az ajánlatot, a közzétevőt vagy az SKU-t. Ezen tulajdonságok bármelyikének módosítása nem engedélyezett.  
**Áthidaló megoldás**: hozzon létre helyette egy új rendszerkép-definíciót.

**Üzenet**: *az erőforrás-<galleryName/imageDefinitionName \> már létezik a (z \_ ) \> <resourceGroup erőforráscsoport <régiójában \> . Nem hozható létre azonos nevű erőforrás a (z \_ ) <2. régióban \> . Válasszon ki egy új erőforrás-nevet.*  
**OK**: van egy meglévő rendszerkép-definíciója ugyanabban a gyűjteményben és erőforráscsoporthoz ugyanazzal a névvel. Megpróbált létrehozni egy másik rendszerkép-definíciót ugyanazzal a névvel, és ugyanabban a gyűjteményben, de egy másik régióban.  
**Megkerülő megoldás**: használjon másik nevet a rendszerkép definíciója számára, vagy helyezze a rendszerkép definícióját egy másik katalógusba vagy erőforráscsoporthoz.

**Üzenet**: *a megadott erőforrás neve <GalleryName \> /<imageDefinitionName \> érvénytelen záró karaktereket tartalmaz: <karakter \> . A név nem végződhet karakterrel: <karakter \>*  
**OK**: a <imageDefinitionName \> neve pont vagy aláhúzás karakterrel végződik.  
**Áthidaló megoldás**: válassza ki a képdefiníció nevét, amely megfelel az alábbi feltételeknek: 
- 80 karakteres korláttal rendelkezik
- Csak angol betűket, számokat, aláhúzást, kötőjelet és pontot tartalmaz
- Angol betűvel vagy számmal kezdődik és végződik.

**Üzenet**: *az entitás neve <imageDefinitionName \> érvénytelen az érvényesítési szabálya szerint: ^ [^ \_ \\ W] [ \\ W-. \_ ] {0,79} (? <! [-.]) $"*  
**OK**: a <imageDefinitionName \> neve pont vagy aláhúzás karakterrel végződik.  
**Áthidaló megoldás**: válassza ki a képdefiníció nevét, amely megfelel az alábbi feltételeknek: 
- 80 karakteres korláttal rendelkezik
- Csak angol betűket, számokat, aláhúzást, kötőjelet és pontot tartalmaz
- Angol betűvel vagy számmal kezdődik és végződik

**Üzenet**: *az eszköz neve galleryImage. properties. Identifier. <tulajdonság érvénytelen \> . Nem lehet üres. Az engedélyezett karakterek: nagybetűk, kisbetűk, számjegyek, kötőjel (-), pont (.), aláhúzás ( \_ ). A név nem végződhet ponttal (.). A név hossza nem lehet hosszabb <szám \> karakternél.*  
**OK**: a közzétevő, az ajánlat vagy az SKU értéke nem felel meg az elnevezési követelményeknek.  
**Áthidaló megoldás**: válasszon olyan értéket, amely megfelel az alábbi feltételeknek: 
- 128 karakteres korlátot biztosít a közzétevőhöz vagy az 64-karakteres korláthoz az ajánlathoz és az SKU-hoz
- Csak angol betűket, számokat, kötőjeleket, aláhúzásokat és pontokat tartalmaz
- Nem végződhet ponttal

**Üzenet**: *nem hajtható végre a kért művelet a beágyazott erőforráson. A szülő erőforrás <galleryName \> nem található.*  
**OK**: nincs olyan katalógus, amelynek a neve <galleryName az \> aktuális előfizetésben és az erőforráscsoporthoz.  
**Áthidaló megoldás**: Győződjön meg arról, hogy a katalógus, az előfizetés és az erőforráscsoport neve helyes. Ellenkező esetben hozzon létre egy <galleryName nevű új katalógust \> .

**Üzenet**: *a megadott hely <régiója \> nem érhető el a "Microsoft. számítás/galériák" erőforrástípus számára. Az erőforrástípus számára elérhető régiók listája..* .  
**OK**: a <régió \> neve helytelen, vagy hozzáférési kérést igényel.  
**Áthidaló megoldás**: Győződjön meg arról, hogy helyesen írta-e be a régió nevét. A parancs futtatásával megtekintheti, hogy mely régiókban férhet hozzá. Ha a régió nem szerepel a listán, küldje el a [hozzáférési kérelmet](/troubleshoot/azure/general/region-access-request-process).

**Üzenet**: *nem szerializálható az érték: <\> az érték típusa: "ISO-8601"., ISO8601Error: ISO 8601 Time kijelölő nem hiányzik. Nem elemezhető a DateTime karakterlánc <\> értéke*  
**OK**: a tulajdonsághoz megadott érték nem megfelelően van formázva dátumként.  
**Megkerülő megoldás**: adjon meg egy dátumot az éééé-hh-nn, éééé-hh-dd'T'HH: PP: Sszzz vagy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)érvényes formátumban.

**Üzenet**: *nem lehet konvertálni a karakterláncot DateTimeOffset: <értékre \> . Elérési út tulajdonságai. <\> tulajdonság*  
**OK**: a tulajdonsághoz megadott érték nem megfelelően van formázva dátumként.  
**Megkerülő megoldás**: adjon meg egy dátumot az éééé-hh-nn, éééé-hh-dd'T'HH: PP: Sszzz vagy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)érvényes formátumban.

**Üzenet**: a *EndOfLifeDate egy jövőbeli dátumra kell beállítani.*  
**OK**: az elhasználódott Date tulajdonság nem megfelelően van formázva a mai dátum utáni dátumként.  
**Megkerülő megoldás**: adjon meg egy dátumot az éééé-hh-nn, éééé-hh-dd'T'HH: PP: Sszzz vagy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)érvényes formátumban.

**Üzenet**: *argumentum – <tulajdonság \> : érvénytelen int érték: <érték \>*  
**OK**: <tulajdonság \> csak egész értékeket fogad el, és <érték \> nem egész szám.  
**Áthidaló megoldás**: válasszon egész értéket.

**Üzenet**: *<tulajdonság minimális értéke \> nem lehet nagyobb, mint <tulajdonság maximális értéke \> .*  
**OK**: a <tulajdonsághoz megadott minimális érték \> nagyobb, mint a <tulajdonsághoz megadott maximális érték \> .  
**Áthidaló megoldás**: módosítsa az értékeket úgy, hogy a minimum kisebb legyen, mint a maximum.

**Üzenet**: *galéria képe: <\> által azonosított imageDefinitionName (közzétevő: <közzétevő \> , ajánlat: <ajánlat \> , SKU: <SKU \> ) már létezik. Válasszon másik közzétevőt, ajánlatot, SKU-kombinációt.*  
**OK**: megpróbált létrehozni egy új rendszerkép-definíciót ugyanazzal a közzétevővel, ajánlattal és SKU tripla néven, amely ugyanabban a gyűjteményben található.  
**Megkerülő megoldás**: a katalógusban minden rendszerkép-definíciónak a közzétevő, az ajánlat és az SKU egyedi kombinációjával kell rendelkeznie. Válasszon egyedi kombinációt, vagy válasszon ki egy új katalógust, és hozza létre újra a rendszerkép definícióját.

**Üzenet**: *nem lehet törölni az erőforrást a beágyazott erőforrások törlése előtt.*  
**OK**: olyan rendszerkép-definíciót próbált meg törölni, amely tartalmazza a rendszerkép verzióját. A rendszerkép definíciójának üresnek kell lennie ahhoz, hogy törölni lehessen.  
**Áthidaló megoldás**: törölje az összes rendszerkép-verziót a rendszerkép-definícióban, majd folytassa a rendszerkép definíciójának törlésével.

**Üzenet**: *nem köthető a (z) paraméter <tulajdonságához \> . Az érték <érték nem konvertálható \> <propertyType típusra \> . Az azonosító neve <érték nem egyezhet \> meg egy érvényes enumerálási névvel. Adjon meg egyet a következő enumerálási nevek közül, majd próbálkozzon újra: <choice1 \> , <choice2 \> ,...*  
**OK**: a tulajdonság a lehetséges értékek korlátozott listáját tartalmazza, és <érték \> nem tartozik ezek közé.  
**Áthidaló megoldás**: válassza ki az egyik lehetséges <választási \> értéket.

**Üzenet**: *nem köthető a (z) paraméter <tulajdonságához \> . A Value <érték nem konvertálható \> a &quot; System. &quot; datetime típusra.*  
**OK**: a tulajdonsághoz megadott érték nem megfelelően van formázva dátumként.  
**Megkerülő megoldás**: adjon meg egy dátumot az éééé-hh-nn, éééé-hh-dd'T'HH: PP: Sszzz vagy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)érvényes formátumban.

**Üzenet**: *nem köthető a (z) paraméter <tulajdonságához \> . A Value <érték nem konvertálható \> &quot; System. Int32 &quot; típusra.*  
**OK**: <tulajdonság \> csak egész értékeket fogad el, és <érték \> nem egész szám.  
**Áthidaló megoldás**: válasszon egész értéket.

**Üzenet**: a *ZRS nem támogatott ebben a régióban.*  
**OK**: a standard Zone-redundáns tárolást (ZRS) választotta egy olyan régióban, amely még nem támogatja azt.  
**Áthidaló megoldás**: módosítsa a Storage-fiók típusát **prémium \_ LRS** vagy **standard \_ LRS**. Tekintse meg a dokumentációt a ZRS előzetes [verzióval rendelkező régiók legújabb listájához](../storage/common/storage-redundancy.md#zone-redundant-storage) .

## <a name="creating-or-updating-image-versions"></a>Rendszerkép-verziók létrehozása vagy frissítése ##

**Üzenet**: *a megadott hely <régiója \> nem érhető el a "Microsoft. számítás/galériák" erőforrástípus számára. Az erőforrástípus számára elérhető régiók listája..* .  
**OK**: a <régió \> neve helytelen, vagy hozzáférési kérést igényel.  
**Áthidaló megoldás**: Győződjön meg arról, hogy helyesen írta-e be a régió nevét. A parancs futtatásával megtekintheti, hogy mely régiókban férhet hozzá. Ha a régió nem szerepel a listán, küldje el a [hozzáférési kérelmet](/troubleshoot/azure/general/region-access-request-process).

**Üzenet**: *nem hajtható végre a kért művelet a beágyazott erőforráson. A szülő erőforrás <galleryName/imageDefinitionName \> nem található.*  
**OK**: nincs olyan katalógus, amelynek a neve <GalleryName/imageDefinitionName a \> jelenlegi előfizetésben és az erőforráscsoporthoz.  
**Áthidaló megoldás**: Győződjön meg arról, hogy a katalógus, az előfizetés és az erőforráscsoport neve helyes. Ellenkező esetben hozzon létre egy új katalógust <galleryName \> és/vagy egy <imageDefinitionName nevű rendszerkép-definíciót \> a jelzett erőforráscsoporthoz.

**Üzenet**: *nem köthető a (z) paraméter <tulajdonságához \> . A Value <érték nem konvertálható \> a &quot; System. &quot; datetime típusra.*  
**OK**: a tulajdonsághoz megadott érték nem megfelelően van formázva dátumként.  
**Megkerülő megoldás**: adjon meg egy dátumot az éééé-hh-nn, éééé-hh-dd'T'HH: PP: Sszzz vagy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)érvényes formátumban.

**Üzenet**: *nem köthető a (z) paraméter <tulajdonságához \> . A Value <érték nem konvertálható \> &quot; System. Int32 &quot; típusra.*  
**OK**: <tulajdonság \> csak egész értékeket fogad el, és <érték \> nem egész szám.  
**Áthidaló megoldás**: válasszon egész értéket.

**Üzenet**: *a Képtár rendszerkép-közzétételi profiljának régiói <publishingRegions \> tartalmaznia kell a rendszerkép verziójának helyét \> <sourceRegion*  
**OK**: a forrás rendszerképének (<sourceRegion) helyét \> szerepelnie kell a <publishingRegions \> listájában.  
**Áthidaló megoldás**: <sourceRegion belefoglalása \> a <publishingRegions \> listába.

**Üzenet**: *a \> paraméter <tulajdonság értéke <értéke \> kívül esik a megengedett tartományon. Az értéknek <minValue \> és <maxValue \> között kell lennie.*  
**OK**: <\> az érték a <tulajdonság lehetséges értékeinek tartományán kívül esik \> .  
**Megkerülő megoldás**: válasszon olyan értéket, amely <minValue és <maxValue tartományon belül van \> \> , beleértve a-t.

**Üzenet**: *a forrás <resourceID \> nem található. Győződjön meg arról, hogy a forrás létezik, és ugyanabban a régióban található, mint a katalógus rendszerkép-verziója.*  
**OK**: nem található forrás <resourceID \> , vagy a forrás <resourceID \> nem ugyanabban a régióban van, mint a létrehozott katalógus-rendszerkép.  
**Áthidaló megoldás**: Győződjön meg arról, hogy a <resourceID \> értéke helyes, és hogy a katalógus rendszerképének verziószáma megegyezik a <resourceID értékének régiójával \> .

**Üzenet**: *a "galleryImageVersion. properties. StorageProfile. <diskImage \> . Source.id" tulajdonság módosítása nem engedélyezett.*  
**OK**: a létrehozás után a katalógus rendszerképének verziószáma nem módosítható.  
**Áthidaló megoldás**: Győződjön meg arról, hogy a forrás azonosítója megegyezik a meglévő forrás-azonosítóval, módosítsa a rendszerkép verziószámát, vagy törölje az aktuális rendszerkép verzióját, és próbálkozzon újra.

**Üzenet**: *a rendszer duplikált LUN-számokat észlelt a bemeneti adatlemezeken. A LUN számának egyedinek kell lennie az egyes adatlemezek esetében.*  
**OK**: Ha lemezkép-verziót hoz létre lemezek és/vagy lemezes Pillanatképek listájának használatával, két vagy több lemez vagy lemez pillanatképe ugyanazzal a logikai egységgel rendelkezik.  
**Áthidaló megoldás**: távolítsa el vagy módosítsa az ismétlődő logikai egységeket.

**Üzenet**: *duplikált forrás azonosítók találhatók a bemeneti lemezeken. A forrás azonosítójának minden lemez esetében egyedinek kell lennie.*  
**OK**: Ha lemezkép-verziót hoz létre lemezek és/vagy lemezes Pillanatképek listájának használatával, két vagy több lemez vagy lemez pillanatképe ugyanazzal az erőforrás-azonosítóval rendelkezik.  
**Áthidaló megoldás**: távolítsa el vagy módosítsa az ismétlődő lemezes források azonosítóit.

**Üzenet**: *a (z) resourceID <tulajdonság azonosítójának \> tulajdonságai. storageProfile. <diskimages \> . Source.id) érvénytelen. A "/subscriptions/{subscriptionId}" vagy a "/providers/{resourceProviderNamespace}/" kezdetű teljes erőforrás-azonosító várható.*  
**OK**: a <resourceID \> értéke helytelen formátumú.  
**Áthidaló megoldás**: Győződjön meg arról, hogy az erőforrás-azonosító helyes.

**Üzenet**: *a forrás azonosítója: <a resourceID \> felügyelt rendszerképnek, virtuális gépnek vagy más katalógus-rendszerképnek kell lennie*  
**OK**: a <resourceID \> értéke helytelen formátumú.  
**Megkerülő megoldás**: Ha virtuális gépet, felügyelt képet vagy katalógusbeli rendszerkép-verziót használ, ellenőrizze, hogy helyes-e a virtuális gép, a felügyelt rendszerkép vagy a gyűjtemény rendszerképének erőforrás-azonosítója.

**Üzenet**: *a forrás azonosítója: <a resourceID \> felügyelt lemeznek vagy pillanatképnek kell lennie.*  
**OK**: a <resourceID \> értéke helytelen formátumú.  
**Megkerülő megoldás**: ha lemezeket és/vagy lemez-pillanatképeket használ a lemezkép verziójának forrásaként, ellenőrizze, hogy helyesek-e a lemezek és/vagy a lemez-Pillanatképek erőforrás-azonosítói.

**Üzenet**: a katalógus *rendszerképének verziója nem hozható létre a következőből: <resourceID, \> mert a szülő katalógus rendszerképében lévő operációs rendszer állapota (<OsState \_ 1 \> ) nem <\_ 2 \> .*  
**OK**: az operációs rendszer állapota (általánosított vagy specializált) nem egyezik meg a rendszerkép definíciójában megadott operációsrendszer-állapottal.  
**Áthidaló megoldás**: válasszon egy olyan virtuális gépet, amelynek operációs rendszere <OsState \_ 1, \> vagy hozzon létre egy új rendszerkép-definíciót a virtuális gépekhez <OsState \_ 2 alapján \> .

**Üzenet**: *a (z) "<resourceID \> " azonosítójú erőforrás eltérő hypervisor-generációval rendelkezik ["<V # \_ 1 \> "], mint a szülő katalógus képfájljának hypervisor-generációja [' <V # \_ 2 \> ']*  
**OK**: a rendszerkép verziója nem felel meg a rendszerkép definíciójában megadott hypervisor-generációnak. A rendszerkép-definíció operációs rendszere <V # \_ 1 \> , a rendszerkép operációs rendszere pedig <v # \_ 2 \> .  
**Áthidaló megoldás**: válasszon egy olyan forrást, amelynek a hypervisor-generációja ugyanaz, mint a rendszerkép definíciója, vagy hozzon létre/válasszon egy új rendszerkép-definíciót, amelynek a hypervisor-generációja megegyezik a rendszerkép verziójával.

**Üzenet**: *a (z) "<resourceID" azonosítójú erőforráshoz a \> (z) ["<OsType 1"] eltérő operációsrendszer-típus tartozik, \_ mint a (z) \> ["<OsType \_ 2 \> "]*  
**OK**: a rendszerkép verziója nem felel meg a rendszerkép definíciójában megadott hypervisor-generációnak. A rendszerkép-definíció operációs rendszer <\_ 1 \> . OsType, és a rendszerkép verziója operációs rendszer <OsType \_ 2 \> .  
**Megkerülő megoldás**: válasszon ki egy azonos operációs rendszerrel (Linux/Windows) rendelkező forrást, vagy hozzon létre/válasszon egy új rendszerkép-definíciót, amelynek a rendszerkép verziója azonos az operációsrendszer-generációval.

**Üzenet**: *a forrásként szolgáló virtuális gép <resourceID \> nem tartalmazhat ideiglenes operációsrendszer-lemezt.*  
**OK**: a forrás <resourceID \> egy ideiglenes operációsrendszer-lemezt tartalmaz. A megosztott rendszerkép-gyűjtemény jelenleg nem támogatja az elmúló operációsrendszer-lemezeket.  
**Áthidaló megoldás**: válasszon másik forrást olyan virtuális gép alapján, amely nem használ ELmúló operációsrendszer-lemezt.

**Üzenet**: a *forrásként szolgáló virtuális gép <resourceID \> nem tartalmazhatja a (z) ["<Beskid \> "] UltraSSD-fiókban tárolt lemezét.*  
**OK**: <a Beskid lemez \> Ultra SSD lemez. A megosztott rendszerkép-katalógus jelenleg nem támogatja ultra SSD lemezeket.  
**Megkerülő megoldás**: használjon olyan forrást, amely csak prémium SSD, standard SSD és/vagy standard HDD felügyelt lemezeket tartalmaz.

**Üzenet**: *a forrás virtuális gép <resourceID \> Managed Disksból kell létrehozni.*  
**OK**: a <resourceID lévő virtuális gép nem \> felügyelt lemezeket használ.  
**Megkerülő megoldás**: használjon olyan virtuális gépeken alapuló forrást, amely csak prémium SSD, standard SSD és/vagy standard HDD felügyelt lemezeket tartalmaz.

**Üzenet**: *túl sok kérelem érkezett a (z) <resourceID forrásához \> . Csökkentse a kérések számát a forráson, vagy várjon egy ideig az újrapróbálkozás előtt.*  
**OK**: a rendszerkép forrása jelenleg túl sok kérelem miatt van szabályozva.  
**Áthidaló megoldás**: próbálja meg később létrehozni a rendszerkép verzióját.

**Üzenet**: *a (z) "<diskEncryptionSetID" lemezes titkosítási készletnek \> ugyanabban az előfizetésben kell lennie \> , mint a "<subscriptionID", mint a katalógus-erőforrás.*  
**OK**: a lemezes titkosítási készletek csak abban az előfizetésben és régióban használhatók, amelyben létrehozták őket.  
**Áthidaló megoldás**: hozzon létre vagy használjon titkosítási készletet ugyanabban az előfizetésben és régióban, mint a rendszerkép verziója.

**Üzenet**: a (z) *"<resourceID" titkosított forrás \> más előfizetés-azonosítóval rendelkezik, mint a jelenlegi katalógus-rendszerkép "<subscriptionID 1" előfizetése \_ \> . Próbálkozzon újra egy titkosítatlan forrással, vagy használja a forrás "<subcriptionID \_ 2" előfizetését \> a katalógus rendszerkép-verziójának létrehozásához.*  
**OK**: a megosztott képkatalógus jelenleg nem támogatja a rendszerkép-verziók más előfizetésben való létrehozását más forrásokból, ha a forrás képe titkosítva van.  
**Megkerülő megoldás**: használjon titkosítatlan forrást, vagy hozza létre a rendszerkép verzióját a forrással megegyező előfizetésben.

**Üzenet**: *a lemez titkosítási készlete <diskEncryptionSetID \> nem található.*  
**OK**: lehet, hogy helytelen a lemez titkosítása.  
**Áthidaló megoldás**: Győződjön meg arról, hogy helyes a lemez titkosítási készletének erőforrás-azonosítója.

**Üzenet**: *a rendszerkép-verzió neve érvénytelen. A rendszerkép verziószámának a következőnek kell lennie: nagy (int). Másodlagos (int). Patch (int) formátum, például: 1.0.0, 2018.12.1 stb.*  
**OK**: a rendszerkép verziójának érvényes formátuma három egész szám, ponttal elválasztva. A rendszerkép-verzió neve nem felelt meg az érvényes formátumnak.  
**Áthidaló megoldás**: használjon olyan rendszerkép-verziót, amely a Major (int) formátumú. Másodlagos (int). Javítás (int). Például: 1.0.0. vagy 2018.12.1.

**Üzenet**: *a galleryArtifactVersion. properties. publishingProfile. targetRegions. encryption. dataDiskImages. diskEncryptionSetId paraméter értéke érvénytelen* .  
**OK**: az adatlemez-lemezképben használt lemez titkosítási készlet erőforrás-azonosítója érvénytelen formátumot használ.  
**Áthidaló megoldás**: Győződjön meg arról, hogy a lemez titkosítási készletének erőforrás-azonosítója a következő formátumot követi:/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName \> .

**Üzenet**: *a galleryArtifactVersion. properties. publishingProfile. targetRegions. encryption. osDiskImage. diskEncryptionSetId paraméter értéke érvénytelen.*  
**OK**: az operációsrendszer-lemez lemezképén használt lemez titkosítási készlet erőforrás-azonosítója érvénytelen formátumot használ.  
**Áthidaló megoldás**: Győződjön meg arról, hogy a lemez titkosítási készletének erőforrás-azonosítója a következő formátumot követi:/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName \> .

**Üzenet**: *nem adható meg az új adatlemez-lemezkép titkosítási LUN [<szám \> ] a (z) [<region \> ] régióban a (z) A verzió frissítéséhez távolítsa el az új logikai egységet. Ha módosítania kell az adatlemez-lemezkép titkosítási beállításait, létre kell hoznia egy új katalógus-rendszerkép verzióját a megfelelő beállításokkal.*  
**OK**: a meglévő lemezkép-verzió adatlemezéhez titkosítást adott hozzá. Meglévő rendszerkép-verzióhoz nem lehet titkosítást hozzáadni.  
**Megkerülő megoldás**: hozzon létre egy új katalógus-rendszerkép verzióját, vagy távolítsa el a hozzáadott titkosítási beállításokat.

**Üzenet**: *a katalógus összetevő verziójának forrása csak közvetlenül a storageProfile alatt, illetve az egyes operációs rendszereken vagy adatlemezeken lehet megadni. Egy és csak egy Forrástípus (felhasználói lemezkép, pillanatkép, lemez, virtuális gép) adható meg.*  
**OK**: a forrás azonosítója hiányzik.  
**Megkerülő megoldás**: Győződjön meg arról, hogy a forrás azonosítója megtalálható.

**Üzenet**: a *forrás nem található: <resourceID \> . Győződjön meg arról, hogy a forrás létezik.*  
**OK**: lehet, hogy a forrás erőforrás-azonosítója helytelen.  
**Áthidaló megoldás**: Győződjön meg arról, hogy a forrás erőforrás-azonosítója helyes.

**Üzenet**: *a (z) galleryArtifactVersion. properties. publishingProfile. targetRegions. encryption. osDiskImage. diskEncryptionSetId a (z) "<1. régió" <diskEncryptionSetId) lemez titkosítási készlete a (z) "<2. régió" \_ \> \> terület megfelelő lemezéhez használatos \_ \>* .  
**OK**: a (z) <2. régió operációsrendszer-lemezén titkosítást használtak \_ \> , de nem az \_ 1. <régióban \> .  
**Áthidaló megoldás**: Ha az operációsrendszer-lemezen titkosítást használ, használja a titkosítást minden régióban.

**Üzenet**: *egy lemezes titkosítási készlet szükséges a (z) "<régió 1" terület "LUN <száma" lemezéhez \> , \_ mert a (z) " \> <diskEncryptionSetID" nevű lemezes titkosítási csoport a (z) \> "<régió \_ 2 \> " régió megfelelő lemezére van felhasználva* .  
**OK**: a (z) <régióban a LUN <számú adatlemez titkosítása a \> \_ 2 \> . <régióban nem található \_ \> .  
**Áthidaló megoldás**: Ha adatlemezen használ titkosítást, használja a titkosítást minden régióban.

**Üzenet**: *érvénytelen LUN [<szám \> ] volt megadva a encryption. dataDiskImages. A LUN értékének a következő értékek egyikének kell lennie: [' 0, 9 '].*  
**OK**: a titkosításhoz megadott logikai egység nem felel meg a virtuális géphez csatolt lemezekhez tartozó logikai egységeknek.  
**Áthidaló megoldás**: módosítsa a titkosításban található LUN-t a virtuális gépen található adatlemez Lun-ba.

**Üzenet**: *a (z) "<száma" duplikált logikai egységeket \> adtak meg a (z) "<régió \> " titkosítási. dataDiskImages.*  
**OK**: <régióban használt titkosítási beállítások \> legalább kétszer MEGADTÁK a logikai egységet.  
**Megkerülő megoldás**: módosítsa <régióban a LUN- \> t, és győződjön meg arról, hogy minden logikai egység egyedi a <régióban \> .

**Üzenet**: *a OSDiskImage és a DataDiskImage nem mutathat ugyanarra a \> blobra <SourceId forrásazonosító*  
**OK**: az operációsrendszer-lemez és legalább egy adatlemez forrása nem egyedi.  
**Áthidaló megoldás**: módosítsa az operációsrendszer-lemez és/vagy az adatlemez forrását, és győződjön meg arról, hogy az operációsrendszer-lemez és az egyes adatlemezek egyediek.

**Üzenet**: *ismétlődő régiók nem engedélyezettek a cél közzétételi régiókban.*  
**OK**: egynél többször szerepel a közzétételi régiók között a régió.  
**Áthidaló megoldás**: távolítsa el az ismétlődő régiót.

**Üzenet**: *új adatlemezek hozzáadása vagy egy adatlemez logikai egységének módosítása meglévő lemezképben nem engedélyezett.*  
**OK**: a lemezkép-verzióra irányuló frissítési hívás egy új adatlemezt tartalmaz, vagy egy lemezhez új LUN tartozik.  
**Megkerülő megoldás**: használja a meglévő rendszerkép-verzió logikai egységeit és adatlemezeit.

**Üzenet**: *a lemez titkosítási készletének <diskEncryptionSetID \> ugyanabban az előfizetési <subscriptionID kell lennie \> , mint a Gallery-erőforrásnak.*  
**OK**: a megosztott képtárat jelenleg nem támogatja egy másik előfizetésben lévő lemezes titkosítási csoport használata.  
**Áthidaló megoldás**: hozza létre a lemezkép verzióját és a lemez titkosítási készletét ugyanabban az előfizetésben.

**Üzenet**: *ebben a régióban a replikálás sikertelen volt, mert a GalleryImageVersion forrás-erőforrás mérete 2048 meghaladja a maximálisan támogatott 1024-os méretet.*  
**OK**: a forrásban lévő adatlemez nagyobb, mint 1 TB.  
**Áthidaló megoldás**: méretezze át az adatlemezt 1 TB-ra.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Virtuális gép vagy méretezési csoport létrehozása vagy frissítése rendszerkép-verzióból ##

**Üzenet**: *nem létezik a "<imageDefinitionResourceID \> " nevű legújabb rendszerkép-verzió*  
**OK**: a virtuális gép üzembe helyezéséhez használt rendszerkép-definíció nem tartalmaz a legújabb verziókban található lemezkép-verziókat.  
**Megkerülő megoldás**: Győződjön meg arról, hogy van legalább egy olyan rendszerkép-verzió, amelynek a "kizárás a legújabbból" beállítás értéke hamis. 

**Üzenet**: *az ügyfél rendelkezik engedéllyel a (z) "Microsoft. számítás/galériák/lemezképek/verziók/olvasás" művelet végrehajtásához a hatókör <resourceID \> , de a jelenlegi bérlő <tenantId1 \> nem jogosult a csatolt előfizetés <a subscriptionId2 elérésére \> .*  
**OK**: a virtuális gép vagy a méretezési csoport egy másik bérlőben található SIG-lemezképpel lett létrehozva. Megpróbálta módosítani a virtuális gépet vagy a méretezési készletet, de nincs hozzáférése a képet birtokló előfizetéshez.  
**Megkerülő megoldás**: lépjen kapcsolatba a rendszerkép-verzió előfizetésének tulajdonosával, hogy olvasási hozzáférést biztosítson a rendszerkép verziójához.

**Üzenet**: *a katalógus rendszerképe <resourceID \> nem érhető el <régió \> régiójában. Kérjük, lépjen kapcsolatba a rendszerkép tulajdonosával, hogy replikálja ezt a régiót, vagy módosítsa a kért régiót.*  
**OK**: a virtuális gép egy olyan régióban jön létre, amely nem szerepel a katalógus rendszerképében közzétett régiók listáján.  
**Áthidaló megoldás**: replikálja a rendszerképet a régióba, vagy hozzon létre egy virtuális gépet a katalógus rendszerképének közzétételi régióiban található egyik régióban.

**Üzenet**: *a "osProfile" paraméter használata nem engedélyezett.*  
**OK**: rendszergazdai felhasználónevet, jelszót vagy SSH-kulcsot adott meg egy speciális rendszerkép-verzióból létrehozott virtuális géphez.  
**Megkerülő megoldás**: ne adja meg a rendszergazdai felhasználónevet, jelszót vagy SSH-kulcsot, ha létre szeretne hozni egy virtuális gépet az adott rendszerképből. Ellenkező esetben használjon általánosított rendszerkép-verziót, és adja meg a rendszergazdai felhasználónevet, jelszót vagy SSH-kulcsot.

**Üzenet**: *hiányzik a kötelező "osProfile" paraméter (null).*  
**OK**: a virtuális gép általánosított rendszerképből lett létrehozva, és hiányzik a rendszergazdai Felhasználónév, jelszó vagy SSH-kulcs. Mivel az általánosított rendszerképek nem őrzik meg a rendszergazdai felhasználónevet, jelszót vagy SSH-kulcsot, ezeket a mezőket meg kell adni egy virtuális gép vagy méretezési csoport létrehozásakor.  
**Megkerülő megoldás**: határozza meg a rendszergazdai felhasználónevet, jelszót vagy SSH-kulcsot, vagy használjon speciális rendszerkép-verziót.

**Üzenet**: a katalógus *rendszerképének verziója nem hozható létre a következőből: <resourceID, \> mivel a szülő-katalógus rendszerképében lévő operációs rendszer állapota ("Specialized") nem "általánosított".*  
**OK**: a rendszerkép verziója egy általánosított forrásból jön létre, de a szülő definíciója speciális.  
**Áthidaló megoldás**: hozza létre a rendszerkép verzióját speciális forrás használatával, vagy használjon általánosított szülő-definíciót.

**Üzenet**: *nem frissíthető a virtuálisgép-méretezési csoport <vmssName \> , mert a virtuálisgép-MÉRETEZÉSi csoport aktuális operációsrendszer-állapota általánosítva van, amely nem azonos a frissített katalógus-rendszerkép operációsrendszer-állapotával.*  
**OK**: a méretezési csoport aktuális forrásának képe egy általánosított forrás rendszerkép, de a frissítése egy speciális forrás-lemezképpel történik. A méretezési csoport aktuális forrásának és az új forrás rendszerképének azonos állapotban kell lennie.  
**Áthidaló megoldás**: a méretezési csoport frissítéséhez használjon általánosított rendszerkép-verziót.

**Üzenet**: a *lemezes titkosítási készlet <diskEncryptionSetId \> a megosztott rendszerkép-katalógusban <VersionId az \> előfizetés <subscriptionId1 tartozik, \> és nem használható a (z) \> "" erőforrással az előfizetésben <subscriptionId2*  
**OK**: a lemezkép verziójának titkosításához használt lemezes titkosítási készlet egy másik előfizetésben található, mint a lemezkép verziójának futtatásához szükséges előfizetés.  
**Megkerülő megoldás**: használja ugyanazt az előfizetést a lemezkép verziója és a lemez titkosítási készlete számára.

**Üzenet**: *a virtuális gép vagy a virtuálisgép-méretezési csoport létrehozása hosszú időt vesz igénybe.*  
**Megkerülő megoldás**: Ellenőrizze, hogy a virtuális gép vagy virtuálisgép-méretezési csoport létrehozásához használni kívánt **OSType** azonos **OSType** rendelkezik-e a rendszerkép verziójának létrehozásához használt forrással. 

## <a name="creating-a-disk-from-an-image-version"></a>Lemez létrehozása lemezkép-verzióból ##

**Üzenet**: *a imageReference paraméter értéke érvénytelen.*  
**OK**: egy SIG-lemezkép verzióról lemezre való exportálást próbált meg használni, de a lemezképen nem létező LUN-pozíciót használt.    
**Megkerülő megoldás**: Ellenőrizze a rendszerkép verziószámát, hogy milyen logikai egységeket használ.

## <a name="sharing-resources"></a>Erőforrások megosztása

Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md)használatával a képkatalógus, a képdefiníciós és a rendszerkép-verzió erőforrásainak megosztása az előfizetések között engedélyezett. 

## <a name="replication-speed"></a>Replikációs sebesség

A **--Expand ReplicationStatus** jelzővel ellenőrizze, hogy befejeződött-e a replikáció az összes megadott régióban. Ha nem, várjon legfeljebb hat órát, amíg a feladatok befejeződik. Ha nem sikerül, aktiválja újra a parancsot a rendszerkép verziójának létrehozásához és replikálásához. Ha sok olyan célcsoport van, amelyet a lemezkép verziója replikál a alkalmazásba, akkor a replikálást fázisokban érdemes elvégezni.

## <a name="azure-limits-and-quotas"></a>Az Azure-ra vonatkozó korlátok és kvóták 

Az [Azure korlátai és kvótái](../azure-resource-manager/management/azure-subscription-service-limits.md) az összes megosztott képkatalógusra, a képdefinícióra és a rendszerkép verzió-erőforrásaira érvényesek. Győződjön meg arról, hogy az előfizetések korlátain belül van. 


## <a name="next-steps"></a>Következő lépések

További információ a [megosztott képtárakról](./shared-image-galleries.md).