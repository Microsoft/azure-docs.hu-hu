---
title: Felügyelt szolgáltatási ajánlat megtervezése a Microsoft kereskedelmi piactérről
description: Új felügyelt szolgáltatás ajánlatának megtervezése az Azure Marketplace-en a kereskedelmi piactér program használatával a Microsoft partner Centerben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: f096e53f8054039f361bde1c5f2adffac615c53d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371940"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>Felügyelt szolgáltatási ajánlat tervezése a Microsoft kereskedelmi piactéren

Ez a cikk bemutatja a felügyelt szolgáltatás ajánlatának a Microsoft kereskedelmi piactéren keresztüli közzétételének követelményeit a partner Center használatával.

A felügyelt szolgáltatások olyan Azure Marketplace-ajánlatok, amelyek lehetővé teszik a több-bérlős és a több-bérlős felügyeletet az Azure Lighthouse segítségével. További információ: [Mi az az Azure Lighthouse?](../lighthouse/overview.md) Ha egy ügyfél felügyelt szolgáltatási ajánlatot vásárol, egy vagy több előfizetést vagy erőforráscsoportot delegálhat. Ezeket az erőforrásokat az Azure világítótorony [Azure által delegált erőforrás-kezelési](../lighthouse/concepts/azure-delegated-resource-management.md) képességeinek használatával végezheti el.

## <a name="eligibility-requirements"></a>Jogosultsági követelmények

A felügyelt szolgáltatások ajánlatának közzétételéhez a Microsoft a Cloud platformon szerzett Gold vagy Silver Microsoft kompetenciával kell rendelkeznie. Ez a kompetencia mutatja be szakértelmét az ügyfeleknek. További információ: [Microsoft Partner Network kompetenciák](https://partner.microsoft.com/membership/competencies).

Az ajánlatoknak meg kell felelniük az Azure Marketplace-en közzétenni kívánt [kereskedelmi Piactéri minősítési szabályzatoknak](/legal/marketplace/certification-policies) .

## <a name="customer-leads"></a>Ügyfél-érdeklődők

Az ügyfelek adatainak összegyűjtéséhez az ajánlatot az Ügyfélkapcsolat-kezelési (CRM) rendszerhez kell kötni. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és online áruházát, ahol az ajánlatot megtalálták, a rendszer a konfigurált CRM-rendszerbe küldi el. A kereskedelmi piactér különböző típusú CRM-rendszereket támogat, valamint lehetővé teszi az Azure-táblák használatát, vagy egy HTTPS-végpont konfigurálását a Power automatizálás használatával.

A CRM-kapcsolatok bármikor hozzáadhatók vagy módosíthatók az ajánlat létrehozásakor vagy azt követően is. Részletes útmutatásért tekintse [meg a kereskedelmi Marketplace-ajánlat ügyfeleinek vezetőit](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Jogi szerződések

A partner Center Tulajdonságok lapján megkéri, hogy adja meg az ajánlat használati **feltételeit** . Megadhatja a feltételeket közvetlenül a partner Centerben, vagy megadhatja a megtalált URL-címet. Az ajánlat megvásárlása előtt az ügyfeleknek el kell fogadniuk a feltételeket és a kikötéseket.

## <a name="offer-listing-details"></a>Ajánlat részletei

Amikor létrehoz egy felügyelt szolgáltatási ajánlatot a partner Centerben, szövegeket, képeket, dokumentumokat és egyéb ajánlatokat kell megadnia. Az ügyfelek ezt fogják látni, amikor az Azure Marketplace-en észlelik az ajánlatot. Lásd a következő példát:

:::image type="content" source="media/example-managed-service.png" alt-text="Bemutatja, hogyan jelenik meg a felügyelt szolgáltatás ajánlata az Azure piactéren.":::

**Lehívási leírások**

1. Embléma
1. Name
1. Rövid leírás
1. Kategóriák
1. Jogi szerződések és adatvédelmi szabályzat
1. Description
1. Képernyőképek/videók
1. Hasznos hivatkozások

Az alábbi példa bemutatja, hogyan jelenik meg az ajánlati lista a Azure Portalban:

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="Bemutatja, hogyan jelennek meg az ajánlat a Azure Portalban.":::

**Lehívási leírások**

1. Név
2. Leírás
3. Hasznos hivatkozások
4. Képernyőképek/videók

> [!NOTE]
> Ha az ajánlat az angoltól eltérő nyelven érhető el, az ajánlati lista ezen a nyelven érhető el, de a leírásnak a következő angol kifejezéssel kell kezdődnie vagy végződnie: "Ez a szolgáltatás az &lt; ajánlat tartalma> nyelvén elérhető. A támogatási dokumentumokat olyan nyelven is megadhatja, amely különbözik az ajánlati lista részleteiben használt nyelvtől.

Ha könnyebben létre szeretné hozni az ajánlatot, készítse elő ezeket az elemeket az idő előtt. Ha másként nincs jelezve, a következő elemek szükségesek.

**Name (név**): ez az ajánlatnak a kereskedelmi piactéren megjelenő címével fog megjelenni. A név lehet védjeggyel ellátott. Nem tartalmazhat hangulatjelek (kivéve, ha a védjegyek és a szerzői jogi szimbólumok), és legfeljebb 50 karakter hosszúnak kell lennie.

**Keresési eredmények összegzése**: az ajánlat célját vagy célját 100 vagy kevesebb karakterből írja le. Ezt az összegzést a kereskedelmi piactéren a keresési eredmények listájában lehet használni. Nem lehet azonos a címmel. Érdemes megfontolni a legnépszerűbb SEO-kulcsszavakat is.

**Rövid leírás**: adja meg az ajánlat rövid leírását (legfeljebb 256 karakter). A Azure Portalban megjelenik az ajánlati listán.

**Leírás**: az ajánlat leírása 3 000 vagy kevesebb karakterből áll. Ez a leírás a kereskedelmi piactéren való listázásban jelenik meg. Vegye figyelembe az érték kiosztását, a kulcsfontosságú előnyöket, a kategória-vagy iparági társításokat, valamint a szükséges információkat.

Íme néhány tipp a Leírás írásához:

* Egyértelműen ismertesse az ajánlat értékét az első néhány mondatban, beleértve a következőket:
    * Az ajánlat által előnyben részesülő felhasználó típusa.
    * Az ügyfelek igényeit vagy problémáit az ajánlat címével.
* Ne feledje, hogy az első néhány mondat a keresési eredmények között jelenhet meg.
* Iparág-specifikus szókincs használata.

A leírást a HTML-címkék használatával formázhatja. További információ a HTML-formázásról: [a kereskedelmi piactér ajánlatának leírásában támogatott HTML-címkék](./supported-html-tags.md).

**Adatvédelmi szabályzat hivatkozása**: adjon meg egy URL-címet a webhelyén tárolt Adatvédelmi Szabályzathoz. Ön felelős azért, hogy az ajánlat megfelel az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson.

**Hasznos hivatkozások** (nem kötelező): kiegészítő online dokumentumokat tölthet fel az ajánlatával kapcsolatban.

**Kapcsolattartási adatok**: adja meg a vállalaton belüli két személy nevét, e-mail-címét és telefonszámát (Ön lehet az egyik): támogatási kapcsolattartó és mérnöki kapcsolattartó. Ezt az információt fogjuk használni az ajánlattal való kommunikációhoz. Ezek az információk nem jelennek meg az ügyfelek számára, de a Cloud Solution Provider (CSP) partnerei számára is elérhetők

**Támogatási URL-címek** (nem kötelező): Ha az Azure-beli globális ügyfelek és/vagy Azure Government ügyfelek számára is támogatja a webhelyeket, adja meg ezeket az URL-címeket.

**Marketplace Media – logók**: adjon meg egy PNG-fájlt az ajánlat nagy méretű emblémája számára. A partner Center közepes és kis logók létrehozására fogja használni. Az emblémákat később is lecserélheti egy másik képpel.

* A nagyméretű embléma (216 x 216 – 350 x 350 px) megjelenik az ajánlaton az Azure Marketplace-en.
* A közepes embléma (90 x 90 px) új erőforrás létrehozásakor jelenik meg.
* A kis embléma (48 x 48 px) az Azure piactér keresési eredményeiben használható.

Kövesse az alábbi irányelveket a logókhoz:

* Győződjön meg arról, hogy a kép nincs kifeszítve.
* Az Azure arculata egyszerű színpalettát használ. Törekedjen minél kevesebb alap- és másodlagos szín használatára az emblémában.
* A Azure Portal színe fehér és fekete. Ezeket ne használja az embléma hátterének. Olyan egyszerű elsődleges színeket ajánlunk, amelyek kiemelten teszik a logót.
* Ha átlátszó hátteret használ, az embléma és a szöveg ne legyen fehér, fekete vagy kék.
* Az embléma megjelenésének és működésének egyszerűnek kell lennie. Kerülje a színátmeneteket. Ne helyezzen el szöveget az emblémára, még a vállalat vagy a márka nevét se.

**Piactéri média – képernyőképek** (nem kötelező): legfeljebb öt képet adhat meg, amely bemutatja, hogyan működik az ajánlat. Az összes képnek 1280 x 720 képpont méretűnek és a-ben kell lennie. PNG-formátum.

**Piactéri média – videók** (nem kötelező): legfeljebb öt videót tölthet fel, amelyek bemutatják az ajánlatát. A videókat a YouTube vagy a Vimeo szolgáltatásban kell tárolni, és egy miniatűrnek kell lennie (1280 x 720 PNG-fájl).

## <a name="preview-audience"></a>Előnézet célközönsége

Az előzetes verzió célközönsége az Azure piactéren való közzététel előtt férhet hozzá az ajánlathoz. A partner Center előzetes verziójának **célközönség** lapján megadhat egy korlátozott előzetes verziót.

> [!NOTE]
> Az előzetes verzió célközönsége különbözik egy privát csomagtól. Egy privát csomag csak a kiválasztott célközönség számára érhető el. Ez lehetővé teszi, hogy egy egyéni csomagot adott ügyfelekkel egyeztesse.

Meghívhat a Microsoft-fiók (MSA) vagy a Azure Active Directory (Azure AD) e-mail-címére. Legfeljebb 10 e-mail-címet adjon meg manuálisan, vagy importáljon akár 20-at egy. CSV-fájllal. Ha az ajánlata már élő, akkor is megadhatja az előnézeti közönséget az ajánlat változásainak és frissítéseinek teszteléséhez.

## <a name="plans-and-pricing"></a>Csomagok és díjszabás

A felügyelt szolgáltatáshoz legalább egy csomag szükséges. A terv meghatározza a megoldás hatókörét, a korlátozásokat és a kapcsolódó díjszabást, ha van ilyen. Az ajánlathoz több csomagot is létrehozhat, így különböző technikai és díjszabási lehetőségeket biztosíthat ügyfeleinek. A csomagokkal kapcsolatos általános útmutatásért, beleértve a saját terveket is, lásd: [a kereskedelmi piactéren elérhető csomagok és díjszabások](plans-pricing.md).

A felügyelt szolgáltatások csak egy díjszabási modellt támogatnak: **saját licenc használata (BYOL)**. Ez azt jelenti, hogy közvetlenül számlázza az ügyfeleit, és a Microsoft nem számít fel díjat.

## <a name="next-steps"></a>Következő lépések

* [Felügyelt szolgáltatásra vonatkozó ajánlat létrehozása](./create-managed-service-offer.md)
* [Ajánlatlistákra vonatkozó ajánlott eljárások](./gtm-offer-listing-best-practices.md)