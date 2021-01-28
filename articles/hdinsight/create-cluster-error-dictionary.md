---
title: Azure HDInsight-fürt létrehozása – hiba szótár
description: Tudnivalók az Azure HDInsight-fürtök létrehozásakor előforduló hibák elhárításáról
author: karkrish
ms.author: v-todmc
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 08/24/2020
ms.openlocfilehash: 6b8c0069d619d3ebd87e3c54b4f653812199f590
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943331"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure-HDInsight: fürtök létrehozásával kapcsolatos hibák

Ez a cikk a fürtök létrehozásakor esetlegesen előforduló hibák megoldásait ismerteti.

> [!NOTE]
> A cikkben ismertetett első három hiba érvényesítési hiba. Akkor fordulhatnak elő, amikor egy Azure HDInsight-termék a **CsmDocument_2_0** osztályt használja.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Hibakód: a (z) "CsmDocument_2_0" DeploymentDocument nem tudta végrehajtani az ellenőrzést.

**Hiba**: "a parancsfájl műveleti helye nem érhető el URI: \<SCRIPT ACTION URL\> "

### <a name="error-message-1"></a>1. hibaüzenet

"A távoli kiszolgáló a következő hibát adta vissza: (404) nem található."

#### <a name="cause"></a>Ok

A HDInsight szolgáltatás nem fér hozzá a fürt létrehozása kérelem részeként megadott parancsfájl-művelet URL-címéhez. A szolgáltatás megkapja az előző hibaüzenetet, amikor megpróbál hozzáférni a parancsfájl művelethez.

#### <a name="resolution"></a>Feloldás

- HTTP-vagy HTTPS-URL-cím esetén ellenőrizze, hogy az URL-cím egy inkognitóban böngészőablakban található-e.
- WASB URL-cím esetén győződjön meg arról, hogy a parancsfájl létezik a kérelemben megadott Storage-fiókban. Győződjön meg arról is, hogy a Storage-fiókhoz tartozó tárolási kulcs helyes.
- ADLS URL-cím esetén győződjön meg arról, hogy a parancsfájl létezik a Storage-fiókban.

---

### <a name="error-message-2"></a>2. hibaüzenet

"A megadott parancsfájl URI \<SCRIPT_URI\> -ja a ADLS, de a fürthöz nem tartozik a (z)"

#### <a name="cause"></a>Ok

A HDInsight szolgáltatás nem fér hozzá a fürt létrehozása kérelem részeként megadott parancsfájl-művelet URL-címéhez. A szolgáltatás megkapja az előző hibaüzenetet, amikor megpróbál hozzáférni a parancsfájl művelethez.

#### <a name="resolution"></a>Feloldás

Adja hozzá a megfelelő Azure Data Lake Storage 1. generációs fiókot a fürthöz. Adja hozzá azt a szolgáltatásnevet is, amely hozzáfér a Data Lake Storage Gen 1 fiókhoz a fürthöz.

---

### <a name="error-message-3"></a>3. hibaüzenet

\<CUSTOMER_SPECIFIED_VM_SIZE\>A kérelemben megadott "virtuálisgép-méret" érvénytelen vagy nem támogatott a (z) "" szerepkörben \<ROLE\> . Az érvényes értékek a következők: \<VALID_VM_SIZE_FOR_ROLE\> . "

#### <a name="cause"></a>Ok

A virtuális gép megadott mérete nem engedélyezett a szerepkörhöz. Ez a hiba akkor fordulhat elő, ha a virtuális gép mérete nem a várt módon működik, vagy nem megfelelő a számítógép szerepkörhöz.

#### <a name="resolution"></a>Feloldás

A hibaüzenet felsorolja a virtuális gép méretének érvényes értékeit. Válassza ki az alábbi értékek egyikét, majd próbálja megismételni a fürt létrehozása kérelmet.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Hibakód: InvalidVirtualNetworkId  

### <a name="error"></a>Hiba

"A VirtualNetworkId érvénytelen. VirtualNetworkId ' \<USER_VIRTUALNETWORKID\> ' * '

### <a name="cause"></a>Ok

A fürt létrehozása során megadott **VirtualNetworkId** érték formátuma nem megfelelő.

### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy a **VirtualNetworkId** és az alhálózati értékek a megfelelő formátumban vannak. A **VirtualNetworkId** értékének lekérése:

1. Nyissa meg az Azure Portalt.
1. Válassza ki a virtuális hálózatot.
1. Válassza a **Tulajdonságok** menüpontot. A **ResourceId** tulajdonság értéke a **VirtualNetworkId** érték.

Íme egy példa a virtuális hálózati AZONOSÍTÓra:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Hibakód: CustomizationFailedErrorCode

### <a name="error"></a>Hiba

"A fürt központi telepítése nem sikerült, mert hiba történt az egyéni parancsfájl műveletében. Sikertelen műveletek: \<SCRIPT_NAME\> , lépjen a Ambari felhasználói felületén a hiba további hibakereséséhez. "

### <a name="cause"></a>Ok

A fürt létrehozásakor megadott egyéni parancsfájl végrehajtása a fürt sikeres telepítése után történik. Ez a hibakód azt jelzi, hogy hiba történt a nevű egyéni parancsfájl végrehajtása során \<SCRIPT_NAME\> .

### <a name="resolution"></a>Feloldás

Mivel a szkript az egyéni szkript, javasoljuk, hogy hárítsa el a problémát, és szükség esetén futtassa újra a parancsfájlt. A parancsfájl hibáinak megoldásához vizsgálja meg a/var/lib/ambari-Agent/* mappában található naplókat. Vagy nyissa meg a Ambari felhasználói felületén az **Operations (műveletek** ) lapot, majd a hiba részleteinek megtekintéséhez válassza ki a **run_customscriptaction** műveletet.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Hibakód: InvalidDocumentErrorCode

### <a name="error"></a>Hiba

"A \<META_STORE_TYPE\> metaadattár-séma verziója az \<METASTORE_MAJOR_VERSION\> adatbázisban \<DATABASE_NAME\> nem kompatibilis a fürt verziójával \<CLUSTER_VERSION\> "

### <a name="cause"></a>Ok

Az egyéni metaadattár nem kompatibilis a kiválasztott HDInsight-fürt verziójával. A HDInsight 4,0-fürtök jelenleg csak a 3,0-es és újabb Metaadattár-verziót támogatják, míg a HDInsight 3,6-fürtök nem támogatják a Metaadattár 3,0-es és újabb verzióit.

### <a name="resolution"></a>Feloldás

Csak azokat a Metaadattár-verziókat használja, amelyeket a HDInsight-fürt verziója támogat. Ha nem ad meg egyéni metaadattár, a HDInsight belsőleg létrehoz egy metaadattár, majd törli azt a fürt törlése után.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Hibakód: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Hiba

"Nem lehet csatlakozni a fürt felügyeleti végponthoz a skálázási művelet végrehajtásához. Ellenőrizze, hogy a hálózati biztonsági szabályok nem blokkolja-e a fürt külső hozzáférését, és hogy a Fürtfelügyelő (Ambari) felhasználói felülete sikeresen elérhető-e. "

### <a name="cause"></a>Ok

A hálózati biztonsági csoporton (NSG) található tűzfalszabály blokkolja a kritikus Azure állapot-és felügyeleti szolgáltatásokkal folytatott kommunikációt.

### <a name="resolution"></a>Feloldás

Ha hálózati biztonsági csoportokat kíván használni a hálózati forgalom szabályozásához, a HDInsight telepítése előtt végezze el a következő műveleteket:

- Azonosítsa az HDInsight használni kívánt Azure-régiót.
- Azonosítsa a HDInsight által igényelt IP-címeket. További információ: [HDInsight kezelése – IP-címek](./hdinsight-management-ip-addresses.md).
  - Hozza létre vagy módosítsa annak az alhálózatnak a hálózati biztonsági csoportjait, amelyre telepíteni kívánja a HDInsight-et.
  - Hálózati biztonsági csoportok esetében engedélyezze a bejövő forgalmat az 443-as porton az IP-címekről. Ez a konfiguráció biztosítja, hogy a HDInsight-kezelési szolgáltatások a virtuális hálózaton kívülről is elérjék a fürtöt.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Hibakód: StoragePermissionsBlockedForMsi

### <a name="error"></a>Hiba

"A felügyelt identitásnak nincs engedélye a Storage-fiókra. Ellenőrizze, hogy a "Storage blob-adatok tulajdonosa" szerepkör hozzá van-e rendelve a Storage-fiók felügyelt identitásához. Storage:/Subscriptions/ \<Subscription ID\> /ResourceGroups/ \< Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\> , felügyelt identitás:/Subscriptions/ \<Subscription ID\> /resourceGroups// \< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\> "

### <a name="cause"></a>Ok

Nem adta meg az identitás kezeléséhez szükséges engedélyeket. A felhasználó által hozzárendelt felügyelt identitás nem rendelkezik Blob Storage közreműködő szerepkörrel a Azure Data Lake Storage Gen2 Storage-fiókban.

### <a name="resolution"></a>Feloldás

1. Nyissa meg az Azure Portalt.
1. Nyissa meg a Storage-fiókját.
1. Keresse meg a **Access Control (iam)** alatt.
1. Győződjön meg arról, hogy a felhasználó rendelkezik a Storage blob adatközreműködői szerepkörrel vagy a hozzájuk rendelt Storage blob-adattulajdonosi szerepkörrel.

További információ: az [Data Lake Storage Gen2 fiókban található felügyelt identitás engedélyeinek beállítása](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Hibakód: InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>Hiba

"A biztonsági szabályok a hálózati biztonsági csoport/Subscriptions/ \<SubscriptionID\> /resourceGroups/<az erőforráscsoport neve \> default/Providers/Microsoft. Network/networkSecurityGroups/ \<Network Security Group Name\> subnet/Subscriptions/ \<SubscriptionID\> /resourceGroups/ \<Resource Group name\> RG-westeurope-vnet-TomTom-default/Providers/Microsoft. Network/virtualNetworks//Subnets/ \<Virtual Network Name\> nem \<Subnet Name\> engedélyezik a szükséges bejövő és/vagy kimenő kapcsolatokat. További információért látogasson el [a virtuális hálózat megtervezése az Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md), vagy forduljon az ügyfélszolgálathoz. "

### <a name="cause"></a>Ok

Ha a hálózati biztonsági csoportok vagy a felhasználó által megadott útvonalak (UDR-EK) vezérlik a HDInsight-fürt felé irányuló bejövő forgalmat, akkor győződjön meg arról, hogy a fürt képes kommunikálni a kritikus Azure állapot-és felügyeleti szolgáltatásokkal.

### <a name="resolution"></a>Feloldás

Ha hálózati biztonsági csoportokat kíván használni a hálózati forgalom szabályozásához, a HDInsight telepítése előtt végezze el a következő műveleteket:

- Azonosítsa a HDInsight használni kívánt Azure-régiót, és hozzon létre egy biztonságos listát a régiója IP-címeiről. További információ [: állapot-és kezelési szolgáltatások: adott régiók](./hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions).
- Azonosítsa a HDInsight által igényelt IP-címeket. További információ: [HDInsight-felügyeleti IP-címek](./hdinsight-management-ip-addresses.md).
- Hozza létre vagy módosítsa annak az alhálózatnak a hálózati biztonsági csoportjait, amelyre telepíteni kívánja a HDInsight-et. Hálózati biztonsági csoportok esetében engedélyezze a bejövő forgalmat az 443-as porton az IP-címekről. Ez a konfiguráció biztosítja, hogy a HDInsight-kezelési szolgáltatások a virtuális hálózaton kívülről is elérjék a fürtöt.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Hibakód: a fürt telepítése nem tudta telepíteni az összetevőket egy vagy több gazdagépre

### <a name="error"></a>Hiba

"A fürt beállítása nem tudta telepíteni az összetevőket egy vagy több gazdagépre. Próbálkozzon újra a kéréssel. "

### <a name="cause"></a>Ok 

Ez a hiba általában akkor jön létre, ha átmeneti probléma merül fel, vagy ha egy Azure-leállás történik.

### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy minden olyan Azure- [leállást](https://status.azure.com) tartalmaz, amely hatással lehet a fürt üzembe helyezésére. Ha nincsenek kimaradások, próbálja megismételni a fürt üzembe helyezését.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Hibakód: FailedToConnectWithClusterErrorCode

### <a name="error"></a>Hiba

Nem lehet csatlakozni a fürt felügyeleti végponthoz. Próbálkozzon újra később.

### <a name="cause"></a>Ok

A HDInsight szolgáltatás nem tud csatlakozni a fürthöz a fürt létrehozásakor

### <a name="resolution"></a>Feloldás

Ha az egyéni VNet hálózati biztonsági csoportot (NSG) és a felhasználó által megadott útvonalakat (UDR) használja, győződjön meg arról, hogy a fürt képes kommunikálni a HDInsight felügyeleti szolgáltatásaival. További információ: [HDInsight-felügyeleti IP-címek](./hdinsight-management-ip-addresses.md).

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Hibakód: az üzemelő példányok házirend megsértése miatt sikertelenek: a (z) erőforrást <Resource URI> a szabályzat nem engedélyezte. Házirend-azonosítók: "[{" policyAssignment ": {" Name ":" <Policy Name> "," id ":"/Providers/Microsoft.Management/managementGroups/ <Management Group Name> providers/Microsoft. Authorization/policyAssignments/ <Policy Name> "}," policyDefinition ": <Policy Definition>

### <a name="cause"></a>Ok

Az előfizetés-alapú Azure-szabályzatok nem tagadhatják meg a nyilvános IP-címek létrehozását. A HDInsight-fürt létrehozásához két nyilvános IP-cím szükséges.

A következő házirendek általában befolyásolják a fürtök létrehozását:

* Szabályzatok, amelyek megakadályozzák az IP-címek vagy terheléselosztó előfizetésen belüli létrehozását.
* Házirend, amely megakadályozza a Storage-fiókok létrehozását.
* Olyan házirend, amely megakadályozza a hálózati erőforrások, például az IP-címek vagy a terheléselosztó törlését.

### <a name="resolution"></a>Feloldás

Törölje vagy tiltsa le az előfizetés-alapú Azure Policy hozzárendelést a HDInsight-fürt létrehozásakor.

---

## <a name="next-steps"></a>Következő lépések

További információ a fürtök létrehozásával kapcsolatos hibák elhárításáról: [fürtök létrehozásával kapcsolatos hibák elhárítása az Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md).