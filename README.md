# PROJETO FINAL

### Instituto Federal de Alagoas - Campus Arapiraca
### Disciplina de Infraestrutura e Serviços de Rede
### Prof. Alaelson Jatobá
### Turma 914 | 2021

### Grupo 3:

- Ana Clara Silva Nunes;
- Isabel Vitória da Silva Gama;
- Jeycy Karollaynny Silva Almeida;
- Lavynia Farias Santos;
- Marya Eduardha Freitas Pereira.

# Sumário

- [X] 1. Introdução;
- [X] 2. Definições Iniciais:
     - [X] 2.1. Tabelas de definições de domínio, com os nomes e os endereços IPs das VMs;
     - [X] 2.2. Edição dos hostnames com o nome de domínio no S.O. de cada MV;
     - [X] 2.3. A configuração de hardware utilizada em cada VM;
     - [X] 2.4. Criar usuários (nomes dos integrantes da equipe) em cada VM;
- [ ] 3. Implementação dos Serviços de Rede (cada serviço uma sessão):
     - [X] 3.1. Configuração do DNS Master (ns1) e DNS Slave (ns2);
     - [ ] 3.2. Instalação do SAMBA;
     - [ ] 3.3. Implementação do servidor Web LAMP;
     - [ ] 3.4. Instalação do Gateway Server NAT;
     - [ ] 3.5. Configuração da rede interna LAN nas VMs;
- [ ] 4. Considerações Finais; 
- [ ] 5. Referências; 

# 1. Introdução

  O seguinte trabalho consiste na configuração dos serviços DNS Master e Slave através de um terminal linux, com o objetivo de criar um ambiente de rede virtualizaado com seis máquinas virtuais.

  O DNS (Domain Name Service) é um sistema de base de dados distribuído que traduz nomes para endereços IP e que permite que um URL como, por exemplo, www. algumaacoisa.edu.br seja traduzido para o endereço 200.131.62.17. Além disso, a configuração split DNS serve para atender de modo diferenciado a diferentes segmentos de rede, podendo responder à consulta de um URL com endereços IP diferentes ou traduzindo diferentes conjuntos de nomes. Para a configuração de um sistema, a zona Slave é utilizada quando o usuário tem dois ou mais servidores de DNS, fazendo a replicação da zona a partir da zona Master que, por sua vez, é onde se fazem as modificações referentes ao domínio e acrescentando as definições para a trasferência de dados paa o servidor Slave.

  Já o Samba consiste, basicamente, em um serviço e um conjunto de ferramentas que possibilitam a conexão de máquinas Windows e Linux, ou seja, ele permite que redes heterogêneas se comuniquem entre si, através do protocolo SMB (Server Message Block)/CIFS (Common Internet File System). Já o DNS (Domain Name System) é o sistema de nomes de domínio da internet, ou seja, é ele que transforma o endereço IP, geralmente confuso para a maioria das pessoas, no nome do site, tornando-o de mais fácil acesso e compreensão para quem não tem ligação com o meio da informática.
  
  O Gateway NAT (Network Address Translation) consiste em um serviço de conversão de endereços de rede que serve para que as instâncias em uma sub-rede privada possam se conectar a serviços fora da VPC, mas sem permitir que os serviço externos iniciem uma conexão com essas instâncias. Esse serviço substitui o endereço IP de origem das instâncias pelo endereço IP do gateway e, com isso, o dispositivo de NAT converte os endereços de volta para o endereço IP de origem inicial.
  
  Já o Web LAMP é uma combinação de softwares de código aberto, que foi uma das primeiras plataformas de código aberto para a rede, e continua sendo uma das maneiras mais comuns de fornecer aplicações web. Além disso, sua nomenclatura é um acrônimo reunindo as iniciais de seus quatro componentes base: Linux, Apache, MySQL e PHP e é considerado por muitos como a melhor plataforma disponível para o desenvolvimento de novos aplicativos web personalizados, devido a ser uma plataforma estável, simples, poderosa, dentre outros adjetivos usados para descrever o LAMP.  
  
  Dessa forma, o roteiro é baseado, inicialmente, em tabelas que possuem os endereços IP's de cada interface de rede.
  
# 2. Definições Iniciais

## 2.1. Tabelas de definições de domínio, com os nomes e os endereços IPs das VMs:

**CONFIGURAÇÃO das INTERFACES de REDE:**

|                 |                  |                    |
| ---             | ---              | ---                |
| IP de Subrede   | 10.9.14.0 / 24   | 192.168.14.16 / 29 |
| IP de Broadcast | 10.9.14.255 / 24 | 192.168.14.23 / 29 |

| Nome da VM   | WAN    | IP          | LAN    | IP            |
|   ---        | :---:  | :---:       | :---:  | :---:         |
| IP do GW:    | ens160 | 10.9.14.113 | ens192 | 192.168.14.17 |
| IP do SAMBA: | ens160 | 10.9.14.101 | ens192 | 192.168.14.18 |
| IP do NS1:   | ens160 | 10.9.14.114 | ens192 | 192.168.14.19 |
| IP do NS2:   | ens160 | 10.9.14.225 | ens192 | 192.168.14.20 |
| IP do WEB:   | ens160 | 10.9.14.215 | ens192 | 192.168.14.21 |
| IP do BD:    | ens160 | 10.9.14.216 | ens192 | 192.168.14.22 |

**DEFINIÇÃO de NOMES e DOMÍNIOS:**

| **VM**    | **DOMÍNIO(ZONA)**                | **grupo3.turma914.ifalara.local** |
|:--------- |----------------------------------|-----------------------------------|
| Aluno13   | FQDN DO GW:                      | gw.grupo3.turma914.ifalara.local  |
| Aluno01   | FQDN DO SAMBA:                   | smb.grupo3.turma914.ifalara.local |
| Aluno14   | FQDN DO NS1:                     | ns1.grupo3.turma914.ifalara.local |
| Aluno25   | FQDN DO NS2:                     | ns2.grupo3.turma914.ifalara.local |
| Grupo3vm1 | FQDN DO WEB:                     | www.grupo3.turma914.ifalara.local |
| Grupo3vm2 | FQDN DO BD:                      | bd.grupo3.turma914.ifalara.local  |


## 2.2. Edição dos hostnames com o nome de domínio no S.O. de cada MV:

Utilizando as infromações da tabela FQDN (tabela anterior) iremos mudar os nomes de cada VM. Para isso utilizaremos o comando ```sudo hostnamectl set-hostname nome.dominio```

Depois reinicie a máquina e verfique a mudança de nome com o comando ```hostname```

- VM 10.9.14.113 (gateway - gw):
```
$ sudo hostnamectl set-hostname gw.grupo3.turma914.ifalara.local
```

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/img1.jpg)

- VM 10.9.14.101 (samba - smb): 
```
$ sudo hostnamectl set-hostname smb.grupo3.turma914.ifalara.local
```

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/img2.jpg)

- VM 10.9.14.114 (nameServer1 - ns1):
```
sudo hostnamectl set-hostname ns1.grupo3.turma914.ifalara.local
```

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/img3.jpg)

- VM 10.9.14.225 (nameServer2 - ns2):
```
$ sudo hostnamectl set-hostname ns2.grupo3.turma914.ifalara.local
```

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/img4.jpg)

- VM 10.9.14.215 (web - www):
```
$ sudo hostnamectl set-hostname www.grupo3.turma914.ifalara.local
```

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/img5.jpg)

- VM 10.9.14.216 (banco - bd):
```
$ sudo hostnamectl set-hostname bd.grupo3.turma914.ifalara.local
```

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/img6.jpg)


## 2.3. A configuração de hardware utilizada em cada VM:

Essas configurações de hardware paracem logo no início, quando entramos na VM. Ao fazer login essas informações são exibidas, mostrando carga da CPU, porcentagem de memória, números de processo de execução, porcentagem de disco utilizada, número de usuários logados, ente outras. Veremos com as imagens a seguir as informações de cada máquina.

- VM do GW (10.9.14.113):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/hard1.jpg)

- VM do SAMBA (10.9.14.101):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/hard2.jpg)

- VM do NS1 (10.9.14.114):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/hard3.jpg)

- VM do NS2 (10.9.14.225):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/hard4.jpg)

- VM do WWW (10.9.14.215):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/hard5.jpg)

- VM do BD (10.9.14.216):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/hard6.jpg)

## 2.4. Criar usuários (nomes dos integrantes da equipe) em cada VM:

Para cada VM criamos 5 usuários que se referem a cada integrante do grupo, vamos mostrar apenas o exemplo da VM do SAMBA (10.9.14.101), mas se quiser observar em cada VM [clique aqui](https://github.com/clari34/Projeto_Redes_Oficial/tree/main/defini%C3%A7%C3%B5es%20iniciais/criar_users)!

Para criar um usuário usamos o comando ```sudo adduser nomeUsuario```

No nosso caso foi 5 usuários (Isabel, Jeycy, Clara, Dudha, Lavynia). Observe na imagem a seguir que esses usuários foram realmente criados, para isso utilize o comando ```getent passwd``` Esse comando mostrará todos os *users* da máquina.

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/criar_users/user2.jpg)


# 3. Implementação dos Serviços de Rede (cada serviço uma sessão)

## 3.1. Configuração do DNS Master (ns1) e DNS Slave (ns2): 

## 3.1.1. DNS MASTER

Iremos configurar como nameserver master a nossa máquina **ns1 de Ip 10.9.14.114**. Conforme a tabela de IPs configurada para este projeto.

- Antes de instalar o servidor DNS faça a atualização da máquina com o comando abaixo para que o serviço seja instalado em um máquina atualizada :)
```
sudo update
```

- Agora vamos instalar o BIND9 (que é o software de servidor de nomes), use o comando a seguir para instalá-lo:
```
sudo apt-get install bind9 dnsutils bind9-doc
```

- Depois verifique o funcionamento do serviço com o comando:

```
sudo systemctl status bind9
```

![img1](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img1.jpg)

Fizemos apenas a instalação, agora precisamos configurá-lo para que ele funcione de fato! Então vamos lá:

- Vamos entrar no diretório *bind* para isso use o comando ```ls -la /etc/bind```

  O que aparece na tela são os arquivos do *bind*. Observe esses aquivos *db*, as zonas são especificadas nesses arquivos.

  > EXPLICANDO: Arquivos *db* são bancos de dados de resolução de nomes, por exemplo, sua VM conhece o nome de uma máquina, mas não sabe o IP, o arquivo *db* resolverá isso! Cada zona deve ter seu próprio arquivo *db*. As zonas especificam o domínio e podem ser zonas de conversão direta (de nome para IP) ou zonas de conversão reversa (de IP para nome).

- Devemos criar uma pasta zones dentro do diretório *bind*. Para isso utilize o comando a seguir:

```
sudo mkdir /etc/bind/zones
```

Depois de executar o comando verifique se a pasta criada já está no diretório *bind* use o comando ```ls -la /etc/bind``` para isso.

![img2](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img2.jpg)

### ZONAS

Como já vimos as zonas especificam o domínio e podem ser zonas de conversão direta (de nome para IP) ou zonas de conversão reversa (de IP para nome). Agora vamos criar essas zonas!

- Para criar as zonas diretas devemos criar um arquivo para elas. 
 
Como sabemos as zonas são especificadas em arquivos *db*, sendo assim iremos criar um arquivo *db* para **zona direta**, use o comando a seguir:

```
sudo cp /etc/bind/db.empty /etc/bind/zones/db.grupo3.turma914.ifalara.local
```

*Observe que utilizamos depois do **db.** o nome de domínio que especificamos logo no começo do documento. Por isso é tão importante as definições da tabela no início, para não nos perdermos durante o processo!!!!*

E para criar um arquivo de **zona reversa** use o comando:

```
sudo cp /etc/bind/db.127 /etc/bind/zones/db.10.9.14.rev
```

*Observe que o prefixo de rede (10.9.14.) já está sendo implementado na zona, no arquivo de configuração você perceberá que não precisaremos usar o IP completo da rede apenas os últimos numeros.*

Para observar se os arquivos foram criados entre no diretório ```cd /etc/bind/zones``` e utilize o comando ```ls -la``` 

![img3](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img3.jpg)

*Os dois aqruivos **db** que criamos já possuem a estrutura que precisamos, ele é um diretório do tipo SOA, é um serviço autoridade.*

O *bind* ainda não está pronto, precisamos configurar os arquivos *db* criados.

> PARA SABER MAIS: O último ponto de *local.* - que deve estar sempre no arquivo *db* - significa que o DNS irá resolver apenas endereços internos, da rede local!

- Para configurar o arquivo de **zona direta** execute o comando

```
sudo nano db.grupo3.turma914.ifalara.local
```

![img4](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img4.jpg)

- Agora iremos configurar o arquivo de **zona reversa** para isso use o comando

```
sudo nano db.10.9.14.rev
```

![img5](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img5.jpg)

- Depois que configuramos esses dois arquivos devemos "avisar" para o arquivo *named.conf.local* (que é o arquivo de configuração do *bind*) que esses arquivos *db* existem. Use o comando 

```
sudo nano /etc/bind/named.conf.local
```

![img6](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img6.jpg)

Depois de salvar os arquivos devemos observar se a sintaxe está correta, para isso use o comando:

```
sudo named-checkconf
```

Se não retornar nenhuma mensagem é porque estão corretos!

Também recisamos verificar a sintaxe da zona, para isso você deve estar na pasta zones (```sudo /etc/bind/zones```), para verificar a sintaxe use o comando

Para **zona direta**:
```
sudo named-checkzone grupo3.turma914.ifalara.local db.grupo3.turma914.ifalara.local
```

Para **zona reversa**:
```
sudo named-checkzone 14.9.10.in-addr.arpa db.10.9.14.rev
```

Se não houver erros a saída é um "OK"

![img7](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img7.jpg)

Antes de reiniciar o serviço *bind* iremos mudar o arquivo *named* para que o nosso DNS resolva apenas IPv4, para isso use o comando 
```
sudo nano /etc/default/named
```

Basta que você adicione um **-4** entre as aspas.

![img8](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img8.jpg)

Usando esse comando ao executarmos o status do _bind_, a nossa zona irá aparecer no log da saída!

- Agora reinicie o *bind* usando o comando
```
sudo systemctl restart bind9
```

E depois veja o status do serviço com o comando 
```
sudo systemctl status bind9
```

![img9](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img9.jpg)

PRONTO!

DNS Master configurado!


## 3.1.2. DNS SLAVE

Fizemos a configuração do nosso DNS Master agora faremos as configurações do Slave. Ele será nosso servidor DNS secundário, como se fosse "uma cópia do Master", o que de fato ele é :)

De acordo com nossa tabela de IP, definida no início do projeto, **nossa máquina slave (ns2) é a de IP 10.9.14.225**

A configuração do *slave* é bem simples, começamos configurando o arquivo *yaml* da pasta */etc/netplan*, para que a máquina *slave* tenha o DNS Master como Server (o principal). Para isso entre na pasta (```cd /etc/netplan```) e use o comando a seguir para entrar no arquivo de configuração das interfaces:

```
sudo nano /etc/bind/named.conf.local
```

Depois use o comando abaixo para salvar o arquivo:

```
sudo netplan apply
```

*Se não houver erros o comando não responderá nada!*

Veja na imagem a seguir como ficou nosso arquivo *yaml*, apenas mudamos o endereço do nameserver na interface ens160, colocamos o nosso nome de domínio no *search*. E só :)

![img10](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img10.jpg)

Use o comando a seguir para ver a saída de resolução da *ens160*, observe que o IP do nosso DNS está como Sever, e o nome do nosso domínio também!

```
systemd-resolve --status ens160
```

![img11](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img11.jpg)

Nossa máquina slave já possui o DNS instalado, então apenas verificamos se ele está funcionando corretamente na máquina, usamos o comando a seguir para verificar:

```
sudo systemctl status bind9
```

*Se a sua máquina slave não possui o DNS instalado, use o comando ```sudo apt-get install bind9 dnsutils bind9-doc```*

Agora vamos alterar o arquivo de configuração do bind, para isso use o comando a seguir:
```
sudo nano /etc/default/named
```

Veja as informações do arquivo na imagem a seguir:

![img12](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img12.jpg)

Depois das alterações reinicie o serviço
```
sudo systemctl restart bind9
```

Depois veja se está funcionando:
```
sudo systemctl status bind9
```

![img13](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img13.jpg)

Percebeu que nesse processo do *slave* não precisamos alterar os arquivo *db*? Não fizemos isso porque o *slave* faz o buckup dos arquivos do *master*. Observe na imagem anterior o nome vermelho *dupimg master file*, isso mostra que o *DNS Slave* está carregando os arquivos do *DNS Master*.

PRONTO!

DNS Slave configurado!


## 3.2. CONFIGURAÇÃO DAS INTERFACES DOS HOSTS PARA USAR O DNS

Para configurar a a interface do host local para que o servidor DNS consultado seja o que cofiguramos devemos entrar na pasta */etc/netplan* com o comando ```cd /etc/netplan```, lá existe o arquivo de configuração das interfaces, o *yaml*, iremos modificá-lo. Para modificar o arquivo execute o comando 
´´´
sudo nano /etc/netpan/00-installer-config-yaml
´´´

*Lembre-se que não pode usar TAB*

Para que possamos usar o BIND como nosso servidor DNS, deveremos alterar o ***endereço nameserver*** da interface ens160. 

Altere o *addresses* colocando o IP do seu servidor DNS e na área *search* coloque nos cochetes [] o nome de domínio que você configurou!

*Para sair do arquico aperte ctrl+x e y.*

**Para este exemplos iremos configurar a VM ns1 de IP 10.9.14.114**

Para salvar o arquivo use o comando
```
sudo netplan apply
``` 

Pronto agora sua máquina tem o servidor DNS próprio!!! :)

![img14](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img14.jpg)

Para visualizarmos o DNS Server utilizado pela interface ens160, usamos o comando abaixo:
```
systemd-resolve --status ens160 
```

O comando devolveu o IP do nosso DNS e o nome de domínio!!! :)

![img15](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img15.jpg)


A seguir as imagens das configurações do arquivo *yaml* de todas as nossas VMs:

- VM do GW (10.9.14.113):

![img16](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img16.jpg)

- VM do SAMBA (10.9.14.101):

![img17](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img17.jpg)

- VM do NS1 (10.9.14.114):

![img14](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img14.jpg)

- VM do NS2 (10.9.14.225):

![img10](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img10.jpg)

- VM do WWW (10.9.14.215):

![img18](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img18.jpg)

- VM do BD (10.9.14.216):

![img19](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/img19.jpg)


## 3.3. TESTES do DNS

Iremos fazer os testes de *dig, nslookup e pin* usando tanto a zona direta quanto a reversa, e forçando usar o nosso DNS Master e Slave:

### 3.3.1. DIG 

- **ZONA DIRETA**

    - VM do GW (10.9.14.113) @ns1:

     ![dig1_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig1_1_dirt.jpg)

     - VM do SAMBA (10.9.14.101) @ns1:

     ![dig2_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig2_1_dirt.jpg)

     - VM do NS1 (10.9.14.114) @ns1:

     ![dig3_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig3_1_dirt.jpg)

     - VM do NS2 (10.9.14.225) @ns1:

     ![dig4_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig4_1_dirt.jpg)

     - VM do WWW (10.9.14.215) @ns1:

     ![dig5_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig5_1_dirt.jpg)

     - VM do BD (10.9.14.216) @ns1:

     ![dig6_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig6_1_dirt.jpg)


     - VM do GW (10.9.14.113) @ns2:

     ![dig1_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig1_2_dirt.jpg)

     - VM do SAMBA (10.9.14.101) @ns2:

     ![dig2_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig2_2_dirt.jpg)

     - VM do NS1 (10.9.14.114) @ns2:

     ![dig3_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig3_2_dirt.jpg)

     - VM do NS2 (10.9.14.225) @ns2:

     ![dig4_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig4_2_dirt.jpg)

     - VM do WWW (10.9.14.215) @ns2:

     ![dig5_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig5_2_dirt.jpg)

     - VM do BD (10.9.14.216) @ns2:

     ![dig6_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/di6_2_dirt.jpg)


- **ZONA REVERSA**

     - VM do GW (10.9.14.113) @ns1:

     ![dig1_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig1_1_rev.jpg)

     - VM do SAMBA (10.9.14.101) @ns1:

     ![dig2_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig2_1_rev.jpg)

     - VM do NS1 (10.9.14.114) @ns1:

     ![dig3_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig3_1_rev.jpg)

     - VM do NS2 (10.9.14.225) @ns1:

     ![dig4_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig4_1_rev.jpg)

     - VM do WWW (10.9.14.215) @ns1:

     ![dig5_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig5_1_rev.jpg)

     - VM do BD (10.9.14.216) @ns1:

     ![dig6_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig6_1_rev.jpg)


     - VM do GW (10.9.14.113) @ns2:

     ![dig1_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig1_2_rev.jpg)

     - VM do SAMBA (10.9.14.101) @ns2:

     ![dig2_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig2_2_rev.jpg)

     - VM do NS1 (10.9.14.114) @ns2:

     ![dig3_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig3_2_rev.jpg)

     - VM do NS2 (10.9.14.225) @ns2:

     ![dig4_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig4_2_rev.jpg)

     - VM do WWW (10.9.14.215) @ns2:

     ![dig5_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig5_2_rev.jpg)

     - VM do BD (10.9.14.216) @ns2:

     ![dig6_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/dig6_2_rev.jpg)


### 3.3.2. NSLOOKUP

- **ZONA DIRETA**

    - VM do GW (10.9.14.113) ns1:

     ![kup1_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup1_1_dirt.jpg)

     - VM do SAMBA (10.9.14.101) ns1:

     ![kup2_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup2_1_dirt.jpg)

     - VM do NS1 (10.9.14.114) ns1:

     ![kup3_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup3_1_dirt.jpg)

     - VM do NS2 (10.9.14.225) ns1:

     ![kup4_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup4_1_dirt.jpg)

     - VM do WWW (10.9.14.215) ns1:

     ![kup5_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup5_1_dirt.jpg)

     - VM do BD (10.9.14.216) ns1:

     ![kup6_1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup6_1_dirt.jpg)


     - VM do GW (10.9.14.113) ns2:

     ![kup1_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup1_2_dirt.jpg)

     - VM do SAMBA (10.9.14.101) ns2:

     ![kup2_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup2_2_dirt.jpg)

     - VM do NS1 (10.9.14.114) ns2:

     ![kup3_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup3_2_dirt.jpg)

     - VM do NS2 (10.9.14.225) ns2:

     ![kup4_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup4_2_dirt.jpg)

     - VM do WWW (10.9.14.215) ns2:

     ![kup5_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup5_2_dirt.jpg)

     - VM do BD (10.9.14.216) ns2:

     ![kup6_2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup6_2_dirt.jpg)


- **ZONA REVERSA**

     - VM do GW (10.9.14.113) ns1:

     ![kup1_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup1_1_rev.jpg)

     - VM do SAMBA (10.9.14.101) ns1:

     ![kup2_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup2_1_rev.jpg)

     - VM do NS1 (10.9.14.114) ns1:

     ![kup3_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup3_1_rev.jpg)

     - VM do NS2 (10.9.14.225) ns1:

     ![kup4_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup4_1_rev.jpg)

     - VM do WWW (10.9.14.215) ns1:

     ![kup5_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup5_1_rev.jpg)

     - VM do BD (10.9.14.216) ns1:

     ![kup6_1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup6_1_rev.jpg)


     - VM do GW (10.9.14.113) ns2:

     ![kup1_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup1_2_rev.jpg)

     - VM do SAMBA (10.9.14.101) ns2:

     ![kup2_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup2_2_rev.jpg)

     - VM do NS1 (10.9.14.114) ns2:

     ![kup3_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup3_2_rev.jpg)

     - VM do NS2 (10.9.14.225) ns2:

     ![kup4_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup4_2_rev.jpg)

     - VM do WWW (10.9.14.215) ns2:

     ![kup5_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup5_2_rev.jpg)

     - VM do BD (10.9.14.216) ns2:

     ![kup6_2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/kup6_2_rev.jpg)


### 3.3.3. PING 

- **ZONA DIRETA**

    - VM do GW (10.9.14.113):

     ![ping1_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping1_dirt.jpg)

     - VM do SAMBA (10.9.14.101):

     ![ping2_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping2_dirt.jpg)

     - VM do NS1 (10.9.14.114):

     ![ping3_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping3_dirt.jpg)

     - VM do NS2 (10.9.14.225):

     ![ping4_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping4_dirt.jpg)

     - VM do WWW (10.9.14.215):

     ![ping5_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping5_dirt.jpg)

     - VM do BD (10.9.14.216):

     ![ping6_dirt](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping6_dirt.jpg)


-**ZONA REVERSA**

     Nesses exemplos utilizaremos o IP da interface *ens192*

     - VM do GW (192.168.14.17):

     ![ping1_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping1_rev.jpg)

     - VM do SAMBA (192.168.14.18):

     ![ping2_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping2_rev.jpg)

     - VM do NS1 (192.168.14.19):

     ![ping3_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping3_rev.jpg)

     - VM do NS2 (192.168.14.20):

     ![ping4_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping4_rev.jpg)

     - VM do WWW (192.168.14.21):

     ![ping5_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping5_rev.jpg)

     - VM do BD (192.168.14.22):

     ![ping6_rev](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/dns/testes/ping6_rev.jpg)


## 3.2. Instalação do SAMBA: 

*Nesse roteiro o servidor samba foi configurado na VM (10.9.14.101)* 

## 3.3. Implementação do servidor Web LAMP:

## 3.4. Instalação do Gateway Server NAT:

*Nesse roteiro o servidor Gateway como NAT foi configurado na VM (10.9.14.113)*

## 3.5. Configuração da rede interna LAN nas VMs:


# 4. Considerações Finais:
    Esse repositório será atualizado de acordo com o andamento do projeto. 
    
# 5. Referências:
