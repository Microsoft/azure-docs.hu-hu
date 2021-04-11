---
title: B2B-X12 üzenetek küldése és fogadása
description: Az Exchange X12-üzenetei a B2B vállalati integrációs forgatókönyvekhez Azure Logic Apps és Enterprise Integration Pack használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 87a2bcc386ec5688fadb68aabdd2e5239e205516
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077470"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange X12-üzenetek a B2B vállalati integrációhoz Azure Logic Appsban Enterprise Integration Pack

A Azure Logic Apps X12 üzeneteinek használatához használhatja az X12-összekötőt, amely eseményindítókat és műveleteket biztosít a X12-kommunikáció kezeléséhez. További információ a EDIFACT-üzenetekről: [Exchange EDIFACT-üzenetek](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az a logikai alkalmazás, amelyből a X12-összekötőt és egy olyan triggert szeretne használni, amely elindítja a logikai alkalmazás munkafolyamatát. Az X12-összekötő csak olyan műveleteket biztosít, amelyek nem eseményindítók. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)lehetőséget.

* Az Azure-előfizetéshez társított [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely ahhoz a logikai alkalmazáshoz van társítva, ahol az X12-összekötőt használni szeretné. A logikai alkalmazásnak és az integrációs fióknak ugyanazon a helyen vagy az Azure-régióban kell lennie.

* Legalább két olyan [kereskedelmi partner](../logic-apps/logic-apps-enterprise-integration-partners.md) , amelyet már definiált az integrációs fiókjában a X12 Identity minősítő használatával.

* Az integrációs fiókhoz már hozzáadott XML-érvényesítéshez használt [sémák](../logic-apps/logic-apps-enterprise-integration-schemas.md) . Ha a HIPAA-sémákkal dolgozik, tekintse meg a [HIPAA sémákat](#hipaa-schemas).

* Az X12-összekötő használata előtt létre kell hoznia egy X12 [szerződést](../logic-apps/logic-apps-enterprise-integration-agreements.md) a kereskedelmi partnerei között, és az integrációs fiókban tárolnia kell a szerződést. Ha az egészségbiztosítási hordozhatósággal és a HIPAA-sémákkal dolgozik, hozzá kell adnia egy `schemaReferences` szakaszt a szerződéséhez. További információ: HIPAA- [sémák](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Fogadási beállítások

A szerződés tulajdonságainak beállítása után beállíthatja, hogy a jelen Szerződés hogyan azonosítsa és kezeli a partnertől kapott bejövő üzeneteket a jelen szerződés alapján.

1. A **Hozzáadás** területen válassza a **fogadási beállítások** lehetőséget.

1. Konfigurálja ezeket a tulajdonságokat a partnerrel kötött szerződése alapján, amely üzeneteket cserél Önnel. A **fogadási beállítások** a következő csoportokba vannak rendezve:

   * [Azonosítók](#inbound-identifiers)
   * [Elismervényt](#inbound-acknowledgement)
   * [Sémák](#inbound-schemas)
   * [Borítékoknak](#inbound-envelopes)
   * [Vezérlőelemek száma](#inbound-control-numbers)
   * [Ellenőrzések](#inbound-validations)
   * [Belső beállítások](#inbound-internal-settings)

   A tulajdonságok leírását a jelen szakasz tábláiban találja.

1. Ha elkészült, győződjön meg arról, hogy az **OK gombra** kattintva menti a beállításokat.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Fogadási beállítások – azonosítók

![Bejövő üzenetek azonosító tulajdonságai](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Tulajdonság | Leírás |
|----------|-------------|
| **ISA1 (engedélyezési minősítő)** | A használni kívánt engedélyezési minősítő érték. Az alapértelmezett érték **00 – nincs megadva engedélyezési információ**. <p>**Megjegyzés**: Ha más értékeket választ, adjon meg egy értéket a **ISA2** tulajdonsághoz. |
| **ISA2** | Az engedélyezési adatok értéke, amelyet akkor kell használni, ha a **ISA1** tulajdonság nem **00 – nem találhatók engedélyezési információk**. Ennek a tulajdonságnak legalább egy alfanumerikus karakterből és legfeljebb 10 értékből kell állnia. |
| **ISA3 (biztonsági minősítő)** | A használni kívánt biztonsági minősítő érték. Az alapértelmezett érték **00 – nincsenek jelen biztonsági információk**. <p>**Megjegyzés**: Ha más értékeket választ, adjon meg egy értéket a **ISA4** tulajdonsághoz. |
| **ISA4** | A **ISA3** tulajdonság értéke nem **00 –** a biztonsági információ értéke nem lehet jelen. Ennek a tulajdonságnak legalább egy alfanumerikus karakterből és legfeljebb 10 értékből kell állnia. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Fogadási beállítások – nyugtázás

![Bejövő üzenetek nyugtázása](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Tulajdonság | Leírás |
|----------|-------------|
| **Várt TA1** | Technikai nyugtázás (TA1) visszaküldése a bankközi feladónak. |
| **A FA várható** | Funkcionális nyugtázás (FA) visszaadása az adatcsere küldőjének. <p>Adja meg a 997-es vagy a 999-es beolvasást a (z) rendszerhez **a séma** verziója alapján. <p>Ha engedélyezni szeretné a AK2 hurkok létrehozását a funkcionális visszaigazolásokban az elfogadott tranzakciótípusok esetében, válassza a **AK2/IK2 hurok belefoglalása** lehetőséget. |

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Fogadási beállítások – sémák

![A bejövő üzenetek sémái](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

Ebben a szakaszban válasszon ki egy [sémát](../logic-apps/logic-apps-enterprise-integration-schemas.md) az [integrációs fiókjából](./logic-apps-enterprise-integration-create-integration-account.md) az egyes tranzakciótípusok (ST01) és a küldő alkalmazások (GS02) számára. Az EDI fogadási folyamata kibontja a beérkező üzenetet úgy, hogy az ebben a szakaszban beállított értékeket és sémát a ST01 és a GS02 értékeit a bejövő üzenetben és a beérkező üzenet sémájánál adja meg. Az egyes sorok befejezését követően automatikusan megjelenik egy új üres sor.

| Tulajdonság | Leírás |
|----------|-------------|
| **Verzió** | A séma X12 verziója |
| **Tranzakció típusa (ST01)** | A tranzakció típusa |
| **Küldő alkalmazás (GS02)** | A küldő alkalmazás |
| **Séma** | A használni kívánt sémafájl |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Fogadási beállítások – borítékok

![A bejövő üzenetek tranzakciós csoportjaiban használandó elválasztók](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Tulajdonság | Leírás |
|----------|-------------|
| **ISA11-használat** | A tranzakciónaplóban használandó elválasztó: <p>- **Standard azonosító**: használjon egy pontot (.) decimális jelölésre, nem pedig a bejövő dokumentum decimális jelölését az EDI fogadási folyamatában. <p>- **Ismétlődés-elválasztó**: megadhatja az elválasztót egy egyszerű adatelem vagy ismétlődő adatstruktúra ismétlődő előfordulása esetén. Például általában a Carat (^) van használatban ismétlődési elválasztóként. HIPAA sémák esetében csak a Carat használatát használhatja. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Fogadási beállítások – vezérlőelemek száma

![A bejövő üzenetek vezérlési számának duplikálása](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Tulajdonság | Leírás |
|----------|-------------|
| **Az adatcsere-vezérlési szám másodpéldányának letiltása** | Duplikált változások blokkolása. A beérkezett adatcsere-vezérlési számhoz tartozó Interchange Control number (ISA13) ellenőrzése. Ha a rendszer egyezést észlel, az EDI fogadási folyamata nem dolgozza fel az adatcserét. <p><p>Az ellenőrzés elvégzéséhez szükséges napok számának megadásához adjon meg egy értéket a **duplikált ISA13 minden (nap) tulajdonságának ellenőrzéséhez** . |
| **Csoport vezérlőelem-szám másodpéldányának letiltása** | Duplikált csoport vezérlőelemeket tartalmazó változások blokkolása. |
| **A tranzakciónapló-vezérlők számának duplikálása nem engedélyezett** | Duplikált tranzakciónapló-vezérlőkkel rendelkező változások blokkolása. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Fogadási beállítások – érvényesítések

![Bejövő üzenetek érvényessége](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

Az **alapértelmezett** sor az EDI-üzenetek típusához használt érvényesítési szabályokat jeleníti meg. Ha más szabályokat szeretne meghatározni, válassza ki azokat a dobozokat, amelyeken a szabályt **igaz** értékre szeretné állítani. Az egyes sorok befejezését követően automatikusan megjelenik egy új üres sor.

| Tulajdonság | Leírás |
|----------|-------------|
| **Üzenet típusa** | Az EDI-üzenet típusa |
| **EDI-ellenőrzés** | A séma EDI-tulajdonságai, a hossz-korlátozások, az üres adatelemek és a záró elválasztók által definiált adattípusokra vonatkozó EDI-érvényesítés végrehajtása. |
| **Kiterjesztett ellenőrzés** | Ha az adattípus nem EDI, az érvényesítés az adatelem követelményének és az engedélyezett ismétlődésnek, enumerálásoknak és adatelem hosszának ellenőrzése (min vagy max). |
| **Kezdő/záró nullák engedélyezése** | Tartsa meg a további kezdő vagy záró nulla és szóköz karaktereket. Ne távolítsa el ezeket a karaktereket. |
| **A kezdő/záró nullák körülvágása** | Távolítsa el a kezdő vagy záró nulla és a szóköz karaktereket. |
| **Záró elválasztó házirend** | Záró elválasztók előállítása. <p>- **Nem engedélyezett**: a záró határolójelek és elválasztók tiltása a bejövő adatcserében. Ha a csomópont záró határolójeleket és elválasztó karaktereket tartalmaz, a rendszer érvénytelenként deklarálja a csomópontot. <p>- Nem **kötelező**: a (z) és az elválasztó karakterrel rendelkező határolók és elválasztók közötti, illetve azok nélküli módosításokat fogad <p>- **Kötelező**: a bejövő adatcserének záró határolójelekkel és elválasztókkal kell rendelkeznie. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Fogadási beállítások – belső beállítások

![Belső beállítások a bejövő üzenetekhez](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Tulajdonság | Leírás |
|----------|-------------|
| **Feltételes decimális formátum (NN) konvertálása az alapszintű 10 numerikus értékre** | Alakítsa át az "nn" formátummal megadott EDI-számot egy Base-10 numerikus értékre. |
| **Üres XML-címkék létrehozása, ha a záró elválasztók engedélyezettek** | Az adatcsere feladójának üres XML-címkéket kell tartalmaznia a záró elválasztók számára. |
| **Adatcsere felosztása tranzakciónaplóként – hiba esetén a tranzakciós készletek felfüggesztése** | Elemezze az egyes csomópontokban lévő összes olyan tranzakciót, amely egy különálló XML-dokumentumba kerül, ehhez alkalmazza a megfelelő borítékot a tranzakciós készletbe. Csak azokat a tranzakciókat felfüggessze, amelyeken az ellenőrzés sikertelen. |
| **Adatcsere felosztása tranzakciós készletként – hiba miatti adatcsere felfüggesztése** | A megfelelő boríték alkalmazásával elemezheti az egyes csomópontokban lévő összes olyan tranzakciót, amely egy másik XML-dokumentumba kerül. Felfüggesztheti a teljes csomópontot, ha egy vagy több készlettranzakció nem ellenőrzi a csomópontot. |
| **Adatcsere megőrzése – tranzakciók felfüggesztése hiba esetén** | Hagyja érintetlenül a csomópontot, és hozzon létre egy XML-dokumentumot a teljes batch-adatcseréhez. Csak azokat a tranzakciókat függessze fel, amelyek sikertelenek az ellenőrzés során, de folytassa az összes többi tranzakciónapló feldolgozását. |
| **Adatcsere megőrzése – az adatcsere felfüggesztése hiba esetén** |Érintetlenül hagyja az adatcserét, egy XML-dokumentumot hoz létre a teljes kötegelt átváltáshoz. Felfüggeszti a teljes adatcsomópontot, ha egy vagy több tranzakciós készlet nem sikerül érvényesítést hajt végre. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Küldési beállítások

A szerződés tulajdonságainak beállítása után beállíthatja, hogy a jelen Szerződés hogyan azonosítsa és kezeli a partnernek a jelen szerződés keretében küldött kimenő üzeneteket.

1. A **Hozzáadás** területen válassza a **küldési beállítások** lehetőséget.

1. Konfigurálja ezeket a tulajdonságokat a partnerrel kötött szerződése alapján, amely üzeneteket cserél Önnel. A tulajdonságok leírását a jelen szakasz tábláiban találja.

   A **küldési beállítások** a következő csoportokba vannak rendezve:

   * [Azonosítók](#outbound-identifiers)
   * [Elismervényt](#outbound-acknowledgement)
   * [Sémák](#outbound-schemas)
   * [Borítékoknak](#outbound-envelopes)
   * [Vezérlő verziószáma](#outbound-control-version-number)
   * [Vezérlőelemek száma](#outbound-control-numbers)
   * [Karakterkészletek és elválasztók](#outbound-character-sets-separators)
   * [Érvényesítés](#outbound-validation)

1. Ha elkészült, győződjön meg arról, hogy az **OK gombra** kattintva menti a beállításokat.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Küldési beállítások – azonosítók

![Kimenő üzenetek azonosító tulajdonságai](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Tulajdonság | Leírás |
|----------|-------------|
| **ISA1 (engedélyezési minősítő)** | A használni kívánt engedélyezési minősítő érték. Az alapértelmezett érték **00 – nincs megadva engedélyezési információ**. <p>**Megjegyzés**: Ha más értékeket választ, adjon meg egy értéket a **ISA2** tulajdonsághoz. |
| **ISA2** | Az engedélyezési adatok értéke, amelyet akkor kell használni, ha a **ISA1** tulajdonság nem **00 – nem találhatók engedélyezési információk**. Ennek a tulajdonságnak legalább egy alfanumerikus karakterből és legfeljebb 10 értékből kell állnia. |
| **ISA3 (biztonsági minősítő)** | A használni kívánt biztonsági minősítő érték. Az alapértelmezett érték **00 – nincsenek jelen biztonsági információk**. <p>**Megjegyzés**: Ha más értékeket választ, adjon meg egy értéket a **ISA4** tulajdonsághoz. |
| **ISA4** | A **ISA3** tulajdonság értéke nem **00 –** a biztonsági információ értéke nem lehet jelen. Ennek a tulajdonságnak legalább egy alfanumerikus karakterből és legfeljebb 10 értékből kell állnia. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Küldési beállítások – nyugtázás

![Kimenő üzenetek nyugtázási tulajdonságai](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Tulajdonság | Leírás |
|----------|-------------|
| **Várt TA1** | Technikai nyugtázás (TA1) visszaküldése a bankközi feladónak. <p>Ezzel a beállítással adható meg, hogy az üzenetet küldő gazdagép partner a szerződésben szereplő vendég partnertől fogad-e nyugtát. Ezeket a nyugtákat a fogadó partner a szerződés fogadási beállításai alapján várta. |
| **A FA várható** | Funkcionális nyugtázás (FA) visszaadása az adatcsere küldőjének. **A (** z) rendszerhez a séma verziója alapján válassza ki a 997-es vagy 999-es nyugtát. <p>Ez a beállítás azt határozza meg, hogy az üzenetet küldő fogadó partner a szerződésben szereplő vendég partnertől kapott nyugtát. Ezeket a nyugtákat a fogadó partner a szerződés fogadási beállításai alapján várta. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Beállítások elküldése – sémák

![A kimenő üzenetek sémái](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

Ebben a szakaszban minden tranzakciótípus (ST01) esetében válasszon ki egy [sémát](../logic-apps/logic-apps-enterprise-integration-schemas.md) az [integrációs fiókjából](./logic-apps-enterprise-integration-create-integration-account.md) . Az egyes sorok befejezését követően automatikusan megjelenik egy új üres sor.

| Tulajdonság | Leírás |
|----------|-------------|
| **Verzió** | A séma X12 verziója |
| **Tranzakció típusa (ST01)** | A séma tranzakciós típusa |
| **Séma** | A használni kívánt sémafájl. Ha először kiválasztja a sémát, a verzió és a tranzakciótípus automatikusan be lesz állítva. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Beállítások elküldése – borítékok

![Kimenő üzenetekhez használt tranzakciótípusok elválasztói](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Tulajdonság | Leírás |
|----------|-------------|
| **ISA11-használat** | A tranzakciónaplóban használandó elválasztó: <p>- **Standard azonosító**: használjon egy pontot (.) decimális jelöléssel, nem pedig az EDI küldési folyamat kimenő dokumentumának decimális jelölését. <p>- **Ismétlődés-elválasztó**: megadhatja az elválasztót egy egyszerű adatelem vagy ismétlődő adatstruktúra ismétlődő előfordulása esetén. Például általában a Carat (^) van használatban ismétlődési elválasztóként. HIPAA sémák esetében csak a Carat használatát használhatja. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Beállítások elküldése – vezérlő verziószáma

![A kimenő üzenetek verziószámának vezérlése](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

Ebben a szakaszban válasszon ki egy [sémát](../logic-apps/logic-apps-enterprise-integration-schemas.md) az [integrációs fiókjából](./logic-apps-enterprise-integration-create-integration-account.md) az egyes adatcserékhez. Az egyes sorok befejezését követően automatikusan megjelenik egy új üres sor.

| Tulajdonság | Leírás |
|----------|-------------|
| **Vezérlő verziószáma (ISA12)** | A X12 standard verziója |
| **Használati jelző (ISA15)** | Egy olyan csomópont kontextusa, amely az **adatok, az adatok vagy** a **termelési** adatok **tesztelésére** szolgál. |
| **Séma** | A GS-és ST-szegmensek az EDI küldési folyamatba küldött X12-kódolású adatcseréhez való generálásához használandó séma. |
| **GS1** | Nem kötelező, válassza ki a funkcionális kódot. |
| **GS2** | Nem kötelező megadni az alkalmazás feladóját. |
| **GS3** | Nem kötelező megadni az alkalmazás fogadóját. |
| **GS4** | Nem kötelező, válassza a **CCYYMMDD** vagy a **ÉÉHHNN** lehetőséget. |
| **GS5** | Nem kötelező, válassza a **óópp**, a **HHMMSS** vagy a **HHMMSSdd** lehetőséget. |
| **GS7** | Nem kötelező, válassza ki a felelős Ügynökség értékét. |
| **GS8** | Nem kötelező megadni a séma dokumentumának verzióját. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Beállítások elküldése – vezérlőelemek száma

![Kimenő üzenetek vezérlési számai](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Tulajdonság | Leírás |
|----------|-------------|
| **Adatcsere-vezérlési szám (ISA13)** | Az adatcsere-vezérlő számának tartománya, amely legalább 1 értékkel és 999999999 maximális értékkel rendelkezhet. |
| **Csoport vezérlőelem száma (GS06)** | A csoport vezérlőelem számának tartománya, amelynek minimális értéke 1, maximális értéke pedig 999999999 |
| **Tranzakciónapló-vezérlő száma (ST02)** | A tranzakciónapló-vezérlő számának tartománya, amelynek minimális értéke 1, maximális értéke pedig 999999999 <p>- **Előtag**: nem kötelező, alfanumerikus érték <br>- **Utótag**: nem kötelező, alfanumerikus érték |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Beállítások küldése – karakterkészletek és elválasztók

![Üzenetek típusaihoz tartozó határolójelek a kimenő üzenetekben](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

Az **alapértelmezett** sor azt a karakterkészletet jeleníti meg, amelyet határolójelként használ egy üzenet sémához. Ha nem szeretné az **alapértelmezett** karakterkészletet használni, megadhat különböző határolójeleket az egyes üzenetek típusaihoz. Az egyes sorok befejezését követően automatikusan megjelenik egy új üres sor.

> [!TIP]
> Speciális karakteres értékek biztosításához szerkessze a szerződést JSON-ként, és adja meg a speciális karakter ASCII-értékét.

| Tulajdonság | Leírás |
|----------|-------------|
| **Használandó karakterkészlet** | A X12 karakterkészlete, amely **Alapszintű**, **bővített** vagy **UTF8** értékű. |
| **Séma** | A használni kívánt séma. A séma kiválasztása után válassza ki a használni kívánt karakterkészletet az alábbi elválasztó leírások alapján. |
| **Bemenet típusa** | A karakterkészlet bemeneti típusa |
| **Összetevő-elválasztó** | Egyetlen karakter, amely elkülöníti az összetett adatelemeket |
| **Adatelem-elválasztó** | Egyetlen karakter, amely elválasztja az egyszerű adatelemeket az összetett adatokat |
| **helyettesítő karakter elválasztója** | Helyettesítő karakter, amely felváltja a hasznos adatokban lévő összes elválasztó karaktert a kimenő X12 üzenet létrehozásakor |
| **Szegmens vége** | Egyetlen karakter, amely egy EDI-szegmens végét jelzi |
| **Utótag** | A szegmens azonosítóval használandó karakter. Ha utótagot ad meg, a szegmens lezáró adateleme üres is lehet. Ha a szegmens lezáró üresen marad, ki kell jelölnie egy utótagot. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Beállítások elküldése – ellenőrzés

![Kimenő üzenetek érvényesítési tulajdonságai](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

Az **alapértelmezett** sor az EDI-üzenetek típusához használt érvényesítési szabályokat jeleníti meg. Ha más szabályokat szeretne meghatározni, válassza ki azokat a dobozokat, amelyeken a szabályt **igaz** értékre szeretné állítani. Az egyes sorok befejezését követően automatikusan megjelenik egy új üres sor.

| Tulajdonság | Leírás |
|----------|-------------|
| **Üzenet típusa** | Az EDI-üzenet típusa |
| **EDI-ellenőrzés** | A séma EDI-tulajdonságai, a hossz-korlátozások, az üres adatelemek és a záró elválasztók által definiált adattípusokra vonatkozó EDI-érvényesítés végrehajtása. |
| **Kiterjesztett ellenőrzés** | Ha az adattípus nem EDI, az érvényesítés az adatelem követelményének és az engedélyezett ismétlődésnek, enumerálásoknak és adatelem hosszának ellenőrzése (min vagy max). |
| **Kezdő/záró nullák engedélyezése** | Tartsa meg a további kezdő vagy záró nulla és szóköz karaktereket. Ne távolítsa el ezeket a karaktereket. |
| **A kezdő/záró nullák körülvágása** | Távolítsa el a kezdő vagy záró nulla és a szóköz karaktereket. |
| **Záró elválasztó házirend** | Záró elválasztók előállítása. <p>- **Nem engedélyezett**: a kimenő adatcserében letiltja a záró határolójeleket és elválasztókat. Ha a csomópont záró határolójeleket és elválasztó karaktereket tartalmaz, a rendszer érvénytelenként deklarálja a csomópontot. <p>- Nem **kötelező**: az adatváltozásokat záró határolójelek és elválasztók nélkül is elküldheti. <p>- **Kötelező**: a kimenő adatcsere záró határolójelekkel és elválasztókkal kell rendelkeznie. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA sémák és üzenetek típusai

Ha HIPAA-sémákkal és a 277-es vagy 837-es típusú üzenettel dolgozik, néhány további lépést is végre kell hajtania. Ezeknek az [GS8 a dokumentum verziószáma](#outbound-control-version-number) 9 karakternél hosszabb (például "005010X222A1"). Emellett egyes dokumentumok verziószáma Variant típusú üzenetekre mutat. Ha nem a megfelelő típusú üzenetre hivatkozik a sémában, és a szerződésében a következő hibaüzenet jelenik meg:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

Ez a táblázat felsorolja az érintett üzenetek típusait, a különböző változatokat, valamint a dokumentum verziószámait, amelyek az adott típusú üzenetekhez vannak leképezve:

| Üzenet típusa vagy Variant |  Leírás | Dokumentum verziószáma (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Egészségügyi információk állapotáról szóló értesítés | 005010X212 |
| 837_I | Health Care-jogcím – intézményi | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Health Care jogcím – fogorvos | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Health Care jogcím Professional | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

A dokumentumok verziószámának használatakor le kell tiltania az EDI-érvényesítést is, mivel a karakteres hossz érvénytelen.

A dokumentumok verziószámának és az üzenetek típusának megadásához kövesse az alábbi lépéseket:

1. A HIPAA sémában cserélje le az aktuális üzenet típusát a használni kívánt dokumentum verziószámának Variant típusú üzenet típusára.

   Tegyük fel például, hogy `005010X222A1` az üzenet típusával szeretné használni a dokumentum verziószámát `837` . A sémában cserélje le `"X12_00501_837"` az egyes értékeket az `"X12_00501_837_P"` értékre.

   A séma frissítéséhez kövesse az alábbi lépéseket:

   1. A Azure Portal lépjen az integrációs fiókjához. Keresse meg és töltse le a sémát. Cserélje le az üzenet típusát, nevezze át a sémafájl nevet, és töltse fel az átdolgozott sémát az integrációs fiókjába. További információ: [sémák szerkesztése](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas).

   1. A szerződése üzenet-beállításaiban válassza ki a módosított sémát.

1. A szerződés `schemaReferences` objektumában adjon hozzá egy másik bejegyzést, amely meghatározza a dokumentum verziószámával egyező Variant típusú üzenetet.

   Tegyük fel például, hogy `005010X222A1` az üzenet típusához szeretné használni a dokumentum verziószámát `837` . A szerződés tartalmazza a `schemaReferences` következő tulajdonságokkal és értékekkel rendelkező szakaszt:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   Ebben a `schemaReferences` szakaszban adjon hozzá egy másik bejegyzést, amely tartalmazza ezeket az értékeket:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   Ha elkészült, a szakasz így `schemaReferences` néz ki:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. A szerződés üzenet-beállításaiban tiltsa le az EDI-érvényesítést úgy, hogy minden egyes üzenet típusa vagy az összes üzenet típusa esetében törli az **EDI-érvényesítés** jelölőnégyzetet, ha az **alapértelmezett** értékeket használja.

   ![Az összes üzenet típusának vagy az egyes üzenetek típusának érvényesítésének letiltása](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az összekötő hencegő fájljában leírt műveleteket és korlátokat az összekötő [hivatkozási oldalán](/connectors/x12/)találja.

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében ez az összekötő ISE által címkézett verziója az [ISE B2B-üzenetek korlátait](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)használja.

## <a name="next-steps"></a>Következő lépések

* További tudnivalók [a Logic apps-összekötők](../connectors/apis-list.md) használatáról
