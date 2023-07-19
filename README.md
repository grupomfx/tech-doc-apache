### Configurando Logs do Apache :sunglasses: :computer:
----


Antes, qualquer coisa, vamos entender alguns pontos importantes para começarmos e garantir que não iremos fazer nenhuma *"desinteligência"* (:hankey:) no processo de configuração


**1**- Verifique qual distro linux está usando através do seguinte comando
    
         cat /etc/os-release 
**2**- Distros derivadas do Debian (`Ubuntu`, `Mint`, `Deepin`, `Kali Linux`) utilizam 

        apt-get install       # Gerenciador de Pacote
        systemctl or Service  # Gerenciador de Serviços
        

**2.1**- Distros derivadas do RedHat que são mais utilizando em ambientes corporativos (`CentOS`, `Fedora`, `OracleLinux`) utilizam

        yum install           # Gerenciador de Pacote
        systemctl or Service  # Gerenciador de Serviços

**3-** Para verificar o status e controlar a execução do servico do apache basta utilizar o gerenciador de serviço da seguinte forma
        
        $ sudo systemctl status apache2  # Verifica o status serviço 
        $ sudo systemctl start apache2   # Inicializa o serviço 
        $ sudo systemctl stop apache2    # Para a execução do serviço
        $ sudo systemctl restart apache2 # Realiza o processo de de stop e start sequencialmente
        
        or
        
        $ sudo service apache2 status    # Verifica o status serviço 
        $ sudo service apache2 start     # Inicializa o serviço 
        $ sudo service apache2 stop      # Para a execução do serviço
        $ sudo service apache2 restart   # Realiza o processo de de stop e start sequencialmente
        
***IMPORANTE** No caso de instalações derivadas do RedHat, o pacote do apache não é referenciado como `apache2` e sim como `httpd` ou seja fariamos os mesmo comandos acima porém trocando o termo do pacote

        $ sudo systemctl status httpd  # Verifica o status serviço 
        $ sudo systemctl start httpd   # Inicializa o serviço 
        $ sudo systemctl stop httpd    # Para a execução do serviço
        $ sudo systemctl restart httpd # Realiza o processo de de stop e start sequencialmente
        
        or
        
        $ sudo service httpd status    # Verifica o status serviço 
        $ sudo service httpd start     # Inicializa o serviço 
        $ sudo service httpd stop      # Para a execução do serviço
        $ sudo service httpd restart   # Realiza o processo de de stop e start sequencialmente


-----

 Agora sim estamos preparados !!! :muscle: :facepunch:
 
 
**1-** Primeira coisa que devemos fazer é garantir que o serviço do apache esteja parado, e isso já sabemos como fazer certo? como aprendemos logo acima!, mas não custa nada deixar aqui também:
 
        $ sudo systemctl stop apache2
        
        or
        
        $ sudo service httpd stop

 
**2-** Logo após vamos ter certeza que paramos o serviço corretamente
 
        $ sudo systemctl status apache2  
        
        or 
        
        $ sudo service httpd status  
        
O nossou output no terminal ou seja saida deve ser algo parecido com isso aqui:
    
    ● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: inactive (dead) since Wed 2023-07-19 09:44:45 -03; 12s ago
       Docs: https://httpd.apache.org/docs/2.4/
    Process: 182919 ExecStart=/usr/sbin/apachectl start (code=exited, status=0/SUCCESS)
    Process: 184906 ExecStop=/usr/sbin/apachectl graceful-stop (code=exited, status=0/SUCCESS)
        CPU: 322ms

        Jul 19 09:44:45 debian systemd[1]: apache2.service: Killing process 182964 (n/a) with signal SIGKILL.
        Jul 19 09:44:45 debian systemd[1]: apache2.service: Killing process 182966 (n/a) with signal SIGKILL.
        Jul 19 09:44:45 debian systemd[1]: apache2.service: Killing process 182971 (n/a) with signal SIGKILL.
        Jul 19 09:44:45 debian systemd[1]: apache2.service: Killing process 182973 (n/a) with signal SIGKILL.
        Jul 19 09:44:45 debian systemd[1]: apache2.service: Killing process 182976 (n/a) with signal SIGKILL.
        Jul 19 09:44:45 debian systemd[1]: apache2.service: Killing process 182978 (n/a) with signal SIGKILL.
        Jul 19 09:44:45 debian systemd[1]: apache2.service: Killing process 182980 (n/a) with signal SIGKILL.
        Jul 19 09:44:45 debian systemd[1]: apache2.service: Killing process 182982 (n/a) with signal SIGKILL.
        Jul 19 09:44:45 debian systemd[1]: apache2.service: Succeeded.
        Jul 19 09:44:45 debian systemd[1]: Stopped The Apache HTTP Server.


 Perfeito, até aqui está tudo bem!, bora seguir porque tem muuuito mais! :fire:

**3-** Bom nesta etapa, vamos acessar o caminho de instalação do apache para então encontrar o tão sonhado `apache2.conf` ou `httpd.conf`que é basicamente o arquivo de configuração do nosso serviço. Normalmente ele tem um diretório raiz padrão da instalação default do apache. 

        $ cd /etc/apache2      # Caminho padrão distros derivadas do debian
        
        $ cd /etc/httpd/conf   # Caminho padrão distros derivadas do RedHat


**4-** Bom uma vez acessando a pasta onde se encontra o arquivo de configuração do apache, basta abri com editor do terminal da sua preferencias (`nano` ou `vi`) apenas lembrando que o editor `vi` ele é nativo para qualquer distribuição linux, já o `nano` só vem disponivel em distros derivadas do `debian`. Um ponto importante aqui é por se tratar de um diretorio raiz do sistema o ideia é abrir o arquivo com o `sudo` pois caso contrário provavelmente ele estará protegido contra gravação.

        $ sudo nano apache2.conf 
        
        or 
        
        $ sudo vi httpd.conf


Ao abrir no terminal, você provavelmente encontrará um arquivo com esta estrutura. :point_down:
            
        1   # This is the main Apache server configuration file.  It contains the
        2   # configuration directives that give the server its instructions.
        3   # See http://httpd.apache.org/docs/2.4/ for detailed information about
        4   # the directives and /usr/share/doc/apache2/README.Debian about Debian specific
        5   # hints.
        6   #
        7   #
        8   # Summary of how the Apache 2 configuration works in Debian:
        9   # The Apache 2 web server configuration in Debian is quite different to
        10  # upstream's suggested way to configure the web server. This is because Debian's
        11  # default Apache2 installation attempts to make adding and removing modules,
        12  # virtual hosts, and extra configuration directives as flexible as possible, in
        13  # order to make automating the changes and administering the server as easy as
        14  # possible.
        15
        16  # It is split into several files forming the configuration hierarchy outlined
        17  # below, all located in the /etc/apache2/ directory:
        18  #
        19  #	/etc/apache2/
        20  #	|-- apache2.conf
        21  #	|	`--  ports.conf
        22  #	|-- mods-enabled
        23  #	|	|-- *.load
        24  #	|	`-- *.conf
        25  #	|-- conf-enabled
        26  #	|	`-- *.conf
        27  # 	`-- sites-enabled
        28  #	 	`-- *.conf
        29  #
        30  #
        31  # * apache2.conf is the main configuration file (this file). It puts the pieces
        32  #   together by including all remaining configuration files when starting up the
        33  #   web server.
        34  #
        35  # * ports.conf is always included from the main configuration file. It is
        36  #   supposed to determine listening ports for incoming connections which can be
        37  #   customized anytime.
        38  #
        39  # * Configuration files in the mods-enabled/, conf-enabled/ and sites-enabled/
        40  #   directories contain particular configuration snippets which manage modules,
        41  #   global configuration fragments, or virtual host configurations,
        42  #   respectively.
        43  #
        44  # * They are activated by symlinking available configuration files from their
        45  #   respective *-available/ counterparts. These should be managed by using our
        46  #   helpers a2enmod/a2dismod, a2ensite/a2dissite and a2enconf/a2disconf. See
        47  #   their respective man pages for detailed information.
        48  #
        49  # * The binary is called apache2. Due to the use of environment variables, in
        50  #   the default configuration, apache2 needs to be started/stopped with
        51  #   /etc/init.d/apache2 or apache2ctl. Calling /usr/bin/apache2 directly will not
        52  #   work with the default configuration.
        53
        54
        55  # Global configuration
        56  #
        57  #
        58  #
        59  # ServerRoot: The top of the directory tree under which the server's
        60  # configuration, error, and log files are kept.
        61  #
        62  # NOTE!  If you intend to place this on an NFS (or otherwise network)
        63  # mounted filesystem then please read the Mutex documentation (available
        64  # at <URL:http://httpd.apache.org/docs/2.4/mod/core.html#mutex>);
        65  # you will save yourself a lot of trouble.
        66  #
        67  # Do NOT add a slash at the end of the directory path.
        68  #
        69  #ServerRoot "/etc/apache2"
        70
        71  #
        72  # The accept serialization lock file MUST BE STORED ON A LOCAL DISK.
        73  #
        74  #Mutex file:${APACHE_LOCK_DIR} default
        75
        76  #
        77  # The directory where shm and other runtime files will be stored.
        78  #
        79
        80  DefaultRuntimeDir ${APACHE_RUN_DIR}
        81
        82  #
        83  # PidFile: The file in which the server should record its process
        84  # identification number when it starts.
        85  # This needs to be set in /etc/apache2/envvars
        86  #
        87  PidFile ${APACHE_PID_FILE}
        88
        89  #
        90  # Timeout: The number of seconds before receives and sends time out.
        91  #
        92  Timeout 300
        93
        94  #
        95  # KeepAlive: Whether or not to allow persistent connections (more than
        96  # one request per connection). Set to "Off" to deactivate.
        97  #
        98  KeepAlive On
        99
        100 #
        101 # MaxKeepAliveRequests: The maximum number of requests to allow
        102 # during a persistent connection. Set to 0 to allow an unlimited amount.
        103 # We recommend you leave this number high, for maximum performance.
        104 #
        105 MaxKeepAliveRequests 100
        106
        107 #
        108 # KeepAliveTimeout: Number of seconds to wait for the next request from the
        109 # same client on the same connection.
        110 #
        111 KeepAliveTimeout 5
        112
        113
        114 # These need to be set in /etc/apache2/envvars
        115 User ${APACHE_RUN_USER}
        116 Group ${APACHE_RUN_GROUP}
        117    
        118 #
        119 # HostnameLookups: Log the names of clients or just their IP addresses
        120 # e.g., www.apache.org (on) or 204.62.129.132 (off).
        121 # The default is off because it'd be overall better for the net if people
        122 # had to knowingly turn this feature on, since enabling it means that
        123 # each client request will result in AT LEAST one lookup request to the
        124 # nameserver.
        125 #
        126 HostnameLookups Off
        127    
        128 # ErrorLog: The location of the error log file.
        129 # If you do not specify an ErrorLog directive within a <VirtualHost>
        130 # container, error messages relating to that virtual host will be
        131 # logged here.  If you *do* define an error logfile for a <VirtualHost>
        132 # container, that host's errors will be logged there and not here.
        133 #
        134 ErrorLog ${APACHE_LOG_DIR}/error.log
        135
        136 #
        137 # LogLevel: Control the severity of messages logged to the error_log.
        138 # Available values: trace8, ..., trace1, debug, info, notice, warn,
        139 # error, crit, alert, emerg.
        140 # It is also possible to configure the log level for particular modules, e.g.
        141 # "LogLevel info ssl:warn"
        142 #
        143 LogLevel warn
        144 CustomLog "| /bin/rotatelogs /var/log/apache2/access.log.%Y-%m-%d-%H_%M_%S 5M" common
        145 
        146 # Include module configuration:
        147 IncludeOptional mods-enabled/*.load
        148 IncludeOptional mods-enabled/*.conf
        149 
        150 # Include list of ports to listen on
        151 Include ports.conf
        152
        153
        154 # Sets the default security model of the Apache2 HTTPD server. It does
        155 # not allow access to the root filesystem outside of /usr/share and /var/www.
        156 # The former is used by web applications packaged in Debian,
        157 # the latter may be used for local directories served by the web server. If
        158 # your system is serving content from a sub-directory in /srv you must allow
        159 # access here, or in any related virtual host.
        160 <Directory />
        161	 Options FollowSymLinks
        162	 AllowOverride None
        163	 Require all denied
        164 </Directory>
        165
        166 <Directory /usr/share>
        167	 AllowOverride None
        168	 Require all granted
        169 </Directory>
        170
        171 <Directory /var/www/>
        172	 Options Indexes FollowSymLinks
        173	 AllowOverride None
        174	 Require all granted
        175 </Directory>
        176
        177 #<Directory /srv/>
        178 #	Options Indexes FollowSymLinks
        179 #	AllowOverride None
        180 #	Require all granted
        181 #</Directory>
        182
        183
        184
        185
        186 # AccessFileName: The name of the file to look for in each directory
        187 # for additional configuration directives.  See also the AllowOverride
        188 # directive.
        189 #
        190 AccessFileName .htaccess
        191
        192 #
        193 # The following lines prevent .htaccess and .htpasswd files from being
        194 # viewed by Web clients.
        195 #
        196 <FilesMatch "^\.ht">
        197 	Require all denied
        198 </FilesMatch>
        199
        200
        201 #
        202 # The following directives define some format nicknames for use with
        203 # a CustomLog directive.
        204 #
        205 # These deviate from the Common Log Format definitions in that they use %O
        206 # (the actual bytes sent including headers) instead of %b (the size of the
        207 # requested file), because the latter makes it impossible to detect partial
        208 # requests.
        209 #
        210 # Note that the use of %{X-Forwarded-For}i instead of %h is not recommended.
        211 # Use mod_remoteip instead.
        212 #
        213 #LogFormat "%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" vhost_combined
        214 #LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
        215    
        216 LogFormat "%h %l %u %t \"%r\" %>s %O" common
        217 #LogFormat "%{Referer}i -> %U" referer
        218 #LogFormat "%{User-agent}i" agent
        219 
        220 # Include of directories ignores editors' and dpkg's backup files,
        221 # see README.Debian for details.
        222 
        223 # Include generic snippets of statements
        224 IncludeOptional conf-enabled/*.conf
        225
        226 # Include the virtual host configurations:
        227 IncludeOptional sites-enabled/*.conf
        228
        229 # vim: syntax=apache ts=4 sw=4 sts=4 sr noet
        230

**5-** Normalmente esse é o arquivo de conf default do apache, nem sempre ele estará justamente igual ao do exemplo acima, devido as particularidades de configurações a quais podem ser feitas neste arquivo. Mas para o ques estamos resolvendo aqui inicialmente teremos uma alteração bem pequena, vejamos a seguir. 

**5.1** Vamos então ir na linha `134` onde temos a declaracão do arquivo de log e vamos alterar para o seguinte comamndo


        CustomLog "| /bin/rotatelogs /var/log/apache2/error.log.%Y-%m-%d-%H_%M_%S 10M" common # Derivados de debian
        
        or
        
        CustomLog "| /bin/rotatelogs /var/log/httpd/error.log.%Y-%m-%d-%H_%M_%S 10M" common   # Derivados de RedHat

Esse comando tecnicamente utiliza uma ferramenta do apache que é o `rotatelogs` justamente para controlar o buffer de logs e permitido modificações na escrita e nas suas parametrizações

Sendo assim temos então um log de erro que será escrito no caminho default do apache `/var/log/apache2/` ou `var/log/httpd/` com o nome de `error.log.2023-07-19-22_00_04` versionando o o dia e também com o parametro `10M` estou especificando qual o tamanho limite do arquivo a ser escrito. 

**5.2** Faremos isso para também o aquivo de acesso o access.log, e para isso vamos inserir na linha `144` o seguinte comando

        CustomLog "| /bin/rotatelogs /var/log/apache2/access.log.%Y-%m-%d-%H_%M_%S 10M" common
        
        or 
        
        CustomLog "| /bin/rotatelogs /var/log/httpd/access.log.%Y-%m-%d-%H_%M_%S 10M" common

**5.3** Agora lá mais pro fim do arquivo vamos procurar onde formatamos os logs através do comando `logFormat` e vamos comentar utilizando `#` as linhas `213`, `214`,`217` e `218` ficando exatamente desta forma, ficando apenas a linha `216` com o comando de fomtação do log, isso deixará o log mais enxuto com um pouco menos de informações


        213 #LogFormat "%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" vhost_combined
        214 #LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
        215    
        216 LogFormat "%h %l %u %t \"%r\" %>s %O" common
        217 #LogFormat "%{Referer}i -> %U" referer
        218 #LogFormat "%{User-agent}i" agent
        
