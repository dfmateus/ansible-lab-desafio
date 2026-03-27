# 🏥 Instruções: Desafio 1 - Auditoria e Saúde (Health Check)

Sua missão é gerar um relatório técnico detalhado sobre o estado atual de cada servidor **Target** utilizando os recursos de automação do Ansible e versionamento via Git.

---

## 🔍 Atividade 0 - Aquecimento: O Poder do `gather_facts`

Antes de automatizar o relatório, você precisa conectar sua branch ao AAP e entender como os dados são organizados.

### 1. Enviar seu código para o GitHub
Para que o AAP consiga executar seu teste, você deve enviar seu progresso inicial para sua branch:
~~~bash
git add debug_facts.yml
git commit -m "Atividade 0: Explorando facts para o relatório"
# O instrutor fornecerá o Token para o push
git push origin aluno/seu-nome-sobrenome
~~~

### 2. Configurar o Projeto no AAP
Agora, conecte o AAP ao seu repositório:
1. No console do AAP, vá em **Resources -> Projects -> Add**.
2. **Name:** `Projeto - Seu Nome`
3. **Source Control Type:** Selecione `Git`.
4. **Source Control URL:** `https://github.com/dfmateus/ansible-lab-desafio.git`
5. **Source Control Branch:** Digite o nome da **SUA** branch (ex: `aluno/goku-silva`).
6. Clique em **Save**.

### 3. Execução no AAP
* Crie um **Job Template** chamado `[Workshop] - Gather Facts - Seu Nome`.
* Selecione o seu **Projeto** e o Playbook **`debug_facts.yml`**.
* **Execute** e analise o log JSON para identificar as chaves exatas que você usará no relatório abaixo (ex: `ansible_memtotal_mb`, `ansible_distribution`, etc).

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
  hosts: ? # TODO: Defina os hosts (all ou o grupo de servidores)
  gather_facts: ? # TODO: Habilite a coleta de fatos (yes/no)
  become: ? # TODO: Habilite o privilégio de root se necessário (yes/no)
  
  roles:
    # TODO: Invoque a role de health_check criada pelo galaxy
    - ?
~~~

### 3. Publicação e Validação (AAP)
Diferente do modo manual, agora o AAP precisa que você "suba" as alterações para o GitHub.

1. **Push:** No VS Code, salve tudo, faça o `commit` e o `push` para sua branch.
2. **Sync:** No console do AAP, vá em seu **Project** e clique no ícone de sincronismo (🔄) para baixar o código novo do GitHub.
3. **Job Template:** Crie o Job **`Desafio 1 - Auditoria e Saude - Seu Nome`** apontando para o playbook `health_check_run.yml`.
4. **Launch:** Execute o Job e acompanhe o log.

---

## ✅ Checklist de Validação
- [ ] **Status do Job:** O Job finalizou com sucesso (Verde) no AAP?
- [ ] **Arquivo Criado:** Acesse um dos servidores via terminal e verifique: `cat /tmp/health_report.txt`.
- [ ] **Dados Dinâmicos:** O relatório exibe as informações reais capturadas ou sobraram interrogações?
- [ ] **Git:** Você sincronizou seu projeto no AAP após o último `git push`?

---
[⬅️ Voltar para o README Principal](../README.md)