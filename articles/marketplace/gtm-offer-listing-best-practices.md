---
title: Ajánlati lista – ajánlott eljárások – Microsoft kereskedelmi piactér
description: Ismerje meg a Microsoft AppSource és az Azure Marketplace-ajánlatokkal kapcsolatos ajánlott eljárásokat.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 07/06/2020
ms.openlocfilehash: 3ea6a0035a9f9354be5c14699936c6a07dea1150
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94492089"
---
# <a name="offer-listing-best-practices"></a>Ajánlatlistákra vonatkozó ajánlott eljárások

Ez a cikk a Microsoft kereskedelmi Marketplace-ajánlatok létrehozásának és folytatásának javaslatait ismerteti. Az alábbi táblázatok az ajánlati információk a partner Centerben való végrehajtásával kapcsolatos ajánlott eljárásokat ismertetik. Az ajánlatok működésének elemzéséhez nyissa meg a Marketplace elemzések [irányítópultot](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/marketplaceinsights) a partner Centerben. 

## <a name="online-store-offer-details"></a>Az online áruház ajánlatának részletei

| Beállítás | Ajánlott eljárás |
|:--- |:--- |  
| Ajánlat neve | Alkalmazások esetében adjon meg egy világos címet, amely tartalmazza a keresési kulcsszavakat, amelyek segítségével az ügyfelek felfedezhetik az ajánlatot. <br> <br> A tanácsadási szolgáltatások esetében kövesse az alábbi formátumot: [ajánlat neve: [időtartam] [ajánlat típusa] (például contoso: 2 hetes implementáció) |
| Ajánlat leírása | Adjon meg egy egyértelmű leírást, amely leírja az ajánlat értékeit az első néhány mondatban.  Ne feledje, hogy ezek a mondatok a keresőmotor eredményeiben is használhatók. Az értékek kiosztásának alapvető összetevői a következők: <ul> <li>A termék vagy megoldás leírása. </li> <li> A termék vagy megoldás előnyeit élvező felhasználói persona. </li> <li> Az ügyfélnek szüksége van a termékre vagy a megoldás címére. </li> </ul> <br> Ha lehetséges, használja az iparági szabványnak megfelelő szókincset vagy juttatáson alapuló szövegezést.  Ne használja a szolgáltatásait és funkcióit a termék értékesítéséhez.  Ehelyett a megadott értékre kell összpontosítania. <br> <br> A tanácsadási szolgáltatások listázásához egyértelműen adja meg az Ön által megadott professzionális szolgáltatást. |

> [!IMPORTANT]
> Győződjön meg arról, hogy az ajánlat neve és az ajánlat leírása megfelel a **[Microsoft védjegy-és márkanevei iránymutatásainak](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)** , valamint a Microsoft-szoftverek,-termékek és-szolgáltatások nevének és a Microsoft-szoftvereknek, termékeinek és szolgáltatásainak a nevére vonatkozó, termékspecifikus irányelveknek.

## <a name="online-store-listing-details"></a>Online áruházbeli listaelemek részletei

Egy másik online áruházhoz tartozó kategóriák és iparágak a különböző ajánlati típusokra lesznek érvényesek.

| Online áruház | Kategóriák <br>online áruház szerint | Kategóriák <br>online áruház szerint | Iparágak <br> AppSource |
| :------------------- |:----------------:|:------:|:-------------:|
| **Ajánlat típusa**   |  **Azure Marketplace**  | **AppSource**  |
| Azure-alkalmazás | X | |
| Tároló | X | |
| Tanácsadási szolgáltatások | | | X |
| Dynamics 365 Customer engagement & Power platform | | X | X |
| Dynamics 365 Finance & ellátási lánc kezelése | | X | X | 
| Dynamics 365 Business Central | | X | X |
| IoT Edge modulok | X | |
| Power BI | | X | X |
| SaaS | X | X | X |
| Azure-beli virtuális gép |  X |    |

### <a name="categories"></a>Kategóriák

A Microsoft AppSource és az Azure Marketplace olyan online áruházak, amelyek különböző típusú megoldásokat kínálnak. Az Azure Marketplace az Azure-ra épülő, vagy az Azure-hoz készült megoldásokat kínál.  Microsoft AppSource olyan üzleti megoldásokat kínál, mint például az iparági SaaS-alkalmazások, a Dynamics 365 beépülő modulok, a Microsoft 365 beépülő modulok és a Power platform-alkalmazások.

A kategóriák és alkategóriák a megoldás típusa alapján vannak leképezve minden online áruházhoz. Az ajánlat a Microsoft AppSource vagy az Azure Marketplace-en lesz közzétéve az ajánlat típusától, az ajánlat tranzakciós képességeitől és a kategória/alkategória kiválasztástól függően. 

Válassza ki azokat a kategóriákat és alkategóriákat, amelyek a legjobban illeszkednek a megoldás típusához. Az alábbiak közül választhat:

* Legfeljebb két kategória, beleértve az elsődleges és a másodlagos kategóriát is (opcionális).
* Legfeljebb két alkategória lehet minden elsődleges és/vagy másodlagos kategóriához. Ha nincs kiválasztva alkategória, akkor az ajánlat csak a kiválasztott kategóriában lesz felderíthető.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

#### <a name="important-saas-offers-and-microsoft-365-add-ins"></a>Fontos: SaaS-ajánlatok és Microsoft 365-bővítmények

Tekintse meg a [kereskedelmi piactéren történő transacting](marketplace-commercial-transaction-capabilities-and-considerations.md) című témakört, amely részletesen ismerteti, hogy a Transact-funkciók milyen hatással lehetnek az ajánlat megtekintésére és a Marketplace-ügyfelek általi vásárlására. Az SaaS-ajánlatok esetében az ajánlat tranzakciós képessége, valamint a kategória kiválasztása határozza meg azt az online áruházat, ahol az ajánlat közzé lesz téve.


| SaaS-ajánlat    | SaaS-ajánlat   | SaaS-ajánlat  | SaaS-ajánlat   | SaaS-ajánlat   | SaaS-ajánlat   | SaaS-ajánlat    | Megfelelő online áruház| Megfelelő online áruház |
|:-------------:|:---:|:--------:|:---------:|:--:|:--:|:---:|:---------------------:|:-------------:|
| Mért számlázás | Bővítmények Microsoft 365 | Kapcsolatfelvétel | Transact (legalább 1 csomag) | Csak privát csomag | Csak nyilvános csomag | Nyilvános & Private-csomagok | AppSource | Azure Piactér |
|  | X |  |  |  |  |  | X |  |
| X |  |  | X | X |  |  |  | X |
| X |  |  | X |  | X |  |  | X |
| X |  |  | X |  |  | X |  | X<sup>2</sup> |
|  |  |  | X | X |  |  |  | X |
|  |  |  | X |  | X |  | X<sup>1</sup> | X<sup>1</sup> |
|  |  |  | X |  |  | X | X<sup>1</sup> | X<sup>1, 2</sup> |
|  |  | X |  |  |  |  | x<sup>1</sup> | X<sup>1</sup> | 

1. Kategóriától, alkategóriától és az iparág kiválasztástól függően
2. A privát csomagokkal rendelkező ajánlatok közzé lesznek téve a Azure Portal


### <a name="industries"></a>Iparágak

Az iparági kiválasztás csak az Azure Marketplace-en közzétett AppSource és tanácsadási szolgáltatásokban közzétett ajánlatokra vonatkozik.  Válassza az iparágak és/vagy a vertikálisak lehetőséget, ha az ajánlata az iparágra jellemző igények kielégítésével foglalkozik, és az ajánlat leírásában az iparágra jellemző képességeket hívja meg. Kiválaszthat legfeljebb két (2) iparágat és két (2) vertikális elemet.

>[!Note]
>Az Azure Marketplace-en elérhető tanácsadási szolgáltatásokhoz nem tartoznak iparági vertikális szolgáltatások.

| **Iparágak** |  **Vertikumok** |
| :------------------- | :----------------|
| **Mezőgazdaság** | |
| **Architektúra & építése** | |
| **Autóipar** | |
| **Disztribúció** | Nagykereskedelem <br> Parcella & csomag szállítása |  
| **Education** | Felsőoktatás <br> Elsődleges & másodlagos edu/K-12 <br> Könyvtárak & múzeumok |
| **Pénzügyi szolgáltatások** | Banki & tőkepiacok <br> Biztosítási | 
| **Államigazgatás** |  Védelmi & intelligencia <br> Polgári kormányzat <br> Közbiztonság & igazságszolgáltatás |
| **Egészségügy** | Állapot költségviselő <br> Állapot szolgáltatója <br> Gyógyszerek | 
| **Vendéglátás & utazás** | Utazási & szállítás <br> Hotelek & szabadidő <br> Éttermek & Food Services | 
| **Gyártási & erőforrások** | Kémiai & Agrokémiai <br> Diszkrét gyártás <br> Energia | 
| **Média & kommunikáció** | Media & Entertainment <br> Távközlés | 
| **Más állami szektorbeli iparágak** | Erdészeti & horgászat <br> Nonprofit | 
| **Professzionális szolgáltatások** | Partneri szakmai szolgáltatások <br> Jogi tudnivalók | 
| **Ingatlan** | |

Csak Microsoft AppSource iparág:

| **Iparág** |  **Vertikumok** |
| :------------------- | :----------------|
| **Lakossági & fogyasztási cikkek** | Kiskereskedők <br> Fogyasztási cikkek |

### <a name="applicable-products"></a>Alkalmazható termékek

Válassza ki azokat a megfelelő termékeket, amelyekkel az alkalmazás együttműködik, hogy az ajánlat megjelenjen a kiválasztott termékek területen a AppSource.

### <a name="search-keywords"></a>Kulcsszavak keresése

A kulcsszavak segítségével az ügyfelek megkereshetik az ajánlatot a kereséskor. Azonosítsa az ajánlat legfontosabb keresési kulcsszavait, és foglalja bele őket az ajánlat összefoglalásához és leírásához, valamint az ajánlat részletei című szakaszban található kulcsszó szakaszhoz.

## <a name="online-store-marketing-details"></a>Online áruházbeli marketing részletei
| Beállítás | Ajánlott eljárás |
|:--- |:--- |  
| Ajánlat emblémája (PNG formátum, 216 × 216 – 350 x 350 px): alkalmazás részletei lap | A digitális médiumok emblémájának megtervezése és optimalizálása:<br>Töltse fel a logót PNG formátumban az ajánlat alkalmazás részletei listázási oldalára. A partner Center átméretezi a szükséges méretű emblémákat. |
| Ajánlat emblémája (PNG formátum, 48 × 48 képpont): keresési oldal | A partner Center a feltöltött nagy embléma alapján hozza ezt az emblémát. Ezt később is lecserélheti egy másik rendszerképpel. |
| "További információ" dokumentumok | A "További információ" című részében szerepelnek a Sales és a marketing-eszközök támogatása, néhány példa:<ul><li>tanulmányok</li><li> brosúrák</li><li>Ellenőrzőlista vagy</li><li> PowerPoint-bemutatók</li></ul><br>Mentse az összes fájlt PDF formátumban. A cél az, hogy az ügyfeleket ne értékesítsen.<br><br>Vegyen fel egy hivatkozást az alkalmazás kezdőlapján az összes dokumentumra, és adjon hozzá URL-paramétereket a látogatók és a próbaverziók nyomon követéséhez. |
| Videók: AppSource, tanácsadási szolgáltatások és SaaS-ajánlatok | A legerősebb videók az ajánlat értékét a narratív formában közlik:<ul> <li> Tegye meg az ügyfelet, és ne a vállalata, a történet hőse. </li> <li> A videónak foglalkoznia kell a megcélzott ügyfél legfontosabb kihívásaival és céljaival. </li> <li> Javasolt hossz: 60-90 másodperc.</li> <li> A videók nevét használó legfontosabb keresési szavakat foglalja bele. </li> <li> Vegye fontolóra további videók, például útmutató, első lépések vagy vásárlói visszajelzések hozzáadását. </li> </ul> |
| Képernyőképek (1280 &nbsp; &times; &nbsp; 720) | Legfeljebb öt képernyőkép hozzáadása:<br>A kulcsok keresési szavainak belefoglalása a fájlnevekben. |

## <a name="link-to-your-offer-page-from-your-website"></a>Az ajánlat lapjára mutató hivatkozás a webhelyről

Ha a webhelyen lévő AppSource vagy az Azure Marketplace jelvényt a kereskedelmi piactéren található listára csatolja, akkor az URL-cím végén a következő lekérdezési paraméterekkel is támogathatja az erős elemzést és jelentéskészítést:
* **src**: adja meg azt a forrást, amelyről a forgalmat át kell irányítani a AppSource (például webhely, LinkedIn vagy Facebook).
* **mktcmpid**: a marketing kampány azonosítója, amely akár 16 karaktert is tartalmazhat betűk, számok, aláhúzások és kötőjelek (például *blogpost_12*) tetszőleges kombinációjában.

A következő példában szereplő URL-cím az előző lekérdezési paramétereket is tartalmazza: `https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

Ha hozzáadja a paramétereket a AppSource URL-címhez, a [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/)elemzési irányítópultján ellenőrizheti a kampány hatékonyságát.

## <a name="next-steps"></a>Következő lépések

További információ a [kereskedelmi piactér előnyeiről](./gtm-your-marketplace-benefits.md).

Az ajánlat létrehozásához és konfigurálásához jelentkezzen be a [partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
