---
title: 'Azure AD Connect: felhasználói bejelentkezés | Microsoft Docs'
description: Felhasználói bejelentkezés Azure AD Connect egyéni beállításokhoz.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3365a58a0c667ca55b74a5120cdd7a78ad0abc79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997783"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Felhasználói bejelentkezési beállítások Azure AD Connect
Azure Active Directory (Azure AD) kapcsolat lehetővé teszi, hogy a felhasználók ugyanazzal a jelszóval jelentkezzenek be a felhőbe és a helyszíni erőforrásokra. Ez a cikk az egyes identitási modellekkel kapcsolatos főbb fogalmakat ismerteti, így kiválaszthatja az Azure AD-ba való bejelentkezéshez használni kívánt identitást.

Ha már ismeri az Azure AD Identity modelt, és szeretne többet megtudni egy adott módszerről, tekintse meg a megfelelő hivatkozást:

* [Jelszó-kivonatolási szinkronizálás](#password-hash-synchronization) [zökkenőmentes egyszeri bejelentkezéssel (SSO)](how-to-connect-sso.md)
* [Átmenő hitelesítés](how-to-connect-pta.md) [zökkenőmentes egyszeri bejelentkezéssel (SSO)](how-to-connect-sso.md)
* [Összevont egyszeri bejelentkezés (Active Directory összevonási szolgáltatások (AD FS) (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Összevonás a PingFederate-tel](#federation-with-pingfederate)

> [!NOTE] 
> Fontos megjegyezni, hogy az Azure AD-összevonás konfigurálásával megbízhatósági kapcsolatot létesít az Azure AD-bérlő és az összevont tartományok között. Ezzel a megbízhatósággal összevont tartomány felhasználói hozzáférhetnek majd a bérlőn belüli Azure AD felhőalapú erőforrásokhoz.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>A felhasználó bejelentkezési módszerének kiválasztása a szervezet számára
Azure AD Connect megvalósításának első döntése kiválasztja, hogy a felhasználók milyen hitelesítési módszert fognak használni a bejelentkezéshez. Fontos, hogy a megfelelő módszert válassza ki, amely megfelel a szervezet biztonsági és speciális követelményeinek. A hitelesítés kritikus fontosságú, mivel a felhasználó identitásait fogja érvényesíteni a felhőben lévő alkalmazásokhoz és adatforrásokhoz való hozzáféréshez. A megfelelő hitelesítési módszer kiválasztásához figyelembe kell vennie az időt, a meglévő infrastruktúrát, az összetettséget és a választott megvalósítás költségeit. Ezek a tényezők minden szervezet esetében eltérőek, és idővel változhatnak.

Az Azure AD a következő hitelesítési módszereket támogatja: 

* **Felhőalapú hitelesítés** – ha ezt a hitelesítési módszert választja, az Azure ad kezeli a felhasználói bejelentkezés hitelesítési folyamatát. A felhőalapú hitelesítés használatával két lehetőség közül választhat: 
   * **Jelszó-kivonat szinkronizálása (PHS)** – a jelszó-kivonatolási szinkronizálás lehetővé teszi, hogy a felhasználók ugyanazt a felhasználónevet és jelszót használják, mint a helyszínen, anélkül, hogy további infrastruktúrát kellene üzembe helyeznie Azure ad Connecton kívül. 
   * **Átmenő hitelesítés (PTA)** – ez a beállítás hasonló a jelszó-kivonatolási szinkronizáláshoz, de egyszerű jelszó-érvényesítést biztosít a helyszíni szoftveres ügynökökkel olyan szervezetek számára, amelyek erős biztonsági és megfelelőségi szabályzatokkal rendelkeznek.
* **Összevont hitelesítés** – ha ezt a hitelesítési módszert választja, az Azure ad kikapcsolja a hitelesítési folyamatot egy különálló megbízható hitelesítési rendszerre, például AD FS vagy egy harmadik féltől származó összevonási rendszerre, hogy érvényesítse a felhasználó bejelentkezését. 

A legtöbb szervezet számára, amely csak a felhasználói bejelentkezést szeretné engedélyezni Microsoft 365, SaaS-alkalmazásokhoz és más Azure AD-alapú erőforrásokhoz, javasoljuk, hogy az alapértelmezett jelszó-kivonatoló szinkronizálási beállítást.
 
A hitelesítési módszer kiválasztásával kapcsolatos részletes információkért lásd: [a megfelelő hitelesítési módszer kiválasztása a Azure Active Directory Hybrid Identity megoldáshoz](./choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Jelszókivonat szinkronizálása
A jelszó-kivonatolási szinkronizálással a felhasználói jelszavak kivonatait a helyszíni Active Directoryról az Azure AD-be szinkronizálja a rendszer. Ha a jelszavakat módosítják vagy alaphelyzetbe állítják, a rendszer azonnal szinkronizálja az új jelszó-kivonatokat az Azure AD-be, hogy a felhasználók mindig ugyanazt a jelszót használják a felhőalapú erőforrásokhoz és a helyszíni erőforrásokhoz. A jelszavakat a rendszer soha nem továbbítja az Azure AD-nek, vagy az Azure AD-ben tárolja tiszta szövegben. A jelszó-kivonatolási szinkronizálást és a jelszó-visszaírást is használhatja az önkiszolgáló jelszó-visszaállítás engedélyezéséhez az Azure AD-ben.

Emellett engedélyezheti a [zökkenőmentes egyszeri bejelentkezést](how-to-connect-sso.md) a felhasználók számára a vállalati hálózaton lévő, tartományhoz csatlakoztatott gépeken. Az egyszeri bejelentkezés lehetővé teszi, hogy a felhasználóknak csak a felhasználónevet kell megadniuk ahhoz, hogy biztonságosan hozzáférhessenek a felhőalapú erőforrásokhoz.

![Jelszókivonat szinkronizálása](./media/plan-connect-user-signin/passwordhash.png)

További információkért lásd a jelszó- [kivonatolás szinkronizálása](how-to-connect-password-hash-synchronization.md) című cikket.

### <a name="pass-through-authentication"></a>Átmenő hitelesítés
Átmenő hitelesítés esetén a rendszer ellenőrzi a felhasználó jelszavát a helyszíni Active Directory vezérlőn. A jelszónak semmilyen formában nem kell megjelenni az Azure AD-ben. Ez lehetővé teszi, hogy a helyszíni házirendek, például a bejelentkezési óra korlátozásai a Cloud Services hitelesítése során legyenek kiértékelve.

Az átmenő hitelesítés egy egyszerű ügynököt használ egy Windows Server 2012 R2 rendszerű, tartományhoz csatlakoztatott gépen a helyszíni környezetben. Ez az ügynök a jelszó-ellenőrzési kérelmeket figyeli. Nem igényli, hogy a bejövő portok nyitva legyenek az internethez.

Emellett engedélyezheti az egyszeri bejelentkezést is a felhasználók számára a vállalati hálózaton lévő, tartományhoz csatlakoztatott gépeken. Az egyszeri bejelentkezés lehetővé teszi, hogy a felhasználóknak csak a felhasználónevet kell megadniuk ahhoz, hogy biztonságosan hozzáférhessenek a felhőalapú erőforrásokhoz.
![Átmenő hitelesítés](./media/plan-connect-user-signin/pta.png)

További információkért lásd:
- [Átmenő hitelesítés](how-to-connect-pta.md)
- [Egyszeri bejelentkezés](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Összevonás, amely egy új vagy meglévő farmot használ AD FS a Windows Server 2012 R2 rendszerben
Az összevont bejelentkezéssel a felhasználók bejelentkezhetnek az Azure AD-alapú szolgáltatásba a helyszíni jelszavaik használatával. Bár a vállalati hálózaton vannak, még nem kell megadniuk a jelszavukat. A AD FS összevonásával új vagy meglévő farmokat telepíthet AD FS a Windows Server 2012 R2 rendszerben. Ha egy meglévő Farm megadását választja, Azure AD Connect konfigurálja a farm és az Azure AD közötti megbízhatósági kapcsolatot, hogy a felhasználók be tudják jelentkezni.

<center>

![Összevonás AD FS a Windows Server 2012 R2-ben](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Összevonás üzembe helyezése AD FS a Windows Server 2012 R2-ben

Ha új farmot telepít, a következőkre lesz szüksége:

* Az összevonási kiszolgáló Windows Server 2012 R2-kiszolgálója.
* Egy Windows Server 2012 R2-kiszolgáló a webalkalmazás-proxyhoz.
* . Pfx-fájl, amely egy TLS/SSL-tanúsítvánnyal rendelkezik a kívánt összevonási szolgáltatás nevéhez. Például: fs.contoso.com.

Ha új farmot telepít, vagy egy meglévő farmot használ, a következőkre lesz szüksége:

* Helyi rendszergazdai hitelesítő adatok az összevonási kiszolgálókon.
* Helyi rendszergazdai hitelesítő adatok bármely munkacsoport-kiszolgálón (nincs tartományhoz csatlakoztatva), amelyre telepíteni kívánja a webalkalmazás-proxy szerepkört a alkalmazásban.
* Az a számítógép, amelyen a varázslót futtatja, hogy csatlakozni tudjon bármely más géphez, amelyet AD FS vagy webalkalmazás-proxyn szeretne telepíteni a Rendszerfelügyeleti webszolgáltatások használatával.

További információ: [az egyszeri bejelentkezés konfigurálása ad FSsal](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Összevonás a PingFederate-tel
Az összevont bejelentkezéssel a felhasználók bejelentkezhetnek az Azure AD-alapú szolgáltatásba a helyszíni jelszavaik használatával. Bár a vállalati hálózaton vannak, még nem kell megadniuk a jelszavukat.

További információ a PingFederate konfigurálásáról a Azure Active Directory használatával: a [PingFederate integrációja Azure Active Directory és az Office 365](https://www.pingidentity.com/AzureADConnect)

A Azure AD Connect PingFederate használatával történő beállításával kapcsolatos információkért lásd: [Azure ad Connect egyéni telepítés](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Jelentkezzen be AD FS vagy egy harmadik féltől származó megoldás korábbi verziójának használatával
Ha már konfigurálta a Felhőbeli bejelentkezést AD FS egy korábbi verziójával (például AD FS 2,0) vagy egy harmadik féltől származó összevonási szolgáltatóval, kihagyhatja a felhasználói bejelentkezési konfigurációt a Azure AD Connect használatával. Ez lehetővé teszi a Azure AD Connect legújabb szinkronizálásának és egyéb képességeinek beszerzését, miközben továbbra is meglévő megoldását használja a bejelentkezéshez.

További információ: az [Azure ad harmadik féltől származó összevonási kompatibilitási listája](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Felhasználói bejelentkezési név és egyszerű Felhasználónév
### <a name="understanding-user-principal-name"></a>Az egyszerű felhasználónevek ismertetése
Active Directory az alapértelmezett egyszerű felhasználónév (UPN) utótag annak a tartománynak a DNS-neve, amelyben a felhasználói fiókot létrehozták. A legtöbb esetben ez az a tartománynév, amely az interneten vállalati tartományként van regisztrálva. Azonban Active Directory tartományok és megbízhatósági kapcsolatok használatával további UPN-utótagokat adhat hozzá.

A felhasználó UPN-je formátuma username@domain . Például egy "contoso.com" nevű Active Directory tartományhoz a János nevű felhasználó rendelkezhet az UPN-vel ( john@contoso.com ""). A felhasználó egyszerű felhasználóneve a 822-es RFC-dokumentumon alapul. Bár az UPN és az e-mail ugyanazzal a formátummal rendelkezik, előfordulhat, hogy a felhasználó UPN-értéke nem egyezik meg a felhasználó e-mail-címével.

### <a name="user-principal-name-in-azure-ad"></a>Egyszerű felhasználónév az Azure AD-ben
A Azure AD Connect varázsló a userPrincipalName attribútumot használja, vagy lehetővé teszi, hogy az Azure AD-ben a helyszínen használt egyszerű felhasználónevet (egyéni telepítésben) adja meg. Ez az az érték, amelyet a rendszer az Azure AD-be való bejelentkezéshez használ. Ha a userPrincipalName attribútum értéke nem felel meg egy ellenőrzött tartománynak az Azure AD-ben, akkor az Azure AD lecseréli egy alapértelmezett. onmicrosoft.com értékre.

Azure Active Directory minden könyvtára egy beépített tartománynévvel rendelkezik, amelynek formátuma contoso.onmicrosoft.com, amely lehetővé teszi az Azure vagy más Microsoft-szolgáltatások használatának megkezdését. Egyéni tartományok használatával javíthatja és egyszerűsítheti a bejelentkezési élményt. További információ az Azure AD egyéni tartománynevéről és a tartományok ellenőrzéséről: [Egyéni tartománynév hozzáadása a Azure Active Directoryhoz](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Azure AD-bejelentkezés konfigurálása
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD bejelentkezési konfiguráció Azure AD Connect
Az Azure AD bejelentkezési élmény attól függ, hogy az Azure AD képes-e az Azure ad-címtárban ellenőrzött egyéni tartományokra szinkronizált felhasználó egyszerű felhasználónevének utótagját megfeleltetni. A Azure AD Connect segítséget nyújt az Azure AD bejelentkezési beállításainak konfigurálásában, így a felhőben a felhasználó bejelentkezési élménye hasonló a helyszíni környezethez.

Azure AD Connect listázza a tartományokhoz definiált UPN-utótagokat, és az Azure AD-ben megpróbálják egyeztetni őket egy egyéni tartománnyal. Ezt követően segítséget nyújt a szükséges műveletek elvégzéséhez.
Az Azure AD bejelentkezési oldala felsorolja a helyszíni Active Directoryhoz definiált UPN-utótagokat, és megjeleníti a megfelelő állapotot az egyes utótagok alapján. Az állapot értéke a következők egyike lehet:

| Állam | Leírás | Beavatkozás szükséges |
|:--- |:--- |:--- |
| Ellenőrzött |Azure AD Connect talált egy megfelelő ellenőrzött tartományt az Azure AD-ben. A tartomány összes felhasználója a helyszíni hitelesítő adataival jelentkezhet be. |Nincs szükség beavatkozásra. |
| Nincs ellenőrizve |Azure AD Connect a megfelelő egyéni tartományt találta az Azure AD-ben, de nem ellenőrzi. A tartomány felhasználóinak UPN-utótagja a szinkronizálás után az alapértelmezett. onmicrosoft.com utótagra változik, ha a tartomány nincs ellenőrizve. | [Ellenőrizze az egyéni tartományt az Azure AD-ben.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Nincs hozzáadva |Azure AD Connect nem talált olyan egyéni tartományt, amely megfelel az UPN-utótagnak. A tartomány felhasználóinak UPN-utótagja az alapértelmezett. onmicrosoft.com utótagra változik, ha a tartomány nincs hozzáadva és ellenőrizve az Azure-ban. | [Adjon hozzá és ellenőrizzen egy egyéni tartományt, amely megfelel az UPN-utótagnak.](../fundamentals/add-custom-domain.md) |

Az Azure AD bejelentkezési oldala felsorolja a helyszíni Active Directoryhoz definiált UPN-utótagokat, valamint az Azure AD-beli megfelelő egyéni tartományt az aktuális ellenőrzési állapottal. Egyéni telepítés esetén mostantól kiválaszthatja az egyszerű felhasználónév attribútumát az **Azure ad bejelentkezési** oldalán.

![Az Azure AD bejelentkezési lapja](./media/plan-connect-user-signin/custom_azure_sign_in.png)

A frissítés gombra kattintva újra beolvashatja az egyéni tartományok legújabb állapotát az Azure AD-ből.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Az egyszerű felhasználónévhez tartozó attribútum kiválasztása az Azure AD-ben
A userPrincipalName attribútum azt az attribútumot használja, amelyet a felhasználók az Azure AD-be és Microsoft 365ba való bejelentkezéskor használnak. Ellenőrizze az Azure AD-ben használt tartományokat (más néven UPN-utótagokat) a felhasználók szinkronizálása előtt.

Határozottan javasoljuk, hogy tartsa meg az alapértelmezett attribútumot a userPrincipalName. Ha ez az attribútum nem irányítható, és nem ellenőrizhető, akkor lehetséges, hogy egy másik attribútumot (például e-mailt) választ a bejelentkezési azonosítót birtokló attribútumként. Ez más néven a másodlagos azonosító. A másodlagos azonosító attribútum értékének az RFC 822 szabványt kell követnie. Bejelentkezési megoldásként használhat egy alternatív azonosítót is a jelszó egyszeri bejelentkezéssel és az összevonási egyszeri bejelentkezéssel.

> [!NOTE]
> Egy másik azonosító használata nem kompatibilis az összes Microsoft 365 munkaterheléssel. További információt a [Másodlagos bejelentkezési azonosító beállítása](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) című cikkben talál.
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Különböző egyéni tartományi állapotok és azok hatása az Azure bejelentkezési felületére
Nagyon fontos tisztában lenni az Azure AD-címtárban lévő egyéni tartományi állapotok és a helyszínen definiált UPN-utótagok közötti kapcsolattal. Ismerkedjen meg a lehetséges Azure-bejelentkezési élményekkel, amikor Azure AD Connect használatával állítja be a szinkronizálást.

A következő információk alapján feltételezzük, hogy az UPN-utótag contoso.com van szó, amelyet a helyszíni címtárban használ az egyszerű felhasználónév részeként – például user@contoso.com .

###### <a name="express-settingspassword-hash-synchronization"></a>Expressz beállítások/jelszó kivonatának szinkronizálása

| Állapot | A felhasználói Azure bejelentkezési felületének hatása |
|:---:|:--- |
| Nincs hozzáadva |Ebben az esetben a contoso.com egyéni tartománya nem lett hozzáadva az Azure AD-címtárban. Azok a felhasználók, akik az utótaggal rendelkeznek a helyszíni UPN-vel, @contoso.com nem fogják tudni használni a helyszíni UPN-t az Azure-ba való bejelentkezéshez. Ehelyett egy új UPN-t kell használniuk, amelyet az Azure AD az alapértelmezett Azure AD-címtár utótagjának hozzáadásával biztosít számukra. Ha például a felhasználókat az Azure AD-címtár azurecontoso.onmicrosoft.com szinkronizálja, akkor a helyszíni felhasználó számára a rendszer UPN-t user@contoso.com kap user@azurecontoso.onmicrosoft.com . |
| Nincs ellenőrizve |Ebben az esetben az Azure AD-címtárban hozzáadott egyéni tartomány contoso.com. Azonban még nincs ellenőrizve. Ha a felhasználókat a tartomány ellenőrzése nélkül szinkronizálja, akkor a felhasználók az Azure AD-ben egy új UPN-t kapnak, akárcsak a "nincs hozzáadott" forgatókönyvben. |
| Ellenőrzött |Ebben az esetben az UPN-utótaghoz az Azure AD-ben már hozzáadott és ellenőrzött egyéni tartományi contoso.com van. A felhasználók az Azure AD-vel való szinkronizálás után használhatják a helyszíni egyszerű felhasználónevet, például az Azure-ba való user@contoso.com bejelentkezéshez. |

###### <a name="ad-fs-federation"></a>AD FS-összevonás
Nem hozható létre összevonás az alapértelmezett. onmicrosoft.com tartománnyal az Azure AD-ben vagy egy nem ellenőrzött egyéni tartományban az Azure AD-ben. Ha a Azure AD Connect varázslót futtatja, ha nem ellenőrzött tartományt választ a alkalmazással való összevonás létrehozásához, akkor Azure AD Connect megkérdezi a szükséges rekordokat, amelyek létrehozásához a DNS-t futtatja a tartomány számára. További információ: [az összevonás számára kijelölt Azure ad-tartomány ellenőrzése](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Ha a felhasználói bejelentkezés lehetőséget választotta a **ad FSval való összevonáshoz**, akkor az Azure ad-ben való összevonás folytatásához egyéni tartománnyal kell rendelkeznie. A vitánk során ez azt jelenti, hogy az Azure AD-címtárban hozzá kell adni egy egyéni tartományi contoso.com.

| Állapot | A felhasználói Azure bejelentkezési felületének hatása |
|:---:|:--- |
| Nincs hozzáadva |Ebben az esetben Azure AD Connect nem talált egyező egyéni tartományt az UPN-utótag contoso.com az Azure AD-címtárban. Egyéni tartományi contoso.com kell hozzáadnia, ha a felhasználóknak a helyszíni UPN-sel (például) AD FS használatával kell bejelentkezniük user@contoso.com . |
| Nincs ellenőrizve |Ebben az esetben Azure AD Connect megkéri a megfelelő részleteket arról, hogyan ellenőrizheti a tartományt egy későbbi időpontban. |
| Ellenőrzött |Ebben az esetben további művelet nélkül megtekintheti a konfigurációt. |

## <a name="changing-the-user-sign-in-method"></a>A felhasználói bejelentkezési mód módosítása
A felhasználói bejelentkezési módszert megváltoztathatja az összevonás, a jelszó-kivonatolás vagy az átmenő hitelesítés használatával, ha a Azure AD Connect kezdeti konfigurálása után a varázslóval Azure AD Connect elérhető feladatok közül választhat. Futtassa újra a Azure AD Connect varázslót, és megtekintheti az elvégezhető feladatok listáját. Válassza a **felhasználói bejelentkezés módosítása** lehetőséget a feladatok listájában.

![Felhasználói bejelentkezés módosítása](./media/plan-connect-user-signin/changeusersignin.png)

A következő lapon meg kell adnia az Azure AD hitelesítő adatait.

![Képernyőkép, amely megmutatja, hol kell beírnia az Azure AD hitelesítő adatait.](./media/plan-connect-user-signin/changeusersignin2.png)

A **felhasználói bejelentkezés** lapon válassza ki a kívánt felhasználói bejelentkezést.

![Csatlakozás az Azure AD szolgáltatáshoz](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Ha csak ideiglenes kapcsolót használ a jelszó-kivonatolási szinkronizáláshoz, jelölje be a **ne alakítsa át a felhasználói fiókokat** jelölőnégyzetet. Ha nem ellenőrzi a lehetőséget, az egyes felhasználókat összevontra konvertálja, és több óráig is eltarthat.
>
>

## <a name="next-steps"></a>Következő lépések
- További információ a helyszíni [identitások és a Azure Active Directory integrálásáról](whatis-hybrid-identity.md).
- További információ a [Azure ad Connect kialakításával kapcsolatos fogalmakról](plan-connect-design-concepts.md).