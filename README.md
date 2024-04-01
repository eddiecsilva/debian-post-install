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

# Objetivos
Esse script funciona como um guia passo a passo para apoiar a pós-instalação/configuração de uma máquina de trabalho baseada em Debian 12 para atividades de edição de vídeo, edição fotográfica e redação para web.

O objetivo deste roteiro **não é ser um script totalmente automatizado**, ele foi testado e recomendo seu uso apenas no Debian 12 Bookworm. Caso você queira seguir este roteiro em distros com outras bases, lembre-se de modificar os pacotes e comandos necessários por conta e risco, moldando conforme necessário para seu sistema, fique à vontade.

A seleção de programas escolhidos neste roteiro, é a que utilizo em minha rotina de trabalho atual, então, remova ou adicione programas de acordo com sua necessidade.

**Haverão algumas configurações extras relacionadas com jogos, mas isso é um bônus.** :-)

---

Este roteiro aborda os seguintes tópicos

Preparação do Debian 12:
- Ativação de repositórios extras (DebMultimedia).
- Instalação drivers de vídeo.
- Ativação do suporte a flatpaks.

Programas:
- Instalação do Davinci Resolve Gratuito.
- Instalação de ferramentas gráficas: Gimp, Inskcape, Shotcut, ColorPicker, Fontbase.
- Instalação de navegadores web: Google Chrome, Microsoft Edge, Firefox e Chromium.
- Instalação de programas diversos: Winff, Video Trimmer, MPV.
- Ferramentas de sistema: Timeshift, Pika Backup, Boxes, VirtualBox.

---
Neste roteiro considero que estamos partindo de uma instalação padrão do Debian 12 com o ambiente GNOME, com todas as atualizações recomendadas instaladas. A **instalação mínima** pode apresentar erros na instalação do Davinci Resolve, fique atento nas mensagens de erro para instalar os pacotes extras que forem necessários.

Meu setup padrão considera que será utilizada uma GPU Nvidia RTX 3060TI e um processador AMD Ryzen 7 5700X. Por fim, eu prefiro utilizar o formato flatpak sempre que possível, adapte conforme suas preferências.

# Prepração do Debian 12 Bookworm

## Adicionar DebMultimedia
O repositório DebMultimedia é um projeto não oficial que disponibiliza alguns pacotes relacionados com codecs e ferramentas de multimedia que não podem ser distribuídos oficialmente por limitações de licença, como o FFMPEG com suporte a aceleração de hardware Nvidia, por exemplo. Trata-se de um repositório de terceiros, então, esteja ciente disso.

```
echo "deb https://www.deb-multimedia.org bookworm main non-free" > /etc/apt/sources.list.d/deb-multimedia.list
apt-get update -oAcquire::AllowInsecureRepositories=true
apt-get install deb-multimedia-keyring
apt-get update; apt-get dist-upgrade
```
https://img.youtube.com/vi/SSE5KYGLn8Q/maxresdefault.jpg


## Instalação do Nvidia CUDA
Os drivers da Nvidia estão disponíveis nos repositórios padrão da distro, para instá-los você precisa ativiar os repositórios "non-free-firmware contrib non-free" no Debian.

NÃO RECOMENDO usar o script fornecido pela Nvidia, use os pacotes fornecidos pelo distro para facilitar a manutenção do sistema.

[![Driver NVIDIA no Debian - Guia COMPLETO para instalar e configurar](https://img.youtube.com/vi/SSE5KYGLn8Q/hqdefault.jpg)](https://youtu.be/SSE5KYGLn8Q)


Após ativar os repositórios extras, basta fazer uma atualização completa do sistema e executar os comandos abaixo.
```
sudo apt install nvidia-driver nvidia-opencl-icd libcuda1 libglu1-mesa libnvidia-encode1
```

**Ativação do suporte a flatpak no sistema**
```
sudo apt install gpm flatpak gnome-software-plugin-flatpak
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```


--

# Instalação do Davinci Resolve Gratuito
Faça o download da [versão gratuita do Davinci Resolve gratuito](https://www.blackmagicdesign.com/br/products/davinciresolve) no site oficial da Black Magic, em meu uso diário não tenho enfrentado nenhum problema com o instalador padrão do Resolve. 

**Resolução de dependências para o Davinci Resolve:** em algumas instalações o Davinci Resolve não inicia devido a falta de dependências no sistema, uma das formas de corrigir este problema é instalar os pacotes abaixo no Debian 12.

```
sudo apt install libxcb-composite0 libxcb-cursor0 libxcb-xinerama0 libxcb-xinput0
```

**OBS.:** Tenho observado alguns bugs na versão 18.6, principalmente relacionado com a gestão das timelines, por isso, recomendo que você faça alguns testes e valide se no seu ambiente está tudo funcionando corretamente. No momento, sigo utilizando a versão 18.5.


**Contornar erro de instalação "do pacote"**
Este problema ocorreu comigo apenas na instalação do Davinci Resolve no openSUSE Tumbleweed, porém, deixo aqui anotado caso afete a instalação de outras pessoas.

```
SKIP_PACKAGE_CHECK=1 ./DaVinci_Resolve_18.X_Linux.run
```

---

## Instalação de ferramentas gráficas: Gimp, Inskcape, Shotcut, ColorPicker, Fontbase.
Canivete suíço de criação de conteúdo, tratamento de imagens, desenho vetorial e edição de vídeo usando software livre.

```
flatpak install org.gimp.GIMP com.obsproject.Studio nl.hjdskes.gcolor3 org.flameshot.Flameshot org.inkscape.Inkscape org.shotcut.Shotcut
```

## Instalação de navegadores web: Google Chrome, Microsoft Edge, Firefox e Chromium.
Eu deixo os principais navegadores instalados para que possa fazer diversos tipos de testes em sites e aplicativos web.

```
flatpak install com.google.Chrome com.microsoft.Edge
```

## Instalação de programas diversos: Winff, Video Trimmer, MPV, Timeshift, Pika Backup, Boxes, VirtualBox.
Esta sessão é totalmente livre e aqui listo vários programas auxiliares que utilizo diariamente, sugiro fortemente que daqui para baixo, ajuste conforme suas preferências.

```
flatpak install com.system76.Popsicle md.obsidian.Obsidian org.onlyoffice.desktopeditors org.gnome.gitlab.YaLTeR.VideoTrimmer org.x.Warpinator
```

```
flatpak install com.usebottles.bottles org.gnome.World.PikaBackup com.github.tchx84.Flatseal org.gnome.Boxes com.system76.Popsicle md.obsidian.Obsidian org.onlyoffice.desktopeditors
```

```
sudo apt install vim bashtop fish gpm yt-dlp ttf-mscorefonts-installer fonts-bebas-neue chromium
```

--

**Extensões do GNOME**
Apesar de não ser incentivado pelo projeto GNOME, ainda utilizo algumas extensões em meu ambiente.

https://extensions.gnome.org/extension/615/appindicator-support/
https://github.com/GSConnect/gnome-shell-extension-gsconnect/wiki

**Remoção de pacotes desnecessários**
```
sudo apt purge libreoffice-common gnome-games --autoremove
```

## Jogos
```
flatpak install com.valvesoftware.Steam com.valvesoftware.Steam.Utility.MangoHud com.valvesoftware.Steam.Utility.vkBasalt com.valvesoftware.Steam.VulkanLayer.MangoHud com.github.tchx84.Flatseal com.heroicgameslauncher.hgl
```
Se for necessário, libere as permissões do pacote flatpak do Steam para acessar outras unidades de disco.

**Download de vídeos do YouTube**
yt-dlp --merge-output-format mp4 https://youtu.be/ab5AXz-GEVU