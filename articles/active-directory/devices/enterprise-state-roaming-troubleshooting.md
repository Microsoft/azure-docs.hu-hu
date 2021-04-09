---
title: A Azure Active Directory Enterprise State Roaming hibáinak megoldása
description: Választ ad a rendszergazdáknak a beállítások és az alkalmazás adatainak szinkronizálásával kapcsolatos kérdésekre.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: df70891ef090d44769aadbc235273e3193bc780e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94837209"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>A Azure Active Directory Enterprise State Roaming beállításainak hibaelhárítása

Ez a témakör arról nyújt tájékoztatást, hogyan lehet elhárítani és diagnosztizálni a Enterprise State Roamingával kapcsolatos problémákat, és megjeleníti az ismert problémák listáját.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Ez a cikk a Microsoft Edge korábbi HTML-alapú böngészőre vonatkozik, amely a Windows 10 2015-es verziójában indult el. A cikk nem vonatkozik az új, 2020. január 15-én kiadott Microsoft Edge Chromium-alapú böngészőre. Az új Microsoft Edge szinkronizálási működésével kapcsolatos további információkért tekintse meg a [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)című cikket.

## <a name="preliminary-steps-for-troubleshooting"></a>A hibaelhárítással kapcsolatos előzetes lépések 

A hibaelhárítás megkezdése előtt ellenőrizze, hogy a felhasználó és az eszköz megfelelően van-e konfigurálva, és hogy az eszköz és a felhasználó a Enterprise State Roaming összes követelményét kielégíti-e. 

1. A Windows 10 és a legújabb frissítések, valamint a 1511-es vagy újabb verzió (OS Build 10586-es vagy újabb) verziója telepítve van az eszközön. 
1. Az eszköz az Azure AD-hez csatlakoztatott vagy hibrid Azure AD-hez csatlakozik. További információkért lásd: [eszköz beszerzése az Azure ad felügyelete alatt](overview.md).
1. Győződjön meg arról, hogy az **Enterprise State roaming** engedélyezve van az Azure ad-bérlő számára az [Enterprise State roaming engedélyezéséhez](enterprise-state-roaming-enable.md). Engedélyezheti a barangolást minden felhasználó számára, vagy csak a kiválasztott felhasználói csoport számára.
1. A felhasználóhoz prémium szintű Azure Active Directory licenc van rendelve.  
1. Az eszközt újra kell indítani, és a felhasználónak újra be kell jelentkeznie Enterprise State Roaming szolgáltatások eléréséhez.

## <a name="information-to-include-when-you-need-help"></a>A segítségkéréshez mellékelendő információk
Ha nem tudja megoldani a problémát az alábbi útmutatással, vegye fel a kapcsolatot a támogatási szakembereivel. Ha felveszi Önnel a kapcsolatot, adja meg a következő információkat:

* **A hiba általános leírása**: vannak-e a felhasználó által látott hibaüzenetek? Ha nem volt hibaüzenet, írja le az észlelt váratlan viselkedést, részletesen. Milyen funkciók vannak engedélyezve a szinkronizáláshoz, és mi a felhasználó, aki a szinkronizálásra vár? Több funkció nem szinkronizálható, vagy egy-egy elkülönített szolgáltatás?
* **Érintett felhasználók** – egy felhasználó vagy több felhasználó szinkronizálása működik/sikertelen? Hány eszköz vesz részt felhasználónként? Az összes szinkronizálása nem történik meg, vagy némelyikük szinkronizálva van, néhány nem szinkronizálva?
* **Információ a felhasználóról** – milyen identitást használ a felhasználó az eszközre való bejelentkezéshez? Hogyan jelentkezik be a felhasználó az eszközre? Lehet, hogy egy kiválasztott biztonsági csoport is szinkronizálva van? 
* **Az eszköz adatai** – az Azure ad-hez csatlakoztatott vagy a tartományhoz csatlakoztatott eszköz? Milyen Build az eszköz? Mik a legújabb frissítések?
* **Dátum/idő/időzóna** – mi volt a pontos dátum és idő, amikor a hibát meglátta (az időzónát is beleértve)?

Ezek az információk a lehető leggyorsabban segítenek a probléma megoldásában.

## <a name="troubleshooting-and-diagnosing-issues"></a>Hibaelhárítás és a problémák diagnosztizálása

Ez a szakasz a Enterprise State Roaming kapcsolatos problémák elhárításával és diagnosztizálásával kapcsolatban nyújt javaslatokat.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>A szinkronizálás ellenőrzése és a "beállítások szinkronizálása" Beállítások lap 

1. Miután csatlakoztatta a Windows 10 rendszerű SZÁMÍTÓGÉPét egy olyan tartományhoz, amely Enterprise State Roaming engedélyezésére van konfigurálva, jelentkezzen be a munkahelyi fiókjával. Lépjen a **Beállítások**  >  **fiókok**  >  **szinkronizálja a beállításokat** , és erősítse meg, hogy a szinkronizálás és az egyes beállítások be vannak kapcsolva, és a beállítások lap felső részén látható, hogy szinkronizálja a munkahelyi fiókjával. Győződjön meg arról, hogy ugyanaz a fiók is használatban van bejelentkezési fiókja a **Beállítások**  >  **fiókjaiban**  >  . 
1. Ellenőrizze, hogy a szinkronizálás több gépen is működik-e az eredeti gépen végrehajtott módosítások végrehajtásával, például a tálca jobbra vagy a képernyő felső részén való áthelyezésével. Tekintse meg a változás propagálását a második gépre öt percen belül. 

   * A képernyő zárolása és feloldása (Win + L) segíthet a szinkronizálás elindításában.
   * Mindkét számítógépen ugyanazzal a fiókkal kell bejelentkeznie a szinkronizáláshoz, mivel Enterprise State Roaming a felhasználói fiókhoz van kötve, és nem a számítógépfiók.

**Lehetséges probléma**: Ha a **Beállítások** lapon lévő vezérlőelemek nem érhetők el, és a "néhány Windows-szolgáltatás csak Microsoft-fiók vagy munkahelyi fiók használata esetén érhető el." üzenet jelenik meg. Ez a probléma a tartományhoz csatlakoztatott és az Azure AD-ben regisztrált eszközök esetében fordulhat elő, de az eszköz még nem lett sikeresen hitelesítve az Azure AD-be. Ennek lehetséges oka az, hogy az eszköz házirendjét alkalmazni kell, de ez az alkalmazás aszinkron módon történik, és néhány órával késleltethető. 

### <a name="verify-the-device-registration-status"></a>Az eszköz regisztrációs állapotának ellenőrzése

Enterprise State Roaming megköveteli, hogy az eszköz regisztrálva legyen az Azure AD-ben. Bár a Enterprise State Roamingra nem jellemző, az alábbi utasításokat követve ellenőrizheti, hogy a Windows 10-ügyfél regisztrálva van-e, és erősítse meg az ujjlenyomatot, az Azure AD-beállítások URL-címét, az NGC-állapotot és az egyéb információkat.

1. Nyissa meg a parancssort nem emelt szintűként. Ehhez a Windows rendszerben nyissa meg a Run launchert (Win + R), és írja be a "CMD" parancsot a megnyitáshoz.
1. Miután megnyitotta a parancssort, írja be a "*dsregcmd.exe/status*" kifejezést.
1. A várt kimenet esetén a **AzureAdJoined** értékének "yes" értékűnek kell lennie, a **WamDefaultSet** mező értéke pedig "yes" kell legyen, és a **WamDefaultGUID** mező értékének egy "(AzureAd)" értékű GUID-nek kell lennie.

**Lehetséges probléma**: a **WamDefaultSet** és a **AzureAdJoined** egyaránt "nem" értékkel rendelkezik a mező értékeként, az eszköz tartományhoz lett csatlakoztatva és regisztrálva van az Azure ad-ben, és az eszköz nem szinkronizál. Ha ezt mutatja, előfordulhat, hogy az eszköznek várnia kell a szabályzat alkalmazására, vagy az eszköz hitelesítése meghiúsult az Azure AD-hez való csatlakozáskor. Előfordulhat, hogy a felhasználónak néhány órát várnia kell a szabályzat alkalmazására. Az egyéb hibaelhárítási lépések közé tartozhat az automatikus regisztráció újrapróbálása a kijelentkezéssel és vissza, vagy a Feladatütemezőben a feladat elindításával. Bizonyos esetekben a "*dsregcmd.exe/Leave*" futtatása egy emelt szintű parancssorablakban, újraindítás után, és a regisztráció újbóli kipróbálása segíthet a probléma megoldásában.

**Lehetséges probléma**: a **SettingsUrl** mezője üres, és az eszköz nem szinkronizál. Előfordulhat, hogy a felhasználó utoljára bejelentkezett az eszközre, mielőtt a Enterprise State Roaming engedélyezve lett volna a Azure Active Directory portálon. Indítsa újra az eszközt, és jelentkezzen be a felhasználói bejelentkezéssel. Ha szeretné, a portálon próbálja meg, hogy a rendszergazda navigáljon **Azure Active Directory**  >  **eszközökre**,  >  **Enterprise State roaming** tiltsa le, majd engedélyezze újra a **felhasználókat, hogy szinkronizálják a beállításokat és az alkalmazásadatok az eszközök között**. Az ismételt engedélyezés után indítsa újra az eszközt, és jelentkezzen be a felhasználói bejelentkezéssel. Ha ez nem oldja meg a problémát, akkor előfordulhat, hogy a **SettingsUrl** üres, ha hibás az eszköz tanúsítványa. Ebben az esetben a "*dsregcmd.exe/Leave*" parancsot egy rendszergazda jogú parancssorablakban kell futtatni, majd újra kell indítani a regisztrációt, és ismét próbálkozhat a regisztrálással.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming és Multi-Factor Authentication 

Bizonyos körülmények között a Enterprise State Roaming az Azure AD-Multi-Factor Authentication konfigurálásakor nem tud szinkronizálni az adatszinkronizálással. További információ ezekről a jelenségekről: támogatási dokumentum [KB3193683](https://support.microsoft.com/kb/3193683). 

**Lehetséges probléma**: Ha az eszköz úgy van konfigurálva, hogy a Azure Active Directory portálon multi-Factor Authentication igényelni, előfordulhat, hogy a Windows 10-es eszközre jelszóval való bejelentkezéskor nem lehet szinkronizálni a beállításokat. Az ilyen típusú Multi-Factor Authentication-konfiguráció egy Azure-beli rendszergazdai fiók megvédésére szolgál. A rendszergazda felhasználók továbbra is tudnak szinkronizálni a Windows 10-es eszközökre való bejelentkezéssel Microsoft Passport for Work PIN-kóddal vagy a Multi-Factor Authentication elvégzésével, miközben más Azure-szolgáltatásokhoz, például Microsoft 365hoz férnek hozzá.

**Lehetséges probléma**: a szinkronizálás meghiúsulhat, ha a rendszergazda konfigurálja a Active Directory összevonási szolgáltatások (AD FS) multi-Factor Authentication feltételes hozzáférési szabályzatot, és a hozzáférési token lejár az eszközön. Ügyeljen arra, hogy jelentkezzen be, és jelentkezzen ki a Microsoft Passport for Work PIN-kóddal, vagy fejezze be a Multi-Factor Authenticationt más Azure-szolgáltatások, például a Microsoft 365 eléréséhez

### <a name="event-viewer"></a>Eseménynapló

A speciális hibaelhárításhoz a Eseménynapló segítségével meghatározott hibák kereshetők. Ezeket az alábbi táblázat ismerteti. Az események a Eseménynapló >- **alkalmazások és-szolgáltatások naplójában** találhatók meg a  >  **Microsoft**  >  **Windows**  >  **SettingSync – Azure-** ban, valamint az alkalmazások és szolgáltatások szinkronizálására szolgáló, az **alkalmazásokkal és szolgáltatásokkal** kapcsolatos problémák a  >  **Microsoft**  >  **Windows**  >  **HRE**.

## <a name="known-issues"></a>Ismert problémák

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>A szinkronizálás nem működik olyan eszközökön, amelyeken az alkalmazások MDM szoftverrel vannak betöltve

A Windows 10 évfordulós frissítését (1607-es verzió) futtató eszközöket érinti. Az SettingSync-Azure-naplók alatt Eseménynapló a 80070259 6013-as AZONOSÍTÓJÚ eseményazonosító gyakran látható.

**Javasolt művelet**  
Győződjön meg arról, hogy a Windows 10 v1607-ügyfél rendelkezik a 2016-es kumulatív frissítéssel ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393,82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Az Internet Explorer Kedvencek nem szinkronizálhatók

A Windows 10 november frissítését (1511-es verzió) futtató eszközöket érinti.

**Javasolt művelet**  
Győződjön meg arról, hogy a Windows 10 v1511-ügyfél rendelkezik a júliusi 2016 összesítő frissítéssel ([KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586,494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>A téma nem szinkronizálható, és a Windows Information Protection védettek. 

Az adatszivárgás megakadályozása érdekében a [windows Information Protection](/windows/security/information-protection/windows-information-protection/protect-enterprise-data-using-wip) védelemmel ellátott adatai a Windows 10 évfordulós frissítését használó eszközökön nem szinkronizálhatók Enterprise State roaming használatával.

**Javasolt művelet**  
Nincsenek. Előfordulhat, hogy a Windows jövőbeli frissítései megoldják ezt a problémát.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>A dátum-, idő-és területi beállítások nem szinkronizálhatók a tartományhoz csatlakoztatott eszközön 
  
A tartományhoz csatlakoztatott eszközök nem fognak szinkronizálni a beállítás dátumával, időpontjával és régiójával: automatikus idő. Az automatikus idő használatával felülbírálhatja a többi dátumot, időt és régió beállításait, és a beállításokat nem szinkronizálhatja. 

**Javasolt művelet**  
Nincsenek. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>UAC-kérések jelszavak szinkronizálásakor

A a Windows 10 november frissítését (1511-es verzió) futtató eszközöket érinti a jelszavak szinkronizálására konfigurált vezeték nélküli hálózati adapterrel.

**Javasolt művelet**  
Győződjön meg arról, hogy a Windows 10 v1511-ügyfél rendelkezik a kumulatív frissítéssel ([KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586,494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>A szinkronizálás nem működik olyan eszközökön, amelyek intelligens kártyát használnak a bejelentkezéshez

Ha intelligens kártyával vagy virtuális intelligens kártyával próbál bejelentkezni a Windows-eszközre, a beállítások szinkronizálása nem fog működni.     

**Javasolt művelet**  
Nincsenek. Előfordulhat, hogy a Windows jövőbeli frissítései megoldják ezt a problémát.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>A tartományhoz csatlakoztatott eszköz szinkronizálása nem történik meg a vállalati hálózat elhagyása után     

Az Azure AD-ben regisztrált, tartományhoz csatlakoztatott eszközök szinkronizálási hibát tapasztalhatnak, ha az eszköz hosszabb ideig nem érhető el, és a tartományi hitelesítés nem hajtható végre.

**Javasolt művelet**  
Az eszköz csatlakoztatása a vállalati hálózathoz a szinkronizálás folytatása érdekében.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Az Azure AD-hez csatlakoztatott eszköz nem szinkronizálható, és a felhasználónak van egy vegyes esete egyszerű neve.

Ha a felhasználó kevert kis-és nagybetűket (például Felhasználónév helyett felhasználónevet) használ, és a felhasználó egy Azure AD-hez csatlakoztatott eszközön található, amely a Windows 10 10586-ről 14393-re frissült, akkor előfordulhat, hogy a felhasználó eszköze nem tud szinkronizálni. 

**Javasolt művelet**  
A felhasználónak meg kell adnia a csatlakozást, és újra csatlakoztatnia kell az eszközt a felhőhöz. Ehhez jelentkezzen be a helyi rendszergazda felhasználóként, és szüntesse meg az eszköz csatlakoztatását a **Beállítások**  >  **rendszer**  >  **névjegyével** , és válassza a "kezelés vagy a Leválasztás a munkahelyről vagy az iskoláról" lehetőséget. Törölje az alábbi fájlokat, majd az Azure ad ismét csatlakozzon az eszközhöz a **Beállítások**  >  **rendszer**  >  **névjegye** területen, és válassza a "csatlakozás munkahelyi vagy iskolai rendszerhez" lehetőséget. Folytassa az eszköz csatlakoztatását Azure Active Directory és fejezze be a folyamatot.

A karbantartás lépésben törölje a következő fájlokat:
- Settings. dat a `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- A mappában található összes fájl `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>6065-es eseményazonosító: 80070533 ez a felhasználó nem tud bejelentkezni, mert ez a fiók jelenleg le van tiltva.  

A SettingSync/hibakeresési naplók alatt látható Eseménynapló ez a hiba akkor jelenik meg, ha a felhasználó hitelesítő adatai lejártak. Emellett akkor is előfordulhat, ha a bérlő nem lett automatikusan kiépítve a AzureRMS. 

**Javasolt művelet**  
Az első esetben a felhasználó frissítheti a hitelesítő adatait, és bejelentkezhet az eszközre az új hitelesítő adatokkal. A AzureRMS probléma megoldásához folytassa a következő témakörben ismertetett lépéseket: [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>1098-es azonosítójú esemény: hiba: a 0xCAA5001C jogkivonat-átvitelszervezője művelete sikertelen volt.  

Eseménynapló a HRE/Operations naplók alatt ez a hiba látható a 1104-as eseménynél: HRE Cloud AP beépülő modul Call Get token visszaadott hiba: 0xC000005F. Ez a probléma akkor fordul elő, ha hiányoznak engedélyek vagy tulajdonosi attribútumok.  

**Javasolt művelet**  
Folytassa a felsorolt lépéseket a [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Következő lépések

Az áttekintést lásd: a [nagyvállalati állapot barangolása – áttekintés](enterprise-state-roaming-overview.md).