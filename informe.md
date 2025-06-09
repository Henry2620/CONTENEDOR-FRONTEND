# Informe: Contenerización de Aplicación Frontend con React y Consumo de API desde Backend en Contenedor

## 1. Título

**Contenerización de una aplicación web frontend en React que consume datos desde un backend Express, ambos desplegados con Docker.**

## 2. Descripción general del proyecto

El objetivo fue desarrollar y contenerizar una aplicación web que muestre en una tabla los datos obtenidos desde una API REST. Esta API se encuentra en otro contenedor, y ambos servicios (frontend y backend) deben comunicarse correctamente dentro de una red definida en Docker Compose.

Esta solución se basa en una aplicación real, perteneciente a mi proyecto de titulación: **Plataforma de Agendamiento de Servicios**, donde el sistema permite visualizar citas programadas por los usuarios.

## 3. Requisitos técnicos cumplidos

- ✅ Aplicación frontend en React (TypeScript) contenerizada.
- ✅ Backend en Node.js con Express contenerizado.
- ✅ Comunicación entre contenedores usando Docker Compose.
- ✅ Visualización en tabla de los datos obtenidos desde la API.
- ✅ Dockerfiles para ambos proyectos y archivo `docker-compose.yml`.
- ✅ Separación de lógica de negocio (backend) y presentación (frontend).

---

## 4. Tecnologías utilizadas

- React + TypeScript
- Node.js + Express
- Docker
- Docker Compose
- VSCode (entorno de desarrollo)
- Navegador (para pruebas)

---

## 5. Estructura del proyecto

AGENDAMIENTO/
├── backend/
│ ├── index.js
│ ├── package.json
│ └── Dockerfile
├── frontend/
│ ├── src/
│ │ └── App.tsx
│ ├── package.json
│ └── Dockerfile
└── docker-compose.yml

---

<img src="captures\paso1.png" alt="drawing" width="500"/>

## 6. Backend: API REST en Node.js (Express)

Se creó un backend básico con un endpoint `/api/citas` que retorna un array de objetos representando citas agendadas.

### Código `index.js`

```js
const express = require("express");
const cors = require("cors");
const app = express();

app.use(cors());

app.get("/api/citas", (req, res) => {
  res.json([
    { id: 1, cliente: "Ana", servicio: "Manicura", hora: "10:00" },
    { id: 2, cliente: "Luis", servicio: "Pedicura", hora: "11:00" },
  ]);
});

app.listen(3001, () => {
  console.log("API Backend corriendo en http://localhost:3001");
});
```

<img src="captures\paso2.png" alt="drawing" width="500"/>

### Dockerfile del backend

FROM node:18

WORKDIR /app

COPY package\*.json ./

RUN npm install

COPY . .

EXPOSE 3001

CMD ["node", "index.js"]

<img src="captures\paso3.png" alt="drawing" width="500"/>

## 7. Frontend: Aplicacion React que consume la API

Se desarrolló una app con React y TypeScript. Al cargar la aplicación, se hace un GET a la API /api/citas para mostrar los datos en una tabla HTML.

### Código `App.tsx`

import React, { useEffect, useState } from 'react';
import axios from 'axios';

interface Cita {
id: number;
cliente: string;
servicio: string;
hora: string;
}

function App() {
const [citas, setCitas] = useState<Cita[]>([]);

useEffect(() => {
axios.get('http://localhost:3001/api/citas')
.then((res) => setCitas(res.data))
.catch((err) => console.error("Error al obtener citas:", err));
}, []);

return (

<div style={{ padding: '2rem', fontFamily: 'Arial' }}>
<h1>Turnos Agendados</h1>
<table border={1} cellPadding={10} cellSpacing={0}>
<thead>
<tr>
<th>ID</th>
<th>Cliente</th>
<th>Servicio</th>
<th>Hora</th>
</tr>
</thead>
<tbody>
{citas.map((cita) => (
<tr key={cita.id}>
<td>{cita.id}</td>
<td>{cita.cliente}</td>
<td>{cita.servicio}</td>
<td>{cita.hora}</td>
</tr>
))}
</tbody>
</table>
</div>
);
}

export default App;

<img src="captures\paso4.png" alt="drawing" width="500"/>

### Dockerfile del frontend

FROM node:18

WORKDIR /app

COPY package\*.json ./

RUN npm install

COPY . .

RUN npm run build

RUN npm install -g serve

EXPOSE 3000

CMD ["serve", "-s", "build"]

<img src="captures\paso5.png" alt="drawing" width="500"/>

## 8. Docker Compose

Para automatizar el despliegue conjunto de frontend y backend, se creó el siguiente archivo:

### docker-compose.yml

version: '3'
services:
backend:
build: ./backend
container_name: api_agendamiento
ports: - "3001:3001"
networks: - agendanet

frontend:
build: ./frontend
container_name: ui_agendamiento
ports: - "3000:3000"
depends_on: - backend
networks: - agendanet

networks:
agendanet:

<img src="captures\paso6.png" alt="drawing" width="500"/>

## 9. Proceso de despliegue

1. Se instaló Docker Desktop en Windows.

2. Se creó la estructura de carpetas y archivos en VSCode.

3. Se desarrollaron los Dockerfile para frontend y backend.

4. Se probó localmente cada servicio por separado.

5. Se levantaron los contenedores con el comando:

```
docker-compose up --build
```

<img src="captures\paso7.png" alt="drawing" width="500"/>

## 10. Se accedio al frontend via navegador:

- http://localhost:3000

<img src="captures\paso8.png" alt="drawing" width="500"/>

## 11. Se valido la API accediendo a:

- http://localhost:3001/api/citas

<img src="captures\paso9.png" alt="drawing" width="500"/>

## 12. Resultados

✅ El contenedor del backend se construyó correctamente y expone la API en el puerto 3001.

✅ El contenedor del frontend se levanta correctamente y accede a la API.

✅ La tabla de citas se carga exitosamente en pantalla.

✅ Ambos servicios se comunican por red interna definida en Docker Compose.

## 13. Conclusion

Se logró contenerizar y desplegar exitosamente una aplicación frontend en React que consume datos desde una API alojada en un backend Express, ambos comunicándose mediante Docker Compose. Esta práctica refuerza el dominio de herramientas modernas de despliegue y prepara el entorno para escalar el sistema o llevarlo a producción en plataformas cloud como Railway o Vercel.

## 14. Audio explicativo

[Link del Audio explicativo](https://drive.google.com/file/d/1qKiKVSb5ZfbC3kNSnqfX2okCAu2DRPmF/view?usp=sharing)

## 15. Bibliografias

- Docker Inc. (2024). _Docker Documentation_. Recuperado de: [https://docs.docker.com/](https://docs.docker.com/)
- Node.js Foundation. (2024). _Express.js - web application framework for Node.js_. Recuperado de: [https://expressjs.com/](https://expressjs.com/)
- Meta Open Source. (2024). _React Documentation_. Recuperado de: [https://react.dev/](https://react.dev/)
- Docker Inc. (2024). _Docker Compose Reference_. Recuperado de: [https://docs.docker.com/compose/](https://docs.docker.com/compose/)
- Mozilla Developer Network (MDN). (2024). _Using fetch / HTTP Requests in JavaScript_. Recuperado de: [https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- GitHub. (2024). _Repositorio base del proyecto de agendamiento_. Recuperado de: [https://github.com/](https://github.com/)
