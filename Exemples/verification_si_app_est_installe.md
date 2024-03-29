# Fonctionnement du code :
1. Détection du système d'exploitation.
2. Détection du gestionnaire de paquets a utiliser.
3. Vérifier si l'application est déjà installée ou pas.
4. Installation de l'application si l'application n'est pas installée.

Compatibilité avec différents types d'installations :
1. Manuelle.
2. Via gestionnaire de paquets (apt, yum et dnf).

```bash
#!/bin/bash

test_pkg() {
  case "$OSTYPE" in
    linux*)
      if [[ -f /etc/os-release || -f /etc/redhat-release || -f /etc/debian_version ]]
      then
        if [[ $(dpkg -s "apt" 2> /dev/null) || $(dpkg -s "apt-get" 2> /dev/null) ]]
        then
          pkg_os='apt-get'
        elif [[ $(rpm -qi "dnf" 2> /dev/null) ]]
        then
          pkg_os='dnf'
        elif [[ $(rpm -qi "yum" 2> /dev/null) ]]
        then
          pkg_os='yum'
        else
          alert_info 'INFO' 'Gestionnaire de paquets non prit en charge'
          exit 1
        fi
      fi ;;
    *) alert_info 'INFO' "OS non prit en charge : $OSTYPE" ;;
  esac
}

alert_info() {
  local msg1="${1}"
  local msg2="${2}"
  # $1 : ERREUR, INFO.
  # $2 : Informations complémentaires.
  echo -e "\n !!! ${msg1} : ${msg2} !!! \n"
}

check_install() {

local pkg_dist="${pkg_os}"
local app="$(echo "${1}" | tr "[:upper:]" "[:lower:]")"

case "$pkg_dist" in
  'apt' | 'apt-get' )

    if ! [[ $(dpkg -s ${app} 2> /dev/null) && $(command -v ${app} 2> /dev/null) ]]
    then
      echo -e "\n Installation de ${app} en cours \n"
      apt-get update -q
      apt-get install -qy ${app}

      if [[ "${?}" == "0" ]]
      then
        alert_info 'INFO' "Installation de ${app} réussie"
      else
        alert_info 'ERREUR' "Installation de ${app} Impossible!"
        exit 1
      fi
    fi ;;
  'dnf' )
    if ! [[ $(rpm -qi "${app}" 2> /dev/null) && $(command -v ${app} 2> /dev/null) ]]
    then
      echo -e "\n Installation de ${app} en cours \n"
      dnf check-update > /dev/null
      dnf install -qy ${app}

      if [[ "${?}" == "0" ]]
      then
        alert_info 'INFO' "Installation de ${app} réussie"
      else
        alert_info 'ERREUR' "Installation de ${app} Impossible!"
        exit 1
      fi
    fi ;;
  'yum' )
    if ! [[ $(rpm -qi "${app}" 2> /dev/null) && $(command -v ${app} 2> /dev/null) ]]
    then
      echo -e "\n Installation de ${app} en cours \n"
      yum check-update > /dev/null
      yum install -qy ${app}

      if [[ "${?}" == "0" ]]
      then
        alert_info 'INFO' "Installation de ${app} réussie"
      else
        alert_info 'ERREUR' "Installation de ${app} Impossible!"
        exit 1
      fi
    fi ;;
esac
}

test_pkg

check_install 'firefox'

```
## Vérification application Flatpak, snap et install

```bash
#!/bin/bash

readonly var=$(echo "${1}" | tr "[:upper:]" "[:lower:]")

readonly app_flat=$(flatpak list --app | grep ${var} | cut -f2)
readonly app_snap=$(snap list --app | grep ${var} | awk '{print $1}')

if [[ $(dpkg -s flatpak 2> /dev/null) && $(command -v flatpak 2> /dev/null) ]]
then
  if ! [[ $(flatpak info ${app_flat} 2> /dev/null) ]]
  then
    flatpak update && flatpak install -y ${app_flat}
  fi
elif [[ $(dpkg -s snapd 2> /dev/null) && $(command -v snap 2> /dev/null) ]]
then
  if ! [[ $(snap info ${app_flat} 2> /dev/null) ]]
  then
    snap install ${app_snap}
  fi
else
  echo ""
fi


```
