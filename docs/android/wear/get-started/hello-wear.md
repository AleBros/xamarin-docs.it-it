---
title: Prima app per Wear
description: Creare la prima app Android Wear ed eseguirla in un emulatore o dispositivo Wear. Questa procedura dettagliata include istruzioni dettagliate per la creazione di un piccolo progetto Android Wear che gestisce i clic sui pulsanti e visualizza un contatore dei clic sul dispositivo Wear. Viene illustrato come eseguire il debug dell'app utilizzando un emulatore Wear o un dispositivo Wear connesso tramite Bluetooth a un telefono Android. Fornisce inoltre un set di suggerimenti per il debug per Android Wear.
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/10/2018
ms.openlocfilehash: ce82f10fdb8159d2007d490747329c1a3b5aeecd
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250063"
---
# <a name="hello-wear"></a>Prima app per Wear

_Creare la prima app Android Wear ed eseguirla in un emulatore o dispositivo Wear. Questa procedura dettagliata include istruzioni dettagliate per la creazione di un piccolo progetto Android Wear che gestisce i clic sui pulsanti e visualizza un contatore dei clic sul dispositivo Wear. Viene illustrato come eseguire il debug dell'app utilizzando un emulatore Wear o un dispositivo Wear connesso tramite Bluetooth a un telefono Android. Fornisce inoltre un set di suggerimenti per il debug per Android Wear._

![Screenshot dell'app Wear da completare in questa esercitazione](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>La prima app Wear

Per creare la prima app Novell. Android Wear, seguire questa procedura:

### <a name="1-create-a-new-android-project"></a>1. Creare un nuovo progetto Android

Creare una nuova **applicazione Android Wear**:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Creazione di una nuova applicazione Android Wear nella finestra di dialogo nuovo progetto](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Creazione di una nuova applicazione Android Wear nella finestra di dialogo nuova soluzione](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----

Questo modello include automaticamente la **libreria Novell Android Wearable Library** NuGet (e le dipendenze), in modo da poter accedere a widget specifici per l'uso. Se non viene visualizzato il modello di usura, consultare la Guida all' [installazione e](~/android/wear/get-started/installation.md) all'installazione per verificare che sia stata installata una Android SDK supportata. 

### <a name="2-choose-the-correct-target-framework"></a>2. Scegliere il **Framework di destinazione** corretto

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Assicurarsi che l'impostazione **minima di Android come destinazione** sia impostata su **Android 5,0 (Lollipop)** o versione successiva: 

[![Impostazione del Framework di destinazione su Android 5,0 in Visual Studio](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Verificare che il Framework di destinazione sia impostato su **Android 5,0 (Lollipop)** o versione successiva:

[![Impostazione del Framework di destinazione su Android 5,0 in Visual Studio per Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

Per altre informazioni sull'impostazione del Framework di destinazione, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="3-edit-the-mainaxml-layout"></a>3. Modificare il layout **Main. aXML**

Configurare il layout in modo che `TextView` contenga `Button` un e un per l'esempio: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent">
  <ScrollView
    android:id="@+id/scroll"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="#000000"
    android:fillViewport="true">
    <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:orientation="vertical">
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:text="Main Activity"
        android:textSize="36sp"
        android:textColor="#006600" />
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:textColor="#cccccc"
        android:id="@+id/result" />
      <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="showNotification"
        android:text="Click Me!"
        android:id="@+id/click_button" />
    </LinearLayout>
  </ScrollView>
</FrameLayout>
```

### <a name="4-edit-the-mainactivitycs-source"></a>4. Modificare l'origine **MainActivity.cs**

Aggiungere il codice per incrementare un contatore e visualizzarlo ogni volta che si fa clic sul pulsante: 

```csharp
[Activity (Label = "WearTest", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
  int count = 1;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    Button button = FindViewById<Button> (Resource.Id.click_button);
    TextView text = FindViewById<TextView> (Resource.Id.result);

    button.Click += delegate {
      text.Text = string.Format ("{0} clicks!", count++);
    };
  }
}
```

### <a name="5-setup-an-emulator-or-device"></a>5. Configurare un emulatore o un dispositivo

Il passaggio successivo consiste nel configurare un emulatore o un dispositivo per distribuire ed eseguire l'app. Se non si ha ancora familiarità con il processo di distribuzione ed esecuzione di app Novell. Android in generale, vedere la [Guida introduttiva a Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

Se non si dispone di un dispositivo Android Wear, ad esempio un SmartWatch Android Wear, è possibile eseguire l'app in un emulatore. Per informazioni sul debug di app in un emulatore, vedere [eseguire il debug di Android Wear in un emulatore](~/android/wear/deploy-test/debug-on-emulator.md).

Se si dispone di un dispositivo Android Wear, ad esempio un SmartWatch Android Wear, è possibile eseguire l'app sul dispositivo anziché usare un emulatore. Per altre informazioni sul debug in un dispositivo Wear, vedere [eseguire il debug su un dispositivo Wear](~/android/wear/deploy-test/debug-on-device.md).

### <a name="6-run-the-android-wear-app"></a>6. Eseguire l'app Android Wear

Il dispositivo Android Wear dovrebbe essere visualizzato nel menu a discesa del dispositivo. Assicurarsi di scegliere il dispositivo Android Wear corretto o l'AVD prima di avviare il debug. Dopo aver selezionato il dispositivo, fare clic sul pulsante Play (Riproduci) per distribuire l'app nell'emulatore o nel dispositivo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Scelta di un AVD Wear nel menu dispositivo di Visual Studio](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Scelta di un AVD Wear nel menu Visual Studio per Mac Device](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

È possibile che venga visualizzato un **solo minuto...** messaggio (o un altro schermo interstiziale): 

![L'emulatore Watch Visualizza solo un minuto...](hello-wear-images/please-wait.png)

Se si usa un emulatore di controllo, l'avvio dell'app potrebbe richiedere qualche minuto. Quando si usa il Bluetooth, la distribuzione dell'app richiede più tempo rispetto alla connessione USB. (Ad esempio, sono necessari circa 5 minuti per distribuire questa app a un orologio LG G collegato con Bluetooth a un telefono Nexus 5).

Dopo che l'app è stata distribuita correttamente, la schermata del dispositivo Wear dovrebbe visualizzare una schermata simile alla seguente:

[![Schermata iniziale dell'app Wear](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

Toccare l' **opzione fare clic su me.** pulsante sulla faccia del dispositivo Wear e vedere l'incremento del conteggio a ogni tocco:

[![Screenshot dell'app Wear dopo 3 clic](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli [esempi di usura](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Android+wear) , incluse le app Android Wear con app per telefono complementari.

Quando si è pronti a distribuire l'app, vedere [uso del pacchetto](~/android/wear/deploy-test/packaging.md).

## <a name="related-links"></a>Collegamenti correlati

- [Fare clic su app me (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-weartest)
