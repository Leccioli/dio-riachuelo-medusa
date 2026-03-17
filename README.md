# Auditoria de Segurança com Medusa: Brute Force e Password Spraying

## 🎯 Sobre o Projeto
Este repositório contém a documentação e os estudos práticos do desafio da DIO sobre ataques de força bruta e *password spraying* utilizando o **Kali Linux** e a ferramenta **Medusa**. 

Como o ambiente em que realizei o estudo possuía restrições para a criação de Máquinas Virtuais (VMs) locais, adaptei a entrega para focar na arquitetura dos ataques, sintaxe da ferramenta, criação de *wordlists* baseadas em OSINT (Open Source Intelligence) e, principalmente, nas formas de mitigar essas vulnerabilidades.

## 🧠 Conceitos Aprendidos

* **Brute Force (Força Bruta):** O ataque tenta adivinhar a credencial testando combinações infinitas de usuários e senhas até acertar. Geralmente faz muito barulho na rede e bloqueia a conta rápido se houver política de bloqueio.
* **Password Spraying:** Ao invés de tentar várias senhas em um único usuário (o que causa bloqueio da conta), o atacante pega uma única senha comum (tipo `123456` ou `alemanha2026`) e testa em vários usuários diferentes. É mais furtivo e tem menos chance de disparar alarmes.
* **Wordlists Customizadas:** Listas de senhas geradas a partir da engenharia social do alvo. Juntar nomes de parceiros, datas de casamento, hobbies (como corrida ou café) e stacks de tecnologia aumenta drasticamente a taxa de sucesso do ataque.

## 💻 Comandos e Utilização do Medusa

O Medusa é uma ferramenta rápida e modular para testes de login. Abaixo, detalho como os ataques seriam executados na prática contra os serviços de um ambiente vulnerável (como o Metasploitable 2).

### 1. Ataque de Força Bruta via FTP
Para testar a segurança de um servidor de arquivos FTP, usamos o módulo `ftp`.
```bash
medusa -u dev_mobile -P senhas.txt -h 192.168.0.10 -M ftp
```
* `-u`: Define um único usuário alvo.
* `-P`: Aponta para o nosso arquivo de wordlist de senhas.
* `-h`: O IP do servidor alvo.
* `-M`: Define o módulo (serviço) que estamos atacando.

### 2. Password Spraying via SMB
Para focar em *password spraying* na rede Windows/SMB, invertemos a lógica: testamos uma senha forte/comum em uma lista de usuários.
```bash
medusa -U usuarios.txt -p "alemanha2026" -h 192.168.0.10 -M smbnt
```
* `-U`: Aponta para uma lista de usuários válidos coletados na rede.
* `-p`: Tenta uma única senha contra todos os usuários da lista.

### 3. Ataque em Formulário Web (HTTP)
Atacando uma página de login web (como a do DVWA).
```bash
medusa -u admin -P senhas.txt -h 192.168.0.10 -M web-form -m DIR:/dvwa/login.php
```

## 🛡️ Recomendações de Mitigação (Como se proteger)
Fazer o ataque é legal, mas entender como se defender é o que o mercado pede. Para evitar que ferramentas como o Medusa tenham sucesso, recomendo:

1.  **Políticas de Lockout (Bloqueio de Conta):** Configurar o sistema para bloquear a conta temporariamente após 3 a 5 tentativas de login incorretas. Isso quebra as pernas do Brute Force tradicional.
2.  **MFA (Autenticação de Múltiplos Fatores):** Mesmo que o atacante descubra a senha usando *password spraying*, ele não vai ter o token do celular do usuário.
3.  **Monitoramento e Alertas:** Ferramentas de SIEM devem gerar alertas se virem muitos logins falhos vindos do mesmo IP em um curto espaço de tempo.
4.  **Senhas Fortes e Sem Contexto Pessoal:** Evitar senhas que contenham datas de aniversário, nomes de familiares, hobbies ou o framework que o dev trabalha.
