// Backend - NestJS con Prisma

// 1. Instalar NestJS CLI y crear un nuevo proyecto
// npm i -g @nestjs/cli
// nest new backend

// 2. Instalar Prisma y configurar base de datos
// cd backend
// npm install prisma @prisma/client
// npx prisma init --datasource-provider sqlite

// 3. Definir el modelo de instrumento en prisma/schema.prisma
model Instrumento {
  id          Int     @id @default(autoincrement())
  nombre      String
  tipo        String
  precio      Float
  descripcion String
}

// 4. Ejecutar migraciones
// npx prisma migrate dev --name init

// 5. Crear el módulo, servicio y controlador en NestJS
// nest generate module instrumentos
// nest generate service instrumentos
// nest generate controller instrumentos

// 6. Implementar CRUD en el servicio (instrumentos.service.ts)
@Injectable()
export class InstrumentosService {
  constructor(private prisma: PrismaService) {}
  
  async getAll() {
    return this.prisma.instrumento.findMany();
  }
  
  async getOne(id: number) {
    return this.prisma.instrumento.findUnique({ where: { id } });
  }
  
  async create(data: Prisma.InstrumentoCreateInput) {
    return this.prisma.instrumento.create({ data });
  }
  
  async update(id: number, data: Prisma.InstrumentoUpdateInput) {
    return this.prisma.instrumento.update({ where: { id }, data });
  }
  
  async delete(id: number) {
    return this.prisma.instrumento.delete({ where: { id } });
  }
}

// 7. Implementar las rutas en el controlador (instrumentos.controller.ts)
@Controller('instrumentos')
export class InstrumentosController {
  constructor(private readonly instrumentosService: InstrumentosService) {}
  
  @Get()
  getAll() {
    return this.instrumentosService.getAll();
  }
  
  @Get(':id')
  getOne(@Param('id', ParseIntPipe) id: number) {
    return this.instrumentosService.getOne(id);
  }
  
  @Post()
  create(@Body() data: Prisma.InstrumentoCreateInput) {
    return this.instrumentosService.create(data);
  }
  
  @Put(':id')
  update(@Param('id', ParseIntPipe) id: number, @Body() data: Prisma.InstrumentoUpdateInput) {
    return this.instrumentosService.update(id, data);
  }
  
  @Delete(':id')
  delete(@Param('id', ParseIntPipe) id: number) {
    return this.instrumentosService.delete(id);
  }
}

// Frontend - Expo con React Native y TypeScript

// 1. Crear un nuevo proyecto Expo
// npx create-expo-app frontend -t with-typescript

// 2. Instalar axios para consumir la API
// cd frontend
// npm install axios

// 3. Estructura de carpetas del frontend
// frontend/
// ┣ app/
// ┃ ┣ index.tsx        # Pantalla principal (Lista de instrumentos)
// ┃ ┣ instrumentos/
// ┃ ┃ ┣ [id].tsx       # Pantalla de detalles y edición de un instrumento
// ┃ ┃ ┣ nuevo.tsx      # Pantalla para agregar un nuevo instrumento
// ┣ components/
// ┃ ┣ InstrumentoCard.tsx # Componente para mostrar un instrumento
// ┃ ┣ Button.tsx       # Botón reutilizable
// ┣ services/
// ┃ ┣ api.ts           # Archivo con funciones para comunicarse con el backend
// ┣ styles/
// ┃ ┣ colors.ts        # Definición de colores
// ┃ ┣ typography.ts    # Definición de tipografía

// 4. Implementar la comunicación con la API en services/api.ts
import axios from 'axios';

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
