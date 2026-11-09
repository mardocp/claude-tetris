---
name: clima
description: Consulta la temperatura y el clima actuales de una ciudad (por defecto Pozuelo de Alarcón, Madrid, España) y los resume en español. Úsalo cuando el usuario pida el tiempo, la temperatura o el clima.
argument-hint: "[ciudad]"
allowed-tools: Bash(curl *), Bash(date)
---

# Clima local

Obtén el clima actual y resúmelo brevemente en español.

## Pasos

1. Ciudad: usa `$ARGUMENTS` si el usuario la indica; si no, `Pozuelo de Alarcón`. Sustituye los espacios por `+` en la URL.
2. Ejecuta con `curl` (no uses WebFetch: cachea 15 minutos y devuelve datos desfasados):

   ```bash
   curl -s "https://wttr.in/Pozuelo+de+Alarcon?format=%C+%t+(sensación+%f)+humedad+%h+viento+%w&lang=es"; echo; date
   ```

3. Responde con un título de una línea (ciudad y hora de la consulta, de `date`) y una tabla Markdown con símbolos, sin más texto salvo avisos de las notas:

   | | Dato | Valor |
   |---|---|---|
   | 🌡️ | Temperatura | 13 °C |
   | 🤒 | Sensación térmica | 10 °C |
   | ☀️ | Cielo | Despejado |
   | 💧 | Humedad | 31 % |
   | 💨 | Viento | 6 km/h ↗ |

   Elige el símbolo del cielo según el estado: ☀️ despejado/soleado, 🌙 despejado de noche, ⛅ parcialmente nublado, ☁️ nublado, 🌧️ lluvia, ⛈️ tormenta, ❄️ nieve, 🌫️ niebla.

## Notas

- Si el resultado está vacío o contiene un error HTML, dilo y no inventes datos.
- wttr.in actualiza con poca frecuencia: repetir la consulta cada minuto suele dar lo mismo.
- Si el cielo dice "soleado" pero es de noche, avisa de que ese dato no es fiable.
