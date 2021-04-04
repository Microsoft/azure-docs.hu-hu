---
title: Tanácsadási szolgáltatás ajánlatának megtervezése a Microsoft kereskedelmi piactéren
description: Új tanácsadói szolgáltatási ajánlat tervezése Microsoft AppSource vagy Azure Marketplace-hez a Microsoft partner Center kereskedelmi Marketplace programjának használatával.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 10c06f5b36de880302f17dc17c1d47d5209dd902
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588471"
---
# <a name="how-to-plan-a-consulting-service-offer-in-the-commercial-marketplace"></a>Tanácsadási szolgáltatás ajánlatának megtervezése a kereskedelmi piactéren

Ez a cikk bemutatja a Microsoft kereskedelmi Marketplace-re irányuló tanácsadói szolgáltatási ajánlatok közzétételének különböző lehetőségeit és követelményeit. Ebből a cikkből megtudhatja, hogyan készítheti elő ajánlatát a partner centerrel való közzétételhez.

## <a name="whats-a-consulting-service"></a>Mi a tanácsadási szolgáltatás?

A tanácsadási szolgáltatások olyan virtuális vagy személyes ügyfélkapcsolatok, amelyek támogatják és kiterjesztik a Microsoft-termékek ügyfeleinek használatát. A tanácsadási szolgáltatással az ügyfelek értékelik, kiértékelik és üzembe helyezhetik az üzleti céljaik további megoldásait. Kiválaszthatja a szolgáltatás hatókörét, időtartamát és árképzési szerkezetét (rögzített vagy ingyenes).

A tanácsadási szolgáltatás ajánlatait a rendszer a **kapcsolatfelvételi** listákként teszi közzé. Ez azt jelenti, hogy az ügyfelek közvetlenül az Ön által megadott adatokból fognak kapcsolatba lépni Önnel. Míg a Microsoft a kereskedelmi piactéren üzemelteti a listát, az ügyfelekkel folytatott összes interakciót kezelheti. Ön felelős a szolgáltatás, a számlázás, a számlázás és az ügyféltől való összegyűjtésért.

## <a name="primary-products-and-online-stores"></a>Elsődleges termékek és online áruházak

Minden tanácsadási szolgáltatásnak az alábbi Microsoft-termékek egyikére kell összpontosítania, amelyet az ajánlat **elsődleges termékének** nevezünk:

:::row:::
    :::column:::
        Azure

        Dynamics 365 Business Central

        Dynamics 365 kereskedelmi

        Dynamics 365 Customer Insights

        Dynamics 365 Customer szolgáltatás

        Dynamics 365 mező szolgáltatás

        Dynamics 365 – Pénzügy

        Dynamics 365 marketing
    :::column-end:::
    :::column:::
        Dynamics 365 Project szolgáltatás automatizálása

        Dynamics 365 Sales

        Dynamics 365 ellátási lánc kezelése

        Microsoft 365

        Power Apps

        Power Automate

        Power BI

        Power Virtual Agents
    :::column-end:::
:::row-end:::

A partner Centerben kiválasztott elsődleges termék meghatározza, hogy az ajánlat közzé lesz-e téve Microsoft AppSource vagy az Azure Marketplace-en.

* Ha a tanácsadási szolgáltatás elsődleges terméke az Azure, az ajánlat az Azure Marketplace-en jelenik meg.
* Ha az elsődleges termék nem az Azure, az ajánlat a AppSource webhelyen jelenik meg.

Ha többet szeretne megtudni a AppSource és az Azure Marketplace közötti különbségekről, tekintse meg a [kereskedelmi piactér online áruházait](./overview.md#commercial-marketplace-online-stores).

## <a name="eligibility-requirements"></a>Jogosultsági követelmények

Annak bemutatásához, hogy az ügyfelek egy adott területen szakértelmét nyújtsanak, meg kell felelnie a jogosultsági követelményeknek, mielőtt közzéteszi a tanácsadói szolgáltatást. A követelmények az ajánlat középpontjában álló terméktől függenek. Az egyes elsődleges termékekre vonatkozó támogathatósági követelmények teljes listája a [tanácsadási szolgáltatások minősítési házirendjeiben](/legal/marketplace/certification-policies#800-consulting-services)található.

> [!NOTE]
> Egyes elsődleges termékek esetén a Microsoft egy arany-vagy ezüst-kompetenciával rendelkezik a megoldás területén. További információ: [Microsoft Partner Network kompetenciák](https://partner.microsoft.com/membership/competencies).

## <a name="service-type-and-duration"></a>Szolgáltatás típusa és időtartama

A kereskedelmi piactér öt típusú tanácsadói szolgáltatást támogat:

* **Értékelés**: az ügyfél környezetének kiértékelése a megoldás alkalmazhatóságának meghatározásához, valamint a megvalósítás költségeit és idővonalának becsléséhez.
* **Tájékoztatás**: egy megoldás vagy szolgáltatás bemutatása keretrendszerek, bemutatók és felhasználói példák használatával.
* **Implementáció**: teljes telepítés, amely teljesen működőképes megoldást eredményez.
* A **koncepció igazolása**: korlátozott hatókörű implementáció annak megállapítására, hogy a megoldás megfelel-e az ügyfél követelményeinek.
* **Workshop**: interaktív részvétel az ügyfél telephelyén. Olyan képzéseket, tájékoztatókat, felméréseket és bemutatókat is tartalmazhat, amelyek az ügyfél által tárolt adatszolgáltatásra vagy környezetre épülnek.

A szolgáltatásnak rögzített és előre meghatározott időtartammal kell rendelkeznie, amely legfeljebb 10 hétig tart. A szolgáltatás időtartamát explicit módon kell megadni az ajánlati listán.

## <a name="customer-leads"></a>Ügyfél-érdeklődők

Az ügyfelek adatainak összegyűjtéséhez az ajánlatot az Ügyfélkapcsolat-kezelési (CRM) rendszerhez kell kötni. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és online áruházát, ahol az ajánlatot megtalálták, a rendszer a konfigurált CRM-rendszerbe küldi el. A kereskedelmi piactér különböző típusú CRM-rendszereket támogat, valamint lehetővé teszi az Azure-táblák használatát, vagy egy HTTPS-végpont konfigurálását a Power automatizálás használatával.

A CRM-kapcsolatok bármikor hozzáadhatók vagy módosíthatók az ajánlat létrehozásakor vagy azt követően is. Részletes útmutatásért tekintse [meg a kereskedelmi Marketplace-ajánlat ügyfeleinek vezetőit](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="offer-listing-details"></a>Ajánlat részletei

Amikor a partner Centerben hozza létre a tanácsadási szolgáltatás ajánlatát, szöveget, képeket, dokumentumokat és egyéb ajánlatokat adhat meg. A AppSource vagy az Azure Marketplace-en megtekintheti az ajánlatait. Lásd a következő példát:

![Bemutatja, hogyan jelenik meg a tanácsadási szolgáltatás ajánlata az online áruházakban](./media/example-consulting-service.png)

**Lehívási leírások**

1. Embléma
2. Szolgáltatás típusa
3. Kompetenciák
4. Megoldási területek (Azure Marketplace)/termékek (AppSource)
5. Iparágak
6. Ország/régió
7. Ajánlat neve
8. Keresési eredmények összegzése
9. Leírás
10. Képernyőképek/videók
11. Támogató dokumentumok

> [!NOTE]
> Ha az angoltól eltérő nyelven fogja kézbesíteni a szolgáltatást, az ajánlati lista ezen a nyelven érhető el, de a leírásnak a következő angol nyelvű kifejezéssel kell kezdődnie vagy végződnie: "Ez a szolgáltatás az &lt; ajánlat tartalma> nyelven elérhető. A támogatási dokumentumokat olyan nyelven is megadhatja, amely különbözik az ajánlati lista részleteiben használt nyelvtől.

Ha könnyebben létre szeretné hozni az ajánlatot, készítse elő ezeket az elemeket az idő előtt. Ha másként nincs jelezve, a következő elemek szükségesek.

**Name (név**): Ez a név jelenik meg a kereskedelmi piactéren az ajánlati lista címeként. A név lehet védjeggyel ellátott. Nem tartalmazhat hangulatjelek (kivéve, ha a védjegyek és a szerzői jogi szimbólumok), és legfeljebb 50 karakter hosszúnak kell lennie. A névnek tartalmaznia kell az ajánlat időtartamát és a szolgáltatás típusát, hogy maximalizálja a keresőmotor-optimalizálást (SEO). A kötelező formátum *neve: időtartam + típus*. Csak akkor adja meg a vállalat nevét, ha a termék neve is. Íme néhány példa:

|Ne mondd |Mondja a következőt:  |
|---|---|
|Első lépések az Azure IoT a gyártásban |Gyártási IoT: 2 napos Értékelés |
|Workshop az intelligens kenyérpirítók számára |Intelligens kenyérpirítók: 1 hetes workshop |
|SQL Server áttelepítési PoC a contoso által |SQL-Migrálás: 3 – hét a koncepció igazolása |
| | |

**Keresési eredmények összegzése**: az ajánlat célját vagy célját 200 vagy kevesebb karakterből írja le. Ezt az összegzést a kereskedelmi piactéren a keresési eredmények listájában lehet használni. Nem lehet azonos a címmel. Érdemes megfontolni a legnépszerűbb SEO-kulcsszavakat is.

**Leírás**: Ez a leírás a kereskedelmi piactér listájában jelenik meg. Vegye figyelembe az érték kiosztását, a főbb előnyöket, a kívánt felhasználói bázist és bármely kategóriát vagy iparági társítást is.

A Leírás írásakor kövesse az alábbi feltételeket a szolgáltatás típusa szerint:

|Szolgáltatás típusa |Leírási követelmények |
|---|---|
|Értékelés |Adjon meg egy részletes napirendet az értékelésekhez egy napnál hosszabb ideig, és ismertesse, hogy az ügyfél mire számíthat. |
|Eligazítás |Magyarázza el, hogy az ügyfél mire számíthat.|
|Implementálás |Több mint egy nap alatt részletes menetrendet adhat meg a megvalósításokhoz, és leírhatja, hogy milyen mérnöki változások, technikai összetevők vagy egyéb összetevők számíthatnak az ügyfél az engagement eredményeire. |
|Megvalósíthatósági vizsgálat |Írja le, hogy milyen mérnöki változások, technikai összetevők vagy egyéb összetevők várhatók az ügyfelek számára az engagement eredményeinek megfelelően. |
|Workshop |Az ajánlat időtartamától függően részletes napi, heti vagy havi rendszerességgel is megadható. A workshop tanulási céljainak és egyéb termékeinek tagolása. |
| | |

Íme néhány tipp a Leírás írásához:

* Egyértelműen ismertesse az ajánlat értékét az első néhány mondatban, beleértve a következőket:
    * Az ajánlat által előnyben részesülő felhasználó típusa.
    * Az ügyfél által igényelt vagy az ajánlat által felmerülő problémák.
* Ne feledje, hogy az első néhány mondat a keresési eredmények között jelenhet meg.
* Ha az ajánlat árát becsültük, magyarázza el, hogy milyen változók határozzák meg a végső árat.
* Iparág-specifikus szókincs használata.

A leírást a HTML-címkék használatával formázhatja. Ebben a mezőben legfeljebb 2 000 karaktert adhat meg, beleértve a HTML-címkéket és a szóközöket is. További információ a HTML-formázásról: [a kereskedelmi piactér ajánlatának leírásában támogatott HTML-címkék](./supported-html-tags.md).

**Kulcsszavak keresése** (nem kötelező): legfeljebb három olyan kulcsszavat adjon meg, amelyekkel az ügyfelek megtalálják az ajánlatot az online áruházakban. Nem kell megadnia az ajánlat **nevét** és **leírását**.

**Időtartam**: a tanácsadási szolgáltatási ajánlatnak előre meghatározott időtartammal kell rendelkeznie, amely legfeljebb 10 hét lehet.

**Kapcsolattartási adatok**: a partner Centerben a vállalatnál két személy nevét, e-mail-címét és telefonszámát kell megadnia (a két partner egyike lehet). Ezt az információt fogjuk használni az ajánlattal való kommunikációhoz. Ezek az információk nem jelennek meg az ügyfelek számára, de a Cloud Solution Provider (CSP) partnerei számára is elérhetők.

**Támogató dokumentumok**: töltsön fel legalább egyet, és akár három, az ügyfelek által elérhető PDF-dokumentumot, amely az ajánlatával kapcsolatos információkat tartalmaz. Ezek lehetnek például tanulmányok vagy brosúrák.

**Marketplace Media – logók**: adjon meg egy PNG-fájlt a nagy méretű emblémához. A partner Center egy kis embléma létrehozására fogja használni. A kis emblémát később is lecserélheti egy másik képre.

* Nagyméretű (216 x 216 és 350 x 350 px, kötelező)
* Kicsi (48 x 48 px, opcionális)

Az Azure Marketplace-en vagy a AppSource-on található ajánlati lista oldalán megjelenik a nagyméretű embléma. A kis embléma az Azure Marketplace keresési eredményei között, illetve a AppSource főoldalán és a keresési eredmények oldalon jelenik meg.

Kövesse az alábbi irányelveket a logókhoz:

* Ügyeljen arra, hogy a kép ne legyen elmosódott vagy kifeszítve.
* Az Azure arculata egyszerű színpalettát használ. Törekedjen minél kevesebb alap- és másodlagos szín használatára az emblémában.
* Ha átlátszó hátteret használ, az embléma és a szöveg ne legyen fehér, fekete vagy kék.
* Kerülje az embléma vagy a háttér színátmeneteit. Ne helyezzen el szöveget az emblémára, még a vállalat vagy a márka nevét se.

**Piactéri média – képernyőképek**: vegyen fel legalább egy és legfeljebb öt képet, amely bemutatja az ajánlatot. Az összes képnek 1280 x 720 képpont méretűnek és a-ben kell lennie. PNG-formátum.

**Marketplace Media – videók** (nem kötelező): legfeljebb négy olyan videót adhat hozzá, amely bemutatja az ajánlatát. A videókat a YouTube vagy a Vimeo szolgáltatásban kell tárolni, és egy miniatűrnek kell lennie (1280 x 720 PNG-fájl).

## <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

A tanácsadási szolgáltatás ajánlatát egy vagy több országban vagy régióban lehet elérhetővé tenni. A partner Centerben eldöntheti, hogy minden egyes kiválasztott piacon milyen árat kell megállapítania. A támogatott piacok és pénznemek teljes listájáért tekintse meg [a kereskedelmi piactér földrajzi elérhetőségét és a pénznemek támogatását](./marketplace-geo-availability-currencies.md)ismertető témakört.


## <a name="next-steps"></a>Következő lépések

* [Tanácsadási szolgáltatási ajánlat létrehozása a kereskedelmi piactéren](./create-consulting-service-offer.md)
* [Ajánlatlistákra vonatkozó ajánlott eljárások](./gtm-offer-listing-best-practices.md)