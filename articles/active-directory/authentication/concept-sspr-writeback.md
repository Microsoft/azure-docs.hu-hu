---
title: Helyszíni jelszó visszaírási önkiszolgáló jelszó-visszaállítással – Azure Active Directory
description: Megtudhatja, hogyan lehet visszaírni a jelszó-módosítási és-visszaállítási eseményeket Azure Active Directory a helyszíni címtár-környezetbe
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
ms.openlocfilehash: 53f416a23dbb47660097c41ada09c8c135434bcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743649"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Hogyan működik az önkiszolgáló jelszó-visszaállítási visszaírási az Azure Active Directory?

Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználók számára, hogy a felhőben visszaállítsa a jelszavukat, de a legtöbb vállalat helyszíni Active Directory Domain Services (AD DS) környezettel is rendelkezik, ahol a felhasználók léteznek. A Password visszaírási egy olyan szolgáltatás, amely [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) lehetővé teszi, hogy a jelszó módosításait a felhőben valós időben lehessen visszaírni egy meglévő helyszíni címtárba. Ebben a konfigurációban, mivel a felhasználók a felhőben a SSPR használatával módosítják vagy alaphelyzetbe állítják a jelszavukat, a frissített jelszavak a helyszíni AD DS-környezetre is visszakerülnek.

> [!IMPORTANT]
> Ez a fogalmi cikk egy rendszergazdát ismerteti, hogy az önkiszolgáló jelszó-visszaállítási visszaírási működése hogyan működik. Ha a végfelhasználó már regisztrálva van az önkiszolgáló jelszó-visszaállításhoz, és vissza kell kérnie a fiókját, lépjen a következőre: https://aka.ms/sspr .
>
> Ha az informatikai csapat nem engedélyezte a saját jelszavának alaphelyzetbe állítását, további segítségért forduljon az ügyfélszolgálathoz.

A jelszó-visszaírási a következő hibrid identitási modelleket használó környezetekben támogatott:

* [Jelszó-kivonat szinkronizálása](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Átmenő hitelesítés](../hybrid/how-to-connect-pta.md)
* [Active Directory összevonási szolgáltatások](../hybrid/how-to-connect-fed-management.md)

A jelszó visszaírási a következő funkciókat biztosítja:

* Helyszíni **Active Directory Domain Services (AD DS) jelszavas szabályzatok kényszerítése**: amikor egy felhasználó visszaállítja a jelszavát, ellenőrzi, hogy az megfelel-e a helyszíni AD DS házirendnek, mielőtt véglegesíti azt az adott könyvtárba. Ez a felülvizsgálat a AD DSban definiált előzmények, összetettség, kor, jelszó-szűrők és egyéb jelszó-korlátozásokat is ellenőrzi.
* **Nulla késleltetésű visszajelzés**: a jelszó visszaírási egy szinkron művelet. A rendszer azonnal értesíti a felhasználókat, ha a jelszavuk nem felel meg a szabályzatnak, vagy bármilyen okból nem lehet alaphelyzetbe állítani vagy módosítani.
* **Támogatja a hozzáférési panel jelszavas módosításait, és Microsoft 365**: Ha az összevont vagy jelszó-kivonatolással szinkronizált felhasználók a lejárt vagy nem lejárt jelszavakat módosítják, a jelszavakat a rendszer visszaírja a ad DSba.
* **Támogatja a jelszó visszaírási, amikor egy rendszergazda visszaállítja azokat a Azure Portal**: Ha a rendszergazda visszaállítja a felhasználó jelszavát a [Azure Portalban](https://portal.azure.com), ha a felhasználó összevont vagy jelszó-kivonattal szinkronizálva van, a rendszer visszaírja a jelszót a helyszíni környezetbe. Ez a funkció jelenleg nem támogatott az Office felügyeleti portálon.
* **Nincs szükség bejövő tűzfalszabályok** használatára: a Password visszaírási egy Azure Service Bus továbbítót használ mögöttes kommunikációs csatornaként. Minden kommunikáció a 443-es porton keresztül kimenő.

> [!NOTE]
> A helyszíni AD-ben található védett csoportokba tartozó rendszergazdai fiókok jelszavas visszaírási is használhatók. A rendszergazdák megváltoztathatják a jelszavukat a felhőben, de nem használhatják a jelszó-visszaállítást az elfelejtett jelszavak alaphelyzetbe állításához. A védett csoportokkal kapcsolatos további információkért lásd: [védett fiókok és csoportok a AD DS](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

A SSPR-visszaírási első lépéseihez hajtsa végre a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: az önkiszolgáló jelszó-visszaállítás (SSPR) visszaírási engedélyezése](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>A jelszóvisszaíró működése

Ha egy összevont vagy jelszó-kivonat szinkronizálta a felhasználót a felhőben tárolt jelszavának visszaállítására vagy módosítására, a következő műveletek történnek:

1. Ellenőrizze, hogy a felhasználó milyen típusú jelszót tartalmaz. Ha a jelszót a helyszínen felügyelik:
   * A rendszer ellenőrzéssel látja el, hogy a visszaírási szolgáltatás működik-e. Ha igen, akkor a felhasználó folytathatja.
   * Ha a visszaírási szolgáltatás nem működik, a rendszer értesíti a felhasználót, hogy a jelszavuk most nem állítható alaphelyzetbe.
1. Ezután a felhasználó átadja a megfelelő hitelesítési kapukat, és eléri a **jelszó alaphelyzetbe állítása** lapot.
1. A felhasználó új jelszót választ, és megerősíti.
1. Ha a felhasználó a **Submit (Küldés**) lehetőséget választja, a rendszer titkosítja az egyszerű szöveges jelszót a visszaírási telepítési folyamata során létrehozott szimmetrikus kulccsal.
1. A titkosított jelszót egy olyan hasznos adat tartalmazza, amely egy HTTPS-csatornán keresztül küldi el a bérlő-specifikus Service Bus-továbbítót (amely a visszaírási telepítési folyamata során van beállítva). Ezt a továbbítót véletlenszerűen generált jelszó védi, amelyet csak a helyszíni telepítés tud.
1. Ha az üzenet eléri a Service Bus-t, a jelszó-visszaállítási végpont automatikusan felébred, és úgy látja, hogy függőben van egy visszaállítási kérelem.
1. A szolgáltatás ezután a Cloud Anchor attribútum használatával keresi a felhasználót. Ahhoz, hogy ez a keresés sikeres legyen, a következő feltételeknek kell teljesülniük:

   * A felhasználói objektumnak léteznie kell a AD DS-összekötő területén.
   * A felhasználói objektumot a megfelelő Metaverse (MV) objektumhoz kell kapcsolni.
   * A felhasználói objektumnak kapcsolódnia kell a megfelelő Azure AD Connector-objektumhoz.
   * Az AD DS Connector objektumból az MV-ra mutató hivatkozáshoz a hivatkozáson a szinkronizálási szabálynak kell tartoznia `Microsoft.InfromADUserAccountEnabled.xxx` .

   Ha a hívás a felhőből érkezik, a Szinkronizáló motor a **cloudAnchor** attribútum használatával keres az Azure ad-összekötő terület objektumát. Ezután a hivatkozás visszakerül az MV objektumra, majd a hivatkozás visszakerül a AD DS objektumra. Mivel több AD DS objektum (több erdő) is lehet ugyanahhoz a felhasználóhoz, a szinkronizálási motor a hivatkozáson alapul, `Microsoft.InfromADUserAccountEnabled.xxx` hogy kiválassza a megfelelőt.

1. A felhasználói fiók megtalálása után a rendszer megkísérli a jelszó alaphelyzetbe állítását közvetlenül a megfelelő AD DS erdőben.
1. Ha a jelszó-megadási művelet sikeres, a felhasználó azt mondja, hogy a jelszava módosult.

   > [!NOTE]
   > Ha a felhasználó jelszavas kivonata szinkronizálva van az Azure AD-val a jelszó-kivonatolási szinkronizálás használatával, fennáll a valószínűsége annak, hogy a helyszíni jelszóházirend gyengébb, mint a felhő jelszavas házirendje. Ebben az esetben a helyszíni házirend érvénybe lép. Ez a szabályzat biztosítja, hogy a helyszíni házirend érvénybe lépjen a felhőben, függetlenül attól, hogy az egyszeri bejelentkezéshez jelszó-kivonatolási szinkronizálást vagy összevonást használ.

1. Ha a jelszó-megadási művelet meghiúsul, a rendszer hibaüzenetet kér a felhasználótól, hogy próbálkozzon újra. A művelet a következő okok miatt sikertelen lehet:
    * A szolgáltatás leállt.
    * A kiválasztott jelszó nem felel meg a szervezet házirendjeinek.
    * Nem található a felhasználó a helyi AD DS környezetben.

   A hibaüzenetek útmutatást nyújtanak a felhasználóknak, hogy rendszergazdai beavatkozás nélkül is fel tudják oldani a megoldást.

## <a name="password-writeback-security"></a>Jelszó visszaírási biztonsága

A jelszó visszaírási egy nagyon biztonságos szolgáltatás. Az adatok védelme érdekében a következő négy rétegű biztonsági modell van engedélyezve:

* **Bérlő-specifikus Service-Bus Relay**
   * A szolgáltatás beállításakor a bérlőre jellemző Service Bus Relay olyan véletlenszerűen generált erős jelszóval védett, amelyet a Microsoft soha nem fér hozzá.
* **Zárolt, titkosítási szempontból erős, jelszó-titkosítási kulcs**
   * A Service Bus-továbbító létrehozása után egy erős szimmetrikus kulcs jön létre, amely a jelszó titkosítására szolgál a huzalon keresztül. Ez a kulcs csak a vállalat titkos tárolójában él a felhőben, amely szigorúan le van zárva és naplózva van, ugyanúgy, mint a címtárban lévő többi jelszó.
* **Iparági szabvány Transport Layer Security (TLS)**
   1. Ha a felhőben új jelszó-visszaállítási vagy módosítási művelet történik, a rendszer a nyilvános kulccsal titkosítja az egyszerű szöveges jelszót.
   1. A titkosított jelszó egy titkosított csatornán küldött HTTPS-üzenetbe kerül, amelyet a Service Bus Relay-hez a Microsoft TLS/SSL-tanúsítványok használatával küld.
   1. Miután az üzenet megérkezik a Service Bus szolgáltatásba, a helyszíni ügynök felébred, és hitelesíti a szolgáltatást a korábban létrehozott erős jelszó használatával.
   1. A helyszíni ügynök felveszi a titkosított üzenetet, és visszafejti azt a titkos kulcs használatával.
   1. A helyszíni ügynök megpróbálja beállítani a jelszót a AD DS SetPassword API-n keresztül. Ez a lépés lehetővé teszi az AD DS helyszíni jelszóházirend (például a bonyolultság, az életkor, az előzmények és a szűrők) kényszerítését a felhőben.
* **Üzenet elévülési szabályzatai**
   * Ha az üzenet a Service Bus szolgáltatásban található, mert a helyszíni szolgáltatás nem érhető el, időtúllépést okoz, és néhány perc múlva el lesz távolítva. Az üzenet időtúllépése és eltávolítása még tovább növeli a biztonságot.

### <a name="password-writeback-encryption-details"></a>Jelszó visszaírási-titkosítás részletei

Miután egy felhasználó elküldte a jelszó alaphelyzetbe állítását, az alaphelyzetbe állítási kérelem több titkosítási lépésen megy keresztül, mielőtt megérkezik a helyszíni környezetbe. Ezek a titkosítási lépések biztosítják a szolgáltatás megbízhatóságának és biztonságának maximális biztonságát. Ezek a következőképpen vannak leírva:

1. **Jelszó-titkosítás 2048 bites RSA-kulccsal**: miután egy felhasználó elküldte a jelszót a helyszíni rendszerbe való visszaíráshoz, a beküldött jelszót egy 2048 bites RSA-kulccsal titkosítja a rendszer.
1. **Csomag szintű TITKOSÍTÁS AES-GCM**: a teljes csomag, a jelszó és a szükséges metaadatok az AES-GCM használatával titkosítva vannak. Ez a titkosítás megakadályozza, hogy bárki közvetlenül hozzáférhessen a mögöttes Service Bus-csatornához a tartalom megtekintésével vagy módosításával.
1. **Minden kommunikáció a TLS/SSL protokollon keresztül** történik: az Service Bus összes kommunikációja egy SSL/TLS-csatornán történik. Ez a titkosítás védi a jogosulatlan harmadik féltől származó tartalmat.
1. **Automatikus Key rollover** hathavonta: minden, hat havonta átadott kulcs, vagy a jelszó visszaírási le van tiltva, majd újból engedélyezve Azure ad Connecton, a maximális biztonság és biztonság érdekében.

### <a name="password-writeback-bandwidth-usage"></a>Jelszó-visszaírási sávszélesség-használat

A jelszó visszaírási egy alacsony sávszélességű szolgáltatás, amely csak a következő esetekben küld vissza kéréseket a helyszíni ügynöknek:

* A rendszer két üzenetet továbbít, ha a funkció engedélyezve van vagy le van tiltva Azure AD Connecton keresztül.
* Egy üzenet küldése a szolgáltatás szívverése után öt percenként történik, amíg a szolgáltatás fut.
* Minden alkalommal, amikor új jelszó érkezik, a rendszer két üzenetet küld:
   * Az első üzenet egy kérelem a művelet végrehajtásához.
   * A második üzenet tartalmazza a művelet eredményét, és a következő esetekben küldi el a rendszer:
      * Minden alkalommal, amikor új jelszót küld a rendszer a felhasználói önkiszolgáló jelszó-visszaállítás során.
      * Minden alkalommal, amikor új jelszót küldenek egy felhasználói jelszó módosítására szolgáló művelet során.
      * Minden alkalommal, amikor új jelszót küldenek egy rendszergazda által kezdeményezett felhasználói jelszó-visszaállítás során (csak az Azure felügyeleti portálról).

#### <a name="message-size-and-bandwidth-considerations"></a>Az üzenetek mérete és a sávszélességgel kapcsolatos megfontolások

A korábban leírt üzenetek mérete általában 1 KB. A jelszó visszaírási szolgáltatás akár extrém terhelések mellett is igénybe vesz néhány kilobit/másodpercet. Mivel az egyes üzeneteket valós időben küldi el a rendszer, csak akkor, ha a jelszó-frissítési művelet megköveteli, és mivel az üzenet mérete igen kicsi, a visszaírási képesség sávszélesség-használata túl kicsi ahhoz, hogy mérhető hatást lehessen gyakorolni.

## <a name="supported-writeback-operations"></a>Támogatott visszaírási-műveletek

A jelszavakat a következő helyzetekben kell visszaírni:

* **Támogatott végfelhasználói műveletek**
   * Minden végfelhasználói önkiszolgáló önkéntes jelszó-módosítási művelet.
   * Bármilyen végfelhasználói önkiszolgáló kényszerített jelszó-módosítási művelet, például a jelszó lejárata.
   * A [jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com)származó összes végfelhasználói önkiszolgáló jelszó-visszaállítás.

* **Támogatott rendszergazdai műveletek**
   * Bármilyen rendszergazdai önkiszolgáló önkéntes jelszó-módosítási művelet.
   * Minden rendszergazda önkiszolgáló kényszerített jelszó-módosítási művelet, például a jelszó lejárata.
   * A jelszó- [visszaállítási portálról](https://passwordreset.microsoftonline.com)származó rendszergazdai önkiszolgáló jelszó-visszaállítás.
   * Minden rendszergazda által kezdeményezett végfelhasználói jelszó alaphelyzetbe állítása a [Azure Portal](https://portal.azure.com).
   * A rendszergazda által kezdeményezett végfelhasználói jelszó alaphelyzetbe állítása a [Microsoft Graph API Beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta)-ból.

## <a name="unsupported-writeback-operations"></a>Nem támogatott visszaírási-műveletek

A jelszavakat a következő helyzetekben nem írja vissza a rendszer:

* **Nem támogatott végfelhasználói műveletek**
   * Bármely végfelhasználó alaphelyzetbe állítja a saját jelszavukat a PowerShell 1-es vagy 2-es verziójának használatával, vagy a Microsoft Graph API-val.
* **Nem támogatott rendszergazdai műveletek**
   * Bármely rendszergazda által kezdeményezett végfelhasználói jelszó alaphelyzetbe állítása a PowerShell 1-es verziójáról, a 2-es verzióról vagy a Microsoft Graph API-ról (a [Microsoft Graph API Beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta) támogatott).
   * A rendszergazda által kezdeményezett végfelhasználói jelszó alaphelyzetbe állítása a [Microsoft 365 felügyeleti központból](https://admin.microsoft.com).
   * Bármely rendszergazda nem használhatja a jelszó-visszaállítási eszközt a jelszó-visszaírási vonatkozó saját jelszó visszaállításához.

> [!WARNING]
> A (z) "a következő bejelentkezéskor a felhasználónak meg kell változtatnia a jelszót" jelölőnégyzet használata a helyszíni AD DS felügyeleti eszközök, például a Active Directory felhasználók és számítógépek, vagy a Active Directory felügyeleti központ előzetes funkciójaként támogatott Azure AD Connect. További információ: jelszó- [kivonatolási szinkronizálás implementálása Azure ad Connect szinkronizálással](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Következő lépések

A SSPR-visszaírási első lépéseihez hajtsa végre a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: az önkiszolgáló jelszó-visszaállítás (SSPR) visszaírási engedélyezése](./tutorial-enable-sspr-writeback.md)