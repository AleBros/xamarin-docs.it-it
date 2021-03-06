---
title: 'Xamarin.Essentials: Preferences'
description: Questo documento descrive la classe Preferences in Xamarin.Essentials, che consente di salvare le preferenze dell'applicazione in un archivio chiave/valore. Illustra come usare la classe e i tipi di dati che possono essere archiviati.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 01/15/2019
ms.custom: video
ms.openlocfilehash: e812ab5b85db396ee3cb473f4a659ac188c9212f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79497043"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials: Preferences

La classe **Preferences** consente di archiviare le preferenze dell'applicazione in un archivio chiave/valore.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-preferences"></a>Uso di Preferences

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per salvare un valore per una determinata _chiave_ nelle preferenze:

```csharp
Preferences.Set("my_key", "my_value");
```

Per recuperare un valore dalle preferenze o un'impostazione predefinita se non impostata:

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

Per verificare se una determinata _chiave_ esiste nelle preferenze:

```csharp
bool hasKey = Preferences.ContainsKey("my_key");
```

Per rimuovere la _chiave_ dalle preferenze:

```csharp
Preferences.Remove("my_key");
```

Per rimuovere tutte le preferenze:

```csharp
Preferences.Clear();
```

Oltre a questi metodi, ogni preferenza accetta un elemento `sharedName` facoltativo che può essere usato per creare contenitori aggiuntivi per le preferenze. Vedere più avanti Informazioni di implementazione specifiche della piattaforma.

## <a name="supported-data-types"></a>Tipi di dati supportati

In **Preferences** sono supportati i tipi di dati seguenti:

- **bool**
- **double**
- **Int**
- **Galleggiante**
- **Lungo**
- **string**
- **Datetime**

## <a name="integrate-with-system-settings"></a>Integrazione con le impostazioni di sistema

Le preferenze sono memorizzate in modo nativo, che consente di integrare le impostazioni nelle impostazioni di sistema nativo. Seguire la docutolazione della piattaforma e gli esempi per l'integrazione con la piattaforma:

* Apple: Implementazione di un pacchetto di [impostazioni iOS](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/UserDefaults/Preferences/Preferences.html)
* [Esempio di preferenze di applicazione iOSIOS Applicaton Preferences Sample](https://docs.microsoft.com/samples/xamarin/ios-samples/appprefs/)
* [Impostazioni watchOS](https://developer.xamarin.com/guides/ios/watch/working-with/settings/)
* Android: [Introduzione alle schermate Impostazioni](https://developer.android.com/guide/topics/ui/settings.html)

## <a name="implementation-details"></a>Dettagli dell'implementazione

I `DateTime` valori di vengono archiviati in un formato binario a `DateTime` 64 [`ToBinary`](xref:System.DateTime.ToBinary) bit (long integer) `DateTime` utilizzando due [`FromBinary`](xref:System.DateTime.FromBinary(System.Int64)) metodi definiti dalla classe: il metodo viene utilizzato per codificare il valore e il metodo decodifica il valore. Vedere la documentazione di questi metodi per le modifiche che potrebbero essere apportate ai valori decodificati quando viene archiviato un valore `DateTime` non UTC (Coordinated Universal Time).

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="android"></a>[Android](#tab/android)

Tutti i dati vengono archiviati nelle [preferenze condivise](https://developer.android.com/training/data-storage/shared-preferences.html). Se non vengono specificati `sharedName`, vengono usate le preferenze condivise, altrimenti viene usato il nome per ottenere le preferenze condivise **private** con il nome specificato.

# <a name="ios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/xamarin/ios/app-fundamentals/user-defaults) viene usato per archiviare i valori nei dispositivi iOS. Se non vengono specificati `sharedName`, viene usato `StandardUserDefaults`, altrimenti viene usato il nome per creare un nuovo `NSUserDefaults` con il nome specificato usato per `NSUserDefaultsType.SuiteName`.

# <a name="uwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer) viene usato per archiviare i valori nel dispositivo. Se non vengono specificati `sharedName`, viene usato `LocalSettings`, altrimenti viene usato il nome per creare un nuovo contenitore in `LocalSettings`. 

`LocalSettings`ha anche la seguente restrizione che il nome di ogni impostazione può essere 255 caratteri di lunghezza al massimo. Ogni impostazione può avere una dimensione massima di 8K byte e ogni impostazione composita può avere una dimensione massima di 64K byte.

--------------

## <a name="persistence"></a>Persistenza

Disinstallando l'applicazione, tutte le _preferenze_ verranno rimosse. Esiste un'eccezione costituita dalle app che specificano come destinazione e vengono eseguite in Android 6.0 (livello API 23) o versione successiva e che usano il [__backup automatico__](https://developer.android.com/guide/topics/data/autobackup). Questa funzionalità è attivata per impostazione predefinita e conserva i dati dell'app che includono le __preferenze condivise__, utilizzate dall'API **Preferences**. Per disabilitarla, seguire la [documentazione](https://developer.android.com/guide/topics/data/autobackup) di Google.

## <a name="limitations"></a>Limitazioni

Quando si archivia una stringa, questa API è progettata per l'archiviazione di piccole quantità di testo.  Le prestazioni potrebbero risultare basse se si prova a usarla per archiviare grandi quantità di testo.

## <a name="api"></a>API

- [Codice sorgente di Preferences](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Documentazione dell'API Preferences](xref:Xamarin.Essentials.Preferences)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Preferences-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
