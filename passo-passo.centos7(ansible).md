Cenário
=================================================================================

Ansible
Client

CentOS 7 - Ansible
=================================================================================

## Instalação

1- Atualizar sistema

	yum update

2- Instalando Ansible

	yum install ansible

## Gerenciando Servidores

1- Faaer backup dos arquivos do arquivo de hosts de invetário do Ansible 
"/etc/ansible/hosts". Este arquivo é usado para agrupar seus servidores e sua 
localização.

	cp /etc/ansible/hosts /etc/ansible/hosts.original

2- Definindo servidores que serão gerenciados

		vi /etc/ansible/hosts

			[webservers]
			10.10.10.11

	# OBS.: caso deseje alterar o local arquivo de hosts do ansible execute o 
	seguinte comando

		export ANSIBLE_HOSTS=/root/example_ansible_host
	
	# Ou você pode expecificar o local de hosts ao executar comandos do ansible

		ansible all --inventory-file=/root/example_ansible_hosts

## Conectando aos Servidores

1- Gerando chave no Nó Master

		ssh-keygen

	# OBS.: Serão gerados dois arquivos, um contndo a chave 
	# privada(~/.ssh/id_rsa) e outro a chave publica(~/.ssh/id_rsa.pub)

2- Copie sua chave pública para os servidores

	ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.10.10.11

## Executando comandos

1- Executando comando em todos os hosts.

		ansible all -m ping 
		ansible 10.10.10.11 -m ping

	# OBS.: A saída que temos do Ansible é JSON informando se a tarefa fez 
	# alguma alteração e qual foi o resultado

	# all: usa todas os servidores definidos a partir do arquivo de inventário.
	# -m ping: usa o módulo ping, que executa o comando e retorna os resultados.

2- Executando comando de instalação

		ansible 10.10.10.11 -m apt -a 'name=apache2'

## Módulos

1- O ansible utiliza "módulos" para relizar a maioria de suas tarefas, como 
instalar softwares, atualiar pacotes, copiar arquivos etc.


## PlayBook

1- Os Playbooks criam várias tarefas(TASKS) e oferecem algumas funcionalidades 
mais avançadas. Abaixo um playbook de instalação de um ambiente LAMP.

		vi webservers.yml

			---
			- hosts: webservers
			  user: root

			  tasks:

			    ## LAMP e outros recursos

			    - name: General | Instalação de pacotes básicos
			      action: apt pkg={{ item }} state=installed
			      with_items:
			        - php7.0
			        - apache2
			        - mysql-server
			        - mysql-client
			        - php7.0-mysql

			    ## Apache2 ##			

			    - name: Apache2 | Habilitar módulos
			      action: command a2enmod rewrite vhost_alias

			    ## Restart de Serviços

			    - name: Restart Apache
			      action: service name=apache2 state=restarted
	
2- Para executar o playbook execute

	ansible-playbook webservers.yml

