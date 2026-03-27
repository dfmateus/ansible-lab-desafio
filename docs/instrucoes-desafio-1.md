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

Antes de automatizar o relatório, entenda como os dados são organizados pelo Ansible.

### 1. No VS Code (Desenvolvimento)
Edite o arquivo `debug_facts.yml` na raiz:
~~~yaml
---
- name: 🔍 Explorando os Ansible Facts
  hosts: all
  gather_facts: true
  tasks:
    - name: 📝 Exibir dados coletados
      ansible.builtin.debug:
        var: ansible_facts
~~~

### 2. Execução pelo ansible-navigator (Local)
No terminal do VS Code, execute:
~~~bash
ansible-navigator run debug_facts.yml -i inventory/hosts --mode stdout
~~~

---

## 🛠️ Passo a Passo do Desafio

### 1. O Template (`roles/health_check/templates/health_report.j2`)
Complete as lacunas substituindo os `{{ ? }}`:
~~~jinja2
=====================================================
📊 SERVER HEALTH CHECK REPORT
=====================================================
Hostname:      {{ ? }}
FQDN:          {{ ? }}
SO:            {{ ? }} {{ ? }}
Kernel:        {{ ? }}
Uptime:        {{ ? | int // 86400 }} dias

-----------------------------------------------------
💻 INFORMAÇÕES DE CPU E MEMÓRIA
-----------------------------------------------------
vCPUs:         {{ ? }}
RAM Total:     {{ ? }} MB
RAM Livre:     {{ ? }} MB
RAM Usada:     {{ ? - ? }} MB
=====================================================
~~~

### 2. Configurando a Task e o Playbook de Execução
* Edite `roles/health_check/tasks/main.yml` usando o módulo `ansible.builtin.template`.
* Edite o arquivo **`health_check_run.yml`** na raiz:
~~~yaml
---
- name: 🏥 Executar Diagnóstico de Saúde
  hosts: all      # TODO: Defina os hosts
  gather_facts: ? # TODO: Habilitar (yes/no)
  become: ?       # TODO: Habilitar (yes/no)
  
  roles:
    - health_check
~~~

### 3. Execução e Validação
~~~bash
ansible-navigator run health_check_run.yml -i inventory/hosts --mode stdout
~~~
**Validação:** Verifique o arquivo nos alvos: `ssh node1 "cat /tmp/health_report.txt"`.

---
[⬅️ Voltar para o README Principal](../README.md)