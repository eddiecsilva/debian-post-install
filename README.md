<p align="center">
<img width="800px" src="https://raw.githubusercontent.com/eddiecsilva/debian-post-install/main/img/project_thumb.png" align="center" alt="white" /></br>
 
<!-- (site para ícones: https://shields.io/ ) -->
 
<img alt="Maintained" src="https://img.shields.io/badge/Maintained%3F-Yes-green">
<img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/eddiecsilva/debian-post-install">
<img alt="GitHub repo size" src="https://img.shields.io/github/repo-size/eddiecsilva/debian-post-install">
<img alt="GitHub commit activity (branch)" src="https://img.shields.io/github/commit-activity/y/eddiecsilva/debian-post-install">

</p>
</br>


# Sumário
- Introdução
- Pós-instalação do Debian 13 Trixie
	- Configuração básica do Firewall
	- Ativação do DebMultimedia
	- Instalação drivers proprietários NVIDIA
	- Ativação do suporte a Flatpak no sistema
	- Instalação do Davinci Resolve Gratuito / Studio
- Preparação do ambiente para produtividade
- Configurações extras para Jogos
- Remoção de pacotes desnecessários

</br>

# Introdução

Esse roteiro funciona como um guia passo a passo para apoiar a pós-instalação, configuração e otimização de uma máquina de trabalho baseada em Debian 13 para atividades de edição de vídeo, edição fotográfica e redação para web.

Ele é testado apenas no Debian 13 Trixie, caso você queira seguir este roteiro em distros com outras bases, lembre-se de modificar os pacotes e comandos necessários por sua conta e risco, moldando conforme necessário para seu sistema.

> [!NOTE]  
> LEIA COM ATENÇÃO OS ROTEIROS ANTES DE FORMATAR:
>
> Aqui você encontra uma versão deste roteiro para o **[Debian 12 - Bookworm](debian-12-bookworm.md)**.
> 
> Já existe um trabalho em progresso para validar este roteiro para a próxima versão que será o **[Debian 14 (Forky)](debian-14-forky.md)**.


</br>

Neste roteiro considero que estamos partindo de uma **instalação limpa do Debian 13 com o ambiente KDE Plasma**, com todas as atualizações recomendadas instaladas. A **instalação mínima** pode dificultar a instalação do Davinci Resolve, fique atento nas mensagens de erro para instalar os pacotes extras que forem necessários.

> [!CAUTION]  
>  O objetivo deste roteiro **não é ser um script totalmente automatizado**, se deseja uma ferramenta que configure tudo para você, sugiro testar o projeto [LinuxToys](https://github.com/psygreg/linuxtoys).

</br>

Minha máquina de trabalho utiliza uma GPU Nvidia RTX 5070TI e um processador AMD Ryzen 7 5700X. Por fim, eu prefiro utilizar o formato flatpak sempre que possível, adapte conforme suas preferências.

| Para mais informações sobre o processo de instalação, assista o vídeo abaixo.                                                          | Para entender melhor as novidades e mudanças incorporadas no Debian 13 (Trixie), recomendo assistir este vídeo.         |
| -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| [![Aprenda a domar o instalador do Debian Linux](https://img.youtube.com/vi/QOuspK8MARk/mqdefault.jpg)](https://youtu.be/QOuspK8MARk) | [![O que vem por aí no Debian 13](https://img.youtube.com/vi/3V5wCdeNt6s/mqdefault.jpg)](https://youtu.be/3V5wCdeNt6s) |

</br>

# Preparação do Debian 13 Trixie

Neste guia eu sugiro usar a **Instalação Avançada do Debian** para ativar os repositórios NON-FREE e NON-FREE-FIRMWARE já na instalação. Não são passos obrigatórios, mas já adiantam alguns processos.

* Ativar e configurar o firewall (UFW).
* Ativação de repositórios extras (DebMultimedia e NVIDIA).
* Instalação dos drivers de vídeo proprietários NVIDIA.
* Ativação do suporte a flatpaks.
* Ajustes básicos do sistema.
* Notas sobre problemas encontrados até o momento.

</br>

## Instalação e configuração básica do Firewall
Ativar um firewall é um cuidado recomendado para diminuir as chances de expor seu computador online, é claro que, para ter realmente segurança online é necessário tomar outros cuidados. 

> [!NOTE]  
> Os comandos abaixo liberam a comunicação nas portas indicadas para quaisquer equipamentos da rede, uma recomendação extra de segurança é liberar o acesso apenas para a sua rede local.

Instalar e habilitar o UFW no Debian 13
```
sudo apt install ufw
sudo ufw enable
```

Libera acesso na rede para o [KDEConnect](https://youtu.be/GTv0o0sOuyQ?si=nWhdiEEeHXKcFTaV)
```
sudo ufw allow 1714:1764/udp
sudo ufw allow 1714:1764/tcp
```

Libera acesso na rede para o [Touch Portal](https://www.touch-portal.com/)
```
sudo ufw allow 12135/tcp
```

Libera acesso na rede para o [Warpinator](https://youtu.be/oayBzLEeIwg?si=3puB8U34rz8NNyqu)
```
sudo ufw allow 42000:42001/udp
sudo ufw allow 42000:42001/tcp
```

</br>

## Ativação de repositórios extras (DebMultimedia)
O repositório DebMultimedia é um projeto não oficial que disponibiliza alguns pacotes relacionados com codecs e ferramentas de multimídia que não podem ser distribuídos oficialmente no Debian por limitações de licença, como o FFMPEG com suporte a aceleração de hardware Nvidia, por exemplo. 

Trata-se de um repositório de terceiros, então, esteja ciente disso. O projeto DebMultimedia não oferece o componente CONTRIB em seus servidores.

```
wget https://www.deb-multimedia.org/pool/main/d/deb-multimedia-keyring/deb-multimedia-keyring_2024.9.1_all.deb
sudo dpkg -i deb-multimedia-keyring_2024.9.1_all.deb
```

Conforme o padrão DEB822, o formato correto do arquivo sources deverá ser salvo em "/etc/apt/sources.list.d/dmo.sources" com o conteúdo abaixo.

```
Types: deb
URIs: https://www.deb-multimedia.org
Suites: trixie
Components: main non-free
Signed-By: /usr/share/keyrings/deb-multimedia-keyring.pgp
Enabled: yes
```


**Opcionalmente** você pode executar o comando abaixo para conferir se todas as entradas para repositórios já estão corretamente formatadas. 

```
sudo apt modernize-sources
```

</br>

## Instalação drivers de vídeo proprietários Nvidia
Para poder utilizar programas que usam vídeo acelerado por hardware, além do driver proprietário também é necessário instalar os pacotes CUDA e suas bibliotecas. No Debian 13 atualmente apenas o driver 550 está disponível e ele não oferece todas as dependências necessárias para utilizar o Davinci Resolve.

Por isso, iremos utilizar os [pacotes gerados pela própria NVIDIA](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Debian&target_version=12&target_type=deb_network) para instalar a versão mais recente do driver, do CUDA e também das bibliotecas auxiliares.

---
**Dependências importantes:** Para garantir que os módulos do kernel sejam corretamente compilados, é recomendável que os pacotes abaixo estejam instalados.

```
sudo apt install --no-install-recommends dkms libdw-dev clang lld llvm build-essential linux-headers-amd64 pipewire-audio-client-libraries
```

---
A NVIDIA já publica um repositório para o Debian 13 (Trixie), os comandos abaixo usam ele.

```
wget https://developer.download.nvidia.com/compute/cuda/repos/debian13/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt update
```

Se você usa sua máquina apenas para jogos, consumo de mídia e/ou edição de vídeo, instalar o pacote nvidia-open cobre todo o setup básico.

```
sudo apt -y install nvidia-open
```

Se você **desenvolve** aplicações CUDA ou precisa de outros recursos avançados para IA - é recomendável instalar o pacote CUDA-DRIVERS.

```
sudo apt -y install cuda-drivers cuda-toolkit
```

---
Para evitar problemas com "disputas" entre os pacotes do Debian e do repositório da NVIDIA, sugiro [aumentar prioridade do repositório da NVIDIA](https://wiki.debian.org/AptConfiguration). Isso vai informar ao sistema que caso os mesmos pacotes existam nas duas fontes, o repositório da NVIDIA sempre terá prioridade para ser instalado.

Crie um arquivo de configuração em "/etc/apt/preferences.d" com o seguinte conteúdo:

```
sudo vim /etc/apt/preferences.d/nvidia-repo
```

```
Package: *
Pin: origin developer.download.nvidia.com
Pin-Priority: 900
```

> [!NOTE]  
> O APT compara apenas o nome do servidor na linha "Pin: origin", sem "https://". Para conferir se a prioridade foi aplicada, rode `apt-cache policy` e procure o valor 900 ao lado do repositório da NVIDIA.
</br>

## Ativação do suporte a Flatpak no sistema

Pacotes necessários para ativar o suporte a [pacotes flatpak no Debian](https://flathub.org/setup/Debian) e adicionar o repositório Flathub.

```
sudo apt install flatpak plasma-discover-backend-flatpak
```

```
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```
</br>

# Preparação do ambiente para produtividade
A seleção de programas escolhidos neste roteiro, é a que utilizo em minha rotina de trabalho atual, então, remova ou adicione programas de acordo com sua necessidade.

</br>

### Ferramentas gráficas: Gimp, Inkscape, OBS Studio, Shotcut.
Canivete suíço de criação de conteúdo, tratamento de imagens, desenho vetorial e edição de vídeo usando software livre.

```
flatpak install org.gimp.GIMP com.obsproject.Studio \
org.inkscape.Inkscape org.shotcut.Shotcut
```

</br>

### Navegadores web: Google Chrome, Microsoft Edge, Zen Browser e Firefox.
Eu deixo os principais navegadores instalados para que possa fazer diversos tipos de testes em sites e aplicativos web. Apenas o Chromium é instalado usando as versões do repositório do Debian.

```
flatpak install com.google.Chrome com.microsoft.Edge org.mozilla.firefox app.zen_browser.zen
```

</br>

### Programas diversos: Handbrake, Video Trimmer, Celluloid, Boxes e outros.
Aqui listo vários programas auxiliares que utilizo diariamente, sugiro que daqui para baixo, ajuste conforme suas preferências.

```
flatpak install md.obsidian.Obsidian org.onlyoffice.desktopeditors \
com.usebottles.bottles com.github.tchx84.Flatseal org.gnome.Boxes \
io.missioncenter.MissionCenter com.dec05eba.gpu_screen_recorder \
fr.handbrake.ghb io.github.celluloid_player.Celluloid
```

```
sudo apt install vim btop fish gpm yt-dlp fonts-bebas-neue chromium aria2 \
gstreamer1.0-plugins-ugly gstreamer1.0-plugins-bad tmux
```

</br>

### Instalação do Davinci Resolve Gratuito / Studio
Faça o download da versão adequada do [Davinci Resolve](https://www.blackmagicdesign.com/br/products/davinciresolve) no site oficial da Black Magic Design, em meu uso diário não tenho enfrentado nenhum problema com o instalador padrão.

As soluções abaixo são um compilado de anotações que acumulei ao longo dos anos usando o Davinci Resolve em diversas distros.

> [!WARNING]
> OBSERVAÇÃO IMPORTANTE: O Davinci Resolve 20.x e mais recentes exigem que a versão mínima do CUDA seja a 12.8, que está disponível apenas à partir do driver 570 da Nvidia.

**Contornar erro de instalação "do pacote"**
Mesmo instalando os pacotes o instalador não inicia, o comando abaixo contorna essa situação e não afeta o funcionamento normal do Davinci Resolve. Lembre de alterar o nome do arquivo de instalação de acordo com a versão que você estiver instalando.

```
SKIP_PACKAGE_CHECK=1 ./DaVinci_Resolve_Studio_20.0b2_Linux.run
```

---
**Dependências para o Davinci Resolve**
Em algumas instalações o Davinci Resolve não inicia devido a falta de dependências no sistema, uma das formas de corrigir este problema é conferir os pacotes abaixo estão instalados no Debian 13.

```
sudo apt install libxcb-composite0 libxcb-cursor0 libxcb-xinerama0 libxcb-xinput0 pkexec
```

---
**Corrigir o erro com instalador gráfico do Resolve "libfuse2"**
Caso o instalador gráfico do Davinci Resolve não abra, execute ele via terminal para ver qual é a mensagem de erro. Caso apareça algo similar a "*libfuse.so.2: cannot open shared object file*" - use o comando abaixo para solucionar o problema.

```
sudo apt install -y libfuse2
```

No Debian 13 o pacote passou a se chamar libfuse2t64, o APT escolhe ele automaticamente.

---
**Resolver problemas com libs do Davinci Resolve**
O pacote do Davinci Resolve incorpora uma série de bibliotecas que podem conflitar com as versões disponíveis em algumas distros Linux.

Existem formas diferentes de contornar esta situação caso ocorra com você, nesta página da [Arch Wiki](https://wiki.archlinux.org/title/DaVinci_Resolve) existem dicas que podem ser úteis. Em minhas instalações, geralmente apagar as libs abaixo já soluciona o problema do Davinci Resolve.

> [!WARNING]  
> Sugiro que você faça um backup dos arquivos antes de removê-los do sistema.

O comando abaixo cria uma cópia das bibliotecas dentro da home do usuário resolvendo links simbólicos.

```
tar -cvhzf ~/backup-libs-resolve.tar.gz /opt/resolve/libs/libgmodule-2.0.so* /opt/resolve/libs/libglib-2.0.so* /opt/resolve/libs/libgio-2.0.so*
```

Agora é só apagar as bibliotecas. **Muita atenção ao executar estes comandos, qualquer erro de digitação pode gerar uma quebra severa do sistema.**

```
sudo rm /opt/resolve/libs/libgmodule-2.0.so*
sudo rm /opt/resolve/libs/libglib-2.0.so*
sudo rm /opt/resolve/libs/libgio-2.0.so*
```

</br>

# Configurações extras para Jogos
Instala os pacotes flatpak necessários para a Steam e Heroic Games Launcher.

```
flatpak install com.valvesoftware.Steam com.valvesoftware.Steam.Utility.vkBasalt \
com.heroicgameslauncher.hgl com.github.Matoking.protontricks com.github.tchx84.Flatseal \
io.github.radiolamp.mangojuice org.vinegarhq.Sober

```

Se for necessário, utilizando o FlatSeal libere as permissões do pacote flatpak do Steam para acessar outras unidades de disco.

</br>

# Remoção de pacotes desnecessários
Limpeza de pacotes que são instalados por padrão e que não utilizo em minha rotina.

```
sudo apt autoremove libreoffice-common \
akregator kontrast kmouth dragonplayer \
kmail juk xterm firefox-esr konqueror
```

</br>

# Configurações ainda em testes
> [!NOTE]  
> Aqui começa a parte experimental do roteiro, são mudanças em relação ao meu ambiente padrão que estou validando.

### Ativar o ZSWAP

Ativar o ZSWAP ou o ZRAM pode impactar no desempenho de diversas formas, o uso do ZRAM parece ser um consenso entre a maioria das distros.  Ainda estou fazendo testes com esse recurso, em meu ambiente atualmente uso SystemD-Boot com ZSWAP ativo.

Parâmetro
```
zswap.enabled=1
```

**Com systemd-boot:** no Debian, as entradas de boot são recriadas pelo `kernel-install` a cada kernel instalado, a partir do arquivo "/etc/kernel/cmdline". Se ele ainda não existir, crie com as opções atuais do sistema e acrescente o parâmetro no final da linha.

```
sudo sh -c 'tr " " "\n" < /proc/cmdline | grep -v -e "^BOOT_IMAGE=" -e "^initrd=" | tr "\n" " " > /etc/kernel/cmdline'
sudo vim /etc/kernel/cmdline
```

Depois recrie a entrada do kernel atual e reinicie o computador.
```
sudo kernel-install add $(uname -r) /boot/vmlinuz-$(uname -r)
```

> [!NOTE]  
> O comando `bootctl update` atualiza apenas o próprio systemd-boot, ele não altera os parâmetros do kernel.

**Com GRUB (padrão do instalador do Debian):** acrescente o parâmetro na linha GRUB_CMDLINE_LINUX_DEFAULT do arquivo "/etc/default/grub", por exemplo `GRUB_CMDLINE_LINUX_DEFAULT="quiet zswap.enabled=1"`, depois atualize o GRUB e reinicie.
```
sudo vim /etc/default/grub
sudo update-grub
```

Documentação de referência sobre ZSWAP: [1](https://wiki.debian.org/Zswap) [2](https://wiki.archlinux.org/title/Zswap) [3](https://wiki.archlinux.org/title/Kernel_parameters)

</br>

# Tutoriais e Programas interessantes

- [Debian descomplicado: da formatação à produtividade](https://plus.diolinux.com.br/t/debian-descomplicado-da-formatacao-a-produtividade/35806?u=eddiecsilva)
- [Configurando a Steam Flatpak: discos, MangoHUD, Gamemode e Remote Play](https://plus.diolinux.com.br/t/configurando-a-steam-flatpak-discos-mangohud-gamemode-e-remote-play/47160?u=eddiecsilva)
- [Como instalar o MangoHud corretamente no Heroic Games Launcher](https://plus.diolinux.com.br/t/como-instalar-o-mangohud-corretamente-no-heroic-games-launcher/72159?u=eddiecsilva)
- [Davinci Resolve FREE no Linux: Setup Perfeito em 2024](https://plus.diolinux.com.br/t/davinci-resolve-free-no-linux-setup-perfeito-em-2024/67074?u=eddiecsilva)

- **[MakeResolveDeb](https://www.danieltufvesson.com/makeresolvedeb)** - Você pode usar o projeto MakeResolveDeb para converter o instalador do Resolve para um pacote compatível com base Debian - sua principal vantagem é permitir que a instalação do Resolve seja gerenciada como um pacote nativo do Debian.
- **[LinuxToys](https://github.com/psygreg/linuxtoys)** - facilita instalar diversas otimizações presentes no CachyOS como o [CachyOS Tweaks ](https://wiki.cachyos.org/features/cachyos_settings/) e um kernel otimizado, o [PsyCachy](https://github.com/psygreg/linux-psycachy/releases) em diversas distros. A ferramenta está em constante evolução e auxilia na instalação de programas com configurações otimizadas.
- **Alpaca** - Ferramenta para interagir com IAs localmente: [com.jeffser.Alpaca](https://flathub.org/apps/com.jeffser.Alpaca)
- **Vesktop** - Cliente não oficial para Discord com recursos extras: [dev.vencord.Vesktop](https://flathub.org/apps/dev.vencord.Vesktop)
- **FlatSweep** - limpador de resíduos de pacotes flatpak: [FlatSweep](https://flathub.org/apps/io.github.giantpinkrobots.flatsweep)
- **CachyOs NVIDIA** - Otimizações para GPU - https://github.com/CachyOS/CachyOS-Settings/blob/master/usr/lib/modprobe.d/nvidia.conf
