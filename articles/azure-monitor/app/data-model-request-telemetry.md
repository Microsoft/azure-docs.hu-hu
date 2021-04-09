---
title: Adatmodell a kérelem telemetria – Azure Application Insights
description: Application Insights adatmodell a kérelem telemetria
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 7a352f4ce3528d395599a91b53031c74b0873152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320561"
---
# <a name="request-telemetry-application-insights-data-model"></a>Kérelem telemetria: Application Insights adatmodell

A kérelem telemetria eleme ( [Application Insights](./app-insights-overview.md)) az alkalmazás külső kérelme által aktivált végrehajtás logikai sorozatot jelöli. Minden kérelem végrehajtását egyedi azonosítja, `ID` és `url` az összes végrehajtási paramétert tartalmazza. A kérelmeket logikusan csoportosíthatja `name` , és megadhatja a `source` kérést. A kód végrehajtása a vagy a szolgáltatáshoz vezethet `success` `fail` `duration` . A sikeres és a sikertelen végrehajtás is elvégezhető a további csoportosításával `resultCode` . A kérés telemetria megadott kezdési ideje.

A telemetria kérése támogatja a standard bővíthetőségi modellt a Custom és a használatával `properties` `measurements` .

## <a name="name"></a>Name

A kérelem neve a kérelem feldolgozásához a kód elérési útját jelöli. Alacsony kardinális érték a kérelmek jobb csoportosításának lehetővé tételéhez. HTTP-kérelmek esetén a a HTTP-metódust és az URL-cím elérési útját jelöli, mint `GET /values/{id}` a tényleges `id` érték nélkül.

Application Insights web SDK a "as is" nevű kérelem nevét küldi el a Letter eset tekintetében. A felhasználói felületen való csoportosítás megkülönbözteti a kis-és nagybetűket, ezért `GET /Home/Index` a rendszer attól függetlenül számítja, hogy `GET /home/INDEX` az adott vezérlő és a művelet végrehajtása gyakran azonos-e. Ennek az az oka, hogy az URL-címek általában [megkülönböztetik a kis-és nagybetűket](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Érdemes megtekinteni, hogy `404` az URL-címek nagybetűvel lettek-e beírva. A ASP.NET web SDK-val kapcsolatos további információk a kérelmek neve gyűjteményben olvashatók a [blogbejegyzésben](https://apmtips.com/posts/2015-02-23-request-name-and-url/).

Maximális hossz: 1024 karakter

## <a name="id"></a>ID (Azonosító)

Egy kérelem hívási példányának azonosítója. A kérelem és az egyéb telemetria elemek közötti korrelációhoz használatos. Az AZONOSÍTÓnak globálisan egyedinek kell lennie. További információ: [korrelációs](./correlation.md) oldal.

Maximális hossz: 128 karakter

## <a name="url"></a>URL-cím

Kérelem URL-címe az összes lekérdezési karakterlánc paraméterrel.

Maximális hossz: 2048 karakter

## <a name="source"></a>Forrás

A kérelem forrása. Ilyenek például a hívó rendszerállapot-kulcsa vagy a hívó IP-címe. További információ: [korrelációs](./correlation.md) oldal.

Maximális hossz: 1024 karakter

## <a name="duration"></a>Időtartam

Kérelem időtartama (formátum `DD.HH:MM:SS.MMMMMM` ):. Értéke csak pozitív és nem lehet `1000` nap. Erre a mezőre azért van szükség, mert a Request telemetria a művelet kezdetét és végét jelöli.

## <a name="response-code"></a>Válasz kódja

A kérelem végrehajtásának eredménye. A HTTP-kérelmek HTTP-állapotkódot. `HRESULT`Más típusú kérelmek esetében az érték vagy a kivétel típusa lehet.

Maximális hossz: 1024 karakter

## <a name="success"></a>Siker

A sikeres vagy sikertelen hívás megjelölése. A mező kitöltése kötelező. Ha nincs explicit módon beállítva a `false` – a kérelem sikeresnek tekintendő. Állítsa be ezt az értéket arra az `false` esetre, ha a művelet kivétel miatt megszakadt, vagy a hiba eredményének kódját adta vissza.

A webalkalmazások esetében Application Insights sikeres kérést adjon meg, ha a válasz kódja kisebb `400` vagy egyenlő `401` . Vannak azonban olyan esetek, amikor ez az alapértelmezett leképezés nem egyezik az alkalmazás szemantikai állapotával. A válasz kódja `404` a "No Records" kifejezésre utalhat, amely a normál folyamat része lehet. Emellett hibás hivatkozást is jelezhet. A hibás hivatkozások esetében még összetettebb logika is megvalósítható. A hibás hivatkozásokat csak akkor lehet hibákként megjelölni, ha a hivatkozások ugyanazon a helyen találhatók, a hivatkozó URL-cím elemzésével. Vagy megjelölheti őket meghibásodásként a vállalat mobil alkalmazásából való hozzáféréskor. Hasonlóképpen `301` , és `302` azt jelzi, hogy hiba történt az átirányítást nem támogató ügyféltől való hozzáféréskor.

A részlegesen elfogadott tartalom `206` egy általános kérelem meghibásodását jelezhetik. A Application Insights végpont például egyetlen kérelemként fogadja a telemetria elemek kötegét. A függvény akkor adja vissza, `206` Ha a köteg egyes elemeit nem sikerült feldolgozni. A növekvő arány a `206` vizsgálandó problémára utal. Hasonló logika vonatkozik arra a `207` többszörös állapotra, ahol a siker lehet az eltérő válasz kódokhoz tartozó legrosszabb érték.

További információ: kérelem eredményének kódja és állapotkód a [blogbejegyzésben](https://apmtips.com/posts/2016-12-03-request-success-and-response-code/).

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Következő lépések

- [Egyéni kérelem telemetria írása](./api-custom-events-metrics.md#trackrequest)
- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- Megtudhatja, hogyan [konfigurálhat ASP.net Core](./asp-net.md) alkalmazást Application Insights használatával.
- Tekintse meg Application Insights által támogatott [platformokat](./platforms.md) .

