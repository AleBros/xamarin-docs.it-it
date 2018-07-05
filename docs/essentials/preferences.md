---
title: 'Xamarin.Essentials: preferenze'
description: Questo documento descrive la classe preferenze in Xamarin.Essentials, che consente di salvare le preferenze dell'applicazione in un archivio chiave/valore. Illustra come usare la classe e i tipi di dati che possono essere archiviati.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ca6d4f1ec60a80b483c79dd75267144e67d80c0b
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403491"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials: preferenze

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **preferenze** classe consente di archiviare le preferenze dell'applicazione in un archivio chiave/valore.

## <a name="using-preferences"></a>Utilizzo delle preferenze

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per salvare un valore per un determinato _chiave_ nelle preferenze:

```csharp
Preferences.Set("my_key", "my_value");
```

Per recuperare un valore da preferenze o un valore predefinito se non impostato:

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

Per rimuovere il _chiave_ dalle preferenze:

```csharp
Preferences.Remove("my_key");
```

Per rimuovere tutte le preferenze:

```csharp
Preferences.Clear();
```

Oltre a questi metodi accettano entrambi un facoltativa `sharedName` che può essere utilizzato per creare contenitori aggiuntivi per la preferenza. Leggere le specifiche di implementazione della piattaforma sottostante.

## <a name="supported-data-types"></a>Tipi di dati supportati

Tipi di dati seguenti sono supportati **preferenze**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="implementation-details"></a>Dettagli di implementazione

I valori di `DateTime` vengono archiviati in un formato di file binario a 64 bit (valore long integer) usando due metodi definiti dal `DateTime` classe: il [ `ToBinary` ](xref:System.DateTime.ToBinary) metodo viene usato per codificare il `DateTime` valore e il [ `FromBinary` ](xref:System.DateTime.FromBinary(System.Int64)) metodo decodifica il valore. Vedere la documentazione di questi metodi per modifiche che potrebbero essere apportate alla decodificato valori quando un `DateTime` è archiviato vale a dire non un valore di Coordinated Universal Time (UTC).

## <a name="platform-implementation-specifics"></a>Funzionalità specifiche di implementazione della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Tutti i dati vengono archiviati nel [preferenze condivise](https://developer.android.com/training/data-storage/shared-preferences.html). Se nessun `sharedName` viene specificato verranno utilizzate le preferenze condivise predefinite, in caso contrario il nome viene utilizzato per ottenere un **private** condivisi delle preferenze con il nome specificato.

# <a name="iostabios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults) viene usato per archiviare i valori nei dispositivi iOS. Se nessun `sharedName` viene specificato il `StandardUserDefaults` sono usata, in caso contrario il nome viene utilizzato per creare un nuovo `NSUserDefaults` con il nome specificato utilizzato per il `NSUserDefaultsType.SuiteName`.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer) viene usato per archiviare i valori nel dispositivo. Se nessun `sharedName` viene specificato il `LocalSettings` vengono usati, in caso contrario il nome viene utilizzato per creare un nuovo contenitore all'interno di `LocalSettings`.

--------------

## <a name="limitations"></a>Limitazioni

Quando si archiviano una stringa, questa API viene usata per archiviare piccole quantità di testo.  Le prestazioni potrebbero risultare restituendo se si tenta di usarlo per archiviare grandi quantità di testo.

## <a name="api"></a>API

- [Le preferenze di codice sorgente](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Documentazione di preferenze API](xref:Xamarin.Essentials.Preferences)
