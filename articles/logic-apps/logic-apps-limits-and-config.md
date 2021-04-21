---
title: Korlátozások és konfiguráció
description: Szolgáltatáskorlátok, például időtartam, átviteli sebesség és kapacitás, valamint konfigurációs értékek, például az engedélyezni szükséges IP-címek a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/16/2021
ms.openlocfilehash: 286da1412e8a74ffbf34e4abb493241914d4f925
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764872"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Információ az Azure Logic Apps korlátozásaival és konfigurálásával kapcsolatban

Ez a cikk az automatizált munkafolyamatok létrehozásának és futtatásának korlátait és konfigurációs részleteit ismerteti a Azure Logic Apps. További Power Automate [lásd: Korlátozások és](/flow/limits-and-config)konfiguráció a Power Automate.

<a name="definition-limits"></a>

## <a name="logic-app-definition-limits"></a>Logikai alkalmazás definíciós korlátai

Egyetlen logikai alkalmazás definíciójának korlátai a következőek:

| Name | Korlát | Jegyzetek |
| ---- | ----- | ----- |
| Műveletek munkafolyamatonként | 500 | A korlát kiterjesztéséhez szükség szerint beágyazott munkafolyamatokat adhat hozzá. |
| Műveletek beágyazásának engedélyezett mélysége | 8 | A korlát kiterjesztéséhez szükség szerint beágyazott munkafolyamatokat adhat hozzá. |
| Régiónkénti munkafolyamatok előfizetésenként | 1,000 | |
| Eseményindítók munkafolyamatonként | 10 | Kódnézetben dolgozva, nem a tervezőben |
| Hatókör esetkorlátja váltása | 25 | |
| Változók munkafolyamatonként | 250 | |
| A vagy a `action` neve `trigger` | 80 karakter | |
| Karakterek kifejezésenként | 8,192 | |
| Hossza `description` | 256 karakter | |
| Maximális száma `parameters` | 50 | |
| Maximális száma `outputs` | 10 | |
| A maximális mérete a `trackedProperties` | 16 000 karakter |
| Beágyazott kód művelet – A kód karakterének maximális száma | 1024 karakter | A korlát 100 000 karakterre való kiterjesztéséhez logikai alkalmazásait a Logikai alkalmazás **(előzetes verzió)** erőforrástípussal hozza létre az [Azure Portal](create-stateful-stateless-workflows-azure-portal.md) vagy a Visual Studio Code és az [Azure Logic Apps **(előzetes verzió)**](create-stateful-stateless-workflows-visual-studio-code.md)bővítmény használatával. |
| Beágyazott kód művelet – A kód futtatásának maximális időtartama | 5 másodperc | A korlát 15 másodpercre való kiterjesztéséhez logikai alkalmazásait a Logikai alkalmazás **(előzetes verzió)** erőforrástípussal hozza létre, akár az [Azure Portal,](create-stateful-stateless-workflows-azure-portal.md) akár az Visual Studio Code és az [Azure Logic Apps **(előzetes verzió) bővítmény használatával.**](create-stateful-stateless-workflows-visual-studio-code.md) |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>A futtatás időtartamára és a megőrzési előzményekre vonatkozó korlátok

Az egyes logikai alkalmazások futtatásának korlátai a következőek:

| Name | Több-bérlős korlát | Integrációs szolgáltatási környezet korlátja | Jegyzetek |
|------|--------------------|---------------------------------------|-------|
| Futtatás időtartama | 90 nap | 366 nap | A futtatás időtartamát a futtatás kezdési ideje és a Futtatás [](#change-duration) előzményeinek megőrzése napokban az adott kezdési időpontban munkafolyamat-beállításban megadott korlát alapján számítja ki a rendszer. <p><p>Az alapértelmezett korlát módosítása: A futtatás időtartamának és az [előzmények megőrzésének módosítása a tárolóban.](#change-duration) |
| Futtatáselőzmények megőrzése a tárolóban | 90 nap | 366 nap | Ha egy futtatás időtartama meghaladja az aktuális futtatáselőzmény-megőrzési korlátot, a futtatás el lesz távolítva a tároló futtatáselőzményeiből. Függetlenül attól, hogy a futtatás befejeződött vagy túllépi az időkorlátot, a futtatás előzményeinek megőrzése mindig a futtatás kezdési ideje és a munkafolyamat-beállításban (a Futtatás előzményeinek megőrzése napokban) megadott jelenlegi korlát alapján [**van kiszámítva.**](#change-retention) Az előző korláttól függetlenül a rendszer mindig az aktuális korlátot használja a megőrzés kiszámításához. <p><p>Az alapértelmezett korlát és további információért lásd: A tárterület időtartamának és [futtatáselőzmény-megőrzésének módosítása.](#change-retention) A maximális korlát növeléséhez vegye fel a kapcsolatot [Logic Apps csapatával,](mailto://logicappspm@microsoft.com) akik segítséget keresnek a követelményekkel kapcsolatban. |
| Minimális ismétlődési időköz | 1 másodperc | 1 másodperc ||
| Ismétlődési időköz maximális száma | 500 nap | 500 nap ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>A futtatás időtartamának és az előzmények megőrzésének módosítása a tárolóban

Ugyanez a beállítás szabályozza a munkafolyamat által futtatható napok maximális számát, valamint a futtatás előzményeinek tárolását. A tulajdonságok alapértelmezett vagy aktuális korlátját az alábbi lépésekkel módosíthatja.

* A több-bérlős Azure-beli logikai alkalmazások esetében a 90 napos alapértelmezett korlát megegyezik a maximális korlát értékéhez. Ezt az értéket csak csökkentheti.

* Integrációs szolgáltatási környezetben a logikai alkalmazások esetében csökkentheti vagy növelheti a 90 napos alapértelmezett korlátot.

Tegyük fel például, hogy a megőrzési korlátot 90 napról 30 napra csökkenti. A 60 napos futtatás el lesz távolítva a futtatás előzményeiből. Ha a megőrzési időszakot 30 napról 60 napra növeli, a 20 napos futtatás további 40 napig marad a futtatás előzményeiben.

> [!IMPORTANT]
> Ha egy futtatás időtartama meghaladja az aktuális futtatáselőzmény-megőrzési korlátot, a futtatás el lesz távolítva a tároló futtatáselőzményeiből. A futtatás előzményeinek elvesztésének elkerülése  érdekében győződjön meg arról, hogy a megőrzési korlát mindig nagyobb, mint a futtatás leghosszabb lehetséges időtartama.

1. A [Azure Portal](https://portal.azure.com) keresőmezőben keresse meg és válassza a **Logic Apps lehetőséget.**

1. Keresse meg és válassza ki a logikai alkalmazást. Nyissa meg a logikai alkalmazást a Logikaialkalmazás-tervezőben.

1. A logikai alkalmazás menüjében válassza a **Munkafolyamat-beállítások lehetőséget.**

1. A **Runtime options (Futtatás beállításai)** alatt a Run history retention in days (Futtatás előzményeinek megőrzése **napokban)** listából válassza az **Egyéni lehetőséget.**

1. Húzza a csúszkát a kívánt napok számának beállításhoz.

1. Ha végzett, a Munkafolyamat beállításai **eszköztáron** válassza a Mentés **lehetőséget.**

Ha létrehoz egy Azure Resource Manager-sablont a logikai alkalmazáshoz, ez a beállítás tulajdonságként jelenik meg a munkafolyamat erőforrás-definíciójában, amelyet a [Microsoft.Logic workflows sablonreferenciája ismertet:](/azure/templates/microsoft.logic/workflows)

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Egyidejűségi, hurkolási és kicsatolási korlátok

Az egyes logikai alkalmazások futtatásának korlátai a következőek:

### <a name="loops"></a>Hurkok

| Name | Korlát | Jegyzetek |
| ---- | ----- | ----- |
| Foreach tömbelemek | 100.000 | Ez a korlát a tömbelemek azon maximális számát írja le, amit egy "for each" hurok feldolgozhat. <p><p>Nagyobb tömbök szűréséhez használhatja a [lekérdezési műveletet.](logic-apps-perform-data-operations.md#filter-array-action) |
| Foreach-egyidejűség | Egyidejűség kikapcsolása: 20 <p><p>Egyidejűség a következőn: <p><p>– Alapértelmezett érték: 20 <br>- Min: 1 <br>– Maximum: 50 | Ez a korlát az egyszerre vagy párhuzamosan futtatható "for each" iterációk maximális száma. <p><p>A korlát módosítása: [Az "egyes"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) egyidejűségi korlát módosítása vagy ["Mindegyikre"](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)futtatása hurkok sorrendben. |
| Until-iterációk | - Alapértelmezett: 60 <br>- Min: 1 <br>- Maximum: 5000 | Az "Until" ciklusok maximális száma a logikai alkalmazás futtatása során. <p><p>Ennek a korlátnak a beállítását a "Until" ciklus alakzatában válassza a Korlátok módosítása lehetőséget, és adja meg a Count (Darabszám) **tulajdonság** értékét. |
| Időtúllépésig | - Alapértelmezett: PT1H (1 óra) | A "Until" ciklus futásának legnagyobb ideje a kilépés előtt, és [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)formátumban van megadva. Az időtúllépési érték minden ciklusra ki lesz értékelve. Ha a hurok bármely művelete hosszabb ideig tart, mint az időkorlát, az aktuális ciklus nem áll le. A következő ciklus azonban nem indul el, mert a korlátozási feltétel nem teljesül. <p><p>Ennek a korlátnak a beállítását a "Until" ciklus alakzatban válassza a Korlátok módosítása lehetőséget, és adja meg az **Időtúllépés tulajdonság** értékét. |
||||

<a name="concurrency-debatching"></a>

### <a name="concurrency-and-debatching"></a>Egyidejűség és visszacsatolás

| Name | Korlát | Jegyzetek |
| ---- | ----- | ----- |
| Egyidejűség aktiválása | Egyidejűség kikapcsolása: Korlátlan <p><p>Az egyidejűség engedélyezésével, amelyet az engedélyezés után nem lehet visszavonni: <p><p>- Alapértelmezett: 25 <br>- Min: 1 <br>– Maximum: 100 | Ez a korlát az egyidejűleg vagy párhuzamosan futtatható logikaialkalmazás-példányok maximális száma. <p><p>**Megjegyzés:** Ha az egyidejűség be van kapcsolva, a SplitOn korlátja 100-ra csökken a [tömbök kicsatolására.](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) <p><p>A korlát módosítása: [Eseményindító](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) egyidejűségi korlát módosítása vagy Példányok aktiválása [sorrendben.](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger) |
| Várakozó futtatás maximális | Ha az egyidejűség ki van kapcsolva: <p><p>- Min: 1 <br>– Maximum: 50 <p><p>Egyidejűség a következőn: <p><p>- Min: 10 plusz az egyidejű futtatások száma (egyidejű trigger) <br>– Maximum: 100 | Ez a korlát azon logikaialkalmazás-példányok maximális száma, amelyek várakozni tudnak, ha a logikai alkalmazás már futtatja az egyidejű példányok maximális számát. <p><p>A korlátot a [Várakozási futtatás korlát módosítása( ) alatt módosíthatja.](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs) |
| SplitOn-elemek | Egyidejűség kikapcsolása: 100 000 <p><p>Egyidejűség a következőn: 100 | A tömböt visszafejtő eseményindítókhoz megadhat egy "SplitOn" tulajdonságot használó kifejezést, amely a "Foreach" hurok használata helyett több munkafolyamat-példányra osztja vagy darabolását teszi elérhetőre. [](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) Ez a kifejezés arra a tömbre hivatkozik, amely minden tömbelemhez munkafolyamat-példány létrehozásához és futtatásához használható. <p><p>**Megjegyzés:** Ha az egyidejűség be van kapcsolva, a SplitOn korlátja 100 elemre csökken. |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Átviteli korlátok

Egyetlen logikai alkalmazás definíciójának korlátai a következőek:

### <a name="multi-tenant-logic-apps-service"></a>Több-bérlős Logic Apps szolgáltatás

| Name | Korlát | Jegyzetek |
| ---- | ----- | ----- |
| Művelet: Végrehajtások 5 perces működés közbeni időközzel | - 100 000 végrehajtás (alapértelmezett) <p><p>- 300 000 végrehajtás (maximális nagy átviteli sebességű módban)  | A logikai alkalmazás alapértelmezett korlátjának a maximális korlátra való emeléséhez lásd: [Futtatás](#run-high-throughput-mode)nagy átviteli sebességű módban, amely előzetes verzióban érhető el. Vagy szükség szerint több logikai alkalmazás között is [eloszthatja a](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) számítási feladatot. |
| Művelet: Egyidejű kimenő hívások | ~2.500 | Csökkentheti az egyidejű kérések számát, vagy szükség szerint csökkentheti az időtartamot. |
| Futásidejű végpont: Egyidejű bejövő hívások | ~1000 | Csökkentheti az egyidejű kérések számát, vagy szükség szerint csökkentheti az időtartamot. |
| Futásidejű végpont: 5 percenkénti olvasási hívások  | 60.000 | Ez a korlát azokra a hívásokra vonatkozik, amelyek a logikai alkalmazás futtatáselőzményeiből kérik le a nyers bemeneteket és kimeneteket. A számítási feladatot szükség szerint több alkalmazás között is eloszthatja. |
| Futásidejű végpont: Hívások meghívása 5 percenként | 45.000 | A számítási feladatokat szükség szerint több alkalmazás között is eloszthatja. |
| 5 percenkénti tartalom-átviteli sebesség | 600 MB | A számítási feladatokat szükség szerint több alkalmazás között is eloszthatja. |
||||

<a name="run-high-throughput-mode"></a>

#### <a name="run-in-high-throughput-mode"></a>Futtatás nagy átviteli sebességű módban

Egyetlen logikai alkalmazás definíciója esetén az 5 percenként végrehajtható műveletek száma alapértelmezett [korlát.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Ha az alapértelmezett korlátot az alapértelmezett korlátnak az alapértelmezett korlát háromszorosára szeretné növelni a logikai alkalmazásra, engedélyezheti a nagy átviteli sebességű módot, amely előzetes verzióban érhető el. [](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Vagy szükség szerint több logikai alkalmazás között is [eloszthatja a](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) számítási feladatot.

1. A Azure Portal a logikai alkalmazás menüjének Beállítások **menüjében válassza** a **Munkafolyamat-beállítások lehetőséget.**

1. A **Futásidejű beállítások Magas** átviteli sebesség alatt módosítsa a beállítást Be  >   **állásba.**

   ![Képernyőkép a logikai alkalmazás menüjéről a Azure Portal "Munkafolyamat-beállítások" és a "Nagy átviteli sebesség" beállítás "Be" beállításával.](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

Ha engedélyezni szeretné ezt a beállítást egy ARM-sablonban a logikai alkalmazás üzembe helyezéséhez, a logikai alkalmazás erőforrás-definíciójának objektumában adja hozzá az objektumot a tulajdonság `properties` `runtimeConfiguration` `operationOptions` `OptimizedForHighThroughput` beállításával:

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

A logikai alkalmazás erőforrás-definícióval kapcsolatos további információkért lásd: Áttekintés: Automatikus üzembe helyezés Azure Logic Apps [sablonokkal Azure Resource Manager használatával.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)

### <a name="integration-service-environment-ise"></a>Integrációs szolgáltatási környezet (ISE)

* [Fejlesztői ISE termékváltozat:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)Percenként legfeljebb 500 végrehajtást biztosít, de vegye figyelembe a következő szempontokat:

  * Győződjön meg arról, hogy ezt a termékváltozatot csak feltárásra, kísérletekre, fejlesztésre vagy tesztelésre használja, éles környezetben vagy teljesítményt tesztelni nem. Ez a termékváltozat nem rendelkezik szolgáltatásiszint-szerződéssel (SLA), skálázható felskálával vagy redundanciával az újrahasznosítás során, ami azt jelenti, hogy késést vagy állásidőt tapasztalhat.

  * A háttérfrissítések időnként megszakítják a szolgáltatást.

* [Prémium ISE termékváltozat:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)Az alábbi táblázat ismerteti ennek a termékváltozatnak az átviteli sebességére vonatkozó korlátokat, de a korlátok normál feldolgozás esetén történő túllépése, vagy a korlátokon túli terheléstesztelések futtatása érdekében forduljon az [Logic Apps](mailto://logicappsemail@microsoft.com) csapatához, hogy segítsenek a követelményekkel kapcsolatban.

  | Name | Korlát | Jegyzetek |
  |------|-------|-------|
  | Alapegység végrehajtási korlátja | Rendszer által szabályozás alatt, ha az infrastruktúra-kapacitás eléri a 80%-ot | Percenként 4000 műveletvégrehajtást biztosít, ami havonta ~160 millió műveletvégrehajtást biztosít |
  | Skálázásegység végrehajtási korlátja | Rendszer által szabályozás alatt, ha az infrastruktúra-kapacitás eléri a 80%-ot | Minden skálázegység ~2000 további műveletvégrehajtást biztosít percenként, ami havonta ~80 millióval több műveletvégrehajtást biztosít |
  | Felvehető méretezési egységek maximális száma | 10 | |
  ||||

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Átjáróra vonatkozó korlátok

Azure Logic Apps támogatja az átjárón keresztüli írási műveleteket, beleértve a beszúrásokat és a frissítéseket is. Ezeknek a műveleteknek azonban [korlátaik vannak a hasznosság méretére.](/data-integration/gateway/service-gateway-onprem#considerations)

<a name="http-limits"></a>

## <a name="http-limits"></a>HTTP-korlátok

Egyetlen bejövő vagy kimenő hívás korlátai a következőek:

<a name="http-timeout-limits"></a>

#### <a name="timeout-duration"></a>Időtúllépés időtartama

Egyes összekötőműveletek aszinkron hívásokat kezdeményeznek, vagy webhookkéréseket figyelnek, így ezeknek a műveleteknek az időkorlátja hosszabb lehet ezen korlátoknál. További információkért tekintse meg az adott összekötő technikai részleteit, valamint a [munkafolyamat-eseményindítókat és műveleteket.](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

| Name | Logic Apps (több-bérlős) | Logic Apps (előzetes verzió) | Integrációs szolgáltatási környezet | Jegyzetek |
|------|---------------------------|----------------------|---------------------------------|-------|
| Kimenő kérelem | 120 másodperc <br>(2 perc) | 230 másodperc <br>(3,9 perc) | 240 másodperc <br>(4 perc) | A kimenő kérések közé tartoznak például a HTTP-eseményindító vagy -művelet által indított hívások. További információ az előzetes verzióról: Azure Logic Apps [előzetes verzió.](logic-apps-overview-preview.md) <p><p>**Tipp:** Hosszabb ideig futó műveletekhez használjon [aszinkron](../logic-apps/logic-apps-create-api-app.md#async-pattern) lekérdezési mintát vagy [egy until ciklust.](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action) Ha egy másik, hívható végponttal ellátott [](logic-apps-http-endpoint.md)logikai alkalmazás hívása során időtúllépési korlátokat kell túllépni, használhatja helyette a beépített Azure Logic Apps-műveletet, amelyet az **összekötőválasztóban** találhat a Beépített alatt. |
| Bejövő kérelem | 120 másodperc <br>(2 perc) | 230 másodperc <br>(3,9 perc) | 240 másodperc <br>(4 perc) | A bejövő kérések közé tartoznak például a Kérés eseményindító által fogadott hívások, a HTTP-webhook-eseményindítók és a HTTP-webhook-művelet. További információ az előzetes verzióról: Azure Logic Apps [előzetes verzió.](logic-apps-overview-preview.md) <p><p>**Megjegyzés:** Ahhoz, hogy az eredeti hívó le tudja kapni a választ, a válasz összes lépésének a korláton belül kell befejeződnie, hacsak nem hív meg egy másik logikai alkalmazást beágyazott munkafolyamatként. További információ: Logikai alkalmazások [hívása, aktiválása vagy beágyazása.](../logic-apps/logic-apps-http-endpoint.md) |
||||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Üzenet mérete

| Name | Több-bérlős korlát | Integrációs szolgáltatási környezet korlátja | Jegyzetek |
|------|--------------------|---------------------------------------|-------|
| Üzenet mérete | 100 MB | 200 MB | A korlátot a nagy méretű üzenetek adattömbökben [való kezelését leíró(](../logic-apps/logic-apps-handle-large-messages.md)) Előfordulhat azonban, hogy egyes összekötők és API-k nem támogatják az adattömbök feldarabolását vagy akár az alapértelmezett korlátot. <p><p>– Az OLYAN összekötők, mint az AS2, az X12 és az EDIFACT, saját [B2B üzenetkorlátokkal rendelkezik.](#b2b-protocol-limits) <br>– Az ISE-összekötők az ISE-korlátot használják, nem a nem ISE-összekötőkre vonatkozó korlátokat. |
| Üzenetméret darabolással | 1 GB | 5 GB | Ez a korlát olyan műveletekre vonatkozik, amelyek natív módon támogatják az adattömbök feldarabolását, vagy lehetővé teszik az adattömbök beállítását a futásidejű konfigurációban. <p><p>HA ISE-t használ, az Logic Apps-motor támogatja ezt a korlátot, de az összekötők saját, a motor korlátjának megfelelő adattömbökre vonatkozó korlátokkal is rendelkezik, lásd például az [Azure Blob Storage-összekötő API-referenciáját.](/connectors/azureblob/) További információ az adattömbökről: Nagy méretű üzenetek kezelés [darabolással.](../logic-apps/logic-apps-handle-large-messages.md) |
|||||

#### <a name="character-limits"></a>Karakterkorlátok

| Name | Korlát | Jegyzetek |
|------|-------|-------|
| Kifejezések kiértékelési korlátja | 131 072 karakter | A `@concat()` , és kifejezések nem lehet `@base64()` `@string()` hosszabbak ennél a korlátnál. |
| Kérelem URL-karakterkorlátja | 16 384 karakter | |
||||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Újrapróbálkozási szabályzat

| Name | Korlát | Jegyzetek |
| ---- | ----- | ----- |
| Újrapróbálkozási kísérletek | 90 | Az alapértelmezett érték 4. Az alapértelmezett beállítás beállítását az [újrapróbálkozási szabályzat paraméterének használatával módosíthatja.](../logic-apps/logic-apps-workflow-actions-triggers.md) |
| Újrapróbálkozások maximális késése | 1 nap | Az alapértelmezett beállítás beállítását az [újrapróbálkozási szabályzat paraméterének használatával módosíthatja.](../logic-apps/logic-apps-workflow-actions-triggers.md) |
| Újrapróbálkozások minimális késése | 5 másodperc | Az alapértelmezett beállítás beállítását az [újrapróbálkozási szabályzat paraméterének használatával módosíthatja.](../logic-apps/logic-apps-workflow-actions-triggers.md) |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Hitelesítési korlátok

A kérelem-eseményindítóval indítható logikai alkalmazások korlátai a következőek, és engedélyezik az Azure Active Directory Open Authentication (Azure AD OAuth) használatát [a](../active-directory/develop/index.yml) Kérés eseményindító bejövő hívásának hitelesítéséhez:

| Name | Korlát | Jegyzetek |
| ---- | ----- | ----- |
| Azure AD engedélyezési szabályzatok | 5 | |
| Jogcímek engedélyezési szabályzatonként | 10 | |
| Jogcímérték – Karakterek maximális száma | 150 |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Egyéni összekötőre vonatkozó korlátok

A webes API-kból létrehozható egyéni összekötők korlátai a következőek.

| Name | Több-bérlős korlát | Integrációs szolgáltatási környezet korlátja | Jegyzetek |
|------|--------------------|---------------------------------------|-------|
| Egyéni összekötők száma | Azure-előfizetésenként 1000 | Azure-előfizetésenként 1000 ||
| Egyéni összekötő kérései percenkénti száma | Kapcsolatonként 500 kérés percenként | Percenként 2000 kérelem egyéni *összekötőnként* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Felügyelt identitások

| Name | Korlát |
|------|-------|
| Felügyelt identitások logikai alkalmazásonként | A rendszer által hozzárendelt identitás vagy 1 felhasználó által hozzárendelt identitás |
| Felügyelt identitással és azure-előfizetésben található logikai alkalmazások száma régiónként | 1,000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Integrációs fiók korlátai

Minden Azure-előfizetésre a következő integrációs fiókok korlátai vonatkoznak:

* [Azure-régiónként](../logic-apps/logic-apps-pricing.md#integration-accounts) egy ingyenes szintű integrációs fiók. Ez a szint csak az Azure nyilvános régióiban érhető el, például az USA nyugati régiójában vagy [Délkelet-Ázsiában,](/azure/china/overview-operations) de nem érhető el a Azure China 21Vianet vagy a [Azure Government.](../azure-government/documentation-government-welcome.md)

* Összesen 1000 integrációs fiók, beleértve az integrációs szolgáltatási környezetekben [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) lévő integrációs fiókokat mind a fejlesztői, mind a [prémium szintű SKUS-ban.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

* Minden ISE( fejlesztői vagy [prémium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)szintű) egyetlen integrációs fiókot használhat további költség nélkül, bár a benne foglalt fiók típusa ISE termékváltozattól függ. További integrációs fiókokat is létrehozhat az ISE-hez a további költségek teljes [korlátja alatt:](logic-apps-pricing.md#fixed-pricing)

  | ISE termékváltozat | Integrációs fiók korlátai |
  |---------|----------------------------|
  | **Prémium** | Összesen 20 fiók, beleértve egy Standard fiókot további költségek nélkül. Ezzel a termékváltozatkal csak Standard [fiókokkal lehet.](../logic-apps/logic-apps-pricing.md#integration-accounts) Ingyenes vagy alapszintű fiókok nem engedélyezettek. |
  | **Fejlesztő** | Összesen 20 fiók, [](../logic-apps/logic-apps-pricing.md#integration-accounts) köztük egy ingyenes fiók (legfeljebb 1). Ezzel a termékváltozatkal az alábbi két kombinációt használhatja: <p>– Ingyenes fiók és legfeljebb 19 [Standard fiók.](../logic-apps/logic-apps-pricing.md#integration-accounts) <br>– Nincs ingyenes fiók és legfeljebb 20 Standard fiók. <p>Alapszintű vagy további ingyenes fiókok nem engedélyezettek. <p><p>**Fontos:** A fejlesztői [termékváltozatot](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) kísérletezésre, fejlesztésre és tesztelésre használhatja, éles környezetben vagy teljesítményt tesztelve azonban nem. |
  |||

Az ISE-k díjszabásának és számlázásának a következő Logic Apps [meg:](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabást a [díjszabást Logic Apps meg.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Összetevőkorlátok integrációs fiókonként

Az integrációs fiók egyes rétegei esetében a következő korlátok vonatkoznak az összetevők számára. A díjszabást a [díjszabást Logic Apps meg.](https://azure.microsoft.com/pricing/details/logic-apps/) Az integrációs fiókok díjszabása és számlázása a következő [díjszabási modellben Logic Apps meg:](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Az Ingyenes szintet csak felderítési forgatókönyvekhez használja, éles forgatókönyvekhez ne. Ez a szint korlátozza az átviteli sebességet és a használatot, és nem rendelkezik szolgáltatói szerződés (SLA) nélkül.

| Összetevő | Ingyenes | Alapszintű | Standard |
|----------|------|-------|----------|
| EDI-kereskedelmi szerződések | 10 | 1 | 1,000 |
| EDI kereskedelmi partnerek | 25 | 2 | 1,000 |
| Maps | 25 | 500 | 1,000 |
| Sémák | 25 | 500 | 1,000 |
| Szerelvények | 10 | 25 | 1,000 |
| Tanúsítványok | 25 | 2 | 1,000 |
| Batch-konfigurációk | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Összetevő kapacitáskorlátai

| Összetevő | Korlát | Jegyzetek |
| -------- | ----- | ----- |
| Szerelvény | 8 MB | A 2 MB-nál nagyobb fájlok feltöltéséhez használjon [Egy Azure Storage-fiókot és egy blobtárolót.](../logic-apps/logic-apps-enterprise-integration-schemas.md) |
| Térkép (XSLT-fájl) | 8 MB | A 2 MB-nál nagyobb fájlok feltöltéséhez használja a [Azure Logic Apps REST API Maps használatával.](/rest/api/logic/maps/createorupdate) <p><p>**Megjegyzés:** A térkép által sikeresen feldolgozhatja az adatok vagy rekordok mennyiségét a rendszer üzenetmérete és a művelet időtúllépési Azure Logic Apps. Ha például HTTP-műveletet használ a [HTTP-üzenetek](#http-limits)mérete és az időtúllépési korlátok alapján, a térkép a HTTP-üzenetek méretkorlátig feldolgozhatja az adatokat, ha a művelet a HTTP-időtúllépési korláton belül fejeződik be. |
| Séma | 8 MB | A 2 MB-nál nagyobb fájlok feltöltéséhez használjon [Egy Azure Storage-fiókot és egy blobtárolót.](../logic-apps/logic-apps-enterprise-integration-schemas.md) |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Átviteli korlátok

| Futásidejű végpont | Ingyenes | Alapszintű | Standard | Jegyzetek |
|------------------|------|-------|----------|-------|
| Hívások olvasása 5 percenként | 3,000 | 30,000 | 60.000 | Ez a korlát azokra a hívásokra vonatkozik, amelyek a logikai alkalmazás futtatáselőzményeiből kérik le a nyers bemeneteket és kimeneteket. A számítási feladatot szükség szerint több fiók között is eloszthatja. |
| Hívások hívása 5 percenként | 3,000 | 30,000 | 45.000 | A számítási feladatot szükség szerint több fiók között is eloszthatja. |
| Hívások nyomon követése 5 percenként | 3,000 | 30,000 | 45.000 | A számítási feladatot szükség szerint több fiók között is eloszthatja. |
| Egyidejű hívások blokkolása | ~1000 | ~1000 | ~1000 | Ugyanaz minden SKUs-hez. Csökkentheti az egyidejű kérések számát, vagy szükség szerint csökkentheti az időtartamot. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B protokoll (AS2, X12, EDIFACT) üzenetméret

A B2B protokollokra vonatkozó üzenetméretkorlátok a következőek:

| Name | Több-bérlős korlát | Integrációs szolgáltatási környezet korlátja | Jegyzetek |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 – 100 MB<br>v1 – 25 MB | v2 – 200 MB <br>v1 – 25 MB | A dekódolásra és a kódolásra vonatkozik |
| X12 | 50 MB | 50 MB | A dekódolásra és a kódolásra vonatkozik |
| EDIFACT | 50 MB | 50 MB | A dekódolásra és a kódolásra vonatkozik |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Logikai alkalmazások letiltása vagy törlése

Amikor letilt egy logikai alkalmazást, a rendszer nem példányosít új futtatásokat. Az összes folyamatban lévő és függőben lévő futtatás a befejezésükig folytatódik, ami időt vehet igénybe.

Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat.

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Tűzfal konfigurációja: IP-címek és szolgáltatáscímkék

Ha a logikai alkalmazásnak egy olyan tűzfalon keresztül kell kommunikálnia, amely  adott [](#inbound) IP-címekre korlátozza a forgalmat, a tűzfalnak engedélyeznie kell a hozzáférést az Logic Apps szolgáltatás vagy a logikai alkalmazást tároló Azure-régióban használt kimenő és bejövő IP-címek számára is. [](#outbound) *Az* ugyanabban a régióban található összes logikai alkalmazás ugyanazt az IP-címtartományt használja.

Ha például az USA nyugati régiójában található logikai alkalmazások által küldött vagy fogadott hívásokat támogatja olyan beépített triggerekkel és  műveletekkel, mint a [HTTP-eseményindító](../connectors/connectors-native-http.md)vagy -művelet, a tűzfalnak engedélyeznie kell a hozzáférést az Logic Apps szolgáltatás összes bejövő IP-címéhez és kimenő IP-címéhez, amely az USA nyugati régiójában található. 

Ha a logikai alkalmazás felügyelt összekötőket is használ , például az Office 365 Outlook-összekötőt vagy az  SQL-összekötőt, vagy egyéni összekötőket használ, a tűzfalnak hozzáférést kell engedélyeznie a felügyelt összekötő összes kimenő [IP-címéhez](#outbound) a logikai alkalmazás Azure-régiójában. [](../connectors/managed.md) [](/connectors/custom-connectors/) Emellett ha olyan egyéni összekötőket használ, amelyek a helyszíni erőforrásokhoz az Azure helyszíni adatátjáró-erőforrásán keresztül férnek hozzá, akkor be kell állítania az átjáró telepítését, hogy engedélyezze a megfelelő felügyelt összekötők kimenő *[IP-címeinek hozzáférését.](#outbound)* [](logic-apps-gateway-connection.md)

Az átjáró kommunikációs beállításainak beállításával kapcsolatos további információkért tekintse meg az alábbi témaköröket:

* [Helyszíni adatátjáró kommunikációs beállításainak módosítása](/data-integration/gateway/service-gateway-communication)
* [Helyszíni adatátjáró proxybeállításainak konfigurálása](/data-integration/gateway/service-gateway-proxy)

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>Tűzfal IP-konfigurációjának szempontjai

Mielőtt beállít egy IP-címet a tűzfalon, tekintse át az alábbi szempontokat:

* Ha a következőt Power Automate: [,](/power-automate/getting-started)néhány művelet, például a **HTTP** és a **HTTP + OpenAPI,** közvetlenül a Azure Logic Apps szolgáltatáson keresztül, és az itt felsorolt IP-címekről indul. A szolgáltatás által használt IP-címekkel kapcsolatos további Power Automate [lásd:](/flow/limits-and-config#ip-address-configuration)A Power Automate.

* A [Azure China 21Vianet](/azure/china/)esetén a rögzített vagy fenntartott IP-címek nem érhetők el az egyéni összekötőkhöz és a felügyelt összekötőkhöz, például az Azure Storage-hoz, az SQL Server-hoz, az Office 365 Outlookhoz stb. [](../logic-apps/custom-connector-overview.md) [](../connectors/managed.md)

* Ha a logikai alkalmazások integrációs szolgáltatási [környezetben (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md)futnak, győződjön meg arról, hogy ezeket a portokat is [megnyitja.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)

* A létrehozni kívánt biztonsági szabályok egyszerűsítése érdekében ehelyett [](../virtual-network/service-tags-overview.md) használhatja a szolgáltatáscímkéket ahelyett, hogy minden régióhoz megadnál IP-címelőtagokat. Ezek a címkék minden olyan régióban működnek, ahol Logic Apps szolgáltatás elérhető:

  * **LogicAppsManagement:** A bejövő IP-címelőtagokat jelöli a Logic Apps szolgáltatáshoz.

  * **LogicApps:** A szolgáltatás kimenő IP-címelőtagját Logic Apps jelöli.

  * **AzureConnectors:** A felügyelt összekötők IP-címelőtagját jelöli, amelyek bejövő webhook-visszahívást kezdeményeznek az Logic Apps-szolgáltatásba, valamint kimenő hívásokat a megfelelő szolgáltatásokhoz, például az Azure Storage-hoz vagy a Azure Event Hubs.

* Ha a logikai alkalmazásokban problémák merültek [](../storage/common/storage-network-security.md)fel a tűzfalakat és tűzfalszabályokat felhasználó Azure Storage-fiókok elérésekor, a hozzáférés engedélyezésére számos egyéb [lehetőség áll rendelkezésre.](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

  A logikai alkalmazások például nem férhetnek hozzá közvetlenül a tűzfalszabályokat felhasználó és ugyanabban a régióban található tárfiókhoz. Ha azonban engedélyezi [](../logic-apps/logic-apps-limits-and-config.md#outbound)a kimenő IP-címeket a régiójában található felügyelt összekötőkhöz, a logikai alkalmazások hozzáférhetnek a más régióban található tárfiókhoz, kivéve, ha az Azure Table Storage vagy Azure Queue Storage összekötőket használja. A http Table Storage vagy Queue Storage eléréséhez használhatja inkább a HTTP-eseményindítót és -műveleteket. További beállításokért lásd: [Access storage accounts behind firewalls (Tűzfalak mögötti tárfiókok elérése).](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Bejövő IP-címek

Ez a szakasz csak a szolgáltatás bejövő IP-Azure Logic Apps sorolja fel. Ha már van Azure Government, [lásd: Azure Government – Bejövő IP-címek.](#azure-government-inbound)

> [!TIP]
> A biztonsági szabályok létrehozásakor a bonyolultság csökkentése érdekében [](../virtual-network/service-tags-overview.md)igény szerint használhatja a **LogicAppsManagement** szolgáltatáscímkét ahelyett, hogy minden régióhoz Logic Apps ip-címelőtagokat ad meg. Használhatja az **AzureConnectors** szolgáltatáscímkét olyan felügyelt összekötőkhöz is, amelyek bejövő webhook-visszahívást hoznak a Logic Apps-szolgáltatásba ahelyett, hogy minden régióhoz megadnék a bejövő felügyelt összekötő IP-címelőtagját. Ezek a címkék minden olyan régióban működnek, ahol Logic Apps szolgáltatás elérhető.
>
> Az alábbi összekötők bejövő webhook-visszahívásokat hoznak Logic Apps szolgáltatásba:
>
> Adobe Creative Cloud, Adobe Sign, Adobe Sign Demo, Adobe Sign Preview, Adobe Sign Stage, Azure Sentinel, Business Central, Calendly, Common Data Service, DocuSign, DocuSign Demo, Dynamics 365 for Fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, Parserr, SAP*, Shifts for Microsoft Teams, Teamwork Projects, Typeform
>
> \***SAP:** A visszatérő hívó attól függ, hogy az üzembe helyezési környezet több-bérlős Azure-beli vagy ISE-e. A több-bérlős környezetben a helyszíni adatátjáró visszahívja a Logic Apps szolgáltatáshoz. Az ISE-összekötőkben az SAP-összekötő visszahívja a Logic Apps szolgáltatást.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Több-bérlős Azure – Bejövő IP-címek

| Több-bérlős régió | IP |
|---------------------|----|
| Kelet-Ausztrália | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Délkelet-Ausztrália | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Dél-Brazília | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Délkelet-Brazília | 20.40.32.59, 20.40.32.162, 20.40.32.80, 20.40.32.49 |
| Közép-Kanada | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Kelet-Kanada | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Közép-India | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Az USA középső régiója | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Kelet-Ázsia | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| USA keleti régiója | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| USA 2. keleti régiója | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Közép-Franciaország | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Dél-Franciaország | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Észak-Németország | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Nyugat-Németország – középső régió | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0 |
| Kelet-Japán | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Nyugat-Japán | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Dél-Korea középső régiója | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Dél-Korea déli régiója | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| USA északi középső régiója | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Észak-Európa | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Kelet-Kelet | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| Dél-Afrika északi régiója | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Dél-Afrika nyugati régiója | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| USA déli középső régiója | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Dél-India | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Délkelet-Ázsia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Észak-Svájc | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Nyugat-Svájc | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| Egyesült Arab Emírségek középső | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Egyesült Arab Emírségek északi iránya | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| Az Egyesült Királyság déli régiója | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Az Egyesült Királyság nyugati régiója | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| USA nyugati középső régiója | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Nyugat-Európa | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Nyugat-India | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| USA nyugati régiója | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| USA 2. nyugati régiója | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government – Bejövő IP-címek

| Azure Government régió | IP |
|-------------------------|----|
| USA-beli államigazgatás – Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| USA-beli államigazgatás – Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| USA-beli államigazgatás – Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD – Középső régió | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Kimenő IP-címek

Ez a szakasz a szolgáltatás és a felügyelt összekötők Azure Logic Apps IP-címeit sorolja fel. Ha már van Azure Government, [lásd: Azure Government - Kimenő IP-címek.](#azure-government-outbound)

> [!TIP]
> A biztonsági szabályok létrehozása során a bonyolultság csökkentése [](../virtual-network/service-tags-overview.md)érdekében igény szerint használhatja a **LogicApps** szolgáltatáscímkét, és nem kell minden régióhoz kimenő Logic Apps IP-címelőtagokat megadnia. Használhatja az **AzureConnectors** szolgáltatáscímkét olyan felügyelt összekötőkhöz is, amelyek kimenő hívásokat kezdeményeznek a megfelelő szolgáltatásokhoz, például az Azure Storage-hoz vagy az Azure Event Hubs-hoz, ahelyett, hogy minden régióhoz megadnál kimenő felügyelt összekötő IP-címelőtagokat. Ezek a címkék minden olyan régióban működnek, ahol Logic Apps szolgáltatás elérhető.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Több-bérlős Azure – Kimenő IP-címek

| Több-bérlős régió | Logic Apps IP-cím | Felügyelt összekötők IP-címe |
|---------------------|---------------|-----------------------|
| Kelet-Ausztrália | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 52.237.214.72, 13.72.243.10, 13.70.72.192 - 13.70.72.207, 13.70.78.224 - 13.70.78.255 |
| Délkelet-Ausztrália | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 52.255.48.202, 13.70.136.174, 13.77.50.240 - 13.77.50.255, 13.77.55.160 - 13.77.55.191 |
| Dél-Brazília | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 191.232.191.157, 104.41.59.51, 191.233.203.192 - 191.233.203.207, 191.233.207.160 - 191.233.207.191 |
| Délkelet-Brazília | 20.40.32.81, 20.40.32.19, 20.40.32.85, 20.40.32.60, 20.40.32.116, 20.40.32.87, 20.40.32.61, 20.40.32.113 | 23.97.120.109, 23.97.121.26 |
| Közép-Kanada | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 52.237.32.212, 52.237.24.126, 13.71.170.208 - 13.71.170.223, 13.71.175.160 - 13.71.175.191 |
| Kelet-Kanada | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 52.242.30.112, 52.242.35.152, 40.69.106.240 - 40.69.106.255, 40.69.111.0 - 40.69.111.31 |
| Közép-India | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.212.129, 52.172.211.12, 20.43.123.0 - 20.43.123.31, 104.211.81.192 - 104.211.81.207 |
| Az USA középső régiója | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 52.173.241.27, 52.173.245.164, 13.89.171.80 - 13.89.171.95, 13.89.178.64 - 13.89.178.95 |
| Kelet-Ázsia | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.110.131, 52.175.23.169, 13.75.36.64 - 13.75.36.79, 104.214.164.0 - 104.214.164.31 |
| USA keleti régiója | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.249.139, 40.71.249.205, 40.114.40.132, 40.71.11.80 - 40.71.11.95, 40.71.15.160 - 40.71.15.191 |
| USA 2. keleti régiója | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 52.225.129.144, 52.232.188.154, 104.209.247.23, 40.70.146.208 - 40.70.146.223, 40.70.151.96 - 40.70.151.127 |
| Közép-Franciaország | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.89.186.239, 40.89.135.2, 40.79.130.208 - 40.79.130.223, 40.79.148.96 - 40.79.148.127 |
| Dél-Franciaország | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.142.154, 52.136.133.184, 40.79.178.240 - 40.79.178.255, 40.79.180.224 - 40.79.180.255 |
| Észak-Németország | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 | 51.116.60.192, 51.116.211.212, 51.116.59.16 - 51.116.59.31, 51.116.60.192 - 51.116.60.223 |
| Nyugat-Németország – középső régió | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51 | 51.116.158.97, 51.116.236.78, 51.116.155.80 - 51.116.155.95, 51.116.158.96 - 51.116.158.127 |
| Kelet-Japán | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.73.21.230, 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.79.189.64 - 40.79.189.95 |
| Nyugat-Japán | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 104.215.27.24, 104.215.61.248, 40.74.100.224 - 40.74.100.239, 40.80.180.64 - 40.80.180.95 |
| Dél-Korea középső régiója | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.1.104, 52.141.36.214, 20.44.29.64 - 20.44.29.95, 52.231.18.208 - 52.231.18.223 |
| Dél-Korea déli régiója | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.201.173, 52.231.163.10, 52.231.147.0 - 52.231.147.15, 52.231.148.224 - 52.231.148.255 |
| USA északi középső régiója | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.126.4, 52.162.242.161, 52.162.107.160 - 52.162.107.175, 52.162.111.192 - 52.162.111.223 |
| Észak-Európa | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 52.169.28.181, 52.178.150.68, 94.245.91.93, 13.69.227.208 - 13.69.227.223, 13.69.231.192 - 13.69.231.223 |
| Kelet-Kelet | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 | 51.120.100.192, 51.120.92.27, 51.120.98.224 - 51.120.98.239, 51.120.100.192 - 51.120.100.223 |
| Dél-Afrika északi régiója | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31 |
| Dél-Afrika nyugati régiója | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15 |
| USA déli középső régiója | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63 |
| Dél-India | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255 |
| Délkelet-Ázsia | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63 |
| Észak-Svájc | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255 |
| Nyugat-Svájc | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 | 51.107.156.224, 51.107.231.190, 51.107.155.16 - 51.107.155.31, 51.107.156.224 - 51.107.156.255 |
| Egyesült Arab Emírségek középső | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| Egyesült Arab Emírségek északi iránya | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 | 65.52.250.208, 40.123.224.120, 40.120.64.64 - 40.120.64.95, 65.52.250.208 - 65.52.250.223 |
| Az Egyesült Királyság déli régiója | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15 |
| Az Egyesült Királyság nyugati régiója | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255 |
| USA nyugati középső régiója | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223 |
| Nyugat-Európa | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126, 13.69.71.160, 13.69.71.161, 13.69.71.162, 13.69.71.163, 13.69.71.164, 13.69.71.165, 13.69.71.166, 13.69.71.167 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223 |
| Nyugat-India | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239 |
| USA nyugati régiója | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32, 13.86.223.0, 13.86.223.1, 13.86.223.2, 13.86.223.3, 13.86.223.4, 13.86.223.5 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175 |
| USA 2. nyugati régiója | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government – Kimenő IP-címek

| Region | Logic Apps IP-cím | Felügyelt összekötők IP-címe |
|--------|---------------|-----------------------|
| US DoD – Középső régió | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223 |
| USA-beli államigazgatás – Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255 |
| USA-beli államigazgatás – Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159 |
| USA-beli államigazgatás – Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhatja létre első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* Megismeri [a gyakori példákat és forgatókönyveket](../logic-apps/logic-apps-examples-and-scenarios.md)
