---
title: Fenyegetési intelligencia importálása az Azure Sentinelbe | Microsoft Docs
description: Az Azure Sentinelben az adatok elemzéséhez, a fenyegetések észleléséhez, valamint a riasztások és incidensek létrehozásához használjon veszélyforrások elleni intelligencia-hírcsatornákat. A fenyegetések felderítésével kapcsolatos információk megjelenítése a munkafüzetekkel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: 0b2a6088effc735076d56ba83dd85135392147b8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574996"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Fenyegetésfelderítési adatok importálása az Azure Sentinelbe

## <a name="introduction-to-threat-intelligence"></a>A fenyegetések felderítésének bemutatása

A számítógépes veszélyforrások felderítése (CTI) olyan információ, amely a rendszerekre és a felhasználókra vonatkozó ismert meglévő vagy lehetséges fenyegetéseket ismerteti. Az ilyen típusú információk sokféle formában jelennek meg, írásos jelentésekben az adott veszélyforrások, az infrastruktúra és a technikák részletes ismertetését, valamint az IP-címek, tartományok és a számítógépes fenyegetésekhez kapcsolódó fájlkivonat-megfigyelések adott megjegyzéseit. A CTI a szervezetek által a szokatlan tevékenységek alapvető kontextusának biztosítására szolgál, hogy a biztonsági személyzet gyorsan tegyen lépéseket a személyek és az eszközök védelmére. A CTI számos különböző helyről, például nyílt forráskódú Adatcsatornákról, a fenyegetések felderítését megosztó közösségekről, a kereskedelmi intelligencia hírcsatornáról, valamint a szervezeten belüli biztonsági vizsgálatok során összegyűjtött helyi intelligencia forrásait is kihasználhatja.

Egy biztonsági információ-és rendezvényközpont-(SIEM-) megoldáson belül, mint például az Azure Sentinel, a CTI legalkalmasabb formája a fenyegetések mutatói, más néven a kompromisszum vagy a IoCs. A veszélyforrások olyan adatokat társítanak, amelyek az ismert veszélyforrásokkal, például az adathalászattal, a botnetekkel vagy a kártevő szoftverrel kapcsolatos megjegyzéseket, például URL-címeket, fájlkivonat-vagy IP-címeket társítanak. A veszélyforrások felderítésének ezt a formáját gyakran taktikai fenyegetési intelligenciának nevezik, mivel a biztonsági termékekre és az automatizálásra nagy méretekben is alkalmazható, így biztosítva az esetleges fenyegetéseket a szervezet számára. Az Azure Sentinelben fenyegetési mutatók segítségével észlelheti a környezetében megfigyelt kártékony tevékenységeket, és környezeteket biztosíthat a biztonsági nyomozók számára, hogy segítséget nyújtson a válaszadási döntések meghozatalában.

A fenyegetések intelligencia (TI) integrálása az Azure Sentinelbe a következő tevékenységek segítségével végezhető el:

- **Adatösszekötők** használata különböző ti platformokon a [fenyegetési intelligencia](./connect-threat-intelligence.md) Azure sentinelbe való importálásához.
- Megtekintheti és kezelheti az importált fenyegetési intelligenciát a **naplókban** és az Azure Sentinel új **veszélyforrások felderítési** területén.
- A beépített **elemzési** szabálygyűjtemény használatával biztonsági riasztásokat és incidenseket hozhatja ki az importált fenyegetési intelligenciával.
- Jelenítse meg a fenyegetési intelligenciával kapcsolatos legfontosabb információkat az Azure Sentinelben a **fenyegetést ismertető munkafüzettel**.

A fenyegetések felderítése más Azure Sentinel-élményekben, például a **vadászatban** és a **jegyzetfüzetekben** is hasznos környezetet biztosít, és a jelen cikkben nem tárgyalja ezeket a tapasztalatokat [Ian Helló, az Azure Sentinel Jupyter Notebooks](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239)szolgáltatásában, amely a CTI a notebookokon belüli használatát fedi le.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>Az Azure Sentinel adatösszekötői a fenyegetések felderítéséhez

Ugyanúgy, mint az Azure Sentinel összes többi esemény-adatkészlete, az adatösszekötők használatával importálhatja a veszélyforrások mutatóit. Az Azure Sentinel szolgáltatásban két adatösszekötő található, kifejezetten a fenyegetésekre mutató, a **fenyegetések felderítésére szolgáló TAXII** és a **fenyegetések felderítésére szolgáló platformokra**. Az adatösszekötőt vagy a két összekötőt is használhatja, attól függően, hogy hol található a szervezet forrásai a veszélyforrások számára. Beszéljünk minden egyes adatösszekötőről.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Fenyegetésekkel kapcsolatos mutatók hozzáadása az Azure Sentinelhez a Threat Intelligence Platforms adatösszekötővel

Számos szervezet használja a fenyegetéseket kezelő platform (TIP) megoldásait különböző forrásokból származó veszélyforrások összevonására, a platformon belüli információk beszerzésére, majd annak kiválasztására, hogy milyen veszélyforrások vonatkoznak a különböző biztonsági megoldásokra, például a hálózati eszközökre, a komplex veszélyforrások elleni védelmi megoldásokra vagy a SIEM-re, például az Azure Sentinel használatára. Ha a szervezete egy integrált TIP-megoldást használ, például a MICSKÓ, a Anomali ThreatStream, a ThreatConnect, a EclecticIQ platform, a ThreatQ veszélyforrások felderítése platform vagy a Palo Alto Networks MineMeld, a **veszélyforrások intelligencia platformok adatösszekötője** lehetővé teszi, hogy a tippet használja a veszélyforrások az Azure sentinelbe való importálására. Mivel az összekötő együttműködik a [Microsoft Graph Security TIINDICATORS API](/graph/api/resources/tiindicator) -val, az összekötőt bármely egyéni veszélyforrások elleni intelligencia platform használhatja, hogy kihasználja a tiIndicators API-t az Azure Sentinel (és más Microsoft biztonsági megoldások, például a Defender ATP) számára.

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="Fenyegetési intelligencia importálási útvonala":::

Az alábbi lépéseket követve importálhatja a veszélyforrások mutatóit az Azure Sentinelbe az integrált TIP vagy az egyéni veszélyforrások felderítési megoldásával:

1. Alkalmazás-azonosító és ügyfél-titkos kód beszerzése az Azure Active Directory

1. Adja meg ezt az információt a TIP-megoldásban vagy az egyéni alkalmazásban

1. A Threat Intelligence Platforms adatösszekötő engedélyezése az Azure Sentinel szolgáltatásban

Az alábbiakban részletesen megtekintheti ezeket a lépéseket.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Alkalmazás-azonosító és ügyfél-titkos kód beszerzése az Azure Active Directory

Akár TIPPtel, akár egyéni megoldással dolgozik, a tiIndicators API-nak néhány alapvető információt kell megadnia a kapcsolódáshoz és a fenyegetési mutatók küldéséhez. A következő három információt kell megszereznie:
- Alkalmazás (ügyfél) azonosítója
- Címtár (bérlő) azonosítója
- Titkos ügyfélkulcs

Ez az információ mindig a Azure Active Directory származik az **alkalmazás-regisztráció** nevű folyamaton keresztül, amely a következő három lépést tartalmazza:
- Alkalmazás regisztrálása az Azure Active Directoryval
- Az Microsoft Graph tiIndicators API-hoz való kapcsolódáshoz és a veszélyforrások megjelenítéséhez szükséges engedélyek megadása az alkalmazás számára
- Szerezze be a szervezet jóváhagyását, hogy megadja ezeket az engedélyeket az alkalmazásnak.  

**Alkalmazás regisztrálása a Azure Active Directory**

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és navigáljon a **Azure Active Directory** szolgáltatáshoz.

1. Válassza az **alkalmazás-regisztrációk** lehetőséget a menüből, és válassza az **új regisztráció** lehetőséget.

1. Válassza ki az alkalmazás regisztrációjának nevét, válassza az **egybérlős** választógombot, és válassza a **regisztráció** lehetőséget.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="Egy alkalmazás regisztrálása":::

1. Az eredményül kapott képernyőn másolja az **alkalmazás (ügyfél) azonosítóját** és a **könyvtár (bérlő) azonosítójának** értékeit. Ezek az első két információ, amelyekre később szüksége lesz a tipp vagy az egyéni megoldás konfigurálásához, hogy veszélyforrási mutatókat küldjön az Azure Sentinelnek.

**Az alkalmazás által igényelt engedélyek megadása**

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és navigáljon a **Azure Active Directory** szolgáltatáshoz.

1. Válassza az **alkalmazás-regisztrációk** lehetőséget a menüből, és válassza ki az újonnan regisztrált alkalmazást.

1. Válassza az **API-engedélyek** lehetőséget a menüben, majd kattintson az **engedély hozzáadása** gombra.

1. Az **API kiválasztása** lapon válassza ki a **Microsoft Graph** lehetőséget a Microsoft Graph engedélyek listájából való kiválasztásához.

1. Ha a rendszer megkérdezi **, hogy milyen típusú engedélyeket igényel az alkalmazás? válassza az** **alkalmazás engedélyei** lehetőséget. Ez az alkalmazás-AZONOSÍTÓval és az alkalmazás-titkokkal (API-kulcsokkal) hitelesítő alkalmazások által használt engedélyek típusa.

1. Válassza a **ThreatIndicators. ReadWrite. OwnedBy** lehetőséget, majd az **engedélyek hozzáadása** gombra kattintva adja hozzá ezt az engedélyt az alkalmazás engedélyeinek listájához.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="Engedélyek megadása":::

**A szervezet jóváhagyásának beszerzése az engedélyek megadásához**

1. A jóváhagyás megadásához egy Azure Active Directory globális rendszergazdának kell lennie, hogy az alkalmazás API-engedélyeinek lapján válassza a **bérlő gombra vonatkozó rendszergazdai jóváhagyás megadása** lehetőséget. Ha nem rendelkezik a globális rendszergazdai szerepkörrel a fiókjában, akkor ez a gomb nem érhető el, és a feladat elvégzéséhez globális rendszergazdát kell kérnie a szervezettől.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="Hozzájárulás megadása":::

1. Miután beleegyezett az alkalmazásba, egy zöld pipa jelenik meg az **állapot** területen.
 
Most, hogy az alkalmazás regisztrálva van, és engedélyt kapott, az utolsó dolog megjelenik a listán – az alkalmazáshoz tartozó ügyfél-titok.

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és navigáljon a **Azure Active Directory** szolgáltatáshoz.

1. Válassza az **alkalmazás-regisztrációk** lehetőséget a menüből, és válassza ki az újonnan regisztrált alkalmazást.

1. Válassza ki a **tanúsítványok & Secrets** elemet a menüből, majd az **új ügyfél titka** gombra kattintva szerezze be az alkalmazáshoz tartozó titkos kulcsot (API-kulcs).

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="Ügyfél titkos kulcsának beolvasása":::

1. Kattintson a **Hozzáadás** gombra, és **Győződjön meg róla, hogy az ügyfél titkos kulcsát másolja**, mert nem tudja újra beolvasni ezt a titkot, ha az oldalról navigál. Erre az értékre szüksége lesz a tipp vagy az egyéni megoldás konfigurálásakor.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Adja meg ezt az információt a TIP-megoldásban vagy az egyéni alkalmazásban

Most már mindhárom információt be kell állítania a tipp vagy az egyéni megoldás számára, hogy veszélyforrási mutatókat küldjön az Azure Sentinelnek. 
- Alkalmazás (ügyfél) azonosítója
- Címtár (bérlő) azonosítója
- Titkos ügyfélkulcs

Adja meg ezeket az értékeket az integrált tipp vagy az egyéni megoldás konfigurációjában, ahol szükséges, és a fenyegetések mutatóit az Azure Sentinel Microsoft Graph tiIndicators API-n keresztül küldi el a rendszer.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>A Threat Intelligence Platforms adatösszekötő engedélyezése az Azure Sentinel szolgáltatásban

Az integrációs folyamat utolsó lépéseként engedélyezni kell a **veszélyforrások elleni hírszerzési platformok** adatösszekötőjét az Azure Sentinel szolgáltatásban. Ez az a lépés, amely a TIP vagy az egyéni megoldás által a Microsoft Graph tiIndicators API-n keresztül az Azure Sentinelbe érkező veszélyforrások mutatóit importálja. Ezek a mutatók a szervezet összes Azure Sentinel-munkaterülete számára elérhetők lesznek. Az alábbi lépéseket követve engedélyezheti a veszélyforrások felderítése platform adatösszekötőt az egyes munkaterületeken:

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és navigáljon az **Azure Sentinel** szolgáltatáshoz.

1. Válassza ki azt a **munkaterületet** , amelybe importálni kívánja a tipp vagy az egyéni megoldás által eljuttatott veszélyforrások mutatóit.

1. Válassza ki az **adatösszekötők** lehetőséget a menüből, válassza ki a **veszélyforrások felderítése platformok** lehetőséget az összekötők galériából, és kattintson az **összekötő megnyitása lap** gombra.

1. Ahogy már végrehajtotta az alkalmazás regisztrációját, és konfigurálta a TIPPet vagy az egyéni megoldást a veszélyforrások megjelenítésére, az egyetlen lépés a bal oldalon kattintson a **Kapcsolódás** gombra.

Néhány percen belül megkezdődik a veszélyforrások beáramlása ebbe az Azure Sentinel-munkaterületre.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Veszélyforrások mutatóinak hozzáadása az Azure Sentinelhez a fenyegetések felderítése – TAXII adatösszekötővel

A veszélyforrások felderítésének a legszélesebb körben elfogadott iparági szabványa a [Stix adatformátum és a TAXII protokoll kombinációja](https://oasis-open.github.io/cti-documentation/). Ha a szervezet az aktuális STIX/TAXII-verziót (2,0 vagy 2,1) támogató megoldásoktól származó veszélyforrásokat észlel, akkor a **Threat Intelligence-TAXII** adatösszekötővel használhatja a veszélyforrások mutatóit az Azure Sentinel szolgáltatásba. A Threat Intelligence-TAXII adatösszekötője lehetővé teszi, hogy az Azure Sentinelben egy beépített TAXII-ügyfél importálja a fenyegetések felderítését a TAXII 2. x kiszolgálókról.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII importálási útvonala":::
 
A következő lépésekkel importálhatja a STIX formátumú veszélyforrások kijelzőit az Azure Sentinel TAXII-kiszolgálóról:

1. A TAXII Server API-gyökér és-gyűjtemény AZONOSÍTÓjának beszerzése

1. A Threat Intelligence-TAXII adatösszekötő engedélyezése az Azure Sentinel szolgáltatásban

Most tekintsük át részletesen az alábbi lépéseket.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>A TAXII Server API-gyökér és-gyűjtemény AZONOSÍTÓjának beszerzése

A TAXII 2. x kiszolgálók API-gyökereket hirdetnek meg, amelyek a fenyegetések intelligencia gyűjteményeit üzemeltető URL-címek. Leggyakrabban az API gyökerét lehet megszerezni a TAXII-kiszolgálót üzemeltető fenyegetési intelligencia szolgáltatójának dokumentációs oldalán keresztül. Előfordulhat azonban, hogy az egyetlen meghirdetett információ egy felderítési végpontként ismert URL-cím. Ebben az esetben könnyen megtalálhatja az API-gyökeret a felderítési végpont használatával. Ha már ismeri a TAXII-kiszolgáló API-gyökerét és a használni kívánt gyűjtemény-azonosítókat, lépjen tovább a következő szakaszra, és **engedélyezze a Threat Intelligence-TAXII adatösszekötőt az Azure sentinelben**.

Nézzük meg, hogyan használhatja a [curl](https://en.wikipedia.org/wiki/CURL)nevű egyszerű parancssori segédprogramot, amely a Windows és a legtöbb Linux-disztribúcióban található, hogy felderítse az API-gyökeret, és megkeresse a TAXII-kiszolgáló gyűjteményeit, amelyek csak a felderítési végponttól indulnak. Ebben a példában a [Anomali limuzin](https://www.anomali.com/community/limo) ThreatStream TAXII 2,0-kiszolgáló felderítési végpontját fogjuk használni.

1.  Egy böngészőben nyissa meg az [THREATSTREAM TAXII 2,0 Server Discovery végpontot](https://limo.anomali.com/taxii) az API gyökerének lekéréséhez. Hitelesítés a felhasználóval és a jelszóval `guest` .

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  A cURL segédprogram és az API-gyökér használatával (az https://limo.anomali.com/api/v1/taxii2/feeds/) előző lépésből megkeresheti az API-gyökérkönyvtárban tárolt gyűjtemények azonosítóinak listáját.

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Most már rendelkezik az Azure Sentinel egy vagy több, a Anomali limuzin által biztosított TAXII-kiszolgálóval való összekapcsolásához szükséges információval.

| **API-gyökér** (https://limo.anomali.com/api/v1/taxii2/feeds/) | Gyűjtemény azonosítója |
| ------------------------------------------------------------ | ------------: |
| **Adathalászó tartály**                                               | 107           |
| **Abuse.ch ransomware IP-címei**                                  | 135           |
| **Abuse.ch ransomware-tartományok**                              | 136           |
| **DShield-vizsgálati IP-címek**                                     | 150           |
| **Kártevők tartományának listája – gyorslista**                            | 200           |
| **Blutmagie TOR-csomópontok**                                      | 209           |
| **Feltörekvő fenyegetések C&C kiszolgáló**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **Kiberbűnözés elleni**                                               |  41           |
| **Felmerülő fenyegetések – sérült**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>A Threat Intelligence-TAXII adatösszekötő engedélyezése az Azure Sentinel szolgáltatásban

Az alábbi lépéseket követve importálhatja a veszélyforrások kijelzőit az Azure Sentinelbe egy TAXII-kiszolgálóról:

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és navigáljon az **Azure Sentinel** szolgáltatáshoz.

1. Válassza ki azt a **munkaterületet** , amelyre a TAXII-kiszolgálóról szeretné importálni a veszélyforrások mutatóit.

1. Válassza az **adatösszekötők** lehetőséget a menüből, válassza a **veszélyforrások felderítése – TAXII** lehetőséget az összekötők katalógusában, majd kattintson az **összekötő megnyitása lapra** .

1. Adja meg a TAXII-kiszolgáló gyűjteményének **nevét** , az **API-gyökér URL-címét**, a **gyűjtemény azonosítóját**, a **felhasználónevet** (ha szükséges) és a **jelszót** (ha szükséges), majd kattintson a **Hozzáadás** gombra.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="TAXII-kiszolgálók konfigurálása":::
 
Meg kell erősítenie, hogy a TAXII-kiszolgálóval létesített kapcsolat sikeresen létrejött, és a fenti 4. lépés többször is megismételhető, ha több gyűjteményhez szeretne csatlakozni ugyanazon vagy különböző TAXII-kiszolgálókról.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Veszélyforrások mutatóinak megtekintése az Azure Sentinelben

Most, hogy sikeresen importálta a fenyegetési mutatókat az Azure Sentinel-be a **fenyegetések intelligencia-platformok** és/vagy a **Threat Intelligence-TAXII** adatösszekötő használatával, megtekintheti azokat a **naplók** **ThreatIntelligenceIndicator** táblájában, amely az összes Azure Sentinel-esemény adatait tárolja. Ez a táblázat az egyéb Azure Sentinel-funkciók, például az elemzések és a munkafüzetek által végrehajtott lekérdezések alapja. Ebből a témakörből megtudhatja és megtekintheti a fenyegetések mutatóit a **ThreatIntelligenceIndicator** táblában.

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és navigáljon az **Azure Sentinel** szolgáltatáshoz.

1. Válassza ki azt a **munkaterületet** , amelyhez a veszélyforrások felderítésére szolgáló adatösszekötő használatával importálta a veszélyforrások mutatóit.

1. Az Azure Sentinel menü **általános** részén válassza a **naplók** lehetőséget.

1. A **ThreatIntelligenceIndicator** tábla az **Azure Sentinel** csoport alatt található.

1. Válassza az **adatok előnézete** ikont (a szem) a táblázat neve mellett, és válassza a **megtekintés a lekérdezés szerkesztőjében** gombot egy olyan lekérdezés végrehajtásához, amely a tábla rekordjait fogja megjeleníteni.

Az eredményeknek az alább látható mintavételi veszélyforráshoz hasonlóan kell kinéznie:

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="Mintául szolgáló lekérdezési adatgyűjtés":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Az Azure Sentinel új veszélyforrások elleni intelligencia területén kezelheti a veszélyforrások mutatóit

Az új **veszélyforrások elleni intelligencia** területtel, amely az Azure Sentinel menüjéből érhető el, megtekintheti, rendezheti, szűrheti és keresheti meg az importált veszélyforrásokat, anélkül, hogy a **naplók** lekérdezését kellene írnia. Ez az új terület azt is lehetővé teszi, hogy közvetlenül az Azure Sentinel felületén hozza létre a veszélyforrásokat, valamint az olyan általános veszélyforrások elleni felügyeleti feladatokat, mint a mutatók címkézése és a biztonsági vizsgálatokhoz kapcsolódó új mutatók létrehozása.
Nézzük meg a leggyakoribb feladatok közül kettőt, és hozzon létre új veszélyforrásokat és címkézési mutatókat az egyszerű csoportosítás és a hivatkozás érdekében.

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és navigáljon az **Azure Sentinel** szolgáltatáshoz.

1. Válassza ki azt a **munkaterületet** , amelyhez a veszélyforrások felderítésére szolgáló adatösszekötő használatával importálta a veszélyforrások mutatóit.

1. Válassza ki a **veszélyforrások felderítése** lehetőséget az Azure Sentinel menüjének fenyegetések kezelése szakaszából.

1. A lap felső menüjében kattintson az **új hozzáadása** gombra.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="Új veszélyforrások mutatójának hozzáadása" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. Válassza ki a kijelző típusát, majd töltse ki a piros csillaggal (*) jelölt kötelező mezőket az **új kijelző** panelen.

1. Kattintson az **Alkalmaz** gombra. A rendszer hozzáadja a kijelzőt a mutatók rácshoz, és a **naplókban** is elküldi a ThreatIntelligenceIndicator táblának.

A veszélyforrások címkézésével egyszerűen csoportosíthatja őket, így könnyebben megtalálhatja őket. A címkét jellemzően egy adott incidenshez kapcsolódó mutatókra, illetve egy adott ismert szereplő vagy egy jól ismert támadási kampány fenyegetéseit képviselő mutatókra alkalmazhatja. Megcímkézheti a veszélyforrások kijelzőit egyenként, vagy egyszerre több kiválaszthatja a jelölőket, és megcímkézheti őket. Az alábbiakban látható egy példa arra, hogy több mutatót is megcímkéz egy incidens-AZONOSÍTÓval. Mivel a címkézés ingyenes, a javasolt eljárás a fenyegetést jelző címkék szabványos elnevezési konvencióinak létrehozása. Az egyes mutatókhoz több címkét is alkalmazhat.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Címkék alkalmazása a veszélyforrások mutatói számára" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>Az elemzések révén a veszélyforrások a potenciális fenyegetések észlelése érdekében működnek

Az Azure Sentinelbe bekerült a veszélyforrások mutatói; látta, hogyan tekintheti meg és kezelheti őket; most lássuk, mit tehetnek. A fenyegetési mutatók legfontosabb felhasználási esete az olyan SIEM-megoldások esetében, mint az Azure Sentinel, a Power Analytics-szabályok.  Ezek a mutatókon alapuló szabályok összehasonlítják az adatforrásokból származó nyers eseményeket a fenyegetési mutatók alapján a szervezeten belüli biztonsági fenyegetések észlelése érdekében. Az Azure Sentinel **Analyticsben** olyan elemzési szabályokat hozhat létre, amelyek ütemezett alapon futnak, és biztonsági riasztásokat állítanak elő. A szabályokat a lekérdezések vezérlik, valamint olyan konfigurációkat is, amelyek meghatározzák, hogy a szabály milyen gyakran fusson, milyen típusú lekérdezési eredményeket kell létrehoznia a biztonsági riasztások létrehozásához, valamint a riasztások létrehozásakor elindított automatizált válaszokat.

Habár a teljesen új elemzési szabályokat is létrehozhat, az Azure Sentinel a Microsoft biztonsági mérnökök által létrehozott beépített szabálykészlet-sablonokat is tartalmaz, amelyeket az Ön igényei szerint használhat, vagy módosíthatja. Könnyen azonosíthatja a veszélyforrásokat használó szabályok sablonjait, mivel azok a "**ti Map**..." kezdetű címmel vannak ellátva. Ezek a szabályok ugyanúgy működnek, mint az egyetlen különbség, hogy milyen típusú veszélyforrások vannak használatban (tartomány, e-mail, fájlkivonat, IP-cím vagy URL), és hogy melyik eseménytípus kell egyeznie. Minden sablon felsorolja a szabály működéséhez szükséges szükséges adatforrásokat, így egy pillantással megtekintheti, ha rendelkezik az Azure Sentinel szolgáltatásban már importált szükséges eseményekkel.

Vessünk egy pillantást az alábbi sablonok egyikére, és ismerkedjen meg a szabály engedélyezésével és konfigurálásával a biztonsági riasztások létrehozásához az Azure Sentinelbe importált veszélyforrások mutatói alapján. Ebben a példában az **AzureActivity IP-Cím entitás** nevű szabályt használjuk. Ez a szabály minden Azure-beli tevékenységi eseménynek megfelelő IP-cím típusú fenyegetést jelez. Ha egyezést talál, a rendszer **riasztást** hoz létre, valamint a biztonsági operatív csapat által a vizsgálathoz kapcsolódó **incidenst** . Ez az elemzési szabály csak akkor fog működni, ha engedélyezte az egyik vagy mindkét **veszélyforrási intelligencia** adatösszekötőt (a veszélyforrások megjelenítéséhez) és az **Azure Activity** adatösszekötőt (az Azure előfizetési szintű eseményeinek importálásához).

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és navigáljon az **Azure Sentinel** szolgáltatáshoz.

1. Válassza ki azt a **munkaterületet** , amely számára a veszélyforrások **felderítésére** szolgáló adatösszekötők és az Azure-tevékenység adatai az **Azure Activity** adatösszekötő használatával importálta a fenyegetési mutatókat

1. Válassza az **elemzés** lehetőséget az Azure Sentinel menü **konfiguráció** szakaszában.

1. Kattintson a **szabály sablonjai** lapra az elérhető analitikai szabályok sablonjainak megtekintéséhez.

1. Navigáljon a **ti Map IP-entitás AzureActivity** , és győződjön meg arról, hogy az alább látható módon csatlakoztatta az összes szükséges adatforrást.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="Szükséges adatforrások":::

1. Válassza ki ezt a szabályt, és kattintson a **szabály létrehozása** gombra. Ekkor megnyílik egy varázsló a szabály konfigurálásához. Itt hajtsa végre a beállításokat, és válassza a **következő: szabály logikai >ának beállítása** gombot.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="Elemzési szabály létrehozása":::

1. A varázsló szabály logikájának része a következőket tartalmazza:
    - A szabályban használt lekérdezés.

    - Az entitás-hozzárendelések, amelyek azt mondják el, hogy az Azure Sentinel hogyan ismeri fel az entitásokat, például a fiókokat, az IP-címeket és az URL-címeket, így az **incidensek** és a **vizsgálatok** megtudhatják, hogyan dolgozhatnak fel a szabály által létrehozott biztonsági riasztás

    - A szabály futtatásának ütemterve.

    - A biztonsági riasztás létrehozása előtt szükséges lekérdezési eredmények száma.

    A sablon alapértelmezett beállításai a következők:
    - Óránként egyszer fusson.

    - A **ThreatIntelligenceIndicator** táblából származó összes IP-cím veszélyforrási mutatónak meg kell egyeznie a **AzureActivity** táblából az elmúlt egy órában talált IP-címmel.

    - Biztonsági riasztás létrehozása, ha a lekérdezés eredménye nullánál nagyobb, ami azt jelenti, hogy bármely egyezés megtalálható.

A követelmények teljesítéséhez meghagyhatja az alapértelmezett beállításokat, vagy módosíthatja ezek bármelyikét. Ha elkészült, válassza a **következő: automatikus válasz >** gombot

1. A varázsló ezen lépése lehetővé teszi bármely olyan automatizálás konfigurálását, amelyet el szeretne indítani, ha az adott elemzési szabályból biztonsági riasztás jön létre. Az Azure Sentinelben való **automatizálás a Azure Logic apps** által működtetett forgatókönyvek használatával történik. További információt ebben az [oktatóanyagban talál: automatizált veszélyforrásokkal kapcsolatos válaszok beállítása az Azure sentinelben](./tutorial-respond-threats-playbook.md). Ebben a példában a folytatáshoz válassza a **következőt: Review >(áttekintés** ) gombra.

1. Ez az utolsó lépés ellenőrzi a szabály beállításait. Ha készen áll a szabály engedélyezésére, válassza a **Létrehozás** gombot, és készen áll.

Most, hogy engedélyezte az Analytics-szabályt, az Azure Sentinel **Analytics** szakaszának **aktív szabályok** lapján találhatja meg az engedélyezett szabályt. Innen szerkesztheti, engedélyezheti, letilthatja, duplikálhatja vagy törölheti az aktív szabályt. Az új szabály az aktiválás után azonnal fut, majd a rendszer a megadott időpontjában fut.

Az alapértelmezett beállításoknak megfelelően a rendszer minden alkalommal, amikor a szabály az adott időpontban fut, biztonsági riasztást fog eredményezni. A biztonsági riasztások az Azure sentinelben a **naplók** szakaszban tekinthetők meg **Az Azure Sentinel csoport** **SecurityAlert** táblájában.

Az Azure Sentinelben az elemzési szabályokból generált riasztások olyan biztonsági incidenseket is generálnak, amelyek az Azure Sentinel menüjének **veszélyforrások kezelése** területén található **incidensekben** találhatók. Az incidensek alapján megállapítható, hogy a biztonsági operatív csapatok milyen osztályozást és vizsgálatot végeznek a megfelelő reagálási műveletek meghatározásához. Ebben az oktatóanyagban részletes információkat talál [: incidensek vizsgálata az Azure Sentinel](./tutorial-investigate-cases.md)használatával.

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>A munkafüzetek elemzéseket biztosítanak a fenyegetések felderítéséről

Végezetül egy Azure Sentinel-munkafüzettel jelenítheti meg a fenyegetések felderítésével kapcsolatos legfontosabb információkat az Azure Sentinel szolgáltatásban, és az üzleti igényeknek megfelelően könnyedén testre szabhatja a munkafüzeteket.
Nézzük meg, hogyan lehet megkeresni az Azure Sentinelben elérhető veszélyforrások intelligencia-munkafüzetét, és azt is bemutatjuk, hogyan lehet szerkeszteni a munkafüzetet a testreszabáshoz.

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és navigáljon az **Azure Sentinel** szolgáltatáshoz.

1. Válassza ki azt a **munkaterületet** , amelyhez a veszélyforrások felderítésére szolgáló adatösszekötő használatával importálta a veszélyforrások mutatóit.

1. Válassza ki a **munkafüzetek** elemet az Azure Sentinel menüjének **fenyegetések kezelése** szakaszából.

1. Navigáljon a **fenyegetés intelligencia** nevű munkafüzethez, és ellenőrizze, hogy vannak-e adatok a **ThreatIntelligenceIndicator** táblában az alábbi ábrán látható módon.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="Az adatok ellenőrzése":::
 
1. Kattintson a **Save (Mentés** ) gombra, és válasszon egy Azure-helyet a munkafüzet tárolásához. Erre a lépésre akkor van szükség, ha bármilyen módon módosítani fogja a munkafüzetet, és menti a módosításokat.

1. Most kattintson a **mentett munkafüzet megtekintése** gombra, hogy megnyissa a munkafüzetet megtekintésre és szerkesztésre.

1. Ekkor megjelenik a sablon által biztosított alapértelmezett diagramok. Most végezzünk el néhány módosítást az egyik diagramon. Kattintson a lap tetején található **Szerkesztés** gombra a munkafüzet szerkesztési módjának megadásához.

1. Vegyünk fel egy új, veszélyforrások által megjelenített diagramot a fenyegetés típusa alapján. Görgessen a lap aljára, és válassza a lekérdezés hozzáadása lehetőséget.

1. Adja hozzá a következő szöveget a **log Analytics munkaterület napló lekérdezése** szövegmezőhöz:
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. A **vizualizáció** legördülő menüben válassza a **sávdiagram** lehetőséget.

1. Válassza a **kész szerkesztés** gombot. Létrehozott egy új diagramot a munkafüzethez.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="Oszlopdiagram":::

A munkafüzetek hatékony interaktív irányítópultokat biztosítanak, amelyek betekintést nyújtanak az Azure Sentinel valamennyi aspektusára. A munkafüzetek teljes egészében elvégezhető, és a megadott sablonok nagyszerű kiindulási pontként használhatók, érdemes lehet bemutatni és testreszabni ezeket a sablonokat, vagy új irányítópultokat létrehozni, amelyek számos különböző adatforrást egyesítenek, így egyedi módon jelenítheti meg az adatait. Mivel az Azure Sentinel-munkafüzetek Azure Monitor munkafüzeteken alapulnak, már széles körű dokumentáció áll rendelkezésre, és számos további sablon is elérhető. Ez a cikk azt ismerteti, hogyan [hozhatók létre interaktív jelentések Azure monitor munkafüzetek](../azure-monitor/visualize/workbooks-overview.md)használatával. 

A [githubon Azure monitor-munkafüzetek](https://github.com/microsoft/Application-Insights-Workbooks) gazdag közössége is letölthető, ahol további sablonokat tölthet le, és hozzájárulhat a saját sablonjaihoz is.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megismerheti az Azure Sentinel fenyegetés-felderítési funkcióit és az új veszélyforrások elleni intelligenciát. Az Azure Sentinel fenyegetés-felderítési képességeinek használatáról az alábbi cikkekben talál gyakorlati útmutatást:

- A [fenyegetési intelligencia-adatok összekapcsolását](./connect-threat-intelligence.md) az Azure Sentinel szolgáltatással.
- Hozzon létre [beépített](./tutorial-detect-threats-built-in.md) vagy [Egyéni](./tutorial-detect-threats-custom.md) riasztásokat, és [vizsgálja](./tutorial-investigate-cases.md) meg az incidenseket az Azure sentinelben.