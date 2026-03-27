# ⚖️ Instruções: Desafio 2 - Alta Disponibilidade (Web & Load Balancer)

Este desafio foca em **Disponibilidade e Escalabilidade**. Sua missão é configurar uma infraestrutura onde o tráfego é distribuído dinamicamente entre múltiplos servidores web através de um balanceador de carga, utilizando dados reais coletados pelo Ansible via Git.

---

## 🔍 Atividade 0 - Aquecimento: Descobrindo a Rede

Para configurar um Load Balancer dinâmico, você precisa saber como o Ansible identifica os endereços IP de todos os servidores no inventário.

### 1. Enviar seu código para o GitHub
Para que o AAP consiga executar seu teste, você deve enviar o arquivo para sua branch:
~~~bash
git add debug_facts.yml
git commit -m "Atividade 0: Explorando facts de rede"
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
* Crie um **Job Template** chamado `[Workshop-Ansible] - Gather Facts - Seu Nome`.
* Selecione o seu **Projeto** e o Playbook **`debug_facts.yml`**.
* **Execute** e localize no log o campo `address` dentro de `ansible_default_ipv4`. Esse dado será essencial para o HAProxy.

---

## 🎯 Necessidade de Negócio
A equipe de infraestrutura precisa automatizar a entrega de dois servidores Web (Apache) e um Load Balancer (HAProxy). O balanceador deve detectar os IPs dos servidores web automaticamente através do inventário, garantindo que novos nós possam ser adicionados sem edição manual de arquivos de configuração.

---

## 🛠️ Passo a Passo do Desafio

### 1. Template da Aplicação (`roles/webserver/templates/index.html.j2`)
Complete o template substituindo os `{{ ? }}` por variáveis de Facts:

~~~html
<!DOCTYPE html>
<html>
<head>
    <title>Workshop Ansible - HA</title>
    <style>body { font-family: Arial; text-align: center; padding: 50px; }</style>
</head>
<body>
    <h1>🚀 Servidor Web Automatizado</h1>
    <div style="border: 1px solid #ccc; padding: 20px; display: inline-block;">
        <p><strong>Hostname:</strong> {{ ? }}</p>
        <p><strong>IP Local:</strong> {{ ? }}</p>
        <p><strong>SO:</strong> {{ ? }} {{ ? }}</p>
        <p><strong>Fabricante:</strong> {{ ? }}</p>
    </div>
    <p>Gerado em: {{ ansible_date_time.datetime }}</p>
</body>
</html>
~~~

### 2. Template do Load Balancer (`roles/loadbalancer/templates/haproxy.cfg.j2`)
Utilize um loop Jinja2 para listar os servidores do grupo `webservers` dinamicamente:

~~~haproxy
backend backend_lab
    balance roundrobin
    # TODO: Use o loop para percorrer o grupo de webservers do inventário
    {% for host in groups['?'] %}
    server {{ host }} {{ hostvars[host]['?']['?'] }}:80 check
    {% endfor %}
~~~

### 3. Configurando as Roles (`tasks/main.yml`)
Edite as tarefas de cada role criada anteriormente com o `ansible-galaxy`:

* **Role webserver**: Instalar `httpd`, aplicar template, abrir porta `80/tcp` no firewall e garantir serviço iniciado.
* **Role loadbalancer**: Instalar `haproxy`, aplicar template, abrir porta `80/tcp` no firewall e garantir serviço iniciado.

### 4. Orquestração no `website_run.yml`
Configure o arquivo na raiz para associar as roles aos grupos corretos:

~~~yaml
---
- name: 🌐 Configurar Camada Web
  hosts: ? # TODO: Defina o grupo correto do inventário para os servidores web
  become: ? # TODO: Habilite o privilégio de root (yes/no)
  roles:
    - webserver

- name: ⚖️ Configurar Camada de Load Balancer
  hosts: ? # TODO: Defina o grupo correto do inventário para o Load Balancer
  become: ? # TODO: Habilite o privilégio de root (yes/no)
  roles:
    - loadbalancer
~~~

### 5. Publicação e Validação (AAP)
1. **Push:** No VS Code, salve tudo, faça o `commit` e o `push` para sua branch.
2. **Sync:** No AAP, vá em seu **Project** e clique no ícone de sincronismo (🔄) para baixar o código novo do GitHub.
3. **Job Template:** Crie o Job **`Desafio 2 - Alta Disponibilidade - Seu Nome`** apontando para o playbook `website_run.yml`.
4. **Launch:** Execute e valide o acesso pelo IP do **node3**.

---

## ✅ Checklist de Validação
- [ ] **Acesso:** O IP do **node3** carrega a página no navegador?
- [ ] **Balanceamento:** Ao pressionar F5, o Hostname alterna entre `node1` e `node2`?
- [ ] **Configuração:** O arquivo `/etc/haproxy/haproxy.cfg` no node3 reflete os IPs dinâmicos?
- [ ] **Idempotência:** Uma segunda execução do Job não resulta em mudanças (Changed=0)?

---
[⬅️ Voltar para o README Principal](../README.md)