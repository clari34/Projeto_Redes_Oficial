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
     - [ ] 3.1. Configuração do DNS Master (ns1) e DNS Slave (ns2);
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
| IP do SAMBA: | ens160 | 10.9.14.114 | ens192 | 192.168.14.18 |
| IP do NS1:   | ens160 | 10.9.14.101 | ens192 | 192.168.14.19 |
| IP do NS2:   | ens160 | 10.9.14.225 | ens192 | 192.168.14.20 |
| IP do WEB:   | ens160 | 10.9.14.215 | ens192 | 192.168.14.21 |
| IP do BD:    | ens160 | 10.9.14.216 | ens192 | 192.168.14.22 |

**DEFINIÇÃO de NOMES e DOMÍNIOS:**

| **VM**    | **DOMÍNIO(ZONA)**                | **grupo3.turma914.ifalara.local** |
|:--------- |----------------------------------|-----------------------------------|
| Aluno13   | FQDN DO GW:                      | gw.grupo3.turma914.ifalara.local  |
| Aluno14   | FQDN DO SAMBA:                   | smb.grupo3.turma914.ifalara.local |
| Aluno01   | FQDN DO NS1:                     | ns1.grupo3.turma914.ifalara.local |
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

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/host1.jpg)

- VM 10.9.14.114 (samba - smb): 
```
$ sudo hostnamectl set-hostname smb.grupo3.turma914.ifalara.local
```

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/host2.jpg)

- VM 10.9.14.101 (nameServer1 - ns1):
```
sudo hostnamectl set-hostname ns1.grupo3.turma914.ifalara.local
```

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/host3.jpg)

- VM 10.9.14.225 (nameServer2 - ns2):
```
$ sudo hostnamectl set-hostname ns2.grupo3.turma914.ifalara.local
```

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/host4.jpg)

- VM 10.9.14.215 (web - www):
```
$ sudo hostnamectl set-hostname www.grupo3.turma914.ifalara.local
```

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/host5.jpg)

- VM 10.9.14.216 (banco - bd):
```
$ sudo hostnamectl set-hostname bd.grupo3.turma914.ifalara.local
```

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hostnamectl/host6.jpg)


## 2.3. A configuração de hardware utilizada em cada VM:

Essas configurações de hardware paracem logo no início, quando entramos na VM. Ao fazer login essas informações são exibidas, mostrando carga da CPU, porcentagem de memória, números de processo de execução, porcentagem de disco utilizada, número de usuários logados, ente outras. Veremos com as imagens a seguir as informações de cada máquina.

- VM do GW (10.9.14.113):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/img1.jpg)

- VM do SAMBA (10.9.14.114):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/img2.jpg)

- VM do NS1 (10.9.14.101):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/img3.jpg)

- VM do NS2 (10.9.14.225):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/img4.jpg)

- VM do WWW (10.9.14.215):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/img5.jpg)

- VM do BD (10.9.14.216):

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/hardware/img6.jpg)

## 2.4. Criar usuários (nomes dos integrantes da equipe) em cada VM:

Para cada VM criamos 5 usuários que se referem a cada integrante do grupo, vamos mostrar apenas o exemplo da VM do SAMBA (10.9.14.114), mas se quiser observar em cada VM [clique aqui](https://github.com/clari34/Projeto_Redes_Oficial/tree/main/defini%C3%A7%C3%B5es%20iniciais/criar_users)!

Para criar um usuário usamos o comando ```sudo adduser nomeUsuario```

No nosso caso foi 5 usuários (Isabel, Jeycy, Clara, Dudha, Lavynia). Observe na imagem a seguir que esses usuários foram realmente criados, para isso utilize o comando ```getent passwd``` Esse comando mostrará todos os *users* da máquina.

![](https://github.com/clari34/Projeto_Redes_Oficial/blob/main/defini%C3%A7%C3%B5es%20iniciais/criar_users/user2.jpg)


# 3. Implementação dos Serviços de Rede (cada serviço uma sessão)

## 3.1. Configuração do DNS Master (ns1) e DNS Slave (ns2): 

*Nesse roteiro o DNS Master foi configurado na VM (ns1 - 10.9.14.101) e o DNS Slave foi configurado na VM (ns2 - 10.9.14.113)*

## 3.2. Instalação do SAMBA: 

*Nesse roteiro o servidor samba foi configurado na VM (10.9.14.225)* 

## 3.3. Implementação do servidor Web LAMP:

## 3.4. Instalação do Gateway Server NAT:

*Nesse roteiro o servidor Gateway como NAT foi configurado na VM (10.9.14.113)*

## 3.5. Configuração da rede interna LAN nas VMs:


# 4. Considerações Finais:
    Esse repositório será atualizado de acordo com o andamento do projeto. 
    
# 5. Referências:
