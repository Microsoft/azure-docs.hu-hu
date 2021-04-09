---
title: Az Azure IoT Central eszköz kapcsolatainak megoldása | Microsoft Docs
description: Az eszközön található adatok nem IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: d1a7c94152b611ea0dbea249156add617178d7ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673234"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Az eszközön lévő adatok nem jelennek meg az Azure IoT Centralban hiba elhárítása

Ebből a dokumentumból megtudhatja, hogy az eszköz fejlesztői miért nem jelennek meg az alkalmazásban, hogy az eszközök milyen adatokat küldenek IoT Centralnak.

Két fő területet kell megvizsgálni:

- Eszköz kapcsolódási problémái
  - A hitelesítési problémák, például az eszköz hitelesítő adatai érvénytelenek
  - Hálózati kapcsolattal kapcsolatos problémák
  - Az eszköz nincs jóváhagyva, vagy le van tiltva
- Az eszköz tartalmának alakzatával kapcsolatos problémák

Ez a hibaelhárítási útmutató az eszközök csatlakozási problémáit és az eszköz hasznos adataival kapcsolatos problémákra koncentrál.

## <a name="device-connectivity-issues"></a>Eszköz kapcsolódási problémái

Ez a szakasz segít megállapítani, hogy az adatai eljussanak-e IoT Central.

Ha még nem tette meg, telepítse az `az cli` eszközt és a `azure-iot` bővítményt.

A telepítésének megismeréséhez `az cli` lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

A [](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) bővítmény telepítéséhez `azure-iot` futtassa a következő parancsot:

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> Előfordulhat, hogy a rendszer `uamqp` a bővítmény első futtatásakor kéri a könyvtár telepítését.

Ha telepítette a `azure-iot` bővítményt, indítsa el az eszközt, és ellenőrizze, hogy az üzenetek küldésének módja a IoT Central.

Az alábbi parancsokkal jelentkezzen be az előfizetésbe, ahol a IoT Central alkalmazást használja:

```azurecli
az login
az set account --subscription <your-subscription-id>
```

Az eszköz által küldött telemetria figyeléséhez használja a következő parancsot:

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Ha az eszköz sikeresen csatlakozott a IoT Centralhoz, a következőhöz hasonló kimenet jelenik meg:

```output
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Ha figyelni szeretné a tulajdonság frissítését, az eszköz IoT Central a következő előnézeti paranccsal végez cserét:

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Ha az eszköz sikeresen elküldte a tulajdonságokat, a következőhöz hasonló kimenet jelenik meg:

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Ha megjelenik az adatai a terminálon, akkor az adatai a IoT Central alkalmazásnak megfelelően jelennek meg.

Ha néhány perc elteltével sem jelenik meg az adat, próbálja meg lenyomni `Enter` a `return` billentyűzeten a vagy a billentyűt arra az esetre, ha a kimenet beragadt.

Ha továbbra sem jelenik meg az adatok a terminálon, akkor valószínű, hogy az eszköz hálózati kapcsolati problémákkal rendelkezik, vagy nem küldi el megfelelően az adatokat IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Az eszköz kiépítési állapotának keresése

Ha az adatai nem jelennek meg a figyelőben, ellenőrizze az eszköz kiépítési állapotát a következő parancs futtatásával:

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

Az alábbi kimenet egy olyan eszközt mutat be, amely nem kapcsolódik a csatlakozáshoz:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Eszköz kiépítési állapota | Leírás | Lehetséges enyhítés |
| - | - | - |
| Kiépítve | Nincs azonnal felismerhető probléma. | N/A |
| Regisztrálva | Az eszköz még nem csatlakozott IoT Centralhoz. | A kapcsolódási problémák ellenőrzése az eszköz naplófájljaiban. |
| Blokkolva | Az eszköz nem csatlakozik IoT Centralhoz. | Az eszköz le van tiltva a IoT Central alkalmazáshoz való csatlakozáskor. Oldja fel az eszköz zárolását IoT Central, majd próbálkozzon újra. További információ: [eszközök letiltása](concepts-get-connected.md#device-status-values). |
| Jóvá nem hagyott | Az eszköz nincs jóváhagyva. | Az eszköz nincs jóváhagyva a IoT Central alkalmazáshoz való kapcsolódáshoz. Hagyja jóvá IoT Central az eszközt, és próbálkozzon újra. További információ: [eszközök jóváhagyása](concepts-get-connected.md#device-registration) |
| Nincs társítva | Az eszköz nincs társítva egy eszköz sablonnal. | Társítsa az eszközt egy eszköz sablonnal, hogy IoT Central tudja, hogyan kell elemezni az adatelemzést. |

További információ az [Eszközállapot-kódokról](concepts-get-connected.md#device-status-values).

### <a name="error-codes"></a>Hibakódok

Ha továbbra sem tudja diagnosztizálni, hogy az adatai miért nem jelennek meg `monitor-events` , a következő lépés az eszköz által jelentett hibakódok megkeresése.

Indítsa el a hibakeresési munkamenetet az eszközön, vagy gyűjtsön naplókat az eszközről. Keresse meg az eszköz által jelentett hibakódokat.

Az alábbi táblázatokban a gyakori hibakódok és a kockázatcsökkentő lehetséges műveletek láthatók.

Ha a hitelesítési folyamattal kapcsolatos problémákat látja:

| Hibakód | Leírás | Lehetséges enyhítés |
| - | - | - |
| 400 | A kérelem törzse érvénytelen. Például nem lehet elemezni, vagy az objektumot nem lehet érvényesíteni. | Győződjön meg arról, hogy a megfelelő kérés törzsét küldi el az igazolási folyamat részeként, vagy használjon egy eszköz SDK-t. |
| 401 | Az engedélyezési jogkivonat nem érvényesíthető. Például lejárt vagy nem vonatkozik a kérelem URI-ra. Ezt a hibakódot a rendszer a TPM-igazolási folyamat részeként is visszaadja az eszközöknek. | Győződjön meg arról, hogy az eszköz rendelkezik a megfelelő hitelesítő adatokkal. |
| 404 | Az eszköz kiépítési szolgáltatásának példánya vagy egy erőforrás, például egy regisztráció nem létezik. | Az [ügyfélszolgálatot tartalmazó jegyet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 412 | A `ETag` kérelemben szereplő nem felel meg a `ETag` meglévő erőforrásnak, mint RFC7232. | Az [ügyfélszolgálatot tartalmazó jegyet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 429 | A szolgáltatás szabályozza a műveleteket. Az egyes szolgáltatási korlátokat lásd: [IoT hub Device Provisioning Service korlátok](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | Csökkentse az üzenetek gyakoriságát, Ossza szét a feladatokat több eszköz között. |
| 500 | Belső hiba történt. | Nyújtson be [egy jegyet az ügyfélszolgálattal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , és ellenőrizze, hogy tud-e további segítséget nyújtani. |

## <a name="payload-shape-issues"></a>Hasznos adattartalom-alakzatokkal kapcsolatos problémák

Ha azt állapította meg, hogy az eszköz adatokat küld a IoT Centralnak, a következő lépés annak biztosítása, hogy az eszköz érvényes formátumban küldje el az adatokat.

A gyakori problémák két fő kategóriába sorolhatók, amelyek hatására az eszközök nem jelennek meg IoT Centralban:

- Az eszköz adattípusának nem megfelelő eszköz sablonja:
  - Nem egyeznek meg a nevek, például az elírások vagy a kis-és nagybetűk egyeztetésével kapcsolatos problémák.
  - Nem modellezett tulajdonságok, amelyekben a séma nincs definiálva az eszköz sablonjában.
  - A séma eltérése, mint például a sablonban definiált típus `boolean` , de az érték egy karakterlánc.
  - Ugyanaz a telemetria-név több felületen is definiálva van, de az eszköz nem IoT Plug and Play megfelelő.
- Érvénytelen az adatalakzat JSON-értéke. További információ: [telemetria, Property és Command hasznos](concepts-telemetry-properties-commands.md)adatok.

Ha szeretné megállapítani, hogy a probléma melyik kategóriába esik, futtassa a legmegfelelőbb parancsot a forgatókönyvhöz:

- A telemetria érvényesítéséhez használja az előnézet parancsot:

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- A tulajdonságok frissítéseinek ellenőrzéséhez használja az előnézet parancsot

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

Előfordulhat, hogy a rendszer először a `uamqp` parancs futtatásakor kéri a könyvtár telepítését `validate` .

Az alábbi kimenet a validate parancs által jelzett hibát és figyelmeztető üzeneteket jeleníti meg:

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Ha inkább grafikus felhasználói felületet szeretne használni, a IoT Central **nyers** adatnézet használatával ellenőrizze, hogy valami nincs-e modellezve. A **nyers adatok** nézet nem ismeri fel, hogy az eszköz helytelenül formázott JSON-t küld-e.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Képernyőfelvétel a nyers adatnézetről":::

Ha észlelte a problémát, lehet, hogy frissítenie kell az eszköz belső vezérlőprogramja, vagy létre kell hoznia egy új sablont, amely korábban nem modellezett adatmodelleket modellez.

Ha úgy döntött, hogy olyan új sablont hoz létre, amely helyesen modellezi az adatmodelleket, telepítse át az eszközöket a régi sablonból az új sablonba. További információ: [eszközök kezelése az Azure IoT Central alkalmazásban](howto-manage-devices.md).

## <a name="next-steps"></a>Következő lépések

Ha további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokon](https://azure.microsoft.com/support/community/)is. Másik lehetőségként egy Azure- [támogatási jegyet](https://portal.azure.com/#create/Microsoft.Support)is megadhat.

További információ: az [Azure IoT-támogatás és a Súgó beállításai](../../iot-fundamentals/iot-support-help.md).
