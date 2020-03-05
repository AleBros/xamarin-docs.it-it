---
title: Caratteri
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/09/2018
ms.openlocfilehash: 3bfa3bbde68fab95d729cc8a558d4eb3baf7b4fa
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "78293073"
---
# <a name="fonts"></a>Caratteri

## <a name="overview"></a>Panoramica

A partire dal livello API 26, il Android SDK consente di trattare i tipi di carattere come risorse, proprio come i layout o drawables. La [libreria di supporto Android 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1) backporting le nuove API dei tipi di carattere alle app che hanno come destinazione il livello API 14 o versione successiva.

Dopo aver indirizzato l'API 26 o aver installato la libreria di supporto Android V26, esistono due modi per usare i tipi di carattere in un'applicazione Android:

1. **Assemblare il tipo di carattere come risorsa Android** &ndash; questo garantisce che il tipo di carattere sia sempre disponibile per l'applicazione, ma aumenterà le dimensioni dell'APK.
2. **Scaricare i tipi** di carattere &ndash; Android supporta anche il download di un tipo di carattere da un _provider di caratteri_. Il provider di tipi di carattere controlla se il tipo di carattere è già presente nel dispositivo. Se necessario, il tipo di carattere verrà scaricato e memorizzato nella cache del dispositivo. Questo tipo di carattere può essere condiviso tra più applicazioni.

I tipi di carattere simili (o un tipo di carattere che può avere stili diversi) possono essere raggruppati in _famiglie di caratteri_. Questo consente agli sviluppatori di specificare determinati attributi del tipo di carattere, ad esempio il peso, e Android seleziona automaticamente il tipo di carattere appropriato dalla famiglia di caratteri.

La libreria di supporto Android V26 backporting il supporto per i tipi di carattere a livello di API 26. Quando si fa riferimento ai livelli API precedenti, è necessario dichiarare lo spazio dei nomi XML `app` e denominare i vari attributi del tipo di carattere usando lo spazio dei nomi `android:` e lo spazio dei nomi `app:`. Se viene usato solo lo spazio dei nomi `android:`, i tipi di carattere non verranno visualizzati nei dispositivi che eseguono il livello API 25 o meno. Questo frammento XML, ad esempio, dichiara una nuova risorsa della [_famiglia di caratteri_](#font_families) che funzionerà a livello API 14 e versioni successive:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

     <font  android:font="@font/sourcesanspro_regular"
            android:fontStyle="normal"
            android:fontWeight="400"
            app:font="@font/sourcesanspro_regular"
            app:fontStyle="normal"
            app:fontWeight="400" />

</font-family>
```

Se i tipi di carattere vengono forniti a un'applicazione Android in modo appropriato, è possibile applicarli a un widget dell'interfaccia utente impostando l' [attributo`fontFamily`](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily). Ad esempio, il frammento di codice seguente illustra come visualizzare un tipo di carattere in un oggetto TextView:

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Questa guida illustra prima di tutto come usare i tipi di carattere come risorsa Android, quindi è possibile passare a esaminare come scaricare i tipi di carattere in fase di esecuzione.

## <a name="fonts-as-a-resource"></a>Tipi di carattere come risorsa

La creazione del pacchetto di un tipo di carattere in un APK Android garantisce che sia sempre disponibile per l'applicazione. Un file del tipo di carattere, ovvero. TTF o. Il file OTF) viene aggiunto a un'applicazione Novell. Android esattamente come qualsiasi altra risorsa, copiando i file in una sottodirectory nella cartella **Resources** di un progetto Novell. Android. Le risorse dei tipi di carattere vengono mantenute in una sottodirectory del **tipo di carattere** della cartella **risorse** del progetto.

> [!NOTE]
> I tipi di carattere devono avere un' **azione di compilazione** di **AndroidResource** o non verranno inclusi nel file apk finale. L'azione di compilazione deve essere impostata automaticamente dall'IDE.

Quando sono presenti molti file del tipo di carattere simili, ad esempio lo stesso tipo di carattere con pesi o stili diversi, è possibile raggrupparli in una famiglia di caratteri.

<a name="font_families" />

### <a name="font-families"></a>Gruppi di caratteri

Una famiglia di caratteri è un set di tipi di carattere con pesi e stili diversi. È ad esempio possibile che siano presenti file di tipi di carattere distinti per i tipi di carattere grassetto o corsivo. La famiglia di caratteri è definita da `font` elementi di un file XML conservati nella directory **Resources/font** . Ogni famiglia di caratteri deve avere il proprio file XML.

Per creare una famiglia di caratteri, aggiungere prima tutti i tipi di carattere alla cartella **Resources/font** . Quindi, creare un nuovo file XML nella cartella font per la famiglia di caratteri. Il nome del file XML non presenta affinità o relazione con i tipi di carattere a cui viene fatto riferimento; il file di risorse può essere qualsiasi nome di file di risorse Android valido. Questo file XML avrà un elemento `font-family` radice che contiene uno o più elementi `font`. Ogni elemento `font` dichiara gli attributi di un tipo di carattere.

Il codice XML seguente è un esempio di una famiglia di caratteri per il tipo di carattere di _origini Sans Pro_ che definisce molti pesi del tipo di carattere diversi. Viene salvato come file nella cartella **Resources/font** denominata **sourcesanspro. XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto">
    <font android:font="@font/sourcesanspro_regular"
          android:fontStyle="normal"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_regular"
          app:fontStyle="normal"
          app:fontWeight="400" />
    <font android:font="@font/sourcesanspro_bold"
          android:fontStyle="normal"
          android:fontWeight="800"
          app:font="@font/sourcesanspro_bold"
          app:fontStyle="normal"
          app:fontWeight="800" />
    <font android:font="@font/sourcesanspro_italic"
          android:fontStyle="italic"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_italic"
          app:fontStyle="italic"
          app:fontWeight="400" />
</font-family>
```

L'attributo `fontStyle` dispone di due valori possibili:

- **normale** &ndash; un tipo di carattere normale
- **corsivo** &ndash; un tipo di carattere corsivo

L'attributo `fontWeight` corrisponde all'attributo `font-weight` CSS e fa riferimento allo spessore del tipo di carattere. Si tratta di un valore compreso nell'intervallo tra 100 e 900. Nell'elenco seguente vengono descritti i valori di peso comune del tipo di carattere e il relativo nome:

- &ndash; **sottile** 100
- **Luce aggiuntiva** &ndash; 200
- **Luce** &ndash; 300
- **Normale** &ndash; 400
- **Media** &ndash; 500
- **Semi-Bold** &ndash; 600
- **Grassetto** &ndash; 700
- **Grassetto aggiuntivo** &ndash; 800
- &ndash; **nero** 900

Una volta definita, una famiglia di caratteri può essere usata in modo dichiarativo impostando gli attributi `fontFamily`, `textStyle`e `fontWeight` nel file di layout.  Il frammento XML seguente, ad esempio, imposta un tipo di carattere 600 peso (normale) e uno stile di testo in corsivo:

```xml
<TextView
    android:text="Sans Source Pro semi-bold italic, 600 weight, italic"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:fontFamily="@font/sourcesanspro"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_horizontal"
    android:fontWeight="600"
    android:textStyle="italic"
    />
```

### <a name="programmatically-assigning-fonts"></a>Assegnazione di tipi di carattere a livello di codice

I tipi di carattere possono essere impostati a livello di codice usando il metodo [`Resources.GetFont`](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int)) per recuperare un oggetto [`Typeface`](https://developer.android.com/reference/android/graphics/Typeface.html) . Molte visualizzazioni hanno una proprietà `TypeFace` che può essere usata per assegnare il tipo di carattere al widget. Questo frammento di codice Mostra come impostare il tipo di carattere a livello di codice su un oggetto TextView:

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

Il metodo `GetFont` caricherà automaticamente il primo carattere all'interno di una famiglia di caratteri.  Per caricare un tipo di carattere corrispondente a uno stile specifico, usare il metodo `Typeface.Create`. Questo metodo tenterà di caricare un tipo di carattere corrispondente allo stile specificato. Questo frammento di codice, ad esempio, tenterà di caricare un oggetto `Typeface` in grassetto da una famiglia di caratteri definita in **risorse/tipi di carattere**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>Download di tipi di carattere

Anziché confezionare i tipi di carattere come risorsa dell'applicazione, Android può scaricare i tipi di carattere da un'origine remota. Questo avrà l'effetto desiderato per ridurre le dimensioni dell'APK.

I tipi di carattere vengono scaricati con l'assistenza di un _provider di tipi di carattere_. Si tratta di un provider di contenuti specializzato che gestisce il download e la memorizzazione nella cache dei tipi di carattere per tutte le applicazioni nel dispositivo. Android 8,0 include un provider di tipi di carattere per scaricare i tipi di carattere dal [repository Google Font](https://fonts.google.com). Il provider di tipi di carattere predefinito viene sottoportato al livello API 14 con la libreria di supporto Android V26.

Quando un'applicazione esegue una richiesta per un tipo di carattere, il provider del tipo di carattere controlla prima di tutto se il tipo di carattere è già presente nel dispositivo. In caso contrario, tenterà di scaricare il tipo di carattere. Se non è possibile scaricare il tipo di carattere, Android userà il tipo di carattere di sistema predefinito. Una volta scaricato, il tipo di carattere è disponibile per tutte le applicazioni nel dispositivo e non solo per l'app che ha effettuato la richiesta iniziale.

Quando viene effettuata una richiesta di download di un tipo di carattere, l'app non esegue direttamente una query sul provider del tipo di carattere. Al contrario, le app utilizzeranno un'istanza dell'API [`FontsContract`](https://developer.android.com/reference/android/provider/FontsContract.html) (oppure il [`FontsContractCompat`](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) se viene utilizzata la libreria di supporto 26).  

Android 8,0 supporta il download di tipi di carattere in due modi diversi:

1. **Dichiarare i tipi di carattere scaricabili come risorsa** &ndash; un'app può dichiarare i tipi di carattere scaricabili in Android tramite file di risorse XML. Questi file conterranno tutti i metadati che Android deve scaricare in modo asincrono quando l'app viene avviata e memorizzata nella cache nel dispositivo.
2. &ndash; API a livello di **codice** in Android API level 26 consentono a un'applicazione di scaricare i tipi di carattere a livello di codice, mentre l'applicazione è in esecuzione. Le app creeranno un oggetto `FontRequest` per un tipo di carattere specificato e passano questo oggetto alla classe `FontsContract`. Il `FontsContract` accetta il `FontRequest` e recupera il tipo di carattere da un _provider di tipi di carattere_. Android scaricherà il tipo di carattere in modo sincrono. Un esempio di creazione di una `FontRequest` verrà illustrato più avanti in questa guida.

Indipendentemente dall'approccio usato, i file di risorse che devono essere aggiunti all'applicazione Novell. Android prima di poter scaricare i tipi di carattere. In primo luogo, i tipi di carattere devono essere dichiarati in un file XML nella directory **Resources/font** come parte di una famiglia di caratteri. Questo frammento di codice è un esempio di come scaricare i tipi di carattere dalla [raccolta di Google Fonts Open Source](https://fonts.google.com) usando il provider di tipi di carattere predefinito disponibile in Android 8,0 (o la libreria di supporto V26):

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto"
             android:fontProviderAuthority="com.google.android.gms.fonts"
             android:fontProviderPackage="com.google.android.gms"
             android:fontProviderQuery="VT323"
             android:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
             app:fontProviderAuthority="com.google.android.gms.fonts"
             app:fontProviderPackage="com.google.android.gms"
             app:fontProviderQuery="VT323"
             app:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
>
</font-family>
```

L'elemento `font-family` contiene gli attributi seguenti, che dichiarano le informazioni richieste da Android per scaricare i tipi di carattere:

1. **fontProviderAuthority** &ndash; l'autorità del provider di tipi di carattere da utilizzare per la richiesta.
2. **fontPackage** &ndash; il pacchetto per il provider di tipi di carattere da utilizzare per la richiesta. Viene utilizzato per verificare l'identità del provider.
3. **fontQuery** &ndash; si tratta di una stringa che consente al provider di tipi di carattere di individuare il tipo di carattere richiesto. I dettagli sulla query del tipo di carattere sono specifici del provider di tipi di carattere. La classe [`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) nell'app di esempio relativa ai [tipi di carattere scaricabili](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) fornisce alcune informazioni sul formato di query per i tipi di carattere della raccolta Open Source di Google Fonts.
4. **fontProviderCerts** &ndash; una matrice di risorse con l'elenco di set di hash per i certificati con cui il provider deve essere firmato.

Una volta definiti i tipi di carattere, potrebbe essere necessario fornire informazioni sui _certificati del tipo di carattere_ interessati dal download.

### <a name="font-certificates"></a>Certificati del tipo di carattere

Se il provider di tipi di carattere non è preinstallato nel dispositivo o se l'app usa la libreria `Xamarin.Android.Support.Compat`, Android richiede i certificati di sicurezza del provider di tipi di carattere. Questi certificati verranno elencati in un file di risorse array mantenuto nella directory **Resources/values** .

Il codice XML seguente, ad esempio, è denominato **Resources/values/fonts_cert. XML** e archivia i certificati per il provider di tipi di carattere Google:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="com_google_android_gms_fonts_certs">
        <item>@array/com_google_android_gms_fonts_certs_dev</item>
        <item>@array/com_google_android_gms_fonts_certs_prod</item>
    </array>
    <string-array name="com_google_android_gms_fonts_certs_dev">
        <item>
            MIIEqDCCA5CgAwIBAgIJANWFuGx90071MA0GCSqGSIb3DQEBBAUAMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTAeFw0wODA0MTUyMzM2NTZaFw0zNTA5MDEyMzM2NTZaMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBANbOLggKv+IxTdGNs8/TGFy0PTP6DHThvbbR24kT9ixcOd9W+EaBPWW+wPPKQmsHxajtWjmQwWfna8mZuSeJS48LIgAZlKkpFeVyxW0qMBujb8X8ETrWy550NaFtI6t9+u7hZeTfHwqNvacKhp1RbE6dBRGWynwMVX8XW8N1+UjFaq6GCJukT4qmpN2afb8sCjUigq0GuMwYXrFVee74bQgLHWGJwPmvmLHC69EH6kWr22ijx4OKXlSIx2xT1AsSHee70w5iDBiK4aph27yH3TxkXy9V89TDdexAcKk/cVHYNnDBapcavl7y0RiQ4biu8ymM8Ga/nmzhRKya6G0cGw8CAQOjgfwwgfkwHQYDVR0OBBYEFI0cxb6VTEM8YYY6FbBMvAPyT+CyMIHJBgNVHSMEgcEwgb6AFI0cxb6VTEM8YYY6FbBMvAPyT+CyoYGapIGXMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbYIJANWFuGx90071MAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEEBQADggEBABnTDPEF+3iSP0wNfdIjIz1AlnrPzgAIHVvXxunW7SBrDhEglQZBbKJEk5kT0mtKoOD1JMrSu1xuTKEBahWRbqHsXclaXjoBADb0kkjVEJu/Lh5hgYZnOjvlba8Ld7HCKePCVePoTJBdI4fvugnL8TsgK05aIskyY0hKI9L8KfqfGTl1lzOv2KoWD0KWwtAWPoGChZxmQ+nBli+gwYMzM1vAkP+aayLe0a1EQimlOalO762r0GXO0ks+UeXde2Z4e+8S/pf7pITEI/tP+MxJTALw9QUWEv9lKTk+jkbqxbsh8nfBUapfKqYn0eidpwq2AzVp3juYl7//fKnaPhJD9gs=
        </item>
    </string-array>
    <string-array name="com_google_android_gms_fonts_certs_prod">
        <item>
            MIIEQzCCAyugAwIBAgIJAMLgh0ZkSjCNMA0GCSqGSIb3DQEBBAUAMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDAeFw0wODA4MjEyMzEzMzRaFw0zNjAxMDcyMzEzMzRaMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBAKtWLgDYO6IIrgqWbxJOKdoR8qtW0I9Y4sypEwPpt1TTcvZApxsdyxMJZ2JORland2qSGT2y5b+3JKkedxiLDmpHpDsz2WCbdxgxRczfey5YZnTJ4VZbH0xqWVW/8lGmPav5xVwnIiJS6HXk+BVKZF+JcWjAsb/GEuq/eFdpuzSqeYTcfi6idkyugwfYwXFU1+5fZKUaRKYCwkkFQVfcAs1fXA5V+++FGfvjJ/CxURaSxaBvGdGDhfXE28LWuT9ozCl5xw4Yq5OGazvV24mZVSoOO0yZ31j7kYvtwYK6NeADwbSxDdJEqO4k//0zOHKrUiGYXtqw/A0LFFtqoZKFjnkCAQOjgdkwgdYwHQYDVR0OBBYEFMd9jMIhF1Ylmn/Tgt9r45jk14alMIGmBgNVHSMEgZ4wgZuAFMd9jMIhF1Ylmn/Tgt9r45jk14aloXikdjB0MQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEUMBIGA1UEChMLR29vZ2xlIEluYy4xEDAOBgNVBAsTB0FuZHJvaWQxEDAOBgNVBAMTB0FuZHJvaWSCCQDC4IdGZEowjTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBBAUAA4IBAQBt0lLO74UwLDYKqs6Tm8/yzKkEu116FmH4rkaymUIE0P9KaMftGlMexFlaYjzmB2OxZyl6euNXEsQH8gjwyxCUKRJNexBiGcCEyj6z+a1fuHHvkiaai+KL8W1EyNmgjmyy8AW7P+LLlkR+ho5zEHatRbM/YAnqGcFh5iZBqpknHf1SKMXFh4dd239FJ1jWYfbMDMy3NS5CTMQ2XFI1MvcyUTdZPErjQfTbQe3aDQsQcafEQPD+nqActifKZ0Np0IS9L9kR/wbNvyz6ENwPiTrjV2KRkEjH78ZMcUQXg0L3BYHJ3lc69Vs5Ddf9uUGGMYldX3WfMBEmh/9iFBDAaTCK
        </item>
    </string-array>
</resources>
```

Con questi file di risorse, l'app è in grado di scaricare i tipi di carattere.

### <a name="declaring-downloadable-fonts-as-resources"></a>Dichiarazione di tipi di carattere scaricabili come risorse

Elencando i tipi di carattere scaricabili in **file AndroidManifest. XML**, i tipi di carattere vengono scaricati in modo asincrono quando l'app viene avviata per la prima volta. I tipi di carattere stessi sono elencati in un file di risorse di matrice, simile a quello riportato di seguito:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

Per scaricare questi tipi di carattere, è necessario dichiararli in **file AndroidManifest. XML** aggiungendo `meta-data` come figlio dell'elemento `application`. Se, ad esempio, i tipi di carattere scaricabili sono dichiarati in un file di risorse in **Resources/values/downloadable_fonts. XML**, il frammento di codice deve essere aggiunto al manifesto:

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>Download di un tipo di carattere con le API font

È possibile scaricare un tipo di carattere a livello di codice creando un'istanza di un oggetto [`FontRequest`](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html) e passandolo al metodo `FontContractCompat.RequestFont`. Il metodo `FontContractCompat.RequestFont` verificherà prima di tutto se il tipo di carattere è presente nel dispositivo e quindi, se necessario, eseguirà una query in modo asincrono sul provider di tipi di carattere e tenterà di scaricare il tipo di carattere per l'app. Se `FontRequest` non è in grado di scaricare il tipo di carattere, Android userà il tipo di carattere di sistema predefinito.

Un oggetto `FontRequest` contiene informazioni che verranno utilizzate dal provider di tipi di carattere per individuare e scaricare un tipo di carattere. Un `FontRequest` richiede quattro tipi di informazioni:

1. **Autorità del provider di tipi di carattere** &ndash; autorità del provider di tipi di carattere da utilizzare per la richiesta.
2. **Pacchetto di tipi di carattere** &ndash; pacchetto per il provider di tipi di carattere da utilizzare per la richiesta. Viene utilizzato per verificare l'identità del provider.
3. **Query dei tipi di carattere** &ndash; si tratta di una stringa che consente al provider di tipi di carattere di individuare il tipo di carattere richiesto. I dettagli sulla query del tipo di carattere sono specifici del provider di tipi di carattere. I dettagli della stringa sono specifici del provider di tipi di carattere. La classe [`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) nell'app di esempio relativa ai [tipi di carattere scaricabili](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) fornisce alcune informazioni sul formato di query per i tipi di carattere della raccolta Open Source di Google Fonts.
4. I **certificati del provider di tipi di carattere** &ndash; una matrice di risorse con l'elenco di set di hash per i certificati con cui deve essere firmato il provider.

Questo frammento di codice è un esempio di creazione di un'istanza di un nuovo oggetto `FontRequest`:

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

Nel frammento di codice precedente `FontToDownload` è una query che aiuterà il tipo di carattere della raccolta Open Source di Google Fonts.

Prima di passare il `FontRequest` al metodo `FontContractCompat.RequestFont`, è necessario creare due oggetti:

- **`FontsContractCompat.FontRequestCallback`** &ndash; si tratta di una classe astratta che deve essere estesa. Si tratta di un callback che verrà richiamato al termine dell'`RequestFont`. Un'app Novell. Android deve sottoclassare `FontsContractCompat.FontRequestCallback` ed eseguire l'override della `OnTypefaceRequestFailed` e `OnTypefaceRetrieved`, fornendo le azioni da intraprendere quando il download ha esito negativo o riesce rispettivamente.
- **`Handler`** &ndash; si tratta di un `Handler` che verrà usato da `RequestFont` per scaricare il tipo di carattere in un thread, se necessario. I tipi di carattere **non** devono essere scaricati nel thread UI.

Questo frammento di codice è un C# esempio di una classe che Scarica in modo asincrono un tipo di carattere dalla raccolta Open Source di Google Fonts. Implementa l'interfaccia `FontRequestCallback` e genera un C# evento al termine dell'`FontRequest`.

```csharp
public class FontDownloadHelper : FontsContractCompat.FontRequestCallback
{
    // A very simple font query; replace as necessary
    public static readonly String FontToDownload = "Courgette";

    Android.OS.Handler Handler = null;

    public event EventHandler<FontDownloadEventArg> FontDownloaded = delegate
    {
        // just an empty delegate to avoid null reference exceptions.  
    };

    public void DownloadFonts(Context context)
    {
        FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms",FontToDownload , Resource.Array.com_google_android_gms_fonts_certs);
        FontsContractCompat.RequestFont(context, request, this, GetHandlerThreadHandler());
    }

    public override void OnTypefaceRequestFailed(int reason)
    {
        base.OnTypefaceRequestFailed(reason);
        FontDownloaded(this, new FontDownloadEventArg(null));
    }

    public override void OnTypefaceRetrieved(Android.Graphics.Typeface typeface)
    {
        base.OnTypefaceRetrieved(typeface);
        FontDownloaded(this, new FontDownloadEventArg(typeface));
    }

    Handler GetHandlerThreadHandler()
    {
        if (Handler == null)
        {
            HandlerThread handlerThread = new HandlerThread("fonts");
            handlerThread.Start();
            Handler = new Handler(handlerThread.Looper);
        }
        return Handler;
    }
}

public class FontDownloadEventArg : EventArgs
{
    public FontDownloadEventArg(Android.Graphics.Typeface typeface)
    {
        Typeface = typeface;
    }
    public Android.Graphics.Typeface Typeface { get; private set; }
    public bool RequestFailed
    {
        get
        {
            return Typeface != null;
        }
    }
}
```

Per usare questo helper, viene creato un nuovo `FontDownloadHelper` e viene assegnato un gestore eventi:  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) =>
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato le nuove API in Android 8,0 per supportare i tipi di carattere e i tipi di carattere scaricabili come risorse. È stato illustrato come incorporare i tipi di carattere esistenti in un APK e usarli in un layout. Viene anche illustrato come Android 8,0 supporta il download di tipi di carattere da un provider di tipi di carattere, a livello di codice o tramite la dichiarazione dei metadati dei tipi di carattere nei file di risorse.

## <a name="related-links"></a>Collegamenti correlati

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources. GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Carattere tipografico](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Libreria di supporto Android 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [Uso di tipi di carattere in Android](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [Specifica di spessore carattere CSS](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Raccolta Open Source di Google Fonts](https://fonts.google.com/)
- [San di origine Pro](https://fonts.google.com/specimen/Source+Sans+Pro)
