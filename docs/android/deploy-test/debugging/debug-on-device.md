---
title: Eseguire il debug nel dispositivo
description: In questo articolo viene illustrato come eseguire il debug di un'applicazione Xamarin.Android su un dispositivo Android fisico.
ms.prod: xamarin
ms.assetid: 153D3746-A27F-198B-48FE-D219C0133A79
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 3ca524e451a7a4eb838805c839b33c4b9dd6bddd
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75556535"
---
# <a name="debug-on-an-android-device"></a>Eseguire il debug su un dispositivo AndroidDebug on an Android device

_In questo articolo viene illustrato come eseguire il debug di un'applicazione Xamarin.Android su un dispositivo Android fisico._

È possibile eseguire il debug di un'app Xamarin.Android in un dispositivo Android mediante Visual Studio per Mac o Visual Studio. Prima di eseguire il debug in un dispositivo, questo deve essere [impostato per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md) e connesso a PC o Mac.

## <a name="debug-application"></a>Debug Application (Eseguire il debug dell'applicazione)

Una volta che il dispositivo è connesso al computer, il debug di un'applicazione Xamarin.Android viene eseguito esattamente come per qualsiasi altro prodotto Xamarin o applicazione .NET. Verificare che nell'IDE siano selezionati la configurazione **Debug** e il dispositivo esterno. In questo modo i simboli di debug necessari sono disponibili e l'IDE può connettersi all'applicazione in esecuzione: 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Configurazione per il debug selezionata](debug-on-device-images/image1-vs.png)

In seguito viene impostato un punto di interruzione nel codice:

![Punto di interruzione impostato nella riga di codice](debug-on-device-images/image2-vs.png)

Quando il dispositivo è stato selezionato, Xamarin.Android si connette al dispositivo, distribuisce l'applicazione e quindi la esegue. Quando viene raggiunto il punto di interruzione, il debugger arresta l'applicazione, consentendo il debug dell'applicazione in modo analogo a qualsiasi altra applicazione C#: 

![Punto di interruzione raggiunto](debug-on-device-images/image3-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![Configurazione per il debug selezionata](debug-on-device-images/image1-xs.png)

In seguito viene impostato un punto di interruzione nel codice:

![Punto di interruzione impostato nella riga di codice](debug-on-device-images/image2-xs.png)

Quando il dispositivo è stato selezionato, Xamarin.Android si connette al dispositivo, distribuisce l'applicazione e quindi la esegue. Quando viene raggiunto il punto di interruzione, il debugger arresta l'applicazione, consentendo il debug dell'applicazione in modo analogo a qualsiasi altra applicazione C#: 

![Punto di interruzione raggiunto](debug-on-device-images/image3-xs.png)

-----

## <a name="summary"></a>Riepilogo

In questo documento è stato illustrato come eseguire il debug di un'applicazione Xamarin.Android impostando un punto di interruzione e selezionando il dispositivo di destinazione.

## <a name="related-links"></a>Collegamenti correlati

- [Configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md)
- [Setting the Debuggable Attribute (Impostazione dell'attributo Debuggable)](~/android/deploy-test/debuggable-attribute.md)
