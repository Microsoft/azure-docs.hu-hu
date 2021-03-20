---
title: 'Azure AD Connect: tervezési fogalmak | Microsoft Docs'
description: Ez a témakör bizonyos megvalósítási tervezési területeket részletez
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: baa03499cc11bda24ead986dd64621572484cbb1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279652"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: tervezési fogalmak
A dokumentum célja, hogy leírja, milyen területeken kell átgondolni a Azure AD Connect megvalósításának tervezése során. Ez a dokumentum részletesen ismerteti az egyes területeket, és ezeket a fogalmakat röviden ismertetjük más dokumentumokban is.

## <a name="sourceanchor"></a>sourceAnchor
A sourceAnchor attribútum az *objektum élettartama során* nem módosítható attribútumként van definiálva. Egyedileg azonosít egy objektumot a helyszínen és az Azure AD-ben azonos objektumként. Az attribútum neve **immutableId** , és a két név is felcserélhetőként használható.

A dokumentum nem módosítható, mert az nem változtatható meg. Mivel ez az attribútum értéke nem módosítható a beállítás után, fontos, hogy olyan kialakítást válasszon, amely támogatja a forgatókönyvet.

Az attribútum a következő esetekben használható:

* Ha egy új szinkronizáló motor-kiszolgálót épít, vagy egy vész-helyreállítási forgatókönyv után újraépít, akkor ez az attribútum az Azure AD-ben meglévő objektumokat csatolja a helyszíni objektumokhoz.
* Ha csak Felhőbeli identitásról szinkronizált identitási modellre helyez át, akkor ez az attribútum lehetővé teszi, hogy az objektumok "nehezen egyeznek" az Azure AD-ban lévő, helyszíni objektumokkal rendelkező objektumok.
* Ha összevonást használ, akkor ez az attribútum és a **userPrincipalName** együtt a felhasználó egyedi azonosítására szolgál.

Ez a témakör csak a sourceAnchor kapcsolatos információkat tárgyalja, mivel azok a felhasználókra vonatkoznak. Ugyanazok a szabályok érvényesek az összes objektumtípus esetében, de csak azokra a felhasználókra vonatkozik, amelyeket ez a probléma általában érint.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Helyes sourceAnchor attribútum kiválasztása
Az attribútum értékének a következő szabályoknak kell megfelelnie:

* Kevesebb mint 60 karakter hosszúságú
  * Nem a-z, A-Z vagy 0-9 karakterek kódolása és 3 karakternek számít.
* Nem tartalmaz speciális karaktert: &#92;! # $% & * +/=? ^ &#96; {} | ~ < >  () '; : , [ ] " \@ _
* Globálisan egyedinek kell lennie
* Karakterlánc, egész szám vagy bináris értéknek kell lennie
* Nem lehet a felhasználó nevén alapulni, mert ezek módosíthatók
* Nem lehet kis-és nagybetűket megkülönböztető és elkerülni az esettől eltérő értékeket
* Az objektum létrehozásakor kell hozzárendelni

Ha a kiválasztott sourceAnchor nem karakterlánc típusú, akkor Azure AD Connect Base64Encode az attribútum értékét, hogy ne jelenjen meg speciális karakterek. Ha az ADFS-nél más összevonási kiszolgálót használ, győződjön meg arról, hogy a kiszolgáló is Base64Encode az attribútumot.

A sourceAnchor attribútum megkülönbözteti a kis-és nagybetűket. A "JohnDoe" érték nem ugyanaz, mint a "JohnDoe". Azonban nem rendelkezhet két olyan objektummal, amelynek csak a különbsége van.

Ha a helyszínen egyetlen erdő található, akkor a használni kívánt attribútum a **ObjectGUID**. Ezt az attribútumot akkor is használja a rendszer, ha a Azure AD Connect Express-beállításokat használ, és az az attribútumot is használja, amelyet az rSync használ.

Ha több erdővel rendelkezik, és nem helyezi át a felhasználókat az erdők és a tartományok között, akkor a **ObjectGUID** egy jó attribútum, amely még ebben az esetben is használható.

Ha a felhasználókat erdők és tartományok között helyezi át, akkor olyan attribútumot kell keresnie, amely nem változik, vagy áthelyezhető a felhasználókkal az áthelyezés során. A javasolt módszer egy szintetikus attribútum bevezetése. Olyan attribútum, amely alkalmas lehet a GUID azonosítóhoz hasonló érték tárolására. Az objektum létrehozásakor létrejön egy új GUID-azonosító, amely a felhasználóra van pecsételve. A szinkronizálási motor kiszolgálójában létrehozhat egy egyéni szinkronizálási szabályt, amely a **ObjectGUID** alapján hozza létre ezt az értéket, és frissíti a kiválasztott attribútumot a hozzáadások lehetőséggel. Az objektum áthelyezésekor ügyeljen arra, hogy az érték tartalmát is másolja.

Egy másik megoldás egy meglévő attribútum kiválasztása, amelyről tudja, hogy nem változik. A gyakran használt attribútumok közé tartozik az **AlkalmazottKód**. Ha olyan attribútumot használ, amely betűket tartalmaz, győződjön meg róla, hogy a kis-és nagybetűk nem változnak az attribútum értékének megváltozása esetén. A helytelen attribútumok nem használhatók a felhasználó nevével. Házasság vagy házasság felbontása esetén a nevet várhatóan módosítani kell, ami nem engedélyezett ehhez az attribútumhoz. Ez az egyik oka annak, hogy az attribútumok, például a **userPrincipalName**, a **mail** és a **targetAddress** nem is választhatók a Azure ad Connect telepítővarázslójának kiválasztásához. Ezek az attribútumok a " \@ " karaktert is tartalmazzák, amely nem engedélyezett a sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>A sourceAnchor attribútum módosítása
A sourceAnchor attribútum értéke nem módosítható, miután létrejött az objektum az Azure AD-ben, és az identitás szinkronizálva van.

Emiatt a következő korlátozások vonatkoznak a Azure AD Connectre:

* A sourceAnchor attribútum csak a kezdeti telepítés során állítható be. Ha újra futtatja a telepítővarázslót, ez a beállítás csak olvasható. Ha módosítania kell ezt a beállítást, el kell távolítania, majd újra kell telepítenie.
* Ha egy másik Azure AD Connect-kiszolgálót telepít, akkor ugyanazt a sourceAnchor attribútumot kell kiválasztania, mint a korábban használt. Ha korábban már használta az rSynct, és áthelyezi a Azure AD Connectre, akkor a **ObjectGUID** -t kell használnia, mivel ez az az attribútum, amelyet az rsync használ.
* Ha a sourceAnchor értéke módosul az objektum Azure AD-be való exportálása után, akkor Azure AD Connect Sync hibát jelez, és a probléma javítása előtt nem teszi lehetővé az adott objektumon végzett további módosításokat, és a sourceAnchor visszavált a forrás könyvtárába.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>MS-DS-ConsistencyGuid használata sourceAnchor
Alapértelmezés szerint a Azure AD Connect (1.1.486.0 és régebbi verzió) az objectGUID-t használja a sourceAnchor attribútumként. A ObjectGUID rendszer által generált. Helyszíni AD-objektumok létrehozásakor nem adhatja meg az értékét. Ahogy az a [sourceAnchor](#sourceanchor)szakaszban is látható, vannak olyan forgatókönyvek, amelyekben meg kell adnia a sourceAnchor értéket. Ha a forgatókönyvek Önre érvényesek, egy konfigurálható AD-attribútumot (például MS-DS-ConsistencyGuid) kell használnia a sourceAnchor attribútumként.

A Azure AD Connect (1.1.524.0 és újabb verziók) mostantól lehetővé teszi az MS-DS-ConsistencyGuid sourceAnchor-attribútumként való használatát. A szolgáltatás használatakor Azure AD Connect automatikusan konfigurálja a szinkronizálási szabályokat a következőre:

1. Használja az MS-DS-ConsistencyGuid sourceAnchor attribútumot a felhasználói objektumok számára. A ObjectGUID más objektumtípusok esetében használatos.

2. Minden olyan helyszíni AD felhasználói objektum esetében, amelynek ms-DS-ConsistencyGuid attribútuma nincs feltöltve, Azure AD Connect a helyszíni Active Directory az MS-DS-ConsistencyGuid attribútumba írja vissza a objectGUID értékét. Az MS-DS-ConsistencyGuid attribútum feltöltése után Azure AD Connect majd exportálja az objektumot az Azure AD-be.

>[!NOTE]
> Ha a helyszíni AD-objektumot a Azure AD Connectba importálja (azaz az AD-összekötő területére, és a metaverse-be tervezték), akkor többé nem változtathatja meg a sourceAnchor értékét. Egy adott helyszíni AD objektum sourceAnchor értékének megadásához konfigurálja az MS-DS-ConsistencyGuid attribútumot, mielőtt a rendszer importálja a Azure AD Connectba.

### <a name="permission-required"></a>Engedély szükséges
Ahhoz, hogy ez a funkció működjön, a helyszíni Active Directory való szinkronizáláshoz használt AD DS fióknak írási engedéllyel kell rendelkeznie az MS-DS-ConsistencyGuid attribútumhoz a helyszíni Active Directoryban.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>A ConsistencyGuid funkció engedélyezése – új telepítés
Az új telepítés során engedélyezheti a ConsistencyGuid as sourceAnchor használatát. Ez a szakasz az expressz és az egyéni telepítés részleteit ismerteti.

  >[!NOTE]
  > Csak a Azure AD Connect újabb verziói (1.1.524.0 és utána) támogatják a ConsistencyGuid as sourceAnchor használatát az új telepítés során.

### <a name="how-to-enable-the-consistencyguid-feature"></a>A ConsistencyGuid funkció engedélyezése

#### <a name="express-installation"></a>Expressz telepítés
A Azure AD Connect Express módban való telepítésekor a Azure AD Connect varázsló automatikusan meghatározza a legmegfelelőbb AD-attribútumot, amelyet sourceAnchor attribútumként kíván használni a következő logika használatával:

* Első lépésként a Azure AD Connect varázsló lekérdezi az Azure AD-bérlőt, hogy lekérje a sourceAnchor attribútumként használt AD-attribútumot az előző Azure AD Connect telepítésben (ha van). Ha ezek az információk elérhetők, Azure AD Connect ugyanazt az AD attribútumot használja.

  >[!NOTE]
  > A telepítés során használt sourceAnchor attribútummal kapcsolatban az Azure AD-bérlőben csak a Azure AD Connect (1.1.524.0 és utána) újabb verziói tárolhatnak adatokat. A Azure AD Connect régebbi verziói nem.

* Ha a használt sourceAnchor attribútummal kapcsolatos információk nem érhetők el, a varázsló ellenőrzi az MS-DS-ConsistencyGuid attribútum állapotát a helyszíni Active Directory. Ha az attribútum nincs konfigurálva a címtár egyik objektumán sem, a varázsló az MS-DS-ConsistencyGuid használja sourceAnchor attribútumként. Ha az attribútum egy vagy több objektumon van konfigurálva a címtárban, a varázsló arra a következtetésre jut, hogy az attribútumot más alkalmazások használják, és nem alkalmas sourceAnchor attribútumként...

* Ebben az esetben a varázsló visszatér a objectGUID használatára a sourceAnchor attribútumként.

* A sourceAnchor attribútum eldöntése után a varázsló tárolja az adatokat az Azure AD-bérlőben. Az adatokat a Azure AD Connect jövőbeli telepítése fogja használni.

Az expressz telepítés befejezése után a varázsló tájékoztatja, hogy melyik attribútumot választotta a forrás-szerkesztőpont attribútumként.

![A varázsló a sourceAnchor számára kiválasztott AD attribútumot értesíti](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Egyéni telepítés
A Azure AD Connect egyéni módban való telepítésekor a Azure AD Connect varázsló két lehetőséget biztosít a sourceAnchor attribútum konfigurálásakor:

![Egyéni telepítés – sourceAnchor-konfiguráció](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Beállítás | Leírás |
| --- | --- |
| Let Azure manage the source anchor for me (Az Azure kezelje a forráshorgonyt) | Válassza ezt a lehetőséget, ha azt szeretné, hogy az Azure AD válassza ki az attribútumot. Ha ezt a lehetőséget választja, a Azure AD Connect varázsló ugyanazt a [sourceAnchor attribútumot alkalmazza, amelyet az expressz telepítéskor használ](#express-installation)a rendszer. Az expressz telepítéshez hasonlóan a varázsló arról tájékoztatja, hogy az egyéni telepítés befejeződése után melyik attribútumot választotta a forrás-szerkesztőpont attribútumként. |
| A specific attribute (Egy adott attribútum) | Válassza ezt a lehetőséget, ha meglévő AD-attribútumot szeretne megadni forráshorgony-attribútumként. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>A ConsistencyGuid funkció engedélyezése – meglévő telepítés
Ha meglévő Azure AD Connect üzemelő példánya, amely a objectGUID-t használja a forrás-szerkesztőpont attribútumként, ehelyett átválthatja a ConsistencyGuid használatára.

>[!NOTE]
> Csak a Azure AD Connect újabb verziói (1.1.552.0 és utána) támogatják a ObjectGuid-ről a ConsistencyGuid-re való áttérést a forrás-Anchor attribútumként.

Váltás a objectGUID és a ConsistencyGuid között a forrás-horgony attribútumként:

1. Indítsa el a Azure AD Connect varázslót, és kattintson a **configure (Konfigurálás** ) gombra a feladatok képernyőre való ugráshoz.

2. Jelölje be a **forrás-rögzítési feladat konfigurálása** beállítást, majd kattintson a **tovább** gombra.

   ![ConsistencyGuid engedélyezése meglévő központi telepítéshez – 2. lépés](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Adja meg az Azure AD-rendszergazdai hitelesítő adatait, és kattintson a **tovább** gombra.

4. Azure AD Connect varázsló elemzi az MS-DS-ConsistencyGuid attribútum állapotát a helyszíni Active Directory. Ha az attribútum nincs konfigurálva a címtár egyik objektumán sem, Azure AD Connect azt a következtetést vonja le, hogy egy másik alkalmazás jelenleg nem használja az attribútumot, és biztonságos a forrás-szerkesztőpont attribútumként használni. A folytatáshoz kattintson a **Tovább** gombra.

   ![ConsistencyGuid engedélyezése meglévő központi telepítéshez – 4. lépés](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. A konfigurálásra **kész** képernyőn kattintson a **Konfigurálás** elemre a konfiguráció módosításának elvégzéséhez.

   ![ConsistencyGuid engedélyezése meglévő központi telepítéshez – 5. lépés](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. A konfiguráció befejeződése után a varázsló azt jelzi, hogy az MS-DS-ConsistencyGuid most már a forrás-Anchor attribútumként van használatban.

   ![ConsistencyGuid engedélyezése meglévő központi telepítéshez – 6. lépés](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Az elemzés során (4. lépés), ha az attribútum egy vagy több objektumon van konfigurálva a címtárban, a varázsló arra a következtetésre jut, hogy az attribútumot egy másik alkalmazás használja, és az alábbi ábrán látható hibát ad vissza. Ez a hiba akkor is előfordulhat, ha korábban engedélyezte a ConsistencyGuid funkciót az elsődleges Azure AD Connect-kiszolgálón, és ugyanezt az átmeneti kiszolgálón próbálja elvégezni.

![ConsistencyGuid engedélyezése meglévő központi telepítéshez – hiba](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Ha biztos abban, hogy az attribútumot más meglévő alkalmazások nem használják, a Azure AD Connect varázsló újraindításával elvégezheti a hibát a megadott **/SkipLdapSearch** kapcsolóval. Ehhez futtassa a következő parancsot a parancssorban:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>AD FS vagy külső összevonási konfigurációra gyakorolt hatás
Ha Azure AD Connectt használ a helyszíni AD FS központi telepítésének kezeléséhez, a Azure AD Connect automatikusan frissíti a jogcím-szabályokat, hogy ugyanazt az AD attribútumot használják, mint a sourceAnchor. Ez biztosítja, hogy az ADFS által létrehozott ImmutableID-jogcím konzisztens legyen az Azure AD-ba exportált sourceAnchor-értékekkel.

Ha Azure AD Connectn kívüli AD FS felügyel, vagy harmadik féltől származó összevonási kiszolgálókat használ a hitelesítéshez, akkor manuálisan kell frissítenie a ImmutableID jogcím szabályait úgy, hogy konzisztensek legyenek az Azure AD-ba exportált sourceAnchor-értékekkel, az [AD FS jogcímek módosítása](./how-to-connect-fed-management.md#modclaims)című szakaszban leírtak szerint. A varázsló a telepítés befejeződése után a következő figyelmeztetést adja vissza:

![Harmadik féltől származó összevonási konfiguráció](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Új címtárak hozzáadása meglévő központi telepítéshez
Tegyük fel, hogy üzembe helyezte a Azure AD Connectt a ConsistencyGuid szolgáltatás engedélyezésével, és most egy másik könyvtárat szeretne hozzáadni a központi telepítéshez. Amikor megpróbálja felvenni a könyvtárat, Azure AD Connect varázsló ellenőrzi az MS-DS-ConsistencyGuid attribútum állapotát a címtárban. Ha az attribútum egy vagy több objektumon van konfigurálva a címtárban, a varázsló arra a következtetésre jut, hogy az attribútumot más alkalmazások használják, és az alábbi ábrán jelzett hibát ad vissza. Ha biztos abban, hogy a meglévő alkalmazások nem használják az attribútumot, akkor a Azure AD Connect varázsló újraindításával letilthatja a fentiekben leírt **/SkipLdapSearch** kapcsolót, vagy további információért kapcsolatba kell lépnie az ügyfélszolgálattal.

![Új címtárak hozzáadása meglévő központi telepítéshez](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD-bejelentkezés
A helyszíni címtár és az Azure AD integrálásával fontos tisztában lenni azzal, hogy a szinkronizálási beállítások milyen hatással lehetnek a felhasználók hitelesítésére. Az Azure AD userPrincipalName (UPN) használatával hitelesíti a felhasználót. Ha azonban szinkronizálja a felhasználókat, ki kell választania a userPrincipalName értékéhez használni kívánt attribútumot.

### <a name="choosing-the-attribute-for-userprincipalname"></a>A userPrincipalName attribútumának kiválasztása
Ha kiválasztja az attribútumot az Azure-ban használandó egyszerű felhasználónév értékének megadásához, akkor biztosítania kell a következőt:

* Az attribútum értékei megfelelnek az UPN-szintaxisnak (RFC 822), vagyis a Felhasználónév tartományának kell lennie \@
* Az értékek utótagja megegyezik az Azure AD-ben ellenőrzött egyéni tartományok valamelyikével

Az expressz beállításokban az attribútum feltételezett megválasztása userPrincipalName. Ha a userPrincipalName attribútum nem tartalmazza azt az értéket, amelyet a felhasználóknak be kell jelentkezniük az Azure-ba, akkor **Egyéni telepítést** kell választania.

### <a name="custom-domain-state-and-upn"></a>Egyéni tartomány állapota és UPN
Fontos, hogy ellenőrizze, hogy van-e ellenőrzött tartomány az UPN-utótaghoz.

John a contoso.com felhasználója. Azt szeretné, hogy János a helyszíni UPN John contoso.com használatával jelentkezzen be az Azure-ba, \@ miután szinkronizálta a felhasználókat az Azure ad-címtár contoso.onmicrosoft.com. Ehhez hozzá kell adnia és ellenőriznie kell a contoso.com egyéni tartományként az Azure AD-ben a felhasználók szinkronizálásának megkezdése előtt. Ha a János UPN-utótagja (például contoso.com) nem felel meg egy ellenőrzött tartománynak az Azure AD-ben, akkor az Azure AD az UPN-utótagot az contoso.onmicrosoft.com-vel helyettesíti.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Nem irányítható helyszíni tartományok és UPN az Azure AD-hez
Egyes szervezetek nem irányítható tartományokkal, például a contoso. local vagy az egyszerű, egycímkés tartományokkal, például a contoso. Nem lehet ellenőrizni az Azure AD-ben nem irányítható tartományt. Azure AD Connect csak ellenőrzött tartományba tud szinkronizálni az Azure AD-ben. Amikor létrehoz egy Azure AD-címtárat, egy olyan, az Azure AD-hoz tartozó alapértelmezett tartományba kerülő, áthelyezhető tartományt hoz létre, például contoso.onmicrosoft.com. Ezért szükség lesz arra, hogy ellenőrizze a többi átirányítható tartományt ebben az esetben, ha nem szeretne szinkronizálni az alapértelmezett onmicrosoft.com-tartománnyal.

A tartományok hozzáadásával és ellenőrzésével kapcsolatos további információkért olvassa el [az Egyéni tartománynév hozzáadása a Azure Active Directoryhoz](../fundamentals/add-custom-domain.md) című témakört.

Azure AD Connect észleli, ha nem átirányítható tartományi környezetben fut, és megfelelően figyelmezteti Önt az expressz beállításokkal. Ha nem átirányítható tartományban működik, akkor valószínű, hogy a felhasználók UPN-je nem irányítható utótaggal rendelkezik. Ha például a contoso. local alatt fut, Azure AD Connect javasolja, hogy az expressz beállítások használata helyett egyéni beállításokat használjon. Egyéni beállításokkal megadhatja azt az attribútumot, amelyet UPN-ként kell használni az Azure-ba való bejelentkezéshez, miután a felhasználók szinkronizálva lettek az Azure AD szolgáltatással.

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).