Cen�rio
=================================================================================

Ansible
Client

CentOS 7 - Ansible
=================================================================================

## Instala��o

1- Atualizar sistema

	yum update

2- Instalando Ansible

	yum install ansible

## Gerenciando Servidores

1- Faaer backup dos arquivos do arquivo de hosts de invet�rio do Ansible 
"/etc/ansible/hosts". Este arquivo � usado para agrupar seus servidores e sua 
localiza��o.

	cp /etc/ansible/hosts /etc/ansible/hosts.original

2- Definindo servidores que ser�o gerenciados

		vi /etc/ansible/hosts

			[webservers]
			10.10.10.11

	# OBS.: caso deseje alterar o local arquivo de hosts do ansible execute o 
	seguinte comando

		export ANSIBLE_HOSTS=/root/example_ansible_host
	
	# Ou voc� pode expecificar o local de hosts ao executar comandos do ansible

		ansible all --inventory-file=/root/example_ansible_hosts

## Conectando aos Servidores

1- Gerando chave no N� Master

		ssh-keygen

	# OBS.: Ser�o gerados dois arquivos, um contndo a chave 
	# privada(~/.ssh/id_rsa) e outro a chave publica(~/.ssh/id_rsa.pub)

2- Copie sua chave p�blica para os servidores

	ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.10.10.11

## Executando comandos

1- Executando comando em todos os hosts.

		ansible all -m ping 
		ansible 10.10.10.11 -m ping

	# OBS.: A sa�da que temos do Ansible � JSON informando se a tarefa fez 
	# alguma altera��o e qual foi o resultado

	# all: usa todas os servidores definidos a partir do arquivo de invent�rio.
	# -m ping: usa o m�dulo ping, que executa o comando e retorna os resultados.

2- Executando comando de instala��o

		ansible 10.10.10.11 -m apt -a 'name=apache2'

## M�dulos

1- O ansible utiliza "m�dulos" para relizar a maioria de suas tarefas, como 
instalar softwares, atualiar pacotes, copiar arquivos etc.


## PlayBook

1- Os Playbooks criam v�rias tarefas(TASKS) e oferecem algumas funcionalidades 
mais avan�adas. Abaixo um playbook de instala��o de um ambiente LAMP.

		vi webservers.yml

			---
			- hosts: webservers
			  user: root

			  tasks:

			    ## LAMP e outros recursos

			    - name: General | Instala��o de pacotes b�sicos
			      action: apt pkg={{ item }} state=installed
			      with_items:
			        - php7.0
			        - apache2
			        - mysql-server
			        - mysql-client
			        - php7.0-mysql

			    ## Apache2 ##			

			    - name: Apache2 | Habilitar m�dulos
			      action: command a2enmod rewrite vhost_alias

			    ## Restart de Servi�os

			    - name: Restart Apache
			      action: service name=apache2 state=restarted
	
2- Para executar o playbook execute

	ansible-playbook webservers.yml

