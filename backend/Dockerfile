FROM node:18 AS build

# Required for Prisma Client to work in container
RUN apt-get update && apt-get install -y openssl

WORKDIR /svr/app

COPY --chown=node:node package*.json ./

# Install npm dependencies
RUN npm ci

COPY --chown=node:node . .

# Generate prisma files
RUN npx prisma generate

# Run the build command which creates the production bundle
RUN npm run build

# Set NODE_ENV environment variable
ENV NODE_ENV production

FROM node:18-alpine3.16 AS production

# Copy the bundled code from the build stage to the production image
COPY --chown=node:node --from=build /svr/app/node_modules ./node_modules
COPY --chown=node:node --from=build /svr/app/dist ./dist

EXPOSE 3000

# Start the server using the production build
CMD [ "node", "dist/main.js" ]
