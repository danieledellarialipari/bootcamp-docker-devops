# Stage 1: builder
FROM node:20.11.1-alpine3.19 AS builder
WORKDIR /app

# Prima solo i file delle dipendenze (cache friendly)
COPY package*.json ./

# Cache persistente dei pacchetti npm tramite BuildKit
RUN --mount=type=cache,target=/root/.npm \
    npm ci --omit=dev

# Poi il sorgente
COPY . .

# Stage 2: runtime
FROM node:20.11.1-alpine3.19 AS runtime
WORKDIR /app

ENV NODE_ENV=production

# Copio dal builder
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/src ./src
COPY --from=builder /app/package*.json ./

USER node

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD wget -q --spider http://localhost:3000/healthz || exit 1

CMD ["node", "src/index.js"]