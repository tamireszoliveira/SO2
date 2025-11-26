## RELATÓRIO DE PRÁTICAS E CÓDIGOS 
 
**Nome do Aluno:** Tamires Alexandre de Oliveira
**Turno:** Noite
**Data do Último Commit:** 25/11/2025

## ATIVIDADE 1: Relatório das Práticas de Aula 
### Capítulo 6: Práticas de Discos e Montagem 

#### Prática 8b65b431 01 (Livro-Texto p. 171)

Foi feita a configuração de uma nova partição na vm Debian Linux. A sequência de comandos mostra:
1. 	Finalização da criação da tabela de partições com o comando w dentro do utilitário de particionamento (como fdisk).
2. 	A formatação da partição  com o sistema de arquivos ext4, usando o comando mkfs.


Descrição das saídas:
Command w:
Finaliza a edição da tabela de partições. O sistema confirma que a tabela foi alterada e sincroniza os discos.
sudo mkfs -t ext4/dev/sdb1:
O sistema executa com sucesso a criação do sistema de arquivos ext4 na partição /dev/sdb1. A saída mostra:


- Criação de  524.288 blocos de 4 KB
- 131.072 inodes
- UUID gerado: b3c5dd4c-0eae-4cb3-b5ae-ec99137ef1c2
- Tabelas de grupo, tabelas de inodes e journal criados com sucesso

Além disso, também foi feita a criação do ponto de montagem /backup e a configuração da montagem automática da partição adicionando a entrada correspondente no arquivo /etc/fstab

#### Prática 8b65b431 02 (Livro-Texto p. 172)
Foi realizada a criação do diretório /home/usuario/cdrom para servir como ponto de montagem. Em seguida, foi feita a montagem manual do dispositivo  /dev/sr0(unidade de CD-ROM) nesse diretório utilizando o comando mount. Após a montagem, foi possível acessar o conteúdo do CD, validando a operação com a leitura de um arquivo de texto.


Descrição das saídas:
df -h:
Filesystem      Size  Used Avail Use% Mounted on
/dev/sr0        1024M  1024M     0 100% /home/userlinux/cdrom
cat /home/userlinux/cdrom/arquivo.txt:
AIED VIVO

### Capítulo 7: Práticas de Processos 
 
#### Prática prc0001 01 (Livro-Texto p. 233)

O objetivo foi mostrar a localização de processos python ativos no sistema utilizando comandos de terminal. Para isso, foram executados os seguintes passos:

- No diretório pessoal, foi gerada a localidade en_US.UTF-8 com o comando sudo locale-gen"en_US.UTF-8", garantindo compatibilidade com ferramentas e scripts.
- Foi usado o comando script para iniciar o registro da sessão, que grava todas as ações realizadas no terminal.
- Utilizou-se o comando ps aux | grep python para listar todos os processos ativos e filtrar aqueles relacionados ao Python.
- Encerrada a sessão com o comando exit.
- Por fim, a validação da prática com o comando sudo aied validar prc0001 checkpoint01, que analisou o conteúdo do arquivo typescript e confirmou a execução correta dos comandos.
Evidência de Validação:


Saída do comando 'cat /home/userlinux/typescript' (após filtrar por 'python')
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


