---
title: 'Xamarin.Essentials: Torcia'
description: In questo documento viene descritta la classe Flashlight in Xamarin.Essentials , che consente di attivare o disattivare la fotocamera flash del dispositivo per trasformarla in una torcia.
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7a8a90674b395c90f698a4a0854dc0dc3fc5fe15
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802354"
---
# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials: Torcia

La classe **Flashlight** può attivare o disattivare il flash della fotocamera del dispositivo per trasformarlo in una torcia.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

Per accedere alla funzionalità **Flashlight**, è necessaria la configurazione seguente specifica della piattaforma.

# <a name="android"></a>[Android](#tab/android)

Le autorizzazioni Flashlight e Camera sono obbligatorie e devono essere configurate nel progetto Android. È possibile aggiungerla nei modi seguenti:

Aprire il file **AssemblyInfo.cs** nella cartella **Proprietà** e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

OPPURE aggiornare il manifesto di Android:

Aprire il file **AndroidManifest.xml** nella cartella **Proprietà** e aggiungere quanto segue all'interno del nodo **manifesto** .

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

Oppure fare clic con il pulsante destro del mouse sul progetto Android e aprire le proprietà del progetto. In **Manifesto Android** trovare l'area **Autorizzazioni necessarie** e selezionare le autorizzazioni **FLASHLIGHT** e **CAMERA**. Il file **AndroidManifest.xml** verrà aggiornato automaticamente.

Aggiungendo queste autorizzazioni, [Google Play filtrerà automaticamente i dispositivi](https://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features) senza hardware specifico. Per aggirare questo problema, aggiungere il codice seguente al file AssemblyInfo.cs nel progetto Android:

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

[!include[](~/essentials/includes/android-permissions.md)]

# <a name="ios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwp"></a>[UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-flashlight"></a>Uso di Flashlight

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La torcia può essere attivata o disattivata tramite i metodi `TurnOnAsync` e `TurnOffAsync`:

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

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

### <a name="android"></a>[Android](#tab/android)

La classe Flashlight è stata ottimizzata in base al sistema operativo del dispositivo.

#### <a name="api-level-23-and-higher"></a>API livello 23 e successivi

Nei livelli API più recenti verrà usata la [modalità torcia](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode) per attivare o disattivare l'unità flash nel dispositivo.

#### <a name="api-level-22-and-lower"></a>API livello 22 e precedenti

Viene creata una trama della superficie della fotocamera per attivare o disattivare l'elemento `FlashMode` dell'unità fotocamera.

### <a name="ios"></a>[iOS](#tab/ios)

Viene usata la classe [AVCaptureDevice](xref:AVFoundation.AVCaptureDevice) per attivare e disattivare la modalità torcia e flash del dispositivo.

### <a name="uwp"></a>[UWP](#tab/uwp)

Viene usata la classe [Lamp](https://docs.microsoft.com/uwp/api/windows.devices.lights.lamp) per rilevare la prima lampada nella parte posteriore del dispositivo da attivare o disattivare.

-----

## <a name="api"></a>API

- [Codice sorgente di Flashlight](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Flashlight)
- [Documentazione dell'API Flashlight](xref:Xamarin.Essentials.Flashlight)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Flashlight-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
