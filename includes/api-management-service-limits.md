---
title: fájl belefoglalása
description: fájl belefoglalása
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 02/19/2021
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 0614e6fb1df13332cb05dcb7bb9b0f07a1764d23
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251271"
---
| Erőforrás | Korlát |
| ---------------------------------------------------------------------- | -------------------------- |
| Méretezési egységek maximális száma | 10 régiónként<sup>1</sup> |
| Gyorsítótár mérete | 5 GiB/egység<sup>2</sup> |
| Egyidejű háttér-kapcsolatok<sup>3</sup> /http-szolgáltató | 2 048/egységenként<sup>4</sup> |
| Gyorsítótárazott válasz maximális mérete | 2 MiB |
| Házirend-dokumentum maximális mérete | 256 KiB<sup>5</sup> |
| Egyéni átjáró-tartományok maximális száma szolgáltatási példány esetén<sup>6</sup> | 20 |
| HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok maximális száma a szolgáltatási példányok esetében<sup>7</sup> | 10 |
| Szolgáltatási példányok maximális száma előfizetésben<sup>8</sup> | 20 |
| Az előfizetések maximális száma a<sup>8</sup> . szolgáltatási példány esetében | 500 |
| Ügyféltanúsítvány-alapú tanúsítványok maximális száma a<sup>8</sup> . szolgáltatási példány esetében | 50 |
| API-k maximális száma a szolgáltatási példányok esetében<sup>8</sup> | 50 |
| Az API Management műveleteinek maximális száma a<sup>8</sup> . szolgáltatási példány esetében | 1,000 |
| Maximális kérelem teljes időtartama<sup>8</sup> | 30 másodperc |
| Maximálisan pufferelt hasznos adatok mérete<sup>8</sup> | 2 MiB |
| Kérelem URL-címének maximális mérete (<sup>9</sup> ) | 4096 bájt |
| URL-cím elérési útjának maximális hossza<sup>10</sup> | 260 karakter |
| Az [érvényesítési házirend](../articles/api-management/validation-policies.md)által használt API-séma maximális mérete<sup>10</sup> | 4 MB |
| Kérelem vagy válasz törzsének maximális mérete a [validate-Content Policy-](../articles/api-management/validation-policies.md#validate-content) ben | 100 KB |
| Saját üzemeltetésű átjárók maximális száma<sup>11</sup> | 25 |

<sup>1</sup> A skálázási korlátok az árképzési szinttől függenek. A díjszabási szintek és a skálázási korlátok részletes ismertetését lásd: [API Management díjszabás](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> Egységenkénti gyorsítótár mérete az árképzési szinttől függ. A díjszabási szintek és a méretezési korlátok megtekintéséhez lásd: [API Management díjszabása](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> A kapcsolatok készletezése és újbóli felhasználása, kivéve, ha a háttér explicit módon lezárta.<br/>
<sup>4</sup> Ez a korlát az alapszintű, a standard és a prémium szint egységenként van. A fejlesztői szintet 1 024-re korlátozzák. Ez a korlát nem vonatkozik a felhasználás szintjére.<br/>
<sup>5</sup> Ez a korlát az alapszintű, a standard és a prémium szintekre vonatkozik. A felhasználási szinten a szabályzat dokumentumának mérete legfeljebb 16 KiB lehet.<br/>
<sup>6</sup> A fejlesztői és a prémium szintű csomagokban több egyéni tartomány is támogatott.<br/>
<sup>7</sup> A CA-tanúsítványok nem támogatottak a felhasználási szinten.<br/>
<sup>8</sup> Ez a korlát csak a felhasználási szinten érvényes. A kategóriákban nincsenek korlátok.<br/>
<sup>9</sup> Csak a felhasználási szintjére vonatkozik. Akár 2048 bájt hosszú lekérdezési karakterláncot tartalmaz.<br/>
<sup>10</sup> a korlát növeléséhez forduljon az [ügyfélszolgálathoz](https://azure.microsoft.com/support/options/).<br/>
<sup>11</sup> A saját üzemeltetésű átjárók csak a fejlesztői és a prémium szinteken támogatottak. A korlát a saját üzemeltetésű [átjáró erőforrásainak](/rest/api/apimanagement/2019-12-01/gateway)számára vonatkozik. A korlát növeléséhez forduljon az [ügyfélszolgálathoz](https://azure.microsoft.com/support/options/). Vegye figyelembe, hogy a saját üzemeltetésű átjáró erőforráshoz társított csomópontok (vagy replikák) száma korlátlan a prémium szinten, és a fejlesztői szint egyetlen csomópontján van korlátozva.
