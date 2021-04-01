---
title: 'Azure AD Connect: a deklaratív kiépítés ismertetése | Microsoft Docs'
description: Ismerteti a deklaratív kiépítési konfigurációs modellt Azure AD Connectban.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59dc94e37dfa1ef8b0b079bf5d78d0504e0cb8c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313620"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect szinkronizálás: a deklaratív kiépítés ismertetése
Ez a témakör a Azure AD Connect konfigurációs modelljét ismerteti. A modell neve deklaratív kiépítés, amely lehetővé teszi a konfiguráció módosítását könnyedén. A témakörben leírt számos dolog speciális, és nem szükséges a legtöbb felhasználói forgatókönyvhöz.

## <a name="overview"></a>Áttekintés
A deklaratív kiépítés a forráshoz csatlakoztatott címtárból érkező objektumokat dolgozza fel, és meghatározza, hogy az objektum és az attribútumok hogyan alakíthatók át forrásról a célhelyre. Egy objektum egy szinkronizálási folyamatban van feldolgozva, és a folyamat a bejövő és kimenő szabályok esetében azonos. Egy bejövő szabály egy összekötő területről a metaverse-re, a Kimenő szabály pedig a metaverse és egy összekötő terület között van.

![A szinkronizálási folyamat példáját bemutató diagram.](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

A folyamat több különböző modullal rendelkezik. Mindegyik az objektum-szinkronizálás egyik koncepciójának felel.

![A folyamat moduljait ábrázoló diagram.](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Forrás, a Forrásobjektum
* [Hatókör](#scope), megkeresi a hatókörben lévő összes szinkronizálási szabályt
* [Csatlakozás](#join), az összekötő terület és a metaverse közötti kapcsolat meghatározása
* Átalakítás, kiszámítja, hogy az attribútumok hogyan legyenek átalakítva és flow
* [Prioritás](#precedence), az ütköző attribútumok hozzájárulásainak feloldása
* Cél, a célobjektum

## <a name="scope"></a>Hatókör
A hatókör-modul kiértékel egy objektumot, és meghatározza a hatókörben található szabályokat, és a feldolgozás részét képezi. Az objektum attribútumainak értékétől függően a rendszer kiértékeli a különböző szinkronizálási szabályokat a hatókörben. Az Exchange-postaládát nem tartalmazó letiltott felhasználók például eltérő szabályokkal rendelkeznek, mint egy postaládával rendelkező felhasználó.  
![Egy objektum hatókör-modulját megjelenítő diagram.](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

A hatókör csoportokként és záradékként van definiálva. A záradékok egy csoporton belül találhatók. Egy logikai és egy csoport összes záradéka között használatos. Például: (részleg = IT és ország = Dánia). A logikai vagy a csoportok között használatos.

![Hatókör](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
A kép hatókörét a következőként kell olvasni: (részleg = IT és ország = Dánia) vagy (ország = Svédország). Ha az 1. vagy a 2. csoport értéke TRUE (igaz), akkor a szabály hatókörben van kiértékelve.

A hatókör-modul a következő műveleteket támogatja.

| Művelet | Leírás |
| --- | --- |
| EGYENLŐ, NOTEQUAL |Karakterlánc-összehasonlítás, amely kiértékeli, hogy az érték egyenlő-e az attribútum értékével. A többértékű attribútumok esetében lásd: ISIN és ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Karakterlánc-összehasonlítás, amely kiértékeli, hogy az érték kisebb-e, mint az attribútumban szereplő érték. |
| TARTALMAZZA, NOTCONTAINS |Egy karakterlánc-összehasonlítás, amely kiértékeli, hogy az érték az attribútumon belül valahol található-e. |
| STARTSWITH, NOTSTARTSWITH |Karakterlánc-összehasonlítás, amely kiértékeli, hogy az érték az attribútum elején található-e. |
| ENDSWITH, NOTENDSWITH |Egy karakterlánc-összehasonlítás, amely kiértékeli, hogy az érték az attribútum végén található-e. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Karakterlánc-összehasonlítás, amely kiértékeli, hogy az érték nagyobb-e, mint az attribútumban szereplő érték. |
| ISNULL, ISNOTNULL |Kiértékeli, hogy az attribútum hiányzik-e az objektumból. Ha az attribútum nem létezik, ezért null értékű, akkor a szabály hatókörben van. |
| ISIN, ISNOTIN |Kiértékeli, hogy az érték szerepel-e a definiált attribútumban. Ez a művelet az EQUAL és a NOTEQUAL többszörös értékű változata. Az attribútumnak többértékű attribútumnak kell lennie, és ha az érték bármelyik attribútum értékében megtalálható, akkor a szabály hatókörben van. |
| ISBITSET, ISNOTBITSET |Kiértékeli, hogy egy adott bit be van-e állítva. Például a userAccountControl lévő BITS kiértékelésére használható, hogy megtudja, van-e engedélyezve vagy letiltva a felhasználó. |
| ISMEMBEROF, ISNOTMEMBEROF |Az értéknek tartalmaznia kell egy, az összekötő terület egyik csoportjába tartozó megkülönböztető nevet. Ha az objektum a megadott csoport tagja, akkor a szabály hatókörben van. |

## <a name="join"></a>Csatlakozás
A szinkronizálási folyamat JOIN moduljának feladata a forrás és a cél objektum közötti kapcsolat megkeresése. Egy bejövő szabály esetében ez a kapcsolat egy olyan objektum, amely egy, a metaverse-objektumhoz fűződő kapcsolatot megkereső összekötői térben található.  
![Csatlakozás a CS és az MV között](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
A cél annak megállapítása, hogy van-e már egy olyan objektum a metaverse-ban, amelyet egy másik összekötő hozott létre, amelyhez társítva kell lennie. Egy fiók – erőforrás erdőben például a fiók erdő felhasználóját csatlakoztatni kell a felhasználóhoz az erőforrás-erdőben.

Az illesztéseket többnyire a bejövő szabályok használják arra, hogy összekapcsolják az összekötő terület objektumait ugyanahhoz a metaverse-objektumhoz.

Az illesztések egy vagy több csoportként vannak meghatározva. Egy csoporton belül vannak záradékok. Egy logikai és egy csoport összes záradéka között használatos. A logikai vagy a csoportok között használatos. A csoportok feldolgozása felülről lefelé történik. Ha egy csoport pontosan egy egyezést talált a célhelyen lévő objektummal, akkor a rendszer nem értékeli ki a többi csatlakoztatási szabályt. Ha a rendszer nulla vagy egynél több objektumot talál, a feldolgozás a következő szabálykészlet szerint folytatódik. Emiatt a szabályokat a legtöbb explicit első és több homályos sorrendben kell létrehozni a végén.  
![Csatlakozás definíciója](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
A képen lévő illesztések felülről lefelé haladva lesznek feldolgozva. Először a szinkronizálási folyamat látja, hogy van-e egyezés az Alkalmazottkód-ben. Ha nem, a második szabály azt látja, hogy a fiók neve használható-e az objektumok összekapcsolásához. Ha ez nem egyezik, a harmadik és a végső szabály a felhasználó nevével megegyezőnek tekintendő.

Ha az összes csatlakozási szabály ki lett értékelve, és nincs pontosan egy egyezés, a **Leírás** lapon a **hivatkozás típusa** lesz használatban. Ha ez a beállítás a **kiépítés** értékre van állítva, a rendszer létrehoz egy új objektumot a célhelyen.  
![A "hivatkozás típusa" legördülő menü megnyitására szolgáló képernyőkép.](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Az objektumnak csak egyetlen szinkronizálási szabállyal kell rendelkeznie a hatókörben lévő csatlakoztatási szabályokkal. Ha több szinkronizálási szabály van megadva a csatlakozáshoz, hiba történik. A kapcsolódási ütközések feloldásához a rendszer nem használja a prioritást. Egy objektumnak rendelkeznie kell egy JOIN szabálysal a hatókörben ahhoz, hogy az attribútumok ugyanazzal a bejövő/kimenő irányba haladjanak. Ha a bejövő és a kimenő attribútumokat is ugyanarra az objektumra kell átadnia, akkor a bejövő és a kimenő szinkronizálási szabállyal is csatlakoznia kell.

A kimenő illesztés speciális viselkedést tartalmaz, amikor megpróbál kiépíteni egy objektumot egy cél összekötő területére. A DN attribútum használatával először próbálkozhat egy fordított illesztéssel. Ha már van egy objektum a cél-összekötőn ugyanazzal a megkülönböztető névvel, akkor az objektumok csatlakoztatva lesznek.

Az illesztési modul csak egyszer lesz kiértékelve, amikor egy új szinkronizálási szabály hatókörbe kerül. Ha egy objektum csatlakoztatva van, akkor még akkor sem történik meg a csatlakozás, ha az illesztési feltételek már nem teljesülnek. Ha meg szeretne adni egy objektumot, az objektumokhoz csatlakozó szinkronizálási szabálynak a hatókörön kívül kell esnie.

### <a name="metaverse-delete"></a>Metaverse törlés
Egy metaverse-objektum addig marad mindaddig, amíg egyetlen szinkronizálási szabály van a hatókörben a **kiépítés** vagy a **StickyJoin** **beállításhoz** . A rendszer akkor használja a StickyJoin, ha egy összekötő nem jogosult új objektumot kiépíteni a metaverse-be, de ha csatlakoztatva van, akkor azt törölni kell a forrásból a metaverse-objektum törlése előtt.

Egy metaverse-objektum törlésekor a rendszer minden **, a** kiépítésre kijelölt kimenő szinkronizálási szabállyal társított objektumot megjelöl a törléshez.

## <a name="transformations"></a>Átalakítások
Az átalakítások segítségével határozható meg, hogy az attribútumok hogyan legyenek a forrástól a célhelyig áramlanak. A folyamatokhoz a következő **típusú** folyamatok tartozhatnak: közvetlen, állandó vagy kifejezés. A közvetlen folyamat egy attribútum értékének átirányítása, amely nem rendelkezik további átalakításokkal. Egy konstans érték beállítja a megadott értéket. Egy kifejezés a deklaratív kiépítési kifejezés nyelvét használja, hogy kifejezze az átalakítást. A kifejezés nyelvének részletei a [deklaratív üzembe helyezési kifejezés nyelvének ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) című témakörben találhatók.

![Kiépítés vagy csatlakozás](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

Az **alkalmazás egyszer** jelölőnégyzet azt határozza meg, hogy az attribútumot csak akkor kell megadni, ha az objektumot eredetileg létrehozták. Ezt a konfigurációt használhatja például egy új felhasználói objektum kezdeti jelszavának beállításához.

### <a name="merging-attribute-values"></a>Attribútumok értékeinek egyesítése
Az attribútum folyamataiban van egy beállítás, amely meghatározza, hogy a többértékű attribútumok egyesítve legyenek-e több különböző Összekötőből. Az alapértelmezett érték a **frissítés**, amely azt jelzi, hogy a legmagasabb prioritású szinkronizálási szabálynak nyernie kell.

![Képernyőkép: az "átalakítások hozzáadása" szakasz "Merge Types" (egyesítési típusok) legördülő menüjének megnyitása.](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

**Egyesítés** és **MergeCaseInsensitive** is. Ezek a beállítások lehetővé teszik különböző forrásokból származó értékek egyesítését. Használhatja például a tag vagy a proxyAddresses attribútum egyesítését több különböző erdőből. Ha ezt a beállítást használja, az objektum hatókörében lévő összes szinkronizálási szabálynak ugyanazt az egyesítési típust kell használnia. Nem határozhat meg **frissítést** egyetlen összekötőről, és **egyesítheti** azt egy másikból. Ha megpróbálja, hibaüzenetet kap.

Az **Egyesítés** és a **MergeCaseInsensitive** közötti különbség a duplikált attribútumok értékének feldolgozása. A Szinkronizáló motor ellenőrzi, hogy az ismétlődő értékek nincsenek-e beszúrva a TARGET attribútumba. A **MergeCaseInsensitive** esetében a duplikált értékek csak akkor jelennek meg, ha nem jelennek meg. Például nem láthatja SMTP:bob@contoso.com a "" és "" kifejezést smtp:bob@contoso.com a TARGET attribútumban. Az **Egyesítés** csak a pontos értékeket és a több értéket vizsgálja, ahol ebben az esetben csak a különbség áll fenn.

A **replace** kapcsoló ugyanaz, mint a **frissítés**, de nem használja.

### <a name="control-the-attribute-flow-process"></a>Az attribútum folyamatának vezérlése
Ha több bejövő szinkronizálási szabály van konfigurálva ahhoz, hogy ugyanahhoz a metaverse-attribútumhoz járuljon hozzá, akkor a rendszer elsőbbséget alkalmaz a győztes meghatározására. A legmagasabb prioritású szinkronizálási szabály (a legalacsonyabb numerikus érték) az értéket fogja támogatni. Ugyanez történik a kimenő szabályokkal. A legnagyobb prioritással rendelkező szinkronizálási szabály WINS-sel járul hozzá, és hozzájárul az érték a csatlakoztatott címtárhoz.

Bizonyos esetekben a szinkronizálási szabálynak meg kell határoznia, hogy a többi szabály hogyan viselkedjen. Ebben az esetben néhány speciális literál szerepel.

A bejövő szinkronizálási szabályok esetében a literál **Null** érték azt jelzi, hogy a folyamatnak nincs a hozzájárulása. Egy másik, alacsonyabb prioritású szabály is hozzájárulhat egy értékhez. Ha egyetlen szabály sem járult hozzá egy értékhez, a rendszer eltávolítja a metaverse-attribútumot. Kimenő szabály esetén, ha az összes szinkronizálási szabály feldolgozása után **Null** értékű a végső érték, akkor a rendszer eltávolítja az értéket a csatlakoztatott címtárban.

A literál **AuthoritativeNull** hasonló a **nullához** , de azzal a különbséggel, hogy az alacsonyabb prioritási szabályok nem járulnak hozzá az értékhez.

Az **IgnoreThisFlow** is használhatnak. Hasonló a NULL értékhez abban az értelemben, hogy az azt jelzi, hogy semmi nem járul hozzá. A különbség az, hogy nem távolítja el a már meglévő értéket a célhelyen. Olyan, mint az attribútum folyamata még soha nem volt ott.

Alább bemutatunk egy példát:

Az *ad-User Exchange Hybrid* szolgáltatásban a következő folyamat található:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
A kifejezést a következőképpen kell beolvasni: Ha a felhasználói postaláda az Azure AD-ben található, akkor az attribútumot az Azure AD-ből az AD-be. Ha nem, ne hajtson végre semmit Active Directory. Ebben az esetben ez megtartja a meglévő értéket az AD-ben.

### <a name="importedvalue"></a>ImportedValue
A ImportedValue függvény különbözik az összes többi függvénytől, mert az attribútum nevét szögletes zárójelek helyett idézőjelek közé kell foglalni:  
`ImportedValue("proxyAddresses")`.

Általában a szinkronizálás során az attribútumok a várt értéket használják, még akkor is, ha még nem exportálták, vagy hiba történt az exportálás során ("a torony tetején"). A bejövő szinkronizálás azt feltételezi, hogy egy olyan attribútum, amely még nem érte el a csatlakoztatott könyvtárat, végül eléri azt. Bizonyos esetekben fontos, hogy csak olyan értéket szinkronizáljon, amelyet a csatlakoztatott könyvtár megerősített ("hologram és különbözeti import Tower").

Erre a függvényre egy példa található a from-Box szinkronizációs szabályban a *from ad – a Common from Exchange*. A hibrid Exchange-ben az Exchange Online által hozzáadott értéket csak akkor kell szinkronizálni, ha meggyőződött arról, hogy az érték exportálása sikeresen megtörtént:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Precedencia
Ha több szinkronizálási szabály próbálkozik ugyanahhoz az attribútum értékéhez a célhoz, a rendszer a prioritási értéket használja a győztes meghatározásához. A legmagasabb prioritású, legalacsonyabb numerikus értékkel rendelkező szabály az attribútumot ütközésben fogja támogatni.

![Egyesítési típusok](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Ennek a rendezésnek a segítségével pontosabban definiálhatja az objektumok kis részhalmazát. A beépített szabályok például gondoskodnak arról, hogy egy engedélyezett fiók (**felhasználói AccountEnabled**) attribútumai elsőbbséget élveznek a többi fióktól.

Az összekötők között megadható a prioritás. Ez lehetővé teszi, hogy a jobb adatokkal rendelkező összekötők először járulnak hozzá az értékekhez.

### <a name="multiple-objects-from-the-same-connector-space"></a>Több objektum ugyanabból az összekötő területből
Ha ugyanahhoz az összekötőhöz több objektum van csatlakoztatva ugyanahhoz a metaverse-objektumhoz, a prioritást módosítani kell. Ha több objektum van ugyanazon szinkronizálási szabály hatókörében, akkor a szinkronizálási motor nem tudja meghatározni a sorrendet. Nem egyértelmű, hogy melyik forrásobjektum-objektumnak kell a metaverse számára az értéket felvennie. Ez a konfiguráció nem egyértelmű, akkor is, ha a forrás attribútumai azonos értékűek.  
![Diagram, amely egy átlátszó piros X átfedéssel rendelkező ugyanahhoz az MV-objektumhoz csatlakoztatott több objektumot mutat be. ](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

Ebben a forgatókönyvben módosítania kell a szinkronizálási szabályok hatókörét, hogy a Forrásobjektum különböző szinkronizálási szabályokkal rendelkezzen a hatókörben. Ez lehetővé teszi a különböző prioritások meghatározását.  
![Több objektum ugyanahhoz az MV-objektumhoz csatlakoztatva](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Következő lépések
* További információ a kifejezés nyelvéről a [deklaratív kiépítési kifejezések ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)című cikkből.
* Tekintse meg, hogyan használható a deklaratív kiépítés az [alapértelmezett konfiguráció megismeréséhez](concept-azure-ad-connect-sync-default-configuration.md).
* Tekintse meg, hogyan lehet [módosítani az alapértelmezett konfigurációt](how-to-connect-sync-change-the-configuration.md)a deklaratív kiépítés használatával.
* Továbbra is olvassa el, hogy a felhasználók és a névjegyek hogyan működnek együtt a [felhasználók és a névjegyek megismerése](concept-azure-ad-connect-sync-user-and-contacts.md)érdekében.

**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

**Referencia-témakörök**

* [Azure AD Connect Sync: függvények referenciája](reference-connect-sync-functions-reference.md)
