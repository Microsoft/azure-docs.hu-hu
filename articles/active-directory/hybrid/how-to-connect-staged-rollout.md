---
title: 'Azure AD Connect: felhőalapú hitelesítés szakaszos bevezetéssel | Microsoft Docs'
description: Ez a cikk azt ismerteti, hogyan lehet áttelepítést végezni az összevont hitelesítésből a felhőalapú hitelesítésre egy előkészített bevezetéssel.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c7f3de20ea3e86e3b56dc71d698354f7eaf782d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709718"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>Migrálás felhőalapú hitelesítésre előkészített bevezetéssel (előzetes verzió)

A lépcsőzetes bevezetéssel szelektíven tesztelheti a felhasználók csoportjait olyan felhőalapú hitelesítési funkciókkal, mint az Azure AD Multi-Factor Authentication (MFA), a feltételes hozzáférés, a kiszivárgott hitelesítő adatok identitásának védelme, a személyazonosság szabályozása és mások, a tartományok kivágása előtt.  Ez a cikk bemutatja, hogyan hajthatja végre a kapcsolót. Mielőtt elkezdené a szakaszos bevezetést, érdemes figyelembe vennie a következményeket, ha az alábbi feltételek közül egy vagy több teljesül:
    
-  Jelenleg helyszíni Multi-Factor Authentication-kiszolgálót használ. 
-  Intelligens kártyákat használ a hitelesítéshez. 
-  Az aktuális kiszolgáló a csak összevonási szolgáltatásokat kínálja.

A szolgáltatás kipróbálása előtt javasoljuk, hogy tekintse át az útmutatót a megfelelő hitelesítési módszer kiválasztásához. További információ: a módszerek összehasonlítása a [Azure Active Directory Hybrid Identity megoldás megfelelő hitelesítési módszerének kiválasztása](./choose-ad-authn.md#comparing-methods)című táblázatban.

A funkció áttekintéséhez tekintse meg ezt a "Azure Active Directory: mi a szakaszos bevezetés?" című témakört. videóinak

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Előfeltételek

-   Van egy Azure Active Directory (Azure AD) bérlő összevont tartománnyal.

-   Úgy döntött, hogy két lehetőség közül választhat:
    - " **A**  -  " lehetőség *jelszó kivonatának szinkronizálása (szinkronizálás)*  +  *zökkenőmentes egyszeri bejelentkezés (SSO)*.  További információ: [Mi az a jelszó-kivonatoló szinkronizálás](whatis-phs.md) , és [Mi a zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso.md)
    - **B**  -  . lehetőség *átmenő hitelesítés*  +  *zökkenőmentes egyszeri bejelentkezés*.  További információ: [Mi az átmenő hitelesítés](how-to-connect-pta.md) ?  
    
    Habár a *zökkenőmentes egyszeri bejelentkezés* nem kötelező, javasoljuk, hogy a tartományhoz csatlakoztatott számítógépeket futtató felhasználók számára is csendes bejelentkezési élményt biztosítson a vállalati hálózaton belül.

-   A Felhőbeli hitelesítésre áttelepített felhasználókhoz szükséges összes bérlői arculatot és feltételes hozzáférési szabályzatot konfigurálta.

-   Ha az Azure AD Multi-Factor Authentication használatát tervezi, javasoljuk, hogy az [önkiszolgáló jelszó-visszaállítás (SSPR) együttes regisztrációját használja, és multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) , hogy a felhasználók egyszer regisztrálják a hitelesítési módszereiket. Megjegyzés: Ha a SSPR-t használja a jelszó alaphelyzetbe állításához vagy a jelszó módosításához a MyProfile oldalon, a szakaszos bevezetéskor Azure AD Connect kell szinkronizálnia az új jelszó-kivonatot, amely akár 2 percet is igénybe vehet.

-   A szakaszos bevezetési funkció használatához globális rendszergazdának kell lennie a bérlőn.

-   A *zökkenőmentes egyszeri bejelentkezés* engedélyezéséhez egy adott Active Directory erdőben tartományi rendszergazdának kell lennie.

-  Ha hibrid Azure AD-t vagy Azure AD-csatlakozást telepít, frissítenie kell a Windows 10 1903 Update verzióra.


## <a name="supported-scenarios"></a>Támogatott esetek

A szakaszos bevezetéshez a következő forgatókönyvek támogatottak. A szolgáltatás csak a következőhöz használható:

- Azok a felhasználók, akik az Azure AD-hez Azure AD Connect használatával lettek kiépítve. Ez nem vonatkozik a csak felhőalapú felhasználókra.

- Felhasználói bejelentkezési forgalom a böngészőkben és a *modern hitelesítési* ügyfeleken. Az örökölt hitelesítést használó alkalmazások vagy felhőalapú szolgáltatások visszaesik az összevont hitelesítési folyamatokra. Előfordulhat például, hogy a modern hitelesítéssel rendelkező Exchange Online vagy az Outlook 2010, amely nem támogatja a modern hitelesítést.

- A csoport mérete jelenleg 50 000 felhasználóra korlátozódik.  Ha a csoportok mérete nagyobb, mint 50 000 felhasználó, akkor azt javasoljuk, hogy ezt a csoportot több csoportra ossza fel a szakaszos bevezetéshez.

- A Windows 10 Hybrid JOIN vagy az Azure AD JOIN Primary refresh token beszerzése anélkül, hogy a Windows 10 1903-es vagy újabb verziójához készült összevonási kiszolgáló számára elérhető legyen, ha a felhasználó UPN-je irányítható, és a tartomány utótagját ellenőrzi az Azure AD-ben.

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek nem támogatottak a szakaszos bevezetésnél:

- Az örökölt hitelesítés (például a POP3 és az SMTP) nem támogatott.

- Bizonyos alkalmazások a hitelesítés során elküldik a "domain_hint" lekérdezési paramétert az Azure AD-nek. Ezek a folyamatok folytatódnak, és az előkészített bevezetésre engedélyezett felhasználók továbbra is a hitelesítéshez használják az összevonást.

<!-- -->

- A rendszergazdák biztonsági csoportok használatával tudják kideríteni a Felhőbeli hitelesítést. Ha a helyszíni Active Directory biztonsági csoportok használatakor szeretné elkerülni a szinkronizálás késését, javasoljuk, hogy használjon Felhőbeli biztonsági csoportokat. A következő feltételek érvényesek:

    - Szolgáltatásként legfeljebb 10 csoportot használhat. Ez azt is megteheti, hogy 10 csoportot használ a *jelszó kivonatának szinkronizálásához*, az *átmenő hitelesítéshez* és a *zökkenőmentes egyszeri bejelentkezéshez*.
    - Beágyazott csoportok *nem támogatottak*. Ez a hatókör a nyilvános előzetes verzióra is érvényes.
    - A szakaszos bevezetéshez *nem használhatók* dinamikus csoportok.
    - A csoporton belüli kapcsolattartási objektumok nem lesznek hozzáadva a csoporthoz.

- Amikor először ad hozzá egy biztonsági csoportot az előkészített bevezetéshez, a rendszer 200-re korlátozza a felhasználóktól, hogy elkerülje az UX időtúllépését. A csoport hozzáadása után további felhasználókat is hozzáadhat közvetlenül hozzá, ha szükséges.

- Míg a felhasználók szakaszos bevezetéssel rendelkeznek, ha a EnforceCloudPasswordPolicyForPasswordSyncedUsers engedélyezve van, a jelszó lejárati szabályzata 90 napra van beállítva, és nincs lehetőség testreszabásra. 

- Windows 10 Hybrid JOIN vagy Azure AD JOIN Primary refresh token beszerzése a Windows 10-es verziónál régebbi, mint 1903. Ez a forgatókönyv vissza fog térni az összevonási kiszolgáló WS-Trust-végpontára, még akkor is, ha a bejelentkezett felhasználó a szakaszos bevezetéshez tartozik.

- A Windows 10 Hybrid JOIN vagy az Azure AD JOIN elsődleges frissítési jogkivonat beszerzése az összes verzióhoz, ha a felhasználó helyszíni UPN-azonosítója nem irányítható át. Ez a forgatókönyv a WS-Trust végpontra kerül vissza szakaszos bevezetési módban, de nem fog működni, ha a szakaszos áttelepítés befejeződött, és a felhasználói bejelentkezés már nem az összevonási kiszolgálón található.

  >[!NOTE]
  >Azure AD Connect vagy PowerShell használatával továbbra is a végső átváltás kell összevontról felhőbe történő hitelesítésre. A előkészített bevezetések nem váltják át a tartományokat összevontról felügyelt állapotba.  További információ a tartományi átváltás: [áttelepítés az összevonásból a jelszó-kivonatolási szinkronizálásba](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso) és [áttelepítés az összevonási szolgáltatásból átmenő hitelesítésre](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
  
## <a name="get-started-with-staged-rollout"></a>Ismerkedés a lépcsőzetes bevezetéssel

Ha tesztelni szeretné a *jelszó-kivonatolási szinkronizálást* a szakaszos bevezetéssel, kövesse a következő szakaszban ismertetett lépéseket.

További információ a használni kívánt PowerShell-parancsmagokról: [Azure AD 2,0 előzetes](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout)verzió.

## <a name="pre-work-for-password-hash-sync"></a>A jelszó-kivonat szinkronizálásának előzetes munkája

1. Engedélyezze a *jelszó-kivonatok szinkronizálását* a Azure ad Connect [választható szolgáltatások](how-to-connect-install-custom.md#optional-features) lapján. 

   ![Képernyőkép a "választható szolgáltatások" lapról Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Győződjön meg arról, hogy a *jelszó kivonatának teljes szinkronizálási* ciklusa fut, így minden felhasználó jelszavas kivonata szinkronizálva lett az Azure ad-vel. A *jelszó-kivonatolási szinkronizálás* állapotának megtekintéséhez használja a PowerShell-diagnosztikát a [jelszó-kivonat szinkronizálásának Azure ad Connect szinkronizálással való hibakereséséhez](tshoot-connect-password-hash-synchronization.md).

   ![Képernyőkép a AADConnect hibaelhárítási naplójáról](./media/how-to-connect-staged-rollout/sr2.png)

Ha lépcsőzetes *bevezetéssel* kívánja tesztelni az átmenő hitelesítési bejelentkezést, akkor a következő szakaszban leírt lépéseket követve engedélyezze azt.

## <a name="pre-work-for-pass-through-authentication"></a>Az átmenő hitelesítés előzetes munkája

1. Azonosítson egy Windows Server 2012 R2 vagy újabb rendszert futtató kiszolgálót, ahol az *átmenő hitelesítési* ügynököt futtatni szeretné. 

   *Ne válassza a* Azure ad Connect-kiszolgálót. Győződjön meg arról, hogy a kiszolgáló tartományhoz van csatlakoztatva, a kijelölt felhasználók hitelesítését Active Directory, és képes kommunikálni az Azure AD-vel a kimenő portok és URL-címek használatával. További információkért tekintse [meg az Azure ad zökkenőmentes egyszeri bejelentkezésének](how-to-connect-sso-quick-start.md)"1. lépés: az Előfeltételek ellenőrzése" című szakaszát.

1. [Töltse le a Azure ad Connect hitelesítési ügynököt](https://aka.ms/getauthagent), és telepítse a kiszolgálóra. 

1. A [magas rendelkezésre állás](how-to-connect-sso-quick-start.md)engedélyezéséhez telepítsen további hitelesítési ügynököket más kiszolgálókra.

1. Győződjön meg arról, hogy megfelelően konfigurálta az [intelligens zárolás beállításait](../authentication/howto-password-smart-lockout.md) . Így biztosíthatja, hogy a felhasználók helyszíni Active Directory fiókjai ne legyenek kizárva a rossz szereplőkkel.

Javasoljuk, hogy engedélyezze a *zökkenőmentes egyszeri bejelentkezést* a bejelentkezési módszertől függetlenül (*jelszó-kivonatolási szinkronizálás* vagy *átmenő hitelesítés*), amelyet a szakaszos bevezetéshez választott ki. A *zökkenőmentes egyszeri bejelentkezés* engedélyezéséhez kövesse a következő szakaszban leírt útmutatást.

## <a name="pre-work-for-seamless-sso"></a>A zökkenőmentes egyszeri bejelentkezéshez szükséges előzetes munka

Engedélyezze a *zökkenőmentes egyszeri bejelentkezést* az Active Directory erdőkön a PowerShell használatával. Ha egynél több Active Directory erdővel rendelkezik, minden egyes erdőhöz engedélyezze azt. A *zökkenőmentes egyszeri bejelentkezés* csak olyan felhasználók számára aktiválódik, akik a szakaszos bevezetésre vannak kiválasztva. Nem érinti a meglévő összevonási beállításokat.

Az alábbi lépésekkel engedélyezheti a *zökkenőmentes egyszeri bejelentkezést* :

1. Jelentkezzen be Azure AD Connect kiszolgálóra.

2. Nyissa meg a *(z)% ProgramFiles% \\ Microsoft Azure Active Directory Connect* mappát.

3. Importálja a *zökkenőmentes SSO* PowerShell-modult a következő parancs futtatásával: 

   `Import-Module .\AzureADSSO.psd1`

4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja meg a t `New-AzureADSSOAuthenticationContext` . Ez a parancs egy olyan ablaktáblát nyit meg, amelyen megadhatja a bérlő globális rendszergazdai hitelesítő adatait.

5. Hívás `Get-AzureADSSOStatus | ConvertFrom-Json` . Ez a parancs Active Directory erdők listáját jeleníti meg (lásd a "tartományok" listáját), amelyen a funkció engedélyezve van. Alapértelmezés szerint a a bérlő szintjén hamis értékre van állítva.

   ![Példa a Windows PowerShell kimenetére](./media/how-to-connect-staged-rollout/sr3.png)

6. Hívás `$creds = Get-Credential` . A parancssorba írja be a kívánt Active Directory erdő tartományi rendszergazdai hitelesítő adatait.

7. Hívás `Enable-AzureADSSOForest -OnPremCredentials $creds` . Ez a parancs létrehoz egy AZUREADSSOACC számítógépfiókot a helyszíni tartományvezérlőről a *zökkenőmentes egyszeri bejelentkezéshez* szükséges Active Directory erdőhöz.

8. A *zökkenőmentes egyszeri bejelentkezéshez* az intranet zónában URL-címek szükségesek. Ha ezeket az URL-címeket Csoportházirendek használatával szeretné telepíteni, tekintse [meg a rövid útmutató: Azure ad zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)című témakört.

9. A teljes bemutatóhoz letöltheti a *zökkenőmentes egyszeri bejelentkezéshez* használható [üzembe helyezési terveket](https://aka.ms/SeamlessSSODPDownload) is.

## <a name="enable-staged-rollout"></a>Szakaszos bevezetés engedélyezése

Egy adott szolgáltatás (*átmenő hitelesítés*, *jelszó-kivonatoló szinkronizálás* vagy *zökkenőmentes SSO*) egy csoporton belüli kiválasztásához kövesse a következő szakaszokban ismertetett utasításokat.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Egy adott szolgáltatás lépcsőzetes bevezetésének engedélyezése a bérlőn

A következő lehetőségek közül választhat:

- " **A**  -  " lehetőség *jelszó kivonatának szinkronizálása*  +  *zökkenőmentes egyszeri bejelentkezés*
- **B**  -  . lehetőség *átmenő hitelesítés*  +  *zökkenőmentes egyszeri bejelentkezés*
- **Nem támogatott**  -  *jelszó kivonatának szinkronizálása*  +  *átmenő hitelesítés*  +  *zökkenőmentes egyszeri bejelentkezés*

Tegye a következőket:

1. Az előnézet UX eléréséhez jelentkezzen be az [Azure ad-portálra](https://aka.ms/stagedrolloutux).

2. Jelölje be a **felügyelt felhasználói bejelentkezés (előzetes verzió) hivatkozásának engedélyezése szakaszos** bevezetést.

   Ha például engedélyezni szeretné az *a kapcsolót*, a **jelszó-kivonatolási szinkronizálást** és a **zökkenőmentes egyszeri bejelentkezéses** vezérlőket csúsztassa a **be** értékre, ahogy az az alábbi képeken látható.

   ![Az Azure AD Connect lap](./media/how-to-connect-staged-rollout/sr4.png)

   !["A szakaszos bevezetési funkciók engedélyezése (előzetes verzió)" oldal](./media/how-to-connect-staged-rollout/sr5.png)

3. Adja hozzá a csoportokat a szolgáltatáshoz az *átmenő hitelesítés* és a *zökkenőmentes egyszeri bejelentkezés* engedélyezéséhez. Az UX-időtúllépés elkerüléséhez győződjön meg arról, hogy a biztonsági csoportok kezdetben legfeljebb 200 tagot tartalmaznak.

   ![A "csoportok kezelése jelszó-kivonat szinkronizálásához (előzetes verzió)" oldal](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >A csoport tagjai automatikusan engedélyezve vannak az előkészített bevezetéshez. A beágyazott és a dinamikus csoportok nem támogatottak a szakaszos bevezetéshez.
   >Új csoport hozzáadásakor a csoportba tartozó felhasználók (egy új csoport számára legfeljebb 200 felhasználó) frissülni fognak a felügyelt hitelesítés azonnali használatára. A csoportok szerkesztésével (felhasználók hozzáadásával vagy eltávolításával) akár 24 óráig is eltarthat, amíg a módosítások érvénybe lépnek.
   >A zökkenőmentes egyszeri bejelentkezés csak akkor érvényes, ha a felhasználók a zökkenőmentes SSO-csoportban, valamint egy PTA vagy PHS csoportban is szerepelnek.

## <a name="auditing"></a>Naplózás

Engedélyezte a naplózási eseményeket a szakaszos bevezetéshez végrehajtott különféle műveletekhez:

- Naplózási esemény, ha engedélyez egy előkészített bevezetést a *jelszó-kivonatolási szinkronizáláshoz*, az *átmenő hitelesítéshez* vagy a *zökkenőmentes egyszeri bejelentkezéshez*.

  >[!NOTE]
  >A rendszer naplózza a naplózási eseményt, ha a *zökkenőmentes egyszeri bejelentkezés* be van kapcsolva a szakaszos bevezetés használatával.

  ![A "bevezetési szabályzat létrehozása a szolgáltatáshoz" ablaktábla – tevékenység lap](./media/how-to-connect-staged-rollout/sr7.png)

  ![A "bevezetési szabályzat létrehozása a szolgáltatáshoz" panel – módosított tulajdonságok lap](./media/how-to-connect-staged-rollout/sr8.png)

- Naplózási esemény, ha egy csoport hozzá lett adva a *jelszó kivonatának szinkronizálásához*, az *átmenő hitelesítéshez* vagy a *zökkenőmentes egyszeri bejelentkezéshez*.

  >[!NOTE]
  >A rendszer naplózza a naplózási eseményt, amikor a rendszer hozzáad egy csoportot a *jelszó-kivonat szinkronizálásához* a szakaszos bevezetéshez.

  ![A "Csoport hozzáadása a szolgáltatások bevezetéséhez" ablaktábla – tevékenység lap](./media/how-to-connect-staged-rollout/sr9.png)

  ![A "Csoport hozzáadása a szolgáltatáshoz" panel – módosított tulajdonságok lap](./media/how-to-connect-staged-rollout/sr10.png)

- Naplózási esemény, ha a csoportba felvett felhasználó engedélyezve van az előkészített bevezetéshez.

  ![A "felhasználó hozzáadása a szolgáltatások bevezetéséhez" ablaktábla – tevékenység lap](media/how-to-connect-staged-rollout/sr11.png)

  ![A "felhasználó hozzáadása a szolgáltatások bevezetéséhez" ablaktábla – cél (ok) lap](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Érvényesítés

A *jelszó-kivonatoló szinkronizálással* vagy *átmenő hitelesítéssel* (Felhasználónév és jelszó bejelentkezéssel) való bejelentkezés teszteléséhez tegye a következőket:

1. Az extraneten nyissa meg az [alkalmazások lapot](https://myapps.microsoft.com) egy privát böngésző-munkamenetben, majd adja meg az előkészített bevezetéshez kiválasztott felhasználói fiók USERPRINCIPALNAME (UPN).

   Azok a felhasználók, akik már megcélozták az előkészített bevezetést, nem lesznek átirányítva az összevont bejelentkezési oldalra. Ehelyett a rendszer felszólítja, hogy jelentkezzen be az Azure AD bérlői márkás bejelentkezési oldalára.

1. Győződjön meg arról, hogy a bejelentkezés sikeresen megtörtént az [Azure ad bejelentkezési tevékenység jelentésében](../reports-monitoring/concept-sign-ins.md) a userPrincipalName való szűréssel.

A bejelentkezés *zökkenőmentes egyszeri bejelentkezéssel* történő tesztelése:

1. Az intraneten nyissa meg az [alkalmazások lapot](https://myapps.microsoft.com) egy privát böngésző-munkamenetben, majd adja meg az előkészített bevezetéshez kiválasztott felhasználói fiók USERPRINCIPALNAME (UPN).

   Azok a felhasználók, akik a *zökkenőmentes SSO* szakaszos kiépítését célozták meg, "próbálnak bejelentkezni..." üzenet, mielőtt a rendszer némán bejelentkezett.

1. Győződjön meg arról, hogy a bejelentkezés sikeresen megtörtént az [Azure ad bejelentkezési tevékenység jelentésében](../reports-monitoring/concept-sign-ins.md) a userPrincipalName való szűréssel.

   Ha nyomon szeretné követni azokat a felhasználói bejelentkezéseket, amelyek továbbra is Active Directory összevonási szolgáltatások (AD FS) (AD FS) időpontban történnek a kiválasztott bevezetési felhasználók számára, kövesse az [AD FS hibaelhárítás: események és naplózás](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)című témakör utasításait. A gyártói dokumentációban tájékozódhat arról, hogyan lehet ezt a külső összevonási szolgáltatóknál megnézni.

## <a name="remove-a-user-from-staged-rollout"></a>Felhasználó eltávolítása az előkészített bevezetésből

Ha eltávolít egy felhasználót a csoportból, azzal letiltja az adott felhasználó szakaszos bevezetését. Az előkészített bevezetési funkció letiltásához csúsztassa vissza a vezérlőt **ki**.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: használhatom ezt a képességet éles környezetben?**

Válasz: igen, használhatja ezt a funkciót az üzemi bérlőben, de javasoljuk, hogy először próbálja ki a tesztelési bérlőben.

**K: használhatja ezt a szolgáltatást állandó "közös létezés" fenntartására, ahol egyes felhasználók összevont hitelesítést használnak, mások pedig Felhőbeli hitelesítést használnak?**

A: nem, ez a funkció a felhőalapú hitelesítés tesztelésére szolgál. A sikeres tesztelést követően néhány felhasználói csoportot a Felhőbeli hitelesítésre kell kivágni. Nem ajánlott állandó vegyes állapotot használni, mivel ez a módszer váratlan hitelesítési folyamatokhoz vezethet.

**K: használhatom a PowerShellt a szakaszos bevezetéshez?**

V: Igen. Ha szeretné megtudni, hogyan használhatja a PowerShellt a szakaszos bevezetéshez, tekintse meg az [Azure ad előzetes](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout)verzióját.

## <a name="next-steps"></a>Következő lépések
- [Azure AD 2,0 előzetes verzió](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout )
- [Bejelentkezési módszer módosítása a jelszó-kivonatolási szinkronizáláshoz](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
- [Bejelentkezési módszer módosítása átmenő hitelesítéshez](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
