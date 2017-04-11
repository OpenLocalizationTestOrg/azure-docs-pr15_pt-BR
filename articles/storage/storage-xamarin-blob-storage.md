<properties
    pageTitle="Como usar o armazenamento de Blob do Xamarin | Microsoft Azure"
    description="A biblioteca de cliente de armazenamento do Azure para Xamarin permite que os desenvolvedores criem iOS, Android e Windows Store apps com suas interfaces do usuário nativo. Este tutorial mostra como usar Xamarin para criar um aplicativo que usa o armazenamento de Blob do Azure."
    services="storage"
    documentationCenter="xamarin"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-xamarin"></a>Como usar o armazenamento de Blob do Xamarin

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>Visão geral

Xamarin permite que os desenvolvedores usam um compartilhado c# base de código para criar o iOS, Android e Windows Store apps com suas interfaces do usuário nativo. Este tutorial mostra como usar o armazenamento de Blob do Azure com um aplicativo de Xamarin. Se você gostaria de saber mais sobre o armazenamento do Azure, antes de mergulhar no código, consulte [Introdução ao armazenamento do Microsoft Azure](storage-introduction.md).

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Criar um novo aplicativo de Xamarin

Para este guia de Introdução, podemos criará um aplicativo que os destinos iOS, Android e Windows. Este aplicativo simplesmente irá criar um contêiner e carregue um blob para este contêiner. Usaremos Visual Studio no Windows para este guia de Introdução, mas as mesmas lições podem ser aplicadas ao criar um aplicativo usando o Xamarin Studio no Mac OS.

Siga estas etapas para criar seu aplicativo:

1. Se você ainda não fez isso, baixe e instale o [Xamarin para Visual Studio](https://www.xamarin.com/download).
2. Abra o Visual Studio e criar um aplicativo em branco (compartilhada nativo): **arquivo > Novo > projeto > entre plataformas > App(Native Shared) em branco**.
3. Clique com botão direito sua solução no painel Solution Explorer e selecione **Gerenciar pacotes NuGet para solução**. Pesquise **WindowsAzure.Storage** e instale a última versão estável a todos os projetos em sua solução.
4. Criar e executar o seu projeto.

Agora você deve ter um aplicativo que permite que você clicar em um botão que um contador é incrementado.

> [AZURE.NOTE] A biblioteca de cliente de armazenamento do Azure para Xamarin atualmente suporta os seguintes tipos de projeto: compartilhados nativo, Xamarin.Forms compartilhado, Xamarin.Android e Xamarin.iOS.

## <a name="create-container-and-upload-blob"></a>Criar contêiner e carregue blob

Em seguida, você adicionará alguns códigos para a classe compartilhada `MyClass.cs` que cria um contêiner e carrega um blob para este contêiner. `MyClass.cs`deve parecer com o seguinte:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;

    namespace XamarinApp
    {
        public class MyClass
        {
            public MyClass ()
            {
            }

            public static async Task createContainerAndUpload()
            {
                // Retrieve storage account from connection string.
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

                // Create the blob client.
                CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

                // Retrieve reference to a previously created container.
                CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

                // Create the container if it doesn't already exist.
                await container.CreateIfNotExistsAsync();

                // Retrieve reference to a blob named "myblob".
                CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

                // Create the "myblob" blob with the text "Hello, world!"
                await blockBlob.UploadTextAsync("Hello, world!");
            }
        }
    }

Certifique-se de substituir "your_account_name_here" e "your_account_key_here" com seu nome de conta real e chave da conta. Você pode usar essa classe compartilhado no seu iOS, Android e Windows Phone aplicativo. Você pode simplesmente adicionar `MyClass.createContainerAndUpload()` para cada projeto. Por exemplo:

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

    using Android.App;
    using Android.Widget;
    using Android.OS;

    namespace XamarinApp.Droid
    {
        [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
        public class MainActivity : Activity
        {
            int count = 1;

            protected override async void OnCreate (Bundle bundle)
            {
                base.OnCreate (bundle);

                // Set our view from the "main" layout resource
                SetContentView (Resource.Layout.Main);

                // Get our button from the layout resource,
                // and attach an event to it
                Button button = FindViewById<Button> (Resource.Id.myButton);

                button.Click += delegate {
                    button.Text = string.Format ("{0} clicks!", count++);
                };

              await MyClass.createContainerAndUpload();
            }
        }
    }

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

    using System;
    using UIKit;

    namespace XamarinApp.iOS
    {
        public partial class ViewController : UIViewController
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.createContainerAndUpload();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage > MainPage.xaml.cs

    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Navigation;

    // The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

    namespace XamarinApp.WinPhone
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.createContainerAndUpload();
            }
        }
    }


## <a name="run-the-application"></a>Executar o aplicativo

Agora você pode executar esse aplicativo em um emulador Android ou Windows Phone. Você também pode executar esse aplicativo em um emulador iOS, mas isso exigirá um Mac. Para obter instruções específicas sobre como fazer isso, leia a documentação de [conexão Visual Studio para um Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Depois que você executar o aplicativo, ele criará o contêiner `mycontainer` em sua conta de armazenamento. Ele deve conter o blob, `myblob`, que possui o texto, `Hello, world!`. Você pode verificar isso usando o [Gerenciador de armazenamento do Microsoft Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Próximas etapas

Este guia de Introdução, você aprendeu como criar um aplicativo entre plataformas no Xamarin que usa o armazenamento do Azure. Este guia de Introdução especificamente voltada para um cenário no armazenamento de Blob. No entanto, você pode fazer muito mais com, não apenas o armazenamento de Blob, mas também com a tabela, arquivo e armazenamento de fila. Confira os artigos a seguir para saber mais:
- [Introdução ao armazenamento de Blob do Azure usando .NET](storage-dotnet-how-to-use-blobs.md)
- [Introdução ao armazenamento de tabela do Azure usando .NET](storage-dotnet-how-to-use-tables.md)
- [Introdução ao armazenamento de fila do Azure usando o .NET](storage-dotnet-how-to-use-queues.md)
- [Introdução ao armazenamento de arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]
