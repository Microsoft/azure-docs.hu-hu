---
title: Azure Active Directory átmenő hitelesítés biztonságának részletes bemutatása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan védi a helyszíni fiókokat a Azure Active Directory (Azure AD) átmenő hitelesítéssel
services: active-directory
keywords: Azure AD Connect átmenő hitelesítés, telepítési Active Directory, szükséges összetevők az Azure AD-hez, egyszeri bejelentkezéshez, egyszeri bejelentkezéshez
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a73c2b1be4b17136ba19e7efb71c2b21359fdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280145"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory átmenő hitelesítés biztonsági mélye

Ez a cikk részletesebb leírást nyújt a Azure Active Directory (Azure AD) átmenő hitelesítés működéséről. A szolgáltatás biztonsági szempontjaira koncentrál. Ez a cikk a biztonsági és informatikai rendszergazdák, a megfelelőségi és biztonsági tisztviselők, valamint más informatikai szakemberek számára készült, akik felelősek az informatikai biztonságért és a megfelelőségért a kis-és közepes méretű szervezeteknél vagy nagyvállalatokban.

A megoldandó témakörök a következők:
- Részletes technikai információk a hitelesítési ügynökök telepítéséhez és regisztrálásához.
- Részletes technikai információk a jelszavak titkosításáról a felhasználói bejelentkezés során.
- A helyszíni hitelesítési ügynökök és az Azure AD közötti csatornák biztonsága.
- Részletes technikai információk a hitelesítési ügynökök működésének megőrzéséről.
- Egyéb biztonsággal kapcsolatos témakörök.

## <a name="key-security-capabilities"></a>Kulcsfontosságú biztonsági képességek

Ezek a funkció legfontosabb biztonsági szempontjai:
- A szolgáltatás olyan biztonságos, több-bérlős architektúrára épül, amely a bérlők közötti bejelentkezési kérelmek elkülönítését biztosítja.
- A helyszíni jelszavakat a rendszer soha nem tárolja semmilyen formában a felhőben.
- Azok a helyszíni hitelesítési ügynökök, amelyek figyelik a-t, és válaszolnak arra, hogy a jelszó-ellenőrzési kérelmek csak kimenő kapcsolatokat hajtanak végre a hálózaton belül. Ezeket a hitelesítési ügynököket nem szükséges a peremhálózaton (DMZ) telepíteni. Ajánlott eljárásként a hitelesítési ügynököket futtató összes kiszolgálót a 0. rétegű rendszerként kell kezelni (lásd: [hivatkozás](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Csak a standard portokat (80 és 443) használja a rendszer a hitelesítési ügynököktől az Azure AD felé irányuló kimenő kommunikációhoz. Nem kell megnyitnia a bejövő portokat a tűzfalon. 
  - Az 443-es port minden hitelesített kimenő kommunikációhoz használatos.
  - Az 80-es portot csak a visszavont tanúsítványok listáinak (CRL-ek) letöltésére használja a rendszer, hogy a szolgáltatás által használt egyik tanúsítványt sem vonták vissza.
  - A hálózati követelmények teljes listájáért lásd [: Azure Active Directory átmenő hitelesítés:](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)gyors útmutató.
- A felhasználók által a bejelentkezés során megadott jelszavak titkosítva vannak a felhőben, mielőtt a helyszíni hitelesítési ügynökök elfogadják őket a Active Directory való érvényesítéshez.
- Az Azure AD és a helyszíni hitelesítési ügynök közötti HTTPS-csatornát kölcsönös hitelesítéssel védi a szolgáltatás.
- Az [Azure ad feltételes hozzáférési szabályzatok](../conditional-access/overview.md), például a multi-Factor Authentication (MFA) és az [örökölt hitelesítés blokkolásával](../conditional-access/concept-conditional-access-conditions.md) , valamint a [találgatásos jelszó elleni támadások kiszűrésével](../authentication/howto-password-smart-lockout.md)védi a felhasználói fiókokat.

## <a name="components-involved"></a>Érintett összetevők

Az Azure AD működésével, szolgáltatásával és adatbiztonságával kapcsolatos általános információkért tekintse meg az [adatvédelmi központot](https://azure.microsoft.com/support/trust-center/). A felhasználói bejelentkezéshez az átmenő hitelesítés használata esetén a következő összetevőket kell használni:
- **Azure ad STS**: egy állapot nélküli biztonsági jogkivonat-szolgáltatás (STS), amely a bejelentkezési kérések feldolgozását és a biztonsági jogkivonatok kiírását kéri a felhasználók böngészői, ügyfelei vagy szolgáltatásai számára.
- **Azure Service Bus**: felhőalapú kommunikációt tesz lehetővé vállalati üzenetkezeléssel és közvetíti a kommunikációt, amely segítséget nyújt a helyszíni megoldások felhővel való összekapcsolásához.
- **Azure ad Connect hitelesítési ügynök**: egy helyszíni összetevő, amely figyeli és válaszol a jelszó-ellenőrzési kérelmekre.
- **Azure SQL Database**: a bérlő hitelesítési ügynökével kapcsolatos információkat tartalmaz, beleértve a metaadatokat és a titkosítási kulcsokat.
- **Active Directory**: helyszíni Active Directory, ahol a felhasználói fiókjai és a jelszavuk tárolása történik.

## <a name="installation-and-registration-of-the-authentication-agents"></a>A hitelesítési ügynökök telepítése és regisztrálása

A hitelesítési ügynökök telepítése és regisztrálása az Azure AD-ben a következők valamelyike esetén:
   - [Átmenő hitelesítés engedélyezése Azure AD Connect](./how-to-connect-pta-quick-start.md#step-2-enable-the-feature)
   - [További hitelesítési ügynökök hozzáadása a bejelentkezési kérések magas rendelkezésre állásának biztosítása érdekében](./how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) 
   
A hitelesítési ügynök működésének beszerzése három fő fázisból áll:

1. Hitelesítési ügynök telepítése
2. Hitelesítési ügynök regisztrációja
3. Hitelesítési ügynök inicializálása

A következő szakaszok részletesen tárgyalják ezeket a szakaszokat.

### <a name="authentication-agent-installation"></a>Hitelesítési ügynök telepítése

Egy helyszíni kiszolgálón csak a globális rendszergazdák telepíthetnek hitelesítési ügynököt (Azure AD Connect vagy önálló használatával). A telepítő két új bejegyzést hoz létre a **Vezérlőpult**  >  **programok**  >  **programok és szolgáltatások** listájához:
- Maga a hitelesítési ügynök alkalmazása. Ez az alkalmazás [NetworkService](/windows/win32/services/networkservice-account) jogosultságokkal fut.
- A hitelesítési ügynök automatikus frissítéséhez használt Updater alkalmazás. Ez az alkalmazás [LocalSystem](/windows/win32/services/localsystem-account) jogosultságokkal fut.

>[!IMPORTANT]
>Biztonsági szempontból a rendszergazdáknak az PTA ESP-ügynököt futtató kiszolgálót kell kezelnie, mintha tartományvezérlővé tennék.  Az PTA-ügynök kiszolgálóit meg kell erősíteni a [tartományvezérlők támadás elleni biztonságossá](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack) tételével megegyező sorok mentén.

### <a name="authentication-agent-registration"></a>Hitelesítési ügynök regisztrációja

A hitelesítési ügynök telepítése után regisztrálnia kell magát az Azure AD-ben. Az Azure AD minden hitelesítési ügynököt egyedi, digitális identitású tanúsítvánnyal rendel, amelyet az Azure AD-vel való biztonságos kommunikációhoz használhat.

A regisztrációs eljárás a Bérlővel is köti a hitelesítési ügynököt. Ez biztosítja, hogy az Azure AD tudja, hogy ez az adott hitelesítési ügynök az egyetlen jogosultsága a jelszó-ellenőrzési kérések kezelésére a bérlő számára. Ezt az eljárást minden egyes regisztrált új hitelesítési ügynök esetében meg kell ismételni.

A hitelesítési ügynökök a következő lépésekkel regisztrálhatják magukat az Azure AD-ben:

![Ügynök regisztrációja](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Az Azure AD először kéri, hogy globális rendszergazda jelentkezzen be az Azure AD-be a hitelesítő adataival. A bejelentkezés során a hitelesítési ügynök olyan hozzáférési jogkivonatot vásárol, amelyet a globális rendszergazda nevében használhat fel.
2. A hitelesítési ügynök Ezután létrehoz egy kulcspárt: egy nyilvános kulcsot és egy titkos kulcsot.
    - A kulcspár a standard RSA 2048 bites titkosítással jön létre.
    - A titkos kulcs abban a helyszíni kiszolgálón marad, ahol a hitelesítési ügynök található.
3. A hitelesítési ügynök "regisztrációs" kérést küld az Azure AD-nak HTTPS-kapcsolaton keresztül, a kérelemben szereplő alábbi összetevőkkel:
    - Az 1. lépésben megszerzett hozzáférési jogkivonat.
    - A 2. lépésben létrehozott nyilvános kulcs.
    - Tanúsítvány-aláírási kérelem (CSR vagy tanúsítványkérelem). Ez a kérelem egy digitális identitás tanúsítványára vonatkozik, az Azure AD-t pedig hitelesítésszolgáltatóként (CA).
4. Az Azure AD érvényesíti a hozzáférési jogkivonatot a regisztrációs kérelemben, és ellenőrzi, hogy a kérés egy globális rendszergazdától származik-e.
5. Az Azure AD ezután aláírja és elküldi a digitális identitás-tanúsítványt a hitelesítési ügynöknek.
    - Az Azure AD legfelső szintű HITELESÍTÉSSZOLGÁLTATÓja a tanúsítvány aláírására szolgál. 

      > [!NOTE]
      > Ez a HITELESÍTÉSSZOLGÁLTATÓ _nem_ a Windows megbízható legfelső szintű hitelesítésszolgáltatók tárolójában található.
    - A HITELESÍTÉSSZOLGÁLTATÓT csak a továbbított hitelesítés funkció használja. A HITELESÍTÉSSZOLGÁLTATÓ csak az ügyfélszolgálati munkatársak aláírására szolgál a hitelesítési ügynök regisztrációja során.
    -  A többi Azure AD-szolgáltatás egyike sem használja ezt a HITELESÍTÉSSZOLGÁLTATÓT.
    - A tanúsítvány tárgya (megkülönböztető név vagy DN) a bérlői AZONOSÍTÓra van beállítva. Ez a DN egy olyan GUID, amely egyedileg azonosítja a bérlőt. Ez a megkülönböztető név csak a bérlőhöz használható.
6. Az Azure AD egy Azure SQL Database adatbázisban tárolja a hitelesítési ügynök nyilvános kulcsát, amely csak az Azure AD-hez férhet hozzá.
7. Az 5. lépésben kiadott tanúsítványt a Windows tanúsítványtárolóban (különösen a [CERT_SYSTEM_STORE_LOCAL_MACHINE](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_LOCAL_MACHINE) helyen) tárolja a helyszíni kiszolgálón. Ezt a hitelesítési ügynök és a frissítési alkalmazások is használják.

### <a name="authentication-agent-initialization"></a>Hitelesítési ügynök inicializálása

Ha a hitelesítési ügynök elindul, vagy a regisztráció után első alkalommal, vagy a kiszolgáló újraindítása után, az informatikai részlegnek képesnek kell lennie biztonságos kommunikációra az Azure AD szolgáltatással, és el kell fogadnia a jelszó-ellenőrzési kérelmeket.

![Ügynök inicializálása](./media/how-to-connect-pta-security-deep-dive/pta2.png)

A hitelesítési ügynökök inicializálásának módja:

1. A hitelesítési ügynök kimenő rendszerindítási kérelmet készít az Azure AD-nek. 
    - Ez a kérelem az 443-as porton keresztül történik, és egy kölcsönösen hitelesített HTTPS-csatornán keresztül történik. A kérelem ugyanazt a tanúsítványt használja, amelyet a hitelesítési ügynök regisztrációja során adott ki.
2. Az Azure AD válaszol a kérésre egy hozzáférési kulcs megadásával egy olyan Azure Service Bus üzenetsor számára, amely egyedi a bérlő számára, és amelyet a bérlő azonosítója azonosít.
3. A hitelesítési ügynök állandó kimenő HTTPS-kapcsolaton (az 443-as porton keresztül) a várólistára helyezi. 
    - A hitelesítési ügynök most már készen áll a jelszó-ellenőrzési kérések lekérésére és kezelésére.

Ha a bérlőn több hitelesítő ügynök van regisztrálva, akkor az inicializálási eljárás biztosítja, hogy mindegyik ugyanahhoz a Service Bus-várólistához kapcsolódjon. 

## <a name="process-sign-in-requests"></a>Bejelentkezési kérelmek feldolgozása 

Az alábbi ábra bemutatja, hogyan dolgozza fel a felhasználói bejelentkezési kéréseket a továbbított hitelesítéssel.

![Bejelentkezési folyamat](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Az átmenő hitelesítés a következő módon kezeli a felhasználó bejelentkezési kérelmét: 

1. Egy felhasználó megpróbál elérni egy alkalmazást, például az [Outlook Web App](https://outlook.office365.com/owa)alkalmazást.
2. Ha a felhasználó még nem jelentkezett be, az alkalmazás átirányítja a böngészőt az Azure AD bejelentkezési oldalára.
3. Az Azure AD STS szolgáltatás visszaválaszol a **felhasználó bejelentkezési** oldalára.
4. A felhasználó beírja a felhasználónevét a **felhasználói bejelentkezési** oldalra, majd kiválasztja a **tovább** gombot.
5. A felhasználó beírja a jelszavát a **felhasználói bejelentkezési** oldalra, majd kiválasztja a **Bejelentkezés** gombot.
6. A felhasználónevet és a jelszót egy HTTPS POST-kérelemben küldi el a rendszer az Azure AD STS-nek.
7. Az Azure AD STS lekéri a nyilvános kulcsokat a bérlőn regisztrált összes hitelesítési ügynök Azure SQL Database, és titkosítja a jelszót a használatával.
    - "N" titkosított jelszavas értékeket hoz létre a bérlőn regisztrált "N" hitelesítési ügynökök számára.
8. Az Azure AD STS elhelyezi a jelszó-ellenőrzési kérelmet, amely a felhasználónevet és a titkosított jelszó értékét tartalmazza a bérlőre jellemző Service Bus várólistára.
9. Mivel a inicializált hitelesítési ügynökök tartósan csatlakoztatva vannak a Service Bus-várólistához, az egyik rendelkezésre álló hitelesítési ügynök lekéri a jelszó-ellenőrzési kérelmet.
10. A hitelesítési ügynök megkeresi a titkosított jelszó értékét, amely a nyilvános kulcsra vonatkozik, azonosító használatával, majd visszafejti azt a titkos kulcs használatával.
11. A hitelesítési ügynök megkísérli érvényesíteni a felhasználónevet és a jelszót a helyszíni Active Directory a [Win32 LOGONUSER API](/windows/win32/api/winbase/nf-winbase-logonusera) -val a **dwLogonType** paraméterrel **LOGON32_LOGON_NETWORK** értékre állítva. 
    - Ez az API ugyanaz az API, amelyet a Active Directory összevonási szolgáltatások (AD FS) (AD FS) használ a felhasználók bejelentkezésére egy összevont bejelentkezési forgatókönyvben.
    - Ez az API a Windows Server standard megoldási folyamatán alapul a tartományvezérlő megkereséséhez.
12. A hitelesítési ügynök megkapja a Active Directory eredményét, például a sikeres, a Felhasználónév vagy a jelszó helytelen, vagy a jelszó lejárt.

   > [!NOTE]
   > Ha a hitelesítési ügynök meghibásodik a bejelentkezési folyamat során, a rendszer elveti a teljes bejelentkezési kérést. Az egyik hitelesítési ügynöktől érkező bejelentkezési kérelmeket nem lehet letiltani a helyszínen egy másik hitelesítési ügynöknek. Ezek az ügynökök csak a felhővel kommunikálnak, és nem egymással.
   
13. A hitelesítési ügynök a 443-as porton keresztül továbbítja az eredményt az Azure AD STS-nek egy kimenő, kölcsönösen hitelesített HTTPS-csatornán keresztül. A kölcsönös hitelesítés a korábban a hitelesítési ügynök számára kiállított tanúsítványt használja a regisztráció során.
14. Az Azure AD STS ellenőrzi, hogy ez az eredmény összefügg-e a bérlőn megadott bejelentkezési kéréssel.
15. Az Azure AD STS a bejelentkezési eljárással folytatja a konfigurálást. Ha például a jelszó ellenőrzése sikeres volt, előfordulhat, hogy a felhasználót Multi-Factor Authentication vagy átirányítják az alkalmazáshoz.

## <a name="operational-security-of-the-authentication-agents"></a>A hitelesítési ügynökök működési biztonsága

Annak biztosítása érdekében, hogy az átmenő hitelesítés működése zavartalan maradjon, az Azure AD rendszeresen megújítja a hitelesítési ügynökök tanúsítványait. Az Azure AD elindítja a megújításokat. A megújítás nem vonatkozik magukra a hitelesítési ügynökökre.

![Működési biztonság](./media/how-to-connect-pta-security-deep-dive/pta4.png)

A hitelesítési ügynök megbízhatóságának megújítása az Azure AD-vel:

1. A hitelesítési ügynök időnként Pingeli az Azure AD-t néhány óránként, hogy ellenőrizze, van-e ideje a tanúsítvány megújítására. A tanúsítvány megújítása a lejárat előtt 30 nappal történik.
    - Ez az ellenőrzés kölcsönösen hitelesített HTTPS-csatornán keresztül történik, és ugyanazt a tanúsítványt használja, amelyet a regisztráció során adott ki.
2. Ha a szolgáltatás azt jelzi, hogy ideje megújítani a szolgáltatást, a hitelesítési ügynök létrehoz egy új kulcspárt: egy nyilvános kulcsot és egy titkos kulcsot.
    - Ezek a kulcsok a standard RSA 2048 bites titkosítással jönnek létre.
    - A titkos kulcs soha nem hagyja el a helyszíni kiszolgálót.
3. A hitelesítési ügynök ezután a "tanúsítvány megújítása" kérést küld az Azure AD-nak HTTPS-kapcsolaton keresztül, a kérelemben szereplő következő összetevőkkel:
    - A Windows tanúsítványtároló CERT_SYSTEM_STORE_LOCAL_MACHINE helyétől beolvasott meglévő tanúsítvány. Ebben az eljárásban nincs olyan globális rendszergazda, amely a globális rendszergazda nevében nem igényel hozzáférési jogkivonatot.
    - A 2. lépésben létrehozott nyilvános kulcs.
    - Tanúsítvány-aláírási kérelem (CSR vagy tanúsítványkérelem). Ez a kérelem egy új digitális azonosító tanúsítványra vonatkozik, az Azure AD-t pedig hitelesítésszolgáltatóként.
4. Az Azure AD a tanúsítvány megújítására vonatkozó kérelemben ellenőrzi a meglévő tanúsítványt. Ezután ellenőrzi, hogy a kérelem érkezett-e a bérlőn regisztrált hitelesítési ügynöktől.
5. Ha a meglévő tanúsítvány még érvényes, az Azure AD aláír egy új digitális identitás-tanúsítványt, és visszaállítja az új tanúsítványt a hitelesítési ügynöknek. 
6. Ha a meglévő tanúsítvány lejárt, az Azure AD törli a hitelesítési ügynököt a bérlő regisztrált hitelesítési ügynökök listájáról. Ezután a globális rendszergazdának manuálisan kell telepítenie és regisztrálnia egy új hitelesítési ügynököt.
    - A tanúsítvány aláírásához használja az Azure AD legfelső szintű HITELESÍTÉSSZOLGÁLTATÓját.
    - Állítsa be a tanúsítvány tulajdonosát (megkülönböztető név vagy DN) a bérlői AZONOSÍTÓra, amely egyedi módon azonosítja a bérlőt. A DN a tanúsítványt csak a bérlőre vonatkozik.
6. Az Azure AD a hitelesítési ügynök új nyilvános kulcsát egy olyan adatbázisban tárolja Azure SQL Databaseban, amelyhez csak hozzá van hozzáférése. A hitelesítési ügynökhöz társított régi nyilvános kulcsot is érvényteleníti.
7. A rendszer ezután az új, az 5. lépésben kiadott tanúsítványt tárolja a kiszolgálón a Windows tanúsítványtárolóban (különösen a [CERT_SYSTEM_STORE_CURRENT_USER](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_CURRENT_USER) helyen).
    - Mivel a megbízhatóság megújítási eljárása nem interaktív módon történik (a globális rendszergazda jelenléte nélkül), a hitelesítési ügynök már nem fér hozzá a meglévő tanúsítvány frissítéséhez a CERT_SYSTEM_STORE_LOCAL_MACHINE helyen. 
    
   > [!NOTE]
   > Ez az eljárás nem távolítja el magát a tanúsítványt a CERT_SYSTEM_STORE_LOCAL_MACHINE helyről.
8. Az új tanúsítvány erre a pontra történő hitelesítésre szolgál. A tanúsítvány minden későbbi megújítása helyettesíti a tanúsítványt a CERT_SYSTEM_STORE_LOCAL_MACHINE helyen.

## <a name="auto-update-of-the-authentication-agents"></a>A hitelesítési ügynökök automatikus frissítése

A Updater alkalmazás automatikusan frissíti a hitelesítési ügynököt, ha a rendszer új verziót (hibajavításokat vagy teljesítménynövelő javításokat) szabadít fel. A Updater alkalmazás nem kezeli a bérlőhöz tartozó jelszó-ellenőrzési kérelmeket.

Az Azure AD a szoftver új verzióját aláírt **Windows Installer csomagként (MSI)** üzemelteti. Az MSI a [Microsoft Authenticode](/previous-versions/windows/internet-explorer/ie-developer/platform-apis/ms537359(v=vs.85)) és a sha256 használatával van aláírva kivonatoló algoritmusként. 

![Automatikus frissítés](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Hitelesítési ügynök automatikus frissítése:

1. A frissítési alkalmazás minden órában Pingeli az Azure AD-t, hogy ellenőrizze, elérhető-e a hitelesítési ügynök új verziója. 
    - Ez az ellenőrzés kölcsönösen hitelesített HTTPS-csatornán végezhető el ugyanazzal a tanúsítvánnyal, amelyet a regisztráció során bocsátottak ki. A hitelesítési ügynök és a Updater megosztja a kiszolgálón tárolt tanúsítványt.
2. Ha új verzió érhető el, az Azure AD visszaküldi az aláírt MSI-t a frissítéshez.
3. A Updater ellenőrzi, hogy a Microsoft aláírta-e az MSI-t.
4. A Updater futtatja az MSI-t. Ez a művelet a következő lépéseket foglalja magában:

   > [!NOTE]
   > A Updater [helyi](/windows/win32/services/localsystem-account) rendszerjogosultságokkal fut.

    - A hitelesítési ügynök szolgáltatás leállítása
    - Telepíti a hitelesítési ügynök új verzióját a kiszolgálón
    - Újraindítja a hitelesítési ügynök szolgáltatását

>[!NOTE]
>Ha több hitelesítő ügynök van regisztrálva a bérlőn, az Azure AD nem újítja meg a tanúsítványait, vagy nem frissíti őket egyszerre. Ehelyett az Azure AD a bejelentkezési kérések magas rendelkezésre állásának biztosítása érdekében egyszerre tesz elérhetővé.
>


## <a name="next-steps"></a>Következő lépések
- [Jelenlegi korlátozások](how-to-connect-pta-current-limitations.md): megtudhatja, hogy mely forgatókönyvek támogatottak, és melyek nem.
- Gyors [útmutató: az](how-to-connect-pta-quick-start.md)Azure ad átmenő hitelesítésének megkezdése és futtatása.
- [Migrálás ad FSról áteresztő hitelesítésre](https://aka.ms/adfstoptadpdownload) – részletes útmutató a AD FS (vagy más összevonási technológiákból) áttelepített hitelesítéshez.
- [Intelligens zárolás](../authentication/howto-password-smart-lockout.md): konfigurálja az intelligens zárolási funkciót a bérlőn a felhasználói fiókok védetté tételéhez.
- [Hogyan működik](how-to-connect-pta-how-it-works.md): megismerheti az Azure ad átmenő hitelesítés működésének alapjait.
- [Gyakori](how-to-connect-pta-faq.md)kérdések: válaszok keresése a gyakori kérdésekre.
- [Hibaelhárítás](tshoot-connect-pass-through-authentication.md): megtudhatja, Hogyan oldhatók fel az áteresztő hitelesítési szolgáltatással kapcsolatos gyakori problémák.
- [Azure ad – zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso.md): További információ erről a kiegészítő funkcióról.