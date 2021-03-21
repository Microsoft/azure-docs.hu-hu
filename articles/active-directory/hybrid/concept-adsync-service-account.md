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
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb23d79caa6964c3f61fbb84c8b8f229f475b8ab
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722157"
---
# <a name="adsync-service-account"></a>ADSync-szolgáltatásfiók
Azure AD Connect egy helyszíni szolgáltatást telepít, amely összehangolja Active Directory és Azure Active Directory közötti szinkronizálást.  A Microsoft Azure AD Sync synchronization Service (ADSync) a helyszíni környezetben futó kiszolgálón fut.  A szolgáltatás hitelesítő adatai alapértelmezés szerint az expressz telepítésekben vannak beállítva, de testreszabhatók a szervezeti biztonsági követelmények teljesítéséhez.  Ezek a hitelesítő adatok nem használhatók a helyszíni erdőkhöz vagy Azure Active Directoryhoz való kapcsolódáshoz.

A ADSync-szolgáltatásfiók kiválasztása fontos tervezési döntés a Azure AD Connect telepítésének megkezdése előtt.  Ha a telepítés után módosítani szeretné a hitelesítő adatokat, a szolgáltatás nem indul el, így elveszti a hozzáférést a szinkronizálási adatbázishoz, és nem tud hitelesíteni a csatlakoztatott címtárakkal (Azure és AD DS).  Az eredeti hitelesítő adatok visszaállítása után nem történik szinkronizálás.

A szinkronizálási szolgáltatás más fiókokban is futhat. Virtuális szolgáltatásfiókot (VSA), felügyelt szolgáltatásfiókot (gMSA/önállóan felügyelt szolgáltatásfiókot) vagy normál felhasználói fiókot is futtathat. A támogatott beállítások megváltoztak a 2017-es és a 2021-es márciusi kiadásban, Azure AD Connect új telepítés esetén. Ha Azure AD Connect korábbi kiadásáról frissít, ezek a további beállítások nem érhetők el. 


|Fiók típusa|Telepítési lehetőség|Description| 
|-----|------|-----|
|Virtual Service-fiók|Express és Custom, 2017 április és újabb| A rendszer minden expressz telepítéshez egy virtuális szolgáltatásfiókot használ, kivéve a tartományvezérlők telepítéseit. Egyéni telepítés használata esetén ez az alapértelmezett beállítás, kivéve, ha egy másik lehetőség van használatban.| 
|Felügyelt szolgáltatásfiók|Custom, 2017 április és újabb|Ha távoli SQL Server használ, azt javasoljuk, hogy egy csoportosan felügyelt szolgáltatásfiókot használjon. |
|Felügyelt szolgáltatásfiók|Express és Custom, 2021 március és újabb|A (z) ADSyncMSA_tel előre rögzített önálló felügyelt szolgáltatásfiók a telepítés során jön létre, ha tartományvezérlőre van telepítve. Egyéni telepítés használata esetén ez az alapértelmezett beállítás, kivéve, ha egy másik lehetőség van használatban.|
|Felhasználói fiók|Express és Custom, 2017 április – 2021 március|A (z) AAD_tel előtaggal rendelkező felhasználói fiók az expressz telepítés során jön létre, amikor a tartományvezérlőre van telepítve. Egyéni telepítés használata esetén ez az alapértelmezett beállítás, kivéve, ha egy másik lehetőség van használatban.|
|Felhasználói fiók|Express és Custom, 2017 március és korábbi verziók|Az expressz telepítés során létrejön egy AAD_-előtaggal rendelkező felhasználói fiók. Egyéni telepítés használata esetén egy másik fiók is megadható.| 

>[!IMPORTANT]
> Ha a (z) 2017 március vagy korábbi verzióról származó buildet használ, ne állítsa alaphelyzetbe a szolgáltatásfiók jelszavát, mivel a Windows biztonsági okokból elpusztítja a titkosítási kulcsokat. A fiók nem módosítható más fiókra Azure AD Connect újratelepítése nélkül. Ha az 2017 április vagy újabb verzióra frissít egy buildre, akkor a szolgáltatás fiókjának jelszava módosítható, de a használt fiók nem módosítható. 

> [!IMPORTANT]
> A szolgáltatásfiókot csak az első telepítéskor lehet beállítani. A szolgáltatásfiók módosítása a telepítés befejezése után nem támogatott. Ha módosítania kell a szolgáltatásfiók jelszavát, akkor ez támogatott, és [itt](how-to-connect-sync-change-serviceacct-pass.md)talál útmutatást.

A következő táblázat a Sync Service-fiók alapértelmezett, ajánlott és támogatott beállításait tartalmazza. 

Jelmagyarázat: 

- A **Bold** az alapértelmezett beállítást jelzi, a legtöbb esetben az ajánlott beállítást. 
- A *dőlt* érték azt jelzi, hogy a javasolt beállítás nem az alapértelmezett beállítás. 
- Nem félkövérrel támogatott beállítás 
- Helyi fiók – helyi felhasználói fiók a kiszolgálón 
- Tartományi fiók – tartományi felhasználói fiók 
- Önállóan felügyelt szolgáltatásfiókot – [önálló felügyelt szolgáltatásfiók](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA – [csoportosan felügyelt szolgáltatásfiók](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

 ||**LocalDB </br> Express**|**LocalDB/LocalSQL </br> Custom**|**Távoli SQL- </br> Egyéni**|
|-----|-----|-----|-----|
|**tartományhoz csatlakoztatott számítógép**|**VSA**|**VSA**</br> *Önállóan felügyelt szolgáltatásfiókot*</br> *gMSA*</br> Helyi fiók</br> Tartományi fiók| *gMSA* </br>Tartományi fiók|
|Tartományvezérlő| **Önállóan felügyelt szolgáltatásfiókot**|**Önállóan felügyelt szolgáltatásfiókot** </br>*gMSA*</br> Tartományi fiók|*gMSA*</br>Tartományi fiók| 

## <a name="virtual-service-account"></a>Virtual Service-fiók 

A virtuális szolgáltatásfiók olyan speciális felügyelt helyi fiók, amely nem rendelkezik jelszóval, és a Windows automatikusan kezeli. 

 ![Virtual Service-fiók](media/concept-adsync-service-account/account-1.png)

A virtuális szolgáltatásfiók olyan forgatókönyvekhez használható, amelyekben a Szinkronizáló motor és az SQL ugyanazon a kiszolgálón található. Ha távoli SQL-t használ, akkor inkább egy csoportosan felügyelt szolgáltatásfiók használatát javasoljuk. 

A virtuális szolgáltatásfiók a [Windows adatvédelmi API (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx) által okozott problémák miatt nem használható tartományvezérlőn. 

## <a name="managed-service-account"></a>Felügyelt szolgáltatásfiók 

Ha távoli SQL Server használ, azt javasoljuk, hogy egy csoportosan felügyelt szolgáltatásfiókot használjon. A Active Directory csoportosan felügyelt szolgáltatásfiók előkészítésével kapcsolatos további információkért lásd: [csoportosan felügyelt szolgáltatásfiókok áttekintése](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)). 

Ha ezt a beállítást szeretné használni, a [szükséges összetevők telepítése](how-to-connect-install-custom.md#install-required-components) lapon jelölje be a **meglévő szolgáltatásfiók használata** jelölőnégyzetet, és válassza a **felügyelt szolgáltatásfiók** lehetőséget. 

 ![felügyelt szolgáltatásfiók](media/concept-adsync-service-account/account-2.png)

Önálló felügyelt szolgáltatásfiók használata is támogatott. Ezeket azonban csak a helyi gépen lehet használni, és az alapértelmezett Virtual Service-fiók használatakor nem kell kihasználni őket. 

### <a name="auto-generated-standalone-managed-service-account"></a>Automatikusan létrehozott önálló felügyelt szolgáltatásfiók 

Ha a Azure AD Connect tartományvezérlőre telepíti, a telepítővarázsló önálló felügyelt szolgáltatásfiókot hoz létre (kivéve, ha az egyéni beállításokban használandó fiókot határozza meg). A fiók előre rögzített **ADSyncMSA_** és a tényleges szinkronizálási szolgáltatás futtatására szolgál. 

Ez a fiók egy felügyelt tartományi fiók, amely nem rendelkezik jelszóval, és a Windows automatikusan kezeli. 

Ez a fiók olyan forgatókönyvekhez használható, amelyekben a Szinkronizáló motor és az SQL a tartományvezérlőn található. 

## <a name="user-account"></a>Felhasználói fiók 

A telepítővarázsló létrehoz egy helyi szolgáltatásfiókot (kivéve, ha megadja az egyéni beállításokban használandó fiókot). A fiók előre rögzített AAD_ és a tényleges szinkronizálási szolgáltatás futtatására szolgál. Ha a Azure AD Connect tartományvezérlőre telepíti, a fiók a tartományban jön létre. A AAD_ szolgáltatás fiókjának a tartományban kell lennie, ha: 
- SQL Server-t futtató távoli kiszolgálót használ 
- hitelesítést igénylő proxyt használ 

 ![felhasználói fiók](media/concept-adsync-service-account/account-3.png)

A fiók olyan hosszú, összetett jelszóval jön létre, amely nem jár le. 

Ez a fiók biztonságos módon tárolja a más fiókok jelszavait. Az egyéb fiókok jelszavait az adatbázisban titkosítva tárolja a rendszer. A titkosítási kulcsokhoz tartozó titkos kulcsokat a Windows adatvédelmi API (DPAPI) használatával védi a titkosítási szolgáltatásoknak a titkos kulcs titkosításával. 

Ha teljes SQL Server használ, a szolgáltatásfiók a létrehozott adatbázis DBO a Szinkronizáló motorhoz. A szolgáltatás semmilyen más engedéllyel nem fog működni. Létrejön egy SQL-bejelentkezés is. 

A fiók a szinkronizálási motorhoz kapcsolódó fájlokhoz, kulcsokhoz és egyéb objektumokhoz is engedélyt kap. 


## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
