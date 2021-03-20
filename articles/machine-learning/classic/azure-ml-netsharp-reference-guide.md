---
title: 'ML Studio (klasszikus): net # Custom neurális hálózatok – Azure'
description: 'A net # neurális hálózatok specifikációs nyelvének szintaxisi útmutatója. Ismerje meg, hogyan hozhat létre egyéni neurális hálózati modelleket Azure Machine Learning Studio (klasszikus).'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: 5137b633f66088efbee41b96ba715eb3b18961dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519252"
---
# <a name="guide-to-net-neural-network-specification-language-for-machine-learning-studio-classic"></a>Útmutató a net # neurális hálózat specifikációjának nyelvéhez Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

A net # a Microsoft által fejlesztett nyelv, amely összetett neurális hálózati architektúrákat, például mély neurális hálózatokat vagy tetszőleges méretű dimenziók átruházását használja. Összetett struktúrákat használhat az adatok, például a képek, videók és hanganyagok megismerésének javítására.

A következő kontextusokban használhat háló # architektúra-specifikációt:

+ Minden neurális hálózati modul Microsoft Azure Machine Learning Studio (klasszikus): [többosztályos neurális hálózat](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [kétosztályos neurális hálózat](/azure/machine-learning/studio-module-reference/two-class-neural-network)és [neurális hálózat regressziója](/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Neurális hálózati függvények a Microsoft ML Serverban: [NeuralNet](/machine-learning-server/r-reference/microsoftml/neuralnet) és [rxNeuralNet](/machine-learning-server/r-reference/microsoftml/rxneuralnet)az R nyelvhez, valamint [rx_neural_network](/machine-learning-server/python-reference/microsoftml/rx-neural-network) a Pythonhoz.


Ez a cikk az egyéni neurális hálózatok NET # használatával történő fejlesztéséhez szükséges alapvető fogalmakat és szintaxist ismerteti.

+ Neurális hálózati követelmények és az elsődleges összetevők meghatározása
+ A net # specifikáció nyelvének szintaxisa és kulcsszavai
+ Példák a NET használatával létrehozott egyéni neurális hálózatokra #



## <a name="neural-network-basics"></a>Neurális hálózat alapjai

A neurális hálózati struktúra rétegekbe rendezett csomópontokból és a csomópontok közötti súlyozott kapcsolatokból (vagy élekből) áll. A kapcsolatok irányt mutatnak, és minden kapcsolathoz tartozik egy forrás csomópont és egy cél csomópont.

Minden betanítható réteg (rejtett vagy kimeneti réteg) egy vagy több **összekapcsolási kötegtel** rendelkezik. A kapcsolati kötegek egy forrásoldali rétegből és az adott forrásból származó kapcsolatok specifikációból állnak. Egy adott csomagban lévő összes kapcsolat a forrás és a cél rétegben. A net # esetében a rendszer a csomag célként megadott rétegéhez tartozó kapcsolódási köteget tekinti át.

A net # támogatja a különböző típusú kapcsolódási csomagokat, amelyek segítségével testre szabhatja, hogy a bemenetek hogyan vannak leképezve a rejtett rétegekre, és hogyan legyenek leképezve a kimenetek.

Az alapértelmezett vagy a standard csomag egy **teljes köteg**, amelyben a forrás réteg minden csomópontja a cél réteg minden csomópontjára csatlakoztatva van.

A net # emellett a következő négy típusú speciális csatlakoztatási köteget támogatja:

+ **Szűrt csomagok**. A predikátumot a forrás réteg csomópontja és a célként megadott réteg csomópontjának helyei alapján definiálhatja. A csomópontok akkor vannak csatlakoztatva, amikor a predikátum igaz.

+ A **többcsomagos csomagok**. Megadhatja a csomópontok kis környékét a forrás rétegben. A célként megadott réteg minden csomópontja a forrás réteg csomópontjainak egy környékéhez csatlakozik.

+ A kötegek és a **reagálási normalizálás kötegek** **összevonása** . Ezek hasonlóak azokhoz a többszintű kötegekhez, amelyekben a felhasználó a forrás rétegben lévő csomópontok kis környékét határozza meg. A különbség az, hogy az ezekben a kötegekben lévő élek súlya nem képezhető be. Ehelyett egy előre meghatározott függvény lesz alkalmazva a forrás csomópont értékeire a cél csomópont értékének meghatározásához.


## <a name="supported-customizations"></a>Támogatott testreszabások

A Azure Machine Learning Studio (klasszikus) szolgáltatásban létrehozott neurális hálózati modellek architektúrája a NET # használatával széles körben testreszabható. A következőket teheti:

+ Rejtett rétegek létrehozása és a csomópontok számának szabályozása az egyes rétegekben.
+ Határozza meg, hogyan kapcsolódjanak egymáshoz a rétegek.
+ Speciális kapcsolati struktúrák definiálása, például a felosztások és a súlyozási csomagok.
+ Különböző aktiválási funkciókat kell megadni.

A specifikáció nyelvi szintaxisának részletes ismertetését lásd: [struktúra specifikációja](#structure-specifications).

[Példák arra, hogy hogyan](#examples-of-net-usage)határozzák meg a neurális hálózatokat a gyakori gépi tanulási feladatokhoz, a szimplex-ból a komplexbe.

## <a name="general-requirements"></a>Általános követelmények

+ Pontosan egy kimeneti rétegnek, legalább egy bemeneti rétegnek, valamint nulla vagy több rejtett rétegnek kell lennie.
+ Minden réteg rendelkezik rögzített számú csomóponttal, amely fogalmi módon rendezhető egy tetszőleges méretű téglalap alakú tömbben.
+ A bemeneti rétegek nem rendelkeznek társított betanított paraméterekkel, és jelölik azt a pontot, ahol a példány adatai bekerülnek a hálózatba.
+ A betanítható rétegek (a rejtett és a kimeneti rétegek) olyan betanított paraméterekkel rendelkeznek, mint a súlyok és a torzítások.
+ A forrás-és a célként megadott csomópontoknak külön rétegben kell lenniük.
+ A kapcsolatoknak aciklikus kell lenniük; más szóval nem lehet olyan kapcsolati lánc, amely a kezdeti forrás csomóponthoz vezet vissza.
+ A kimeneti réteg nem lehet a kapcsolatfájl egyik rétegének a forrása.

## <a name="structure-specifications"></a>Struktúra specifikációi

A neurális hálózati struktúra specifikációja három részből áll: a **konstans deklarációtól**, a **réteg deklarációtól** és a **kapcsolatok deklarációjának**. Létezik egy választható **megosztási deklarációs** szakasz is. A szakaszt bármilyen sorrendben meg lehet adni.

## <a name="constant-declaration"></a>Állandó deklaráció

Konstans deklaráció megadása nem kötelező. Ez egy olyan módszert biztosít, amely a neurális hálózat definíciójában másutt használt értékeket határozza meg. A deklaráció utasítás egy azonosítóból, majd egy egyenlőségjelet és egy érték kifejezésből áll.

Az alábbi utasítás például állandót határoz meg `x` :

`Const X = 28;`

Ha egyszerre két vagy több állandót szeretne meghatározni, az azonosító nevét és az értékeket kapcsos zárójelek közé kell tenni, és pontosvesszővel kell elválasztani őket. Például:

`Const { X = 28; Y = 4; }`

Az egyes hozzárendelési kifejezések jobb oldalán egész szám, valós szám, logikai érték (igaz vagy hamis) vagy matematikai kifejezés lehet. Például:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Réteg deklarációja

A réteg deklarációjának megadása kötelező. Meghatározza a réteg méretét és forrását, beleértve annak kapcsolati kötegeit és attribútumait. A deklaráció utasítás a réteg (bemeneti, rejtett vagy kimeneti) nevével kezdődik, amelyet a réteg méretei (pozitív egész számok egy rekordja) követnek. Például:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ A dimenziók szorzata a rétegben lévő csomópontok száma. Ebben a példában két dimenzió létezik [5, 20], ami azt jelenti, hogy a rétegben 100 csomópont található.
+ A rétegeket bármilyen sorrendben deklarálhatja, egyetlen kivétellel: Ha több bemeneti réteg van definiálva, akkor a deklarált sorrendnek meg kell egyeznie a bemeneti adatokban szereplő funkciók sorrendjével.

Annak megadásához, hogy a rétegben lévő csomópontok száma automatikusan legyen meghatározva, használja a `auto` kulcsszót. A `auto` kulcsszó eltérő hatással van a rétegtől függően:

+ A bemeneti réteg deklarációjában a csomópontok száma a bemeneti adatokban található szolgáltatások száma.
+ Rejtett réteg deklarációjában a csomópontok száma a **rejtett csomópontok számának** a paraméter értéke által megadott szám.
+ A kimeneti réteg deklarációjában a csomópontok száma 2 a kétosztályos besorolásnál, 1 a regresszióhoz, és egyenlő a többosztályos besorolás kimeneti csomópontjainak számával.

A következő hálózati definíció például lehetővé teszi az összes réteg méretének automatikus meghatározását:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

A betanítható réteg (a rejtett vagy kimeneti rétegek) rétegbeli deklarációjában szerepelhetnek a kimeneti függvény (más néven aktiválási függvény), amely alapértelmezés szerint **szigmabél** a besorolási modellekhez, és **lineáris** a regressziós modellekhez. Akkor is, ha az alapértelmezett beállítást használja, explicit módon megadhatja az aktiválási funkciót, ha az egyértelművé tételhez szükséges.

A következő kimeneti függvények támogatottak:

+ szigmabél
+ lineáris
+ SOFTMAX szoftvert fejlesztettek
+ rlinear
+ Square
+ Sqrt
+ srlinear
+ ABS
+ TANH
+ brlinear

A következő deklaráció például a **SOFTMAX szoftvert fejlesztettek** függvényt használja:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Kapcsolatok deklarációja

A betanítható réteg definiálása után azonnal be kell jelentenie a kapcsolatokat a definiált rétegek között. A kapcsolódási köteg deklarációja a kulcsszóval kezdődik `from` , majd a köteg forrás rétegének és a létrehozandó kapcsolódási kötegnek a neve.

Jelenleg a kapcsolatok öt típusa támogatott:

+ A kulcsszó által jelzett **teljes** kötegek`all`
+ A kulcsszó által jelzett **szűrt** csomagok, `where` amelyeket egy predikátum kifejezés követ
+  A kulcsszó által jelzett, a következőhöz tartozó, a megkötési `convolve` attribútumokkal kiegészített, egymáshoz kapcsolódó csomagok
+ **Készletezési** csomagok, amelyeket a **Max. Pool** vagy **Mean Pool** kulcsszó jelöl
+ **Válasz normalizálása** kötegek, amelyeket a kulcsszó **válaszának normája** jelez

## <a name="full-bundles"></a>Teljes csomag

A teljes körű kapcsolódási köteg a forrás réteg minden csomópontja és a célként megadott réteg egyes csomópontjai közötti kapcsolatokat tartalmaz. Ez az alapértelmezett hálózati kapcsolattípus.

## <a name="filtered-bundles"></a>Szűrt csomagok

A szűrt kapcsolatok csomagjának specifikációja egy predikátumot tartalmaz, amely a C# lambda kifejezéshez hasonlóan van kifejezve. Az alábbi példa két szűrt csomagot definiál:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ A (z) esetében az `ByRow` `s` egy olyan paraméter, amely az indexet jelképezi a bemeneti réteg csomópontjainak téglalap alakú sorában, és egy olyan paraméter, amely az `Pixels` `d` indexet jelképezi a rejtett réteg csomópontjainak tömbje számára `ByRow` . Mindkettő típusa és a `s` `d` két hosszúságú egész számok rekordja. Elméletileg a és a, valamint az egész szám egészére kiterjedő egész számok, valamint a () és a () közötti tartományok `s` `0 <= s[0] < 10` `0 <= s[1] < 20` `d` `0 <= d[0] < 10` `0 <= d[1] < 12` .

+ A predikátum kifejezés jobb oldalán van egy feltétel. Ebben a példában minden értéknél, a `s` `d` feltétel értéke pedig igaz, a forrás réteg csomópontból a cél réteg csomópontra van egy szegély. Így ez a szűrési kifejezés azt jelzi, hogy a csomag tartalmaz egy, a csomópont által a csomópont által meghatározott csomópontot, `s` amelyben az összes olyan esetben, ahol az `d` s [0] a d [0] értékkel egyenlő.

A szűrt kötegek esetében megadhatja a súlyok készletét is. A **súlyok** attribútum értékének olyan lebegőpontos értéknek kell lennie, amelynek hossza megegyezik a köteg által definiált kapcsolatok számával. Alapértelmezés szerint a súlyok véletlenszerűen jönnek létre.

A súlyozási értékek a cél csomópont-index szerint vannak csoportosítva. Ez azt eredményezi, hogy ha az első cél csomópont csatlakozik a K forrás-csomópontjaihoz, a `K` **súlyok** rekord első eleme az első cél csomópont súlyozása, a forrás index sorrendjében. Ugyanez vonatkozik a többi cél csomópontra is.

A súlyok megadhatók közvetlenül állandó értékként. Ha például megismerte a súlyozást, a következő szintaxissal lehet állandóként megadnia őket:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Egyazon csomagok

Ha a betanítási adat homogén struktúrával rendelkezik, az adatkezelési kapcsolatok általában az adat magas szintű funkcióinak megismerésére szolgálnak. Például a képek, hangok és videók adatainak, a térbeli vagy időbeli dimenzióját meglehetősen egységesek lehetnek.

A többszintű kötegek olyan téglalap alakú **kerneleket** alkalmaznak, amelyek a dimenzión keresztül csúszott. Az egyes kernelek lényegében a helyi környezetekben alkalmazott súlyok készletét határozzák meg, amelyeket **kernel-alkalmazásoknak** nevezünk. Mindegyik kernel-alkalmazás a forrás réteg egyik csomópontjának felel meg, amelyet a **központi csomópontnak** nevezünk. A kernelek súlya több kapcsolat között van megosztva. A közösen használt kötegekben minden kernel téglalap alakú, és az összes kernel-alkalmazás azonos méretű.

A rendszer a következő attribútumokat támogatja:

A **InputShape** definiálja a dimenzióját a forrás réteghez. Az értéknek pozitív egész számokból álló rekordnak kell lennie. Az egész számok szorzatának egyenlőnek kell lennie a forrás réteg csomópontjainak számával, de más esetben nem kell megegyeznie a dimenzióját deklarált értékkel. Ennek a rekordnak a hossza a **aritása** érték lesz. A aritása jellemzően a függvény által elvégezhető argumentumok vagy operandusok számát jelöli.

A kernelek alakzatának és helyeinek meghatározásához használja a **KernelShape**, a **Stride**, a **padding**, a **LowerPad** és a **UpperPad** attribútumot:

+ **KernelShape**: (kötelező) az egyes kernelek dimenzióját határozza meg a rendszerhez. Az értéknek a köteg aritása egyenlő hosszúságú pozitív egész számnak kell lennie. A rekord minden összetevője nem lehet nagyobb, mint a **InputShape** megfelelő összetevője.

+ **Lépéshossz**: (nem kötelező) meghatározza az egymást követően a (az egyes dimenziók egy lépésének méretét), azaz a központi csomópontok közötti távolságot. Az értéknek olyan pozitív egész számokból álló rekordnak kell lennie, amelynek hossza a köteg aritása. A rekord minden összetevője nem lehet nagyobb, mint a **KernelShape** megfelelő összetevője. Az alapértelmezett érték egy olyan rekord, amelyben az összes összetevő eggyel egyenlő.

+ **Megosztás**: (nem kötelező) meghatározza az egyes dimenziók súlyozását. Az érték lehet egy logikai érték, vagy egy logikai értékből álló rekord, amely a köteg aritása hosszúságú. Egyetlen logikai érték van kiterjesztve, hogy a megfelelő hosszúságú rekord legyen a megadott értékkel egyenlő összes összetevővel. Az alapértelmezett érték egy olyan rekord, amely az összes igaz értéket tartalmazza.

+ **MapCount**: (nem kötelező) meghatározza, hogy a rendszer milyen számú szolgáltatási térképet tartalmaz a többszintű köteghez. Az érték lehet egyetlen pozitív egész szám vagy egy pozitív egész szám, amely a köteg aritása. Egyetlen egész értéket kell kiterjeszteni, hogy a megfelelő hosszúságú rekord legyen a megadott értékkel, és az összes többi összetevő eggyel egyenlő legyen. Az alapértelmezett érték egy. A funkciós térképek teljes száma a rekord összetevőinek szorzata. Ennek az összesített számnak az összetevői az összetevők között határozzák meg, hogyan vannak csoportosítva a funkció-hozzárendelési értékek a cél csomópontokban.

+ **Súlyok**: (nem kötelező) meghatározza a köteg kezdeti súlyozását. Az értéknek olyan lebegőpontos értékeket tartalmazó rekordnak kell lennie, amely a jelen cikk későbbi részében meghatározott kernelek száma a kernelek száma alapján. Az alapértelmezett súlyok véletlenszerűen jönnek létre.

A kitöltést vezérlő tulajdonságok két készlete van, a tulajdonságok kölcsönösen kizárják egymást:

+ **Kitöltés**: (nem kötelező) meghatározza, hogy a bemenet kitöltése egy **alapértelmezett kitöltési séma** használatával történjen-e. Az érték lehet egyetlen logikai érték, vagy lehet egy olyan logikai értékből álló rekord, amely a köteg aritása.

    Egyetlen logikai érték van kiterjesztve, hogy a megfelelő hosszúságú rekord legyen a megadott értékkel egyenlő összes összetevővel.

    Ha a dimenzió értéke TRUE (igaz), a forrás logikailag be van margója a dimenzióban nulla értékű cellákkal a további kernel alkalmazások támogatásához, például hogy az adott dimenzió első és utolsó kernelének központi csomópontjai a forrás rétegben lévő első és utolsó csomópontok. Így az egyes dimenziókban a "dummy" csomópontok számát a rendszer automatikusan határozza meg, hogy pontosan illeszkedjenek `(InputShape[d] - 1) / Stride[d] + 1` a megfelelő kernelekhez a párnázott forrás rétegbe.

    Ha egy dimenzió értéke hamis, a kernelek úgy vannak meghatározva, hogy az egyes oldalakon lévő csomópontok száma azonos legyen (legfeljebb 1). Az attribútum alapértelmezett értéke egy olyan rekord, amelyben az összes összetevő hamis értékkel egyenlő.

+ **UpperPad** és **LowerPad**: (nem kötelező) nagyobb mértékű szabályozást biztosít a használathoz. **Fontos:** Ezek az attribútumok meghatározhatók, ha és csak akkor, ha a fenti **kitöltési** tulajdonság **_nem_*_ van meghatározva. Az értékeknek egész szám értékű rekordok kell lenniük, amelynek hosszát a köteg aritása kell megadni. Ha ezek az attribútumok meg vannak adva, a rendszer a "dummy" csomópontokat hozzáadja a bemeneti réteg egyes dimenzióinak alsó és felső végéhez. Az egyes dimenziók alsó és felső végéhez hozzáadott csomópontok számát _* LowerPad**[i] és **UpperPad**[i] érték határozza meg.

    Annak biztosítása érdekében, hogy a kernelek csak "valódi" csomópontoknak feleljen meg, és ne a "dummy" csomópontokra, a következő feltételeknek kell teljesülniük:
  - A **LowerPad** minden összetevőjének szigorúan kisebbnek kell lennie, mint `KernelShape[d]/2` .
  - A **UpperPad** minden összetevője nem lehet nagyobb, mint `KernelShape[d]/2` .
  - Ezen attribútumok alapértelmezett értéke a 0 értékkel egyenlő összes összetevővel rendelkező rekord.

    A **kitöltés** beállítása = true beállítás lehetővé teszi, hogy a kernel középpontja a "valódi" bemeneten belül maradjon. Ez egy kicsit megváltoztatja a matematikai teljesítményt a kimeneti méret kiszámításához. A *D* kimeneti méret általában úgy van kiszámítva `D = (I - K) / S + 1` , hogy hol `I` van a bemeneti méret, `K` a kernel mérete, `S` a lépéshossz, és `/` az egész szám osztása (a nulla irányába kerekítve). Ha a UpperPad = [1, 1] érték van beállítva, a bemeneti méret `I` gyakorlatilag 29, és így tovább `D = (29 - 5) / 2 + 1 = 13` . Ha azonban a **padding** = True értékre van `I` állítva, lényegében a általa összeütközött `K - 1` `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14` . A **UpperPad** és a **LowerPad** értékeinek megadásával sokkal jobban kézben tarthatja a padding-t, mint ha csak a **kitöltést** választja = igaz értéket.

A kapcsolódó hálózatokkal és alkalmazásokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

+ [http://d2l.ai/chapter_convolutional-neural-networks/lenet.html ](http://d2l.ai/chapter_convolutional-neural-networks/lenet.html )
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Készletezési csomagok

A **készletezési kötegek a közös** kapcsolathoz hasonló geometriát alkalmaznak, de előre definiált függvényeket használnak a csomópont-értékek forrásához a cél csomópont értékének származtatása érdekében. Ezért a készletezési csomagok nem rendelkeznek betanítható állapottal (súlyok vagy torzítások). A készletezési csomagok támogatják az összes, a **megosztást**, a **MapCount** és a **súlyozást** is.

Általában a szomszédos készletezési egységek által összefoglalt kernelek nem fedik át egymást. Ha a lépéshossz [d] egyenlő a KernelShape [d] értékkel az egyes dimenziókban, akkor a beszerzett réteg a hagyományos helyi készletezési réteg, amely általában a közösen használt neurális hálózatokban van használatban. Mindegyik cél csomópont a forrás rétegben lévő kernel tevékenységeinek maximális vagy középértékét számítja ki.

Az alábbi példa egy készletezési csomagot mutat be:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ A köteg aritása 3: Ez a rekordok, és a hosszát `InputShape` `KernelShape` `Stride` .
+ A forrás rétegben lévő csomópontok száma `5 * 24 * 24 = 2880` .
+ Ez egy hagyományos helyi készletezési réteg, mert a **KernelShape** és a **lépéshossz** egyenlő.
+ A célként megadott rétegben található csomópontok száma `5 * 12 * 12 = 1440` .

További információ a rétegek készletezésével kapcsolatban:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (3,4. szakasz)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Válasz normalizálása kötegek

A **Válasz normalizálása** egy olyan helyi normalizáló séma, amelyet először az Geoffrey Hinton, az et al vezetett be, a papír [ImageNet besorolása pedig a mélyreható, a nagy-és a decentralizációs neurális hálózatokkal](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

A válasz normalizálása a neurális hálók általánosításának támogatására szolgál. Ha az egyik neuron nagyon magas aktiválási szinten vált, a helyi válasz normalizálása letiltja a környező neuronok aktiválási szintjét. Ez három paraméterrel ( `α` , `β` , és `k` ), valamint egy, a rendszerhez tartozó (vagy környéki) szerkezettel történik. Az **y** célként megadott réteg minden neuronja egy neuron **x** -nek felel meg a forrás rétegben. Az **y** aktiválási szintjét a következő képlet adja meg, ahol a `f` egy neuron aktiválási szintje, és `Nx` a kernel (vagy az **x** szomszédságában található neuronokat tartalmazó készlet), az alábbi, a következő összetételi struktúra által definiált módon:

![a többszabályos struktúra képlete](./media/azure-ml-netsharp-reference-guide/formula_large.png)

A válasz normalizálása kötegek támogatják az összes, a megosztást, a **MapCount** és a **súlyozást** **megosztó** attribútumot.

+ Ha a kernel olyan neuronokat tartalmaz, mint a ***x** _, a normalizálás sémája: _ * ugyanaz a leképezési normalizálás * *. Ha ugyanazt a leképezési normalizált szeretné meghatározni, a **InputShape** első koordinátája 1 értékkel kell rendelkeznie.

+ Ha a kernel olyan neuronokat tartalmaz, amelyek ugyanabban a térbeli helyzetben vannak, mint ***x** _, de a neuronok más térképeken találhatók, a normalizálás sémája a Maps-normalizálás * * alatt található. Az ilyen típusú válaszok normalizálása a valódi neuronokban található típus által ihletett oldalirányú gátlás formáját valósítja meg, amely a különböző térképeken kiszámított neuron-kimenetek közti nagy aktiválási szintek versenyét hozza létre. A leképezések normalizálása között az első koordináta csak egy egész szám lehet, és nem haladhatja meg a térképek számát, és a koordináták további részének 1 értékűnek kell lennie.

Mivel a válasz normalizálása kötegek előre definiált függvényt alkalmaznak a csomópont-értékek forrására a cél csomópont értékének meghatározásához, nem rendelkeznek betanítható állapottal (súlyok vagy torzítások).

> [!NOTE]
> A célként megadott réteg csomópontjai a kernelek központi csomópontjaihoz tartozó neuronoknak felelnek meg. Ha például a `KernelShape[d]` páratlan, akkor `KernelShape[d]/2` a a központi kernel csomópontnak felel meg. Ha `KernelShape[d]` még van, a központi csomópont a következő helyen található: `KernelShape[d]/2 - 1` . Ezért ha a `Padding[d]` értéke hamis, akkor az első és az utolsó `KernelShape[d]/2` csomópont nem rendelkezik a célként megadott rétegben lévő megfelelő csomópontokkal. A helyzet elkerüléséhez **adja meg a** következőt: [true, true,..., True].

A korábban leírt négy attribútumon kívül a válasz normalizálása csomagok is támogatják a következő attribútumokat:

+ **Alpha**: (kötelező) egy lebegőpontos értéket ad meg, amely megfelel `α` az előző képletnek.
+ **Béta**: (kötelező) `β` az előző képletben szereplő lebegőpontos értéket adja meg.
+ **Eltolás**: (nem kötelező) egy lebegőpontos értéket ad meg, amely megfelel `k` az előző képletnek. Alapértelmezés szerint 1.

Az alábbi példa a válasz normalizálása csomagot definiálja a következő attribútumok használatával:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ A forrás réteg öt térképet tartalmaz, amelyek mindegyike AOF dimenzióval rendelkezik, 1440-csomópontok összesen.
+ A **KernelShape** értéke azt jelzi, hogy ez ugyanaz a leképezési normalizálás réteg, ahol a környék egy 3x3-as téglalap alakú.
+ A **kitöltés** alapértelmezett értéke hamis, így a célként megadott rétegben csak 10 csomópont található az egyes dimenziókban. Ha a célként megadott rétegben egy olyan csomópontot szeretne felvenni, amely megfelel a forrás réteg minden csomópontjának, adja hozzá a kitöltés = [igaz, igaz, igaz] értéket. és módosítsa a RN1 méretét [5, 12, 12] értékre.

## <a name="share-declaration"></a>Megosztási nyilatkozat

A net # opcionálisan támogatja a megosztott súlyok használatát több köteg definiálásához. A két köteg súlya megosztható, ha a szerkezetük megegyeznek. A következő szintaxis a megosztott súlyozású csomagokat definiálja:

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

A következő megosztási deklaráció például meghatározza a réteg nevét, amely azt jelzi, hogy a súlyozásokat és a torzításokat is meg kell osztani:

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases
```

+ A bemeneti funkciók két egyenlő méretű bemeneti rétegre vannak particionálva.
+ A rejtett rétegek ezután a két bemeneti réteg magasabb szintű funkcióit számítják ki.
+ A Share-deklaráció azt adja meg, hogy a *H1* és a *H2* értéket ugyanúgy kell kiszámítani, mint a saját bemeneteit.

Azt is megteheti, hogy ezt két külön megosztási deklarációval is megadhatja a következőképpen:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

A rövid űrlapot csak akkor használhatja, ha a rétegek egyetlen köteget tartalmaznak. Általánosságban elmondható, hogy a megosztás csak abban az esetben lehetséges, ha a megfelelő struktúra azonos, ami azt jelenti, hogy azonos a méretük, a közös használatú geometriájuk, és így tovább.

## <a name="examples-of-net-usage"></a>Példák a net # használatára

Ez a szakasz néhány példát mutat be arra, hogyan használható a net # a rejtett rétegek hozzáadására, a rejtett rétegek más rétegekkel való interakciójának módjára, valamint a többszintű hálózatok kiépítésére.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Hozzon létre egy egyszerű egyéni neurális hálózatot: "Hello World" példa

Ez az egyszerű példa azt szemlélteti, hogyan hozható létre egy olyan neurális hálózati modell, amely egyetlen rejtett réteggel rendelkezik.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

A példa néhány alapszintű parancsot mutat be a következők szerint:

+ Az első sor a bemeneti réteget (named `Data` ) adja meg. A kulcsszó használatakor  `auto` a neurális hálózat automatikusan tartalmazza a bemeneti példákban szereplő összes funkció oszlopot.
+ A második sor létrehozza a rejtett réteget. A név a `H` rejtett réteghez van hozzárendelve, amelynek 200 csomópontja van. Ez a réteg teljes mértékben csatlakozik a bemeneti réteghez.
+ A harmadik sor meghatározza a kimeneti réteget (named `Out` ), amely 10 kimeneti csomópontot tartalmaz. Ha a neurális hálózat besorolásra van használatban, az osztályban egy kimeneti csomópont van. A kulcsszó **szigmabél** azt jelzi, hogy a kimeneti függvény a kimeneti rétegre lesz alkalmazva.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Több rejtett réteg definiálása: számítógépes jövőkép – példa

Az alábbi példa bemutatja, hogyan határozhat meg egy valamivel összetettebb neurális hálózatot több egyéni rejtett réteggel.

```Net#
// Define the input layers
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100]
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]
{
from Gather all;
from MetaData all;
}
```

Ez a példa a neurális hálózatok specifikációs nyelvének számos funkcióját szemlélteti:

+ A szerkezet két bemeneti réteget tartalmaz, `Pixels` és `MetaData` .
+ A `Pixels` réteg a két összekapcsolási köteghez tartozó forrásoldali réteg, a célként megadott rétegek `ByRow` és a `ByCol` .
+ A rétegek `Gather` és a `Result` célként megadott rétegek több összekapcsolási csomagban találhatók.
+ A kimeneti réteg, a a `Result` két összekapcsolási kötegben található, az egyik a második szint rejtett rétege `Gather` , a másik pedig a bemeneti réteg `MetaData` .
+ A rejtett rétegek és a megjelenő `ByRow` `ByCol` kifejezések használatával szűrt kapcsolatot is megadunk. Pontosabban a `ByRow` (z) [x, y] helyen lévő csomópont csatlakozik a csomóponthoz, `Pixels` amely az első index koordinálásával egyenlő a csomópont első koordinátája, x. Hasonlóképpen, `ByCol` Ha a (z) [x, y] helyen lévő csomópont csatlakozik a csomópontokhoz, a `Pixels` második index koordinálja a csomópont második koordinátája (y) egyikét.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Hozzon létre egy, a többosztályos besoroláshoz használandó egydimenziós hálózatot: számjegy-felismerési példa

A következő hálózat definíciója úgy van kialakítva, hogy felismerje a számokat, és bemutatja a neurális hálózatok testreszabására szolgáló speciális technikákat.

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;
```

+ A struktúra egyetlen bemeneti réteggel rendelkezik `Image` .
+ A kulcsszó `convolve` azt jelzi, hogy a névvel ellátott rétegek és a megnevezett rétegek `Conv1` `Conv2` . A rétegbeli deklarációk mindegyikét a rendszer a többes számú egymást követő attribútumok listája követi.
+ A net egy harmadik rejtett réteggel rendelkezik, `Hid3` amely teljesen csatlakoztatva van a második rejtett réteghez `Conv2` .
+ A kimeneti réteg `Digit` csak a harmadik rejtett réteghez csatlakozik `Hid3` . A kulcsszó `all` azt jelzi, hogy a kimeneti réteg teljesen csatlakoztatva van a következőhöz: `Hid3` .
+ A aritása három: a rekordok hossza, a `InputShape` `KernelShape` és a `Stride` `Sharing` .
+ A tömegek száma a kernelben `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26` . Vagy `26 * 50 = 1300` .
+ Az egyes rejtett rétegek csomópontjai a következőképpen számíthatók ki:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ A csomópontok teljes száma a (z) [50, 5, 5] réteg deklarált dimenzióját használatával számítható ki a következőképpen: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Mivel a `Sharing[d]` csak hamis `d == 0` , a kernelek száma `MapCount * NodeCount\[0] = 10 * 5 = 50` .

## <a name="acknowledgements"></a>Köszönetnyilvánítás

A neurális hálózatok architektúrájának testre szabására szolgáló net # nyelv a Microsoft számára készült az Katzenberger (Architect, Machine Learning) és a Alekszej Kamenev (szoftverfejlesztő mérnök, Microsoft Research) számára. Belsőleg használatos a gépi tanulási projektekhez és az alkalmazásokhoz, a képek észlelése és a szöveges elemzések között. További információ: [neurális hálók a Azure Machine learning Studióban – bevezetés a net #](/archive/blogs/machinelearning/neural-nets-in-azure-ml-introduction-to-net) használatába