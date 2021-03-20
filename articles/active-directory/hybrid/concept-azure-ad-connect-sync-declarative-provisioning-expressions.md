---
title: 'Azure AD Connect: deklaratív kiépítési kifejezések | Microsoft Docs'
description: Ismerteti a deklaratív kiépítési kifejezéseket.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02490839a9e35695ae2e8b3f750e139ad7413aa4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89280213"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect szinkronizálás: a deklaratív kiépítési kifejezések ismertetése
A Azure AD Connect Sync szolgáltatás a Forefront Identity Manager 2010-es verziójában elsőként bevezetett deklaratív üzembe helyezésre épül. Lehetővé teszi a teljes identitás-integráció üzleti logikájának megvalósítását, anélkül, hogy lefordított kódot kellene írnia.

A deklaratív kiépítés elengedhetetlen része az attribútum-folyamatokban használt kifejezés nyelve. A használt nyelv a Microsoft® Visual Basic® for Applications (VBA) része. A rendszer ezt a nyelvet használja Microsoft Office és a VBScript felhasználói felülettel rendelkező felhasználók is felismerik. A deklaratív kiépítési kifejezés nyelve csak a függvények használatát és nem strukturált nyelvet használ. Nincsenek metódusok vagy utasítások. A függvények Ehelyett az expressz program folyamatához vannak beágyazva.

További részletekért tekintse [meg az Office 2013 Visual Basic for Applications nyelvi referenciáját](/office/vba/api/overview/language-reference)ismertető témakört.

Az attribútumok erősen begépeltek. A függvény csak a megfelelő típusú attribútumokat fogadja el. Megkülönbözteti a kis-és nagybetűket is. A függvények nevének és az attribútumainak megfelelő burkolattal kell rendelkezniük, vagy hibát kell eldobni.

## <a name="language-definitions-and-identifiers"></a>Nyelvi definíciók és azonosítók
* A függvények neve a következő zárójelekben szereplő argumentumokkal követhető: függvénynév (1. argumentum, N argumentum).
* Az attribútumokat szögletes zárójelek jelölik: [attributeName]
* A paramétereket a következő százalékos jelek azonosítják:% ParameterName%
* A karakterlánc-konstansok idézőjelek közé tartoznak: például "contoso" (Megjegyzés: a "" egyenes idézőjeleket kell használnia "", és nem intelligens idézőjelek "")
* A numerikus értékek idézőjelek nélkül vannak megadva, és várhatóan tizedes törtek lesznek. A hexadecimális értékek &H előtaggal vannak ellátva. Például 98052, &HFF
* A logikai értékek konstansokkal vannak kifejezve: true, false.
* A beépített konstansok és literálok csak a nevükkel vannak kifejezve: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
A deklaratív kiépítés számos funkciót használ, hogy lehetővé váljon az attribútumok értékének átalakítása. Ezek a függvények egymásba ágyazhatók, így az egyik függvény eredményét egy másik függvénybe adja át a rendszer.

`Function1(Function2(Function3()))`

A függvények teljes listája a függvények [dokumentációjában](reference-connect-sync-functions-reference.md)található.

### <a name="parameters"></a>Paraméterek
A paramétereket egy összekötő vagy egy, a PowerShellt használó rendszergazda határozza meg. A paraméterek általában a rendszertől a rendszertől eltérő értékeket tartalmaznak, például annak a tartománynak a nevét, amelyben a felhasználó található. Ezeket a paramétereket használhatja az attribútumok folyamataiban.

Az Active Directory-összekötő a következő paramétereket biztosított a bejövő szinkronizálási szabályokhoz:

| Paraméter neve | Megjegyzés |
| --- | --- |
| Tartomány. NetBIOS |Az éppen importált tartomány NetBIOS-formátuma, például FABRIKAMSALES |
| Tartomány. FQDN |Az éppen importált tartomány teljes tartományneve, például sales.fabrikam.com |
| Tartomány. LDAP |Az éppen importált tartomány LDAP-formátuma, például DC = Sales, DC = Fabrikam, DC = com |
| Erdő. NetBIOS |Az éppen importált erdő nevének NetBIOS-formátuma, például FABRIKAMCORP |
| Erdő. FQDN |Az éppen importált erdő nevének FQDN-formátuma, például fabrikam.com |
| Erdő. LDAP |Az éppen importált erdő nevének LDAP-formátuma, például DC = Fabrikam, DC = com |

A rendszer a következő paramétert adja meg, amely a jelenleg futó összekötő azonosítójának beolvasására szolgál:  
`Connector.ID`

Íme egy példa, amely feltölti a metaverse attribútum tartományát annak a tartománynak a NetBIOS-nevével, ahol a felhasználó található:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operátorok
A következő operátorok használhatók:

* **Összehasonlítás**: <, <=,  <>, =, >, >=
* **Matematika**: +,-, \* ,-
* **Karakterlánc**: & (összefűzés)
* **Logikai**:  &&  (és), | | vagy
* **Kiértékelési sorrend**: ()

A kezelők kiértékelése balról jobbra történik, és ugyanaz a kiértékelési prioritás. Vagyis a \* (szorzó) nem lesz kiértékelve a-(kivonási) előtt. 2 \* (5 + 3) nem ugyanaz, mint 2 \* 5 + 3. A szögletes zárójelek () a kiértékelési sorrend módosítására szolgálnak, ha a jobb oldali kiértékelési sorrend nem megfelelő.

## <a name="multi-valued-attributes"></a>Többértékű attribútumok
A függvények egyértékű és több értékű attribútumokon is működhetnek. A többértékű attribútumok esetében a függvény minden értéknél működik, és minden értékhez ugyanazt a függvényt alkalmazza.

Például:  
`Trim([proxyAddresses])` Végezze el a proxyAddress attribútum minden értékének vágását.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` A-val minden értéknél @-sign cserélje le a tartományt a következőre: @contoso.com .  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Keresse meg a SIP-címeket, és távolítsa el az értékek közül.

## <a name="next-steps"></a>Következő lépések
* További információ a konfigurációs modellről a [deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md)című cikkből.
* Tekintse meg, hogyan használható a deklaratív kiépítés az [alapértelmezett konfiguráció megismeréséhez](concept-azure-ad-connect-sync-default-configuration.md).
* Tekintse meg, hogyan lehet [módosítani az alapértelmezett konfigurációt](how-to-connect-sync-change-the-configuration.md)a deklaratív kiépítés használatával.

**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

**Referencia-témakörök**

* [Azure AD Connect Sync: függvények referenciája](reference-connect-sync-functions-reference.md)