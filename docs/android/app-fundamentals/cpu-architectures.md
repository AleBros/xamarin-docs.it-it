---
title: Architetture CPU
description: Xamarin. Android supporta diverse architetture della CPU, inclusi i dispositivi a 32 e 64 bit. Questo articolo illustra come specificare come destinazione di un'app a uno o più architetture della CPU Android è supportata.
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f2865858552d4445dff95c85767c41849c19cc29
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122270"
---
# <a name="cpu-architectures"></a>Architetture CPU

_Xamarin. Android supporta diverse architetture della CPU, inclusi i dispositivi a 32 e 64 bit. Questo articolo illustra come specificare come destinazione di un'app a uno o più architetture della CPU Android è supportata._

## <a name="cpu-architectures-overview"></a>Panoramica delle architetture della CPU

Quando si prepara l'app per la versione, è necessario specificare quali architetture CPU della piattaforma supportata dall'app. Un singolo APK può contenere codice macchina per supportare più architetture diverse. Ogni raccolta di codice specifico dell'architettura è associata un' *Application Binary Interface* (ABI). Ogni interfaccia ABI definisce come questo codice macchina si prevede di interagire con Android in fase di esecuzione.
Per altre informazioni sul funzionamento, vedere [dispositivi multicore &amp; xamarin. Android](~/android/deploy-test/multicore-devices.md).


## <a name="how-to-specify-supported-architectures"></a>Come specificare le architetture supportate

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In genere, si selezionare in modo esplicito un'architettura (o architetture) quando l'app è configurata per **rilascio**. Quando l'app è configurata per **Debug**, il **Usa Runtime condiviso** e **Usa Fast Deployment** opzioni sono abilitate, che disabilita selezione esplicita di architettura.

In Visual Studio, fare doppio clic sul progetto con il **Esplora soluzioni** e selezionare **proprietà**. Sotto il **opzioni Android** pagina controllo la **proprietà per creazione pacchetto** sezione e verificare che **Usa Runtime condiviso** è disabilitato (disabilitazione di questa impostazione consente in modo esplicito Selezionare sulle ABI da supportare). Scegliere il **avanzate** pulsante e, in **architetture supportate**, controllare le architetture che si desidera supportare:

[![Selezione armeabi e armeabi-v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In genere, si selezionare in modo esplicito un'architettura (o architetture) quando l'app è configurata per **rilascio**. Quando l'app è configurata per **Debug**, il **Usa runtime di Mono condiviso** e **distribuzione rapida di assembly** opzioni sono abilitate, che impediscono di architettura esplicita selezione.

In Visual Studio per Mac, individuare il progetto nel **soluzione** riempimento, fai clic sull'icona a forma di ingranaggio accanto al progetto e selezionare **opzioni**. Nel **opzioni progetto** finestra di dialogo, fare clic su **compilazione Android**. Fare clic sui **generali** scheda e verificare che **Usa runtime di Mono condiviso** è disabilitato (disattivando questa opzione consente di selezionare quale ABI da supportare in modo esplicito). Fare clic sui **avanzate** scheda e, in **interfacce ABI supportate**, controllare l'ABI per le architetture che si vuole supportare:

[![Selezione armeabi e armeabi-v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android supporta le architetture seguenti:

-   **armeabi** &ndash; CPU basate su ARM che supportano almeno il set di istruzioni ARMv5TE. Si noti che `armeabi` non è thread-safe e non deve essere usata nei dispositivi con più CPU.

-   **armeabi-v7a** &ndash; CPU basate su ARM con operazioni a virgola mobile hardware e di più dispositivi di CPU (SMP). Si noti che `armeabi-v7a` codice macchina non verranno eseguite su dispositivi ARMv5.

-   **arm64 v8a** &ndash; CPU basate sull'architettura di ARMv8 a 64 bit.

-   **x86** &ndash; CPU che supportano x86 (o IA-32) set di istruzioni. Questo set di istruzioni è equivalente a quella di Pentium Pro, incluse le istruzioni MMX, SSE, SSE2 e SSE3.

-   **X86_64** CPU che supportano x86 a 64 bit (noto anche come *x64* e *AMD64*) set di istruzioni.

Xamarin. Android per impostazione predefinita `armeabi-v7a` per **rilascio** compilazioni. Questa impostazione offre prestazioni notevolmente migliori rispetto a `armeabi`. Se la destinazione è una piattaforma ARM a 64 bit (ad esempio Nexus 9), selezionare `arm64-v8a`. Se si distribuisce l'app in un x86 dispositivo, selezionare `x86`. Se il dispositivo di destinazione x86 utilizza un'architettura della CPU a 64 bit, selezionare `x86_64`.

## <a name="targeting-multiple-platforms"></a>Destinazione a piattaforme Multiple

Per impostare come destinazione più architetture della CPU, è possibile selezionare più di un ABI (a scapito della dimensione del file APK). È possibile usare la **generare un pacchetto (apk) per ogni ABI selezionato** opzione (descritto in [impostare proprietà per creazione pacchetto](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) per creare un file APK separati per ogni architettura supportata.

Non è necessario selezionare **arm64 v8a** oppure **x86_64** di interagire con dispositivi a 64 bit; il supporto a 64 bit non è necessario per eseguire l'app su hardware a 64 bit. Ad esempio, i dispositivi ARM a 64 bit (ad esempio la [Nexus 9](http://www.google.com/nexus/9/)) possono eseguire le app configurate per `armeabi-v7a`. Il vantaggio principale dell'abilitazione del supporto a 64 bit è per rendere possibile per l'app soddisfare una maggiore quantità di memoria.

> [!NOTE]
> supporto di runtime a 64 bit è attualmente una funzionalità sperimentale. Tenere presente che i Runtime a 64 bit vengono *non* necessari per eseguire l'app nei dispositivi a 64 bit. 

## <a name="additional-information"></a>Informazioni aggiuntive

In alcuni casi, potrebbe essere necessario creare un file APK separati per ogni architettura (per ridurre le dimensioni dell'APK, oppure perché l'app ha librerie condivise che sono specifiche per una particolare architettura della CPU).
Per altre informazioni su questo approccio, vedere [compilazione di Apk specifici di ABI](~/android/deploy-test/building-apps/abi-specific-apks.md).
