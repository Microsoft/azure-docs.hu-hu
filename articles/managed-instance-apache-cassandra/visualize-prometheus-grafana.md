---
title: A Grafana konfigurálása az Azure felügyelt példányból az Apache Cassandra-ra kibocsátott mérőszámok megjelenítéséhez
description: Ismerje meg, hogyan telepítheti és konfigurálhatja a Grafana egy virtuális gépen az Azure által felügyelt példányból az Apache Cassandra-fürthöz kibocsátott mérőszámok megjelenítéséhez.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745592"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Grafana konfigurálása a felügyelt példány fürtjéből kibocsátott mérőszámok megjelenítéséhez

> [!IMPORTANT]
> Az Apache Cassandra Azure felügyelt példánya jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Amikor üzembe helyez egy Azure felügyelt példányt az Apache Cassandra-fürthöz, a szolgáltatás kiépíti a [Prometheus](https://prometheus.io/) -t futtató kiszolgálót, amely különböző ügyféleszközök által felhasználható. A Prometheus egy nyílt forráskódú figyelési megoldás. A felügyelt példány kibocsátja a metrikákat, és 10 percet vagy 10 GB-nyi adat megtartását (amelyik eléri a küszöbértéket). Ez a cikk azt ismerteti, hogyan konfigurálható a Grafana a felügyelt példányok fürtjéből kibocsátott mérőszámok megjelenítéséhez. A metrikák megjelenítéséhez a következő feladatok szükségesek:

* Helyezzen üzembe egy Ubuntu rendszerű virtuális gépet az Azure-Virtual Network belül, ahol a felügyelt példány található.
* Telepítse a nyílt forráskódú [Grafana eszközt](https://grafana.com/grafana/) az irányítópultok létrehozásához és a Prometheus által kibocsátott mérőszámok megjelenítéséhez.

## <a name="deploy-a-ubuntu-server"></a>Ubuntu-kiszolgáló üzembe helyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Navigáljon ahhoz az erőforráscsoporthoz, ahol a felügyelt példány fürtje található. Válassza a **Hozzáadás** lehetőséget, és keresse meg az **Ubuntu Server 18,04 LTS** -rendszerképet:

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Az Ubuntu-kiszolgáló rendszerképének keresése a Azure Portal." border="true":::

1. Válassza ki a rendszerképet, és kattintson a **Létrehozás** gombra.

1. A **virtuális gép létrehozása** panelen adja meg az alábbi mezők értékeit, és az egyéb mezők alapértelmezett értékeit is meghagyhatja:

   * **Virtuálisgép-név** – adjon meg egy nevet a virtuális gép számára.
   * **Régió** – válassza ki ugyanazt a régiót, ahol a Virtual Network üzembe lett helyezve.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Töltse ki az űrlapot, és hozzon létre egy virtuális gépet Ubuntu Server-lemezképpel." border="true":::

1. A **hálózatkezelés** lapon válassza ki azt a Virtual Network, amelyben a felügyelt példány telepítve van:

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Az Ubuntu-kiszolgáló hálózati beállításainak konfigurálása." border="true":::

1. Végül válassza a **felülvizsgálat + létrehozás** lehetőséget a Grafana-kiszolgáló létrehozásához.

## <a name="install-grafana"></a>A Grafana telepítése

1. A Azure Portal nyissa meg azt a Virtual Network, amelyen üzembe helyezte a felügyelt példányt és a Grafana-kiszolgálót. Megjelenik egy **Cassandra-Jump (0. példány)** nevű virtuálisgép-méretezési csoport példánya. Ez a Prometheus-metrikák a virtuálisgép-méretezési csoportokban vannak tárolva. Jegyezze fel a példány IP-címét:

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="A Prometheus-példány IP-címének lekérése." border="true":::

1. Kapcsolódjon az újonnan létrehozott Ubuntu-kiszolgálóhoz az [Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) használatával vagy az előnyben részesített ügyfélprogrammal az SSH-n keresztül történő kapcsolódáshoz.

1. A virtuális GÉPHEZ való csatlakozás után telepítenie és konfigurálnia kell a Grafana-t ahhoz, hogy csatlakozhasson a metrikákat futtató virtuálisgép-méretezési csoporthoz. Nyisson meg egy parancssort, és írja be a `nano` parancsot a nano Text Editor megnyitásához. Illessze be a következő szkriptet a szövegszerkesztőbe, és cserélje le a `<prometheus IP address>` -t az előző lépésben rögzített IP-cím helyére:

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. A fájl mentéséhez írja be a következőt: `ctrl + X` . Megadhatja a fájl nevét `grafana.sh` .

1. Adja meg a parancsot a parancssorban a `./grafana.sh` Grafana telepítéséhez.

1. A telepítés befejezése után a Grafana a kiszolgáló IP-címének 3000-as **portján** lesz elérhető, ahogy az alábbi képernyőképen látható:

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="Futtassa a Grafana a 3000-es porton." border="true":::

1. A Grafana Apache Cassandra-hoz létrehozott nyílt forráskódú irányítópultok közül választhat, például a [fürt – áttekintés JSON-](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) fájl. Töltse le és importálja az irányítópult JSON-definícióját a Grafana-be:

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Importálja a Grafana JSON-definícióját." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Grafana JSON-definíció feltöltése." border="true":::

1. Ezt követően nyomon követheti a Cassandra Managed instance-fürtöt a kiválasztott irányítópulton:

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Tekintse meg a Cassandra felügyelt példány metrikáit az irányítópulton." border="true":::

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja az irányítópultokat a Prometheus metrikáinak megjelenítéséhez a Grafana használatával. További információ az Apache Cassandra Azure felügyelt példányáról a következő cikkekben:

* [Az Apache Cassandra Azure felügyelt példányának áttekintése](introduction.md)
* [Felügyelt Apache Spark-fürt üzembe helyezése Azure Databricks (előzetes verzió)](deploy-cluster-databricks.md)
