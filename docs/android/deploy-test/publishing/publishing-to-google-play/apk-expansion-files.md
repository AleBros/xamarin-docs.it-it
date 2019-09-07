---
title: File di espansione APK
ms.prod: xamarin
ms.assetid: DB7E38E8-3C4E-5191-27EA-22DE63044FE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: e542336cfd3bf1eac50c343a3edfeb0efa414d0c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753615"
---
# <a name="apk-expansion-files"></a>File di espansione APK

Alcune applicazioni (alcuni giochi, ad esempio) richiedono più risorse e asset di quanto sia possibile fornire entro il limite di dimensioni massime per le app Android imposto da Google Play. Questo limite dipende dalla versione di Android a cui è destinato il pacchetto dell'applicazione Android (APK):

- 100 MB per APK destinati a Android 4.0 o versioni successive (livello API 14 o successivo).
- 50 MB per APK destinati a Android 3.2 o versioni precedenti (livello API 13 o successivo).

Per ovviare a questa limitazione, Google Play ospiterà e distribuirà due *file di espansione* insieme a un file APK, per consentire a un'applicazione di superare questo limite indirettamente. 

Nella maggior parte dei dispositivi, al momento dell'installazione di un'applicazione verranno scaricati i file di espansione insieme all'APK e verranno salvati nello spazio di archiviazione condiviso (scheda SD o partizione montabile su USB) nel dispositivo. In alcuni dispositivi meno recenti, i file di espansione potrebbero non essere installati automaticamente con l'APK. In questi casi, è necessario che l'applicazione contenga codice per il download dei file di espansione alla prima esecuzione da parte dell'utente.

I file di espansione sono considerati come *BLOB binari opachi (OBB, Opaque Binary Blob)* e possono avere dimensioni fino a 2 GB. Android non esegue alcuna elaborazione speciale su questi file dopo il download, quindi i file possono essere in qualsiasi formato appropriato per l'applicazione. L'approccio consigliato per i file di espansione è concettualmente il seguente:

- **Espansione principale**: questo è il file di espansione principale per le risorse e gli asset che non rientrano nei limiti di dimensioni dell'APK. Il file di espansione principale deve contenere gli asset principali richiesti da un'applicazione e dovrebbe essere aggiornato raramente.
- **Espansione patch**: questo file è destinato a piccoli aggiornamenti del file di espansione principale. Questo file non può essere aggiornato. È responsabilità dell'applicazione eseguire eventuali patch o aggiornamenti necessari da questo file.

I file di espansione devono essere caricati insieme all'APK.
Google Play non consente il caricamento di un file di espansione in un APK esistente o per APK esistenti da aggiornare. Se è necessario aggiornare un file di espansione, è necessario caricare un nuovo APK con il valore `versionCode` aggiornato.

## <a name="expansion-file-storage"></a>Archiviazione dei file di espansione

Quando i file vengono scaricati in un dispositivo, verranno archiviati in **_archivio-condiviso_/Android/obb/_nome-pacchetto_** :

- **_archivio-condiviso_** : questa è la directory specificata da `Android.OS.Environment.ExternalStorageDirectory`.
- **_nome-pacchetto_** : questo è il nome del pacchetto in stile Java dell'applicazione.

Dopo il download, i file di espansione non devono essere spostati, modificati, rinominati o eliminati dal relativo percorso nel dispositivo. In caso contrario, i file di espansione verranno scaricati di nuovo e i file precedenti eliminati. La directory dei file di espansione, inoltre, deve contenere solo i file del pacchetto di espansione.

I file di espansione non offrono alcuna misura di sicurezza o protezione per il contenuto, quindi altri utenti o applicazioni possono accedere ai file salvati nello spazio di archiviazione condiviso.

Se è necessario decomprimere un file di espansione, i file decompressi devono essere archiviati in una directory distinta, ad esempio una in `Android.OS.Environment.ExternalStorageDirectory`.

Un'alternativa all'estrazione dei file da un file di espansione è la lettura di asset o risorse direttamente dal file di espansione. Il file di espansione è semplicemente un file ZIP che può essere usato con un `ContentProvider` appropriato. [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) contiene un assembly, [System.IO.Compression.Zip](https://github.com/mattleibow/Android.Play.ExpansionLibrary/tree/master/System.IO.Compression.Zip), che include un `ContentProvider` che consente l'accesso diretto ai file per alcuni file multimediali. Se i file multimediali sono compressi in un file ZIP, le chiamate per la riproduzione di elementi multimediali potrebbero usare direttamente i file nel file ZIP senza doverlo decomprimere. I file multimediali non devono essere compressi quando vengono aggiunti al file ZIP. 

### <a name="filename-format"></a>Formato del nome di file

Al momento del download dei file di espansione, Google Play userà lo schema seguente per l'assegnazione dei nomi:

```
[main|patch].<expansion-version>.<package-name>.obb
```

I tre componenti di questo schema sono:

- `main` o `patch`: specifica se questo è il file di espansione principale o patch. È possibile usare uno solo di questi componenti.
- `<expansion-version>`: intero corrispondente al valore `versionCode` dell'APK a cui è stato inizialmente associato il file.
- `<package-name>`: questo è il nome del pacchetto in stile Java dell'applicazione.

Ad esempio, se la versione dell'APK è 21 e il nome del pacchetto è `mono.samples.helloworld`, il file di espansione principale sarà denominato **main.21.mono.samples.helloworld**.

## <a name="download-process"></a>Processo di download

Quando un'applicazione viene installata da Google Play, i file di espansione devono essere scaricati e salvati insieme all'APK. In alcuni casi ciò potrebbe non verificarsi o può succedere che i file di espansione vengano eliminati. Per gestire questa condizione, un'app deve verificare se i file di espansione esistono e quindi eseguirne il download, se necessario. Il diagramma di flusso seguente mostra il flusso di lavoro consigliato per questo processo:

[![Diagramma di flusso dell'espansione dell'APK](apk-expansion-files-images/apkexpansion.png)](apk-expansion-files-images/apkexpansion.png#lightbox)

All'avvio, un'applicazione deve controllare se i file di espansione appropriati esistono nel dispositivo corrente. Se non sono disponibili, l'applicazione deve effettuare una richiesta dal [servizio di licenza per le applicazioni](https://developer.android.com/google/play/licensing/index.html) di Google Play. Questo controllo viene eseguito tramite la *libreria LVL (License Verification Library)* e deve essere eseguito sia per le applicazioni gratuite che per quelle con licenza. La libreria LVL viene usata principalmente dalle applicazioni a pagamento per applicare restrizioni di licenza. Tuttavia, Google ha esteso la libreria LVL in modo che possa essere usata anche con le librerie di espansione. Anche le applicazioni gratuite devono eseguire il controllo LVL, ma possono ignorare le restrizioni di licenza. La richiesta LVL deve fornire le informazioni seguenti relative ai file di espansione richieste dall'applicazione: 

- **Dimensioni del file**: le dimensioni dei file di espansione vengono usate nell'ambito del controllo che determina se sono già stati scaricati i file di espansione corretti.
- **Nomi di file**: si tratta del nome di file (nel dispositivo corrente) in cui devono essere salvati i pacchetti di espansione.
- **URL per il download**: URL che deve essere usato per scaricare i pacchetti di espansione. Questo URL è univoco per ogni download e ha scadenza a breve.

Dopo aver eseguito il controllo LVL, l'applicazione deve scaricare i file di espansione, prendendo in considerazione i punti seguenti come parte del download:

- Il dispositivo potrebbe non avere spazio sufficiente per archiviare i file di espansione.
- Se non è disponibile una connessione Wi-Fi, l'utente dovrebbe avere la possibilità di sospendere o annullare il download per evitare costi indesiderati.
- I file di espansione vengono scaricati in background per evitare di bloccare le interazioni dell'utente.
- Mentre è in corso il download in background, deve essere visualizzato un indicatore di stato.
- Gli errori che si verificano durante il download devono essere gestiti in modo regolare e ripristinabili.

## <a name="architectural-overview"></a>Panoramica dell'architettura

All'avvio, l'attività principale verifica se i file di espansione sono stati scaricati. In caso affermativo, occorre verificarne la validità.

Se i file di espansione non sono stati scaricati o se i file correnti non sono validi, è necessario scaricare nuovi file di espansione. Un servizio associato viene creato come parte dell'applicazione. All'avvio, l'attività principale dell'applicazione usa il servizio associato per eseguire un controllo nei servizi di gestione delle licenze di Google per individuare i nomi dei file di espansione e l'URL per il download dei file. Il servizio associato scaricherà quindi i file in un thread in background.

Per semplificare le operazioni richieste per l'integrazione dei file di espansione in un'applicazione, Google ha creato varie librerie in Java. Le librerie in questione sono:

- **Downloader Library**: si tratta di una libreria che riduce l'impegno necessario per integrare i file di espansione in un'applicazione. La libreria scarica i file di espansione in un servizio in background, visualizza le notifiche utente, gestisce i problemi di connettività di rete, riprende i download e così via.
- **License Verification Library (LVL)** : libreria per effettuare ed elaborare le chiamate ai servizi di licenza per le applicazioni. Può anche essere usata per eseguire controlli delle licenze, per determinare se l'uso dell'applicazione nel dispositivo è autorizzato.
- **APK Expansion Zip Library (facoltativa)** : se i file di espansione sono inclusi in un file ZIP, questa libreria funge da provider di contenuto e consente a un'applicazione di leggere risorse e asset direttamente dal file ZIP senza doverlo espandere.

Queste librerie sono state convertite per C# e sono disponibili con la licenza di Apache 2.0. Per integrare rapidamente i file di espansione in un'applicazione esistente, è possibile aggiungere queste librerie a un'applicazione Xamarin.Android esistente. Il codice è disponibile in [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) su GitHub.
