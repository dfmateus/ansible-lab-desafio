# 🏥 Instruções: Desafio 1 - Auditoria e Saúde (Health Check)

Sua missão é gerar um relatório técnico detalhado sobre o estado atual de cada servidor **Target** utilizando o `ansible-navigator` localmente.

---

## 🛠️ Passo 0 - O Inventário Local (Formato INI)

Certifique-se de que seu arquivo `inventory/hosts` (sem extensão ou .ini) está configurado assim:

~~~ini
node1
node2
node3
~~~

---

## 🔍 Atividade 0 - Aquecimento: O Poder do `gather_facts`

Antes de automatizar o relatório, você precisa entender como os dados (facts) são organizados pelo Ansible.

### 1. No VS Code (Desenvolvimento)
Utilize o arquivo `debug_facts.yml` na raiz da sua pasta de trabalho:

~~~yaml
---
- name: 🔍 Explorando os Ansible Facts (Fatos do Ansible)
  hosts: all
  gather_facts: true
  tasks:
    - name: 📝 Exibir todos os dados coletados na saída do Job
      ansible.builtin.debug:
        var: ansible_facts
~~~

### 2. Execução pelo ansible-navigator (Local)
No terminal do VS Code, execute o playbook para visualizar os facts coletados dos servidores:
~~~bash
ansible-navigator run debug_facts.yml -i inventory/hosts --mode stdout
~~~
**Dica:** Analise a saída JSON para identificar as chaves exatas que você usará no relatório (ex: `ansible_memtotal_mb`, `ansible_distribution`, etc).

---

## 🎯 Necessidade de Negócio
A equipe de infraestrutura precisa de um snapshot técnico em `/tmp/health_report.txt` contendo informações de hardware, sistema operacional e rede de forma padronizada. O objetivo é facilitar auditorias rápidas sem precisar logar manualmente em cada máquina.

---

## 🛠️ Passo a Passo do Desafio

### 1. O Template (`roles/health_check/templates/health_report.j2`)
Sua tarefa é completar o arquivo de template. Substitua todos os `{{ ? }}` pelas variáveis (facts) que você validou na Atividade 0:

~~~jinja2
=====================================================
📊 SERVER HEALTH CHECK REPORT
=====================================================
Hostname:      {{ ? }}
FQDN:          {{ ? }}
SO:            {{ ? }} {{ ? }}
Kernel:        {{ ? }}
Arquitetura:   {{ ? }}
Uptime:        {{ ? | int // 86400 }} dias

-----------------------------------------------------
💻 INFORMAÇÕES DE CPU E MEMÓRIA
-----------------------------------------------------
vCPUs:         {{ ? }}
RAM Total:     {{ ? }} MB
RAM Livre:     {{ ? }} MB
RAM Usada:     {{ ? - ? }} MB

-----------------------------------------------------
🌐 INFORMAÇÕES DE REDE
-----------------------------------------------------
IPv4 Padrão:   {{ ? | default('N/A') }}
Gateway:       {{ ? | default('N/A') }}
=====================================================
~~~

### 2. Configurando a Task e o Playbook de Execução
* Edite o arquivo `roles/health_check/tasks/main.yml` e utilize o módulo `ansible.builtin.template` para processar o relatório.
* Edite o arquivo **`health_check_run.yml`** na raiz para orquestrar a execução:

~~~yaml
---
- name: 🏥 Executar Diagnóstico de Saúde
  hosts: ? # TODO: Defina os hosts (all ou o grupo de servidores)
  gather_facts: ? # TODO: Habilite a coleta de fatos (yes/no)
  become: ? # TODO: Habilite o privilégio de root se necessário (yes/no)
  
  roles:
    # TODO: Invoque a role de health_check criada pelo galaxy
    - ?
~~~

### 3. Execução Local com Ansible-Navigator
Agora, execute a automação final para gerar os relatórios nos servidores alvo:
~~~bash
ansible-navigator run health_check_run.yml -i inventory/hosts.yml --mode stdout
~~~

---

## ✅ Checklist de Validação
- [ ] **Execução:** O `ansible-navigator` finalizou sem erros (failed=0)?
- [ ] **Arquivo Criado:** Acesse um dos servidores via terminal e verifique: `ssh node1 "cat /tmp/health_report.txt"`.
- [ ] **Dados Dinâmicos:** O relatório exibe as informações reais capturadas ou sobraram interrogações?
- [ ] **Cálculo de RAM:** A lógica de "RAM Usada" está exibindo o valor correto em MB?

---
[⬅️ Voltar para o README Principal](../README.md)