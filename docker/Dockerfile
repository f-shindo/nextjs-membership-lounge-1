# =============================================================================
# BUILD
FROM node:16-slim AS build


# データを収集しないようにする https://nextjs.org/telemetry
ENV NEXT_TELEMETRY_DISABLED=1
WORKDIR /app
COPY package.json package-lock.json ./
# プログレスバー非表示
RUN npm set progress=false
RUN npm ci
COPY . .
RUN npm run build
RUN find '.next' -name *.map -delete
RUN find '.next' -type f \( -iname \*.js \) -exec sed -i -E 's/\/(\/|\*)# sourceMappingURL=[^ ]*\.js\.map(\*\/)?//' {} +

# =============================================================================
# Production Run
FROM gcr.io/distroless/nodejs:16
ENV NODE_ENV=production
# データを収集しないようにする https://nextjs.org/telemetry
ENV NEXT_TELEMETRY_DISABLED=1
WORKDIR /app

COPY package.json package-lock.json next.config.js ./
COPY --from=build /app/public ./public
COPY --from=build /app/.next ./.next
COPY --from=build /app/node_modules ./node_modules

# ${PORT}がなぜか動かないので明示的に8080を指定する
EXPOSE 8080

CMD ["node_modules/.bin/next", "start", "-p", "8080"]