---
title: EDIFACT üzenetek a B2B-integrációhoz
description: Az Exchange EDIFACT-üzeneteinek összevonása EDI formátumban a VÁLLALATKÖZI vállalati integrációhoz Azure Logic Appsban Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b0df55e59bd519a816c4022f2434edfcd4460780
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006523"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>EDIFACT-üzenetek váltása nagyvállalati integráció esetében az Azure Logic Appsben az Enterprise Integration Pack csomaggal

Mielőtt EDIFACT üzeneteket Azure Logic Appshoz, létre kell hoznia egy EDIFACT-szerződést, és az integrációs fiókban tárolnia kell a szerződést. Az alábbi lépéseket követve hozhat létre EDIFACT-szerződéseket.

> [!NOTE]
> Ez az oldal a Azure Logic Apps EDIFACT funkcióit ismerteti. További információ: [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Előkészületek

Az alábbi elemek szükségesek:

* Az Azure-előfizetéshez már definiált és társított [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md)  
* Legalább két olyan [partner](logic-apps-enterprise-integration-partners.md) , amely már definiálva van az integrációs fiókban

> [!NOTE]
> A szerződések létrehozásakor a partnernek a kapott vagy küldendő üzenetekben szereplő tartalomnak meg kell egyeznie a szerződés típusával.

[Az integrációs fiók létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és a [partnerek hozzáadása](logic-apps-enterprise-integration-partners.md)után létrehozhat egy EDIFACT-szerződést a következő lépésekkel.

## <a name="create-an-edifact-agreement"></a>EDIFACT-szerződés létrehozása 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal"). 

2. Az Azure fő menüjében válassza a **minden szolgáltatás** lehetőséget. A keresőmezőbe írja be az "integráció" kifejezést, majd válassza az **integrációs fiókok** elemet.

   ![Integrációs fiók megkeresése](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Ha az **összes szolgáltatás** nem jelenik meg, lehetséges, hogy először ki kell bontania a menüt. Az összecsukott menü tetején válassza a **Szövegcímkék megjelenítése** lehetőséget.

3. Az **integrációs fiókok** területen válassza ki azt az integrációs fiókot, amelyben létre szeretné hozni a szerződést.

   ![Válassza ki az integrációs fiókot, ahol létre kívánja hozni a szerződést](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Válassza a **szerződések** lehetőséget. Ha nem rendelkezik szerződések csempével, először adja hozzá a csempét.   

   ![Válassza a "szerződések" csempét](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. A szerződések lapon válassza a **Hozzáadás** lehetőséget.

   ![Válassza a "Hozzáadás" lehetőséget.](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. A **Hozzáadás** területen adja meg a szerződés **nevét** . A **szerződés típusa** beállításnál válassza a **EDIFACT** lehetőséget. Válassza ki a **gazdagép partnerét**, a **gazdagép identitását**, a **vendég partnert** és a **vendég identitását** a szerződéshez.

   ![Szerződés részleteinek megadása](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Tulajdonság | Leírás |
   | --- | --- |
   | Név |A szerződés neve |
   | Szerződés típusa | EDIFACT kell lennie |
   | Gazda partner |Egy szerződéshez a gazdagép és a vendég partner is szükséges. A fogadó partner a szerződést konfiguráló szervezetet jelöli. |
   | Gazdagép identitása |A gazdagép-partner azonosítója |
   | Vendég partner |Egy szerződéshez a gazdagép és a vendég partner is szükséges. A vendég partner a gazda partnerrel üzleti tevékenységet folytató szervezetet jelöli. |
   | Vendég identitás |A vendég partner azonosítója |
   | Fogadási beállítások |Ezek a tulajdonságok a szerződések által fogadott összes üzenetre érvényesek. |
   | Küldési beállítások |Ezek a tulajdonságok a Szerződés által küldött összes üzenetre érvényesek. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Annak konfigurálása, hogy a szerződés hogyan kezelje a fogadott üzeneteket

Most, hogy beállította a szerződés tulajdonságait, beállíthatja, hogy a jelen Szerződés hogyan azonosítsa és kezeli a partnertől érkező bejövő üzeneteket a jelen szerződés alapján.

> [!IMPORTANT]
> Az EDIFACT-összekötő csak UTF-8 karaktereket támogat.
> Ha a kimenet váratlan karaktereket tartalmaz, ellenőrizze, hogy a EDIFACT-üzenetei az UTF-8 karakterkészletet használják-e.

1. A **Hozzáadás** területen válassza a **fogadási beállítások** lehetőséget.
Konfigurálja ezeket a tulajdonságokat a partnerrel kötött szerződése alapján, amely üzeneteket cserél Önnel. A tulajdonságok leírását a jelen szakasz tábláiban találja.

   A **fogadási beállítások** a következő csoportokba vannak rendezve: azonosítók, visszaigazolások, sémák, vezérlési számok, érvényesítés és belső beállítások.

   ![A "fogadási beállítások" konfigurálása](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Ha elkészült, győződjön meg arról, hogy az **OK gombra** kattintva menti a beállításokat.

A szerződés most már készen áll a kiválasztott beállításoknak megfelelő bejövő üzenetek kezelésére.

### <a name="identifiers"></a>Azonosítók

| Tulajdonság | Leírás |
| --- | --- |
| UNB 6.1 (címzettek hivatkozási jelszava) |Írjon be egy 1 és 14 karakter közötti alfanumerikus értéket. |
| UNB 6.2 (címzettek hivatkozási minősítője) |Írjon be egy alfanumerikus értéket legalább egy karakterrel, és legfeljebb két karakterből állhat. |

### <a name="acknowledgments"></a>Elismerések

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet fogadása (CONTRL) |Jelölje be ezt a jelölőnégyzetet, ha technikai (CONTRL) nyugtát szeretne visszaadni az adatcsere küldőjének. A rendszer elküldte a nyugtát az adatcsere küldőjének a szerződés küldési beállításai alapján. |
| Nyugtázás (CONTRL) |Jelölje be ezt a jelölőnégyzetet, ha egy funkcionális (CONTRL) visszaigazolást szeretne visszaadni az adatcsere-feladónak, és a rendszer elküldi a nyugtát az adatcsere küldőjének a szerződés küldési beállításai alapján. |

### <a name="schemas"></a>Sémák

| Tulajdonság | Leírás |
| --- | --- |
| UNH 2.1 (TÍPUS) |Válasszon egy készlettranzakció-típust. |
| UNH 2.2 (VERZIÓ) |Adja meg az üzenet verziószámát. (Minimum, egy karakter; maximum, három karakter). |
| UNH 2.3 (KIADÁS) |Adja meg az üzenet kiadásának számát. (Minimum, egy karakter; maximum, három karakter). |
| UNH 2.5 (TÁRSÍTOTT HOZZÁRENDELT KÓD) |Adja meg a hozzárendelt kódot. (Legfeljebb, hat karakter. Alfanumerikusnak kell lennie). |
| UNG 2.1 (ALKALMAZÁS FELADÓJÁNAK AZONOSÍTÓJA) |Írjon be egy alfanumerikus értéket legalább egy karakterrel, és legfeljebb 35 karaktert. |
| UNG 2.2 (ALKALMAZÁS KÜLDŐJE KÓD MINŐSÍTŐJE) |Írjon be egy alfanumerikus értéket, amely legfeljebb négy karakter hosszú lehet. |
| SÉMA |Válassza ki azt a korábban feltöltött sémát, amelyet a társított integrációs fiókból szeretne használni. |

### <a name="control-numbers"></a>Vezérlőelemek száma

| Tulajdonság | Leírás |
| --- | --- |
| Az adatcsere-vezérlési szám másodpéldányának letiltása |Ha meg szeretné tiltani az ismétlődő módosításokat, válassza ki ezt a tulajdonságot. Ha bejelöli ezt a jelölőnégyzetet, a EDIFACT dekódolása művelet ellenőrzi, hogy a fogadott adatcsere esetében (UNB5) nem egyezik-e a korábban feldolgozott adatcsere-vezérlőelem számával. Ha a rendszer egyezést észlel, akkor a rendszer nem dolgozza fel a csomópontot. |
| Ismétlődő UNB5 keresése minden (nap) |Ha úgy döntött, hogy letiltja az adatcsere-vezérlőelemek számát, megadhatja, hogy az ellenőrzés elvégzéséhez hány nap elteltével adja meg a beállítás megfelelő értékét. |
| Csoport vezérlőelem-szám másodpéldányának letiltása |A duplikált csoport-ellenőrzési számokkal (UNG5) rendelkező adatmódosítások blokkolásához válassza ezt a tulajdonságot. |
| A tranzakciónapló-vezérlők számának duplikálása nem engedélyezett |A duplikált tranzakciónapló-vezérlők számával (UNH1) való adatmódosítások blokkolásához válassza ezt a tulajdonságot. |
| EDIFACT nyugtázási vezérlő száma |Ha meg szeretné határozni a tranzakciónapló hivatkozási számait a nyugtán való használatra, adja meg az előtag értékét, a hivatkozási számok egy tartományát és egy utótagot. |

### <a name="validation"></a>Érvényesítés

Az egyes ellenőrzési sorok elvégzése után a rendszer automatikusan hozzáadja a másikat. Ha nem ad meg szabályokat, az érvényesítés az "alapértelmezett" sort használja.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet típusa |Válassza ki az EDI-üzenet típusát. |
| EDI-ellenőrzés |A séma EDI-tulajdonságai, a hossz-korlátozások, az üres adatelemek és a záró elválasztók által definiált adattípusokra vonatkozó EDI-érvényesítés végrehajtása. |
| Kiterjesztett ellenőrzés |Ha az adattípus nem EDI, az érvényesítés az adatelem követelménye és az engedélyezett ismétlődés, enumerálás és adatelem hossza ellenőrzés (min/max) alapján történik. |
| Kezdő/záró nullák engedélyezése |Tartsa meg a további kezdő vagy záró nulla és szóköz karaktereket. Ne távolítsa el ezeket a karaktereket. |
| A kezdő/záró nullák körülvágása |A kezdő vagy záró nulla és a szóköz karakter eltávolítása. |
| Záró elválasztó házirend |Záró elválasztók előállítása. <p>Válassza a **nem engedélyezett** lehetőséget a záró határolójelek és elválasztók tiltásához a fogadott adatcserében. Ha a csomópont záró határolójeleket és elválasztó karaktereket tartalmaz, a rendszer érvénytelenként deklarálja a csomópontot. <p>Válassza a **választható** lehetőséget, ha el szeretné fogadni a módosításokat a záró határolójelekkel és elválasztókkal együtt vagy anélkül. <p>Válassza a **kötelező** lehetőséget, ha a fogadott adatcsere záró határolójelekkel és elválasztókkal kell rendelkeznie. |

### <a name="internal-settings"></a>Belső beállítások

| Tulajdonság | Leírás |
| --- | --- |
| Üres XML-címkék létrehozása, ha a záró elválasztók engedélyezettek |Jelölje be ezt a jelölőnégyzetet, ha azt szeretné, hogy az adatcsere küldője üres XML-címkéket tartalmazzon a záró elválasztók számára. |
| Adatcsere felosztása tranzakciónaplóként – hiba esetén a tranzakciós készletek felfüggesztése|A rendszer az adatcsere során beállított összes tranzakciót egy külön XML-dokumentumba elemezve a megfelelő borítékot alkalmazza a tranzakciós készletre. Csak azokat a tranzakciónaplókat függessze fel, amelyek érvényesítése sikertelen. |
| Adatcsere felosztása tranzakciós készletként – hiba miatti adatcsere felfüggesztése|A megfelelő boríték alkalmazásával elemzi az egyes csomópontokon beállított összes tranzakciót egy külön XML-dokumentumba. Felfüggesztheti a teljes csomópontot, ha egy vagy több készlettranzakció nem ellenőrzi a csomópontot. | 
| Adatcsere megőrzése – tranzakciók felfüggesztése hiba esetén |Érintetlenül hagyja az adatcserét, egy XML-dokumentumot hoz létre a teljes kötegelt átváltáshoz. Csak azokat a készlettranzakciók felfüggesztését hajtsa végre, amelyeken az ellenőrzés sikertelen, miközben az összes többi tranzakció-készlet feldolgozása továbbra is fennáll. |
| Adatcsere megőrzése – az adatcsere felfüggesztése hiba esetén |Érintetlenül hagyja az adatcserét, egy XML-dokumentumot hoz létre a teljes kötegelt átváltáshoz. Felfüggesztheti a teljes csomópontot, ha egy vagy több készlettranzakció nem ellenőrzi a csomópontot. |

## <a name="configure-how-your-agreement-sends-messages"></a>Annak konfigurálása, hogy a szerződés hogyan küldjön üzeneteket

Beállíthatja, hogy a jelen Szerződés hogyan azonosítsa és kezeli a partnerek számára a jelen szerződés keretében küldött kimenő üzeneteket.

1.  A **Hozzáadás** területen válassza a **küldési beállítások** lehetőséget.
Konfigurálja ezeket a tulajdonságokat a partnerrel kötött szerződése alapján, akik üzeneteket cserélnek Önnel. A tulajdonságok leírását a jelen szakasz tábláiban találja.

    A **küldési beállítások** a következő részekre vannak rendezve: azonosítók, visszaigazolások, sémák, borítékok, karakterkészletek és elválasztók, vezérlőelem-számok és érvényesítések.

    ![A "küldési beállítások" beállítása](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Ha elkészült, győződjön meg arról, hogy az **OK gombra** kattintva menti a beállításokat.

A szerződés most már készen áll a kiválasztott beállításoknak megfelelő kimenő üzenetek kezelésére.

### <a name="identifiers"></a>Azonosítók

| Tulajdonság | Leírás |
| --- | --- |
| UNB 1.2 (szintaxis verziója) |Válasszon egy **1** és **4** közötti értéket. |
| UNB 2.3 (küldő fordított irányú útválasztási címe) |Írjon be egy alfanumerikus értéket legalább egy karakterrel, és legfeljebb 14 karakterből állhat. |
| UNB 3.3 (címzett fordított irányú útválasztási címe) |Írjon be egy alfanumerikus értéket legalább egy karakterrel, és legfeljebb 14 karakterből állhat. |
| UNB 6.1 (címzettek hivatkozási jelszava) |Adjon meg legalább egy alfanumerikus értéket, és legfeljebb 14 karakterből állhat. |
| UNB 6.2 (címzettek hivatkozási minősítője) |Írjon be egy alfanumerikus értéket legalább egy karakterrel, és legfeljebb két karakterből állhat. |
| UNB7 (alkalmazás-hivatkozási azonosító) |Írjon be egy alfanumerikus értéket legalább egy karakterrel, és legfeljebb 14 karakterből állhat. |

### <a name="acknowledgment"></a>Tudomásul vétele

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet fogadása (CONTRL) |Jelölje be ezt a jelölőnégyzetet, ha az üzemeltetett partner technikai (CONTRL) nyugtát szeretne kapni. Ez a beállítás azt határozza meg, hogy az üzenetet küldő üzemeltetett partner visszaigazolást kér a vendég partnertől. |
| Nyugtázás (CONTRL) |Jelölje be ezt a jelölőnégyzetet, ha az üzemeltetett partner funkcionális (CONTRL) nyugtát szeretne kapni. Ez a beállítás azt határozza meg, hogy az üzenetet küldő üzemeltetett partner visszaigazolást kér a vendég partnertől. |
| SG1/SG4 hurok előállítása az elfogadott tranzakciótípusok esetében |Ha a funkcionális visszaigazolás igénylését választotta, jelölje be ezt a jelölőnégyzetet, ha a SG1/SG4 hurkok létrehozását szeretné kényszeríteni az elfogadott tranzakciótípusok esetében a funkcionális CONTRL-visszaigazolásokban. |

### <a name="schemas"></a>Sémák

| Tulajdonság | Leírás |
| --- | --- |
| UNH 2.1 (TÍPUS) |Válasszon egy készlettranzakció-típust. |
| UNH 2.2 (VERZIÓ) |Adja meg az üzenet verziószámát. |
| UNH 2.3 (KIADÁS) |Adja meg az üzenet kiadásának számát. |
| SÉMA |Válassza ki a használni kívánt sémát. A sémák az integrációs fiókban találhatók. A sémák eléréséhez először csatolja az integrációs fiókot a logikai alkalmazáshoz. |

### <a name="envelopes"></a>Borítékoknak

| Tulajdonság | Leírás |
| --- | --- |
| UNB8 (prioritási kód feldolgozása) |Adjon meg egy olyan ABC-értéket, amely nem több karakternél hosszabb. |
| UNB10 (kommunikációs szerződés) |Írjon be egy alfanumerikus értéket legalább egy karakterrel, és legfeljebb 40 karaktert. |
| UNB11 (teszt kijelző) |Jelölje be ezt a jelölőnégyzetet, ha azt szeretné, hogy a létrehozott adatcsere a teszt adatai |
| Az UNA-szegmens alkalmazása (Service string-tanácsok) |Jelölje be ezt a jelölőnégyzetet, ha egy UNA-szegmenst szeretne előállítani az adatcsere elküldéséhez. |
| UNG-szegmensek alkalmazása (Function Group-fejléc) |Jelölje be ezt a jelölőnégyzetet a csoportosítási szegmensek létrehozásához a funkcionális csoport fejlécében a vendég partnernek küldött üzenetekben. A UNG-szegmensek létrehozásához a következő értékeket kell használni: <p>A **UNG1** mezőbe írjon be egy alfanumerikus értéket, amely legalább egy karakterből és legfeljebb hat karakterből állhat. <p>A **Ung 2.1** esetében adjon meg egy alfanumerikus értéket, amely legalább egy karakterből és legfeljebb 35 karakterből állhat. <p>A **Ung 2.2** esetében adjon meg egy alfanumerikus értéket, amely legfeljebb négy karakter hosszú lehet. <p>A **Ung 3.1** esetében adjon meg egy alfanumerikus értéket, amely legalább egy karakterből és legfeljebb 35 karakterből állhat. <p>A **Ung 3.2** esetében adjon meg egy alfanumerikus értéket, amely legfeljebb négy karakter hosszú lehet. <p>A **UNG6** mezőben adjon meg egy alfanumerikus értéket, amely legalább egy, legfeljebb három karakterből állhat. <p>A **Ung 7.1** esetében adjon meg egy alfanumerikus értéket, amely legalább egy karakterből és legfeljebb három karakterből állhat. <p>A **Ung 7.2** esetében adjon meg egy alfanumerikus értéket, amely legalább egy karakterből és legfeljebb három karakterből állhat. <p>A **Ung 7.3** esetében adjon meg egy alfanumerikus értéket, amely legalább 1 karakterből és legfeljebb 6 karakterből állhat. <p>A **UNG8** mezőbe írjon be egy alfanumerikus értéket, amely legalább egy karakterből és legfeljebb 14 karakterből állhat. |

### <a name="character-sets-and-separators"></a>Karakterkészletek és elválasztók

A karakterkészleten kívül más elválasztó karakterek is megadhatók az egyes üzenetek típusaihoz. Ha egy karakterkészlet nincs megadva egy adott üzenet sémához, akkor a rendszer az alapértelmezett karakterkészletet használja.

| Tulajdonság | Leírás |
| --- | --- |
| UNB 1.1 (rendszerazonosító) |Válassza ki a kimenő adatcserére alkalmazni kívánt EDIFACT karakterkészletet. |
| Séma |Válasszon ki egy sémát a legördülő listából. Az egyes sorok elvégzése után automatikusan létrejön egy új sor. A kiválasztott séma esetében válassza ki a használni kívánt elválasztó készletet az alábbi elválasztó leírások alapján. |
| Bevitel típusa |Válasszon egy bemeneti típust a legördülő listából. |
| Összetevő-elválasztó |Az összetett adatelemek elkülönítéséhez adjon meg egyetlen karaktert. |
| Adatelem-elválasztó |Az összetett adatelemeken belüli egyszerű adatelemek elkülönítéséhez adjon meg egyetlen karaktert. |
| Szegmens vége |Egy EDI-szegmens végének jelzéséhez adjon meg egy karaktert. |
| Utótag |Válassza ki a szegmens azonosítójával használt karaktert. Ha kijelöl egy utótagot, a szegmens lezáró adatelem üres is lehet. Ha a szegmens lezáró üresen marad, akkor meg kell jelölnie egy utótagot. |

### <a name="control-numbers"></a>Vezérlőelemek száma

| Tulajdonság | Leírás |
| --- | --- |
| UNB5 (Interchange Control Number) |Adjon meg egy előtagot, egy értéktartomány-tartományt és egy utótagot. Ezek az értékek a kimenő adatcsere létrehozásához használhatók. Az előtag és az utótag nem kötelező, a vezérlő számának megadása kötelező. Minden új üzenetnél nő a vezérlő száma; az előtag és az utótag változatlan marad. |
| UNG5 (csoport vezérlőelem száma) |Adjon meg egy előtagot, egy értéktartomány-tartományt és egy utótagot. Ezek az értékek a csoport vezérlőelem számának létrehozásához használhatók. Az előtag és az utótag nem kötelező, a vezérlő számának megadása kötelező. A rendszer minden új üzenetnél megnöveli a vezérlő számát, amíg el nem éri a maximális értéket. az előtag és az utótag változatlan marad. |
| UNH1 (üzenet fejlécének hivatkozási száma) |Adjon meg egy előtagot, egy értéktartomány-tartományt és egy utótagot. Ezek az értékek az üzenet fejlécének hivatkozási számának előállítására szolgálnak. Az előtag és az utótag nem kötelező, míg a hivatkozási számot kötelező megadni. Minden új üzenetnél nő a hivatkozási szám. az előtag és az utótag változatlan marad. |

### <a name="validation"></a>Érvényesítés

Az egyes ellenőrzési sorok elvégzése után a rendszer automatikusan hozzáadja a másikat. Ha nem ad meg szabályokat, az érvényesítés az "alapértelmezett" sort használja.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet típusa |Válassza ki az EDI-üzenet típusát. |
| EDI-ellenőrzés |A séma EDI-tulajdonságai, a hossz-korlátozások, az üres adatelemek és a záró elválasztók által definiált adattípusok esetében végezze el az EDI-érvényesítést. |
| Kiterjesztett ellenőrzés |Ha az adattípus nem EDI, az érvényesítés az adatelem követelménye és az engedélyezett ismétlődés, enumerálás és adatelem hossza ellenőrzés (min/max) alapján történik. |
| Kezdő/záró nullák engedélyezése |Tartsa meg a további kezdő vagy záró nulla és szóköz karaktereket. Ne távolítsa el ezeket a karaktereket. |
| A kezdő/záró nullák körülvágása |A kezdő vagy záró nulla karakterek eltávolítása. |
| Záró elválasztó házirend |Záró elválasztók előállítása. <p>Válassza a **nem engedélyezett** lehetőséget a záró elválasztók és elválasztók tiltásához az eljuttatott adatcserében. Ha a csomópont záró határolójeleket és elválasztó karaktereket tartalmaz, a rendszer érvénytelenként deklarálja a csomópontot. <p>Válassza a **választható** lehetőséget, ha az egymás utáni határolójeleket és elválasztókat is el szeretné küldeni. <p>Válassza a **kötelező** lehetőséget, ha az eljuttatott adatcsere záró határolójelekkel és elválasztókkal kell rendelkeznie. |

## <a name="find-your-created-agreement"></a>A létrehozott szerződés megkeresése

1.  Miután befejezte az összes szerződési tulajdonság beállítását, a **Hozzáadás** lapon kattintson az **OK** gombra a szerződés létrehozásához és az integrációs fiókhoz való visszatéréshez.

    Az újonnan hozzáadott szerződés most megjelenik a **szerződések** listájában.

2.  A szerződéseit az integrációs fiók áttekintésében is megtekintheti. Az integrációs fiók menüben válassza az **Áttekintés**, majd a **szerződések** csempét. 

    ![Képernyőkép, amely a szerződések csempét jeleníti meg.](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő részletes technikai részleteit, például az összekötő hencegő fájljában leírt műveleteket és korlátokat az összekötő [hivatkozási oldalán](/connectors/edifact/)találja.

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében ez az összekötő ISE által címkézett verziója az [ISE B2B-üzenetek korlátait](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)használja.

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
