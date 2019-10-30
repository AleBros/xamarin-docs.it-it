---
title: Architetture CPU
description: Novell. Android supporta diverse architetture della CPU, inclusi i dispositivi a 32 bit e a 64 bit. Questo articolo illustra come indirizzare un'app a una o più architetture CPU supportate da Android.
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2019
ms.openlocfilehash: 2fec93ce6924d74f848cbd24736e75b0d1304584
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019365"
---
# <a name="cpu-architectures"></a>Architetture CPU

_Novell. Android supporta diverse architetture della CPU, inclusi i dispositivi a 32 bit e a 64 bit. Questo articolo illustra come indirizzare un'app a una o più architetture CPU supportate da Android._

## <a name="cpu-architectures-overview"></a>Cenni preliminari sulle architetture della CPU

Quando si prepara l'app per il rilascio, è necessario specificare le architetture CPU della piattaforma supportate dall'app. Un singolo APK può contenere codice macchina per supportare più architetture diverse. Ogni raccolta di codice specifico per l'architettura è associata a un'interfaccia ABI ( *Application Binary Interface* ). Ogni ABI definisce il modo in cui il codice macchina dovrebbe interagire con Android in fase di esecuzione.
Per altre informazioni sul funzionamento, vedere dispositivi multicore [&amp; Novell. Android](~/android/deploy-test/multicore-devices.md).

## <a name="how-to-specify-supported-architectures"></a>Come specificare le architetture supportate

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In genere, è possibile selezionare in modo esplicito un'architettura (o architetture) quando l'app è configurata per la **versione**. Quando l'app è configurata per il **debug**, le opzioni **Usa runtime condiviso** e **Usa Fast Deployment** sono abilitate, che disabilitano la selezione esplicita dell'architettura.

In Visual Studio fare clic con il pulsante destro del mouse sul progetto nella **Esplora soluzioni** e scegliere **Proprietà**. Nella pagina **Opzioni Android** selezionare la sezione **Proprietà pacchetto** e verificare che l'opzione **Usa runtime condiviso** sia disabilitata (la disattivazione consente di selezionare in modo esplicito l'Abi da supportare). Fare clic sul pulsante **Avanzate** e, in **architetture supportate**, controllare le architetture che si desidera supportare:

[![selezione di ARMEABI e ARMEABI-v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In genere, è possibile selezionare in modo esplicito un'architettura (o architetture) quando l'app è configurata per la **versione**. Quando l'app è configurata per il **debug**, le opzioni **Usa runtime Mono condiviso** e **Fast assembly** sono abilitate, impedendo la selezione esplicita dell'architettura.

In Visual Studio per Mac individuare il progetto nel riquadro della **soluzione** , fare clic sull'icona a forma di ingranaggio accanto al progetto e selezionare **Opzioni**. Nella finestra di dialogo **Opzioni progetto** fare clic su **Build Android**. Fare clic sulla scheda **generale** e verificare che l'opzione **Usa runtime di mono condiviso** sia disabilitata (la disattivazione consente di selezionare in modo esplicito l'Abi da supportare). Fare clic sulla scheda **Avanzate** e, in **Abi supportato**, controllare l'ABI per le architetture che si desidera supportare:

[![selezione di ARMEABI e ARMEABI-v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----

Xamarin.Android supporta le architetture seguenti:

- **armeabi** &ndash; CPU basate su ARM che supportano almeno il set di istruzioni ARMv5TE. Si noti che `armeabi` non è thread-safe e non deve essere usato su dispositivi con più CPU.

> [!NOTE]
> A partire da [Novell. Android 9,2](https://docs.microsoft.com/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture), `armeabi` non è più supportato.

- **ARMEABI-v7a** &ndash; CPU basate su ARM con operazioni a virgola mobile hardware e più dispositivi di CPU (SMP). Si noti che `armeabi-v7a` codice computer non viene eseguito sui dispositivi ARMv5.

- **arm64-v8a** &ndash; CPU basata sull'architettura ARMv8 a 64 bit.

- **x86** &ndash; CPU che supportano il set di istruzioni x86 (o IA-32). Questo set di istruzioni è equivalente a quello di Pentium Pro, incluse le istruzioni MMX, SSE, SSE2 e SSE3.

- **x86_64** CPU che supportano il set di istruzioni x86 a 64 bit (noto anche come *x64* e *amd64*).

Il valore predefinito di Novell. Android è `armeabi-v7a` per le build di **rilascio** . Questa impostazione garantisce prestazioni significativamente migliori rispetto a `armeabi`. Se la destinazione è una piattaforma ARM a 64 bit, ad esempio Nexus 9, selezionare `arm64-v8a`. Se si distribuisce l'app in un dispositivo x86, selezionare `x86`. Se il dispositivo x86 di destinazione usa un'architettura della CPU a 64 bit, selezionare `x86_64`.

## <a name="targeting-multiple-platforms"></a>Destinazione di più piattaforme

Per definire come destinazione più architetture della CPU, è possibile selezionare più di un'ABI (a scapito delle dimensioni del file APK più grandi). Per creare un APK separato per ogni architettura supportata, è possibile usare l'opzione **genera un pacchetto (con estensione APK) per ogni Abi selezionato** (descritta in [impostare le proprietà](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)di creazione pacchetti).

Non è necessario selezionare **arm64-V8A** o **x86_64** per i dispositivi di destinazione a 64 bit; non è necessario il supporto a 64 bit per eseguire l'app su hardware a 64 bit. Ad esempio, i dispositivi ARM a 64 bit (ad esempio, [Nexus 9](https://www.google.com/nexus/9/)) possono eseguire app configurate per `armeabi-v7a`. Il vantaggio principale dell'abilitazione del supporto a 64 bit consiste nel consentire all'app di gestire una maggiore quantità di memoria.

> [!NOTE]
> Da agosto 2018 le nuove app dovranno usare il livello API 26 come destinazione e da agosto 2019 le app [dovranno fornire versioni a 64 bit](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html) oltre alla versione a 32 bit.

## <a name="additional-information"></a>Informazioni aggiuntive

In alcune situazioni, potrebbe essere necessario creare un APK separato per ogni architettura (per ridurre le dimensioni dell'APK o perché l'app dispone di librerie condivise specifiche di una particolare architettura della CPU).
Per altre informazioni su questo approccio, vedere [creare apk specifici di Abi](~/android/deploy-test/building-apps/abi-specific-apks.md).
