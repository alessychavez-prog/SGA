# Stage 1: Builder
FROM node:20 AS builder

# Establecer directorio de trabajo
WORKDIR /app

# Copiar package.json y package-lock.json
COPY package*.json ./

# Instalar dependencias (incluyendo dev)
RUN npm ci

# Copiar código fuente
COPY . .

# Generar cliente Prisma
RUN npx prisma generate

# Construir la aplicación NestJS
RUN npm run build


# Stage 2: Production
FROM node:20-slim AS production

# Establecer directorio de trabajo
WORKDIR /app

# Instalar bash y certificados (node:20-slim incluye OpenSSL/libssl que Prisma necesita)
RUN apt-get update && apt-get install -y bash ca-certificates && rm -rf /var/lib/apt/lists/*

# Copiar package.json y package-lock.json
COPY package*.json ./

# Instalar dependencias completas (necesarias para prisma migrate y seed)
RUN npm ci

# Copiar código compilado y archivos necesarios
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/prisma ./prisma
COPY --from=builder /app/start-railway.js ./

# Exponer puerto
EXPOSE 3001

# Comando para ejecutar la aplicación
CMD ["node", "start-railway.js"]
