---
title: Come funzionano i provider di contenuti
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e61be6f0189eb825c15fd75764a16706e588ebc9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020513"
---
# <a name="how-content-providers-work"></a>Come funzionano i provider di contenuti

Esistono due classi che interessano l'interazione `ContentProvider`:

- **ContentProvider** &ndash; implementa un'API che espone un set di dati in modo standard. I metodi principali sono query, INSERT, Update e DELETE.

- **ContentResolver** &ndash; un proxy statico che comunica con un `ContentProvider` per accedere ai dati, dall'interno della stessa applicazione o da un'altra applicazione.

Un provider di contenuti è in genere supportato da un database SQLite, ma l'API significa che l'utilizzo di codice non richiede informazioni sull'SQL sottostante. Le query vengono eseguite tramite un URI usando costanti per fare riferimento ai nomi delle colonne (per ridurre le dipendenze dalla struttura di dati sottostante) e viene restituito un `ICursor` per eseguire l'iterazione del codice.

## <a name="consuming-a-contentprovider"></a>Utilizzo di un ContentProvider

`ContentProviders` esporre le funzionalità tramite un URI registrato in **file AndroidManifest. XML** dell'applicazione che pubblica i dati. Esiste una convenzione in cui l'URI e le colonne di dati esposte devono essere disponibili come costanti per semplificare l'associazione ai dati. Le `ContentProviders` predefinite di Android forniscono classi di praticità con costanti che fanno riferimento alla struttura dei dati nello spazio dei nomi [`Android.Providers`](xref:Android.Provider) .

### <a name="built-in-providers"></a>Provider predefiniti

Android consente di accedere a un'ampia gamma di dati di sistema e utente usando `ContentProviders`:

- *Browser* &ndash; segnalibri e cronologia del browser (richiede l'autorizzazione `READ_HISTORY_BOOKMARKS` e/o `WRITE_HISTORY_BOOKMARKS`).

- *CallLog* &ndash; le chiamate recenti effettuate o ricevute con il dispositivo.

- *Contatti* &ndash; informazioni dettagliate dall'elenco dei contatti dell'utente, tra cui persone, telefoni, foto & gruppi.

- *MediaStore* &ndash; contenuto del dispositivo dell'utente: audio (album, artisti, generi, playlist), immagini (incluse le anteprime) & video.

- *Impostazioni* &ndash; impostazioni e preferenze del dispositivo a livello di sistema.

- *UserDictionary* &ndash; contenuto del dizionario definito dall'utente utilizzato per l'input di testo predittivo.

- *Segreteria vocale* &ndash; cronologia dei messaggi della segreteria telefonica.

## <a name="classes-overview"></a>Cenni preliminari sulle classi

Di seguito sono illustrate le classi primarie usate quando si usa un `ContentProvider`:

[![diagramma classi dell'applicazione del provider di contenuti e utilizzo delle interazioni con le applicazioni](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

In questo diagramma, il `ContentProvider` implementa le query e registra gli URI utilizzati da altre applicazioni per individuare i dati. Il `ContentResolver` funge da "proxy" per la `ContentProvider` (metodi di query, inserimento, aggiornamento ed eliminazione). Il `SQLiteOpenHelper` contiene i dati utilizzati dall'`ContentProvider`, ma non è esposto direttamente alle applicazioni che utilizzano.
Il `CursorAdapter` passa il cursore restituito dall'`ContentResolver` da visualizzare in un `ListView`. Il `UriMatcher` è una classe helper che analizza gli URI durante l'elaborazione delle query.

Lo scopo di ogni classe è descritto di seguito:

- **ContentProvider** &ndash; implementano i metodi della classe astratta per esporre i dati. L'API viene resa disponibile ad altre classi e applicazioni tramite l'attributo URI aggiunto alla definizione della classe.

- **SQLiteOpenHelper** &ndash; consente di implementare l'archivio dati SQLite esposto dall'`ContentProvider`.

- **UriMatcher** &ndash; usare `UriMatcher` nell'implementazione di `ContentProvider` per gestire gli URI usati per eseguire query sul contenuto.

- **ContentResolver** &ndash; utilizzo del codice utilizza un `ContentResolver` per accedere a un'istanza di `ContentProvider`. Le due classi si occupano insieme dei problemi di comunicazione tra processi, consentendo la condivisione semplice dei dati tra le applicazioni. L'utilizzo del codice non crea mai una classe `ContentProvider` in modo esplicito; al contrario, è possibile accedere ai dati creando un cursore in base a un URI esposto dall'applicazione `ContentProvider`.

- **CursorAdapter** &ndash; usare `CursorAdapter` o `SimpleCursorAdapter` per visualizzare i dati a cui si accede tramite un `ContentProvider`.

L'API `ContentProvider` consente agli utenti di eseguire una serie di operazioni sui dati, ad esempio:

- Eseguire query sui dati per restituire elenchi o singoli record.
- Modificare i singoli record.
- Aggiungere nuovi record.
- Eliminare i record.

Questo documento contiene un esempio che usa un `ContentProvider`fornito dal sistema, nonché un semplice esempio di sola lettura che implementa un `ContentProvider`personalizzato.
