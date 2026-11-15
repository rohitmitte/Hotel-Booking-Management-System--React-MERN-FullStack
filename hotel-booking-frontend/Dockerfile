FROM node:20-alpine AS build

WORKDIR /app

COPY hotel-booking-frontend/package*.json ./hotel-booking-frontend/
WORKDIR /app/hotel-booking-frontend

RUN npm ci

WORKDIR /app

COPY shared ./shared
COPY hotel-booking-frontend ./hotel-booking-frontend

WORKDIR /app/hotel-booking-frontend

ARG VITE_API_BASE_URL
ENV VITE_API_BASE_URL=$VITE_API_BASE_URL

RUN npm run build

FROM nginx:alpine

COPY --from=build /app/hotel-booking-frontend/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

