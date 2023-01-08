FROM node
WORKDIR /app
ENV VITE_BACKEND_URL http://not-localhost:1000
COPY package*.json ./
RUN npm install
COPY . .
## EXPOSE [Port you mentioned in the vite.config file]
EXPOSE 9000
CMD ["npm", "run", "dev"]