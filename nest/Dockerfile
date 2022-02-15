FROM node:17-alpine3.14 As development
WORKDIR /usr/src/app
COPY package*.json ./
RUN yarn install
COPY . .
EXPOSE 80:3000
RUN npm run build
CMD ["npm", "run", "start:cloud"]