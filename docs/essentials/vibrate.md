---
title: 'Xamarin.Essentials: vibrazione'
description: Questo documento descrive la classe di vibrazione in Xamarin.Essentials, che consente di avviare e arrestare le funzionalità di vibrazione per un periodo di tempo desiderato.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ca21f43631c261cd384f9049f30f0fa29e2ca44e
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855169"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials: vibrazione

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **vibrazione** classe consente di avviare e arrestare le funzionalità di vibrazione per un periodo di tempo desiderato.

## <a name="getting-started"></a>Introduzione

Per l'accesso di **vibrazione** è necessaria la funzionalità le seguenti impostazioni specifiche della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

L'autorizzazione di vibrazione è obbligatorio e deve essere configurato nel progetto Android. Ciò è possibile aggiungere nei modi seguenti:

Aprire il **AssemblyInfo.cs** file sotto il **proprietà** cartella e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

In alternativa, aggiornare il manifesto di Android:

Aprire il **androidmanifest. XML** file sotto il **proprietà** cartella e aggiungere il codice seguente all'interno del **manifesto** nodo.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

Oppure fare clic con il pulsante destro sul progetto di piattaforma Android e aprire le proprietà del progetto. Sotto **manifesto Android** trovare il **autorizzazioni necessarie:** area e selezionare il **VIBRAZIONE** l'autorizzazione. Si aggiornerà automaticamente il **androidmanifest. XML** file.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-vibration"></a>Uso di vibrazione

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di vibrazione può essere richiesta per un determinato periodo di tempo o il valore predefinito di 500 millisecondi.

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

Può essere richiesto l'annullamento di vibrazione dispositivo con il `Cancel` metodo:

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

Nessuna differenza di piattaforma.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Solo emissione degli avvisi visivi quando il dispositivo viene impostato su "In suoneria, vibrazione".
* Emissione sempre degli avvisi visivi per 500 millisecondi.
* Non è possibile annullare vibrazione.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Nessuna differenza di piattaforma.

-----

## <a name="api"></a>API

- [Codice sorgente di esempio sulle vibrazioni](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Documentazione di vibrazione API](xref:Xamarin.Essentials.Vibration)
