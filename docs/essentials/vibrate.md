---
title: 'Xamarin.Essentials: Vibration'
description: Questo documento descrive la classe Vibration in Xamarin.Essentials, che consente di avviare e arrestare la funzionalità di vibrazione per un periodo di tempo desiderato.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: d9bf7a1e5e0d15f1fdc909745cd439115b6f8463
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898937"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials: Vibration

La classe **Vibration** consente di avviare e arrestare la funzionalità di vibrazione per un periodo di tempo desiderato.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

Per accedere alla funzionalità **Vibration**, è necessaria la configurazione seguente specifica della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

L'autorizzazione Vibrate è obbligatoria e deve essere configurata nel progetto Android. È possibile aggiungerla nei modi seguenti:

Aprire il file **AssemblyInfo.cs** nella cartella **Proprietà** e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

OPPURE aggiornare il manifesto di Android:

Aprire il file **AndroidManifest.xml** nella cartella **Proprietà** e aggiungere quanto segue all'interno del nodo **manifest**.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

Oppure fare clic con il pulsante destro del mouse sul progetto Android e aprire le proprietà del progetto. In **Manifesto Android** trovare l'area **Autorizzazioni necessarie** e selezionare l'autorizzazione **VIBRATE**. Il file **AndroidManifest.xml** verrà aggiornato automaticamente.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Non esistono differenze per questa piattaforma.

-----

## <a name="using-vibration"></a>Uso di Vibration

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Vibration può essere richiesta per un periodo di tempo impostato o per 500 millisecondi, che è l'impostazione predefinita.

```csharp
try
{
    // Use default vibration length
    Vibration.Vibrate();

    // Or use specified time
    var duration = TimeSpan.FromSeconds(1);
    Vibration.Vibrate(duration);
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

L'annullamento della vibrazione del dispositivo può essere richiesto con il metodo `Cancel`:

```csharp
try
{
    Vibration.Cancel();
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

Non esistono differenze per questa piattaforma.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Vibra solo quando il dispositivo è impostato su "Quando non silenzioso".
* Vibra sempre per 500 millisecondi.
* Non è possibile annullare la vibrazione.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Non esistono differenze per questa piattaforma.

-----

## <a name="api"></a>API

- [Codice sorgente di Vibration](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Documentazione dell'API Vibration](xref:Xamarin.Essentials.Vibration)
