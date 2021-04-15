---
title: Eszközkapcsolatok hibaelhárítása Azure IoT Central | Microsoft Docs
description: Elháríthatja, hogy miért nem látja az eszközökről származó adatokat a IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: 494608f9dd8fbf986dcda6eeb782a64f6a2ca008
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378567"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Az eszközön lévő adatok nem jelennek meg az Azure IoT Centralban hiba elhárítása

Ez a dokumentum segít az eszközfejlesztőknek megtudni, hogy az eszközök IoT Central miért nem találják meg az adatokat az alkalmazásban.

Két fő területet kell megvizsgálni:

- Eszközkapcsolati problémák
  - Hitelesítési problémák, például az eszköz hitelesítő adatai érvénytelenek
  - Hálózati kapcsolati problémák
  - Az eszköz nincs jóváhagyva vagy letiltva
- Az eszköz hasznos adatokatalakzatának problémái

Ez a hibaelhárítási útmutató az eszközkapcsolati problémákra és az eszköz hasznos adatalakzatának problémáira összpontosít.

## <a name="device-connectivity-issues"></a>Eszközkapcsolati problémák

Ez a szakasz segít megállapítani, hogy az adatok elérik-e IoT Central.

Ha még nem tette meg, telepítse az `az cli` eszközt és a `azure-iot` bővítményt.

A telepítésének elsajátításért `az cli` lásd: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

A [bővítmény](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) `azure-iot` telepítéséhez futtassa a következő parancsot:

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> Előfordulhat, hogy a rendszer a bővítményparancs első futtatásakor a kódtár telepítését `uamqp` kéri.

A bővítmény telepítése után indítsa el az eszközt, és nézze meg, hogy az elküldött üzenetek úton vannak-e a `azure-iot` IoT Central.

Az alábbi parancsokkal jelentkezzen be arra az előfizetésre, amelyben a IoT Central van:

```azurecli
az login
az set account --subscription <your-subscription-id>
```

Az eszköz által küldött telemetria figyelése a következő paranccsal:

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Ha az eszköz sikeresen csatlakozott a IoT Central, a következőhöz hasonló kimenetet fog látni:

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

A tulajdonságfrissítések figyelése érdekében az eszköz IoT Central az alábbi előzetes verziójú parancsot használja:

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Ha az eszköz sikeresen elküldi a tulajdonságfrissítéseket, a következőhöz hasonló kimenet fog látni:

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Ha az adatok megjelennek a terminálon, akkor az adatok olyan távol esnek a IoT Central alkalmazásáig.

Ha néhány perc után nem jelennek meg adatok, próbálja meg megnyomni a vagy a billentyűt a billentyűzeten, ha a `Enter` `return` kimenet elakadt.

Ha továbbra sem jelennek meg adatok a terminálon, valószínű, hogy az eszközön hálózati kapcsolati problémák vannak, vagy nem küldi el megfelelően az adatokat a IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Az eszköz kiépítési állapotának ellenőrzése

Ha az adatok nem jelennek meg a figyelőben, ellenőrizze az eszköz kiépítési állapotát a következő parancs futtatásával:

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

Az alábbi kimenet egy olyan eszközre mutat példát, amely nem csatlakozik:

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

| Eszköz kiépítési állapota | Leírás | Lehetséges kockázatcsökkentés |
| - | - | - |
| Kiépítve | Nincs azonnal felismerhető probléma. | N/A |
| Regisztrálva | Az eszköz még nem csatlakozott a IoT Central. | Ellenőrizze, hogy vannak-e csatlakozási problémák az eszköznaplókban. |
| Blokkolva | Az eszköz nem csatlakozik a IoT Central. | Az eszköz nem csatlakozik a IoT Central alkalmazáshoz. Oldja fel az eszköz zárolását a IoT Central, majd próbálja meg újra. További információ: Eszközök [blokkolása.](concepts-get-connected.md#device-status-values) |
| Jóvá nem hagyott | Az eszköz nincs jóváhagyva. | Az eszköz nem engedélyezett a IoT Central való csatlakozáshoz. Hagyja jóvá az eszközt a IoT Central majd próbálja újra. További információ: Eszközök [jóváhagyása](concepts-get-connected.md#device-registration) |
| Társítatlan | Az eszköz nincs eszközsablonhoz társítva. | Társítsa az eszközt egy eszközsablonhoz, IoT Central tudja, hogyan elemezze az adatokat. |

További információ az [eszközállapot-kódokról:](concepts-get-connected.md#device-status-values).

### <a name="error-codes"></a>Hibakódok

Ha továbbra sem tudja diagnosztizálni, hogy az adatok miért nem jelenhetnek meg az -ban, a következő lépés az eszköz által jelentett `monitor-events` hibakódok megjelenítése.

Indítson el egy hibakeresési munkamenetet az eszközön, vagy gyűjtsön naplókat az eszközről. Ellenőrizze, hogy vannak-e olyan hibakódok, amelyekről az eszköz jelentést készít.

Az alábbi táblázatok a gyakori hibakódokat és a lehetséges megoldási műveleteket mutatják be.

Ha a hitelesítési folyamattal kapcsolatos problémákat lát:

| Hibakód | Leírás | Lehetséges kockázatcsökkentés |
| - | - | - |
| 400 | A kérelem törzse érvénytelen. Például nem elemezhető, vagy az objektum nem érvényesíthető. | Győződjön meg arról, hogy az igazolási folyamat részeként a megfelelő kérelem törzsét küldi el, vagy eszközoldali SDK-t használ. |
| 401 | Az engedélyezési jogkivonat nem érvényesíthető. Például lejárt, vagy nem vonatkozik a kérés URI-jra. Ezt a hibakódot a rendszer a TPM-igazolási folyamat részeként is visszaadja az eszközöknek. | Győződjön meg arról, hogy az eszköz a megfelelő hitelesítő adatokkal rendelkezik. |
| 404 | A Device Provisioning Service-példány vagy egy erőforrás, például egy regisztráció nem létezik. | [Nyújtson be egy jegyet az ügyfélszolgálattal.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) |
| 412 | A kérelemben lévő nem egyezik meg a meglévő erőforrás erőforrásának a `ETag` `ETag` RFC7232 szabványnak megfelelővel. | [Nyújtson be egy jegyet az ügyfélszolgálattal.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) |
| 429 | A műveleteket a szolgáltatás szabályozása alatt állnak. Konkrét szolgáltatási korlátokért lásd: IoT Hub Device Provisioning Service [korlátozások.](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits) | Az üzenetek gyakoriságának csökkentése, a felelősségek felosztása több eszköz között. |
| 500 | Belső hiba történt. | [Ha további segítségre van szüksége,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) nyújtson be egy jegyet az ügyfélszolgálatnak. |

### <a name="file-upload-error-codes"></a>Fájlfeltöltési hibakódok

Az alábbi lista azokat a gyakori hibakódokat sorolja fel, amelyek akkor jelenhetnek meg, ha egy eszköz megpróbál feltölteni egy fájlt a felhőbe. Ne feledje, hogy mielőtt az eszköz feltölthet egy fájlt, konfigurálnia kell az eszköz [fájlfeltöltését](howto-configure-file-uploads.md) az alkalmazásban.

| Hibakód | Leírás | Lehetséges kockázatcsökkentés |
| - | - | - |
| 403006  | Túllépte az egyidejű fájlfeltöltési műveletek számát. Minden eszközoldali ügyfél legfeljebb 10 egyidejű fájlfeltöltést tölthet fel. | Győződjön meg arról, hogy az eszköz IoT Central értesítést a fájlfeltöltési művelet befejezéséről. Ha ez nem működik, próbálja meg csökkenteni a kérés időtúllépését. |

## <a name="payload-shape-issues"></a>Hasznos formában kapcsolatos problémák

Miután megállapította, hogy az eszköz adatokat küld a IoT Central, a következő lépés annak biztosítása, hogy az eszköz érvényes formátumban küldi el az adatokat.

A gyakori problémák két fő kategóriája miatt az eszközadatok nem jelennek meg a IoT Central:

- Eszközsablonok eltérése az eszközadatok között:
  - Az elnevezések, például az el hibák vagy a kis- és az esetegyeztetési problémák eltérése.
  - Nem módosított tulajdonságok, amelyekben a séma nincs definiálva az eszközsablonban.
  - A sémák nem egyezőek, például a sablonban típusként definiált típus, de az `boolean` adatok sztringek.
  - Ugyanazt a telemetrianevet több felületen definiálja a rendszer, de az eszköz nem IoT Plug and Play megfelelő.
- Az adatalakzat érvénytelen JSON. További információ: [Telemetria,](concepts-telemetry-properties-commands.md)tulajdonság és hasznos parancsok.

A probléma kategóriáinak észleléséhez futtassa a forgatókönyvnek legmegfelelőbb parancsot:

- A telemetria érvényesítéséhez használja az előzetes verzió parancsát:

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- A tulajdonságfrissítések ellenőrzéséhez használja az előzetes verzió parancsát

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

Előfordulhat, hogy a rendszer a parancs első futtatásakor kéri a `uamqp` kódtár `validate` telepítését.

Az alábbi kimenet a validate parancstól származó hiba- és figyelmeztető üzeneteket mutatja be:

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Ha grafikus felhasználói felület használatát részesíti előnyben, használja a IoT Central **Nyers** adatok nézetben, hogy lássa, van-e nem modellezés alatt. A **Nyers adatok** nézet nem észleli, ha az eszköz helytelenül formázott JSON-adatokat küld.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="A Nyers adatok nézet képernyőképe":::

Ha észlelte a problémát, előfordulhat, hogy frissítenie kell az eszköz belső vezérlőprogramját, vagy létre kell hoznia egy új eszközsablont, amely korábban nem módosított adatokat modellel.

Ha úgy döntött, hogy létrehoz egy új sablont, amely megfelelően modelle az adatokat, akkor a régi sablonból az új sablonba telepítheti át az eszközöket. További tudnivalókért lásd: Eszközök kezelése a Azure IoT Central [alkalmazásban.](howto-manage-devices.md)

## <a name="next-steps"></a>Következő lépések

Ha további segítségre van szüksége, kapcsolatba léphet az Azure-szakértőkkel az MSDN Azure-on, és Stack Overflow [fórumain.](https://azure.microsoft.com/support/community/) Másik lehetőségként be is Azure-támogatás [jegyet.](https://portal.azure.com/#create/Microsoft.Support)

További információ: [Azure IoT támogatási és súgóbeállítások.](../../iot-fundamentals/iot-support-help.md)
