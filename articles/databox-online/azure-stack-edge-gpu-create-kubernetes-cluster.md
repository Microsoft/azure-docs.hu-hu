---
title: Kubernetes-fürt létrehozása és kezelése Azure Stack Edge Pro GPU-eszközön | Microsoft Docs
description: Ismerteti, hogyan lehet Kubernetes-fürtöt létrehozni és felügyelni Azure Stack Edge Pro GPU-eszközön a Windows PowerShell felületén keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: ddbf3c99845f4c38f511260f4dec274903a9d586
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631956"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes-fürt csatlakoztatása és kezelése a Azure Stack Edge Pro GPU-eszközön a kubectl használatával

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A Azure Stack Edge Pro-eszközön a számítási szerepkör konfigurálásakor létrejön egy Kubernetes-fürt. A Kubernetes-fürt létrehozása után a fürt helyileg is csatlakoztatható egy ügyfélszámítógépről egy natív eszköz, például a *kubectl* használatával.

Ez a cikk azt ismerteti, hogyan csatlakozhat egy Kubernetes-fürthöz a Azure Stack Edge Pro-eszközön, majd hogyan kezelheti azt a *kubectl* használatával. 


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Egy Azure Stack Edge Pro-eszközhöz férhet hozzá.

2. Aktiválta Azure Stack Edge Pro-eszközét a [Azure stack Edge Pro aktiválása](azure-stack-edge-gpu-deploy-activate.md)című témakörben leírtak szerint.

3. Engedélyezte a számítási szerepkört az eszközön. Egy Kubernetes-fürt is létrejött az eszközön, amikor az eszközön a számítás [konfigurálása az Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-deploy-configure-compute.md)című témakör útmutatása szerint konfigurálta az eszközt.

4. Hozzáférése van egy PowerShell 5,0 vagy újabb rendszert futtató Windows rendszerű ügyfélhez az eszköz eléréséhez. Bármely más ügyfél [támogatott operációs rendszerrel](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) is rendelkezhet. 

5. A Kubernetes API-végpont a helyi webes felhasználói felület **eszköz** lapján található. További információkért lásd a [KUBERNETES API-végpont beolvasása](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) című témakör utasításait.


## <a name="connect-to-powershell-interface"></a>Kapcsolódás a PowerShell-felülethez

A Kubernetes-fürt létrehozása után elérheti ezt a fürtöt névterek és felhasználók létrehozásához, valamint a felhasználók névterekhez rendeléséhez. Ehhez csatlakoznia kell az eszköz PowerShell-felületéhez. Kövesse az alábbi lépéseket a PowerShellt futtató Windows-ügyfélen.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-kubernetes-rbac"></a>A fürt elérésének konfigurálása a Kubernetes-RBAC keresztül

A Kubernetes-fürt létrehozása után a *kubectl* a cmdline keresztül a fürt eléréséhez használhatja. 

Ebben a megközelítésben létre kell hoznia egy névteret és egy felhasználót. Ezután társítsa a felhasználót a névtérhez. Emellett olyan *konfigurációs* fájlt is be kell szereznie, amely lehetővé teszi, hogy egy Kubernetes-ügyféllel közvetlenül beszéljen a létrehozott Kubernetes-fürthöz anélkül, hogy csatlakoznia kellene a Azure stack Edge Pro-eszköz PowerShell-felületéhez.

1. Hozzon létre egy névteret. Típus:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > A névtér és a felhasználónevek esetében a [DNS-altartományok elnevezési konvenciói](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) érvényesek.

    Itt látható egy mintakimenet:

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. Hozzon létre egy felhasználót, és szerezzen be egy konfigurációs fájlt. Típus:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > A *aseuser* nem használható felhasználónévként, mert az Azure stack Edge Pro-hoz tartozó IoT-névtérhez társított alapértelmezett felhasználó számára van fenntartva.

    Az alábbi példa a konfigurációs fájl kimenetét jeleníti meg:
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. A konfigurációs fájlok egyszerű szövegben jelennek meg. Másolja ezt a fájlt, és mentse *konfigurációs* fájlként. 

    > [!IMPORTANT]
    > Ne mentse a konfigurációs fájlt *. txt* fájlként, mentse a fájlt fájlkiterjesztés nélkül.

4. A konfigurációs fájlnak a `.kube` helyi számítógépen lévő felhasználói profil mappájában kell lennie. Másolja a fájlt a mappába a felhasználói profilban.

    ![A konfigurációs fájl helye az ügyfélen](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)

5. Társítsa a névteret a létrehozott felhasználóval. Típus:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    Itt látható egy mintakimenet:

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    A konfigurációs fájl futtatása után nincs szükség fizikai hozzáférésre a fürthöz. Ha az ügyfél pingelheti az Azure Stack Edge Pro-eszköz IP-címét, a *kubectl* parancsok használatával irányíthatja a fürtöt.

6. Indítson el egy új PowerShell-munkamenetet az ügyfélen. Nem kell csatlakoznia az eszköz felületéhez. Most már telepítheti az `kubectl` ügyfelet az alábbi parancs használatával:

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    Ha például a Kubernetes fő csomópontja a v 1.15.2-t futtatta, telepítse a v 1.15.2-t az ügyfélen.

    > [!IMPORTANT]
    > Olyan ügyfelet töltsön le, amely legfeljebb egy alverzióval tér el a főverziótól. Az ügyfél verziója, de a főkiszolgálót akár egy alverzión is elvezethetik. A v 1.3-as főkiszolgáló például a v 1.1, v 1.2 és v 1.3 csomópontokkal működik együtt, és működnie kell a v 1.2, v 1.3 és v 1.4 rendszerű ügyfelekkel. A Kubernetes-ügyfélszoftversel kapcsolatos további információkért lásd: [a Kubernetes verziója és verziója](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew). A Azure Stack Edge Pro Kubernetes Server-verziójával kapcsolatos további információkért keresse fel a Kubernetes-kiszolgáló verziójának beolvasása című témakört.<!-- insert link-->
    > Előfordulhat, `kubectl` hogy a rendszer előre telepíti a rendszert, ha a Windowshoz vagy más eszközökhöz a Docker-t futtatja. Fontos, hogy az ebben a szakaszban jelzett módon töltse le az adott verziót a `kubectl` kubernetes-fürttel való együttműködéshez. 

    A telepítés több percet is igénybe vehet.

7. Ellenőrizze, hogy telepítve van-e a letöltött verzió. Adja meg azt az abszolút elérési utat, ahová a `kubectl.exe` rendszerre telepítették.
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    A `kubectl` Kubernetes-fürt felügyeletéhez használt parancsokkal kapcsolatos további információkért [tekintse meg a kubectl áttekintését](https://kubernetes.io/docs/reference/kubectl/overview/).

8. Adjon hozzá egy DNS-bejegyzést a gazdagépek fájljához a rendszeren. 

    1. Futtassa rendszergazdaként a jegyzettömböt, és nyissa meg a `hosts` következő helyen található fájlt: `C:\windows\system32\drivers\etc\hosts` . 
    2. A korábbi lépésben a helyi felhasználói felület **eszköz** lapján mentett információk alapján hozza létre a bejegyzést a Hosts fájlban. 

        Másolja például ezt a végpontot a `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` következő bejegyzés létrehozásához az eszköz IP-címe és a DNS-tartomány használatával: 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Annak ellenőrzéséhez, hogy tud-e csatlakozni a Kubernetes hüvelyéhez, írja be a következőt:
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
Mostantól üzembe helyezheti az alkalmazásait a névtérben, majd megtekintheti az alkalmazásokat és a naplókat.

> [!IMPORTANT]   
> Számos parancs nem futtatható, például olyan parancsok, amelyekhez rendszergazdai hozzáférés szükséges. Csak olyan műveleteket hajthat végre, amelyek engedélyezve vannak a névtérben.


## <a name="remove-kubernetes-cluster"></a>Kubernetes-fürt eltávolítása

A Kubernetes-fürt eltávolításához el kell távolítania a IoT Edge konfigurációt.

Részletes útmutatásért lásd: a [IoT Edge konfigurációjának kezelése](azure-stack-edge-gpu-manage-compute.md#manage-iot-edge-configuration).
   

## <a name="next-steps"></a>Következő lépések

- [Állapot nélküli alkalmazás üzembe helyezése a Azure stack Edge Pro](azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)-ban.
