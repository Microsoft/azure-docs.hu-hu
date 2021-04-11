---
title: Dynamics 365 létrehozása a Customer engagement & PowerApps ajánlathoz a Microsoft kereskedelmi piactéren
description: Dynamics 365 létrehozása az ügyfelek engagement & PowerApps ajánlatában Microsoft AppSource. Az ajánlatot AppSource vagy a Cloud Solution Provider (CSP) programon keresztül listázhatja vagy értékesítheti.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 1661fd78812bff14d9efddf9ef1c31a359aa959d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107363"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Dynamics 365 for Customer Engagement- és PowerApps-ajánlat létrehozása

Ez a cikk bemutatja, hogyan hozhat létre új Dynamics 365-t az ügyfelek engagement & PowerApps ajánlatához. A Dynamics 365 for Customer engagement (PowerApps, Sales, Service, Project Service és Field Service) összes alkalmazásának a minősítési folyamaton keresztül kell megfelelnie, amely a standard követelmények, a kompatibilitás és a megfelelő eljárások tekintetében ellenőrzi a megoldást. A próbaverzió lehetővé teszi, hogy a felhasználók egy élő Dynamics 365-környezetbe telepítse a megoldást.

A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](../create-account.md) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

>[!NOTE]
> Az ajánlat közzétételét követően az ajánlat szerkesztése csak a partner Centerben és az online áruházban lesz frissítve, miután újra elküldte az ajánlatot a kiadványhoz.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés** lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **Dynamics 365 lehetőséget a Customer engagement & PowerApps**.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

## <a name="new-offer"></a>Új ajánlat

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a Piactéri ajánlathoz tartozó webcímek és Azure Resource Manager sablonok esetében, ha van ilyen.
- A közzétevő AZONOSÍTÓval összevont ajánlat AZONOSÍTÓjának 40 karakternél rövidebbnek kell lennie.
- Csak kisbetűket és számokat használjon. Kötőjeleket és aláhúzást tartalmazhat, de nem tartalmaz szóközt. Ha például a közzétevő azonosítója, és az ajánlat `testpublisherid` **-1 tesztet** adja meg, akkor az ajánlat webcíme lesz `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Ez az azonosító nem módosítható a **Létrehozás** gombra kattintva.

Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

- Ez a név nem használatos a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.
- A **Létrehozás** gombra kattintva ez a név nem módosítható.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-setup"></a>Ajánlat beállítása

### <a name="alias"></a>Alias

Adjon meg egy leíró nevet, amelyet a rendszer kizárólag a partner centeren belüli ajánlatra való hivatkozáshoz használ. Ez a név (előre kitöltve az ajánlat létrehozásakor megadott módon) nem lesz használatban a piactéren, és eltér az ügyfelek számára megjelenített ajánlat nevétől. Ha később szeretné frissíteni az ajánlat nevét, lépjen az [ajánlati lista](#offer-listing) oldalra.

### <a name="setup-details"></a>Telepítés részletei

Hogyan kívánja használni a **potenciális ügyfeleket a jelen tőzsdei ajánlathoz?** válassza ki az ajánlathoz használni kívánt beállítást.

- **Töltse le most (ingyenes)** – ajánlatát ingyenesen kilistázhatja az ügyfeleknek.
- **Ingyenes próbaverzió (Listázás)** – az ajánlat az ingyenes próbaverzióra mutató hivatkozással sorolja fel az ügyfeleket. Az ingyenes próbaverziók listáját a szolgáltatás hozza létre, felügyeli és konfigurálja, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

    > [!NOTE]
    > Az alkalmazás által a próbaverziós hivatkozáson keresztül fogadott jogkivonatok csak Azure Active Directory (Azure AD) használatával szerezhetik be a felhasználói adatokat, hogy automatizálják a fiókok létrehozását az alkalmazásban. A Microsoft-fiókok nem támogatottak a jogkivonat használatával történő hitelesítéshez.

- **Kapcsolatfelvétel** – ügyfél-kapcsolattartási adatok összegyűjtése az Ügyfélkapcsolat-kezelési (CRM) rendszer csatlakoztatásával. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és Piactéri forrását, ahol az ajánlat megtalálható, a rendszer elküldi a konfigurált CRM-rendszernek. A CRM konfigurálásával kapcsolatos további információkért lásd: [ügyfél-érdeklődők](#customer-leads).

### <a name="test-drive"></a>Tesztelési meghajtó

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a vásárlás előtt kipróbálhatja a "kipróbálás előtt" lehetőséget, ami növeli a konverziót és a magas minősítésű érdeklődők generációját. További információ: [Mi a test Drive](../what-is-test-drive.md).

Ha egy tesztelési meghajtót egy meghatározott ideig szeretne engedélyezni, jelölje be a **Test Drive engedélyezése** jelölőnégyzetet. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

### <a name="customer-leads"></a>Ügyfél-érdeklődők

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

További információ: az [ólom kezelése – áttekintés](./commercial-marketplace-get-customer-leads.md).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="properties"></a>Tulajdonságok

Ezen a lapon megadhatja az ajánlatnak a piactéren, az alkalmazás verziójában és az ajánlatát támogató jogi szerződésekben való csoportosításához használt kategóriákat és iparágakat.

### <a name="categories"></a>Kategóriák

Válassza ki a kategóriákat és alkategóriákat, hogy az ajánlatot a piactér megfelelő keresési területein helyezze el. Ügyeljen arra, hogy az ajánlata milyen módon támogassa ezeket a kategóriákat az ajánlat leírásában. A következők szerint válasszon:

- Legalább egy és legfeljebb két kategória, beleértve az elsődleges és a másodlagos kategóriát is (opcionális).
- Legfeljebb két alkategória lehet minden elsődleges és/vagy másodlagos kategóriához. Ha nem alkalmazható alkategória az ajánlatra, válassza a **nem alkalmazható** lehetőséget.

Tekintse meg a kategóriák és alkategóriák teljes listáját az [ajánlatban az ajánlott eljárásokat felsorolva](../gtm-offer-listing-best-practices.md).

### <a name="industries"></a>Iparágak

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Alkalmazható Dynamics 365-termékek

Válassza ki az összes olyan Dynamics 365-terméket, amelyre ez az ajánlat vonatkozik.

### <a name="app-version"></a>Az alkalmazás verziója

Adja meg az ajánlat verziószámát. Az ügyfelek ezt a verziót fogják látni az ajánlat részleteit tartalmazó oldalon.<!-- If you are only updating the version number due to marketing/descriptive changes, check the **Marketing only change** box. This option allows the offer to bypass the certification and provisioning stages.-->

### <a name="terms-and-conditions"></a>használati feltételei

Itt megadhatja a saját jogi feltételeit. Megadhatja azt is, hogy hol találhatók a feltételek és kikötések. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="offer-listing"></a>Ajánlati lista

<!--This page displays the languages in which your offer will be listed. Currently, **English (United States)** is the only available option.

Define marketplace details for each language/market here, such as offer name, description, and images. Select the language/market name to provide this information.-->This page lets you define offer details such as offer name, description, links, and contacts.

> [!NOTE]
> Adja meg az ajánlati adatokat csak egy nyelven. Nem kell angol nyelven lennie, amíg az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelvű] nyelven érhető el." Azt is elfogadható, hogy egy *hasznos hivatkozási URL-címet* adjon meg, amely nem az ajánlatban szereplő tartalomban használt tartalmat tartalmazza.

Íme egy példa arra, hogyan jelennek meg az ajánlati információk a Microsoft AppSourceban (a felsorolt árak kizárólag a tényleges költségekkel kapcsolatos célokat szolgálnak):
<!-- update screen? -->
:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat a Microsoft AppSourceban.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Embléma
1. Termékek
1. Kategóriák
1. Támogatási címe (hivatkozás)
1. Használati feltételek
1. Ajánlat neve
1. Description
1. Képernyőképek/videók

### <a name="marketplace-details"></a>Piactér – részletek

Az itt megadott **név** jelenik meg az ügyfelek számára az ajánlati lista címének megfelelően. Ez a mező előre fel van töltve az ajánlat- **aliashoz** megadott szöveggel az ajánlat létrehozásakor, de ez az érték módosítható. Ez a név lehet védjeggyel ellátott (és előfordulhat, hogy védjegyeket vagy szerzői jogi szimbólumokat is tartalmaz). A név nem lehet hosszabb 50 karakternél, és nem tartalmazhat hangulatjelek.

Adja meg az ajánlat rövid leírását, amely akár 100 karaktert is tartalmaz a **keresési eredmények összegzéséhez**. Ez a leírás a piactér keresési eredményeiben használható.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

Akár három **keresési kulcsszót** is megadhat, amelyek segítségével az ügyfelek megtalálják az ajánlatot a piactéren. A legjobb eredmény érdekében ezeket a kulcsszavakat is használhatja a leírásában.

Ha szeretné, hogy az ügyfelek tudják, hogy **az alkalmazás melyik termékkel működik együtt**, adjon meg legfeljebb három terméknév-nevet.

### <a name="helpprivacy-urls"></a>Súgó/adatvédelmi URL-címek

Adja meg az alkalmazás (URL) **Súgó hivatkozását,** ahol az ügyfelek többet tudnak nyújtani az ajánlatról. A Súgó URL-címe nem egyezhet meg a támogatási URL-címmel.

Adja meg az **adatvédelmi szabályzat hivatkozását** (URL-címét) a szervezete adatvédelmi szabályzatához. Ön felelős azért, hogy az alkalmazás megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson.

### <a name="contact-information"></a>Kapcsolattartási adatok

Adja meg a **támogatási kapcsolattartó** nevét, e-mail-címét és telefonszámát, valamint egy **mérnöki kapcsolattartót**. Ezek az információk nem jelennek meg az ügyfelek számára, de a Microsoft számára elérhetővé válnak, és a CSP-partnerek számára is biztosítva lesznek.

A **támogatási kapcsolattartó** szakaszban adja meg a **támogatási URL-címet** , ahol a CSP-partnerek az ajánlathoz támogatást kaphatnak. A támogatási URL-cím nem egyezhet meg a Súgó URL-címével.

### <a name="supporting-documents"></a>Támogató dokumentumok

Adjon meg legalább egy (és legfeljebb három) kapcsolódó marketing-dokumentumot, például a tanulmányokat, a brosúrákat, a feladatlistákat vagy a bemutatókat PDF formátumban.

### <a name="marketplace-media"></a>Piactéri adathordozó

Adja meg azokat az emblémákat és képeket, amelyeket az ajánlat ügyfeleknek való megjelenítésekor használni fog. Minden képnek PNG formátumúnak kell lennie.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

#### <a name="logos"></a>Logók

Adjon meg egy PNG-fájlt a **nagy** méretű emblémához. A partner Center ezt fogja használni más szükséges méretek létrehozásához. Ezt később is lecserélheti egy másik rendszerképpel.

Ezeket az emblémákat a lista különböző helyein használják:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Képernyőképek

Képernyőképeket adhat hozzá, amelyek bemutatják, hogyan működik az ajánlata. Legalább egy képernyőképre van szükség, és legfeljebb öt adható hozzá. Minden képernyőképnek 1280 x 720 képpont és PNG formátumúnak kell lennie.

#### <a name="videos"></a>Videók

Opcionálisan akár négy videót is hozzáadhat az ajánlat bemutatásához. A videókat külső helyen kell tárolni. Mindegyiknél adja meg a videó nevét, a címeit és a videó miniatűr képét (1280 x 720 képpont).

További információforrások a piactéren: ajánlott [eljárások a Piactéri ajánlatokhoz](../gtm-offer-listing-best-practices.md).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="availability"></a>Rendelkezésre állás

Ezen a lapon megadhatja, hogy hol és hogyan szeretné elérhetővé tenni az ajánlatot.

### <a name="markets"></a>Piacok

Ha meg szeretné adni azokat a piacokat, amelyeken ajánlatának elérhetőnek kell lennie, válassza a **piacok szerkesztése** lehetőséget a **piaci kiválasztási** felugró ablak megjelenítéséhez.

Válasszon ki legalább egy piacot. Az **összes kijelölése** lehetőség kiválasztásával elérhetővé teheti az ajánlatot minden lehetséges piacon, vagy kiválaszthatja a kívánt piacokat. Ha elkészült, válassza a **Mentés** lehetőséget.

Az itt megadott beállítások csak az új beszerzésekre érvényesek; Ha valaki már rendelkezik az alkalmazással egy bizonyos piacon, és később eltávolítja ezt a piacot, azok a felhasználók továbbra is használhatják ezt a piacot, de az adott piacon már nem jelennek meg új ügyfelek az ajánlatának megszerzéséhez.

> [!IMPORTANT]
> Az Ön felelőssége, hogy megfeleljen a helyi jogi követelményeknek, még akkor is, ha ezek a követelmények nem szerepelnek itt vagy a partner Centerben. Még akkor is, ha az összes piac lehetőséget választja, a helyi törvények, korlátozások vagy egyéb tényezők miatt bizonyos ajánlatokat egyes országokban és régiókban is meg lehet akadályozni.

### <a name="preview-audience"></a>Előnézet célközönsége

Mielőtt közzéteszi ajánlatát a szélesebb körű Piactéri ajánlatban, először elérhetővé kell tennie egy korlátozott **előzetes verzió célközönségét**. Itt adhatja meg az **elrejtési kulcsot** (a csak kisbetűket és/vagy számokat használó karakterláncokat). Az előzetes verzió célközönségének tagjai ezt az elrejtési kulcsot jogkivonatként használhatják a piactéren elérhető ajánlat előnézetének megtekintéséhez.

Ezután, amikor készen áll az ajánlat elérhetővé tételére és az előzetes verzió korlátozásának eltávolítására, el kell távolítania az **elrejteni kívánt kulcsot** , és újra közzé kell tennie.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="technical-configuration"></a>Technikai konfiguráció

Ez az oldal az ajánlathoz való kapcsolódáshoz használt technikai részleteket határozza meg. Ez a kapcsolat lehetővé teszi, hogy az ajánlatot a végfelhasználók számára kiépítse, ha úgy dönt, hogy megszerezzék.

### <a name="offer-information"></a>Ajánlati információk

Az **Alap licencelési modell** határozza meg, hogy az ügyfelek Hogyan kapják meg az alkalmazást a CRM felügyeleti központban. Válassza ki az **erőforrást** a példány-alapú licenceléshez vagy a **felhasználóhoz** , ha a licencek egy bérlőhöz vannak rendelve.

A **S2S kimenő és a CRM Biztonságos tár elérésének** engedélyezése jelölőnégyzet lehetővé teszi a CRM Biztonságos tár vagy a kiszolgáló – kiszolgáló (S2S) kimenő hozzáférésének konfigurálását. Ehhez a szolgáltatáshoz a minősítési fázisban a Dynamics 365 csapata speciális figyelmet igényel. A Microsoft felveszi Önnel a kapcsolatot a funkció támogatásához szükséges további lépések végrehajtásához.

Hagyja üresen az **alkalmazás konfigurációjának URL-címét** ; későbbi használatra.

### <a name="crm-package"></a>CRM-csomag

A **csomag helyének URL-címéhez** adja meg egy Azure Blob Storage-fiók URL-címét, amely tartalmazza a feltöltött CRM Package. zip fájlt. Egy írásvédett SAS-kulcsot is tartalmaz az URL-címben, így a Microsoft a csomagot az ellenőrzéshez is felveheti.

> [!IMPORTANT]
> A közzétételi blokk elkerüléséhez győződjön meg arról, hogy a blob Storage URL-címében szereplő lejárati dátum nem járt le. A dátum a Szabályzathoz való hozzáféréssel módosítható. Azt javasoljuk, hogy a **lejárati idő** legyen legalább egy hónappal később.

Ha alkalmazható, válassza ki a **csomag fájljában a több CRM-csomagot** . Ha igen, ügyeljen arra, hogy a. zip fájlban lévő összes csomagot tartalmazza.

A csomag létrehozásával és szerkezetének frissítésével kapcsolatos részletes információkért lásd a [3. lépés: AppSource-csomag létrehozása az alkalmazáshoz](/powerapps/developer/common-data-service/create-package-app-appsource)című témakört.

### <a name="crm-package-availability"></a>CRM-csomag elérhetősége

A **+ régió hozzáadása** lehetőség kiválasztásával megadhatja azokat a földrajzi régiókat, amelyekben a CRM-csomag elérhető lesz az ügyfelek számára. A következő szuverén régiókban való üzembe helyezéshez különleges engedélyek és érvényesítés szükséges a minősítési folyamat során: [Németország](../../germany/index.yml), [USA kormányzati felhője](../../azure-government/documentation-government-welcome.md)és tipp.

Alapértelmezés szerint az egyes régiókban a fentiekben megadott **konfigurációs URL-cím** lesz használva. Ha szeretné, megadhat egy külön alkalmazás-konfigurációs URL-címet egy vagy több meghatározott régióhoz.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

<!-- ## Test drive technical configuration

This page lets you set up a demonstration ("test drive") that allows customers to try your offer before purchasing it. Learn more in [What is test drive](../what-is-test-drive.md).

To enable a test drive, select the **Enable a test drive** check box on the [Offer setup](#test-drive) tab. To remove test drive from your offer, clear this check box.

When you've finished setting up your test drive, select **Save draft** before continuing. -->

## <a name="supplemental-content"></a>Kiegészítő tartalom

Ezen az oldalon további információkat adhat meg, amelyek segítenek az ajánlat érvényesítésében. Ezek az információk nem jelennek meg az ügyfelek számára, és nem jelennek meg a piactéren.

### <a name="key-usage-scenario"></a>Kulcshasználat forgatókönyv

Töltsön fel egy PDF-fájlt, amely felsorolja az ajánlat legfontosabb felhasználási forgatókönyveit. Az ellenőrzési csapat minden forgatókönyvet ellenőrizheti, mielőtt jóváhagyja ajánlatát a piactéren.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="publish"></a>Közzététel

### <a name="submit-offer-to-preview"></a>Ajánlat beküldése az előzetes verzióra

Az ajánlat összes szükséges részének befejezése után válassza a **felülvizsgálat és közzététel** elemet a portál jobb felső sarkában.

Ha első alkalommal teszi közzé ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát.
    - **Nincs elindítva** – a szakasz nem lett megérintve, ezért el kell végezni.
    - **Hiányos** – a szakasz olyan hibákat tartalmaz, amelyeket meg kell oldania, vagy további információra van szüksége. Térjen vissza a szakasz (ok) hoz, és frissítse azt.
    - A **Complete (Befejezés** ) szakasz befejeződött, az összes szükséges információ megadásra került, és nincsenek hibák. Az ajánlat minden részének teljes állapotban kell lennie ahhoz, hogy el tudja küldeni az ajánlatot.
- A **minősítési megjegyzések** szakaszban adja meg a minősítési csoport tesztelési utasításait, hogy az alkalmazás megfelelően legyen tesztelve, valamint az alkalmazás megértéséhez szükséges kiegészítő megjegyzések mellett.
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. E-mailben küldünk Önnek, ha az ajánlat előzetes verziója elérhető a felülvizsgálathoz és jóváhagyáshoz. Térjen vissza a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat nyilvános közzétételéhez.

## <a name="next-steps"></a>Következő lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
