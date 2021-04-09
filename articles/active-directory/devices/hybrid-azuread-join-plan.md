---
title: Hibrid Azure Active Directory-csatlakozás tervezése – Azure Active Directory
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a hibrid Azure Active Directory-csatlakoztatott eszközöket.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cadba181ea7d6a12ca64c78f3c7c58654d5f756f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500808"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Útmutató: a hibrid Azure Active Directory JOIN implementációjának megtervezése

A felhasználóhoz hasonló módon az eszköz egy másik alapvető identitás, amelyet védetté szeretne tenni, és így bármikor és bárhonnan védetté teheti az erőforrásokat. Ezt a célt az eszközök identitásának az Azure AD-ben való üzembe helyezésével és kezelésével végezheti el az alábbi módszerek egyikével:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Az Azure AD-be való bevonással maximalizálható a felhasználók munkahatékonysága, köszönhetően az egyszeri bejelentkezésnek (SSO), amely a felhőbeli és a helyszíni erőforrásokhoz is hozzáférést nyújt. Ugyanakkor a felhőben és a helyszíni erőforrásokhoz is biztonságossá teheti a hozzáférést a [feltételes hozzáféréssel](../conditional-access/overview.md).

Ha helyszíni Active Directory (AD) környezettel rendelkezik, és az AD-tartományhoz csatlakoztatott számítógépeket az Azure AD-hez szeretné csatlakoztatni, ezt a hibrid Azure AD JOIN használatával végezheti el. Ez a cikk a hibrid Azure AD-csatlakozás környezetbe való bevezetéséhez kapcsolódó lépéseket ismerteti. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy ismeri a [Azure Active Directory eszköz-identitások kezelésének bevezetését](./overview.md).

> [!NOTE]
> A Windows 10 hibrid Azure AD JOIN minimálisan szükséges tartományvezérlői verziója a Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>A megvalósítás megtervezése

A hibrid Azure AD-megvalósítás megtervezéséhez Ismerkedjen meg a következővel:

> [!div class="checklist"]
> - Támogatott eszközök áttekintése
> - Tekintse át a tudni kívánt dolgokat
> - Hibrid Azure AD-csatlakozás ellenőrzött ellenőrzésének áttekintése
> - Válassza ki a forgatókönyvet az identitás-infrastruktúra alapján
> - A hibrid Azure AD-csatlakozás helyszíni AD UPN-támogatásának áttekintése

## <a name="review-supported-devices"></a>Támogatott eszközök áttekintése

A hibrid Azure AD JOIN a Windows-eszközök széles körét támogatja. Mivel a Windows régebbi verzióit futtató eszközök konfigurációja további vagy eltérő lépéseket igényel, a támogatott eszközök két kategóriába vannak csoportosítva:

### <a name="windows-current-devices"></a>Windows aktuális eszközök

- Windows 10
- Windows Server 2016
  - **Megjegyzés**: az Azure National Cloud-ügyfeleknek az 1803-es verzióra van szükségük
- Windows Server 2019

A Windows asztali operációs rendszert futtató eszközök esetében a támogatott verzió a [Windows 10 kiadási információi](/windows/release-information/)című cikkben szerepel. Ajánlott eljárásként a Microsoft azt javasolja, hogy frissítsen a Windows 10-es legújabb verziójára.

### <a name="windows-down-level-devices"></a>Windows Down szintű eszközök

- Windows 8.1
- A Windows 7 támogatása 2020 január 14-én fejeződött be. További információ: [a Windows 7 támogatása befejeződött](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. A Windows Server 2008-es és 2008 R2-es verziójának támogatási információit lásd: [felkészülés a Windows server 2008 támogatásának végére](https://www.microsoft.com/cloud-platform/windows-server-2008).

Első tervezési lépésként tekintse át a környezetet, és állapítsa meg, hogy szükséges-e a Windows Down-szintű eszközeinek támogatása.

## <a name="review-things-you-should-know"></a>Tekintse át a tudni kívánt dolgokat

### <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek
- A hibrid Azure AD-csatlakozás jelenleg nem támogatott, ha a környezet egyetlen AD-erdőből áll, és több Azure AD-bérlőhöz szinkronizálja az azonosító adatait.

- A hibrid Azure AD JOIN nem támogatott a tartományvezérlő (DC) szerepkört futtató Windows Serveren.

- A hibrid Azure AD JOIN nem támogatott a Windows rendszerű, a hitelesítő adatok központi vagy felhasználói profiljának központi vagy kötelező profil használata esetén.

- A Server Core operációs rendszer nem támogatja semmilyen típusú eszköz regisztrációját.

- A Felhasználóáttelepítő (USMT) nem működik az eszköz regisztrálásával.  

### <a name="os-imaging-considerations"></a>OPERÁCIÓSRENDSZER-lemezképek szempontjai
- Ha a rendszer-előkészítő eszköz (Sysprep) szolgáltatásra támaszkodik, és ha a telepítés **előtt Windows 10 1809** rendszerképet használ, győződjön meg arról, hogy a rendszerkép nem olyan eszközről származik, amely már regisztrálva van az Azure ad-ben hibrid Azure ad-csatlakozásként.

- Ha virtuális gép (VM) pillanatképét szeretné létrehozni további virtuális gépek létrehozásához, győződjön meg arról, hogy a pillanatkép nem olyan virtuális gépről származik, amely már regisztrálva van az Azure AD-ben hibrid Azure AD-csatlakozásként.

- Ha [egyesített írási szűrőt](/windows-hardware/customize/enterprise/unified-write-filter) és hasonló technológiákat használ, amelyek törlik a lemez változásait az újraindításkor, akkor azokat az eszköz hibrid Azure ad-hez való csatlakoztatása után kell alkalmazni. Ha az ilyen technológiákat a hibrid Azure AD-csatlakozás befejezése előtt engedélyezi, a rendszer minden újraindításkor megkezdi az eszköz csatlakoztatását

### <a name="handling-devices-with-azure-ad-registered-state"></a>Eszközök feldolgozása az Azure AD regisztrált állapotával
Ha a Windows 10 tartományhoz csatlakoztatott eszközök az Azure AD-t a bérlőhöz [regisztrálják](overview.md#getting-devices-in-azure-ad) , akkor a hibrid Azure ad-hez csatlakoztatott és az Azure ad-beli regisztrált eszközök kettős állapotát eredményezheti. Javasoljuk, hogy a forgatókönyv automatikus megoldásához frissítsen a Windows 10 1803 (KB4489894 alkalmazott) vagy újabb verzióra. Az 1803-os kiadásokban a hibrid Azure AD-csatlakozás engedélyezése előtt manuálisan el kell távolítania az Azure AD-beli regisztrált állapotot. A 1803-es és újabb verzióiban a következő módosítások történtek a kettős állapot elkerüléséhez:

- Egy felhasználó meglévő Azure AD-beli regisztrált állapota automatikusan törlődik, <i>Miután az eszköz hibrid Azure ad-hez csatlakozik, és ugyanaz a felhasználó jelentkezik be</i>. Ha például a felhasználó egy Azure AD-beli regisztrált állapottal rendelkezik az eszközön, akkor az A felhasználó kettős állapota csak akkor törlődik, ha a felhasználó bejelentkezik az eszközre. Ha több felhasználó is van ugyanazon az eszközön, akkor a kettős állapotot a rendszer egyenként megtisztítja, amikor a felhasználók bejelentkeznek. Az Azure AD regisztrált állapotának eltávolítása mellett a Windows 10 törli az eszköz regisztrációját az Intune-ból vagy más MDM is, ha a regisztráció az Azure AD regisztrálásának részeként történt automatikus regisztrációval.
- Ez a változás nem érinti az Azure AD-ban regisztrált állapotot az eszközön lévő összes helyi fiókon. Csak a tartományi fiókokra vonatkozik. Így az Azure AD-beli regisztrált állapot a helyi fiókoknál még a felhasználó bejelentkezése után sem lesz automatikusan eltávolítva, mert a felhasználó nem tartományi felhasználó. 
- Megakadályozhatja, hogy a tartományhoz csatlakoztatott eszköz regisztrálva legyen az Azure AD-ben, ha hozzáadja a következő beállításértéket a HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin" = DWORD: 00000001.
- Windows 10 1803 rendszerben, ha a vállalati Windows Hello konfigurálva van, a felhasználónak újra kell telepítenie a Windows Hello for businesst a kettős állapot törlése után. Ez a probléma a KB4512509-mel foglalkozott

> [!NOTE]
> Annak ellenére, hogy a Windows 10 automatikusan eltávolítja az Azure AD-beli regisztrált állapotot, az Azure AD-beli eszköz-objektum nem törlődik azonnal, ha az Intune felügyeli. A dsregcmd/status futtatásával ellenőrizheti az Azure AD regisztrált állapotának eltávolítását, és azt is megvizsgálhatja, hogy az eszköz ne legyen regisztrálva az Azure AD-ben.

### <a name="additional-considerations"></a>Néhány fontos megjegyzés
- Ha a környezete virtuális asztali infrastruktúrát (VDI) használ, tekintse meg az [eszköz identitása és az asztali virtualizálási](./howto-device-identity-virtual-desktop-infrastructure.md)témakört.

- A hibrid Azure AD JOIN a FIPS-kompatibilis TPM 2,0 és a TPM 1,2 esetében nem támogatott. Ha az eszközei rendelkeznek FIPS-kompatibilis TPM 1,2-mel, a hibrid Azure AD-csatlakozás előtt le kell tiltania azokat. A Microsoft nem biztosít olyan eszközöket, amelyekkel letiltható a TPM FIPS üzemmódja, mert a TPM-gyártótól függ. Támogatásért forduljon a hardver OEM-hez. 

- A Windows 10 1903 kiadástól kezdve a TPM 1,2 nem használatos a hibrid Azure AD-csatlakozással és azokkal az eszközökkel, amelyek esetében ezek a TPM nem rendelkeznek TPM-mel.

- Az UPN-módosítások csak a Windows 10 2004 frissítésének megkezdése után támogatottak. A Windows 10 2004 frissítést megelőzően a felhasználók SSO és feltételes hozzáférési problémákba ütköznek az eszközökön. A probléma megoldásához meg kell szüntetnie az eszköz csatlakoztatását az Azure AD-ből (futtassa a "dsregcmd/Leave" parancsot emelt szintű jogosultságokkal), és csatlakozzon újra (automatikusan megtörténik). A vállalati Windows Hello szolgáltatással bejelentkezett felhasználók azonban nem szembesülnek ezzel a problémával.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás ellenőrzött ellenőrzésének áttekintése

Ha az összes előfeltétel teljesül, a Windows rendszerű eszközök automatikusan regisztrálják az eszközöket az Azure AD-bérlőben. Az Azure AD-beli eszköz-identitások állapotát hibrid Azure AD-csatlakozásnak nevezzük. A cikkben ismertetett fogalmakkal kapcsolatos további információkért tekintse meg a [Azure Active Directory eszköz-Identitáskezelés kezelése](overview.md)című cikket.

Előfordulhat, hogy a szervezetek a hibrid Azure AD-csatlakozás ellenőrzött érvényesítését szeretnék elvégezni, mielőtt a teljes szervezetben egyszerre engedélyezzék az egész szervezetet. Tekintse át a [hibrid Azure ad-hez való csatlakozás ellenőrzött ellenőrzésének](hybrid-azuread-join-control.md) lépéseit, és Ismerje meg, hogyan valósítható meg.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Válassza ki a forgatókönyvet az identitás-infrastruktúra alapján

A hibrid Azure AD JOIN mindkét, felügyelt és összevont környezettel működik, attól függően, hogy az egyszerű felhasználónév irányítható vagy nem irányítható. Tekintse meg a lap alját a táblázathoz a támogatott forgatókönyvek esetében.  

### <a name="managed-environment"></a>Felügyelt környezet

A felügyelt környezetek a [jelszó kivonatos szinkronizálásával (PHS)](../hybrid/whatis-phs.md) vagy a [hitelesítés (PTA ESP)](../hybrid/how-to-connect-pta.md) használatával is üzembe helyezhetők, [zökkenőmentes egyszeri bejelentkezéssel](../hybrid/how-to-connect-sso.md).

Ezekhez a forgatókönyvekhez nem szükséges összevonási kiszolgálót konfigurálni a hitelesítéshez.

> [!NOTE]
> A lépcsőzetes bevezetést [használó felhőalapú hitelesítés](../hybrid/how-to-connect-staged-rollout.md) csak a Windows 10 1903 frissítésének megkezdése után támogatott

### <a name="federated-environment"></a>Összevont környezet

Az összevont környezetnek rendelkeznie kell egy olyan identitás-szolgáltatóval, amely a következő követelményeket támogatja. Ha Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával összevont környezettel rendelkezik, az alábbi követelmények már támogatottak.

- **WIAORMULTIAUTHN jogcím:** Ez a jogcím szükséges a hibrid Azure AD-csatlakozáshoz a Windows rendszerű eszközökön.
- **Ws-Trust protokoll:** Ez a protokoll szükséges a Windows jelenlegi hibrid Azure AD-hez csatlakoztatott eszközök Azure AD-vel való hitelesítéséhez. AD FS használatakor engedélyeznie kell a következő WS-Trust végpontokat: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Az **ADFS/Services/Trust/2005/windowstransport** , vagy az **ADFS/Services/Trust/13/windowstransport** beállítást csak intranetes végpontként kell engedélyezni, és a webalkalmazás-proxyn keresztül nem szabad az extranet felé irányuló végpontok számára elérhetővé tenni. Ha többet szeretne megtudni a WS-Trust Windows-végpontok letiltásáról, tekintse meg [a WS-Trust Windows-végpontok letiltása a proxyn](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)című témakört. Láthatja, hogy mely végpontok vannak engedélyezve a AD FS felügyeleti konzolon a **szolgáltatási**  >  **végpontok** alatt.

> [!NOTE]
> Az Azure AD nem támogatja a felügyelt tartományokban található intelligens kártyákat vagy tanúsítványokat.

Az 1.1.819.0-s verziótól kezdve az Azure AD Connectben egy varázsló segíti a hibrid Azure AD-csatlakozások konfigurálását. Ez a varázsló jelentősen leegyszerűsíti a konfigurálási folyamatot. Ha a Azure AD Connect szükséges verziójának telepítése nem lehetséges, tekintse meg az [eszközök regisztrációjának manuális konfigurálását](hybrid-azuread-join-manual.md)ismertető témakört. 

Az identitás-infrastruktúrának megfelelő forgatókönyv alapján lásd:

- [Hibrid Azure Active Directory csatlakozás konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
- [Hibrid Azure Active Directory csatlakozás konfigurálása felügyelt környezethez](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>A helyszíni AD felhasználói UPN-támogatásának áttekintése hibrid Azure AD-csatlakozáshoz

Előfordulhat, hogy a helyszíni AD-felhasználók UPN-felhasználónevei eltérőek lehetnek az Azure AD UPN-ben. Ilyen esetekben a Windows 10 Hybrid Azure AD JOIN korlátozott támogatást biztosít a helyszíni AD UPN-hez a [hitelesítési módszer](../hybrid/choose-ad-authn.md), a tartomány típusa és a Windows 10 verziója alapján. A környezetben két típusú helyszíni AD UPN létezik:

- Irányítható felhasználói UPN: az átirányítható UPN egy érvényes ellenőrzött tartománnyal rendelkezik, amelyet egy tartományregisztráló regisztrál. Ha például az contoso.com az elsődleges tartomány az Azure AD-ben, a contoso.org az elsődleges tartomány a contoso által birtokolt és az [Azure ad-ben ellenőrzött](../fundamentals/add-custom-domain.md) helyszíni ad-ben.
- Nem átirányítható felhasználók UPN: egy nem átirányítható UPN nem rendelkezik ellenőrzött tartománnyal. Csak a szervezet magánhálózaton belül alkalmazható. Ha például az contoso.com az elsődleges tartomány az Azure AD-ben, a contoso. local az elsődleges tartomány a helyszíni AD-ben, de nem ellenőrizhető tartomány az interneten, és csak a contoso hálózatán belül használatos.

> [!NOTE]
> Az ebben a szakaszban található információk csak a helyszíni felhasználók UPN-jére vonatkoznak. Nem alkalmazható helyszíni számítógép-tartomány utótagra (például: Számítógép1. contoso. local).

Az alábbi táblázat részletesen ismerteti ezen helyszíni AD UPN-ket a Windows 10 hibrid Azure AD JOIN szolgáltatásban

| A helyszíni AD UPN típusa | Alkalmazási tartomány típusa | Windows 10 verzió | Leírás |
| ----- | ----- | ----- | ----- |
| Irányítható | Összevont | 1703-es kiadásból | Általánosan elérhető |
| Nem irányítható | Összevont | 1803-es kiadásból | Általánosan elérhető |
| Irányítható | Felügyelt | 1803-es kiadásból | Általánosan elérhető, a Windows lockscreen futó Azure AD-SSPR nem támogatott. A helyszíni UPN-t szinkronizálni kell az     `onPremisesUserPrincipalName` Azure ad-ben található attribútummal |
| Nem irányítható | Felügyelt | Nem támogatott | |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hibrid Azure Active Directory csatlakozás konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md) 
>  [Hibrid Azure Active Directory csatlakozás konfigurálása felügyelt környezethez](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
