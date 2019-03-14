---
title: Caricamento manuale dell'APK
ms.prod: xamarin
ms.assetid: 1309C251-ABF0-4412-B1F5-200DC8321A9D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 76292dac6281837ee48b3bb76a362255108bd62c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667503"
---
# <a name="manually-uploading-the-apk"></a>Caricamento manuale dell'APK


La prima volta che si invia un file APK a Google Play o se si usa una versione precedente di Xamarin.Android), l'APK deve essere caricato manualmente usando [Google Play Developer Console](https://play.google.com/apps/publish). Questa guida illustra i passaggi necessari per questo processo. 


## <a name="google-play-developer-console"></a>Google Play Developer Console

Dopo aver compilato il pacchetto APK e preparato gli asset promozionali, l'applicazione deve essere caricata in Google Play. Questa operazione viene eseguita effettuando l'accesso a [Google Play Developer Console](https://play.google.com/apps/publish), come illustra l'immagine qui accanto. Fare clic sul pulsante **Publish an Android App on Google Play** (Pubblica un'app Android su Google Play) per inizializzare il processo di distribuzione di un'applicazione.

[![Google Play Developer Console](manually-uploading-the-apk-images/00-google-play-developer-console-sml.png)](manually-uploading-the-apk-images/00-google-play-developer-console.png#lightbox)

Se si usa già un'app registrata con Google Play, fare clic sul pulsante **Aggiungi nuova applicazione**:

[![Pulsante di aggiunta di una nuova applicazione](manually-uploading-the-apk-images/01-existing-app-sml.png)](manually-uploading-the-apk-images/01-existing-app.png#lightbox)

Quando viene visualizzata la finestra di dialogo **AGGIUNGI NUOVA APPLICAZIONE**, immettere il nome dell'app e fare clic su **Upload APK** (Carica APK):

[![Pulsante di caricamento APK](manually-uploading-the-apk-images/02-add-new-application-sml.png)](manually-uploading-the-apk-images/02-add-new-application.png#lightbox)

La schermata successiva consente la pubblicazione dell'app per eseguire test della versione alfa o della versione o per la produzione. Nell'esempio seguente è selezionata la scheda **ALPHA TESTING**. Poiché **MyApp** non usa i servizi di gestione delle licenze, il pulsante **Get license key** (Codice di licenza) non deve essere usato per questo esempio. Qui viene scelto il pulsante **Upload your first APK to Alpha** (Carica il primo APK su alfa) per pubblicare sul canale alfa:

[![Pulsante di caricamento del primo APK su alfa](manually-uploading-the-apk-images/03-upload-to-alpha-sml.png)](manually-uploading-the-apk-images/03-upload-to-alpha.png#lightbox)

Viene visualizzata la finestra di dialogo **UPLOAD NEW APK TO ALPHA** (Carica nuovo APK su alpha). Il file APK può essere caricato facendo clic sul pulsante **Browse files** (Sfoglia file) o trascinando e rilasciando l'APK: 

[![Finestra di dialogo di caricamento del nuovo APK su alpha](manually-uploading-the-apk-images/04-upload-dialog-sml.png)](manually-uploading-the-apk-images/04-upload-dialog.png#lightbox)

Assicurarsi di caricare il file APK pronto per la versione che deve essere distribuito.
La finestra di dialogo successiva indica l'avanzamento del caricamento dell'APK:

[![Indicazione dell'avanzamento del caricamento](manually-uploading-the-apk-images/05-upload-progress-sml.png)](manually-uploading-the-apk-images/05-upload-progress.png#lightbox)

Dopo aver caricato l'APK, è possibile selezionare un metodo di test:

[![Finestra di dialogo di selezione del metodo di test](manually-uploading-the-apk-images/06-select-testing-method-sml.png)](manually-uploading-the-apk-images/06-select-testing-method.png#lightbox)

Per altre informazioni sui test dell'app, vedere la guida alla [configurazione dei test alfa/beta](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en) di Google.

Al termine del caricamento l'APK viene salvato come bozza. Non può essere pubblicato finché non si specificano altre informazioni in Google Play come descritto di seguito.


## <a name="store-listing"></a>Presentazione nello store

Fare clic su **Scheda dello store** in **Google Play Developer Console** per immettere le informazioni che verranno visualizzate da Google Play ai potenziali utenti dell'applicazione: 

[![Finestra di dialogo Scheda dello store](manually-uploading-the-apk-images/07-store-listing-sml.png)](manually-uploading-the-apk-images/07-store-listing.png#lightbox)


### <a name="graphics-assets"></a>Risorse grafiche

Scorrere verso il basso fino alla sezione **RISORSE GRAFICHE** della pagina di **presentazione nello store**:

[![Sezione Risorse grafiche](manually-uploading-the-apk-images/08-graphic-assets-sml.png)](manually-uploading-the-apk-images/08-graphic-assets.png#lightbox)

Tutti gli asset promozionali preparati in precedenza vengono caricati in questa sezione. Vengono visualizzate indicazioni relative agli asset che devono essere specificati e al formato in cui devono essere specificati.


### <a name="categorization"></a>Classificazione

Dopo la sezione **RISORSE GRAFICHE** appare la sezione **CLASSIFICAZIONE** in cui si possono selezionare il tipo e la categoria dell'applicazione:

[![Sezione di classificazione](manually-uploading-the-apk-images/09-categorization-sml.png)](manually-uploading-the-apk-images/09-categorization.png#lightbox)

La classificazione del contenuto viene illustrata dopo la sezione successiva.


### <a name="contact-details"></a>Dettagli del contatto

La sezione finale di questa pagina è **INFORMAZIONI DI CONTATTO**. Questa sezione viene usata per raccogliere informazioni di contatto sullo sviluppatore dell'applicazione:

[![Sezione dei dati di contatto](manually-uploading-the-apk-images/10-contact-details-sml.png)](manually-uploading-the-apk-images/10-contact-details.png#lightbox)

È possibile specificare un URL per l'informativa sulla privacy dell'app nella sezione **NORME SULLA PRIVACY**, come indicato in precedenza.


## <a name="content-rating"></a>Classificazione dei contenuti

Fare clic su **Classificazione dei contenuti**  in **Google Play Developer Console**. Specificare in questa pagina la classificazione del contenuto per l'app. Google Play richiede che per tutte le applicazioni venga specificata una classificazione del contenuto. Fare clic sul pulsante **Continua** per completare il questionario sulla classificazione del contenuto:

[![Sezione di classificazione del contenuto](manually-uploading-the-apk-images/11-content-rating-sml.png)](manually-uploading-the-apk-images/11-content-rating.png#lightbox)

Tutte le applicazioni in Google Play devono essere classificate in base al sistema di classificazione di Google Play. Oltre alla classificazione del contenuto, tutte le applicazioni devono essere conformi alle [norme relative al contenuto per gli sviluppatori](http://www.android.com/us/developer-content-policy.html) di Google.

Di seguito sono elencati i quattro livelli del sistema di classificazione di Google Play e sono riportate alcune linee guida relative alle funzionalità o ai contenuti che richiedono o forzano il livello di classificazione: 

-   **Per tutti** &ndash; Non può accedere, pubblicare o condividere i dati sulla località. Non può ospitare contenuti generati dall'utente. Non può consentire la comunicazione tra gli utenti. 

-   **Maturità bassa** &ndash; Applicazioni che accedono ma non condividono dati sulla località. Descrizioni con violenza limitata o cartoonistica. 

-   **Maturità media** &ndash; Riferimenti a droghe, alcol o tabacco. Temi di gioco d'azzardo o gioco d'azzardo simulato. Contenuto istigatorio. Linguaggio o umorismo volgare e offensivo. Riferimenti sessualmente allusivi. 
    Violenza di fantasia. Violenza realistica. Consentire agli utenti di trovarsi tra loro. Consentire agli utenti di comunicare tra loro. 
    Condivisione dei dati sulla località di un utente. 

-   **Maturità alta** &ndash; Particolare attenzione al consumo o vendita di alcol, tabacco o droghe. Particolare attenzione ai riferimenti sessualmente allusivi. Immagini violente. 

Le voci dell'elenco Maturità media sono soggettive, di conseguenza è possibile che una situazione che rientra nella tipologia Maturità media sia tale da poter essere classificata al livello Maturità alta. 


## <a name="pricing-amp-distribution"></a>Prezzi &amp; distribuzione

Fare clic su **Prezzi e distribuzione** in **Google Play Developer Console**. Impostare un prezzo in questa pagina se l'app è a pagamento.
In alternativa, l'applicazione può essere distribuita gratuitamente a tutti gli utenti. Se un'applicazione viene indicata come gratuita, deve rimanere gratuita.
Google Play non consente di trasformare un'applicazione gratuita in un'app a pagamento, tuttavia è possibile vendere contenuto con fatturazione in-app con un'applicazione gratuita. Google Play consente di trasformare un'app a pagamento in un'app gratuita in qualsiasi momento.

Per pubblicare un'app a pagamento è necessario un account commerciante. A tale scopo, **impostare un account commerciante** e seguire le istruzioni.

[![Finestra di dialogo Prezzi e distribuzione](manually-uploading-the-apk-images/12-pricing-sml.png)](manually-uploading-the-apk-images/12-pricing.png#lightbox)


### <a name="manage-countries"></a>Gestire i paesi

La sezione successiva consente di **gestire i paesi**, ovvero stabilire in quali paesi si può distribuire un'app:

[![Finestra di dialogo di gestione dei paesi](manually-uploading-the-apk-images/13-manage-countries-sml.png)](manually-uploading-the-apk-images/13-manage-countries.png#lightbox)


### <a name="other-information"></a>Altre informazioni

Scorrere ancora verso il basso per specificare se l'app contiene annunci. Inoltre, la sezione delle **categorie di dispositivi** contiene opzioni per distribuire facoltativamente l'app per Android Wear, Android TV o Android Auto:

[![Contiene una sezione di annunci](manually-uploading-the-apk-images/14-contains-ads-sml.png)](manually-uploading-the-apk-images/14-contains-ads.png#lightbox)

Dopo questa sezione è possibile selezionare opzioni aggiuntive, ad esempio scegliere **Per la famiglia** e distribuire l'app in Google Play for Education.


### <a name="consent"></a>Consenso

Nella parte inferiore della pagina di **prezzi &amp; distribuzione** è visualizzata la sezione **CONSENSO**.
La sezione è obbligatoria e viene usata per dichiarare che l'applicazione soddisfa le [linee guida di Android per i contenuti](http://www.android.com/market/terms/developer-content-policy.html#hl=us) e riconoscere che l'applicazione è soggetta alle leggi sull'esportazione degli Stati Uniti:

[![Sezione Consenso](manually-uploading-the-apk-images/15-consent-sml.png)](manually-uploading-the-apk-images/15-consent.png#lightbox)

Esistono molti altri argomenti relativi alla pubblicazione di un'app Xamarin.Android che non sono trattati in questa guida.
Per altre informazioni sulla pubblicazione dell'app in Google Play, vedere [Benvenuto nel Centro assistenza di Google Play Console](https://support.google.com/googleplay/android-developer#topic=3450769).



## <a name="google-play-filters"></a>Filtri di Google Play

Quando gli utenti cercano le applicazioni sul sito Web di Google Play sono in grado di cercare tutte le applicazioni pubblicate. Quando gli utenti esplorano Google Play da un dispositivo Android, i risultati sono leggermente diversi. I risultati vengono filtrati in base alla compatibilità con il dispositivo in uso. Ad esempio, se un'applicazione deve inviare messaggi SMS, Google Play non visualizzerà l'applicazione per i dispositivi che non sono in grado di inviare messaggi SMS. I filtri applicati a una ricerca vengono creati in base a quanto segue:

1.  La configurazione hardware del dispositivo.
2.  Le dichiarazioni nel file manifesto delle applicazioni.
3.  Il gestore telefonico usato (se presente).
4.  La posizione del dispositivo.

È possibile aggiungere elementi al manifesto dell'app per controllare il modo in cui l'app viene filtrata in Google Play Store. Di seguito sono riportati gli elementi e gli attributi del manifesto che possono essere usati per filtrare le applicazioni:

-   [supports-screen](https://developer.android.com/guide/topics/manifest/supports-screens-element.html) &ndash; Google Play usa gli attributi per determinare se un'applicazione può essere distribuita a un dispositivo in base alle dimensioni dello schermo. 
    Google Play presuppone che Android sia in grado di adattare un layout più piccolo a schermi più grandi, ma non viceversa. Quindi, un'applicazione che dichiara il supporto per gli schermi normali apparirà nelle ricerche di schermi grandi ma non di schermi piccoli. Se un'applicazione Xamarin.Android non specifica un elemento `<supports-screen>` nel file manifesto, Google Play presuppone che tutti gli attributi abbiano un valore true e che l'applicazione supporti tutte le dimensioni dello schermo. Questo elemento deve essere aggiunto al file **AndroidManifest.xml** manualmente. 

-   [uses-configuration](https://developer.android.com/guide/topics/manifest/uses-configuration-element.html) &ndash; Questo elemento del manifesto viene usato per richiedere determinate funzionalità hardware, ad esempio il tipo di tastiera, i dispositivi di navigazione, un touch-screen e così via. Questo elemento deve essere aggiunto al file **AndroidManifest.xml** manualmente. 

-   [uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) &ndash; Questo elemento del manifesto dichiara le funzionalità hardware o software che un dispositivo deve avere affinché l'applicazione funzioni. Questo attributo è solo informativo. Google Play non visualizzerà l'applicazione nei dispositivi che non soddisfano questo filtro. È comunque possibile scaricare l'applicazione o installarla manualmente. Questo elemento deve essere aggiunto al file **AndroidManifest.xml** manualmente. 

-   [uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) &ndash; Questo elemento specifica che determinate librerie devono essere presenti nel dispositivo, ad esempio Google Maps. Questo elemento può anche essere specificato con `Android.App.UsesLibraryAttribute`. Ad esempio: 

    ```csharp
    [assembly: UsesLibrary("com.google.android.maps", true)]
    ```

-   [uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) &ndash; Questo elemento viene usato per dedurre determinate funzionalità hardware necessarie per l'esecuzione dell'applicazione che non sono state dichiarate correttamente con un elemento `<uses-feature>`. Ad esempio, se un'applicazione richiede l'autorizzazione per usare la fotocamera, Google Play presuppone che i dispositivi devono avere una fotocamera, anche se non è presente un elemento `<uses-feature>` che dichiara la fotocamera. Questo elemento può essere impostato con `Android.App.UsesPermissionsAttribute`. Ad esempio: 

    ```csharp
    [assembly: UsesPermission(Manifest.Permission.Camera)]
    ```

-   [uses-sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) &ndash; L'elemento viene usato per dichiarare il livello minimo dell'API Android richiesto per l'applicazione. Questo elemento può essere impostato nelle opzioni Xamarin.Android di un progetto Xamarin.Android. 

-   [compatible-screens](https://developer.android.com/guide/topics/manifest/compatible-screens-element.html) &ndash; Questo elemento viene usato per filtrare le applicazioni che non corrispondono alle dimensioni dello schermo e alla densità specificate da questo elemento. La maggior parte delle applicazioni non deve usare questo filtro. È destinato a determinati giochi o applicazioni a prestazioni elevate che richiedono controlli severi sulla distribuzione delle applicazioni. Si consiglia di usare l'attributo `<support-screen>` indicato in precedenza. 

-   [supports-gl-texture](https://developer.android.com/guide/topics/manifest/supports-gl-texture-element.html) &ndash; Questo elemento viene usato per dichiarare formazioni di compressione della trama GL richieste dall'applicazione. La maggior parte delle applicazioni non deve usare questo filtro. È destinato a determinati giochi o applicazioni a prestazioni elevate che richiedono controlli severi sulla distribuzione delle applicazioni. 

Per altre informazioni sulla configurazione del manifesto dell'app, vedere l'argomento relativo al [manifesto dell'applicazione](https://developer.android.com/guide/topics/manifest/manifest-intro.html) per Android.
