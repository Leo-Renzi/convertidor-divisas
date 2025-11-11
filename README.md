# Convertidor de Divisas

Trabajo Práctico - Aplicaciones Web con Vue.js

## Demo en Vivo

**Aplicación desplegada:** [https://convertidor-divisas-vue.netlify.app/]

## Descripción

Aplicación web para convertir monedas en tiempo real utilizando Vue.js 3 y la API de ExchangeRate-API.

### Funcionalidades

- ✅ Conversión de múltiples divisas (USD, EUR, ARS, GBP, BRL, MXN, CLP, JPY)
- ✅ Tasas de cambio en tiempo real mediante API REST
- ✅ Intercambio rápido de monedas con botón swap
- ✅ Conversión automática al cambiar valores
- ✅ Interfaz responsive (adaptable a móviles)
- ✅ Diseño moderno con animaciones suaves
- ✅ Marca de tiempo de última actualización

## Tecnologías Utilizadas

- **Vue.js 3** - Framework progresivo de JavaScript
- **Vite** - Herramienta de construcción y desarrollo
- **ExchangeRate-API** - API REST para obtener tasas de cambio
- **CSS3** - Estilos y animaciones
- **Netlify** - Despliegue y hosting

### Pasos

1. Clonar el repositorio:

```bash
git clone https://github.com/TU-USUARIO/convertidor-divisas.git
cd convertidor-divisas
```

2. Instalar dependencias:

```bash
npm install
```

3. Ejecutar en modo desarrollo:

```bash
npm run dev
```

4. Abrir en el navegador: `http://localhost:5173`

## Compilar para Producción

```bash
npm run build
```

Los archivos optimizados se generarán en la carpeta `dist/`

## Estructura del Proyecto

```
convertidor-divisas/
├── src/
│   ├── assets/          # Imágenes y recursos
│   ├── App.vue          # Componente principal
│   └── main.js          # Punto de entrada
├── public/              # Archivos estáticos
├── dist/                # Build de producción (generado)
├── package.json         # Dependencias del proyecto
└── README.md           # Este archivo
```

## Conceptos de Vue.js Implementados

## API Utilizada

**ExchangeRate-API**

- Endpoint: `https://api.exchangerate-api.com/v4/latest/{currency}`
- Proporciona tasas de cambio actualizadas
- No requiere autenticación
- Gratuita para uso educativo

## Autor

Leonardo Renzi
Tecnicatura Superior en Desarrollo de Software

**Repositorio:** https://github.com/TU-USUARIO/convertidor-divisas
**Demo:** https://convertidor-divisas-vue.netlify.app/
