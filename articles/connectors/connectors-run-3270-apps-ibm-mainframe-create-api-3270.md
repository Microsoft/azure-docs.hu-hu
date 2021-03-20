---
title: Kapcsolódás 3270-alkalmazásokhoz IBM-es nagyszámítógépeken
description: A 3270-es képernyő-vezérelt alkalmazások integrálása és automatizálása az Azure-ban Azure Logic Apps és IBM 3270-összekötő használatával
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 02/03/2021
tags: connectors
ms.openlocfilehash: 7873076618125241d9bdf4f225c194572553d060
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99573432"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>A 3270-es képernyő-vezérelt alkalmazások integrálása az Azure-ba a Azure Logic Apps és az IBM 3270 Connector használatával

Az Azure Logic Appsszel és az IBM 3270-összekötővel elérheti és futtathatja azokat az IBM-nagyszámítógépeken elérhető alkalmazásokat, amelyeket általában a 3270-emulátor képernyőin keresztül vezérelhet. Így az IBM-nagyszámítógépeken elérhető alkalmazásokat az Azure-, Microsoft- és egyéb alkalmazásokba, szolgáltatásokba és rendszerekbe integrálhatja, ha automatizált munkafolyamatokat hoz létre az Azure Logic Apps segítségével. Az összekötő TN3270 típusú protokoll használatával kommunikál az IBM-nagyszámítógépekkel, és minden Azure Logic Apps-régióban elérhető, az Azure Government és az Azure China 21Vianet kivételével. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

Ez a cikk az 3270-összekötő használatára vonatkozó szempontokat ismerteti: 

* Miért használja a Azure Logic Apps IBM 3270-összekötőt, és hogy az összekötő hogyan fut a 3270-es képernyő-vezérelt alkalmazások között

* Az 3270-összekötő használatának előfeltételei és beállítása

* Az 3270-összekötő műveleteinek logikai alkalmazáshoz való hozzáadásának lépései

## <a name="why-use-this-connector"></a>Miért érdemes ezt az összekötőt használni?

Az IBM-es nagyszámítógépeken futó alkalmazások eléréséhez általában egy 3270-es terminál-emulátort használ, amelyet gyakran "zöld képernyőnek" neveznek. Ez a módszer egy időben tesztelt mód, de korlátozásokkal rendelkezik. Bár a gazdagép-integrációs kiszolgáló (a) segítségével közvetlenül dolgozhat ezekkel az alkalmazásokkal, esetenként nem lehet elválasztani a képernyőt és az üzleti logikát. Vagy lehet, hogy már nem rendelkezik a gazdagép alkalmazásai működésével kapcsolatos információkkal.

Ezen forgatókönyvek kibővítéséhez az IBM 3270-es összekötő a Azure Logic Apps együttműködik a 3270 kialakítási eszközzel, amely az adott feladathoz használt gazdagép-képernyők és a "rögzítés", illetve az adott feladat számára a navigációs folyamat az adott feladat számára való továbbítására szolgál, és meghatározza a metódusokat az adott feladathoz tartozó bemeneti és kimeneti paraméterekkel. A kialakítási eszköz azokat a metaadatokba konvertálja, amelyeket az 3270-összekötő használ, amikor olyan műveletet hív meg, amely az adott feladatot a logikai alkalmazásban jelöli.

Miután létrehozta a metaadat-fájlt a tervezési eszközből, ezt a fájlt hozzáadja egy integrációs fiókhoz az Azure-ban. Így a logikai alkalmazás a 3270-es összekötő művelet hozzáadásakor elérheti az alkalmazás metaadatait. Az összekötő beolvassa a metaadat-fájlt az integrációs fiókjából, kezeli az 3270 képernyőn belüli navigálást, és dinamikusan mutatja be a 3270-összekötő műveletének paramétereit. Ezután megadhatja az adatok a gazda alkalmazásnak, és az összekötő visszaadja az eredményeket a logikai alkalmazásnak. Ily módon integrálhatja régi alkalmazásait az Azure-ba, a Microsoft-ba, valamint a Azure Logic Apps által támogatott egyéb alkalmazásokkal, szolgáltatásokkal és rendszerekkel.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ajánlott: [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Ezt a környezetet a logikai alkalmazás létrehozásához és futtatásához is kiválaszthatja. Az ISE hozzáférést biztosít a logikai alkalmazásból az Azure-beli virtuális hálózatokon belüli védelemmel ellátott erőforrásokhoz.

* Az 3270 képernyő-vezérelt alkalmazás automatizálásához és futtatásához használandó logikai alkalmazás

  Az IBM 3270-összekötő nem rendelkezik eseményindítókkal, ezért használjon egy másik eseményindítót a logikai alkalmazás elindításához, például az **Ismétlődés** eseményindítóját. Ezután hozzáadhatja a 3270-összekötő műveleteit. Első lépésként [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Ha ISE-t használ, válassza ki azt az ISE-t a logikai alkalmazás helyeként.

* [Töltse le és telepítse a 3270 kialakítási eszközt](https://aka.ms/3270-design-tool-download).
Az egyetlen előfeltétele [Microsoft .NET Framework 4,8](https://aka.ms/net-framework-download).

  Ez az eszköz segítséget nyújt a képernyők, navigációs útvonalak, metódusok és paraméterek rögzítéséhez az alkalmazásban, amelyet az 3270-es összekötő műveletként hozzáad és futtat. Az eszköz létrehoz egy Host Integration Designer XML-fájlt (HIDX), amely biztosítja a szükséges metaadatokat ahhoz, hogy az összekötő használni lehessen a mainframe-alkalmazás vezetéséhez.
  
  Az eszköz letöltése és telepítése után kövesse az alábbi lépéseket a gazdagéphez való csatlakozáshoz:

  1. Nyissa meg a 3270 kialakítási eszközét. A **munkamenet** menüben válassza a **gazdagép-munkamenetek** lehetőséget.
  
  1. Adja meg a TN3270-gazda kiszolgálójának adatait.

* [Integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), amely az a hely, ahol a HIDX-fájlt térképként tárolja, így a logikai alkalmazás elérheti a fájlban található metaadatokat és metódus-definíciókat. 

  Győződjön meg arról, hogy az integrációs fiókja az Ön által használt logikai alkalmazáshoz van társítva. Ha ISE-t használ, győződjön meg arról, hogy az integrációs fiók helye ugyanaz az ISE, amelyet a logikai alkalmazás használ.

* Hozzáférés a mainframe-alkalmazást üzemeltető TN3270-kiszolgálóhoz

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Metaadatok létrehozása – áttekintés

A 3270 képernyő-vezérelt alkalmazásokban a képernyők és az adatok mezői egyediek a forgatókönyvek számára, így a 3270-összekötőnek szüksége van az alkalmazással kapcsolatos adatokra, amelyeket metaadatokként adhat meg. Ez a metaadatok azokat a információkat ismerteti, amelyek segítségével a logikai alkalmazás azonosíthatja és felismeri a képernyőket, leírja, hogyan lehet navigálni a képernyők, a bemeneti adatok és a várt eredmények között. A metaadatok megadásához és létrehozásához használja a 3270 kialakítási eszközt, amely végigvezeti az adott *módokon* vagy fázisokon a további részletekben leírtak szerint:

* **Rögzítés**: ebben a módban rögzíti azokat a képernyőket, amelyek szükségesek egy adott feladat végrehajtásához a nagyvállalati alkalmazásban, például banki egyenleg beszerzése.

* **Navigáció**: ebben a módban megadhatja azt a tervet vagy elérési utat, amely segítségével navigálhat a mainframe-alkalmazás képernyőjén az adott feladathoz.

* **Metódusok**: ebben a módban megadhatja a metódust (például `GetBalance` ), amely a képernyő navigációs útvonalát írja le. Az egyes képernyőkön található mezőket is kiválaszthatja, amelyek a metódus Bemeneti és kimeneti paraméterei lesznek.

### <a name="unsupported-elements"></a>Nem támogatott elemek

A kialakítási eszköz nem támogatja ezeket az elemeket:

* Részleges IBM alapszintű leképezés-támogatási (BMS) térképek: Ha BMS-leképezést importál, a tervezési eszköz figyelmen kívül hagyja a részleges képernyő-definíciókat.

* Menü feldolgozása

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Képernyők rögzítése

Ebben a módban minden 3270 képernyőn megjelöl egy olyan tételt, amely egyedileg azonosítja a képernyőt. Megadhat például egy sor szöveget vagy összetettebb feltételeket, például egy adott szöveget és egy nem üres mezőt. Rögzítheti ezeket a képernyőket élő kapcsolaton keresztül a gazdagép-kiszolgálóval, vagy importálhatja ezeket az adatokat egy IBM alapszintű leképezési támogatási (BMS) térképről. Az élő kapcsolat TN3270 emulátort használ a gazdagéphez való csatlakozáshoz. Minden összekötő műveletnek egyetlen olyan feladathoz kell tartoznia, amely a munkamenethez való csatlakozással kezdődik, és a munkamenetből való leválasztással végződik.

1. Ha még nem tette meg, nyissa meg a 3270 kialakítás eszközt. Az eszköztáron válassza a **rögzítés** lehetőséget, hogy beírja a rögzítési módot.

1. A **munkamenet** menüben válassza a **kapcsolat** lehetőséget.

1. A rögzítés elindításához a **rögzítés** menüben válassza a **rögzítés indítása** lehetőséget. (Billentyűzet: CTRL + E)

1. A **rögzítés** ablaktáblán az alkalmazás első képernyőjétől kezdve lépjen be az alkalmazásba az adott feladathoz.

1. A feladat befejezése után jelentkezzen ki az alkalmazásból, ahogy azt általában elvégezte.

1. A **munkamenet** menüben válassza a **Leválasztás** lehetőséget.

1. A rögzítés leállításához a **rögzítési** menüben válassza a **rögzítés leállítása** lehetőséget. (Billentyűzet: CTRL + SHIFT + E)

   A feladatok képernyőjének rögzítése után a Designer eszköz megjeleníti a képernyőket jelképező miniatűrket. Néhány megjegyzés ezekről a bélyegképekről:

   * A rögzített képernyők esetében egy "Empty" nevű képernyő jelenik meg.

     Amikor először csatlakozik a [CICS](https://www.ibm.com/it-infrastructure/z/cics)-hoz, el kell küldenie a "Clear" kulcsot, mielőtt megadhatja a futtatni kívánt tranzakció nevét. A "Clear" kulcsot elküldő képernyő nem rendelkezik olyan *felismerési attribútumokkal*, mint például a képernyő címe, amelyet a Screen Recognition Editor használatával adhat hozzá. A képernyő megjelenítéséhez a miniatűrök tartalmaz egy "Empty" nevű képernyőt. Később ezt a képernyőt használhatja azon képernyő megjelenítéséhez, ahol a tranzakció nevét adja meg.

   * Alapértelmezés szerint a rögzített képernyő neve a képernyő első szavát használja. Ha a név már létezik, a kialakítási eszköz egy aláhúzással és egy számmal fűzi hozzá a nevet, például: "WBGB" és "WBGB_1".

1. Az alábbi lépéseket követve adjon meg egy értelmes nevet a rögzített képernyőhöz:

   1. A **gazdagép képernyőjének** ablaktábláján válassza ki az átnevezni kívánt képernyőt.

   1. Ugyanebben az ablaktáblában, a lap alján, az azonos ablaktáblán keresse meg a **képernyő neve** tulajdonságot.

   1. Módosítsa az aktuális képernyő nevét egy leíró névre.

1. Most határozza meg az egyes képernyők azonosítására szolgáló mezőket.

   Az 3270 adatfolyamban a képernyők nem rendelkeznek alapértelmezett azonosítókkal, ezért minden képernyőn egyedi szöveget kell kijelölnie. Összetett forgatókönyvek esetén több feltételt is megadhat, például az egyedi szöveget és egy adott feltételt tartalmazó mezőt.

Miután befejezte a felismerési mezők kijelölését, váltson a következő módra.

### <a name="conditions-for-identifying-repeated-screens"></a>Az ismétlődő képernyők azonosításának feltételei

Ahhoz, hogy az összekötő navigáljon és megkülönböztetse a képernyőket, általában egy olyan egyedi szöveget talál a képernyőn, amelyet azonosítóként használhat a rögzített képernyők között. Ismétlődő képernyők esetén további azonosítási módszerekre lehet szükség. Tegyük fel például, hogy két olyan képernyője van, amelyek ugyanúgy néznek ki, mint az egyik képernyő érvényes értéket ad vissza, míg a másik képernyő hibaüzenetet ad vissza.

A kialakítási eszközben hozzáadhat *felismerési attribútumokat*, például a "fiók egyenlegének lekérése" címet a képernyő-felismerési szerkesztő használatával. Ha elágazó elérési úttal rendelkezik, és mindkét ág ugyanazt a képernyőt adta vissza, de különböző eredményekkel rendelkezik, akkor más felismerési attribútumokra van szüksége. Futási időben az összekötő ezeket az attribútumokat használja az aktuális ág és elágazás meghatározásához. A következő feltételek használhatók:

* Megadott érték: ez az érték a megadott karakterlánccal egyezik meg a megadott helyen.
* NEM adott érték: ez az érték nem felel meg a megadott karakterláncnak a megadott helyen.
* Üres: Ez a mező üres.
* NEM üres: Ez a mező nem üres.

További információért lásd a jelen témakör későbbi, [példaként szolgáló navigációs tervét](#example-plan) .

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Navigációs csomagok meghatározása

Ebben a módban megadhatja a folyamatot vagy a lépéseket a mainframe-alkalmazás képernyőjén az adott feladathoz való navigáláshoz. Előfordulhat például, hogy az alkalmazásnak egynél több elérési útja van, ahol az egyik útvonal a megfelelő eredményt állítja elő, míg a másik elérési út hibát eredményez. Minden egyes képernyőn meg kell adni a következő képernyőre való áttéréshez szükséges billentyűleütéseket, például: `CICSPROD <enter>` .

> [!TIP]
> Ha több olyan feladatot dolgoz fel, amelyek ugyanazt a csatlakoztatási és leválasztási képernyőt használják, a kialakítási eszköz speciális kapcsolódási és leválasztási tervet biztosít. A csomagok meghatározásakor felveheti őket a navigációs terv elejére és végére.

### <a name="guidelines-for-plan-definitions"></a>A csomag-definíciók iránymutatásai

* Adja meg az összes képernyőt, kezdve a csatlakozással és a befejezéssel a kapcsolat bontásával.

* Létrehozhat egy önálló csomagot, vagy használhatja a kapcsolódási és leválasztási terveket, amelyek lehetővé teszik, hogy az összes tranzakcióhoz közös képernyőket használjon.

  * A csatlakozási terv utolsó képernyőjének a navigációs terv első képernyőjével megegyező képernyőnek kell lennie.

  * A leválasztási terv első képernyőjének ugyanolyan képernyőnek kell lennie, mint a navigációs terv utolsó képernyője.

* A rögzített képernyők több ismétlődő képernyőt is tartalmazhatnak, ezért a csomagban lévő ismétlődő képernyők közül csak egy példányt válasszon ki és használjon. Íme néhány példa az ismétlődő képernyőkre:

  * A bejelentkezési képernyő, például az **msg-10** képernyő
  * A CICS üdvözlő képernyője
  * A "Clear" vagy az **üres** képernyő

<a name="create-plans"></a>

### <a name="create-plans"></a>Csomagok létrehozása

1. Az 3270-es kialakítási eszköz eszköztárán válassza a **navigáció** lehetőséget a navigációs mód megadásához.

1. A terv elindításához a **navigációs** ablaktáblán válassza az **új terv** lehetőséget.

1. Az **új csomag nevének kiválasztása** területen adja meg a csomag nevét. A **típus** listából válassza ki a csomag típusát:

   | Csomag típusa | Description |
   |-----------|-------------|
   | **Folyamat** | Önálló vagy kombinált csomagok esetén |
   | **Kapcsolódás** | Csatlakozási csomagok esetén |
   | **Leválasztás** | Leválasztási csomagok esetén |
   |||

1. A **gazdagép képernyőjének** ablaktábláján húzza a rögzített miniatűröket **a navigációs panel navigációs** terv felületére.

   Az üres képernyő megjelenítéséhez, ahol a tranzakció nevét adja meg, használja az "üres" képernyőt.

1. Rendezze a képernyőket abban a sorrendben, amely leírja a definiált feladatot.

1. A képernyők, például az elágazások és az illesztések közötti folyamat elérési útjának megadásához a tervezési eszköz eszköztárán válassza a **flow** lehetőséget.

1. Válassza ki a folyamat első képernyőjét. Húzzon egy kapcsolódást a folyamat következő képernyőjére.

1. Minden egyes képernyőn adja meg a **támogatási kulcs** tulajdonság (Figyelem azonosító) és a **rögzített szöveg** tulajdonság értékét, amely a következő képernyőre helyezi át a folyamatot.

   Lehet, hogy csak a támogatási kulcs vagy a támogatási kulcs és a rögzített szöveg szerepel.

A navigációs terv befejezését követően megadhatja [a metódusokat a következő módban](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Példa

Ebben a példában tegyük fel, hogy egy "WBGB" nevű CICS-tranzakciót futtat, amely az alábbi lépéseket hajtja végre: 

* Az első képernyőn meg kell adnia egy nevet és egy fiókot.
* A második képernyőn a fiók egyenlege jelenik meg.
* Kilép az "üres" képernyőre.
* Kijelentkezés a CICS az "MSG-10" képernyőre.

Azt is tegyük fel, hogy megismétli ezeket a lépéseket, de helytelen adatbevitelt végez, így a hibát bemutató képernyő rögzíthető. Itt láthatók a rögzített képernyők:

* MSG – 10
* CICS Üdvözöljük
* Üres
* WBGB_1 (bemenet)
* WBGB_2 (hiba)
* Empty_1
* MSG – 10_1

Bár itt számos képernyő egyedi nevet kap, néhány képernyő ugyanaz a képernyő, például "MSG-10" és "Empty". Ismétlődő képernyő esetén a tervben csak egy példányt használhat a képernyőn. Az alábbi példák bemutatják, hogyan lehet egy különálló csomagot, a csatlakoztatási tervet, a leválasztási tervet, valamint egy kombinált tervet keresni:

* Önálló csomag

  ![Önálló navigációs terv](./media/connectors-create-api-3270/standalone-plan.png)

* Csomag összekötése

  ![Csomag összekötése](./media/connectors-create-api-3270/connect-plan.png)

* Csomag leválasztása

  ![Csomag leválasztása](./media/connectors-create-api-3270/disconnect-plan.png)

* Kombinált terv

  ![Kombinált terv](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Példa: ismétlődő képernyők azonosítása

Ahhoz, hogy az összekötő navigáljon és megkülönböztetse a képernyőket, általában olyan egyedi szöveget talál a képernyőn, amelyet azonosítóként használhat a rögzített képernyők között. Ismétlődő képernyők esetén további azonosítási módszerekre lehet szükség. A példa egy elágazást tartalmaz, ahol hasonló módon nézheti meg a képernyőket. Az egyik képernyő visszaadja a fiók egyenlegét, míg a másik képernyő egy hibaüzenetet ad vissza.

A kialakítási eszköz lehetővé teszi az elismerési attribútumok hozzáadását, például egy "fiók egyenlegének beolvasása" nevű képernyő-címet a képernyő-felismerési szerkesztő használatával. Hasonló képernyők esetén más attribútumokra is szükség van. Futási időben az összekötő ezeket az attribútumokat használja az ág és az elágazás meghatározásához.

* Az érvényes bemenetet visszaadó ág, amely a fiók egyenlegét adja vissza, egy "nem üres" feltételt tartalmazó mezőt adhat hozzá.

* A hibát visszaadó ágakban hozzáadhat egy "üres" feltételt tartalmazó mezőt.

<a name="define-method"></a>

## <a name="define-methods"></a>Metódusok definiálása

Ebben a módban a navigációs tervhez társított metódust kell megadnia. Minden metódus paraméternél meg kell adnia az adattípust, például karakterláncot, egész számot, dátumot vagy időpontot, és így tovább. Ha elkészült, tesztelheti a metódust az élő gazdagépen, és ellenőrizheti, hogy a metódus a várt módon működik-e. Ezután létrehozhatja a metaadat-fájlt vagy a Host Integration Designer XML-fájlt (HIDX), amely mostantól az IBM 3270-összekötőhöz tartozó művelet létrehozására és futtatására használható metódus-definíciókat tartalmaz.

1. Az 3270 kialakítási eszköz eszköztárán válassza a **metódusok** lehetőséget, hogy beírja a metódusok módot. 

1. A **navigációs** ablaktáblán válassza ki a kívánt bemeneti mezőket tartalmazó képernyőt.

1. A metódus első bemeneti paraméterének hozzáadásához kövesse az alábbi lépéseket:

   1. A **rögzítés** ablaktábla 3270 Emulator képernyőjén válassza a teljes mezőt, ne csak a mezőn belüli szöveget, amelyet az első bemenetként kíván használni.

      > [!TIP]
      > Az összes mező megjelenítéséhez és a teljes mező kiválasztásához válassza a **nézet** menü **összes mező** elemét.

   1. A kialakítási eszköz eszköztárán válassza a **beviteli mező** lehetőséget. 

   További bemeneti paraméterek hozzáadásához ismételje meg az előző lépéseket az egyes paramétereknél.

1. A metódus első kimeneti paraméterének hozzáadásához kövesse az alábbi lépéseket:

   1. A **rögzítés** ablaktábla 3270 Emulator képernyőjén válassza a teljes mezőt, ne csak a mezőn belüli szöveget, amelyet az első kimenetként kíván használni.

      > [!TIP]
      > Az összes mező megjelenítéséhez és a teljes mező kiválasztásához válassza a **nézet** menü **összes mező** elemét.

   1. A kialakítási eszköz eszköztárán válassza a **kimenet mező** elemet.

   További kimeneti paraméterek hozzáadásához ismételje meg az előző lépéseket az egyes paramétereknél.

1. Miután hozzáadta az összes metódus paraméterét, adja meg ezeket a tulajdonságokat az egyes paraméterekhez:

   | Tulajdonság neve | Lehetséges értékek | 
   |---------------|-----------------|
   | **Adattípus** | Bájt, dátum és idő, decimális, int, Long, Short, string |
   | **Mező kitöltésének technikája** | A paraméterek ezeket a kitöltési típusokat támogatják, ha szükséges, üres értékekkel töltik fel őket: <p><p>- **Típus**: írja be a karaktereket egymás után a mezőbe. <p>- **Fill (kitöltés**): cserélje le a mező tartalmát a karakterekre, és ha szükséges, üres értékeket kell kitöltenie. <p>- **EraseEofType**: törölje a mezőt, majd írja be egymás után a karaktereket a mezőbe. |
   | **Formázó sztring** | Egyes paraméterek adattípusai formázó karakterláncot használnak, amely tájékoztatja az 3270-összekötőt, és a képernyőn lévő szöveg konvertálása .NET-adattípusba: <p><p>- **Dátum** és idő: a DateTime Format sztring a [.net egyéni dátum-és időformátum-karakterláncokat](/dotnet/standard/base-types/custom-date-and-time-format-strings)követi. A dátum például a `06/30/2019` Format karakterláncot használja `MM/dd/yyyy` . <p>- **Decimális**: a decimális formátum karakterlánca a [Cobol Picture záradékot](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_73/rzasb/picture.htm)használja. A szám például `100.35` a Format karakterláncot használja `999V99` . |
   |||

## <a name="save-and-view-metadata"></a>Metaadatok mentése és megtekintése

A metódus meghatározása után, de a metódus tesztelése előtt mentse az összes megadott információt egy RAP (. rap) fájlba.
A RAP-fájlba bármikor elmentheti a fájlt, bármilyen módban. A kialakítási eszköz tartalmaz egy minta RAP-fájlt is, amelyet megnyithat és áttekintheti, ha megnyitja a tervezési eszköz telepítési mappáját ezen a helyen, és megnyitja a "WoodgroveBank. rap" fájlt:

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Ha azonban megpróbálja menteni a minta RAP-fájl módosításait, vagy egy HIDX-fájlt hoz létre a minta-RAP fájlból, miközben a fájl a tervezési eszköz telepítési mappájában marad, akkor a "hozzáférés megtagadva" hibaüzenet jelenhet meg. Alapértelmezés szerint a kialakítási eszköz a program Files mappában emelt szintű engedélyek nélkül települ. Ha hibaüzenetet kap, próbálkozzon az alábbi megoldások egyikével:

* Másolja a mintát egy másik helyre.
* Futtassa a kialakítási eszközt rendszergazdaként.
* Hozza magával az SDK-mappa tulajdonosát.

## <a name="test-your-method"></a>A metódus tesztelése

1. Ha a metódust az élő gazdagépen szeretné futtatni, miközben a metódusok mód üzemmódban van, nyomja le az F5 billentyűt, vagy a tervezési eszköz eszköztárán válassza a **teszt** elemet.

   > [!TIP]
   > Bármikor megváltoztathatja a módokat. A **fájl** menüben válassza a **mód** lehetőséget, majd válassza ki a kívánt módot.

1. Adja meg a paraméterek értékeit, majd kattintson **az OK gombra**.

1. A következő képernyőre való továbblépéshez kattintson a **tovább** gombra.

1. Ha elkészült, válassza a **kész** gombot, amely megjeleníti a kimeneti paraméterek értékeit.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>HIDX-fájl előállítása és feltöltése

Ha elkészült, a HIDX-fájl létrehozásával feltöltheti az integrációs fiókjába. Az 3270 kialakítási eszköze létrehozza a HIDX-fájlt egy új almappában, ahol mentette a RAP-fájlt.

1. Az 3270 kialakítási eszköz **eszközök** menüjében válassza a **definíciók előállítása** lehetőséget. (Billentyűzet: F6)

1. Keresse meg a RAP-fájlt tartalmazó mappát, és nyissa meg azt az almappát, amelyet az eszköz a HIDX-fájl létrehozása után hozott létre. Győződjön meg arról, hogy az eszköz létrehozta a HIDX fájlt.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és keresse meg az integrációs fiókját.

1. Adja hozzá a HIDX-fájlt térképként az integrációs fiókjához, ehhez [kövesse az alábbi hasonló lépéseket a térképek hozzáadásához](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), de ha kiválasztja a Térkép típusát, válassza a **HIDX** lehetőséget.

A témakör későbbi részében, amikor első alkalommal ad hozzá egy IBM 3270-műveletet a logikai alkalmazáshoz, a rendszer megkéri, hogy hozzon létre kapcsolatot a logikai alkalmazás és a gazdagép között a kapcsolati adatok megadásával, például az integrációs fiók és a gazdagép nevével. A kapcsolatok létrehozása után kiválaszthatja a korábban hozzáadott HIDX-fájlt, a futtatandó metódust és a használni kívánt paramétereket.

Ha befejezi ezeket a lépéseket, használhatja a logikai alkalmazásban létrehozott műveletet az IBM mainframe-hez való csatlakozáshoz, az alkalmazáshoz tartozó meghajtók képernyőjéhez, az adatbevitelhez, az eredmények visszaadásához stb. Emellett további műveleteket is hozzáadhat a logikai alkalmazáshoz más alkalmazásokkal, szolgáltatásokkal és rendszerekkel való integrációhoz.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>IBM 3270-műveletek futtatása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Az utolsó lépésben, amelyben hozzá szeretne adni egy műveletet, válassza az **új lépés** **>** **művelet hozzáadása** lehetőséget. 

1. A keresőmezőbe válassza a **vállalat** elemet. A keresőmezőbe írja be `3270` szűrőként a kifejezést. A műveletek listából válassza ki a **TN3270-kapcsolaton keresztül egy mainframe-program futtatása** nevű műveletet.

   ![3270 művelet kiválasztása](./media/connectors-create-api-3270/select-3270-action.png)

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. Ha még nem létezik kapcsolatok, adja meg a szükséges adatokat a kapcsolatban, majd válassza a **Létrehozás** lehetőséget.

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Yes | <*kapcsolattípus*> | A kapcsolatok neve |
   | **Integrációs fiók azonosítója** | Yes | <*integráció – fióknév*> | Az integrációs fiók neve |
   | **Integrációs fiók SAS URL-címe** | Yes | <*integráció-fiók-SAS-URL*> | Az integrációs fiók közös hozzáférés-aláírási (SAS) URL-címe, amelyet a Azure Portal az integrációs fiók beállításai alapján hozhatja meg. <p>1. az integrációs fiók menüjének **Beállítások** területén válassza a **visszahívási URL-cím** elemet. <br>2. a jobb oldali ablaktáblában másolja a **generált visszahívási URL** értékét. |
   | **Kiszolgáló** | Yes | <*TN3270 – kiszolgáló neve*> | A TN3270 szolgáltatás kiszolgálójának neve |
   | **Port** | No | <*TN3270 – kiszolgáló – port*> | A TN3270-kiszolgáló által használt port. Ha üresen hagyja, az összekötő `23` az alapértelmezett értéket használja. |
   | **Eszköz típusa** | No | <*IBM-terminál-Model*> | Az IBM-terminál által emulált modell neve vagy száma. Ha üresen hagyja, az összekötő az alapértelmezett értékeket használja. |
   | **Kódlap** | No | <*kód – oldal – szám*> | A gazdagéphez tartozó kódlap száma. Ha üresen hagyja, az összekötő `37` az alapértelmezett értéket használja. |
   | **Logikai egység neve** | No | <*logikai egység neve*> | A gazdagéptől kérésre megadott logikai egység neve |
   | **Engedélyezi az SSL használatát?** | No | Be vagy ki | A TLS titkosítás bekapcsolása vagy kikapcsolása. |
   | **Ellenőrzi a gazdagép SSL-tanúsítványát?** | No | Be vagy ki | Kapcsolja be vagy kapcsolja ki a kiszolgáló tanúsítványának érvényesítését. |
   ||||

   Például:

   ![Kapcsolat tulajdonságai](./media/connectors-create-api-3270/connection-properties.png)

1. Adja meg a művelethez szükséges adatokat:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Hidx neve** | Yes | <*HIDX-fájlnév*> | Válassza ki a használni kívánt 3270 HIDX-fájlt. |
   | **Metódus neve** | Yes | <*metódus – név*> | Válassza ki a használni kívánt HIDX-fájl metódusát. Miután kiválasztott egy metódust, megjelenik az **új paraméterek hozzáadása** lista, ahol kiválaszthatja a metódussal használandó paramétereket. |
   ||||

   Például:

   **HIDX-fájl kiválasztása**

   ![HIDX-fájl kiválasztása](./media/connectors-create-api-3270/select-hidx-file.png)

   **Válassza ki a metódust**

   ![Módszer kiválasztása](./media/connectors-create-api-3270/select-method.png)

   **Paraméterek kiválasztása**

   ![Paraméterek kiválasztása](./media/connectors-create-api-3270/add-parameters.png)

1. Ha elkészült, mentse és futtassa a logikai alkalmazást.

   A logikai alkalmazás futásának befejeződése után megjelennek a Futtatás lépései. 
   A sikeres lépések bemutatják a jelöléseket, míg a nem sikeres lépések az "X" betűt mutatják.

1. Az egyes lépések bemeneteit és kimeneteit az adott lépés kibontásával tekintheti át.

1. A kimenetek áttekintéséhez válassza a **nyers kimenetek megtekintése** lehetőséget.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő részletes technikai részleteiről, például az eseményindítók, a műveletek és a korlátok az összekötő hencegő fájljában leírtak alapján: az [összekötő hivatkozási lapja](/connectors/si3270/).

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében az összekötő ISE által címkézett verziója az [ISE-üzenetek korlátait](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja helyette.

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése

