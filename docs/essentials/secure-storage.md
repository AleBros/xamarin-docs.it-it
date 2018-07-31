---
title: 'Xamarin.Essentials: Archiviazione protetta'
description: Questo documento descrive la classe SecureStorage in Xamarin.Essentials, che consente di archiviare in modo sicuro le coppie chiave/valore semplice. Illustra come usare la classe, funzionalità specifiche di implementazione della piattaforma e le limitazioni.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: 2dfdb7051b269e73c68290a557849b9ae606c165
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353295"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials: Archiviazione protetta

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **SecureStorage** classe consente di archiviare in modo sicuro le coppie chiave/valore semplice.

## <a name="getting-started"></a>Introduzione

Per l'accesso di **SecureStorage** funzionalità, è necessaria la configurazione seguente specifica della piattaforma:

# <a name="androidtabandroid"></a>[Android](#tab/android)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="iostabios"></a>[iOS](#tab/ios)

Quando si sviluppa in un simulatore iOS, abilitare la **Keychain** entitlement e aggiungere un gruppo di accesso keychain per identificatore del bundle dell'applicazione.

Aprire il **entitlements. plist** nel progetto iOS e trovare il **Keychain** entitlement e abilitarlo. Identificatore dell'applicazione verrà aggiunto automaticamente come un gruppo.

Nelle proprietà del progetto, sotto **firma del Bundle iOS** impostare il **Entitlement personalizzati** al **entitlements. plist**.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-secure-storage"></a>Uso di archiviazione sicura

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per salvare un valore per un determinato _chiave_ in un archivio protetto:

```csharp
try
{
  await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

Per recuperare un valore da un archivio protetto:

```csharp
try
{
  var oauthToken = await SecureStorage.GetAsync("oauth_token");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

> [!NOTE]
> Se è presente alcun valore associato alla chiave richiesta, `GetAsync` restituirà `null`.

Per rimuovere una chiave specifica, chiamare:

```csharp
SecureStorage.Remove("oauth_token");
```

Per rimuovere tutte le chiavi, chiamare:

```csharp
SecureStorage.RemoveAll();
```


## <a name="platform-implementation-specifics"></a>Funzionalità specifiche di implementazione della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Il [archivio chiavi Android](https://developer.android.com/training/articles/keystore.html) viene usato per archiviare la chiave di crittografia usata per crittografare il valore prima che venga salvato in un [preferenze condivise](https://developer.android.com/training/data-storage/shared-preferences.html) con il nome di **.xamarinessentials [YOUR-APP-PACKAGE-ID]** .  La chiave usata nel file di preferenze condivise è un _Hash MD5_ della chiave passata il `SecureStorage` API.

## <a name="api-level-23-and-higher"></a>Livello API 23 e versioni successive

Sui livelli API più recenti, un' **AES** chiave viene ottenuta dall'archivio chiavi Android e usata con un **AES/GCM/NoPadding** pacchetti di crittografia per crittografare il valore prima di essere archiviato nel file di preferenze condivise.

## <a name="api-level-22-and-lower"></a>Livello API 22 e inferiore

Sui livelli API precedenti, l'archivio chiavi Android supporta solo l'archiviazione **RSA** le chiavi, che viene usato con un **RSA/ECB/PKCS1Padding** pacchetti di crittografia per crittografare un' **AES** chiave (in modo casuale generato in fase di esecuzione) e archiviati nel file di preferenze condivise nella chiave _SecureStorageKey_, se uno non è già stato generato.

**SecureStorage** utilizza le [preferenze](preferences.md) API e la persistenza dei dati stessi descritti nella seguente il [preferenze](preferences.md#persistence) documentazione. Se un dispositivo viene aggiornato dal livello API 22 o inferiore a livello API 23 e versioni successiva, questo tipo di crittografia continuerà a essere utilizzato a meno che l'app viene disinstallata oppure **RemoveAll** viene chiamato.

# <a name="iostabios"></a>[iOS](#tab/ios)

[KeyChain](https://developer.xamarin.com/api/type/Security.SecKeyChain/) viene usato per archiviare i valori in modo sicuro sui dispositivi iOS.  Il `SecRecord` usato per archiviare il valore è una `Service` valore impostato su **.xamarinessentials [YOUR-APP-BUNDLE-ID]**.

In alcuni casi i dati di KeyChain sono sincronizzati con iCloud e disinstallare l'applicazione non potrà rimuovere i valori protetti dal iCloud e altri dispositivi dell'utente.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) viene utilizzato per i valori crittografati in modo sicuro sui dispositivi UWP.

I valori crittografati sono archiviati in `ApplicationData.Current.LocalSettings`, all'interno di un contenitore con il nome **.xamarinessentials [YOUR-APP-ID]**.

**SecureStorage** utilizza le [preferenze](preferences.md) API e la persistenza dei dati stessi descritti nella seguente il [preferenze](preferences.md#persistence) documentazione.

-----

## <a name="limitations"></a>Limitazioni

Questa API viene usata per archiviare piccole quantità di testo.  Le prestazioni potrebbero risultare lenta se si tenta di usarlo per archiviare grandi quantità di testo.

## <a name="api"></a>API

- [Codice sorgente SecureStorage](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/SecureStorage)
- [Documentazione dell'API SecureStorage](xref:Xamarin.Essentials.SecureStorage)
