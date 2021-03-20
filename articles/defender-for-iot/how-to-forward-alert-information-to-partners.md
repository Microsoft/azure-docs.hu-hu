---
title: Riasztási információk továbbítása
description: Riasztási információkat küldhet a partneri rendszereknek a továbbítási szabályokkal való együttműködéssel.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0b71f7ca3f812de1514612f8b0dd5915f3f81bc4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100522733"
---
# <a name="forward-alert-information"></a>Riasztási információk továbbítása

Riasztási információkat küldhet olyan partnereknek, akik integrálják az Azure Defender for IoT, a syslog-kiszolgálókat és az e-mail-címeket, és így tovább. A továbbítási szabályok használata lehetővé teszi a riasztási információk gyors kézbesítését a biztonsági érintett felek számára.  

A rendszernapló és az egyéb alapértelmezett továbbítási műveletek a rendszerbe kerülnek. Több továbbítási művelet is elérhetővé válhat, ha integrálni szeretné a partner-szállítókkal, például a Microsoft Azure Sentinel, a ServiceNow vagy a splunk szolgáltatással.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="Riasztási információk.":::

A IoT-rendszergazdák számára készült Defender jogosult a továbbítási szabályok használatára.

## <a name="about-forwarded-alert-information"></a>A továbbított riasztásokkal kapcsolatos információk

A riasztások részletesen ismertetik a biztonsági és működési események széles körét. Például:

  - A riasztás dátuma és időpontja

  - Az eseményt észlelő motor

  - Riasztás címe és leíró üzenet

  - A riasztás súlyossága

  - Forrás és cél neve és IP-címe

  - Gyanús forgalom észlelhető

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="A rendszer a címek vizsgálatát észlelte.":::

A rendszer elküldi a kapcsolódó információkat a partnerek rendszereinek a továbbítási szabályok létrehozásakor.

## <a name="create-forwarding-rules"></a>Továbbítási szabályok létrehozása

Új továbbítási szabály létrehozása:

1. Válassza a **továbbítás** lehetőséget az oldalsó menüben.

   :: képtípus = "tartalom" forrás = "média/útmutató – munka-with-riasztások – érzékelő/create-forwarding-rule-screen.png" Alt-text = "továbbító szabály létrehozása ikon.":::

2. Válassza a **továbbítási szabály létrehozása** lehetőséget.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="Hozzon létre egy új továbbítási szabályt.":::

3. Adja meg a továbbítási szabály nevét.

### <a name="forwarding-rule-criteria"></a>Továbbítási szabály feltételei 

Adja meg a továbbítási szabály elindításához használandó feltételeket. A továbbítási szabály feltételeinek használata segít az érzékelőtől a külső rendszereknek továbbított adatok mennyiségének meghatározásában és kezelésében. A következő lehetőségek érhetők el:

**Protokollok**: csak akkor aktiválja a továbbítási szabályt, ha a észlelt forgalom meghatározott protokollokon keresztül futott. Válassza ki a szükséges protokollokat a legördülő listából, vagy válassza ki őket.

**Motorok**: válassza ki a szükséges motorokat, vagy válassza ki őket. A rendszer elküldi a kiválasztott motorok riasztásait.

**Súlyossági szintek**: Ez a minimálisan szükséges incidens a súlyossági szint szempontjából. Ha például a **kisebb**, a másodlagos riasztások és az ezen súlyossági szint feletti riasztások lehetőséget választja, továbbítja a rendszer. A szintek előre definiálva vannak.

### <a name="forwarding-rule-actions"></a>Szabály műveleteinek továbbítása

A továbbítási szabály műveletei arra utasítja az érzékelőt, hogy továbbítsa a riasztási információkat a partnerek szállítóinak vagy kiszolgálóinak. Az egyes továbbítási szabályokhoz több műveletet is létrehozhat.

A rendszerrel együtt továbbított továbbítási műveletek mellett más műveletek is elérhetővé válhatnak a partner-szállítókkal való integráció során. 

#### <a name="email-address-action"></a>E-mail cím művelet

A riasztási adatokat tartalmazó e-mail küldése. Minden szabályhoz megadhat egy e-mail címet.

E-mail megadása a továbbítási szabályhoz:

1. Adjon meg egyetlen e-mail-címet. Ha egynél több e-mail küldése szükséges, hozzon létre egy másik műveletet.

2. Adja meg az időbélyegzőt a SIEM-ben a riasztás észleléséhez.

3. Válassza a **Küldés** lehetőséget.

#### <a name="syslog-server-actions"></a>Syslog-kiszolgáló műveletei

A következő formátumok támogatottak:

- SMS-ek

- CEF üzenetek

- LEEF üzenetek

- Objektum üzenetei

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="Továbbító szabály műveleteinek létrehozása.":::

Adja meg a következő paramétereket:

- Syslog-gazdagép neve és portja.

- TCP és UDP protokoll.

- A riasztás észlelésének időbélyegzője az SIEM-ben.

- TLS titkosítási tanúsítvány fájlja és a CEF-kiszolgálók kulcsfájl (nem kötelező).
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="Állítsa be a titkosítást a továbbítási szabályhoz.":::

| Syslog szöveges üzenet kimeneti mezői | Description |
|--|--|
| Dátum és idő | Az a dátum és idő, ameddig a syslog-kiszolgáló számítógépe megkapta az információt. |
| Prioritás | User. Alert |
| Hostname (Gazdanév) | Érzékelő IP-címe |
| Protokoll | TCP vagy UDP |
| Üzenet | Érzékelő: az érzékelő neve.<br /> Riasztás: a riasztás címe.<br /> Típus: a riasztás típusa. A **protokoll megsértését**, a **szabályzat megsértését**, **a kártevőket**, a **rendellenességeket** vagy a **működést** lehet megsérteni.<br /> Súlyosság: a riasztás súlyossága. **Figyelmeztetés**, **kisebb**, **fő** vagy **kritikus** lehet.<br /> Forrás: a forrás eszköz neve.<br /> Forrás IP-cím: a forrásoldali eszköz IP-címe.<br /> Cél: a cél eszköz neve.<br /> Cél IP-cím: a céleszköz IP-címe.<br /> Üzenet: a riasztás üzenete.<br /> Riasztási Csoport: a riasztáshoz társított riasztási csoport. |


| Syslog-objektum kimenete | Description |
|--|--|
| Dátum és idő |   Az a dátum és idő, ameddig a syslog-kiszolgáló számítógépe megkapta az információt. |  
| Prioritás |    User. Alert | 
| Hostname (Gazdanév) |    Érzékelő IP-címe | 
| Üzenet | Érzékelő neve: a készülék neve. <br /> Riasztás időpontja: a riasztás észlelésének időpontja: a syslog-kiszolgáló számítógépének idejétől függően változhat, és a továbbítási szabály időzóna-konfigurációjától függ. <br /> Riasztás címe: a riasztás címe. <br /> Riasztási üzenet: a riasztás üzenete. <br /> Riasztás súlyossága: a riasztás súlyossága: **Figyelmeztetés**, **kisebb**, **fő** vagy **kritikus**. <br /> Riasztás típusa: **protokoll megsértése**, **szabályzat megsértése**, **kártevő**, **rendellenesség** vagy **működési**. <br /> Protokoll: a riasztás protokollja.  <br /> **Source_MAC**: a forrásoldali eszköz IP-címe, neve, szállítója vagy operációs rendszere. <br /> Destination_MAC: a célhely IP-címe, neve, szállítója vagy operációs rendszere. Ha hiányzik az adat, az érték **N/a** lesz. <br /> alert_group: a riasztáshoz társított riasztási csoport. |


| Syslog CEF kimeneti formátuma | Description |
|--|--|
| Dátum és idő | Az a dátum és idő, ameddig a syslog-kiszolgáló számítógépe megkapta az információt. |
| Prioritás | User. Alert | 
| Hostname (Gazdanév) | Érzékelő IP-címe |
| Üzenet | CEF: 0 <br />Azure Defender IoT-hez <br />Érzékelő neve: az érzékelő berendezés neve. <br />Érzékelő verziója <br />Riasztás címe: a riasztás címe. <br />msg: a riasztás üzenete. <br />protokoll: a riasztás protokollja. <br />Súlyosság: **Figyelmeztetés**, **kisebb**, **fő** vagy **kritikus**. <br />típus: **protokoll megsértése**, **szabályzat megsértése**, **kártevő**, **rendellenesség** vagy **működési**. <br /> Kezdés: a riasztás észlelésének időpontja. <br />A syslog-kiszolgáló számítógépének idejétől eltérő lehet, és a továbbítási szabály időzóna-konfigurációjától függ. <br />src_ip: a forrás eszköz IP-címe.  <br />dst_ip: a céleszköz IP-címe.<br />Cat: a riasztáshoz társított riasztási csoport.  |

| Syslog LEEF kimeneti formátuma | Description |
|--|--|
| Dátum és idő |   Az a dátum és idő, ameddig a syslog-kiszolgáló számítógépe megkapta az információt. |  
| Prioritás |    User. Alert | 
| Hostname (Gazdanév) |    Érzékelő IP-címe |
| Üzenet | Érzékelő neve: az Azure Defender IoT-berendezés neve. <br />LEEF: 1.0 <br />Azure Defender IoT-hez <br />Érzékelő  <br />Érzékelő verziója <br />Azure Defender a IoT-riasztáshoz <br />title: a riasztás címe. <br />msg: a riasztás üzenete. <br />protokoll: a riasztás protokollja.<br />Súlyosság: **Figyelmeztetés**, **kisebb**, **fő** vagy **kritikus**. <br />típus: a riasztás típusa: **protokoll megsértése**, **házirend megsértése**, **kártevő**, **rendellenesség** vagy **működési**. <br />indítás: a riasztás időpontja.Vegye figyelembe, hogy a syslog-kiszolgáló számítógépének időpontjától eltérő lehet. (Ez az időzóna-konfigurációtól függ.) <br />src_ip: a forrás eszköz IP-címe.<br />dst_ip: a céleszköz IP-címe. <br />Cat: a riasztáshoz társított riasztási csoport. |

Az összes információ megadása után válassza a **Küldés** lehetőséget.

#### <a name="netwitness-action"></a>NetWitness művelet

Riasztási információk küldése egy NetWitness-kiszolgálónak.

NetWitness-továbbítási paraméterek meghatározása:

1. Adja meg a NetWitness **állomásnév** és **port** információit.

2. Adja meg az időbélyegzőt a SIEM-ben a riasztás észleléséhez.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="Adjon hozzá egy időzónát a továbbítási szabályhoz.":::

3. Válassza a **Küldés** lehetőséget.

#### <a name="integrated-vendor-actions"></a>Integrált szállítói műveletek

Lehetséges, hogy a rendszer biztonsági, eszközkezelés vagy más iparági gyártóval van integrálva. Ezek az integrációk a következőket teszik lehetővé:

  - Riasztási információk küldése.

  - Eszköz leltározási adatainak küldése.

  - Kommunikáció a szállítói oldali tűzfalakkal.

Az integrációk segítenek áthidalni a korábban silózott biztonsági megoldásokat, javítják az eszközök láthatóságát, és felgyorsítják a rendszerszintű választ, hogy gyorsabban csökkentsék a kockázatokat.

A műveletek szakasz segítségével megadhatja a hitelesítő adatokat és az integrált szállítókkal való kommunikációhoz szükséges egyéb információkat.

Az integrációk továbbítási szabályainak beállításával kapcsolatos részletekért tekintse meg a megfelelő partner-integrációs cikkeket.

### <a name="test-forwarding-rules"></a>Tesztelési szabályok továbbítása

Tesztelje a kapcsolatot az érzékelő és a partner-kiszolgáló között, amelyet a továbbítási szabályok határoznak meg:

1. Válassza ki a szabályt a **továbbítási szabály** párbeszédpanelen.

2. Válassza a **továbbiak** mezőt.

3. Válassza a **Tesztüzenet küldése** lehetőséget.

4. Nyissa meg a partneri rendszerét, és ellenőrizze, hogy az érzékelő által küldött információk fogadása megérkezett-e.

### <a name="edit-and-delete-forwarding-rules"></a>Továbbítási szabályok szerkesztése és törlése 

Továbbítási szabály szerkesztése:

- A **továbbítási szabály** képernyőn válassza a **Szerkesztés** lehetőséget a **további** legördülő menüben. Végezze el a kívánt módosításokat, és válassza a **Küldés** lehetőséget.

Továbbítási szabály eltávolítása:

- A **továbbítási szabály** képernyőn válassza az **Eltávolítás** lehetőséget a **további** legördülő menüben. A **Figyelmeztetés** párbeszédpanelen kattintson az **OK gombra**.

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>Továbbítási szabályok és riasztás kizárási szabályai

Lehet, hogy a rendszergazda definiálta a riasztások kizárási szabályait. Ezek a szabályok segítenek a rendszergazdáknak a riasztások aktiválásának részletesebb szabályozásában azáltal, hogy az érzékelőt arra utasítja, hogy a különböző paraméterek alapján figyelmen kívül hagyja a riasztási eseményeket. Ezek a paraméterek lehetnek például az eszközök címei, a riasztások nevei vagy a megadott érzékelők.

Ez azt jelenti, hogy az Ön által definiált továbbítási szabályok figyelmen kívül hagyhatók a rendszergazda által létrehozott kizárási szabályok alapján. A kizárási szabályok a helyszíni felügyeleti konzolon vannak meghatározva.

## <a name="see-also"></a>Lásd még

[Riasztási munkafolyamatok felgyorsítása](how-to-accelerate-alert-incident-response.md)
