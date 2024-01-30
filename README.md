#!/bin/bash

# Define los nombres permitidos
nombres_permitidos=("arys" "emeterio" "solofe")

# Pide al usuario que ingrese su nombre
read -p "Por favor, introduce tu nombre: " nombre

# Verifica si el nombre es válido
if [[ " ${nombres_permitidos[@]} " =~ " ${nombre} " ]]; then
    echo "¡Bienvenido, ${nombre}!"
else
    echo "Lo siento, no estás autorizado para acceder."
    exit 1
fi

read -p "Introduce la dirección IP que deseas buscar: " ip

info=$(curl -s "https://ipinfo.io/${ip}")

if ! command -v jq &> /dev/null
then
    echo "La herramienta jq no está instalada. Por favor, instálala para ejecutar este script."
    exit 1
fi

pais=$(echo "${info}" | jq -r '.country')

if [[ $ip == *:* ]]; then
    tipo="IPv6"
else
    tipo="IPv4"
fi

isp=$(whois -h whois.arin.net " ${ip}" | grep -i "netname\|org-name" | awk -F':' '{print $2}' | sed 's/^[ \t]*//;s/[ \t]*$//')

echo "La dirección IP ${ip} es de tipo ${tipo}, está ubicada en el país: ${pais} y pertenece al ISP: ${isp}"

echo "${info}" > "${ip}.txt"
