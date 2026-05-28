FROM node:20-alpine AS build

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM node:20-alpine

WORKDIR /app

COPY --from=build /app/.next/standalone ./
COPY --from=build /app/.next/static ./.next/static
COPY --from=build /app/public ./public

EXPOSE 38088

ENV PORT=38088
ENV HOSTNAME="0.0.0.0"
ENV BACKEND_URL="http://dev-oscholar-gateway-service"

CMD ["node", "server.js"]
