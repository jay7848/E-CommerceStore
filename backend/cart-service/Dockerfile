# Dockerfile for Node.js backend microservice
FROM node:18-alpine

# Create app directory
WORKDIR /app

# Install app dependencies
COPY package*.json ./
RUN npm install

# Copy app source code
COPY . .

# Expose the service port (update if needed)
EXPOSE 3000

# Start the service
CMD ["npm", "start"]
