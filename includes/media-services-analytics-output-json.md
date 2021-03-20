---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179533"
---
A feladat létrehoz egy JSON kimeneti fájlt, amely az észlelt és nyomon követett arcokhoz tartozó metaadatokat tartalmaz. A metaadatok tartalmazzák az arcok helyét jelző koordinátákat, valamint az adott személy nyomon követését jelző Arcfelismerés-számot. A Face ID-számok olyan körülmények között állnak alaphelyzetbe, amikor az elülső arc elveszett vagy átfedésben van a keretben, így egyes személyek több azonosítót kapnak.

A kimeneti JSON a következő elemeket tartalmazza:

### <a name="root-json-elements"></a>Gyökér JSON-elemek

| Elem | Leírás |
| --- | --- |
| version |Ez a videó API verziójára vonatkozik. |
| időskála |A videó másodpercenkénti száma. |
| offset |Az időbélyegek időbeli eltolása. A video API-k 1,0-es verziójában ez mindig 0 lesz. Az általunk támogatott jövőbeli forgatókönyvek esetében ez az érték változhat. |
| szélesség, magasság |A kimeneti videó keretének szélessége és magassága képpontban megadva|
| frameráta |Képkockák másodpercenkénti száma a videóban. |
| [töredékek](#fragments-json-elements) |A metaadatok a töredékek nevű különböző szegmensekben vannak kiosztva. Minden töredék tartalmaz kezdési időpontot, időtartamot, intervallumszámot és esemény(eke)t. |

### <a name="fragments-json-elements"></a>Töredékek JSON-elemek

|Elem|Leírás|
|---|---|
| start |Az első esemény kezdő időpontja "ketyeg". |
| duration |A töredék hossza "ketyeg". |
| index | (Csak Azure Media Redactorra vonatkozik) meghatározza az aktuális esemény frame indexét. |
| interval |Az egyes események bejegyzésének intervalluma a töredéken belül "ketyeg". |
| események |Minden esemény tartalmazza az észlelt és nyomon követett arcokat az adott időtartamon belül. Az események tömbje. A külső tömb egy időintervallumot jelöl. A belső tömb 0 vagy több eseményből áll, amelyek az adott időpontban történtek. Egy üres zárójel [] nem észlelt arcokat. |
| id |A nyomon követett arc azonosítója. Ez a szám akaratlanul változhat, ha egy arc észlelése észrevétlen lesz. Egy adott személynek ugyanazzal az AZONOSÍTÓval kell rendelkeznie a teljes videóban, de ez az észlelési algoritmus (elzáródás stb.) korlátai miatt nem garantálható. |
| x, y |Az oldal bal felső X és Y koordinátái a 0,0 és 1,0 közötti normalizált méretezési mezőben. <br/>-Az X és Y koordináták mindig a fekvő tájoláshoz viszonyítva jelennek meg, így ha van egy portré videója (vagy az iOS esetében is), akkor ennek megfelelően át kell ültetni a koordinátákat. |
| szélesség, magasság |Az arc határoló mező szélessége és magassága a 0,0 és 1,0 közötti normalizált méretekben. |
| facesDetected |Ez a JSON-eredmények végén található, és összegzi azon arcok számát, amelyeket az algoritmus észlelt a videó során. Mivel az azonosítókat véletlenül vissza lehet állítani, ha egy arc észlelése nem történik meg (például az arc kikapcsolja a képernyőt, megtekinti a lapot), ez a szám nem mindig egyenlő a videóban található arcok számával. |
