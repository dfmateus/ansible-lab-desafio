# ⚖️ Instruções: Desafio 2 - Alta Disponibilidade (Web & Load Balancer)

Configure uma infraestrutura de alta disponibilidade com Apache e HAProxy rodando localmente.

---

## 🛠️ Passo 0 - Grupos no Inventário Local (Formato INI)

Edite seu arquivo `inventory/hosts` para organizar os servidores em grupos:

~~~ini
[webservers]
node1
node2

[loadbalancer]
node3
~~~

---

## 🔍 Atividade 0 - Aquecimento: Descobrindo a Rede

Use o `debug_facts.yml` para identificar o campo `address` dentro de `ansible_default_ipv4`:
~~~bash
ansible-navigator run debug_facts.yml -i inventory/hosts --mode stdout
~~~

---

## 🛠️ Passo a Passo do Desafio

### 1. Template da Aplicação (`roles/webserver/templates/index.html.j2`)
Complete o template substituindo os `{{ ? }}`:
~~~html
<h1>🚀 Servidor Web Automatizado</h1>
<p><strong>Hostname:</strong> {{ ? }}</p>
<p><strong>IP Local:</strong> {{ ? }}</p>
<p><strong>SO:</strong> {{ ? }} {{ ? }}</p>
~~~

### 2. Template do Load Balancer (`roles/loadbalancer/templates/haproxy.cfg.j2`)
- Utilize um loop Jinja2 para listar os servidores do grupo `webservers`:
- Adicione o seguinte bloco dentro do template, substituindo os `{{ ? }}`:
~~~haproxy
backend backend_lab
    balance roundrobin
    {% for host in groups['?'] %}
    server {{ host }} {{ hostvars[host]['ansible_default_ipv4']['address'] }}:80 check
    {% endfor %}
~~~

### 3. Playbook de Orquestração (`website_run.yml`)
~~~yaml
---
- name: 🌐 Configurar Camada Web
  hosts: ? # TODO: Grupo webservers
  become: ?
  roles:
    - webserver

- name: ⚖️ Configurar Camada de Load Balancer
  hosts: ? # TODO: Grupo loadbalancer
  become: ?
  roles:
    - loadbalancer
~~~

### 4. Execução Local (Ansible-Navigator)
~~~bash
ansible-navigator run website_run.yml -i inventory/hosts --mode stdout
~~~

### 5. Validação
Acesse o IP do **node3** (Load Balancer) e valide o balanceamento com F5 no navegador ou via:
~~~bash
curl http://node3
~~~

---
[⬅️ Voltar para o README Principal](../README.md)