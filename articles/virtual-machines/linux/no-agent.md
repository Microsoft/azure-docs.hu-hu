---
title: Linux-rendszerképek létrehozása kiépítési ügynök nélkül
description: Általános Linux-rendszerképeket hozhat létre az Azure-beli kiépítési ügynök nélkül.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: c7ca147f0a5b907ee0c5c66d53a219fe75ab2179
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551708"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>Általánosított rendszerképek létrehozása kiépítési ügynök nélkül

A Microsoft Azure a [walinuxagent](https://github.com/Azure/WALinuxAgent) vagy a [Cloud-init](https://github.com/canonical/cloud-init) (ajánlott) formában biztosítja a linuxos virtuális gépek kiépítési ügynökeit. Előfordulhat azonban, hogy nem kívánja használni ezeket az alkalmazásokat a kiépítési ügynökhöz, például a következő esetekben:

- A Linux-disztribúció/-verzió nem támogatja a Cloud-init/Linux-ügynököt.
- Szükség van bizonyos virtuálisgép-tulajdonságok beállítására, például az állomásnévre.

> [!NOTE] 
>
> Ha nincs szükség semmilyen tulajdonság beállítására vagy a kiépítés bármely formájára, érdemes megfontolnia egy speciális rendszerkép létrehozását.

Ez a cikk bemutatja, hogyan állíthatja be a virtuálisgép-rendszerképet az Azure-platform követelményeinek kielégítésére és az állomásnév beállítására a kiépítési ügynök telepítése nélkül.

## <a name="networking-and-reporting-ready"></a>Hálózatkezelés és jelentéskészítés kész

Ahhoz, hogy a linuxos virtuális gép kommunikáljon az Azure-összetevőkkel, a DHCP-ügyfélnek a gazdagép IP-címének lekérését kell kérnie a virtuális hálózatról, valamint a DNS-feloldást és az útválasztást. A legtöbb disztribúció az alábbi segédprogramokkal rendelkezik. Az Azure-ban a Linux-disztribúciók gyártói által tesztelt eszközök közé tartoznak a, `dhclient` `network-manager` `systemd-networkd` és egyebek.

> [!NOTE]
>
> Jelenleg a kiépítési ügynök nélküli általánosított rendszerképek létrehozása csak a DHCP-kompatibilis virtuális gépeket támogatja.

A hálózatkezelés beállítása és konfigurálása után készen kell állnia a jelentésre. Ez azt közli az Azure-val, hogy a virtuális gép sikeresen kiépítve.

> [!IMPORTANT]
>
> Ha nem sikerül jelentést készíteni az Azure-hoz, a rendszer újraindítja a virtuális gépet.

## <a name="demosample"></a>Bemutató/minta

Ebből a bemutatóból megtudhatja, hogyan hozhat létre meglévő Piactéri rendszerképet (ebben az esetben egy Debian Buster VM-t), és hogyan távolíthatja el a Linux-ügynököt (walinuxagent), de az Azure-ba történő jelentéskészítéshez is létrehozhatja a legalapvetőbb folyamatot, amely készen áll a virtuális gép számára.

### <a name="create-the-resource-group-and-base-vm"></a>Az erőforráscsoport és az alapszintű virtuális gép létrehozása:

```bash
$ az group create --location eastus --name demo1
```

Az alapszintű virtuális gép létrehozása:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>A rendszerkép kiépítési ügynökének eltávolítása

Ha a virtuális gép üzembe helyezése megtörtént, SSH-t telepíthet a gépre, és eltávolíthatja a Linux-ügynököt:

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>A szükséges kód hozzáadása a virtuális géphez

A virtuális gépen belül is, mert eltávolítottank az Azure Linux-ügynököt, hogy elérhetővé kell tenni egy olyan mechanizmust, amely készen áll a jelentésre. 

#### <a name="python-script"></a>Python-szkript

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>Általános lépések (a Python használata nélkül)

Ha a virtuális gépnek nincs telepítve vagy elérhető a Python, programozott módon reprodukálhatja a fenti parancsfájl-logikát a következő lépésekkel:

1. Kérje le a `ContainerId` és a `InstanceId` WireServer válaszának elemzésével: `curl -X GET -H 'x-ms-version: 2012-11-30' http://168.63.129.16/machine?comp=goalstate` .

2. A következő XML-adatok kiépítése, az elemzett `ContainerId` és a `InstanceId` fenti lépés beinjektálása:
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. Tegye közzé ezeket az WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>A kód futtatásának automatizálása az első rendszerindításkor

Ez a bemutató rendszerszintű, a modern Linux-disztribúciók leggyakoribb init rendszerét használja. Tehát a legegyszerűbb és legnatív módszer, amellyel biztosítható, hogy a jelentés kész mechanizmusa a megfelelő időben fusson egy rendszerszintű szolgáltatási egység létrehozásához. A következő egységnyi fájlt adhatja hozzá `/etc/systemd/system` (ez a példa az egység fájljának nevét `azure-provisioning.service` ):

```bash
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

Ez a rendszerű szolgáltatás az alapszintű kiépítés során három dolgot tesz:

1. Készen áll az Azure-ra (amely azt jelzi, hogy sikeres volt a jelentés).
1. Átnevezi a virtuális gépet a felhasználó által megadott virtuálisgép-név alapján, ha ezt az adatforrást az [Azure instance metadata Serviceról (IMDS)](./instance-metadata-service.md)húzza. **Megjegyzés** A IMDS más [példány-metaadatokat](./instance-metadata-service.md#access-azure-instance-metadata-service)is biztosít, például az SSH nyilvános kulcsokat, így az állomásnévnél többet is beállíthat.
1. Letiltja önmagát, hogy csak az első rendszerindításkor fusson, nem pedig a későbbi újraindítások során.

A fájlrendszerben lévő egységhez futtassa a következő parancsot az engedélyezéséhez:

```bash
$ sudo systemctl enable azure-provisioning.service
```

Most, hogy a virtuális gép készen áll általánosítani, és létrehoz egy rendszerképet. 

#### <a name="completing-the-preparation-of-the-image"></a>A rendszerkép előkészítésének befejezése

A fejlesztői gépen a következő lépésekkel készítheti elő a rendszerkép létrehozását az alapszintű virtuális gépről:

```bash
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

És hozza létre a rendszerképet ebből a virtuális gépről:

```bash
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

Most már készen áll arra, hogy létrehozzon egy új virtuális gépet (vagy több virtuális gépet) a rendszerképből:

```bash
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> Fontos, hogy a beállítás értéke `--enable-agent` , `false` mert a walinuxagent nem létezik ezen a virtuális gépen, amelyet a rendszerképből létre fog hozni.

A virtuális gép üzembe helyezése sikeresen megtörtént. Jelentkezzen be az újonnan kiépítő virtuális gépre, és tekintse meg a jelentés kész rendszerbe állítására szolgáló szolgáltatás kimenetét:

```bash
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>Támogatás

Ha saját üzembe helyezési kódot vagy ügynököt valósít meg, akkor a Microsoft támogatási szolgálata csak a nem elérhető kiépítési felületekkel kapcsolatos problémákat vizsgálja. Folyamatosan fejlesztünk javításokat és változásokat ezen a területen, ezért figyelnie kell a Cloud-init és az Azure Linux-ügynök változásait az API-változások kiépítési folyamatához.

## <a name="next-steps"></a>Következő lépések

További információ: Linux- [kiépítés](provisioning.md).
