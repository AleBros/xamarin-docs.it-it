---
title: 'Xamarin.Essentials: Archiviazione sicura'
description: Questo documento descrive la classe SecureStorage in Xamarin.Essentials , che consente di archiviare in modo sicuro coppie chiave/valore semplici. Illustra come usare la classe, le informazioni di implementazione specifiche della piattaforma e le limitazioni.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 061bc1cfe785ad080092ba21340f7d38bc499ed9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84801946"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials: Archiviazione sicura

La classe **SecureStorage** consente di archiviare in modo sicuro semplici coppie chiave/valore.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

Per accedere alla funzionalità **SecureStorage**, è necessaria la configurazione seguente specifica della piattaforma:

# <a name="android"></a>[Android](#tab/android)

> [!TIP]
> Il [backup automatico per le app](https://developer.android.com/guide/topics/data/autobackup) è una funzionalità di Android 6.0 (livello API 23) e versioni successive per il backup dei dati delle app dell'utente (preferenze condivise, file nello spazio di archiviazione interno dell'app e altri file specifici). I dati vengono ripristinati quando un'app viene reinstallata o installata in un nuovo dispositivo. Ciò può influire su `SecureStorage` che usa preferenze condivise incluse nel backup e che non possono essere decrittografate quando viene eseguito il ripristino. Xamarin.Essentialsgestisce automaticamente questo caso rimuovendo la chiave in modo che possa essere reimpostata, ma è possibile eseguire un ulteriore passaggio disabilitando il backup automatico.

### <a name="enable-or-disable-backup"></a>Abilitare o disabilitare il backup
È possibile scegliere di disabilitare il backup automatico per l'intera applicazione impostando `android:allowBackup` su false nel file `AndroidManifest.xml`. Questo approccio è consigliato solo se si prevede di ripristinare i dati in altro modo.

```xml
<manifest ... >
    ...
    <application android:allowBackup="false" ... >
        ...
    </application>
</manifest>
```

### <a name="selective-backup"></a>Backup selettivo
Il backup automatico può essere configurato per disabilitare contenuto specifico dal backup. È possibile creare un set di regole personalizzate per escludere gli elementi `SecureStore` dal backup.

1. Impostare l'attributo `android:fullBackupContent` nel file **AndroidManifest.xml**:

    ```xml
    <application ...
        android:fullBackupContent="@xml/auto_backup_rules">
    </application>
    ```

2. Creare un nuovo file XML denominato **auto_backup_rules.xml** nella directory **Resources/xml** con l'azione di compilazione **AndroidResource**. Impostare quindi il contenuto seguente che include tutte le preferenze condivise ad eccezione di `SecureStorage`:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <full-backup-content>
        <include domain="sharedpref" path="."/>
        <exclude domain="sharedpref" path="${applicationId}.xamarinessentials.xml"/>
    </full-backup-content>
    ```

# <a name="ios"></a>[iOS](#tab/ios)

Quando si sviluppa nel **simulatore iOS**, abilitare l'entitlement **Keychain** e aggiungere un gruppo di accesso keychain per l'identificatore del bundle dell'applicazione.

Aprire il file **Entitlements.plist** nel progetto iOS, trovare l'entitlement **Keychain** e quindi abilitarlo. L'identificatore dell'applicazione verrà aggiunto automaticamente come gruppo.

Nelle proprietà del progetto, in **Firma del bundle iOS** impostare **Entitlement personalizzati** su **Entitlements.plist**.

> [!TIP]
> Per la distribuzione in un dispositivo iOS questo entitlement non è richiesto e deve essere rimosso.

# <a name="uwp"></a>[UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-secure-storage"></a>Uso dell'archiviazione sicura

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per salvare un valore per una determinata _chiave_ nell'archiviazione sicura:

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

Per recuperare un valore dall'archiviazione sicura:

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
> Se alla chiave richiesta non è associato alcun valore, `GetAsync` restituirà `null`.

Per rimuovere una chiave specifica, chiamare:

```csharp
SecureStorage.Remove("oauth_token");
```

Per rimuovere tutte le chiavi, chiamare:

```csharp
SecureStorage.RemoveAll();
```

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="android"></a>[Android](#tab/android)

L'[archivio chiavi Android](https://developer.android.com/training/articles/keystore.html) viene usato per archiviare la chiave di crittografia usata per crittografare il valore prima di salvarlo nelle [preferenze condivise](https://developer.android.com/training/data-storage/shared-preferences.html) con il nome di file **[ID-PACCHETTO-APP].xamarinessentials**.  La chiave (non una chiave crittografica, ma la _chiave_ per il _valore_) usata nel file delle preferenze condivise è un _hash MD5_ della chiave passata nelle API `SecureStorage`.

**API livello 23 e successivi**

Nei livelli API più recenti, una chiave **AES** viene ottenuta dall'archivio chiavi Android e usata con la crittografia **AES/GCM/NoPadding** per crittografare il valore prima dell'archiviazione nel file delle preferenze condivise.

**API livello 22 e precedenti**

Nei livelli API precedenti, l'archivio chiavi Android supporta solo l'archiviazione di chiavi **RSA**, usate con la crittografia **RSA/ECB/PKCS1Padding** per crittografare una chiave **AES** (generata in modo casuale in fase di esecuzione) e archiviarla nel file delle preferenze condivise nella chiave _SecureStorageKey_, se non ne è già stata generata una.

**SecureStorage** usa l'API [Preferences](preferences.md) e segue gli stessi criteri di persistenza dei dati descritti nella documentazione per [Preferences](preferences.md#persistence). Se un dispositivo viene aggiornato dal livello API 22 o precedente al livello API 23 e successivi, questo tipo di crittografia continuerà a essere usata a meno che l'app non venga disinstallata oppure non venga chiamato **RemoveAll**.

# <a name="ios"></a>[iOS](#tab/ios)

[KeyChain](xref:Security.SecKeyChain) viene usato per archiviare i valori in modo sicuro nei dispositivi iOS.  Il `SecRecord` usato per archiviare il valore ha un valore `Service` impostato su **[ID-BUNDLE-APP].xamarinessentials**.

In alcuni casi i dati KeyChain sono sincronizzati con iCloud e la disinstallazione dell'applicazione potrebbe non rimuovere i valori sicuri da iCloud e altri dispositivi dell'utente.

# <a name="uwp"></a>[UWP](#tab/uwp)

Per crittografare i valori in modo sicuro nei dispositivi UWP, si usa [DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider).

I valori crittografati sono archiviati in `ApplicationData.Current.LocalSettings`, all'interno di un contenitore con il nome **[ID-APP].xamarinessentials**.

**SecureStorage** usa l'API [Preferences](preferences.md) e segue gli stessi criteri di persistenza dei dati descritti nella documentazione per [Preferences](preferences.md#persistence). USA inoltre `LocalSettings` la restrizione che il nome di ogni impostazione può avere una lunghezza massima di 255 caratteri. Ogni impostazione può avere dimensioni fino a 8 KB e ogni impostazione composita può avere dimensioni massime di 64K byte.

-----

## <a name="limitations"></a>Limitazioni

Questa API è progettata per l'archiviazione di piccole quantità di testo.  Le prestazioni potrebbero risultare lente se si tenta di usarla per archiviare grandi quantità di testo.

## <a name="api"></a>API

- [Codice sorgente di SecureStorage](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/SecureStorage)
- [Documentazione dell'API SecureStorage](xref:Xamarin.Essentials.SecureStorage)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Secure-Storage-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
