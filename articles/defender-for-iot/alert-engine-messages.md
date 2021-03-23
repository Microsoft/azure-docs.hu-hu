---
title: A riasztás típusai és leírása
description: Tekintse át a Defender IoT-riasztások leírását.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/22/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 3c4cc5f7bb9f0c529e603b91ee96c6c1c476f20d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787544"
---
# <a name="defender-for-iot-engine-alerts"></a>IoT-motorral kapcsolatos riasztások

Ez a cikk azokat a riasztásokat ismerteti, amelyek a Defender for IoT Engines szolgáltatásban hozhatók létre. A riasztások a riasztások ablakában jelennek meg, ahol kezelheti a riasztási eseményt. 

## <a name="policy-engine-alerts"></a>Házirend-végrehajtó riasztások

A házirend-végrehajtó riasztások a megismert alaphálózati viselkedéstől való eltéréseket írják le.

| Cím  | Leírás | Súlyosság |
|--|--|--|
| MAC-címek rendellenes használata | A rendszer új forrásoldali eszközt észlelt a hálózaton, de nem engedélyezte. | Kismérvű |
| Beckhoff szoftver módosítva | A belső vezérlőprogram frissítése egy forrásoldali eszközön történt. Ez engedélyezhető tevékenység lehet, például egy tervezett karbantartási eljárás. | Jelentős |
| Az adatbázis-bejelentkezés sikertelen | A rendszer sikertelen bejelentkezési kísérletet észlelt egy forrásoldali eszközről a célkiszolgálóra. Ez az emberi hiba eredménye lehet, de arra is utalhat, hogy rosszindulatú kísérletet tett a kiszolgáló vagy az adatai sérülésére. | Jelentős |
| Emerson ROC belső vezérlőprogram verziója módosítva | A belső vezérlőprogram frissítése egy forrásoldali eszközön történt. Ez engedélyezhető tevékenység lehet, például egy tervezett karbantartási eljárás. | Jelentős |
| Az internettel kommunikáló hálózaton belüli külső címe | A hálózat részeként definiált forrásoldali eszközök internetes címekkel kommunikálnak. A forrás nem rendelkezik jogosultsággal az internetes címekkel való kommunikációhoz. | Kritikus |
| A mező eszköz váratlanul fel lett derítve | A rendszer új forrásoldali eszközt észlelt a hálózaton, de nem engedélyezte. | Jelentős |
| A belső vezérlőprogram módosítása észlelhető | A belső vezérlőprogram frissítése egy forrásoldali eszközön történt. Ez engedélyezhető tevékenység lehet, például egy tervezett karbantartási eljárás. | Jelentős |
| A belső vezérlőprogram verziója megváltozott | A belső vezérlőprogram frissítése egy forrásoldali eszközön történt. Ez engedélyezhető tevékenység lehet, például egy tervezett karbantartási eljárás. | Jelentős |
| Foxboro I/A jogosulatlan művelet | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Az FTP-bejelentkezés sikertelen | A rendszer sikertelen bejelentkezési kísérletet észlelt egy forrásoldali eszközről a célkiszolgálóra. Ez az emberi hiba eredménye lehet, de arra is utalhat, hogy rosszindulatú kísérletet tett a kiszolgáló vagy az adatai sérülésére. | Jelentős |
| A függvény kódja jogosulatlan kivételt okozott. | Egy forrásoldali eszköz (slave) kivételt adott vissza egy céleszköz (Master) számára. | Jelentős |
| LIBA-üzenet típusának beállításai | Egy forrásoldali eszközön módosult az üzenet (a protokoll azonosítója alapján azonosítva). | Figyelmeztetés |
| A Honeywell belső vezérlőprogram-verziója megváltozott | A belső vezérlőprogram frissítése egy forrásoldali eszközön történt. Ez engedélyezhető tevékenység lehet, például egy tervezett karbantartási eljárás. | Jelentős |
| Szabálytalan HTTP-kommunikáció | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Az Internet-hozzáférés észlelve | A hálózat részeként definiált forrásoldali eszközök internetes címekkel kommunikálnak. A forrás nem rendelkezik jogosultsággal az internetes címekkel való kommunikációhoz. | Jelentős |
| A Mitsubishi belső vezérlőprogram verziója megváltozott | A belső vezérlőprogram frissítése egy forrásoldali eszközön történt. Ez engedélyezhető tevékenység lehet, például egy tervezett karbantartási eljárás. | Jelentős |
| Modbus-címtartomány megsértése | A Főeszköz hozzáférést kért egy új alárendelt memória-címnek. | Jelentős |
| A Modbus belső vezérlőprogram-verziója megváltozott | A belső vezérlőprogram frissítése egy forrásoldali eszközön történt. Ez engedélyezhető tevékenység lehet, például egy tervezett karbantartási eljárás. | Jelentős |
| Új tevékenység észlelhető – CIP-osztály | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – CIP-osztály szolgáltatás | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – CIP PCCC parancs | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – CIP-szimbólum | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – EtherNet/IP I/O-kapcsolatok | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – EtherNet/IP protokoll parancs | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – GSM-üzenet kódja | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – LonTalk parancs kódja | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új port felderítése | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Figyelmeztetés |
| Új tevékenység észlelhető – LonTalk hálózati változó | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Ovation-adatkérés | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – olvasás/írás parancs (AMS-index csoport) | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – olvasási/írási parancs (AMS-index eltolása) | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – jogosulatlan DeltaV-üzenet típusa | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – jogosulatlan DeltaV ROC-művelet | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelve – jogosulatlan RPC-üzenet típusa | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – jogosulatlan RPC-eljárás meghívása | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – AMS protokoll parancs használatával | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – a Siemens SICAM parancs használatával | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Suitelink protokoll parancs használatával | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – Suitelink protokoll-munkamenetek használata | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új tevékenység észlelhető – a Yokogawa VNetIP parancs használatával | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Új eszköz észlelhető | A rendszer új forrásoldali eszközt észlelt a hálózaton, de nem engedélyezte. | Jelentős |
| Új LLDP-eszköz konfigurációja | A rendszer új forrásoldali eszközt észlelt a hálózaton, de nem engedélyezte. | Jelentős |
| Új port felderítése | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Figyelmeztetés |
| Omron-USZONYok jogosulatlan parancsa | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| S7 plusz PLC belső vezérlőprogram megváltoztatva | A belső vezérlőprogram frissítése egy forrásoldali eszközön történt. Ez engedélyezhető tevékenység lehet, például egy tervezett karbantartási eljárás. | Jelentős |
| Mintául szolgáló értékek üzenet típusának beállításai | Egy forrásoldali eszközön módosult az üzenet (a protokoll azonosítója alapján azonosítva). | Figyelmeztetés |
| Szabálytalan integritás-ellenőrzés gyanúja | A rendszer vizsgálatot észlelt egy DNP3-forrás eszközön (az állomáson). Ez a vizsgálat nem volt engedélyezve a hálózatban megtanult forgalomnak. | Jelentős |
| Toshiba számítógép nem engedélyezett parancsának csatolása | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Kismérvű |
| Nem engedélyezett ABB Totalflow-fájl művelete | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Nem engedélyezett ABB Totalflow-regisztrálási művelet | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan hozzáférés a Siemens S7 adatblokkhoz | Egy forrásoldali eszköz megkísérelt hozzáférni egy erőforráshoz egy másik eszközön. Ez a két eszköz nem engedélyezte az erőforráshoz való hozzáférési kísérletet a hálózaton. | Figyelmeztetés |
| Jogosulatlan hozzáférés a Siemens S7 Plus objektumhoz | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan hozzáférés a Wonderware címkéhez | Egy forrásoldali eszköz megkísérelt hozzáférni egy erőforráshoz egy másik eszközön. Ez a két eszköz nem engedélyezte az erőforráshoz való hozzáférési kísérletet a hálózaton. | Jelentős |
| Jogosulatlan BACNet objektum-hozzáférés | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan BACNet útvonal | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan adatbázis-bejelentkezés | A rendszer bejelentkezési kísérletet észlelt a forrás-ügyfél és a célkiszolgáló között. Az eszközök közötti kommunikációt nem engedélyezték a hálózatán észlelt forgalomnak. | Jelentős |
| Jogosulatlan adatbázis-művelet | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan Emerson ROC-művelet | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan GE-SRTP fájlokhoz való hozzáférése | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan GE SRTP protokoll parancsa | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Nem engedélyezett a GE SRTP rendszermemória-művelete | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan HTTP-tevékenység | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan HTTP-kiszolgáló | A rendszer jogosulatlan alkalmazást észlelt egy forrásoldali eszközön. Az alkalmazás nem lett engedélyezve a hálózatban megtanult alkalmazásként. | Jelentős |
| Nem engedélyezett HTTP SOAP-művelet | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan HTTP-felhasználói ügynök | A rendszer jogosulatlan alkalmazást észlelt egy forrásoldali eszközön. Az alkalmazás nem lett engedélyezve a hálózatban megtanult alkalmazásként. | Jelentős |
| Jogosulatlan internetkapcsolat észlelhető | A hálózat részeként definiált forrásoldali eszközök internetes címekkel kommunikálnak. A forrás nem rendelkezik jogosultsággal az internetes címekkel való kommunikációhoz. | Kritikus |
| Nem engedélyezett Mitsubishi MELSEC parancs | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan MMS-programhoz való hozzáférés | Egy forrásoldali eszköz megkísérelt hozzáférni egy erőforráshoz egy másik eszközön. Ez a két eszköz nem engedélyezte az erőforráshoz való hozzáférési kísérletet a hálózaton. | Jelentős |
| Jogosulatlan MMS-szolgáltatás | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan csoportos küldés/szórásos kapcsolatok | A rendszer csoportos/szórásos kapcsolatot észlelt egy forrás-eszköz és más eszközök között. A csoportos küldés/szórásos kommunikáció nincs engedélyezve. | Kritikus |
| Jogosulatlan név lekérdezése | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan OPC UA-tevékenység | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan OPC UA-kérelem/-válasz | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Egy felhasználó által megadott szabály nem engedélyezett műveletet észlelt | A rendszer forgalmat észlelt két eszköz között. Ez a tevékenység nem engedélyezett a felhasználó által definiált egyéni riasztási szabályok alapján. | Jelentős |
| Nem engedélyezett PLC-konfiguráció olvasása | A forrásoldali eszköz nem programozási eszközként van definiálva, de olvasási/írási műveletet hajtott végre egy célként megadott vezérlőn. A programozási módosításokat csak a programozási eszközök hajtják végre. Lehetséges, hogy már telepítve van egy programozási alkalmazás az eszközön. | Figyelmeztetés |
| Nem engedélyezett PLC-konfiguráció írása | A forrásoldali eszköz eljuttatott egy parancsot a cél-vezérlő programjának olvasásához/írásához. Ez a tevékenység korábban nem volt látható. | Jelentős |
| Jogosulatlan PLC-program feltöltése | A forrásoldali eszköz eljuttatott egy parancsot a cél-vezérlő programjának olvasásához/írásához. Ez a tevékenység korábban nem volt látható. | Jelentős |
| Jogosulatlan PLC-programozás | A forrásoldali eszköz nem programozási eszközként van definiálva, de olvasási/írási műveletet hajtott végre egy célként megadott vezérlőn. A programozási módosításokat csak a programozási eszközök hajtják végre. Lehetséges, hogy már telepítve van egy programozási alkalmazás az eszközön. | Kritikus |
| Jogosulatlan Profinet-keret típusa | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan SAIA S-Bus parancs | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Az ellenőrző funkció nem engedélyezett a Siemens S7-ben való végrehajtásakor | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Felhasználó által definiált függvény nem engedélyezett a Siemens S7-ben való végrehajtásakor | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan Siemens S7 és blokkolási hozzáférés | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan Siemens S7 plusz művelet | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan SMB-bejelentkezés | A rendszer bejelentkezési kísérletet észlelt a forrás-ügyfél és a célkiszolgáló között. Az eszközök közötti kommunikációt nem engedélyezték a hálózatán észlelt forgalomnak. | Jelentős |
| Nem engedélyezett SNMP-művelet | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Jogosulatlan SSH-hozzáférés | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| Nem engedélyezett Windows-folyamat | A rendszer jogosulatlan alkalmazást észlelt egy forrásoldali eszközön. Az alkalmazás nem lett engedélyezve a hálózatban megtanult alkalmazásként. | Jelentős |
| Nem engedélyezett Windows-szolgáltatás | A rendszer jogosulatlan alkalmazást észlelt egy forrásoldali eszközön. Az alkalmazás nem lett engedélyezve a hálózatban megtanult alkalmazásként. | Jelentős |
| Egy felhasználó által megadott szabály nem engedélyezett műveletet észlelt | A rendszer új forgalmi paramétereket észlelt. Ez a paraméter-kombináció sérti a felhasználó által megadott szabályt. | Jelentős |
| Nem engedélyezett a Modbus Schneider elektromos bővítmény | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| ASDU-típusok nem engedélyezett használata | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| A DNP3 függvény kódjának nem engedélyezett használata | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |
| A belső jelzés nem engedélyezett használata (AA) | A DNP3 forrásoldali eszköz (külső) olyan belső jelzést jelentett (AA), amely nem volt engedélyezve a hálózatán észlelt forgalomnak. | Jelentős |
| A Modbus függvény kódjának nem engedélyezett használata | A rendszer új forgalmi paramétereket észlelt. Ezt a paraméter-kombinációt nem engedélyezték a hálózatban megtanult forgalomnak. A következő kombináció nem engedélyezett. | Jelentős |

## <a name="anomaly-engine-alerts"></a>Anomália-motor riasztásai

| Cím | Leírás | Súlyosság |
|--|--|--|
| Rendellenes kivételi minta a Slave-ben | Egy forrásoldali eszközön túl sok hibát észlelt a rendszer. Ez egy működési probléma eredménye lehet. | Kismérvű |
| Rendellenes HTTP-fejléc hossza | A forrásoldali eszköz rendellenes üzenetet küldött. Ez a cél eszköz támadására tett kísérletre utalhat. | Kritikus |
| A HTTP-fejlécben szereplő paraméterek rendellenes száma | A forrásoldali eszköz rendellenes üzenetet küldött. Ez a cél eszköz támadására tett kísérletre utalhat. | Kritikus |
| Rendellenes rendszeres viselkedés a kommunikációs csatornában | A rendszer a forrás és a cél eszközök közötti kommunikáció gyakoriságának változását észlelte. | Kismérvű |
| Alkalmazások rendellenes leállítása | Egy forrásoldali eszközön túl sok leállítási parancs észlelhető. Ennek oka lehet egy működési probléma, vagy az eszköz módosítására tett kísérlet. | Jelentős |
| Rendellenes forgalmi sávszélesség | A rendszer rendellenes sávszélességet észlelt egy csatornán. Úgy tűnik, hogy a sávszélesség jóval alacsonyabb vagy magasabb, mint a korábban észlelt. A részletekért a teljes sávszélesség widgettel dolgozhat. | Figyelmeztetés |
| Rendellenes forgalmi sávszélesség az eszközök között | A rendszer rendellenes sávszélességet észlelt egy csatornán. Úgy tűnik, hogy a sávszélesség jóval alacsonyabb vagy magasabb, mint a korábban észlelt. A részletekért a teljes sávszélesség widgettel dolgozhat. | Figyelmeztetés |
| A címek vizsgálata észlelhető | A rendszer egy forrásoldali eszközt észlelt A hálózati eszközök vizsgálatakor. Ez az eszköz nem lett engedélyezve hálózati ellenőrzési eszközként. | Kritikus |
| Az ARP-címek vizsgálata észlelhető | A rendszer egy forrásoldali eszközt észlelt a hálózati eszközök vizsgálatára az ARP protokoll használatával. Ez az eszköz címe nincs engedélyezve érvényes ARP-ellenőrzési címként. | Kritikus |
| Az ARP-címek vizsgálata észlelhető | A rendszer egy forrásoldali eszközt észlelt a hálózati eszközök vizsgálatára az ARP protokoll használatával. Ez az eszköz címe nincs engedélyezve érvényes ARP-ellenőrzési címként. | Kritikus |
| ARP-hamisítás | A hálózatban rendellenes mennyiségű csomagot észlelt a rendszer. Ez egy támadásra utalhat, például egy ARP-hamisítás vagy egy ICMP-árvíz elleni támadás. | Figyelmeztetés |
| Túlzott bejelentkezési kísérletek | Egy forrásoldali eszköz túlzott bejelentkezési kísérleteket hajtott végre a célkiszolgálón. Ez egy találgatásos támadás lehet. A kiszolgálót rosszindulatú színész is veszélyeztetheti. | Kritikus |
| Sok munkamenet | Egy forrásoldali eszköz túlzott bejelentkezési kísérleteket hajtott végre a célkiszolgálón. Ez egy találgatásos támadás lehet. A kiszolgálót rosszindulatú színész is veszélyeztetheti. | Kritikus |
| Egy állomás túlzott újraindítási sebessége | Egy forrásoldali eszközön túl sok újraindítási parancs észlelhető. Ennek oka lehet egy működési probléma, vagy az eszköz módosítására tett kísérlet. | Jelentős |
| Túlzott SMB bejelentkezési kísérletek | Egy forrásoldali eszköz túlzott bejelentkezési kísérleteket hajtott végre a célkiszolgálón. Ez egy találgatásos támadás lehet. A kiszolgálót rosszindulatú színész is veszélyeztetheti. | Kritikus |
| ICMP-elárasztás | A hálózatban rendellenes mennyiségű csomagot észlelt a rendszer. Ez egy támadásra utalhat, például egy ARP-hamisítás vagy egy ICMP-árvíz elleni támadás. | Figyelmeztetés |
| Szabálytalan HTTP-fejléc tartalma | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Kritikus |
| Inaktív kommunikációs csatorna | A két eszköz közötti kommunikációs csatorna inaktív volt egy olyan időszakban, amelyben a tevékenység általában látható. Ez arra utalhat, hogy a forgalmat generáló program módosult, vagy a program nem érhető el. Javasoljuk, hogy tekintse át a telepített program konfigurációját, és ellenőrizze, hogy megfelelően van-e konfigurálva. | Figyelmeztetés |
| Hosszú időtartamú címek vizsgálata észlelhető | A rendszer egy forrásoldali eszközt észlelt A hálózati eszközök vizsgálatakor. Ez az eszköz nem lett engedélyezve hálózati ellenőrzési eszközként. | Kritikus |
| Jelszó-találgatásos kísérlet észlelve | Egy forrásoldali eszköz túlzott bejelentkezési kísérleteket hajtott végre a célkiszolgálón. Ez egy találgatásos támadás lehet. A kiszolgálót rosszindulatú színész is veszélyeztetheti. | Kritikus |
| PLC-vizsgálat észlelhető | A rendszer egy forrásoldali eszközt észlelt A hálózati eszközök vizsgálatakor. Ez az eszköz nem lett engedélyezve hálózati ellenőrzési eszközként. | Kritikus |
| A port vizsgálata észlelve | A rendszer egy forrásoldali eszközt észlelt A hálózati eszközök vizsgálatakor. Ez az eszköz nem lett engedélyezve hálózati ellenőrzési eszközként. | Kritikus |
| Nem várt üzenet hossza | A forrásoldali eszköz rendellenes üzenetet küldött. Ez a cél eszköz támadására tett kísérletre utalhat. | Kritikus |
| Váratlan forgalom a standard porthoz | A rendszer forgalmat észlelt egy eszközön egy másik protokoll számára fenntartott port használatával. | Jelentős |

## <a name="protocol-violation-engine-alerts"></a>Protokoll-megsértési motor riasztásai

| Cím | Leírás | Súlyosság |
|--|--|--|
| Túlzottan helytelenül formázott csomagok egyetlen munkamenetben | Rendellenes számú helytelenül formázott csomag érkezik a forrásoldali eszközről a céleszköz. Ez hibás kommunikációt, vagy a megcélzó eszköz kezelésének kísérletét jelezheti. | Jelentős |
| Belsővezérlőprogram-frissítés | Egy forrásoldali eszköz eljuttatott egy parancsot a belső vezérlőprogram frissítéséhez egy céleszköz. Győződjön meg arról, hogy a célként megadott eszközön végzett legutóbbi programozási, konfigurációs és belső vezérlőprogram-frissítések érvényesek. | Figyelmeztetés |
| Az állomás nem támogatja a függvény kódját | A céleszköz érvénytelen kérést kapott. | Jelentős |
| Érvénytelen BACNet-üzenet | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Jelentős |
| Érvénytelen csatlakozási kísérlet a 0. porton | A forrásoldali eszköz a nulla (0) portszámon próbált meg csatlakozni a célként megadott eszközhöz. A TCP esetében a 0. port foglalt, ezért nem használható. Az UDP esetében a port nem kötelező, és a 0 érték azt jelenti, hogy nincs port. Általában nincs olyan rendszer, amely a 0. porton figyeli a szolgáltatást. Ez az esemény arra utalhat, hogy kísérletet tesz a céleszköz támadására, vagy azt jelzi, hogy egy alkalmazás helytelenül lett beprogramozva. | Kismérvű |
| Szabálytalan DNP3 művelet | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Jelentős |
| Szabálytalan MODBUS művelet (a főkiszolgáló által kiváltott kivétel) | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Jelentős |
| Szabálytalan MODBUS művelet (hibakód nulla) | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Jelentős |
| Érvénytelen protokoll-verzió | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Jelentős |
| Helytelen paraméter lett elküldve a kiállomásnak | A céleszköz érvénytelen kérést kapott. | Jelentős |
| Elavult kód kezdeményezése (az adatinicializálási művelet) | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Kismérvű |
| Elavult kód kezdeményezése (konfiguráció mentése) | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Kismérvű |
| A főkiszolgáló kérelmezte az alkalmazás rétegének megerősítését | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Figyelmeztetés |
| Modbus kivétel | Egy forrásoldali eszköz (slave) kivételt adott vissza egy céleszköz (Master) számára. | Jelentős |
| A Slave eszköz nem engedélyezett ASDU-típust kapott | A céleszköz érvénytelen kérést kapott. | Jelentős |
| A Slave eszköz az átvitelhez nem engedélyezett parancsot kapott | A céleszköz érvénytelen kérést kapott. | Jelentős |
| A Slave-eszköz szabálytalan közös címeket kapott | A céleszköz érvénytelen kérést kapott. | Jelentős |
| A Slave eszköz érvénytelen adatcímes paramétert kapott | A céleszköz érvénytelen kérést kapott. | Jelentős |
| A Slave eszköz érvénytelen adatérték-paramétert kapott. | A céleszköz érvénytelen kérést kapott. | Jelentős |
| A Slave eszköz szabálytalan kódot kapott | A céleszköz érvénytelen kérést kapott. | Jelentős |
| A Slave eszköz érvénytelen információ-objektum-címeket kapott | A céleszköz érvénytelen kérést kapott. | Jelentős |
| Ismeretlen objektum küldve a kifelé | A céleszköz érvénytelen kérést kapott. | Jelentős |
| Fenntartott függvény kódjának használata | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Jelentős |
| Helytelen formázás használata az állomáson | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Figyelmeztetés |
| Fenntartott állapot jelzői (AA) használata | Egy DNP3 forrásoldali eszköz (külső) a fenntartott belső 2,6-as kijelzőt használta. Javasoljuk, hogy vizsgálja meg az eszköz konfigurációját. | Figyelmeztetés |

## <a name="malware-engine-alerts"></a>Kártevő-végrehajtó riasztások

| Cím | Leírás| Súlyosság |
|--|--|--|
| Az ismert kártékony IP-címhez való kapcsolódási kísérlet | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Jelentős |
| Érvénytelen SMB-üzenet (DoublePulsar Backdoor implantátum) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| Rosszindulatú tartománynév kérése | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Jelentős |
| Kártevő-tesztelési fájl észlelhető – EICAR AV-sikeres | A rendszer EICAR AV-teszt fájlt észlelt két eszköz közötti forgalomban. A fájl nem kártevő szoftver. Annak ellenőrzésére szolgál, hogy a víruskereső szoftver megfelelően van-e telepítve; bemutatjuk, hogy mi történik vírus keresésekor, valamint a belső eljárások és a reakciók ellenőrzése a vírus megtalálása esetén. A víruskereső szoftvernek úgy kell felderíteni a EICAR, mintha valódi vírus lenne. | Jelentős |
| A Conficker kártevő gyanúja | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Jelentős |
| Szolgáltatásmegtagadási támadás gyanúja | Egy forrásoldali eszköz túl sok új kapcsolatot próbált kezdeményezni egy céleszköz használatával. Ez szolgáltatásmegtagadást okozhat a céleszköz ellen, és megszakíthatja az eszközök működését, hatással lehet a teljesítményre és a szolgáltatás rendelkezésre állására, vagy helyreállíthatatlan hibákat okozhat. | Kritikus |
| Rosszindulatú tevékenység gyanúja | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Jelentős |
| Rosszindulatú tevékenység gyanúja (BlackEnergy) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| Rosszindulatú tevékenység gyanúja (DarkComet) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| Rosszindulatú tevékenység gyanúja (Duqu) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| Rosszindulatú tevékenység gyanúja (láng) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| Rosszindulatú tevékenység gyanúja (Havex) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| Rosszindulatú tevékenység gyanúja (Karagany) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| Rosszindulatú tevékenység gyanúja (LightsOut) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| Rosszindulatú tevékenység gyanúja (nevek lekérdezése) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Jelentős |
| Rosszindulatú tevékenység gyanúja (Poison Ivy) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| Rosszindulatú tevékenység gyanúja | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| Rosszindulatú tevékenység gyanúja (Stuxnet) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| Rosszindulatú tevékenység gyanúja (WannaCry) | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Jelentős |
| NotPetya kártevők gyanúja – a rendszer szabálytalan SMB-paramétereket észlelt | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| NotPetya kártevők gyanúja – a rendszer szabálytalan SMB-tranzakciót észlelt | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |
| A PsExec-mel történő távoli kódfuttatást gyanúja | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Jelentős |
| A távoli Windows-szolgáltatások felügyeletének gyanúja | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Jelentős |
| A rendszer gyanús végrehajtható fájlt észlelt a végponton | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Jelentős |
| Gyanús forgalom észlelhető | A rendszer gyanús hálózati tevékenységet észlelt. Ez a tevékenység olyan támadással társítható, amely egy ismert kártevő által használt metódust használ fel. | Kritikus |

## <a name="operational-engine-alerts"></a>Operatív motorral kapcsolatos riasztások

| Cím | Leírás | Súlyosság |
|--|--|--|
| Az S7 stop PLC-parancs küldése megtörtént | A forrásoldali eszköz leállítási parancsot küldtünk egy célhely vezérlőnek. A vezérlő nem fog működni, amíg el nem küld egy indítási parancsot. | Figyelmeztetés |
| Sikertelen volt a BACNet művelet | A kiszolgáló hibakódot adott vissza. Ez egy kiszolgálóhiba vagy egy ügyfél érvénytelen kérelmét jelzi. | Jelentős |
| Helytelen MMS-eszköz állapota | Egy MMS-beli virtuális gyártó eszköz (VMD) elküldött egy állapotjelző üzenetet. Az üzenet azt jelzi, hogy előfordulhat, hogy a kiszolgáló nem megfelelően van konfigurálva, részben vagy egyáltalán nem működik. | Jelentős |
| Az eszköz konfigurációjának módosítása | A rendszer konfigurációs változást észlelt egy forrásoldali eszközön. | Kismérvű |
| Folyamatos esemény pufferének túlcsordulása az állomáson | A rendszer puffer túlcsordulási eseményt észlelt egy forrásoldali eszközön. Az esemény okozhatja az adatsérülést, a program összeomlását vagy a kártékony kódok végrehajtását. | Jelentős |
| Vezérlő alaphelyzetbe állítása | A forrásoldali eszköz alaphelyzetbe állítási parancsot adott vissza a célhely vezérlője számára. A vezérlő átmenetileg leállt, és automatikusan elindult. | Figyelmeztetés |
| Vezérlő leállítása | A forrásoldali eszköz leállítási parancsot küldtünk egy célhely vezérlőnek. A vezérlő nem fog működni, amíg el nem küld egy indítási parancsot. | Figyelmeztetés |
| Az eszköz nem tudott dinamikus IP-címet fogadni | A forrásoldali eszköz úgy van konfigurálva, hogy dinamikus IP-címet kapjon egy DHCP-kiszolgálóról, de nem kapott címet. Ez az eszköz konfigurációs hibáját vagy működési hibát jelez a DHCP-kiszolgálón. Javasoljuk, hogy értesítse az incidens hálózati rendszergazdáját | Jelentős |
| Az eszköz le van választva (nem válaszol) | Egy forrásoldali eszköz nem válaszolt a neki küldött parancsra. Előfordulhat, hogy a parancs elküldésekor megszakadt a kapcsolat. | Jelentős |
| Az EtherNet/IP CIP szolgáltatás kérése sikertelen volt. | A kiszolgáló hibakódot adott vissza. Ez egy kiszolgálóhiba vagy egy ügyfél érvénytelen kérelmét jelzi. | Jelentős |
| Nem sikerült az EtherNet/IP-beágyazási protokoll parancsa | A kiszolgáló hibakódot adott vissza. Ez egy kiszolgálóhiba vagy egy ügyfél érvénytelen kérelmét jelzi. | Jelentős |
| Az esemény pufferének túlcsordulása az állomáson | A rendszer puffer túlcsordulási eseményt észlelt egy forrásoldali eszközön. Az esemény okozhatja az adatsérülést, a program összeomlását vagy a kártékony kódok végrehajtását. | Jelentős |
| A várt biztonsági mentési művelet nem történt meg | A várt biztonsági mentési/fájlátviteli tevékenység két eszköz között nem történt meg. Ez hibákat jelezhet a biztonsági mentési/fájlátviteli folyamat során. | Jelentős |
| A GE SRTP parancsának hibája | A kiszolgáló hibakódot adott vissza. Ez egy kiszolgálóhiba vagy egy ügyfél érvénytelen kérelmét jelzi. | Jelentős |
| A GE SRTP stop PLC-parancs küldése megérkezett | A forrásoldali eszköz leállítási parancsot küldtünk egy célhely vezérlőnek. A vezérlő nem fog működni, amíg el nem küld egy indítási parancsot. | Figyelmeztetés |
| A liba-vezérlési blokk további konfigurálást igényel | A forrásoldali eszköz egy liba-üzenetet küldött, amely azt jelzi, hogy az eszköznek szüksége van az üzembe helyezésre. Ez azt jelenti, hogy a liba-ellenőrző blokk további konfigurálást igényel, és a liba üzenetei részben vagy teljesen nem működnek. | Jelentős |
| A liba-adatkészlet konfigurációja módosult | Egy forrásoldali eszközön módosult egy üzenet (a protokoll azonosítója alapján azonosítva) adatkészlet. Ez azt jelenti, hogy az eszköz egy másik adatkészletet fog jelenteni ehhez az üzenethez. | Figyelmeztetés |
| Honeywell-vezérlő – nem várt állapot | Egy Honeywell-vezérlő váratlan diagnosztikai üzenetet küldött az állapot változásának jelzésére. | Figyelmeztetés |
| HTTP-ügyfél hibája | A forrásoldali eszköz érvénytelen kérelmet kezdeményezett. | Figyelmeztetés |
| Érvénytelen IP-cím | A rendszer észlelte a forgalmat egy forrásoldali eszköz és az IP-cím között, amely érvénytelen cím. Ez helytelen konfigurációt vagy a tiltott forgalom előállítására irányuló kísérletet jelezhet. | Kismérvű |
| Hitelesítési hiba Master-Slave | A DNP3 forrásoldali eszköz (főkiszolgáló) és a célként megadott eszköz (állomás) közötti hitelesítési folyamat sikertelen volt. | Kismérvű |
| Nem sikerült az MMS-szolgáltatás kérése | A kiszolgáló hibakódot adott vissza. Ez egy kiszolgálóhiba vagy egy ügyfél érvénytelen kérelmét jelzi. | Jelentős |
| Nem észlelhető forgalom az érzékelő felületen | Egy érzékelő leállított egy hálózati adapter hálózati forgalmának észlelését. | Kritikus |
| Az OPC UA-kiszolgáló olyan eseményt váltott ki, amely felhasználói beavatkozást igényel | Az OPC UA-kiszolgáló eseményt küldött az ügyfélnek. Az ilyen típusú eseményekhez felhasználói beavatkozás szükséges | Jelentős |
| Az OPC UA szolgáltatási kérelem sikertelen | A kiszolgáló hibakódot adott vissza. Ez egy kiszolgálóhiba vagy egy ügyfél érvénytelen kérelmét jelzi. | Jelentős |
| Az állomás újraindítása megkezdődött | A rendszer hideg újraindítást észlelt egy forrásoldali eszközön. Ez azt jelenti, hogy az eszköz fizikailag ki van kapcsolva, és újra visszatért. | Figyelmeztetés |
| Az állomások újraindítása gyakran | Egy forrásoldali eszközön túl sok hideg újraindítást észlelt a rendszer. Ez azt jelenti, hogy az eszköz fizikailag ki van kapcsolva, és újra visszakerül a túlzott számú alkalommal. | Kismérvű |
| Az állomás konfigurációjának változása | A rendszer konfigurációs változást észlelt egy forrásoldali eszközön. | Jelentős |
| Az állomás sérült konfigurációját észlelte a rendszer | Ez a DNP3-forrás eszköz (adó) sérült konfigurációt jelentett. | Jelentős |
| Nem sikerült a Profinet DCP parancs végrehajtása | A kiszolgáló hibakódot adott vissza. Ez egy kiszolgálóhiba vagy egy ügyfél érvénytelen kérelmét jelzi. | Jelentős |
| Profinet-eszköz gyári alaphelyzetbe állítása | A forrásoldali eszköz gyári beállítások visszaállítása parancsot adott vissza egy Profinet. Az Alaphelyzetbe állítás parancs törli a Profinet-eszköz konfigurációit, és leállítja a műveletet. | Figyelmeztetés |
| Sikertelen RPC-művelet | A kiszolgáló hibakódot adott vissza. Ez egy kiszolgálóhiba vagy egy ügyfél érvénytelen kérelmét jelzi. | Jelentős |
| A mintavételi értékek üzenetének adatkészlet-konfigurációja módosult | Egy forrásoldali eszközön módosult egy üzenet (a protokoll azonosítója alapján azonosítva) adatkészlet. Ez azt jelenti, hogy az eszköz egy másik adatkészletet fog jelenteni ehhez az üzenethez. | Figyelmeztetés |
| Helyreállíthatatlan hiba történt a Slave eszközön | Helyreállíthatatlan feltételi hiba észlelhető egy forrásoldali eszközön. Az ilyen típusú hibák általában hardverhiba miatt vagy egy adott parancs végrehajtásának sikertelenségét jelzik. | Jelentős |
| A hardverrel kapcsolatos problémák gyanúja az állomáson | Helyreállíthatatlan feltételi hiba észlelhető egy forrásoldali eszközön. Az ilyen típusú hibák általában hardverhiba miatt vagy egy adott parancs végrehajtásának sikertelenségét jelzik. | Jelentős |
| Nem válaszoló MODBUS-eszköz gyanúja | Egy forrásoldali eszköz nem válaszolt a neki küldött parancsra. Előfordulhat, hogy a parancs elküldésekor megszakadt a kapcsolat. | Kismérvű |
| Az érzékelő felületen észlelt forgalom | Egy érzékelő folytatja a hálózati forgalom észlelését egy hálózati adapteren. | Figyelmeztetés |

## <a name="next-steps"></a>Következő lépések

A [riasztási eseményeket kezelheti](how-to-manage-the-alert-event.md).
Tudnivalók a [riasztási információk továbbításáról](how-to-forward-alert-information-to-partners.md).
