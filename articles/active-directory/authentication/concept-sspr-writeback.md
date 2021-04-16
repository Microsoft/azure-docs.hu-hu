---
title: Helyszíni jelszóvisszaírás új jelszó önkiszolgáló visszaállításával – Azure Active Directory
description: Megtudhatja, hogyan írható vissza Azure Active Directory jelszóváltozási vagy -visszaállítási események egy helyszíni címtárkörnyezetbe
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8a84da331568d36b6f6910054fdb2aea76f490
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530329"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Hogyan működik az önkiszolgáló jelszóvisszaírás a Azure Active Directory?

Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználók számára a jelszavuk visszaállítását a felhőben, de a legtöbb vállalatnak van egy helyi Active Directory Domain Services (AD DS) környezete is, amelyben a felhasználók vannak. A jelszóvisszaírás egy [](../hybrid/whatis-hybrid-identity.md) olyan szolgáltatás, Azure AD Connect lehetővé teszi a felhőben végrehajtott jelszóváltozások valós idejű visszaírását egy meglévő helyszíni címtárba. Ebben a konfigurációban, amikor a felhasználók az SSPR használatával módosítják vagy visszaállítják jelszavukat a felhőben, a frissített jelszavak a helyszíni környezetbe is visszaírva AD DS vissza

> [!IMPORTANT]
> Ez a fogalmi cikk ismerteti egy rendszergazdának, hogyan működik az önkiszolgáló jelszó-visszaállítási visszaírás. Ha Ön már regisztrált végfelhasználó az új jelszó önkiszolgáló nevére, és vissza kell kapnia a fiókját, akkor a(nak) gombra kell https://aka.ms/sspr állítania.
>
> Ha az it-csapat nem engedélyezte a saját jelszó alaphelyzetbe állításának képességét, további segítségért az segélycsapat segítségét kell kérnie.

A jelszóvisszaírás az alábbi hibrid identitásmodelleket felhasználó környezetekben támogatott:

* [Jelszó kivonatszinkronizálása](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Átmenő hitelesítés](../hybrid/how-to-connect-pta.md)
* [Active Directory összevonási szolgáltatások](../hybrid/how-to-connect-fed-management.md)

A jelszóvisszaírás a következő szolgáltatásokat biztosítja:

* **Az helyi Active Directory Domain Services (AD DS)** jelszó-házirendek kényszerítése: Amikor egy felhasználó visszaállítja a jelszavát, a rendszer ellenőrzi, hogy megfelel-e a helyszíni AD DS-szabályzatnak, mielőtt véglegesné teszi azt az adott címtárban. Ez a felülvizsgálat magában foglalja az előzmények, az összetettség, az életkor, a jelszószűrők és az egyéb jelszókorlátozások ellenőrzését, amelyek a AD DS.
* **Nulla késleltetésű visszajelzés:** A jelszóvisszaírás szinkron művelet. A felhasználók azonnal értesítést kapnak, ha a jelszavuk nem felel meg a szabályzatnak, vagy bármilyen okból nem állíthatók vissza vagy módosíthatók.
* **Támogatja a** hozzáférési panelen és a Microsoft 365: Ha az összevont vagy jelszó kivonatával szinkronizált felhasználók módosítják lejárt vagy nem lejárt jelszavukat, a rendszer visszaírja ezeket a jelszavakat a AD DS.
* Támogatja a jelszóvisszaírást, amikor egy rendszergazda alaphelyzetbe állítja őket a Azure Portal-ból: Amikor egy rendszergazda alaphelyzetbe állítja egy felhasználó jelszavát a [Azure Portal-ban,](https://portal.azure.com)ha a felhasználó összevont, vagy a jelszó kivonata szinkronizálva van, a rendszer visszaírja a jelszót a helyszíni környezetbe. Ez a funkció jelenleg nem támogatott az Office felügyeleti portálján.
* **Nem igényel bejövő** tűzfalszabályokat: A jelszóvisszaírás egy Azure Service Bus továbbítót használ mögöttes kommunikációs csatornaként. Minden kommunikáció kimenő a 443-as porton keresztül.

> [!NOTE]
> A helyszíni AD-hez védett csoportokban található rendszergazdai fiókok jelszóvisszaírással használhatók. A rendszergazdák módosíthatják a jelszavukat a felhőben, de nem használhatnak új jelszó-visszaállítást az elfelejtett jelszó visszaállításához. További információ a védett csoportokról: Védett fiókok és csoportok a [AD DS.](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory)

Az SSPR-visszaírás első lépésekhez töltse ki a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az új jelszó önkiszolgáló visszaállításának (SSPR) visszaírásának engedélyezése](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>A jelszóvisszaíró működése

Amikor egy szinkronizált összevont vagy jelszó kivonatával szinkronizált felhasználó megpróbálja visszaállítani vagy módosítani a jelszavát a felhőben, a következő műveletekre kerül sor:

1. A rendszer ellenőrzi, hogy a felhasználó milyen típusú jelszóval rendelkezik. Ha a jelszót a helyszínen kezelik:
   * A rendszer ellenőrzi, hogy a visszaírási szolgáltatás működik-e. Ha igen, a felhasználó folytathatja.
   * Ha a visszaírási szolgáltatás leáll, a felhasználó értesítést kap arról, hogy a jelszava jelenleg nem állítható vissza.
1. Ezután a felhasználó átadja a megfelelő hitelesítési kapukat, és eléri a **Jelszó alaphelyzetbe állítása** lapot.
1. A felhasználó kiválaszt egy új jelszót, és megerősíti azt.
1. Amikor a felhasználó a **Submit**(Küldés) lehetőséget választja, a rendszer a visszaírási beállítási folyamat során létrehozott szimmetrikus kulccsal titkosítja az egyszerű szöveges jelszót.
1. A titkosított jelszót egy olyan hasznos adat tartalmazza, amely HTTPS-csatornán keresztül lesz elküldve a bérlőspecifikus Service Bus Relaynek (ez a visszaírási beállítási folyamat során van beállítva). A továbbító védelmét egy véletlenszerűen generált jelszó védi, amelyet csak a helyszíni telepítés tud.
1. Miután az üzenet eléri a Service Bust, a jelszó-visszaállítási végpont automatikusan fel fog ébredni, és azt látja, hogy az alaphelyzetbe állítási kérés függőben van.
1. A szolgáltatás ezután a cloud anchor (felhőhorgony) attribútummal keres a felhasználó után. Ahhoz, hogy ez a keresés sikeres legyen, a következő feltételeknek kell teljesülnie:

   * A felhasználói objektumnak az összekötő AD DS kell lennie.
   * A felhasználói objektumot a megfelelő metaverzum- (MV-) objektumhoz kell kapcsolni.
   * A felhasználói objektumot a megfelelő Azure AD-összekötő objektumhoz kell kapcsolni.
   * A AD DS összekötőobjektum és az MV között található hivatkozásnak a hivatkozáson kell lennie a `Microsoft.InfromADUserAccountEnabled.xxx` szinkronizálási szabálynak.

   Amikor a hívás a felhőből érkezik, a szinkronizációs motor a **cloudAnchor** attribútummal keres az Azure AD-összekötőtér objektumában. Ezután az MV objektumra mutató hivatkozást követi, majd a hivatkozást az MV-objektumra AD DS vissza. Mivel ugyanannak a felhasználónak több AD DS objektuma is lehet, a szinkronizálási motor a hivatkozásra támaszkodva kiválasztja `Microsoft.InfromADUserAccountEnabled.xxx` a megfelelőt.

1. Miután megtalálta a felhasználói fiókot, a rendszer megkísérli a jelszó visszaállítását közvetlenül a megfelelő AD DS erdőben.
1. Ha a jelszó-beállítási művelet sikeres, a rendszer a felhasználótól azt adja meg, hogy a jelszava megváltozott.

   > [!NOTE]
   > Ha a felhasználó jelszó-kivonatát jelszó-szinkronizálással szinkronizálja az Azure AD-be, akkor a helyszíni jelszó-szabályzat gyengébb lehet, mint a felhőbeli jelszó szabályzata. Ebben az esetben a helyszíni szabályzat kényszerítve van. Ez a szabályzat biztosítja, hogy a helyszíni szabályzat kényszerítve legyen a felhőben, függetlenül attól, hogy jelszó-kivonat-szinkronizálást vagy összevonást használ az egyszeri bejelentkezéshez.

1. Ha a jelszó-beállítási művelet sikertelen, egy hiba felszólítja a felhasználót, hogy próbálkozzon újra. A művelet a következő okok miatt meghiúsulhat:
    * A szolgáltatás leállt.
    * A kiválasztott jelszó nem felel meg a szervezet szabályzatának.
    * A felhasználó nem található a helyi AD DS környezetben.

   A hibaüzenetek útmutatást nyújtanak a felhasználóknak, hogy a rendszergazda beavatkozása nélkül megpróbálják megoldani a problémát.

## <a name="password-writeback-security"></a>Jelszóvisszaírás biztonsága

A jelszóvisszaírás egy rendkívül biztonságos szolgáltatás. Az adatok védelme érdekében egy négyszintű biztonsági modell van engedélyezve a következőképpen:

* **Bérlőspecifikus service-bus relay**
   * A szolgáltatás beállításakor egy bérlőspecifikus Service Bus Relay lesz beállítva, amelyet egy véletlenszerűen generált erős jelszó véd, amelyhez a Microsoft soha nem fér hozzá.
* **Zárolt, titkosítási szempontból erős, jelszótitkosítási kulcs**
   * A Service Bus Relay létrehozása után egy erős szimmetrikus kulcs jön létre, amely a jelszó titkosítására használatos, miközben a vezetéken keresztül érkezik. Ez a kulcs csak a vállalat felhőbeli titkos tárolójában található, amelyet erősen zároltak és naplóztak, csakúgy, mint a címtár összes többi jelszava.
* **Iparági szabvány Transport Layer Security (TLS)**
   1. Amikor jelszó-visszaállítási vagy -módosítási művelet történik a felhőben, az egyszerű szöveges jelszót a nyilvános kulccsal titkosítja a rendszer.
   1. A titkosított jelszó egy HTTPS-üzenetbe kerül, amelyet microsoftos TLS/SSL-tanúsítványokkal küld el a Service Bus Relaynek.
   1. Miután az üzenet megérkezik a Service Busba, a helyszíni ügynök felébreszti és hitelesíti magát a Service Buson a korábban létrehozott erős jelszóval.
   1. A helyszíni ügynök felveszi a titkosított üzenetet, és visszafejti azt a titkos kulccsal.
   1. A helyszíni ügynök a SetPassword API-n keresztül próbálja AD DS jelszót. Ez a lépés teszi lehetővé a AD DS jelszó-szabályzat kényszerítését (például az összetettséget, az életkort, az előzményeket és a szűrőket) a felhőben.
* **Üzenetek elévülési szabályzata**
   * Ha az üzenet a Service Busban található, mert a helyszíni szolgáltatás leállt, időkorrekta, és néhány perc múlva el lesz távolítva. Az üzenet időkorrekta és eltávolítása még tovább növeli a biztonságot.

### <a name="password-writeback-encryption-details"></a>Jelszóvisszaírás titkosításának részletei

Miután egy felhasználó új jelszót kér, az alaphelyzetbe állítási kérés több titkosítási lépésen is átesik, mielőtt megérkezik a helyszíni környezetbe. Ezek a titkosítási lépések biztosítják a szolgáltatás maximális megbízhatóságát és biztonságát. Ezek leírása a következő:

1. Jelszótitkosítás **2048** bites RSA-kulccsal: Miután a felhasználó beküld egy jelszót, hogy visszaírható legyen a helyszíni környezetbe, maga a beküldött jelszó egy 2048 bites RSA-kulccsal lesz titkosítva.
1. **Csomagszintű titkosítás AES-GCM-el:** A teljes csomag, a jelszó és a szükséges metaadatok az AES-GCM használatával vannak titkosítva. Ez a titkosítás megakadályozza, hogy a mögöttes Service Bus megtekintsen vagy illetéktelenül módosítson tartalmakat.
1. **Minden kommunikáció TLS/SSL protokollon** keresztül történik: A kapcsolattal Service Bus kommunikáció ssl-/TLS-csatornán történik. Ez a titkosítás védi a tartalmat a jogosulatlan külső felektől.
1. **Automatikus kulcsváltás** hat hónaponként: Minden kulcs hat hónaponként, vagy minden alkalommal le van tiltva, majd újra engedélyezve van a Azure AD Connect a maximális szolgáltatásbiztonság és -biztonság érdekében.

### <a name="password-writeback-bandwidth-usage"></a>Jelszóvisszaírás sávszélesség-használata

A jelszóvisszaírás egy alacsony sávszélességű szolgáltatás, amely csak a következő esetekben küldi vissza a kéréseket a helyszíni ügynöknek:

* Ha a funkció engedélyezve van vagy le van tiltva, a rendszer két üzenetet küld Azure AD Connect.
* A szolgáltatás szívveréseként öt percenként egy üzenetet küld a rendszer, amíg a szolgáltatás fut.
* Új jelszó küldésekor mindig két üzenet lesz elküldve:
   * Az első üzenet a művelet végrehajtásához szükséges kérés.
   * A második üzenet a művelet eredményét tartalmazza, és a következő esetekben lesz elküldve:
      * Minden alkalommal, amikor új jelszót ad be a felhasználó önkiszolgáló jelszó-visszaállítási szolgáltatása során.
      * Minden alkalommal, amikor új jelszót ad be egy felhasználói jelszó-módosítási művelet során.
      * Minden alkalommal, amikor új jelszót ad be egy rendszergazda által kezdeményezett felhasználói jelszó-visszaállítás során (csak az Azure felügyeleti portálról).

#### <a name="message-size-and-bandwidth-considerations"></a>Üzenetmérettel és sávszélességgel kapcsolatos szempontok

A korábban leírt üzenetek mérete általában 1 KB alatt van. Még szélsőséges terhelések esetén is maga a jelszóvisszaírási szolgáltatás használ néhány kilobit/másodperc sávszélességet. Mivel az egyes üzenetek valós időben vannak elküldve, csak akkor, ha egy jelszófrissítési művelet miatt szükség van rá, és mivel az üzenet mérete nagyon kicsi, a visszaírási képesség sávszélesség-használata túl kicsi ahhoz, hogy mérhető hatással legyen.

## <a name="supported-writeback-operations"></a>Támogatott visszaírási műveletek

A jelszavak visszaíratása az alábbi helyzetekben lehet:

* **Támogatott végfelhasználói műveletek**
   * Minden önkiszolgáló végfelhasználói önkéntes jelszóváltozási művelet.
   * Minden végfelhasználói önkiszolgáló kényszerített jelszóváltozási művelet, például jelszó lejárata.
   * Bármely végfelhasználói önkiszolgáló jelszó-visszaállítás, amely a jelszó-visszaállítási [portálról származik.](https://passwordreset.microsoftonline.com)

* **Támogatott rendszergazdai műveletek**
   * Bármely önkiszolgáló rendszergazdai önkéntes jelszóváltozási művelet.
   * Bármely rendszergazdai önkiszolgáló kényszerített jelszóváltozási művelet, például jelszó lejárata.
   * Bármely rendszergazdai önkiszolgáló jelszó-visszaállítás, amely a jelszó-visszaállítási [portálról származik.](https://passwordreset.microsoftonline.com)
   * Bármely rendszergazda által kezdeményezett végfelhasználói jelszó-visszaállítás a [Azure Portal.](https://portal.azure.com)
   * Bármely rendszergazda által kezdeményezett végfelhasználói jelszó-visszaállítás a [Microsoft Graph API-ból.](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http)

## <a name="unsupported-writeback-operations"></a>Nem támogatott visszaírási műveletek

A jelszavak nem írnak vissza semmit a következő helyzetekben:

* **Nem támogatott végfelhasználói műveletek**
   * Minden végfelhasználó visszaállítja a saját jelszavát a PowerShell 1-es, 2-es verziójával vagy a Microsoft Graph API-val.
* **Nem támogatott rendszergazdai műveletek**
   * Bármely rendszergazda által kezdeményezett végfelhasználói jelszó-visszaállítás a PowerShell 1-es, 2-es verziójából vagy a Microsoft Graph API-ból (az [Microsoft Graph API](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http) támogatott).
   * Bármely rendszergazda által kezdeményezett végfelhasználói jelszó-visszaállítás a [Microsoft 365 Felügyeleti központ.](https://admin.microsoft.com)
   * Bármely rendszergazda nem használhatja a jelszó-visszaállítási eszközt a saját jelszavának visszaállításához a jelszóvisszaíráshoz.

> [!WARNING]
> A helyszíni AD DS felügyeleti eszközeiben, például az Active Directory - felhasználók és számítógépek-ban vagy a Active Directory felügyeleti központ előzetes verziójú funkciójaként a "A felhasználónak meg kell változtatnia a jelszót a következő bejelentkezéskor" jelölőnégyzet Azure AD Connect. További információ: [Jelszó kivonatszinkronizálásának megvalósítása Azure AD Connect szinkronizálásával.](../hybrid/how-to-connect-password-hash-synchronization.md)

## <a name="next-steps"></a>Következő lépések

Az SSPR-visszaírás első lépésekhez töltse ki a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az új jelszó önkiszolgáló visszaállításának (SSPR) visszaírásának engedélyezése](./tutorial-enable-sspr-writeback.md)