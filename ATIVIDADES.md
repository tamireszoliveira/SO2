## RELATÓRIO DE PRÁTICAS E CÓDIGOS 
 
**Nome do Aluno:** Tamires Alexandre de Oliveira
**Turno:** Noite
**Data do Último Commit:** 15/11/2025

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




