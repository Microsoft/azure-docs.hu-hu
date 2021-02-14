---
title: Hibrid Azure Active Directory csatlakozás konfigurálása a felügyelt tartományokhoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat hibrid Azure Active Directory illesztést a felügyelt tartományokhoz.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fb45f222fcf5abd762d97d702925655db6cba60
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365769"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Oktatóanyag: A hibrid Azure Active Directory-csatlakozás konfigurálása felügyelt tartományokhoz

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja a hibrid Azure Active Directory (Azure AD) illesztést Active Directory tartományhoz csatlakoztatott eszközökhöz. Ez a módszer olyan felügyelt környezetet támogat, amely a helyszíni Active Directory és az Azure AD-t is tartalmazza.

A szervezetben lévő felhasználóhoz hasonlóan az eszköz a védelemmel ellátni kívánt alapvető identitás. Az eszközök identitásával bármikor és bárhonnan biztosíthatja erőforrásait. Ezt a célt az eszközök identitásának az Azure AD-ben való kezelésével végezheti el. Használja az alábbi módszerek egyikét:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Ez a cikk a hibrid Azure AD-csatlakozásra összpontosít.

Ha az eszközöket az Azure AD-be szeretné állítani, az egyszeri bejelentkezés (SSO) révén maximalizálja a felhasználók hatékonyságát a felhőben és a helyszíni erőforrásokban. A [feltételes hozzáféréssel](../conditional-access/howto-conditional-access-policy-compliant-device.md) a felhőben és a helyszíni erőforrásokhoz is biztonságossá teheti a hozzáférést.

A felügyelt környezeteket a jelszó- [kivonatolási szinkronizálás (PHS)](../hybrid/whatis-phs.md) vagy az [átmenő hitelesítés (PTA ESP)](../hybrid/how-to-connect-pta.md) használatával, [zökkenőmentes egyszeri bejelentkezéssel](../hybrid/how-to-connect-sso.md)is üzembe helyezheti. Ezekhez a forgatókönyvekhez nem szükséges összevonási kiszolgálót konfigurálni a hitelesítéshez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A hibrid Azure AD-csatlakozás konfigurálása
> * A korábbi verziójú Windows-eszközök engedélyezése
> * Csatlakoztatott eszközök ellenőrzése
> * Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

- A [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 vagy újabb)
- Az Azure AD-bérlő globális rendszergazdájának hitelesítő adatai
- Az egyes erdők vállalati rendszergazdai hitelesítő adatai

Ismerkedjen meg a következő cikkekkel:

- [Mi az az eszközidentitás?](overview.md)
- [Útmutató: a hibrid Azure Active Directory JOIN implementációjának megtervezése](hybrid-azuread-join-plan.md)
- [Hibrid Azure AD-csatlakozás szabályozott ellenőrzése](hybrid-azuread-join-control.md)

> [!NOTE]
> Az Azure AD nem támogatja a felügyelt tartományokban található intelligens kártyák vagy tanúsítványok használatát.

Ellenőrizze, hogy a Azure AD Connect szinkronizálta-e azon eszközök számítógép-objektumait, amelyeket hibrid Azure AD-hez csatlakoztatni szeretne az Azure AD-hez. Ha a számítógép-objektumok meghatározott szervezeti egységekhez (OU-hoz) tartoznak, konfigurálja a szervezeti egységek szinkronizálását Azure AD Connect. Ha többet szeretne megtudni a számítógép-objektumok Azure AD Connect használatával történő szinkronizálásáról, tekintse meg a [szervezeti egység-alapú szűrés](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)című témakört.

A 1.1.819.0 verziótól kezdődően a Azure AD Connect tartalmaz egy varázslót, amellyel konfigurálhatja a hibrid Azure AD Joint. A varázsló jelentősen leegyszerűsíti a konfigurációs folyamatot. A varázsló konfigurálja az eszközök regisztrálásához szükséges szolgáltatáskapcsolódási pontokat.

A jelen cikkben szereplő konfigurációs lépések a varázsló Azure AD Connect-ben való használatán alapulnak.

A hibrid Azure AD-csatlakozáshoz az eszközöknek a szervezet hálózatán belüli alábbi Microsoft-erőforrásokhoz való hozzáférésre van szükségük:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Ha a vagy a-t használja, és nem szeretné használni a zökkenőmentes SSO-t)

> [!WARNING]
> Ha a szervezet olyan proxykiszolgálót használ, amelyek az SSL-forgalmat az adatveszteség-megelőzési vagy az Azure AD-bérlői korlátozások miatt észlelik, ügyeljen arra, hogy a (z) "" forgalom ne legyen https://device.login.microsoftonline.com kizárva a TLS-megszakítás és-vizsgálat alól. A (z) "" kizárása az https://device.login.microsoftonline.com ügyféltanúsítvány-alapú hitelesítés zavarásával járhat, ami problémákat okoz az eszközök regisztrációja és az eszközön alapuló feltételes hozzáférés miatt.

Ha a szervezete egy kimenő proxyn keresztül fér hozzá az internethez, a [webproxy automatikus felderítésének (WPAD) megvalósításával](/previous-versions/tn-archive/cc995261(v=technet.10)) engedélyezheti a Windows 10-es számítógépek számára az Azure ad-vel való regisztrációt. A WPAD konfigurálásával és kezelésével kapcsolatos problémák megoldásához tekintse meg az [automatikus észlelés hibaelhárítása](/previous-versions/tn-archive/cc302643(v=technet.10))című témakört. A Windows 10 rendszerű eszközökön a 1709-es frissítés előtt a WPAD az egyetlen elérhető lehetőség a proxyk hibrid Azure AD-csatlakozással való működésének konfigurálására. 

Ha nem használ WPAD-t, a Windows 10 1709 rendszertől kezdődően konfigurálhatja a WinHTTP-proxybeállításokat a számítógépen. További információ: [a GPO által központilag telepített WinHTTP-proxybeállítások](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Ha a proxybeállításokat a WinHTTP-beállítások használatával konfigurálja a számítógépen, a konfigurált proxyhoz nem csatlakoztatható számítógépek nem fognak csatlakozni az internethez.

Ha a szervezete hitelesített kimenő proxyn keresztül fér hozzá az internethez, győződjön meg arról, hogy a Windows 10 rendszerű számítógépek sikeresen hitelesítve vannak a kimenő proxyn. Mivel a Windows 10 rendszerű számítógépek számítógép-környezettel futtatják az eszközök regisztrációját, a kimenő proxy hitelesítését a gépi környezet használatával kell konfigurálni. A konfiguráció követelményeivel kapcsolatban forduljon a kimenő proxy szolgáltatójához.

Ellenőrizze, hogy az eszköz hozzáférhet-e a fenti Microsoft-erőforrásokhoz a rendszerfiókban az [eszköz regisztrálása kapcsolati](https://docs.microsoft.com/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) parancsfájl használatával.

## <a name="configure-hybrid-azure-ad-join"></a>A hibrid Azure AD-csatlakozás konfigurálása

Hibrid Azure AD-csatlakozás konfigurálása Azure AD Connect használatával:

1. Indítsa el Azure AD Connect, majd válassza a **Konfigurálás** lehetőséget.

1. A **További feladatok** területen válassza az **eszközbeállítások konfigurálása** lehetőséget, majd kattintson a **tovább** gombra.

   ![További feladatok](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. Az **Áttekintés** területen válassza a **tovább** lehetőséget.

1. A **Kapcsolódás az Azure ad-hoz** mezőben adja meg az Azure ad-bérlő globális rendszergazdájának hitelesítő adatait.  

1. Az **eszközbeállítások** területen válassza a **hibrid Azure ad-csatlakozás konfigurálása** lehetőséget, majd kattintson a **tovább** gombra.

   ![Eszközbeállítások](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. Az eszközök **operációs rendszerek** területén válassza ki azokat az operációs rendszereket, amelyeket az Active Directory környezetében lévő eszközök használnak, majd válassza a **tovább** lehetőséget.

   ![Eszköz operációs rendszere](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. Az **SCP-konfigurációban** minden olyan erdő esetében, amelyben Azure ad Connect szeretné KONFIGURÁLNI az scp-t, hajtsa végre az alábbi lépéseket, majd kattintson a **tovább** gombra.

   1. Válassza ki az **erdőt**.
   1. Válasszon **hitelesítési szolgáltatást**.
   1. Válassza a **Hozzáadás** lehetőséget a vállalati rendszergazda hitelesítő adatainak megadásához.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. A **konfigurálásra kész** területen válassza a **Konfigurálás** lehetőséget.

1. A **Konfigurálás befejeződött** területen válassza a **Kilépés** lehetőséget.

## <a name="enable-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök némelyike Windows Down-szintű eszközök, a következőket kell tennie:

- A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz
- Zökkenőmentes SSO konfigurálása
- Telepítse a Microsoft Workplace Joint a Windows régebbi verziójú számítógépekre

> [!NOTE]
> A Windows 7 támogatása 2020 január 14-én fejeződött be. További információ: a [Windows 7 támogatása befejeződött](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz

A Windows rendszerhez készült régebbi eszközök hibrid Azure AD-csatlakozásának befejezéséhez, valamint a tanúsítványok az Azure AD-be való hitelesítésének elkerüléséhez leküldheti a szabályzatot a tartományhoz csatlakoztatott eszközökre, hogy hozzáadja a következő URL-címeket a helyi intranet zónához az Internet Explorerben:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Emellett engedélyeznie kell a **frissítések állapotát az állapotsoron** a felhasználó helyi intranet zónájában lévő parancsfájl használatával.

### <a name="configure-seamless-sso"></a>Zökkenőmentes SSO konfigurálása

Ahhoz, hogy egy felügyelt tartományhoz tartozó, a [jelszó-kivonatoló szinkronizálást](../hybrid/whatis-phs.md) vagy [átmenő hitelesítést](../hybrid/how-to-connect-pta.md) használó Azure ad-beli Windows Down-szintű eszközök hibrid Azure ad-csatlakozását az Azure ad felhőalapú hitelesítési módszerként is be kell állítania, a [zökkenőmentes egyszeri bejelentkezést is konfigurálnia](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)kell.

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Telepítse a Microsoft Workplace Joint a Windows régebbi verziójú számítógépekre

A Windows Down szintű eszközök regisztrálásához a szervezeteknek telepíteniük kell [a Microsoft Workplace Joint a nem Windows 10 rendszerű számítógépekre](https://www.microsoft.com/download/details.aspx?id=53554). A nem Windows 10 rendszerű számítógépekhez készült Microsoft Workplace Join a Microsoft letöltőközpontban érhető el.

A csomagot központilag telepítheti egy szoftverterjesztési rendszer, például a [Microsoft Endpoint Configuration Manager](/configmgr/)használatával. A csomag támogatja a standard csendes telepítési beállításokat a `quiet` paraméterrel. A Configuration Manager aktuális verziója a korábbi verziók előnyeit kínálja, mint például a befejezett regisztrációk nyomon követésének lehetősége.

A telepítő létrehoz egy ütemezett feladatot a felhasználói környezetben futó rendszeren. A feladat akkor aktiválódik, amikor a felhasználó bejelentkezik a Windowsba. A feladat csendesen csatlakoztatja az eszközt az Azure AD-vel az Azure AD-vel végzett hitelesítés után a felhasználói hitelesítő adatok használatával.

## <a name="verify-the-registration"></a>A regisztráció ellenőrzése

Az eszköz állapotának megállapításához és ellenőrzéséhez az alábbi 3 módszer használható:

### <a name="locally-on-the-device"></a>Helyileg az eszközön

1. Nyissa meg a Windows PowerShellt.
2. Írja be a következő szöveget: `dsregcmd /status`.
3. Ellenőrizze, hogy a **AzureAdJoined** és a **DomainJoined** is **Igen** értékre van-e állítva.
4. Használhatja a **DeviceID** eszközt, és összehasonlíthatja a szolgáltatás állapotát a Azure Portal vagy a PowerShell használatával.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

1. Nyissa meg az eszközök lapot a [közvetlen hivatkozás](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)használatával.
2. Az eszközök megkeresésének módjáról [az eszköz identitásának kezelése a Azure Portal segítségével](./device-management-azure-portal.md)című témakörben talál információt.
3. Ha a **regisztrált** oszlop **függőben** van, akkor a hibrid Azure ad-csatlakozás nem fejeződött be.
4. Ha a **regisztrált** oszlop egy **dátumot és időpontot** tartalmaz, akkor a hibrid Azure ad JOIN befejeződött.

### <a name="using-powershell"></a>A PowerShell használata

A **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** használatával ellenőrizze az eszköz regisztrációs állapotát az Azure-bérlőben. Ez a parancsmag a [Azure Active Directory PowerShell-modulban](/powershell/azure/active-directory/install-msonlinev1)található.

Ha a **Get-MSolDevice** parancsmagot használja a szolgáltatás részleteinek megtekintéséhez:

- Léteznie kell egy olyan objektumnak **, amely megfelel a Windows** -ügyfél azonosítójának.
- A **DeviceTrustType** értéke **tartományhoz van csatlakoztatva**. Ez a beállítás megegyezik a **hibrid Azure ad-hez csatlakoztatott** állapottal az Azure ad-portál **eszközök** lapján.
- A feltételes hozzáférésben használt eszközök esetében az **enabled** érték **true (igaz** ), a **DeviceTrustLevel** pedig **felügyelt**.

1. Nyissa meg a Windows PowerShellt rendszergazdaként.
2. Az `Connect-MsolService` Azure-bérlőhöz való kapcsolódáshoz adja meg a következőt:.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Az összes hibrid Azure AD-hez csatlakoztatott eszköz száma (a **függő** állapot kizárása nélkül)

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Az összes **függő** állapotú, hibrid Azure ad-hez csatlakoztatott eszköz száma

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Az összes hibrid Azure AD-hez csatlakoztatott eszköz listázása

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Az összes **függő** állapotú, hibrid Azure ad-hez csatlakoztatott eszköz listázása

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Egyetlen eszköz részleteinek listázása:

1. ENTER `get-msoldevice -deviceId <deviceId>` (ez az eszközön helyileg beszerzett **DeviceID** ).
2. Ellenőrizze, hogy az **Engedélyezve** beállításhoz az **Igaz** érték van-e megadva.

## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha a tartományhoz csatlakoztatott Windows-eszközök hibrid Azure AD-csatlakozásának kitöltésével kapcsolatos problémákat tapasztal, tekintse meg a következőt:

- [Eszközök hibaelhárítása a dsregcmd paranccsal](./troubleshoot-device-dsregcmd.md)
- [Az Azure Active Directoryhoz csatlakoztatott hibrid eszközök hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)
- [A hibrid Azure Active Directory csatlakoztatása a régebbi verziójú eszközökhöz](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan kezelheti az eszközök identitásait a Azure Portal használatával.
> [!div class="nextstepaction"]
> [Eszközidentitások kezelése](device-management-azure-portal.md)
