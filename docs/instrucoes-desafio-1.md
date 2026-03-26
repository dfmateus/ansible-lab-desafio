# 🏥 Instruções: Desafio 1 - Auditoria e Saúde (Health Check)

Sua missão é gerar um relatório técnico detalhado sobre o estado atual de cada servidor **Target** utilizando os recursos de automação do Ansible.

---

## 🔍 Atividade 0 - Aquecimento: O Poder do `gather_facts`

Antes de automatizar o relatório, você precisa conectar sua pasta ao AAP e entender como os dados são organizados.

### 1. Configurar o Projeto no AAP
Para que o AAP enxergue seus arquivos locais via link simbólico:
1. No console do AAP, vá em **Resources -> Projects -> Add**.
2. **Name:** `Projeto - Seu Nome`
3. **Source Control Type:** Selecione `Manual`.
4. **Project Base Path:** Confirme se está em `/home/ec2-user/aap/controller/data/projects`.
5. **Playbook Directory:** Selecione a sua pasta (ex: `ansible-workshop-desafio-seu-nome`).
6. Clique em **Save**.

### 2. No VS Code (Desenvolvimento)
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
~~~ccccccccccccccccc

### 3. Execução no AAP
* Crie um **Job Template** chamado `[Workshop] - Gather Facts - Seu Nome`.
* Selecione o seu **Projeto** e o Playbook **`debug_facts.yml`**.
* **Execute** e analise o log JSON para identificar as chaves exatas que você usará no relatório abaixo (ex: `ansible_memtotal_mb`, `ansible_distro`, etc).

---

## 🎯 Necessidade de Negócio
A equipe de infraestrutura precisa de um snapshot técnico em `/tmp/health_report.txt` contendo informações de hardware, sistema operacional e rede de forma padronizada. O objetivo é facilitar auditorias rápidas sem precisar logar manualmente em cada máquina.

---

## 🛠️ Passo a Passo do Desafio

### 1. O Template (`roles/health_check/templates/health_report.j2`)
Sua tarefa é completar o arquivo de template. Substitua todos os `{{ ? }}` pelas variáveis (facts) validadas na Atividade 0:

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
  hosts: all # TODO: Defina os hosts (all ou o grupo de servidores)
  gather_facts: ? # TODO: Habilite a coleta de fatos (yes/no)
  become: ? # TODO: Habilite o privilégio de root se necessário (yes/no)
  
  roles:
    # TODO: Invoque a role de health_check criada pelo galaxy
    - ?
~~~

### 3. Publicação e Validação (AAP)
Como você está usando um **Link Simbólico**, não é necessário fazer upload. O AAP reflete suas alterações assim que você salva o arquivo no VS Code.

1. No console do AAP, crie um novo Job Template chamado **`Desafio 1 - Auditoria e Saude - Seu Nome`**.
2. Selecione o Playbook **`health_check_run.yml`**.
3. Garanta que a credencial de **Machine** esteja selecionada.
4. **Launch:** Execute o Job e acompanhe o log.

---

## ✅ Checklist de Validação
- [ ] **Status do Job:** O Job finalizou com sucesso (Verde) no AAP?
- [ ] **Arquivo Criado:** Acesse um dos servidores via terminal e verifique: `cat /tmp/health_report.txt`.
- [ ] **Dados Dinâmicos:** O relatório exibe as informações reais capturadas ou sobraram interrogações?
- [ ] **Lógica:** O cálculo de RAM Usada (`Total - Free`) está exibindo o valor correto?

---
[⬅️ Voltar para o README Principal](../README.md)