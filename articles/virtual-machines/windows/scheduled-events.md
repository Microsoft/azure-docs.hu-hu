---
title: Scheduled Events a Windows rendszerű virtuális gépekhez az Azure-ban
description: Ütemezett események az Azure Metadata Service használatával a Windows rendszerű virtuális gépekhez.
author: EricRadzikowskiMSFT
ms.service: virtual-machines
ms.subservice: scheduled-events
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: ericrad
ms.reviwer: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a388ade2b44260bfa21e22866d85a46e482bc97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499951"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure Metadata Service: Scheduled Events a Windows rendszerű virtuális gépekhez

Scheduled Events egy Azure-Metadata Service, amely lehetővé teszi az alkalmazási idő előkészítését a virtuális gép (VM) karbantartásához. Információt nyújt a közelgő karbantartási eseményekről (például újraindítás), hogy az alkalmazás előkészítse őket, és korlátozza a megszakadást. Minden Azure Virtual Machines típushoz elérhető, beleértve a Pásti és a IaaS-t is Windows és Linux rendszeren. 

További információ a Linux rendszeren futó Scheduled Eventsről: [Scheduled Events Linux rendszerű virtuális gépekhez](../linux/scheduled-events.md).

> [!Note] 
> A Scheduled Events általánosan elérhető az összes Azure-régióban. A legfrissebb kiadási információkért lásd a [verzió és a régió rendelkezésre állását](#version-and-region-availability) .

## <a name="why-use-scheduled-events"></a>Miért érdemes Scheduled Events?

Számos alkalmazás kihasználhatja a virtuális gépek karbantartására való felkészülés idejét. Az idő a rendelkezésre állást, a megbízhatóságot és a használhatóságot javító alkalmazásspecifikus feladatok végrehajtásához használható, beleértve a következőket: 

- Ellenőrzőpont és visszaállítás.
- A kapcsolatok kiürítése.
- Elsődleges replika feladatátvétele.
- Eltávolítás a terheléselosztó készletéből.
- Eseménynaplózás.
- Biztonságos leállítás.

A Scheduled Events segítségével az alkalmazás képes észlelni, ha a karbantartás bekövetkezik, és a tevékenységek kiváltásával korlátozzák a hatásukat.  

Scheduled Events a következő használati esetekben nyújt eseményeket:

- [Platform által kezdeményezett karbantartás](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json) (például virtuális gép újraindítása, élő áttelepítés vagy memória megőrzése a gazdagépen)
- A virtuális gép olyan [csökkentett teljesítményű gazdagép hardverén](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) fut, amely hamarosan meghiúsul
- Felhasználó által kezdeményezett karbantartás (például egy felhasználó újraindítja vagy újratelepíti a virtuális gépet)
- [Helyszíni virtuális gép](../spot-vms.md) és [direktszín-méretezési csoport](../../virtual-machine-scale-sets/use-spot.md) példányainak kizárása.

## <a name="the-basics"></a>Az alapok  

  A Metadata Service a virtuális gépekről elérhető REST-végpontok segítségével teszi elérhetővé a futó virtuális gépek információit. Az információk egy nem irányítható IP-címen keresztül érhetők el, hogy a virtuális gépen kívül ne legyen kitéve.

### <a name="scope"></a>Hatókör
Az ütemezett események a következőre érkeznek:

- Önálló Virtual Machines.
- Egy felhőalapú szolgáltatás összes virtuális gépe.
- Egy rendelkezésre állási csoportba tartozó összes virtuális gép.
- Egy rendelkezésre állási zónában lévő összes virtuális gép.
- Egy méretezési csoport elhelyezési csoportjában lévő összes virtuális gép. 

> [!NOTE]
> A Fabric Controller-(FC-) bérlőben lévő összes virtuális gép Scheduled Events egy FC-bérlő minden virtuális gépe számára elérhető. Az FC bérlő egy önálló virtuális géphez, egy teljes körű felhőalapú szolgáltatáshoz, egy teljes rendelkezésre állási készlethez és egy virtuálisgép-méretezési csoport (VMSS) elhelyezési csoportjának felel meg a rendelkezésre állási zónák kihasználtsága nélkül. 

Ennek eredményeképpen jelölje be az `Resources` esemény mezőjét az érintett virtuális gépek azonosításához.

### <a name="endpoint-discovery"></a>Végpont felderítése
A VNET-kompatibilis virtuális gépek esetében a Metadata Service statikus, nem irányítható IP-címről érhető el `169.254.169.254` . A Scheduled Events legújabb verziójának teljes végpontja a következő: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

Ha a virtuális gép nem egy Virtual Networkon belül jön létre, a Cloud Services és a klasszikus virtuális gépek esetében az alapértelmezett esetekben további logikára van szükség a használandó IP-cím felderítéséhez. A [gazdagép végpontjának felderítéséhez](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)tekintse meg ezt a mintát.

### <a name="version-and-region-availability"></a>Verzió és régió elérhetősége
A Scheduled Events szolgáltatás verziója. A verziók megadása kötelező; a jelenlegi verzió: `2019-01-01` .

| Verzió | Kiadás típusa | Régiók | Kibocsátási megjegyzések | 
| - | - | - | - | 
| 2019-08-01 | Általános elérhetőség | Mind | <li> EventSource-támogatás hozzáadva |
| 2019-04-01 | Általános elérhetőség | Mind | <li> Az esemény leírásának támogatása hozzáadva |
| 2019-01-01 | Általános elérhetőség | Mind | <li> A virtuálisgép-méretezési csoportok támogatásának támogatása a EventType leállításához |
| 2017-11-01 | Általános elérhetőség | Mind | <li> A (z) megelőzik helyszíni VM-kilakoltatás EventType támogatása<br> | 
| 2017-08-01 | Általános elérhetőség | Mind | <li> Eltávolított előtagértéke aláhúzás a IaaS virtuális gépek erőforrásainak neveiből<br><li>Metaadatok fejlécére vonatkozó követelmények kényszerítve az összes kérelemhez | 
| 2017-03-01 | Előnézet | Mind | <li>Kezdeti kiadás |


> [!NOTE] 
> A korábbi előzetes verziókban Scheduled Events támogatott {Latest} API-verzióként. Ez a formátum már nem támogatott, és a jövőben elavulttá válik.

### <a name="enabling-and-disabling-scheduled-events"></a>Scheduled Events engedélyezése és letiltása
Az Scheduled Events engedélyezve van a szolgáltatáshoz, amikor az első alkalommal kérelmet készít az eseményekről. Egy késleltetett választ kell várnia az első hívásában akár két percig is.

A Scheduled Events le van tiltva a szolgáltatás esetében, ha 24 óráig nem végeznek kérést.

### <a name="user-initiated-maintenance"></a>Felhasználó által kezdeményezett karbantartás
A felhasználó által kezdeményezett virtuális gépek karbantartása az Azure Portal, az API, a CLI vagy a PowerShell segítségével ütemezett eseményt eredményez. Ezután tesztelheti a karbantartási előkészítési logikát az alkalmazásban, és az alkalmazás előkészítheti a felhasználó által kezdeményezett karbantartást.

Ha újraindít egy virtuális gépet, a típussal rendelkező esemény `Reboot` ütemezve van. Ha újratelepít egy virtuális gépet, a típussal rendelkező esemény `Redeploy` ütemezve van.

## <a name="use-the-api"></a>Az API használata

### <a name="headers"></a>Fejlécek
Metadata Service lekérdezése során meg kell adnia a fejlécet `Metadata:true` annak biztosítására, hogy a kérés akaratlanul nem lett átirányítva. Az `Metadata:true` összes ütemezett eseményre vonatkozó kérelem fejlécét kötelező megadni. Nem sikerült belefoglalni a fejlécet a kérelembe a Metadata Service "hibás kérés" válaszában.

### <a name="query-for-events"></a>Események lekérdezése
Az ütemezett eseményeket a következő hívással kérdezheti le:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

A válasz ütemezett események tömbjét tartalmazza. Az üres tömb azt jelenti, hogy jelenleg nincsenek ütemezett események.
Abban az esetben, ha ütemezett események vannak, a válasz események tömbjét tartalmazza. 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},       
            "Description": {eventDescription},
            "EventSource" : "Platform" | "User",
        }
    ]
}
```

### <a name="event-properties"></a>Esemény tulajdonságai
|Tulajdonság  |  Leírás |
| - | - |
| Napszállta | Az esemény globálisan egyedi azonosítója. <br><br> Példa: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Ez az esemény okozza a hatását. <br><br> Értékek: <br><ul><li> `Freeze`: A virtuális gép néhány másodpercig szünetelteti az ütemezést. Előfordulhat, hogy a processzor és a hálózati kapcsolat fel van függesztve, de nincs hatással a memóriára vagy a megnyitott fájlokra.<li>`Reboot`: A virtuális gép újraindításra van ütemezve (nem állandó memória elvész). <li>`Redeploy`: A virtuális gép egy másik csomópontra való áthelyezésre van ütemezve (az ideiglenes lemezek elvesznek). <li>`Preempt`: A helyszíni virtuális gép törlődik (az ideiglenes lemezek elvesznek). <li> `Terminate`: A virtuális gép törlésre van ütemezve. |
| ResourceType | Az eseményt érintő erőforrás típusa. <br><br> Értékek: <ul><li>`VirtualMachine`|
| Források| Az eseményt érintő erőforrások listája. A lista garantáltan legfeljebb egy [frissítési tartományból](../availability.md)származó gépeket tartalmazhat, de előfordulhat, hogy nem tartalmazza a UD összes számítógépét. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Az esemény állapota. <br><br> Értékek: <ul><li>`Scheduled`: Ez az esemény a tulajdonságban megadott idő után indul el `NotBefore` .<li>`Started`: Ez az esemény megkezdődött.</ul> `Completed`A rendszer nem adott meg és nem is hasonló állapotot. Az eseményt a rendszer már nem adja vissza az esemény befejeződése után.
| NotBefore| Az esemény elindításának időpontja. <br><br> Példa: <br><ul><li> Hétfő, 19 Sep 2016 18:29:47 GMT  |
| Leírás | Az esemény leírása. <br><br> Példa: <br><ul><li> A gazdagép-kiszolgáló karbantartás alatt áll. |
| EventSource | Az esemény kezdeményezője. <br><br> Példa: <br><ul><li> `Platform`: Ezt az eseményt a platform kezdeményezi. <li>`User`: Ezt az eseményt a felhasználó kezdeményezi. |

### <a name="event-scheduling"></a>Események ütemezése
Az események a jövőben az esemény típusa alapján a lehető legkevesebb időt ütemezik. Ez az idő egy esemény tulajdonságában jelenik meg `NotBefore` . 

|EventType  | Minimális figyelmeztetés |
| - | - |
| Fázik| 15 perc |
| Újraindítás | 15 perc |
| Ismételt üzembe helyezés | 10 perc |
| Megelőzik | 30 másodperc |
| Befejezés | [Felhasználó által konfigurálható](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): 5 – 15 perc |

> [!NOTE] 
> Bizonyos esetekben az Azure képes megjósolni a gazdagép meghibásodását, mivel a hardver elromlott, és megkísérli a szolgáltatás megszakadásának enyhítését az áttelepítés ütemezésével. Az érintett virtuális gépek egy ütemezett eseményt kapnak, `NotBefore` amely általában néhány napig tart a jövőben. A tényleges idő a várható meghibásodási kockázattól függ. Az Azure a lehetségesnél 7 napos előzetes értesítést próbál megadni, de a tényleges idő változó, és kisebb is lehet, ha az előrejelzés szerint a hardver hamarosan leáll. Ha a hardvert a rendszer által kezdeményezett áttelepítés előtt nem sikerül végrehajtani, akkor a lehető leghamarabb telepítse újra a virtuális gépet.

### <a name="polling-frequency"></a>Lekérdezés gyakorisága

A végpontot a frissítésekhez gyakran vagy ritkán lehet lekérdezni, ahogy szeretné. A kérelmek közötti idő hosszabb ideig azonban minél több időt vesz igénybe, hogy reagáljon egy közelgő eseményre. A legtöbb esemény 5 – 15 percet vesz igénybe, bár bizonyos esetekben az előzetes értesítés akár 30 másodperc is lehet. Annak biztosítása érdekében, hogy a lehető legtöbb időt igénybe vegye a kockázatcsökkentő műveletek elvégzésére, javasoljuk, hogy másodpercenként egyszer kérdezze le a szolgáltatást.

### <a name="start-an-event"></a>Esemény indítása 

Miután elolvasta a közelgő eseményt, és befejezi a logikát a zökkenőmentes leállítás érdekében, jóváhagyhatja a függő eseményt úgy, `POST` hogy meghívja a metadata Service `EventId` . Ez a hívás az Azure-ra utal, hogy lerövidítheti az értesítés minimális idejét (ha lehetséges). 

A kérelem törzsében a következő JSON-minta várható `POST` . A kérésnek tartalmaznia kell egy listát `StartRequests` . Mindegyik `StartRequest` `EventId` a felgyorsítani kívánt eseményhez tartalmaz:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash-minta
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Egy esemény elfogadásával lehetővé válik, hogy az esemény minden esetben folytassa az eseményt `Resources` , nem csak az eseményt visszaigazoló virtuális gépet. Ezért dönthet úgy, hogy egy vezetőt választ ki a nyugtázás koordinálására, ami a mező első számítógépének egyszerűvé válhat `Resources` .

## <a name="python-sample"></a>Python-minta 

Az alábbi lekérdezési Metadata Service az ütemezett eseményekhez, és jóváhagyja az egyes fennmaradó eseményeket:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01"
this_host = socket.gethostname()


def get_scheduled_events():
    req = urllib2.Request(metadata_url)
    req.add_header('Metadata', 'true')
    resp = urllib2.urlopen(req)
    data = json.loads(resp.read())
    return data


def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ", "_")
    description = evt['Description']
    eventSource = evt['EventSource']
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + 
        " by " + eventSource + 
        " with description " + description +
        " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Következő lépések 
- Tekintse [meg Scheduled Eventsét az Azure fridayban](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) , és tekintse meg a bemutatót. 
- Tekintse át a Scheduled Events kód mintáit az [Azure-példány metaadatainak Scheduled Events GitHub-tárházban](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- További információ a [instance metadata Service](instance-metadata-service.md)elérhető API-król.
- Ismerje meg az [Azure-beli Windows rendszerű virtuális gépek tervezett karbantartását](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json).
