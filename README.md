# Proyecto Expo + NestJS + Prisma

Este proyecto es una aplicación móvil desarrollada con Expo (React Native + TypeScript) que consume una API REST construida con NestJS y Prisma. Permite la gestión de instrumentos musicales con funcionalidades CRUD.

## Backend - NestJS con Prisma

### Instalación y configuración
1. Instalar NestJS CLI y crear un nuevo proyecto:
   ```sh
   npm i -g @nestjs/cli
   nest new backend
   ```
2. Instalar Prisma y configurar base de datos:
   ```sh
   cd backend
   npm install prisma @prisma/client
   npx prisma init --datasource-provider sqlite
   ```
3. Definir el modelo de instrumento en `prisma/schema.prisma`:
   ```prisma
   model Instrumento {
     id          Int     @id @default(autoincrement())
     nombre      String
     tipo        String
     precio      Float
     descripcion String
   }
   ```
4. Ejecutar migraciones:
   ```sh
   npx prisma migrate dev --name init
   ```
5. Generar módulo, servicio y controlador en NestJS:
   ```sh
   nest generate module instrumentos
   nest generate service instrumentos
   nest generate controller instrumentos
   ```

### Rutas del API REST
- `GET /instrumentos` - Obtener todos los instrumentos
- `GET /instrumentos/:id` - Obtener un instrumento por ID
- `POST /instrumentos` - Crear un nuevo instrumento
- `PUT /instrumentos/:id` - Actualizar un instrumento existente
- `DELETE /instrumentos/:id` - Eliminar un instrumento

## Frontend - Expo con React Native y TypeScript

### Instalación y configuración
1. Crear un nuevo proyecto Expo:
   ```sh
   npx create-expo-app frontend -t with-typescript
   ```
2. Instalar axios para consumir la API:
   ```sh
   cd frontend
   npm install axios
   ```

### Estructura del proyecto
```
frontend/
┣ app/
┃ ┣ index.tsx        # Pantalla principal (Lista de instrumentos)
┃ ┣ instrumentos/
┃ ┃ ┣ [id].tsx       # Pantalla de detalles y edición de un instrumento
┃ ┃ ┣ nuevo.tsx      # Pantalla para agregar un nuevo instrumento
┣ components/
┃ ┣ InstrumentoCard.tsx # Componente para mostrar un instrumento
┃ ┣ Button.tsx       # Botón reutilizable
┣ services/
┃ ┣ api.ts           # Archivo con funciones para comunicarse con el backend
┣ styles/
┃ ┣ colors.ts        # Definición de colores
┃ ┣ typography.ts    # Definición de tipografía
```

### Implementación de la API en `services/api.ts`
```typescript
import axios, { AxiosError } from 'axios';

const API_URL = 'http://192.168.18.125:3000'; // ⚠️ Usa esta IP

const api = axios.create({
  baseURL: API_URL,
  headers: {
    'Content-Type': 'application/json',
    'Accept': 'application/json',
  },
});

// Definición del tipo para los datos de instrumento
interface InstrumentoData {
  nombre: string;
  tipo: string;
  precio: number;
  descripcion: string;
}

// Obtener todos los instrumentos
export const getInstrumentos = async () => {
  try {
    const response = await api.get('/instruments');
    return response.data;
  } catch (error: unknown) {
    const axiosError = error as AxiosError;
    console.error('Error al obtener los instrumentos:', axiosError.response?.data || axiosError.message);
    throw new Error('No se pudieron obtener los instrumentos');
  }
};

// Obtener un instrumento por ID
export const getInstrumentoById = async (id: number) => {
  try {
    const response = await api.get(`/instruments/${id}`);
    return response.data;
  } catch (error: unknown) {
    const axiosError = error as AxiosError;
    console.error('Error al obtener el instrumento:', axiosError.response?.data || axiosError.message);
    throw new Error('No se pudo obtener el instrumento');
  }
};

// Crear un nuevo instrumento
export const createInstrumento = async (data: InstrumentoData) => {
  try {
    const response = await api.post('/instruments', data);
    return response.data;
  } catch (error: unknown) {
    const axiosError = error as AxiosError;
    console.error('Error al crear el instrumento:', axiosError.response?.data || axiosError.message);
    throw new Error('No se pudo crear el instrumento');
  }
};

// Actualizar un instrumento por ID
export const updateInstrumento = async (id: number, data: InstrumentoData) => {
  try {
    const response = await api.put(`/instruments/${id}`, data);
    return response.data;
  } catch (error: unknown) {
    const axiosError = error as AxiosError;
    console.error('Error al actualizar el instrumento:', axiosError.response?.data || axiosError.message);
    throw new Error('No se pudo actualizar el instrumento');
  }
};

// Eliminar un instrumento por ID
export const deleteInstrumento = async (id: number) => {
  try {
    const response = await api.delete(`/instruments/${id}`);
    return response.data;
  } catch (error: unknown) {
    const axiosError = error as AxiosError;
    console.error('Error al eliminar el instrumento:', axiosError.response?.data || axiosError.message);
    throw new Error('No se pudo eliminar el instrumento');
  }
};

```

## Cómo ejecutar el proyecto
### Backend
```sh
cd backend
npm install
npm run start
```
### Frontend
```sh
cd frontend
npm install
npx expo start
```
