---
title: Configurazione di app Mac
description: Questo documento descrive come configurare un'app Xamarin.Mac per la pubblicazione. Vengono illustrate le impostazioni dell'applicazione e della firma, oltre a quelle di compilazione.
ms.prod: xamarin
ms.assetid: fea66a34-1581-4cd6-b714-3fbff215a542
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: f008ac42bfffeda2a47ca30aa2991d91f990732f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725038"
---
# <a name="mac-app-configuration"></a>Configurazione di app Mac

## <a name="mac-app-configuration"></a>Configurazione di app Mac

Fare clic con il pulsante destro del mouse sul progetto dell'applicazione Mac in Visual Studio per Mac e scegliere **Opzioni**.

### <a name="application-settings"></a>Impostazioni dell'applicazione

Per modificare le impostazioni di un'applicazione Xamarin.Mac, fare doppio clic sul file **Info.plist** nel **riquadro della soluzione**:

![Selezione del file info.plist](app-configuration-images/config04.png "Selezione del file info.plist")

In questo modo verranno visualizzate le opzioni disponibili per l'app:

 [![Modifica del file info.plist](app-configuration-images/config01.png "Modifica del file info.plist")](app-configuration-images/config01-large.png#lightbox)

Per eseguire le applicazioni Mac create con Xamarin.Mac sono necessari i requisiti di sistema seguenti:

- Computer Mac che esegue Mac OS X 10.7 o versione successiva.

### <a name="signing-settings"></a>Impostazioni della firma

La sezione **Firma Mac** della finestra di dialogo **Opzioni progetto** consente allo sviluppatore di firmare un'app Xamarin.Mac per il test, per l'autorilascio o per il rilascio tramite l'App Store di Apple:

[![Editor della firma Mac](app-configuration-images/config02.png "Editor della firma Mac")](app-configuration-images/config02-large.png#lightbox)

Da qui selezionare Identità, Profilo di provisioning e tutti i diritti personalizzati usati per firmare l'app quando viene compilata. Lo sviluppatore può facoltativamente firmare il programma di installazione usato per installare l'app in un altro Mac.

### <a name="build-settings"></a>Impostazioni di compilazione

La sezione **Build di Mac** della finestra di dialogo Opzioni **progetto** consente allo sviluppatore di selezionare l'architettura per un'app Xamarin.Mac, di controllare quale versione di macOS supporterà l'app e di creare facoltativamente un pacchetto di installazione quando l'app viene compilata correttamente:

 [![Modifica delle impostazioni di compilazione](app-configuration-images/config03.png "Modifica delle impostazioni di compilazione")](app-configuration-images/config03-large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [Installazione](/visualstudio/mac/installation/)
- [Esempio Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuire le app in Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID sviluppatore e GateKeeper](https://developer.apple.com/developer-id/)
