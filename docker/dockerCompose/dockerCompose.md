# Docker Compose

- turn off logging for a service
```yml
services:
  my_service:
    image: my_image
    logging:
      driver: none
```