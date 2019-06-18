## Considerações

Após me ser concedido o desafio de automatizar a formatação de máquinas físicas encontrei algumas dificuldades e com esse material desejo sanar algumas das que você, caro leitor, possa ter também.

Será explicado cada seção da receita, a base foi retirada do site do [Debian](https://www.debian.org/releases/wheezy/example-preseed.txt). E no final sera deixado a minha receita final.

## Como utilizar o arquivo

O resultado da receita tem que ser salvo em um arquivo com formato .cfg e deixar acessivel a maquina que o utilizara, pode ser via rede ou dentro do pendrive/cd utilizado para formatar.

Nesse caso apresento a forma pela rede.

Ao dar boot no pendrive/cd para instalação terá essa tela

![boot-install](https://raw.githubusercontent.com/RummeniggePires/preseed-debian/master/img/boot-debian.png)

Precione a tecla TAB para editar a inicialização da instalação

![boot-edit](https://raw.githubusercontent.com/RummeniggePires/preseed-debian/master/img/boot-edit.png)

Após a palavra quiet adione auto url=http://endereco/do/arquivo/preseed.cfg e tecle enter.

## Como fazer seu preseed-debian

O código é dividido por blocos para facilitar a leitura. Deve ser seguida a ordem dos blocos.


Predefine o local, idioma e país.
```
	d-i debian-installer/locale string en_US
```
Os valores também podem ser pré-configurados individualmente para maior flexibilidade.
```
	d-i debian-installer/language string en
	d-i debian-installer/country string NL
	d-i debian-installer/locale string en_GB.UTF-8
```
Opcionalmente, especifique os locais adicionais a serem gerados.
```
	d-i localechooser/supported-locales multiselect en_US.UTF-8, nl_NL.UTF-8
```

Seleção de teclado.
Keymap é um apelido para keyboard-configuration
```
	keymap d-i select us
	d-i keyboard-configuration/toggle select No toggling
```
Desativa totalmente a configuração de rede. Isso é útil para o cdrom instalações em dispositivos sem rede onde a rede questiona,
aviso e longos tempos limite são um incômodo.
```
	d-i netcfg/enable boolean false
```
Netcfg escolherá uma interface que tenha link, se possível. Pulando a exibição de uma lista se houver mais de uma interface.
```
	d-i netcfg/choose_interface select auto
```
Para escolher uma interface específica
```
	d-i netcfg/choose_interface select eth1
``` 
Para definir um tempo limite de detecção de link diferente (o padrão é 3 segundos).
Valores são interpretados como segundos.
```
	d-i netcfg/link_detection_timeout string 10
```
Se você tiver um servidor dhcp lento e o instalador expirar, esperando isso pode ser útil.
```
	d-i netcfg/dhcp_timeout string 60
	d-i netcfg/dhcpv6_timeout string 60
```
Se você preferir configurar a rede manualmente
```
	d-i netcfg/disable_autoconfig boolean true
```
Se você quiser que o arquivo de pré-configuração funcione em sistemas com e sem um servidor dhcp.
```
	d-i netcfg/dhcp_failed note
	d-i netcfg/dhcp_options select Configure network manually
```
Para configuração estática da rede
```
	IPv4 exemplo
	d-i netcfg/get_ipaddress string 192.168.1.42
	d-i netcfg/get_netmask string 255.255.255.0
	d-i netcfg/get_gateway string 192.168.1.1
	d-i netcfg/get_nameservers string 192.168.1.1
	d-i netcfg/confirm_static boolean true

	IPv6 exemplo
	d-i netcfg/get_ipaddress string fc00::2
	d-i netcfg/get_netmask string ffff:ffff:ffff:ffff::
	d-i netcfg/get_gateway string fc00::1
	d-i netcfg/get_nameservers string fc00::1
	d-i netcfg/confirm_static boolean true
```
Qualquer nome de host e nomes de domínio atribuídos a partir do dhcp têm precedência sobre valores definidos aqui. No entanto, a configuração dos valores ainda impede as perguntas de ser mostrado, mesmo se os valores vierem do dhcp. (Isso segundo a documentação, na pratica o arquivo só será utilizado após esse ponto, pois aqui a maquina recebe um ip da rede e comeca a trafegar dados.)
```
	d-i netcfg/get_hostname string unassigned-hostname
	d-i netcfg/get_domain string unassigned-domain
```
 Se você quiser forçar um nome de host, independentemente de qual seja o DHCP server retorna ou o que é a entrada de DNS reversa para o IP, descomente e ajuste a linha a seguir.
```
 	d-i netcfg/hostname string somehost
```
Desabilitar caixa de diálogo da senha WEP
```
	d-i netcfg/wireless_wep string
```
ISPs usam como uma espécie de senha
```
 	d-i netcfg/dhcp_hostname string radish
```
Se for necessário firmware não livre para a rede ou outro hardware, você pode configure o instalador para sempre tentar carregá-lo, sem avisar. Ou mude para falso para desativar a pergunta.
```
	d-i hw-detect/load_firmware boolean true
```
Use as seguintes configurações se você deseja usar o console de rede componente para instalação remota via SSH. Isso só faz sentido se você pretendem executar o restante da instalação manualmente.
```
	d-i anna/choose_modules string network-console
	d-i network-console/authorized_keys_url string http://10.0.0.1/openssh-key
	d-i network-console/password password r00tme
	d-i network-console/password-again password r00tme
```

Configurações de espelho, se você selecionar ftp, a sequência de espelhamento/país não precisará ser definida.
```
	d-i mirror/protocol string ftp
	d-i mirror/country string manual
	d-i mirror/http/hostname string http.us.debian.org
	d-i mirror/http/directory string /debian
	d-i mirror/http/proxy string
```
Suite de instalação.
```
	d-i mirror/suite string testing
```
Suite a ser usada para carregar componentes do instalador(opcional)
```
d-i mirror/udeb/suite string testing
```
Configuracao das contas
Pula a criação do root (o usuário normal pode usar o sudo).
```
	d-i passwd/root-login boolean false
```
Alternativa para não criar usuário normal.
```
	d-i passwd/make-user boolean false
```
Senha do root, com texto simple
```
	d-i passwd/root-password password r00tme
	d-i passwd/root-password-again password r00tme
```
Criptografado usanda hash MD5.
```
	d-i passwd/root-password-crypted password [MD5 hash]
```
Para criar uma conta de usuário normal.
```
	d-i passwd/user-fullname string Debian User
	d-i passwd/username string debian
```
Senha do usuário normal, com texto simples
```
	d-i passwd/user-password password insecure
	d-i passwd/user-password-again password insecure
```
Criptografado usando um hash MD5.
```
	d-i passwd/user-password-crypted password [MD5 hash]
```
Crie o primeiro usuário com o UID especificado em vez do padrão.
```
	d-i passwd/user-uid string 1010
```
A conta do usuário será adicionada a alguns grupos iniciais padrão. Para substituir isso, use isso.
```
	d-i passwd/user-default-groups string audio cdrom video
```
