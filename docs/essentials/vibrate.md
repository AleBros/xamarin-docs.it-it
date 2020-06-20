---
title: 'Xamarin.Essentials: Vibrazione'
description: In questo documento viene descritta la classe Vibration in Xamarin.Essentials , che consente di avviare e arrestare la funzionalità di vibrazione per un periodo di tempo desiderato.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2ed2dfdca6b6811089d4dbb5d6e90e794c79591
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84801803"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials: Vibrazione

La classe **Vibration** consente di avviare e arrestare la funzionalità di vibrazione per un periodo di tempo desiderato.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

Per accedere alla funzionalità **Vibration**, è necessaria la configurazione seguente specifica della piattaforma.

# <a name="android"></a>[Android](#tab/android)

L'autorizzazione Vibrate è obbligatoria e deve essere configurata nel progetto Android. È possibile aggiungerla nei modi seguenti:

Aprire il file **AssemblyInfo.cs** nella cartella **Proprietà** e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

OPPURE aggiornare il manifesto di Android:

Aprire il file **AndroidManifest.xml** nella cartella **Proprietà** e aggiungere quanto segue all'interno del nodo **manifesto** .

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

Oppure fare clic con il pulsante destro del mouse sul progetto Android e aprire le proprietà del progetto. In **Manifesto Android** trovare l'area **Autorizzazioni necessarie** e selezionare l'autorizzazione **VIBRATE**. Il file **AndroidManifest.xml** verrà aggiornato automaticamente.

# <a name="ios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwp"></a>[UWP](#tab/uwp)

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

# <a name="android"></a>[Android](#tab/android)

Non esistono differenze per questa piattaforma.

# <a name="ios"></a>[iOS](#tab/ios)

- Vibra solo quando il dispositivo è impostato su "Quando non silenzioso".
- Vibra sempre per 500 millisecondi.
- Non è possibile annullare la vibrazione.

# <a name="uwp"></a>[UWP](#tab/uwp)

Non esistono differenze per questa piattaforma.

-----

## <a name="api"></a>API

- [Codice sorgente di Vibration](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Vibration)
- [Documentazione dell'API Vibration](xref:Xamarin.Essentials.Vibration)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Vibration-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
