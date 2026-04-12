## Instalando o WordPress

### Criando diretório da aplicação

```yaml
- name: Criar diretório da aplicação
  ansible.builtin.file:
    path: /srv/www
    state: directory
    owner: www-data
    group: www-data
  become: yes
```

---

### Baixando e descompactando o WordPress

```yaml
- name: Baixar e extrair WordPress
  ansible.builtin.unarchive:
    src: https://wordpress.org/latest.tar.gz
    dest: /srv/www
    remote_src: yes
  become: yes
```

---

### Copiando arquivo de configuração do Apache

```yaml
- name: Copiar configuração do Apache
  ansible.builtin.copy:
    src: files/wordpress.conf
    dest: /etc/apache2/sites-available/000-default.conf
  become: yes
  notify:
    - restart apache
```

---

### Handler para reiniciar o Apache

```yaml
handlers:
  - name: restart apache
    ansible.builtin.service:
      name: apache2
      state: restarted
    become: yes
```
