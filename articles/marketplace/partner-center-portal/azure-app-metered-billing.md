---
title: A felügyelt alkalmazások díjas számlázása a piactér-mérési szolgáltatás használatával | Azure piactér
description: Ez a dokumentáció az Azure-alkalmazások rugalmas számlázási modellekkel való közzétételéhez nyújt útmutatást.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: d015cec30e516541b50c2acfac38fad898965e1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436351"
---
# <a name="managed-application-metered-billing"></a>Felügyelt alkalmazás mért számlázása 

A Marketplace mérési szolgáltatással olyan felügyelt alkalmazáscsomag hozhatók létre az Azure-alkalmazásokhoz, amelyek a nem standard egységek szerint vannak felszámítva. Az ajánlat közzététele előtt meg kell határoznia a számlázási dimenziókat, például a sávszélességet, a jegyeket vagy a feldolgozott e-maileket. Az ügyfelek ezt a dimenziók fogyasztása alapján fizetik ki.  A rendszer tájékoztatni fogja a Microsoftot a kiszámlázható események Piactéri mérési szolgáltatásának API-jával.

## <a name="prerequisites-for-metered-billing"></a>A mért számlázás előfeltételei

Ahhoz, hogy egy felügyelt alkalmazás megtervezze a mért számlázást, a következőket kell tennie:

* Teljesítse az összes ajánlatra vonatkozó követelményt az [Azure-alkalmazás létrehozása ajánlatban](../create-new-azure-apps-offer.md)ismertetett módon.
* Konfigurálja a **díjszabást** az ügyfelek számára a szolgáltatás havi díja alapján. A díj nulla lehet, ha nem szeretne rögzített díjat fizetni, hanem teljes mértékben mért számlázásra támaszkodik.
* Adja meg a **Számlázási dimenziókat** azon mérési eseményeknél, amelyeket az ügyfél az átalány alapján fog fizetni.
* Integrálja a [piactér-mérési szolgáltatás API](./marketplace-metering-service-apis.md) -kkal, hogy tájékoztassa a Microsoftot a számlázható eseményekről.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Hogyan illeszkedik a mért számlázás a díjszabáshoz

Ha az ajánlatot az árképzési modelljeivel együtt határozza meg, fontos megérteni az ajánlat-hierarchiát.

* Minden Azure-alkalmazáshoz megoldási sablon vagy felügyelt alkalmazáscsomag tartozhat.
* A mért számlázás csak a felügyelt alkalmazási csomagokkal valósítható meg.
* Minden felügyelt alkalmazási csomaghoz tartozik egy díjszabási modell. 
* A díjszabási modell havi ismétlődő díjat tartalmaz, amely $0-ra állítható be.
* Az ismétlődő díj mellett a terv olyan választható dimenziókat is tartalmazhat, amelyeket a rendszer az átalányban nem szereplő használati díjak megadására használ fel. Az egyes dimenziók egy számlázható egységet jelölnek, amelyet a szolgáltatás kommunikál a Microsofttal a [Marketplace-mérési szolgáltatás API-ja](marketplace-metering-service-apis.md)használatával.

## <a name="sample-offer"></a>Minta ajánlat

A contoso például egy contoso Analytics (CoA) nevű felügyelt alkalmazási szolgáltatás közzétevője. A CoA lehetővé teszi, hogy az ügyfelek nagy mennyiségű adattárházat elemezzenek a jelentéskészítés és az adatraktározás során. A contoso közzétevőként van regisztrálva az Azure-ügyfelek számára elérhető kereskedelmi piactér program keretében. A következő két csomag van társítva a CoA-hoz:

* Alapcsomag
    * 100 GB elemzése és 100-jelentések készítése $0/hónapra
    * Az 100 GB-nál nagyobb mennyiségű $10 minden 1 GB-os
    * Az 100-jelentéseken kívül minden jelentés esetében fizessen a $1
* Prémium szintű csomag
    * Az 1000 GB elemzése és a 1000-jelentések előállítása 350/hó-ig
    * A 1000 GB-nál újabb kiadásban fizessen a $100 1 TB-nál
    * Az 1000-jelentéseken kívül minden jelentés esetében fizessen a $0,5

Egy Azure-ügyfél, amely a CoA szolgáltatásra előfizető, a kiválasztott csomag alapján havonta elemezheti és jelentéseket készíthet. A contoso a használati események a Microsoftnak való elküldése nélkül méri a használatot a befoglalt mennyiséggel. Ha az ügyfelek a benne foglalt mennyiségnél többet használnak, nem kell megváltoztatniuk a csomagokat, vagy bármi mást elvégezniük. A contoso a befoglalt mennyiségen felül méri a túlterhelést, és megkezdi a használati események kibocsátását a Microsoftnak a [Piactéri mérési szolgáltatás API](./marketplace-metering-service-apis.md)használatával történő további használat érdekében. A Microsoft ezt követően a kiadó által megadott további használatért díjat számít fel.

## <a name="billing-dimensions"></a>Számlázási méretek

A számlázási dimenziók használatával kommunikálnak a felhasználóval arról, hogy hogyan lesznek kiszámlázva a szoftver használatáért.  Ezeket a dimenziókat a használati események a Microsoftnak való kommunikációjához is használják. Ezek az alábbiak szerint vannak meghatározva:

* **Dimenzió azonosítója**: a használati események kibocsátása közben megváltoztathatatlan azonosító.
* **Dimenzió neve**: a dimenzióhoz társított megjelenítendő név, például "szöveges üzenetek elküldve".
* **Mértékegység**: a számlázási egység leírása, például "szöveges üzenet" vagy "/100 e-mailek".
* **Díj/egység**: a dimenzió egy egységének díja.
* **Belefoglalt mennyiség a havi időszakra**: az ismétlődő havi díjat fizető ügyfelek számára havi dimenzióban szereplő mennyiségnek egész számnak kell lennie.

A számlázási dimenziók az ajánlat minden csomagjában meg vannak osztva. Egyes attribútumok a dimenzióra vonatkoznak az összes csomagra, és más attribútumok is megtervezve.

A dimenziót definiáló attribútumok az ajánlat minden csomagjában meg vannak osztva. Mielőtt közzéteszi az ajánlatot, az ezen attribútumok bármely csomag kontextusában történt módosítása hatással lesz az összes csomag dimenziójának meghatározására. Miután közzétette az ajánlatot, ezek az attribútumok többé nem lesznek szerkeszthetve. Az attribútumok a következők:

* Azonosító
* Name
* Mértékegység

A dimenziók egyéb attribútumai az egyes csomagokra jellemzőek, és a tervtől eltérő értékekkel rendelkezhetnek.  A terv közzététele előtt módosíthatja ezeket az értékeket, és csak ezt a csomagot fogja érinteni. Miután közzétette a csomagot, ezek az attribútumok többé nem lesznek szerkeszthetve. Az attribútumok a következők:

* Egységár
* A havi ügyfelek belefoglalt mennyisége 
* Befoglalt mennyiség az éves ügyfelek számára 

A méretek két speciális fogalmat is tartalmazhatnak, amelyek "engedélyezve" és "végtelen":

* Az **engedélyezve** érték azt jelzi, hogy ez a csomag részt vesz ebben a dimenzióban.  Ha olyan új csomagot hoz létre, amely nem küldi el ezt a dimenziót, előfordulhat, hogy ezt a beállítást nem kell kijelölnie. Emellett a csomag első közzététele után hozzáadott új dimenziók a már közzétett tervben "nem engedélyezettként" jelennek meg.  A kikapcsolt dimenziók nem jelennek meg az ügyfelek által látott csomag dimenzióinak listájában.
* A **végtelen**, amelyet a "∞" végtelen szimbólum jelöl, azt jelzi, hogy ez a csomag részt vesz ebben a dimenzióban, a dimenzióra vonatkozó mért használat nélkül. Ha meg szeretné jeleníteni ügyfelei számára, hogy a dimenzió által képviselt funkciók beletartoznak a csomagba, de a használat korlátozás nélkül.  A végtelen használatú dimenziók az ügyfelek által látott csomag dimenzióinak listájában jelennek meg.  Ez a csomag soha nem számít fel díjat.

>[!Note] 
>A következő forgatókönyvek explicit módon támogatottak:  <br> – Új dimenziót adhat hozzá egy új tervhez.  Az új dimenzió nem lesz engedélyezve a már közzétett csomagok esetében. <br> – A csomagot rögzített havi díjjal és dimenziók nélkül is közzéteheti, majd hozzáadhat egy új tervet, és konfigurálhat egy új dimenziót az adott tervhez. Az új dimenzió nem lesz engedélyezve a már közzétett tervekhez.

## <a name="constraints"></a>Korlátozások

### <a name="locking-behavior"></a>Zárolási viselkedés

A Marketplace-mérési szolgáltatással használt dimenzió azt mutatja be, hogy az ügyfél hogyan fog fizetni a szolgáltatásért.  Egy adott dimenzió összes adatát már nem szerkesztheti az ajánlat közzétételekor.  Az ajánlat közzététele előtt fontos, hogy a dimenziók teljes mértékben meg legyenek határozva.

Ha egy ajánlatot közzétesznek egy dimenzióval, az adott dimenzióra vonatkozó ajánlati szintű adatok már nem módosíthatók:

* Azonosító
* Name
* Mértékegység

A csomag közzététele után a terv szintű részletek már nem módosíthatók:

* Egységár
* Belefoglalt mennyiség a havi időszakra
* Azt határozza meg, hogy a dimenzió engedélyezve van-e a csomaghoz

>[!Note]
>A piactér-mérési szolgáltatással mért számlázás a Azure Government felhőben még nem támogatott.

### <a name="upper-limits"></a>Felső határértékek

Az egyetlen ajánlathoz konfigurálható méretek maximális száma 30 egyedi dimenzió lehet.

## <a name="get-support"></a>Támogatás kérése

Ha az alábbi problémák valamelyikével rendelkezik, nyisson meg egy támogatási jegyet.

* Technikai problémák a Marketplace-mérési szolgáltatás API-jával.
* Egy olyan problémát, amelyet az oldal hibája vagy hibája miatt meg kell adni (pl. helytelen használati esemény).
* A mért számlázással kapcsolatos egyéb problémák.

Kövesse a [partner Center kereskedelmi piactér programjának támogatását](../support.md) ismertető témakör útmutatását a kiadói támogatási lehetőségek megismeréséhez és a Microsoft támogatási jegyének megnyitásához.

## <a name="next-steps"></a>Következő lépések

- További információért lásd: [Marketplace-mérési szolgáltatás API](./marketplace-metering-service-apis.md) -k.
