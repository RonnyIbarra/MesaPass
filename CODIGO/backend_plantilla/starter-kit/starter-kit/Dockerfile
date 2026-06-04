# Dockerfile para Next.js Frontend
# Optimizado para Railway
# Build cache buster: 2026-06-04-12-30 - Force rebuild without frozen lockfile
# Etapas: deps -> builder -> runtime

FROM node:22-alpine AS deps
WORKDIR /app
COPY CODIGO/backend_plantilla/starter-kit/starter-kit/package.json CODIGO/backend_plantilla/starter-kit/starter-kit/pnpm-lock.yaml ./
RUN npm install -g pnpm && pnpm install --no-frozen-lockfile

FROM node:22-alpine AS builder
WORKDIR /app
COPY CODIGO/backend_plantilla/starter-kit/starter-kit/package.json CODIGO/backend_plantilla/starter-kit/starter-kit/pnpm-lock.yaml ./
RUN npm install -g pnpm && pnpm install --no-frozen-lockfile
COPY CODIGO/backend_plantilla/starter-kit/starter-kit/ .
RUN pnpm run build

FROM node:22-alpine AS runtime
WORKDIR /app
ENV NODE_ENV=production

# Arg to bust build cache when needed. Update the value when you want a fresh install layer.
ARG CACHEBUST=2026-06-04-12-30

COPY CODIGO/backend_plantilla/starter-kit/starter-kit/package.json CODIGO/backend_plantilla/starter-kit/starter-kit/pnpm-lock.yaml ./
# Use the CACHEBUST arg to ensure this layer is re-run when we push an updated Dockerfile
RUN echo "CACHEBUST=$CACHEBUST" && npm install -g pnpm && pnpm install --prod --no-frozen-lockfile

COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=10s --start-period=40s --retries=3 \
    CMD node -e "require('http').get('http://127.0.0.1:3000', (r) => {if (r.statusCode !== 200) throw new Error(r.statusCode)})"

CMD ["pnpm", "start"]
