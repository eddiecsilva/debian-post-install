<p align="center">
<img width="800px" src="https://raw.githubusercontent.com/eddiecsilva/debian-post-install/main/img/project_thumb.png" align="center" alt="white" /><br><br>
 
<!-- (site para ícones: https://shields.io/ ) -->
 
<img alt="Maintained" src="https://img.shields.io/badge/Maintained%3F-Yes-green">
<img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/eddiecsilva/debian-post-install">
<img alt="GitHub repo size" src="https://img.shields.io/github/repo-size/eddiecsilva/debian-post-install">
<img alt="GitHub commit activity (branch)" src="https://img.shields.io/github/commit-activity/y/eddiecsilva/debian-post-install">

</p>


# AVISO
Ao usar este roteiro você assume que entende os riscos e assume total responsabilidade por suas ações. Todos os arquivos que fazem parte desse repositório são distribuídos livremente para serem adaptados. Porém, não há nenhuma garantia implícita ou explícita do seu funcionamento.

---


# Objetivos
Esse roteiro funciona como um guia passo a passo para apoiar a pós-instalação/configuração de uma máquina de trabalho baseada em Debian 12 para atividades de edição de vídeo, edição fotográfica e redação para web.

O objetivo deste roteiro **não é ser um script totalmente automatizado**, utilizo ele em meu ambiente, sendo  recomendado e testado apenas no Debian 12 Bookworm. Caso você queira seguir este roteiro em distros com outras bases, lembre-se de modificar os pacotes e comandos necessários por conta e risco, moldando conforme necessário para seu sistema, fique à vontade.

A seleção de programas escolhidos neste roteiro, é a que utilizo em minha rotina de trabalho atual, então, remova ou adicione programas de acordo com sua necessidade. **Haverão algumas configurações extras relacionadas com jogos e ajustes cosméticos, mas isso é um bônus.** :wink:

Este roteiro aborda os seguintes tópicos

Preparação do Debian 12:
- Ativação de repositórios extras (DebMultimedia).
- Instalação drivers de vídeo proprietários Nvidia.
- Ativação do suporte a flatpaks.

Instalação dos programas:
- Davinci Resolve Gratuito.
- Ferramentas gráficas: Gimp, Inskcape, Shotcut, ColorPicker, Fontbase.
- Navegadores web: Google Chrome, Microsoft Edge, Firefox e Chromium.
- Utilários diversos: Winff, Video Trimmer, MPV.
- Ferramentas de sistema: Timeshift, Pika Backup, Boxes, VirtualBox.


---


Neste roteiro considero que estamos partindo de uma instalação padrão do Debian 12 com o ambiente GNOME, com todas as atualizações recomendadas instaladas. A **instalação mínima** pode apresentar erros na instalação do Davinci Resolve, fique atento nas mensagens de erro para instalar os pacotes extras que forem necessários.

Para entender melhor as diferenças entre as versões do Debian, recomendo assistir este vídeo.
[![Então, esse é o SEGREDO da ESTABILIDADE do Debian?](https://img.youtube.com/vi/JK03ZcXYAoE/mqdefault.jpg)](https://youtube.com/watch?v=JK03ZcXYAoE)

Para mais informações sobre o processo de instalação, recomendo assistir o vídeo abaixo.

[![Aprenda a domar o instalador do Debian Linux ](https://img.youtube.com/vi/QOuspK8MARk/mqdefault.jpg)](https://youtu.be/QOuspK8MARk)

Meu setup padrão considera que será utilizada uma GPU Nvidia RTX 3060TI e um processador AMD Ryzen 7 5700X. Por fim, eu prefiro utilizar o formato flatpak sempre que possível, adapte conforme suas preferências.


---


# Preparação do Debian 12 Bookworm

## Ativação de repositórios extras (DebMultimedia)
O repositório DebMultimedia é um projeto não oficial que disponibiliza alguns pacotes relacionados com codecs e ferramentas de multimedia que não podem ser distribuídos oficialmente por limitações de licença, como o FFMPEG com suporte a aceleração de hardware Nvidia, por exemplo. Trata-se de um repositório de terceiros, então, esteja ciente disso.

```shellscript
echo "deb https://www.deb-multimedia.org bookworm main non-free" > /etc/apt/sources.list.d/deb-multimedia.list
apt-get update -oAcquire::AllowInsecureRepositories=true
apt-get install deb-multimedia-keyring
apt-get update; apt-get dist-upgrade
```

## Instalação drivers de vídeo proprietários Nvidia
Os drivers da Nvidia estão disponíveis nos repositórios padrão da distro, para instá-los você precisa ativar os repositórios "non-free-firmware contrib non-free" no Debian. Para poder utilizar os Davinci Resolve e outros programas que usam vídeo acelerado por hardware, além do driver proprietário também é necessário instalar os pacotes CUDA e suas bibliotecas.

NÃO RECOMENDO usar o script .RUN fornecido pela Nvidia, use os pacotes fornecidos pelo distro para facilitar a manutenção do sistema. Ainda não fiz testes com o novo driver opensource da NVIDIA, uma vez que ele ainda não está oficialmente disponível nos repositórios do Debian 12.

[![Driver NVIDIA no Debian - Guia COMPLETO para instalar e configurar](https://img.youtube.com/vi/SSE5KYGLn8Q/mqdefault.jpg)](https://youtu.be/SSE5KYGLn8Q)


Após ativar os repositórios extras, basta fazer uma atualização completa do sistema e executar os comandos abaixo.

```shellscript
sudo apt install nvidia-driver nvidia-opencl-icd firmware-misc-nonfree nvidia-cuda-dev nvidia-cuda-toolkit libcuda1 libglu1-mesa libnvidia-encode1 libnvoptix1
```

**Ativação do suporte a Flatpak no sistema**

```shellscript
sudo apt install flatpak gnome-software-plugin-flatpak
```

```shellscript
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```


---


# Instalação do Davinci Resolve Gratuito
```
OBSERVAÇÃO IMPORTANTE: O Davinci Resolve 19.0 e mais recentes exigem que a versão mínima do CUDA seja a 12.3, o que inviabiliza sua utilização no Debian 12 Bookworm por padrão. Atualmente a versão do CUDA suportada pelo driver 535.x é a 12.2.
```


Faça o download da [versão gratuita do Davinci Resolve gratuito](https://www.blackmagicdesign.com/br/products/davinciresolve) no site oficial da Black Magic, em meu uso diário não tenho enfrentado nenhum problema com o instalador padrão do Resolve. 

**Resolução de dependências para o Davinci Resolve**

Em algumas instalações o Davinci Resolve não inicia devido a falta de dependências no sistema, uma das formas de corrigir este problema é instalar os pacotes abaixo no Debian 12.

```shellscript
sudo apt install libxcb-composite0 libxcb-cursor0 libxcb-xinerama0 libxcb-xinput0
```

**OBS.:** Tenho observado alguns bugs na versão 18.6 e posteriores, principalmente relacionado com a gestão das timelines, por isso, recomendo que você faça alguns testes e valide se no seu ambiente está tudo funcionando corretamente. No momento, sigo utilizando a versão 18.5.


**Contornar erro de instalação "do pacote"** 

Este problema ocorreu comigo apenas na instalação do Davinci Resolve no openSUSE Tumbleweed e em instalações feitas no modo avançado do Debian. Ao executar o instalador, é exibida uma mensagem de que exitem pacotes faltando no sistema e mesmo instalando os pacotes o instalador não inicia.

Deixo aqui anotado caso afete a instalação de outras pessoas.

```shellscript
SKIP_PACKAGE_CHECK=1 ./DaVinci_Resolve_18.X_Linux.run
```

**Corrigir o erro com instalador gráfico do Resolve "libfuse2"**

Caso o instalador gráfico do Davinci Resolve não abra, execute ele via terminal para ver qual é a mensagem de erro. Caso apareça algo similar a "*libfuse.so.2: cannot open shared object file*" - use o comando abaixo para contornar o problema.

```shellscript
apt install -y libfuse2
```


**Resolver problemas com libs do Davinci Resolve"**

O pacote do Davinci Resolve incorpora uma série de bibliotecas que podem conflitar com as versões disponíveis em algumas distros Linux. Existem formas diferentes de contornar esta situação caso ocorra com você, nesta página da [Arch Wiki](https://wiki.archlinux.org/title/DaVinci_Resolve) existem diversas dicas que podem ser úteis. 
Em minhas instalações, geralmente apagar as libs abaixo já resolvem o problema do Resolve. Sugiro que você faça um backup dos arquivos antes de removê-los do sistema. :-)


O comando abaixo cria uma cópia dos arquivos das bibliotecas dentro da home do usuário resolvendo links simbólicos.

```shellscript
tar -cvhzf ~/backup-libs-resolve.tar.gz /opt/resolve/libs/libgmodule-2.0.so* /opt/resolve/libs/libglib-2.0.so* /opt/resolve/libs/libgio-2.0.so*
```

Agora é só apagar as bibliotecas que geralmente dão problemas. **Muita atenção ao executar estes comandos, qualquer erro de digitação pode gerar uma quebra severa do sistema.**

```shellscript
sudo rm /opt/resolve/libs/libgmodule-2.0.so*
sudo rm /opt/resolve/libs/libglib-2.0.so*
sudo rm /opt/resolve/libs/libgio-2.0.so*
```

---

# Preparação do ambiente para produtividade

## Instalação de ferramentas gráficas: Gimp, Inskcape, Shotcut, ColorPicker.

Canivete suíço de criação de conteúdo, tratamento de imagens, desenho vetorial e edição de vídeo usando software livre.

```shellscript
flatpak install org.gimp.GIMP com.obsproject.Studio nl.hjdskes.gcolor3 org.flameshot.Flameshot org.inkscape.Inkscape org.shotcut.Shotcut
```


## Instalação de navegadores web: Google Chrome, Microsoft Edge, Firefox e Chromium.

Eu deixo os principais navegadores instalados para que possa fazer diversos tipos de testes em sites e aplicativos web. O Firefox e o Chromium instalo as versões do repositório do Debian.

```shellscript
flatpak install com.google.Chrome com.microsoft.Edge
```


## Instalação de programas diversos: Winff, Video Trimmer, MPV, Timeshift, Boxes, VirtualBox.

Esta sessão é totalmente livre e aqui listo vários programas auxiliares que utilizo diariamente, sugiro fortemente que daqui para baixo, ajuste conforme suas preferências.

```shellscript
flatpak install com.system76.Popsicle md.obsidian.Obsidian org.onlyoffice.desktopeditors org.gnome.gitlab.YaLTeR.VideoTrimmer org.x.Warpinator
```

```shellscript
flatpak install com.usebottles.bottles com.github.tchx84.Flatseal org.gnome.Boxes
```

```shellscript
sudo apt install vim bashtop fish gpm yt-dlp ttf-mscorefonts-installer fonts-bebas-neue chromium aria2
```

Obs.: parei de utilizar o Pika Backup após sofrer 2 corrompimentos seguidos de backup que não puderam ser recuperados.


---


# Configurações extras

**Jogos**

Instala os pacotes flatpak necessários para a Steam e Heroic Games Launcher.

```shellscript
flatpak install com.valvesoftware.Steam com.valvesoftware.Steam.Utility.MangoHud com.valvesoftware.Steam.Utility.vkBasalt com.valvesoftware.Steam.VulkanLayer.MangoHud com.heroicgameslauncher.hgl
```
Se for necessário, utilizando o FlatSeal libere as permissões do pacote flatpak do Steam para acessar outras unidades de disco.


**Extensões do GNOME**

Apesar de não ser incentivado pelo projeto GNOME, ainda utilizo algumas extensões em meu ambiente.
- [AppIndicator and KStatusNotifierItem Support](https://extensions.gnome.org/extension/615/appindicator-support/)
- [GSConnect](https://github.com/GSConnect/gnome-shell-extension-gsconnect/wiki)
- [Blur My Shell](https://github.com/aunetx/blur-my-shell)
- [Screenshot Window Sizer](https://extensions.gnome.org/extension/881/screenshot-window-sizer/)


**Remoção de pacotes desnecessários**

Limpeza de pacotes que são instalados por padrão e que não utilizo em minha rotina.

```shellscript
sudo apt purge libreoffice-common gnome-games --autoremove
```

**Tema de ícones**

Nas minhas instalações eu gosto de utilizar o tema para ícones [Papirus](https://github.com/PapirusDevelopmentTeam/papirus-icon-theme) na variante dark, ele existe nos repositórios oficiais. Um dos motivos para utilizar este tema é que ele cobre todos os programas que eu uso, no tema Adwaita padrão, faltam ícones para diversos programas.

Basta instalar o tema e ativar usando o GNOME Ajustes.

```shellscript
sudo apt install papirus-icon-theme
```