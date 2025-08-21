# Fase 1: Build - Usiamo un'immagine Node.js completa per installare e compilare
FROM node:18-alpine AS builder

# Imposta la directory di lavoro all'interno del container
WORKDIR /app

# Copia i file package.json e il lockfile
COPY package.json ./
COPY pnpm-lock.yaml ./
COPY prisma ./prisma/

# Installa pnpm (o npm/yarn se usi quelli) e le dipendenze
RUN npm install -g pnpm
RUN pnpm install

# Copia il resto del codice sorgente dell'applicazione
COPY . .

# Esegui il build del codice TypeScript in JavaScript
RUN pnpm run build

# Esegui la generazione del client Prisma
RUN pnpm prisma generate


# Fase 2: Production - Usiamo un'immagine snella per l'esecuzione
FROM node:18-alpine

WORKDIR /app

# Copia solo i file necessari dalla fase di build
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/prisma ./prisma

# Esponi la porta su cui il tuo server Express è in ascolto (es. 8080)
EXPOSE 8080

# Il comando per avviare l'applicazione
CMD ["node", "dist/main.js"]