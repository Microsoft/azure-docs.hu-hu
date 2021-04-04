---
title: Az SaaS-ajánlatok díjas számlázása a Microsoft kereskedelmi Marketplace-mérési szolgáltatásának használatával
description: Ismerje meg az SaaS-ajánlatok rugalmas számlázási modelljeit a kereskedelmi Marketplace-mérési szolgáltatás használatával.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9f72d54fda8f66c2fce35f0520b51406aa276bb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92892753"
---
# <a name="metered-billing-for-saas-using-the-commercial-marketplace-metering-service"></a>Díjköteles számlázás az SaaS-hez a kereskedelmi Piactéri mérési szolgáltatás használatával

A kereskedelmi piactér-mérési szolgáltatással olyan szolgáltatott szoftvereket (SaaS) tartalmazó ajánlatokat hozhat létre, amelyek a nem standard egységek alapján lesznek felszámítva. Mielőtt egy SaaS-ajánlatot közzétesz a kereskedelmi piactéren, meg kell határoznia a számlázási dimenziókat, például a sávszélességet, a jegyeket vagy a feldolgozott e-maileket.  Az ügyfelek ezt a dimenziók fogyasztása alapján fizetik ki, és a rendszer a Microsoftot a kereskedelmi Marketplace-mérési szolgáltatási API-n keresztül, a felszámított eseményeken keresztül értesíti.  

## <a name="prerequisites-for-metered-billing"></a>A mért számlázás előfeltételei

Ahhoz, hogy egy SaaS-ajánlat mért számlázást használjon, először a következőket kell tennie:

- Ismerje meg a [Microsoft-ajánlaton keresztüli értékesítésre](../plan-saas-offer.md#listing-options) vonatkozó összes ajánlati követelményt, amelyet az [SaaS-ajánlat létrehozása a kereskedelmi piactéren](../create-new-saas-offer.md)című rész tartalmazza.
- Integrálhatja a [SaaS-megvalósítási API-kat](./pc-saas-fulfillment-api-v2.md) az ügyfeleknek az ajánlat kiépítéséhez és az azokhoz való kapcsolódáshoz.  
- Az **átalánydíjas** díjszabási modellhez konfigurálható, ha a szolgáltatás ügyfeleinek töltése történik.  A méretek az átalánydíjas árképzési modell opcionális kiterjesztése. 

Ezután az SaaS-ajánlat integrálható a [kereskedelmi Marketplace-mérési szolgáltatás API](./marketplace-metering-service-apis.md) -kkal, hogy tájékoztassa a Microsoftot a számlázható eseményekről.

>[!Note]
>A piactér-mérési szolgáltatás csak az átalány számlázási modellben érhető el, és nem vonatkozik a felhasználónkénti számlázási modellre.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Hogyan illeszkedik a mért számlázás a díjszabáshoz

Az ajánlati hierarchia megismerése fontos, amikor az ajánlatot az árképzési modelljeivel együtt határozza meg.

- Minden SaaS-ajánlat úgy van konfigurálva, hogy a Microsofton keresztül értékesítsen vagy sem.  Az ajánlat közzétételekor ez a lehetőség nem módosítható.
- A Microsofton keresztüli értékesítésre konfigurált SaaS-ajánlatok egy vagy több csomaggal rendelkezhetnek.  A felhasználók a SaaS-ajánlatra fizetnek, de a Microsoft a csomag keretében vásárolható meg.
- Minden csomaghoz hozzá van rendelve egy díjszabási modell: **átalánydíjas** vagy **felhasználónként**. Az ajánlat minden csomagját ugyanahhoz az árképzési modellhez kell társítani. Nem lehet például olyan ajánlat, amely egy átalánydíjas díjszabási modellre vonatkozó tervekkel rendelkezik, és egy másik a felhasználónkénti díjszabási modell.
- Az átalánydíjas számlázási modellhez konfigurált minden egyes csomagon belül legalább egy ismétlődő díj szerepel (amely $0):
    - Ismétlődő **havi** díj: a csomag megvásárlásakor a rendszer havi ismétlődéssel előre fizetett átalányösszegű havi díjat számít fel.
    - Ismétlődő **éves** díj: a csomag megvásárlásakor a felhasználó éves ismétlődéssel előre fizetett átalányösszegű éves díj.
- Az ismétlődő díjak mellett egy átalánydíjas csomag is tartalmazhat opcionális egyéni dimenziókat, amelyek az átalányban nem szereplő túlhasználati felhasználásért használják fel az ügyfeleket.  Minden dimenzió egy számlázható egységet képvisel, amelyet a szolgáltatás a [kereskedelmi Marketplace mérési szolgáltatás API](./marketplace-metering-service-apis.md)-val kommunikál a Microsofttal.

## <a name="sample-offer"></a>Minta ajánlat

A contoso például egy contoso Notification Services (CNS) nevű SaaS-szolgáltatással rendelkező közzétevő. A CNS lehetővé teszi, hogy az ügyfelek e-mailben vagy szöveges üzenetben küldjenek értesítéseket. A contoso regisztrálva van a partner Centerben, a kereskedelmi piactér program számára, amely SaaS-ajánlatokat tesz közzé az Azure-ügyfeleknek.  A következő két terv van társítva a CNS-hez:

- Alapszintű csomag
    - 10000-os e-mailek és 1000-es szövegek küldése 0/hónapra (havi díj)
    - Az 10000-es e-mailek után fizessen $1 minden egyes 100 e-mailhez
    - Az 1000-es szövegeken kívül minden szövegért fizessen $0,02

    [![Alapszintű csomag díjszabása](./media/saas-basic-pricing.png "Kattintson a bővített nézetre")](./media/saas-basic-pricing.png)

- Prémium szintű csomag
    - 50000-os e-mailek és 10000-es szövegek küldése a $350/hónap vagy 5M e-mailek és 1M-$3500 szövegek számára évente
    - Az 50000-es e-mailek után fizessen $0,5 minden egyes 100 e-mailhez
    - Az 10000-es szövegeken kívül minden szövegért fizessen $0,01

    [![Prémium csomag díjszabása](./media/saas-premium-pricing.png "Kattintson a bővített nézetre")](./media/saas-premium-pricing.png)

- Nagyvállalati csomag
    - Korlátlan számú e-mail-cím és 50000-es szöveg küldése 400-as hónapra
    - Az 50000-es szövegeken kívül minden txt esetében $0,005-et fizetünk

    [![A nagyvállalati csomag díjszabása](./media/saas-enterprise-pricing.png "Kattintson a bővített nézetre")](./media/saas-enterprise-pricing.png)

A kiválasztott csomag alapján egy Azure Customer-beli ügyfél-vásárlási előfizetés a CNS SaaS-ajánlathoz elküldheti az előfizetési időszakba foglalt szöveges és e-mailek mennyiségét (hónap vagy év, ahogy az előfizetés részletei – startDate és endDate).  A contoso a használati események a Microsoftnak való elküldése nélkül számítja fel a használatot a benne foglalt mennyiségre. Ha az ügyfelek a benne foglalt mennyiségnél többet használnak, nem kell megváltoztatniuk a csomagokat, vagy bármi mást elvégezniük.  A contoso a befoglalt mennyiségen felül méri a túlterhelést, és megkezdi a használati események kibocsátását a Microsoftnak a túlhasználati [forgalomnak a kereskedelmi Marketplace-mérési szolgáltatás API](./marketplace-metering-service-apis.md)-val történő kitöltéséhez.  A Microsoft ekkor felszámítja az ügyfelet az egyéni dimenziókban a közzétevő által meghatározott túlhasználati felhasználásra. A kereten túli számlázás a következő elszámolási időszakban történik (havonta, de negyedéves vagy korai lehet egyes ügyfelek esetében).  A havi átalánydíjas csomag esetében a túlhasználati számlázás minden olyan hónapban történik, ahol a túlhasználatot felhasználták.  Egy éves átalánydíjas csomag esetében, ha az alap/év összegben foglalt mennyiséget felhasználjuk, az egyéni fogyasztásmérő által kibocsátott minden további használatot a számlázási ciklusban (havonta), az előfizetés éves időszakának végéig kell kiszámlázni.

## <a name="billing-dimensions"></a>Számlázási méretek

Mindegyik számlázási dimenzió egy egyéni egységet határoz meg, amely alapján az ISV használati eseményeket bocsáthat ki.  A számlázási dimenziókat arra is felhasználjuk, hogy az ügyfélnek a szoftver használatáért való számlázása során is kommunikáljanak.  Ezek az alábbiak szerint vannak meghatározva:

- **Azonosító**: a használati események kibocsátásakor a nem módosítható dimenzió-azonosító.
- **Megjelenítendő név**: a dimenzióhoz társított megjelenítendő név, például "szöveges üzenetek elküldve".
- **Mértékegység**: a számlázási egység leírása, például "szöveges üzenet" vagy "/100 e-mailek".
- **Egységenkénti díj USD-ben**: a dimenzió egy egységének ára.  Ez lehet 0. 
- **Havi mennyiség az alapban**: az ismétlődő havi díjat fizető ügyfelek számára havi dimenzió mennyisége, egész számnak kell lennie. Ez lehet 0 vagy korlátlan.
- Az **alapszintű mennyiség** a következőben: az éves díj összegét kifizető ügyfelek számára minden évben felszámított dimenzió mennyisége egész számnak kell lennie. 0 vagy korlátlan lehet.

A számlázási dimenziók az ajánlat minden csomagjában meg vannak osztva.  Egyes attribútumok a dimenzióra vonatkoznak az összes csomagra, és más attribútumok is megtervezve.

A dimenziót definiáló attribútumok az ajánlat minden csomagjában meg vannak osztva.  Mielőtt közzéteszi az ajánlatot, az ezen attribútumok bármely csomag kontextusában történt módosítása hatással lesz az összes csomag dimenziójának meghatározására.  Miután közzétette az ajánlatot, ezek az attribútumok többé nem lesznek szerkeszthetve.  Ezek az attribútumok a következők:

- ID (Azonosító)
- Megjelenítendő név
- Mértékegység

A dimenziók egyéb attribútumai az egyes csomagokra jellemzőek, és a tervtől eltérő értékekkel rendelkezhetnek.  A terv közzététele előtt módosíthatja ezeket az értékeket, és csak ezt a csomagot fogja érinteni.  Miután közzétette a csomagot, ezek az attribútumok többé nem lesznek szerkeszthetve.  Ezek az attribútumok a következők:

- Egységenkénti díj USD-ben
- Az alapban foglalt havi mennyiség  
- A baseIncluded szereplő éves mennyiség  

A méretek két speciális fogalmat is tartalmazhatnak, amelyek "engedélyezve" és "végtelen":

- Az **engedélyezve** érték azt jelzi, hogy ez a csomag részt vesz ebben a dimenzióban.  Ha olyan új csomagot hoz létre, amely nem küldi el a használati eseményeket ezen dimenzió alapján, akkor ez a beállítás nincs bejelölve.  Emellett a csomag első közzététele után hozzáadott új dimenziók a már közzétett tervben "nem engedélyezettként" jelennek meg.  A kikapcsolt dimenziók nem jelennek meg az ügyfelek által látott csomag dimenzióinak listájában.
- A "∞" végtelen szimbólum által képviselt **végtelen** érték azt jelzi, hogy ez a csomag részt vesz ebben a dimenzióban, de nem bocsátja ki a használatot ezen a dimenzión.  Ha meg szeretné jeleníteni ügyfelei számára, hogy a dimenzió által képviselt funkciók beletartoznak a csomagba, de a használat korlátozás nélkül.  A végtelen használatú dimenziók az ügyfelek által látott csomag dimenzióinak listájában jelennek meg, ami azt jelzi, hogy a csomagnak soha nem kell díjat fizetnie.

>[!Note] 
>A következő forgatókönyvek explicit módon támogatottak: <br> – Új dimenziót adhat hozzá egy új tervhez.  Az új dimenzió nem lesz engedélyezve a már közzétett csomagok esetében. <br> – Bármilyen dimenzió nélkül közzétehet egy **átalánydíjas** csomagot, majd hozzáadhat egy új tervet, és konfigurálhat egy új dimenziót az adott tervhez. Az új dimenzió nem lesz engedélyezve a már közzétett tervekhez.

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>Dimenzió árának beállítása a támogatott piacokon

A átalánydíjas díjszabáshoz hasonlóan a számlázási dimenzió árai is megadhatók egy támogatott országban vagy régióban. A következő módon kell használnia az árképzési adatimportálás és exportálás funkciót a partner Centerben.

1. Határozza meg a kívánt dimenziókat, és adja meg, hogy mely piacok támogatottak. 
1. Exportálja ezeket az adatfájlokat egy fájlba.
1. Adja hozzá a megfelelő árakat országonként/régiónként, és importálja a fájlt a partner Centerben.

A mérő felhasználói felülete úgy változik, hogy tükrözze, hogy a dimenzió árai csak a fájlban láthatók.

[![kereskedelmi piactér-mérési szolgáltatás méretei](media/metering-service-dimensions.png "Kattintson a bővített nézetre")](media/metering-service-dimensions.png)

### <a name="private-plan"></a>Saját csomag

A átalánydíjas csomagokhoz hasonlóan a dimenziókat tartalmazó csomagok is megadhatók privát csomagként, csak a csomag meghatározott célközönsége számára.

## <a name="constraints"></a>Korlátozások

### <a name="trial-behavior"></a>Próbaverziós viselkedés

A kereskedelmi Piactéri mérési szolgáltatással mért számlázás nem kompatibilis az ingyenes próbaverzióval.  Nem lehet beállítani egy tervet a mért számlázás és az ingyenes próbaverzió használatára.

### <a name="locking-behavior"></a>Zárolási viselkedés

Mivel a kereskedelmi Marketplace-mérési szolgáltatással használt dimenzió azt mutatja be, hogy az ügyfél hogyan fizet a szolgáltatásért, a dimenziók összes részletét már nem szerkesztheti a közzététel után.  A közzététel előtt fontos, hogy a méretek teljes mértékben meg legyenek határozva a tervekhez.

Ha egy ajánlatot közzétesznek egy dimenzióval, az adott dimenzióra vonatkozó ajánlati szintű adatok már nem módosíthatók:

- ID (Azonosító)
- Megjelenítendő név
- Mértékegység

A csomag közzététele után a terv szintű részletek már nem módosíthatók:

- Egységenkénti díj USD-ben
- Az alapban foglalt havi mennyiség
- Az alapban foglalt éves mennyiség
- Azt jelzi, hogy a dimenzió engedélyezve van-e a csomaghoz

### <a name="upper-limits"></a>Felső határértékek

Az egyetlen ajánlathoz konfigurálható méretek maximális száma 30 egyedi dimenzió lehet.

## <a name="get-support"></a>Támogatás kérése

Ha az alábbi problémák valamelyikével rendelkezik, nyisson meg egy támogatási jegyet.

- Technikai problémák a Marketplace-mérési szolgáltatás API-jával.
- Egy olyan problémát, amelyet az oldal hibája vagy hibája miatt meg kell adni (pl. helytelen használati esemény).
- A mért számlázással kapcsolatos egyéb problémák.

A kiadói támogatási lehetőségek megismeréséhez és a Microsoft támogatási jegyének megnyitásához kövesse a következő témakör utasításait: a [partner Center kereskedelmi Marketplace programjának támogatása](../support.md).

## <a name="next-steps"></a>Következő lépések

- [Marketplace metering service API-k](./marketplace-metering-service-apis.md)
