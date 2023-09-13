# Specify a BASE image
FROM node:14-alpine

WORKDIR /usr/app

# Install some dependencies
COPY ./ ./

# Install some dependencies
RUN npm install

# Default command
CMD ["npm", "start"]