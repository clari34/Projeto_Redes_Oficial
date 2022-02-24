# Instituto Federal de Alagoas - Campus Arapiraca
### Prof. Alaelson Jatobá

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

## 1. Introdução

  O seguinte trabalho consiste na configuração dos serviços DNS Master e Slave através de um terminal linux, com o objetivo de criar um ambiente de rede virtualizaado com seis máquinas virrtuais.

  O DNS (Domain Name Service) é um sistema de base de dados distribuído que traduz nomes para endereços IP e que permite que um URL como, por exemplo, www.algumacoisa.edu.br seja traduzido para o endereço 200.131.62.17. Além disso, a configuração split DNS serve para atender de modo diferenciado a diferentes segmentos de rede, podendo responder à consulta de um URL com endereços IP diferentes ou traduzindo diferentes conjuntos de nomes. Para a configuração de um sistema, a zona Slave é utilizada quando o usuário tem dois ou mais servidores de DNS, fazendo a replicação da zona a partir da zona Master que, por sua vez, é onde se fazem as modificações referentes ao domínio e acrescentando as definições para a trasferência de dados paa o servidor Slave. 
  
  Dessa forma, o roteiro é baseado, inicialmente, em tabelas que possuem os endereços IP's de cada interface de rede.
  
## 2. Definições Iniciais

### TABELAS DE DEFINIÇÕES DE IPs E NOMES PARA TODAS AS VMs:

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

## 3. Implementação dos Serviços de Rede (Cada serviço uma sessão)

### A CONFIGURAÇÃO DE HARDWARE UTILIZADA EM CADA MV:

|                 _GW_                 |
|:------------------------------------:|
| System load: 0.0                     |
| Processes: 198                       |
| Usage of/: 37.4% of 18.57GB          |
| Users logged in: 0                   |
| Memory usage: 22%                    |
| IPv4 address for ens160: 10.9.14.114 |
| Swap usage:0%                        |


## Considerações Finais
