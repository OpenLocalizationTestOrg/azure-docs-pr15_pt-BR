<properties
    pageTitle="Azure B2C diretório ativo: Chamar uma web API de um aplicativo do Android | Microsoft Azure"
    description="Este artigo mostrará como criar um dispositivo Android 'lista de tarefas pendentes' aplicativo que chama um Node web API usando tokens de portador OAuth 2.0. O aplicativo Android e web API usam Azure Active Directory B2C para gerenciar identidades de usuário e autenticar usuários."
    services="active-directory-b2c"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>B2C do Azure AD: Chamar uma web API de um aplicativo do Android

> [AZURE.WARNING] Este tutorial requer algumas atualizações importantes, especificamente para remover o uso de ADAL Android para B2C.  Vamos publicar novas instruções para o uso do Azure AD B2C nos aplicativos Android na próxima semana, e recomendamos adiando até esse momento.  Mas se você quiser experimentar coisas, fique à vontade para continuar com o artigo a seguir.



Usando B2C Azure Active Directory (AD Azure), você pode adicionar recursos de gerenciamento de identidade de autoatendimento poderosos para aplicativos Android e web APIs em algumas etapas curtas. Este artigo mostrará como criar um dispositivo Android "lista de tarefas pendentes" aplicativo que chama um Node web API usando tokens de portador OAuth 2.0. O aplicativo Android e web API usam Azure AD B2C para gerenciar identidades de usuário e autenticar usuários.

Neste início rápido requer que você tenha um web API que é protegido por Azure AD com B2C para funcionar totalmente. Criamos uma para .NET e Node para uso. Este passo a passo pressupõe que o exemplo de web API Node está configurado. Para obter mais informações, consulte [Azure AD B2C web API para Node tutorial](active-directory-b2c-devquickstarts-api-node.md).

Para clientes Android que precisam acessar recursos protegidos, Azure AD fornece a biblioteca de autenticação do Active Directory (ADAL). A única finalidade de ADAL é mais fácil para o aplicativo obter tokens de acesso. Para demonstrar como é fácil, neste guia será criamos um aplicativo de lista de tarefas pendentes Android que:

- Obtém os tokens de acesso que uma lista de tarefas pendentes API de chamadas usando o [protocolo de autenticação de OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
- Obtém listas de tarefas pendentes dos usuários.
- Sinais de usuários.

> [AZURE.NOTE] Este artigo não aborda como implementar entrar, inscrição e gerenciamento de perfil usando o Azure AD B2C. Ele aborda como chamar web APIs depois que o usuário é autenticado. Se você ainda não começou, você deve começar com o [.NET web app tutorial de Introdução](active-directory-b2c-devquickstarts-web-dotnet.md) aprender as Noções básicas do Azure AD B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório de Azure AD B2C

Antes de poder usar Azure AD B2C, você deve criar um diretório ou de locatários. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e muito mais. Se você não tenha um, [Crie um diretório de B2C](active-directory-b2c-get-started.md) antes de continuar neste guia.

## <a name="create-an-application"></a>Criar um aplicativo

Em seguida, você precisa criar um aplicativo no seu diretório B2C. Isso fornece informações de Azure AD que ele precisa se comunicar com segurança com seu aplicativo. O aplicativo e web API são representados por um único **ID de aplicativo** nesse caso, pois elas compõem um aplicativo lógico. Para criar um aplicativo, siga [estas instruções](active-directory-b2c-app-registration.md). Certifique-se de:

- Incluir um **aplicativo web**/**API da web** no aplicativo.
- Insira `urn:ietf:wg:oauth:2.0:oob` como uma **URL de resposta**. É a URL padrão para este exemplo de código.
- Crie um **segredo do aplicativo** para o seu aplicativo e copiá-lo. Você precisará dele mais tarde. Observe que este valor deve ser [precedido de XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de você usá-lo.
- Copie a **ID do aplicativo** que está atribuído a seu aplicativo. Você também precisará isso mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas regras

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este aplicativo contém três experiências de identidade: inscrever-se, entrar e entrar usando o Facebook.  Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando você cria sua três diretivas, certifique-se a:

- Escolha o **nome de exibição** e outros atributos de inscrição na sua política de inscrição.
- Escolha as declarações de aplicativo **nome para exibição** e a **ID de objeto** em cada política. Você pode escolher outras reivindicações também.
- Copie o **nome** de cada política depois de criá-lo. Ele deve ter o prefixo `b2c_1_`.  Posteriormente, você precisará esses nomes de política.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, você está pronto para criar seu aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre como as políticas funcionam no Azure AD B2C, comece com o [.NET web app tutorial de Introdução](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Baixe o código

O código para este tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Para criar a amostra enquanto você trabalha, você pode [baixar o projeto reduzido como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **É necessário baixar o esqueleto para concluir este tutorial.** Devido a complexidade da implementação de um aplicativo do Android totalmente funcional, a estrutura tem código UX que será executado depois de concluir este tutorial. Esta é uma medida para economizar tempo para desenvolvedores. O código UX não é germane para o tópico de como adicionar B2C para um aplicativo do Android.

O aplicativo concluído também está [disponível como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) ou na `complete` ramificação do mesmo repositório.

Para criar com Maven, você pode usar `pom.xml` no nível superior.

  1. Siga as etapas na [seção pré-requisitos para configurar Maven para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  2. Configure um emulator com SDK 21.
  3. Vá para a pasta raiz onde você clonar o repo.
  4. Execute o comando `mvn clean install`.
  5. Altere o diretório para a amostra de início rápido `cd samples\hello`.
  6. Execute o comando `mvn android:deploy android:run`.

Você deve ver o aplicativo do início. Insira as credenciais de usuário de teste para experimentar.

Pacotes de arquivamento de Java (JAR) também serão enviados ao lado do pacote de arquivo morto Android (AAR).

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>Baixe o ADAL Android e adicioná-lo ao seu espaço de trabalho do Android Studio

Você tem opções sobre como usar essa biblioteca em seu projeto Android:

* Você pode usar o código-fonte para importar a biblioteca para Eclipse e o link para o seu aplicativo.
* Se você usar Studio Android, você pode usar o formato de pacote AAR e referenciar os binários.

### <a name="option-1-binaries-via-gradle-recommended"></a>Opção 1: Binários via Gradle (recomendado)

Você pode acessar os binários do repo central Maven. O pacote de AAR pode ser incluído em seu projeto no Android Studio (por exemplo, em `build.gradle`) desta forma:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### <a name="option-2-aar-via-maven"></a>Opção 2: AAR via Maven

Se você usar o `m2e` plug-in Eclipse, você pode especificar a dependência no seu `pom.xml` arquivo:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>Opção 3: Fonte via gito (últimos reclassificar)

Para obter o código-fonte do SDK via gito, insira:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Use a ramificação **convergência.**

## <a name="set-up-your-configuration-file"></a>Configurar o seu arquivo de configuração

Usar a configuração que você configurar anteriores no portal de B2C para configurar o projeto Android.

Abrir `helpes/Constants.java` e preencher os valores para o seguinte:

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
- `SCOPES`: Os escopos que você passa para o servidor que você deseja solicitar do servidor quando um usuário entra. Para visualização B2C, você passar `client_id`. No entanto, isso é esperado para alterar para `read scopes` no futuro. Este documento será atualizado quando que ocorre.
- `ADDITIONAL_SCOPES`: Escopos adicionais que talvez você queira usar para seu aplicativo. Eles devem ser usados no futuro.
- `CLIENT_ID`: A ID de aplicativo obtido a partir do portal.
- `REDIRECT_URL`: O redirecionamento onde você espera o token para ser postada.
- `EXTRA_QP`: Nada extra que você deseja passar para o servidor em um formato codificada como URL.
- `FB_POLICY`: A política que você está chamando. Essa é a parte mais importante para estas noções básicas.
- `EMAIL_SIGNIN_POLICY`: A política que você está chamando. Essa é a parte mais importante para estas noções básicas.
- `EMAIL_SIGNUP_POLICY`: A política que você está chamando. Essa é a parte mais importante para estas noções básicas.

## <a name="add-references-to-android-adal-to-your-project"></a>Adicionar referências para Android ADAL ao seu projeto

> [AZURE.NOTE]  ADAL para Android usa um modelo baseado em sua intenção para invocar autenticação. Tentativas "disponha" o aplicativo para realizar o trabalho. Este exemplo inteiro e todos os ADAL para Android, centros sobre como gerenciar propósitos e passar informações entre elas.

Primeiro, informe Android sobre o layout do seu aplicativo, incluindo as tentativas que você deseja usar. Esses propósitos serão explicados em detalhes posteriormente neste tutorial.

Atualizar seu projeto `AndroidManifest.xml` arquivo para incluir todos os seus propósitos:

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Como você pode ver, você definir cinco atividades. Você usará todas essas.

- `AuthenticationActivity`: Isso provém da ADAL e fornece o modo de exibição de entrada da web.
- `LoginActivity`: Exibe suas políticas de entrada e os botões para cada política.
- `SettingsActivity`: Use isso para alterar as configurações de aplicativo no tempo de execução.
- `AddTaskActivity`: Use isso para adicionar tarefas à sua API REST protegidos por Azure AD.
- `ToDoActivity`: Isso é a atividade principal que exibe tarefas.

## <a name="create-the-sign-in-activity"></a>Criar a atividade de entrada

Criar uma atividade principal e chame- `LoginActivity`.

Criar um arquivo chamado `LoginActivity.java`.

Você precisa inicializar a atividade e adicione alguns botões que controlarão a interface do usuário. Isso é familiar se você gravou código Android antes.

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
Agora você criou botões que chamam seu `ToDoActivity` intenção (que chama ADAL quando precisar de um token). Eles fazem isso usando sua atividade como uma referência e um parâmetro extra. Este parâmetro extra é passado pela `intent.putExtra()` método. Você define `"thePolicy"` usando o que você especificou no `Constants.java`. Isso informa à intenção qual diretiva invocar durante a autenticação.

## <a name="create-the-settings-activity"></a>Criar a atividade de configurações

Esta é uma atividade que preenche suas configurações de interface do usuário.

Criar um arquivo chamado `SettingsActivity.java` para simples criar, ler, atualizar e excluir operações (CRUD).

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

    //private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        loadSettings();
//      checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//      checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


    }

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

    private void saveSettings(String key, boolean value) {
        SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
                Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
        Editor prefsEditor = prefs.edit();
        prefsEditor.putBoolean(key, value);
        prefsEditor.commit();
    }
}
```

## <a name="create-the-add-task-activity"></a>Criar a atividade de tarefa de adicionar

Você pode usar essa atividade para adicionar uma tarefa ao seu ponto de extremidade de API REST.

Criar um arquivo chamado `AddTaskActivity.java` e gravar o seguinte.

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

## <a name="create-the-to-do-list-activity"></a>Criar a atividade de lista de tarefas pendentes

Esta é a atividade mais importante. Você pode utilizar para obter um token do Azure AD para uma política e use esse token para chamar o servidor da API REST de tarefa.

Criar um arquivo chamado `ToDoActivity.java` e gravar o seguinte. (As chamadas serão explicadas posteriormente.)

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}

```


 Você deve ter notado que isso depende de métodos que ainda não tiver sido escritos. Eles incluem `updateLoggedInUser()`, `clearSessionCookie()`, e `getTasks()`. Você vai escrever aqueles neste manual. Ignore os erros no Android Studio agora.

Explicação dos parâmetros:

  - `SCOPES`: Necessário, os escopos que você está tentando solicitar acesso para. Para visualização B2C, esta é a mesma que `client_id`, mas isso poderá mudar no futuro.
  - `POLICY`: A política para quando você desejar autenticar o usuário.
  - `CLIENT_ID`: Necessária, vem a partir do portal do Azure AD.
  - `redirectUri`: Pode ser configurado como o nome do pacote. Não é necessário para ser fornecido para o `acquireToken` chamar.
  - `getUserInfo()`: A maneira de pesquisar se um usuário já está no cache. O parâmetro também descreve como solicitar que um usuário se esse usuário não for encontrado ou se o token de acesso do usuário é inválido. Esse método será gravado neste manual.
  - `PromptBehavior.always`: Ajuda a solicitar credenciais ignorar o cache e cookie.
  - `Callback`: Chamado depois que um código de autorização é trocado para um token. Ela terá um objeto `AuthenticationResult`, que contém o token de acesso, data de vencimento e informações de token de ID.

> [AZURE.NOTE]  O aplicativo de portal da empresa Microsoft Intune fornece o componente de agente e ele pode ser instalado no dispositivo do usuário. O aplicativo fornece o acesso de (SSO) logon único em todos os aplicativos do dispositivo. Desenvolvedores devem estar preparados para permitir o Intune. ADAL para Android usará a conta do agente se houver uma conta de usuário criada em autenticador. Para usar o corretor, o desenvolvedor precisa registrar um especial `redirectUri` do corretor usar. `redirectUri`está no formato de msauth://packagename/Base64UrlencodedSignature. Você pode obter `redirectUri` para o aplicativo usando o script `brokerRedirectPrint.ps1` ou usando a chamada API `mContext.getBrokerRedirectUri()`. A assinatura está relacionada ao seu certificados de assinatura da loja do Google Play.

 Você pode pular o usuário agente usando:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Para reduzir a complexidade neste início rápido B2C, podemos optou por ignorar o corretor em nosso exemplo.

Em seguida, crie métodos de auxiliar que obter o token sozinho durante chamadas de autenticação para a tarefa API.

Na mesma `ToDoActivity.java` arquivo, escreva o seguinte.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Também adicionar métodos que serão "definir" e "obter" `AuthenticationResult` (que tem o token) ao global `Constants`. Embora `ToDoActivity.java` usa `sResult` seus fluxos, você precisa adicionar esses métodos. Se não estiver, seu outras atividades não terão acesso ao token para realizar o trabalho (como a adição de uma tarefa em `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>Crie um método para retornar um identificador de usuário

ADAL Android representa o usuário na forma de um `UserIdentifier` objeto. Isso gerencia o usuário. Você pode usar o objeto para identificar se o mesmo usuário está sendo usado em suas chamadas. Usando essas informações, você pode confiar no cache, em vez de fazer uma chamada de novo no servidor. Para tornar mais fácil, criamos o `getUserInfo()` método que retorna `UserIdentifier`. Você pode usá-lo com `acquireToken()`. Também criamos uma `getUniqueId()` método que retorna a identificação de `UserIdentifier` no cache.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }

```

### <a name="write-helper-methods"></a>Escrever métodos auxiliar

Em seguida, escrever alguns métodos helper para ajudá-lo a limpar os cookies e forneça `AuthenticationCallback`. Esses métodos são usados puramente de amostra para certificar-se de que você está em um estado limpo quando você chamar seu `ToDo` atividade.

No mesmo arquivo chamado `ToDoActivity.java`, escreva o seguinte.

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
```

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }

```   

## <a name="call-the-task-api"></a>Chamar a API de tarefa

Depois que sua atividade pronta para atrair tokens, você pode escrever sua API para acessar o servidor de tarefa.

`getTasks`Fornece uma matriz que representa as tarefas no seu servidor.

Iniciar com `getTasks`.

No mesmo arquivo chamado `ToDoActivity.java`, escreva o seguinte.

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```

Também escreva um método que irá inicializar suas tabelas na primeira execução.

No mesmo arquivo chamado `ToDoActivity.java`, escreva o seguinte.

```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```

Você pode ver o que este código requer alguns métodos adicionais para fazer seu trabalho. Escreva esses Avançar.

### <a name="create-an-endpoint-url-generator"></a>Criar um gerador de URL do ponto de extremidade

Você precisa gerar a URL de ponto de extremidade que você vai ser se conectando. Faça isso no mesmo arquivo de classe.

**No mesmo arquivo** denominado `ToDoActivity.java`, escreva o seguinte.

 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


Observe que você adicionar o token de acesso à solicitação do código discutido na próxima seção.

## <a name="write-some-ux-methods"></a>Escreva alguns métodos UX

Android requer que você lide com alguns retornos de chamada para operar o aplicativo. Estas são `createAndShowDialog` e `onResume()`. Isso é familiar se você gravou código Android antes.

No mesmo arquivo chamado `ToDoActivity.java`, escreva o seguinte.

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }


```

Em seguida, gerencie seus retornos de chamada de caixa de diálogo.

No mesmo arquivo chamado `ToDoActivity.java`, escreva o seguinte.

```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }

```

Agora você deve ter uma `ToDoActivity.java` arquivo que compila. Todo o projeto também deve compilar neste momento.

## <a name="run-the-sample-app"></a>Executar o aplicativo de amostra

Finalmente, criar e executar o aplicativo no Android Studio ou Eclipse. Inscrever ou entrar no aplicativo. Crie tarefas para o usuário conectado. Sair e entrar novamente como um usuário diferente e, em seguida, criar tarefas para esse usuário.

Observe que as tarefas são armazenados por usuário a API, porque a API extrai a identidade do usuário do token de acesso que ele recebe.

Para referência, o exemplo concluído [é fornecido como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Você também pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## <a name="important-information"></a>Informações importantes


### <a name="encryption"></a>Criptografia

ADAL criptografa os tokens e a loja no `SharedPreferences` por padrão. Você pode examinar o `StorageHelper` classe para ver os detalhes. Android introduzido **AndroidKeyStore para 4.3(API18)** armazenamento seguro de chaves particulares. ADAL que usa para API18 e acima. Se você deseja usar ADAL para versões anteriores do SDK, você precisará fornecer uma chave secreta no `AuthenticationSettings.INSTANCE.setSecretKey`.

### <a name="session-cookies-in-web-view"></a>Cookies de sessão exibição na web

Modo de exibição de web Android não limpar cookies de sessão depois que o aplicativo for fechado. Você pode fazer isso com o código de exemplo a seguir.
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[Saiba mais sobre cookies](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).
