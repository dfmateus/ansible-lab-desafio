# 🚀 Ansible Lab Desafio: Automation Workshop

Bem-vindo ao desafio prático de Ansible! Este ambiente utiliza o **Ansible Automation Platform (AAP)** integrado ao **GitHub** para simular um fluxo real de GitOps.

## 📊 Topologia do Laboratório
Abaixo, a representação visual dos **Targets (Alvos)** que iremos automatizar neste workshop:

<p align="center">
  <img width="604" height="297" alt="Ansible Lab Topology" src="https://github.com/user-attachments/assets/5a14883c-6e87-40ee-9872-c81b8e63126c">
</p>

* **Control Node:** Ansible Automation Platform (AAP).
* **Managed Nodes:** 3 instâncias RHEL (Red Hat Enterprise Linux).

---

## 🛠️ Preparação Inicial (Obrigatório)

Siga exatamente estes passos para configurar seu ambiente de trabalho no VS Code:

### 1. Configurar sua Identidade Git
Antes de clonar, identifique-se para que seus commits fiquem registrados:
~~~bash
git config --global user.name "Seu Nome Sobrenome"
git config --global user.email "seu-email@exemplo.com"
~~~

### 2. Clonar o Repositório
Faça o download do código base para o seu VS Code:
~~~bash
git clone https://github.com/dfmateus/ansible-lab-desafio.git
cd ansible-lab-desafio
~~~

### 3. Criar sua Branch Exclusiva (IMPORTANTE)
Para não sobrescrever o trabalho dos colegas, você **DEVE** criar uma branch com seu nome:
~~~bash
# Padrão: desafio/nome-sobrenome
git checkout -b desafio/goku-silva
~~~

### 4. Criar as Roles
Utilize o `ansible-galaxy` para gerar a estrutura:
~~~bash
mkdir roles
ansible-galaxy init roles/webserver
ansible-galaxy init roles/loadbalancer
ansible-galaxy init roles/health_check
~~~

### 5. Arquivos de Orquestração
Crie os playbooks na raiz:
~~~bash
touch debug_facts.yml health_check_run.yml website_run.yml
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