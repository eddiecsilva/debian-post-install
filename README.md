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
- Instalação drivers de vídeo.
- Ativação de repositórios extras (Marillat).
- Ativação do suporte a flatpaks.

Programas:
- Instalação do Davinci Resolve Gratuito.
- Instalação de ferramentas gráficas: Gimp, Inskcape, Shotcut, ColorPicker, Fontbase.
- Instalação de navegadores web: Google Chrome, Microsoft Edge, Firefox e Chromium.
- Instalação de programas diversos: Winff, Video Trimmer, MPV.
- Ferramentas de sistema: Timeshift, Pika Backup, Boxes, VirtualBox.

---
Neste cenário considero que estamos partindo de uma instalação limpa do Debian 12 com o ambiente GNOME, com todas as atualizações recomendadas instaladas.
Meu setup padrão considera que será utilizada uma GPU Nvidia RTX 3060TI e um processador AMD Ryzen 7 5700X. 

# Prepração do Debian 12 Bookworm

## Adicionar DebMultimedia
O repositório DebMultimedia é um projeto não oficial que disponibiliza alguns pacotes relacionados com codecs e ferramentas de multimedia que não podem ser distribuídos oficialmente por limitações de licença. Trata-se de um repositório de terceiros, então, esteja ciente disso.

```
echo "**deb https://www.deb-multimedia.org bookworm main non-free**" > /etc/apt/sources.list.d/deb-multimedia.list
apt-get update -oAcquire::AllowInsecureRepositories=true
apt-get install deb-multimedia-keyring
apt-get update; apt-get dist-upgrade
```

**Resolução de dependências**
```
sudo apt install libxcb-composite0 libxcb-cursor0 libxcb-xinerama0 libxcb-xinput0
```

**Contornar erro de instalação "do pacote"**
SKIP_PACKAGE_CHECK=1 ./DaVinci_Resolve_18.X_Linux.run

## Instalação do Nvidia CUDA
```
sudo apt-get install nvidia-driver nvidia-opencl-icd libcuda1 libglu1-mesa libnvidia-encode1
```

## Instalação de programas básicos

**Formato Flatpak**
```
flatpak install com.google.Chrome com.microsoft.Edge com.system76.Popsicle md.obsidian.Obsidian org.onlyoffice.desktopeditors
```

**Repositórios do sistema**
```
sudo apt install gpm flatpak gnome-software-plugin-flatpak
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```
```
sudo apt install vim bashtop fish gpm yt-dlp ttf-mscorefonts-installer fonts-bebas-neue chromium
```

**Extensões do GNOME**
https://extensions.gnome.org/extension/945/cpu-power-manager/
https://extensions.gnome.org/extension/615/appindicator-support/

**Remoção de pacotes desnecessários**
```
sudo apt purge libreoffice-common gnome-games --autoremove
```

## Jogos
```
flatpak install com.valvesoftware.Steam com.valvesoftware.Steam.Utility.MangoHud com.valvesoftware.Steam.Utility.vkBasalt com.valvesoftware.Steam.VulkanLayer.MangoHud com.github.tchx84.Flatseal
```
Se for necessário, libere as permissões do pacote flatpak do Steam para acessar outras unidades de disco.

**Download de vídeos do YouTube**
yt-dlp --merge-output-format mp4 https://youtu.be/ab5AXz-GEVU