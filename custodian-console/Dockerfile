FROM node:24-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
# npm ci demands byte-exact lock-file parity including platform-specific
# optional deps (native bindings pulled in by tailwindcss/lightningcss) -
# the lock file here was generated on Windows, so a strict Linux-container
# `ci` rejects it over optional-dependency variants that were never meant
# to match across platforms. `install` resolves the current platform's
# variants instead of demanding the host's.
RUN npm install --no-audit --no-fund

FROM node:24-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:24-alpine AS runner
WORKDIR /app
# The policy explorer shells out to `git log` against the mounted policies
# directory - not bundled with the base node image.
RUN apk add --no-cache git

ENV NODE_ENV=production
RUN addgroup --system --gid 1001 nodejs && adduser --system --uid 1001 console

# No `COPY /app/public` here: this app has no public/ directory. The favicon
# uses the App Router convention (app/favicon.ico) and is emitted into .next,
# so the boilerplate copy from the Next.js standalone docs only ever failed
# the build. Re-add it if a public/ directory is ever introduced.
COPY --from=builder --chown=console:nodejs /app/.next/standalone ./
COPY --from=builder --chown=console:nodejs /app/.next/static ./.next/static

USER console
EXPOSE 3000
ENV PORT=3000
ENV HOSTNAME=0.0.0.0
CMD ["node", "server.js"]
