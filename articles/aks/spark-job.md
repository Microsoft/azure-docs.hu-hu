---
title: Apache Spark-feladatok futtatása az Azure Kubernetes szolgáltatással (ak)
description: Az Azure Kubernetes Service (ak) használatával Apache Spark feladatot hozhat létre és futtathat a nagyméretű adatfeldolgozáshoz.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 74a3fe79291f3a5c7f5bbd664bf6d55a5fb77eae
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181193"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Apache Spark feladatok futtatása az AK-on

A [Apache Spark][apache-spark] egy gyors motor a nagy léptékű adatfeldolgozáshoz. A [Spark 2.3.0-kiadástól][spark-kubernetes-earliest-version]kezdve a Apache Spark támogatja a Kubernetes-fürtökkel való natív integrációt. Az Azure Kubernetes Service (ak) az Azure-ban futó felügyelt Kubernetes-környezet. Ez a dokumentum részletesen ismerteti Apache Spark feladatok Azure Kubernetes Service-(ak-) fürtön történő előkészítését és futtatását.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához a következőkre lesz szüksége.

* A Kubernetes és a [Apache Spark][spark-quickstart]alapszintű ismerete.
* [Docker hub][docker-hub] -fiók vagy egy [Azure Container Registry][acr-create].
* Az Azure CLI [telepítve van][azure-cli] a fejlesztői rendszeren.
* A [JDK 8][java-install] telepítve van a rendszeren.
* Az [Apache Maven][maven-install] telepítve van a rendszeren.
* A SBT ([Scala Build eszköz][sbt-install]) telepítve van a rendszeren.
* A rendszerre telepített git parancssori eszközök.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

A Spark nagyméretű adatfeldolgozásra szolgál, és megköveteli, hogy a Kubernetes-csomópontok megfeleljenek a Spark-erőforrások követelményeinek. Az `Standard_D3_v2` Azure Kubernetes-szolgáltatás (ak) csomópontjainak minimális mérete ajánlott.

Ha olyan AK-fürtre van szüksége, amely megfelel ennek a minimális javaslatnak, futtassa a következő parancsokat.

Hozzon létre egy erőforráscsoportot a fürthöz.

```azurecli
az group create --name mySparkCluster --location eastus
```

Hozzon létre egy egyszerű szolgáltatásnevet a fürthöz. A létrehozást követően szüksége lesz a következő parancs egyszerű appId és jelszavára.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Hozza létre az AK-fürtöt a mérettel rendelkező csomópontokkal `Standard_D3_v2` , valamint a AppID és a jelszó értékeit a szolgáltatás-elsődleges és az ügyfél-titkos paraméterekként.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Kapcsolódjon az AK-fürthöz.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Ha Azure Container Registryt (ACR) használ a tárolók lemezképének tárolására, konfigurálja az AK és az ACR közötti hitelesítést. Tekintse meg ezeket a lépéseket az [ACR hitelesítési dokumentációjában][acr-aks] .

## <a name="build-the-spark-source"></a>A Spark-forrás létrehozása

Mielőtt a Spark-feladatokat egy AK-fürtön futtatja, létre kell hoznia a Spark forráskódját, és be kell csomagolnia egy tároló-rendszerképbe. A Spark-forrás olyan parancsfájlokat tartalmaz, amelyek a folyamat végrehajtásához használhatók.

A Spark Project-tárház klónozása a fejlesztői rendszeren.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Váltson át a klónozott tárház könyvtárára, és mentse a Spark forrásának elérési útját egy változóra.

```bash
cd spark
sparkdir=$(pwd)
```

Ha több JDK-verzió is van telepítve, állítsa `JAVA_HOME` a 8-as verzió használata az aktuális munkamenethez lehetőséget.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Futtassa a következő parancsot a Spark forráskódjának Kubernetes-támogatással történő létrehozásához.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

A következő parancsok létrehozzák a Spark-tároló rendszerképét, és leküldik azt egy tároló-rendszerkép beállításjegyzékbe. Cserélje le a `registry.example.com` nevet a tároló-beállításjegyzék nevére, és `v1` a használni kívánt címkére. Ha a Docker hub-t használja, ez az érték a beállításjegyzék neve. Azure Container Registry (ACR) használata esetén ez az érték az ACR bejelentkezési kiszolgáló neve.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Küldje le a tároló rendszerképét a tároló rendszerképének beállításjegyzékbe.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Spark-feladatok előkészítése

Következő lépésként készítse elő a Spark-feladatot. A Spark-feladatok tárolására szolgáló jar-fájl a parancs futtatásakor szükséges `spark-submit` . A jar nyilvános URL-címen vagy egy tároló-rendszerképben előre becsomagolva is elérhetővé tehető. Ebben a példában egy minta jar jön létre a PI érték kiszámításához. Ezt a jar-t ezután feltölti az Azure Storage-ba. Ha van egy meglévő jar, a helyettesítő

Hozzon létre egy könyvtárat, amelyben létre szeretné hozni a projektet egy Spark-feladatokhoz.

```bash
mkdir myprojects
cd myprojects
```

Hozzon létre egy új Scala-projektet egy sablonból.

```bash
sbt new sbt/scala-seed.g8
```

Ha a rendszer kéri, adja meg `SparkPi` a projekt nevét.

```bash
name [Scala Seed Project]: SparkPi
```

Navigáljon az újonnan létrehozott projekt könyvtárba.

```bash
cd sparkpi
```

Futtassa az alábbi parancsokat egy SBT beépülő modul hozzáadásához, amely lehetővé teszi a projekt jar-fájlként való csomagolását.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Futtassa ezeket a parancsokat a mintakód az újonnan létrehozott projektbe való másolásához, és adja hozzá az összes szükséges függőséget.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

A projekt Jarba való becsomagolásához futtassa a következő parancsot.

```bash
sbt assembly
```

A sikeres csomagolás után az alábbihoz hasonló kimenetnek kell megjelennie.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Másolási feladatok tárolóba

Hozzon létre egy Azure Storage-fiókot és-tárolót a jar-fájl tárolásához.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Töltse fel a jar-fájlt az Azure Storage-fiókba az alábbi parancsokkal.

```azurecli
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

A változó `jarUrl` most már tartalmazza a jar-fájl nyilvánosan elérhető elérési útját.

## <a name="submit-a-spark-job"></a>Spark-feladatok elküldése

Indítsa el a Kube-proxyt egy külön parancssorban a következő kóddal.

```bash
kubectl proxy
```

Váltson vissza a Spark-tárház gyökerére.

```bash
cd $sparkdir
```

Hozzon létre olyan szolgáltatásfiókot, amely megfelelő engedélyekkel rendelkezik a feladatok futtatásához.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Küldje el a feladatot a használatával `spark-submit` .

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Ez a művelet elindítja a Spark-feladatot, amely a feladatok állapotát a rendszerhéj-munkamenetbe továbbítja. A feladatok futtatása közben a Spark Driver Pod és a végrehajtó hüvelyek a kubectl Get hüvelyes paranccsal láthatók. Nyisson meg egy második terminál-munkamenetet a parancsok futtatásához.

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

A feladatok futtatása közben a Spark felhasználói felületét is elérheti. A második terminál-munkamenetben használja a `kubectl port-forward` parancsot a Spark felhasználói felülethez való hozzáférés biztosításához.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

A Spark felhasználói felületének eléréséhez nyissa meg a címeket `127.0.0.1:4040` egy böngészőben.

![Spark felhasználói felület](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>A feladatok eredményeinek és naplóinak beolvasása

A feladatoknak a befejezése után az illesztőprogram-Pod "befejezett" állapotban lesz. Szerezze be a pod nevét a következő paranccsal.

```bash
kubectl get pods --show-all
```

Kimenet:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

A `kubectl logs` paranccsal lekérhet naplókat a Spark Driver Pod-ból. Cserélje le a pod nevet az illesztőprogram-Pod nevére.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Ezen naplókon belül megtekintheti a Spark-feladatok eredményét, amely a PI értéke.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Jar csomag tároló képpel

A fenti példában a Spark jar-fájl fel lett töltve az Azure Storage-ba. Egy másik lehetőség, hogy a jar-fájlt egyéni kialakítású Docker-rendszerképekbe csomagolja.

Ehhez keresse meg a `dockerfile` (z) könyvtárban található Spark-rendszerképet `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` . Adjon hozzá egy `ADD` utasítást a Spark-feladatokhoz a `jar` és a `WORKDIR` `ENTRYPOINT` deklarációk között.

Frissítse a jar elérési utat a fájl helyére a `SparkPi-assembly-0.1.0-SNAPSHOT.jar` fejlesztői rendszeren. Használhatja a saját egyéni jar-fájlját is.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Felépítheti és leküldheti a képet a mellékelt Spark-parancsfájlokkal.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

A feladatok futtatásakor a rendszer a távoli jar URL-cím helyett a `local://` Docker-rendszerképben található jar-fájl elérési útját használja.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> A Spark [dokumentációjában][spark-docs]: "a Kubernetes Scheduler jelenleg kísérleti jellegű. A későbbi verziókban a konfiguráció, a tároló-lemezképek és a entrypoints viselkedési változásai lehetnek.

## <a name="next-steps"></a>Következő lépések

További részletekért tekintse meg a Spark dokumentációját.

> [!div class="nextstepaction"]
> [A Spark dokumentációja][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: /azure/developer/java/fundamentals/java-jdk-long-term-support
[maven-install]: https://maven.apache.org/install.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-kubernetes-earliest-version]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-quickstart]: ./index.yml
[azure-cli]: /cli/azure/
[storage-account]: ../storage/blobs/storage-quickstart-blobs-cli.md
