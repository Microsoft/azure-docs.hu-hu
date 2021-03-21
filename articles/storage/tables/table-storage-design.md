---
title: Méretezhető és teljesítményű táblák tervezése az Azure Table Storage-ban. | Microsoft Docs
description: Megtudhatja, hogyan tervezhet méretezhető és teljesítményű táblákat az Azure Table Storage-ban. Tekintse át a táblák partícióit, az entitás-csoportok tranzakcióit, valamint a kapacitás és a Cost szempontjait.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 03/09/2020
ms.subservice: tables
ms.openlocfilehash: 8f3bd2a998066804bfb589e3262ac5e68db601fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93306947"
---
# <a name="design-scalable-and-performant-tables"></a>Méretezhető és nagy teljesítményű táblák tervezése

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

A skálázható és az elvégezhető táblázatok kialakításához olyan tényezőket kell figyelembe venni, mint a teljesítmény, a méretezhetőség és a költséghatékonyság. Ha korábban már tervezett sémákat a kapcsolati adatbázisokhoz, ezek a szempontok jól ismertek, de bár vannak hasonlóságok az Azure Table service Storage-modell és a kapcsolati modellek között, szintén fontos különbségek vannak. Ezek a különbségek jellemzően olyan különböző kialakításokhoz vezetnek, amelyek a viszonyítási adatbázisok megismerése érdekében hasznosak vagy helytelenek lehetnek, de érdemes lehet a NoSQL kulcs/érték tárolót, például az Azure Table service-t tervezni. A tervezési eltérések jelentős része annak a ténynek felel meg, hogy a Table service olyan felhőalapú alkalmazások támogatására lett tervezve, amelyek több milliárd entitást (vagy a kapcsolódó adatbázis-terminológia sorait) tartalmazhatnak, illetve olyan adatkészleteket, amelyeknek támogatniuk kell a nagy tranzakciós köteteket. Ezért másképp kell meggondolni, hogyan tárolja az adatait, és hogyan tudja értelmezni a Table service működését. A jól megtervezett NoSQL-adattár lehetővé teszi, hogy a megoldás sokkal nagyobb mértékben és olcsóbban méretezhető legyen, mint a viszonyítási adatbázist használó megoldás. Ez az útmutató segítséget nyújt ezekről a témakörökről.  

## <a name="about-the-azure-table-service"></a>Tudnivalók az Azure Table service
Ez a szakasz a Table service főbb jellemzőit mutatja be, amelyek különösen a teljesítmény és a méretezhetőség tervezéséhez szükségesek. Ha még nem ismeri az Azure Storage-t és a Table servicet, először olvassa el a [Microsoft Azure Storage](../../storage/common/storage-introduction.md) és az [Azure Table Storage használatának első lépéseit](../../cosmos-db/tutorial-develop-table-dotnet.md) , mielőtt beolvassa a cikk hátralévő részét. Bár az útmutató témája a Table service, az Azure üzenetsor és a blob Services megvitatását, valamint azt, hogy miként használhatja őket a Table service.  

Mi a Table service? Ahogy a neve is várható, a Table service táblázatos formátumot használ az adatok tárolására. A standard terminológiában a tábla minden sora egy entitást jelöl, és az oszlopok az entitás különböző tulajdonságait tárolják. Minden entitás rendelkezik egy pár kulccsal az egyedi azonosításhoz, valamint egy időbélyeg-oszlophoz, amelyet a Table service az entitás utolsó frissítésének nyomon követéséhez használ. A rendszer automatikusan alkalmazza az időbélyeget, és nem tudja manuálisan felülírni az időbélyeget tetszőleges értékkel. A Table service ezt az utolsó módosítás timestamp (LMT) használatával kezeli az optimista egyidejűséget.  

> [!NOTE]
> A Table service REST API műveletek egy **ETAG** értéket is visszaadnak, amelyet az LMT-ből származtatnak. Ez a dokumentum a ETag és az LMT által megváltoztathatatlan kifejezéseket használja, mivel ugyanazok az alapul szolgáló adatokat használják.  
> 
> 

Az alábbi példa egy egyszerű tábla-kialakítást mutat be az alkalmazottak és részleg entitások tárolásához. Az útmutatóban később bemutatott példák közül sokat ezen az egyszerű kialakításon alapul.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Jún</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Részleg</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Eddig ezek az adathalmazok a relációs adatbázisban lévő táblákhoz hasonlóan jelennek meg, amelyek a kötelező oszlopok fő különbségei, valamint a több entitás típusának egyazon táblában való tárolásának lehetősége. Emellett a felhasználó által definiált tulajdonságok (például a **FirstName** vagy a **Age** ) adattípusa (például Integer vagy string) egy, a viszonyítási adatbázisban lévő oszlophoz hasonlóan. A relációs adatbázistól eltérően azonban a Table service séma nélküli jellege azt jelenti, hogy egy tulajdonságnak nem kell ugyanazzal az adattípussal rendelkeznie minden entitáson. Az összetett adattípusok egyetlen tulajdonságba való tárolásához szerializált formátumot kell használni, például JSON vagy XML. További információ a Table Service-ről, például a támogatott adattípusokról, a támogatott dátumtartomány, az elnevezési szabályok és a méretek megkötéséről: [a Table Service adatmodell ismertetése](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Az Ön által választott **PartitionKey** és **RowKey** a jó tábla kialakításához elengedhetetlen. A táblában tárolt összes entitásnak a **PartitionKey** és a **RowKey** egyedi kombinációjának kell lennie. Mint a **PartitionKey** és a **RowKey** értékek, mint a kulcsok egy kapcsolódó adatbázis-táblában, a rendszer indexeli az indexeket, és létrehoz egy fürtözött indexet a gyors keresések lehetővé tételéhez. A Table service azonban nem hoz létre másodlagos indexeket, így a **PartitionKey** és a **RowKey** az egyetlen indexelt tulajdonság. A [táblázatos tervezési mintákban](table-storage-design-patterns.md) leírt minták némelyike szemlélteti, hogy miként lehet megkerülni ennek a látszólagos korlátozásnak a körét.  

Egy tábla egy vagy több partícióból áll, és a tervezési döntések többsége egy megfelelő **PartitionKey** és **RowKey** kiválasztásával segíti a megoldás optimalizálását. Egy megoldás egy olyan táblából állhat, amely az összes olyan entitást tartalmazza, amely partíciókat rendez, de általában egy megoldás több táblával rendelkezik. A táblázatok segítségével logikailag rendszerezheti az entitásokat, és a hozzáférés-vezérlési lista segítségével kezelheti az adathozzáférést, és egyetlen tárolási művelettel elvégezheti a teljes tábla eldobását.  

## <a name="table-partitions"></a>Tábla partíciói
A fiók neve, a tábla neve és a **PartitionKey** együtt azonosítja azt a tárolási szolgáltatáson belüli partíciót, amelyben a Table szolgáltatás tárolja az entitást. Az entitások címzési sémájának részeként a partíciók határozzák meg a tranzakciók hatókörét (lásd az alábbi [Entity Transactions-tranzakciókat](#entity-group-transactions) ), és a Table Service skálázásának alapjait alkotják. További információ a partíciókon: a [Table Storage teljesítmény-és méretezhetőségi ellenőrzőlistája](storage-performance-checklist.md).  

A Table service egy vagy több teljes partíciót, a szolgáltatás pedig a csomópontok közötti dinamikusan terheléselosztást végez. Ha egy csomópont terhelés alatt van, a Table szolgáltatás *feloszthatja* az adott csomópont által kiszolgált partíciók tartományát különböző csomópontokra. Ha a forgalom alá esik, a szolgáltatás *egyesítheti* a partíciók tartományait a csendes csomópontokból egyetlen csomópontra.  

További információ a Table service belső adatairól, valamint arról, hogy a szolgáltatás hogyan kezelje a partíciókat, a papír [Microsoft Azure Storage: magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia mellett](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

## <a name="entity-group-transactions"></a>Entitás-csoport tranzakciói
Az Table serviceban az Entity Transactions (EGTs) az egyetlen beépített mechanizmus, amellyel több entitáson végezheti el az Atomic-frissítéseket. A EGTs néha *Batch-tranzakcióknak* is nevezzük. A EGTs csak ugyanabban a partícióban tárolt entitásokban működhetnek (azaz ugyanazon a partíciós kulccsal osztoznak egy adott táblában). Így bármikor megkövetelheti, hogy az atomi tranzakciós viselkedés több entitásban is legyen, gondoskodnia kell arról, hogy ezek az entitások ugyanabban a partícióban legyenek. Ez gyakran indokolja, hogy több entitást is tartson ugyanabban a táblában (és partícióban), és ne használjon több táblát a különböző típusú entitásokhoz. Egyetlen EGT legfeljebb 100 entitáson működhet.  Ha több párhuzamos EGTs küld a feldolgozáshoz, akkor fontos, hogy a EGTs ne működjenek olyan entitásokon, amelyek a EGTs-ben közösek. Ellenkező esetben a feldolgozás késleltethető lehet.

A EGTs emellett potenciális kompromisszumot is bevezet, hogy kiértékelje a kialakítását. Ez a több partíció használata növeli az alkalmazás méretezhetőségét, mivel az Azure több lehetőséget kínál a különböző csomópontokon belüli terheléselosztási kérelmekre. A további partíciók használata azonban korlátozhatja, hogy az alkalmazás képes legyen atomi tranzakciókat végrehajtani, és erős konzisztenciát fenntartani az adatokhoz. Emellett vannak olyan méretezhetőségi célok is a partíció szintjén, amelyek korlátozhatják az egyetlen csomópontra várható tranzakciók átviteli sebességét. Az Azure standard Storage-fiókok méretezhetőségi céljaival kapcsolatos további információkért lásd [a standard szintű Storage-fiókok méretezhetőségi céljait](../common/scalability-targets-standard-account.md)ismertető témakört. A Table service skálázhatósági céljaival kapcsolatos további információkért lásd: a [táblázatos tárolás skálázhatósági és teljesítményi céljai](scalability-targets.md).

## <a name="capacity-considerations"></a>Kapacitással kapcsolatos szempontok

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
A Table Storage szolgáltatás viszonylag olcsó, azonban a kapacitás kihasználtsága és a tranzakciók mennyisége a Table service megoldások kiértékelésének részeként is szerepelnie kell. Számos esetben azonban a denormalizált vagy ismétlődő adatok tárolása a megoldás teljesítményének és méretezhetőségének javítása érdekében érvényes megközelítés. A díjszabással kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Következő lépések

- [Tábla kialakítási mintái](table-storage-design-patterns.md)
- [Kapcsolatok modellezése](table-storage-design-modeling.md)
- [Tervezés lekérdezéshez](table-storage-design-for-query.md)
- [A tábla adatai titkosítva](table-storage-design-encrypt-data.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)