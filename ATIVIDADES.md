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
  ```
 *Breve Descrição:* O programa conseguiu identificar que o disco /dev/sda possui 3 partições.
- A primeira (/dev/sda1) foi corretamente reconhecida como ext4, com um UUID válido.
- Já a segunda (/dev/sda2) apresentou valores truncados ou corrompidos (UUID= ST U, TYPE= WT U), o que indica que o blkid não conseguiu interpretar corretamente os metadados dessa partição. Isso pode acontecer em casos como:
- Partição sem sistema de arquivos formatado.
- Partição reservada ou usada por outro tipo de estrutura (ex.: LVM, criptografia).
- Erro de leitura ou ausência de LABEL/UUID definidos.




