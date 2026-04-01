version: '3.8'
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: new_application_db
    volumes:
      - /home/ubuntu/compose-lab/mount_host_directory_instead_of_volumes:/var/lib/mysql
  app:
    image: ubuntu:latest
    tty: true
    stdin_open: true
volumes:
  mysql-composed-data:
