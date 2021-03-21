---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007998"
---
| Nyelv/keretrendszer | Projekt bekapcsolva<br/>GitHub                                                                 | Csomag                                                                                | Szerzés<br/>lépések                           | Bejelentkezési felhasználók                                            | Hozzáférés a webes API-khoz                                                 | Általánosan elérhető (GA) *vagy*<br/>Nyilvános előzetes verzió<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Gyors útmutató](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![A könyvtár nem kérhet azonosító jogkivonatot a felhasználói bejelentkezéshez.][n] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![A könyvtár nem kérhet azonosító jogkivonatot a felhasználói bejelentkezéshez.][n] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| Csomópont               | [MSAL csomópont](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal – csomópont](https://www.npmjs.com/package/@azure/msal-node)  | [Gyors útmutató](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![A könyvtár nem kérhet azonosító jogkivonatot a felhasználói bejelentkezéshez.][n] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal – Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Gyors útmutató](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![A könyvtár nem kérhet azonosító jogkivonatot a felhasználói bejelentkezéshez.][n] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> a Microsoft Azure előzetes verziókra [vonatkozó kiegészítő használati feltételek][preview-tos] a *nyilvános előzetes* verzióban lévő tárakra vonatkoznak.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/