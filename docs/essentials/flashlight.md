---
title: 'Xamarin.Essentials: torcia'
description: Questo documento descrive la classe torcia in Xamarin.Essentials, che ha la possibilità di attivare o disattivare la fotocamera del dispositivo flash per trasformarlo in un torcia.
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a5c559653bff38c692f0b1d881d5d8f4cac3d383
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831411"
---
# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials: torcia

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **torcia** classe ha la possibilità di attivare o disattivare la fotocamera del dispositivo flash per trasformarlo in un torcia.

## <a name="getting-started"></a>Introduzione

Per l'accesso di **torcia** è necessaria la funzionalità le seguenti impostazioni specifiche della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Le autorizzazioni torcia e fotocamera sono necessari e devono essere configurate nel progetto Android. Ciò è possibile aggiungere nei modi seguenti:

Aprire il **AssemblyInfo.cs** file sotto il **proprietà** cartella e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

In alternativa, aggiornare il manifesto di Android:

Aprire il **androidmanifest. XML** file sotto il **proprietà** cartella e aggiungere il codice seguente all'interno del **manifesto** nodo.

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

Oppure fare clic con il pulsante destro sul progetto di piattaforma Android e aprire le proprietà del progetto. Sotto **manifesto Android** trovare il **autorizzazioni necessarie:** area e selezionare il **TORCIA** e **fotocamera** autorizzazioni. Si aggiornerà automaticamente il **androidmanifest. XML** file.

Aggiungendo queste autorizzazioni [Google Play verranno automaticamente esclusi i dispositivi](http://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features) senza hardware specifico. È possibile evitare questo problema aggiungendo il codice seguente al file AssemblyInfo.cs nel progetto Android:

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

# <a name="iostabios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-flashlight"></a>Usando torcia

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Il torcia può essere attivata e disattivata tramite il `TurnOnAsync` e `TurnOffAsync` metodi:

```csharp
try
{
    // Turn On
    await Flashlight.TurnOnAsync();

    // Turn Off
    await Flashlight.TurnOffAsync();
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to turn on/off flashlight
}
```

## <a name="platform-implementation-specifics"></a>Funzionalità specifiche di implementazione della piattaforma

### <a name="androidtabandroid-specifics"></a>[Android](#tab/android-specifics)

La classe torcia è stata optmized basato sul sistema operativo del dispositivo.

#### <a name="api-level-23-and-higher"></a>Livello API 23 e versioni successive

Livelli di API più recenti, [Torch modalità](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode) verrà usato per attivare o disattivare l'unità flash del dispositivo.

#### <a name="api-level-22-and-lower"></a>Livello API 22 e inferiore

Viene creata una trama della superficie della fotocamera per attivare o disattivare il `FlashMode` dell'unità della fotocamera. 

### <a name="iostabios-specifics"></a>[iOS](#tab/ios-specifics)

[AVCaptureDevice](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureDevice/) consente di attivare e disattivare il Torch e la modalità Flash del dispositivo.

### <a name="uwptabuwp-specifics"></a>[PIATTAFORMA UWP](#tab/uwp-specifics)

[Lamp](https://docs.microsoft.com/en-us/uwp/api/windows.devices.lights.lamp) viene usato per rilevare il primo lamp nella parte posteriore del dispositivo da attivare o disattivare.

-----

## <a name="api"></a>API

- [Codice sorgente torcia](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Flashlight)
- [Documentazione di torcia API](xref:Xamarin.Essentials.Flashlight)
