# ⚖️ Instruções: Desafio 2 - Alta Disponibilidade (Web & Load Balancer)

Este desafio foca em **Disponibilidade e Escalabilidade**. Sua missão é configurar uma infraestrutura onde o tráfego é distribuído dinamicamente entre múltiplos servidores web através de um balanceador de carga, utilizando dados reais coletados pelo Ansible.

---

## 🔍 Atividade 0 - Aquecimento: Descobrindo a Rede

Para configurar um Load Balancer dinâmico, você precisa saber como o Ansible identifica os endereços IP de todos os servidores no inventário.

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
~~~

### 3. Execução no AAP
Como você configurou o **Link Simbólico**, o AAP já tem acesso ao arquivo.
* **Job Template:** Execute o Job `[Workshop-Ansible] - Gather Facts - Seu Nome`.
* **Análise:** Localize o campo `address` dentro de `ansible_default_ipv4`. É este valor que o HAProxy precisará para direcionar o tráfego.

---

## 🎯 Necessidade de Negócio
A equipe de infraestrutura precisa automatizar a entrega de dois servidores Web (Apache) e um Load Balancer (HAProxy). O balanceador deve ser capaz de detectar os IPs dos servidores web automaticamente através do inventário Ansible, sem que você precise digitar os IPs manualmente no arquivo de configuração.

---

## 🛠️ Passo a Passo do Desafio

### 1. Template da Aplicação (`roles/webserver/templates/index.html.j2`)
Complete o template que será exibido no navegador. Substitua os `{{ ? }}` por variáveis de Facts:

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
Este é o coração da alta disponibilidade. Utilize um loop Jinja2 para listar os servidores do grupo `webservers` dinamicamente:

~~~haproxy
backend backend_lab
    balance roundrobin
    # TODO: Use o loop para percorrer o grupo de webservers do inventário
    {% for host in groups['?'] %}
    server {{ host }} {{ hostvars[host]['ansible_default_ipv4']['address'] }}:80 check
    {% endfor %}
~~~

### 3. Configurando as Roles (`tasks/main.yml`)
Edite as tarefas de cada role criada anteriormente com o `ansible-galaxy`:

* **Role webserver**:
    1. Instale o pacote `httpd`.
    2. Use o módulo `template` para enviar o `index.html.j2` para `/var/www/html/index.html`.
    3. Use o módulo `ansible.posix.firewalld` para liberar o serviço `http` (porta 80).
    4. Garanta que o serviço `httpd` esteja `started` e `enabled`.

* **Role loadbalancer**:
    1. Instale o pacote `haproxy`.
    2. Use o módulo `template` para enviar o `haproxy.cfg.j2` para `/etc/haproxy/haproxy.cfg`.
    3. Libere a porta `80/tcp` no firewall.
    4. Garanta que o serviço `haproxy` esteja `started` e `enabled`.

### 4. Orquestração no `website_run.yml`
Configure o arquivo na raiz para associar as roles aos grupos corretos do inventário:

~~~yaml
---
- name: 🌐 Configurar Camada Web
  hosts: ? # TODO: Defina o grupo correto do inventário para os servidores web
  become: # TODO: Habilite o privilégio de root se necessário
  roles:
    - ? # TODO: Invoque a role de webserver

- name: ⚖️ Configurar Camada de Load Balancer
  hosts: ? # TODO: Defina o grupo correto do inventário para o Load Balancer
  become: # TODO: Habilite o privilégio de root se necessário
  roles:
    - ? # TODO: Invoque a role de loadbalancer
~~~

### 5. Publicação e Validação (AAP)
Como você está usando um **Link Simbólico**, não é necessário fazer upload. O AAP reflete suas alterações assim que você salva o arquivo no VS Code.

1. No console do AAP, crie um novo Job Template chamado **`Desafio 2 - Alta Disponibilidade (Web & LB) - Seu Nome`**.
2. Selecione o Playbook **`website_run.yml`**.
3. Garanta que a credencial de **Machine** esteja selecionada.
4. **Launch:** Execute e acompanhe a mágica acontecer!

---

## ✅ Checklist de Validação
- [ ] **Acesso:** Abra o navegador e acesse o IP do **node3**. A página carregou?
- [ ] **Balanceamento:** Ao pressionar F5 (refresh), o Hostname e o IP exibidos na página mudam entre `node1` e `node2`?
- [ ] **Configuração Local:** O arquivo `/etc/haproxy/haproxy.cfg` no node3 contém os IPs reais dos servidores web?
- [ ] **Idempotência:** Se você rodar o Job novamente, ele termina sem erros e sem mudanças (OK/Skipped)?

---
[⬅️ Voltar para o README Principal](../README.md)