---
title: Dynamics 365 Business Central-ajánlat létrehozása a Microsoft AppSource
description: Dynamics 365 for Business Central-ajánlat létrehozása a Microsoft AppSource. Listás vagy értékesítés az AppSource-ban vagy a Felhőszolgáltató (CSP) programon keresztül.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 001f7453c29e7a8525fb88a96dd9a867468460e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501433"
---
# <a name="create-a-dynamics-365-for-business-central-offer"></a>Dynamics 365 for Business Central-ajánlat létrehozása

Ez a cikk bemutatja, hogyan hozhat létre új Dynamics 365 Business Central-ajánlatot. [A Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) egy nagyvállalati erőforrás-tervezési (ERP) szolgáltatás, amely számos üzleti folyamatot kezel, beleértve a pénzügyet, az üzemeltetést, az ellátási láncot, a CRM-et, valamint a projektkezelést és az elektronikus kereskedelemmel kapcsolatos folyamatokat. A prémium csomagok a klasszikus üzembe helyezési modellt és gyártást is támogatják. A Dynamics 365 Business Centralra vonatkozó összes ajánlatnak végig kell mennie a tanúsítási folyamatunkon.

A kezdés előtt [hozzon létre egy kereskedelmi piactéri](../create-account.md) fiókot a Partnerközpont, ha még nem tette meg. Győződjön meg arról, hogy a fiókja regisztrálva van a kereskedelmi piactéri programban.

>[!NOTE]
> Az ajánlat közzététele után az ajánlat szerkesztései csak a Partnerközpont online áruházban frissülnek, miután újrakérte az ajánlatot a közzétételre.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [Partnerközpont.](https://partner.microsoft.com/dashboard/home)
2. A bal oldali navigációs menüben válassza a **Kereskedelmi piactér – áttekintés**  >  **lehetőséget.**
3. Az Áttekintés lapon válassza az **+ Új ajánlat** Dynamics  >  **365 Business Central lehetőséget.**

    ![A bal oldali navigációs menüt mutatja be.](./media/new-offer-dynamics-365-business-central.png)

## <a name="new-offer"></a>Új ajánlat

Adjon meg egy **ajánlatazonosítót.** Ez az érték a fiók minden ajánlatának egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a marketplace-ajánlat webcímén, és Azure Resource Manager sablonokat, ha van ilyen.
- Az ajánlatazonosítónak és a közzétevő azonosítójának 40 karakternél nem hosszabbnak kell lennie.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásjeleket, szóközök nélkül. Ha például a közzétevő azonosítója , és a `testpublisherid` **test-offer-1** címet adja meg, az ajánlat webcíme `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` lesz.
- Ez az azonosító a Létrehozás lehetőséget választva nem **módosítható.**

Adjon meg egy **Ajánlat aliast.** Ez az ajánlat neve a Partnerközpont.

- Ez a név nem használatos a piactéren, és eltér az ajánlat nevétől és az ügyfelek számára megjelenő egyéb értékektől.
- A Létrehozás lehetőséget választva ez a név nem **módosítható.**

Válassza **a Létrehozás lehetőséget** az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-setup"></a>Ajánlat beállítása

### <a name="alias"></a>Alias

Adjon meg egy leíró nevet, amely kizárólag az ajánlatra való hivatkozáshoz Partnerközpont. Ez a név (az ajánlat létrehozásakor megadott adatokkal előre kitöltve) nem lesz használva a piactéren, és eltér az ügyfelek számára megjelenő ajánlatnévtől. Ha később frissíteni szeretné az ajánlat nevét, kattintson az [Ajánlatlista oldalra.](#offer-listing)

### <a name="setup-details"></a>Beállítás részletei

Válassza ki **az ajánlatra** vonatkozó Csomagtípust:

- A **bővítményalkalmazások** kibővítik a Dynamics 365 Business Central felhasználói élményét és meglévő funkcióit. Részletekért [lásd: Bővítményalkalmazások.](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)
- A **Connect-alkalmazások** abban a forgatókönyvben használhatók, ahol pont–pont kapcsolatot kell létesíteni a Dynamics 365 Business Central és egy külső megoldás vagy szolgáltatás között. Részletekért lásd: [Alkalmazások csatlakoztatása.](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)

A **Hogyan szeretné, hogy a** lehetséges ügyfelek használják ezt a termékoldali ajánlatot? beállításnál válassza ki az ajánlathoz használni kívánt lehetőséget.

- **Szerezze be most (ingyenes)** – Ajánlata ingyenesen elérhető az ügyfelek számára.
- **Ingyenes próbaverzió (termékoldal) –** Listázás az ügyfeleknek egy ingyenes próbaverzióra mutató hivatkozással. Az ajánlatlista ingyenes próbaverzióit a szolgáltatása hozta létre, kezeli és konfigurálta, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

    > [!NOTE]
    > Az alkalmazás a próbahivatkozáson keresztül kapott jogkivonatokkal csak a Azure Active Directory (Azure AD) használatával szerezheti be a felhasználói adatokat, így automatizálhatja a fiók létrehozását az alkalmazásban. A Microsoft-fiókok nem támogatottak a token használatával való hitelesítéshez.

- **Kapcsolatfelvétel –** Az ügyfélkapcsolat-kezelési (CRM) rendszer csatlakoztatásával gyűjtse össze az ügyfél kapcsolattartási adatait. A rendszer engedélyt kér az ügyféltől az adataik megosztására. Ezeket az ügyféladatokat, valamint az ajánlat nevét, azonosítóját és az ajánlatot megtaláló Marketplace-forrást a konfigurált CRM-rendszernek küldi el a rendszer. A CRM konfigurálásával kapcsolatos további információkért lásd: [Érdeklődők .](#customer-leads)

### <a name="test-drive"></a>Teszt meghajtó

A tesztverzió remek módja annak, hogy bemutassa ajánlatát a potenciális ügyfeleknek azáltal, hogy lehetőséget ad nekik arra, hogy "kipróbálják a vásárlás előtt", ami nagyobb konverziót és magas minősítésű érdeklődők generációját eredményezi. További információ: Mi [az a teszt meghajtó?](../what-is-test-drive.md)

Ha egy teszt meghajtót egy meghatározott ideig engedélyezni szeretné, jelölje be **a Teszt** meghajtó engedélyezése jelölőnégyzetet. Ha el szeretné távolítani a teszt meghajtót az ajánlatból, törölje ezt a jelölőnégyzetet.

### <a name="customer-leads"></a>Érdeklődők az ügyfelek számára

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

További információ: [Érdeklődőkezelés áttekintése.](./commercial-marketplace-get-customer-leads.md)

A **folytatás előtt válassza a Piszkozat** mentése lehetőséget.

## <a name="properties"></a>Tulajdonságok

Ezen az oldalon meghatározhatja az ajánlat piactéren való csoportosítására használt kategóriákat és iparágakat, az alkalmazás verzióját, valamint az ajánlatot támogató jogi szerződéseket.

### <a name="categories"></a>Kategóriák

Válassza ki a kategóriákat és alkategóriákat, hogy az ajánlatot a megfelelő piactéri keresési területeken helyezze el. Mindenképpen írja le, hogy az ajánlat hogyan támogatja ezeket a kategóriákat az ajánlat leírásában. A következők szerint válasszon:

- Legalább egy és legfeljebb két kategória, köztük egy elsődleges és egy másodlagos kategória (nem kötelező).
- Minden elsődleges és/vagy másodlagos kategóriához legfeljebb két alkategória tartozik. Ha az ajánlatra nem vonatkozik alkategória, válassza a **Nem alkalmazható lehetőséget.**

A kategóriák és alkategóriák teljes listáját az Ajánlott [ajánlatlistázási eljárások rész tartalmazza.](../gtm-offer-listing-best-practices.md)

### <a name="industries"></a>Iparágak

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>Az alkalmazás verziója

Adja meg az ajánlat verziószámát. Az ügyfelek láthatják ezt a verziót az ajánlat részletező oldalán.

### <a name="terms-and-conditions"></a>használati feltételei

Adja meg a saját jogi feltételeit. Meg is használhatja azt a címet, ahol a használati feltételek találhatók. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

A **folytatás előtt válassza a Piszkozat** mentése lehetőséget.

## <a name="offer-listing"></a>Ajánlatlista

Ezen az oldalon meghatározhatja az ajánlat részleteit, például az ajánlat nevét, leírását, hivatkozását és névjegyeit.

> [!NOTE]
> Az ajánlatlista részleteit csak egy nyelven adja meg. Nem kötelező angol nyelvűnek lennie, ha az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak [nem angol nyelven] érhető el." Hasznos hivatkozás URL-címének megadása is elfogadható, ha nem az Ajánlat termékoldali tartalomban használt nyelven kínál tartalmat. 

Íme egy példa arra, hogyan jelennek meg az ajánlatinformációk a Microsoft AppSource (a felsorolt árak csak példák, és nem a tényleges költségeket hivatottak tükrözni):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Bemutatja, hogyan jelenik meg ez az ajánlat Microsoft AppSource.":::

### <a name="call-out-descriptions"></a>A felhívások leírása

1. Embléma
2. Termékek
3. Kategóriák
4. Támogatási cím (hivatkozás)
5. Használati feltételek
6. Adatvédelmi irányelvek
7. Ajánlat neve
8. Összefoglalás
9. Leírás
10. Képernyőképek/videók

### <a name="marketplace-details"></a>A Marketplace részletei

Az **itt** beírott Név az ajánlat termékoldalának címeként jelenik meg az ügyfelek számára. Ez a mező előre ki van töltve az **Ajánlat** aliashoz az ajánlat létrehozásakor megadott szöveggel, de ezt az értéket módosíthatja. Ez a név lehet védjegyes (és ön védjegy- vagy szerzői jogi szimbólumokat is tartalmazhat). A név nem lehet hosszabb 50 karakternél, és nem tartalmazhat hangulatjeleket.

Adja meg az ajánlat rövid leírását (legfeljebb 100 karakter) a Keresési eredmények **összegzése mezőben.** Ez a leírás használható a Marketplace keresési eredményeiben.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

Igény szerint akár három  Keresési kulcsszót is megadhatja, hogy az ügyfelek megtalálják az ajánlatot a piactéren. A legjobb eredmény érdekében ezeket a kulcsszavakat is használja a leírásban.

Ha szeretné tudatni az ügyfelekkel, hogy az alkalmazás milyen termékekkel **működik együtt,** adjon meg legfeljebb három terméknevet.

### <a name="helpprivacy-urls"></a>Súgó/adatvédelmi URL-címek

Adja meg **az alkalmazás súgóhivatkozását** (URL-címét), ahol az ügyfelek további információt kaphatnak az ajánlatról. A Súgó URL-címe nem lehet ugyanaz, mint a támogatási URL-cím.

Adja meg **a szervezet** adatvédelmi szabályzatának Adatvédelmi szabályzatra mutató hivatkozását (URL-címét). Az Ön felelőssége, hogy alkalmazása megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosít.

### <a name="contact-information"></a>Kapcsolattartási adatok

Adja meg egy támogatási kapcsolattartó és  egy mérnöki kapcsolattartó nevét, e-mail-címét **és telefonszámát.** Ezek az információk nem jelennek meg az ügyfelek számára, de elérhetők lesznek a Microsoft számára, és a CSP-partnerek számára is elérhetők lesznek.

A Támogatási **kapcsolattartó szakaszban** adja meg a támogatási **URL-címet,** ahol a CSP-partnerek támogatást találhatnak az ajánlathoz. A támogatási URL-cím nem lehet ugyanaz, mint a súgó URL-címe.

### <a name="supporting-documents"></a>Támogató dokumentumok

Adjon meg legalább egy (és legfeljebb három) kapcsolódó marketingdokumentumot, például tanulmányokat, fotókat, ellenőrzőlistákat vagy bemutatókat PDF formátumban.

### <a name="marketplace-media"></a>Marketplace-média

Adjon meg olyan emblémákat és képeket, amelyek az ajánlat ügyfelek számára való megjelenítésekor lesznek használva. Minden képnek PNG formátumúnak kell lennie.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Ha probléma ad a fájlok feltöltésével, győződjön meg arról, hogy a helyi hálózat nem blokkolja a `https://upload.xboxlive.com` Partnerközpont.

#### <a name="logos"></a>Logók

Adjon meg egy PNG-fájlt a **Nagy méretű emblémához.** Partnerközpont a kezdeti fájlt fogja használni más szükséges méretek létrehozásához. Az átméretezett képet később lecserélheti a saját képére.

Ezeket az emblémákat a lista különböző helyei használják:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Képernyőképek

Képernyőképeket adhat hozzá, amelyek az ajánlat működését mutatják be. Legalább három képernyőképre van szükség, és legfeljebb ötöt adhat hozzá. Minden képernyőképnek 1280 x 720 képpont méretűnek kell lennie, PNG formátumban.

#### <a name="videos"></a>Videók

Igény szerint akár négy videót is hozzáadhat, amelyek bemutatják az ajánlatot. A videókat külső webhelyen kell üzemeltetni. Mindegyikhez adja meg a videó nevét, címét és miniatűr képét (1280 x 720 képpont).

További marketplace-termékoldali forrásokkal kapcsolatban lásd: [Ajánlott eljárások a Marketplace-ajánlatoldalakhoz.](../gtm-offer-listing-best-practices.md)

A **folytatás előtt válassza a Piszkozat** mentése lehetőséget.

## <a name="availability"></a>Rendelkezésre állás

Ezen az oldalon meghatározhatja, hogy hol és hogyan tegye elérhetővé az ajánlatot.

### <a name="markets"></a>Piacok

A piacokat, ahol az ajánlatnak elérhetőnek kell lennie, válassza a **Piacok szerkesztése** lehetőséget a **Piacválasztás felugró** ablak megjelenítéséhez.

Válasszon ki legalább egy piacot. Válassza **az Összes kiválasztása** lehetőséget, ha az ajánlatot minden lehetséges piacon elérhetővé szeretné tenni, vagy csak a kívánt piacokat válassza ki. Ha elkészült, válassza a **Mentés lehetőséget.**

Az itt kiválasztott beállítások csak az új beszerzésekre vonatkoznak; Ha valaki már rendelkezik egy adott piacon az alkalmazással, és Ön később eltávolítja azt a piacot, azok, akik már rendelkezik az adott piacon elérhető ajánlattal, továbbra is használhatja azt, de az adott piacon egyetlen új ügyfél sem fogja tudni szerezni az ajánlatot.

> [!IMPORTANT]
> Az Ön felelőssége, hogy megfeleljen a helyi jogi követelményeknek, még akkor is, ha ezek a követelmények nem szerepelnek itt vagy a Partnerközpont. Még ha kiválasztja is az összes piacot, a helyi jogszabályokat, korlátozásokat és egyéb tényezőket, előfordulhat, hogy bizonyos ajánlatok nem szerepelnek egyes országokban és régiókban.

### <a name="preview-audience"></a>Előzetes verzió célközönsége

Mielőtt élőben közzéten egy ajánlatot a szélesebb piactéri ajánlatban, először elérhetővé kell tennie egy korlátozott előzetes verziójú **célközönség számára.** Adjon meg **egy Elrejtés kulcsot** (bármilyen sztringet, amely csak kisbetűket és/vagy számokat használ) itt. Az előzetes verzió célközönségének tagjai ezt az elrejtő kulcsot jogkivonatként használva megtekinthetik az ajánlat előnézetét a piactéren.

Ezután, amikor készen áll az ajánlat elérhetővé tenni és eltávolítani az előzetes verzióra vonatkozó korlátozást, el kell távolítania az **Elrejtés** kulcsot, és újra közzé kell tennie.

A **folytatás előtt válassza a Piszkozat** mentése lehetőséget.

## <a name="technical-configuration"></a>Technikai konfiguráció

Ez az oldal határozza meg az ajánlathoz való csatlakozáshoz használt technikai részleteket. Ez a kapcsolat lehetővé teszi, hogy kiépítsük az ajánlatot a végfelhasználó számára, ha úgy döntenek, hogy megvásárolják.

Az ajánlathoz beküldött bővítményeknek meg kell felelnie a műszaki érvényesítési ellenőrzőlista [követelményeinek.](/dynamics365/business-central/dev-itpro/developer/devenv-checklist-submission)

### <a name="file-upload"></a>Fájlfeltöltés

Ha korábban a **Hozzáadás a következőn** lehetőséget választotta, ahol fel fogja tölteni az ajánlat csomagfájlját, valamint az összes olyan bővítmény csomagfájlját, amelyhez függőségei vannak.

#### <a name="extension-package-file"></a>Bővítménycsomag-fájl

Töltse fel az ajánlat bővítménycsomag-fájlját (.app).

#### <a name="library-extension-package-file"></a>Könyvtárbővítmény csomagfájlja

Kötelező, ha az ajánlatot egy másik bővítménysel együtt kell telepíteni, amely nem lesz közzétéve a piactéren. Ha igen, töltse fel ide az .app fájlt.

>[!NOTE]
>A függőségcsomag-fájl már nincs használva. Ehelyett töltsön fel egy könyvtárbővítmény-csomagfájlt.

A **folytatás előtt válassza a Piszkozat** mentése lehetőséget.

## <a name="supplemental-content"></a>Kiegészítő tartalom

Ezen az oldalon további információkat is meg tud adni az ajánlat ellenőrzésében. Ezek az információk nem jelennek meg az ügyfelek számára, és nem jelennek meg a piactéren.

### <a name="target-release"></a>Célkiadás

Adja meg, hogy a microsoftos Dynamics Business Central melyik kiadását célozza a megoldás: **Aktuális,** **Következő nagyobb**, vagy **Következő alverzió.** Ezek az információk lehetővé teszi a megoldás megfelelő tesztelését.

### <a name="supported-editions"></a>Támogatott kiadások

Ha az ajánlathoz a Microsoft Dynamics 365 Business Central Prémium kiadására van szükség, válassza a **Csak Prémium lehetőséget.** Ellenkező esetben válassza az **Alapvető szolgáltatások és** a Prémium **lehetőséget** is.

### <a name="key-usage-scenario"></a>Kulcshasználati forgatókönyv

Töltsön fel egy PDF-fájlt, amely felsorolja az ajánlat kulcshasználati forgatókönyvét. Az itt felsorolt forgatókönyveket az érvényesítési csapatunk ellenőrizheti, mielőtt jóváhagyjuk a piactérre vonatkozó ajánlatát.

### <a name="test-accounts"></a>Tesztfiókok

Ha tesztelési fiókra van szükség ahhoz, hogy a minősítő csapatunk megfelelően áttekintse az ajánlatot, töltsön fel egy .pdf, .doc vagy .docx fájlt a **Tesztfiókok adataival.**

### <a name="app-tests-automation"></a>Alkalmazástesztek automatizálása

Ha az ajánlat egy bővítményalkalmazás, fel kell töltenie egy **alkalmazástesztek** automatizálási fájlját (.app). Ez a fájl nem vonatkozik az alkalmazások csatlakoztatása esetén.

A **folytatás előtt válassza a Piszkozat** mentése lehetőséget.

## <a name="publish"></a>Közzététel

### <a name="submit-offer-to-preview"></a>Ajánlat bekülde az előzetes verzióra

Az ajánlat összes szükséges szakaszának  befejezése után válassza a portál jobb felső sarkában található Áttekintés és közzététel lehetőséget.

Ha most először közzéteszi ezt az ajánlatot, a következőt használhatja:

- Tekintse meg az ajánlat egyes szakaszának befejezési állapotát.
  - **Nincs elindítva** – A szakaszt nem érintette a folyamat, ezért be kell fejeződni.
  - **Hiányos –** A szakasz olyan hibákat tartalmaz, amelyek kijavítása vagy további információkra van szükség. Vissza a szakasz(ak)hoz, és frissítse.
  - **Complete** (Kész) – A szakasz elkészült, minden szükséges adat meg lett téve, és nincsenek hibák. Az ajánlat minden szakaszának teljes állapotban kell lennie az ajánlat beküldését megelőzően.
- A Megjegyzések **a tanúsításhoz** szakaszban adjon meg tesztelési utasításokat a minősítő csapatnak, hogy az alkalmazás tesztelése megfelelő legyen, valamint adjon meg kiegészítő megjegyzéseket, amelyek hasznosak lehetnek az alkalmazás megértéséhez.
- Küldje el közzétételre az ajánlatot a Submit (Küldés) **lehetőség kiválasztásával.** E-mailben értesítjük, ha az ajánlat előzetes verziója felülvizsgálható és jóváhagyható. Térjen vissza Partnerközpont, és válassza a **Go-live lehetőséget** az ajánlat nyilvános közzétételéhez.

## <a name="next-steps"></a>Következő lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
