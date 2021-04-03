---
title: Azure Lab Services – rendszergazdai útmutató | Microsoft Docs
description: Ez az útmutató segít a rendszergazdáknak, hogy Azure Lab Services használatával hozzanak létre és kezeljenek labor-fiókokat.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 3ad3ee38a6c08a6af85822d76012cc6dfc34ff4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462466"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – rendszergazdai útmutató
Az Egyetem Felhőbeli erőforrásait kezelő informatikai rendszergazdák általában felelősek az iskolájuk labor-fiókjának beállításához. Miután beállított egy labor-fiókot, a rendszergazdák vagy a pedagógusok létrehozzák a fiókban található laborokat. Ez a cikk átfogó áttekintést nyújt az érintett Azure-erőforrásokról és a létrehozásához szükséges útmutatóról.

![Az Azure-erőforrások Lab-fiókban való magas szintű nézetének ábrája.](./media/administrator-guide/high-level-view.png)

- A Labs egy Azure Lab Services tulajdonában lévő Azure-előfizetésen belül üzemel.
- A labor-fiókok, a megosztott képgyűjtemények és a képverziók az előfizetésen belül találhatók.
- A labor-fiókját és a megosztott képtárat is megtekintheti ugyanabban az erőforráscsoporthoz. Ebben a diagramban különböző erőforráscsoportok találhatók.

További információ az architektúráról: [Labs architektúra alapjai](./classroom-labs-fundamentals.md).

## <a name="subscription"></a>Előfizetés
Az Egyetem egy vagy több Azure-előfizetéssel is rendelkezhet. Az előfizetések segítségével kezelheti a számlázást és a biztonságot az azon belül használt összes Azure-erőforrás és-szolgáltatás esetében, beleértve a labor-fiókokat is.

A labor-fiók és az előfizetése közötti kapcsolat azért fontos, mert:

- A számlázás a labor-fiókot tartalmazó előfizetésen keresztül történik.
- Az előfizetés Azure Active Directory (Azure AD) bérlője számára biztosíthatja a felhasználók számára a Azure Lab Services elérését. Hozzáadhat felhasználókat labor-fiók tulajdonosként vagy közreműködőként, vagy tesztkörnyezet-Létrehozóként vagy labor-tulajdonosként is.

A Labs-t és a virtuális gépeket (VM) felügyeli és üzemelteti a Azure Lab Services tulajdonában lévő előfizetésen belül.

## <a name="resource-group"></a>Erőforráscsoport
Egy előfizetés egy vagy több erőforráscsoportot tartalmaz. Az erőforráscsoportok használatával létrehozhatók az Azure-erőforrások logikai csoportjai, amelyek ugyanabban a megoldásban együtt használatosak.  

Labor-fiók létrehozásakor be kell állítania azt az erőforráscsoportot, amely a labor-fiókot tartalmazza. 

[Megosztott képgyűjtemény](#shared-image-gallery)létrehozásakor is szükség van egy erőforráscsoporthoz. A labor-fiókját és a megosztott képtárat ugyanabba az erőforráscsoporthoz vagy két különálló erőforráscsoporthoz helyezheti el. Érdemes lehet ezt a második megközelítést használni, ha azt tervezi, hogy megosztja a képtárat a különböző megoldások között.

Amikor létrehoz egy tesztkörnyezet-fiókot, automatikusan létrehozhat és csatolhat megosztott képtárat.  Ez a beállítás azt eredményezi, hogy a labor fiók és a megosztott rendszerkép-katalógus külön erőforráscsoportok között jön létre. Ez a viselkedés akkor jelenik meg, ha követi a [megosztott rendszerkép konfigurálása a tesztkörnyezet létrehozásakor](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) oktatóanyagban ismertetett lépéseket. A cikk elején található rendszerkép ezt a konfigurációt használja. 

Javasoljuk, hogy az erőforráscsoportok felépítésének megtervezéséhez növelje meg az időkorlátot, mert a létrehozása után *nem* lehet módosítani a labor-fiókot vagy a megosztott képgyűjteményi erőforráscsoportot. Ha módosítania kell az erőforrás-csoportot ezen erőforrások esetében, törölnie kell, majd újra létre kell hoznia a labor-fiókot vagy a közös képtárat.

## <a name="lab-account"></a>Labor-fiók

A labor-fiókok egy vagy több laborhoz tárolóként szolgálnak. A Azure Lab Services első lépéseiben a leggyakoribb, hogy egyetlen labor-fiókkal rendelkezzenek. A labor használatának felskálázása után később további labor-fiókokat is létrehozhat.

Az alábbi lista olyan forgatókönyveket mutat be, amelyekben több labor-fiók is hasznos lehet:

- **Különböző házirend-követelmények kezelése a laborok között**

    Labor-fiók beállításakor beállíthatja azokat a házirendeket, amelyek a labor-fiók alatti *összes* laborra érvényesek, például:
    - Az Azure-beli virtuális hálózat olyan megosztott erőforrásokkal, amelyekhez a labor hozzáférhet. Előfordulhat például, hogy olyan Labs-készlettel rendelkezik, amely hozzáférést igényel egy megosztott adatkészlethez egy virtuális hálózaton belül.
    - A Labs által a virtuális gépek létrehozásához használható virtuálisgép-lemezképek. Előfordulhat például, hogy olyan Labs-készlettel rendelkezik, amely hozzáférést igényel a [Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Azure Marketplace-rendszerkép Data Science VM.

    Ha a laborok mindegyike egyedi házirend-követelményekkel rendelkezik, érdemes lehet külön Lab-fiókokat létrehozni az egyes laborok kezeléséhez.

- **Külön költségvetés kiosztása minden Lab-fiókhoz**
  
    Ahelyett, hogy az összes laboratóriumi költséget egyetlen labor-fiókon keresztül kellene jelentenie, lehet, hogy világosabban kiosztható költségvetést kell megadnia. Létrehozhat például külön labor-fiókokat az Egyetem matematikai részlegéhez, a számítástechnikai részleghez és így tovább, hogy a költségvetést a részlegek között terjessze.  Ezután a [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md)használatával megtekintheti az egyes labor-fiókok költségeit.

- **A kísérleti Labs elkülönítése az aktív vagy a termelési laborokból**
  
    Előfordulhat, hogy olyan esetekre van szüksége, amelyekben az aktív vagy a termelési laborok nem befolyásolása nélkül kell megváltoztatnia a szabályzatot a tesztkörnyezetben. Ilyen esetben a tesztelési célokra külön labor-fiók létrehozása lehetővé teszi a módosítások elkülönítését. 

## <a name="lab"></a>Tesztkörnyezet

A labor olyan virtuális gépeket tartalmaz, amelyek mindegyike egyetlen tanulóhoz van rendelve.  Általánosságban elmondható, hogy az alábbiakra számíthat:

- Minden osztályhoz egy labor tartozik.
- Hozzon létre egy új Labs-készletet az Ön által használt félévekhez, negyedévekhez vagy más akadémiai rendszerekhez. Az azonos rendszerképeket használó osztályok esetében [megosztott képtárat](#shared-image-gallery)kell használnia. Így a rendszerképeket újra felhasználhatja a laborokban és a tudományos időszakokban is.

A laborok szerkezetének meghatározásakor vegye figyelembe a következő szempontokat:

- **A laboron belüli összes virtuális gép ugyanarra a közzétett lemezképre van telepítve**

    Ennek eredményeképpen, ha olyan osztállyal rendelkezik, amelyhez különböző labor-lemezképeket kell közzétenni egyszerre, minden lemezképhez külön labort kell létrehozni.
  
- **A használati kvóta a labor szintjén van beállítva, és a laborban lévő összes felhasználóra érvényes.**

    Ha különböző kvótákat szeretne beállítani a felhasználók számára, külön Labs-t kell létrehoznia. Azonban előfordulhat, hogy a kvóta beállítása után több órát is hozzáadhat bizonyos felhasználókhoz.
  
- **Az indítási vagy leállítási ütemterv a tesztkörnyezet szintjén van beállítva, és a laborban található összes virtuális gépre vonatkozik.**

    A kvóta-beállításhoz hasonlóan, ha különböző ütemterveket kell beállítania a felhasználók számára, minden egyes ütemtervhez külön labort kell létrehoznia.

Alapértelmezés szerint minden tesztkörnyezet saját virtuális hálózattal rendelkezik.  Ha engedélyezve van a virtuális hálózat társítása, minden egyes tesztkörnyezet saját alhálózattal fog rendelkezni a megadott virtuális hálózattal.

## <a name="shared-image-gallery"></a>Közös Képtár

A megosztott képkatalógus egy labor-fiókhoz van csatolva, és központi tárházként szolgál a lemezképek tárolásához. A rendszer egy képet ment a katalógusba, ha egy oktató úgy dönt, hogy a labor sablonból származó virtuális gépről exportálja azt. Minden alkalommal, amikor egy oktató módosítja a sablon virtuális gépet, és exportálja, a rendszer menti a rendszerkép új verzióit, és karbantartja az előző verziókat.

Az oktatók közzétehetnek egy rendszerkép-verziót a megosztott rendszerkép-katalógusban, amikor új labort hoznak létre. Bár a katalógus egy rendszerkép több verzióját tárolja, a pedagógusok csak a legújabb verziót tudják kiválasztani a tesztkörnyezet létrehozásakor.

A megosztott rendszerkép-katalógus szolgáltatás egy opcionális erőforrás, amelyet esetleg nem kell azonnal megadnia, ha csak néhány labort használ. A megosztott rendszerkép-katalógus azonban számos előnyt kínál, amelyek hasznosak lehetnek a további Labs-ra való vertikális felskálázás során:

- **Egy sablon virtuálisgép-rendszerképének verzióit mentheti és kezelheti**

    Érdemes egyéni rendszerképet létrehozni, vagy változtatásokat (szoftvereket, konfigurációt stb.) készíteni a nyilvános Azure Marketplace-katalógusban található rendszerképre.  Például gyakori, hogy a pedagógusok eltérő szoftvereket vagy eszközöket telepítsenek. Ahelyett, hogy a tanulóknak manuálisan kellene telepíteniük ezeket az előfeltételeket, a sablon virtuálisgép-rendszerképének különböző verziói exportálhatók egy megosztott képkatalógusba. Ezeket a rendszerképeket az új Labs létrehozásakor is használhatja.

- **A sablon virtuálisgép-rendszerképeit megoszthatja és használhatja a laborokban**

    Mentheti és újra felhasználhatja a képeket, így nem kell minden alkalommal konfigurálnia, amikor új labort hoz létre. Ha például több osztálynak ugyanazt a rendszerképet kell használnia, akkor egyszer is létrehozhatja, majd exportálhatja azt a megosztott rendszerkép-katalógusba, hogy az a laboratóriumokban is megosztható legyen.

- **A lemezkép rendelkezésre állását az automatikus replikáció biztosítja**

    Amikor egy tesztkörnyezetben ment egy képet a megosztott képkatalógusba, a rendszer automatikusan replikálja [az azonos földrajzi régión belüli más régiókba](https://azure.microsoft.com/global-infrastructure/regions/). Ha egy régió esetében leállás történik, akkor a rendszer nem érinti a rendszerkép közzétételét a laborban, mert egy másik régióból származó rendszerkép-replika használható.  A virtuális gépek több replikából való közzététele is segíthet a teljesítményben.

A megosztott rendszerképek logikai csoportosításához a következők egyikét teheti:

- Hozzon létre több megosztott rendszerkép-gyűjteményt. Az egyes tesztkörnyezet-fiókok csak egy megosztott képtárat tudnak csatlakozni, ezért ehhez a beállításhoz több tesztkörnyezet-fiókot is létre kell hoznia.
- Egyetlen megosztott képtárat használjon, amelyet több Lab fiók is megosztva használ. Ebben az esetben az egyes tesztkörnyezet-fiókok csak azokat a lemezképeket engedélyezhetik, amelyek alkalmazhatók a fiókban található laborokra.

## <a name="naming"></a>Elnevezés

A Azure Lab Services első lépéseiben azt javasoljuk, hogy hozzon létre elnevezési konvenciókat az erőforráscsoportok, a labor-fiókok, a Labs és a megosztott képgyűjtemény számára. Bár a létrehozott elnevezési konvenciók egyediek lesznek a szervezet igényeinek megfelelően, a következő táblázat általános irányelveket tartalmaz:

| Erőforrás típusa | Szerepkör | Javasolt minta | Példák |
| ------------- | ---- | ----------------- | -------- | 
| Erőforráscsoport | Egy vagy több Lab-fiókot, valamint egy vagy több megosztott képgyűjteményt tartalmaz | \<organization short name\>-\<environment\>– RG<ul><li>A **szervezet rövid neve** azonosítja annak a szervezetnek a nevét, amelyet az erőforráscsoport támogat.</li><li>A **környezet** azonosítja az erőforrás környezetét, például a *kísérleti* vagy a *üzemi* környezetet.</li><li>A **RG** az erőforrástípus *erőforráscsoport*.</li></ul> | contosouniversitylabs – RG<br/>contosouniversitylabs-Pilot-RG<br/>contosouniversitylabs – Prod-RG |
| Labor-fiók | Egy vagy több labort tartalmaz | \<organization short name\>-\<environment\>– La<ul><li>A **szervezet rövid neve** azonosítja annak a szervezetnek a nevét, amelyet az erőforráscsoport támogat.</li><li>A **környezet** azonosítja az erőforrás környezetét, például a *kísérleti* vagy a *üzemi* környezetet.</li><li>A **La** az erőforrástípus Lab- *fiókja*.</li></ul> | contosouniversitylabs-La<br/>mathdeptlabs-La<br/>sciencedeptlabs – pilóta – La<br/>sciencedeptlabs-Prod-La |
| Tesztkörnyezet | Egy vagy több virtuális gépet tartalmaz |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>Az **osztály neve** azonosítja a labor által támogatott osztály nevét.</li><li>Az **időkeret** azonosítja azt az időkeretet, amelyben az osztály elérhető.</li>A **pedagógus azonosítója** azonosítja azt a pedagógust, aki a labor tulajdonosa.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| Közös Képtár | Egy vagy több virtuálisgép-rendszerkép-verziót tartalmaz | \<organization short name\>Galéria | contosouniversitylabsgallery |

További információ az egyéb Azure-erőforrások elnevezéséről: [Az Azure-erőforrások elnevezési konvenciói](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regions\locations

A Azure Lab Services erőforrásainak beállításakor meg kell adnia az adatközpont azon régióját vagy helyét, amely az erőforrásokat fogja tárolni. A következő szakaszok azt ismertetik, hogy egy adott régió vagy hely milyen hatással lehet a laborok beállításával érintett összes erőforrásra.

### <a name="resource-group"></a>Erőforráscsoport

A régió meghatározza azt az adatközpontot, ahol az erőforráscsoport információit tárolja. Az erőforráscsoporthoz tartozó Azure-erőforrások a szülőtől eltérő régióban lehetnek.

### <a name="lab-account"></a>Labor-fiók

A Lab-fiókok helye azt a régiót jelzi, amelyben az erőforrás található.  

### <a name="lab"></a>Tesztkörnyezet

A labor a következő tényezőktől függően változik:

  - **A labor-fiók egy virtuális hálózattal van összetársítva**
  
    A [virtuális hálózatokkal egyenrangú labor-fiókot](./how-to-connect-peer-virtual-network.md) is létrehozhat, ha ugyanabban a régióban vannak.  Ha egy tesztkörnyezet egy virtuális hálózattal van társítva, a Labs automatikusan ugyanabban a régióban jön létre, mint a labor-fiók és a virtuális hálózat.

    > [!NOTE]
    > Ha egy tesztkörnyezet egy virtuális hálózattal van társítva, a labor- **létrehozó engedélyezése a labor helyének kiválasztására** beállítás le van tiltva. További információ: [a tesztkörnyezet létrehozójának engedélyezése a tesztkörnyezet helyének kiválasztásához](./allow-lab-creator-pick-lab-location.md).
    
  - **Nem található virtuális hálózat, és a labor *-* készítők nem választhatják ki a labor helyét**
  
    Ha *nincs* virtuális hálózat társítva a labor-fiókkal, és a [labor létrehozói *nem jogosultak* a labor helyének kiválasztására](./allow-lab-creator-pick-lab-location.md), a Labs automatikusan létrejön egy olyan régióban, ahol rendelkezésre áll a virtuális gép kapacitása.  Pontosabban Azure Lab Services keresi a rendelkezésre állást azokban a [régiókban, amelyek a labor-fiókkal megegyező földrajzi területen belül vannak](https://azure.microsoft.com/global-infrastructure/regions).

  - **Nem található virtuális hálózat, *és* a labor-készítők választhatják a labor helyét**
       
    Ha *egyetlen* virtuális hálózat sincs összekapcsolva, és a [labor létrehozói *is* választhatják a labor helyét](./allow-lab-creator-pick-lab-location.md), a tesztkörnyezet által kiválasztható helyek a rendelkezésre álló kapacitástól függenek.

> [!NOTE]
> Annak biztosításához, hogy egy adott régió rendelkezik-e elegendő virtuálisgép-kapacitással, fontos, hogy a tesztkörnyezet létrehozásakor először a labor-fiókon keresztül igényelje a kapacitást.

Általános szabály egy erőforrás régiójának beállítása a felhasználókhoz legközelebb eső értékre. A Labs esetében ez azt jelenti, hogy a tanulók számára legközelebb eső labort hozza létre. Az olyan online kurzusok esetében, amelyeknek a tanulói a világ minden tájáról találhatók, a legjobb ítélet alapján hozzon létre egy központilag elhelyezett labort. Az osztályokat több laborba is feloszthatja a tanulók régiói szerint.

## <a name="vm-sizing"></a>Virtuális gép méretezése

Ha a rendszergazdák vagy a labor-készítők létrehoznak egy labort, a különböző virtuálisgép-méretek közül választhatnak, attól függően, hogy a tantermi igényeknek megfelelően működnek-e. Ne feledje, hogy a számítási méret rendelkezésre állása attól a régiótól függ, ahol a labor-fiókja található.

| Méret | Jellemzők | Adatsorozat | Javasolt használat |
| ---- | ----- | ------ | ------------- |
| Kicsi| <ul><li>2 &nbsp; mag</li><li>3,5 gigabájt (GB) RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A legmegfelelőbb a parancssorhoz, a webböngésző, az alacsony forgalmú webkiszolgálók, a kis és közepes adatbázisok megnyitásához. |
| Közepes | <ul><li>4 &nbsp; mag</li><li>7 &nbsp; GB &nbsp; RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Legmegfelelőbb a kapcsolódó adatbázisokhoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez. |
| Közepes (beágyazott virtualizálás) | <ul><li>4 &nbsp; mag</li><li>16 &nbsp; GB &nbsp; RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Legmegfelelőbb a kapcsolódó adatbázisokhoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez.
| Nagy | <ul><li>8 &nbsp; mag</li><li>16 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | A legjobb megoldás a gyorsabb processzorokat, nagyobb teljesítményű helyi lemezeket, nagyméretű adatbázisokat és nagyméretű memória-gyorsítótárat igénylő alkalmazások számára.  Ez a méret támogatja a beágyazott virtualizálás szolgáltatást is. |
| Nagyméretű (beágyazott virtualizálás) | <ul><li>8 &nbsp; mag</li><li>32 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | A legjobb megoldás a gyorsabb processzorokat, nagyobb teljesítményű helyi lemezeket, nagyméretű adatbázisokat és nagyméretű memória-gyorsítótárat igénylő alkalmazások számára. |
| Kis GPU (vizualizáció) | <ul><li>6 &nbsp; mag</li><li>56 &nbsp; GB &nbsp; RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | A legjobb megoldás a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz és a kódoláshoz olyan keretrendszerek használatával, mint az OpenGL és a DirectX. |
| Kis GPU (számítás) | <ul><li>6 &nbsp; mag</li><li>56 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |A legmegfelelőbb a számítógép-igényes alkalmazásokhoz, mint például az AI és a Deep learning. |
| Közepes GPU (vizualizáció) | <ul><li>12 &nbsp; mag</li><li>112 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | A legjobb megoldás a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz és a kódoláshoz olyan keretrendszerek használatával, mint az OpenGL és a DirectX. |

## <a name="manage-identity"></a>Identitás kezelése

Az [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) használatával a labor-fiókokhoz és a laborokhoz való hozzáféréshez a következő szerepköröket rendelheti hozzá:

- Labor-fiók **tulajdonosa**

    A labor-fiókot létrehozó rendszergazda automatikusan hozzárendeli a labor-fiók tulajdonosi szerepkörét. A tulajdonos szerepkör a következőket teheti:
     - A labor-fiók beállításainak módosítása
     - Tulajdonosként vagy közreműködőként más rendszergazdák is hozzáférhetnek a labor-fiókhoz.
     - Pedagógusok hozzáférésének engedélyezése a laborokhoz Létrehozóként, tulajdonosként vagy közreműködőként.
     - Az összes Labs létrehozása és kezelése a labor-fiókban.

- Labor-fiók **közreműködői**

    A közreműködő szerepkört hozzárendelő rendszergazda a következőket teheti:
    - A labor-fiók beállításainak módosítása
    - Az összes Labs létrehozása és kezelése a labor-fiókban.

    Azonban a közreműködő *nem tud* hozzáférést biztosítani más felhasználóknak sem labor-fiókokhoz, sem laborokhoz.

- **Tesztkörnyezet létrehozója**

    Ha labor-fiókon belül szeretne laborokat létrehozni, a pedagógusnak a labor létrehozói szerepkör tagjának kell lennie.  A labort létrehozó pedagógus automatikusan hozzá lesz adva labor-tulajdonosként. További információ: [felhasználó hozzáadása a labor Creator szerepkörhöz](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role). 

- Labor **tulajdonosa** vagy **közreműködője**
  
    A labor tulajdonosi vagy közreműködői szerepkör oktatói megtekinthetik és módosíthatják a labor beállításait. A személynek szintén tagja kell lennie a labor Account Reader szerepkörnek.

    A labor tulajdonosi és közreműködői szerepkörei között a legfontosabb különbség az, hogy csak egy tulajdonos adhat hozzáférést más felhasználóknak a laborok kezeléséhez. Egy közreműködő *nem* adhat hozzáférést más felhasználóknak a laborok kezeléséhez.

- **Közös Képtár**

    Ha megosztott képtárat csatlakoztat egy labor-fiókhoz, a labor-fiók tulajdonosai és közreműködői, valamint a labor-készítők, a labor-tulajdonosok és a labor-közreműködők automatikusan hozzáférést kapnak a képek megtekintéséhez és mentéséhez a gyűjteményben.

Szerepkörök hozzárendelésével a következő tippeket követheti:

   - Általában csak a rendszergazdáknak kell tartozniuk egy labor-fiók tulajdonosának vagy közreműködői szerepkörének. A labor-fióknak több tulajdonosa vagy közreműködői is lehet.
   - Ahhoz, hogy a pedagógusok új laborokat hozzanak létre, és kezelni tudják az általuk létrehozott laborokat, csak a labor létrehozói szerepkört kell hozzárendelnie.
   - Ahhoz, hogy a pedagógusok képesek legyenek bizonyos laborok kezelésére, de *nem* lehet új Labs-t létrehozni, Rendeljen hozzájuk tulajdonosi vagy közreműködői szerepkört mindegyik felügyelni kívánt laborhoz. Tegyük fel például, hogy egy professzort és egy tanítási asszisztenst is szeretne használni a laborban. További információ: [tulajdonosok hozzáadása laborhoz](./how-to-add-user-lab-owner.md).

## <a name="pricing"></a>Díjszabás

### <a name="azure-lab-services"></a>Azure Lab Services

A díjszabással kapcsolatos további információkért tekintse meg a [Azure Lab Services díjszabását](https://azure.microsoft.com/pricing/details/lab-services/).


### <a name="shared-image-gallery"></a>Megosztott rendszerkép-katalógus

A megosztott képkatalógus szolgáltatás díjszabását is figyelembe kell vennie, ha megosztott képtárakat szeretne használni a rendszerkép-verziók tárolására és kezelésére. 

A megosztott képgyűjtemény létrehozása és a labor-fiókhoz való csatolása ingyenes. Amíg nem ment egy rendszerkép-verziót a katalógusba, a költségek nem merülnek fel. A megosztott képgyűjtemény használatának díjszabása általában meglehetősen elhanyagolható, de fontos megérteni a számítás módját, mert nem szerepel a Azure Lab Services díjszabásában.  

#### <a name="storage-charges"></a>Tárolási díjak

Lemezkép-verziók tárolásához a megosztott képkatalógus szabványos merevlemez-meghajtó (HDD) által felügyelt lemezeket használ. A használt HDD által felügyelt lemez mérete az éppen tárolt lemezkép-verzió méretétől függ. A díjszabással kapcsolatos további információkért lásd: [felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Replikációs és hálózati kimenő forgalom díjai

Ha a rendszerképeket a tesztkörnyezet sablonjának használatával menti, akkor Azure Lab Services először a forrás-régióban tárolja, majd automatikusan replikálja a forrás rendszerkép verzióját egy vagy több célhelyre. 

Fontos megjegyezni, hogy Azure Lab Services automatikusan replikálja a forrás rendszerkép verzióját a földrajzi hely összes olyan [régiójába](https://azure.microsoft.com/global-infrastructure/regions/) , ahol a labor található. Ha például a labor az USA földrajzában van, a rendszer az Egyesült Államokban található nyolc régióba replikálja a rendszerkép verzióját.

A kimenő hálózati forgalom akkor fordul elő, ha egy rendszerkép verziója replikálódik a forrás régiójából a további célpontokra. A kiszámított összeg a rendszerkép verziójának méretétől függ, amikor a rendszerkép adatait először a forrás régiójából továbbítja a rendszer.  A díjszabással kapcsolatos részletekért lásd: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/).

Előfordulhat, hogy a kimenő forgalomra vonatkozó díjak az [oktatási megoldások](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) ügyfeleitől lemondanak. További információért forduljon a fiók kezelőjéhez. 

További információ: "milyen adatátviteli programok léteznek az akadémiai ügyfelek számára, és Hogyan szerezhetem be a jogosultságot?" a [programok az oktatási intézmények számára](https://azure.microsoft.com/pricing/details/bandwidth/) lapon a GYIK szakaszban talál.

#### <a name="pricing-example"></a>Példa a díjszabásra

Nézzük meg a sablon virtuálisgép-rendszerképének megosztott képgyűjteménybe való mentésének költségeit. A következő helyzetekben feltételezzük:

- Egy egyéni virtuálisgép-lemezképpel rendelkezik.
- A rendszerkép két verzióját menti.
- A labor az USA-ban található, amely összesen nyolc régióval rendelkezik.
- Minden rendszerkép-verzió 32 GB méretű; Ennek eredményeképpen a HDD által felügyelt lemez ára $1,54 havonta.

A havi teljes díj a következőképpen becsülhető meg:

* *Lemezképek száma – a &times; &times; replikák &times; felügyelt lemez árának száma = teljes költség havonta*

Ebben a példában a Cost a következő:

* 1 egyéni rendszerkép (32 GB) &times; 2 &times; 8 US-régió &times; $1,54 = $24,64/hó

> [!NOTE]
> Az előző számítás csak példaként szolgál. A megosztott képkatalógus használatával kapcsolatos tárolási költségeket fedi le, és *nem* tartalmazza a kimenő forgalom költségeit. A tárolás tényleges díjszabása: [Managed Disks díjszabása](https://azure.microsoft.com/en-us/pricing/details/managed-disks/).

#### <a name="cost-management"></a>Költségkezelés

A labor-fiókok rendszergazdái számára fontos, hogy a katalógusból rendszeresen törölje a szükségtelen rendszerkép-verziókat. 

Ne törölje a replikálást meghatározott régiókba úgy, hogy csökkentse a költségeket, bár ez a lehetőség a megosztott rendszerkép-katalógusban található. Előfordulhat, hogy a replikációs módosítások kedvezőtlen hatással vannak a Azure Lab Services a megosztott képtárban mentett rendszerképekből származó virtuális gépek közzétételére.

## <a name="next-steps"></a>Következő lépések

A laborok beállításával és kezelésével kapcsolatos további információkért lásd:

- [Lab-fiók telepítési útmutatója](account-setup-guide.md)  
- [Labor telepítési útmutatója](setup-guide.md)  
- [Cost Management tesztkörnyezetekhez](cost-management-guide.md)  
- [Azure Lab Services használata a Teams szolgáltatásban](lab-services-within-teams-overview.md)
