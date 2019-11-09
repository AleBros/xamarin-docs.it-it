---
title: Perché la finestra di progettazione XAML di Visual Studio non funziona per i file XAML di Xamarin.Forms?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: ce318faab1af07b95a7769d81a506979b37a34e4
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842993"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>Perché la finestra di progettazione XAML di Visual Studio non funziona per i file XAML di Xamarin.Forms?

Novell. Forms attualmente non supporta le finestre di progettazione visiva per i file XAML. Per questo motivo, quando si tenta di aprire un file XAML di form nella finestra di *progettazione* dell'interfaccia utente XAML di Visual Studio o nella *finestra di progettazione dell'interfaccia utente XAML con codifica*, viene generato il messaggio di errore seguente:

> "Impossibile aprire il file con l'editor selezionato. Scegliere un altro editor. "

Questa limitazione è descritta nella Guida alle [nozioni di base XAML di Novell. Forms](~/xamarin-forms/xaml/xaml-basics/index.md) :

> "Non esiste ancora una finestra di progettazione visiva per la generazione di XAML nelle applicazioni Novell. Forms, quindi tutto il codice XAML deve essere scritto manualmente".

È tuttavia possibile visualizzare il Visualizzatore anteprima Novell. Forms XAML selezionando l'opzione di menu **visualizza > altre finestre > Novell. Forms** del Visualizzatore anteprime.
