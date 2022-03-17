# Instituto Federal de Alagoas - Campus Arapiraca
### Disciplina de Infraestrutura e Serviços de Rede
### Prof. Alaelson Jatobá
### Turma 914

#### ALUNAS :
|          |           **GRUPO 3**           |
|:--------:|:-------------------------------:|
|          |                                 |
|          | _NOME DOS INTEGRANTES:_         |
| ALUNA 1: | Jeycy Karollaynny Silva Almeida |
| ALUNA 2: | Marya Eduardha Freitas Pereira  |
| ALUNA 3: | Ana Clara Silva Nunes           |
| ALUNA 4: | Isabel Vitória da Silva Gama    |
| ALUNA 5: | Lavynia Farias Santos           |
|          |                                 |


## Sumário

- [X] 1. Introdução;
- [ ] 2. Definições Iniciais:
     - [X] 2.1. A configuração de hardware utilizada em cada VM;
     - [X] 2.2. Tabelas de definições de domínio, com os nomes e os endereços IPs das VMs;
     - [ ] 2.3. Edição dos hostnames com o nome de domínio (tabela anterior) no S.O. de cada MV;
     - [ ] 2.4. Criar usuários (nomes dos integrantes da equipe) em cada VM;
- [ ] 3. Implementação dos Serviços de Rede (cada serviço uma sessão):
     - [ ] 3.1. Instalação do Gateway Server NAT;
     - [ ] 3.2. Instalação do SAMBA;
     - [ ] 3.3. Configuração da rede interna LAN nas VMs;
     - [ ] 3.4. Configuração do DNS Master (ns1) e DNS Slave (ns2);
     - [ ] 3.5. Implementação do servidor Web LAMP;
- [ ] 4. Considerações Finais; 

## 1. Introdução

  O seguinte trabalho consiste na configuração dos serviços DNS Master e Slave através de um terminal linux, com o objetivo de criar um ambiente de rede virtualizaado com seis máquinas virtuais.

  O DNS (Domain Name Service) é um sistema de base de dados distribuído que traduz nomes para endereços IP e que permite que um URL como, por exemplo, www. algumaacoisa.edu.br seja traduzido para o endereço 200.131.62.17. Além disso, a configuração split DNS serve para atender de modo diferenciado a diferentes segmentos de rede, podendo responder à consulta de um URL com endereços IP diferentes ou traduzindo diferentes conjuntos de nomes. Para a configuração de um sistema, a zona Slave é utilizada quando o usuário tem dois ou mais servidores de DNS, fazendo a replicação da zona a partir da zona Master que, por sua vez, é onde se fazem as modificações referentes ao domínio e acrescentando as definições para a trasferência de dados paa o servidor Slave.

  Já o Samba consiste, basicamente, em um serviço e um conjunto de ferramentas que possibilitam a conexão de máquinas Windows e Linux, ou seja, ele permite que redes heterogêneas se comuniquem entre si, através do protocolo SMB (Server Message Block)/CIFS (Common Internet File System). Já o DNS (Domain Name System) é o sistema de nomes de domínio da internet, ou seja, é ele que transforma o endereço IP, geralmente confuso para a maioria das pessoas, no nome do site, tornando-o de mais fácil acesso e compreensão para quem não tem ligação com o meio da informática.
  
  O Gateway NAT (Network Address Translation) consiste em um serviço de conversão de endereços de rede que serve para que as instâncias em uma sub-rede privada possam se conectar a serviços fora da VPC, mas sem permitir que os serviço externos iniciem uma conexão com essas instâncias. Esse serviço substitui o endereço IP de origem das instâncias pelo endereço IP do gateway e, com isso, o dispositivo de NAT converte os endereços de volta para o endereço IP de origem inicial.
  
  Já o Web LAMP é uma combinação de softwares de código aberto, que foi uma das primeiras plataformas de código aberto para a rede, e continua sendo uma das maneiras mais comuns de fornecer aplicações web. Além disso, sua nomenclatura é um acrônimo reunindo as iniciais de seus quatro componentes base: Linux, Apache, MySQL e PHP e é considerado por muitos como a melhor plataforma disponível para o desenvolvimento de novos aplicativos web personalizados, devido a ser uma plataforma estável, simples, poderosa, dentre outros adjetivos usados para descrever o LAMP.  
  
  Dessa forma, o roteiro é baseado, inicialmente, em tabelas que possuem os endereços IP's de cada interface de rede.
  
## 2. Definições Iniciais


### 2.1. A configuração de hardware utilizada em cada VM:

|               **GW :**               |               **SMB :**              |
|:------------------------------------:|:------------------------------------:|
| System load: 0.0                     | System load:0.0                      |
| Processes: 198                       | Processes: 211                       |
| Usage of/: 37.4% of 18.57GB          | Usage of /: 38.5% of 18.57GB         |
| Users logged in: 0                   | Users logged in: 0                   |
| Memory usage: 22%                    | Memory usage: 27%                    |
| IPv4 address for ens160: 10.9.14.114 | IPv4 address for ens160: 10.9.14.225 |
| Swap usage:0%                        | Swap usage: 0%                       |
|               **NS1 :**              |               **NS2 :**              |
| System load: 0.0                     | System load: 0.0                     |
| Processes: 204                       | Processes: 204                       |
| Usage of /: 38.4% of 18.57GB         | Usage of /: 39.1% of 18.57GB         |
| Users logged in: 0                   | Users logged in: 1                   |
| Memory usage: 25%                    | Memory usage: 24%                    |
| IPv4 address for ens160: 10.9.14.101 | IPv4 address for ens160: 10.9.14.113 |
| Swap usage: 0%                       | Swap usage: 0%                       |


### 2.2. Tabelas de definições de domínio, com os nomes e os endereços IPs das VMs:

|                    |         | **CONFIGURAÇÕES DAS INTERFACES DE REDE:** |         |                  |
|--------------------|---------|-------------------------------------------|---------|------------------|
|                    |         |                                           |         |                  |
| _IP DA SUBREDE:_   |         | 10.9.14.0/24                              |         | 192.168.14.16/29 |
| _IP DE BROADCAST:_ |         | 10.9.14.255/24                            |         | 192.168.14.23    |
| _IP DO GW:_        | ENS 160 | 10.9.14.114                               | ENS 192 | 192.168.14.17    |
| _IP DO SAMBA:_     | ENS 160 | 10.9.14.225                               | ENS 192 | 192.168.14.18    |
| _IP DO NS1:_       | ENS 160 | 10.9.14.101                               | ENS 192 | 192.168.14.19    |
| _IP DO NS2:_       | ENS 160 | 10.9.14.113                               | ENS 192 | 192.168.14.20    |
| _IP DO WEB_        | ENS 160 |                                           | ENS 192 | 192.168.14.21    |
| _IP DO BD_         | ENS 160 |                                           | ENS 192 | 192.168.14.22    |


|           | **DEFINIÇÃO DE NOMES E DOMÍNIO** |                                   |
|:---------:|----------------------------------|-----------------------------------|
| _VM_      | _DOMÍNIO(ZONA)_                  | _grupo3.turma914.ifalara.local_   |
| Aluno14   | FQDN DO GW:                      | gw.grupo3.turma914.ifalara.local  |
| Aluno25   | FQDN DO SAMBA:                   | smb.grupo3.turma914.ifalara.local |
| Aluno01   | FQDN DO NS1:                     | ns1.grupo3.turma914.ifalara.local |
| Aluno13   | FQDN DO NS2:                     | ns2.grupo3.turma914.ifalara.local |
| Grupo3vm1 | FQDN DO WEB:                     | www.grupo3.turma914.ifalara.local |
| Grupo3vm2 | FQDN DO BD:                      | bd.grupo3.turma914.ifalara.local  |


### 2.3. Edição dos hostnames com o nome de domínio (tabela anterior) no S.O. de cada MV:

- VM 10.9.14.225 (samba - smb): 
```
$ sudo hostnamectl set-hostname smb.grupo3.turma914.ifalara.local
```

- VM 10.9.14.114 (gateway - gw):
```
$ sudo hostnamectl set-hostname gw.grupo3.turma914.ifalara.local
```

- VM 10.9.14.101 (nameServer1 - ns1):
```
sudo hostnamectl set-hostname ns1.grupo3.turma914.ifalara.local
```

- VM 10.9.14.113 (nameServer2 - ns2):
```
$ sudo hostnamectl set-hostname ns2.grupo3.turma914.ifalara.local
```

### 2.4. Criar usuários (nomes dos integrantes da equipe) em cada VM;

## 3. Implementação dos Serviços de Rede (cada serviço uma sessão)

### **ROTEIRO:** 
### 3.1. Instalação do Gateway Server NAT:

*Nesse roteiro o servidor Gateway como NAT foi configurado na VM (10.9.14.114)*

### 3.2. Instalação do SAMBA: 

*Nesse roteiro o servidor samba foi configurado na VM (10.9.14.225)*

- Utilize as informações das tabelas para modificar o arquivo por meio do comando $sudo nano /etc/netplan/00-installer-config.yaml : 
network:
  ethernets:
    ens160:
      dhcp4: false
      addresses: [10.9.14.225/24]
      gateway4: 10.9.14.1
      nameservers:
         addresses:
           - 10.9.14.225
         search: [smb.grupo3.turma914.ifalara.local]
    ens192:
      addresses: [192.168.0.194/29]
  version: 2

- Utilize Ctrl+x para salvar e execute $ sudo netplan apply para ativar as configurações

- Verifique o funcionamento do gateway($ route -n), e observe se o IP do mesmo é devolvido

- Instale o servidor Samba($ sudo apt-get install samba)

- Verifique se o arquivo já está na sua máquina($ whereis samba) e depois olhe se ela está ativa($ sudo systemctl status smbd)
smbd.service - Samba SMB Daemon
     Loaded: loaded (/lib/systemd/system/smbd.service; enabled; vendor preset: enabled)
     Active: *active* (running) since Tue 2022-02-22 19:59:02 

- Verifique o funcionamento das portas($ netstat -an | grep LISTEN)

- Realize o backup por meio do comando $ sudo cp /etc/samba/smb.conf{, .backup}

- 

### 3.3. Configuração da rede interna LAN nas VMs;

### 3.4. Configuração do DNS Master (ns1) e DNS Slave (ns2): 

*Nesse roteiro o DNS Master foi configurado na VM (ns1 - 10.9.14.101) e o DNS Slave foi configurado na VM (ns2 - 10.9.14.113)*

### 3.5. Implementação do servidor Web LAMP:

## 4. Considerações Finais:
    Esse repositório será atualizado de acordo com o andamento do projeto. 
