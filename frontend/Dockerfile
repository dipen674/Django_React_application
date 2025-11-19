FROM node:16-alpine

# Set work directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy source code
COPY . .

# Install http-proxy-middleware if not already in package.json
RUN npm install http-proxy-middleware --save

# Expose port
EXPOSE 3000

# Start development server
CMD ["npm", "start"]