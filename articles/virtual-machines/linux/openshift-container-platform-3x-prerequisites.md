---
title: OpenShift Container platform 3,11 az Azure-előfeltételekben | Microsoft Docs
description: Az OpenShift Container platform 3,11 Azure-beli üzembe helyezésének előfeltételei.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 591cc7a4b84f75536446abbcbe32a69a122ddf5a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392669"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>A OpenShift Container platform 3,11 Azure-beli üzembe helyezésének gyakori előfeltételei

Ez a cikk a OpenShift-tároló platformjának vagy OKD az Azure-ban való üzembe helyezésének általános előfeltételeit ismerteti.

A OpenShift telepítése Ansible forgatókönyveket használ. A Ansible a Secure Shell (SSH) használatával csatlakozik a fürt összes gazdagépéhez a telepítési lépések befejezéséhez.

Ha a Ansible lehetővé teszi az SSH-csatlakozást a távoli gazdagépekhez, nem adhat meg jelszót. Emiatt a titkos kulcshoz nem tartozhat jelszó (hozzáférési kód) társítva, vagy a telepítés meghiúsul.

Mivel a virtuális gépek (VM-EK) Azure Resource Manager-sablonokon keresztül telepíthetők, a rendszer ugyanazt a nyilvános kulcsot használja az összes virtuális géphez való hozzáféréshez. A megfelelő titkos kulcsnak a virtuális gépen kell lennie, amely az összes forgatókönyvét is végrehajtja. A művelet biztonságos elvégzéséhez az Azure Key Vault használatával továbbítja a titkos kulcsot a virtuális géphez.

Ha állandó tárterületre van szükség a tárolók számára, akkor állandó kötetek szükségesek. A OpenShift támogatja az Azure-beli virtuális merevlemezeket (VHD-ket) az állandó kötetek számára, de az Azure-t először a felhőalapú szolgáltatóként kell konfigurálni.

Ebben a modellben a OpenShift:

- Egy VHD-objektumot hoz létre egy Azure Storage-fiókban vagy egy felügyelt lemezen.
- Csatlakoztatja a virtuális merevlemezt egy virtuális géphez, és formázza a kötetet.
- Csatlakoztatja a kötetet a pod-hoz.

Ahhoz, hogy ez a konfiguráció működjön, a OpenShift engedélyekkel kell rendelkeznie ezen feladatok végrehajtásához az Azure-ban. Erre a célra egy egyszerű szolgáltatásnév használatos. Az egyszerű szolgáltatásnév egy Azure Active Directory biztonsági fiók, amely engedélyeket biztosít az erőforrásokhoz.

Az egyszerű szolgáltatásnak hozzá kell férnie a fürtöt alkotó Storage-fiókokhoz és virtuális gépekhez. Ha az összes OpenShift-fürterőforrás egyetlen erőforráscsoport számára van üzembe helyezve, az egyszerű szolgáltatás engedélyt kap az adott erőforráscsoport számára.

Ez az útmutató az előfeltételekhez társított összetevők létrehozását ismerteti.

> [!div class="checklist"]
> * Hozzon létre egy Key vaultot a OpenShift-fürt SSH-kulcsainak kezeléséhez.
> * Hozzon létre egy egyszerű szolgáltatást az Azure Cloud Provider általi használatra.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 
Jelentkezzen be az Azure-előfizetésbe az az [login](/cli/azure/reference-index) paranccsal, és kövesse a képernyőn megjelenő utasításokat, vagy kattintson a **kipróbálás** gombra a Cloud Shell használatához.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A Key Vault üzemeltetéséhez dedikált erőforráscsoportot kell használnia. Ez a csoport a OpenShift-fürt erőforrásainak üzembe helyezéséhez használt erőforráscsoporthoz eltér.

A következő példában létrehozunk egy *keyvaultrg* nevű erőforráscsoportot a *eastus* helyen:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy kulcstartót, amely a fürthöz tartozó SSH-kulcsokat az az kulcstartó [create](/cli/azure/keyvault) paranccsal tárolja. A kulcstároló nevének globálisan egyedinek kell lennie, és engedélyezni kell a sablon központi telepítéséhez, vagy a központi telepítés "KeyVaultParameterReferenceSecretRetrieveFailed" hibaüzenettel fog működni.

A következő példa egy kulcstartó nevű kulcstárolót hoz *létre a* *keyvaultrg* erőforráscsoporthoz:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH-kulcs létrehozása 
Egy SSH-kulcsra van szükség a OpenShift-fürthöz való hozzáférés biztonságossá tételéhez. SSH-kulcspár létrehozása a `ssh-keygen` parancs használatával (Linux vagy macOS rendszeren):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Az SSH-kulcspárt nem lehet jelszó/hozzáférési kód.

A Windows SSH-kulcsaival kapcsolatos további információkért lásd: [ssh-kulcsok létrehozása Windows](/azure/virtual-machines/linux/ssh-from-windows)rendszeren. Ügyeljen arra, hogy az OpenSSH formátumban exportálja a titkos kulcsot.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>A titkos SSH-kulcs tárolása Azure Key Vault
A OpenShift üzemelő példány a létrehozott SSH-kulcsot használja a OpenShift-főkiszolgáló hozzáférésének biztosításához. Ha engedélyezni szeretné a központi telepítés számára az SSH-kulcs biztonságos lekérését, az alábbi paranccsal tárolja Key Vault a kulcsot:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása 
A OpenShift felhasználónévvel és jelszóval vagy egyszerű szolgáltatásnév használatával kommunikál az Azure-nal. Az Azure egyszerű szolgáltatás olyan biztonsági identitás, amely az alkalmazásokkal, szolgáltatásokkal és automatizálási eszközökkel, például a OpenShift is használható. Megadhatja és meghatározhatja azokat az engedélyeket, amelyeket az egyszerű szolgáltatás az Azure-ban végrehajthat. A teljes előfizetés helyett érdemes az egyszerű szolgáltatásnév engedélyeit az adott erőforráscsoportok hatókörére kiterjeszteni.

Hozzon létre egy egyszerű szolgáltatást az [az ad SP Create-for-RBAC](/cli/azure/ad/sp) , és adja meg a OpenShift által igényelt hitelesítő adatokat.

A következő példa létrehoz egy szolgáltatásnevet, és hozzárendeli az IT közreműködői engedélyeket egy openshiftrg nevű erőforráscsoporthoz.

Először hozza létre az openshiftrg nevű erőforráscsoportot:

```azurecli
az group create -l eastus -n openshiftrg
```

Egyszerű szolgáltatásnév létrehozása:

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope \
```
Ha Windows rendszert használ, hajtsa végre a ```az group show --name openshiftrg --query id``` értéket, és használja $scope helyett a kimenetet.

Jegyezze fel a parancs által visszaadott appId tulajdonságot:
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Ügyeljen arra, hogy biztonságos jelszót hozzon létre. Kövesse az alábbi cikkben ismertetett útmutatást: [Az Azure AD-jelszavakra vonatkozó szabályok és korlátozások](/azure/active-directory/active-directory-passwords-policy).

Az egyszerű szolgáltatásokkal kapcsolatos további információkért lásd: [Azure-szolgáltatásnév létrehozása az Azure CLI-vel](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Csak Resource Manager-sablonra vonatkozó előfeltételek

A titkos SSH-kulcs (**sshPrivateKey**), az Azure ad Client Secret (**aadClientSecret**), a OpenShift admin password (**OpenshiftPassword**) és a Red Hat Subscription Manager jelszava vagy aktiválási kulcsa **alapján létre kell hozni a titkokat. rhsmPasswordOrActivationKey**).  Emellett ha egyéni SSL-tanúsítványokat használ, akkor hat további titkot kell létrehoznia – **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**és  **masterkeyfile**.  A paramétereket részletesebben is ismertetjük.

A sablon megadott titkos nevekre hivatkozik, ezért a fent felsorolt félkövér neveket **kell** használnia (kis-és nagybetűk megkülönböztetése).

### <a name="custom-certificates"></a>Egyéni tanúsítványok

Alapértelmezés szerint a sablon a OpenShift webkonzol és az útválasztási tartomány önaláírt tanúsítványainak használatával helyez üzembe egy OpenShift-fürtöt. Ha egyéni SSL-tanúsítványokat szeretne használni, állítsa a "routingCertType" lehetőséget az "egyéni" és a "masterCertType" értékre az "Custom" értékre.  A tanúsítványokhoz. PEM formátumban kell lennie a CA, a CERT és a Key fájloknak.  Egyéni tanúsítványokat is használhat egy másikhoz, de nem.

Ezeket a fájlokat Key Vault titokban kell tárolnia.  Ugyanazt a Key Vault használja, mint a titkos kulcshoz használt.  Ahelyett, hogy 6 további bemenetet követel meg a titkos kódokhoz, a sablon nem módosítható, hogy az egyes SSL-tanúsítványfájl-fájlokhoz egyedi titkos neveket használjon.  Tárolja a tanúsítvány adatait az alábbi táblázat információi alapján.

| Titok neve      | Tanúsítványfájl   |
|------------------|--------------------|
| mastercafile     | fő HITELESÍTÉSSZOLGÁLTATÓI fájl     |
| mastercertfile   | fő tanúsítvány fájlja   |
| masterkeyfile    | főkulcs fájlja    |
| routingcafile    | útválasztási HITELESÍTÉSSZOLGÁLTATÓI fájl    |
| routingcertfile  | útválasztási tanúsítvány fájlja  |
| routingkeyfile   | útválasztási kulcs fájlja   |

Hozza létre a titkokat az Azure CLI használatával. Alább látható egy példa.

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk a következő témákat tárgyalta:
> [!div class="checklist"]
> * Hozzon létre egy Key vaultot a OpenShift-fürt SSH-kulcsainak kezeléséhez.
> * Hozzon létre egy egyszerű szolgáltatásnevet a Azure Cloud Solution Provider általi használatra.

Következő lépésként helyezzen üzembe egy OpenShift-fürtöt:

- [OpenShift-tároló platform üzembe helyezése](./openshift-container-platform-3x.md)
- [A OpenShift-tároló platform saját üzemeltetésű Piactéri ajánlatának üzembe helyezése](./openshift-container-platform-3x-marketplace-self-managed.md)
