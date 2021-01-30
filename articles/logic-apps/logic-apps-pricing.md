---
title: Számlázási & díjszabási modelljei
description: A díjszabási és számlázási modellek Azure Logic Apps-ben való működésének áttekintése
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 0de0c5d53bd3195a24f75f4a2e65c19602e2a2b3
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99088921"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>A Azure Logic Apps díjszabása és számlázási modelljei

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) segítségével olyan automatizált integrációs munkafolyamatokat hozhat létre és futtathat, amelyek méretezhetők a felhőben. Ez a cikk azt ismerteti, hogyan működik a számlázási és díjszabási modell a Logic Apps szolgáltatás és a kapcsolódó erőforrások esetében. A díjszabással kapcsolatos további információkért lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps). A költségek tervezésével, kezelésével és figyelésével kapcsolatos további információkért lásd: [Azure Logic apps költségeinek tervezése és kezelése](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Több-bérlős díjszabás

A díjköteles használati díjszabási modell a nyilvános, "globális", több-bérlős Logic Apps szolgáltatásban futó logikai alkalmazásokra vonatkozik. Az összes sikeres és sikertelen Futtatás mérése és számlázása.

Egy lekérdezési trigger által kezdeményezett kérelem például továbbra is végrehajtásra kerül, még akkor is, ha az adott trigger ki van hagyva, és nem jön létre logikai alkalmazás munkafolyamat-példánya.

| Elemek | Description |
|-------|-------------|
| [Beépített](../connectors/apis-list.md#built-in) eseményindítók és műveletek | Natív módon futtatható a Logic Apps szolgáltatásban, és a [ **műveletek** ára](https://azure.microsoft.com/pricing/details/logic-apps/)alapján mérhető. <p><p>Például a HTTP-eseményindító és a kérelem eseményindítója beépített eseményindítók, míg a HTTP-művelet és a válasz művelet beépített művelet. Az adatműveletek, a kötegelt műveletek, a változó műveletek és a [munkafolyamat-vezérlési műveletek](../connectors/apis-list.md#control-workflow), például a hurkok, a feltételek, a kapcsoló, a párhuzamos ágak stb. is beépített műveletek. |
| [Szabványos összekötő](../connectors/apis-list.md#managed-connectors) -eseményindítók és műveletek <p><p>[Egyéni összekötő](../connectors/apis-list.md#custom) -eseményindítók és műveletek | A [standard szintű összekötő díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/)alapján mérhető. |
| [Vállalati összekötő](../connectors/apis-list.md#managed-connectors) -eseményindítók és műveletek | A [nagyvállalati összekötő díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/)alapján mérhető. A nyilvános előzetes verzióban azonban a vállalati összekötők a [ *standard szintű* összekötő díjszabásával](https://azure.microsoft.com/pricing/details/logic-apps/)mérhetők. |
| [Hurkon](logic-apps-control-flow-loops.md) belüli műveletek | Minden ciklusban futó műveletet a rendszer minden futó hurok esetében mérten méri. <p><p>Tegyük fel például, hogy rendelkezik egy "for each" ciklussal, amely egy listát feldolgozó műveleteket tartalmaz. A Logic Apps-szolgáltatás minden olyan műveletet lefuttat, amely az adott hurokban fut. ehhez szorozza meg az elemek számát a hurokban lévő műveletek számával, és hozzáadja a hurkot indító műveletet. Így a 10 listaelem kiszámítása (10 * 1) + 1, amely 11 művelet végrehajtását eredményezi. |
| Újrapróbálkozási kísérletek | A legalapvetőbb kivételek és hibák kezeléséhez beállíthatja az [újrapróbálkozási szabályzatot](logic-apps-exception-handling.md#retry-policies) az eseményindítók és a támogatott műveletek esetében. Ezeket az újrapróbálkozásokat az eredeti kéréssel együtt díjszabás alapján számítjuk fel, attól függően, hogy a trigger vagy a művelet beépített, standard vagy Enterprise típusú-e. Egy 2 Újrapróbálkozással végrehajtott művelet esetében például 3 művelet végrehajtására kerül sor. |
| [Adatmegőrzési és-tárolási felhasználás](#data-retention) | Az adatmegőrzési díj mérésével mérhető, amely a [Logic apps díjszabási oldalán](https://azure.microsoft.com/pricing/details/logic-apps/), a **díjszabás részletei** táblában található. |
|||

További információkat a következő cikkekben talál:

* [A végrehajtás és a tárterület-felhasználás metrikáinak megtekintése](plan-manage-costs.md#monitor-billing-metrics)
* [Korlátok a Azure Logic Appsban](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Nem mért

* A nem kielégíthető feltételek miatt kihagyott eseményindítók
* Olyan műveletek, amelyek nem futnak, mert a logikai alkalmazás a befejezés előtt leállt
* [Letiltott logikai alkalmazások](#disabled-apps)

### <a name="other-related-resources"></a>Egyéb kapcsolódó erőforrások

A Logic apps más kapcsolódó erőforrásokkal, például integrációs fiókokkal, helyszíni adatátjárókkal és integrációs szolgáltatási környezetekkel (ISEs) működik együtt. Az erőforrások díjszabásáról a jelen témakör későbbi részében tájékozódhat:

* [On-premises data gateway (Helyszíni adatátjáró)](#data-gateway)
* [Integrációs fiók díjszabási modellje](#integration-accounts)
* [ISE díjszabási modellje](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Tippek a használati költségek becsléséhez

A pontosabb felhasználási költségek becsléséhez tekintse át a következő tippeket:

* Vegye figyelembe a lehetséges számú üzenetet vagy eseményt, amely egy adott napon érkezik, ahelyett, hogy csak a lekérdezési időközre alapozza a számításokat.

* Ha egy esemény vagy üzenet megfelel az eseményindító feltételeinek, számos eseményindító azonnal megpróbálja beolvasni a feltételeknek megfelelő összes többi várakozási eseményt vagy üzenetet. Ez azt jelenti, hogy még akkor is, ha egy több lekérdezési időközt választ ki, az eseményindító a várakozó események vagy a munkafolyamatok elindítására jogosító üzenetek száma alapján indul el. A viselkedést követő triggerek közé tartozik a Azure Service Bus és az Azure Event hub.

  Tegyük fel például, hogy olyan triggert állít be, amely naponta ellenőrzi a végpontot. Amikor az eseményindító ellenőrzi a végpontot, és 15 olyan eseményt talál, amely megfelel a feltételeknek, az eseményindító a megfelelő munkafolyamatot 15 alkalommal futtatja és futtatja. A Logic Apps szolgáltatás a 15 munkafolyamat által végrehajtott összes műveletet elvégzi, beleértve az indító kérelmeket is.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>ISE díjszabása

A rögzített díjszabási modell az [ *integrációs szolgáltatási környezetben* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)futó logikai alkalmazásokra vonatkozik. Az ISE számlázása a [integrációs szolgáltatási környezet ár](https://azure.microsoft.com/pricing/details/logic-apps)alapján történik, amely a létrehozott [ISE szintjétől vagy *SKU* -](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) tól függ. Ez a díjszabás eltér a több-bérlős díjszabástól, mivel a fenntartott kapacitásért és a dedikált erőforrásokhoz fizet, függetlenül attól, hogy használja-e őket.

| ISE SKU | Description |
|---------|-------------|
| **Prémium** | Az alapegység kapacitása rögzített, és [a prémium SKU óradíja](https://azure.microsoft.com/pricing/details/logic-apps)alapján számítjuk fel a díjat. Ha több átviteli sebességre van szüksége, [további skálázási egységeket adhat hozzá](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) az ISE vagy azután. Az egyes skálázási egységek [óradíja nagyjából az alapegység díjszabása](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>A határértékekkel kapcsolatos információkért lásd: [az ISE korlátai a Azure Logic Appsban](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Fejlesztő** | Az alapegység rögzített kapacitással rendelkezik, és [a fejlesztői SKU óradíja](https://azure.microsoft.com/pricing/details/logic-apps)alapján számítjuk fel a díjat. Ez az SKU nem rendelkezik vertikális Felskálázási képességgel, szolgáltatói szerződéssel (SLA) vagy közzétett korlátozásokkal. Ezt az SKU-t kizárólag feltárásra, kísérletezésre, fejlesztésre és tesztelésre használja, nem éles környezetben, sem pedig a teljesítmény teszteléséhez. |
|||

### <a name="included-at-no-extra-cost"></a>Külön díj nélkül

| Elemek | Description |
|-------|-------------|
| [Beépített](../connectors/apis-list.md#built-in) eseményindítók és műveletek | Jelenítse meg az **alapvető** címkét, és futtassa a logikai alkalmazásokkal megegyező ISE-ben. |
| [Szokványos összekötők](../connectors/apis-list.md#managed-connectors) <p><p>[Vállalati összekötők](../connectors/apis-list.md#enterprise-connectors) | – Az **ISE** -címkét megjelenítő felügyelt összekötők kifejezetten úgy vannak kialakítva, hogy a helyszíni adatátjáró nélkül működjenek, és UGYANABBAN az ISE-ben fussanak, mint a Logic apps szolgáltatásban. Az ISE díjszabása annyi vállalati kapcsolatot tartalmaz, amennyit csak szeretne. <p><p>– A több-bérlős Logic Apps szolgáltatásban futó ISE-címkét nem megjelenítő összekötők. Az ISE díjszabása azonban magában foglalja az ISE-ben futó Logic Apps-alkalmazások végrehajtását. |
| [Hurkon](logic-apps-control-flow-loops.md) belüli műveletek | Az ISE díjszabása minden olyan műveletet magában foglal, amely egy ciklusban fut minden futó ciklusban. <p><p>Tegyük fel például, hogy rendelkezik egy "for each" ciklussal, amely egy listát feldolgozó műveleteket tartalmaz. A művelet-végrehajtások teljes számának lekéréséhez szorozza meg a listaelemek számát a hurokban lévő műveletek számával, majd adja hozzá a hurkot indító műveletet. Így a 10 listaelem kiszámítása (10 * 1) + 1, amely 11 művelet végrehajtását eredményezi. |
| Újrapróbálkozási kísérletek | A legalapvetőbb kivételek és hibák kezeléséhez beállíthatja az [újrapróbálkozási szabályzatot](logic-apps-exception-handling.md#retry-policies) az eseményindítók és a támogatott műveletek esetében. Az ISE díjszabása az eredeti kérelemmel együtt újrapróbálkozásokat is tartalmaz. |
| [Adatmegőrzési és-tárolási felhasználás](#data-retention) | Az ISE-beli logikai alkalmazások nem foglalnak magukban megőrzési és tárolási költségeket. |
| [Integrációs fiókok](#integration-accounts) | Az ISE SKU-ra épülő, egyetlen integrációs fiókra vonatkozó szintet tartalmaz, külön díj nélkül. |
|||

A határértékekkel kapcsolatos információkért lásd: [az ISE korlátai a Azure Logic Appsban](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integrációs fiókok

Az [integrációs fiók](../logic-apps/logic-apps-pricing.md#integration-accounts) egy különálló erőforrás, amelyet Ön hoz létre és csatol a Logic apps szolgáltatáshoz, így az [EDI](logic-apps-enterprise-integration-b2b.md) -és [XML-feldolgozási](logic-apps-enterprise-integration-xml.md) képességeket használó B2B-integrációs megoldások megismeréséhez, összeállításához és teszteléséhez. Azure Logic Apps az alábbi integrációs fiókok szintjeit vagy rétegeit kínálja:

| Szint | Description |
|------|-------------|
| **Basic** | Olyan forgatókönyvek esetében, amelyekben csak az üzenetek kezelésére van szükség, vagy olyan kis üzleti partnernek kell működnie, amely egy nagyobb üzleti egységgel rendelkező kereskedelmi partneri kapcsolattal rendelkezik. <p><p>A Logic Apps SLA támogatja. |
| **Standard** | Olyan forgatókönyvek esetén, ahol összetettebb VÁLLALATKÖZI kapcsolatok és nagyobb számú entitást kell kezelni. <p><p>A Logic Apps SLA támogatja. |
| **Ingyenes** | Feltáró forgatókönyvek esetén nem éles környezetben. Ez a keret korlátozza a régió rendelkezésre állását, az átviteli sebességet és a használatot. Az ingyenes szintet például csak az Azure nyilvános régiói, például az USA nyugati régiója vagy Délkelet-Ázsia, de nem az [Azure China 21Vianet](/azure/china/overview-operations) vagy a [Azure Government](../azure-government/documentation-government-welcome.md)számára is elérhető. <p><p>**Megjegyzés**: a Logic apps SLA nem támogatja. |
|||

További információ az integrációs fiók korlátairól: [a Azure Logic apps korlátai és konfigurálása](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), például:

* [Az integrációs fiókokra vonatkozó korlátozások Azure-előfizetések esetében](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Egy integrációs fiókra vonatkozó különböző összetevők korlátai](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Az összetevők közé tartoznak a kereskedelmi partnerek, a szerződések, a térképek, a sémák, a szerelvények, a tanúsítványok, a Batch-konfigurációk stb.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Integrációs fiókok a felhasználáson alapuló logikai alkalmazásokhoz

Az integrációs fiókok számlázása rögzített [integrációs fiók díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/) alapján történik, amely a használt fiók szintjétől függ.

### <a name="ise-based-logic-apps"></a>ISE-alapú logikai alkalmazások

Az ISE többletköltség nélkül egyetlen integrációs fiókot tartalmaz, amely az ISE SKU-ra épül. Többletköltség esetén több integrációs fiókot is létrehozhat az ISE számára, hogy az az [ISE teljes korlátját](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)használja. További információ a jelen témakör korábbi, [ISE díjszabási modelljéről](#fixed-pricing) .

| ISE SKU | Belefoglalt integrációs fiók | További díjak |
|---------|------------------------------|-----------------|
| **Prémium** | Egyetlen [standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiók | Akár 19 további szabványos fiók. Ingyenes vagy alapszintű fiókok használata nem engedélyezett. |
| **Fejlesztő** | Egyetlen [ingyenes](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiók | Akár 19 további szabványos fiók, ha már rendelkezik ingyenes fiókkal, vagy 20 teljes standard fiókot, ha nem rendelkezik ingyenes fiókkal. Nem engedélyezett alapszintű fiók. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Adatmegőrzési és-tárolási felhasználás

A logikai alkalmazás futtatási előzményeinek összes bemenete és kimenete az alkalmazás [futtatási időtartama és az előzmények megőrzési időtartama](logic-apps-limits-and-config.md#run-duration-retention-limits)alapján kerül tárolásra és mérésre.

* A több-bérlős Logic Apps szolgáltatásban található Logic apps esetén a tárterület-használatot rögzített áron kell kiszámlázni, amelyet a **díjszabási adatok** táblázata alatt talál a [Logic apps díjszabási oldalán](https://azure.microsoft.com/pricing/details/logic-apps).

* A ISEs logikai alkalmazásai esetében a tárterület-felhasználás nem jár adatmegőrzési költségekkel.

A tárterület-használat felhasználásának figyeléséhez lásd: [mérőszámok megtekintése a végrehajtásokhoz és a tárhelyek felhasználásához](plan-manage-costs.md#monitor-billing-metrics).

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Helyszíni adatátjáró

[A helyszíni adatátjáró](../logic-apps/logic-apps-gateway-install.md) egy különálló erőforrás, amelyet úgy hozhat létre, hogy a logikai alkalmazások hozzáférjenek a helyszíni adataihoz adott átjáró által támogatott összekötők használatával. Az átjárón keresztül futó összekötők a költségekkel járnak, de maga az átjáró nem számít fel díjat.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Letiltott logikai alkalmazások

A letiltott logikai alkalmazások nem számítanak fel díjat, mert nem tudnak új példányokat létrehozni, miközben le vannak tiltva. A logikai alkalmazások letiltása után a jelenleg futó példányok eltarthat egy ideig, mielőtt teljesen leállnak.

## <a name="next-steps"></a>Következő lépések

* [A Azure Logic Apps költségeinek megtervezése és kezelése](plan-manage-costs.md)
