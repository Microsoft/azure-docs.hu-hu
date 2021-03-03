---
title: Tanúsítvány megújítása a Microsoft 365 és az Azure AD-felhasználók számára | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy Microsoft 365 felhasználók Hogyan oldhatók meg a tanúsítványok megújításával kapcsolatos, e-mailekkel kapcsolatos problémák.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: effdd156858caf5717aac92433e8bc5f4f6147ad
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686869"
---
# <a name="renew-federation-certificates-for-microsoft-365-and-azure-active-directory"></a>Microsoft 365 és Azure Active Directory összevonási tanúsítványainak megújítása
## <a name="overview"></a>Áttekintés
Az Azure Active Directory (Azure AD) és a Active Directory összevonási szolgáltatások (AD FS) (AD FS) közötti sikeres összevonáshoz a biztonsági jogkivonatok Azure AD-be való aláírásához a AD FS által használt tanúsítványoknak meg kell egyezniük az Azure AD-ben konfigurált beállításokkal. Az eltérések megszakadó megbízhatóságot okozhatnak. Az Azure AD biztosítja, hogy ezek az információk szinkronban legyenek a AD FS és a webalkalmazás-proxy telepítésekor (extranetes hozzáférés esetén).

Ez a cikk további információkat tartalmaz a jogkivonat-aláíró tanúsítványok kezeléséhez és az Azure AD-vel való szinkronizálás megtartásához a következő esetekben:

* Nem telepíti a webalkalmazás-proxyt, ezért az összevonási metaadatok nem érhetők el az extraneten.
* Nem az AD FS alapértelmezett konfigurációját használja a jogkivonat-aláíró tanúsítványokhoz.
* Külső gyártótól származó identitás-szolgáltatót használ.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>AD FS alapértelmezett konfigurációja a jogkivonat-aláíró tanúsítványokhoz
A jogkivonat-aláírás és a jogkivonat-visszafejtő tanúsítványok általában önaláírt tanúsítványok, és egy évig jók. Alapértelmezés szerint a AD FS tartalmaz egy **autocertificaterollover beállítást** nevű automatikus megújítási folyamatot. Ha AD FS 2,0-es vagy újabb verziót használ, Microsoft 365 és az Azure AD automatikusan frissíti a tanúsítványt a lejárat előtt.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Megújítási értesítés a Microsoft 365 felügyeleti központból vagy egy e-mailből
> [!NOTE]
> Ha az Office-tanúsítvány megújítását kérő e-mailt vagy egy portálról szóló értesítést kapott, tekintse meg a következő témakört: a [jogkivonat-aláíró tanúsítványok módosításainak kezelése](#managecerts) , és ellenőrizze, hogy szükséges-e bármilyen művelet. A Microsoft tisztában van azzal a lehetséges hibával, hogy értesítéseket küldhet a tanúsítvány megújításáról, még akkor is, ha nincs szükség beavatkozásra.
>
>

Az Azure AD megkísérli az összevonási metaadatok figyelését, és frissíti a jogkivonat-aláíró tanúsítványokat a metaadatokban jelzett módon. a jogkivonat-aláíró tanúsítványok lejárta előtt 30 nappal az Azure AD ellenőrzi, hogy az összevonási metaadatok lekérdezésével új tanúsítványok érhetők-e el.

* Ha sikeresen lekérdezheti az összevonási metaadatokat, és lekérheti az új tanúsítványokat, a felhasználónak nincs e-mail-értesítés vagy figyelmeztetés a Microsoft 365 felügyeleti központban.
* Ha nem tudja beolvasni az új jogkivonat-aláíró tanúsítványokat, mert az összevonási metaadatok nem érhetők el, vagy ha nincs engedélyezve az automatikus tanúsítvány-átváltási lehetőség, az Azure AD e-mailben értesítést küld, és figyelmeztetést ad a Microsoft 365 felügyeleti központban.

![Office 365-portál – értesítés](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Ha AD FS használ, az üzletmenet folytonosságának biztosítása érdekében ellenőrizze, hogy a kiszolgálók rendelkeznek-e a következő frissítésekkel, hogy az ismert problémákra vonatkozó hitelesítési hibák ne legyenek. Ez a megújítási és jövőbeli megújítási időszakok ismert AD FS proxykiszolgáló-problémáinak enyhítésére szolgál:
>
> Kiszolgáló 2012 R2 – a [Windows Server 2014](https://support.microsoft.com/kb/2955164) -es verziójának összesítése
>
> A Server 2008 R2 és a 2012 – [a proxyn keresztüli hitelesítés meghiúsul a Windows Server 2012 vagy a windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446) rendszerben
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Ellenőrizze, hogy a tanúsítványokat frissíteni kell-e <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>1. lépés: a Autocertificaterollover beállítást állapotának keresése
A AD FS-kiszolgálón nyissa meg a PowerShellt. Győződjön meg arról, hogy a Autocertificaterollover beállítást értéke TRUE (igaz).

```azurepowershell-interactive
Get-Adfsproperties
```

![Autocertificaterollover beállítást](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Ha a AD FS 2,0-et használja, először futtassa Add-Pssnapin Microsoft. ADFS. PowerShell eszközt.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>2. lépés: annak ellenőrzése, hogy a AD FS és az Azure AD szinkronban van-e
A AD FS-kiszolgálón nyissa meg a MSOnline PowerShell-parancssort, és kapcsolódjon az Azure AD-hez.

> [!NOTE]
> MSOL-Cmdlets a MSOnline PowerShell-modul részét képezik.
> A MSOnline PowerShell-modult közvetlenül a PowerShell-galéria töltheti le.
> 
>

```azurepowershell-interactive
Install-Module MSOnline
```

Kapcsolódjon az Azure AD-hez a MSOnline PowerShell-modul használatával.

```azurepowershell-interactive
Import-Module MSOnline
Connect-MsolService
```

Tekintse át a AD FS és az Azure AD-megbízhatóság tulajdonságaiban konfigurált tanúsítványokat a megadott tartományhoz.

```azurepowershell-interactive
Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate
```

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Ha a ujjlenyomatai megfelelnek mindkét kimenet egyezik, a tanúsítványok szinkronban vannak az Azure AD-vel.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>3. lépés: Ellenőrizze, hogy a tanúsítvány hamarosan lejár-e
A Get-MsolFederationProperty vagy a Get-AdfsCertificate kimenetében keresse meg a "nem után" dátumot. Ha a dátum kevesebb, mint 30 nap, el kell végeznie a műveletet.

| Autocertificaterollover beállítást | Az Azure AD-vel szinkronizált tanúsítványok | Az összevonási metaadatok nyilvánosan elérhetők | Érvényességi | Művelet |
|:---:|:---:|:---:|:---:|:---:|
| Igen |Igen |Igen |- |Nincs szükség művelet végrehajtására. Lásd: [jogkivonat-aláíró tanúsítvány automatikus megújítása](#autorenew). |
| Igen |Nem |- |Kevesebb, mint 15 nap |Azonnali megújítás. Lásd: [jogkivonat-aláíró tanúsítvány manuális megújítása](#manualrenew). |
| Nem |- |- |Kevesebb mint 30 nap |Azonnali megújítás. Lásd: [jogkivonat-aláíró tanúsítvány manuális megújítása](#manualrenew). |

\[-] Nem számít

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>A jogkivonat-aláíró tanúsítvány automatikus megújítása (ajánlott) <a name="autorenew"></a>
Nem kell manuális lépéseket végrehajtania, ha az alábbiak mindegyike teljesül:

* Telepítette a webalkalmazás-proxyt, amely lehetővé teszi az összevonás-metaadatok elérését az extranetről.
* A AD FS alapértelmezett konfigurációját használja (a Autocertificaterollover beállítást engedélyezve van).

A következő ellenőrzésével ellenőrizheti, hogy a tanúsítvány automatikusan frissíthető-e.

**1. a AD FS tulajdonság Autocertificaterollover beállítást True értékre kell állítani.** Ez azt jelzi, hogy AD FS automatikusan új jogkivonat-aláírási és jogkivonat-visszafejtési tanúsítványokat hoz majd, mielőtt a régiek lejárnak.

**2. a AD FS összevonási metaadatok nyilvánosan elérhetők.** Győződjön meg arról, hogy az összevonási metaadatok nyilvánosan elérhetők, ha a nyilvános interneten (a vállalati hálózaton kívül) lévő számítógép következő URL-címére navigál:

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

ahol a `(your_FS_name)` helyére a szervezet által használt összevonási szolgáltatás állomásneve kerül, például FS.contoso.com.  Ha mindkét beállítást sikeresen ellenőrizni tudja, nem kell mást tennie.  

Például: `https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml`
## <a name="renew-the-token-signing-certificate-manually"></a>Jogkivonat-aláíró tanúsítvány manuális megújítása <a name="manualrenew"></a>
Dönthet úgy, hogy manuálisan megújítja a jogkivonat-aláíró tanúsítványokat. Előfordulhat például, hogy a következő forgatókönyvek jobban működnek a manuális megújítás érdekében:

* A jogkivonat-aláíró tanúsítványok nem önaláírt tanúsítványok. Ennek a leggyakoribb oka az, hogy a szervezet felügyeli AD FS a szervezeti hitelesítésszolgáltatótól beléptetett tanúsítványokat.
* A hálózati biztonság nem teszi lehetővé az összevonási metaadatok nyilvánosan elérhetővé tételét.

Ezekben a forgatókönyvekben minden alkalommal, amikor frissíti a jogkivonat-aláíró tanúsítványokat, frissítenie kell a Microsoft 365 tartományt is a PowerShell-paranccsal, az Update-MsolFederatedDomain használatával.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>1. lépés: Győződjön meg arról, hogy a AD FS új jogkivonat-aláíró tanúsítványokkal rendelkezik
**Nem alapértelmezett konfiguráció**

Ha a AD FS nem alapértelmezett konfigurációját használja (ha a **Autocertificaterollover beállítást** **hamis** értékre van állítva), akkor valószínűleg egyéni tanúsítványokat (nem önaláírt) használ. Az AD FS jogkivonat-aláíró tanúsítványok megújításával kapcsolatos további információkért lásd: az [összevont kiszolgálók tanúsítványokra vonatkozó követelményei](/windows-server/identity/ad-fs/design/certificate-requirements-for-federation-servers).

**Az összevonási metaadatok nem nyilvánosan elérhetők**

Ha viszont az **autocertificaterollover beállítást** értéke **true (igaz**), de az összevonási metaadatok nem nyilvánosan elérhetők, először győződjön meg arról, hogy a AD FS új jogkivonat-aláíró tanúsítványokat generált. A következő lépések végrehajtásával erősítse meg, hogy új jogkivonat-aláíró tanúsítványokat használ:

1. Ellenőrizze, hogy be van-e jelentkezve az elsődleges AD FS-kiszolgálóra.
2. Tekintse át a AD FS aktuális aláíró tanúsítványait egy PowerShell-parancssorablak megnyitásával, és futtassa a következő parancsot:

    PS C: \> Get-ADFSCertificate – CertificateType jogkivonat – aláírás

   > [!NOTE]
   > Ha a AD FS 2,0-et használja, először futtassa Add-Pssnapin Microsoft. ADFS. PowerShell eszközt.
   >
   >
3. Tekintse meg a parancs kimenetét a felsorolt tanúsítványokban. Ha AD FS új tanúsítványt generált, akkor két tanúsítványt kell látnia a kimenetben: az egyiket, amelynél a **IsPrimary** értéke **igaz** , és a közelmúltbeli dátum 5 napon **belül van,** és az egyik, hogy a **IsPrimary** **hamis** , **és a** legkésőbbi egy évig.
4. Ha csak egy tanúsítvány jelenik meg, és a még **5 napon belül** megjelenő dátum, új tanúsítványt kell létrehoznia.
5. Új tanúsítvány létrehozásához futtassa a következő parancsot egy PowerShell-parancssorban: `PS C:\Update-ADFSCertificate –CertificateType token-signing` .
6. A frissítés ellenőrzéséhez futtassa újra a következő parancsot: PS C: \> Get-ADFSCertificate – CertificateType jogkivonat-aláírás

Most két tanúsítványnak kell szerepelnie, amelyek közül az egyik **egy még egy év** múlva a későbbiekben, a **IsPrimary** értéke pedig **hamis**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-microsoft-365-trust"></a>2. lépés: a Microsoft 365 megbízhatóságának új jogkivonat-aláíró tanúsítványainak frissítése
Frissítse Microsoft 365 a megbízhatósághoz használni kívánt új jogkivonat-aláíró tanúsítványokkal, az alábbiak szerint.

1. Nyissa meg a Microsoft Azure Active Directory modult a Windows PowerShellhez.
2. Futtassa $cred = Get-hitelesítő adatokat. Ha ez a parancsmag kéri a hitelesítő adatok megadását, írja be a Cloud Service rendszergazdai fiókjának hitelesítő adatait.
3. Futtassa Connect-MsolService – hitelesítő adatok $cred. Ez a parancsmag csatlakozik a Cloud Service-hez. Az eszköz által telepített további parancsmagok futtatása előtt a Cloud Service-hez csatlakozó környezet létrehozása szükséges.
4. Ha ezeket a parancsokat olyan számítógépen futtatja, amely nem a AD FS elsődleges összevonási kiszolgáló, akkor futtassa Set-MSOLAdfscontext-Computer &lt; AD FS elsődleges kiszolgálót &gt; , ahol a &lt; AD FS elsődleges kiszolgáló az &gt; elsődleges AD FS-kiszolgáló belső FQDN-neve. Ez a parancsmag olyan környezetet hoz létre, amely összekapcsolja a AD FS.
5. Update-MSOLFederatedDomain – tartománynév &lt; tartomány futtatása &gt; . Ez a parancsmag a AD FS beállításait a Cloud Service-be frissíti, és konfigurálja a két közötti megbízhatósági kapcsolatot.

> [!NOTE]
> Ha több legfelső szintű tartományt is támogatnia kell (például contoso.com és fabrikam.com), akkor a **SupportMultipleDomain** kapcsolót minden parancsmaggal együtt kell használnia. További információ: [több felső szintű tartomány támogatása](how-to-connect-install-multiple-domains.md).
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Az Azure AD-megbízhatóság javítása Azure AD Connect használatával <a name="connectrenew"></a>
Ha a AD FS farmját és az Azure AD-megbízhatóságot a Azure AD Connect használatával konfigurálta, akkor a Azure AD Connect használatával észlelheti, hogy szükséges-e a jogkivonat-aláíró tanúsítványoknak bármilyen művelete. Ha meg kell újítania a tanúsítványokat, a Azure AD Connect is használhatja.

További információ: [a megbízhatóság javítása](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>A AD FS és az Azure AD-tanúsítvány frissítésének lépései
A jogkivonat-aláíró tanúsítványok standard X509 tanúsítványok, amelyek az összevonási kiszolgáló által kiállított összes token biztonságos aláírására szolgálnak. A jogkivonat-visszafejtési tanúsítványok standard X509 tanúsítványok, amelyek a bejövő jogkivonatok visszafejtésére szolgálnak. 

Alapértelmezés szerint a AD FS úgy van konfigurálva, hogy jogkivonat-aláírási és jogkivonat-visszafejtési tanúsítványokat állítson be automatikusan, mind a kezdeti konfigurációs időpontban, mind a tanúsítványok lejárati dátumának megközelítve.

Az Azure AD az aktuális tanúsítvány lejárta előtt 30 nappal megpróbálja lekérni az összevonási szolgáltatás metaadatainak új tanúsítványát. Ha az adott időpontban nem érhető el új tanúsítvány, az Azure AD továbbra is rendszeresen figyeli a metaadatokat a napi rendszerességgel. Amint az új tanúsítvány elérhető a metaadatokban, a tartományhoz tartozó összevonási beállítások frissülnek az új tanúsítvány adataival. A használatával `Get-MsolDomainFederationSettings` ellenőrizheti, hogy az új tanúsítvány megjelenik-e az NextSigningCertificate/SigningCertificate.

További információ a jogkivonat-aláíró tanúsítványokról AD FS lásd: [jogkivonat-aláírási és jogkivonat-visszafejtési tanúsítványok beszerzése és konfigurálása ad FShoz](/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
