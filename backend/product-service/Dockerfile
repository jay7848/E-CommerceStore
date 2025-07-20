FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

# NOTE: Do NOT hardcode PORT — Kubernetes will inject it
EXPOSE 3000

CMD ["npm", "start"]
