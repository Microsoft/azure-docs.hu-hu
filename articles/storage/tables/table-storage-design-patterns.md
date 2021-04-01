---
title: Az Azure Storage tábla kialakítási mintái | Microsoft Docs
description: Tekintse át az Azure Table service-megoldásokkal való használatra alkalmas tervezési mintákat. A más cikkekben tárgyalt problémák és kompromisszumok.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 2eb109078728b8a9070b3991733450c1da790d9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879595"
---
# <a name="table-design-patterns"></a>Táblatervezési minták
Ez a cikk a Table service-megoldásokkal való használatra alkalmas mintákat ismerteti. Azt is megtudhatja, hogyan lehet gyakorlatilag a más Table Storage-kialakítási cikkekben tárgyalt problémák és kompromisszumok kezelésére. A következő ábra összefoglalja a különböző minták közötti kapcsolatokat:  

![kapcsolódó adat megkeresése](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


A fenti minta-hozzárendelés kiemeli az útmutatóban ismertetett minták (kék) és a (narancssárga) mintázatok közötti kapcsolatokat. Számos más mintát érdemes figyelembe venni. A Table Service egyik fő forgatókönyve például az, ha a [(z) parancs lekérdezési felelősségének elkülönítése (CQRS)](/previous-versions/msp-n-p/jj554200(v=pandp.10)) mintából származó, az [anyagon alapuló megtekintési mintát](/previous-versions/msp-n-p/dn589782(v=pandp.10)) használja.  

## <a name="intra-partition-secondary-index-pattern"></a>Partíción belüli másodlagos index minta
Az egyes entitások több példányát is tárolhatja különböző **RowKey** -értékekkel (ugyanabban a partícióban) a gyors és hatékony keresési és alternatív rendezési sorrendek különböző **RowKey** -értékek használatával történő engedélyezéséhez. A másolatok közötti frissítések konzisztensek lehetnek a EGTs használatával.  

### <a name="context-and-problem"></a>Kontextus és probléma
A Table service automatikusan indexeli az entitásokat a **PartitionKey** és a **RowKey** értékkel. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan lekérje az entitásokat ezen értékek használatával. Például az alább látható táblázat struktúrájának használatával az ügyfélalkalmazás egy pont lekérdezéssel kérheti le az egyes alkalmazotti entitásokat a részleg neve és az alkalmazott azonosítója (a **PartitionKey** és a **RowKey** értékek) használatával. Az ügyfelek az egyes részlegeken belül az alkalmazotti azonosító szerint rendezett entitásokat is lekérhetik.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Ha egy másik tulajdonság (például az e-mail-cím) alapján szeretné megkeresni az alkalmazotti entitást, akkor kevésbé hatékony partíciós vizsgálatot kell használnia, hogy megtalálja a megfelelőt. Ennek az az oka, hogy a Table szolgáltatás nem biztosít másodlagos indexeket. Emellett nincs lehetőség arra, hogy az alkalmazottak listáját a **RowKey** sorrendjében eltérő sorrendbe rendezze.  

### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerülése érdekében az egyes entitások több példányát is tárolhatja az egyes másolatokkal egy másik **RowKey** érték használatával. Ha az alább látható struktúrákkal rendelkező entitást tárol, akkor az e-mail-cím vagy az alkalmazott azonosítója alapján hatékonyan lekérheti az alkalmazotti entitásokat. A **RowKey**, a "empid_" és a "email_" előtag-értékei lehetővé teszik egy adott alkalmazott vagy egy tartomány egy adott csoportjának lekérdezését az e-mail-címek vagy az alkalmazottak azonosítóinak használatával.  

![Alkalmazott entitások](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

A következő két szűrési feltétel (az alkalmazotti azonosító és egy e-mail-cím alapján megtekintett) a pontok lekérdezéseit is megadja:  

* $filter = (PartitionKey EQ "Sales") és (RowKey EQ "empid_000223")  
* $filter = (PartitionKey EQ ' Sales ') és (RowKey EQ ' email_jonesj@contoso.com ')  

Ha az alkalmazotti entitások egy tartományát kérdezi le, megadhatja az alkalmazotti azonosító sorrendbe rendezett tartományt, vagy az e-mail-címek sorrendjét az **RowKey** megfelelő előtaggal rendelkező entitások lekérdezésével.  

* Az értékesítési részleg összes alkalmazottjának megkeresése az 000100 – 000199 tartományba tartozó alkalmazotti AZONOSÍTÓval: $filter = (PartitionKey EQ "Sales") és (RowKey GE "empid_000100") és (RowKey le "empid_000199")  
* Az értékesítési részleg összes alkalmazottjának megkeresése az "a" betűvel kezdődő e-mail-címmel: $filter = (PartitionKey EQ "Sales") és (RowKey GE "email_a") és (RowKey lt "email_b")  
  
  A fenti példákban használt szűrési szintaxis a Table service REST API, további információ: [lekérdezési entitások](/rest/api/storageservices/Query-Entities).  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A Table Storage szolgáltatás viszonylag olcsó ahhoz, hogy az ismétlődő adatokat tároló költségek terhelése ne legyen jelentős aggodalom. A várható tárolási követelmények alapján azonban mindig értékelnie kell a terv költségeit, és csak ismétlődő entitásokat kell hozzáadnia az ügyfélalkalmazás által végrehajtandó lekérdezések támogatásához.  
* Mivel a másodlagos index entitások ugyanabban a partícióban vannak tárolva, mint az eredeti entitások, gondoskodjon arról, hogy ne lépje túl az egyes partíciók méretezhetőségi céljait.  
* A EGTs használatával megtarthatja a duplikált entitások egymással való egységességét, ha az entitás két példányát atomian frissíti. Ez azt jelenti, hogy az entitások összes példányát ugyanabban a partícióban kell tárolnia. További információ: az [Entity Transactions használatával](table-storage-design.md#entity-group-transactions)foglalkozó szakasz.  
* A **RowKey** használt értéknek egyedinek kell lennie az egyes entitásokhoz. Használjon összetett kulcsos értékeket.  
* A **RowKey** lévő numerikus értékek (például a 000223 alkalmazott azonosítója) kitöltése lehetővé teszi a megfelelő rendezést és szűrést a felső és alsó határok alapján.  
* Nem feltétlenül kell duplikálnia az entitás összes tulajdonságát. Ha például a **RowKey** található e-mail-címmel rendelkező entitásokat megkereső lekérdezésekhez soha nem szükséges az alkalmazott kora, akkor ezek az entitások a következő szerkezettel rendelkezhetnek:

   ![Alkalmazotti entitás szerkezete](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Általában jobb az ismétlődő adattárolás, és gondoskodni arról, hogy egyetlen lekérdezéssel kérdezze le az összes szükséges adatkérést, mint ha egyetlen lekérdezés használatával keres egy entitást, és egy másikat a szükséges értékek megkereséséhez.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha az ügyfélalkalmazás számos különböző kulccsal kell lekérnie az entitásokat, amikor az ügyfélnek különböző rendezési sorrendbe kell beolvasnia az entitásokat, és az egyes entitásokat különféle egyedi értékek alapján kell azonosítania. Azonban ügyeljen arra, hogy ne lépje túl a particionálási korlátokat, ha a különböző **RowKey** értékeket használó entitás-keresési műveleteket végez.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Partíciók közötti másodlagos index mintája](#inter-partition-secondary-index-pattern)
* [Összetett kulcs mintája](#compound-key-pattern)
* Entitás-csoport tranzakciói
* [Heterogén entitások típusának használata](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Partíciók közötti másodlagos index mintája
Több példányban tárolhatja az egyes entitásokat különböző **RowKey** -értékekkel külön partíciókban vagy külön táblákban, így a gyors és hatékony keresési és alternatív rendezési sorrendeket különböző **RowKey** -értékek használatával engedélyezheti.  

### <a name="context-and-problem"></a>Kontextus és probléma
A Table service automatikusan indexeli az entitásokat a **PartitionKey** és a **RowKey** értékkel. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan lekérje az entitásokat ezen értékek használatával. Például az alább látható táblázat struktúrájának használatával az ügyfélalkalmazás egy pont lekérdezéssel kérheti le az egyes alkalmazotti entitásokat a részleg neve és az alkalmazott azonosítója (a **PartitionKey** és a **RowKey** értékek) használatával. Az ügyfelek az egyes részlegeken belül az alkalmazotti azonosító szerint rendezett entitásokat is lekérhetik.  

![Alkalmazott azonosítója](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Ha egy másik tulajdonság (például az e-mail-cím) alapján szeretné megkeresni az alkalmazotti entitást, akkor kevésbé hatékony partíciós vizsgálatot kell használnia, hogy megtalálja a megfelelőt. Ennek az az oka, hogy a Table szolgáltatás nem biztosít másodlagos indexeket. Emellett nincs lehetőség arra, hogy az alkalmazottak listáját a **RowKey** sorrendjében eltérő sorrendbe rendezze.  

Nagy mennyiségű tranzakciót számít fel ezen entitások ellen, és csökkenteni szeretné az ügyfél Table service szabályozásának kockázatát.  

### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerülése érdekében az egyes entitások több példányát is tárolhatja az egyes példányok különböző **PartitionKey** és **RowKey** értékek használatával. Ha az alább látható struktúrákkal rendelkező entitást tárol, akkor az e-mail-cím vagy az alkalmazott azonosítója alapján hatékonyan lekérheti az alkalmazotti entitásokat. A **PartitionKey**, a "empid_" és a "email_" előtag értékei lehetővé teszik annak azonosítását, hogy melyik indexet szeretné használni a lekérdezésekhez.  

![Elsődleges index és másodlagos index](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


A következő két szűrési feltétel (az alkalmazotti azonosító és egy e-mail-cím alapján megtekintett) a pontok lekérdezéseit is megadja:  

* $filter = (PartitionKey EQ "empid_Sales") és (RowKey EQ "000223")
* $filter = (PartitionKey EQ "email_Sales") és (RowKey EQ " jonesj@contoso.com ")  

Ha az alkalmazotti entitások egy tartományát kérdezi le, megadhatja az alkalmazotti azonosító sorrendbe rendezett tartományt, vagy az e-mail-címek sorrendjét az **RowKey** megfelelő előtaggal rendelkező entitások lekérdezésével.  

* Az értékesítési részleg összes alkalmazottjának a **000100** – **000199** tartományba tartozó alkalmazotti azonosítóval történő megkereséséhez használja az alkalmazotti azonosító sorrendjét: $Filter = (PartitionKey EQ "empid_Sales") és (RowKey GE "000100") és (RowKey le "000199")  
* Az értékesítési részleg összes alkalmazottjának megkeresése egy e-mail-címmel, amely az e-mail-címek sorrendjét használja: $filter = (PartitionKey EQ "email_Sales") és (RowKey GE "a") és (RowKey lt "b")  

A fenti példákban használt szűrési szintaxis a Table service REST API, további információ: [lekérdezési entitások](/rest/api/storageservices/Query-Entities).  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Megtarthatja, hogy az ismétlődő entitások végül konzisztensek legyenek egymással, ha a [végül konzisztens tranzakciós mintát](#eventually-consistent-transactions-pattern) használják az elsődleges és a másodlagos index entitások karbantartásához.  
* A Table Storage szolgáltatás viszonylag olcsó ahhoz, hogy az ismétlődő adatokat tároló költségek terhelése ne legyen jelentős aggodalom. A várható tárolási követelmények alapján azonban mindig értékelnie kell a terv költségeit, és csak ismétlődő entitásokat kell hozzáadnia az ügyfélalkalmazás által végrehajtandó lekérdezések támogatásához.  
* A **RowKey** használt értéknek egyedinek kell lennie az egyes entitásokhoz. Használjon összetett kulcsos értékeket.  
* A **RowKey** lévő numerikus értékek (például a 000223 alkalmazott azonosítója) kitöltése lehetővé teszi a megfelelő rendezést és szűrést a felső és alsó határok alapján.  
* Nem feltétlenül kell duplikálnia az entitás összes tulajdonságát. Ha például a **RowKey** található e-mail-címmel rendelkező entitásokat megkereső lekérdezésekhez soha nem szükséges az alkalmazott kora, akkor ezek az entitások a következő szerkezettel rendelkezhetnek:
  
   ![Alkalmazott entitás (másodlagos index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Általában jobb, ha ismétlődő adattárolást végez, és biztosítja, hogy az összes szükséges, egyetlen lekérdezéssel rendelkező felhasználó lekérdezzen egy entitást a másodlagos index használatával, és egy másikat, hogy megkeresse az elsődleges indexben szereplő szükséges adattípust.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha az ügyfélalkalmazás számos különböző kulccsal kell lekérnie az entitásokat, amikor az ügyfélnek különböző rendezési sorrendbe kell beolvasnia az entitásokat, és az egyes entitásokat különféle egyedi értékek alapján kell azonosítania. Akkor használja ezt a mintát, ha el szeretné kerülni, hogy a partíciók skálázhatósági korlátai meghaladják a különböző **RowKey** -értékekkel rendelkező entitás-keresési értékeket.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern)  
* [Összetett kulcs mintája](#compound-key-pattern)  
* Entitás-csoport tranzakciói  
* [Heterogén entitások típusának használata](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Végül konzisztens tranzakciós minta
Az Azure Queues használatával engedélyezheti a partíciós határok vagy a tárolási rendszerek határain belüli, végül konzisztens viselkedést.  

### <a name="context-and-problem"></a>Kontextus és probléma
A EGTs lehetővé teszi az atomi tranzakciók több entitás között, amelyek ugyanazt a partíciós kulcsot használják. A teljesítményre és méretezhetőségre vonatkozó okok miatt dönthet úgy, hogy különálló partíciókban vagy különálló tárolási rendszeren található konzisztencia-követelményeknek megfelelő entitásokat tárol: ilyen esetben nem használhatja a EGTs-t a konzisztencia fenntartásához. Előfordulhat például, hogy meg kell őriznie a végleges konzisztencia fenntartásának követelményét:  

* Ugyanazon tábla két különböző partíciójában, különböző táblákban vagy különböző tárolási fiókokban tárolt entitások.  
* A Table serviceban tárolt entitás és a Blob serviceban tárolt blob.  
* A Table serviceban tárolt entitás és a fájlrendszerben található fájl.  
* Az Azure Cognitive Search szolgáltatással még indexelt Table serviceban tárolt entitás.  

### <a name="solution"></a>Megoldás
Az Azure Queues használatával olyan megoldást valósíthat meg, amely két vagy több partícióra vagy tárolási rendszerre kiterjedő végleges konzisztenciát biztosít.
Ennek a megközelítésnek a szemléltetéséhez feltételezhető, hogy a régi alkalmazotti entitások archiválására van szükség. A régi alkalmazotti entitásokat ritkán kérdezik le, és ki kell zárni azokat a tevékenységeket, amelyek az aktuális alkalmazottakkal foglalkoznak. Ennek a követelménynek a megvalósításához az **aktuális** táblában és a régi alkalmazottakban tárolt aktív alkalmazottakat az **archív** táblában tárolja. Az alkalmazottak archiválásához törölnie kell az entitást az **aktuális** táblából, és fel kell vennie az entitást az **archív** táblába, de nem használhat EGT a két művelet végrehajtásához. Annak elkerülése érdekében, hogy a hiba miatt egy entitás mindkét vagy egyik táblában megjelenjen, az archiválási műveletnek végül konzisztensnek kell lennie. A következő Sequence diagram a művelet lépéseit ismerteti. További részleteket a következő szövegben található kivételi elérési utakhoz biztosítunk.  

![Azure Queues-megoldás](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Az ügyfél az archiválási műveletet úgy indítja el, hogy egy Azure-várólistára helyez egy üzenetet, amely ebben a példában az Employee #456 archiválására vonatkozik. Egy feldolgozói szerepkör lekérdezi az új üzenetek várólistáját; Ha megtalál egyet, beolvassa az üzenetet, és elhagy egy rejtett másolatot a várólistán. A következő feldolgozói szerepkör beolvassa az entitás egy másolatát az **aktuális** táblából, beszúr egy másolatot az **archív** táblába, majd törli az eredetit az **aktuális** táblából. Végül, ha az előző lépések nem voltak hibák, a feldolgozói szerepkör törli a rejtett üzenetet a várólistából.  

Ebben a példában a 4. lépés beszúrja az alkalmazottat az **archív** táblába. Hozzáadhatja az alkalmazottat egy blobhoz a Blob service vagy egy fájlrendszerben.  

### <a name="recovering-from-failures"></a>Helyreállítás a hibákból
Fontos, hogy a **4** . és az **5** . lépésben szereplő műveleteknek *idempotens* kell lenniük abban az esetben, ha a feldolgozói szerepkörnek újra kell indítania az archiválási műveletet. Ha a Table service használja, a **4** . lépésnél használjon "INSERT vagy replace" műveletet; az **5** . lépésben a használt ügyfél-függvénytár "Törlés, ha létezik" műveletét kell használnia. Ha más tárolási rendszereket használ, megfelelő idempotens műveletet kell használnia.  

Ha a feldolgozói szerepkör soha nem fejezi be a **6**. lépést, akkor időtúllépés után az üzenet ismét megjelenik az üzenetsor számára, hogy a feldolgozói szerepkör megpróbáljon újradolgozni. A feldolgozói szerepkör megtekintheti, hogy a várólistán lévő üzenetek hányszor lettek elolvasva, és ha szükséges, a "méreg" üzenet a vizsgálathoz, hogy elküldje azt egy külön várólistára. A várólista-üzenetek olvasásával és a sorok számának ellenőrzésével kapcsolatos további információkért lásd: [üzenetek](/rest/api/storageservices/Get-Messages)beolvasása.  

A tábla és a várólista-szolgáltatások bizonyos hibái átmeneti hibák, és az ügyfélalkalmazás megfelelő újrapróbálkozási logikát kell tartalmaznia a kezeléséhez.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ez a megoldás nem biztosítja a tranzakciók elkülönítését. Előfordulhat például, hogy egy ügyfél beolvassa az **aktuális** és az **archív** táblát, amikor a feldolgozói szerepkör a **4** . és az **5**. lépés között volt, és az adatok inkonzisztens nézetét látja. Az adategységek végül konzisztensek lesznek.  
* Győződjön meg arról, hogy a 4. és az 5. lépés idempotens a végleges konzisztencia biztosítása érdekében.  
* A megoldás több várólista és feldolgozói szerepkör-példány használatával is méretezhető.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha a különböző partíciókban vagy táblákban található entitások közötti végleges konzisztenciát szeretné garantálni. Ezt a mintát kiterjesztve biztosíthatja a Table service és a Blob service és egyéb nem Azure Storage-adatforrások, például az adatbázis vagy a fájlrendszer közötti műveletek végleges egységességét.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* Entitás-csoport tranzakciói  
* [Egyesítés vagy csere](#merge-or-replace)  

> [!NOTE]
> Ha a tranzakció elkülönítése fontos a megoldás számára, érdemes megfontolnia a táblák újratervezését, hogy lehetővé váljon a EGTs használata.  
> 
> 

## <a name="index-entities-pattern"></a>Entitások indexelése minta
Az entitások listáját visszaadó hatékony keresések engedélyezéséhez indexelő entitásokat kell fenntartani.  

### <a name="context-and-problem"></a>Kontextus és probléma
A Table service automatikusan indexeli az entitásokat a **PartitionKey** és a **RowKey** értékkel. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan kérdezze le az entitásokat egy pont lekérdezéssel. Például az alább látható táblázat struktúrájának használatával az ügyfélalkalmazás hatékonyan lekérheti az egyes alkalmazottak entitásokat a részleg neve és az alkalmazott azonosítója (a **PartitionKey** és a **RowKey**) használatával.  

![Alkalmazotti entitás](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Ha azt is szeretné, hogy az alkalmazott entitások listájának lekérése egy másik nem egyedi tulajdonság értéke alapján történjen, például a vezetékneve, akkor kevésbé hatékony partíciós vizsgálatot kell használnia a egyezések kereséséhez, ahelyett, hogy az index használatával közvetlenül megkeresse őket. Ennek az az oka, hogy a Table szolgáltatás nem biztosít másodlagos indexeket.  

### <a name="solution"></a>Megoldás
Ha a vezetéknevet a fent látható entitás-struktúra szerint szeretné engedélyezni, az alkalmazotti azonosítók listáját kell fenntartania. Ha szeretné lekérni az alkalmazott entitásokat egy adott vezetéknevtel (például Jones), először meg kell keresnie a Jones-beli alkalmazottakhoz tartozó alkalmazotti azonosítók listáját a vezetéknevük alapján, majd le kell kérnie az alkalmazottak entitásait. Az alkalmazotti azonosítók listáját három fő lehetőséggel lehet tárolni:  

* BLOB Storage használata.  
* Hozzon létre index entitásokat az alkalmazott entitásokkal megegyező partícióban.  
* Hozzon létre index entitásokat egy külön partícióban vagy táblában.  

<u>#1 lehetőség: blob Storage használata</u>  

Az első lehetőségnél hozzon létre egy blobot minden egyedi vezetéknevhez, és mindegyik blob tárolja a **PartitionKey** (részleg) és a **RowKey** (alkalmazotti azonosító) értékét a vezetéknevet használó alkalmazottak számára. Egy alkalmazott hozzáadásakor vagy törlésekor gondoskodnia kell arról, hogy az érintett blob tartalma végül konzisztens legyen az alkalmazott entitásokkal.  

<u>#2 lehetőség:</u> Index entitások létrehozása ugyanabban a partícióban  

A második lehetőség esetében használja az indexelő entitásokat, amelyek a következő adategységeket tárolják:  

![Alkalmazotti index entitás](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

A **EmployeeIDs** tulajdonság a **RowKey** tárolt utolsó névvel rendelkező alkalmazottak alkalmazotti azonosítóinak listáját tartalmazza.  

A következő lépések azt a folyamatot ismertetik, amelyet követnie kell, amikor új alkalmazottat ad hozzá, ha a második lehetőséget használja. Ebben a példában egy 000152 AZONOSÍTÓJÚ alkalmazottat és egy vezetéknevet adunk hozzá az értékesítési részleghez:  

1. Az index entitás beolvasása az "értékesítés" **PartitionKey** értékkel és a "Jones" **RowKey** értékkel. Mentse az entitás ETag a 2. lépésben való használatra.  
2. Hozzon létre egy Entity Group-tranzakciót (azaz egy batch-műveletet), amely beszúrja az új alkalmazotti entitást (**PartitionKey** "Sales **" és "** 000152"), és frissíti az index entitást (**PartitionKey** érték "Sales" és **RowKey** Value "Jones") az új alkalmazott azonosítójának a EmployeeIDs mezőben szereplő listához való hozzáadásával. További információ az Entity Transactions szolgáltatással kapcsolatban: Entity Transactions.  
3. Ha az entitás-csoport tranzakciója optimista egyidejűségi hiba miatt meghiúsul (valaki más csak módosította az index entitást), akkor újra kell kezdenie az 1. lépésben.  

A második lehetőség használata esetén hasonló megközelítést használhat az alkalmazottak törléséhez. Az alkalmazott vezetéknevének módosítása valamivel összetettebb, mert egy olyan Entity Group-tranzakciót kell végrehajtania, amely három entitást frissít: az alkalmazott entitást, a régi vezetéknev index entitását, valamint az új vezetéknevet index entitást. Az egyes entitásokat le kell kérnie a módosítások megkezdése előtt, hogy lekérje a ETag értékeit, amelyek a frissítések optimista párhuzamosságtal történő végrehajtásához használhatók.  

A következő lépések ismertetik azt a folyamatot, amelyet követnie kell, ha a második lehetőség használatakor az összes alkalmazottra vonatkozóan meg kell keresnie egy adott vezetéknevet a részlegen. Ebben a példában a Sales részleg összes alkalmazottját megkeresi a vezetékneve Jones néven:  

1. Az index entitás beolvasása az "értékesítés" **PartitionKey** értékkel és a "Jones" **RowKey** értékkel.  
2. Elemezze az alkalmazotti azonosítók listáját a EmployeeIDs mezőben.  
3. Ha további információra van szüksége az alkalmazottakról (például az e-mail-címekről), a 2. lépésben beszerzett alkalmazottak listájából kérje le az egyes alkalmazottak entitásokat az "értékesítés" **PartitionKey** értékkel, illetve a **RowKey** értékeit.  

<u>#3 lehetőség:</u> Index entitások létrehozása külön partícióban vagy táblázatban  

A harmadik lehetőség esetében használja az indexelő entitásokat, amelyek a következő adategységeket tárolják:  

![Az alkalmazott index entitása egy külön partícióban](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


A **EmployeeIDs** tulajdonság a **RowKey** tárolt utolsó névvel rendelkező alkalmazottak alkalmazotti azonosítóinak listáját tartalmazza.  

A harmadik lehetőség esetében a EGTs nem használható a konzisztencia fenntartásához, mert az index entitások az alkalmazott entitások külön partíciójában vannak. Győződjön meg arról, hogy az indexelő entitások végül konzisztensek az alkalmazott entitásokkal.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ehhez a megoldáshoz legalább két lekérdezésre van szükség a megfelelő entitások lekéréséhez: az egyiket, hogy lekérdezze az index entitásokat a **RowKey** -értékek listájának beolvasásához, majd lekérdezi az egyes entitásokat a listában.  
* Mivel az egyes entitások legfeljebb 1 MB méretűek lehetnek, a megoldás #2 és az Option #3 lehetőség azt feltételezi, hogy az adott vezetéknevhez tartozó alkalmazotti azonosítók listája soha nem nagyobb, mint 1 MB. Ha az alkalmazottak azonosítóinak listája valószínűleg nagyobb, mint 1 MB, használja a #1 lehetőséget, és tárolja az indexet a blob Storage-ban.  
* Ha a (z) #2 lehetőséget használja (az EGTs használatával kezeli az alkalmazottak hozzáadását és törlését, valamint az alkalmazott vezetéknevének módosítását), akkor ki kell értékelnie, hogy a tranzakciók mennyisége megközelíti-e az adott partíció skálázhatósági korlátait. Ebben az esetben érdemes megfontolni egy végül konzisztens megoldást (#1 vagy Option #3), amely várólistákat használ a frissítési kérelmek kezeléséhez, és lehetővé teszi, hogy az index entitásait külön partícióban tárolja az alkalmazott entitásokból.  
* Az ebben a megoldásban #2 beállítás azt feltételezi, hogy a vezetéknevét egy részlegen belül szeretné megkeresni: például szeretné lekérdezni az alkalmazottak listáját az értékesítési részleg vezetékneve Jones névvel. Ha szeretné megkeresni az összes olyan alkalmazottat, aki vezetékneve Jones a teljes szervezeten belül, használja a #1 vagy a Option #3 lehetőséget.
* Megvalósíthat egy üzenetsor-alapú megoldást, amely végleges konzisztenciát biztosít (további részletekért tekintse meg a [végül konzisztens tranzakciók mintáját](#eventually-consistent-transactions-pattern) ).  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitásokat szeretne keresni, amelyek mindegyike közös tulajdonságot használ, például a vezetéknevét az utolsó nevű alkalmazottal.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* Entitás-csoport tranzakciói  
* [Heterogén entitások típusának használata](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Denormalizálás mintája
A kapcsolódó adategységeket egyetlen entitásban egyesítheti, így egyetlen pont lekérdezéssel kérheti le az összes szükséges adat lekérését.  

### <a name="context-and-problem"></a>Kontextus és probléma
A kapcsolódó adatbázisokban általában az adatok normalizálása a több táblából származó adatok lekérdezését eredményező lekérdezések ismétlődésének eltávolításához. Ha az adatait az Azure-táblákban normalizálja, akkor a kapcsolódó adatok lekérése érdekében az ügyféltől több oda-vissza kell, hogy beolvassa a kiszolgálót. Az alábbi táblázat szerkezete például két oda-vissza váltásra van szüksége egy részleg adatainak lekéréséhez: az egyiket a felettes AZONOSÍTÓját tartalmazó részleg entitás beolvasásához, majd egy másik kérést, amely a felettes adatait egy alkalmazott entitásban kéri le.  

![Részleg entitása és alkalmazotti entitása](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Megoldás
Ahelyett, hogy két különálló entitásban tárolja az adatokat, denormalizálja az adatokat, és megtartja a felettes adatainak másolatát a részleg entitásban. Például:  

![Részleg entitása](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Az ezekkel a tulajdonságokkal rendelkező részleg entitásokkal mostantól lekérheti az adott részlegre vonatkozó összes szükséges adatot egy pont lekérdezés használatával.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az adattárolással kapcsolatos költségek kétszer vannak tárolva. A teljesítmény (a tárolási szolgáltatáshoz való kevesebb kérés miatt) általában a tárolási költségek marginális növekedését méri (és ez a költség részben ellensúlyozza a részleg részleteinek beolvasásához szükséges tranzakciók számának csökkenését).  
* Meg kell őriznie a két entitás konzisztenciáját, amelyek adatokat tárolnak a kezelők számára. A konzisztencia problémát a EGTs használatával kezelheti egyetlen atomi tranzakcióban: ebben az esetben a részleg entitást és a Department Manager alkalmazott entitását ugyanazon a partíción tárolja a rendszer.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha gyakran kell kapcsolódó információkat keresnie. Ez a minta csökkenti azoknak a lekérdezéseknek a számát, amelyeket az ügyfélnek el kell végeznie a szükséges adatforrások lekéréséhez.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* Entitás-csoport tranzakciói  
* [Heterogén entitások típusának használata](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Összetett kulcs mintája
Összetett **RowKey** -értékek használatával lehetővé teheti, hogy az ügyfél egyetlen pont lekérdezéssel keressen kapcsolódó adatokkal.  

### <a name="context-and-problem"></a>Kontextus és probléma
A kapcsolati adatbázisban természetes, hogy a lekérdezésekben a kapcsolódó adatmennyiségeket egy lekérdezésben a-ügyfélhez való visszatéréshez használja. Az alkalmazotti AZONOSÍTÓval például megkeresheti a kapcsolódó entitások listáját, amelyek az adott alkalmazott teljesítmény-és felülvizsgálati adatait tartalmazzák.  

Tegyük fel, hogy az alábbi struktúra használatával tárolja az alkalmazott entitásokat a Table serviceban:  

![Képernyőkép, amely bemutatja, hogyan tárolhatja az alkalmazottak entitásait a Table serviceban.](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Emellett a korábbi adatokat is tárolnia kell a felülvizsgálatokhoz és a teljesítményhez az alkalmazottak által a szervezet számára dolgozott munkavégzéshez, és az adatoknak évente kell hozzáférnie. Az egyik lehetőség egy másik tábla létrehozása, amely az entitásokat az alábbi struktúrával tárolja:  

![Alternatív alkalmazotti entitás szerkezete](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Figyelje meg, hogy ezzel a megközelítéssel eldöntheti, hogy egy bizonyos információt (például utónév és vezetéknév) duplikál az új entitásban, hogy lehetővé tegye az adatok lekérését egyetlen kérelemmel. Azonban nem lehet erős konzisztenciát fenntartani, mert nem használhat EGT a két entitás atomi frissítésére.  

### <a name="solution"></a>Megoldás
Egy új entitás típusának tárolása az eredeti táblában az alábbi struktúrával rendelkező entitások használatával:  

![Megoldás az alkalmazotti entitások struktúrájához](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Figyelje meg, hogy a **RowKey** mostantól egy összetett kulcs, amely az ALKALMAZOTTi azonosítóból és a felülvizsgálati adat évének számított részét képezi, amely lehetővé teszi az alkalmazott teljesítményének beolvasását és az adatellenőrzést egyetlen entitásra vonatkozó egyetlen kéréssel.  

Az alábbi példa azt ismerteti, hogyan kérhető le egy adott alkalmazott összes felülvizsgálati adata (például 000123 alkalmazott az értékesítési részlegben):  

$filter = (PartitionKey EQ ' Sales ') és (RowKey GE ' empid_000123 ') és (RowKey lt ' 000123_2012 ') &$select = RowKey, felettes minősítés, társ-minősítés, megjegyzések  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Használjon egy megfelelő elválasztó karaktert, amely megkönnyíti a **RowKey** érték elemzését: például **000123_2012**.  
* Ezt az entitást ugyanabban a partícióban tárolja, mint a többi olyan entitást, amely ugyanahhoz az alkalmazotthoz kapcsolódó adatait tartalmazza, ami azt jelenti, hogy a EGTs használatával erős konzisztencia tartható fenn.
* Érdemes megfontolni, hogy milyen gyakran fogja lekérdezni az adatlekérdezést, hogy a minta megfelelő-e.  Ha például az áttekintő adatelemzést ritkán szeretné elérni, és a fő alkalmazottak adatai gyakran külön entitásként lesznek tárolva.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha egy vagy több, gyakran lekérdezett kapcsolódó entitást kell tárolnia.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* Entitás-csoport tranzakciói  
* [Heterogén entitások típusának használata](#working-with-heterogeneous-entity-types)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Napló farok mintája
A partícióhoz legutóbb hozzáadott *n* entitások lekérése egy olyan **RowKey** -értékkel, amely fordított dátum és idő sorrendbe rendez.  

### <a name="context-and-problem"></a>Kontextus és probléma
Gyakori követelmény, hogy beolvassa a legutóbb létrehozott entitásokat, például az alkalmazott által küldött 10 legutóbbi költségtérítési jogcímet. A táblázatos lekérdezések egy **$Top** lekérdezési műveletet támogatnak a készlet első *n* entitásának visszaadásához: nincs megfelelő lekérdezési művelet, amely egy készlet utolsó n entitását adja vissza.  

### <a name="solution"></a>Megoldás
Az entitásokat egy olyan **RowKey** használatával tárolhatja, amely természetes módon rendezi a fordított dátum és idő sorrendjét, így a legújabb bejegyzés mindig az első a táblában.  

Ha például le szeretné kérni az alkalmazott 10 legutóbbi költségét, az aktuális dátum/idő értékből származtatott fordított osztásjelek értékét is használhatja. A következő C#-kódrészlet az egyik módszert mutatja be egy olyan **RowKey** , amely a legutóbbiról a legrégebbi értékre rendezi a megfelelő "fordított kullancsok" értékét:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

A dátum/idő értékhez a következő kódot használhatja:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A tábla lekérdezése a következőképpen néz ki:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A fordított osztásjelek értékét a bevezető nullákkal kell ellátni, hogy a karakterlánc-érték a várt módon legyen rendezve.  
* A méretezhetőségi célokat a partíció szintjén kell figyelembe venni. Ügyeljen arra, hogy ne hozzon létre gyors elérésű helyet.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha az entitásokat fordított dátum/idő sorrendben kell elérnie, vagy ha a legutóbb hozzáadott entitásokhoz kell hozzáférnie.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Előtag/Hozzáfűzés Anti-pattern](#prepend-append-anti-pattern)  
* [Entitások beolvasása](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Nagy mennyiségű törlési minta
Engedélyezze a nagy mennyiségű entitás törlését úgy, hogy az összes entitást egyidejű törlésre tárolja a saját külön táblában. az entitásokat a tábla törlésével törölheti.  

### <a name="context-and-problem"></a>Kontextus és probléma
Számos alkalmazás törli azokat a régi adatmennyiségeket, amelyek már nem szükségesek az ügyfélalkalmazás számára, vagy hogy az alkalmazás archiválva lett egy másik tárolóeszközre. Ezeket az adatokat általában egy dátum alapján azonosítjuk: például a 60 napnál régebbi bejelentkezési kérelmek rekordjainak törlésére van szükség.  

Az egyik lehetséges megoldás a bejelentkezési kérelem dátumának és időpontjának használata a **RowKey**:  

![Bejelentkezési kísérlet dátuma és időpontja](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Ezzel a módszerrel elkerülhetők a partíciós pontok, mert az alkalmazás beillesztheti és törölheti az egyes felhasználók bejelentkezési entitásait egy külön partíción. Ez a megközelítés azonban költséges és időigényes lehet, ha nagyszámú entitással rendelkezik, mert először egy táblázatos vizsgálatot kell végeznie a törölni kívánt entitások azonosításához, majd minden régi entitást törölnie kell. A régi entitások törléséhez a EGTs-ba történő több törlési kérelem kötegelt feldolgozásával csökkentheti a lekerekítési utak számát a kiszolgálóra.  

### <a name="solution"></a>Megoldás
Használjon külön táblázatot a bejelentkezési kísérletek minden napján. A fenti entitás kialakításával elkerülheti, hogy az entitások ne legyenek beillesztve, és a régi entitások törlése mostantól csupán egy tábla törlésének kérdése (egyetlen tárolási művelet), és nem kell minden nap több száz és ezer egyéni bejelentkezési entitást megkeresni és törölni.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az Ön által használt kialakítás más módon támogatja az adatokat, például adott entitások keresését, más adatokkal való összekapcsolást vagy összesített információk létrehozását?  
* Az új entitások beszúrásakor a terv elkerüli a gyors helyeket?  
* Késleltetés, ha a törlés után ugyanazt a táblanév-nevet szeretné használni. Jobb, ha mindig egyedi táblanév-nevet használ.  
* Némi szabályozás várható, amikor először használ egy új táblázatot, miközben a Table service megtanulja a hozzáférési mintákat, és elosztja a partíciókat a csomópontok között. Érdemes megfontolni, hogy milyen gyakran kell új táblákat létrehoznia.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha nagy mennyiségű entitást kell törölni egyszerre.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* Entitás-csoport tranzakciói
* [Entitások módosítása](#modifying-entities)  

## <a name="data-series-pattern"></a>Adatsorozat mintája
Egyetlen entitásban tárolhatja a teljes adatsorozatokat, így csökkentve a kérelmek számát.  

### <a name="context-and-problem"></a>Kontextus és probléma
Gyakori forgatókönyv, hogy egy alkalmazás egy adatsorozatot tárol, amelyet általában egyszerre kell lekérnie. Előfordulhat például, hogy az alkalmazás rögzíti, hogy az egyes alkalmazottak hány ÜZENETKÜLDÉSi üzenetet küldenek óránként, majd ezt az információt felhasználva ábrázolják, hogy az egyes felhasználók hány üzenetet küldenek az előző 24 órában. Az egyik kialakítás lehet 24 entitás tárolása az egyes alkalmazottak számára:  

![24 entitás tárolása minden alkalmazott számára](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Ezzel a kialakítással könnyedén megkeresheti és frissítheti az entitásokat, amelyeket frissíteni kell az egyes alkalmazottak számára, amikor az alkalmazásnak frissítenie kell az üzenetek számának értékét. Ahhoz azonban, hogy lekérje a tevékenység diagramjának az előző 24 órában való ábrázolásához szükséges információkat, 24 entitást kell lekérnie.  

### <a name="solution"></a>Megoldás
Az alábbi kialakítással külön tulajdonsággal tárolhatja az üzenetek darabszámát minden órában:  

![Üzenet stats entitása](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Ezzel a kialakítással egy adott órában egy alkalmazotthoz tartozó üzenetek számának frissítése egyesítési művelettel végezhető el. Most lekérheti az összes szükséges információt, hogy a diagramot egyetlen entitásra vonatkozó kérelem használatával ábrázolja.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ha a teljes adatsorozat nem fér el egyetlen entitásba (egy entitás legfeljebb 252 tulajdonsággal rendelkezhet), használjon másik adattárat, például egy blobot.  
* Ha több ügyfél is frissít egyszerre egy entitást, akkor a **ETAG** kell használnia az optimista Egyidejűség megvalósításához. Ha sok ügyfele van, nagy mértékben megtapasztalhatja a tartalmat.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha egy adott entitáshoz társított adatsorozatot kell frissítenie és beolvasnia.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Nagyméretű entitások mintája](#large-entities-pattern)  
* [Egyesítés vagy csere](#merge-or-replace)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern) (ha az adatsorozatot egy blobban tárolja)  

## <a name="wide-entities-pattern"></a>Széles entitások mintája
Több mint 252 tulajdonságú logikai entitások tárolására több fizikai entitást használjon.  

### <a name="context-and-problem"></a>Kontextus és probléma
Az egyes entitások legfeljebb 252 tulajdonsággal rendelkezhetnek (kivéve a kötelező rendszertulajdonságokat), és összesen legfeljebb 1 MB adatmennyiséget tárolhatnak. A relációs adatbázisokban általában a sorok méretére vonatkozó korlátokat kell felvennie egy új tábla hozzáadásával, és egy-az-1 közötti kapcsolat kényszerítésével.  

### <a name="solution"></a>Megoldás
A Table service használatával több entitást is tárolhat, hogy egyetlen nagy üzleti objektumot, több mint 252 tulajdonságot képvisel. Ha például az elmúlt 365 napban az egyes alkalmazottak által küldött IM-üzenetek számának számát szeretné tárolni, akkor a következő kialakítást használhatja, amely két, különböző sémákkal rendelkező entitást használ:  

![Több entitás](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Ha olyan módosítást kell végeznie, amelyhez mindkét entitást frissíteni kell, hogy azok szinkronizálva maradjanak egymással, használhat egy EGT. Ellenkező esetben egyetlen egyesítési művelettel frissítheti az üzenetek darabszámát egy adott napra vonatkozóan. Egy adott alkalmazott összes értékének lekéréséhez mindkét entitást le kell kérnie, amely két, **PartitionKey** és **RowKey** értéket használó hatékony kéréssel végezhető el.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A teljes logikai entitások beolvasása legalább két tárolási tranzakciót foglal magában: egyet az egyes fizikai entitások lekéréséhez.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitásokat kell tárolnia, amelyek mérete vagy tulajdonságai meghaladják a Table service egyes entitásának korlátait.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* Entitás-csoport tranzakciói
* [Egyesítés vagy csere](#merge-or-replace)

## <a name="large-entities-pattern"></a>Nagyméretű entitások mintája
Nagyméretű tulajdonságértékek tárolására használjon blob Storage-t.  

### <a name="context-and-problem"></a>Kontextus és probléma
Az egyes entitások összesen legfeljebb 1 MB adatmennyiséget tárolhatnak. Ha a tulajdonságok közül egy vagy több olyan értéket tárol, amely az entitás teljes méretét eredményezi, akkor a Table serviceban nem lehet a teljes entitást tárolni.  

### <a name="solution"></a>Megoldás
Ha az entitás mérete meghaladja az 1 MB-ot, mert egy vagy több tulajdonság nagy mennyiségű adatmennyiséget tartalmaz, akkor a Blob service tárolhatja az adatait, majd a blob címeit az entitás egyik tulajdonságában tárolhatja. Tárolhat például egy alkalmazott fényképét a blob Storage-ban, és a fényképre mutató hivatkozást tárolhat az alkalmazotti entitás **Photo (fénykép** ) tulajdonságában:  

![Fénykép tulajdonság](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A Table service és az Blob serviceban lévő entitások közötti állandó konzisztencia fenntartása érdekében a [végül konzisztens tranzakciós mintát](#eventually-consistent-transactions-pattern) használja az entitások karbantartásához.
* A teljes entitások beolvasása legalább két tárolási tranzakciót foglal magában: egyet az entitás lekéréséhez, egyet pedig a blob adatainak lekéréséhez.  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitásokat kell tárolnia, amelyek mérete meghaladja a Table service egyes entitásának korlátait.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* [Széles entitások mintája](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Előtag/Hozzáfűzés Anti-pattern
Növelje a méretezhetőséget, ha nagy mennyiségű lapkákkal rendelkezik a lapkák több partíción való elosztásával.  

### <a name="context-and-problem"></a>Kontextus és probléma
Az entitások a tárolt entitások számára történő előállítása vagy hozzáfűzése általában azt eredményezi, hogy az alkalmazás új entitásokat ad hozzá a partíciók egy sorozatából lévő első vagy utolsó partícióhoz. Ebben az esetben a beillesztett adatok mindegyike ugyanabban a partícióban zajlik, és egy olyan hotspotot hoz létre, amely megakadályozza a Table szolgáltatás terheléselosztását több csomópont között, és esetleg azt okozza, hogy az alkalmazás megnyomja a partícióra vonatkozó méretezhetőségi célokat. Ha például olyan alkalmazással rendelkezik, amely az alkalmazottak hálózati és erőforrás-hozzáférését naplózza, akkor az alábbi ábrán látható entitás-struktúra az aktuális óra partíciójának válik elérhetővé, ha a tranzakciók mennyisége eléri az egyes partíciók skálázhatósági célját:  

![Entitás szerkezete](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Megoldás
A következő alternatív entitás-struktúra elkerüli a hotspotot egy adott partíción, amikor az alkalmazás naplózza az eseményeket:  

![Alternatív entitás szerkezete](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Figyelje meg ezt a példát, hogy a **PartitionKey** és a **RowKey** is összetett kulcsok legyenek. A **PartitionKey** mind a részleg, mind az alkalmazott azonosítója használatával osztja szét a naplózást több partíció között.  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az alternatív kulcs szerkezete, amely elkerüli a gyakori partíciók gyors létrehozását a lapkákon, hatékonyan támogatja az ügyfélalkalmazás által nyújtott lekérdezéseket?  
* Az Ön várható tranzakciós mennyisége azt jelenti, hogy valószínűleg el fogja érni az egyes partíciók méretezhetőségi céljait, és a tárolási szolgáltatás szabályozza azt?  

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Kerülje a előtag/Hozzáfűzés elleni mintázatot, ha a tranzakciók mennyisége valószínűleg a tárolási szolgáltatás általi szabályozást eredményez, amikor egy gyors partíciót próbál elérni.  

### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* [Napló farok mintája](#log-tail-pattern)  
* [Entitások módosítása](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Adatnapló adattípusa
A naplófájlok tárolásához általában a Table service helyett a Blob service kell használnia.  

### <a name="context-and-problem"></a>Kontextus és probléma
A naplózási adatok általános használati esete egy adott dátum-és időtartományhoz tartozó naplóbejegyzések beolvasása. például szeretné megkeresni az alkalmazás által a 15:04 és a 15:06 között az adott napon naplózott összes hibát és kritikus üzenetet. Nem kívánja használni a naplófájl dátumát és időpontját, hogy meghatározza azt a partíciót, amelybe a napló entitásokat menteni kívánja: Ez a művelet egy gyors partíciót eredményez, mivel az adott időpontban minden napló entitás ugyanazt a **PartitionKey** -értéket fogja megosztani (lásd a következő szakaszt: [előtag/Hozzáfűzés Anti-pattern](#prepend-append-anti-pattern)). A naplófájl következő entitás-sémája például egy gyors partíciót eredményez, mivel az alkalmazás az összes naplózási üzenetet az aktuális dátumra és órára írja a partícióra:  

![Üzenet naplózása entitás](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

Ebben a példában a **RowKey** tartalmazza a napló üzenetének dátumát és időpontját, így biztosítva, hogy a rendszer a naplófájlokat dátum/idő sorrendbe rendezi, és tartalmazza az üzenet azonosítóját abban az esetben, ha több naplófájl ugyanazt a dátumot és időpontot használja.  

Egy másik módszer egy olyan **PartitionKey** használata, amely biztosítja, hogy az alkalmazás üzeneteket írjon a különböző partíciók között. Ha például a naplófájl forrása lehetővé teszi az üzenetek több partíción keresztüli terjesztését, a következő entitás-sémát használhatja:  

![Alternatív napló üzenetének entitása](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

A séma problémája azonban az, hogy egy adott időtartomány összes naplófájljának lekéréséhez a táblában lévő összes partíción keresnie kell.

### <a name="solution"></a>Megoldás
Az előző szakasz rámutatott arra a problémára, amellyel a Table service a naplóbejegyzések tárolására, és javasolt két, nem kielégítő, formatervezési minták használatára. Az egyik megoldás egy olyan gyors partíciót eredményezett, amely gyenge teljesítményű írási naplót jelent. a másik megoldás rossz lekérdezési teljesítményt eredményezett, mert a táblázatban lévő összes partíció vizsgálatára vonatkozó követelmény egy adott időtartományhoz tartozó naplófájlok beolvasásához szükséges. A blob Storage jobb megoldást kínál az ilyen típusú forgatókönyvekhez, és így Azure Storage Analytics tárolja az általa gyűjtött napló adatait.  

Ez a szakasz azt ismerteti, hogyan tárolja a Storage Analytics a blob Storage-ban tárolt naplófájlokat az olyan adattárolási módszer szemléltetésére, amelyet általában a tartomány alapján kérdez le.  

Storage Analytics a naplófájlokat több blobban tagolt formátumban tárolja. A tagolt formátum megkönnyíti az ügyfélalkalmazás számára a naplófájlban lévő adatelemzést.  

Storage Analytics a Blobok elnevezési konvenciójának használatával megkeresheti azokat a blobokat (vagy blobokat), amelyek a keresett naplófájlokat tartalmazzák. Egy "üzenetsor/2014/07/31/1800/000001. log" nevű blob például a üzenetsor-szolgáltatáshoz kapcsolódó naplófájlokat tartalmaz, amelyek az óra 18:00. július 31-ig, 2014-kor kezdődnek. A "000001" azt jelzi, hogy ez az időszak első naplófájlja. A Storage Analytics a fájlban tárolt első és utolsó naplózási üzenetek időbélyegét is rögzíti a blob metaadatainak részeként. A blob Storage-hoz készült API lehetővé teszi, hogy megkeresse a tárolóban lévő blobokat egy név előtag alapján: Ha meg szeretné keresni az összes olyan blobot, amely üzenetsor-naplózási adat18:00 tárolási naplót tartalmaz, akkor a "üzenetsor/2014/07/31/1800" előtagot használhatja.  

Storage Analytics pufferek belső naplófájlokat küldenek, majd rendszeresen frissítik a megfelelő blobot, vagy létrehoznak egy újat a naplóbejegyzések legújabb kötegével. Ez csökkenti a blob szolgáltatásnak elvégezhető írások számát.  

Ha a saját alkalmazásában hasonló megoldást valósít meg, meg kell fontolnia, hogyan kezelheti a megbízhatóság és a szolgáltatás közötti kompromisszumot (a blob Storage-ba való beléptetésének megírásával), valamint a költséghatékonyságot és méretezhetőséget (az alkalmazásban lévő frissítések pufferelése és a blob Storage-tárolóba történő írása).  

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A naplófájlok tárolási módjának meghatározásakor vegye figyelembe a következő szempontokat:  

* Ha olyan tábla-kialakítást hoz létre, amely elkerüli a lehetséges gyors partíciókat, előfordulhat, hogy a rendszer nem fér hozzá a napló adataihoz.  
* A naplófájlok feldolgozásához az ügyfeleknek gyakran sok rekordot kell betölteni.  
* Bár a naplózási adatként gyakran strukturált, a blob Storage lehet jobb megoldás.  

## <a name="implementation-considerations"></a>Implementálási szempontok
Ez a szakasz azokat a szempontokat ismerteti, amelyeket figyelembe kell venni az előző szakaszokban leírt mintázatok megvalósításakor. Ennek a szakasznak a nagy része C# nyelven írt példákat használ, amelyek a Storage ügyféloldali kódtárat használják (az írás időpontjában a 4.3.0 verziója).  

## <a name="retrieving-entities"></a>Entitások beolvasása
Ahogy az a lekérdezési szakaszban is látható, a leghatékonyabb lekérdezés a pont lekérdezése. Bizonyos esetekben azonban előfordulhat, hogy több entitást kell lekérnie. Ez a szakasz néhány gyakori megközelítést ismertet az entitások tárolási ügyféloldali kódtár használatával történő beolvasásához.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Pont lekérdezés végrehajtása a Storage ügyféloldali kódtár használatával
A pontok lekérdezésének legegyszerűbb módja a táblázat **lekérése** művelet használata a következő C# kódrészletben látható módon, amely egy "Sales" **PartitionKey** és egy "212" értékű **RowKey** rendelkező entitást kér le:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Figyelje meg, hogy ez a példa arra vár, hogy a beolvasott entitás **EmployeeEntity** típusú legyen.  

### <a name="retrieving-multiple-entities-using-linq"></a>Több entitás beolvasása a LINQ használatával
A LINQ használatával több entitást is beolvashat a Table serviceból, ha Microsoft Azure Cosmos Table standard könyvtárral dolgozik. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Ahhoz, hogy az alábbi példák működjenek, meg kell adnia a névtereket:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

A employeeTable egy CloudTable-objektum, amely egy CreateQuery \<ITableEntity> () metódust valósít meg, amely egy TableQuery ad vissza \<ITableEntity> . Az ilyen típusú objektumok egy IQueryable valósítanak meg, és lehetővé teszik a LINQ lekérdezési kifejezések és a dot jelölési szintaxis használatát is.

Több entitás beolvasása és egy **Where** záradékkal rendelkező lekérdezés megadásával érhető el. A táblák vizsgálatának elkerüléséhez mindig adja meg a **PartitionKey** értékét a WHERE záradékban, és ha lehetséges, akkor a **RowKey** értéket a tábla és a partíció ellenőrzésének elkerülése érdekében. A Table szolgáltatás támogatja az összehasonlító operátorok korlátozott készletét (nagyobb, mint, nagyobb vagy egyenlő, kisebb, mint, kisebb vagy egyenlő, egyenlő és nem egyenlő) a WHERE záradékban való használathoz. 

A következő C# kódrészlet megkeresi az összes olyan alkalmazottat, akiknek a vezetékneve "B" karakterrel kezdődik (feltéve, hogy a **RowKey** az utolsó nevet tárolja) az értékesítési részlegben (feltéve, hogy a **PartitionKey** a részleg nevét tárolja):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Figyelje meg, hogyan határozza meg a lekérdezés a **RowKey** és a **PartitionKey** is a jobb teljesítmény érdekében.  

A következő mintakód az egyenértékű funkciókat mutatja be a LINQ szintaxis használata nélkül:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> A minta több **CombineFilters** metódust ágyaz be, hogy tartalmazza a három szűrési feltételt.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Nagy számú entitás beolvasása egy lekérdezésből
Az optimális lekérdezés egy egyedi entitást ad vissza egy **PartitionKey** érték és egy **RowKey** érték alapján. Bizonyos esetekben azonban előfordulhat, hogy számos entitást kell visszaadnia ugyanabból a partícióból, vagy akár sok partícióból is.  

Az alkalmazás teljesítményét mindig teljes mértékben tesztelheti ilyen esetekben.  

A Table szolgáltatásra irányuló lekérdezés egyszerre legfeljebb 1 000 entitást adhat vissza, és legfeljebb öt másodpercig futhat. Ha az eredményhalmaz több mint 1 000 entitást tartalmaz, ha a lekérdezés nem fejeződött be öt másodpercen belül, vagy ha a lekérdezés átlépi a partíció határát, a Table service egy folytatási tokent ad vissza, amely lehetővé teszi, hogy az ügyfélalkalmazás a következő készletet igényelje. A folytatási tokenek működésével kapcsolatos további információkért lásd: [lekérdezési időkorlát és tördelés](/rest/api/storageservices/Query-Timeout-and-Pagination).  

Ha a Storage ügyféloldali kódtárat használja, akkor automatikusan képes kezelni a folytatási jogkivonatokat, mert az entitásokat visszaadja a Table serviceból. A következő C#-mintakód a Storage ügyféloldali kódtár használatával automatikusan kezeli a folytatási jogkivonatokat, ha a Table szolgáltatás a válaszban visszaadja őket:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

A következő C#-kód explicit módon kezeli a folytatási jogkivonatokat:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

A folytatási tokenek explicit módon történő használatával szabályozhatja, hogy az alkalmazás Mikor kéri le a következő adatszegmenst. Ha például az ügyfélalkalmazás lehetővé teszi a felhasználók számára, hogy a táblázatban tárolt entitásokon keresztül Lapozzák fel a felhasználókat, a felhasználók dönthetnek úgy, hogy a lekérdezés által lekért összes entitáson át nem teszik a lapot, így az alkalmazás csak a folytatási tokent fogja használni, hogy lekérje a következő szegmenst, amikor a felhasználó befejezte a lapozást az aktuális szegmens összes entitásán. Ez a megközelítés számos előnnyel jár:  

* Ez lehetővé teszi, hogy korlátozza a lekérdezni kívánt adatok mennyiségét a Table serviceból, és a hálózaton halad át.  
* Lehetővé teszi, hogy aszinkron IO-t végezzen a .NET-ben.  
* Lehetővé teszi a folytatási jogkivonat szerializálását állandó tárterületre, hogy az alkalmazás összeomlása esetén is folytatódjon.  

> [!NOTE]
> A folytatási token általában egy 1 000 entitást tartalmazó szegmenst ad vissza, bár kevesebb lehet. Ez abban az esetben is igaz, ha a lekérdezés által visszaadott bejegyzések számát korlátozza a keresési feltételeknek megfelelő első n entitások visszaadásához: a Table szolgáltatás olyan szegmenst ad vissza, amely kevesebb, mint n entitást tartalmaz, és a folytatási **token segítségével** lekéri a fennmaradó entitásokat.  
> 
> 

A következő C#-kód azt mutatja be, hogyan lehet módosítani a szegmensen belül visszaadott entitások számát:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Kiszolgálóoldali kivetítés
Egyetlen entitás legfeljebb 255 tulajdonsággal rendelkezhet, és legfeljebb 1 MB méretű lehet. Ha lekérdezi a táblázatot, és beolvassa az entitásokat, előfordulhat, hogy nincs szüksége az összes tulajdonságra, és szükségtelenül nem tudja átvinni az adatátvitelt (a késés és a Cost csökkentése érdekében). Kiszolgálóoldali leképezés használatával csak a szükséges tulajdonságokat viheti át. A következő példa az **e-mail-** tulajdonságot (a **PartitionKey**, a **RowKey**, a **timestamp** és a **ETAG**) a lekérdezés által kiválasztott entitásokból kéri le.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Figyelje meg, hogyan érhető el a **RowKey** értéke annak ellenére, hogy nem szerepel a lekérdezni kívánt tulajdonságok listáján.  

## <a name="modifying-entities"></a>Entitások módosítása
A Storage ügyféloldali kódtára lehetővé teszi, hogy az entitások beszúrásával, törlésével és frissítésével módosítsa a Table szolgáltatásban tárolt entitásokat. A EGTs használatával több beszúrási, frissítési és törlési műveletet is felhasználhat, hogy csökkentse a szükséges lekerekítési utak számát, és javítsa a megoldás teljesítményét.  

Kivételek történtek, ha a Storage ügyféloldali kódtár egy EGT hajt végre, jellemzően tartalmazza a köteget okozó entitás indexét. Ez akkor hasznos, ha az EGTs-t használó kódot hibakeresést végez.  

Azt is gondolja át, hogy a terv hogyan befolyásolja az ügyfélalkalmazás a párhuzamossági és frissítési műveleteket.  

### <a name="managing-concurrency"></a>Az egyidejűség kezelése
Alapértelmezés szerint a Table szolgáltatás a **beszúrási**, **egyesítési** és **törlési** műveletek szintjén hajtja végre az optimista egyidejűségi ellenőrzéseket, bár lehetséges, hogy egy ügyfél kényszeríti a Table szolgáltatást, hogy megkerülje ezeket az ellenőrzéseket. További információ arról, hogyan kezeli a Table szolgáltatás a párhuzamosságot: az  [Egyidejűség kezelése Microsoft Azure Storageban](../blobs/concurrency-manage.md).  

### <a name="merge-or-replace"></a>Egyesítés vagy csere
A **TableOperation** osztály **replace** metódusa mindig a Table Service teljes entitását váltja fel. Ha nem tartalmaz tulajdonságot a kérelemben, ha az adott tulajdonság létezik a tárolt entitásban, a kérelem eltávolítja a tulajdonságot a tárolt entitásból. Hacsak nem szeretne explicit módon eltávolítani egy tulajdonságot egy tárolt entitásból, a kérelemben szereplő összes tulajdonságot fel kell vennie.  

A **TableOperation** osztály **egyesítési** metódusával csökkentheti a Table Service küldött adatmennyiséget, ha egy entitást szeretne frissíteni. Az **egyesítési** módszer lecseréli a tárolt entitás bármely tulajdonságát a kérelemben szereplő entitás tulajdonságértékek értékével, de érintetlenül hagy minden olyan tulajdonságot, amely nem szerepel a kérelemben. Ez akkor hasznos, ha nagyméretű entitásokkal rendelkezik, és csak kis számú tulajdonságot kell frissítenie egy kérelemben.  

> [!NOTE]
> A **replace** és az **merge** metódus meghiúsul, ha az entitás nem létezik. Másik lehetőségként használhatja a **InsertOrReplace** és a **InsertOrMerge** metódust, amely új entitást hoz létre, ha az nem létezik.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Heterogén entitások típusának használata
Az Table service egy *séma nélküli tábla-* áruház, amely azt jelenti, hogy egyetlen tábla képes több típusú entitás tárolására, amelyek nagy rugalmasságot biztosítanak a kialakításban. Az alábbi példa egy olyan táblázatot mutat be, amely az alkalmazottak és a részleg entitásait tárolja:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Minden entitásnak továbbra is **PartitionKey**, **RowKey** és **timestamp** értékekkel kell rendelkeznie, de bármilyen tulajdonsága lehet. Ezen kívül nincs lehetőség arra, hogy az entitás típusát jelezze, ha nem úgy dönt, hogy valahol tárolja ezt az információt. Az entitás típusának azonosítására két lehetőség áll rendelkezésre:  

* Adja meg az entitás típusát a **RowKey** (vagy esetleg a **PartitionKey**). Például **EMPLOYEE_000123** vagy **DEPARTMENT_SALES** **RowKey** értékként.  
* Egy külön tulajdonsággal rögzítheti az entitás típusát az alábbi táblázatban látható módon.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Részleg</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Az első lehetőség, amely az entitás típusától függően a **RowKey**, akkor hasznos, ha fennáll a lehetősége, hogy a különböző típusú entitások esetében azonos a kulcs értéke. A partícióban azonos típusú entitásokat is csoportosít.  

Az ebben a szakaszban ismertetett módszerek különösen az útmutató korábbi, a [modellezési kapcsolatok](table-storage-design-modeling.md)című cikkben szereplő, a megbeszélések [öröklési kapcsolataira](table-storage-design-modeling.md#inheritance-relationships) vonatkoznak.  

> [!NOTE]
> Érdemes megfontolnia, hogy az entitás típusa érték tartalmazza-e a verziószámot, hogy az ügyfélalkalmazások a POCO objektumokat fejlődjön, és különböző verziókkal működjenek.  
> 
> 

A szakasz további része a Storage ügyféloldali kódtár néhány olyan szolgáltatását ismerteti, amely megkönnyíti a több entitás típusának használatát ugyanabban a táblában.  

### <a name="retrieving-heterogeneous-entity-types"></a>Heterogén entitások típusának beolvasása
Ha a Storage ügyféloldali kódtárat használja, három lehetősége van több entitás típusának használatára.  

Ha ismeri a megadott **RowKey** -és **PartitionKey** -értékekkel tárolt entitás típusát, akkor megadhatja az entitás típusát, amikor az előző két példa szerint beolvassa a **EmployeeEntity** típusú entitásokat: a [tárolási ügyféloldali kódtár használatával lekéri a pont lekérdezését](#executing-a-point-query-using-the-storage-client-library) , és [több entitást is beolvas a LINQ használatával](#retrieving-multiple-entities-using-linq).  

A második lehetőség, hogy a **DynamicTableEntity** típust (a tulajdonság táska) használja a konkrét poco típusú entitások típusa helyett (ez a beállítás javíthatja a teljesítményt, mert nem szükséges szerializálni és deszerializálni az entitást a .net-típusokra). A következő C#-kód lehetséges, hogy több, különböző típusú entitást kér le a táblából, de az összes entitást **DynamicTableEntity** -példányként adja vissza. Ezután a **EntityType** tulajdonsággal határozza meg az egyes entitások típusát:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
    
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
    EntityProperty entityTypeProperty;
    if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
    {
        if (entityTypeProperty.StringValue == "Employee")
        {
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Más tulajdonságok beolvasásához a **DynamicTableEntity** osztály **Properties (Tulajdonságok** ) tulajdonságában a **TryGetValue** metódust kell használnia.  

Egy harmadik lehetőség a **DynamicTableEntity** típus és egy **EntityResolver** -példány használatával történő egyesítés. Ez lehetővé teszi több POCO típus feloldását ugyanabban a lekérdezésben. Ebben a példában a **EntityResolver** delegált a **EntityType** tulajdonság használatával különbözteti meg a lekérdezés által visszaadott két típusú entitást. A **megoldási** metódus a **feloldó** delegált használatával oldja fel a **DynamicTableEntity** -példányokat **TableEntity** -példányokra.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{
    TableEntity resolvedEntity = null;
    if (props["EntityType"].StringValue == "Department")
    {
        resolvedEntity = new DepartmentEntity();
    }
    else if (props["EntityType"].StringValue == "Employee")
    {
        resolvedEntity = new EmployeeEntity();
    }
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Heterogén entitások típusának módosítása
Nem kell tudnia, hogy milyen típusú entitást kell törölni, és mindig ismeri az entitás típusát az beszúrásakor. A **DynamicTableEntity** -típus használatával azonban a típusának ismerete nélkül frissítheti az entitásokat, és nem használhatja a Poco Entity osztályt. A következő mintakód egyetlen entitást kérdez le, és a frissítés előtt ellenőrzi, hogy a **EmployeeCount** tulajdonság létezik-e.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Hozzáférés szabályozása közös hozzáférésű aláírásokkal
A megosztott hozzáférés-aláírási (SAS-) tokenekkel engedélyezheti az ügyfélalkalmazások számára a tábla entitások módosítását (és lekérdezését) anélkül, hogy tartalmaznia kellene a Storage-fiók kulcsát a kódban. Az SAS használatának három fő előnye van az alkalmazásban:  

* A Storage-fiók kulcsát nem szükséges egy nem biztonságos platformra (például egy mobileszközön) terjeszteni ahhoz, hogy az eszköz hozzáférjen és módosíthassa a Table service entitásait.  
* A webes és feldolgozói szerepkörök egy részét kiszervezheti az entitások az ügyfélszámítógépekre, például a végfelhasználói számítógépekre és a mobileszközök kezelésére.  
* Az ügyfelek számára korlátozott és időkorlátos engedélyeket rendelhet hozzá (például a csak olvasási hozzáférés engedélyezése adott erőforrásokhoz).  

Az SAS-tokenek Table service használatával történő használatáról további információt a [közös hozzáférésű aláírások (SAS) használata](../../storage/common/storage-sas-overview.md)című témakörben talál.  

Azonban továbbra is olyan SAS-jogkivonatokat kell generálnia, amelyek az ügyfélalkalmazások számára engedélyezik a Table szolgáltatás entitásait: ezt olyan környezetben kell végrehajtania, amely biztonságos hozzáférést biztosít a Storage-fiók kulcsaihoz. Általában webes vagy feldolgozói szerepkört használ az SAS-jogkivonatok létrehozásához, és azokat olyan ügyfélalkalmazások számára kézbesíteni, amelyeknek hozzá kell férniük az entitásokhoz. Mivel továbbra is az SAS-tokenek ügyfeleknek való létrehozásával és megvalósításával kapcsolatos költségek merülnek fel, érdemes megfontolni, hogy a lehető leghatékonyabban csökkentse a terhelést, különösen nagy mennyiségű forgatókönyv esetén.  

Olyan SAS-tokent is létre lehet hozni, amely hozzáférést biztosít egy tábla entitásai egy részhalmazához. Alapértelmezés szerint egy teljes táblázathoz hoz létre SAS-jogkivonatot, de azt is megadhatja, hogy az SAS-jogkivonat hozzáférést biztosítson egy **PartitionKey** -értékhez, vagy egy **PartitionKey** -és **RowKey** -érték tartományához. Úgy is dönthet, hogy SAS-jogkivonatokat állít elő a rendszer egyes felhasználói számára, hogy az egyes felhasználók SAS-jogkivonata csak a Table szolgáltatásban lévő saját entitásokhoz férhessenek hozzá.  

## <a name="asynchronous-and-parallel-operations"></a>Aszinkron és párhuzamos műveletek
Ha több partíción terjeszti át a kéréseket, az átviteli sebességet és az ügyfelek közötti rugalmasságot aszinkron vagy párhuzamos lekérdezések használatával növelheti.
Előfordulhat például, hogy két vagy több feldolgozói szerepkörrel rendelkező példánya párhuzamosan fér hozzá a táblákhoz. Lehet, hogy egyes feldolgozói szerepkörök a partíciók meghatározott készleteit terhelik, vagy egyszerűen több feldolgozói szerepkör-példánnyal rendelkeznek, amelyek mindegyike egy tábla összes partíciójának elérésére képes.  

Egy ügyfél-példányon belül a tárolási műveletek aszinkron végrehajtásával javíthatja az átviteli sebességet. A Storage ügyféloldali kódtára megkönnyíti az aszinkron lekérdezések és módosítások írását. Előfordulhat például, hogy a szinkron metódussal indul, amely egy partíció összes entitását lekéri a következő C#-kódban látható módon:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

A kód egyszerűen módosítható úgy, hogy a lekérdezés aszinkron módon fusson a következőképpen:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Ebben az aszinkron példában a szinkron verzió következő változásai láthatók:  

* A metódus aláírása mostantól tartalmazza az **aszinkron** módosítót, és visszaadja a **feladat** példányát.  
* Ahelyett, hogy a **ExecuteSegmented** metódust az eredmények beolvasására szólítja fel, a metódus most meghívja a **ExecuteSegmentedAsync** metódust, és a **várakozási** módosító használatával aszinkron módon kéri le az eredményeket.  

Az ügyfélalkalmazás többször is meghívhatja ezt a metódust (a **Department** paraméter különböző értékeivel), és mindegyik lekérdezés külön szálon fog futni.  

A **végrehajtási** metódusnak nincs aszinkron verziója a **TableQuery** osztályban, mert az **IEnumerable** felület nem támogatja az aszinkron enumerálást.  

Az entitásokat aszinkron módon is beszúrhatja, frissítheti és törölheti. A következő C#-példa egy egyszerű, szinkron módszert mutat be egy alkalmazott entitás beszúrásához vagy cseréjéhez:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

A kód egyszerűen módosítható úgy, hogy a frissítés aszinkron módon fusson a következőképpen:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Ebben az aszinkron példában a szinkron verzió következő változásai láthatók:  

* A metódus aláírása mostantól tartalmazza az **aszinkron** módosítót, és visszaadja a **feladat** példányát.  
* Ahelyett, hogy meghívja a **Execute** metódust az entitás frissítéséhez, a metódus most meghívja a **ExecuteAsync** metódust, és a **várakozási** módosító használatával aszinkron módon kéri le az eredményeket.  

Az ügyfélalkalmazás több aszinkron metódust is meghívhat, mint például ez, és minden metódus meghívása külön szálon fut.  

## <a name="next-steps"></a>Következő lépések

- [Kapcsolatok modellezése](table-storage-design-modeling.md)
- [Tervezés lekérdezéshez](table-storage-design-for-query.md)
- [Táblaadatok titkosítása](table-storage-design-encrypt-data.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)