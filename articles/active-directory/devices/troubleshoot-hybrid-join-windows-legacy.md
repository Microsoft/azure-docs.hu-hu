---
title: Az örökölt hibrid Azure Active Directory csatlakoztatott eszközök hibáinak megoldása
description: A hibrid Azure Active Directory összekapcsolta a régebbi eszközökhöz kapcsolódó hibaelhárítást.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18104f06e779046786a2c7794736d01c35139490
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365803"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>A hibrid Azure Active Directory csatlakoztatása a régebbi verziójú eszközökhöz 

Ez a cikk csak a következő eszközökre vonatkozik: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Windows 10 vagy Windows Server 2016 esetén tekintse meg a következőt: a [hibrid Azure Active Directory csatlakoztatása a Windows 10 és a Windows server 2016 rendszerű eszközökhöz](troubleshoot-hybrid-join-windows-current.md).

Ez a cikk azt feltételezi, hogy a [hibrid Azure Active Directory csatlakoztatott eszközöket úgy konfigurálta](hybrid-azuread-join-plan.md) , hogy támogassa a következő forgatókönyveket:

- Eszközalapú feltételes hozzáférés

Ez a cikk a lehetséges problémák megoldásával kapcsolatos hibaelhárítási útmutatást nyújt.  

**Tudnivalók:** 

- Az alacsonyabb szintű Windows-eszközök hibrid Azure AD-csatlakoztatása némileg eltérő módon működik, mint a Windows 10-es verzióban. Sok ügyfél nem veszi észre, hogy szükségük van AD FSra (összevont tartományokra) vagy zökkenőmentes egyszeri bejelentkezésre (felügyelt tartományok esetén).
- A zökkenőmentes egyszeri bejelentkezés nem működik a Firefox és a Microsoft Edge böngészők privát böngészési módjában. Emellett az Internet Explorerben nem működik, ha a böngésző fokozottan védett módban fut.
- Az összevont tartományokkal rendelkező ügyfelek esetében, ha a szolgáltatáskapcsolódási pont (SCP) úgy van konfigurálva, hogy a felügyelt tartománynévre mutasson (például contoso.onmicrosoft.com, contoso.com helyett), akkor a hibrid Azure AD JOIN az alacsonyabb szintű Windows-eszközökhöz nem fog működni.
- Ugyanaz a fizikai eszköz többször is megjelenik az Azure AD-ben, ha több tartományi felhasználó jelentkezik be az alacsonyabb szintű hibrid Azure AD-hez csatlakoztatott eszközökre.  Ha például a *jdoe* és a *jharnett* egy eszközre jelentkezik be, akkor a **felhasználói** adatok lapon külön regisztráció (DeviceID) jön létre. 
- A felhasználói adatok lapon több bejegyzést is megadhat egy eszközhöz az operációs rendszer újratelepítése, illetve a manuális ismételt regisztráció miatt.
- Az eszközök kezdeti regisztrálása/csatlakoztatása úgy van konfigurálva, hogy a bejelentkezés vagy a zárolás/zárolás feloldására irányuló kísérletet végezzen. Egy Feladatütemező feladat 5 perces késleltetést váltott ki. 
- Győződjön meg arról, hogy a [KB4284842](https://support.microsoft.com/help/4284842) telepítve van a Windows 7 SP1 vagy a windows Server 2008 R2 SP1 esetén. Ez a frissítés megakadályozza a jövőbeli hitelesítési hibákat, mivel a jelszó módosítása után az ügyfél a védett kulcsokhoz való hozzáférésének elvesztése miatt megszakadt.
- A hibrid Azure AD-csatlakozás meghiúsulhat, ha egy felhasználó UPN-módosítása megtörtént, és a zökkenőmentes SSO-hitelesítési folyamat megszakad. Az illesztési folyamat során láthatja, hogy továbbra is elküldi a régi UPN-t az Azure AD-nek, kivéve, ha a böngésző-munkamenet cookie-jait törlik, vagy a felhasználó explicit módon kijelentkezik, és eltávolítja a régi UPN-t.

## <a name="step-1-retrieve-the-registration-status"></a>1. lépés: a regisztrációs állapot beolvasása 

**A regisztrációs állapot ellenőrzése:**  

1. Jelentkezzen be azzal a felhasználói fiókkal, amely hibrid Azure AD-csatlakozást hajtott végre.
1. A Parancssor megnyitása 
1. Írja be a következőt: `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Ez a parancs egy párbeszédpanelt jelenít meg, amely a csatlakozás állapotával kapcsolatos részleteket tartalmaz.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/01.png" alt-text="A Windows Workplace Join párbeszédpanel képernyőképe. Az e-mail-címet tartalmazó szöveg azt jelzi, hogy egy adott eszköz egy munkahelyhez csatlakozik." border="false":::

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>2. lépés: a hibrid Azure AD-csatlakozás állapotának kiértékelése 

Ha az eszköz nem csatlakozott a hibrid Azure AD-hez, az "összekapcsolás" gombra kattintva megkísérelheti a hibrid Azure AD-csatlakozást. Ha a hibrid Azure AD-csatlakozásra tett kísérlet meghiúsul, a hiba részletei jelennek meg.

**A leggyakoribb problémák a következők:**

- Helytelenül konfigurált AD FS vagy Azure AD-vagy hálózati probléma

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/02.png" alt-text="A Windows Workplace Join párbeszédpanel képernyőképe. A szöveges jelentések azt jelzik, hogy hiba történt a fiók hitelesítése során." border="false":::
    
   - A Autoworkplace.exe nem tud csendes hitelesítést végezni az Azure AD-vel vagy AD FSval. Ezt okozhatja a hiányzó vagy helytelenül konfigurált AD FS (összevont tartományok esetében), illetve hiányzik vagy helytelenül konfigurált Azure AD zökkenőmentes önálló Sign-On (felügyelt tartományokhoz) vagy hálózati problémák. 
   - Előfordulhat, hogy a többtényezős hitelesítés (MFA) engedélyezve van/konfigurálva van a felhasználóhoz, és a WIAORMULTIAUTHN nincs konfigurálva a AD FS-kiszolgálón. 
   - Egy másik lehetőség, hogy a Home Realm Discovery (HRD) oldal a felhasználói interakcióra vár, ami megakadályozza, hogy **autoworkplace.exe** a jogkivonat csendes kérelmezését.
   - Előfordulhat, hogy a AD FS és az Azure AD URL-címei hiányoznak az IE intranet zónájában az ügyfélen.
   - Előfordulhat, hogy a hálózati kapcsolat problémái miatt **autoworkplace.exe** a AD FS vagy az Azure ad URL-címeinek elérését. 
   - **Autoworkplace.exe** megköveteli, hogy az ügyfél közvetlen vonallal lássa el az ügyfelet a szervezet helyszíni ad-tartományvezérlőjére, ami azt jelenti, hogy a hibrid Azure ad-csatlakozás csak akkor sikeres, ha az ügyfél a szervezet intranetéhez csatlakozik.
   - A szervezet az Azure AD zökkenőmentes egyszeri bejelentkezést használja, `https://autologon.microsoftazuread-sso.com` vagy `https://aadg.windows.net.nsatc.net` nem szerepel az eszköz IE intranetes beállításain, és az **állapotsoron keresztüli frissítés engedélyezése parancsfájl használatával** nincs engedélyezve az intranetes zónában.
- Nincs bejelentkezve tartományi felhasználóként

   :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/03.png" alt-text="A Windows Workplace Join párbeszédpanel képernyőképe. A szöveges jelentések azt jelzik, hogy hiba történt a fiók ellenőrzése során." border="false":::

   Ez a következő okok miatt fordulhat elő:

   - A bejelentkezett felhasználó nem tartományi felhasználó (például helyi felhasználó). A régebbi verziójú Azure AD-csatlakoztatások csak tartományi felhasználók számára támogatottak.
   - Az ügyfél nem tud csatlakozni a tartományvezérlőhöz.    
- Elérte A kvótát

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/04.png" alt-text="A Windows Workplace Join párbeszédpanel képernyőképe. A szöveg hibát jelez, mert a felhasználó elérte a csatlakoztatott eszközök maximális számát." border="false":::

- A szolgáltatás nem válaszol 

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/05.png" alt-text="A Windows Workplace Join párbeszédpanel képernyőképe. A szöveges jelentések azt jelzik, hogy hiba történt, mert a kiszolgáló nem válaszolt." border="false":::

Az állapotadatok az eseménynaplóban találhatók: **alkalmazások és szolgáltatások Log\Microsoft-Workplace JOIN**
  
**A sikertelen hibrid Azure AD-csatlakozás leggyakoribb okai a következők:** 

- A számítógép nincs csatlakoztatva a szervezet belső hálózatához vagy egy VPN-hez a helyszíni AD-tartományvezérlővel létesített kapcsolattal.
- Helyi számítógépfiók használatával jelentkezett be a számítógépre. 
- Szolgáltatás konfigurációs problémái: 
   - A AD FS-kiszolgáló nincs konfigurálva a **WIAORMULTIAUTHN** támogatására. 
   - A számítógép erdője nem rendelkezik olyan szolgáltatási kapcsolódási pont objektummal, amely az ellenőrzött tartománynévre mutat az Azure AD-ben 
   - Vagy ha a tartomány felügyelt, akkor a zökkenőmentes egyszeri bejelentkezés nem lett konfigurálva vagy nem működik.
   - A felhasználó elérte az eszközök korlátját. 

## <a name="next-steps"></a>Következő lépések

További kérdések: eszközkezelés – [Gyakori kérdések](faq.md)  
