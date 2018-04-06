---
title: Pubblicazione indipendente
ms.prod: xamarin
ms.assetid: 6FB4DEF2-01AD-C5FE-0950-CE1BF088A9C6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/21/2017
ms.openlocfilehash: 6d278f3ae046a31be6e4335119572fb509672a66
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="publishing-independently"></a>Pubblicazione indipendente

È possibile pubblicare un'applicazione senza usare alcuno dei marketplace esistenti per Android. In questa sezione verranno descritti questi altri metodi di pubblicazione e i livelli di licenza di Xamarin.Android.


## <a name="xamarin-licensing"></a>Gestione delle licenze Xamarin

Sono disponibili quattro licenze per lo sviluppo, l'implementazione e la distribuzione di app Xamarin.Android:

-   **Visual Studio Community** &ndash; Per studenti, piccoli team e sviluppatori di sistemi operativi che usano Windows.

-   **Visual Studio Professional** &ndash; Per singoli sviluppatori o piccoli team (solo Windows). Questa licenza offre una licenza standard o cloud, l'accesso a contenuto aggiuntivo Xamarin University e non prevede restrizioni di utilizzo.

-   **Visual Studio Enterprise** &ndash; Per team di qualsiasi dimensione (solo Windows). La licenza include funzionalità Enterprise, una sottoscrizione standard o cloud e uno sconto del 25% per Xamarin Test Cloud.

Visitare il sito [visualstudio.com](https://www.visualstudio.com/xamarin/) per scaricare l'edizione Community o per altre informazioni sull'acquisto delle edizioni Professional ed Enterprise.


## <a name="allow-installation-from-unknown-sources"></a>Consentire l'installazione da origini sconosciute

Per impostazione predefinita, Android impedisce agli utenti di scaricare e installare applicazioni da posizioni diverse da Google Play. Per consentire l'installazione da origini non marketplace, è necessario abilitare l'impostazione *Origini sconosciute* in un dispositivo prima di installare un'applicazione. Questa opzione è disponibile in **Impostazioni > Sicurezza**, come illustrato nello screenshot seguente:

[![Schermata delle impostazioni Sicurezza](publishing-independently-images/settings.png)](publishing-independently-images/settings.png#lightbox)


> [!IMPORTANT]
> Alcuni provider di rete potrebbero impedire l'installazione di applicazioni da origini sconosciute, indipendentemente da questa impostazione.



## <a name="publishing-by-e-mail"></a>Pubblicazione tramite posta elettronica

Allegare la versione finale dell'APK a un messaggio di posta elettronica è un modo semplice e rapido per distribuire un'applicazione agli utenti. Quando l'utente apre il messaggio di posta elettronica in un dispositivo Android, il sistema riconoscerà l'APK allegato e visualizzerà un pulsante **Installa** come illustrato nella figura seguente:

[![Pulsante Installa per l'allegato](publishing-independently-images/publishing-via-email.png)](publishing-independently-images/publishing-via-email.png#lightbox)

Anche se la distribuzione tramite posta elettronica è semplice, offre poche protezioni da atti di pirateria informatica o dalla distribuzione non autorizzata. È consigliabile riservarla ai casi in cui i destinatari dell'applicazione sono pochi e ci si può fidare che non distribuiscano l'applicazione.


## <a name="publishing-by-web"></a>Pubblicazione tramite Web

È possibile distribuire un'applicazione da un server Web. Per eseguire questa operazione è sufficiente caricare l'applicazione nel server Web e quindi fornire un collegamento di download agli utenti. Quando un dispositivo Android visita un collegamento e quindi scarica l'applicazione, tale applicazione verrà installata automaticamente al termine del download.


## <a name="manually-installing-an-apk"></a>Installazione manuale di un pacchetto APK

L'installazione manuale è una terza opzione per l'installazione di applicazioni. Per eseguire l'installazione manuale di un'applicazione:

1.   **Distribuire una copia dell'APK all'utente** &ndash; Ad esempio, distribuire la copia tramite un CD o un'unità flash USB.
1.   **L'utente installa l'applicazione in un dispositivo Android** &ndash; Usare lo strumento da riga di comando *Android Debug Bridge* (**adb**). **adb** è uno strumento da riga di comando versatile che consente la comunicazione con un'istanza dell'emulatore o con un dispositivo Android. Android SDK include **adb** nella directory **<sdk>/platform-tools /**.

Il dispositivo Android deve essere collegato con un cavo USB al computer.
I computer Windows potrebbero anche richiedere ulteriori driver USB dal fornitore del telefono per essere riconosciuti da **adb**. Le istruzioni per l'installazione di questi driver USB aggiuntivi esulano dagli scopi di questo documento.

Prima di eseguire qualsiasi comando **adb**, è utile sapere quali istanza dell'emulatore o dispositivi sono eventualmente connessi. È possibile visualizzare un elenco dei dispositivi connessi tramite il comando `devices`, come illustrato nel frammento seguente:

```shell
$ adb devices
List of devices attached
        0149B2EC03012005device
```

Dopo aver verificato i dispositivi connessi, l'applicazione può essere installata eseguendo il comando `install` con **adb**:

```shell
$ adb install <path-to-apk>
```

Il frammento di codice seguente mostra un esempio di installazione di un'applicazione in un dispositivo connesso:

```shell
$ adb install helloworld.apk
3772 KB/s (3013594 bytes in 0.780s)
        pkg: /data/local/tmp/helloworld.apk
Success
```

Se l'applicazione è già installata, il comando `adb install` non potrà installare l'APK e segnalerà un errore, come illustrato nell'esempio seguente:

```shell
$ adb install helloworld.apk
4037 KB/s (3013594 bytes in 0.728s)
        pkg: /data/local/tmp/helloworld.apk
Failure [INSTALL_FAILED_ALREADY_EXISTS]
```

Sarà necessario disinstallare l'applicazione dal dispositivo. Eseguire prima di tutto il comando `adb uninstall`:

```shell
adb uninstall <package_name>
```

Il frammento seguente è un esempio di disinstallazione di un'applicazione:

```shell
$ adb uninstall mono.samples.helloworld
Success
```
