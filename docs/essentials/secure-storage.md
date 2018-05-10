---
title: Archiviazione protetta Xamarin.Essentials
description: La classe SecureStorage consente di archiviare in modo sicuro coppie chiave/valore semplice.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: 24d1e29ba0203aaafc3e21533478f6c505cc09b3
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-secure-storage"></a>Archiviazione protetta Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **SecureStorage** classe consente di archiviare in modo sicuro coppie chiave/valore semplice.

## <a name="using-secure-storage"></a>Utilizzo dell'archiviazione sicura

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per salvare un valore per un determinato _chiave_ nel servizio di archiviazione sicura:

```csharp
await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
```

Per recuperare un valore dall'archiviazione sicura:

```csharp
var oauthToken = await SecureStorage.GetAsync("oauth_token");
```

## <a name="platform-implementation-specifics"></a>Informazioni specifiche sull'implementazione della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Il [KeyStore Android](https://developer.android.com/training/articles/keystore.html) viene utilizzato per archiviare la chiave di crittografia utilizzata per crittografare il valore prima che vengano salvati in un [preferenze condivise](https://developer.android.com/training/data-storage/shared-preferences.html) con il nome di **.xamarinessentials [YOUR-APP-PACKAGE-ID]** .  La chiave utilizzata nel file di preferenze condivise è un _Hash MD5_ della chiave passata il `SecureStorage` dell'API.

## <a name="api-level-23-and-higher"></a>Livello API 23 e versioni successive

Sui livelli di API più recenti, un **AES** chiave viene ottenuta da Android KeyStore e utilizzata con un **AES/GCM/NoPadding** pacchetti di crittografia per crittografare il valore prima che venga archiviata nel file di preferenze condivise.

## <a name="api-level-22-and-lower"></a>Livello API 22 e inferiore

Nei livelli di API meno recenti, KeyStore Android supporta solo l'archiviazione **RSA** chiavi, che viene usato con un **RSA/ECB/PKCS1Padding** pacchetti di crittografia per crittografare un **AES** chiave (in modo casuale generato in fase di esecuzione) e archiviati nel file di preferenze condivise nella chiave _SecureStorageKey_, se uno non è già stato generato.

Verranno rimossi tutti i valori crittografati quando l'app viene disinstallata dal dispositivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

[KeyChain](https://developer.xamarin.com/api/type/Android.Security.KeyChain/) viene utilizzato per archiviare i valori in modo sicuro sui dispositivi iOS.  Il `SecRecord` utilizzata per archiviare il valore è un `Service` valore impostato su **.xamarinessentials [YOUR-APP-BUNDLE-ID]**.

In alcuni casi portachiavi dati vengono sincronizzati con iCloud e disinstallare l'applicazione potrebbe non rimuovere i valori sicura iCloud e altri dispositivi dell'utente.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/en-us/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) viene utilizzato per i valori encryped in modo sicuro sui dispositivi UWP.

Encryped valori vengono archiviati nel `ApplicationData.Current.LocalSettings`, all'interno di un contenitore con il nome **.xamarinessentials [YOUR-APP-ID]**.

L'applicazione se si disinstalla il _LocalSettings_e i valori crittografati tutti deve essere rimosso anche.

-----

## <a name="limitations"></a>Limitazioni

Questa API è progettata per archiviare piccole quantità di testo.  Le prestazioni potrebbero risultare lenta se si tenta di utilizzarlo per archiviare grandi quantità di testo.

## <a name="api"></a>API

- [Codice sorgente SecureStorage](https://github.com/xamarin/Essentials/tree/master/Essentials/SecureStorage)
- [Documentazione dell'API SecureStorage](xref:Xamarin.Essentials.SecureStorage)
