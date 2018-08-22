---
redirect_url: /xamarin/tools/live-player/
title: XAML anteprima in tempo reale
description: Questo documento illustra come usare Xamarin Live Player per live anteprima XAML pagine, modificare il XAML e visualizzare le modifiche visualizzate immediatamente nel dispositivo.
ms.prod: xamarin
ms.assetid: 86E9A179-21F8-4F3A-A9CE-36F0FC5DB4A8
author: topgenorth
ms.author: toopge
ms.date: 08/08/2018
ms.openlocfilehash: 200d19aa0a13d0557e52cb90021190978838ed39
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251136"
---
# <a name="xaml-live-previewing"></a>XAML anteprima in tempo reale

Uno dei vantaggi di Xamarin Live Player è la possibilità di live pagine XAML dell'anteprima, apportare modifiche al codice in Visual Studio e visualizzare le modifiche vengono visualizzate immediatamente nel dispositivo. Nel dispositivo Android o in un simulatore o un emulatore, è possibile eseguire l'anteprima in tempo reale. Questa guida illustra come usare la funzionalità di anteprima in tempo reale per visualizzare le schermate XAML singoli.

## <a name="requirements"></a>Requisiti

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Un computer che eseguono Windows 7 o versione successiva.
2. Visual Studio 2017 versione 15.4 o versioni successive con il **sviluppo di applicazioni per dispositivi mobili con .NET** installato il carico di lavoro.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Un Mac con OS X 10.11, macOS 10.12 o versione successiva.
2. Visual Studio per Mac 7.2 o versioni successive. È consigliabile la versione più recente.

-----

<a name="deploydevice" />

## <a name="deploying-to-device"></a>Distribuzione nel dispositivo

Prima di Xamarin Live Player è possibile usare con il dispositivo Android, è necessario scaricare l'app Xamarin Live Player e abbinarlo a Visual Studio, come descritto nel [installare](~/tools/live-player/install.md) Guida. Una volta correttamente avere associato il dispositivo a Visual Studio, è possibile iniziare l'anteprima in tempo reale della pagina XAML. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire la pagina XAML che si desidera anteprima in tempo reale nell'editor di Visual Studio 2017:

    ![](live-view-images/vs-image1.png)

2. Impostare la configurazione del dispositivo **Debug** e selezionare il dispositivo Live Player dall'elenco:

    ![](live-view-images/vs-image2.png)

3. Per eseguire questa pagina XAML come visualizzazione attiva nel dispositivo, selezionare **strumenti > Xamarin Live Player > Vista corrente Live Run** dalla barra dei menu:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire la pagina XAML che si desidera anteprima in tempo reale in Visual Studio per l'editor Mac:

    ![](live-view-images/image1.png)

2. Impostare la configurazione del dispositivo **Debug** e selezionare il dispositivo Live Player dall'elenco:

    ![](live-view-images/image2.png)

3. Per eseguire questa pagina XAML come visualizzazione attiva nel dispositivo, selezionare **Esegui > Vista corrente Live Run** dalla barra dei menu:

    ![](live-view-images/image3.png)

-----

## <a name="deploying-to-android-emulator"></a>Distribuzione emulatore di Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Aprire la pagina XAML che si desidera anteprima in tempo reale nell'editor di Visual Studio 2017:

    ![](live-view-images/vs-image1.png)

2. Impostare la configurazione del dispositivo **Debug** per Android e selezionare il dispositivo Live Player dall'elenco:

    ![](live-view-images/vs-image4.png)

3. Per eseguire questa pagina XAML come visualizzazione attiva nell'emulatore Android, selezionare **strumenti > Xamarin Live Player > Vista corrente Live Run** dalla barra dei menu:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Aprire la pagina XAML che si desidera anteprima in tempo reale in Visual Studio per l'editor Mac:

    ![](live-view-images/image7.png)

2. Impostare la configurazione del dispositivo **Debug** per Android e selezionare il dispositivo Live Player dall'elenco:

    ![](live-view-images/image6.png)

3. Per eseguire questa pagina XAML come visualizzazione attiva nel dispositivo, selezionare Esegui > Vista corrente Live Run dalla barra dei menu:

    ![](live-view-images/image3.png)

-----

## <a name="related-links"></a>Collegamenti correlati

- [Panoramica di Xamarin Live Player](https://xamarin.com/live)
- [Post di blog](https://blog.xamarin.com/live-player/)
- [Esempi di Xamarin Live Player](~/tools/live-player/samples.md)
