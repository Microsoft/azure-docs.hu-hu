---
title: Service Fabric alkalmazások kapacitásának megtervezése
description: Útmutatás a Service Fabric alkalmazásokhoz szükséges számítási csomópontok számának azonosításához
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75377208"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Service Fabric alkalmazások kapacitásának megtervezése
Ebből a dokumentumból megtudhatja, hogyan becsülheti meg az Azure Service Fabric-alkalmazások futtatásához szükséges erőforrások mennyiségét (CPU-t, RAM-ot, lemezes tárolást). Az erőforrás-követelmények időbeli változása gyakori. Általában kevés erőforrásra van szükség a szolgáltatás fejlesztésekor/tesztelésekor, majd az éles környezetbe való bevezetéshez több erőforrásra van szükség, és az alkalmazása egyre népszerűbb. Az alkalmazás tervezésekor gondolja át a hosszú távú követelményeket, és olyan döntéseket hozzon, amelyek lehetővé teszik a szolgáltatás számára, hogy megfeleljen a nagy ügyfelek igényeinek.

 Service Fabric-fürt létrehozásakor eldöntheti, hogy a fürt milyen típusú virtuális gépeket (VM) hoz létre. Minden virtuális gép korlátozott mennyiségű erőforrást tartalmaz a processzorok (magok és sebesség), a hálózati sávszélesség, a RAM és a lemezes tárolás formájában. Ahogy a szolgáltatás idővel növekszik, olyan virtuális gépekre frissíthet, amelyek nagyobb erőforrást kínálnak, és/vagy több virtuális gépet adnak hozzá a fürthöz. Ha ezt az utóbbit szeretné elvégezni, először meg kell terveznie a szolgáltatást, hogy kihasználhassa a fürthöz dinamikusan hozzáadott új virtuális gépeket.

Egyes szolgáltatások nem csupán a virtuális gépeken tárolt adatkezelést kezelik. Ezért a szolgáltatások kapacitásának megtervezéséhez elsősorban a teljesítményre kell összpontosítania, ami azt jelenti, hogy a virtuális gépek megfelelő processzorait (magok és sebesség) választják. Emellett érdemes megfontolnia a hálózati sávszélességet, beleértve a hálózati átvitelek gyakoriságát és az adatátvitel mennyiségét. Ha a szolgáltatásnak jól kell megfelelnie a szolgáltatás használatának, akkor további virtuális gépeket adhat hozzá a fürthöz, és a hálózati kérések terheléselosztását az összes virtuális gépen keresztül végezheti el.

A virtuális gépek nagy mennyiségű adatmennyiségét kezelő szolgáltatások esetében a kapacitás tervezésének elsősorban a méretre kell összpontosítania. Ezért alaposan mérlegelje a virtuális gép RAM-és lemezes tárolásának kapacitását. A Windowsban a virtuális memória-kezelési rendszer lemezterületet eredményez a MEMÓRIÁban az alkalmazás kódjához. Emellett a Service Fabric Runtime intelligens lapozást biztosít, amely csak a memóriában tárolt és a hidegen tárolt adatoknak a lemezre történő áthelyezését teszi lehetővé. Az alkalmazások így több memóriát is használhatnak, mint amennyit fizikailag elérhetővé a virtuális gépen. A több RAM memóriával való ellátása egyszerűen növeli a teljesítményt, mivel a virtuális gép több lemezterületet is képes tárolni a RAM-ban. A kiválasztott virtuális gépnek elég nagy méretűnek kell lennie ahhoz, hogy tárolni lehessen a virtuális gépen használni kívánt adatmennyiséget. Hasonlóképpen, a virtuális gépnek elegendő RAM-mal kell rendelkeznie a kívánt teljesítmény biztosításához. Ha a szolgáltatás adatai idővel növekednek, további virtuális gépeket adhat hozzá a fürthöz, és particionálhatja az adatait az összes virtuális gép között.

## <a name="determine-how-many-nodes-you-need"></a>Határozza meg, hány csomópontra van szüksége
A szolgáltatás particionálása lehetővé teszi a szolgáltatás adatainak méretezését. További információ a particionálásról: [particionálás Service Fabric](service-fabric-concepts-partitioning.md). Minden partíciónak egyetlen virtuális gépen belül kell lennie, de a több (kis) partíció egyetlen virtuális gépre helyezhető el. Így a kisebb partíciók nagyobb rugalmasságot biztosítanak, mint néhány nagyobb partíció. A kikapcsolás azt eredményezi, hogy sok partíció növeli Service Fabric terhelést, és a partíciók között nem hajtható végre tranzakciós művelet. Több lehetséges hálózati forgalom is fennáll, ha a szolgáltatási kódnak gyakran kell hozzáférni a különböző partíciókban élő adatokhoz. A szolgáltatás tervezésekor alaposan meg kell fontolnia, hogy milyen előnyökkel és hátrányokkal kell megérkezniük egy hatékony particionálási stratégiára.

Tegyük fel, hogy az alkalmazása egyetlen állapot-nyilvántartó szolgáltatással rendelkezik, amelynek tárolási mérete várhatóan egy évig DB_Size GB-ra nő. További alkalmazásokat (és partíciókat) kíván hozzáadni, miközben az adott évben növekedni fog.  A replikációs tényező (RF), amely meghatározza, hogy a szolgáltatás replikái hányan befolyásolják a teljes DB_Size. Az összes replikán az összes DB_Size a replikálási tényező a DB_Size szorzata.  Node_Size a szolgáltatáshoz használni kívánt lemezterületet/RAM-ot jelöli. A legjobb teljesítmény érdekében a DB_Sizenek a fürt teljes memóriájában kell lennie, és a virtuális gép RAM-ban található Node_Size kell kiválasztani. A RAM-kapacitásnál nagyobb Node_Size kiosztásával a Service Fabric Runtime által biztosított lapozásra támaszkodik. Ezért előfordulhat, hogy a teljesítmény nem optimális, ha a teljes adatok gyorsak (mivel az adatok bekerülnek a be-vagy kifelé). Számos olyan szolgáltatás esetében azonban, ahol csak az adatok egy része meleg, a költséghatékonyság költséghatékony.

A maximális teljesítményhez szükséges csomópontok száma a következőképpen számítható ki:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Fiók a növekedéshez
Érdemes lehet kiszámítani a csomópontok számát azon DB_Size alapján, amelyek várhatóan növekednek a szolgáltatásban, és a megkezdett DB_Size mellett. Ezután növelje a csomópontok számát a szolgáltatás növekedésével, hogy ne legyenek túlterhelve a csomópontok száma. A partíciók számát azonban azon csomópontok száma alapján kell megadni, amelyekre szükség van a szolgáltatás maximális növekedéssel történő futtatásakor.

Érdemes lehet néhány további gépet bármikor elérhetővé tenni, így bármilyen váratlan csúcsot vagy hibát kezelhet (például ha néhány virtuális gép leáll).  Míg a további kapacitást a várt tüskékkel kell meghatározni, a kiindulási pont a néhány további virtuális gép (5-10 százalékos extra) lefoglalása.

A fentiekben egyetlen állapot-nyilvántartó szolgáltatást feltételez. Ha egynél több állapot-nyilvántartó szolgáltatással rendelkezik, fel kell vennie a többi szolgáltatáshoz társított DB_Size az egyenletbe. Azt is megteheti, hogy a csomópontok számát külön kiszámítja az egyes állapot-nyilvántartó szolgáltatásokhoz.  Előfordulhat, hogy a szolgáltatás replikákat vagy partíciókat tartalmaz, amelyek nem kiegyensúlyozottak. Ne feledje, hogy a partíciók a többinél több adattal is rendelkezhetnek. A particionálással kapcsolatos további információkért lásd az [ajánlott eljárások particionálásával foglalkozó cikket](service-fabric-concepts-partitioning.md). Az előző egyenlet azonban a partíció és a replika agnosztikus, mivel Service Fabric biztosítja, hogy a replikák optimalizált módon legyenek kiosztva a csomópontok között.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Táblázat használata a költségadatok kiszámításához
Most tegyünk néhány valós számot a képletbe. Egy [példa táblázat](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) bemutatja, hogyan tervezze meg egy olyan alkalmazás kapacitását, amely három típusú adatobjektumot tartalmaz. Minden objektum esetében a méretet és a várt objektumok számát közelítjük meg. Azt is kiválasztjuk, hogy az egyes objektumtípusok hány replikát kívánnak használni. A táblázat kiszámítja a fürtben tárolandó memória teljes mennyiségét.

Ezután adja meg a virtuális gép méretét és a havi költségeket. A virtuális gép mérete alapján a táblázat megadja a partíciók minimális számát, amelyet az adatfelosztáshoz használnia kell, hogy a csomópontokon fizikailag illeszkedjenek. Előfordulhat, hogy nagyobb számú partíciót szeretne kezelni az alkalmazás konkrét számítási és hálózati adatforgalmi igényei szerint. A táblázat a felhasználói profil objektumait kezelő partíciók számát jeleníti meg egy és hat között.

Most, hogy mindezen információk alapján a táblázat azt mutatja, hogy fizikailag lekérheti az összes adatot a kívánt partíciókkal és replikákkal egy 26 csomópontos fürtön. Ez a fürt azonban sűrűn csomagolva lenne, ezért érdemes lehet néhány további csomópontot használni a csomópontok hibáinak és frissítéseinek befogadásához. A táblázat azt is mutatja, hogy több mint 57 csomóponttal rendelkezik, és nem tartalmaz további értéket, mert üres csomópontokkal rendelkezett volna. A csomópontok meghibásodásának és frissítéseinek eléréséhez érdemes lehet a 57-csomópontok felett is meghaladni. A számolótáblát beállíthatja úgy, hogy megfeleljen az alkalmazás konkrét igényeinek.   

![A Cost számításához használt táblázat][Image1]

## <a name="next-steps"></a>Következő lépések
A szolgáltatás particionálásával kapcsolatos további tudnivalókért tekintse meg a [Service Fabric-szolgáltatások particionálását][10] ismertető témakört.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
