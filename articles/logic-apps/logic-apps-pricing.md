---
title: Számlázási & díjszabási modellek
description: A díjszabási és számlázási modellek Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a3c20dd85c94c359259cf69e25bb9083d56857fc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777148"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Díjszabási és számlázási modellek Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) segítségével olyan automatizált integrációs munkafolyamatokat hozhat létre és futtathat, amelyek skálázhatóak a felhőben. Ez a cikk azt ismerteti, hogyan működnek a számlázási és díjszabási modellek Logic Apps szolgáltatás és a kapcsolódó erőforrások esetében. Konkrét díjszabásért tekintse meg a [díjszabást Logic Apps.](https://azure.microsoft.com/pricing/details/logic-apps) A költségek tervezésével, kezelésével és monitorzható kezelésével kapcsolatos további információkért lásd: A költségek megtervése és [Azure Logic Apps.](plan-manage-costs.md)

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Több-bérlős díjszabás

A használat alapján fizetett díjszabási modell a nyilvános, "globális" és több-bérlős szolgáltatásban futó logikai alkalmazásokra Logic Apps vonatkozik. A sikeres és sikertelen futtatásokat a program számláz és számláz.

Például a lekérdezéses eseményindító által indított kéréseket a rendszer akkor is végrehajtásként használja, ha kihagyja az eseményindítót, és nem jön létre logikaialkalmazás-munkafolyamat-példány.

| Elemek | Description |
|-------|-------------|
| [Beépített eseményindítók](../connectors/built-in.md) és műveletek | Futtassa natív módon a Logic Apps szolgáltatásban, és a rendszer a [ **Műveletek árával használja a mérést.**](https://azure.microsoft.com/pricing/details/logic-apps/) <p><p>A HTTP-eseményindító és a Kérés eseményindító például beépített eseményindítók, a HTTP-művelet és a Válasz művelet pedig beépített műveletek. Az adatműveletek, kötegműveletek, változóműveletek és munkafolyamat-vezérlési műveletek [(](../connectors/built-in.md)például hurkok, feltételek, kapcsolók, párhuzamos ágak stb.) szintén beépített műveletek. |
| [Szabványos összekötő-eseményindítók](../connectors/managed.md) és műveletek <p><p>[Egyéni összekötő eseményindítói](../connectors/apis-list.md#custom-apis-and-connectors) és -műveletek | Mérés a [Standard összekötő árával.](https://azure.microsoft.com/pricing/details/logic-apps/) |
| [Vállalati összekötő eseményindítói](../connectors/managed.md) és -műveletek | Mérés az [Enterprise-összekötő árával.](https://azure.microsoft.com/pricing/details/logic-apps/) A nyilvános előzetes verzióban azonban a nagyvállalati összekötők mérése a [ *Standard* összekötő árán keresztül érhető el.](https://azure.microsoft.com/pricing/details/logic-apps/) |
| Hurkon [belüli műveletek](logic-apps-control-flow-loops.md) | A hurkban futó összes művelet mérése minden futó ciklusciklushoz meg lesz mérésre. <p><p>Tegyük fel például, hogy van egy "for each" ciklusa, amely olyan műveleteket tartalmaz, amelyek egy listát feldolgoznak. A Logic Apps a hurkban futó összes műveletet megszorozza a listaelemek számával a hurokban lévő műveletek számával, és hozzáadja a ciklust elindításakor szükséges műveletet. A 10 elemből áll lista számítása tehát (10 * 1) + 1, ami 11 műveletvégrehajtást ad vissza. |
| Újrapróbálkozási kísérletek | A legfontosabb kivételek és hibák kezeléshez újrapróbálkozási szabályzatot állíthat be [az](logic-apps-exception-handling.md#retry-policies) eseményindítókhoz és a támogatott műveletekhez. Ezek az újrakérések és az eredeti kérés díjszabása attól függ, hogy az eseményindító vagy művelet beépített, Standard vagy Enterprise típusú. Egy 2 újra megkísérelt műveletet például 3 műveletvégrehajtásért kell fizetnie. |
| [Adatmegőrzés és tárhely-felhasználás](#data-retention) | Az adatmegőrzési díjszabás alapján van megszámfejve, amelyet a díjszabási [Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)oldalon talál a **Díjszabás részletei táblázatban.** |
|||

További információkat a következő cikkekben talál:

* [A végrehajtások és a tárterület-felhasználás metrikainak megtekintése](plan-manage-costs.md#monitor-billing-metrics)
* [A Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Nincs forgalmi díjas

* Kihagyott feltételek miatt kihagyott eseményindítók
* Olyan műveletek, amelyek nem futottak, mert a logikai alkalmazás leállt a befejezés előtt
* [Letiltott logikai alkalmazások](#disabled-apps)

### <a name="other-related-resources"></a>Egyéb kapcsolódó erőforrások

A logikai alkalmazások más kapcsolódó erőforrásokkal, például integrációs fiókokkal, helyszíni adatátjárókkal és integrációs szolgáltatási környezetekkel (ISE-ket) is használnak. Ezeknek az erőforrásoknak a díjszabását a témakör későbbi részében, a következő szakaszokban olvashatja el:

* [On-premises data gateway (Helyszíni adatátjáró)](#data-gateway)
* [Integrációs fiók díjszabási modellje](#integration-accounts)
* [ISE-díjszabási modell](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Tippek a fogyasztási költségek becslésére

A pontosabb fogyasztási költségek becsléséhez tekintse át az alábbi tippeket:

* Vegye figyelembe, hogy hány üzenet vagy esemény érkezhet egy adott napon, ahelyett, hogy a számításokat csak a lekérdezési időköz alapján kellene végeznie.

* Ha egy esemény vagy üzenet megfelel az eseményindító feltételeinek, számos eseményindító azonnal megpróbálja beolvasni a feltételeknek megfelelő összes többi várakozó eseményt vagy üzenetet. Ez a viselkedés azt jelenti, hogy még ha hosszabb lekérdezési időközt is kiválaszt, az eseményindító a várakozási események vagy az indítási munkafolyamatokhoz megfelelő üzenetek száma alapján aktiválódik. Az ezt a viselkedést követő eseményindítók közé tartozik Azure Service Bus Azure Event Hub.

  Tegyük fel például, hogy olyan eseményindítót hoz létre, amely minden nap ellenőrzi a végpontot. Amikor az eseményindító ellenőrzi a végpontot, és 15, a feltételeknek megfelelő eseményt talál, az eseményindító aktiválódik, és 15 alkalommal futtatja a megfelelő munkafolyamatot. A Logic Apps szolgáltatás a 15 munkafolyamat által végrehajtani minden műveletet, beleértve az eseményindító-kérelmeket is.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>ISE-díjszabás

A rögzített díjszabási modell az integrációs szolgáltatási környezetben [  (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)futó logikai alkalmazásokra vonatkozik. Az ISE-t a integrációs szolgáltatási környezet [alapján](https://azure.microsoft.com/pricing/details/logic-apps)számlázjuk, amely a létrehozott ISE-szinttől vagy [ *termékváltozattól*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) függ. Ez a díjszabás eltér a több-bérlős díjszabástól, mivel a fenntartott kapacitásért és a dedikált erőforrásokért fizet, függetlenül attól, hogy használja-e őket.

| ISE termékváltozat | Description |
|---------|-------------|
| **Prémium** | Az alapegység rögzített [kapacitással rendelkezik,](logic-apps-limits-and-config.md#integration-service-environment-ise) és a prémium termékváltozatért óradíjat [kell fizetni.](https://azure.microsoft.com/pricing/details/logic-apps) Ha nagyobb átviteli sebességre van szüksége, további skálázható egységeket adhat [hozzá](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) az ISE létrehozásakor vagy azt követően. Az egyes skálázható egységek számlázása óránkénti díj alapján történik, amely nagyjából feleannyi [alapegységnek .](https://azure.microsoft.com/pricing/details/logic-apps) <p><p>Kapacitással és korlátozásokkal kapcsolatos információkért lásd: [ISE limits in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Fejlesztő** | Az alapegység rögzített [kapacitással rendelkezik,](logic-apps-limits-and-config.md#integration-service-environment-ise) és a fejlesztői termékváltozatért óradíjat [kell fizetni.](https://azure.microsoft.com/pricing/details/logic-apps) Ez a termékváltozat azonban nem rendelkezik szolgáltatásiszint-szerződéssel (SLA), skálázható felskálával vagy redundanciával az újrahasznosítás során, ami azt jelenti, hogy késést vagy állásidőt tapasztalhat. A háttérfrissítések időnként megszakítják a szolgáltatást. <p><p>**Fontos:** Ügyeljen arra, hogy ezt a termékváltozatot csak feltárásra, kísérletekre, fejlesztésre és tesztelésre használja, éles környezetben vagy teljesítményt tesztelve. <p><p>Kapacitással és korlátozásokkal kapcsolatos információkért lásd: [ISE limits in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Többletköltség nélkül tartalmazza

| Elemek | Description |
|-------|-------------|
| [Beépített eseményindítók](../connectors/built-in.md) és műveletek | Jelenítse meg **a Core** címkét, és futtassa ugyanazt az ISE-t, mint a logikai alkalmazásokat. |
| [Szokványos összekötők](../connectors/managed.md) <p><p>[Vállalati összekötők](../connectors/managed.md#enterprise-connectors) | – Az **ISE** címkét megjelenítő felügyelt összekötők kifejezetten helyszíni adatátjáró nélkül működnek, és a logikai alkalmazásokkal azonos ISE-környezetben futnak. Az ISE díjszabása annyi vállalati kapcsolatot tartalmaz, amennyit csak szeretne. <p><p>– Az ISE címkét nem megjelenítő összekötők a több-bérlős Logic Apps futnak. Az ISE díjszabása azonban magában foglalja ezeket a végrehajtásokat az ISE környezetben futó logikai alkalmazások esetében. |
| Hurkon [belüli műveletek](logic-apps-control-flow-loops.md) | Az ISE díjszabása minden olyan műveletet tartalmaz, amely hurokban fut minden futó hurokciklushoz. <p><p>Tegyük fel például, hogy van egy "for each" ciklusa, amely olyan műveleteket tartalmaz, amelyek egy listát feldolgoznak. A műveletvégrehajtások teljes számának leához szorozza meg a listaelemek számát a hurokban lévő műveletek számával, majd adja hozzá a ciklust elindításakor szükséges műveletet. A 10 elemből áll lista számítása tehát (10 * 1) + 1, ami 11 műveletvégrehajtást ad vissza. |
| Újrapróbálkozási kísérletek | A legfontosabb kivételek és hibák kezeléshez újrapróbálkozási szabályzatot állíthat be [az](logic-apps-exception-handling.md#retry-policies) eseményindítókhoz és a támogatott műveletekhez. Az ISE díjszabása magában foglalja az újrakéréseket az eredeti kéréssel együtt. |
| [Adatmegőrzés és tárhely-felhasználás](#data-retention) | Az ISE-beli logikai alkalmazások nem jár megőrzési és tárolási költségekkel. |
| [Integrációs fiókok](#integration-accounts) | Használatot tartalmaz egyetlen integrációs fiókszinthez is, az ISE termékváltozat alapján, többletköltség nélkül. |
|||

A korlátozásokkal kapcsolatos információkért lásd az [ISE-korlátokat a Azure Logic Apps.](logic-apps-limits-and-config.md#integration-service-environment-ise)

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integrációs fiókok

Az [integrációs](../logic-apps/logic-apps-pricing.md#integration-accounts) fiók egy különálló erőforrás, amely logikai alkalmazásokra hivatkozik, így feltárhatja, létrehozhatja és tesztelheti az [EDI-](logic-apps-enterprise-integration-b2b.md) és XML-feldolgozási képességeket használni képes B2B integrációs [megoldásokat.](logic-apps-enterprise-integration-xml.md)

Azure Logic Apps a díjszabási és számlázási modellben [](https://azure.microsoft.com/pricing/details/logic-apps/) eltérő integrációs [](logic-apps-pricing.md#integration-accounts)fiókszinteket vagy szinteket kínálja attól függően, hogy a logikai alkalmazások használatalapúak vagy ISE-alapúak:

| Szint | Description |
|------|-------------|
| **Basic** | Olyan forgatókönyvek esetén, ahol csak az üzenetek kezelését szeretné kezelni, vagy olyan kis üzleti partnerként szeretne viselkedni, amely kereskedelmi partnerkapcsolattal rendelkezik egy nagyobb üzleti entitással. <p><p>Az sla Logic Apps támogatja. |
| **Standard** | Olyan forgatókönyvek esetén, ahol összetettebb B2B-kapcsolatok vannak, és több entitást kell kezelnie. <p><p>Az sla Logic Apps támogatja. |
| **Ingyenes** | Feltáró jellegű forgatókönyvekhez, nem éles forgatókönyvekhez. Ez a szint korlátozza a régiók rendelkezésre állását, átviteli sebességét és használatát. Az Ingyenes szint például csak az Azure nyilvános régióiban érhető el, például az USA nyugati régiójában vagy [Délkelet-Ázsiában,](/azure/china/overview-operations) de nem érhető el az Azure China 21Vianet vagy [Azure Government.](../azure-government/documentation-government-welcome.md) <p><p>**Megjegyzés:** Az SLA nem Logic Apps támogatja. |
|||

További információ az integrációs fiókok korlátairól: [A](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)Azure Logic Apps korlátai és konfigurációja, például:

* [Integrációs fiókok korlátai Azure-előfizetésenként](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Különböző összetevőkre vonatkozó korlátozások integrációs fiókonként.](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) Az összetevők közé tartoznak a kereskedelmi partnerek, a szerződések, a térképek, a sémák, a szerelvények, a tanúsítványok, a kötegkonfigurációk stb.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Integrációs fiókok használatalapú logikai alkalmazásokhoz

Az integrációs fiókok számlázása rögzített [integrációs](https://azure.microsoft.com/pricing/details/logic-apps/) fiókár alapján, a használt fiókszinten alapul.

### <a name="ise-based-logic-apps"></a>ISE-alapú logikai alkalmazások

Az ISE külön költség nélkül tartalmaz egyetlen integrációs fiókot az ISE termékváltozat alapján. További költségekért további integrációs fiókokat hozhat létre az ISE számára, amelyek a teljes [ISE-korlátig használhatók.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) A témakör [korábbi, ISE díjszabási](#fixed-pricing) modelljéről itt olvashat bővebben.

| ISE termékváltozat | Tartalmazott integrációs fiók | További költségek |
|---------|------------------------------|-----------------|
| **Prémium** | Egyetlen [Standard integrációs](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) fiók | Legfeljebb 19 további Standard fiók. Ingyenes vagy alapszintű fiókok nem engedélyezettek. |
| **Fejlesztő** | Egyetlen [ingyenes integrációs](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) fiók | Ha már rendelkezik ingyenes fiókkal, legfeljebb 19 Standard fiókot, vagy ha még nem rendelkezik ingyenes fiókkal, összesen 20 Standard-fiókkal. Alapszintű fiókok nem engedélyezettek. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Adatmegőrzés és tárhely-felhasználás

A logikai alkalmazás futtatáselőzményeibemenetek és -kimenetek tárolása és mérése az alkalmazás futási időtartama és az előzmények megőrzési időtartama alapján [() lesz tárolva és felmérőzve.](logic-apps-limits-and-config.md#run-duration-retention-limits)

* A több-bérlős Logic Apps szolgáltatás logikai alkalmazásai esetében a tárterület-használat rögzített áron van kiszámlázva, amelyet [az Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps)díjszabási oldalán talál a Díjszabás **részletei** táblázatban.

* Az ISE-kben a logikai alkalmazások esetében a tárterület-használat nem jár adatmegőrzési költségekkel.

A tárterület-használat monitorhasználatának figyelése: [A végrehajtások metrikainak és a tárhelyhasználatnak a megtekintése.](plan-manage-costs.md#monitor-billing-metrics)

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Helyszíni adatátjáró

A [helyszíni adatátjáró](../logic-apps/logic-apps-gateway-install.md) egy külön erőforrás, amelyet ön hoz létre, így a logikai alkalmazások adott átjáró által támogatott összekötők használatával férhetnek hozzá a helyszíni adatokhoz. Az átjárón keresztül futó összekötőműveletekkel kapcsolatban díjat kell fizetni, de maga az átjáró nem számít fel költségeket.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Letiltott logikai alkalmazások

A letiltott logikai alkalmazások nem díj ellenében hozhatók létre új példányok, amíg le vannak tiltva. A logikai alkalmazás letiltása után a jelenleg futó példányok teljesen leállnak, és némi időt is eltelhet.

## <a name="next-steps"></a>Következő lépések

* [A költségek megtervzésére és Azure Logic Apps](plan-manage-costs.md)
