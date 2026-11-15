
FROM node:20-alpine

WORKDIR /app

COPY hotel-booking-backend/package*.json ./

RUN npm ci

COPY hotel-booking-backend/ ./

COPY shared /shared

RUN npm run build

EXPOSE 5001

CMD ["npm", "start"]

