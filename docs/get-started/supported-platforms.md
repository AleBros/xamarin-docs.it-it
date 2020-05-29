---
title: " Xamarin.Forms piattaforme supportate" Descrizione: "requisiti per piattaforma e sistema di sviluppo per Xamarin.Forms ".
ms. prod: Novell MS. AssetID: eecaf6a5-567c-49b2-AC83-2a195596c5bf ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 01/22/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-supported-platforms"></a>Xamarin.Formspiattaforme supportate

Xamarin.Formsle applicazioni possono essere scritte per i sistemi operativi seguenti:

- iOS 9 o versione successiva.
- Android 4.4 (API 19) o versione successiva ([altri dettagli](#android-platform-support)). È tuttavia consigliabile usare Android 5.0 (API 21) come API minima, perché garantisce la compatibilità completa con tutte le librerie di supporto Android, pur continuando ad avere come destinazione la maggior parte dei dispositivi Android.
- Piattaforma UWP (Universal Windows Platform) per Windows 10.

Xamarin.Formsle app per iOS, Android e la piattaforma UWP (Universal Windows Platform) (UWP) possono essere compilate in Visual Studio. Per lo sviluppo iOS è tuttavia necessario un Mac connesso alla rete con la versione più recente di Xcode e la versione minima di macOS specificata da Apple. Per altre informazioni, vedere [Requisiti Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

Xamarin.Formsle app per iOS e Android possono essere compilate in Visual Studio per Mac. Per altre informazioni, vedere [Requisiti macOS](~/cross-platform/get-started/requirements.md#macos-requirements).

> [!NOTE]
> Per lo sviluppo di app con è Xamarin.Forms richiesta una certa familiarità con [.NET standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="additional-platform-support"></a>Supporto di piattaforme aggiuntive

Xamarin.Formssupporta piattaforme aggiuntive oltre a iOS, Android e Windows:

- Samsung Tizen
- macOS 10,13 o versione successiva
- GTK#
- WPF

Lo stato di queste piattaforme è disponibile nel [ Xamarin.Forms wiki del supporto della piattaforma GitHub](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

## <a name="android-platform-support"></a>Supporto della piattaforma Android

È consigliabile installare la versione più recente di Android SDK Tools e della piattaforma API Android. È possibile eseguire l'aggiornamento alle versioni più recenti usando [Android SDK Manager](~/android/get-started/installation/android-sdk.md).

La versione di destinazione/compilazione per i progetti Android **deve** inoltre essere impostata su *Usa la piattaforma installata più recente*. La versione minima può tuttavia essere impostata su API 19 in modo da poter continuare a supportare i dispositivi che usano Android 4.4 e versioni successive. Questi valori vengono impostati in **Opzioni progetto**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

**Opzioni progetto > Applicazione > Proprietà dell'applicazione**

![Opzioni di compilazione Android in Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

**Compila > Generale**

![Selezionare il framework di destinazione più recente](requirements-images/options-general-sml.png)

**Compila > Applicazione Android**

![Selezionare le versioni di Android minima e di destinazione per l'app](requirements-images/options-android-sml.png)

-----

## <a name="deprecated-platforms"></a>Piattaforme deprecate

Queste piattaforme non sono supportate quando si usa Xamarin.Forms 3,0 o versioni successive:

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
