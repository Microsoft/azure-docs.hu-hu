---
title: Hibrid Azure Active Directory illesztés konfigurálása összevont tartományokhoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat hibrid Azure Active Directory illesztést összevont tartományokhoz.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2a455e1ee6f8f714cf50ebdf6a59dab568489ca
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646299"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>A hibrid Azure Active Directory-csatlakozás konfigurálása összevont tartományokhoz

A szervezetben lévő felhasználóhoz hasonlóan az eszköz a védelemmel ellátni kívánt alapvető identitás. Az eszközök identitásával bármikor és bárhonnan biztosíthatja erőforrásait. Ezt a célt úgy hajthatja végre, hogy az eszköz identitásait és a Azure Active Directory (Azure AD) felügyeletét az alábbi módszerek egyikével látja el:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Ha az eszközöket az Azure AD-be szeretné állítani, az egyszeri bejelentkezés (SSO) révén maximalizálja a felhasználók hatékonyságát a felhőben és a helyszíni erőforrásokban. A [feltételes hozzáféréssel](../conditional-access/howto-conditional-access-policy-compliant-device.md) a felhőben és a helyszíni erőforrásokhoz is biztonságossá teheti a hozzáférést.

Az összevont környezetnek rendelkeznie kell egy olyan identitás-szolgáltatóval, amely a következő követelményeket támogatja. Ha Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával összevont környezettel rendelkezik, az alábbi követelmények már támogatottak.

- **WIAORMULTIAUTHN jogcím:** Ez a jogcím szükséges a hibrid Azure AD-csatlakozáshoz a Windows rendszerű eszközökön.
- **Ws-Trust protokoll:** Ez a protokoll szükséges a Windows jelenlegi hibrid Azure AD-hez csatlakoztatott eszközök Azure AD-vel való hitelesítéséhez.
  AD FS használatakor engedélyeznie kell a következő WS-Trust végpontokat: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Az **ADFS/Services/Trust/2005/windowstransport** és az **ADFS/Services/Trust/13/windowstransport** egyaránt engedélyezve kell lennie csak intranetes végpontok számára, és a webalkalmazás-proxyn keresztül nem szabad az extranetes végpontok számára elérhetővé tenni. Ha többet szeretne megtudni a WS-Trust Windows-végpontok letiltásáról, tekintse meg [a WS-Trust Windows-végpontok letiltása a proxyn](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)című témakört. Láthatja, hogy mely végpontok vannak engedélyezve a AD FS felügyeleti konzolon a **szolgáltatási**  >  **végpontok** alatt.

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja a hibrid Azure AD Joint Active Directory tartományhoz csatlakoztatott számítógépek eszközeihez egy összevont környezetben AD FS használatával.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A hibrid Azure AD-csatlakozás konfigurálása
> * Régebbi Windows-eszközök engedélyezése
> * A regisztráció ellenőrzése
> * Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már ismeri a következő cikkeket:

- [Mi az az eszközidentitás?](overview.md)
- [Hibrid Azure AD JOIN megvalósításának megtervezése](hybrid-azuread-join-plan.md)
- [Hibrid Azure AD-csatlakozás vezérelt ellenőrzése](hybrid-azuread-join-control.md)

Az oktatóanyagban ismertetett forgatókönyvhöz az alábbiakra van szükség:

- Windows Server 2012 R2 és AD FS
- [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 vagy újabb verzió

A 1.1.819.0 verziótól kezdődően a Azure AD Connect tartalmaz egy varázslót, amely használatával konfigurálhatja a hibrid Azure AD Joint. A varázsló jelentősen leegyszerűsíti a konfigurációs folyamatot. A kapcsolódó varázsló:

- Konfigurálja a szolgáltatáskapcsolódási pontokat (szolgáltatáskapcsolódási pontok) az eszköz regisztrálásához.
- Igazolja a meglévő Azure AD-beli függő entitás megbízhatóságát
- Frissíti az Azure AD megbízhatósági jogcímszabályait

A cikkben szereplő konfigurációs lépések a Azure AD Connect varázsló használatával foglalkoznak. Ha a Azure AD Connect korábbi verziója van telepítve, a varázsló használatához frissítenie kell a 1.1.819 vagy újabb verzióra. Ha a Azure AD Connect legújabb verzióját telepíti, a következő témakörben talál további információt: [hibrid Azure ad JOIN manuális konfigurálása](hybrid-azuread-join-manual.md).

A hibrid Azure AD-csatlakozáshoz az eszközöknek a szervezet hálózatán belüli alábbi Microsoft-erőforrásokhoz való hozzáférésre van szükségük:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Szervezete biztonsági jogkivonat-szolgáltatása (STS) (összevont tartományokhoz)
- `https://autologon.microsoftazuread-sso.com` (Ha a vagy a-t használja, és nem szeretné használni a zökkenőmentes SSO-t)

> [!WARNING]
> Ha a szervezet olyan proxykiszolgálót használ, amelyek az SSL-forgalmat az adatveszteség-megelőzési vagy az Azure AD-bérlői korlátozások miatt észlelik, ügyeljen arra, hogy a (z) "" forgalom ne legyen https://device.login.microsoftonline.com kizárva a TLS-megszakítás és-vizsgálat alól. A (z) "" kizárása az https://device.login.microsoftonline.com ügyféltanúsítvány-alapú hitelesítés zavarásával járhat, ami problémákat okoz az eszközök regisztrációja és az eszközön alapuló feltételes hozzáférés miatt.

A Windows 10 1803-es verziójától kezdve, ha a AD FS sikertelen használatával összevont környezethez tartozó, hibrid Azure AD-csatlakozás nem sikerül, a Azure AD Connect az Azure AD-ban lévő számítógép-objektum szinkronizálására támaszkodunk, amelyet később a hibrid Azure AD JOIN eszköz regisztrációjának befejezéséhez használtak. Ellenőrizze, hogy a Azure AD Connect szinkronizálta-e azon eszközök számítógép-objektumait, amelyeket hibrid Azure AD-hez csatlakoztatni szeretne az Azure AD-hez. Ha a számítógép-objektumok meghatározott szervezeti egységekhez (OU-hoz) tartoznak, akkor a szervezeti egységeket is konfigurálnia kell Azure AD Connectban való szinkronizáláshoz. Ha többet szeretne megtudni a számítógép-objektumok Azure AD Connect használatával történő szinkronizálásáról, tekintse meg a [szűrés konfigurálása Azure ad Connect használatával](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)című témakört.

Ha a szervezete egy kimenő proxyn keresztül fér hozzá az internethez, a Microsoft a [webproxy automatikus felderítésének (WPAD) megvalósítását](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) javasolja a Windows 10-es számítógépek Azure ad-vel való regisztrációjának engedélyezéséhez. Ha a WPAD konfigurálásával és kezelésével kapcsolatos problémákat tapasztal, tekintse meg az [automatikus észlelés hibaelhárítása](/previous-versions/tn-archive/cc302643(v=technet.10))című témakört. 

Ha nem használ WPAD-t, és szeretné konfigurálni a proxybeállításokat a számítógépen, a Windows 10 1709-es verziójától kezdve is megteheti. További információ: [a WinHTTP-beállítások konfigurálása csoportházirend-objektummal (GPO)](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Ha a proxybeállításokat a WinHTTP-beállítások használatával konfigurálja a számítógépen, a konfigurált proxyhoz nem csatlakoztatható számítógépek nem fognak csatlakozni az internethez.

Ha a szervezete hitelesített kimenő proxyn keresztül fér hozzá az internethez, meg kell győződnie arról, hogy a Windows 10 rendszerű számítógépek sikeresen hitelesíteni tudják a kimenő proxyt. Mivel a Windows 10 rendszerű számítógépek gépi környezettel futtatják az eszközök regisztrációját, a kimenő proxy hitelesítését a gépi környezet használatával kell konfigurálni. A konfiguráció követelményeivel kapcsolatban forduljon a kimenő proxy szolgáltatójához.

Annak ellenőrzéséhez, hogy az eszköz képes-e hozzáférni a fenti Microsoft-erőforrásokhoz a rendszerfiókban, használhatja a [teszt eszköz regisztrációjának kapcsolati](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) parancsfájlját.

## <a name="configure-hybrid-azure-ad-join"></a>A hibrid Azure AD-csatlakozás konfigurálása

Ha Azure AD Connect használatával szeretne hibrid Azure AD-csatlakozást konfigurálni, a következőkre lesz szüksége:

- Az Azure AD-bérlő globális rendszergazdájának hitelesítő adatai  
- Az egyes erdők vállalati rendszergazdai hitelesítő adatai
- A AD FS rendszergazdája hitelesítő adatai

**Hibrid Azure ad-csatlakozás konfigurálása Azure ad Connect használatával**:

1. Indítsa el Azure AD Connect, majd válassza a **Konfigurálás** lehetőséget.

   ![Üdvözlőképernyő](./media/hybrid-azuread-join-federated-domains/11.png)

1. A **További feladatok** lapon válassza az **eszközbeállítások konfigurálása** lehetőséget, majd kattintson a **tovább** gombra.

   ![További feladatok](./media/hybrid-azuread-join-federated-domains/12.png)

1. Az **Áttekintés** lapon válassza a **tovább** lehetőséget.

   ![Áttekintés](./media/hybrid-azuread-join-federated-domains/13.png)

1. A **Kapcsolódás az Azure ad-hoz** lapon adja meg az Azure ad-bérlő globális rendszergazdájának hitelesítő adatait, majd kattintson a **tovább** gombra.

   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/hybrid-azuread-join-federated-domains/14.png)

1. Az **eszközbeállítások** lapon válassza a **hibrid Azure ad-csatlakozás konfigurálása** lehetőséget, majd kattintson a **tovább** gombra.

   ![Eszközbeállítások](./media/hybrid-azuread-join-federated-domains/15.png)

1. Az **SCP** lapon végezze el a következő lépéseket, majd válassza a **Next (tovább**) gombot:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Válassza ki az erdőt.
   1. Válassza ki a hitelesítési szolgáltatást. Ki kell választania **AD FS kiszolgálót** , kivéve, ha a szervezete kizárólag Windows 10-es ügyfeleket használ, és konfigurálta a számítógép/eszköz szinkronizálását, vagy ha a szervezete zökkenőmentes egyszeri bejelentkezést használ.
   1. Válassza a **Hozzáadás** lehetőséget a vállalati rendszergazda hitelesítő adatainak megadásához.

1. Az **eszköz operációs rendszerek** lapján válassza ki azokat az operációs rendszereket, amelyeket az Active Directory környezetében lévő eszközök használnak, majd válassza a **tovább** lehetőséget.

   ![Eszköz operációs rendszere](./media/hybrid-azuread-join-federated-domains/17.png)

1. Az **összevonási konfiguráció** lapon adja meg a AD FS rendszergazdája hitelesítő adatait, majd kattintson a **tovább** gombra.

   ![Összevonás konfigurálása](./media/hybrid-azuread-join-federated-domains/18.png)

1. A **konfigurálásra kész** lapon válassza a **Konfigurálás** lehetőséget.

   ![Ready to configure (Konfigurálásra kész)](./media/hybrid-azuread-join-federated-domains/19.png)

1. A **konfiguráció kész** lapon válassza a **Kilépés** lehetőséget.

   ![A konfigurálás befejeződött](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Régebbi Windows-eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök némelyike Windows régebbi verziójú eszközök, a következőket kell tennie:

- A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz
- A Microsoft Workplace Join telepítése régebbi Windows rendszerű számítógépekhez

> [!NOTE]
> A Windows 7 támogatása 2020 január 14-én fejeződött be. További információ: [a Windows 7 támogatása befejeződött](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz

A Windows régebbi verziójú eszközök hibrid Azure AD-csatlakozásának sikeres elvégzéséhez, valamint a tanúsítványok az Azure AD-be való hitelesítésének elkerülése érdekében leküldheti a szabályzatot a tartományhoz csatlakoztatott eszközökre, hogy hozzáadja a következő URL-címeket a helyi intranet zónához az Internet Explorerben:

- `https://device.login.microsoftonline.com`
- Szervezete STS (összevont tartományok esetében)
- `https://autologon.microsoftazuread-sso.com` (Zökkenőmentes SSO esetén)

Emellett engedélyeznie kell a **frissítések állapotát az állapotsoron** a felhasználó helyi intranet zónájában lévő parancsfájl használatával.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>A Microsoft Workplace Join telepítése régebbi Windows rendszerű számítógépekhez

A Windows régebbi verziójú eszközök regisztrálásához a szervezeteknek telepíteniük kell [a Microsoft Workplace Joint a nem Windows 10 rendszerű számítógépekre](https://www.microsoft.com/download/details.aspx?id=53554). A nem Windows 10 rendszerű számítógépekhez készült Microsoft Workplace Join a Microsoft letöltőközpontban érhető el.

A csomagot központilag telepítheti egy szoftverterjesztési rendszer, például a [Microsoft Endpoint Configuration Manager](/configmgr/)használatával. A csomag támogatja a standard csendes telepítési beállításokat a `quiet` paraméterrel. A Configuration Manager aktuális ága a korábbi verziók előnyeit kínálja, mint például a befejezett regisztrációk nyomon követésének lehetősége.

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
3. Ha a **regisztrált** oszlop **függőben** van, akkor a hibrid Azure ad-csatlakozás nem fejeződött be. Összevont környezetekben ez csak akkor fordulhat elő, ha a regisztráció sikertelen volt, és a HRE-kapcsolat az eszközök szinkronizálására van konfigurálva.
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

Ha a tartományhoz csatlakoztatott Windows-eszközök hibrid Azure AD-csatlakozásának befejezésével kapcsolatos problémákat tapasztal, tekintse meg a következőt:

- [Eszközök hibaelhárítása a dsregcmd paranccsal](./troubleshoot-device-dsregcmd.md)
- [Hibrid Azure AD-csatlakozás a Windows aktuális eszközeihez – problémamegoldás](troubleshoot-hybrid-join-windows-current.md)
- [A hibrid Azure AD JOIN használata a Windows régebbi verziójú eszközökhöz](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [kezelheti az eszközök identitásait a Azure Portal használatával](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png