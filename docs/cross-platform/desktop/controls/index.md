---
ms.assetid: 4D47185C-8998-4903-AE64-7E2A67F9DF7A
title: Confronto tra i controlli dell'interfaccia utente
description: Questo documento fornisce un confronto dei controlli dell'interfaccia utente tra xamarin. Forms, Windows Form e WPF. Anche possibile collegare ad altra documentazione che confronta WPF a xamarin. Forms.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 8103bcbf9ab5e6158bf100c181c541215fbb76c9
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650126"
---
# <a name="ui-controls-comparison"></a>Confronto tra i controlli dell'interfaccia utente

Di seguito è riportato il confronto dei controlli di xamarin. Forms con Windows Form e WPF, in base [questa tabella](/dotnet/framework/wpf/advanced/windows-forms-controls-and-equivalent-wpf-controls).

Altre informazioni, vedere la [analogie e differenze tra WPF e xamarin. Forms](wpf.md) per aggiornare le proprie conoscenze del desktop per lo sviluppo di app per dispositivi mobili.

|Windows Form|WPF|Xamarin.Forms|
|--- |--- |--- |
|[BindingNavigator](https://msdn.microsoft.com/library/system.windows.forms.bindingnavigator(v=vs.110).aspx)|-|-|
|[BindingSource](https://msdn.microsoft.com/library/system.windows.forms.bindingsource(v=vs.110).aspx)|[CollectionViewSource](https://msdn.microsoft.com/library/system.windows.data.collectionviewsource(v=vs.110).aspx)|Associazione di proprietà, ad es. BindingContext|
|[Pulsante](https://msdn.microsoft.com/library/system.windows.forms.button(v=vs.110).aspx)|[Pulsante](https://msdn.microsoft.com/library/system.windows.controls.button(v=vs.110).aspx)|Button|
|[CheckBox](https://msdn.microsoft.com/library/system.windows.forms.checkbox(v=vs.110).aspx)|[CheckBox](https://msdn.microsoft.com/library/system.windows.controls.checkbox(v=vs.110).aspx)|Opzione|
|[CheckedListBox](https://msdn.microsoft.com/library/system.windows.forms.checkedlistbox(v=vs.110).aspx)|[Casella di riepilogo](https://msdn.microsoft.com/library/system.windows.controls.listbox(v=vs.110).aspx) con composizione.|ListView con composizione.|
|[ColorDialog](https://msdn.microsoft.com/library/system.windows.forms.colordialog(v=vs.110).aspx)|-|-|
|[ComboBox](https://msdn.microsoft.com/library/system.windows.forms.combobox(v=vs.110).aspx)|[Casella combinata](https://msdn.microsoft.com/library/system.windows.controls.combobox(v=vs.110).aspx) (non supporta il completamento automatico)|Selezione|
|[ContextMenuStrip](https://msdn.microsoft.com/library/system.windows.forms.contextmenustrip(v=vs.110).aspx)|[ContextMenu](https://msdn.microsoft.com/library/system.windows.controls.contextmenu(v=vs.110).aspx)|-|
|[DataGridView](https://msdn.microsoft.com/library/system.windows.forms.datagridview(v=vs.110).aspx)|[DataGrid](https://msdn.microsoft.com/library/system.windows.controls.datagrid(v=vs.110).aspx)|-|
|[DateTimePicker](https://msdn.microsoft.com/library/system.windows.forms.datetimepicker(v=vs.110).aspx)|[DatePicker](https://msdn.microsoft.com/library/system.windows.controls.datepicker(v=vs.110).aspx)|DatePicker & TimePicker|
|[DomainUpDown](https://msdn.microsoft.com/library/system.windows.forms.domainupdown(v=vs.110).aspx)|[Nella casella di testo](https://msdn.microsoft.com/library/system.windows.controls.textbox(v=vs.110).aspx) e due [RepeatButton](https://msdn.microsoft.com/library/system.windows.controls.primitives.repeatbutton(v=vs.110).aspx) controlli.|Gestore di istruzioni|
|[ErrorProvider](https://msdn.microsoft.com/library/system.windows.forms.errorprovider(v=vs.110).aspx)|-|-|
|[FlowLayoutPanel](https://msdn.microsoft.com/library/system.windows.forms.flowlayoutpanel(v=vs.110).aspx)|[WrapPanel](https://msdn.microsoft.com/library/system.windows.controls.wrappanel(v=vs.110).aspx) o [StackPanel](https://msdn.microsoft.com/library/system.windows.controls.stackpanel(v=vs.110).aspx)|StackLayout o FlexLayout|
|[FolderBrowserDialog](https://msdn.microsoft.com/library/system.windows.forms.folderbrowserdialog(v=vs.110).aspx)|-|-|
|[FontDialog](https://msdn.microsoft.com/library/system.windows.forms.fontdialog(v=vs.110).aspx)|-|-|
|[Modulo](https://msdn.microsoft.com/library/system.windows.forms.form(v=vs.110).aspx)|[Finestra](https://msdn.microsoft.com/library/system.windows.window(v=vs.110).aspx)|Pagina|
|[GroupBox](https://msdn.microsoft.com/library/system.windows.forms.groupbox(v=vs.110).aspx)|[GroupBox](https://msdn.microsoft.com/library/system.windows.controls.groupbox(v=vs.110).aspx)|-|
|[HelpProvider](https://msdn.microsoft.com/library/system.windows.forms.helpprovider(v=vs.110).aspx)|Nessun controllo equivalente (utilizzare le descrizioni comandi).|-|
|[HScrollBar](https://msdn.microsoft.com/library/system.windows.forms.hscrollbar(v=vs.110).aspx)|[Barra di scorrimento](https://msdn.microsoft.com/library/system.windows.controls.primitives.scrollbar(v=vs.110).aspx) (scorrevole è integrato nei controlli contenitore)|usare ScrollView|
|[ImageList](https://msdn.microsoft.com/library/system.windows.forms.imagelist(v=vs.110).aspx)|-|-|
|[Etichetta](https://msdn.microsoft.com/library/system.windows.forms.label(v=vs.110).aspx)|[Etichetta](https://msdn.microsoft.com/library/system.windows.controls.label(v=vs.110).aspx)|Label|
|[LinkLabel](https://msdn.microsoft.com/library/system.windows.forms.linklabel(v=vs.110).aspx)|Nessun controllo equivalente (è possibile usare la [Hyperlink](https://msdn.microsoft.com/library/system.windows.documents.hyperlink(v=vs.110).aspx) classe ospitare collegamenti ipertestuali all'interno del contenuto di flusso).|-|
|[ListBox](https://msdn.microsoft.com/library/system.windows.forms.listbox(v=vs.110).aspx)|[ListBox](https://msdn.microsoft.com/library/system.windows.controls.listbox(v=vs.110).aspx)|Usare ListView|
|[ListView](https://msdn.microsoft.com/library/system.windows.forms.listview(v=vs.110).aspx)|[ListView](https://msdn.microsoft.com/library/system.windows.controls.listview(v=vs.110).aspx)|ListView|
|[MaskedTextBox](https://msdn.microsoft.com/library/system.windows.forms.maskedtextbox(v=vs.110).aspx)|-|-|
|[MenuStrip](https://msdn.microsoft.com/library/system.windows.forms.menustrip(v=vs.110).aspx)|[Menu](https://msdn.microsoft.com/library/system.windows.controls.menu(v=vs.110).aspx)|Prendere in considerazione MasterDetailPage o TabbedPage|
|[MonthCalendar](https://msdn.microsoft.com/library/system.windows.forms.monthcalendar(v=vs.110).aspx)|[Calendario](https://msdn.microsoft.com/library/system.windows.controls.calendar(v=vs.110).aspx)|-|
|[NotifyIcon](https://msdn.microsoft.com/library/system.windows.forms.notifyicon(v=vs.110).aspx)|-|-|
|[NumericUpDown](https://msdn.microsoft.com/library/system.windows.forms.numericupdown(v=vs.110).aspx)|[Nella casella di testo](https://msdn.microsoft.com/library/system.windows.controls.textbox(v=vs.110).aspx) e due [RepeatButton](https://msdn.microsoft.com/library/system.windows.controls.primitives.repeatbutton(v=vs.110).aspx) controlli.|Gestore di istruzioni|
|[OpenFileDialog](https://msdn.microsoft.com/library/system.windows.forms.openfiledialog(v=vs.110).aspx)|[OpenFileDialog](https://msdn.microsoft.com/library/microsoft.win32.openfiledialog(v=vs.110).aspx)|-|
|[PageSetupDialog](https://msdn.microsoft.com/library/system.windows.forms.pagesetupdialog(v=vs.110).aspx)|-|-|
|[Pannello](https://msdn.microsoft.com/library/system.windows.forms.panel(v=vs.110).aspx)|[Canvas](https://msdn.microsoft.com/library/system.windows.controls.canvas(v=vs.110).aspx)|AbsoluteLayout o una vista|
|[PictureBox](https://msdn.microsoft.com/library/system.windows.forms.picturebox(v=vs.110).aspx)|[Immagine](https://msdn.microsoft.com/library/system.windows.controls.image(v=vs.110).aspx)|Image|
|[PrintDialog](https://msdn.microsoft.com/library/system.windows.forms.printdialog(v=vs.110).aspx)|[PrintDialog](https://msdn.microsoft.com/library/system.windows.controls.printdialog(v=vs.110).aspx)|-|
|[PrintDocument](https://msdn.microsoft.com/library/system.drawing.printing.printdocument(v=vs.110).aspx)|-|-|
|[PrintPreviewControl](https://msdn.microsoft.com/library/system.windows.forms.printpreviewcontrol(v=vs.110).aspx)|[DocumentViewer](https://msdn.microsoft.com/library/system.windows.controls.documentviewer(v=vs.110).aspx)|-|
|[PrintPreviewDialog](https://msdn.microsoft.com/library/system.windows.forms.printpreviewdialog(v=vs.110).aspx)|-|-|
|[ProgressBar](https://msdn.microsoft.com/library/system.windows.forms.progressbar(v=vs.110).aspx)|[ProgressBar](https://msdn.microsoft.com/library/system.windows.controls.progressbar(v=vs.110).aspx)|ProgressBar|
|[PropertyGrid](https://msdn.microsoft.com/library/system.windows.forms.propertygrid(v=vs.110).aspx)|-|-|
|[RadioButton](https://msdn.microsoft.com/library/system.windows.forms.radiobutton(v=vs.110).aspx)|[RadioButton](https://msdn.microsoft.com/library/system.windows.controls.radiobutton(v=vs.110).aspx)|-|
|[RichTextBox](https://msdn.microsoft.com/library/system.windows.forms.richtextbox(v=vs.110).aspx)|[RichTextBox](https://msdn.microsoft.com/library/system.windows.controls.richtextbox(v=vs.110).aspx)|Editor non supporta testo RTF (formattato), voce di testo a riga singola|
|[SaveFileDialog](https://msdn.microsoft.com/library/system.windows.forms.savefiledialog(v=vs.110).aspx)|[SaveFileDialog](https://msdn.microsoft.com/library/microsoft.win32.savefiledialog(v=vs.110).aspx)|-|
|[ScrollableControl](https://msdn.microsoft.com/library/system.windows.forms.scrollablecontrol(v=vs.110).aspx)|[ScrollViewer](https://msdn.microsoft.com/library/system.windows.controls.scrollviewer(v=vs.110).aspx)|ScrollView|
|[SoundPlayer](https://msdn.microsoft.com/library/system.media.soundplayer(v=vs.110).aspx)|[MediaPlayer](https://msdn.microsoft.com/library/system.windows.media.mediaplayer(v=vs.110).aspx)|-|
|[SplitContainer](https://msdn.microsoft.com/library/system.windows.forms.splitcontainer(v=vs.110).aspx)|[GridSplitter](https://msdn.microsoft.com/library/system.windows.controls.gridsplitter(v=vs.110).aspx)|Prendere in considerazione MasterDetailPage|
|[StatusStrip](https://msdn.microsoft.com/library/system.windows.forms.statusstrip(v=vs.110).aspx)|[StatusBar](https://msdn.microsoft.com/library/system.windows.controls.primitives.statusbar(v=vs.110).aspx)|-|
|[TabControl](https://msdn.microsoft.com/library/system.windows.forms.tabcontrol(v=vs.110).aspx)|[TabControl](https://msdn.microsoft.com/library/system.windows.controls.tabcontrol(v=vs.110).aspx)|TabbedPage|
|[TableLayoutPanel](https://msdn.microsoft.com/library/system.windows.forms.tablelayoutpanel(v=vs.110).aspx)|[Griglia](https://msdn.microsoft.com/library/system.windows.controls.grid(v=vs.110).aspx)|Grid|
|[TextBox](https://msdn.microsoft.com/library/system.windows.forms.textbox(v=vs.110).aspx)|[TextBox](https://msdn.microsoft.com/library/system.windows.controls.textbox(v=vs.110).aspx)|Editor non supporta testo RTF (formattato)|
|[Timer](https://msdn.microsoft.com/library/system.windows.forms.timer(v=vs.110).aspx)|[DispatcherTimer](https://msdn.microsoft.com/library/system.windows.threading.dispatchertimer(v=vs.110).aspx)|Device.StartTime()|
|[ToolStrip](https://msdn.microsoft.com/library/system.windows.forms.toolstrip(v=vs.110).aspx)|[ToolBar](https://msdn.microsoft.com/library/system.windows.controls.toolbar(v=vs.110).aspx)|Page.ToolbarItems e ToolbarItem|
|[ToolStripContainer](https://msdn.microsoft.com/library/system.windows.forms.toolstripcontainer(v=vs.110).aspx), [ToolStripDropDown](https://msdn.microsoft.com/library/system.windows.forms.toolstripdropdown(v=vs.110).aspx), [ToolStripDropDownMenu](https://msdn.microsoft.com/library/system.windows.forms.toolstripdropdownmenu(v=vs.110).aspx), [ToolStripPanel](https://msdn.microsoft.com/library/system.windows.forms.toolstrippanel(v=vs.110).aspx)|[Sulla barra degli strumenti](https://msdn.microsoft.com/library/system.windows.controls.toolbar(v=vs.110).aspx) con composizione.|Page.ToolbarItems e ToolbarItem con composizione|
|[ToolTip](https://msdn.microsoft.com/library/system.windows.forms.tooltip(v=vs.110).aspx)|[ToolTip](https://msdn.microsoft.com/library/system.windows.controls.tooltip(v=vs.110).aspx)|Usare le funzionalità di accessibilità|
|[TrackBar](https://msdn.microsoft.com/library/system.windows.forms.trackbar(v=vs.110).aspx)|[Dispositivo di scorrimento](https://msdn.microsoft.com/library/system.windows.controls.slider(v=vs.110).aspx)|Slider|
|[TreeView](https://msdn.microsoft.com/library/system.windows.forms.treeview(v=vs.110).aspx)|[TreeView](https://msdn.microsoft.com/library/system.windows.controls.treeview(v=vs.110).aspx)|Prendere in considerazione ListView gerarchici in una NavigationPage|
|[UserControl](https://msdn.microsoft.com/library/system.windows.forms.usercontrol(v=vs.110).aspx)|[UserControl](https://msdn.microsoft.com/library/system.windows.controls.usercontrol(v=vs.110).aspx)|Visualizzazione e anche renderer personalizzati|
|[VScrollBar](https://msdn.microsoft.com/library/system.windows.forms.vscrollbar(v=vs.110).aspx)|[Barra di scorrimento](https://msdn.microsoft.com/library/system.windows.controls.primitives.scrollbar(v=vs.110).aspx)|usare ScrollView|
|[WebBrowser](https://msdn.microsoft.com/library/system.windows.forms.webbrowser(v=vs.110).aspx)|[WebBrowser](https://msdn.microsoft.com/library/system.windows.controls.webbrowser(v=vs.110).aspx)|WebView|
