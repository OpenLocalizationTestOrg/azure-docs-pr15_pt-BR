## <a name="send-messages-to-event-hubs"></a>Enviar mensagens para Hubs de evento

Nesta seção, podemos gravará um aplicativo C para enviar eventos para seu Hub de evento. Usaremos a biblioteca de massa do próton AMQP do [projeto Apache Qpid](http://qpid.apache.org/). Isso é análogo a usar tópicos e filas do barramento de serviço com AMQP de C como mostrado [aqui](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para obter mais informações, consulte a [documentação de massa do Qpid próton](http://qpid.apache.org/proton/index.html).

1. Na [página de Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), clique no link de **Instalação massa do Qpid próton** e siga as instruções dependendo do seu ambiente. Vamos supor que um ambiente Linux; Por exemplo, uma [Máquina virtual do Azure Linux](../articles/virtual-machines/virtual-machines-linux-quick-create-cli.md) com Ubuntu 14.04.

2. Para compilar a biblioteca de massa do próton, instale os pacotes a seguir:

    ```
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```

3. Baixar a biblioteca de [Massa do Qpid próton](http://qpid.apache.org/proton/index.html) e extrair, por exemplo:

    ```
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```

4. Crie um diretório de compilação, a compilação e a instalação:

    ```
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```

5. No seu diretório de trabalho, crie um novo arquivo chamado **sender.c** com o seguinte conteúdo. Lembre-se de substituir o valor para o seu nome de evento Hub e namespace (a última normalmente é `{event hub name}-ns`). Você também deve substituir uma versão codificada como URL da chave para o **SendRule** criado anteriormente. Você pode codificar URL-lo [aqui](http://www.w3schools.com/tags/ref_urlencode.asp).

    ```
    #include "proton/message.h"
    #include "proton/messenger.h"

    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>

    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }  

    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  

    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }

    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";

        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();

        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);

        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));

        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);

        pn_message_free(message);
    }

    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");

        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);

        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }

        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);

        return 0;
    }
    ```

6. Compile o arquivo, considerando que **gcc**:

    ```
    gcc sender.c -o sender -lqpid-proton
    ```

> [AZURE.NOTE] Nesse código, podemos usar uma janela de saída de 1 para forçar as mensagens enviadas assim que possível. Em geral, seu aplicativo deve tentar mensagens de lote para aumentar a produtividade. Consulte a [página de Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) para obter mais informações sobre como usar a biblioteca de massa do Qpid próton neste e em outros ambientes e de plataformas para as quais ligações são fornecidas (atualmente Perl, PHP, Python e Ruby).
