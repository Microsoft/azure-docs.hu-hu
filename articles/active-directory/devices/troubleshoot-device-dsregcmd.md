---
title: Hibakeresés a dsregcmd parancs használatával – Azure Active Directory
description: A dsregcmd kimenetének használata az Azure AD-ban lévő eszközök állapotának megismeréséhez
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 614b3f927dc22bc534168f8fe95cc2f97031b621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725523"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Eszközök hibaelhárítása a dsregcmd parancs használatával

A dsregcmd/status segédprogramot tartományi felhasználói fiókkal kell futtatni.

## <a name="device-state"></a>Eszköz állapota

Ez a szakasz az eszköz csatlakoztatási állapotának paramétereit sorolja fel. Az alábbi táblázat felsorolja az eszköz különböző illesztési állapotokban való bekapcsolásának feltételeit.

| AzureAdJoined | EnterpriseJoined | DomainJoined | Eszköz állapota |
| ---   | ---   | ---   | ---   |
| IGEN | NO | NO | Azure AD-hez csatlakoztatott |
| NO | NO | IGEN | Tartományhoz csatlakoztatott |
| IGEN | NO | IGEN | Hibrid AD-hez csatlakoztatott |
| NO | IGEN | IGEN | A helyszíni DRS csatlakoztatva |

> [!NOTE]
> Workplace Join (az Azure AD-ben regisztrált) állapot a "felhasználói állapot" szakaszban jelenik meg

- **AzureAdJoined:** – az "igen" értékre állítva, ha az eszköz csatlakoztatva van az Azure ad-hez. "Nem" egyéb esetben.
- **EnterpriseJoined:** -az "igen" értékre van állítva, ha az eszköz egy helyszíni DRS-hoz csatlakozik. Egy eszköz nem lehet egyszerre EnterpriseJoined és AzureAdJoined.
- **DomainJoined:** – az "igen" értékre van állítva, ha az eszköz tartományhoz (ad) van csatlakoztatva.
- **Tartománynév:** – állítsa be a tartomány nevét, ha az eszköz tartományhoz van csatlakoztatva.

### <a name="sample-device-state-output"></a>Példa eszköz állapotának kimenetére

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Eszközadatok

Csak akkor jelenik meg, ha az eszköz az Azure AD-hez csatlakozott vagy a hibrid Azure AD-hez csatlakozik (nem az Azure AD-ben regisztrált). Ez a szakasz felsorolja az eszköz a felhőben tárolt adatait.

- **DeviceID:** – az eszköz egyedi azonosítója az Azure ad-bérlőben
- **Ujjlenyomat:** – az eszköz tanúsítványának ujjlenyomata 
- **DeviceCertificateValidity:** – az eszköz tanúsítványának érvényessége
- **KeyContainerId:** – az eszköz tanúsítványához tartozó titkos kulcs ContainerId
- Kulcstartó **:** – az eszköz titkos kulcsának tárolására szolgáló (hardver/szoftver).
- **TpmProtected:** -"igen", ha az eszköz titkos kulcsát hardveres TPM tárolja.

### <a name="sample-device-details-output"></a>Minta eszköz részleteinek kimenete

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Bérlő részletei

Csak akkor jelenik meg, ha az eszköz az Azure AD-hez csatlakozott vagy a hibrid Azure AD-hez csatlakozik (nem az Azure AD-ben regisztrált). Ez a szakasz felsorolja a bérlő közös adatait, amikor egy eszköz csatlakozik az Azure AD-hez.

> [!NOTE]
> Ha a szakasz MDM URL-címei üresek, azt jelzi, hogy a MDM nincs konfigurálva, vagy az aktuális felhasználó nem a MDM-regisztráció hatókörében van. A MDM konfigurációjának áttekintéséhez tekintse meg az Azure AD mobilitási beállításait.

> [!NOTE]
> Még ha a MDM URL-címek is láthatók, ez nem jelenti azt, hogy az eszközt egy MDM felügyeli. Az információk akkor jelennek meg, ha a bérlő az automatikus regisztráláshoz MDM-konfigurációt is tartalmaz, akkor is, ha maga az eszköz nem felügyelt. 

### <a name="sample-tenant-details-output"></a>Példa bérlői részletek kimenetére

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Felhasználói állapot

Ez a szakasz felsorolja az eszközre jelenleg bejelentkezett felhasználó különböző attribútumainak állapotát.

> [!NOTE]
> Az érvényes állapot lekéréséhez a parancsnak felhasználói környezetben kell futnia.

- **NgcSet:** – ha az aktuálisan bejelentkezett felhasználóhoz be van állítva, a "yes" értékre kell állítani.
- **NgcKeyId:** – a Windows Hello-kulcs azonosítója, ha az aktuálisan bejelentkezett felhasználóhoz be van állítva.
- **CanReset:** – azt jelzi, hogy a Windows Hello-kulcs alaphelyzetbe állítható-e a felhasználó által. 
- **Lehetséges értékek:** -DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive vagy ismeretlen, ha hiba történt. 
- **WorkplaceJoined:** – az "igen" értékre állítva, ha az Azure ad-beli regisztrált fiókok hozzá lettek adva az eszközhöz az aktuális Ntuser-környezetben.
- **WamDefaultSet:** – állítsa Igen értékre, ha a bejelentkezett felhasználóhoz LÉTREJÖN egy WAM alapértelmezett webfiók. Ez a mező hibát jelez, ha a dsreg/status rendszergazda jogú parancssorból fut. 
- **WamDefaultAuthority:** – az Azure ad-ben "szervezetek" értékre van állítva.
- **WamDefaultId:** – mindig " https://login.microsoft.com " az Azure ad-hez.
- **WamDefaultGUID:** – a WAM szolgáltató (Azure AD/Microsoft-fiók) GUID azonosítója az alapértelmezett WAM webfiókhoz. 

### <a name="sample-user-state-output"></a>Felhasználói állapot kimenetének mintája

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO-állapot

Ez a szakasz figyelmen kívül hagyható az Azure AD által regisztrált eszközökön.

> [!NOTE]
> A parancsnak felhasználói környezetben kell futnia az adott felhasználó érvényes állapotának lekéréséhez.

- **AzureAdPrt:** – az "igen" értékre van állítva, ha a bejelentkezett felhasználó számára egy PRT van jelen az eszközön.
- **AzureAdPrtUpdateTime:** – a PRT utolsó frissítésekor a következő időpontra van BEÁLLÍTVA: UTC.
- **AzureAdPrtExpiryTime:** – az UTC időpontra van állítva, amikor a PRT lejár, ha nem újítja meg.
- **AzureAdPrtAuthority:** – Azure ad-szolgáltató URL-címe
- **EnterprisePrt:** – állítsa Igen értékre, ha az eszköz a helyszíni ADFS-vel kapcsolatos PRT-ket tartalmaz. A hibrid Azure AD-hez csatlakoztatott eszközök esetében az eszközön az Azure AD-vel és a helyszíni AD-vel egyidejűleg is lehet PRT-ket csatlakoztatni. A helyszíni csatlakoztatott eszközök csak nagyvállalati PRT-vel rendelkeznek.
- **EnterprisePrtUpdateTime:** – a vállalati PRT utolsó frissítésének időpontjában (UTC) van beállítva.
- **EnterprisePrtExpiryTime:** – az UTC időpontra van állítva, amikor a PRT lejár, ha nem újítja meg.
- **EnterprisePrtAuthority:** – ADFS-szolgáltató URL-címe

### <a name="sample-sso-state-output"></a>SSO-állapot kimenetének mintája

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Diagnosztikai adatok

### <a name="pre-join-diagnostics"></a>Csatlakozás előtti diagnosztika

Ez a szakasz csak akkor jelenik meg, ha az eszköz tartományhoz csatlakozik, és nem tud hibrid Azure AD-csatlakozást létesíteni.

Ez a szakasz különböző teszteket hajt végre a csatlakozási hibák diagnosztizálásához. Ez a szakasz az előző (?) részleteit is tartalmazza. Ez az információ tartalmazza a hiba fázisát, a hibakódot, a kiszolgálói kérelem AZONOSÍTÓját, a kiszolgáló válaszának http-állapotát, a kiszolgálói válasz hibaüzenetét.

- **Felhasználói környezet:** – az a környezet, amelyben a diagnosztika fut. Lehetséges értékek: rendszer, nem EMELt szintű felhasználó, EMELt szintű felhasználó. 

   > [!NOTE]
   > Mivel a tényleges illesztés a rendszerkörnyezetben történik, a diagnosztika futtatása a rendszerkörnyezetben a legközelebb esik a tényleges illesztési forgatókönyvhöz. A diagnosztika a rendszerkörnyezetben való futtatásához a dsregcmd/status parancsot egy rendszergazda jogú parancssorból kell futtatni.

- **Ügyfél időpontja:** – a rendszeridő UTC szerint.
- **Ad-kapcsolat tesztelése:** – a test kapcsolati tesztet hajt végre a tartományvezérlőn. Ennek a tesztnek a hibája valószínűleg csatlakozási hibákat eredményez az előzetes ellenőrzési fázisban.
- **Ad-konfigurációs teszt:** – a teszt beolvassa és ellenőrzi, hogy az scp-objektum megfelelően van-e konfigurálva a helyszíni ad-erdőben. Ebben a tesztben a hibák valószínűleg csatlakozási hibákat eredményeznek a felderítési fázisban a hibakód 0x801c001d.
- **DRS felderítési teszt:** – a teszt lekéri a DRS-végpontokat a felderítési metaadatok végpontján, és elvégzi a felhasználói tartományra vonatkozó kérelmet. Ebben a tesztben a hibák valószínűleg csatlakozási hibákat eredményeznek a felderítési fázisban.
- **DRS-kapcsolat tesztelése:** a test alapszintű kapcsolati tesztet hajt végre a DRS-végponton.
- **Jogkivonat-beszerzési teszt:** a test megpróbál beolvasni egy Azure ad-hitelesítési tokent, ha a felhasználói bérlő összevont. Ebben a tesztben a hibák valószínűleg csatlakozási hibákat eredményeznek az hitelesítési fázisban. Ha az Auth nem sikerül, a rendszer tartalékként kísérli meg a szinkronizálási csatlakozást, kivéve, ha a tartalék explicit módon le van tiltva az alábbi beállításkulcs-beállításokkal.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Tartalék szinkronizáláshoz – csatlakozás:** – az "enabled" értékre állítva, ha a fenti beállításkulcs lehetővé teszi, hogy a tartalék szinkronizálási hibákkal való CSATLAKOZTATÁSa ne legyen jelen. Ez a beállítás a Windows 10 1803-es és újabb verzióiban érhető el.
- **Korábbi regisztráció:** – az előző csatlakozási kísérlet ideje. A rendszer csak a sikertelen csatlakoztatási kísérleteket naplózza.
- **Error fázis:** – a csatlakozás megszakított szakasza. A lehetséges értékek: előzetes vizsgálat, felderítés, hitelesítés, csatlakozás.
- **Ügyfél-errorcode:** – visszaadott ügyfél-HIBAKÓD (HRESULT).
- **Kiszolgáló errorcode:** – kiszolgálói hibakód, ha a rendszer elküldte a kérelmet a kiszolgálónak, és a kiszolgáló egy hibakódtal válaszolt vissza. 
- **Kiszolgálói üzenet:** – a hibakódtal együtt visszaadott kiszolgálói üzenet.
- **Https-állapot:** – a kiszolgáló által visszaadott http-állapot.
- **Kérelem azonosítója:** – a rendszer elküldi a-ügyfél kérelemazonosító a kiszolgálónak. Hasznos a kiszolgálóoldali naplók összekapcsolásához.

### <a name="sample-pre-join-diagnostics-output"></a>Mintavétel előtti diagnosztika kimenete

A következő példa egy felderítési hiba miatt sikertelen diagnosztikai tesztet mutat be.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

A következő példa a diagnosztikai tesztek elvégzését mutatja be, de a regisztrációs kísérlet sikertelen volt, mert a szinkronizáláshoz való csatlakozás várható. Miután a Azure AD Connect szinkronizálási feladata befejeződik, az eszköz csatlakozhat.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Csatlakozás utáni diagnosztika

Ez a szakasz a felhőhöz csatlakoztatott eszközön elvégzett, józan ész-ellenőrzések kimenetét jeleníti meg.

- **AadRecoveryEnabled:** – ha az "igen", az eszközön tárolt kulcsok nem használhatók, és az eszköz ki van jelölve helyreállításra. A következő bejelentkezés elindítja a helyreállítási folyamatot, majd regisztrálja újra az eszközt.
- **KeySignTest:** – ha a "Passed" az eszköz kulcsai jó állapotban vannak. Ha a KeySignTest sikertelen, az eszköz általában a helyreállításhoz lesz megjelölve. A következő bejelentkezés elindítja a helyreállítási folyamatot, majd regisztrálja újra az eszközt. A hibrid Azure AD-hez csatlakoztatott eszközök esetén a helyreállítás csendes. Az Azure AD-hez csatlakoztatott vagy az Azure AD-regisztrációt követően az eszközök szükség esetén megkérik a felhasználók hitelesítését az eszköz helyreállításához és újbóli regisztrálásához. **A KeySignTest emelt szintű jogosultságok szükségesek.**

#### <a name="sample-post-join-diagnostics-output"></a>Példa a csatlakozás utáni diagnosztika kimenetére

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC Előfeltételek ellenőrzése

Ez a szakasz a Windows Hello for Business (WHFB) üzembe helyezésének előfeltétel-ellenőrzéseit hajtja végre. 

> [!NOTE]
> Ha a felhasználó már sikeresen beállította a WHFB-t, előfordulhat, hogy a dsregcmd/status nem látja az NGC előfeltétel-ellenőrzési részleteit.

- **IsDeviceJoined:** – az "igen" értékre állítva, ha az eszköz csatlakoztatva van az Azure ad-hez.
- **IsUserAzureAD:** – az "igen" értékre állítva, ha a bejelentkezett felhasználó megtalálható az Azure ad-ben.
- **PolicyEnabled:** – állítsa az "igen" értékre, ha a WHFB szabályzat engedélyezve van az eszközön.
- **PostLogonEnabled:** – az "igen" értékre állítva, ha a WHFB-regisztrációt a platform natív módon indítja el. Ha a "nem" értékre van állítva, az azt jelzi, hogy egy egyéni mechanizmus aktiválja a vállalati Windows Hello-regisztrációt
- **DeviceEligible:** – az "igen" értékre állítva, ha az eszköz megfelel a WHFB-regisztrációhoz szükséges hardverkövetelmények követelményeinek.
- **SessionIsNotRemote:** – állítsa Igen értékre, ha az aktuális felhasználó közvetlenül az eszközre van bejelentkezve, és nem távolról.
- **CertEnrollment:** – a WHFB tanúsítvány-megbízhatóság telepítésére vonatkozik, amely a WHFB tanúsítványigénylési szolgáltatóját jelzi. A "beléptetési szolgáltató" értékre van állítva, ha a WHFB házirend forrása Csoportházirend, "mobileszköz-kezelés", ha a forrás MDM. "nincs", máskülönben
- **AdfsRefreshToken:** – a WHFB tanúsítvány-megbízhatóság telepítésére vonatkozó specifikus. Csak akkor jelennek meg, ha a CertEnrollment "beléptetési szolgáltató". Azt jelzi, hogy az eszköz rendelkezik-e vállalati PRT-vel a felhasználó számára.
- **AdfsRaIsReady:** – a WHFB tanúsítvány-megbízhatóság telepítésére vonatkozó specifikus.  Csak akkor jelennek meg, ha a CertEnrollment "beléptetési szolgáltató". Állítsa az Igen értékre, ha az ADFS a WHFB által támogatott felderítési metaadatokban szerepel, *és* ha elérhető a bejelentkezési tanúsítvány sablonja.
- **LogonCertTemplateReady:** – a WHFB tanúsítvány-megbízhatóság telepítésére vonatkozó specifikus. Csak akkor jelennek meg, ha a CertEnrollment "beléptetési szolgáltató". Ha az "igen" értékre van állítva, ha a bejelentkezési tanúsítvány sablonjának állapota érvényes, és segít az ADFS-k hibakeresésében.
- **PreReqResult:** – a WHFB előfeltétel-kiértékelésének eredményét adja meg. Ha a felhasználó a következő alkalommal jelentkezik be, a "kiépítés" értékre van állítva, ha a WHFB-regisztráció Bejelentkezés utáni feladatként indul el.

### <a name="sample-ngc-prerequisite-check-output"></a>Az NGC előfeltétel-ellenőrzési kimenetének mintája

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Következő lépések

További kérdések: eszközkezelés – [Gyakori kérdések](faq.md)
