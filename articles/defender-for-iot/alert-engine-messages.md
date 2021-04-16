---
title: Riasztástípusok és leírások
description: Tekintse át a Defender ioT-riasztások leírását.
ms.date: 4/8/2021
ms.topic: how-to
ms.openlocfilehash: 483563b53a5849b0354986269568bc42b9124cc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477995"
---
# <a name="alert-types-and-descriptions"></a>Riasztástípusok és leírások

Ez a cikk az összes olyan riasztástípust ismerteti, amely a Defender for IoT-motorból generálható. A riasztások a Riasztások ablakban jelennek meg, amely lehetővé teszi a riasztási esemény kezelését. 

## <a name="policy-engine-alerts"></a>Szabályzatmotor-riasztások

A szabályzatmotor riasztásai a megtanult alapkonfigurációtól való eltéréseket írják le.

| Cím  | Leírás | Súlyosság |
|--|--|--|
| MAC-címek rendellenes használata | A rendszer egy új forráseszközt észlelt a hálózaton, de nem engedélyezte. | Kismérvű |
| A szoftver megváltozott | A belső vezérlőprogram frissítve lett egy forráseszközön. Ez lehet egy engedélyezett tevékenység, például egy tervezett karbantartási eljárás. | Jelentős |
| Az adatbázisba való bejelentkezés sikertelen | A rendszer sikertelen bejelentkezési kísérletet észlelt egy forráseszközről egy célkiszolgálóra. Ez lehet emberi hiba eredménye, de arra is utalhat, hogy rosszindulatú kísérlet történt a kiszolgáló vagy a rajta található adatok feltörére. | Jelentős |
| Az Emerson ROC belső vezérlőprogramjának verziója megváltozott | A belső vezérlőprogram frissítve lett egy forráseszközön. Ez lehet egy engedélyezett tevékenység, például egy tervezett karbantartási eljárás. | Jelentős |
| A hálózaton belüli külső cím, az internettel kommunikálva | A hálózat részeként definiált forráseszköz internetes címekkel kommunikál. A forrás nem jogosult az internetcímekkel való kommunikációra. | Kritikus |
| Váratlanul felderített mezőeszköz | A rendszer új forráseszközt észlelt a hálózaton, de nem engedélyezte. | Jelentős |
| Belső vezérlőprogram-módosítás észlelhető | A belső vezérlőprogram frissítve lett egy forráseszközön. Ez lehet engedélyezett tevékenység, például tervezett karbantartási eljárás. | Jelentős |
| A belső vezérlőprogram verziója megváltozott | A belső vezérlőprogram frissítve lett egy forráseszközön. Ez lehet egy engedélyezett tevékenység, például egy tervezett karbantartási eljárás. | Jelentős |
| Foxboro I/A Unauthorized Operation | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Az FTP-bejelentkezés sikertelen | A rendszer sikertelen bejelentkezési kísérletet észlelt egy forráseszközről egy célkiszolgálóra. Ez lehet emberi hiba eredménye, de arra is utalhat, hogy rosszindulatú kísérlet történt a kiszolgáló vagy a rajta található adatok feltörére. | Jelentős |
| A függvénykód jogosulatlan kivételt okozott | A forráseszköz (a főkiszolgáló) kivételt adott vissza a céleszköznek. | Jelentős |
| AZ ANNAK üzenettípusának beállításai | A protokollazonosító által azonosított üzenetbeállítások módosultak a forráseszközön. | Figyelmeztetés |
| A Honeywell belső vezérlőprogramjának verziója megváltozott | A belső vezérlőprogram frissítve lett egy forráseszközön. Ez lehet egy engedélyezett tevékenység, például egy tervezett karbantartási eljárás. | Jelentős |
| Érvénytelen HTTP-kommunikáció | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Internet-hozzáférés észlelhető | A hálózat részeként definiált forráseszköz internetes címekkel kommunikál. A forrás nem jogosult az internetcímekkel való kommunikációra. | Jelentős |
| A Belső vezérlőprogram verziója megváltozott | A belső vezérlőprogram frissítve lett egy forráseszközön. Ez lehet egy engedélyezett tevékenység, például egy tervezett karbantartási eljárás. | Jelentős |
| Modbus-címtartomány megsértése | A főeszköz hozzáférést kért egy új memória-főkiszolgálói memóriacímhez. | Jelentős |
| A Modbus belső vezérlőprogramjának verziója megváltozott | A belső vezérlőprogram frissítve lett egy forráseszközön. Ez lehet egy engedélyezett tevékenység, például egy tervezett karbantartási eljárás. | Jelentős |
| Új tevékenység észlelhető – CIP-osztály | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – CIP-osztályszolgáltatás | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – CIP PCCC-parancs | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – CIP-szimbólum | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – EtherNet/IP I/O-kapcsolat | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – EtherNet/IP Protokoll parancs | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – A RENDSZER EGY ÚJ üzenetkódot észlelt | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – LonTalk-parancskódok | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új portfelderítés | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Figyelmeztetés |
| Új tevékenység észlelhető – LonTalk Hálózati változó | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Ovation Data Request | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Olvasási/írási parancs (AMS-indexcsoport) | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Olvasási/írási parancs (AMS-indexeltolás) | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Jogosulatlan DeltaV üzenettípus | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Jogosulatlan DeltaV ROC-művelet | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Jogosulatlan RPC-üzenettípus | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Jogosulatlan RPC-eljáráshívás | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – AZ AMS protokoll parancsának használata | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – a Következő SICAM-parancs használatával: | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – A Suitelink Protocol parancs használata | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Suitelink Protocol-munkamenetek használata | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Yokogawa VNetIP parancs használata | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Új eszköz észlelhető | A rendszer új forráseszközt észlelt a hálózaton, de nem engedélyezte. | Jelentős |
| Új LLDP-eszközkonfiguráció | A rendszer új forráseszközt észlelt a hálózaton, de nem engedélyezte. | Jelentős |
| Új portfelderítés | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Figyelmeztetés |
| Omron FINS – Jogosulatlan parancs | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Az S7 Plus PLC belső vezérlőprogramja megváltozott | A belső vezérlőprogram frissítve lett egy forráseszközön. Ez lehet egy engedélyezett tevékenység, például egy tervezett karbantartási eljárás. | Jelentős |
| Mintaértékek Üzenettípus-beállítások | A protokollazonosító által azonosított üzenetbeállítások módosultak a forráseszközön. | Figyelmeztetés |
| Érvénytelen integritásvizsgálat gyanúja | A rendszer vizsgálatot észlelt egy DNP3-forráseszközön (outstation). Ez a vizsgálat nem lett engedélyezve a hálózata megtanult forgalmaként. | Jelentős |
| A Computer Link nem engedélyezett parancsa | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Kismérvű |
| Jogosulatlan ABB Totalflow-fájlművelet | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan ABB Totalflow-regisztrálási művelet | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan hozzáférés a Access S7 Data Blockhoz | Egy forráseszköz megkísérelt hozzáférni egy másik eszközön található erőforráshoz. A két eszköz közötti, az erőforráshoz való hozzáférési kísérlet nem lett engedélyezve a hálózati megtanult forgalomként. | Figyelmeztetés |
| Jogosulatlan hozzáférés a Access S7 Plus objektumhoz | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan hozzáférés a Wonderware-címkéhez | Egy forráseszköz megkísérelt hozzáférni egy másik eszközön található erőforráshoz. A két eszköz közötti, az erőforráshoz való hozzáférési kísérlet nem lett engedélyezve a hálózati megtanult forgalomként. | Jelentős |
| Jogosulatlan BACNet-objektum-hozzáférés | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan BACNet-útvonal | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan adatbázis-bejelentkezés | A rendszer bejelentkezési kísérletet észlelt egy forrásoldali ügyfél és a célkiszolgáló között. Az ezen eszközök közötti kommunikáció nem lett engedélyezve a hálózati adatforgalomként. | Jelentős |
| Jogosulatlan adatbázis-művelet | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan emerson ROC-művelet | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Nem engedélyezett GE SRTP-fájlelérés | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Nem engedélyezett GE SRTP protokollparancs | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Nem engedélyezett GE SRTP rendszermemória-művelet | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Nem engedélyezett HTTP-tevékenység | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan HTTP-kiszolgáló | A rendszer jogosulatlan alkalmazást észlelt egy forráseszközön. Az alkalmazást nem engedélyezték tanult alkalmazásként a hálózaton. | Jelentős |
| Jogosulatlan HTTP SOAP-művelet | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan HTTP-felhasználói ügynök | A rendszer jogosulatlan alkalmazást észlelt egy forráseszközön. Az alkalmazást nem engedélyezték tanult alkalmazásként a hálózaton. | Jelentős |
| Jogosulatlan internetkapcsolat észlelhető | A hálózat részeként definiált forráseszköz internetes címekkel kommunikál. A forrás nem jogosult az internetcímekkel való kommunikációra. | Kritikus |
| JogosulatlanUl engedélyezett Melsec-parancs | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan MMS-program-hozzáférés | Egy forráseszköz megkísérelt hozzáférni egy másik eszközön található erőforráshoz. A két eszköz közötti, az erőforráshoz való hozzáférési kísérlet nem lett engedélyezett a hálózati megtanult forgalomként. | Jelentős |
| Jogosulatlan MMS-szolgáltatás | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Nem engedélyezett csoportos/szórásos kapcsolat | A rendszer csoportos küldési/szórási kapcsolatot észlelt egy forráseszköz és más eszközök között. A csoportos/szórásos kommunikáció nem engedélyezett. | Kritikus |
| Jogosulatlan névlekérdezés | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan OPC UA-tevékenység | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan OPC UA-kérés/-válasz | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Felhasználó által megadott szabály jogosulatlan műveletet észlelt | A rendszer forgalmat észlelt két eszköz között. Ez a tevékenység nem engedélyezett egy felhasználó által meghatározott egyéni riasztási szabály alapján. | Jelentős |
| Jogosulatlan PLC-konfiguráció olvasása | A forráseszköz nincs programozási eszközként definiálva, de olvasási/írási műveletet végzett a célvezérlőn. A programozási módosításokat csak a programozási eszközöknek kell végrehajtaniuk. Előfordulhat, hogy egy programozási alkalmazás van telepítve az eszközön. | Figyelmeztetés |
| Jogosulatlan PLC-konfiguráció írása | A forráseszköz egy parancsot küldött a célvezérlő programjának olvasására/írására. Ez a tevékenység korábban nem volt látható. | Jelentős |
| Jogosulatlan PLC-programfeltöltés | A forráseszköz egy parancsot küldött a célvezérlő programjának olvasására/írására. Ez a tevékenység korábban nem volt látható. | Jelentős |
| Jogosulatlan PLC-programozás | A forráseszköz nincs programozási eszközként definiálva, de olvasási/írási műveletet hajtott végre a célvezérlőn. A programozási módosításokat csak a programozási eszközöknek kell végrehajtaniuk. Előfordulhat, hogy egy programozási alkalmazás van telepítve az eszközön. | Kritikus |
| Jogosulatlan Profinet kerettípus | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Nem engedélyezett SAIA S-Bus parancs | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| A Control Function jogosulatlan végrehajtása az S7-es szoftverben | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Felhasználó által meghatározott függvény jogosulatlan végrehajtása a Következőben: S7 | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| JogosulatlanUl engedélyezett Access Az S7 Plus blokk-hozzáférése | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| JogosulatlanÜl engedélyezett a Plus üzemeltetése a 7.-es S7-es szoftverhez | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan SMB-bejelentkezés | A rendszer bejelentkezési kísérletet észlelt egy forrás ügyfél és a célkiszolgáló között. Az ezen eszközök közötti kommunikáció nem lett engedélyezve a hálózati adatforgalomként. | Jelentős |
| Jogosulatlan SNMP-művelet | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan SSH-hozzáférés | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan Windows-folyamat | A rendszer jogosulatlan alkalmazást észlelt egy forráseszközön. Az alkalmazást nem engedélyezték tanult alkalmazásként a hálózaton. | Jelentős |
| Jogosulatlan Windows-szolgáltatás | A rendszer jogosulatlan alkalmazást észlelt egy forráseszközön. Az alkalmazást nem engedélyezték tanult alkalmazásként a hálózaton. | Jelentős |
| A felhasználó által megadott szabály jogosulatlan műveletet észlelt | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció megsérti a felhasználó által megadott szabályt | Jelentős |
| Ki nem bocsátott ModbusRel elektromos bővítmény | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| ASDU-típusok nem véglegesített használata | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| A DNP3 függvénykód nem véglegesített használata | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |
| Belső jelzés (IIN) nem véglegesített használata | A DNP3-forráseszköz (outstation) belső jelzést (IIN) jelentett, amely nem hitelesítette a hálózati megtanult forgalmat. | Jelentős |
| Modbus-függvénykód nem véglegesített használata | A rendszer új forgalmi paramétereket észlelt. Ez a paraméterkombináció nem lett engedélyezve a hálózat megtanult forgalmaként. Az alábbi kombináció nem engedélyezett. | Jelentős |

## <a name="anomaly-engine-alerts"></a>Anomáliamotor-riasztások

Az anomáliamotor-riasztások a hálózati tevékenységben észlelt anomáliákat írják le.

| Cím | Leírás | Súlyosság |
|--|--|--|
| Rendellenes kivételminta a következőben: Slave | A rendszer túl sok hibát észlelt egy forráseszközön. Ez egy működési probléma eredménye lehet. | Kismérvű |
| RENDELLENES HTTP-fejléchossz | A forráseszköz rendellenes üzenetet küldött. Ez a céleszköz elleni támadásra utalhat. | Kritikus |
| Paraméterek rendellenes száma a HTTP-fejlécben | A forráseszköz rendellenes üzenetet küldött. Ez a céleszköz elleni támadásra utalhat. | Kritikus |
| Rendellenes rendszeres viselkedés a kommunikációs csatornán | A rendszer változást észlelt a forrás- és céleszközök közötti kommunikáció gyakoriságában. | Kismérvű |
| Alkalmazások rendellenes megszakítása | A rendszer túl sok leállítási parancsot észlelt a forráseszközön. Ez egy működési probléma vagy az eszköz manipulálására tett kísérlet eredménye lehet. | Jelentős |
| Rendellenes forgalom sávszélessége | Rendellenes sávszélesség észlelhető egy csatornán. A sávszélesség a korábban észleltnél jelentősen alacsonyabbnak/magasabbnak tűnik. Részletekért használja a Teljes sávszélesség widgetet. | Figyelmeztetés |
| Rendellenes forgalom sávszélessége az eszközök között | Rendellenes sávszélesség észlelhető egy csatornán. A sávszélesség a korábban észleltnél jelentősen alacsonyabbnak/magasabbnak tűnik. Részletekért használja a Teljes sávszélesség widgetet. | Figyelmeztetés |
| Címvizsgálat észlelhető | A rendszer egy forráseszközt észlelt a hálózati eszközök vizsgálatához. Ez az eszköz nincs engedélyezve hálózatvizsgálati eszközként. | Kritikus |
| ARP-címvizsgálat észlelhető | A rendszer egy forráseszközt észlelt, amely a hálózati eszközöket az Address Resolution Protocol (ARP) protokoll használatával átvizsgálja. Ez az eszközcím nem lett érvényes ARP-ellenőrzési címként engedélyezve. | Kritikus |
| ARP-címvizsgálat észlelhető | A rendszer egy forráseszközt észlelt, amely a hálózati eszközöket az Address Resolution Protocol (ARP) protokoll használatával átvizsgálja. Ez az eszközcím nem lett érvényes ARP-ellenőrzési címként engedélyezve. | Kritikus |
| ARP-hamisítás | A rendszer rendellenes mennyiségű csomagot észlelt a hálózatban. Ez egy támadásra utalhat, például ARP-hamisításra vagy ICMP-elárasztásos támadásra. | Figyelmeztetés |
| Túlzott bejelentkezési kísérletek | A rendszer azt látta, hogy egy forráseszköz túl sok bejelentkezési kísérletet tett a célkiszolgálóra. Ez találgatásos támadás lehet. Előfordulhat, hogy egy rosszindulatú szereplő feltöri a kiszolgálót. | Kritikus |
| A munkamenetek túlzott száma | A rendszer egy forráseszközt látott, amely túl sok bejelentkezési kísérletet tett a célkiszolgálóra. Ez találgatásos támadás lehet. Előfordulhat, hogy egy rosszindulatú szereplő feltöri a kiszolgálót. | Kritikus |
| Túlzott újraindítási arány egy indításakor | A rendszer túl sok újraindítási parancsot észlelt a forráseszközön. Ez egy működési probléma vagy az eszköz manipulálására tett kísérlet eredménye lehet. | Jelentős |
| Túlzott SMB-bejelentkezési kísérletek | A rendszer egy forráseszközt látott, amely túl sok bejelentkezési kísérletet tett a célkiszolgálóra. Ez találgatásos támadás lehet. Előfordulhat, hogy egy rosszindulatú szereplő feltöri a kiszolgálót. | Kritikus |
| ICMP-elárasztás | A rendszer rendellenes mennyiségű csomagot észlelt a hálózatban. Ez egy támadásra utalhat, például egy ARP-hamisításra vagy ICMP-elárasztásos támadásra. | Figyelmeztetés |
| Érvénytelen HTTP-fejléc tartalma | A forráseszköz érvénytelen kérést kezdeményezett. | Kritikus |
| Inaktív kommunikációs csatorna | A két eszköz közötti kommunikációs csatorna inaktív volt egy olyan időszakban, amelyben általában látható a tevékenység. Ez azt jelezheti, hogy a forgalmat előállító program módosult, vagy a program nem érhető el. Javasoljuk, hogy tekintse át a telepített program konfigurációját, és ellenőrizze, hogy megfelelően van-e konfigurálva. | Figyelmeztetés |
| Hosszú időtartamú címvizsgálatot észleltünk | A rendszer egy forráseszközt észlelt a hálózati eszközök vizsgálatához. Ez az eszköz nincs engedélyezve hálózatvizsgálati eszközként. | Kritikus |
| Jelszó-találgatási kísérlet észlelhető | A rendszer azt látta, hogy egy forráseszköz túl sok bejelentkezési kísérletet tett a célkiszolgálóra. Ez találgatásos támadás lehet. Előfordulhat, hogy egy rosszindulatú szereplő feltöri a kiszolgálót. | Kritikus |
| PLC-vizsgálat észlelhető | A rendszer egy forráseszközt észlelt a hálózati eszközök vizsgálatához. Ez az eszköz nincs engedélyezve hálózatvizsgálati eszközként. | Kritikus |
| Portvizsgálat észlelhető | A rendszer egy forráseszközt észlelt a hálózati eszközök vizsgálatához. Ez az eszköz nincs engedélyezve hálózatvizsgálati eszközként. | Kritikus |
| Váratlan üzenethossz | A forráseszköz rendellenes üzenetet küldött. Ez a céleszköz elleni támadásra utalhat. | Kritikus |
| Váratlan forgalom a standard porthoz | A rendszer egy másik protokoll számára fenntartott portot használó eszközön észlelte a forgalmat. | Jelentős |

## <a name="protocol-violation-engine-alerts"></a>Protokollsértési motor riasztásai

A protokollmotor-riasztások a csomagstruktúra vagy a mezőértékek észlelt eltérését írják le a protokollsokajátságokkal összehasonlítva.

| Cím | Leírás | Súlyosság |
|--|--|--|
| Túlzottan helytelenül formázott csomagok egyetlen munkamenetben | A forráseszközről a céleszközre küldött helytelenül formázott csomagok rendellenes száma. Ez téves kommunikációra vagy a megcélzott eszköz manipulálására tett kísérletre utalhat. | Jelentős |
| Belsővezérlőprogram-frissítés | A forráseszköz egy parancsot küldött a céleszköz belső vezérlőprogramjának frissítésére. Ellenőrizze, hogy a céleszközön végzett legutóbbi programozás, konfiguráció és belsővezérlőprogram-frissítések érvényesek-e. | Figyelmeztetés |
| Az outstation által nem támogatott függvénykód | A céleszköz érvénytelen kérelmet kapott. | Jelentős |
| Érvénytelen BACNet-üzenet | A forráseszköz érvénytelen kérést kezdeményezett. | Jelentős |
| Érvénytelen csatlakozási kísérlet a 0-s porton | Egy forráseszköz a nulla számú (0) porton próbált csatlakozni a céleszközhöz. A TCP esetében a 0 port le van foglalva, és nem használható. UDP esetén a port nem kötelező, a 0 érték pedig azt jelenti, hogy nincs port. Általában nincs szolgáltatás olyan rendszeren, amely a 0-s portot figyeli. Ez az esemény a céleszköz támadási kísérletére utalhat, vagy jelezheti, hogy egy alkalmazás helytelenül lett programozva. | Kismérvű |
| Érvénytelen DNP3-művelet | A forráseszköz érvénytelen kérelmet kezdeményezett. | Jelentős |
| Érvénytelen MODBUS-művelet (a főkiszolgáló által okozott kivétel) | A forráseszköz érvénytelen kérelmet kezdeményezett. | Jelentős |
| Érvénytelen MODBUS-művelet (nulla függvénykód) | A forráseszköz érvénytelen kérést kezdeményezett. | Jelentős |
| Érvénytelen protokollverzió | A forráseszköz érvénytelen kérést kezdeményezett. | Jelentős |
| Helytelen paraméter elküldve a következőnek: Outstation | A céleszköz érvénytelen kérelmet kapott. | Jelentős |
| Elavult függvénykód kezdeményezése (adatok inicializálása) | A forráseszköz érvénytelen kérést kezdeményezett. | Kismérvű |
| Elavult függvénykód kezdeményezése (Konfiguráció mentése) | A forráseszköz érvénytelen kérést kezdeményezett. | Kismérvű |
| Főkiszolgáló alkalmazásréteg-megerősítést kért | A forráseszköz érvénytelen kérést kezdeményezett. | Figyelmeztetés |
| Modbus-kivétel | A forráseszköz (a főkiszolgáló) kivételt adott vissza a céleszköznek. | Jelentős |
| Érvénytelen ASDU-típust kapott a következő típusú visszacsatotos eszköz: | A céleszköz érvénytelen kérelmet kapott. | Jelentős |
| A slave device nem megengedett parancs miatt kapott átvitelt | A céleszköz érvénytelen kérelmet kapott. | Jelentős |
| Érvénytelenül kapott visszacsatott eszköz – gyakori cím | A céleszköz érvénytelen kérelmet kapott. | Jelentős |
| Érvénytelen adatcímparamétert kapott a leszolgátott eszköz | A céleszköz érvénytelen kérelmet kapott. | Jelentős |
| Érvénytelen adatérték paramétert kapott a leszolgátott eszköz | A céleszköz érvénytelen kérelmet kapott. | Jelentős |
| Érvénytelen függvénykódot kapott a leszolgaolt eszköz | A céleszköz érvénytelen kérelmet kapott. | Jelentős |
| Érvénytelen információs objektumcímet kapott az eszközhöz | A céleszköz érvénytelen kérelmet kapott. | Jelentős |
| Ismeretlen objektum elküldve az Outstationnek | A céleszköz érvénytelen kérelmet kapott. | Jelentős |
| Fenntartott függvénykód használata | A forráseszköz érvénytelen kérelmet kezdeményezett. | Jelentős |
| Nem megfelelő formázás használata az outstation használatával | A forráseszköz érvénytelen kérelmet kezdeményezett. | Figyelmeztetés |
| Fenntartott állapotjelzők (IIN) használata | A DNP3-forráseszköz (outstation) a fenntartott Belső jelző 2.6-ot használta. Javasoljuk, hogy ellenőrizze az eszköz konfigurációját. | Figyelmeztetés |

## <a name="malware-engine-alerts"></a>Kártevőmotor-riasztások

A kártevőmotor-riasztások az észlelt kártékony hálózati tevékenységeket írják le.

| Cím | Leírás| Súlyosság |
|--|--|--|
| Kapcsolódási kísérlet ismert kártékony IP-címekhez | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Jelentős |
| Érvénytelen SMB-üzenet (DoublePulsar Backdoor Fog) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| Rosszindulatú tartománynév-kérés | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Jelentős |
| Kártevőtesztfájl észlelhető – EICAR AV sikeres | A rendszer egy EICAR AV tesztfájlt észlelt a két eszköz közötti forgalomban. A fájl nem kártevő. Ezzel lehet megerősíteni, hogy a víruskereső szoftver megfelelően van-e telepítve; bemutatja, mi történik, ha vírust talál, és ellenőrzi a belső eljárásokat és a víruskeresési reakciót. A víruskereső szoftvernek úgy kell észlelnie az EICAR-t, mintha valódi vírus lenne. | Jelentős |
| Conficker kártevők gyanúja | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Jelentős |
| Szolgáltatásmegtagadási támadás gyanúja | Egy forráseszköz túl sok új kapcsolatot próbált meg kezdeményezni a céleszközhöz. Ez lehet egy szolgáltatásmegtagadási (DOS) támadás a céleszköz ellen, amely megszakíthatja az eszköz működését, befolyásolhatja a teljesítményt és a szolgáltatás rendelkezésre állását, vagy kijavíthatatlan hibákat okozhat. | Kritikus |
| Rosszindulatú tevékenység gyanúja | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Jelentős |
| Rosszindulatú tevékenység gyanúja (BlackEnergy) | Gyanús hálózati tevékenységet észleltünk. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| Rosszindulatú tevékenység gyanúja (DarkComet) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| Rosszindulatú tevékenység gyanúja (Duqu) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| Rosszindulatú tevékenység gyanúja (Microsoft) | Gyanús hálózati tevékenységet észleltünk. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| Rosszindulatú tevékenység gyanúja (Havex) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| Rosszindulatú tevékenység gyanúja (Foggany) | Gyanús hálózati tevékenységet észleltünk. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| Rosszindulatú tevékenység gyanúja (LightsOut) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| Rosszindulatú tevékenység gyanúja (névlekérdezések) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Jelentős |
| Rosszindulatú tevékenység gyanúja (kártevő ikalom) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| Rosszindulatú tevékenység gyanúja (újragondolás) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| Rosszindulatú tevékenység gyanúja (Stuxnet) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| Rosszindulatú tevékenység gyanúja (WannaCry) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Jelentős |
| NotPetya kártevő gyanúja – A rendszer érvénytelen SMB-paramétereket észlelt | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| NotPetya kártevő gyanúja – A rendszer érvénytelen SMB-tranzakciót észlelt | Gyanús hálózati tevékenységet észleltünk. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |
| A psexec távoli kódvégrehajtás gyanúja | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Jelentős |
| A Távoli Windows-szolgáltatásfelügyelet gyanúja | Gyanús hálózati tevékenységet észleltünk. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Jelentős |
| Gyanús végrehajtható fájl észlelhető a végponton | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Jelentős |
| Gyanús forgalom észlelhető | Gyanús hálózati tevékenységet észleltünk. Ez a tevékenység az ismert kártevők által használt módszert kihasználó támadással társítható. | Kritikus |

## <a name="operational-engine-alerts"></a>Működési motor riasztásai

Az operatív motor riasztásai az észlelt működési incidenseket vagy hibás entitásokat írják le.

| Cím | Leírás | Súlyosság |
|--|--|--|
| S7 Stop PLC parancs lett elküldve | A forráseszköz leállítási parancsot küldött a célvezérlőnek. A vezérlő mindaddig nem működik tovább, amíg el nem küld egy start parancsot. | Figyelmeztetés |
| A BACNet-művelet nem sikerült | A kiszolgáló hibakódot adott vissza. Ez kiszolgálói hibát vagy az ügyfél érvénytelen kérését jelzi. | Jelentős |
| Rossz MMS-eszközállapot | Egy MMS virtuális gyártóeszköz (VMD) állapotüzenetet küldött. Az üzenet azt jelzi, hogy előfordulhat, hogy a kiszolgáló nincs megfelelően konfigurálva, részben működőképes, vagy egyáltalán nem működik. | Jelentős |
| Eszközkonfiguráció módosítása | A rendszer konfigurációs változást észlelt egy forráseszközön. | Kismérvű |
| Folyamatos eseménypuffer túlcsordulása üzemen | A rendszer puffertúlcsordulási eseményt észlelt egy forráseszközön. Az esemény adatsérülést, program-összeomlást vagy rosszindulatú kód végrehajtását okozhatja. | Jelentős |
| Vezérlő alaphelyzetbe állítása | A forráseszköz egy visszaállítási parancsot küldött a célvezérlőnek. A vezérlő ideiglenesen leállt, és automatikusan újraindult. | Figyelmeztetés |
| Vezérlő leállítása | A forráseszköz leállítási parancsot küldött a célvezérlőnek. A vezérlő mindaddig nem működik tovább, amíg el nem küld egy start parancsot. | Figyelmeztetés |
| Az eszköz nem tudott dinamikus IP-címet kapni | A forráseszköz úgy van konfigurálva, hogy dinamikus IP-címet fogad egy DHCP-kiszolgálótól, de nem kapott címet. Ez konfigurációs hibát vagy a DHCP-kiszolgálón észlelt működési hibát jelez. Javasoljuk, hogy értesítse a hálózati rendszergazdát az incidensről | Jelentős |
| Az eszköz leválasztottnak gyanítható (nem válaszol) | A forráseszköz nem válaszolt a neki küldött parancsra. Lehet, hogy a parancs el lett küldve. | Jelentős |
| Az EtherNet/IP CIP-szolgáltatáskérés meghiúsult | A kiszolgáló hibakódot adott vissza. Ez kiszolgálói hibát vagy az ügyfél érvénytelen kérését jelzi. | Jelentős |
| Az EtherNet/IP Encapsulation Protocol parancs meghiúsult | A kiszolgáló hibakódot adott vissza. Ez kiszolgálói hibát vagy az ügyfél érvénytelen kérését jelzi. | Jelentős |
| Eseménypuffer túlcsordulása az outstationben | Puffertúlcsordulási eseményt észleltünk egy forráseszközön. Az esemény adatsérülést, program-összeomlást vagy rosszindulatú kód végrehajtását okozhatja. | Jelentős |
| A várt biztonsági mentési művelet nem történt meg | A várt biztonsági mentési/fájlátviteli tevékenység nem történt meg két eszköz között. Ez a biztonsági mentési/fájlátviteli folyamat hibáit jelezheti. | Jelentős |
| GE SRTP-parancshiba | A kiszolgáló hibakódot adott vissza. Ez kiszolgálói hibát vagy az ügyfél érvénytelen kérését jelzi. | Jelentős |
| A GE SRTP Stop PLC parancs el lett küldve | A forráseszköz leállítási parancsot küldött a célvezérlőnek. A vezérlő mindaddig nem működik tovább, amíg el nem küld egy start parancsot. | Figyelmeztetés |
| A VEZÉRLŐELEM-blokk további konfigurálást igényel | A forráseszköz EGY OLYAN ÜZENETET küldött, amely jelzi, hogy az eszközt be kell fogni. Ez azt jelenti, hogy a JELENT vezérlőblokk további konfigurálást igényel, a PEDIG RÉSZBEN vagy teljesen nem működőképes. | Jelentős |
| A RENDSZER módosította az adatkészlet konfigurációját | A protokollazonosító által azonosított üzenet adatkészlete módosult egy forráseszközön. Ez azt jelenti, hogy az eszköz egy másik adatkészletet fog jelenteni ehhez az üzenethez. | Figyelmeztetés |
| Honeywell Controller Unexpected Status | Egy Honeywell-vezérlő váratlan diagnosztikai üzenetet küldött, amely állapotváltozást jelez. | Figyelmeztetés |
| HTTP-ügyfélhiba | A forráseszköz érvénytelen kérést kezdeményezett. | Figyelmeztetés |
| Érvénytelen IP-cím | A rendszer forgalmat észlelt a forráseszköz és az IP-cím között, amely érvénytelen cím. Ez helytelen konfigurációra vagy érvénytelen forgalom generálási kísérletére utalhat. | Kismérvű |
| Master-Slave hitelesítési hiba | A DNP3-forráseszköz (főeszköz) és a céleszköz (állomás) közötti hitelesítési folyamat sikertelen volt. | Kismérvű |
| Az MMS-szolgáltatáskérés meghiúsult | A kiszolgáló hibakódot adott vissza. Ez kiszolgálói hibát vagy az ügyfél érvénytelen kérését jelzi. | Jelentős |
| Nem észlelhető forgalom az érzékelő felületén | Egy érzékelő már nem észleli a hálózati forgalmat a hálózati adapteren. | Kritikus |
| Az OPC UA-kiszolgáló olyan eseményt adott ki, amely felhasználói beavatkozást igényel | Egy OPC UA-kiszolgáló eseményértesítést küldött az ügyfélnek. Az ilyen típusú esemény felhasználói beavatkozást igényel | Jelentős |
| Az OPC UA-szolgáltatáskérés meghiúsult | A kiszolgáló hibakódot adott vissza. Ez kiszolgálói hibát vagy az ügyfél érvénytelen kérését jelzi. | Jelentős |
| Outstation Restarted | A rendszer hideg újraindítást észlelt egy forráseszközön. Ez azt jelenti, hogy az eszköz fizikailag ki volt kapcsolva, majd újra be volt kapcsolva. | Figyelmeztetés |
| Az állomás-újraindítások gyakoriak | A rendszer túl sok hideg újraindítást észlelt a forráseszközön. Ez azt jelenti, hogy az eszköz fizikailag ki volt kapcsolva, majd túl sokszor volt újra bekapcsolva. | Kismérvű |
| Az outstation konfigurációja megváltozott | A rendszer konfigurációs változást észlelt egy forráseszközön. | Jelentős |
| Az outstation sérült konfigurációja észlelhető | Ez a DNP3-forráseszköz (outstation) sérült konfigurációt jelentett. | Jelentős |
| A Profinet DCP-parancs nem sikerült | A kiszolgáló hibakódot adott vissza. Ez kiszolgálói hibát vagy az ügyfél érvénytelen kérését jelzi. | Jelentős |
| Profinet Device Factory Reset | A forráseszköz gyári beállítások visszaállítása parancsot küldött egy Profinet céleszközre. Az alaphelyzetbe állítási parancs törli a Profinet eszközkonfigurációit, és leállítja annak működését. | Figyelmeztetés |
| Az RPC-művelet nem sikerült | A kiszolgáló hibakódot adott vissza. Ez kiszolgálói hibát vagy az ügyfél érvénytelen kérését jelzi. | Jelentős |
| Mintaértékek Üzenet adatkészlet-konfigurációja módosult | A protokollazonosító által azonosított üzenet adatkészlete módosult egy forráseszközön. Ez azt jelenti, hogy az eszköz egy másik adatkészletet fog jelenteni ehhez az üzenethez. | Figyelmeztetés |
| A nem állítható vissza vissza nem állítható eszköz hibája | A rendszer egy nem állítható vissza állapotú hibát észlelt egy forráseszközön. Ez a hiba általában hardverhiba vagy egy adott parancs végrehajtása sikertelenségére utal. | Jelentős |
| Hardverhibák gyanúja az outstationben | A rendszer egy nem állítható vissza állapotú hibát észlelt egy forráseszközön. Ez a hiba általában hardverhiba vagy egy adott parancs végrehajtása sikertelenségére utal. | Jelentős |
| Nem válaszoló MODBUS-eszköz gyanúja | A forráseszköz nem válaszolt a neki küldött parancsra. Lehet, hogy a parancs el lett küldve. | Kismérvű |
| Forgalom észlelhető az érzékelő felületén | Az érzékelő folytatja a hálózati forgalom észlelését egy hálózati adapteren. | Figyelmeztetés |

## <a name="next-steps"></a>Következő lépések

A [riasztási eseményeket kezelheti.](how-to-manage-the-alert-event.md)
Ismerje meg, hogyan [továbbíthatóak a riasztási információk.](how-to-forward-alert-information-to-partners.md)
