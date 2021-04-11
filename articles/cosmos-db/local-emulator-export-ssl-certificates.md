---
title: Az Azure Cosmos DB Emulator tanúsítványok exportálása
description: Ismerje meg, hogyan exportálhatja a Azure Cosmos DB Emulator-tanúsítványt Java-, Python-és Node.js-alkalmazásokkal való használatra. A tanúsítványokat exportálni kell, és olyan nyelvekhez és futásidejű környezetekhez kell használni, amelyek nem használják a Windows tanúsítványtárolót.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 952be09662c2c74f883d63de72bba2b9cb58d0e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554003"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>A Azure Cosmos DB Emulator-tanúsítványok exportálása Java-, Python-és Node.js-alkalmazásokkal való használatra
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Cosmos DB Emulator helyi környezetet biztosít, amely az Azure Cosmos DB szolgáltatást emulálja a fejlesztéshez. Azure Cosmos DB Emulator csak TLS-kapcsolatokon keresztül támogatja a biztonságos kommunikációt.

A Azure Cosmos DB helyi emulátorban lévő tanúsítványok az emulátor első futtatásakor jönnek létre. Két tanúsítvány van. Egyikük a helyi emulátorhoz való kapcsolódásra szolgál, a másik pedig az Emulator-adatemulátoron belüli alapértelmezett titkosítás kezelésére szolgál. Az exportálandó tanúsítvány a csatlakozáshoz való. Ennek felhasználóbarát neve "DocumentDBEmulatorCertificate".

Ha az emulátor használatával különböző nyelveken (például Java, Python vagy Node.js) fejleszt alkalmazásokat, exportálnia kell az Emulator-tanúsítványt, és importálnia kell azt a szükséges tanúsítványtárolóba.

A .NET nyelv és futtatókörnyezet a Windows-tanúsítványtárolót használja a Azure Cosmos DB helyi emulátorhoz való biztonságos csatlakozáshoz, amikor az alkalmazás egy Windows operációs rendszerű gazdagépen fut. A többi nyelv saját módszert használ a tanúsítványok kezelésére és használatára. A Java például saját [tanúsítványtárolót](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)használ, a Python [szoftvercsatorna-burkolókat](https://docs.python.org/2/library/ssl.html)használ, és Node.js [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)használ.

Ez a cikk bemutatja, hogyan exportálhatja a TLS/SSL-tanúsítványokat különböző nyelveken és futtatókörnyezeti környezetekben való használatra, amelyek nem integrálhatók a Windows-tanúsítványtárolóba. Az emulátorról a [Fejlesztés és tesztelés az Azure Cosmos DB Emulator használatával](./local-emulator.md) című cikk ír bővebben.

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>A Azure Cosmos DB TLS/SSL-tanúsítvány exportálása

Exportálnia kell az emulátor tanúsítványát, hogy az emulátor végpontját olyan nyelvekről és futásidejű környezetekről használja, amelyek nem integrálják a Windows-tanúsítványtárolót. A tanúsítványt a Windows tanúsítványkezelő használatával exportálhatja. A következő lépésenkénti útmutatót követve exportálhatja a "DocumentDBEmulatorCertificate" tanúsítványt BASE-64 kódolású X. 509 (. cer) fájlként:

1. Indítsa el a Windows Tanúsítványkezelőjét a certlm.msc futtatásával, nyissa meg a Személyes->Tanúsítványok mappát, majd nyissa meg a **DocumentDbEmulatorCertificate** nevű tanúsítványt.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Azure Cosmos DB helyszíni emulátor 1. exportálási lépés":::

1. Kattintson a **Részletek**, majd az **OK** lehetőségre.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Azure Cosmos DB helyszíni emulátor 2. exportálási lépés":::

1. Kattintson a **Másolás fájlba...** lehetőségre.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Azure Cosmos DB helyszíni emulátor 3. exportálási lépés":::

1. Kattintson a **Tovább** gombra.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Azure Cosmos DB helyszíni emulátor 4. exportálási lépés":::

1. Kattintson a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőségre, majd a **Tovább** gombra.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Azure Cosmos DB helyszíni emulátor 5. exportálási lépés":::

1. Kattintson a **Base-64 kódolású X.509 (.CER)** lehetőségre, majd a **Tovább** gombra.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Azure Cosmos DB helyszíni emulátor 6. exportálási lépés":::

1. Nevezze el a tanúsítványt. Ebben a példában a név **documentdbemulatorcert**. Végül kattintson a **Tovább** lehetőségre.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Azure Cosmos DB helyszíni emulátor 7. exportálási lépés":::

1. Kattintson a **Finish** (Befejezés) gombra.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Azure Cosmos DB helyszíni emulátor 8. exportálási lépés":::

## <a name="use-the-certificate-with-java-apps"></a>A tanúsítvány használata Java-alkalmazásokkal

Ha Java-alapú ügyfelet használó Java-alkalmazásokat vagy MongoDB-alkalmazásokat futtat, könnyebben telepítheti a tanúsítványt a Java alapértelmezett tanúsítványtárolóba, mint a `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` jelzők átadása. A mellékelt Java bemutató alkalmazás ( `https://localhost:8081/_explorer/index.html` ) például az alapértelmezett tanúsítványtárolótól függ.

Az X. 509 tanúsítvány az alapértelmezett Java-tanúsítványtárolóba való importálásához kövesse a [tanúsítvány hozzáadása a Java-tanúsítványok tárolójához](https://docs.oracle.com/cd/E54932_01/doc.705/e54936/cssg_create_ssl_cert.htm) című témakör utasításait. Ne feledje, hogy a *(z)% JAVA_HOME%* könyvtárban fog működni a-eszköz futtatásakor. Miután importálta a tanúsítványt a tanúsítványtárolóba, az SQL-és a Azure Cosmos DB API-MongoDB csatlakozni tudnak a Azure Cosmos DB emulátorhoz.

Azt is megteheti, hogy a következő bash-szkriptet futtatja a tanúsítvány importálásához:

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

Miután telepítette a "CosmosDBEmulatorCertificate" TLS/SSL-tanúsítványt, az alkalmazásnak képesnek kell lennie csatlakozni a helyi Azure Cosmos DB Emulator használatára. Ha problémák merülnek fel, kövesse az [SSL/TLS-kapcsolatok hibakeresése](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) című cikket. A legtöbb esetben előfordulhat, hogy a tanúsítvány nincs telepítve a *(z)% JAVA_HOME%/JRE/lib/security/cacerts* áruházba. Ha például a Java több telepített verziójával rendelkezik, előfordulhat, hogy az alkalmazás egy másik hitesítésszolgáltatói-tárolót használ, mint amit a frissített.

## <a name="use-the-certificate-with-python-apps"></a>A tanúsítvány használata Python-alkalmazásokkal

Ha Python-alkalmazásokból csatlakozik az emulátorhoz, a TLS-ellenőrzés le van tiltva. Alapértelmezés szerint a [PYTHON SDK (2.0.0 vagy újabb verzió)](sql-api-sdk-python.md) az SQL API-hoz nem fogja használni a TLS/SSL-tanúsítványt a helyi emulátorhoz való csatlakozáskor. Ha azonban TLS-ellenőrzést szeretne használni, kövesse a [Python socket Wrappers](https://docs.python.org/2/library/ssl.html) dokumentációjában található példákat.

## <a name="how-to-use-the-certificate-in-nodejs"></a>A tanúsítvány használata a Node.js-ben

Node.js SDK-k emulátorához való csatlakozáskor a TLS-ellenőrzés le van tiltva. Alapértelmezés szerint a [Node.js SDK (1.10.1 vagy újabb verzió)](sql-api-sdk-node.md) az SQL API-hoz nem fogja tudni használni a TLS/SSL-tanúsítványt a helyi emulátorhoz való csatlakozáskor. Ha azonban TLS-ellenőrzést szeretne használni, kövesse az [Node.js dokumentációjában](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)található példákat.

## <a name="rotate-emulator-certificates"></a>Emulator-tanúsítványok elforgatása

Az Emulator-tanúsítványok újbóli előállításához válassza az **adatok alaphelyzetbe állítása** lehetőséget a Windows tálcán futó Azure Cosmos db-emulátorban. Vegye figyelembe, hogy ez a művelet az emulátor által helyileg tárolt összes adat törlését is törli.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Azure Cosmos DB helyszíni emulátor adatok alaphelyzetbe állítása":::

Ha telepítette a tanúsítványt a Java-tanúsítványtárolóba, vagy másutt használta őket, újra kell importálnia azokat az aktuális tanúsítványok használatával. Az alkalmazás addig nem tud kapcsolódni a helyi emulátorhoz, amíg nem frissíti a tanúsítványokat.

## <a name="next-steps"></a>Következő lépések

* [Az emulátor vezérléséhez használjon parancssori paramétereket és PowerShell-parancsokat](emulator-command-line-parameters.md)
* [Hibakeresési problémák az emulátorral](troubleshoot-local-emulator.md)
