---
title: XAML Live anteprima
description: Testare le modifiche al codice dell'applicazione in tempo reale sul dispositivo iOS o Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 19B1F126-866E-4672-92D2-BE2B70ACF0F1
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 12/21/2017
ms.openlocfilehash: 0982818556eb3aac55b30343075017c3773199ec
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-live-previewing"></a>XAML Live anteprima

Uno dei vantaggi di Xamarin Player in tempo reale è la possibilità di pagine XAML anteprima live, apportare modifiche al codice in Visual Studio e visualizzare le modifiche vengono visualizzate immediatamente nel dispositivo. Il dispositivo iOS o Android, o in un simulatore o nell'emulatore, è possibile eseguire l'anteprima in tempo reale. Questa guida viene illustrato come utilizzare la funzionalità di anteprima in tempo reale per visualizzare schermate XAML singoli.

## <a name="requirements"></a>Requisiti

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Un computer che eseguono Windows 7 o versione successiva.
2. Visual Studio 2017 15,4 o versione successiva con il **sviluppo di applicazioni Mobile con .NET** installato carico di lavoro.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Un Mac con OS X 10.11, macOS 10.12 o superiore.
2. Visual Studio per Mac 7.2 o versione successiva. È consigliabile la versione più recente.

-----



<a name="deploydevice" />

## <a name="deploying-to-device"></a>Distribuzione al dispositivo

Prima di poter utilizzare Xamarin Player in tempo reale con il dispositivo iOS o Android, è necessario scaricare l'app Xamarin Player in tempo reale e associarla a Visual Studio, come descritto nel [installare](~/tools/live-player/install.md) Guida. Una volta disporre di una coppia correttamente al dispositivo di Visual Studio, è possibile iniziare l'anteprima della pagina XAML in tempo reale. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Aprire la pagina XAML che si desidera anteprima in tempo reale nell'editor di Visual Studio 2017:

    ![](live-view-images/vs-image1.png)

2. Impostare la configurazione del dispositivo **Debug | iPhone** per iOS o **Debug** per Android, selezionare il dispositivo lettore Live dall'elenco:

    ![](live-view-images/vs-image2.png)

3. Per eseguire questa pagina XAML come una visualizzazione in tempo reale sul dispositivo, selezionare **strumenti > Xamarin Player in tempo reale > Live visualizzazione corrente eseguire** dalla barra dei menu:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Aprire la pagina XAML che si desidera anteprima in tempo reale in Visual Studio per editor Mac:

    ![](live-view-images/image1.png)

2. Impostare la configurazione del dispositivo **Debug | iPhone** per iOS o **Debug** per Android, selezionare il dispositivo lettore Live dall'elenco:

    ![](live-view-images/image2.png)

3. Per eseguire questa pagina XAML come una visualizzazione in tempo reale sul dispositivo, selezionare **eseguire > Live visualizzazione corrente eseguire** dalla barra dei menu:

    ![](live-view-images/image3.png)

-----








## <a name="deploying-to-android-emulator"></a>Distribuzione emulatore Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Aprire la pagina XAML che si desidera anteprima in tempo reale nell'editor di Visual Studio 2017:

    ![](live-view-images/vs-image1.png)

2. Impostare la configurazione del dispositivo **Debug** per Android e selezionare il dispositivo lettore Live dall'elenco:

    ![](live-view-images/vs-image4.png)

3. Per eseguire questa pagina XAML come una visualizzazione in tempo reale nell'emulatore Android, selezionare **strumenti > Xamarin Player in tempo reale > Live visualizzazione corrente eseguire** dalla barra dei menu:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Aprire la pagina XAML che si desidera anteprima in tempo reale in Visual Studio per editor Mac:

    ![](live-view-images/image7.png)

2. Impostare la configurazione del dispositivo **Debug** per Android e selezionare il dispositivo lettore Live dall'elenco:

    ![](live-view-images/image6.png)

3. Per eseguire questa pagina XAML come una visualizzazione in tempo reale sul dispositivo, selezionare Esegui > Live eseguire visualizzazione corrente dalla barra dei menu:

    ![](live-view-images/image3.png)

-----





## <a name="deploying-to-ios-simulator"></a>Distribuzione di simulatore IOS

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Attualmente non è disponibile alcun supporto per l'utilizzo di XAML in tempo reale anteprima nel simulatore iOS remoto in Windows. È invece necessario [distribuisce in un dispositivo](#deploydevice).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Aprire la pagina XAML che si desidera anteprima in tempo reale in Visual Studio per editor Mac:

    ![](live-view-images/image1.png)

2. Impostare la configurazione del dispositivo **Debug | iPhoneSimulator** per iOS e selezionare un simulatore di iOS nell'elenco:

    ![](live-view-images/image2.png)

3. Selezionare **eseguire > Live visualizzazione corrente eseguire** dalla barra dei menu per avviare il simulatore e visualizzare la pagina XAML:

    ![](live-view-images/image4.png)

4. Una volta ha avviato il simulatore, puoi iniziare a modificare il codice XAML e visualizzare l'anteprima in tempo reale:

    ![](live-view-images/image5.png)  

-----








## <a name="related-links"></a>Collegamenti correlati

- [Panoramica di Xamarin Player in tempo reale](https://xamarin.com/live)
- [post di blog](https://blog.xamarin.com/live-player/)
- [Esempi di Xamarin Player in tempo reale](~/tools/livehttps://developer.xamarin.com/samples.md)
