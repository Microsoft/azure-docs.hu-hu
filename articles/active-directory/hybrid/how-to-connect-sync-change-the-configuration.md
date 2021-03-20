---
title: 'Azure AD Connect szinkronizálás: az alapértelmezett konfiguráció módosítása'
description: Végigvezeti az Azure AD Connect Sync konfigurációjának módosításán.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2044653673da10de59d5ff125da44ac1f89e22f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861850"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect szinkronizálás: az alapértelmezett konfiguráció módosítása
Ebből a cikkből megtudhatja, hogyan módosíthatja az alapértelmezett konfigurációt Azure Active Directory (Azure AD) csatlakozási szinkronizálásban. Néhány gyakori forgatókönyvhöz nyújt lépéseket. Ezzel az ismerettel a saját üzleti szabályai alapján egyszerű módosításokat végezhet saját konfigurációjában.

> [!WARNING]
> Ha módosítja az alapértelmezett használaton kívüli szinkronizálási szabályokat, akkor ezek a változások felül lesznek írva, amikor a következő Azure AD Connect frissülnek, ami váratlan és valószínűleg nemkívánatos szinkronizálási eredményeket eredményez.
>
> Az alapértelmezett beépített szinkronizálási szabályok ujjlenyomattal rendelkeznek. Ha módosítja ezeket a szabályokat, az ujjlenyomat már nem megfelelő. A jövőben problémák merülhetnek fel, amikor új Azure AD Connect-kiadást próbál alkalmazni. Ebben a cikkben csak a módosításokat hajtja végre.

## <a name="synchronization-rules-editor"></a>Szinkronizációs szabályok szerkesztője
A szinkronizálási szabályok szerkesztője az alapértelmezett konfiguráció megtekintésére és módosítására szolgál. A **Start** menüben a **Azure ad Connect** csoport alatt találhatja meg.  
![Start menü szinkronizálási szabály szerkesztőjével](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

A szerkesztő megnyitásakor megjelenik az alapértelmezett beépített szabályok.

![A szinkronizálási szabály szerkesztője](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigálás a szerkesztőben
A szerkesztő tetején található legördülő lista használatával gyorsan megtalálhatja az adott szabályt. Ha például meg szeretné tekinteni azokat a szabályokat, amelyekben a proxyAddresses attribútum szerepel, a legördülő lista a következőre módosítható:  
![SRE szűrés](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
A szűrés alaphelyzetbe állításához és egy új konfiguráció betöltéséhez nyomja le az F5 billentyűt a billentyűzeten.

A jobb felső sarokban az **új szabály hozzáadása** gomb látható. Ennek a gombnak a használatával saját egyéni szabályt hozhat létre.

Az alul található gombok a kiválasztott szinkronizálási szabályon való működéshez szükségesek. **Szerkesztheti** és **törölheti** a várt műveleteket. Az **Exportálás** egy PowerShell-szkriptet hoz létre a szinkronizálási szabály újbóli létrehozásához. Ezzel az eljárással áthelyezheti a szinkronizálási szabályt az egyik kiszolgálóról a másikra.

## <a name="create-your-first-custom-rule"></a>Az első egyéni szabály létrehozása
A leggyakoribb változások az attribútum folyamatai. Előfordulhat, hogy a forrás könyvtára nem azonos az Azure AD-beli adataival. A jelen szakasz példájában ellenőrizze, hogy a felhasználó megadott neve mindig *megfelelő*-e.

### <a name="disable-the-scheduler"></a>Ütemező letiltása
Alapértelmezés szerint az [ütemező](how-to-connect-sync-feature-scheduler.md) 30 percenként fut. Győződjön meg róla, hogy nem indul el, amíg a módosításokat végez, és elhárítja az új szabályokat. Az ütemező ideiglenes letiltásához indítsa el a PowerShellt, és futtassa a parancsot `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![Ütemező letiltása](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>A szabály létrehozása
1. Kattintson az **új szabály hozzáadása** lehetőségre.
2. A **Leírás** lapon adja meg a következőket:  
   ![Bejövő szabályok szűrése](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Név**: adjon egy leíró nevet a szabálynak.
   * **Leírás**: adjon meg némi pontosítást, hogy valaki más tudja megérteni, mi a szabály.
   * **Csatlakoztatott rendszer**: ez az a rendszer, amelyben az objektum található. Ebben az esetben válassza az **Active Directory-összekötő** lehetőséget.
   * **Csatlakoztatott rendszer/metaverse objektumtípus**: válassza a **felhasználó** és a **személy** lehetőséget.
   * **Hivatkozás típusa**: módosítsa ezt az értéket a **csatlakozáshoz**.
   * **Prioritás**: adjon meg egy értéket, amely egyedi a rendszeren. Az alacsonyabb numerikus érték magasabb prioritást jelez.
   * **Címke**: hagyja üresen. A Microsoft csak a beépített szabályainak megfelelően kell kitölteni ezt a mezőt.
3. A **hatókör-szűrő** lapon adja meg a **givenName ISNOTNULL**.  
   ![Bejövő szabály hatókörének szűrője](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Ez a szakasz határozza meg, hogy mely objektumokra vonatkozzon a szabály. Ha üresen hagyta, a szabály az összes felhasználói objektumra érvényes lesz. Azonban ez magában foglalja a konferenciatermek, a szolgáltatásfiókok és az egyéb nem személy felhasználói objektumokat.
4. Az **illesztési szabályok** lapon hagyja üresen a mezőt.
5. Az **átalakítások** lapon módosítsa a **FlowType** **kifejezést kifejezésre**. A **Target attribútumnál** válassza a **givenName** lehetőséget. A **forrás** mezőben adja meg a következőt: **PCase ([givenName])**.
   ![Bejövő szabályok átalakítása](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   A Szinkronizáló motor megkülönbözteti a kis-és nagybetűket a függvény nevével és az attribútum nevével. Ha valami helytelen értéket ad meg, a szabály hozzáadásakor figyelmeztetés jelenik meg. Mentheti és folytathatja, de újra meg kell nyitnia és ki kell javítania a szabályt.
6. A szabály mentéséhez kattintson a **Hozzáadás** gombra.

Az új egyéni szabálynak meg kell jelennie a rendszeren lévő többi szinkronizálási szabálynak.

### <a name="verify-the-change"></a>A módosítás ellenőrzése
Ezzel az új módosítással győződjön meg róla, hogy a várt módon működik-e, és nem dobott semmilyen hibát. Az objektumok számától függően kétféleképpen hajthatja végre ezt a lépést:

- Minden objektumon futtasson teljes szinkronizálást.
- Egyetlen objektumon futtathatja az előnézetet és a teljes szinkronizálást.

Nyissa meg a **szinkronizálási szolgáltatást** a **Start** menüből. Az ebben a szakaszban szereplő lépések mindegyike ebben az eszközben található.

**Teljes szinkronizálás minden objektumon**  

   1. Válassza a felső **Összekötők** lehetőséget. Azonosítsa az előző szakaszban módosított összekötőt (ebben az esetben Active Directory Domain Services), majd jelölje ki. 
   2. A **műveletek** területen válassza a **Futtatás** lehetőséget.
   3. Válassza a **teljes szinkronizálás** lehetőséget, majd kattintson **az OK gombra**.
   ![Teljes szinkronizálás](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Az objektumok mostantól a metaverse-ben frissülnek. Ellenőrizze a módosításokat úgy, hogy megtekinti a metaverse objektumát.

**Előzetes verzió és teljes szinkronizálás egyetlen objektumon**  

   1. Válassza a felső **Összekötők** lehetőséget. Azonosítsa az előző szakaszban módosított összekötőt (ebben az esetben Active Directory Domain Services), majd jelölje ki.
   2. Válassza a **Keresés összekötő terület** lehetőséget. 
   3. A **hatókör** használatával megkeresheti a módosítás teszteléséhez használni kívánt objektumot. Jelölje ki az objektumot, majd kattintson az **előnézet** gombra. 
   4. Az új képernyőn válassza a **véglegesítés várható eredménye** lehetőséget.  
   ![Előzetes verzió véglegesítve](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   A módosítás most véglegesítve lett a metaverse-ben.

**Az objektum megtekintése a metaverse-ben**  

1. Válasszon ki néhány objektumot, és győződjön meg arról, hogy a várt érték és a szabály alkalmazása megtörténik. 
2. Válassza ki a **metaverse Search** elemet a felső listából. Vegyen fel minden olyan szűrőt, amely a megfelelő objektumok megtalálásához szükséges. 
3. A keresési eredmények közül nyisson meg egy objektumot. Tekintse meg az attribútumok értékeit, és ellenőrizze a **szinkronizálási szabályok** oszlopban, hogy a szabály a várt módon van-e alkalmazva.  
![Keresés a metaverzumban](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>A Scheduler engedélyezése
Ha minden a vártnak megfelelően, engedélyezheti az ütemező újbóli engedélyezését. A PowerShellből futtassa a parancsot `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="other-common-attribute-flow-changes"></a>Más általános attribútum-flow-változások
Az előző szakasz azt ismerteti, hogyan lehet módosítani az attribútumok folyamatát. Ebben a szakaszban további példákat is ismertetünk. A szinkronizálási szabály létrehozásának lépései rövidítve jelennek meg, de az előző szakaszban megtalálhatja a teljes lépéseket.

### <a name="use-an-attribute-other-than-the-default"></a>Az alapértelmezetttől eltérő attribútum használata
Ebben a fabrikam-forgatókönyvben egy olyan erdő található, amelyben a név, a vezetéknév és a megjelenítendő név a helyi ábécét használja. Ezen attribútumok latin betűs ábrázolása a bővítmény attribútumaiban található. Az Azure AD-ben és a Microsoft 365ban a globális címlista létrehozásához a szervezet ezeket az attribútumokat szeretné használni.

Alapértelmezett konfiguráció esetén a helyi erdő egy objektuma így néz ki:  
![Attribútum 1. folyamata](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Ha más attribútum-folyamatokkal rendelkező szabályt szeretne létrehozni, tegye a következőket:

1. Nyissa meg a **szinkronizálási szabályok szerkesztőjét** a **Start** menüből.
2. Ha a **bejövő** továbbra is a bal oldalon van kiválasztva, kattintson az **új szabály hozzáadása** gombra.
3. Adja meg a szabály nevét és leírását. Válassza ki a helyszíni Active Directory példányt és a megfelelő objektumtípust. A **kapcsolat típusa** területen válassza a **Csatlakozás** lehetőséget. **Elsőbbség** esetén válasszon olyan számot, amelyet más szabály nem használ. A beépített szabályok a 100-as verzióval kezdődnek, így ebben a példában a 50 értéket lehet használni.
  ![2. attribútum](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Hagyja üresen a **hatókör-szűrőt** . (Ez azt eredményezi, hogy az erdőben lévő összes felhasználói objektumra alkalmaznia kell.)
5. Az **illesztési szabályok** üresek maradnak. (Ez azt is lehetővé teszi, hogy a beépített szabály kezelje az illesztéseket.)
6. Az **átalakításokban** hozza létre a következő folyamatokat:  
  ![3. attribútum](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. A szabály mentéséhez kattintson a **Hozzáadás** gombra.
8. Lépjen **synchronization Service Manager**. Az **Összekötők** területen válassza ki azt az összekötőt, ahová a szabályt hozzáadta. Válassza a **Futtatás** lehetőséget, majd válassza a **teljes szinkronizálás** lehetőséget. A teljes szinkronizálás az aktuális szabályok alapján újraszámítja az összes objektumot.

Ez az egyéni szabállyal azonos objektum eredménye:  
![4. attribútum](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Attribútumok hossza
A karakterlánc attribútumai alapértelmezés szerint indexelhető, a maximális hossz pedig 448 karakter. Ha olyan karakterlánc-attribútumokkal dolgozik, amelyek többt is tartalmazhatnak, ügyeljen arra, hogy a következőt tartalmazza az attribútum folyamatában:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>A userPrincipalSuffix módosítása
A felhasználók nem ismerik a userPrincipalName attribútumot a Active Directoryban, és előfordulhat, hogy nem alkalmasak bejelentkezési AZONOSÍTÓként való használatra. A Azure AD Connect Sync telepítővarázsló segítségével másik attribútumot is választhat – például a *levelezést*. Bizonyos esetekben azonban az attribútumot ki kell számítani.

A contoso cég például két Azure AD-címtárral rendelkezik, egyet az éles környezethez, egyet pedig teszteléshez. Azt szeretnék, hogy a tesztelési bérlőben lévő felhasználók egy másik utótagot használjanak a bejelentkezési AZONOSÍTÓban:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Ebben a kifejezésben minden az első @-sign (szó) és egy rögzített karakterlánccal összefűzött karakterláncot kell kihagyni.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Többértékű attribútum átalakítása egyetlen értékre
Active Directory néhány attribútuma többértékű a sémában, annak ellenére, hogy Active Directory felhasználók és számítógépek esetében egyértékű. Példa a Description (Leírás) attribútumra:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Ebben a kifejezésben, ha az attribútum értékkel rendelkezik, akkor az attribútum első elemének (*elemének*) eltávolításához távolítsa el a kezdő és záró szóközöket (*Trim*), majd tartsa meg az első 448 karaktert (*balra*) a karakterláncban.

### <a name="do-not-flow-an-attribute"></a>Ne flow-attribútum
Az ebben a szakaszban található forgatókönyv hátterében lásd: [az attribútum folyamatának szabályozása](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Az attribútumok kétféleképpen nem áramlanak. Az első a [kiválasztott attribútumok eltávolítására](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering)szolgáló telepítővarázsló használatával. Ez a beállítás akkor működik, ha még soha nem szinkronizálta az attribútumot. Ha azonban elkezdte szinkronizálni ezt az attribútumot, és később eltávolítja ezt a funkciót, a Szinkronizáló motor leállítja az attribútum kezelését, és a meglévő értékek az Azure AD-ben maradnak.

Ha el szeretné távolítani egy attribútum értékét, és győződjön meg arról, hogy a jövőben nem áramlik fel, létre kell hoznia egy egyéni szabályt.

Ebben a fabrikam-forgatókönyvben észrevettük, hogy a felhőbe szinkronizált attribútumok némelyike nem lehet ott. Szeretnénk meggyőződni arról, hogy ezek az attribútumok el lesznek távolítva az Azure AD-ből.  
![Rossz kiterjesztési attribútumok](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Hozzon létre egy új bejövő szinkronizálási szabályt, és töltse fel a leírást.
  ![Leírások](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Hozzon létre attribútumokat a **FlowType** és a **AuthoritativeNull** for **Source** **kifejezéssel** . A literális **AuthoritativeNull** azt jelzi, hogy az értéknek üresnek kell lennie a metaverse-ben, még akkor is, ha egy alacsonyabb prioritású szinkronizálási szabály megpróbálja feltölteni az értéket.
  ![A bővítmény attribútumainak átalakítása](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Mentse a szinkronizálási szabályt. Indítsa el a **szinkronizálási szolgáltatást**, keresse meg az összekötőt, válassza a **Futtatás** lehetőséget, majd válassza a **teljes szinkronizálás** lehetőséget. Ez a lépés újraszámítja az összes attribútum folyamatát.
4. Győződjön meg arról, hogy a kívánt módosítások exportálására kerül sor az összekötő területének keresésével.
  ![Előkészített törlés](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Szabályok létrehozása a PowerShell-lel
A szinkronizálási szabály szerkesztőjének használata jól működik, ha csak néhány módosítást hajt végre. Ha sok módosítást kell végeznie, a PowerShell lehet jobb megoldás. Néhány speciális funkció csak a PowerShell használatával érhető el.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Egy beépített szabály PowerShell-parancsfájljának beolvasása
A beépített szabályt létrehozó PowerShell-parancsfájl megtekintéséhez válassza ki a szabályt a szinkronizálási szabályok szerkesztőben, majd kattintson az **Exportálás** elemre. Ez a művelet megadja a szabályt létrehozó PowerShell-parancsfájlt.

### <a name="advanced-precedence"></a>Speciális prioritás
A beépített szinkronizálási szabályok a 100-as prioritási értékkel kezdődnek. Ha sok erdővel rendelkezik, és sok egyéni módosítást kell végeznie, akkor előfordulhat, hogy a 99 szinkronizálási szabályok nem elégségesek.

A beépített szabályok előtt utasíthatja arra a szinkronizálási motort, amelyhez további szabályokat kíván beszúrni. Ennek a viselkedésnek a beszerzéséhez kövesse az alábbi lépéseket:

1. Jelölje meg az első, a szinkronizálási szabályok szerkesztőjében található, a **AD-User illesztésből** bekapcsolt szinkronizálási szabályt, és válassza az **Exportálás** lehetőséget. Másolja az SR-azonosító értékét.  
![A PowerShell módosítása előtt](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Hozza létre az új szinkronizálási szabályt. A szinkronizálási szabályok szerkesztőjével létrehozhatja. Exportálja a szabályt egy PowerShell-parancsfájlba.
3. A **PrecedenceBefore** tulajdonságban szúrja be az azonosító értékét a nem beépített szabályból. Állítsa a **sorrendet** **0-ra**. Győződjön meg arról, hogy az azonosító attribútum egyedi, és hogy nem használ GUID azonosítót egy másik szabálytól. Győződjön meg arról is, hogy a **ImmutableTag** tulajdonság nincs beállítva. Ezt a tulajdonságot csak egy beépített szabályhoz kell beállítani.
4. Mentse a PowerShell-parancsfájlt, és futtassa. Ennek eredményeképpen az egyéni szabály a 100-as prioritási értékkel van társítva, és az összes többi beépített szabály is növekszik.  
![PowerShell a módosítás után](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Ha szükséges, több egyéni szinkronizálási szabályt is használhat ugyanazzal a **PrecedenceBefore** értékkel.

## <a name="enable-synchronization-of-usertype"></a>A UserType szinkronizálásának engedélyezése
Azure AD Connect támogatja a **UserType** attribútum szinkronizálását a 1.1.524.0 vagy újabb verzióban lévő **felhasználói** objektumokhoz. Pontosabban a következő módosítások lettek bevezetve:

- Az Azure AD Connectorban az objektumtípus **felhasználójának** sémája ki van bővítve, hogy tartalmazza a UserType attribútumot, amely karakterlánc típusú, és egyértékű.
- A metaverse-beli objektumtípus **személy** sémája ki van bővítve, hogy tartalmazza a UserType attribútumot, amely karakterlánc típusú, és egyértékű.

Alapértelmezés szerint a UserType attribútum nincs engedélyezve a szinkronizáláshoz, mert nincs megfelelő UserType attribútum a helyszíni Active Directoryban. Manuálisan kell engedélyeznie a szinkronizálást. Mielőtt ezt megtenné, jegyezze fel az alábbi, az Azure AD által kényszerített viselkedést:

- Az Azure AD csak két értéket fogad el a UserType attribútumhoz: **tag** és **vendég**.
- Ha a UserType attribútum nincs engedélyezve a Azure AD Connect való szinkronizáláshoz, a címtár-szinkronizálás révén létrehozott Azure AD-felhasználók rendelkezhetnek a **tag** értékre a UserType attribútummal.
- A 1.5.30.0 verzió előtt az Azure AD nem engedélyezte a meglévő Azure AD-felhasználók UserType attribútumának módosítását Azure AD Connect. A régebbi verziók esetében csak az Azure AD-felhasználók létrehozásakor és a [PowerShell használatával módosult](/powershell/module/azuread/set-azureaduser).

A UserType attribútum szinkronizálásának engedélyezése előtt először el kell döntenie, hogyan származtatja az attribútumot a helyszíni Active Directoryból. A leggyakoribb módszerek a következők:

- Kijelöl egy nem használt helyszíni AD-attribútumot (például extensionAttribute1), amelyet a forrás attribútumként kíván használni. A kijelölt helyszíni ad-attribútumnak **karakterlánc** típusúnak kell lennie, egyértékű kell lennie, és tartalmaznia kell a **tag** vagy a **vendég** értéket. 

    Ha ezt a módszert választja, meg kell győződnie arról, hogy a kijelölt attribútum megfelelő értékkel van feltöltve a helyszíni Active Directoryban lévő összes meglévő felhasználói objektumhoz, amelyeket az Azure AD-vel szinkronizált a UserType attribútum szinkronizálásának engedélyezése előtt.

- Azt is megteheti, hogy származtatja a UserType attribútum értékét más tulajdonságok alapján. Például ha a helyszíni AD userPrincipalName attribútuma a tartomány részével **végződik,** szinkronizálni szeretné az összes felhasználót <em>@partners.fabrikam123.org</em> . 

    Ahogy korábban említettük, a Azure AD Connect régebbi verziói nem teszik lehetővé a meglévő Azure AD-felhasználók UserType attribútumának Azure AD Connect általi módosítását. Ezért biztosítania kell, hogy az Ön által választott logika konzisztens legyen azzal, hogy a UserType attribútum hogyan van konfigurálva a bérlő összes meglévő Azure AD-felhasználója számára.

A UserType attribútum szinkronizálásának engedélyezéséhez szükséges lépések a következőképpen foglalhatók össze:

1.  Tiltsa le a szinkronizálási ütemező szolgáltatást, és ellenőrizze, hogy folyamatban van-e a szinkronizálás.
2.  Adja hozzá a forrás attribútumot a helyszíni AD Connector sémához.
3.  Adja hozzá a UserType az Azure AD Connector sémához.
4.  Hozzon létre egy bejövő szinkronizálási szabályt, amely a helyszíni Active Directory az attribútumérték áramlását hajtja végre.
5.  Hozzon létre egy kimenő szinkronizálási szabályt az attribútum értékének az Azure AD-be való áthaladásához.
6.  Futtasson egy teljes szinkronizálási ciklust.
7.  A szinkronizálási ütemező engedélyezése.

>[!NOTE]
> A szakasz további része ezeket a lépéseket ismerteti. A leírásokat egy, az egyerdős topológiával rendelkező Azure AD-telepítés kontextusában, egyéni szinkronizálási szabályok nélkül ismertetjük. Ha többerdős topológiával rendelkezik, az egyéni szinkronizálási szabályok konfigurálva vannak, vagy átmeneti kiszolgálóval rendelkezik, a lépéseket ennek megfelelően kell módosítania.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés: a szinkronizálási ütemező letiltása, és annak ellenőrzése, hogy nincs-e folyamatban szinkronizálás
Ha nem szeretné, hogy az Azure AD nem kívánt módosításait ne exportálja, győződjön meg arról, hogy a szinkronizálási szabályok frissítése közben nem történik szinkronizálás. A beépített szinkronizálási ütemező letiltása:

 1. Indítsa el a PowerShell-munkamenetet a Azure AD Connect-kiszolgálón.
 2. A parancsmag futtatásával tiltsa le az ütemezett szinkronizálást `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 3. A   >  **szinkronizálási szolgáltatás** elindításához nyissa meg a synchronization Service Manager.
 4. Lépjen az **Operations (műveletek** ) lapra, és ellenőrizze, hogy nincs *-e folyamatban* állapotú művelet.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>2. lépés: a forrás attribútum hozzáadása a helyszíni AD Connector sémához
Nem minden Azure AD-attribútum importálható a helyszíni AD-összekötő területére. A forrás attribútum hozzáadása az importált attribútumok listájához:

 1. Nyissa meg a Synchronization Service Manager **Összekötők** lapját.
 2. Kattintson a jobb gombbal a helyszíni AD-összekötőre, és válassza a **Tulajdonságok** lehetőséget.
 3. Az előugró párbeszédpanelen lépjen az **attribútumok kiválasztása** lapra.
 4. Győződjön meg arról, hogy a forrás attribútum be van jelölve az attribútumok listájában.
 5. Kattintson **az OK** gombra a mentéshez.
![Forrás attribútum hozzáadása a helyszíni AD Connector sémához](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-attribute-to-the-azure-ad-connector-schema"></a>3. lépés: a UserType attribútum hozzáadása az Azure AD Connector sémához
Alapértelmezés szerint a rendszer nem importálja a UserType attribútumot a Azure AD Connect térbe. A UserType attribútum hozzáadása az importált attribútumok listájához:

 1. Nyissa meg a Synchronization Service Manager **Összekötők** lapját.
 2. Kattintson a jobb gombbal az **Azure ad-összekötőre** , és válassza a **Tulajdonságok** lehetőséget.
 3. Az előugró párbeszédpanelen lépjen az **attribútumok kiválasztása** lapra.
 4. Győződjön meg arról, hogy az UserType attribútum be van jelölve az attribútumok listájában.
 5. Kattintson **az OK** gombra a mentéshez.

![Forrás attribútum hozzáadása az Azure AD Connector sémához](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>4. lépés: hozzon létre egy bejövő szinkronizálási szabályt, amely az attribútumérték helyi Active Directory való áramlását hajtja végre.
A bejövő szinkronizálási szabály lehetővé teszi, hogy az attribútumérték a helyszíni Active Directoryról a metaverse-re váltson a forrás attribútumból:

1. A **szinkronizálási** szabályok szerkesztőjének megnyitásához nyissa meg a  >  **szinkronizálási szabályok szerkesztőjét**.
2. Állítsa be a keresési szűrő **irányát** **bejövő** értékre.
3. Új bejövő szabály létrehozásához kattintson az **új szabály hozzáadása** gombra.
4. A **Leírás** lapon adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Name | *Adjon meg egy nevet* | Például az *ad – User UserType* |
    | Description | *Adja meg a leírást* |  |
    | Csatlakoztatott rendszerek | *A helyszíni AD-összekötő kiválasztása* |  |
    | Csatlakoztatott rendszerobjektum típusa | **Felhasználó** |  |
    | Metaverse objektum típusa | **Személy** |  |
    | Hivatkozás típusa | **Csatlakozás** |  |
    | Precedencia | *Válasszon egy 1 – 99 közötti számot* | az 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Ne válasszon olyan értéket, amelyet egy másik szinkronizálási szabály használ. |

5. Lépjen a **hatóköri szűrő** lapra, és vegyen fel **egyetlen hatókörű szűrőt** a következő záradékkal:

    | Attribútum | Operátor | Érték |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Felhasználói\_ |

    A hatóköri szűrő határozza meg, hogy mely helyszíni AD-objektumokra vonatkozik ez a bejövő szinkronizálási szabály. Ebben a példában ugyanazt a hatókör-szűrőt használjuk, amelyet a *in ad – User Common* out-of-box szinkronizációs szabály használ, amely megakadályozza, hogy a szinkronizálási szabály az Azure ad felhasználói visszaírási szolgáltatáson keresztül létrehozott felhasználói objektumokra legyen alkalmazva. Előfordulhat, hogy a Azure AD Connect központi telepítésének megfelelően kell megcsípése a hatókör-szűrőt.

6. Nyissa meg az **átalakítás** lapot, és hajtsa végre a kívánt átalakítási szabályt. Ha például egy nem használt helyszíni ad-attribútumot (például extensionAttribute1) jelölt ki a UserType forrás attribútuma, akkor egy közvetlen attribútum-folyamatot valósíthat meg:

    | Folyamat típusa | Cél attribútum | Forrás | Egyszeri alkalmazás | Egyesítés típusa |
    | --- | --- | --- | --- | --- |
    | Direct | UserType (Felhasználótípus) | extensionAttribute1 | Nincs bejelölve | Frissítés |

    Egy másik példában szeretné származtatni a UserType attribútum értékét más tulajdonságok alapján. Például ha a helyszíni AD userPrincipalName attribútuma a tartomány részével végződik, szinkronizálni szeretné az összes felhasználót <em>@partners.fabrikam123.org</em> . A következőhöz hasonló kifejezés valósítható meg:

    | Folyamat típusa | Cél attribútum | Forrás | Egyszeri alkalmazás | Egyesítés típusa |
    | --- | --- | --- | --- | --- |
    | Expression | UserType (Felhasználótípus) | IIF (IsPresent ([userPrincipalName]), IIF (CBool (LCase ([userPrincipalName]), " @partners.fabrikam123.org ") = 0), "tag", "vendég"), hiba ("a userPrincipalName nem áll rendelkezésre a UserType meghatározásához")) | Nincs bejelölve | Frissítés |

7. A Bejövő szabály létrehozásához kattintson a **Hozzáadás** gombra.

![Bejövő szinkronizálási szabály létrehozása](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>5. lépés: hozzon létre egy kimenő szinkronizációs szabályt az attribútum értékének az Azure AD-be való áthaladásához
A kimenő szinkronizálási szabály lehetővé teszi, hogy az attribútum értéke a metaverse-ből a UserType attribútumba kerüljön az Azure AD-ben:

1. Nyissa meg a szinkronizálási szabályok szerkesztőjét.
2. Állítsa a keresési szűrő **irányát** **kimenő** értékre.
3. Kattintson az **új szabály hozzáadása** gombra.
4. A **Leírás** lapon adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | ----- | ------ | --- |
    | Name | *Adjon meg egy nevet* | Például *a HRE – User UserType* |
    | Description | *Adja meg a leírást* ||
    | Csatlakoztatott rendszerek | *Válassza ki a HRE-összekötőt* ||
    | Csatlakoztatott rendszerobjektum típusa | **Felhasználó** ||
    | Metaverse objektum típusa | **Személy** ||
    | Hivatkozás típusa | **Csatlakozás** ||
    | Precedencia | *Válasszon egy 1 – 99 közötti számot* | az 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Ne válasszon olyan értéket, amelyet egy másik szinkronizálási szabály használ. |

5. Lépjen a **hatókör-szűrő** lapra, és vegyen fel **egyetlen hatókörű szűrőt** két záradékkal:

    | Attribútum | Operátor | Érték |
    | --- | --- | --- |
    | sourceObjectType | EGYENLŐ | User |
    | cloudMastered | NOTEQUAL | Igaz |

    A hatóköri szűrő határozza meg, hogy mely Azure AD-objektumokra vonatkozik ez a kimenő szinkronizálási szabály. Ebben a példában ugyanezt a hatókör-szűrőt használjuk a *kimenetből az ad-user identity* out-of-box szinkronizációs szabály alapján. Megakadályozza, hogy a szinkronizálási szabály olyan felhasználói objektumokra legyen alkalmazva, amelyek nincsenek szinkronizálva a helyszíni Active Directory. Előfordulhat, hogy a Azure AD Connect központi telepítésének megfelelően kell megcsípése a hatókör-szűrőt.

6. Nyissa meg az **átalakítás** lapot, és hajtsa végre a következő átalakítási szabályt:

    | Folyamat típusa | Cél attribútum | Forrás | Egyszeri alkalmazás | Egyesítés típusa |
    | --- | --- | --- | --- | --- |
    | Direct | UserType (Felhasználótípus) | UserType (Felhasználótípus) | Nincs bejelölve | Frissítés |

7. A Kimenő szabály létrehozásához kattintson a **Hozzáadás** gombra.

![Kimenő szinkronizálási szabály létrehozása](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>6. lépés: teljes szinkronizálási ciklus futtatása
Általánosságban elmondható, hogy teljes szinkronizálási ciklusra van szükség, mivel új attribútumok is hozzá lettek adva a Active Directory és az Azure AD Connector sémához, és bevezették az egyéni szinkronizálási szabályokat. Ellenőrizni szeretné a módosításokat az Azure AD-ba való exportálás előtt. 

A következő lépésekkel ellenőrizheti a módosításokat a teljes szinkronizálási ciklust alkotó lépések manuális futtatása során.

1. **Teljes importálás** futtatása a helyszíni **ad-összekötőn**:

   1. Nyissa meg a Synchronization Service Manager **Összekötők** lapját.
   2. Kattintson a jobb gombbal a helyszíni **ad-összekötőre** , és válassza a **Futtatás** lehetőséget.
   3. Az előugró párbeszédpanelen válassza a **teljes importálás** lehetőséget, majd kattintson **az OK** gombra.
   4. Várjon, amíg a művelet befejeződik.

      > [!NOTE]
      > Ha a forrás attribútum már szerepel az importált attribútumok listáján, kihagyhatja a teljes importálást a helyszíni AD-összekötőn. Más szóval nem kellett módosítania a [2. lépés: a forrás attribútum hozzáadása a helyszíni ad Connector sémához](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. **Teljes importálás** futtatása az **Azure ad-összekötőn**:

   1. Kattintson a jobb gombbal az **Azure ad-összekötőre** , és válassza a **Futtatás** lehetőséget.
   2. Az előugró párbeszédpanelen válassza a **teljes importálás** lehetőséget, majd kattintson **az OK** gombra.
   3. Várjon, amíg a művelet befejeződik.

3. A szinkronizálási szabály módosításainak ellenőrzése meglévő felhasználói objektumon:

    A helyszíni Active Directory és az Azure AD UserType származó forrásoldali attribútumot a rendszer a megfelelő összekötő-Tárhelybe importálta. A teljes szinkronizálás megkezdése előtt végezze el a helyszíni AD-összekötő területének egy meglévő felhasználói objektumának **előzetes** verzióját. A kiválasztott objektumnak a forrás attribútummal kell rendelkeznie.
    
    A metaverse-ben feltöltött UserType sikeres **előzetes** verziója jól jelzi, hogy helyesen konfigurálta a szinkronizálási szabályokat. Az **előzetes** verzióval kapcsolatos információkért tekintse meg a [módosítás ellenőrzése](#verify-the-change)című szakaszt.

4. **Teljes szinkronizálás** futtatása a helyszíni **ad-összekötőn**:

   1. Kattintson a jobb gombbal a helyszíni **ad-összekötőre** , és válassza a **Futtatás** lehetőséget.
   2. Az előugró párbeszédpanelen válassza a **teljes szinkronizálás** lehetőséget, majd kattintson **az OK** gombra.
   3. Várjon, amíg a művelet befejeződik.

5. Az Azure AD-re **irányuló függőben lévő exportálás** ellenőrzése:

   1. Kattintson a jobb gombbal az **Azure ad-összekötőre** , és válassza az **összekötő terület keresése** lehetőséget.
   2. A **keresési összekötő területének** felugró ablaka párbeszédpanelen:

      - **Hatókör** beállítása **függőben lévő exportálásra**.
      - Jelölje be mind a három jelölőnégyzetet: **Hozzáadás**, **módosítás** és **Törlés**.
      - Kattintson a **Keresés** gombra az exportálandó módosításokat tartalmazó objektumok listájának lekéréséhez. Egy adott objektum változásainak vizsgálatához kattintson duplán az objektumra.
      - Ellenőrizze, hogy a módosítások várhatóak-e.

6. **Exportálás** futtatása az **Azure ad-összekötőn**:

   1. Kattintson a jobb gombbal az **Azure ad-összekötőre** , és válassza a **Futtatás** lehetőséget.
   2. Az összekötő előugró ablak **futtatása** párbeszédpanelen válassza az **Exportálás** lehetőséget, majd kattintson **az OK** gombra.
   3. Várjon, amíg befejeződik az Exportálás az Azure AD-be.

> [!NOTE]
> Ezek a lépések nem tartalmazzák a teljes szinkronizálási és exportálási lépéseket az Azure AD-összekötőn. Ezek a lépések nem szükségesek, mert az attribútumok csak a helyszíni Active Directoryról az Azure AD-re áramlanak.

### <a name="step-7-re-enable-the-sync-scheduler"></a>7. lépés: a szinkronizálási ütemező újbóli engedélyezése
Engedélyezze újra a beépített szinkronizálási ütemező funkciót:

1. Indítsa el a PowerShell-munkamenetet.
2. Engedélyezze újra az ütemezett szinkronizálást a parancsmag futtatásával `Set-ADSyncScheduler -SyncCycleEnabled $true` .


## <a name="next-steps"></a>Következő lépések
* További információ a konfigurációs modellről a [deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md)című cikkből.
* További információ a kifejezés nyelvéről a [deklaratív kiépítési kifejezések ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)című cikkből.

**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
