---
title: Architetture CPU
description: "Xamarin supporta diverse architetture della CPU, inclusi i dispositivi a 32 bit e 64 bit. In questo articolo viene illustrato come destinazione un'applicazione per uno o più architetture della CPU supportata Android."
ms.topic: article
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 3df6dc72eaed74ad335596d55db8b1295b16f3c2
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="cpu-architectures"></a>Architetture CPU

_Xamarin supporta diverse architetture della CPU, inclusi i dispositivi a 32 bit e 64 bit. In questo articolo viene illustrato come destinazione un'applicazione per uno o più architetture della CPU supportata Android._

## <a name="cpu-architectures-overview"></a>Panoramica di architetture della CPU

Quando si prepara l'app per il rilascio, è necessario specificare quali architetture di piattaforma CPU supporta l'app. Un singolo APK può contenere codice macchina per supportare più architetture diverse. Ogni raccolta di codice specifico dell'architettura è associato un *Application Binary Interface* (ABI). Ogni ABI definisce la modalità in cui è previsto questo codice macchina per interagire con Android in fase di esecuzione.
Per ulteriori informazioni sul funzionamento, vedere [dispositivi multicore &amp; xamarin](~/android/deploy-test/multicore-devices.md).


## <a name="how-to-specify-supported-architectures"></a>Come specificare architetture supportate

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

In genere, si selezionare in modo esplicito un'architettura (o architetture) quando l'app è configurata per **versione**. Quando l'app è configurata per **Debug**, **Usa fase di esecuzione condiviso** e **Usa distribuzione rapida** opzioni sono abilitate, che disabilita selezione esplicita architettura.

In Visual Studio, fare doppio clic su **proprietà** nel progetto in **Esplora** e selezionare il **opzioni Android** pagina. Fare clic su di **imballaggio** scheda e verificare che **Usa fase di esecuzione condiviso** è disabilitato (disattivando questa opzione consente di selezionare quali ABIs per supportare in modo esplicito). Fare clic su di **avanzate** scheda e, in **proprietà avanzate**, controllare le architetture che si desidera supportare:

[![Selezione di armeabi e armeabi v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

In genere, si selezionare in modo esplicito un'architettura (o architetture) quando l'app è configurata per **versione**. Quando l'app è configurata per **Debug**, **Usa condivisa runtime Mono** e **rapida distribuzione di assembly** opzioni sono abilitate, che impediscono l'architettura esplicita selezione.

In Visual Studio per Mac, individuare il progetto nel **soluzione** di riempimento, fare clic sull'icona a forma di ingranaggio accanto al progetto e selezionare **opzioni**. Nel **opzioni progetto** finestra di dialogo, fare clic su **di compilazione Android**. Fare clic su di **generale** scheda e verificare che **Usa condivisa runtime Mono** è disabilitato (disattivando questa opzione consente di selezionare quali ABIs per supportare in modo esplicito). Fare clic su di **avanzate** scheda e, in **supportati ABIs**, controllare il ABIs per le architetture che si desidera supportare:

[![Selezione di armeabi e armeabi v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android supporta le architetture seguenti:

-   **armeabi** &ndash; CPU basato su ARM supportano almeno il set di istruzioni ARMv5TE. Si noti che `armeabi` non è thread-safe e non deve essere utilizzato nei dispositivi con più CPU.

-   **armeabi v7a** &ndash; CPU basato su ARM con operazioni a virgola mobile hardware e di più dispositivi di CPU (SMP). Si noti che `armeabi-v7a` codice macchina non verrà eseguito su dispositivi ARMv5.

-   **arm64 v8a** &ndash; CPU in base all'architettura di ARMv8 a 64 bit.

-   **x86** &ndash; CPU che supportano x86 (o IA-32) set di istruzioni. Questo set di istruzioni è equivalente a quello di Pentium Pro, incluse le istruzioni MMX, SSE, SSE2 e SSE3.

-   **X86_64** CPU che supportano x86 a 64 bit (nota anche come *x64* e *AMD64*) set di istruzioni.

Per impostazione predefinita xamarin `armeabi-v7a` per **versione** compilazioni. Questa impostazione offre prestazioni decisamente migliori rispetto a `armeabi`. Se la destinazione è una piattaforma a 64 bit ARM (ad esempio 9 Nexus), selezionare `arm64-v8a`. Se si distribuisce l'app in x86 dispositivo, seleziona `x86`. Se il dispositivo di destinazione x86 utilizza un'architettura della CPU a 64 bit, selezionare `x86_64`.

## <a name="targeting-multiple-platforms"></a>Destinazione a piattaforme Multiple

Per diverse architetture della CPU di destinazione, è possibile selezionare più ABI (a scapito della dimensione del file APK). È possibile utilizzare il **generare un pacchetto (con estensione apk) per ABI selezionato** opzione (descritto in [imposta proprietà di creazione di pacchetti](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) per creare un file APK separato per ogni architettura supportata.

Non è necessario selezionare **arm64 v8a** o **x86_64** per dispositivi a 64 bit; supporto a 64 bit non è necessario eseguire l'app su hardware a 64 bit. Ad esempio, i dispositivi ARM a 64 bit (ad esempio il [9 Nexus](http://www.google.com/nexus/9/)) può eseguire le applicazioni configurate per `armeabi-v7a`. Il vantaggio principale dell'attivazione del supporto a 64 bit è per rendere possibile per l'app per una maggiore quantità di memoria.

> [!NOTE]
> supporto di runtime a 64 bit è attualmente una funzionalità sperimentale. Tenere presente che il runtime a 64 bit sono *non* necessarie per eseguire l'app nei dispositivi a 64 bit. 

## <a name="additional-information"></a>Informazioni aggiuntive

In alcuni casi, potrebbe essere necessario creare un file APK separato per ogni architettura (per ridurre le dimensioni dell'APK, oppure perché l'app ha condiviso librerie specifiche di una particolare architettura della CPU).
Per ulteriori informazioni, vedere [compilare ABI specifiche APKs](~/android/deploy-test/building-apps/abi-specific-apks.md).
