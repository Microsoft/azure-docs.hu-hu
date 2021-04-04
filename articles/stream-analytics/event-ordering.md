---
title: Az események rendezésére vonatkozó szabályzatok konfigurálása Azure Stream Analyticshoz
description: Ez a cikk azt ismerteti, hogyan lehet még megrendelési beállításokat konfigurálni Stream Analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: f7ec6f32b48a93a29210311c7ba6747eb2e2d066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014295"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Az események rendezésére vonatkozó szabályzatok konfigurálása Azure Stream Analyticshoz

Ez a cikk bemutatja, hogyan kell beállítani és használni a késői érkezési és a nem megrendelési eseményekre vonatkozó szabályzatokat a Azure Stream Analyticsban. Ezeket a házirendeket csak akkor alkalmazza a rendszer, ha a lekérdezésben a [timestamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics) záradékot használja, és csak Felhőbeli bemeneti forrásokra érvényesek.

## <a name="event-time-and-arrival-time"></a>Esemény időpontja és érkezési idő

Az Stream Analytics-feladatok az eseményeket akár az *esemény* vagy az *érkezési idő* alapján is feldolgozhatják. Az esemény **/alkalmazás ideje** az esemény-adattartalomban (az esemény létrehozásakor) lévő időbélyeg. A **beérkezési idő** az az időpont, amikor az esemény a bemeneti forrásnál (Event Hubs/IoT hub/blob Storage) érkezett. 

Alapértelmezés szerint a Stream Analytics az eseményeket *beérkezési idő* szerint dolgozza fel, de az események *időpontra* történő feldolgozását a lekérdezés [timestamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics) záradék használatával végezheti el. A késői érkezés és a megrendelési szabályzatok csak akkor érvényesek, ha az eseményeket esemény szerint dolgozzák fel. Mindig vegye figyelembe a forgatókönyv késésre és helyes sorrendre vonatkozó követelményeit ezen beállítások konfigurálásakor. 

## <a name="what-is-late-arrival-policy"></a>Mi a kései érkezésre vonatkozó szabályzat?

Időnként az események különböző okok miatt későn érkeznek. Ha például a 40 másodperces időpontot megérkezik, az esemény időpontja = 00:10:00 és érkezési idő = 00:10:40. Ha a késői érkezési szabályzatot 15 másodpercre állítja be, akkor a 15 másodpercnél későbbi megérkezik események el lesznek dobva (nem Stream Analytics), vagy az esemény időpontját módosítani kell. A fenti példában az esemény a 40 másodpercnél (több mint házirend-készletnél) későn érkezik, az esemény idejét a rendszer a 00:10:25 késői érkezési idő (az érkezés időpontjának megérkezése esetén) értékre állítja be. Az alapértelmezett késői érkezési szabályzat 5 másodperc.

## <a name="what-is-out-of-order-policy"></a>Mi az a rendelésen kívüli szabályzat? 

Előfordulhat, hogy az esemény is megérkezik. Ha az esemény időpontját a késői beérkezési szabályzat alapján igazították ki, akkor azt is megteheti, hogy automatikusan eldobja vagy módosítja a nem kívánt eseményeket. Ha a szabályzatot 8 másodpercre állítja be, akkor a megrendelésen kívüli, de a 8 másodperces időszakon belül megjelenő események az esemény időpontjában lesznek átrendezve. A később megjelenő események elvesznek, vagy a maximálisan megrendelésen kívüli házirend értékének megfelelően lesznek módosítva. Az alapértelmezett elutasítási szabályzat 0 másodperc. 

## <a name="adjust-or-drop-events"></a>Események módosítása vagy eldobása

Ha az események a konfigurált szabályzatok alapján késve érkeznek vagy nem sorrendben érkeznek meg, akkor elhúzhatja az ilyen eseményeket (Stream Analytics nem dolgozza fel), vagy az esemény időpontját módosítani kell.

Lássunk egy példát a szabályzatokra működés közben.
<br> **Késői érkezési szabályzat:** 15 másodperc
<br> Nem **megrendelési szabályzat:** 8 másodperc

| Esemény nem. | Esemény időpontja | Érkezés időpontja | System.Timestamp | Magyarázat |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Az esemény későn, a tolerancia szintjén kívülre érkezett. Így az esemény időpontját a maximális késői érkezési toleranciára igazítottuk.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Az esemény későn, de tűréshatáron belül érkezett. Így az esemény időpontja nem módosul.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Az esemény időben érkezett. Nincs szükség módosításra.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Az esemény megérkezett, de 8 másodperces tűréshatáron belül. Így az esemény időpontja nem módosul. Elemzési célokra ez az esemény a 4. számú eseménnyel fog tekinteni.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Az esemény nem érkezett meg a sorrendtől, és 8 másodpercen kívül esik a tűréshatáron. Így az esemény időpontját a rendszer a maximálisan megengedettnél több tűréshatárra módosítja. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Késleltetheti ezek a beállítások a saját feladatok kimenetét? 

Igen. Alapértelmezés szerint az elutasítási házirend értéke nulla (00 perc és 00 másodperc). Ha módosítja az alapértelmezett beállítást, a rendszer ezt az értéket (vagy annál nagyobb) késlelteti a feladatok első kimenetében. 

Ha a bemenetek egyik partíciója nem kap eseményeket, a kimenetet a késői érkezési szabályzat értékével kell késleltetni. Ha szeretné megismerni a okát, olvassa el az alábbi InputPartition-hiba szakaszt. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Látom a LateInputEvents üzeneteket a saját tevékenység naplójában

Ezek az üzenetek tájékoztatják, hogy az események későn érkeztek, és a konfigurációjuk szerint el vannak dobva vagy ki vannak igazítva. Ezeket az üzeneteket figyelmen kívül hagyhatja, ha megfelelően konfigurálta a késői érkezési szabályzatot. 

Példa erre az üzenetre: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Látom a InputPartitionNotProgressing a saját tevékenység naplójában

A bemeneti forrás (Event hub/IoT Hub) valószínűleg több partícióval rendelkezik. A Azure Stream Analytics csak akkor állítja elő a T1 időbélyegzőhöz tartozó kimenetet, ha az összes összevont partíció legalább T1 időpontban van. Tegyük fel például, hogy a lekérdezés két partícióval rendelkező Event hub-partícióról olvas. A P1 egyik partíciója a T1 időpontig eseményeket tartalmaz. A másik partíció, a P2, a T1 + x időtartamig eseményeket tartalmaz. A rendszer ezután a kimenetet a T1 időpontig állítja elő. Ha azonban az PartitionId záradék explicit partíciókat is tartalmaz, a partíciók egymástól függetlenül haladnak. 

Ha ugyanabból a bemeneti adatfolyamból több partíció is össze van összevonva, a késői beérkezés tűréshatára azt a maximális időtartamot adja meg, ameddig az egyes partíciók megvárnak az új adatokra. Ha az Event hub egyik partíciója van, vagy ha IoT Hub nem kap bemeneteket, akkor a partícióhoz tartozó ütemterv addig nem halad, amíg el nem éri a késői érkezési tűréshatár küszöbértékét. Ez késlelteti a kimenetet a késői érkezési tűréshatár küszöbértéke alapján. Ilyen esetekben a következő üzenet jelenhet meg:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Ez az üzenet tájékoztat arról, hogy a bemenetben legalább egy partíció üres, és a késői érkezési küszöbérték miatt késlelteti a kimenetet. Ennek megoldásához a következők ajánlottak:  
1. Győződjön meg arról, hogy az Event hub/IoT Hub összes partíciója fogadja a bemenetet. 
2. A lekérdezésben használja a Partition by PartitionID záradékot. 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>Miért látok 5 másodperces késleltetést akkor is, ha a késői érkezési szabályzat értéke 0?
Ez akkor történik meg, ha van olyan bemeneti partíció, amely soha nem kapott semmilyen bemenetet. A bemeneti metrikákat a partíció alapján ellenőrizheti a viselkedés ellenőrzéséhez. 

Ha egy partícióhoz nem tartozik több, mint a beállított késői érkezési küszöbérték, a stream Analytics az alkalmazás időbélyegét az esemény rendezési szempontjai című szakaszban ismertetett módon részletezi. Ehhez a becsült érkezési időt kell megbecsülni. Ha a partíció soha nem tartalmazott semmilyen adatforrást, a stream Analytics *helyi idő szerint (5 másodperc*) megbecsüli az érkezési időt. Az olyan partíciók miatt, amelyek soha nem voltak adatai, 5 másodperces vízjel-késleltetést mutatnak.  

## <a name="next-steps"></a>Következő lépések
* [Időkezelési megfontolások](stream-analytics-time-handling.md)
* [Stream Analytics elérhető metrikák](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)