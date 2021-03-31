---
title: identitásszinkronizálás és duplikált attribútum rugalmassága | Microsoft Docs
description: Az egyszerű felhasználónévvel vagy ProxyAddress kapcsolatos ütközések kezelésének új viselkedése a címtár-szinkronizálás során Azure AD Connect használatával.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09dd6a127bd04ae698cb6cad2ffd7f35e3b51c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94413428"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identitásszinkronizálás és ismétlődő attribútumok rugalmassága
A duplikált attribútum-rugalmasság a Azure Active Directory egyik funkciója, amely megszünteti a **userPrincipalName** és az SMTP- **ProxyAddress** ütközések okozta súrlódást a Microsoft szinkronizálási eszközeinek valamelyikének futtatásakor.

A két attribútumnak általában egyedinek kell lennie az adott Azure Active Directory bérlő összes **felhasználó**-, **csoport**-vagy **kapcsolattartó** -objektumában.

> [!NOTE]
> Csak a felhasználók rendelkezhetnek UPN-vel.
> 
> 

A szolgáltatás által biztosított új viselkedés a szinkronizálási folyamat Felhőbeli részében van, így az ügyfél-és a Microsoft szinkronizációs termékhez kapcsolódóan, beleértve a Azure AD Connect, az rSync és a webalkalmazás-összekötőt is. Ebben a dokumentumban a "Sync Client" általános kifejezést használjuk, hogy az ilyen termékek bármelyikét képviseljék.

## <a name="current-behavior"></a>Jelenlegi viselkedés
Ha egy olyan UPN-vagy ProxyAddress-értékkel rendelkező új objektum kiépítésére tett kísérlet, amely nem sérti ezt az egyediségi korlátozást, Azure Active Directory blokkolja az objektum létrehozását. Hasonlóképpen, ha egy objektumot nem egyedi egyszerű felhasználónévvel vagy ProxyAddress frissít, a frissítés sikertelen lesz. A szinkronizációs ügyfél a kiépítési kísérletet vagy a frissítést újra megpróbálja végrehajtani az egyes exportálási ciklusok esetén, és az ütközés feloldása után továbbra is meghiúsul. A rendszer hibajelentési e-mailt generál minden egyes kísérlet után, és a szinkronizálási ügyfél naplózza a hibát.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Ismétlődő attribútum-rugalmasságot biztosító viselkedés
A duplikált attribútummal rendelkező objektumok kiépítése és frissítése helyett a "karanténba helyezi" a duplikált attribútumot Azure Active Directory, amely nem sérti az egyediségi megkötést. Ha ez az attribútum szükséges a kiépítéshez, például a UserPrincipalName, a szolgáltatás helyőrző értéket rendel hozzá. Az ideiglenes értékek formátuma  
_**\<OriginalPrefix> + \<4DigitNumber> \@ \<InitialTenantDomain> . onmicrosoft.com**_.

Az attribútum rugalmassági folyamata csak UPN-és SMTP- **ProxyAddress** -értékeket kezel.

Ha az attribútum nem szükséges, például egy  **ProxyAddress**, Azure Active Directory egyszerűen karanténba helyezi az ütközési attribútumot, és folytatja az objektum létrehozását vagy frissítését.

Az attribútum karanténba helyezése után a rendszer a régi viselkedésben használt, a hibajelentésben szereplő e-mail-címre küldi el az ütközésre vonatkozó információkat. Ez az információ azonban csak egyszer jelenik meg a hibajelentésben, amikor a Karanténba kerül, nem folytatja a jövőbeli e-mailek beléptetését. Továbbá, mivel az objektum exportálása sikeres volt, a szinkronizálási ügyfél nem naplóz egy hibát, és nem próbálja meg újból végrehajtani a létrehozás/frissítés műveletet a következő szinkronizálási ciklusok után.

Ennek a viselkedésnek a támogatásához új attribútum lett hozzáadva a felhasználó, a csoport és a kapcsolattartási objektum osztályaihoz:  
**DirSyncProvisioningErrors**

Ez egy többértékű attribútum, amely az ütköző attribútumok tárolására szolgál, amelyek megsértik az egyediségi megkötést, és általában hozzá kell adni őket. A háttérben futó időzítő feladat engedélyezve van Azure Active Directoryban, amely óránként fut, hogy megkeresse a duplikált attribútumokat, amelyeket megoldottak, és automatikusan eltávolítja a kérdéses attribútumokat a karanténba.

### <a name="enabling-duplicate-attribute-resiliency"></a>Ismétlődő attribútum rugalmasságának engedélyezése
A duplikált attribútum rugalmassága az összes Azure Active Directory-bérlő új alapértelmezett viselkedése lesz. Alapértelmezés szerint minden olyan bérlőnél, amely az első alkalommal engedélyezte a szinkronizálást, a 2016-as és újabb verziókban. A szinkronizálást engedélyező bérlők a kötegekben engedélyezve lesznek a szolgáltatással. Ez a bevezetés a 2016 szeptemberében kezdődik, és e-mailben értesítést küldünk minden bérlő technikai értesítési feladatához a szolgáltatás engedélyezésének adott napjával.

> [!NOTE]
> Ha a duplikált attribútum rugalmassága be van kapcsolva, nem tiltható le.

Annak ellenőrzéséhez, hogy a szolgáltatás engedélyezve van-e a bérlő számára, töltse le a Azure Active Directory PowerShell-modul legújabb verzióját, és futtassa a következőt:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> A továbbiakban nem használhatja Set-MsolDirSyncFeature parancsmagot, hogy proaktívan engedélyezze a duplikált attribútum rugalmassági funkcióját, mielőtt bekapcsolja a bérlőhöz. A szolgáltatás teszteléséhez létre kell hoznia egy új Azure Active Directory bérlőt.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>DirSyncProvisioningError típusú hibákat tartalmazó objektumok azonosítása
A rendszer jelenleg két módszerrel azonosítja azokat az objektumokat, amelyek az ismétlődő tulajdonságok ütközése miatt a hibákkal rendelkeznek, Azure Active Directory a PowerShellt és a [Microsoft 365 felügyeleti központot](https://admin.microsoft.com). A jövőben további portálon alapuló jelentéskészítésre is kiterjeszthetők.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Ebben a témakörben a PowerShell-parancsmagok esetében a következők teljesülnek:

* A következő parancsmagok mindegyike megkülönbözteti a kis-és nagybetűket.
* A **– ErrorCategory PropertyConflict** mindig szerepelnie kell. Jelenleg nincsenek más típusú **ErrorCategory**, de ez a későbbiekben bővíthető.

Első lépésként futtassa a **MsolService** , és adja meg a bérlői rendszergazda hitelesítő adatait.

Ezután használja a következő parancsmagokat és operátorokat a hibák különböző módokon történő megtekintéséhez:

1. [Az összes megjelenítése](#see-all)
2. [Tulajdonság típusa szerint](#by-property-type)
3. [Ütköző értékkel](#by-conflicting-value)
4. [Karakterlánc-keresés használata](#using-a-string-search)
5. Sorted
6. [Korlátozott mennyiségű vagy az összes](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Az összes megjelenítése
Csatlakozás után az attribútum-kiépítési hibák általános listáját tekintheti meg a bérlő futtatásában:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Ez a következőhöz hasonló eredményt hoz létre:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Tulajdonság típusa szerint
Ha meg szeretné tekinteni a hibákat a tulajdonság típusa szerint, adja hozzá a **-PropertyName** jelzőt a **userPrincipalName** vagy a **ProxyAddresses** argumentummal:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Vagy

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Ütköző értékkel
Egy adott tulajdonsággal kapcsolatos hibák megtekintéséhez adja hozzá a **-tulajdonságérték** jelzőt (**-PropertyName** kell használni a jelző hozzáadásakor is):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Karakterlánc-keresés használata
A széles sztringek kereséséhez használja a **-KeresendoString** jelzőt. Ez a fenti jelzők függetlenül is használható, a **-ErrorCategory PropertyConflict** kivételével, ami mindig szükséges:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Korlátozott mennyiségű vagy az összes
1. **MaxResults \<Int>** a lekérdezés meghatározott számú értékre való korlátozására használható.
2. Az **összes eredmény** beolvasása a következő esetekben lehetséges, hogy nagy számú hiba létezik.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Microsoft 365 felügyeleti központ
A címtár-szinkronizálási hibákat a Microsoft 365 felügyeleti központban tekintheti meg. A Microsoft 365 felügyeleti központban található jelentés csak azokat a **felhasználói** objektumokat jeleníti meg, amelyek rendelkeznek ezekkel a hibákkal. Nem jeleníti meg a **csoportok** és a **névjegyek** közötti ütközésekkel kapcsolatos információkat.

![Képernyőkép, amely a címtár-szinkronizálási hibákat mutatja a Microsoft 365 felügyeleti központban.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Aktív felhasználók")

A címtár-szinkronizálási hibák a Microsoft 365 felügyeleti központban való megtekintésével kapcsolatos útmutatásért lásd: [a címtár-szinkronizálási hibák azonosítása a Microsoft 365ban](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>identitásszinkronizálás hibajelentés
Ha egy duplikált attribútummal rendelkező objektumot kezel ezzel az új viselkedéssel, akkor a rendszer értesítést küld a normál identitás-szinkronizálási hibajelentés e-mail-címéről, amelyet a bérlőnek szóló technikai értesítési kapcsolattartó kap. Ez a viselkedés azonban fontos változást mutat be. A múltban a duplikált attribútumokra vonatkozó Adatütközések az ütközés feloldása előtt minden további hibajelentésben szerepelni fognak. Ezzel az új viselkedéssel az adott ütközésre vonatkozó hibaüzenet csak egyszer jelenik meg, amikor az ütköző attribútum Karanténba kerül.

Íme egy példa arra, hogy az e-mail-értesítés hogyan néz ki a ProxyAddress ütközés esetén:  
    ![Képernyőkép, amely egy ProxyAddress ütközésre vonatkozó e-mailes értesítést mutat be.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktív felhasználók")  

## <a name="resolving-conflicts"></a>Ütközések feloldása
A hibákkal kapcsolatos hibaelhárítási stratégiák és a megoldási taktikák nem térhetnek el az ismétlődő attribútumokra vonatkozó hibák múltbeli kezelésének módjától. Az egyetlen különbség, hogy az időzítő feladat a szolgáltatás oldalán lévő bérlőn keresztül fut, hogy az ütközés feloldása után automatikusan hozzáadja a szóban forgó attribútumot a megfelelő objektumhoz.

A következő cikk a különböző hibaelhárítási és megoldási stratégiákat ismerteti: [ismétlődő vagy érvénytelen attribútumok megakadályozzák a címtár-szinkronizálást az Office 365-ben](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Ismert problémák
Ezen ismert problémák egyike sem okozza az adatvesztést vagy a szolgáltatások romlását. Ezek közül több esztétikai, mások pedig a standard "*Pre-rugalmasság*" ismétlődő attribútum hibáit okozzák az ütközési attribútum karanténba helyezése helyett, és egy másik, bizonyos hibák miatt további manuális javítást igényelnek.

**Alapvető viselkedés:**

1. Az adott attribútum-konfigurációval rendelkező objektumok továbbra is megkapják az exportálási hibákat a karanténba helyezett attribútum (ok) helyett.  
   Például:
   
    a. Új felhasználó jön létre az AD-ben a **joe \@ contoso.com** és a ProxyAddress SMTP UPN-vel **: Joe \@ contoso.com**
   
    b. Az objektum tulajdonságai ütköznek egy meglévő csoporttal, ahol a ProxyAddress **SMTP: Joe \@ contoso.com**.
   
    c. Exportáláskor a rendszer **ProxyAddress ütközési hibát okoz** a karanténba helyezett ütközési attribútumok helyett. A rendszer újrapróbálkozik a művelettel minden további szinkronizálási cikluson, mivel a rugalmassági funkció engedélyezése előtt lenne.
2. Ha két csoport jön létre a helyszínen ugyanazzal az SMTP-címekkel, az egyik nem tudja kiépíteni az első kísérletet a standard ismétlődő **ProxyAddress** hibával. Az ismétlődő érték azonban megfelelően Karanténba kerül a következő szinkronizálási ciklusra.

**Office portál jelentés**:

1. Az UPN-ütközőben lévő két objektum részletes hibaüzenete ugyanaz. Ez azt jelzi, hogy mindkét esetben az UPN-t módosították/karanténba helyezte, ha valójában csak az egyikük módosította az adatmennyiséget.
2. Az egyszerű felhasználónévi ütközés részletes hibaüzenete egy olyan felhasználó helytelen displayName-üzenetét jeleníti meg, aki az UPN-t módosította/karanténba helyezte. Például:
   
    a. **A felhasználó** elsőként szinkronizál az **UPN = User \@ contoso.com**.
   
    b. A **B felhasználó** szinkronizálása az **UPN = User \@ contoso.com** mellett történt.
   
    c. **B felhasználó** Az egyszerű felhasználónév **User1234 \@ contoso.onmicrosoft.com** , a **felhasználói \@ contoso.com** pedig a **DirSyncProvisioningErrors**.
   
    d. A "B" **felhasználó** hibaüzenete azt jelzi **, hogy a felhasználó már** rendelkezik UPN-ként a **felhasználó \@ contoso.com** , de a **b felhasználó** saját DisplayName.

**Identitásszinkronizálás hibajelentés**:

A *probléma megoldásához szükséges lépések* hivatkozása helytelen:  
    ![Aktív felhasználók](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktív felhasználók")  

A következőre kell mutatnia: [https://aka.ms/duplicateattributeresiliency]() .

## <a name="see-also"></a>Lásd még
* [Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
* [A címtár-szinkronizálási hibák azonosítása a Microsoft 365ban](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)