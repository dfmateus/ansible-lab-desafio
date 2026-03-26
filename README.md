# 🚀 Ansible Lab Desafio: Automation Workshop (Modo Local + Link Simbólico)

Bem-vindo ao repositório oficial do desafio prático de Ansible! Este ambiente foi projetado para simular um cenário real de automação utilizando o **Ansible Automation Platform (AAP)** em um ambiente isolado.

## 📊 Topologia do Laboratório
Abaixo, a representação visual dos **Targets (Alvos)** que iremos automatizar neste workshop:

<p align="center">
  <img width="604" height="297" alt="Ansible Lab Topology" src="https://github.com/user-attachments/assets/5a14883c-6e87-40ee-9872-c81b8e63126c">
</p>

* **Control Node:** Ansible Automation Platform (AAP).
* **Managed Nodes:** 3 instâncias RHEL (Red Hat Enterprise Linux).

---

## 🛠️ Preparação Inicial (Obrigatório)

Para facilitar o desenvolvimento, vamos criar um **Link Simbólico**. Isso fará com que sua pasta de trabalho no VS Code seja "espelhada" automaticamente para o diretório de projetos do AAP.

### 1. Criar sua Pasta de Desenvolvimento
No terminal do seu VS Code, crie a pasta raiz do projeto (substitua `seu-nome` pelo seu identificador, ex: `goku`):
~~~bash
mkdir -p ~/ansible-workshop-desafio-seu-nome
cd ~/ansible-workshop-desafio-seu-nome
~~~

### 2. Criar o Link Simbólico com o AAP
Execute o comando abaixo para conectar sua pasta de usuário ao diretório que o AAP monitora:
~~~bash
# Comando para espelhar sua pasta (Ajuste o 'seu-nome' no final do caminho de destino)
sudo ln -s ~/ansible-workshop-desafio-seu-nome /home/ec2-user/aap/controller/data/projects/ansible-workshop-desafio-seu-nome

# Garanta as permissões de acesso para o container do AAP
sudo chmod -R 777 ~/ansible-workshop-desafio-seu-nome
~~~

### 3. Criar Arquivos de Orquestração (Raiz)
Crie os playbooks principais na raiz da sua pasta de trabalho:
~~~bash
touch debug_facts.yml health_check_run.yml website_run.yml
~~~

### 4. Criar as Roles Profissionalmente
Utilizaremos o `ansible-galaxy` para gerar a estrutura correta. Execute dentro da pasta do projeto:
~~~bash
mkdir roles
ansible-galaxy init roles/webserver
ansible-galaxy init roles/loadbalancer
ansible-galaxy init roles/health_check
~~~

---

## 🎯 Escolha seu Desafio
👉 [**Ir para: Desafio 1 - Auditoria (Health Check)**](docs/instrucoes-desafio-1.md)
👉 [**Ir para: Desafio 2 - Alta Disponibilidade (Web & LB)**](docs/instrucoes-desafio-2.md)

---

## 📌 **_Instrutor_** 📌

👤 **Diego Felipe Mateus**

* E-mail: [dfmateus@hotmail.com](mailto:dfmateus@hotmail.com)
* Github: [dfmateus](https://github.com/dfmateus)
* LinkedIn: [dfmateus-LinkedIn](https://www.linkedin.com/in/dfmateus/)

---

## 📝 **_License_** 📝

[Apache License 2.0](http://www.apache.org/licenses/)