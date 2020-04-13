---
title: Analizzatori di diagnostica di layout AndroidAndroid Layout Diagnostics Analyzers
description: Questa guida elenca tutti gli analizzatori diagnostici del layout Android attualmente supportati in Xamarin.Android.This guide lists all of the currently supported Android layout diagnostic analyzers on Xamarin.Android.
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 04/07/2020
ms.openlocfilehash: 6203ce444bb97fa453a912a724f7f5724558b32a
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987606"
---
# <a name="android-designer-diagnostic-analyzers"></a>Analizzatori diagnostici di progettazione Android

Questa guida elenca tutti gli analizzatori diagnostici del layout Android attualmente supportati.

## <a name="accessibility"></a>Accessibilità

Gli analizzatori seguenti consentono di migliorare il supporto dell'accessibilità:The following analyzers help to improve accessibility support:

| ID | Titolo | Gravità | Descrizione |
|----|-------|----------|-------------|
| ContentDescription | Immagine senza`contentDescription` | Avviso | Attributo mancante nell'immagine `contentDescription` |

## <a name="correctness"></a>Correttezza

Gli analizzatori seguenti consentono di risolvere i problemi di correttezza in un layout:The following analyzers help fix correctness issues in a layout:

| ID | Titolo | Gravità | Descrizione | Guida |
|----|-------|----------|-------------|------|
| AdattatorViewChildren | AdapterView con elementi figlio | Avviso | AdapterViews cannot have children in XML | [Collegamento](xref:Android.Widget.AdapterView) |
| MissingId MissingId (ID missing) | I frammenti `id` devono specificare un`tag` | Avviso |Questo `<fragment>` tag deve `id` specificare un o un `tag` oggetto per mantenere lo stato tra i riavvii dell'attivitàThis tag should specify a or a to preserve state across activity restarts | [Collegamento](xref:Android.App.Fragment) |
| NestedScrollingVertical | Elementi di scorrimento verticale nidificati | Avviso | Widget a scorrimento nidificati |
| NestedScrollingHorizontal | Elementi scorrevoli orizzontali nidificati | Avviso | Widget a scorrimento nidificati |
| Ridimensiona per ScrollView | ScrollVisualizza gli elementi figlio con dimensioni fill_parent/match_parent errate | Avviso | ScrollVisualizza gli elementi figlio con dimensioni fill_parent/match_parent errate |
| ScrollViewCount | ScrollViews può avere un solo figlio | Avviso | Una visualizzazione a scorrimento può avere un solo figlio |
| MissingAndroidNamespace | Spazio dei nomi Android mancante nell'attributo | Errore | Spazio dei nomi XML Android mancante; l'attributo verrà interpretato come un attributo personalizzato |
| DuplicateIDs | ID duplicati | Errore | ID duplicati all'interno di un singolo layout |
| IncludeLayoutParamsMissingWidthAndHeight | Mancano sia la larghezza che l'altezza | Errore | Parametri di layout ignorati su include | [Collegamento](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| IncludeLayoutParamsMissingWidth | Larghezza mancante | Errore | Parametri di layout ignorati su include | [Collegamento](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| IncludeLayoutParamsMissingHeight | Altezza mancante | Errore | Parametri di layout ignorati su include | [Collegamento](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| Orientamento | Orientamento esplicito mancante | Errore | Orientamento esplicito mancante |
| Sospetta0dp | Dimensione 0dp sospetta | Errore | Dimensione 0dp sospetta |
| RequiredSizeWidth | Attributo larghezza mancante | Errore | Attributo mancante: layout_width |
| RequiredSizeHeight | Attributo altezza mancante | Errore | Attributo mancante: layout_height |
| Layout WebViewLayout | Visualizzazioni Web in wrap_content elementi padre | Errore |
| Caso sbagliato | Caso errato per il tag di visualizzazione | Errore | Caso errato per il tag di visualizzazione | [Collegamento](xref:Android.App.Fragment) |

## <a name="design"></a>Progettazione

Gli analizzatori seguenti consentono di migliorare la modalità di join dei file di layout:The following analyzers help to improve how you join layout files:

| ID | Titolo | Gravità | Descrizione |
|----|-------|----------|-------------|
| HardcodedColor (Colore hardcoded) | Colore hardcoded | Info | Il colore hardcoded spesso porta a incoerenze |
| HardcodedSize  | Dimensione hardcoded  | Info | La dimensione hardcoded spesso porta a incoerenze  |
| HardcodedText (Testo hardcoded)  | Testo hardcoded  | Avviso | Testo hardcoded |
| Risorsa non risoltoUnresolvedResource | URL risorsa non risolto | Avviso | Impossibile risolvere l'URL della risorsa |
| XmlErrors (Errori Xml) | Errore di sintassi XML | Errore | Errore di sintassi XML |

## <a name="performance"></a>Prestazioni

Gli analizzatori seguenti consentono di migliorare le prestazioni del layout:

| ID | Titolo | Gravità | Descrizione |
|----|-------|----------|-------------|
| Pesi annidati | Pesi di layout nidificati | Avviso | Pesi nidificati sono cattivi per le prestazioni |
| TooManyViews | Il layout ha troppe visualizzazioni | Avviso | Il layout ha troppe visualizzazioni |
| TooDeepLayout | La gerarchia dei layout è troppo profonda | Avviso | La gerarchia dei layout è troppo profonda |
| InutilePadre | Layout padre inutile | Avviso | Layout padre inutile |
| Inutile | Layout foglia inutile | Avviso | Questa `%1$s` vista è inutile `background`(nessun `id`bambino, nessun, nessun, no , no `style`) |

## <a name="usability"></a>Usabilità

Gli analizzatori seguenti consentono di migliorare l'usabilità del layout per i clienti:The following analyzers help improve layout usability for your customers:

| ID | Titolo | Gravità | Descrizione |
|----|-------|----------|-------------|
| Margine Negativo | Margini negativi | Avviso | Margini negativi |
| MancanteTipoInput | EditText senza inputType | Avviso | Nessun tipo di input specificato |
| InputTypePhone | EditText sembra essere un numero di telefono | Avviso | Il nome della vista suggerisce che si `phone` tratta di un numero di telefono, ma non include`inputType` |
| InputTypeNumber | EditText sembra essere un numero | Avviso | Il nome della vista suggerisce che si tratta `inputType` di `numberDecimal`un numero, ma non include un numero (ad esempio ) |
| InputTypePassword | EditText sembra essere una password | Avviso | Il nome della `password` `inputType` vista suggerisce che si tratta di `textVisiblePassword`una password, ma non include nel (ad esempio ) |
| InputTypePIN (InputTypePIN) | EditText sembra essere un PIN | Avviso | Il nome della vista suggerisce che si tratta `numberPassword` di una password (PIN), ma non include`inputType` |
| InputTypeEmail (Messaggio E-mail di InputType | EditText sembra essere un'e-mail | Avviso | Il nome della vista suggerisce che si tratta `email` di `inputType` un indirizzo `textEmailAddress`di posta elettronica, ma non include nel (ad esempio ) |
| InputTypeURI | EditText sembra essere un URI | Avviso | Il nome della vista suggerisce che si `textUri` tratta di un URI, ma non include`inputType` |
| InputTypeDate | EditText sembra essere una data | Avviso | Il nome della `date` `inputType` vista suggerisce che si tratta di `datetime`una data, ma non include nel (ad esempio ) |
