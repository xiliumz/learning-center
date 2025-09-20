# Template

Template for common use cases

## Basic Backend App (Copy -> Build -> Serve)



```Dockerfile
# Build stage
FROM [IMAGE]-alpine AS builder # Usually use alpine build for size consideration, but because this is builder I think using anything would be fine

# Set working directory
WORKDIR /app

# NOTE: Run neccessary commands

# Enable Corepack for Yarn 4.x support
RUN corepack enable

# Copy package files
COPY package.json yarn.lock .yarnrc.yml ./

# Install dependencies
RUN yarn install --immutable

# Copy source code
COPY . .

# Build the application
RUN yarn build

# NOTE Create prduction ready image, usually use alpine

# Production stage
FROM node:20-alpine AS production

# Enable Corepack for Yarn 4.x support
RUN corepack enable

# Create app system user
RUN adduser -S nodejs -u 1001

# Set working directory
WORKDIR /app

# NOTE: Run neccessary commands

# Copy package files
COPY package.json yarn.lock .yarnrc.yml ./

# Install dependencies
RUN yarn install --immutable && yarn cache clean

# Copy built application from builder stage
COPY --from=builder /app/dist ./dist

# Change ownership to nodejs user and switch to nodejs user
RUN chown -R nodejs /app
USER nodejs

# Expose port
EXPOSE 3000

# Health check

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000/health', (res) => process.exit(res.statusCode === 200 ? 0 : 1)).on('error', () => process.exit(1))"

# Start the application
CMD ["node", "dist/index.js"]
```
