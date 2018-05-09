---
title: Preferenze Xamarin.Essentials
description: La classe preferenze Salva preferenze per l'applicazione in un archivio chiave/valore.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3886432f51125508349fd7815ac374cb0339c254
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-preferences"></a>Preferenze Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **preferenze** classe consente di archiviare le preferenze dell'applicazione in un archivio chiave/valore.

## <a name="using-secure-storage"></a>Utilizzo dell'archiviazione sicura

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per salvare un valore per un determinato _chiave_ nelle preferenze:

```csharp
Preferences.Set("my_key", "my_value");
```

Per recuperare un valore dalle preferenze o un valore predefinito se non impostato:

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

Oltre a questi metodi prendono in facoltativa `sharedName` che può essere usato per creare altri contenitori alle preferenze. Leggere le specifiche di implementazione della piattaforma sottostante.

## <a name="supported-data-types"></a>Tipi di dati supportati

Tipi di dati seguenti sono supportati **preferenze**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**

## <a name="platform-implementation-specifics"></a>Informazioni specifiche sull'implementazione della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Tutti i dati vengono archiviati nel [preferenze condivise](https://developer.android.com/training/data-storage/shared-preferences.html). Se nessun `sharedName` viene specificato vengono utilizzate le preferenze condiviso predefinito, altrimenti il nome viene utilizzato per ottenere un **privato** condivisa preferenze con il nome specificato.

# <a name="iostabios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults) viene utilizzato per archiviare i valori nei dispositivi iOS. Se nessun `sharedName` viene specificato il `StandardUserDefaults` sono utilizzati, in caso contrario il nome viene utilizzato per creare un nuovo `NSUserDefaults` con il nome specificato utilizzato per il `NSUserDefaultsType.SuiteName`.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer) viene utilizzato per archiviare i valori nel dispositivo. Se nessun `sharedName` viene specificato il `LocalSettings` vengono utilizzati, in caso contrario il nome viene utilizzato per creare un nuovo contenitore all'interno di `LocalSettings`.

--------------

## <a name="limitations"></a>Limitazioni

Quando si archiviano una stringa, questa API è progettata per archiviare piccole quantità di testo.  Le prestazioni potrebbero essere restituendo se si tenta di utilizzarlo per archiviare grandi quantità di testo.

## <a name="api"></a>API

- [Preferenze del codice sorgente.](https://github.com/xamarin/Essentials/tree/master/Essentials/Preferences)
- [Documentazione di preferenze API](xref:Xamarin.Essentials.Preferences)
