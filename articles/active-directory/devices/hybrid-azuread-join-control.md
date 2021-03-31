---
title: Hibrid Azure AD-csatlakozás vezérelt ellenőrzése – Azure AD
description: Ismerje meg, hogyan végezheti el a hibrid Azure AD-csatlakozás ellenőrzött érvényesítését, mielőtt a teljes szervezeten belül engedélyezné
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38754b9e349e27afcff58dac27a616e3e4fb5319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860932"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás szabályozott ellenőrzése

Ha az összes előfeltétel teljesül, a Windows rendszerű eszközök automatikusan regisztrálják az eszközöket az Azure AD-bérlőben. Az Azure AD-beli eszköz-identitások állapotát hibrid Azure AD-csatlakozásnak nevezzük. Az ebben a cikkben ismertetett fogalmakkal kapcsolatos további információkért tekintse meg a cikkek a [Azure Active Directory eszközök felügyeletének bemutatása](overview.md) című cikket, és [tervezze meg a hibrid Azure Active Directory illesztés megvalósítását](hybrid-azuread-join-plan.md).

Előfordulhat, hogy a szervezetek a hibrid Azure AD-csatlakozás ellenőrzött érvényesítését szeretnék elvégezni, mielőtt a teljes szervezetben egyszerre engedélyezzék az egész szervezetet. Ez a cikk bemutatja, hogyan végezhető el a hibrid Azure AD-csatlakozás vezérelt ellenőrzése.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Hibrid Azure AD-csatlakozás ellenőrzött ellenőrzése a Windows aktuális eszközein

A Windows asztali operációs rendszert futtató eszközök esetében a támogatott verzió a Windows 10 évfordulós frissítése (1607-es verzió) vagy újabb. Ajánlott eljárásként frissítsen a Windows 10-es legújabb verziójára.

A hibrid Azure AD-csatlakozásnak a Windows aktuális eszközökön való ellenőrzéséhez a következőket kell tennie:

1. Ha létezik, törölje a szolgáltatáskapcsolódási pont (SCP) bejegyzését Active Directoryból (AD).
1. Ügyféloldali beállításjegyzék-beállítás konfigurálása SZOLGÁLTATÁSKAPCSOLÓDÁSI ponthoz a tartományhoz csatlakoztatott számítógépeken Csoportházirend objektum (GPO) használatával
1. Ha AD FS használ, az ügyféloldali beállításjegyzék-beállítást is konfigurálnia kell a SZOLGÁLTATÁSKAPCSOLÓDÁSI ponthoz a AD FS-kiszolgálón a csoportházirend-objektum használatával  
1. Előfordulhat, hogy a Azure AD Connect [szinkronizálási beállításait is testre kell szabnia](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) az eszközök szinkronizálásának engedélyezéséhez. 


### <a name="clear-the-scp-from-ad"></a>SZOLGÁLTATÁSKAPCSOLÓDÁSI pont törlése az AD-ből

A Active Directory Services Interfaces Editor (ADSI szerkesztő) segítségével módosítsa az SCP-objektumokat az AD-ben.

1. Indítsa el az **ADSI-szerkesztő** asztali alkalmazást a és a felügyeleti munkaállomásról, vagy egy tartományvezérlőt vállalati rendszergazdaként.
1. Kapcsolódjon a tartomány **konfiguráció-névhasználati környezetéhez** .
1. Tallózással keresse meg a **CN = Configuration, DC = contoso, DC = com**  >  **CN = Services**  >  **CN = eszköz regisztrációs konfigurációját**
1. Kattintson a jobb gombbal a **CN = 62a0ff2e-97b9-4513-943f-0d221bd30080** levél objektumra, és válassza a **Tulajdonságok** lehetőséget.
   1. Válassza ki a **kulcsszavakat** az **attribútum-szerkesztő** ablakban, és kattintson a **Szerkesztés** gombra.
   1. Válassza ki a **azureADId** és a **azureADName** értékeit (egy egyszerre), majd kattintson az **Eltávolítás** gombra.
1. Az **ADSI-szerkesztő** lezárása


### <a name="configure-client-side-registry-setting-for-scp"></a>Ügyféloldali beállításjegyzék-beállítás konfigurálása SZOLGÁLTATÁSKAPCSOLÓDÁSI ponthoz

A következő példa segítségével hozzon létre egy Csoportházirend objektumot (GPO) egy olyan beállításjegyzék-beállítás üzembe helyezéséhez, amely egy SCP-bejegyzést konfigurál az eszközök beállításjegyzékében.

1. Nyisson meg egy Csoportházirend felügyeleti konzolt, és hozzon létre egy új Csoportházirend objektumot a tartományban.
   1. Adja meg az újonnan létrehozott csoportházirend-objektum nevét (például ClientSideSCP).
1. Szerkessze a csoportházirend-objektumot, és keresse meg a következő elérési utat: **számítógép-konfigurációs**  >  **Beállítások**  >  **Windows-beállítások**  >  **beállításjegyzék**
1. Kattintson a jobb gombbal a beállításjegyzékre, és válassza az **új**  >  **beállításjegyzék elemet** .
   1. Az **általános** lapon konfigurálja a következőket
      1. Művelet: **frissítés**
      1. Struktúra: **HKEY_LOCAL_MACHINE**
      1. Kulcs elérési útja: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Érték neve: **TenantId**
      1. Érték típusa: **REG_SZ**
      1. Value (érték): az Azure ad-példány GUID- **azonosítója vagy könyvtára** (ez az érték a **Azure Portal**  >  **Azure Active Directory**  >  **Tulajdonságok**  >  **könyvtár-azonosítójában** található)
   1. Kattintson **az OK** gombra
1. Kattintson a jobb gombbal a beállításjegyzékre, és válassza az **új**  >  **beállításjegyzék elemet** .
   1. Az **általános** lapon konfigurálja a következőket
      1. Művelet: **frissítés**
      1. Struktúra: **HKEY_LOCAL_MACHINE**
      1. Kulcs elérési útja: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Érték neve: **TenantName**
      1. Érték típusa: **REG_SZ**
      1. Érték adatai: az ellenőrzött **tartománynevet** , ha összevont környezetet használ, például AD FS. A hitelesített **tartománynevet** vagy az onmicrosoft.com tartománynevet, ha például `contoso.onmicrosoft.com` felügyelt környezetet használ
   1. Kattintson **az OK** gombra
1. Az újonnan létrehozott csoportházirend-objektum szerkesztőjének lezárása
1. Csatolja az újonnan létrehozott GPO-t az ellenőrzött bevezetési populációhoz tartozó, tartományhoz csatlakoztatott számítógépeket tartalmazó kívánt szervezeti egységhez.

### <a name="configure-ad-fs-settings"></a>AD FS beállítások konfigurálása

Ha AD FS használ, először konfigurálnia kell az ügyféloldali SCP-t a fent említett utasítások alapján a csoportházirend-objektumnak a AD FS-kiszolgálókhoz való összekapcsolásával. Az SCP objektum határozza meg az eszköz-objektumok szolgáltatójának forrását. Helyszíni vagy Azure AD-t is használhat. Ha az ügyféloldali SZOLGÁLTATÁSKAPCSOLÓDÁSI pont AD FS van konfigurálva, az eszköz objektumainak forrása Azure AD-ként van kialakítva.

> [!NOTE]
> Ha nem tudta konfigurálni az ügyféloldali szolgáltatáskapcsolódási pontot a AD FS-kiszolgálókon, az eszköz identitásának forrása a helyszínen tekinthető. Az ADFS ezután megkezdi az eszközbeállítások törlését a helyszíni címtárból az ADFS-eszköz regisztrációjának "MaximumInactiveDays" attribútumában meghatározott meghatározott időtartam után. Az ADFS-eszköz regisztrációs objektumai a [Get-AdfsDeviceRegistration parancsmag](/powershell/module/adfs/get-adfsdeviceregistration)használatával találhatók meg.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Hibrid Azure AD-csatlakozás ellenőrzött ellenőrzése Windows rendszerű eszközökön

A Windows Down szintű eszközök regisztrálásához a szervezeteknek telepíteniük kell a Microsoft Workplace Joint a Microsoft letöltőközpontból elérhető, [nem Windows 10 rendszerű számítógépekre](https://www.microsoft.com/download/details.aspx?id=53554) .

A csomagot központilag telepítheti egy szoftverterjesztési rendszer, például a [Microsoft Endpoint Configuration Manager](/configmgr/)használatával. A csomag a csendes paraméterrel támogatja a normál csendes telepítési beállításokat. A Configuration Manager aktuális ága a korábbi verziók előnyeit kínálja, mint például a befejezett regisztrációk nyomon követésének lehetősége.

A telepítő létrehoz egy ütemezett feladatot a felhasználói környezetben futó rendszeren. A feladat akkor aktiválódik, amikor a felhasználó bejelentkezik a Windowsba. A feladat csendesen csatlakoztatja az eszközt az Azure AD-vel a felhasználói hitelesítő adatokkal az Azure AD-vel való hitelesítés után.

Az eszköz regisztrációjának szabályozásához a Windows Installer csomagot a Windows Down-szintű eszközök kiválasztott csoportjára kell telepítenie.

> [!NOTE]
> Ha nincs SZOLGÁLTATÁSKAPCSOLÓDÁSI pont konfigurálva az AD-ben, akkor a tartományhoz csatlakoztatott számítógépeken a Csoportházirend objektum (GPO) használatával az [ügyféloldali beállításjegyzék-beállítás konfigurálása az scp számára](#configure-client-side-registry-setting-for-scp)című témakörben leírtak szerint járjon el.


Miután meggyőződött róla, hogy minden a várt módon működik-e, automatikusan regisztrálhatja a Windows jelenlegi és régebbi verziójú eszközeit az Azure AD-vel az [SCP konfigurálásával Azure ad Connect használatával](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Következő lépések

[A hibrid Azure Active Directory-csatlakozás megvalósításának megtervezése](hybrid-azuread-join-plan.md)
