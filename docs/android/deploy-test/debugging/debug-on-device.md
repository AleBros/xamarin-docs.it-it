---
title: Eseguire il debug nel dispositivo
description: In questo articolo viene illustrato come eseguire il debug di un'applicazione Xamarin.Android su un dispositivo Android fisico.
ms.topic: article
ms.prod: xamarin
ms.assetid: 153D3746-A27F-198B-48FE-D219C0133A79
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 5e9874fba52b576494be5ac42ff13fdd0be4d9e7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="debug-on-device"></a>Eseguire il debug nel dispositivo

_In questo articolo viene illustrato come eseguire il debug di un'applicazione Xamarin.Android su un dispositivo Android fisico._

## <a name="debug-on-device-overview"></a>Panoramica del debug nel dispositivo

È possibile eseguire il debug di un'applicazione Xamarin.Android in un dispositivo Android mediante Visual Studio per Mac o Visual Studio. Prima di eseguire il debug in un dispositivo, questo deve essere [impostato per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md) e connesso a PC o Mac.

<a name="Debug_Application" />

## <a name="debug-application"></a>Debug Application (Eseguire il debug dell'applicazione)

Una volta che il dispositivo è connesso al computer, il debug di un'applicazione Xamarin.Android viene eseguito esattamente come per qualsiasi altro prodotto Xamarin o applicazione .NET. Verificare che nell'IDE siano selezionati la configurazione **Debug** e il dispositivo esterno. In questo modo i simboli di debug necessari sono disponibili e l'IDE può connettersi all'applicazione in esecuzione: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Configurazione per il debug selezionata](debug-on-device-images/image1-vs.png)

In seguito viene impostato un punto di interruzione nel codice:

![Punto di interruzione impostato nella riga di codice](debug-on-device-images/image2-vs.png)

Quando il dispositivo è stato selezionato, Xamarin.Android si connette al dispositivo, distribuisce l'applicazione e quindi la esegue. Quando viene raggiunto il punto di interruzione, il debugger arresta l'applicazione, consentendo il debug dell'applicazione in modo analogo a qualsiasi altra applicazione C#: 

![Punto di interruzione raggiunto](debug-on-device-images/image3-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Configurazione per il debug selezionata](debug-on-device-images/image1-xs.png)

In seguito viene impostato un punto di interruzione nel codice:

![Punto di interruzione impostato nella riga di codice](debug-on-device-images/image2-xs.png)

Quando il dispositivo è stato selezionato, Xamarin.Android si connette al dispositivo, distribuisce l'applicazione e quindi la esegue. Quando viene raggiunto il punto di interruzione, il debugger arresta l'applicazione, consentendo il debug dell'applicazione in modo analogo a qualsiasi altra applicazione C#: 

![Punto di interruzione raggiunto](debug-on-device-images/image3-xs.png)

-----


<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo documento è stato illustrato come eseguire il debug di un'applicazione Xamarin.Android impostando un punto di interruzione e selezionando il dispositivo di destinazione.


## <a name="related-links"></a>Collegamenti correlati

- [Set Up Device for Development](~/android/get-started/installation/set-up-device-for-development.md) (Configurare il dispositivo per lo sviluppo)
- [Impostazione dell'attributo Debuggable](~/android/deploy-test/debuggable-attribute.md)
