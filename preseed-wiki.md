##Considerações

Após me ser concedido o desafio de automatizar a formatação de máquinas físicas encontrei algumas dificuldades e com esse material desejo sanar algumas das que você, caro leitor, possa ter também.

Será explicado cada seção da receita, a base foi retirada do site do [Debian](https://www.debian.org/releases/wheezy/example-preseed.txt). E no final sera deixado a minha receita final.

##Como utilizar o arquivo

O resultado da receita tem que ser salvo em um arquivo com formato .cfg e deixar acessivel a maquina que o utilizara, pode ser via rede ou dentro do pendrive/cd utilizado para formatar.

Nesse caso apresento a forma pela rede.

Ao dar boot no pendrive/cd para instalação terá essa tela

![boot-install](https://raw.githubusercontent.com/RummeniggePires/preseed-debian/master/img/boot-debian.png)

Precione a tecla TAB para editar a inicialização da instalação

![boot-edit](https://raw.githubusercontent.com/RummeniggePires/preseed-debian/master/img/boot-edit.png)

Após a palavra quiet adione auto url=http://endereco/do/arquivo/preseed.cfg e tecle enter.

##Como fazer seu preseed-debian

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
