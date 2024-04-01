# Instalação do Davinci Resolve

## Adicionar Debian Multimedia
echo "**deb https://www.deb-multimedia.org bookworm main non-free**" > /etc/apt/sources.list.d/deb-multimedia.list
apt-get update -oAcquire::AllowInsecureRepositories=true
apt-get install deb-multimedia-keyring
apt-get update; apt-get dist-upgrade

**Resolução de dependências**
sudo apt install libxcb-composite0 libxcb-cursor0 libxcb-xinerama0 libxcb-xinput0

**Contornar erro de instalação "do pacote"**
SKIP_PACKAGE_CHECK=1 ./DaVinci_Resolve_18.X_Linux.run

## Instalação do Nvidia CUDA
sudo apt-get install nvidia-driver nvidia-opencl-icd libcuda1 libglu1-mesa libnvidia-encode1

## Instalação de programas básicos

**Formato Flatpak**
flatpak install com.google.Chrome com.microsoft.Edge com.system76.Popsicle md.obsidian.Obsidian org.onlyoffice.desktopeditors

**Repositórios do sistema**
sudo apt install gpm flatpak gnome-software-plugin-flatpak 
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo

sudo apt install vim bashtop fish gpm yt-dlp ttf-mscorefonts-installer fonts-bebas-neue chromium

**Extensões do GNOME**
https://extensions.gnome.org/extension/945/cpu-power-manager/
https://extensions.gnome.org/extension/615/appindicator-support/

**Remoção de pacotes desnecessários**
sudo apt purge libreoffice-common gnome-games --autoremove

## Jogos
flatpak install com.valvesoftware.Steam com.valvesoftware.Steam.Utility.MangoHud com.valvesoftware.Steam.Utility.vkBasalt com.valvesoftware.Steam.VulkanLayer.MangoHud com.github.tchx84.Flatseal

* Se for necessário, libere as permissões do pacote flatpak do Steam para acessar outras unidades de disco.

------

**Download de vídeos do YouTube**
yt-dlp --merge-output-format mp4 https://youtu.be/ab5AXz-GEVU