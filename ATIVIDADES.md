(Início do Modelo ATIVIDADES.md) 
 
Markdown 
 
 
# Projeto Final - Capítulos 6, 7 e 9 
 
## RELATÓRIO DE PRÁTICAS E CÓDIGOS 
 
**Nome do Aluno:** Tamires Alexandre de  Oliveira
**Turno:** Noite
**Data do Último Commit:** 26/11/2025 
 
 --- 
 
## ATIVIDADE 1: Relatório das Práticas de Aula 
 
Esta seção documenta a execução das práticas de administração de sistemas realizadas em 
sala, conforme solicitado no final de cada capítulo do livro-texto. 
 
**Instrução:** Para cada prática, forneça um breve resumo do que foi feito e cole a saída de 
texto dos comandos de validação solicitados. **Não use imagens (printscreens)**. 


### Capítulo 6: Práticas de Discos e Montagem 
 
#### Prática 8b65b431 01 (Livro-Texto p. 171) 
* **Resumo da Prática:** Foi feita a configuração de uma nova partição na vm Debian Linux. A sequência de comandos mostra:
1. 	Finalização da criação da tabela de partições com o comando w dentro do utilitário de particionamento (como fdisk).
2. 	A formatação da partição  com o sistema de arquivos ext4, usando o comando mkfs.
3. 	Além disso, também foi feita a criação do ponto de montagem /backup e a configuração da montagem automática da partição adicionando a entrada correspondente no arquivo /etc/fstab
. 
* **Evidência de Validação:** 
    ```bash 
    # Saída do comando 'cat /etc/fstab' 
    /dev/sdb1   /backup   ext4   defaults   0   2 
 
    # Saída do comando 'df -h' 
    /dev/sdb1       20G   1.1G   18G   6%   /backup 
    ``` 

#### Prática 8b65b431 02 (Livro-Texto p. 172)

* **Resumo da Prática:** Foi realizada a criação do diretório /home/usuario/cdrom para servir como ponto de montagem. Em seguida, foi feita a montagem manual do dispositivo  /dev/sr0(unidade de CD-ROM) nesse diretório utilizando o comando mount. Após a montagem, foi possível acessar o conteúdo do CD, validando a operação com a leitura de um arquivo de texto. 
* **Evidência de Validação:** 
    ```bash 
    # Saída do comando 'df -h' 
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/sr0        1024M  1024M     0 100% /home/userlinux/cdrom 

 
    # Saída do comando 'cat /home/usuario/cdrom/arquivo.txt' 
    AIED VIVO


### Capítulo 7: Práticas de Processos 


#### Prática prc0001 01 (Livro-Texto p. 233) 
* **Resumo da Prática:** 
O objetivo foi mostrar a localização de processos python ativos no sistema utilizando comandos de terminal. Para isso, foram executados os seguintes passos:

- No diretório pessoal, foi gerada a localidade en_US.UTF-8 com o comando sudo locale-gen"en_US.UTF-8", garantindo compatibilidade com ferramentas e scripts.
- Foi usado o comando script para iniciar o registro da sessão, que grava todas as ações realizadas no terminal.
- Utilizou-se o comando ps aux | grep python para listar todos os processos ativos e filtrar aqueles relacionados ao Python.
- Encerrada a sessão com o comando exit.
- Por fim, a validação da prática com o comando sudo aied validar prc0001 checkpoint01, que analisou o conteúdo do arquivo typescript e confirmou a execução correta dos comandos.
Evidência de Validação:


* **Evidência de Validação:**
* Saída do comando 'cat /home/userlinux/typescript' (após filtrar por 'python')
    ```bash 
    userlinux@debian:~$ ps aux | grep python
    userlin+     970  0.0  0.1   6336  2132 pts/2    S+   22:44   0:00 grep python 
 

### Capítulo 9: Práticas de Redes 
 
#### Prática 0002 checkpoint03 (Livro-Texto p. 286)



### Códigos do Capítulo 6 (Discos e Montagem) 
 
#### `devices.cpp` (Livro-Texto p. 151-152) 
 
* **Objetivo do Código:** Ler o arquivo virtual `/proc/mounts` para descobrir e imprimir qual 
dispositivo de bloco (ex: `/dev/sda1`) está atualmente montado no diretório raiz (`/`). 
* **Código-Fonte:** 
    ```cpp 
    #include <iostream> 
    #include <fstream> 
    #include <optional> 
    #include <string> 
 
    std::optional<std::string> get_device_of_mount_point(std::string path) { 
        std::ifstream mounts("/proc/mounts"); 
        std::string mountPoint; 
        std::string device; 
        while (mounts >> device >> mountPoint) { 
            if (mountPoint == path) 
                return device; 
        } 
        return std::nullopt; 
    } 
 
    int main() { 
        if (const auto device = get_device_of_mount_point("/")) 
            std::cout << *device << "\n"; 
        else 
            std::cout << "Not found\n"; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `g++ -o devices devices.cpp -std=c++17` 
    * *Saída da Execução:* 
        ```bash 
        /dev/sda1
        ``` 
    * *Breve Descrição:* (Explique o que a saída significa. O dispositivo que apareceu (ex: `/dev/sda1`) é 
o que você esperava para a sua partição raiz? Por quê?) 
A saída foi a esperada pois o dispositivo que apareceu na saída do programa foi /dev/sda1; é o esperado para a partição raiz () do sistema, pois em instalações típicas do Linux — especialmente em ambientes de máquina virtual — o disco principal costuma ser identificado como sda, e a primeira partição como sda1.


* **Objetivo do Código:** Usar a biblioteca `libblkid` para listar todas as partições de um disco 
(ex: `/dev/sda`) e imprimir seus atributos, como **UUID**, **LABEL** e **TYPE**. 
* **Código-Fonte:** 
    ```c 
    #include <stdio.h> 
    #include <string.h> 
    #include <err.h> 
    #include <blkid/blkid.h> 
 
    int main (int argc, char *argv[]) { 
        if (argc != 2) { 
            fprintf(stderr, "Uso: %s <dispositivo>\nEx: %s /dev/sda\n", argv[0], argv[0]); 
            return 1; 
        } 
         
        blkid_probe pr = blkid_new_probe_from_filename(argv[1]); 
        if (!pr) { 
            err(1, "Falha ao abrir %s", argv[1]); 
        } 
 
        blkid_partlist ls; 
        int nparts, i; 
 
        ls = blkid_probe_get_partitions(pr); 
        if (!ls) { 
             err(1, "Falha ao obter partições de %s", argv[1]); 
        } 
 
        nparts = blkid_partlist_numof_partitions(ls); 
        printf("Número de partições em %s: %d\n", argv[1], nparts); 
 
        const char *uuid, *label, *type; 
 
        for (i = 0; i < nparts; i++) { 
             char dev_name[20]; 
             // (Cria o nome da partição, ex: /dev/sda + 1 = /dev/sda1) 
             sprintf(dev_name, "%s%d", argv[1], (i+1)); 
              
             blkid_probe pr_part = blkid_new_probe_from_filename(dev_name); 
             if (!pr_part) continue;  
              
             blkid_do_probe(pr_part); 
              
             blkid_probe_lookup_value(pr_part, "UUID", &uuid, NULL); 
             blkid_probe_lookup_value(pr_part, "LABEL", &label, NULL); 
             blkid_probe_lookup_value(pr_part, "TYPE", &type, NULL); 
              
             printf("  Partição: %s, UUID=%s, LABEL=%s, TYPE=%s\n", dev_name,  
                    (uuid ? uuid : "null"), (label ? label : "null"), (type ? type : "null")); 
              
             blkid_free_probe(pr_part); 
        } 
         
        blkid_free_probe(pr); 
        return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `gcc -o getuuid getuuid.c -lblkid` 
    * *Saída da Execução:* (Execute com `sudo ./getuuid /dev/sda`) 
        ```bash 
       Número de partições em /dev/sda: 3
       Partição: /dev/sda1, UUID=c3c36174-8146-445e-9db2-1f048f98c2c6, LABEL=null, TYPE=ext4
       Partição: /dev/sda2, UUID=�ST��U, LABEL=null, TYPE=�WT��U

 *Breve Descrição:* O programa conseguiu identificar que o disco /dev/sda possui 3 partições.
- A primeira (/dev/sda1) foi corretamente reconhecida como ext4, com um UUID válido.
- Já a segunda (/dev/sda2) apresentou valores truncados ou corrompidos (UUID= ST U, TYPE= WT U), o que indica que o blkid não conseguiu interpretar corretamente os metadados dessa partição. Isso pode acontecer em casos como:
- Partição sem sistema de arquivos formatado.
- Partição reservada ou usada por outro tipo de estrutura (ex.: LVM, criptografia).
- Erro de leitura ou ausência de LABEL/UUID definidos.



#### `calcfb.cpp` (Livro-Texto p. 187) 
*(Esta prática requer dois arquivos)* 
 
* **Objetivo do Código:** Demonstrar como criar e usar uma biblioteca de cabeçalho (`.h`) 
local. O `calcfb.cpp` (programa principal) incluirá `fibonacci.h` (biblioteca) para calcular um 
número da sequência. 
* **Código-Fonte (`fibonacci.h`):** 
    ```cpp 
    // (p. 187) 
    int fibonacci(int n) { 
        if (n <= 1) return n; 
        return fibonacci(n - 1) + fibonacci(n - 2); 
    } 
    ``` 
* **Código-Fonte (`calcfb.cpp`):** 
    ```cpp 
    // (p. 187) 
    #include <stdio.h> 
    #include "fibonacci.h" // Aspas "" para incluir um arquivo local 
 
    int main(int argc, char* argv[]) { 
        printf("F%d: %d \n", 4, fibonacci(4)); 
        return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `g++ -o calcfb calcfb.cpp` 
    * *Saída da Execução:* 
        ```bash 
        F4: 3
        ``` 
    * *Breve Descrição:* A saída foi  porque o programa calcula o 4º número da sequência de Fibonacci, que é 3. A função é recursiva e soma os dois anteriores:  f3 + f2  = 2 + 1 = 3.

 
  #### `thread.cpp` (Livro-Texto p. 190) 
 
* **Objetivo do Código:** Demonstrar a criação de múltiplas threads que executam 
concorrentemente com a thread principal (`main`). 
* **Código-Fonte:** 
    ```cpp 
    // (p. 190) 
    #include <iostream> 
    #include <thread> 
    #include <string> 
 
    using namespace std; 
     
    // (Função de exemplo baseada no livro) 
    void task1(string msg) { 
        cout << "A thread está falando: " << msg << endl; 
    } 
 
    int main() { 
        thread t1(task1, "Olá"); 
         
        cout << "A 'main' executou..." << endl; 
         
        t1.join(); // A main espera a thread t1 terminar 
        return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `g++ thread.cpp -o thread -pthread -std=c++11` 
    * *Saída da Execução:* 
        ```bash 
        A 'main' executou...
        A thread está falando: Olá 
        ``` 
    * *Breve Descrição:* A linha  geralmente aparece antes, mas isso depende do agendamento das threads pelo sistema operacional.
    	O comando t1.join() faz a thread principal esperar até que a thread  termine sua execução. Sem ele, o programa poderia encerrar antes da thread secundária terminar.



#### `usefork.cpp` (Livro-Texto p. 191) 
 
* **Objetivo do Código:** Demonstrar a chamada `fork()`. O programa se clona; o pai e o filho 
executam o *mesmo* código, mas alteram variáveis diferentes, provando que têm espaços de 
memória separados. 
* **Código-Fonte:** 
    ```cpp 
    // (p. 191) 
    #include <iostream> 
    #include <string> 
    #include <unistd.h> // Para fork() 
    #include <stdlib.h> // Para exit() 
 
    using namespace std; 
     
    int variavelGlobal = 2; // (p. 191, linha 5) 
 
    int main() { 
        string identidade; 
        int variavelFuncao = 20; // (p. 191, linha 9) 
 
        pid_t pID = fork(); // (p. 191, linha 11) 
 
        if (pID == 0) { 
            // Processo filho 
            identidade = "Processo filho: "; 
            variavelGlobal++; 
            variavelFuncao++; 
        }  
        else if (pID < 0) { 
            // Erro 
            cerr << "Failed to fork" << endl; 
            exit(1); 
        }  
        else { 
            // Processo pai 
            identidade = "Processo pai:"; 
        } 
 
        // Este código é executado por AMBOS 
        cout << identidade; 
        cout << " Variavel Global: " << variavelGlobal; 
        cout << " Variável Funcao: " << variavelFuncao << endl; 
        return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `g++ -o usefork usefork.cpp` 
    * *Saída da Execução:* 
        ```bash 
        Processo pai: Variavel Global: 2 Variável Funcao: 20
        userlinux@debian:~$ Processo filho:  Variavel Global: 3 Variável Funcao: 21
        ``` 
    * *Breve Descrição:* 	A variável  e  têm valores diferentes porque o processo filho recebe uma cópia do espaço de memória do pai, e modifica suas próprias cópias.
     	O processo filho incrementa ambas as variáveis ( e ), enquanto o pai mantém os valores originais. 	O processo que termina primeiro varia a cada execução. Como não há sincronização explícita, o sistema decide qual processo imprime primeiro.

      #### `usewait.cpp` (Livro-Texto p. 193) 
 
* **Objetivo do Código:** Demonstrar a chamada `wait()`. O processo-pai usa `wait(NULL)` 
para pausar sua própria execution e aguardar que o processo-filho termine antes de 
continuar. 
* **Código-Fonte:** 
    ```cpp 
    // (p. 193) 
    #include <iostream> 
    #include <unistd.h> 
    #include <sys/wait.h> // (p. 193, linha 3) 
 
    using namespace std; 
 
    int main() { 
        pid_t pID = fork(); 
        pid_t cpid; 
 
        if ( pID == 0 ) { 
            // Filho 
            cout << "Saindo do processo filho. " << endl; 
            return 0;   
        } else if (pID > 0) { 
            // Pai 
            cout << "Pai esperando o filho terminar..." << endl; 
            cpid = wait(NULL); // (p. 193, linha 17) 
        } else { 
            // Erro 
            cerr << "Failed to fork" << endl; 
            return 1; 
        } 
         
        cout << "PID do pai: " << getpid() << endl; 
        cout << "PID do filho (retornado por wait): " << cpid << endl; 
        return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `g++ -o usewait usewait.cpp` 
    * *Saída da Execução:* 
        ```bash 
        Pai esperando o filho terminar...
        Saindo do processo filho.
        PID do pai: 646
        PID do filho (retornado por wait): 647
        ``` 
    * *Breve Descrição:* A linha  sempre aparece antes de , porque o pai chama  e fica bloqueado até o filho terminar.
    	O PID do filho é impresso pelo processo-pai porque a função  retorna o identificador do processo-filho que terminou.
    	Isso demonstra que o pai só continua sua execução após o término do filho, garantindo ordem e sincronização entre os processos



#### `usewait_exit.cpp` (Livro-Texto p. 194) 
 
* **Objetivo do Código:** Expandir o `wait()`, mostrando como o pai pode capturar o *código de 
saída* (status) do filho, usando `WIFEXITED` e `WEXITSTATUS`. 
* **Código-Fonte:** 
    ```c 
    // (p. 194) (Este código é C, não C++) 
    #include <stdio.h> 
    #include <stdlib.h> 
    #include <unistd.h> 
    #include <sys/wait.h> 
    #include <signal.h> // Para psignal 
 
    int main() { 
        pid_t pid; 
        int stat; // (p. 194, linha 11) 
         
        pid = fork(); 
        if(pid == 0) { 
            // Filho 
            printf("Saindo do processo filho.\n"); 
            exit(1); // (p. 194, linha 15) 
        } 
        else if (pid > 0) { 
            // Pai 
            wait(&stat); // (p. 194, linha 17 - modificado do NULL) 
            if(WIFEXITED(stat)) { // (p. 194, linha 20) 
                printf("WEXIT: %d\n", WEXITSTATUS(stat)); // (p. 194, linha 21) 
            } 
            else if (WIFSIGNALED(stat)) { 
                psignal(WTERMSIG(stat), "Sinal de saída: "); 
            } 
             
            printf("PID do pai: %d\n", getpid()); 
            printf("PID do filho: %d\n", pid); 
        } 
        return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `gcc -o usewait_exit usewait_exit.cpp` 
    * *Saída da Execução:* 
        ```bash 
        Saindo do processo filho.
        WEXIT: 1
        PID do pai: 656
        PID do filho: 657
        ``` 
    * *Breve Descrição:* O status de saída impresso pelo WEXIT foi 1, porque o processo-filho chamou exit1.
 	 O wait(stat) permite que o pai recupere esse valor.
  	A macro wifexited(stat) confirma que o filho terminou normalmente (não por sinal).
 	 A macro wexitstatus(stat) extrai o código de saída passado pelo exit() do filho.
  	Assim, o pai consegue saber como o filho terminou e qual valor ele retornou.


#### `waitpid.cpp` (Livro-Texto p. 195) 
 
* **Objetivo do Código:** Demonstrar o `waitpid()` para gerenciar *múltiplos* filhos. O pai cria 5 
filhos, e então espera por *cada um* deles especificamente, coletando seus códigos de saída 
(100 a 104). 
* **Código-Fonte:** 
    ```cpp 
    // (p. 195) 
    #include <iostream> 
    #include <sys/wait.h> 
    #include <unistd.h>  
     
    using namespace std; 
 
    int main() { 
        pid_t pid[5]; 
        int i, stat; 
 
        for (i = 0; i < 5; i++) { 
            pid[i] = fork(); // (p. 195, linha 11) 
            if( pid[i] == 0) { 
                // Filho 
                sleep(1); // (p. 195, linha 14) 
                exit(100 + i); // (p. 195, linha 15) 
            } 
        } 
 
        for (i = 0; i < 5; i++) { 
            pid_t cpid = waitpid(pid[i], &stat, 0); // (p. 195, linha 19) 
            if (WIFEXITED(stat)) { 
                cout << "O filho " << cpid << " terminou com o status: "  
                     << WEXITSTATUS(stat) << endl; 
            } 
        } 
        return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `g++ -o waitpid waitpid.cpp` 
    * *Saída da Execução:* 
        ```bash 
        O filho 668 terminou com o status: 100
        O filho 669 terminou com o status: 101
        O filho 670 terminou com o status: 102
        O filho 671 terminou com o status: 103
        O filho 672 terminou com o status: 104
        ``` 
    * *Breve Descrição:*
-  Os PIDs dos filhos não necessariamente aparecem em ordem crescente, pois dependem de como o sistema operacional atribui e agenda os processos.
- 	Os códigos de status (100–104) aparecem em ordem, porque o laço  no pai chama  para cada filho específico, garantindo que o pai espere cada PID na sequência em que foram criados.
A diferença entre wait() e waitpid() é que:
-  wait() Apenas espera qualquer filho terminar, sem controle sobre qual.
-  waitpid() Permite ao pai esperar um filho específico (pelo PID), dando controle e previsibilidade sobre a ordem de coleta dos resultados.


#### `system.cpp` (Livro-Texto p. 196) 
 
* **Objetivo do Código:** Demonstrar a função `system()`, que é um atalho (e geralmente 
inseguro) para `fork + exec + wait`. O programa C++ pausa, executa um comando de shell (`ls -l`) e depois continua. 
* **Código-Fonte:** 
    ```cpp 
    // (p. 196) 
    #include <iostream> 
    #include <stdlib.h> // Para system() 
 
    int main() { 
        system("ls -l"); 
        std::cout << "Executado" << std::endl; 
        return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `g++ -o system system.cpp` 
    * *Saída da Execução:* 
        ```bash 
        total 220
        -rwxr-xr-x 1 userlinux userlinux 16000 Nov 26 22:41 calcfb
        -rw-r--r-- 1 userlinux userlinux   137 Nov 26 22:40 calcfb.cpp
         drwxr-xr-x 2 userlinux userlinux  4096 Sep 12 22:13 diretorio1
         drwxr-xr-x 2 userlinux userlinux  4096 Sep 12 22:13 diretorio2
         drwxr-xr-x 2 userlinux userlinux  4096 Sep 12 22:13 diretorio3
        -rw-r--r-- 1 userlinux userlinux   118 Nov 26 22:40 fibonacci.h
        -rwxr-xr-x 1 userlinux userlinux 16528 Nov 26 20:45 getuuid
        -rw-r--r-- 1 userlinux userlinux  1523 Nov 26 20:45 getuuid.c
        -rw-r--r-- 1 userlinux userlinux    19 Sep 12 21:47 hard_link.txt
        lrwxrwxrwx 1 userlinux userlinux    12 Sep 12 21:48 link_simbolico.txt -> original.txt
        -rwxr-xr-x 1 userlinux userlinux 16600 Nov 26 23:21 system
        -rw-r--r-- 1 userlinux userlinux   159 Nov 26 23:20 system.cpp
        -rwxr-xr-x 1 userlinux userlinux 26288 Nov 26 22:49 thread
        -rw-r--r-- 1 userlinux userlinux   320 Nov 26 22:49 thread.cpp
        -rw-r--r-- 1 userlinux userlinux  8192 Nov 15 01:02 typescript
        -rwxr-xr-x 1 userlinux userlinux 17496 Nov 26 22:55 usefork
        -rw-r--r-- 1 userlinux userlinux   634 Nov 26 22:54 usefork.cpp
        -rwxr-xr-x 1 userlinux userlinux 16800 Nov 26 22:59 usewait
        -rw-r--r-- 1 userlinux userlinux   688 Nov 26 22:59 usewait.cpp
        -rwxr-xr-x 1 userlinux userlinux 16272 Nov 26 23:05 usewait_exit
        -rw-r--r-- 1 userlinux userlinux   817 Nov 26 23:04 usewait_exit.cpp
        -rwxr-xr-x 1 userlinux userlinux 16792 Nov 26 23:11 waitpid
        -rw-r--r-- 1 userlinux userlinux   675 Nov 26 23:11 waitpid.cpp
        Executado
        ``` 
    * *Breve Descrição:*
- O comando  faz o programa pausar e executar o comando de shell .
- 	Como o  internamente faz , o processo principal espera o comando terminar antes de continuar.
- 	Por isso, a listagem de arquivos aparece antes da palavra .
- 	Só depois que o comando  termina, o programa imprime  e encerra.

#### `pop.cpp` (Livro-Texto p. 197) 
 
* **Objetivo do Código:** Demonstrar a função `popen()` (pipe open). Similar ao `system()`, ele 
executa um comando, mas permite ao programa C++ *capturar* a saída do comando (`ls -l`) e 
processá-la linha por linha. 
* **Código-Fonte:** 
    ```cpp 
    // (p. 197) 
    #include <iostream> 
    #include <stdio.h> // Para popen, pclose, FILE 
    #include <stdlib.h> // Para exit 
     
    int main() { 
        FILE *fpipe; 
        char *command = (char *)"ls -l"; 
        char line[256]; 
 
        if ( !(fpipe = (FILE*)popen(command,"r")) ) { // (p. 197, linha 9) 
            perror("Falha ao abrir um pipe"); 
            exit(1); 
        } 
     
        while ( fgets( line, sizeof line, fpipe)) { // (p. 197, linha 13) 
            std::cout << "Linha: " << line; // line já contém \n 
        } 
     
        pclose(fpipe); 
        return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `g++ -o pop pop.cpp` 
    * *Saída da Execução:* 
        ```bash 
        Linha: total 244
        Linha: -rwxr-xr-x 1 userlinux userlinux 16000 Nov 26 22:41 calcfb
        Linha: -rw-r--r-- 1 userlinux userlinux   137 Nov 26 22:40 calcfb.cpp
        Linha: drwxr-xr-x 2 userlinux userlinux  4096 Sep 12 22:13 diretorio1
        Linha: drwxr-xr-x 2 userlinux userlinux  4096 Sep 12 22:13 diretorio2
        Linha: drwxr-xr-x 2 userlinux userlinux  4096 Sep 12 22:13 diretorio3
        Linha: -rw-r--r-- 1 userlinux userlinux   118 Nov 26 22:40 fibonacci.h
        Linha: -rwxr-xr-x 1 userlinux userlinux 16528 Nov 26 20:45 getuuid
        Linha: -rw-r--r-- 1 userlinux userlinux  1523 Nov 26 20:45 getuuid.c
        Linha: -rw-r--r-- 1 userlinux userlinux    19 Sep 12 21:47 hard_link.txt
        Linha: lrwxrwxrwx 1 userlinux userlinux    12 Sep 12 21:48 link_simbolico.txt -> original.txt
        Linha: -rwxr-xr-x 1 userlinux userlinux 16624 Nov 26 23:28 pop
        Linha: -rw-r--r-- 1 userlinux userlinux   519 Nov 26 23:27 pop.cpp
        Linha: -rwxr-xr-x 1 userlinux userlinux 16600 Nov 26 23:21 system
        Linha: -rw-r--r-- 1 userlinux userlinux   159 Nov 26 23:20 system.cpp
        Linha: -rwxr-xr-x 1 userlinux userlinux 26288 Nov 26 22:49 thread
        Linha: -rw-r--r-- 1 userlinux userlinux   320 Nov 26 22:49 thread.cpp
        Linha: -rw-r--r-- 1 userlinux userlinux  8192 Nov 15 01:02 typescript
        Linha: -rwxr-xr-x 1 userlinux userlinux 17496 Nov 26 22:55 usefork
        Linha: -rw-r--r-- 1 userlinux userlinux   634 Nov 26 22:54 usefork.cpp
        Linha: -rwxr-xr-x 1 userlinux userlinux 16800 Nov 26 22:59 usewait
        Linha: -rw-r--r-- 1 userlinux userlinux   688 Nov 26 22:59 usewait.cpp
        Linha: -rwxr-xr-x 1 userlinux userlinux 16272 Nov 26 23:05 usewait_exit
        Linha: -rw-r--r-- 1 userlinux userlinux   817 Nov 26 23:04 usewait_exit.cpp
        Linha: -rwxr-xr-x 1 userlinux userlinux 16792 Nov 26 23:11 waitpid
        Linha: -rw-r--r-- 1 userlinux userlinux   675 Nov 26 23:11 waitpid.cpp
        ``` 
    * *Breve Descrição:*
 - A diferença para o programa system.cpp é que lá o comando ls -l era executado e sua saída ia direto para o terminal, sem intervenção do programa.
 -	Com popen(), o programa captura a saída do comando e pode processá-la linha por linha;	Isso permite manipular, filtrar ou transformar a saída antes de exibi-la.
 - 	No exemplo, cada linha da listagem é prefixada com "Linha:", mostrando que o programa tem controle sobre o texto retornado pelo comando.


   #### `receivesignal.cpp` (Livro-Texto p. 203) 
 
* **Objetivo do Código:** Demonstrar como um processo pode "capturar" (handle) um sinal. 
Este programa entra em loop infinito, mas se o usuário pressionar `Ctrl+C` (que envia o sinal 
`SIGINT`), o programa executa a função `signal_handler` em vez de fechar imediatamente. 
* **Código-Fonte:** 
    ```cpp 
    // (p. 203) 
    #include <iostream> 
    #include <csignal> 
    #include <unistd.h> 
     
    using namespace std; 
     
    void signal_handler (int signum) { // (p. 203, linha 6) 
       cout << "Processo será interrompido pelo sinal: (" << signum << ")." << endl; 
       exit(signum);   
    } 
     
    int main () { 
       signal(SIGINT, signal_handler);  // (p. 203, linha 12) 
        
       while(1) { 
           cout << "Dentro do laço de repetição infinito." << endl; 
           sleep(1); 
       } 
       return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `g++ -o receivesignal receivesignal.cpp` 
    * *Saída da Execução:* (Deixe o programa rodar por 3 segundos e então pressione `Ctrl+C`) 
        ```bash 
        Dentro do laço de repetição infinito.
        Dentro do laço de repetição infinito.
        Dentro do laço de repetição infinito.
        Dentro do laço de repetição infinito.
        Dentro do laço de repetição infinito.
       ^CProcesso será interrompido pelo sinal: (2).
        ``` 
    * *Breve Descrição:*  Ao pressionar Ctrl+C, o terminal envia o sinal SIGINT (número 2) para o processo. Normalmente isso encerraria o programa imediatamente e silenciosamente mas como o código registrou um handler com signal(SIGINT, signal_handler), o programa executa a função signal_handler antes de terminar e exibe aquela mensagem "Processo será interrompido pelo sinal: (2)".
 


 #### `ignoresignal.cpp` (Livro-Texto p. 204) 
 
* **Objetivo do Código:** Demonstrar como um processo pode *ignorar* ativamente um sinal. 
Este programa é similar ao anterior, mas usa `SIG_IGN` para se tornar imune ao `Ctrl+C` 
(SIGINT). 
* **Código-Fonte:** 
    ```cpp 
    // (p. 204) 
    #include <iostream> 
    #include <csignal> 
    #include <unistd.h> 
     
    using namespace std; 
     
    int main () { 
       signal(SIGINT, SIG_IGN);  // (p. 204, linha 8) 
        
       int i = 0; 
       while(1) { 
           cout << "Estou em loop imune... (" << ++i << ")" << endl; 
           sleep(1); 
       } 
       return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `g++ -o ignoresignal ignoresignal.cpp` 
    * *Saída da Execução:* (Pressione `Ctrl+C` várias vezes. Para parar, use `Ctrl+\` (SIGQUIT) ou `kill -9` 
de outro terminal). 
        ```bash 
        Estou em loop imune... (1)
        Estou em loop imune... (2)
        Estou em loop imune... (3)
        Estou em loop imune... (4)
        Estou em loop imune... (5)
        Estou em loop imune... (6)
        ^CEstou em loop imune... (7)
        Estou em loop imune... (8)
        Estou em loop imune... (9)
        Estou em loop imune... (10)
        Estou em loop imune... (11)
        Estou em loop imune... (12)
        Estou em loop imune... (13)
        Estou em loop imune... (14)
        Estou em loop imune... (15)
        ^\Quit
        ``` 
    * *Breve Descrição:* O programa não parou quando foi pressionado ^C -  porque o sinal SIGINT foi configurado para ser ignorado (SIG_IGN). Isso torna o processo "imune" ao Ctrl+C. Para encerrar, foi necessário enviar outro sinal (SIGQUIT ou SIGKILL) mostrando como um processo pode ignorar sinais específicos, controlando como (ou se) reage a interrupções externas.



#### `raisesignal.cpp` (Livro-Texto p. 204-205) 
 
* **Objetivo do Código:** Demonstrar como um processo pode enviar um sinal *para si mesmo* 
usando a função `raise()`. O programa irá rodar por 5 segundos e então se autoenviar um 
SIGINT. 
* **Código-Fonte:** 
    ```cpp 
    // (p. 204-205) 
    #include <iostream> 
    #include <csignal> 
    #include <unistd.h> 
     
    using namespace std; 
     
    void signal_handler(int signum) { 
       cout << "Auto-sinal recebido: (" << signum << ")." << endl; 
       exit(signum); 
    } 
     
    int main () { 
        int i = 0; 
        signal(SIGINT, signal_handler); // (p. 205, linha 14) 
         
        while (++i) { 
            cout << "Dentro do laço de repetição infinito." << endl; 
            if( i == 5) { 
                raise(SIGINT); // (p. 205, linha 19) 
            } 
            sleep(1); 
        } 
        return 0; 
    } 
    ``` 
* **Análise da Saída:** 
    * *Comando de Compilação:* `g++ -o raisesignal raisesignal.cpp` 
    * *Saída da Execução:* 
        ```bash 
        Dentro do laço de repetição infinito.
        Dentro do laço de repetição infinito.
        Dentro do laço de repetição infinito.
        Dentro do laço de repetição infinito.
        Dentro do laço de repetição infinito.
        Auto-sinal recebido: (2).
        ``` 
    * *Breve Descrição:* Durante os primeiros 5 segundos, o programa imprime repetidamente "dentro do laço de repetição infinita".	Quando i ==5, o comando raise(SIGINT) é chamado. Isso     envia o sinal SIGINT (2) para o próprio processo. 	Como o programa registrou um handler (), essa função é chamada em vez de encerrar silenciosamente.	O handler imprime "Auto-sinal      recebido: (2) e depois chama exit(signum), encerrando o programa sozinho depois de 5s.

      



   

