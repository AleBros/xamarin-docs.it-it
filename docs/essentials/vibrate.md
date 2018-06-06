---
title: 'Xamarin.Essentials: vibrazione'
description: Questo documento descrive la classe vibrazione in Xamarin.Essentials, che consente di avviare e arrestare le funzionalità vibrate per un periodo di tempo desiderato.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2a2902ca4eac8b889f6875580d7cb4ea352803a8
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782921"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials: vibrazione

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **vibrazione** classe consente di avviare e arrestare le funzionalità vibrate per un periodo di tempo desiderato.

## <a name="getting-started"></a>Introduzione

Per l'accesso di **vibrazione** è necessaria la funzionalità le seguenti impostazioni specifiche della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

L'autorizzazione Vibrate è obbligatorio e deve essere configurato nel progetto Android. Questo può essere aggiunto nei modi seguenti:

Aprire la **AssemblyInfo.cs** file sotto il **proprietà** cartella e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

O aggiornare il manifesto Android:

Aprire la **androidmanifest. XML** file sotto il **proprietà** cartella e aggiungere il codice seguente all'interno del **manifesto** nodo.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

Oppure fare clic con il pulsante destro sul progetto di piattaforma Android e aprire le proprietà del progetto. Sotto **manifesto Android** trovare il **delle autorizzazioni necessarie:** area e controllare il **VIBRATE** l'autorizzazione. Verrà automaticamente aggiornato il **Androidmanifest** file.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-vibration"></a>Utilizzando vibrazione

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità vibrazione può essere richiesta per un determinato periodo di tempo o il valore predefinito di 500 millisecondi.

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

Può essere richiesto l'annullamento di resistenza alle vibrazioni dispositivo con il `Cancel` metodo:

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

## <a name="platform-differences"></a>Differenze di piattaforma

| Piattaforma | Differenza |
| --- | --- |
| iOS | Emissione sempre degli avvisi visivi per 500 millisecondi. |
| iOS | Non è possibile annullare vibrazione. |

## <a name="api"></a>API

- [Codice sorgente vibrazione](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Documentazione vibrazione API](xref:Xamarin.Essentials.Vibration)
