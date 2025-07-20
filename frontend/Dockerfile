# Stage 1: Build
FROM node:18-alpine as build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

# ✅ Make react-scripts executable
RUN chmod +x node_modules/.bin/react-scripts

# 🛠️ Build the React app
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:alpine

# ✅ Copy built files
COPY --from=build /app/build /usr/share/nginx/html

# Optional: custom nginx config
# COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
