FROM node:20

WORKDIR /app/server

# Copy dependency files first (better caching)
COPY package.json ./

# Install dependencies
RUN npm install

# Copy application code
COPY *.js .

# Environment variables
ENV MONGO="true" \
    MONGO_URL="mongodb://10.100.182.148:27017/catalogue"

# Start applications
CMD ["node", "server.js"]