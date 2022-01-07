# Prueba Frontend IPCOM

El código y las herramientas utilizadas con propósitos de desarrollo y agilidad, por lo que pueden existir algunas no tan buenas prácticas.

Aplicación creada con: vue-cli - vue-ui

Librerias y/o paquetes utilizados:
tailwindcss - axios

Para la construcción del calendario se tomó como referencia los cinco días hábiles de la semana comenzando desde el lunes y terminando el viernes.

Se toma como fecha de inicio la del sistema local Llamado a la API crea matriz con los horarios disponibles para cada día (5), cargados todos de forma concurrente.

Si la fecha seleccionada está dentro de la semana que se muestra en pantalla, se valida sin cargar datos o hacer peticiones al servidor API; si es diferente se cambia por la semana correspondiente.

La fecha se valida al seleccionar y/ o cambiar fecha y hora.
Adicional se habilita acción evento click para recuadros con fechas disponibles que rellena campos de fecha y hora.

Muy motivado para continuar en este proceso, gracias por la oportunidad.

Recibo observaciones y sugerencias... :) Saludos!

@davidromanl

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
