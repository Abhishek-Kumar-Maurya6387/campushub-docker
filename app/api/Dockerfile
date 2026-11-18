FROM node:20.18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --omit=dev --no-audit --no-fund && npm cache clean --force && rm -rf /opt/yarn-* /usr/local/bin/yarn /usr/local/bin/yarnpkg
COPY . .
USER node
EXPOSE 3000
HEALTHCHECK --interval=15s --timeout=3s --start-period=40s --retries=5 CMD wget -qO- http://127.0.0.1:3000/api/health >/dev/null || exit 1
CMD ["npm", "start"]