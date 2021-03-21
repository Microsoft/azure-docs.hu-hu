---
title: 'Azure AD Connect: ADSync szolgáltatás fiókja | Microsoft Docs'
description: Ez a témakör a ADSync szolgáltatásfiókot ismerteti, és a fiókkal kapcsolatos ajánlott eljárásokat tartalmazza.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dddfb8426b769c06cb5b7494431b7eee34dbf9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94410895"
---
# <a name="adsync-service-account"></a>ADSync-szolgáltatásfiók
Azure AD Connect egy helyszíni szolgáltatást telepít, amely összehangolja Active Directory és Azure Active Directory közötti szinkronizálást.  A Microsoft Azure AD Sync synchronization Service (ADSync) a helyszíni környezetben futó kiszolgálón fut.  A szolgáltatás hitelesítő adatai alapértelmezés szerint az expressz telepítésekben vannak beállítva, de testreszabhatók a szervezeti biztonsági követelmények teljesítéséhez.  Ezek a hitelesítő adatok nem használhatók a helyszíni erdőkhöz vagy Azure Active Directoryhoz való kapcsolódáshoz.

A ADSync-szolgáltatásfiók kiválasztása fontos tervezési döntés a Azure AD Connect telepítésének megkezdése előtt.  Ha a telepítés után módosítani szeretné a hitelesítő adatokat, a szolgáltatás nem indul el, így elveszti a hozzáférést a szinkronizálási adatbázishoz, és nem tud hitelesíteni a csatlakoztatott címtárakkal (Azure és AD DS).  Az eredeti hitelesítő adatok visszaállítása után nem történik szinkronizálás.

## <a name="the-default-adsync-service-account"></a>Az alapértelmezett ADSync-szolgáltatásfiók

Ha egy tagkiszolgálón fut, a AdSync szolgáltatás egy Virtual Service-fiók (VSA) kontextusában fut.  A termékek korlátozása miatt a rendszer létrehoz egy egyéni szolgáltatásfiókot a tartományvezérlőre történő telepítéskor.  Ha az Express Settings szolgáltatás fiókja nem felel meg a szervezet biztonsági követelményeinek, telepítse a Azure AD Connect a Testreszabás lehetőség választásával.  Ezután válassza ki a szolgáltatási fiók lehetőséget, amely megfelel a szervezet követelményeinek.

>[!NOTE]
>A tartományvezérlőn telepített alapértelmezett szolgáltatásfiók a következő: tartomány \ AAD_InstallationIdentifier.  A fiók jelszava véletlenszerűen jön létre, és jelentős kihívást jelent a helyreállítás és a jelszó elforgatása terén.  A Microsoft egy önálló vagy csoportosan felügyelt szolgáltatásfiók (önállóan felügyelt szolgáltatásfiókot/gMSA) használatára javasolja a szolgáltatásfiók testreszabását a tartományvezérlő kezdeti telepítése során.

|Azure AD Connect helye|Szolgáltatásfiók létrehozva|
|-----|-----|
|Tagkiszolgáló|NT SERVICE\ADSync|
|Tartományvezérlő|Tartomány \ AAD_74dc30c01e80 (lásd: Megjegyzés)|

## <a name="custom-adsync-service-accounts"></a>Egyéni ADSync-szolgáltatásfiókok
A Microsoft a ADSync szolgáltatás futtatását javasolja egy virtuális szolgáltatásfiók vagy egy önálló vagy csoportosan felügyelt szolgáltatásfiók kontextusában.  A tartományi rendszergazda dönthet úgy is, hogy az adott szervezeti biztonsági követelményeknek való megfelelés érdekében kiépített szolgáltatásfiókot is létrehoz.   A telepítés során használt szolgáltatásfiók testreszabásához válassza a Testreszabás lehetőséget az alábbi expressz beállítások oldalon.   A következő lehetőségek érhetők el:

- alapértelmezett fiók – a Azure AD Connect a fentiekben leírtak szerint fogja kiépíteni a szolgáltatásfiókot
- felügyelt szolgáltatásfiók – a rendszergazda által kiépített önálló vagy csoportos MSA használata
- tartományi fiók – a rendszergazda által kiépített tartományi szolgáltatási fiók használata

![Képernyőfelvétel a Azure AD Connect Express Settings lapról "Testreszabás" vagy "expressz beállítások használata" lehetőséggel.](media/concept-adsync-service-account/adsync1.png)

![Képernyőkép a Azure AD Connect "a szükséges összetevők telepítése" oldalon, a meglévő felügyelt szolgáltatásfiók használatára vonatkozó lehetőséggel.](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>A ADSync-szolgáltatásfiók változásainak diagnosztizálása
Ha a telepítés után megváltoztatja a ADSync szolgáltatás hitelesítő adatait, a szolgáltatás nem indul el, így elveszti a hozzáférést a szinkronizálási adatbázishoz, és nem tud hitelesíteni a csatlakoztatott címtárakkal (Azure és AD DS).  Az adatbázis-hozzáférés engedélyezése az új ADSync-szolgáltatásfiók számára nem elegendő a probléma helyreállításához. Az eredeti hitelesítő adatok visszaállítása után nem történik szinkronizálás.

A ADSync szolgáltatás egy hibaüzenetet ad ki az eseménynaplóba, amikor nem tud elindulni.  Az üzenet tartalma attól függően változhat, hogy a beépített adatbázis (localdb) vagy a teljes SQL használatban van-e.  A következő példák az Eseménynapló azon bejegyzéseire mutatnak, amelyek lehetnek jelen.

### <a name="example-1"></a>1\. példa

Nem található a AdSync szolgáltatás titkosítási kulcsa, és a rendszer újból létrehozta őket.  A szinkronizálás a probléma kijavítása után nem történik meg.

A probléma elhárítása: Microsoft Azure AD szinkronizálási titkosítási kulcsok elérhetetlenné válnak, ha módosulnak a AdSync szolgáltatás bejelentkezési hitelesítő adatai.  Ha a hitelesítő adatok módosultak, a Services alkalmazással állítsa vissza a bejelentkezési fiókot az eredetileg konfigurált értékre (pl. NT SERVICE\AdSync), és indítsa újra a szolgáltatást.  Ez azonnal visszaállítja a AdSync szolgáltatás helyes működését.

További információért tekintse meg a következő [cikket](./whatis-hybrid-identity.md) .

### <a name="example-2"></a>2\. példa

A szolgáltatás nem indult el, mert nem sikerült csatlakozni a helyi adatbázishoz (localdb).

A probléma elhárítása: az Microsoft Azure AD Sync szolgáltatás elveszti az engedélyt a helyi adatbázis-szolgáltató elérésére, ha a AdSync szolgáltatás bejelentkezési hitelesítő adatai módosulnak.  Ha a hitelesítő adatok módosultak, használja a Services alkalmazást, hogy a bejelentkezési fiókot visszaállítsa az eredetileg konfigurált értékre (pl. NT SERVICE\AdSync), és indítsa újra a szolgáltatást.  Ez azonnal visszaállítja a AdSync szolgáltatás helyes működését.

További információért tekintse meg a következő [cikket](./whatis-hybrid-identity.md) .

További részletek a szolgáltató a következő hibaüzeneteket adta vissza:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).