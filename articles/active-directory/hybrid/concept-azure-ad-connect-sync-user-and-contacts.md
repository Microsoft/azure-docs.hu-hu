---
title: 'Azure AD Connect Sync: a felhasználók, csoportok és névjegyek ismertetése | Microsoft Docs'
description: Ismerteti a felhasználókat, a csoportokat és a névjegyeket Azure AD Connect szinkronizálásban.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e015f7937db6788aa4473a8a04434121299901e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861782"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect szinkronizálás: a felhasználók, csoportok és névjegyek ismertetése
Számos különböző okból kifolyólag több Active Directory erdő van, és több különböző üzembe helyezési topológiával is rendelkezhet. Az általános modellek közé tartozik a fiók-erőforrás üzembe helyezése és a GAL Sync ' ED erdők egyesítése & beszerzése után. De még ha vannak is tiszta modellek, a hibrid modellek is gyakoriak. Az Azure AD Connect Sync alapértelmezett konfigurációja nem feltételezi az adott modellt, de attól függően, hogy a felhasználói megfeleltetés hogyan lett kiválasztva a telepítési útmutatóban, a különböző viselkedések megfigyelhetők.

Ebben a témakörben bemutatjuk, hogyan viselkedik az alapértelmezett konfiguráció bizonyos topológiákban. Áttekintjük a konfigurációt, és a szinkronizációs szabályok szerkesztővel megnézheti a konfigurációt.

Van néhány általános szabály, amelyet a konfiguráció feltételez:
* Függetlenül attól, hogy milyen sorrendben importálunk a forrás aktív címtárakból, a végeredménynek mindig azonosnak kell lennie.
* Egy aktív fiók mindig hozzájárul a bejelentkezési adatokhoz, beleértve a **userPrincipalName** és a **sourceAnchor** is.
* A letiltott fiókok hozzájárulnak a userPrincipalName és a sourceAnchor, kivéve, ha ez egy csatolt postaláda, ha nem található aktív fiók.
* Egy csatolt postaládával rendelkező fiók soha nem lesz felhasználva a userPrincipalName és a sourceAnchor. Feltételezzük, hogy az aktív fiók később is megtalálható.
* Lehet, hogy egy kapcsolattartási objektum az Azure AD-hez való kapcsolatként vagy felhasználóként van kiépítve. Nem igazán ismeri, amíg az összes forrás Active Directory erdő fel nem dolgozva.

## <a name="groups"></a>Csoportok
Fontos tudnivalók a csoportok Active Directoryról Azure AD-re történő szinkronizálásáról:

* Azure AD Connect kizárja a címtár-szinkronizálás beépített biztonsági csoportjait.

* A Azure AD Connect nem támogatja az [elsődleges](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771489(v=ws.11)) CSOPORTTAGSÁGOK Azure ad-beli szinkronizálását.

* Azure AD Connect nem támogatja a [dinamikus terjesztési csoportok tagságának](/Exchange/recipients/dynamic-distribution-groups/dynamic-distribution-groups) szinkronizálását az Azure ad-be.

* Active Directory csoport szinkronizálása az Azure AD-val levelezési csoportként:

    * Ha a csoport *proxyAddress* attribútuma üres, a *mail* -attribútumnak értékkel kell rendelkeznie

    * Ha a csoport *proxyAddress* attribútuma nem üres, tartalmaznia kell legalább egy SMTP-proxy címe értéket. Íme néhány példa:
    
      * Egy Active Directory csoport, amelynek proxyAddress attribútumának értéke *{"X500:/0 = contoso. com/ou = Users/CN = testgroup"}* , nem lesz engedélyezve a levelezés az Azure ad-ben. Nincs SMTP-címe.
      
      * Egy Active Directory csoport, amelynek proxyAddress attribútumának értéke *{"X500:/0 = contoso. com/ou = Users/CN = testgroup", "SMTP: johndoe \@ contoso.com"}* az Azure ad-ben levelezésre lesz engedélyezve.
      
      * Egy Active Directory csoport, amelynek proxyAddress attribútumának értéke *{"X500:/0 = contoso. com/ou = Users/CN = testgroup", "SMTP: johndoe \@ contoso.com"}* az Azure ad-ben is levelezésre engedélyezett lesz.

## <a name="contacts"></a>Kapcsolattartók
Ha egy másik erdőben lévő felhasználót képviselő kapcsolattartók közösek, az egyesítés & a beszerzést követően, amelyben egy GALSync-megoldás két vagy több Exchange-erdőt is Áthidal. A Contact objektum mindig az összekötő területéről csatlakozik a metaverse-hez a mail attribútum használatával. Ha már van olyan Contact objektum vagy felhasználói objektum, amely ugyanazzal a levelezési névvel rendelkezik, akkor az objektumok együtt csatlakoznak egymáshoz. Ez az **ad – Contact JOIN** elemben található szabályban van konfigurálva. Létezik egy nevű szabály is az **ad-ben – a közös** kapcsolattal együtt, amely a metaverse attribútumhoz tartozó **sourceObjectType** és az állandó **Kapcsolatfelvétel**. Ez a szabály nagyon alacsony prioritású, így ha bármelyik felhasználói objektum ugyanahhoz a metaverse-objektumhoz van csatlakoztatva, akkor a **from ad – User Common** szabály a felhasználó értékével járul hozzá ehhez az attribútumhoz. Ebben a szabályban ez az attribútum akkor fog megjelenni az értéknél, ha nincs felhasználó csatlakoztatva, és az érték felhasználó, ha legalább egy felhasználó található.

Ha egy objektumot az Azure AD-be kíván kiépíteni, a Kimenő szabály a **HRE-hez – a Contact JOIN** egy Contact objektumot hoz létre, ha a metaverse attribútum **sourceObjectType** **kapcsolatra** van beállítva. Ha ez az attribútum a **felhasználó** értékre van állítva, akkor a szabály a **HRE – a felhasználó csatlakoztatása** helyett felhasználói objektumot fog létrehozni.
Lehetséges, hogy egy objektumot a kapcsolatból a felhasználóhoz kell előléptetni, ha a rendszer a forrásként szolgáló Active könyvtárakat importálja és szinkronizálja.

Például egy GALSync-topológiában a második erdőben található, mindenki számára elérhető kapcsolattartási objektumokat fogjuk megkeresni az első erdő importálásakor. Ez a HRE-összekötőben új kapcsolattartási objektumokat fog bevezetni. Amikor később importálja és szinkronizálja a második erdőt, megkeresjük a valódi felhasználókat, és csatlakoztatjuk őket a meglévő metaverse-objektumokhoz. Ezután töröljük a Contact objektumot a HRE-ben, és létrehozunk egy új felhasználói objektumot.

Ha van olyan topológiája, amelyben a felhasználók névjegyként vannak megjelölve, ügyeljen arra, hogy a telepítési útmutatóban a levelezés attribútumhoz tartozó felhasználókat válassza. Ha másik lehetőséget választ, akkor egy rendeléstől függő konfiguráció fog megjelenni. A Contact Objects mindig a mail attribútumhoz fog csatlakozni, de a felhasználói objektumok csak akkor csatlakoznak a mail attribútumhoz, ha ez a beállítás be van jelölve a telepítési útmutatóban. Ezután a metaverse-ben két különböző objektummal is elvégezheti ugyanazt a mail attribútumot, ha a Contact objektum a felhasználói objektum előtt lett importálva. Az Azure AD-ba való exportálás során hiba történik. Ez a viselkedés a tervezés szerint történik, és a hibás adatértékeket jelez, vagy a topológia a telepítés során nem volt megfelelően azonosítva.

## <a name="disabled-accounts"></a>Letiltott fiókok
A letiltott fiókok szinkronizálása is megtörténik az Azure AD-ben. A letiltott fiókok közösek az Exchange-ben, például a konferenciatermekben lévő erőforrások ábrázolására. A kivétel egy csatolt postaládával rendelkező felhasználó. Amint azt korábban említettük, ezek soha nem fognak kiépíteni egy fiókot az Azure AD-be.

Feltételezi, hogy ha a rendszer letiltott felhasználói fiókot talál, akkor később nem találunk újabb aktív fiókot, és az objektum az Azure AD-be van kiépítve a talált userPrincipalName és sourceAnchor. Ha egy másik aktív fiók ugyanahhoz a metaverse-objektumhoz csatlakozik, akkor a rendszer a userPrincipalName és a sourceAnchor fogja használni.

## <a name="changing-sourceanchor"></a>SourceAnchor módosítása
Ha egy objektumot az Azure AD-be exportáltak, akkor többé nem lehet módosítani a sourceAnchor. Ha az objektum exportálása megtörtént, a **cloudSourceAnchor** metaverse attribútum az Azure ad által elfogadott **sourceAnchor** értékre van beállítva. Ha a **sourceAnchor** módosul, és nem felel meg a **CLOUDSOURCEANCHOR**, a **HRE-User JOIN (a felhasználó csatlakoztatása) beállítással** **megváltoztak a hiba sourceAnchor attribútuma**. Ebben az esetben a konfigurációt vagy az adatokat ki kell javítani, hogy a metaverse újra megegyezzen az objektum újbóli szinkronizálása előtt.

## <a name="additional-resources"></a>További források
* [Azure AD Connect Sync: szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
