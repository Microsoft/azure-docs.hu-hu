---
title: Dynamics 365-ajánlatok Microsoft AppSource
description: Dynamics 365-ajánlatok Microsoft AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/16/2021
ms.openlocfilehash: c5746b70f84ff68ea578ba141cc5251cc5ce07ea
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819378"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>Microsoft Dynamics 365-ajánlat megterve

Ez a cikk a Dynamics 365-ajánlat különböző lehetőségeit és funkcióit ismerteti Microsoft AppSource kereskedelmi piactéren. Az AppSource olyan ajánlatokat tartalmaz, amelyek a Microsoft 365, a Dynamics 365, a PowerApps és a Power BI. Az AppSource lehetővé teszi a fizetős (*Get It Now*), list ( Contact *Me*) és a trial *(Kipróbálás* most) ajánlatokat.

Mielőtt elkezdené, hozzon létre [](./partner-center-portal/create-account.md) egy kereskedelmi piactéri fiókot a Partnerközpont, és győződjön meg arról, hogy az regisztrálva van a kereskedelmi piactér programjában. Tekintse át a [közzétételi folyamatot és az irányelveket is.](/office/dev/store/submit-to-appsource-via-partner-center)

## <a name="licensing-options"></a>Licencelési lehetőségek

Az új ajánlat közzétételének előkészítéseként el kell döntenie, hogy melyik licencelési lehetőséget válassza. Ez határozza meg, hogy milyen további információkat kell majd a későbbiekben szolgáltatni az ajánlat létrehozásához a Partnerközpont.

A Dynamics 365-ajánlatokhoz a következő licencelési lehetőségek érhetők el:

| Licencelési lehetőség | Tranzakciós folyamat |
| --- | --- |
| Szerezze be most (ingyenes) | Ajánlata ingyenesen elérhető az ügyfelek számára. |
| Ingyenes próbaverzió (termékoldal) | Egy, három vagy hat hónapos ingyenes próbaverziót kínálhat ügyfeleinek. Az ajánlatlista ingyenes próbaverzióit a szolgáltatása hozta létre, kezeli és konfigurálta, és nem rendelkezik a Microsoft által kezelt előfizetésekkel. |
| Kapcsolatfelvétel | Gyűjtse össze az ügyfél kapcsolattartási adatait az Ügyfélkapcsolat-kezelő (CRM) rendszer csatlakoztatásával. A rendszer engedélyt kér az ügyféltől az adataik megosztására. Ezeket az ügyféladatokat, valamint az ajánlat nevét, azonosítóját és az ajánlatot megtaláló Marketplace-forrást a konfigurált CRM-rendszernek küldi el a rendszer. A CRM konfigurálásával kapcsolatos további  információkért tekintse meg az ajánlattípus Ajánlatbeállítási oldalának Ügyfél érdeklődők **szakaszát.** |
|

## <a name="test-drive"></a>Teszt meghajtó

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>Érdeklődők az ügyfelek számára

Amikor ajánlatot ad közzé a kereskedelmi piactéren az Partnerközpont, csatlakoztatnia kell azt az Ügyfélkapcsolat-kezelési (CRM) rendszerhez. Ez lehetővé teszi, hogy azonnal megkapja az ügyfél kapcsolattartási adatait, amint valaki érdeklődik a termék iránt, vagy használja azt. Ha engedélyezni szeretne egy teszt meghajtót, a CRM-hez való csatlakozásra van szükség; ellenkező esetben a CRM-hez való csatlakozás nem kötelező. Partnerközpont támogatja az Azure Table, a Dynamics 365 Customer Engagement, a HTTPS-végpont, a Marketo és a Salesforce használatát.

## <a name="legal"></a>Jogi tudnivalók

Hozza létre a **használati feltételeket.** Az ügyfeleknek el kell fogadniuk ezeket, mielőtt kipróbálhatják az ajánlatot. A Microsoft rendelkezik egy standard szerződéssel, de nem vonatkozik a Dynamics 365-ajánlatokra.

## <a name="offer-listing-details"></a>Ajánlat termékoldalának részletei

Íme egy példa arra, hogyan jelennek meg az ajánlatinformációk a Microsoft AppSource (a felsorolt árak csak példaként szolgálnak, és nem tükrözik a tényleges költségeket):

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="Bemutatja, hogyan jelenik meg ez az ajánlat Microsoft AppSource.":::

> [!NOTE]
> Az ajánlatlista tartalmának nem kell angol nyelvűnek lennie, ha az ajánlat leírása a "This application is available only in [non-english language]" (Ez az alkalmazás csak [nem angol nyelven] érhető el" kifejezéssel kezdődik.

Az ajánlat egyszerű létrehozásához előre készítse elő ezeket az elemeket. Mindegyikre szükség van, kivéve a feljegyzett helyeket.

- **Név** – A név fog megjelenni az ajánlat termékoldalának címeként a kereskedelmi piactéren. A név védjegye lehet. Nem tartalmazhat hangulatjeleket (kivéve, ha azok a védjegy és a szerzői jogi szimbólumok), és legfeljebb 50 karakter hosszúságú lehet.
- **Keresési eredmények összegzése** – Az ajánlat célja vagy funkciója egyetlen mondatként, 100 karakternél rövidebb sortörés nélkül. Ezt a rendszer a kereskedelmi piactér keresési eredményeiben használja.
- **Leírás** – Ez a leírás megjelenik a kereskedelmi piactéri termékoldalak áttekintésében. Érdemes lehet értékajánlatot, főbb előnyöket, kívánt felhasználói bázist, bármilyen kategóriát vagy iparági társítást, alkalmazáson belüli vásárlási lehetőségeket, a szükséges közzétételeket és egy hivatkozást is megadni további információhoz. Ez a szövegmező rich text szövegszerkesztő vezérlőkkel rendelkezik, hogy a leírás vonzóbb legyen. Formázáshoz HTML-címkéket is használhat.
- **Kulcsszavak keresése** (nem kötelező) – Legfeljebb három keresési kulcsszó, amelyek segítségével az ügyfelek megtalálják az ajánlatot. Ne foglalja bele az ajánlat **nevét és** **leírását;** A rendszer automatikusan hozzáadja ezt a szöveget a kereséshez.
- **Az alkalmazással való munka terméke** (nem kötelező) – Az ajánlatban szereplő legfeljebb három termék neve.
- **Súgó és adatvédelmi szabályzat hivatkozásai** – Az ajánlat súgóját és a vállalat adatvédelmi szabályzatát mutató URL-címek. Az Ön felelőssége annak biztosítása, hogy alkalmazása megfeleljen az adatvédelmi törvényeknek és előírásoknak.
- **Kapcsolattartási adatok**
  - **Támogatási kapcsolattartó** – A Microsoft-partnerek által a jegyek megnyitásakor használt név, telefon és e-mail-cím. Foglalja bele a támogatási webhely URL-címét.
  - **Mérnöki kapcsolattartó** – A Microsoft által közvetlenül az ajánlattal kapcsolatos problémák esetén használható név, telefon és e-mail-cím. Ezek a kapcsolattartási adatok nem szerepelnek a kereskedelmi piactéren.
- **Támogató dokumentumok** – Legfeljebb három ügyfélkapcsolati dokumentum, például tanulmányok, tanulmányok, ellenőrzőlisták vagy PowerPoint-bemutatók PDF-formátumban.
- **Média**
    - **Emblémák** – A Nagy embléma **PNG-fájlja.** Partnerközpont más szükséges emblémaméretek létrehozásához is ezt fogja használni. Ezeket később más képekre is lecserélheti.
    - **Képernyőképek** – Legalább egy és legfeljebb öt képernyőkép, amely az ajánlat működését mutatja be. A képeknek 1280 x 720 képpontosnak kell lennie, PNG formátumban, és felirattal kell tartalmazniuk.
    - **Videók** (nem kötelező) – Legfeljebb négy, az ajánlatot szemléltető videó. Tartalmaz egy nevet, egy URL-címet a YouTube vagy a Vimeo számára, valamint egy 1280 x 720 képpontos PNG-miniatűrt.

> [!Note]
> Az ajánlatnak meg kell felelnie az általános [kereskedelmi piactéri tanúsítási](/legal/marketplace/certification-policies#100-general) szabályzatnak, hogy közzéte legyen közzétéve a kereskedelmi piactéren.

## <a name="additional-sales-opportunities"></a>További értékesítési lehetőségek

Dönthet úgy, hogy feliratkozik a Microsoft által támogatott marketing- és értékesítési csatornákra. Amikor a microsoftos Partnerközpont hoz létre, két lapot fog látni a Microsofttal való közös értékesítés **folyamatának vége felé.** Ez a lehetőség lehetővé teszi a Microsoft értékesítési csapatai számára, hogy az ön IP-hez való közös értékesítésre jogosult megoldását mérlegelik az ügyfelek igényeinek kiértékelése során. Az [ajánlat kiértékelési](commercial-marketplace-co-sell.md) előkészítésével kapcsolatos Partnerközpont lásd az értékesítésre vonatkozó lehetőségeket a Partnerközpont oldalon.

## <a name="next-steps"></a>Következő lépések

Miután megfontolta a fent leírt tervezési elemeket, válassza ki a következők egyikét (amely a bal oldali tartalomjegyzékben is elérhető) az ajánlat létrehozásának megkezdéséhez.

| Közzétételi útmutató    | Jegyzetek  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) | Amikor a közzétételi folyamatra Enterprise kiadás tekintse át ezeket a további [közzétételi folyamatokat és irányelveket.](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source) |
| [Dynamics 365 for Business Central](partner-center-portal/create-new-business-central-offer.md) |   |
| [Dynamics 365 for Customer Engagement & Power Apps](dynamics-365-customer-engage-offer-setup.md) | Először tekintse át ezeket a további [közzétételi folyamatokat és irányelveket.](/dynamics365/customer-engagement/developer/publish-app-appsource) |
| [Power BI](/partner-center-portal/create-power-bi-app-offer.md) | Először tekintse át ezeket a további [közzétételi folyamatokat és irányelveket.](/power-bi/developer/office-store) |
|
