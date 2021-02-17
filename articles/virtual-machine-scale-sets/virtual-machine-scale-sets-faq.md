---
title: Azure Virtual Machine Scale Sets – gyakori kérdések
description: Választ kaphat az Azure-beli virtuálisgép-méretezési csoportokkal kapcsolatos leggyakrabban feltett kérdésekre.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: faq
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 3bc259f9ee6cb1e6fd927af82a1740403d3ae7d8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587943"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Azure Virtual Machine Scale Sets – gyakori kérdések

Választ kaphat az Azure-beli virtuálisgép-méretezési csoportokkal kapcsolatos gyakori kérdésekre.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>A méretezési csoportokra vonatkozó leggyakoribb gyakori kérdések

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Hány virtuális gépet tartalmazhat egy méretezési csoport?

A méretezési csoportokhoz a platform rendszerképein alapuló 0 – 1 000 virtuális gépek, illetve 0 – 600 virtuális gépek állíthatók be egyéni lemezképek alapján.

### <a name="are-data-disks-supported-within-scale-sets"></a>Támogatott az adatlemezek használata a méretezési csoportokon belül?

Igen. A méretezési csoportok meghatározhatnak egy csatlakoztatott adatlemezekből álló konfigurációt, amely a csoport összes virtuális gépére érvényes. További információ: [Azure-beli méretezési csoportok és csatlakoztatott adatlemezek](virtual-machine-scale-sets-attached-disks.md). Egyéb adattárolási lehetőségek:

* Azure Files (SMB-megosztásos meghajtók)
* Operációs rendszer meghajtója
* Ideiglenes meghajtó (helyi, nem Azure Storage-alapú)
* Azure-adatszolgáltatás (például Azure-táblák, Azure-blobok)
* Külső adatszolgáltatás (például távoli adatbázis)

### <a name="which-azure-regions-support-scale-sets"></a>Mely Azure-régiók támogatják a méretezési csoportokat?

Mindegyik régió támogatja a méretezési csoportokat.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Hogyan lehet egyéni rendszerképekből méretezési csoportot létrehozni?

Hozzon létre és rögzítsen egy virtuálisgép-lemezképet, majd használja azt a méretezési csoport forrásaként. Az egyéni virtuálisgép-rendszerképek létrehozásáról és használatáról az [Azure CLI](tutorial-use-custom-image-cli.md) vagy a [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Ha a méretezési csoportom kapacitását 20-ról 15-re csökkentem, mely virtuális gépek lesznek eltávolítva?

Alapértelmezés szerint a rendszer eltávolítja a virtuális gépeket a méretezési csoportból egyenletesen a rendelkezésre állási zónák között (ha a méretezési csoport a zóna-konfigurációban van telepítve) és a tartalék tartományok a rendelkezésre állás maximalizálása érdekében. A rendszer a legmagasabb azonosítóval rendelkező virtuális gépeket távolítja el először.

A virtuális gép eltávolításának sorrendjét a méretezési csoport [méretezési házirendjének](virtual-machine-scale-sets-scale-in-policy.md) megadásával módosíthatja.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Mi történik, ha ezután 15-ről 18-ra növelem a kapacitást?

Ha 18-ra növeli a kapacitást, akkor a rendszer 3 új virtuális gépet hoz létre. A rendszer minden alkalommal a legmagasabb előző értéktől növeli a virtuálisgép-példány azonosítóját (például: 20, 21, 22). A virtuális gépek kiegyensúlyozva vannak a tartalék tartományok között.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Ha több bővítményt használok egy méretezési csoportban, van lehetőség végrehajtási sorrend kényszerítésére?

Igen, a méretezési csoport [bővítmények sorrendjét](virtual-machine-scale-sets-extension-sequencing.md)is használhatja.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Használhatok virtuálisgép-méretezési csoportokat Azure rendelkezésre állási csoportokkal?

A regionális (nem zónákra kiterjedő) méretezési *csoport elhelyezési csoportokat* használ, amelyek az implicit rendelkezésre állási csoportok öt tartalék tartománnyal és öt frissítési tartománnyal működnek. A több mint 100 virtuális gép méretezési csoportjai több elhelyezési csoportot is kiterjedhetnek. További információ az elhelyezési csoportokról: [Nagyméretű virtuálisgép-méretezési csoportok használata](virtual-machine-scale-sets-placement-groups.md). A virtuális gépek rendelkezésre állási csoportja létrejöhet ugyanabban a virtuális hálózatban, mint a virtuálisgép-méretezési csoport. Az egyik gyakran alkalmazott konfiguráció egy rendelkezésre állási csoportba helyezi a vezérlő csomópont virtuális gépeit (ezek gyakran igényelnek egyéni konfigurálást), és a méretezési csoportba helyezi az adatcsomópontokat.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Működnek a méretezési csoportok az Azure rendelkezésre állási zónákkal?

Igen! További információkért lásd a [méretezési csoport zónájának dokumentációját](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatikus méretezés

### <a name="what-are-best-practices-for-azure-autoscale"></a>Mik az Azure-ra vonatkozó ajánlott eljárások?

Az automatikus skálázás ajánlott eljárásaiért lásd: [ajánlott eljárások a virtuális gépek automatikus skálázásához](../azure-monitor/autoscale/autoscale-best-practices.md).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Hol találom meg a gazdagép-alapú metrikákat használó automatikus skálázás metrikáinak nevét?

A gazdagép-alapú metrikákat használó automatikus skálázás metrikáinak neveivel kapcsolatban lásd: [támogatott metrikák Azure monitor](../azure-monitor/essentials/metrics-supported.md).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Van-e példa az automatikus skálázásra egy Azure Service Bus témakör és a várólista hossza alapján?

Igen. Példa az automatikus skálázásra egy Azure Service Bus témakör és a várólista hossza alapján: az automatikus [skálázás általános metrikáinak Azure monitor](../azure-monitor/autoscale/autoscale-common-metrics.md).

Service Bus üzenetsor esetében használja a következő JSON-t:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Tárolási várólista esetén használja a következő JSON-t:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Cserélje le a példában szereplő értékeket az erőforrás egységes erőforrás-azonosítóra (URI).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>A gazdagép-alapú metrikák vagy a diagnosztikai bővítmények használatával kell autoskálázást használni?

Egy virtuális gépen létrehozhat egy autoskálázási beállítást, amely a gazdagép szintű metrikák vagy a vendég operációs rendszer alapú metrikák használatára szolgál.

A támogatott metrikák listáját itt tekintheti meg: [Azure monitor általános mérőszámok automatikus skálázása](../azure-monitor/autoscale/autoscale-common-metrics.md).

A virtuálisgép-méretezési csoportok teljes mintája a [virtuálisgép-méretezési csoportok Resource Manager-sablonjainak használatával: speciális automatikus skálázási konfiguráció](../azure-monitor/autoscale/autoscale-virtual-machine-scale-sets.md).

A minta a gazdagép szintű CPU-metrikát és egy üzenet darabszám metrikáját használja.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Hogyan beállítani a riasztási szabályokat egy virtuálisgép-méretezési csoporton?

A virtuális gépek méretezési csoportjaihoz a PowerShell vagy az Azure CLI használatával riasztásokat hozhat létre. További információ: [Azure monitor PowerShell](../azure-monitor/powershell-samples.md#create-metric-alerts) -gyors példák és [Azure monitor platformfüggetlen CLI-gyors minták](../azure-monitor/cli-samples.md#work-with-alerts).

A virtuálisgép-méretezési csoport Targetresourceid azonosítója a következőképpen néz ki:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Bármelyik virtuálisgép-teljesítményszámláló kiválasztható metrikaként a riasztások beállításához. További információkért lásd: [vendég operációs rendszer mérőszámai a Resource Manager-alapú Windows rendszerű virtuális gépekhez](../azure-monitor/autoscale/autoscale-common-metrics.md#guest-os-metrics-for-resource-manager-based-windows-vms) és a Linux rendszerű [virtuális gépek vendég operációs rendszer metrikái](../azure-monitor/autoscale/autoscale-common-metrics.md#guest-os-metrics-linux-vms) a [Azure monitor automatikus skálázás általános mérőszámai](../azure-monitor/autoscale/autoscale-common-metrics.md) cikkben.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Hogyan beállítani az autoskálázást a virtuálisgép-méretezési csoportokon a PowerShell használatával?

Ha a PowerShell használatával szeretné beállítani az automatikus skálázást a virtuálisgép-méretezési csoporton, olvassa el [a virtuálisgép-méretezési csoport automatikus skálázása](tutorial-autoscale-powershell.md)című témakört. Az [Azure CLI](tutorial-autoscale-cli.md) és az [Azure-sablonok](tutorial-autoscale-template.md) használatával is konfigurálhatja az autoskálázást


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Ha leállítottam (lefoglaltam) egy virtuális gépet, az az, hogy a virtuális gép egy autoskálázási művelet részeként elindult?

Nem. Ha az autoskálázási szabályok további virtuálisgép-példányokat igényelnek a méretezési csoport részeként, létrejön egy új virtuálisgép-példány. A leállított (delefoglalt) virtuálisgép-példányok nem kezdődnek el az autoscale esemény részeként. A leállított (megszüntetett) virtuális gépeket azonban törölheti egy, a példányok számának megfelelő méretezési esemény részeként, ugyanúgy, ahogyan a virtuálisgép-példányok a virtuálisgép-példányok AZONOSÍTÓjának sorrendje alapján törölhetők.



## <a name="certificates"></a>Tanúsítványok

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Hogyan biztonságosan szállítson egy tanúsítványt a virtuális géphez?

A tanúsítványoknak a virtuális géphez való biztonságos szállításához közvetlenül telepítheti az ügyfél kulcstárolóját egy Windows-tanúsítványtárolóba.

Használja a következő JSON-t:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

A kód támogatja a Windowst és a Linuxot.

További információ: virtuálisgép- [méretezési csoport létrehozása vagy frissítése](/rest/api/compute/virtualmachinescalesets/createorupdate).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Hogyan használhat az Azure Service Fabric-fürtökhöz kiépített önaláírt tanúsítványokat?
A legújabb példa a következő Azure CLI-utasítást használja az Azure shellben, olvassa el a Service Fabric CLI-modul – példa dokumentációját, amely az stdout-ra lesz kinyomtatva:

```azurecli
az sf cluster create -h
```

Az önaláírt tanúsítványok nem használhatók a hitelesítésszolgáltató által biztosított elosztott megbízhatósági kapcsolatokhoz, és nem használhatók olyan Service Fabric-fürthöz, amely vállalati üzemi megoldások üzemeltetésére szolgál. További Service Fabric biztonsági útmutatásért tekintse át az [Azure Service Fabric biztonsági eljárásokat](../security/fundamentals/service-fabric-best-practices.md) és az [Service Fabric-fürtök biztonsági forgatókönyveit](../service-fabric/service-fabric-cluster-security.md).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Megadhatok egy SSH-kulcspárt az SSH-hitelesítéshez egy Resource Manager-sablonból származó linuxos virtuálisgép-méretezési csoport használatával?

Igen. A **osProfile** REST API a standard VM-REST API hasonló.

**OsProfile** belefoglalása a sablonba:

```json
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```

Ez a JSON-blokk ebben az [Azure gyorsindítási sablonban](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)van használatban.

További információ: virtuálisgép- [méretezési csoport létrehozása vagy frissítése](/rest/api/compute/virtualmachinescalesets/createorupdate#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Hogyan eltávolítja az elavult tanúsítványokat?

Az elavult tanúsítványok eltávolításához távolítsa el a régi tanúsítványt a tároló-tanúsítványok listából. Hagyja meg az összes olyan tanúsítványt, amelyet meg szeretne őrizni a számítógépen a listában. Ez nem távolítja el a tanúsítványt az összes virtuális gépről. Emellett nem adja hozzá a tanúsítványt a virtuálisgép-méretezési csoportba létrehozott új virtuális gépekhez.

Ha el szeretné távolítani a tanúsítványt a meglévő virtuális gépekről, egyéni parancsfájl-bővítmény használatával távolítsa el manuálisan a tanúsítványokat a tanúsítványtárolóból.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Hogyan befecskendez egy meglévő nyilvános SSH-kulcsot a virtuálisgép-méretezési csoport SSH-rétegére a kiépítés során?

Ha a virtuális gépeket csak nyilvános SSH-kulccsal látja el, a nyilvános kulcsokat nem kell Key Vaultba helyeznie. A nyilvános kulcsok nem titkosak.

Linuxos virtuális gép létrehozásakor egyszerű szövegként is megadhat SSH nyilvános kulcsokat:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
}
```

linuxConfiguration elem neve | Kötelező | Típus | Description
--- | --- | --- | ---
SSH | No | Gyűjtemény | Megadja egy Linux operációs rendszer SSH-kulcsának konfigurációját
path | Igen | Sztring | Megadja a Linux-fájl elérési útját, ahol az SSH-kulcsokat vagy a tanúsítványokat kell elhelyezni
alapértékek | Igen | Sztring | Base64 kódolású nyilvános SSH-kulcsot ad meg

Példaként tekintse meg [a 101-VM-Sshkey GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)rövid útmutatójának sablonját.

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Ha `Update-AzVmss` ugyanazon kulcstartóból egynél több tanúsítvány hozzáadását követően futtatok, a következő üzenet jelenik meg:

>Update-AzVmss: a titkos lista a/Subscriptions//ResourceGroups/Internal-RG-dev/Providers/Microsoft.KeyVault/Vaults/Internal-keyvault-dev ismétlődő példányait tartalmazza \<my-subscription-id> , ami nem engedélyezett.

Ez akkor fordulhat elő, ha ugyanazt a tárat próbálja újra felvenni a meglévő forrás-tárolóhoz tartozó új tár tanúsítványának használata helyett. A `Add-AzVmssSecret` parancs nem működik megfelelően, ha további titkos kulcsokat ad hozzá.

Ha több titkot szeretne hozzáadni ugyanahhoz a kulcstartóhoz, frissítse a $vmss. properties. osProfile. Secrets [0]. vaultCertificates listát.

A várt bemeneti struktúra esetében lásd: [virtuális gép készletének létrehozása vagy frissítése](/rest/api/compute/virtualmachinescalesets/createorupdate).

Keresse meg a titkot a Key vaultban található virtuálisgép-méretezési csoport objektumban. Ezután adja hozzá a tanúsítvány referenciáját (az URL-címet és a titkos tároló nevét) a tárolóhoz társított listához.

> [!NOTE]
> Jelenleg a virtuálisgép-méretezési csoport API használatával nem távolíthatja el a virtuális gépek tanúsítványait.
>

Az új virtuális gépeken nem lesz a régi tanúsítvány. Azonban a tanúsítvánnyal rendelkező virtuális gépek, amelyek már telepítve vannak, a régi tanúsítvánnyal fognak rendelkezni.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Leküldhetem a tanúsítványokat a virtuálisgép-méretezési csoportba a jelszó megadása nélkül, ha a tanúsítvány a titkos tárolóban található?

Parancsfájlokban nem szükséges a jelszavakhoz tartozó programkódok használata. Az üzembe helyezési parancsfájl futtatásához használt engedélyekkel dinamikusan lekérheti a jelszavakat. Ha olyan szkripttel rendelkezik, amely a titkos tároló kulcstárolóból helyez át egy tanúsítványt, a titkos tároló `get certificate` parancs a. pfx fájl jelszavát is megjeleníti.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Hogyan működik az virtualMachineProfile. osProfile Secrets tulajdonsága a virtuálisgép-méretezési csoportokhoz? Miért van szükség a sourceVault értékre, ha meg kell adni a tanúsítvány abszolút URI azonosítóját a certificateUrl tulajdonsággal?

A Rendszerfelügyeleti webszolgáltatások (WinRM) tanúsítványának hivatkozását az operációs rendszer profiljának Secrets tulajdonságában kell megadnia.

A forrás-tároló megjelenítésének célja, hogy kikényszerítse a felhasználó Azure Cloud Service-modelljében található hozzáférés-vezérlési lista (ACL) házirendjeit. Ha nincs megadva a forrás-tároló, akkor a Key vaulthoz tartozó titkos kulcsok üzembe helyezésére és elérésére nem jogosult felhasználók egy számítási erőforrás-szolgáltatón (CRP-on) keresztül tudnak hozzáférni. Az ACL-ek még a nem létező erőforrások esetében is léteznek.

Ha helytelen forrás-tároló-azonosítót ad meg, de érvényes kulcstartó URL-címet ad meg, a rendszer hibát jelez a művelet lekérdezése során.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Ha egy meglévő virtuálisgép-méretezési csoporthoz veszek fel titkokat, a titkos kulcsokat a rendszer befecskendezi a meglévő virtuális gépekbe, vagy csak újakba?

A rendszer az összes virtuális géphez hozzáadja a tanúsítványokat, még a korábban is. Ha a virtuálisgép-méretezési csoport upgradePolicy tulajdonsága **kézi** értékre van állítva, a rendszer hozzáadja a tanúsítványt a virtuális géphez, amikor manuális frissítést hajt végre a virtuális gépen.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Hová helyezhetem a Linux rendszerű virtuális gépek tanúsítványait?

A Linux rendszerű virtuális gépek tanúsítványainak központi telepítésének megismeréséhez lásd: [tanúsítványok központi telepítése virtuális gépekre egy ügyfél által felügyelt kulcstartóból](/archive/blogs/kv/deploy-certificates-to-vms-from-customer-managed-key-vault).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Hogyan új tanúsítványtároló-tanúsítványt egy új tanúsítvány-objektumhoz?

Ha egy tár tanúsítványát meglévő titkos kulcshoz szeretné adni, tekintse meg a következő PowerShell-példát. Csak egy titkos objektumot használjon.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Mi történik a tanúsítványokkal, ha alaphelyzetbe állítja a virtuális gépet?

Ha alaphelyzetbe állítja a virtuális gépet, a rendszer törli a tanúsítványokat. Az újrarendszerkép törli a teljes operációsrendszer-lemezt.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Mi történik, ha töröl egy tanúsítványt a Key vaultból?

Ha a titkos kulcsot törli a kulcstartóból, majd futtatja az összes virtuális gépre, majd `stop deallocate` újra elindítja őket, hiba lép fel. A hiba azért fordul elő, mert a CRP-nak le kell kérnie a titkos kulcsokat a Key vaultból, de nem. Ebben a forgatókönyvben törölheti a tanúsítványokat a virtuálisgép-méretezési csoport modelljéből.

A CRP-összetevő nem őrzi meg az ügyfelek titkos adatait. Ha `stop deallocate` a-t a virtuálisgép-méretezési csoport összes virtuális gépén futtatja, a rendszer törli a gyorsítótárat. Ebben a forgatókönyvben a titkos kulcsokat a Key vaultból kéri le a rendszer.

Ez a probléma nem fordulhat elő, mert a titkos kulcs gyorsítótárazott másolata az Azure Service Fabric (az egyhálós bérlői modellben).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Miért kell megadnia a tanúsítvány verzióját a Key Vault használatakor?

A tanúsítvány verziójának megadásához szükséges Key Vault célja, hogy egyértelmű legyen a felhasználó számára, hogy milyen tanúsítvány van telepítve a virtuális gépeken.

Ha létrehoz egy virtuális gépet, majd a Key vaultban frissíti a titkos kulcsot, az új tanúsítvány nem töltődik le a virtuális gépekre. A virtuális gépek azonban úgy jelennek meg, hogy hivatkoznak rá, és az új virtuális gépek megkapják az új titkot. Ennek elkerüléséhez egy titkos verzióra kell hivatkoznia.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>A csapatom számos, a. cer nyilvános kulcsként terjesztett tanúsítvánnyal működik együtt. Mi az ajánlott módszer ezeknek a tanúsítványoknak a virtuálisgép-méretezési csoportokra való üzembe helyezéséhez?

Ha a. cer nyilvános kulcsait egy virtuálisgép-méretezési csoportba kívánja telepíteni, létrehozhat egy. pfx fájlt, amely csak. cer fájlokat tartalmaz. Ehhez használja a következőt: `X509ContentType = Pfx` . Töltse be például a. cer fájlt X509certificate2)-objektumként a C# vagy a PowerShell használatával, majd hívja meg a metódust.

További információ: [x509. export metódus (X509ContentType, string)](/dotnet/api/system.security.cryptography.x509certificates.x509certificate.export?view=netcore-3.1#system_security_cryptography_x509certificates_x509certificate_export_system_security_cryptography_x509certificates_x509contenttype_system_string_).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>A tanúsítványokat Base64-karakterláncként Hogyan továbbítani?

A tanúsítvány Base64-karakterláncként való átadásának emulálása érdekében kibonthatja a legújabb verzióban elérhető URL-címet egy Resource Manager-sablonban. Adja meg a következő JSON-tulajdonságot a Resource Manager-sablonban:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Be kell csomagolni a tanúsítványokat JSON-objektumokba a Key vaultokban?

A virtuálisgép-méretezési csoportokban és virtuális gépeken a tanúsítványokat JSON-objektumokban kell becsomagolni.

A Content Type Application/x-PKCS12/pfx-profil is támogatott.

Jelenleg nem támogatjuk a. cer fájlokat. A. cer fájlok használatához exportálja őket. pfx-tárolóba.



## <a name="compliance-and-security"></a>Megfelelőség és biztonság

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>A virtuálisgép-méretezési készletek PCI-kompatibilisek-e?

A virtuálisgép-méretezési csoportok egy vékony API-réteget alkotnak a KSZT tetején. Mindkét összetevő a számítási platform részét képezi az Azure-szolgáltatások rendszerében.

A megfelelőség szempontjából nézve a virtuálisgép-méretezési csoportok az Azure számítási platformjának alapvető részét képezik. Ezek a csoportok ugyanazt csapatot, eszközöket, folyamatokat, üzembe helyezési módszereket, biztonsági vezérlőket, igény szerinti (JIT) fordításokat, felügyeletet, riasztásokat stb. használják, mint maga a KSZT. A virtuálisgép-méretezési csoportok megfelelnek a PCI-szabványnak, mert a KSZT része a jelenlegi PCI Data Security Standard (DSS) igazolásnak.

További információkért lásd: [Microsoft Adatvédelmi központ](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Működik a [felügyelt identitások az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) a virtuálisgép-méretezési csoportokkal?

Igen. A [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) és a [Windows rendszerhez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi)készült Azure gyorsindítási sablonokban néhány példát láthat az MSI-sablonokra.

## <a name="deleting"></a>Törlés

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Figyelembe veszik a virtuálisgép-méretezési csoport példányain beállított zárolásokat a példányok törlésekor?

Az Azure Portalon lehetősége van egy adott példány vagy tömeges törlés törlésére több példány kiválasztásával. Ha olyan példányt próbál meg törölni, amely zárolva van, a rendszer betartja a zárolást, és nem fogja tudni törölni a példányt. Ha azonban több példányt szeretne kijelölni, és ezek bármelyike zárolva van, a rendszer nem veszi figyelembe a zárolás (oka) t, és az összes kiválasztott példány törölve lesz.

Az Azure CLI-ben csak az egyes példányok törölhetők. Ha olyan példányt próbál meg törölni, amely zárolva van, a rendszer betartja a zárolást, és nem fogja tudni törölni a példányt.

## <a name="extensions"></a>Bővítmények

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Hogyan törölni a virtuálisgép-méretezési csoport bővítményét?

A virtuálisgép-méretezési csoport bővítményének törléséhez használja a következő PowerShell-példát:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

A extensionName értéke megtalálható a ben `$vmss` .

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Van egy virtuálisgép-méretezési csoport sablonja, amely integrálható Azure Monitor naplókba?

Egy virtuálisgép-méretezési csoport sablonja, amely integrálható Azure Monitor naplókba, tekintse meg a második példát az [Azure Service Fabric-fürt üzembe helyezése és a figyelés Azure monitor naplók használatával történő engedélyezéséhez](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Hogyan bővítményt hozzáadni a virtuálisgép-méretezési csoport összes virtuális gépére?

Ha a frissítési szabályzat **automatikus** értékre van állítva, akkor a sablon új bővítmény tulajdonságaival való újbóli üzembe helyezése frissíti az összes virtuális gépet.

Ha a frissítési szabályzat **manuális** értékre van állítva, először frissítse a bővítményt, majd manuálisan frissítse a virtuális gépek összes példányát.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Ha a meglévő virtuálisgép-méretezési csoportokhoz társított bővítmények frissülnek, a meglévő virtuális gépek is érintettek?

Ha a bővítmény definíciója a virtuálisgép-méretezési csoport modelljében frissül, és a upgradePolicy tulajdonság értéke **automatikus**, akkor frissíti a virtuális gépeket. Ha a upgradePolicy tulajdonság beállítása **kézi**, a bővítmények a modellnek nem megfelelőként vannak megjelölve.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>A bővítmények újra futnak, amikor egy meglévő gép szolgáltatás által meggyógyult vagy alaphelyzetbe áll?

Ha egy meglévő virtuális gép szolgáltatás által meggyógyult, akkor a rendszer újraindítással jelenik meg, és a bővítmények nem futnak újra. Ha a virtuális gép rendszerképét alaphelyzetbe állítja, a folyamat hasonló módon helyettesíti az operációsrendszer-meghajtót a forrás rendszerképpel. A legújabb modelltől (például a bővítmények) való specializáció újra fut.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Hogyan csatlakoztatni egy virtuálisgép-méretezési készletet egy Active Directory tartományhoz?

Egy virtuálisgép-méretezési csoport Active Directory (AD) tartományhoz való csatlakoztatásához megadhat egy bővítményt.

A bővítmény definiálásához használja a JsonADDomainExtension tulajdonságot:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>A virtuálisgép-méretezési csoport bővítménye megpróbál telepíteni egy újraindítást igénylő újat.

Ha a virtuálisgép-méretezési csoport bővítménye olyan dolgot próbál telepíteni, amely újraindítást igényel, használhatja a Azure Automation kívánt állapot-konfiguráció (Automation DSC) bővítményt. Ha az operációs rendszer a Windows Server 2012 R2, az Azure lekéri a Windows Management Framework (WMF) 5,0 telepítését, újraindul, majd folytatja a konfigurálást.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Hogyan bekapcsolni az antimalware-t a virtuálisgép-méretezési csoporton?

A virtuálisgép-méretezési csoport antimalware szolgáltatásának bekapcsolásához használja a következő PowerShell-példát:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Hogyan egy egyéni parancsfájlt, amelyet egy privát Storage-fiókban futtat?

Egy privát Storage-fiókban üzemeltetett egyéni parancsfájl végrehajtásához állítsa be a védett beállításokat a Storage-fiók kulcsával és nevével. További információ: [Egyéni szkriptek bővítménye](../virtual-machines/extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json#property-managedidentity).

## <a name="passwords"></a>Jelszavak

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Hogyan a virtuálisgép-méretezési csoportba tartozó virtuális gépek jelszavának alaphelyzetbe állítása?

A virtuális gépek jelszava a méretezési csoportokban két fő módon módosítható.

- Módosítsa a virtuálisgép-méretezési csoport modelljét közvetlenül. Elérhető a 2017-12-01-es és újabb API-val.

    Frissítse a rendszergazdai hitelesítő adatokat közvetlenül a méretezési csoport modelljében (például a Azure Erőforrás-kezelő, a PowerShell vagy a parancssori felület használatával). A méretezési csoport frissítése után minden új virtuális gép rendelkezik az új hitelesítő adatokkal. A meglévő virtuális gépek csak akkor rendelkeznek új hitelesítő adatokkal, ha alaphelyzetbe állnak.

- Állítsa alaphelyzetbe a jelszót a VM-hozzáférési bővítmények használatával. Győződjön meg arról, hogy az [itt](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)leírtak szerint kövesse a jelszóra vonatkozó követelményeket.

    Használja a következő PowerShell-példát:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Hálózatkezelés

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Lehet hálózati biztonsági csoportot (NSG) rendelni egy méretezési csoporthoz, hogy az a készlet összes virtuálisgép-hálózati adapterére vonatkozzon?

Igen. Egy hálózati biztonsági csoport közvetlenül alkalmazható a méretezési csoportokra úgy, hogy a hálózati profil Networkinterfaceconfigurations szakaszához szakaszában hivatkozik rá. Példa:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Hogyan a virtuális gépek méretezési csoportjaihoz tartozó VIP-cserét ugyanabban az előfizetésben és régióban?

Ha két virtuálisgép-méretezési csoporttal rendelkezik Azure Load Balancer előtérrel, és ugyanabban az előfizetésben és régióban van, akkor felszabadíthatja a nyilvános IP-címeket, és hozzárendelheti a másikhoz. Lásd [: VIP-swap: kék-zöld üzembe helyezés Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) például. Ez egy késleltetést is jelent, ha az erőforrásokat a hálózati szinten fel kell osztani vagy le kell osztani. A gyorsabb megoldás az Azure Application Gateway használata két háttér-készlettel és egy útválasztási szabály használatával. Azt is megteheti, hogy az alkalmazást az [Azure app Service szolgáltatással](https://azure.microsoft.com/services/app-service/) üzemelteti, amely támogatást nyújt az átmeneti és a üzemi tárolóhelyek közötti gyors váltáshoz.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Hogyan a statikus magánhálózati IP-címek kiosztásához használt magánhálózati IP-címek tartományát kell megadni?

Az IP-címek a megadott alhálózatból vannak kiválasztva.

A virtuálisgép-méretezési csoport IP-címeinek kiosztási módszere mindig "dinamikus", de ez nem jelenti azt, hogy ezek az IP-címek módosíthatók. Ebben az esetben a "dinamikus" érték csak azt jelenti, hogy nem adja meg az IP-címet egy PUT-kérelemben. Adja meg a statikus készletet az alhálózat használatával.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Hogyan egy virtuálisgép-méretezési csoport központi telepítését egy meglévő Azure-beli virtuális hálózatra?

A virtuálisgép-méretezési csoport meglévő Azure-beli virtuális hálózatra való telepítéséhez lásd: [virtuálisgép-méretezési csoport telepítése meglévő virtuális hálózatra](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Használhatom a gyorsított hálózatkezeléssel rendelkező méretezési csoportokat?

Igen. A gyorsított hálózatkezelés használatához állítsa a enableAcceleratedNetworking True értékre a méretezési csoport Networkinterfaceconfigurations szakaszához beállításainál. Példa:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "niconfig1",
            "properties": {
                "primary": true,
                "enableAcceleratedNetworking" : true,
                "ipConfigurations": [
                ]
            }
        }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Hogyan állíthatom be a méretezési csoport által használt DNS-kiszolgálókat?

Ha egyéni DNS-konfigurációval rendelkező virtuálisgép-méretezési készletet szeretne létrehozni, adjon hozzá egy dnsSettings JSON-csomagot a méretezési csoport Networkinterfaceconfigurations szakaszához szakaszhoz. Példa:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Hogyan konfigurálható egy méretezési csoport egy nyilvános IP-cím az egyes virtuális gépekhez való hozzárendeléséhez?

Egy olyan virtuálisgép-méretezési csoport létrehozásához, amely egy nyilvános IP-címet rendel az egyes virtuális gépekhez, győződjön meg arról, hogy a Microsoft. számítási/virtualMachineScaleSets erőforrásának API-verziója 2017-03-30, és adjon hozzá egy _publicipaddressconfiguration_ JSON-csomagot a méretezési csoport ipConfigurations szakaszához. Példa:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Konfigurálható egy méretezési csoport több Application Gateway-átjáróval való együttműködéshez?

Igen. A méretezési csoport hálózati profiljának _ipConfigurations_ szakaszának _applicationGatewayBackendAddressPools_ listájához több Application Gateway háttérbeli címkészlet erőforrás-azonosítóit is hozzáadhatja.

## <a name="scale"></a>Méretezés

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>Milyen esetben hozzon létre egy virtuálisgép-méretezési készletet kevesebb mint két virtuális géppel?

Egy virtuálisgép-méretezési csoport kevesebb mint két virtuális géppel való létrehozásának egyik oka az, hogy egy virtuálisgép-méretezési csoport rugalmas tulajdonságait kellene használnia. A virtuálisgép-méretezési csoport például nulla virtuális gépekkel is üzembe helyezhető az infrastruktúra definiálásához anélkül, hogy a virtuális gép üzemeltetési költségeit kellene fizetnie. Ezután, amikor készen áll a virtuális gépek üzembe helyezésére, növelje a virtuálisgép-méretezési csoport "kapacitását" a termelési példányok számánál.

Egy másik ok, hogy létrehozhat egy virtuálisgép-méretezési készletet, amelynek kevesebb mint két virtuális gépe van, ha a rendelkezésre állás kevesebb, mint a különálló virtuális gépekkel rendelkező rendelkezésre állási csoport használata esetén. A virtuálisgép-méretezési csoportok lehetővé teszik, hogy a nem differenciált számítási egységeket helyettesítse. Ez az egységesség a virtuálisgép-méretezési csoportok és a rendelkezésre állási csoportok kulcsfontosságú differenciálása. Számos állapot nélküli számítási feladat nem nyomon követheti az egyes egységeket. Ha a számítási feladat elveszik, a méretezést lekicsinyítheti egy számítási egységre, majd akár többre is felskálázást hajthat végre a munkaterhelés növekedésével.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Hogyan módosíthatja a virtuálisgép-méretezési csoportba tartozó virtuális gépek számát?

Ha módosítani szeretné egy virtuálisgép-méretezési csoportban lévő virtuális gépek számát a Azure Portalban, a virtuálisgép-méretezési csoport tulajdonságai szakaszban kattintson a "skálázás" panelre, és használja a csúszka sávot.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Hogyan egyéni riasztásokat határozhat meg bizonyos küszöbértékek elérésekor?

A megadott küszöbértékekkel kapcsolatos riasztások kezelése némi rugalmasságot biztosít. Megadhatja például a testreszabott webhookokat. A következő webhook-példa egy Resource Manager-sablonból származik:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Javítások és műveletek

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Létrehozhatok egy méretezési csoportot egy meglévő erőforráscsoporthoz?

Igen, létrehozhat egy méretezési csoportot egy meglévő erőforráscsoporthoz.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Át lehet helyezni egy méretezési csoportot egy másik erőforráscsoporthoz?

Igen, a méretezési csoport erőforrásai áthelyezhetők egy új előfizetésre vagy erőforráscsoport-csoportba.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Hogyan frissíthetem a virtuálisgép-méretezési csoportját egy új képre? Hogyan a javítások kezelése?

A virtuálisgép-méretezési csoport új rendszerképre való frissítéséhez és a javítások kezeléséhez tekintse meg [a virtuálisgép-méretezési csoport frissítése](./virtual-machine-scale-sets-upgrade-scale-set.md)című témakört.

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Használhatom a rendszerkép visszaállítása műveletet a virtuális gép alaphelyzetbe állításához a rendszerkép módosítása nélkül? (Azaz szeretnék alaphelyzetbe állítani egy virtuális gépet a gyári beállításokra új rendszerkép helyett.)

Igen, a rerendszerkép művelettel a rendszerkép módosítása nélkül állíthatja alaphelyzetbe a virtuális gépet. Ha azonban a virtuálisgép-méretezési csoport egy platform-rendszerképre hivatkozik `version = latest` , a virtuális gép egy későbbi operációsrendszer-rendszerképre tud frissíteni a hívásakor `reimage` .

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Lehetséges a méretezési csoportok integrálása Azure Monitor naplókkal?

Igen, telepítheti a Azure Monitor bővítményt a méretezési csoport virtuális gépei között. Az alábbi példa egy Azure CLI-példát mutat be:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

A szükséges munkaterület azonosítója és workspaceKey a Azure Portal Log Analytics munkaterületén találhatja meg. Az Áttekintés lapon kattintson a beállítások csempére. Kattintson a felül található csatlakoztatott források fülre.

> [!NOTE]
> Ha a méretezési csoport _UpgradePolicy_ manuális értékre van állítva, a bővítményt a készletben lévő összes virtuális gépre alkalmaznia kell a frissítés meghívásával. A CLI-ben ez az _az vmss Update-instances_ lenne.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Hogyan bekapcsolja a rendszerindítási diagnosztikát?

A rendszerindítási diagnosztika bekapcsolásához először hozzon létre egy Storage-fiókot. Ezután helyezze el ezt a JSON-blokkot a virtuálisgép-méretezési csoport **virtualMachineProfile**, és frissítse a virtuálisgép-méretezési csoportját:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Új virtuális gép létrehozásakor a virtuális gép InstanceView tulajdonsága a képernyőkép részleteit jeleníti meg, és így tovább. Bemutatunk egy példát:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Virtuális gép tulajdonságai

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Hogyan lekérni az egyes virtuális gépekre vonatkozó információkat több hívás nélkül? Hogyan szerezhetem be a tartalék tartományt a virtuálisgép-méretezési csoportba tartozó 100-es virtuális gépek esetében?

Ha az egyes virtuális gépekhez több hívás nélkül szeretne tulajdonságokat beolvasni, a `ListVMInstanceViews` REST API a következő erőforrás-URI-n keresztül hívhat `GET` meg:

/Subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines? $expand = instanceView&$select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Különböző kiterjesztési argumentumok adhatók át a virtuálisgép-méretezési csoportokban lévő különböző virtuális gépekhez?

Nem, a virtuálisgép-méretezési csoportokban nem lehet eltérő kiterjesztési argumentumokat átadni a különböző virtuális gépekhez. A bővítmények azonban a-on futó virtuális gép egyedi tulajdonságai alapján működhetnek, például a gép nevén. A bővítmények a példány metaadatainak lekérdezésével is lekérhetik a http://169.254.169.254 virtuális géppel kapcsolatos további információkat.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Miért van különbség a virtuálisgép-méretezési csoport virtuális gépek nevei és virtuálisgép-azonosítói között? Például: 0, 1, 3...

A virtuálisgép-méretezési csoport virtuális gépek nevei és virtuálisgép-azonosítói között rések vannak, mivel a virtuálisgép-méretezési csoport **túlépítésének tulajdonsága** az alapértelmezett **true** értékre van állítva. Ha a túlzott kiépítése **igaz** értékre van állítva, a rendszer több virtuális gépet hoz létre a kértnél. Ezután további virtuális gépek törlődnek. Ebben az esetben magasabb szintű üzembe helyezési megbízhatóságot biztosít, de a folytonos névhasználati és a folytonos hálózati címfordítási (NAT-) szabályok rovására.

Ezt a tulajdonságot **hamis** értékre állíthatja. A kisméretű virtuálisgép-méretezési csoportok esetében ez nem befolyásolja jelentősen az üzembe helyezés megbízhatóságát.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Mi a különbség a virtuálisgép-méretezési csoportokban lévő virtuális gépek törlése és a virtuális gép felszabadítása között? Mikor érdemes választani egyet a másikon?

A virtuálisgép-méretezési csoportokban lévő virtuális gépek és a virtuális gép felszabadítása közötti fő különbség az, hogy `deallocate` nem törli a virtuális merevlemezeket (VHD-ket). A futtatáshoz tárolási költségek vannak társítva `stop deallocate` . A következő okok egyikére használhatja az egyiket vagy a másikat:

- Le szeretné állítani a számítási költségek kifizetését, de meg szeretné őrizni a virtuális gépek lemezes állapotát.
- A virtuálisgép-méretezési csoport felskálázásához gyorsabban szeretné elindítani a virtuális gépek készletét.
  - Ezzel kapcsolatban előfordulhat, hogy létrehozta a saját autoskálázási motort, és gyorsabb, végpontok közötti méretezést szeretne.
- Olyan virtuálisgép-méretezési csoporttal rendelkezik, amely egyenetlenül van elosztva a tartalék tartományok vagy a frissítési tartományok között. Ennek az lehet az oka, hogy szelektíven törölte a virtuális gépeket, vagy mert a túlzott kiépítése után törölték a virtuális gépeket. A Futtatás `stop deallocate` után a `start` virtuálisgép-méretezési csoport egyenletesen osztja el a virtuális gépeket a tartalék tartományok vagy a frissítési tartományok között.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Hogyan készítsen pillanatképet a virtuálisgép-méretezési csoport példányairól?
Hozzon létre egy pillanatképet egy virtuálisgép-méretezési csoport egy példányáról.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Hozzon létre egy felügyelt lemezt a pillanatképből.

```azurepowershell-interactive
$snapshotName = "mySnapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
